# Модуль 0. Java / JVM / Concurrency / Performance

## Часть 0.1: JVM Internals (Практический минимум для Highload)

В банковском энтерпрайзе мы редко запускаем "просто Java-приложение". Мы управляем ресурсами. Понимание того, как JVM работает с памятью и кодом, позволяет отвечать на вопросы: "Почему вырос p99?", "Почему контейнер убит OOMKilled, хотя heap свободный?" и "Почему прогрев приложения занимает 2 минуты?".

### 1. Память: Heap, Metaspace, Stack и скрытые детали

Для Senior-разработчика важно видеть память не просто как "кучу", а как набор областей с разной стоимостью доступа и жизненным циклом.

#### Heap (Куча)

Здесь живут объекты. Но важно знать про **TLAB (Thread Local Allocation Buffer)**.

* **Проблема:** Выделение памяти в куче требует блокировки (или CAS), так как куча общая. В высоконагруженном приложении это bottleneck.
* **Решение (TLAB):** JVM выделяет каждому потоку небольшой кусочек в Eden-пространстве. Поток аллоцирует объекты в *своем* кусочке без синхронизации.
* **Практика:** Если TLAB'ы слишком маленькие, а объекты большие, они будут аллоцироваться сразу в "общем" Eden (slow path) или даже в Old Gen. Это видно в JFR (Java Flight Recorder) как событие `ObjectAllocationOutsideTLAB`.

#### Metaspace (Off-heap)

Здесь живут метаданные классов (не сами объекты `Class`, а их внутреннее представление JVM).

* **Нюанс:** Metaspace растет динамически. В контейнерах (Kubernetes) обязательно нужно ограничивать `-XX:MaxMetaspaceSize`, иначе Native Memory может съесть всю память пода, и приложение упадет от системного OOM Killer, даже если Heap пустой.

#### Compressed Oops (Ordinary Object Pointers)

* **Суть:** На 64-битной JVM указатели занимают 8 байт. Это раздувает память.
* **Оптимизация:** Если хип меньше ~32 ГБ, JVM использует 32-битные смещения вместо полных адресов.
* **Влияние:** Увеличение хипа с 31 ГБ до 33 ГБ может **уменьшить** полезный объем памяти, так как отключатся Compressed Oops, и все ссылки "потолстеют" в 2 раза.

### 2. GC в проде: G1 vs ZGC

Выбор GC — это выбор между пропускной способностью (Throughput) и задержкой (Latency).

#### G1 GC (Garbage First) — Дефолт с Java 9

* **Как работает:** Делит кучу на регионы (Eden, Survivor, Old, Humongous). Очищает те, где больше всего мусора.
* **Сценарий:** Универсальный солдат. Подходит для большинства микросервисов с хипом 4GB–16GB.
* **Тюнинг:** Главная ручка — `-XX:MaxGCPauseMillis=200` (целевая пауза). Но не ставьте слишком мало (например, 10мс), иначе G1 будет работать постоянно, убивая CPU.

#### ZGC (The Z Garbage Collector) — Production Ready в Java 21

* **Как работает:** Использует "цветные указатели" (colored pointers) и load barriers. Фазы пометки и перемещения объектов происходят **конкурентно** с работой приложения.
* **Киллер-фича:** Паузы не зависят от размера хипа. 10 МБ или 10 ТБ — пауза < 1 мс.
* **Сценарий:** Платежные шлюзы, антифрод-системы, где SLA жесткий (latency sensitive).
* **Цена:** Чуть выше оверхед по CPU (нужны ресурсы на барьеры) и чуть ниже общая пропускная способность (throughput) по сравнению с G1.

**Пример конфигурации (Java 21):**

```bash
# Вариант для стандартного микросервиса (G1)
java -Xms4G -Xmx4G \
     -XX:+UseG1GC \
     -XX:MaxGCPauseMillis=200 \
     -XX:+FlightRecorder \  # Всегда включен в проде для диагностики
     -jar app.jar

# Вариант для Low Latency сервиса (ZGC)
java -Xms8G -Xmx8G \
     -XX:+UseZGC \
     -XX:+ZGenerational \   # В Java 21 ZGC стал generational (разделение на young/old), это буст!
     -jar app.jar

```

### 3. JIT / HotSpot: Магия оптимизации

Почему Java код "разгоняется" со временем?

#### Inlining (Инлайнинг)

Самая важная оптимизация. JIT берет тело вызываемого метода и вставляет его прямо в место вызова, убирая накладные расходы на `invoke`.

* **Следствие:** Пишите маленькие методы. Огромные методы ("простыни") JIT не сможет заинлайнить (есть лимиты на размер байткода).

#### Escape Analysis & Scalar Replacement

JIT анализирует область видимости объекта.

* **Сценарий:** Вы создаете объект внутри метода, используете его поля и забываете. Он не возвращается из метода и не присваивается глобальной переменной.
* **Магия:** JIT может **не создавать** этот объект в куче вообще. Он разложит его на поля (скаляры) и положит их прямо на стек или в регистры процессора.
* **Вывод:** Не бойтесь создавать короткоживущие объекты-обертки или итераторы. JIT их "сотрет".

#### Deoptimization

Если JIT сделал оптимизацию (например, предположил, что `List` — это всегда `ArrayList`), а потом прилетел `LinkedList`, происходит "Deoptimization". Код выбрасывается обратно в интерпретатор (медленно), перепрофилируется и компилируется заново. Это вызывает спайки latency.

### 4. Classloading: Типовые боли

В Enterprise часто встречаются сложные иерархии (особенно если есть легаси или application servers, хотя в Spring Boot всё проще).

* **Classloader Leaks:** Классика в старых системах. Если вы динамически грузите классы и не вычищаете ссылки (например, в `ThreadLocal`, который привязан к потоку из пула), то метаспейс забьется и `OutOfMemoryError: Metaspace`.
* **Dependency Hell:** Когда библиотека А хочет `Jackson 2.13`, а библиотека Б хочет `Jackson 2.9`.
* **Решение:** Maven Enforcer Plugin или Gradle dependency constraints.
* **Shading:** Перепаковка библиотеки с переименованием пакетов (например, `com.fasterxml...` -> `shaded.jackson...`). Часто используется в инфраструктурных библиотеках (агенты мониторинга), чтобы не конфликтовать с приложением.



---

### Практическое задание (Mental Check)

Представь, что у тебя есть сервис обработки транзакций.

1. В логах видишь периодические паузы по 300-400мс.
2. Хип 16 ГБ.
3. Используется Java 17 и дефолтный GC.

**Твои действия как Senior-разработчика:**

1. Понять, что дефолт — это G1.
2. Включить GC логи (`-Xlog:gc*`) или посмотреть метрики Micrometer (`jvm.gc.pause`).
3. Если паузы бьют по SLA, рассмотреть переход на ZGC (особенно если есть возможность апнуться на Java 21 с Generational ZGC).
4. Проверить, не вылезают ли аллокации за пределы TLAB (через JFR), что может триггерить частые мелкие GC циклы.

---

# Модуль 0.2 Concurrency углублённо

Разберем не просто "как запустить поток", а как гарантировать корректность данных (JMM) и как масштабировать I/O (Virtual Threads).

### 1. Java Memory Model (JMM): Контракт с реальностью

JMM — это не про то, как работает CPU, а про набор правил, гарантирующих, что один поток увидит то, что записал другой.

#### Три кита безопасности потоков:

1. **Atomicity (Атомарность):** Операция выполняется целиком или не выполняется вообще. (Пример: чтение `long` на 32-битных системах *не* атомарно без `volatile`, но в 64-битных современных JVM это уже редкость).
2. **Visibility (Видимость):** Изменения, сделанные одним потоком, видны другим.
3. **Ordering (Упорядочивание):** Защита от переупорядочивания инструкций (Reordering) компилятором или процессором ради оптимизации.

#### Happens-Before

Это ключевое отношение частичного порядка. Если событие **A** *happens-before* **B**, то все изменения памяти в **A** видны в **B**.

* **Правило монитора:** Разблокировка мьютекса (выход из `synchronized`) *happens-before* захват того же мьютекса.
* **Правило volatile:** Запись в `volatile` переменную *happens-before* каждое последующее чтение этой же переменной.
* **Правило старта потока:** `thread.start()` *happens-before* любое действие в запущенном потоке.

#### Volatile (Практика)

`volatile` — это не замена блокировке. Он не гарантирует атомарность (например, `i++` не атомарен даже с volatile).

* **Что делает:** Гарантирует видимость (чтение всегда из основной памяти/L3, мимо локального кэша ядра) и ставит "барьеры памяти" (Memory Barriers), запрещая перестановку инструкций.
* **Кейс:** Флаги остановки, Double-Checked Locking (в синглтонах).

```java
public class TaskFlag {
    // БЕЗ volatile другой поток может бесконечно читать 
    // кэшированное значение false и никогда не остановиться.
    private volatile boolean running = true; 

    public void stop() {
        running = false; // Write barrier
    }

    public void doWork() {
        while (running) { // Read barrier
            // logic
        }
    }
}

```

---

### 2. CompletableFuture: Асинхронный "клей"

До Java 21 это был стандарт де-факто для асинхронной композиции.

**Важный нюанс:** `CompletableFuture` без указания Executor'а использует глобальный `ForkJoinPool.commonPool()`.

* **Риск:** Если вы запустите там блокирующую задачу (DB call, HTTP request), вы можете "повесить" весь пул. Это заблокирует работу других частей приложения (например, параллельных стримов), которые тоже используют commonPool.
* **Best Practice:** Всегда передавайте свой пул потоков для I/O задач.

**Пример (Pipeline: Получить клиента -> Получить счета -> Собрать отчет):**

```java
ExecutorService ioExecutor = Executors.newFixedThreadPool(10); // Пул для I/O

public CompletableFuture<Report> buildReport(String userId) {
    return CompletableFuture.supplyAsync(() -> userService.getUser(userId), ioExecutor)
        .thenCompose(user -> {
            // thenCompose используется для цепочки фьючерсов (как flatMap)
            return accountService.getAccounts(user.getId()) // Возвращает CF<List<Account>>
                .thenApply(accounts -> new Report(user, accounts));
        })
        .orTimeout(2, TimeUnit.SECONDS) // Java 9+: Таймаут обязателен в банке!
        .exceptionally(ex -> {
            log.error("Failed to build report for user {}", userId, ex);
            return Report.EMPTY; // Fallback (Graceful degradation)
        });
}

```

---

### 3. ForkJoinPool: Work-Stealing

Почему он особенный?

* **Work-Stealing:** Каждый поток в пуле имеет свою очередь задач (Deque). Когда поток разгреб свою очередь, он может "украсть" задачу из хвоста очереди другого потока.
* **Рекурсия:** Идеален для задач "разделяй и властвуй" (RecursiveTask), где подзадачи порождают новые подзадачи.
* **LIFO vs FIFO:** Собственные задачи поток берет по принципу LIFO (свежее — горячее в кэше CPU), а крадет по FIFO.

---

### 4. Virtual Threads (Project Loom, Java 21): Геймчейнджер

Это самое значимое изменение в Java за последние 10 лет. Оно убивает необходимость в реактивном программировании (WebFlux/Reactor) для 90% задач.

#### Концепция

* **Platform Thread (OS Thread):** Тяжелый (1-2 МБ стека), дорогой контекст свитч, лимит ~10к на машину.
* **Virtual Thread:** Дешевый (байты при простое), управляется JVM, не привязан к ядру 1:1.

#### M:N Model

Множество виртуальных потоков (M) мапятся на небольшое количество платформенных потоков-носителей (N, обычно равно числу ядер CPU).

* **Магия:** Когда виртуальный поток делает **блокирующую операцию** (JDBC запрос, HTTP call, `Thread.sleep`), JVM "отцепляет" (unmount) его от платформенного потока. Платформенный поток берет следующий виртуальный.
* **Результат:** Вы можете иметь 1 миллион виртуальных потоков, ждущих ответа от базы, используя всего 10 ядер CPU.

#### Подводные камни (Pinning)

В Java 21 есть проблема "Pinning" (закрепление).
Если виртуальный поток заходит в блок `synchronized` или вызывает native метод, он **приклеивается** к платформенному потоку. Если внутри `synchronized` сделать долгий I/O, вы заблокируете реальное ядро.

* **Решение:** Используйте `ReentrantLock` вместо `synchronized`, если внутри планируется I/O. (В будущих версиях Java это обещают исправить).

**Сравнение:**

```java
// OLD WAY: Thread Pool (ограничен ресурсами ОС)
// Если все 200 потоков ждут БД, сервис встал.
ExecutorService oldPool = Executors.newFixedThreadPool(200);

// NEW WAY: Virtual Threads (Thread-per-request is back!)
// Executors.newVirtualThreadPerTaskExecutor() создает новый VT на каждую задачу.
// Пул не нужен, так как потоки "бесплатные".
ExecutorService loomExecutor = Executors.newVirtualThreadPerTaskExecutor();

loomExecutor.submit(() -> {
    // Этот код выглядит как блокирующий, но JVM сделает его неблокирующим
    var data = dbRepository.findAll(); // Unmounts here
    return process(data);
});

```

### 5. Structured Concurrency (Java 21 Preview)

Попытка навести порядок в хаосе асинхронности. Если вы запускаете 3 параллельные задачи, и одна упала, остальные часто продолжают работать впустую.

**StructuredTaskScope** позволяет связать потоки в блок, похожий на `try-with-resources`.

```java
// Пример: "Умри, если хоть одна подзадача упала" (ShutdownOnFailure)
try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
    
    Supplier<String> userTask = scope.fork(() -> userService.getUser());
    Supplier<List<Order>> ordersTask = scope.fork(() -> orderService.getOrders());

    scope.join();           // Ждем всех
    scope.throwIfFailed();  // Если кто-то упал, кидаем исключение

    // Если мы здесь, значит всё ок
    return new Response(userTask.get(), ordersTask.get());
}
// При выходе из блока try, scope гарантирует, что все потоки завершены или убиты.

```

---

### Практический вывод для собеседования

1. **На вопрос "Как работает volatile?"** отвечай про *Happens-Before*, видимость и барьеры, а не просто "не кэшируется".
2. **На вопрос "Зачем нужны Virtual Threads?"** говори: "Чтобы вернуть модель *thread-per-request* для I/O-bound задач и писать простой императивный код вместо сложного реактивного, сохраняя пропускную способность".
3. **Про Pinning:** Обязательно упомяни, что в Java 21 `synchronized` может блокировать carrier-поток, поэтому в новом коде под Loom лучше смотреть в сторону `ReentrantLock`.

---

# Модуль 0.3. Диагностика и профилирование

### 1. JFR (Java Flight Recorder) и JMC (Java Mission Control)

JFR — это "черный ящик" самолета. Он записывает события внутри JVM (GC, блокировки, I/O, компиляцию) в кольцевой буфер памяти.

* **Главная фича:** Extremely Low Overhead (< 1%). Его можно и **нужно** держать включенным в продакшене постоянно.
* **Continuous Profiling:** В банках часто настраивают JFR так, чтобы он дампил последние 5 минут перед падением или по триггеру.

**Как включить (Java 17+):**

```bash
# StartFlightRecording: начать запись при старте
# disk=true: писать на диск во временную папку (чтобы не забить RAM)
# dumponexit=true: сохранить файл при остановке приложения
# maxage=10m: хранить только последние 10 минут
java -XX:StartFlightRecording=disk=true,dumponexit=true,filename=recording.jfr,maxage=10m -jar app.jar

```

**Анализ в JMC:**
Открыв файл `.jfr` в Java Mission Control, ищи вкладку **"Method Profiling"** и **"Lock Instances"**.

* Если видишь, что 80% времени поток висит в `socketRead` — проблема в сети или медленной БД.
* Если видишь `monitor enter` — потоки дерутся за `synchronized` блок.

### 2. Thread Dump (Снимок потоков)

Мгновенный снимок того, чем заняты все потоки в данный момент.

**Когда нужен:**

* Приложение "зависло" (не отвечает на HTTP).
* CPU 100% (нужно найти "горячий" поток).
* Deadlock (взаимная блокировка).

**Как снять (без GUI):**

```bash
# jstack <pid> - старый добрый способ
jstack 1 | grep "BLOCKED" -A 10

# jcmd - современный способ
jcmd 1 Thread.print

```

**Паттерн "Pool Exhausted":**
Ты увидишь 200 потоков `http-nio-8080-exec-...` в состоянии `WAITING` или `TIMED_WAITING`, и все они висят на одной строке, например:
`at com.zaxxer.hikari.pool.HikariPool.getConnection(...)`
**Диагноз:** Закончились коннекты к БД. Приложение живо, но не может обработать ни одного запроса.

### 3. Heap Dump (Снимок памяти)

Полная карта всех объектов в памяти.

**Осторожно! (Danger Zone):**
Снятие хип-дампа — это **Stop-The-World** операция. Если у тебя Heap 32GB, JVM остановит мир на 10-30 секунд (или больше), пока пишет файл на диск. В платежном контуре это может привести к таймаутам на клиентах и разрыву кластера. Делать только в крайнем случае или при выводе узла из балансировки.

**Инструмент анализа:** Eclipse Memory Analyzer (MAT).

* **Shallow Heap:** Размер самого объекта.
* **Retained Heap:** Сколько памяти освободится, если удалить этот объект (сумма размеров объекта + всех объектов, которые он удерживает). Именно на эту метрику нужно смотреть при поиске утечек.

### 4. async-profiler (Тяжелая артиллерия)

Стандартные профайлеры (VisualVM, JVisualVM) имеют проблему **Safepoint Bias**. Они могут снимать стек только в безопасных точках (safepoints). Если поток крутится в нативном коде или плотном цикле без аллокаций, стандартный профайлер его не "увидит".

**async-profiler** использует `perf_events` Linux и видит всё.

**Как читать Flame Graph (Пламенные графы):**

* **Ось Y (Высота):** Глубина стека вызовов.
* **Ось X (Ширина):** Процент времени CPU (или аллокаций), которое занял метод *вместе со своими детьми*.
* **Поиск:** Ищи самые широкие "плато" на вершинах гор. Это и есть боттлнеки.

**Команда (пример запуска внутри контейнера):**

```bash
# Профилируем 30 секунд и сохраняем в HTML
./profiler.sh -d 30 -f /tmp/flamegraph.html <pid>

```

### 5. Нагрузочное тестирование (Load Testing)

Senior не верит, что "код работает", пока не увидит график деградации.

**Инструменты:**

* **Gatling / JMeter:** Классика, мощно, но JVM-based (требует ресурсов на генератор).
* **k6 (Go):** Современный стандарт. Пишешь сценарии на JS, запускаешь бинарник. Очень легкий.

**Что тестируем (Сценарии):**

1. **Baseline:** Нормальная нагрузка (например, 100 RPS). Latency должно быть ровным.
2. **Stress:** Ищем точку отказа. Повышаем RPS, пока p99 не пробьет потолок или не посыпятся 50x ошибки.
3. **Soak (Endurance):** Держим нагрузку 24 часа. Ищем утечки памяти (медленный рост Old Gen) или утечки соединений.

**Пример скрипта k6 (проверка API):**

```javascript
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  stages: [
    { duration: '30s', target: 20 }, // Разгон до 20 юзеров
    { duration: '1m', target: 20 },  // Полка
    { duration: '10s', target: 0 },  // Остывание
  ],
  thresholds: {
    http_req_duration: ['p(95)<200'], // 95% запросов должны быть быстрее 200мс
  },
};

export default function () {
  const res = http.get('http://localhost:8080/api/v1/users/1');
  check(res, { 'status was 200': (r) => r.status == 200 });
  sleep(1);
}

```

---

### Практическое резюме

1. **JFR** включен всегда. Это твоя страховка.
2. **Heap Dump** в проде снимаем только с умирающего пода (или с флагом `-XX:+HeapDumpOnOutOfMemoryError`), иначе убьем latency.
3. Если CPU 100% и непонятно почему — **async-profiler** + Flame Graph.
4. Никогда не выкатывай фичу в Highload без прогона **k6** (хотя бы базового сценария).

---

## Часть 1.1: IoC Container & Bean Lifecycle (Механика "под капотом")

### ApplicationContext vs BeanFactory

На собеседовании спросят: "В чем разница?".

* **BeanFactory:** Это "движок". Ленивая загрузка, создание бинов.
* **ApplicationContext:** Это "обвес". Наследуется от BeanFactory. Добавляет: события (Events), AOP, работу с ресурсами, профили, интернационализацию.
* **Spring Boot:** Всегда поднимает `ApplicationContext` (обычно `AnnotationConfigServletWebServerApplicationContext` или Reactive аналог).

### Bean Scopes: Скрытые нюансы

1. **Singleton (default):** Один экземпляр на весь контекст.
* **Danger:** Бины должны быть **stateless** (без состояния). Если вы запишете данные пользователя в поле синглтона, их увидят все остальные пользователи. Это классический баг.


2. **Prototype:** Новый экземпляр при каждом запросе (`ctx.getBean()`).
3. **Request / Session (Web scopes):**
* **Проблема:** Как внедрить бин со скоупом `Request` (живет миллисекунды) в `Singleton` (живет вечно)?
* **Решение (Scoped Proxy):** Spring внедряет не сам объект, а CGLIB-прокси. При вызове метода прокси он идет в текущий `HttpServletRequest`, находит там реальный бин и делегирует ему вызов.



### Жизненный цикл бина (The Bean Lifecycle)

Это то, что отличает Senior от Middle. Порядок вызова методов при поднятии контекста.

**Упрощенная схема (Main Path):**

1. **Instantiation:** Вызов конструктора (Java создает объект).
2. **Populate Properties:** Сеттеры и внедрение `@Value`, `@Autowired` (в поля).
3. **BeanNameAware / BeanFactoryAware:** Если бин хочет знать свое имя или получить доступ к фабрике.
4. **BeanPostProcessor (BeforeInitialization):** Магия начинается здесь.
5. **Initialization:** `@PostConstruct` -> `InitializingBean.afterPropertiesSet()` -> `init-method` (в конфиге).
6. **BeanPostProcessor (AfterInitialization):** **Критический этап**. Здесь создаются **Прокси** (AOP). Если бин помечен `@Transactional`, `@Async` или `@Cacheable`, именно здесь оригинальный объект подменяется на обертку.
7. **Ready to use.**

**Почему это важно?**
Если вы попытаетесь вызвать `@Transactional` метод из `@PostConstruct`, транзакция **не сработает**, потому что прокси (шаг 6) создается *после* инициализации (шаг 5).

---

## Часть 1.2: Конфигурация и Внедрение (DI)

### Java-based Configuration: `@Configuration` vs `@Component`

В чем разница между этим:

```java
@Configuration
public class AppConfig {
    @Bean
    public MyService myService() { return new MyService(); }
}

```

и "Lite mode" (когда `@Bean` внутри обычного класса без `@Configuration`)?

* **Full Mode (@Configuration):** Класс оборачивается в CGLIB прокси. Вызовы методов перехватываются.
* Если вы вызовете метод `myService()` 5 раз внутри этого класса, Spring вернет **один и тот же** экземпляр (из кэша контекста). Это сохраняет семантику Singleton.


* **Lite Mode (@Component):** Прокси нет. Вызов метода — это просто вызов метода Java. Вы получите 5 **разных** объектов. В энтерпрайзе это часто приводит к багам с дублированием бинов (например, 5 разных пулов соединений).

### Типы внедрения зависимостей (Best Practices)

#### 1. Field Injection (Избегать!)

```java
@Autowired
private UserService userService; // Зло

```

* **Почему плохо:**
* Скрытая зависимость. Глядя на конструктор, не видно, что нужно классу.
* `NullPointerException` в Unit-тестах. Вам придется использовать Reflection или Spring Extension, чтобы засунуть туда мок.
* Нельзя сделать поле `final` (иммутабельность нарушена).



#### 2. Setter Injection (Опционально)

Используется редко, только для *опциональных* зависимостей, которые могут меняться в рантайме (почти никогда в современном Spring).

#### 3. Constructor Injection (Рекомендовано)

```java
@Service
public class OrderService {
    private final UserService userService; // final!

    // @Autowired в Boot 3 необязателен, если конструктор один
    public OrderService(UserService userService) {
        this.userService = userService;
    }
}

```

* **Плюсы:**
* Гарантия неизменяемости (`final`).
* Нельзя создать объект в невалидном состоянии (компилятор не даст забыть аргумент).
* Легкое тестирование: `new OrderService(new MockUserService())`.



### Циклические зависимости (Circular Dependencies)

`A -> B -> A`.

* Если **Field Injection**: Spring может разрулить это, создав бин A, потом B, и *потом* внедрив их друг в друга (через Property Population). Но вы получите warning в логах.
* Если **Constructor Injection**: Приложение упадет с `BeanCurrentlyInCreationException`.
* **Мнение Senior:** Падение приложения при старте — это **хорошо**. Это сигнал, что у вас кривая архитектура. Разбейте сервисы, выделите общий компонент или используйте `@Lazy` в конструкторе как временный "костыль".

---

### Разбор кода: BeanPostProcessor (BPP)

Чтобы понять магию Spring, нужно написать свой BPP. Представим, мы хотим замерять время инициализации всех бинов.

```java
@Component
public class BenchmarkPostProcessor implements BeanPostProcessor {

    private Map<String, Long> startTimes = new HashMap<>();

    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) {
        // Запоминаем время перед вызовом @PostConstruct
        startTimes.put(beanName, System.nanoTime());
        return bean; // Можно вернуть подмененный объект!
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) {
        // Считаем время после отработки init-методов
        Long start = startTimes.get(beanName);
        if (start != null) {
            long duration = System.nanoTime() - start;
            if (duration > 1_000_000_000) { // Если дольше 1 сек
                System.out.println("WARNING: Bean " + beanName + " took long to init!");
            }
        }
        return bean; // Именно здесь AOP создает прокси
    }
}

```

Именно так работают `@Validated`, `@Scheduled` и вся магия Spring. Они просто подменяют ваши бины в методе `postProcessAfterInitialization`.

---

### Практическое задание (Mental Check)

У тебя есть класс:

```java
@Service
public class ReportService {
    @Autowired
    private ReportService self; // Внедрение самого себя

    @Transactional
    public void generate() { ... }

    public void start() {
        generate(); // Вызов 1
        self.generate(); // Вызов 2
    }
}

```

**Вопрос:** В чем разница между Вызовом 1 и Вызовом 2?
**Ответ:**

* `generate()` (Вызов 1): Это вызов метода внутри объекта (`this.generate()`). Он идет мимо прокси. Транзакция **не откроется**.
* `self.generate()` (Вызов 2): Вызов идет через внедренный прокси. Интерцептор перехватит вызов, откроет транзакцию и делегирует выполнение реальному методу.

Это называется **Self-Invocation Problem**. Решение: не вызывать транзакционные методы из того же класса или делать инъекцию самого себя (как в примере), или (лучше) выносить метод в другой сервис.

---

# Модуль 1.3. SpEL и Внешняя Конфигурация

### 1. `@Value` vs `@ConfigurationProperties`

Это классический холивар, но в Enterprise побеждает **Type-Safe Configuration**.

#### `@Value("${app.timeout}")` — "Быстро, но грязно"

* **Минусы:**
* **String-oriented:** Вы размазываете строковые ключи по всему коду. Опечатался в одном месте — приложение упало в рантайме (или, что хуже, записало `null`).
* **Нет валидации:** Сложно проверить, что таймаут > 0, не запуская бизнес-логику.
* **Нет метаданных:** IDE не подсказывает ключи в `application.yaml`.



#### `@ConfigurationProperties` — "Стандарт Enterprise"

Вы создаете POJO, который мапится на структуру YAML.

```java
@ConfigurationProperties(prefix = "bank.transfer")
@Validated // Включаем JSR-303 валидацию
public class TransferProperties {

    @NotNull
    private Duration timeout = Duration.ofSeconds(30); // Значение по умолчанию

    @Min(1)
    private int maxRetries;
    
    private List<String> allowedCurrencies;

    // Getters / Setters / ConstructorBinding (с Boot 3.0 можно immutable record!)
}

```

* **Relaxed Binding:** Spring Boot умен. Он поймет, что `BANK_TRANSFER_MAX_RETRIES` (env variable) мапится в `maxRetries`. Это критично для Docker/K8s.
* **Fail Fast:** Если вы забыли указать обязательное свойство, приложение упадет **при старте**, показав внятную ошибку валидации.
* **IDE Support:** Подключив `spring-boot-configuration-processor`, вы получите автодополнение в YAML файлах для ваших кастомных пропертей.

### 2. SpEL (Spring Expression Language) — Мощно, но опасно

SpEL позволяет писать логику прямо в аннотациях: `@Value("#{systemProperties['user.region'] ?: 'US'}")`.

* **Best Practice:** В крупных проектах стараются избегать сложного SpEL. Логика конфигурации должна быть явной (в Java-коде), а не спрятанной в строках внутри аннотаций. Это ад для дебага.

---

# Модуль 2. Spring Boot 3.x Internals (Magic Revealed)

Многие думают, что Spring Boot — это "черная магия". На самом деле это просто очень много конфигурации, написанной за вас.

### 2.1. AutoConfiguration: Как это работает в Boot 3.x

До Spring Boot 2.7 мы использовали `spring.factories`. В Spring Boot 3 механизм изменился (важно знать для миграции!).

**Алгоритм загрузки:**

1. При старте `@SpringBootApplication` (который включает `@EnableAutoConfiguration`) сканирует classpath всех подключенных JAR-файлов.
2. Он ищет файл: **`META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`**.
3. В этом файле лежит список классов-конфигураций (например, `KafkaAutoConfiguration`, `DataSourceAutoConfiguration`).
4. Spring пытается загрузить их **все**.
5. Тут вступают в игру **Conditionals** (Фильтры).

### 2.2. Conditionals (`@Conditional...`) — Фейсконтроль бинов

Это механизм, который решает: "Нужен нам этот бин или нет?".

* `@ConditionalOnClass(ObjectMapper.class)`: "Загрузи эту конфигурацию JSON, только если библиотека Jackson есть в classpath".
* `@ConditionalOnProperty(name = "app.feature.enabled", havingValue = "true")`: Feature Flags. Самый частый кейс в банке. Выкатываем код, но включаем его пропертью только для тестовой группы.
* `@ConditionalOnMissingBean`: "Создай этот бин, только если пользователь (разработчик) не создал свой такой же". Именно так работают дефолты. Если вы объявили свой `DataSource`, автоконфигурация Boot'а отступает.

### 2.3. Debugging AutoConfig

Вы запускаете приложение, а бин не создался. Или создался не тот. Что делать?

**Инструмент №1: Condition Evaluation Report**
Запустите приложение с флагом `--debug` или свойством `debug=true`.
В консоли вывалится огромный отчет:

```text
============================
CONDITIONS EVALUATION REPORT
============================

Positive matches:
-----------------
   DataSourceAutoConfiguration matched:
      - @ConditionalOnClass found required class 'javax.sql.DataSource' (OnClassCondition)

Negative matches:
-----------------
   MongoAutoConfiguration:
      - @ConditionalOnClass did not find required class 'com.mongodb.client.MongoClient' (OnClassCondition)

```

Это единственный способ достоверно узнать, почему Spring принял то или иное решение.

---

### Практическое задание: Пишем свой Starter

Чтобы закрепить понимание Boot, давай спроектируем структуру кастомного стартера для банка. Например, `bank-audit-starter`, который автоматически логирует все HTTP запросы, если подключен.

**Структура проекта:**

1. **`bank-audit-autoconfigure` (Module):** Вся логика.
2. **`bank-audit-starter` (Module):** Пустой, содержит только `pom.xml` с зависимостями (на autoconfigure модуль + нужные либы). Пользователь подключает именно его.

**Код Autoconfigure:**

```java
@AutoConfiguration // Новая аннотация в Boot 2.7+
@ConditionalOnProperty(prefix = "bank.audit", name = "enabled", matchIfMissing = true)
@ConditionalOnWebApplication // Только для веб-приложений
@EnableConfigurationProperties(AuditProperties.class) // Подключаем наши настройки
public class AuditAutoConfiguration {

    @Bean
    @ConditionalOnMissingBean // Если пользователь не определил свой фильтр
    public AuditFilter auditFilter(AuditProperties properties) {
        return new AuditFilter(properties.getLevel());
    }
}

```

**Регистрация (файл `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`):**

```text
com.bank.audit.AuditAutoConfiguration

```

**Нюанс порядка (Order):**
Если ваш автоконфиг зависит от другого (например, вам нужен `DataSource`), используйте `@AutoConfigureAfter(DataSourceAutoConfiguration.class)`. Порядок инициализации обычных `@Configuration` не гарантирован, но для `@AutoConfiguration` порядок строгий.

---

### Резюме по Модулю 2

1. Используйте `@ConfigurationProperties` с валидацией (`@Validated`) вместо `@Value`.
2. Знайте, где искать автоконфигурации в Boot 3: файл `.imports`.
3. `--debug` — ваш лучший друг, когда "магия сломалась".
4. Понимайте разницу между `@ConditionalOnClass` (зависимость в pom) и `@ConditionalOnBean` (бин в контексте).

---

## Механика Spring AOP: Проксирование

Spring AOP работает в рантайме. Он не меняет байт-код ваших классов при компиляции (в отличие от полноценного AspectJ Weaving). Вместо этого он создает "обертку".

### 1. JDK Dynamic Proxy vs. CGLIB

Когда вы инжектите сервис, вы инжектите не сам объект, а прокси.

* **JDK Dynamic Proxy:**
* Встроен в Java.
* Работает **только если класс реализует интерфейс**.
* Прокси реализует тот же интерфейс и делегирует вызовы.


* **CGLIB (Code Generation Library):**
* Работает через наследование (**Subclassing**). Генерирует наследника вашего класса на лету и переопределяет методы.
* **Важно:** С Spring Boot 2.0+ по умолчанию используется CGLIB (`proxy-target-class="true"`), даже если есть интерфейсы. Это сделано для предсказуемости.



**Ограничения CGLIB (вопрос на засыпку):**
Поскольку CGLIB наследуется от класса:

1. Нельзя сделать аспект на **final** класс.
2. Нельзя перехватить **final** метод (его нельзя переопределить).
3. Нельзя перехватить **private** метод (он не виден наследнику и не вызывается извне через прокси).

---

## Практика: Пишем полезный аспект для банка

Допустим, нам нужно замерять время выполнения критичных методов и отправлять их в метрики, но мы не хотим засорять бизнес-код вызовами таймеров.

### Шаг 1: Создаем аннотацию

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface MeasureTime {
    String metricName() default "method.exec.time";
}

```

### Шаг 2: Реализуем Аспект

```java
@Aspect
@Component
@Slf4j
public class MetricsAspect {

    private final MeterRegistry meterRegistry; // Micrometer (стандарт в Spring Boot)

    public MetricsAspect(MeterRegistry meterRegistry) {
        this.meterRegistry = meterRegistry;
    }

    // Pointcut: Где применять? (На методах с аннотацией @MeasureTime)
    @Around("@annotation(measureTime)") 
    public Object logExecutionTime(ProceedingJoinPoint joinPoint, MeasureTime measureTime) throws Throwable {
        
        long start = System.nanoTime();
        
        // Tagging для метрик: имя класса и метода
        String className = joinPoint.getSignature().getDeclaringType().getSimpleName();
        String methodName = joinPoint.getSignature().getName();

        try {
            // !!! Самая важная строка. Передает управление реальному методу.
            // Если забыть вызвать proceed(), метод не выполнится.
            return joinPoint.proceed(); 
        } catch (Throwable ex) {
            // Можно добавить метрику ошибок здесь
            meterRegistry.counter(measureTime.metricName() + ".errors").increment();
            throw ex; // Обязательно пробрасываем исключение дальше!
        } finally {
            // Записываем тайминг даже если упала ошибка
            long duration = System.nanoTime() - start;
            
            Timer.builder(measureTime.metricName())
                .tag("class", className)
                .tag("method", methodName)
                .register(meterRegistry)
                .record(Duration.ofNanos(duration));
                
            // Логируем для отладки (аккуратно в проде!)
            log.debug("Method {} executed in {} ns", methodName, duration);
        }
    }
}

```

---

## Важные нюансы для Production

### 1. Порядок Аспектов (`@Order`)

В одном методе может быть несколько аспектов: `@Transactional`, `@Cacheable`, `@MeasureTime`.
**Вопрос:** Что выполнится раньше — открытие транзакции или наш таймер?

* Если таймер сработает *до* транзакции, мы замерим "чистое" время + время на открытие БД-соединения.
* Если таймер *внутри* транзакции, мы замерим только бизнес-логику.

Чтобы управлять этим, используйте аннотацию `@Order` на классе аспекта. Чем меньше число, тем выше приоритет (тем "снаружи" обертка).

* Spring TransactionInterceptor обычно имеет Order = `Integer.MAX_VALUE` (самый низкий приоритет, выполняется последним/самым внутренним), но это настраивается.

### 2. Безопасность данных (PII)

Если вы делаете аспект для **логирования аргументов** (`joinPoint.getArgs()`):

* **Критично:** Никогда не логируйте всё подряд вслепую. В аргументах могут быть пароли, CVV-коды, паспортные данные.
* **Решение:** Пишите кастомные маскеры (Masker) или помечайте чувствительные поля аннотацией `@ToString.Exclude` (если Lombok) / `@JsonIgnore`. Попадание PAN карты в логи — это провал PCI DSS аудита.

### 3. Производительность (Overhead)

`ProceedingJoinPoint` и рефлексия (получение имен методов, аргументов) стоят небесплатно.

* Не вешайте сложные аспекты на методы, которые вызываются 10 000 раз в секунду в цикле.
* В микро-бенчмарках вызов через CGLIB прокси медленнее прямого вызова, но в масштабах работы с БД/сетью это незаметно.

---

## Разбор "Self-Invocation" через призму AOP

Мы уже касались этого, но здесь механика становится очевидной.

```java
@Service
public class PaymentService {

    @MeasureTime // Аспект висит здесь
    public void processPayment() { ... }

    public void batchProcess() {
        for (int i = 0; i < 10; i++) {
            this.processPayment(); // ВЫЗОВ 1
        }
    }
}

```

Когда вы вызываете `batchProcess()` из контроллера, вы проходите через Прокси. Но внутри метода `batchProcess` переменная `this` указывает на **реальный объект** `PaymentService`, а не на Прокси.
Следовательно, `this.processPayment()` вызывает "голый" метод Java. Код аспекта `@MeasureTime` **не выполнится**.

**Решение (AopContext):**
Грязный хак, но иногда нужен (требует `expose-proxy=true` в конфиге):

```java
((PaymentService) AopContext.currentProxy()).processPayment();

```

Лучшее архитектурное решение — вынести метод в другой бин.

---

## Мини-чеклист по AOP

1. **Понимаю**, что аспекты применяются только к публичным методам (при использовании Spring AOP).
2. **Помню** про `joinPoint.proceed()` и обработку исключений (не проглотить ошибку случайно).
3. **Знаю**, что аспекты не работают при вызове метода внутри того же класса.
4. **Умею** задавать порядок выполнения через `@Order`, если на методе висит несколько аннотаций.

---

## 1. Entity Lifecycle и Persistence Context (L1 Cache)

Многие считают, что Hibernate просто "сохраняет объекты в базу". На самом деле он управляет **состояниями**.

### Четыре состояния сущности:

1. **Transient:** Просто объект `new User()`. База о нем не знает.
2. **Persistent (Managed):** Объект "привязан" к текущей сессии (`EntityManager`). Любое изменение поля в этом объекте **автоматически** улетит в базу при коммите транзакции. Вызывать `save()` не нужно!
3. **Detached:** Сессия закрылась (транзакция завершилась), но объект остался в памяти. Изменения в нем игнорируются базой.
4. **Removed:** Объект помечен на удаление.

### Dirty Checking (Грязная проверка)

Как Hibernate понимает, что нужно сделать `UPDATE`?

* При загрузке объекта в L1 Cache (Persistence Context), Hibernate делает его **Snapshot** (копию).
* При `flush()` (обычно перед коммитом) он сравнивает текущее состояние объекта со снэпшотом.
* Если есть различия — генерируется SQL `UPDATE`.

**Прод-нюанс:**
Если вы вычитываете 10,000 объектов в транзакции просто для "посмотреть", Hibernate сделает 10,000 снэпшотов. Это **жрет память и CPU** на сравнение.

* **Решение:** Используйте **Read-Only транзакции** (`@Transactional(readOnly = true)`). В этом режиме Hibernate (в последних версиях и при правильной настройке Spring) может не делать снэпшоты, экономя ресурсы.

---

## 2. Проблема N+1 (The Silent Killer)

Главный враг производительности ORM. Вы запрашиваете список родителей, а Hibernate делает по дополнительному запросу на каждого родителя, чтобы достать детей.

**Пример (Ловушка):**

```java
@Entity
public class User {
    @Id Long id;
    
    // FetchType.LAZY - это стандарт (EAGER - зло!).
    // Но именно LAZY порождает N+1 при итерации.
    @OneToMany(mappedBy = "user", fetch = FetchType.LAZY) 
    private List<Order> orders;
}

// ... в сервисе ...
List<User> users = userRepository.findAll(); // 1 запрос: SELECT * FROM users
// А теперь мы хотим отправить отчеты
for (User user : users) {
    // В этот момент происходит инициализация прокси
    // N запросов: SELECT * FROM orders WHERE user_id = ?
    sendEmail(user.getOrders().size()); 
}

```

Если пользователей 1000, вы сделаете 1001 запрос к БД. Сетевые задержки убьют приложение.

### Решения (Как лечить):

#### А. `JOIN FETCH` (JPQL)

Самый надежный способ. Мы явно говорим: "Принеси мне пользователей сразу вместе с заказами".

```java
@Query("SELECT u FROM User u JOIN FETCH u.orders")
List<User> findAllWithOrders();

```

* **Минус:** Может возникнуть `Cartesian Product` (декартово произведение), если фетчить несколько коллекций сразу (`MultipleBagFetchException`).

#### Б. `@EntityGraph` (Spring Data JPA)

Декларативный способ подсказать Hibernate, что загружать.

```java
// Переопределяем метод findAll
@EntityGraph(attributePaths = {"orders"})
List<User> findAll();

```

---

## 3. Проблема генерации ID (Identity vs Sequence)

Выбор стратегии `@GeneratedValue` критически влияет на возможность пакетной вставки (**Batch Insert**).

### `GenerationType.IDENTITY` (Auto-increment в MySQL/PG)

* **Как работает:** База сама назначает ID при вставке.
* **Проблема:** Чтобы Hibernate узнал ID вставленной записи (чтобы положить её в L1 Cache), ему нужно выполнить `INSERT` **немедленно**.
* **Результат:** **Batching отключается**. Даже если вы сохраняете список из 1000 записей через `saveAll()`, Hibernate сделает 1000 отдельных `INSERT` запросов.

### `GenerationType.SEQUENCE` (PostgreSQL / Oracle)

* **Как работает:** Hibernate просит у базы: "Дай мне следующие 50 ID" (allocationSize). База выдает диапазон.
* **Плюс:** Hibernate знает ID *до* вставки. Он может накопить 50 объектов в памяти и отправить их одним пакетом `INSERT ... VALUES (...), (...), (...)`.
* **Совет Senior:** Всегда используйте `SEQUENCE` (или `pooled-lo` алгоритм) в PostgreSQL.

```java
@Id
@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "user_seq")
@SequenceGenerator(name = "user_seq", sequenceName = "users_id_seq", allocationSize = 50)
private Long id;

```

---

## 4. LazyInitializationException

Классика новичков: `org.hibernate.LazyInitializationException: could not initialize proxy - no Session`.

**Сценарий:**

1. Сервис открыл транзакцию, загрузил `User`, закрыл транзакцию.
2. Контроллер получил `User` (он detached) и пытается обратиться к `user.getOrders()`.
3. Прокси пытается пойти в базу, но Сессии (соединения) больше нет. Падение.

**Анти-паттерн (Не делай так):** `spring.jpa.open-in-view=true` (OSIV).

* Эта настройка (включена по умолчанию в Boot!) держит сессию БД открытой до самого конца рендеринга ответа (JSON).
* **Почему это зло:** Транзакция БД держится открытой, пока медленный клиент качает JSON. Пул коннектов (`HikariCP`) быстро исчерпывается.
* **Правильное решение:** Инициализировать все нужные данные на уровне Сервиса (через DTO или `JOIN FETCH`), транзакция должна быть максимально короткой. Отключай OSIV: `spring.jpa.open-in-view=false`.

---

## 5. Locking (Конкурентный доступ)

В банке два менеджера могут одновременно открыть карточку клиента и нажать "Сохранить". Кто победит?

### Optimistic Locking (`@Version`)

Лучший выбор для большинства систем (Read-heavy).
Мы не блокируем запись в БД. Мы добавляем поле `version`.

```java
@Entity
public class Account {
    @Id Long id;
    BigDecimal balance;
    
    @Version
    private Long version;
}

```

**Механика:**

1. Транзакция А читает Account (version=1).
2. Транзакция Б читает Account (version=1).
3. Транзакция Б сохраняет. SQL: `UPDATE account SET balance=..., version=2 WHERE id=... AND version=1`. Успех.
4. Транзакция А пытается сохранить. SQL: `UPDATE ... WHERE id=... AND version=1`.
5. База говорит: "Обновлено 0 строк".
6. Hibernate кидает `OptimisticLockException`.
7. Spring ловит его и (если настроен `@Retryable`) может попробовать операцию заново.

### Pessimistic Locking (`SELECT ... FOR UPDATE`)

Когда конкуренция очень высокая (например, списание денег со счета "горячего" клиента).

```java
@Lock(LockModeType.PESSIMISTIC_WRITE)
@Query("SELECT a FROM Account a WHERE a.id = :id")
Optional<Account> findByIdLocked(Long id);

```

Это создает жесткую блокировку на уровне БД. Другие транзакции встанут в очередь и будут ждать (или отвалятся по таймауту). Надежно, но медленно.

---

### Практическое задание (Mental Check)

Представь, что тебе нужно вставить в базу 100 000 записей логов (LogEntry) максимально быстро.

1. Какую стратегию ID ты выберешь? (Ответ: Sequence).
2. Будешь ли ты использовать `LogEntryRepository.save(entity)` в цикле? (Ответ: Нет, это 100к вызовов прокси).
3. Как оптимизировать? (Ответ: Использовать `saveAll(list)` и настроить `spring.jpa.properties.hibernate.jdbc.batch_size=50`).
4. Что делать с L1 Cache? (Ответ: Каждые 1000 записей делать `entityManager.flush()` и `entityManager.clear()`, иначе 100к объектов повиснут в памяти и словишь `OutOfMemory`).

---

# Модуль 4.2. Spring Data JPA (Best Practices)

Spring Data дает нам интерфейсы `Repository`, избавляя от бойлерплейта. Но эта магия часто провоцирует написание неэффективного кода.

### 1. Методы-монстры vs `@Query`

**Derived Query Methods** (методы, выводимые из имени):
`findByNameAndStatusAndCreatedAtAfterOrderByCreatedAtDesc(...)`

* **Проблема:** Читать это невозможно. Имя метода становится длиннее SQL-запроса. К тому же, ты не контролируешь сгенерированный SQL (например, порядок джойнов).
* **Best Practice:** Если критериев больше двух — пиши явный `@Query` (JPQL или Native SQL). Это делает код чище и предсказуемее.

### 2. Projections (DTO Projection) — Экономим память

Зачем тащить из БД огромный объект `User` (с полями `bio`, `avatarBlob`, `auditLogs`), если тебе нужны только `id` и `fullName` для выпадающего списка?

**Плохо (Entity Fetching):**

```java
// Вытаскивает ВСЕ поля, включая тяжелые @Lob, если они не ленивые
List<User> findAll(); 

```

**Хорошо (Interface/Record Projection):**
Spring Data умеет мапить результат выборки сразу в интерфейс или Java Record (Java 14+).

```java
// 1. Объявляем Record (DTO)
public record UserSummary(Long id, String fullName) {}

// 2. Репозиторий
public interface UserRepository extends JpaRepository<User, Long> {
    // Spring сгенерирует SQL: SELECT id, full_name FROM users ...
    List<UserSummary> findByStatus(String status);
}

```

**Выигрыш:** Меньше трафик из БД, меньше нагрузка на CPU (не нужно создавать Entity, парсить все поля, трекать их в Hibernate Context), меньше GC.

### 3. Пагинация: `Page` vs `Slice`

Когда ты возвращаешь `Page<T>`, Spring делает **два** запроса:

1. Запрос данных с `LIMIT/OFFSET`.
2. `SELECT COUNT(*) FROM ...` — чтобы узнать общее количество страниц.

**Нюанс:** На больших таблицах (миллионы строк) `COUNT(*)` может выполняться секунды (особенно в Postgres).
**Решение:** Если фронтенду достаточно кнопки "Загрузить еще" (Infinite Scroll) и не нужно знать точное число страниц ("Страница 1 из 10500"), используй `Slice<T>`. Он не делает count-запрос, а просто запрашивает `limit + 1` запись, чтобы узнать, есть ли следующая страница.

---

# Модуль 4.3. Управление транзакциями (`@Transactional`)

Аннотация `@Transactional` — это декларативная граница атомарности.

### 1. Propagation (Распространение)

Как транзакции взаимодействуют друг с другом?

* `REQUIRED` (Default): Если транзакция уже есть — используем её. Если нет — создаем новую.
* `REQUIRES_NEW`: Всегда создает **новую независимую** транзакцию. Текущая (если есть) приостанавливается.

**Кейс в банке (Audit Log):**
Ты делаешь перевод денег. Даже если перевод упал с ошибкой (Rollback), запись в аудит-логе ("Попытка перевода") должна сохраниться.

```java
@Service
public class TransferService {
    @Transactional // Главная транзакция
    public void transfer(String from, String to, BigDecimal amount) {
        try {
            // Бизнес-логика...
            accountRepository.debit(from, amount);
            // Ошибка!
            if (true) throw new RuntimeException("Fraud check failed");
        } finally {
            // Вызов метода аудита
            auditService.logAttempt(from, to); 
        }
    }
}

@Service
public class AuditService {
    // ВАЖНО: REQUIRES_NEW. Эта транзакция закоммитится, 
    // даже если внешняя (transfer) откатится.
    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void logAttempt(String from, String to) {
        auditRepository.save(new AuditLog(from, to));
    }
}

```

### 2. Isolation (Изоляция)

В 99% случаев мы живем с дефолтным `READ_COMMITTED`. Но важно знать про `REPEATABLE_READ` и `SERIALIZABLE` для защиты от фантомных чтений в критичных расчетах, хотя чаще это решается через `SELECT FOR UPDATE` (Pessimistic Locking), о котором говорили в прошлом модуле.

### 3. Главный анти-паттерн: Long Transaction (Долгая транзакция)

Это убийца производительности №1.

**Сценарий:**

1. Открываем транзакцию (`@Transactional`).
2. Читаем данные из БД.
3. **Делаем синхронный HTTP-запрос во внешнюю систему** (например, подтверждение платежа в ЦБ), который длится 3 секунды.
4. Сохраняем результат в БД.
5. Коммитим.

**Проблема:** Все 3 секунды мы держим соединение с БД (Connection Pool) и, возможно, блокировки на строках (Locks). Если пул HikariCP = 10 коннектов, то пропускная способность сервиса = 10 / 3 = **3.3 запроса в секунду**. Сервис ляжет.

**Решение:**
Выносите длительные операции **за пределы** транзакции БД.

```java
// ПЛОХО: Весь метод в транзакции
@Transactional 
public void process() {
    Entity e = repo.findById(1);
    externalService.call(); // 3 сек
    e.setStatus("DONE");
} // Commit

// ХОРОШО: Транзакции точечные
public void processBetter() {
    // 1. Читаем (короткая транзакция или вообще без неё, если read-only)
    Data data = myservice.loadData(1); 
    
    // 2. Долгая работа БЕЗ удержания коннекта к БД
    var result = externalService.call(data); 
    
    // 3. Сохраняем (отдельная короткая транзакция)
    myservice.updateStatus(1, result); 
}

```

---

# Модуль 4.4. SQL для разработчика (PostgreSQL Focus)

Ты не можешь полагаться только на Hibernate. Нужно уметь читать планы запросов.

### 1. Индексы: B-Tree vs GIN

* **B-Tree:** Стандарт для сортируемых данных (ID, даты, числа, строки). Позволяет быстро искать `=` и диапазоны (`<`, `>`, `BETWEEN`).
* **GIN (Generalized Inverted Index):** Must-have для поиска по JSONB полям или полнотекстового поиска (tsvector) в Postgres. Если у тебя есть поле `details` типа JSONB и ты ищешь `details ->> 'account_id'`, обычный индекс не поможет (или поможет слабо), нужен GIN.

### 2. `EXPLAIN (ANALYZE)`

Команда, которая показывает реальный план выполнения.
На что смотреть Senior-разработчику:

* **Seq Scan (Sequential Scan):** Полное сканирование таблицы. На таблице в 10 записей — ок. На 10 млн — катастрофа. Значит, индекс не используется.
* **Index Scan:** Поиск по индексу. Хорошо.
* **Index Only Scan:** Данные берутся прямо из индекса, даже не заглядывая в кучу (таблицу). Идеально.
* **Actual time:** Реальное время выполнения этапов.

### 3. HikariCP Tuning

Пул соединений.

* **`maximumPoolSize`:** Не ставь "побольше". Для Postgres формула: `(core_count * 2) + effective_spindle_count`. Для обычного микросервиса 10-20 коннектов обычно за глаза. Больше коннектов = больше context switching на стороне БД.
* **`connectionTimeout`:** Время ожидания свободного коннекта из пула. Если стоит 30 сек (дефолт), то при нагрузке клиенты будут висеть полминуты, прежде чем отвалиться. В Highload лучше ставить меньше (1-2 сек), чтобы быстро фейлить запросы (Fail Fast) и не накапливать очередь.

---

### Практическое резюме по Data Layer

1. **DTO Projections:** Всегда используй их для чтения списков. Не вычитывай Entity для API `GET /users`.
2. **Transactions:** Внешние вызовы (HTTP, S3, тяжелые вычисления) всегда выноси за скобки `@Transactional`.
3. **Logs:** Если нужен аудит, который должен выжить при откате бизнес-транзакции — используй `REQUIRES_NEW`.
4. **SQL:** Если запрос тормозит, сделай `EXPLAIN ANALYZE` и проверь, не сканируется ли вся таблица (Seq Scan).

---

## 6.1. Асинхронность внутри JVM (`@Async`)

Аннотация `@Async` позволяет вынести выполнение метода в отдельный поток.

### Главная ловушка: Default Executor

По умолчанию Spring использует `SimpleAsyncTaskExecutor`.

* **Что он делает:** Создает **новый поток** на каждую задачу!
* **Результат в проде:** Если прилетит 1000 запросов, он создаст 1000 потоков. OS уйдет в OOM или Context Switching Death.

### Правильная настройка (Custom Executor)

```java
@Configuration
@EnableAsync
public class AsyncConfig {

    @Bean(name = "taskExecutor")
    public Executor taskExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(10); // Постоянно живут
        executor.setMaxPoolSize(50);  // Пиковая нагрузка
        executor.setQueueCapacity(500); // Очередь задач
        executor.setThreadNamePrefix("Async-");
        
        // ВАЖНО: Что делать, если очередь полна?
        // AbortPolicy (default) - кидает исключение
        // CallerRunsPolicy - выполняет задачу в потоке вызывающего (тормозит продюсера) - Best Practice для стабильности
        executor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
        
        executor.initialize();
        return executor;
    }
}

```

### Context Propagation (MDC)

Когда поток меняется, `MDC` (логирование), `SecurityContext` и `TransactionContext` теряются.

* **Решение:** Использовать `TaskDecorator` для копирования контекста из родительского потока в дочерний.

---

## 6.2. Apache Kafka (Хребет банковской архитектуры)

В банке Kafka — это не просто труба для логов, это **source of truth** для событий.

### Consumer Groups & Rebalancing

* **Группа потребителей:** Позволяет параллельно читать топик. Если у вас 3 партиции и 3 инстанса сервиса (в одной группе), каждый читает свою партицию.
* **Rebalance:** Если один инстанс упал, Kafka перераспределяет его партиции на живых. В этот момент чтение может "замереть" (Stop-the-world для группы).
* **Тюнинг:** `session.timeout.ms` и `max.poll.interval.ms`. Если обработка батча сообщений долгая (DB call), Kafka может решить, что консьюмер умер, и запустить ребаланс. Увеличивайте `max.poll.interval.ms`.



### Delivery Semantics (Семантика доставки)

1. **At-most-once:** "Выстрелил и забыл". Потеря сообщений допустима (логи, метрики).
2. **At-least-once (Де-факто стандарт):** Сообщение доставлено минимум один раз.
* **Механика:** Сначала обрабатываем сообщение, потом коммитим офсет (`ack`).
* **Проблема:** Если обработали (записали в БД), но упали до коммита офсета — при рестарте сообщение придет снова.
* **Решение:** **Идемпотентность** на стороне получателя (см. Модуль 5.3).


3. **Exactly-once (Kafka Transactions):** Сложно, дорого, требует поддержки на всей цепочке (producer -> topic -> consumer). Используется редко, чаще эмулируется через идемпотентность.

### Poison Pill (Отравленное сообщение)

Сообщение, которое всегда вызывает ошибку при обработке (например, битый JSON).

* **Сценарий:** Консьюмер читает -> Падает -> Рестартится -> Читает то же самое -> Падает (Infinite Loop).
* **Решение:** Настроить **Dead Letter Queue (DLQ)**. После N попыток (Retry) сообщение перекладывается в отдельный топик `.dlq` для ручного разбора, а консьюмер идет дальше. В Spring Kafka это настраивается через `DefaultErrorHandler`.

---

## 6.3. HTTP Clients (Feign & RestClient)

Когда один микросервис ходит в другой (синхронно).

### Feign Client (Declarative)

```java
@FeignClient(name = "account-service", url = "${accounts.url}")
public interface AccountClient {
    @GetMapping("/accounts/{id}")
    AccountDto getAccount(@PathVariable("id") Long id);
}

```

* **Плюсы:** Читаемо, быстро писать.
* **Минусы:** Скрывает сложность. Под капотом должен быть настроенный `OkHttp` или `ApacheHttpClient` с пулом соединений. Дефолтный `HttpURLConnection` не имеет пула!

### Resiliency (Устойчивость)

В сети всегда что-то идет не так.

1. **Timeouts:** Обязательно (Connect Timeout + Read Timeout). "Вечный" запрос вешает поток.
2. **Retries (Повторы):** Опасно!
* Если первый запрос прошел, но ответ потерялся (Timeout), ретрай сделает операцию дважды.
* **Правило:** Ретраить можно только **идемпотентные** методы (GET, PUT) или если сервер вернул 503/504. Не ретрайте 4xx или POST (без Idempotency Key).



---

## 6.4. Планировщики (Schedulers)

### `@Scheduled` в кластере Kubernetes

Вы написали метод, который раз в час начисляет проценты:

```java
@Scheduled(cron = "0 0 * * * *")
public void calculateInterest() { ... }

```

Вы задеплоили 3 реплики сервиса (пода).
**Результат:** В 00:00 запустятся **три** потока (по одному в каждом поде). Проценты начислятся трижды.

### Решение: Distributed Lock (ShedLock / Quartz)

Нужно, чтобы задача выполнилась только в одном экземпляре.

**ShedLock (на базе БД или Redis):**

```java
@Scheduled(cron = "0 0 * * * *")
@SchedulerLock(name = "calculateInterest", lockAtMostFor = "10m", lockAtLeastFor = "1m")
public void calculateInterest() {
    // 1. Пытаемся сделать INSERT в таблицу locks (ключ = имя задачи).
    // 2. Если успех (Unique constraint) — выполняем.
    // 3. Если ошибка (уже есть запись) — пропускаем.
}

```

**Quartz:** Более тяжеловесный. Умеет хранить состояние джобов в БД, делать retry при падении ноды (recovery), сложные расписания. Нужен, если управление джобами — это бизнес-функция (пользователь сам настраивает расписание отчетов).

---

## Практическое задание (Mental Check)

1. **Kafka:** Почему растет `consumer lag`?
* *Ответ:* Продюсер пишет быстрее, чем консьюмер разгребает. Решения: добавить партиций и инстансов консьюмеров, или оптимизировать логику обработки (батчинг).


2. **Async:** Метод помечен `@Async` и `@Transactional`. Как это работает?
* *Ответ:* `@Transactional` привязан к потоку (`ThreadLocal`). В новом потоке (`@Async`) транзакции не будет. Нужно открывать транзакцию **внутри** асинхронного метода, а не снаружи.


3. **Resiliency:** Внешний сервис лежит (отвечает 50 секунд). У нас таймаут 1 секунда и 3 ретрая. Что будет с нашей системой при нагрузке?
* *Ответ:* Мы усилим нагрузку на лежачий сервис (DDoS своими руками). Здесь нужен паттерн **Circuit Breaker** (предохранитель), который быстро вернет ошибку, не пытаясь делать запросы.



---

## 7.3. Transactional Outbox (Главный паттерн интеграции)

**Проблема Dual Write (Двойная запись):**
Вам нужно сделать два действия атомарно:

1. Сохранить сущность в БД (Postgres).
2. Отправить событие в Kafka ("Сущность создана").

```java
// НАИВНЫЙ ПОДХОД (Сломан)
@Transactional
public void createOrder(Order order) {
    repository.save(order); // 1. DB Insert
    kafkaTemplate.send("orders", order); // 2. Network call
} 
// 3. Commit

```

* **Сценарий краха 1:** Kafka недоступна. Метод падает, транзакция БД откатывается. Клиент получает ошибку. (Вроде ок, но доступность страдает).
* **Сценарий краха 2:** Сообщение в Kafka ушло, а на этапе `Commit` БД упала (свет мигнул).
* **Итог:** В Kafka есть сообщение "Заказ создан", другие сервисы начали работу (доставку, списание), а **в БД заказа нет**. Это катастрофа несогласованности.



### Решение: Outbox Pattern

Мы записываем сообщение **в ту же БД**, в соседнюю табличку `outbox`, в рамках **той же транзакции**.

1. **Таблица `outbox`:**
* `id` (UUID)
* `aggregate_type` ("Order")
* `aggregate_id` ("123")
* `payload` (JSON события)
* `created_at`


2. **Процесс:**
```java
@Transactional
public void createOrder(Order order) {
    // 1. Сохраняем бизнес-сущность
    repository.save(order);

    // 2. Сохраняем событие в Outbox (Это простой INSERT, он надежен)
    outboxRepository.save(new OutboxEvent("ORDER_CREATED", order));
}
// 3. Атомарный коммит БД. Либо всё сохранилось, либо ничего.

```


3. **Relay (Доставщик):**
Отдельный процесс (или Debezium CDC) читает таблицу `outbox` и перекладывает записи в Kafka. После успешной отправки удаляет запись (или ставит статус SENT).
* **Гарантия:** At-Least-Once. Relay может упасть после отправки в Kafka, но до удаления из БД. При рестарте он отправит дубль. Поэтому консьюмеры должны быть **идемпотентны** (см. Inbox).



---

## 7.4. Saga Pattern (Длинные транзакции)

Как сделать транзакцию, которая затрагивает 5 микросервисов? (Списание -> Резерв товара -> Создание накладной -> Уведомление).

### Choreography (Хореография) — Event Driven

Сервисы общаются событиями.

* Сервис А публикует `OrderCreated`.
* Сервис Б слушает `OrderCreated`, делает резерв, публикует `GoodsReserved`.
* Сервис В слушает `GoodsReserved`...

**Плюсы:** Слабая связность.
**Минусы (Ад в Enterprise):** Сложно понять бизнес-процесс целиком. "Кто слушает это событие?". Сложно отлаживать циклические зависимости.

### Orchestration (Оркестрация) — Предпочтительно для банков

Есть центральный координатор (Orchestrator). Часто реализуется через **Camunda**, **Temporal** или просто State Machine в коде.

```java
public class OrderSagaOrchestrator {
    public void processOrder(Order order) {
        try {
            paymentService.debit(order); // Шаг 1
            inventoryService.reserve(order); // Шаг 2
            deliveryService.schedule(order); // Шаг 3
        } catch (Exception e) {
            // КОМПЕНСАЦИЯ (Откат)
            // Если упала доставка, мы должны вернуть деньги и отменить резерв
            inventoryService.cancelReserve(order);
            paymentService.refund(order);
            order.setStatus(FAILED);
        }
    }
}

```

**Компенсирующие транзакции:** В Саге нет `ROLLBACK`. Есть только явная операция отмены (`refund`). Вы должны спроектировать её для каждого шага.

---

## 7.1. CQRS (Command Query Responsibility Segregation)

Разделение моделей чтения и записи.

* **Command Model (Write):** Нормализованная БД (3NF), строгая валидация, сложный ORM. Оптимизирована под запись и консистентность.
* **Query Model (Read):** Денормализованные "плоские" таблицы или вообще ElasticSearch/Redis. Оптимизирована под чтение (готовые DTO).

**Сценарий:** История операций в мобильном банке.

* Запись идет в мастер-систему (Core Banking, медленно).
* События перетекают в ElasticSearch.
* Клиент ищет "траты в Starbucks за год" мгновенно через Elastic.
* **Цена:** Eventual Consistency (данные в поиске могут отстать на секунду от реальности).

---

## 7.6. Resiliency (Устойчивость)

### Circuit Breaker (Предохранитель)

Паттерн для защиты от каскадных сбоев.
Если сервис "Кредитный скоринг" тормозит или сыпет 500-ками, нет смысла долбить его запросами.

**Библиотека: Resilience4j** (стандарт вместо Hystrix).

**Состояния:**

1. **CLOSED (Норма):** Ток идет, запросы проходят.
2. **OPEN (Разрыв):** Ошибки превысили порог (напр, 50% за 10 сек). Запросы **сразу** отбиваются ошибкой (Fast Fail), не нагружая мертвый сервис.
3. **HALF-OPEN:** Прошло время (sleep window). Пропускаем 1-2 пробных запроса. Если успех — переходим в CLOSED. Если нет — обратно в OPEN.

```java
@CircuitBreaker(name = "scoringService", fallbackMethod = "defaultScore")
public Score checkClient(String clientId) {
    return externalClient.getScore(clientId);
}

// Fallback: деградация функционала
// Если скоринг лежит, можем выдать базовый лимит или отказать
public Score defaultScore(String clientId, Throwable t) {
    log.warn("Scoring unavailable, using default", t);
    return Score.ZERO;
}

```

---

## 7.7. Cache (Redis Patterns)

В хайлоаде кэш — это не только ускорение, но и защита БД.

### Cache Stampede (Dogpile Effect)

Ситуация: Кэш для "курса валют" протух (TTL истек). В эту же секунду приходят 5000 запросов.
Все 5000 видят, что кэша нет, и ломятся в БД считать курс. БД падает.

**Решение 1: Вероятностный TTL (Jitter)**
Ставить TTL не ровно 10 минут, а `10 минут + random(0...60 сек)`. Тогда ключи будут протухать размазанно.

**Решение 2: Locking (Mutex)**
Если кэша нет, только **один** поток идет в БД, остальные ждут его результата (как в `loadingCache` Caffeine).

**Решение 3: Logical TTL vs Physical TTL**
Хранить в Redis объект вместе с полем `expireAt`.

* Физический TTL Redis ставим +1 час.
* Логический `expireAt` ставим +10 мин.
* Если клиент видит, что `expireAt` прошел — он отдает старое значение (stale), но **в фоне** запускает пересчет.

---

## Практическое задание (Mental Check)

Ты проектируешь систему переводов по номеру телефона (СБП).

1. **Outbox:** Почему нельзя просто кинуть сообщение в RabbitMQ после коммита транзакции? (Ответ: Если сервер упадет сразу после коммита, но до отправки в Rabbit, деньги спишутся, а перевод не уйдет. Consistency нарушена).
2. **Idempotency:** Получатель (Inbox) получил сообщение "Зачислить 100р" с ID=555. В базе уже есть запись с ID=555. Твои действия? (Ответ: Ничего не делать, вернуть успех `ack`. Это повторная доставка).
3. **Circuit Breaker:** Зачем он нужен, если есть таймауты? (Ответ: Таймаут все равно занимает время (поток ждет 2-3 сек). При 1000 RPS это убьет пул потоков вызывающего. CB отбивает мгновенно).

---

## 8.1. Spring Security Internals: The Filter Chain

Многие разработчики боятся Spring Security, потому что это "черный ящик", который либо работает, либо выдает 403 Forbidden.

На самом деле, Spring Security — это просто **цепочка Servlet-фильтров**.

### Как это работает:

1. Запрос приходит в Tomcat/Jetty.
2. Попадает в `DelegatingFilterProxy` (мост между Сервлетами и Спрингом).
3. Передается в `FilterChainProxy`.
4. Проходит через список `SecurityFilterChain`:
* **SecurityContextPersistenceFilter:** Пытается найти `SecurityContext` (например, в HTTP Session) и положить его в `ThreadLocal`.
* **UsernamePasswordAuthenticationFilter** (или **BearerTokenAuthenticationFilter**): Пытается извлечь креденшалы (логин/пароль или токен) и аутентифицировать пользователя.
* **ExceptionTranslationFilter:** Ловит `AccessDeniedException` и превращает его в 403 или редирект на логин.
* **FilterSecurityInterceptor:** Самый последний страж. Проверяет права доступа к конкретному URL.



### Конфигурация в Spring Boot 3 (Lambda DSL)

Забудьте про `extends WebSecurityConfigurerAdapter`. Он удален. Теперь мы объявляем бин `SecurityFilterChain`.

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf(AbstractHttpConfigurer::disable) // Для REST API (stateless) отключаем
            .sessionManagement(session -> 
                session.sessionCreationPolicy(SessionCreationPolicy.STATELESS)) // Никаких JSESSIONID
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/public/**", "/actuator/health").permitAll()
                .requestMatchers("/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            )
            .oauth2ResourceServer(oauth2 -> oauth2.jwt(Customizer.withDefaults()));
            
        return http.build();
    }
}

```

---

## 8.2. OAuth2 / OIDC: Современный стандарт

В энтерпрайзе больше не используют Basic Auth. Мы используем токены.

### Роли:

1. **Resource Server:** Ваш микросервис. Он хранит данные, но не умеет проверять пароли. Он доверяет токенам.
2. **Authorization Server (IdP):** Keycloak, Okta, Active Directory. Он хранит пользователей, проверяет пароли и выдает токены.
3. **Client:** Фронтенд или мобильное приложение.

### JWT (JSON Web Token) — Анатомия

Токен состоит из трех частей: `Header.Payload.Signature`.

* **Header:** Алгоритм (RS256).
* **Payload (Claims):** Полезная нагрузка.
* `sub` (Subject): ID пользователя.
* `exp` (Expiration): Когда протухнет.
* `iss` (Issuer): Кто выдал (http://keycloak...).
* `aud` (Audience): Для кого этот токен.
* `scope`: Права (read, write).


* **Signature:** Цифровая подпись сервера авторизации.

### Валидация токена (Stateless vs Stateful)

**Stateless (Offline validation):**
Сервис скачивает публичный ключ (JWK) с сервера авторизации (обычно при старте или раз в сутки). При получении токена сервис сам проверяет подпись, используя этот ключ.

* **Плюс:** Быстро (нет сетевого похода в Keycloak на каждый запрос).
* **Минус:** Нельзя мгновенно отозвать токен (revoke). Если токен украли, он валиден до конца `exp`. (Решается коротким временем жизни токена — 5-15 минут).

**Stateful (Introspection):**
На каждый запрос сервис ходит в Keycloak (`/introspect`), посылая токен, и спрашивает: "Он жив?".

* **Плюс:** Мгновенный отзыв.
* **Минус:** Огромная нагрузка на Keycloak и latency сети. Используется редко.

---

## 8.3. Method Security (`@PreAuthorize`)

Защита URL (`.requestMatchers("/api/**")`) — это грубая защита периметра (защита от дурака). Настоящая безопасность живет на уровне методов бизнес-логики.

Включаем: `@EnableMethodSecurity` (в Boot 3 заменяет `@EnableGlobalMethodSecurity`).

### ABAC (Attribute-Based Access Control)

Частый кейс: "Юзер может смотреть документы, но только **свои**". Роли `ROLE_USER` здесь недостаточно.

```java
@Service
public class DocumentService {

    // SpEL: "principal" - это текущий залогиненный юзер (из SecurityContext)
    // #documentId - аргумент метода
    @PreAuthorize("hasRole('ADMIN') or @docSecurity.isOwner(authentication, #documentId)")
    public Document getDocument(Long documentId) {
        return repository.findById(documentId);
    }
}

@Component("docSecurity")
public class DocumentSecurity {
    public boolean isOwner(Authentication auth, Long docId) {
        String currentUserId = auth.getName(); // sub из JWT
        String ownerId = documentRepository.getOwnerId(docId);
        return currentUserId.equals(ownerId);
    }
}

```

---

## 8.4. AppSec: Secure by Default

### CORS (Cross-Origin Resource Sharing)

Кошмар фронтендеров. Браузер блокирует запросы с `localhost:3000` на `api.bank.com`.

* **Ошибка:** `Access-Control-Allow-Origin: *`. Это дыра. Любой вредоносный сайт может дергать ваш API от лица пользователя.
* **Правильно:** Явно перечислять разрешенные домены фронтенда.

### CSRF (Cross-Site Request Forgery)

Атака, когда пользователь заходит на `evil.com`, а там скрытый скрипт отправляет POST-запрос на `bank.com/transfer`.

* **Если у вас REST API на JWT:** CSRF можно **выключить**. Браузер не подставляет JWT автоматически (в отличие от Cookies), поэтому атака невозможна (если вы не храните JWT в куках, конечно).
* **Если у вас MVC с сессиями (Cookies):** CSRF **обязателен**. Spring Security генерирует CSRF-токен, который фронт должен прикладывать к запросам.

### Secrets Management

Где хранить пароль от БД?

* `application.yaml` в Git? **Увольнение.**
* Environment Variables? Лучше, но видно в `docker inspect`.
* **Vault / AWS Secrets Manager / K8s Secrets:** Стандарт. Приложение при старте идет в Vault, аутентифицируется (через K8s Service Account) и забирает пароли прямо в память. На диске их нет.

---

## Практическое задание (Mental Check)

1. **Сценарий:** Вы используете JWT. Как реализовать кнопку "Выйти" (Logout)?
* *Ответ:* На сервере — никак (мы stateless). "Выход" — это просто удаление токена на клиенте (из LocalStorage). Чтобы реально запретить доступ до истечения срока действия токена, нужно внедрять "Blacklist" в Redis, но это убивает идею stateless.


2. **Сценарий:** Аудитор нашел у вас зависимость `log4j` старой версии. Что делать?
* *Ответ:* Это Supply Chain атака. Срочно обновить зависимость (Maven/Gradle) или использовать патчи. В CI/CD должен стоять сканер уязвимостей (Trivy, Snyk, Dependency Check), который ломает билд, если найдена CVE.


3. **Вопрос:** Чем `@PreAuthorize` отличается от `@PostAuthorize`?
* *Ответ:* `@Pre` проверяет права **до** вызова метода (экономит ресурсы). `@Post` выполняет метод, а потом решает, можно ли отдать результат (нужно, когда право доступа зависит от возвращаемых данных, которые мы еще не знаем до вызова).



---

## 9.1. Spring Boot Actuator: Пульс приложения

Actuator добавляет в приложение "админку" для машин.

### Liveness vs Readiness Probes (Для Kubernetes)

Это критически важно для работы в K8s. Actuator отдает их по `/actuator/health/liveness` и `/readiness`.

1. **Liveness (Я жив?):**
* **Вопрос:** "Нужно ли меня перезагрузить?"
* **Логика:** Если приложение зависло (Deadlock), упал главный цикл — проба падает. K8s убивает под и запускает новый.
* **Ошибка:** Проверять здесь доступность БД. Если БД легла, и все поды ответят "Я умер", K8s перезагрузит весь кластер, но это не починит БД. Циклическая смерть.


2. **Readiness (Готов работать?):**
* **Вопрос:** "Можно ли на меня слать трафик?"
* **Логика:** Приложение стартует, прогревает кэш, устанавливает соединения. Пока проба `false`, K8s не шлет сюда запросы (убирает из Service Endpoints).
* **Кейс:** Если сервис перегружен (Circuit Breaker открыт), можно временно уронить Readiness, чтобы балансировщик перестал слать сюда нагрузку (Backpressure).



### Security Warning

Никогда не открывайте всё (`management.endpoints.web.exposure.include=*`) в интернет.

* `/heapdump` — позволит скачать память приложения (с паролями и данными клиентов).
* `/env` — покажет переменные окружения.

---

## 9.2. Structured Logging & MDC

`System.out.println` — это для школы. В проде логи должны быть машиночитаемыми.

### JSON Logs

Вместо текстовой строки:
`2023-10-05 12:00 ERROR [main] User failed to login: admin`

Мы пишем JSON (через Logstash Logback Encoder):

```json
{
  "@timestamp": "2023-10-05T12:00:00.123Z",
  "level": "ERROR",
  "logger": "com.bank.auth.Service",
  "message": "User failed to login",
  "user_id": "admin", 
  "error_code": "AUTH_001",
  "trace_id": "64c3f5..."
}

```

**Зачем:** ELK (Elasticsearch, Logstash, Kibana) или Splunk могут мгновенно фильтровать по полю `user_id`. Грепать текст на гигабайтах логов — невозможно.

### MDC (Mapped Diagnostic Context)

В многопоточной среде (где один поток обслуживает 100 разных клиентов по очереди) логи превращаются в кашу.
MDC — это `Map<String, String>`, привязанная к `ThreadLocal`.

```java
// В фильтре или интерсепторе
try {
    MDC.put("requestId", request.getHeader("X-Request-ID"));
    MDC.put("userId", token.getSub());
    
    chain.doFilter(request, response);
} finally {
    MDC.clear(); // Обязательно чистить, иначе поток вернется в пул "грязным"
}

// В глубине бизнес-логики (не нужно передавать userId аргументом!)
log.info("Processing transaction"); 
// В логе автоматически будет: "Processing transaction {userId=123, requestId=abc}"

```

---

## 9.3. Metrics (Micrometer)

Метрики — это дешево. Вы можете хранить миллионы точек данных, так как это просто числа.
**Micrometer** — это фасад (как SLF4J), который отправляет метрики в Prometheus, Datadog или Graphite.

### Типы метрик

1. **Counter (Счетчик):** Только растет. "Количество запросов", "Количество ошибок".
2. **Gauge (Датчик):** Может расти и падать. "Размер очереди", "Количество потоков", "Температура CPU".
3. **Timer:** Самый важный. Измеряет частоту событий И их длительность (распределение).

### Dimensional Metrics (Теги)

Анти-паттерн (Взрыв кардинальности):

```java
// ПЛОХО: Создает новую метрику на каждый ID
registry.counter("orders.created." + userId).increment(); 

```

Если у вас 1 млн юзеров, Prometheus упадет от 1 млн метрик.

Правильно (Теги):

```java
// ХОРОШО: Одна метрика, разные теги
registry.counter("orders.created", "region", "RU", "status", "VIP").increment();

```

Потом в Grafana вы пишете query: `sum(orders_created) by (region)`.

### The Golden Signals (Что мониторить в первую очередь)

Если у вас всего 4 графика на дашборде, это должны быть:

1. **Latency:** Время ответа (p50, p99).
2. **Traffic:** RPS (запросов в секунду).
3. **Errors:** Процент ошибок (5xx кодов).
4. **Saturation:** Насколько система полна (размер пула потоков, утилизация CPU, память).

---

## 9.4. Distributed Tracing (OpenTelemetry)

Когда микросервис А вызывает Б, а тот вызывает В, и где-то возникли тормоза — логи не помогут (каждый сервис пишет в свой файл).

Нужен **Трейсинг**.

* **Trace ID:** Глобальный ID всего запроса. Генерируется на входе (Ingress/Gateway).
* **Span ID:** ID конкретного этапа (вызов метода, SQL запрос).

**Как это работает:**

1. Spring (через Micrometer Tracing / Sleuth) автоматически добавляет заголовки в HTTP-запросы (`traceparent` или `X-B3-TraceId`).
2. Логи также обогащаются Trace ID.
3. Асинхронно данные отправляются в Jaeger или Zipkin.

**Практика:**
Увидев в Jaeger длинную полоску (Span), вы понимаете: "Ага, сервис А ждал сервис Б 5 секунд, потому что сервис Б делал тяжелый SQL запрос".

---

## 9.5. SLO / SLI (Культура надежности)

Это уже не код, а соглашение с бизнесом.

* **SLI (Indicator):** Что меряем? (Пример: "Количество успешных HTTP 200 ответов на `/api/pay`").
* **SLO (Objective):** Цель. (Пример: "99.9% запросов за месяц должны быть успешными").
* **Error Budget:** Право на ошибку. Если у нас 99.9% доступность, значит, мы можем лежать 43 минуты в месяц.
* Если бюджет исчерпан — **Freeeze**. Никаких новых фич, команда чинит техдолг и стабильность.



---

## Практическое задание (Mental Check)

1. **Ситуация:** Графана показывает, что heap memory пилообразно растет и падает (норма), но общий уровень медленно ползет вверх каждый день.
* **Диагноз:** Memory Leak. Скорее всего, кто-то складывает объекты в `static Map` или не закрывает ThreadLocal. Нужен Heap Dump.


2. **Ситуация:** Вы хотите замерить время выполнения метода. Что лучше: `System.currentTimeMillis()` в лог или `Timer` в метрики?
* **Ответ:** `Timer`. Логи дороги (I/O диска), их сложно агрегировать ("покажи среднее за час"). Метрики созданы для агрегации.


3. **Логи:** Почему нельзя логировать полный JSON запроса в `INFO`?
* **Ответ:**
1. Безопасность (там могут быть пароли/PII).
2. Объем (забьете диск логами за час при высокой нагрузке).
3. Производительность (сериализация объекта в String стоит CPU).





---

## 10.1. Unit Testing: Честность и скорость

Юнит-тесты должны отрабатывать за миллисекунды. Если ваш юнит-тест идет в базу или поднимает Spring Context — это не юнит-тест.

### Mockito: Strictness & BDD

Современный стиль — это BDD (Behavior Driven Development) через `Mockito` и `AssertJ`.

**Анти-паттерн:** Мокать всё подряд.
Если вы тестируете `UserService`, и он вызывает `HelperUtils.formatDate()`, не надо мокать утилиту. Мокайте только внешние зависимости (IO, Repository, API Clients).

```java
@ExtendWith(MockitoExtension.class) // JUnit 5
class OrderServiceTest {

    @Mock
    private OrderRepository orderRepository; // Мок (пустышка)

    @InjectMocks
    private OrderService orderService; // Сюда внедрятся моки

    @Test
    void shouldCreateOrder_whenStockIsAvailable() {
        // GIVEN (Подготовка)
        var request = new OrderRequest("item-1", 5);
        // Обучаем мок: при вызове верни true
        given(orderRepository.hasStock("item-1", 5)).willReturn(true);
        // Обучаем мок: при сохранении верни объект с ID
        given(orderRepository.save(any(Order.class)))
            .willAnswer(invocation -> {
                Order ord = invocation.getArgument(0);
                ord.setId(123L);
                return ord;
            });

        // WHEN (Действие)
        Long orderId = orderService.createOrder(request);

        // THEN (Проверка)
        assertThat(orderId).isEqualTo(123L);
        
        // Verify: Убеждаемся, что метод save был вызван ровно 1 раз
        then(orderRepository).should(times(1)).save(any(Order.class));
    }
}

```

### Mutation Testing (PITest)

**Code Coverage — это метрика тщеславия.**
Я могу написать тест без `assert`, и покрытие будет 100%, но тест ничего не проверяет.

**Mutation Testing** — это "тест для тестов".

1. PITest запускает тесты.
2. Меняет байт-код вашего приложения (мутации): меняет `if (a > b)` на `if (a >= b)`, `return true` на `return false`.
3. Запускает тесты снова.
4. Если тесты **прошли** (зеленые) на сломанном коде — значит, тесты плохие (мутант выжил).
5. Если тесты **упали** — отлично, вы поймали изменение логики.

В банках это часто запускают на критичных модулях (расчет процентов, скоринг).

---

## 10.2. Spring Test Slices (Срезы)

Поднимать полный `@SpringBootTest` (со всеми конфигами, Kafka, Redis) долго (10-20 секунд).
Spring предлагает "слайсы" — подъем только части контекста.

### `@WebMvcTest` (Только контроллеры)

Не грузит сервис-слой и репозитории.

```java
@WebMvcTest(UserController.class)
class UserControllerTest {

    @Autowired MockMvc mockMvc; // Эмулятор HTTP вызовов
    @MockBean UserService userService; // Заменяем реальный сервис моком

    @Test
    void shouldReturnUser() throws Exception {
        given(userService.findById(1L)).willReturn(new UserDto("John"));

        mockMvc.perform(get("/users/1"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.name").value("John"));
    }
}

```

### `@DataJpaTest` (Только БД)

Грузит Hibernate, DataSource, Repositories.

* **По умолчанию:** Пытается поднять In-Memory H2 базу.
* **В Enterprise:** Мы отключаем H2 и используем Testcontainers, чтобы тестировать на честном Postgres.

---

## 10.3. Integration Testing & Testcontainers (Золотой стандарт)

**H2 is a lie.**
H2 не поддерживает `jsonb`, специфичные диалекты Postgres, хранимые процедуры. Тесты на H2 проходят, а на проде падают.

**Testcontainers** — это Java-библиотека, которая запускает настоящий Docker-контейнер с базой (или Kafka/Redis) на время тестов.

### Singleton Container Pattern (Оптимизация)

Поднимать контейнер Postgres (3-5 секунд) на каждый тест-класс — слишком долго.
Делаем абстрактный класс, который поднимает контейнер **один раз** на весь прогон тестов.

```java
@ActiveProfiles("test")
@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
public abstract class AbstractIntegrationTest {

    static final PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:15-alpine");

    static {
        postgres.start(); // Старт до контекста Спринга
    }

    // Динамически подменяем проперти подключения
    @DynamicPropertySource
    static void configureProperties(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", postgres::getJdbcUrl);
        registry.add("spring.datasource.username", postgres::getUsername);
        registry.add("spring.datasource.password", postgres::getPassword);
    }
}

```

Теперь все интеграционные тесты наследуются от этого класса.

**Что тестируем здесь:**

* Сложные SQL запросы.
* Миграции Flyway/Liquibase (проходят ли они на чистой базе).
* Взаимодействие компонентов.

---

## 10.4. ArchUnit: Тестируем Архитектуру

В больших командах архитектура "плывет". Джуниор может заинжектить `Repository` прямо в `Controller`, минуя `Service`. Или дернуть класс из пакета `internal`.

**ArchUnit** — это юнит-тесты, которые проверяют структуру кода.

```java
@AnalyzeClasses(packages = "com.bank.app")
public class ArchitectureTest {

    @ArchTest
    static final ArchRule layers_should_be_respected = layeredArchitecture()
        .consideringOnlyDependenciesInAnyPackage("com.bank.app..")
        .layer("Controller").definedBy("..controller..")
        .layer("Service").definedBy("..service..")
        .layer("Persistence").definedBy("..repository..")

        .whereLayer("Controller").mayNotBeAccessedByAnyLayer()
        .whereLayer("Service").mayOnlyBeAccessedByLayers("Controller")
        .whereLayer("Persistence").mayOnlyBeAccessedByLayers("Service");
        
    @ArchTest
    static final ArchRule no_cycles = slices().matching("com.bank.app.(*)..")
        .should().beFreeOfCycles();
}

```

Если кто-то нарушит правило слоев — билд упадет.

---

## 10.5. Dirty Context (Боль Спринга)

Spring Test Framework кэширует контекст приложения. Если вы запустили 100 тестов с одинаковой конфигурацией, контекст поднимется 1 раз.

**Враг кэша:** `@MockBean`.
Каждый раз, когда вы используете `@MockBean` в тестовом классе, вы **меняете** контекст (добавляете в него мок). Spring не может переиспользовать старый контекст и вынужден создавать новый.

* **Результат:** Время прогона тестов растет экспоненциально.
* **Решение:** Старайтесь выносить конфигурацию моков в общие конфиги или использовать `@TestConfiguration`, если возможно.

---

## Практическое задание (Mental Check)

1. **Задача:** Тестируем метод, который отправляет сообщение в Kafka.
* *Unit:* Мокаем `KafkaTemplate`. Проверяем `verify(template).send(...)`. (Быстро, но не гарантирует, что сериализация JSON верна).
* *Integration:* Используем `KafkaContainer` (Testcontainers). Реально отправляем сообщение и читаем его консьюмером в тесте. (Медленно, но надежно).


2. **Проблема:** Тест падает только в 3 часа ночи.
* *Причина:* Использование `new Date()` или `LocalDateTime.now()` в логике.
* *Решение:* Никогда не используйте `now()` в коде напрямую. Внедряйте `Clock` (java.time.Clock). В тестах вы сможете подсунуть `Clock.fixed()`.


3. **Вопрос:** Зачем нужен `@Transactional` над тестовым методом в интеграционном тесте?
* *Ответ:* Чтобы после выполнения теста Spring автоматически сделал **Rollback**. База останется чистой для следующего теста. (Осторожно: это не сработает, если тестируемый код запускает транзакции в *новых* потоках или использует `REQUIRES_NEW`).



---

## 11.1. Build Systems (Maven/Gradle): Гигиена зависимостей

Сборщик — это не просто "скомпилировать классы". Это управление хаосом транзитивных зависимостей.

### BOM (Bill of Materials)

Никогда не указывайте версии Spring-библиотек вручную.
Используйте **BOM** (`spring-boot-dependencies`). Это гарантирует, что `spring-web` и `spring-security` будут совместимых версий.

### Maven Enforcer Plugin

В банке этот плагин обязателен. Он ломает билд, если правила нарушены.

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-enforcer-plugin</artifactId>
    <executions>
        <execution>
            <id>enforce-rules</id>
            <goals><goal>enforce</goal></goals>
            <configuration>
                <rules>
                    <requireReleaseDeps>
                        <onlyWhenRelease>true</onlyWhenRelease>
                    </requireReleaseDeps>
                    <dependencyConvergence/>
                    <requireJavaVersion><version>17</version></requireJavaVersion>
                </rules>
            </configuration>
        </execution>
    </executions>
</plugin>

```

### Reproducible Builds (Воспроизводимые сборки)

Если вы собрали JAR сегодня и завтра из одного коммита — **хэш-сумма файла должна совпадать**.
Это защита от подмены компилятора. В Maven это достигается фиксацией таймстемпов файлов внутри JAR (`project.build.outputTimestamp`).

---

## 11.2. Docker: The Java Way

Упаковать Java в Docker можно "как попало" (COPY target/*.jar app.jar) и "правильно".

### Multi-stage builds

Мы не тащим исходники и Maven (со всем кэшем `.m2`) в финальный образ.

```dockerfile
# Stage 1: Build
FROM maven:3.9-eclipse-temurin-17 AS builder
WORKDIR /app
COPY pom.xml .
COPY src ./src
# Сборка без запуска тестов (тесты прошли на шаге CI)
RUN mvn clean package -DskipTests

# Stage 2: Runtime
# Используем минимальный JRE (не JDK!), часто Distroless или Alpine
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
# Создаем пользователя (security requirement - не запускать под root!)
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

COPY --from=builder /app/target/myapp.jar app.jar

ENTRYPOINT ["java", "-jar", "/app/app.jar"]

```

### Layered JARs (Слои)

Spring Boot JAR весит 100 МБ. Из них 99 МБ — это библиотеки (dependencies), которые не меняются месяцами. И 1 МБ — ваш код.
Если менять 1 строку кода, Docker пересобирает весь слой в 100 МБ. Это медленно и забивает Registry.

**Решение:** Spring Boot умеет распаковывать JAR на слои (`dependencies`, `spring-boot-loader`, `application`).
В Dockerfile мы копируем их по отдельности. Если код изменился, Docker перекачает только верхний слой (1 МБ).

### Distroless Images

Google Distroless образы (`gcr.io/distroless/java17`) не содержат шелла (`/bin/sh`), пакетного менеджера (`apk`/`apt`).

* **Плюс:** Если хакер взломает приложение (RCE), он не сможет запустить `curl` или майнер, потому что их там просто нет.
* **Минус:** Вы не сможете зайти в контейнер через `docker exec -it ... bash` для отладки. (Для отладки используют эфемерные debug-контейнеры).

---

## 11.3. Supply Chain Security (Безопасность поставки)

Это горячая тема после взлома SolarWinds и Log4Shell. Банки требуют доказательств, что код не подменили по пути.

### SBOM (Software Bill of Materials)

Это "паспорт" вашего приложения. Список всех библиотек, их версий и лицензий в формате JSON (CycloneDX или SPDX).

* Генерируется плагином Maven во время сборки.
* Загружается в систему анализа (Dependency Track), которая кричит, если в `jackson-databind` нашли новую дыру.

### Container Signing (Cosign / Sigstore)

1. CI собирает Docker-образ.
2. CI подписывает образ своим приватным ключом.
3. Kubernetes (через Admission Controller, например Kyverno) проверяет подпись перед запуском. Если подписи нет или она левая — деплой блокируется.

---

## 11.4. Kubernetes для Разработчика

Вам не нужно быть админом K8s, но вы должны понимать, как конфигурировать свой Pod.

### Resources: Requests vs Limits

Самая частая причина аварий — неправильная работа с памятью JVM в контейнере.

```yaml
resources:
  requests:
    memory: "512Mi" # Гарантировано выделено
    cpu: "500m"     # 0.5 ядра
  limits:
    memory: "1Gi"   # Если съест больше - OOMKilled
    cpu: "1000m"    # Если съест больше - Throttling (тормоза)

```

**JVM Flags:**
JVM до 10-й версии плохо понимала лимиты контейнера и видела всю память хоста (64 ГБ), пытаясь отъесть кусок от неё.
Современная Java (17+) умная, но ей надо помочь:
`-XX:MaxRAMPercentage=75.0`
Это значит: "Используй под Heap 75% от лимита контейнера (limits.memory)". Остальные 25% нужны под Metaspace, Stack, Native Memory и сам оверхед контейнера.
**Ошибка:** Ставить `-Xmx` жестко (например `-Xmx1G`). Если вы измените лимит в K8s, вам придется менять и флаг. Проценты гибче.

### Graceful Shutdown (Мягкая остановка)

Когда K8s убивает под (деплой новой версии или скейлинг вниз), он шлет сигнал `SIGTERM`.

1. Приложение перестает принимать трафик.
2. Приложение дорабатывает текущие запросы.
3. Если не успело за 30 сек — прилетает `SIGKILL`.

В Spring Boot это включается одной строкой:
`server.shutdown=graceful`

В K8s нужно добавить `preStop` hook, чтобы дать балансировщику время обновить списки IP-адресов:

```yaml
lifecycle:
  preStop:
    exec:
      command: ["/bin/sh", "-c", "sleep 10"]

```

Иначе клиенты получат 502 Bad Gateway в момент деплоя.

---

## 11.5. CI/CD Pipeline Logic

Типовой пайплайн в банке выглядит так (Quality Gates):

1. **Checkout & Build:** Компиляция, Unit-тесты.
2. **Static Analysis (SAST):** SonarQube (качество кода), Checkmarx (уязвимости в коде). Если Critical Vulnerability — стоп.
3. **Dependency Scan (SCA):** Проверка библиотек (CVE).
4. **Container Build:** Сборка Docker образа + SBOM.
5. **Container Scan:** Trivy/Clair (поиск дыр в базовом образе Linux).
6. **Deploy to Dev/Test:** Выкатка.
7. **Integration Tests:** Запуск API-тестов против живого стенда.
8. **Promote to Prod:** Ручное подтверждение или авто-деплой (GitOps/ArgoCD).

---

## Практическое задание (Mental Check)

1. **Проблема:** Приложение в K8s постоянно перезагружается с ошибкой `OOMKilled`.
* **Диагностика:** Вы поставили `-Xmx` равным `limits.memory`. JVM заняла весь лимит под Heap, а на Metaspace и потоки памяти не хватило. Kernel убил процесс.
* **Решение:** Уменьшить Heap (MaxRAMPercentage=75) или увеличить лимит контейнера.


2. **Проблема:** При нагрузке приложение начинает адски тормозить, но CPU на графиках всего 100% (от лимита).
* **Диагностика:** **CPU Throttling**. Контейнер уперся в лимит, и планировщик Linux просто не дает ему процессорное время. GC не успевает работать.
* **Решение:** Увеличить CPU limits или оптимизировать код.


3. **Безопасность:** Зачем запускать Java процесс от отдельного юзера (`USER appuser`), а не от root?
* **Ответ:** Если в Java найдут RCE уязвимость (удаленное выполнение кода), хакер получит права того пользователя, под которым запущено приложение. Если это root — он может вырваться из контейнера на хост-систему (Container Escape).



---

## 12.1. DDD (Domain-Driven Design): Основы

DDD — это не про папки в проекте. Это про то, как перенести ментальную модель банкира в Java-код с минимальными искажениями.

### Ubiquitous Language (Единый язык)

Если эксперт говорит "Сторнировать транзакцию", а в коде у тебя `deleteTransaction()`, — это баг дизайна.

* **Правило:** Код должен говорить на языке бизнеса. Классы должны называться `Reversal`, `Accrual`, `LoanApplication`.

### Bounded Contexts (Ограниченные контексты)

Самая важная концепция для микросервисов.
Что такое "Клиент" (`Customer`)?

* В **Контексте продаж:** Это Лид (имя, телефон, интерес).
* В **Контексте счетов:** Это владелец счета (KYC статус, паспорт, список счетов).
* В **Контексте поддержки:** Это Тикет (история обращений).

**Ошибка:** Делать одного гигантского монстра `User`, у которого 200 полей для всех контекстов сразу.
**Решение:** Разные модели для разных контекстов. В микросервисах это разные сервисы. В монолите — разные пакеты, которые не видят друг друга.

### Aggregate (Агрегат) — Граница транзакции

Агрегат — это кластер объектов, которые можно менять **только целиком**.

* Пример: `Order` (Заказ) и `OrderItem` (Позиция).
* **Правило:** Нельзя загрузить `OrderItem` отдельно от `Order`, изменить его цену и сохранить. Вы сломаете инвариант (общая сумма заказа не сойдется).
* Вы загружаете Агрегат целиком, меняете его состояние, и сохраняете целиком.

---

## 12.2. Hexagonal Architecture (Ports & Adapters)

Классическая "Слоистая архитектура" (Controller -> Service -> Repository -> Entity) имеет фатальный недостаток: **Зависимость от Базы Данных**.
Весь ваш бизнес-код пропитан аннотациями `@Entity`, `@Table`, и ленивой загрузкой. Если вы захотите сменить БД или сделать тесты без БД — будет больно.

**Гексагональная архитектура переворачивает зависимости.**

### Принцип:

1. **Domain (Core):** В центре. Чистая Java. Никакого Spring, никакого Hibernate, никакого JSON. Только бизнес-логика.
2. **Ports (Интерфейсы):** Домен объявляет, что ему нужно ("Мне нужно уметь сохранять счета"). Это Java-интерфейс `SaveAccountPort`.
3. **Adapters (Инфраструктура):**
* **Driving (Входящие):** REST Controller, Kafka Listener. Они вызывают Домен.
* **Driven (Исходящие):** Postgres Adapter, Redis Adapter. Они реализуют интерфейсы Портов.



**Код (Инверсия зависимостей):**

```java
// --- DOMAIN LAYER (Core) ---
// Чистая бизнес-логика
public class AccountService {
    private final LoadAccountPort loadPort; // Интерфейс!
    private final SaveAccountPort savePort; // Интерфейс!

    public void deposit(Long id, BigDecimal amount) {
        Account acc = loadPort.load(id);
        acc.deposit(amount);
        savePort.save(acc);
    }
}

// --- INFRASTRUCTURE LAYER (Adapter) ---
// Реализация "грязных" деталей. Домен о них не знает.
@Component
class JpaAccountAdapter implements LoadAccountPort, SaveAccountPort {
    private final SpringDataAccountRepository repo;

    @Override
    public Account load(Long id) {
        return mapToDomain(repo.findById(id));
    }
}

```

---

## 12.3. Spring Modulith: Модульный Монолит

Микросервисы — это сложно (сеть, транзакции, деплой). Часто лучше начать с Монолита, но **правильного**.

**Spring Modulith** — это библиотека от команды Spring, которая помогает строить модули внутри одного JAR, запрещая им "спагетти-связи".

### Структура пакетов:

* `com.bank.inventory` (Модуль)
* `com.bank.order` (Модуль)

Если `Order` захочет дернуть класс из `com.bank.inventory.internal`, билд упадет. Доступ разрешен только к классам в корне пакета (API модуля).

### Event-Driven interaction

Модули общаются через Spring Events (`ApplicationEventPublisher`).

* `Order` модуль публикует `OrderCompletedEvent`.
* `Inventory` модуль слушает его (`@ApplicationModuleListener`) и списывает товар.
* **Магия:** Если транзакция упадет, событие не потеряется (благодаря встроенному Outbox pattern в Spring Modulith).

---

## 12.4. Documentation as Code (ADR)

Архитектура — это набор принятых решений. Почему мы выбрали Kafka, а не RabbitMQ? Почему PostgreSQL, а не Mongo?
Через год никто не вспомнит.

**ADR (Architecture Decision Record)** — это markdown-файлы, которые лежат в git-репозитории рядом с кодом (`/docs/adr/`).

**Формат (Пример):**

* **Title:** ADR-001: Использование UUID v7 для первичных ключей.
* **Status:** Accepted.
* **Context:** Нам нужно сливать данные из разных шардов, int sequence дает коллизии. UUID v4 дает плохую производительность B-Tree индексов из-за рандома.
* **Decision:** Используем UUID v7 (сортируемый по времени).
* **Consequences:** Нужно обновить версию драйвера JDBC. Читаемость ключей в логах ухудшится (длинные строки).

Это делает онбординг новых разработчиков мгновенным. "Почему тут так сделано?" — "Читай ADR-005".

---

## 12.5. Главные Анти-паттерны

1. **Distributed Monolith (Распределенный монолит):**
* Вы разбили сервис на 10 микросервисов.
* Но чтобы выполнить один запрос, сервис А вызывает Б, тот вызывает В, тот Г.
* Если упал Г — лежит всё. Latency суммируется.
* **Лечение:** Асинхронность, Event-Driven, репликация данных.


2. **Shared Database (Общая база):**
* Три микросервиса ходят в одну схему БД и дергают одни таблицы.
* **Почему плохо:** Вы не можете поменять структуру таблицы в сервисе А, не сломав сервис Б. Любая миграция — боль.
* **Лечение:** Database per Service (или хотя бы Schema per Service).


3. **Anemic Domain Model (Анемичная модель):**
* Классы `Entity` — это просто мешки для данных (Getters/Setters).
* Вся логика (`if`, проверки, расчеты) лежит в классах `Service`.
* **В DDD:** Логика должна быть внутри сущностей. `Account.debit(amount)` должен сам проверить баланс и выкинуть исключение, а не Сервис.



---

## 13.1. Spring Cloud Gateway (API Gateway)

Если у вас 50 микросервисов, фронтенд не должен знать IP-адреса каждого из них. Ему нужна единая точка входа.
Zuul 1.x умер (он был блокирующим). Spring Cloud Gateway — это стандарт, построенный на **Project Reactor (WebFlux)**.

### Ключевые фичи для банка:

1. **Route Predicates (Маршрутизация):**
* "Если путь начинается с `/api/v1/credits` -> отправь в сервис `credit-core`".
* "Если заголовок `X-Beta-User: true` -> отправь в сервис `credit-core-v2` (Canary deploy)".


2. **Global Filters:**
* **Rate Limiting (Redis):** Ограничить клиента 10 запросами в секунду, чтобы не за-DDoS-ил.
* **Security:** Валидация JWT токена *до* того, как запрос попадет в микросервис (разгрузка сервисов).
* **Correlation ID:** Генерация `Trace-ID`, если его нет, и проброс дальше.


3. **Circuit Breaker Integration:**
* Если сервис `credit-core` лежит, Gateway может сразу отдать заглушку (Fallback JSON), не заставляя клиента ждать таймаута.



**Код (YAML config):**

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: credit_service
          uri: lb://credit-service # Load Balancer (клиентский)
          predicates:
            - Path=/api/credits/**
          filters:
            - StripPrefix=1
            - name: RequestRateLimiter
              args:
                redis-rate-limiter.replenishRate: 10
                redis-rate-limiter.burstCapacity: 20

```

## 13.2. Spring Cloud Config (Externalized Configuration)

Даже в эпоху K8s ConfigMaps, Spring Cloud Config Server остается популярным в банках.
**Причина:** Audit Log.
K8s ConfigMap — это просто состояние. Spring Cloud Config смотрит в **Git**.

* Вы видите `git blame`: Кто изменил таймаут? Когда? В каком коммите? Это требование безопасности.

### @RefreshScope

Киллер-фича. Позволяет менять настройки **без рестарта** приложения.

1. Меняем конфиг в Git.
2. Шлем POST запрос `/actuator/refresh` (или через Spring Cloud Bus + Kafka всем сразу).
3. Бины с аннотацией `@RefreshScope` пересоздаются с новыми настройками.
* *Кейс:* Включить `DEBUG` логирование на 5 минут во время инцидента и выключить обратно.



## 13.3. Spring Cloud Kubernetes

Если вы решили, что Config Server — это оверхед, и хотите жить "нативно" в K8s.

1. **Discovery:** Реализация `DiscoveryClient`, которая ходит в K8s API сервер и получает список IP-адресов подов по имени сервиса. (Альтернатива DNS).
2. **Config:** Умеет мапить K8s `ConfigMap` и `Secret` прямо в Spring `Environment`.
* Если `ConfigMap` изменился, Spring Cloud Kubernetes может поймать событие и сделать горячую перезагрузку бинов (аналог `@RefreshScope`).



## 13.4. Spring Cloud Stream (Event Driven Abstraction)

Мы говорили про Kafka в Модуле 6. Spring Cloud Stream — это абстракция над брокерами (Kafka, RabbitMQ, Pulsar).

### Зачем это нужно?

В теории — чтобы легко менять брокеров. На практике в банке брокера меняют раз в 10 лет.
**Реальная польза:** Удобная программная модель (Functional Style).

Вместо возни с `KafkaTemplate` и настройками сериализации, вы пишите простые Java Functions:

```java
@Configuration
public class StreamConfig {

    // Consumer: Читает транзакцию, ничего не возвращает
    @Bean
    public Consumer<Transaction> processTransaction() {
        return tx -> {
            System.out.println("Processing: " + tx);
        };
    }

    // Processor: Читает заказ, возвращает событие "Заказ проверен"
    @Bean
    public Function<Order, OrderEvent> validateOrder() {
        return order -> {
            return new OrderEvent(order.id(), "VALIDATED");
        };
    }
}

```

Все настройки (какой топик, какая группа, DLQ) выносятся в `application.yaml`. Это делает код чистым и легко тестируемым (можно подсунуть заглушку вместо Kafka).

## 13.5. Spring Cloud LoadBalancer (Client-Side LB)

Netflix Ribbon **мертв** (Maintenance mode).
Его замена — **Spring Cloud LoadBalancer**.

Когда вы используете `FeignClient` или `WebClient` с аннотацией `@LoadBalanced`:

1. Он спрашивает Discovery (K8s или Eureka): "Дай мне список IP для сервиса `account-service`".
2. Получает: `[10.0.1.5, 10.0.1.6]`.
3. Сам выбирает один из них (Round Robin или Random) и делает запрос.
4. **Важно для K8s:** В Кубере есть свой Service Load Balancing (через IPVS/IPTables). Часто Spring Cloud LoadBalancer используют, чтобы делать более умную балансировку (например, предпочитать поды в той же Availability Zone), чего стандартный K8s Service не всегда умеет гибко.

---

### Legacy-чеклист (Что НЕ надо тащить в 2026)

На собеседовании могут спросить про "Старый стек". Знай, что это, но отвечай, что это Legacy.

1. **Netflix Eureka:** Реестр сервисов. В K8s не нужен (K8s сам знает, где его поды). Используется только если вы деплоитесь на голые виртуалки.
2. **Netflix Hystrix:** Circuit Breaker. **Мертв**. Заменен на **Resilience4j** (Spring Cloud Circuit Breaker).
3. **Netflix Zuul 1:** Gateway. **Мертв**. Заменен на **Spring Cloud Gateway**.
4. **Netflix Ribbon:** Load Balancer. **Мертв**. Заменен на **Spring Cloud LoadBalancer**.

---

### Практическое дополнение к Roadmap:

Если ты идешь на позицию **Architect** или **Tech Lead**, добавь эти пункты:

1. **Backend for Frontend (BFF) Pattern:** Использование Spring Cloud Gateway не как единого шлюза, а как отдельных шлюзов под каждого клиента (отдельно Gateway для Mobile App, отдельно для Web App), чтобы агрегировать данные специфично для UI.
2. **Contract Testing в облаке:** Использование **Spring Cloud Contract** для проверки того, что микросервисы (потребитель и провайдер) не сломали API, *до* деплоя в облако.



---

# Дополнительно: Maven Deep Dive (Build Engineering)

В простых проектах мы копипастим `pom.xml`. В энтерпрайзе мы проектируем **Build Pipeline**. Maven здесь выступает гарантом того, что в прод улетит именно то, что было протестировано, и что в зависимостях нет уязвимостей.

## 1. Философия: Lifecycle, Phases и Goals

Maven работает на концепции **Жизненного Цикла (Lifecycle)**. Это последовательность этапов.
Каждая **Фаза (Phase)** — это просто метка (например, `test`).
К каждой фазе привязываются **Цели (Goals)** плагинов (например, `surefire:test`).

### Три основных цикла:

1. **Default:** Основной цикл сборки (`validate` -> `compile` -> `test` -> `package` -> `verify` -> `install` -> `deploy`).
2. **Clean:** Очистка артефактов (`clean`).
3. **Site:** Генерация документации (в современном мире используется редко).

### Прод-нюанс: `package` vs `install` vs `deploy`

* `mvn package`: Собирает JAR в папку `/target`. **Используется в CI (Jenkins/GitLab)** для создания артефакта.
* `mvn install`: Собирает JAR и копирует его в **локальный репозиторий** (`~/.m2/repository`).
* **Вред:** На CI-агентах лучше избегать `install`, чтобы не засорять кэш агента нестабильными сборками и не создавать побочных эффектов для других билдов.


* `mvn deploy`: Отправляет JAR в удаленный репозиторий (Nexus/Artifactory). Это делает только CD-пайплайн.

---

## 2. Управление зависимостями (Dependency Management)

### Transitive Dependencies (Транзитивность)

Если вы подключили `Spring Boot Starter Web`, он подтянет `Jackson`, `Tomcat`, `Logback`. Это дерево зависимостей.

* **Dependency Mediation:** Если либа A хочет `log4j 1.0`, а либа B хочет `log4j 2.0`, Maven выберет ту, которая **ближе** к корню в дереве (nearest definition). Это часто приводит к `NoSuchMethodError` в рантайме.

### Scopes (Области видимости)

1. **compile** (default): Есть везде (компиляция, тесты, рантаим).
2. **provided**: Нужна при компиляции, но в рантайме её предоставит контейнер (или JDK).
* *Пример:* `Lombok` (нужен только компилятору), `Servlet API` (предоставляет Tomcat).


3. **runtime**: Не нужна при компиляции кода, но нужна при запуске.
* *Пример:* Драйвер `PostgreSQL`. Вы не должны использовать классы драйвера в коде напрямую (только через JDBC интерфейсы), но класс должен быть в classpath.


4. **test**: Только для тестов.
* *Пример:* `JUnit`, `Mockito`, `Testcontainers`. Не попадет в итоговый JAR.


5. **import**: Используется только в `<dependencyManagement>` для импорта BOM.

### BOM (Bill of Materials)

Вместо того чтобы прописывать версии для каждой из 20 библиотек Spring, мы импортируем "Счет материалов".

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>3.2.0</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>

```

**Разница:**

* `<dependencyManagement>`: Только **объявляет** версии. Сами библиотеки не подключаются.
* `<dependencies>`: Реально подключает библиотеки. Версию указывать не нужно — она возьмется из Management.

---

## 3. Топ Плагинов для Enterprise (Must-Have)

### Базовые плагины (Core)

#### 1. `maven-compiler-plugin`

Отвечает за компиляцию `.java` в `.class`.

* **Важно:** Начиная с Java 9, вместо `source`/`target` используйте `<release>17</release>`. Это гарантирует, что вы случайно не используете API из Java 21, компилируя под 17.

#### 2. `maven-surefire-plugin` vs `maven-failsafe-plugin`

Это классический вопрос на собеседовании.

* **Surefire:** Запускает **Unit-тесты** (обычно `*Test.java`).
* Фаза: `test`.
* Поведение: Если тест упал — билд падает сразу.


* **Failsafe:** Запускает **Integration-тесты** (обычно `*IT.java`).
* Фаза: `integration-test` (запуск) и `verify` (проверка результатов).
* Поведение: Если тест упал, билд **продолжается** до фазы `post-integration-test` (чтобы успеть погасить Docker-контейнеры), и падает только на фазе `verify`.



#### 3. `spring-boot-maven-plugin`

Превращает обычный JAR в **Executable JAR** (Fat JAR).

* Repackage: Берет ваш код и все зависимости, кладет их внутрь JAR, создает структуру `BOOT-INF/lib` и прописывает правильный Manifest.
* Build Info: Генерирует `build-info.properties` (версия, время сборки), которые видит Actuator.

### Плагины Качества (Quality Gates)

#### 4. `maven-enforcer-plugin` (Полиция проекта)

Самый важный плагин для техлида. Запрещает бардак.

```xml
<plugin>
    <artifactId>maven-enforcer-plugin</artifactId>
    <executions>
        <execution>
            <goals><goal>enforce</goal></goals>
            <configuration>
                <rules>
                    <dependencyConvergence/> <bannedDependencies>
                        <excludes>
                            <exclude>log4j:log4j</exclude> </excludes>
                    </bannedDependencies>
                </rules>
            </configuration>
        </execution>
    </executions>
</plugin>

```

#### 5. `jacoco-maven-plugin` (Code Coverage)

Генерирует отчеты о покрытии кода тестами.

* Настраивается порог (Quality Gate): "Билд падает, если покрытие < 80%".

#### 6. `spotbugs-maven-plugin` / `maven-checkstyle-plugin`

Статический анализ.

* **Checkstyle:** Проверяет форматирование (где скобочки, длина строки).
* **Spotbugs:** Ищет баги (NullPointer dereference, не закрытые стримы, сравнение строк через `==`).

### Плагины Поставки (Delivery)

#### 7. `versions-maven-plugin`

Управление версиями.

* `mvn versions:display-dependency-updates`: Показывает, какие либы устарели.
* `mvn versions:set -DnewVersion=1.2.3`: Меняет версию проекта во всех `pom.xml` (включая дочерние модули).

#### 8. `cyclonedx-maven-plugin` (Security)

Генерирует **SBOM** (Software Bill of Materials).
Создает JSON файл со списком всех компонентов. Этот файл скармливается сканерам безопасности (Dependency Track), чтобы узнать, есть ли у вас уязвимые библиотеки.

---

## 4. Multi-module Projects (Reactor)

В крупных системах код делят на модули.

```text
my-bank-app (Parent)
├── bank-core (Domain logic)
├── bank-api (DTOs)
└── bank-web (Spring Boot Starter, Controllers)

```

### Parent POM

Хранит общую конфигурацию: версии плагинов, `<dependencyManagement>`, общие зависимости (Lombok, Testcontainers).
Дочерние модули наследуются от него и становятся чище.

### Reactor Commands

Вы не обязаны собирать всё каждый раз.

* `mvn install -pl bank-web -am`:
* `-pl` (Project List): Собрать только `bank-web`.
* `-am` (Also Make): Собрать также все модули, от которых зависит `bank-web` (core, api).



---

## 5. Maven Wrapper (`mvnw`)

**Никогда** не рассчитывайте, что на сервере CI или компьютере коллеги стоит правильная версия Maven.
В проекте должны лежать файлы `mvnw` (скрипт) и папка `.mvn`.

**Зачем:**
Когда вы запускаете `./mvnw clean install`, скрипт сам скачивает нужную версию Maven (изолированно для проекта) и запускает сборку. Это гарантирует воспроизводимость сборки через 5 лет.

---

# Gradle Deep Dive (Build Engineering)

Gradle работает иначе, чем Maven. Это не просто конфиг, это скрипт (Groovy или Kotlin DSL). Главное преимущество Gradle в больших проектах — **Incremental Builds** и **Build Cache**. Он не пересобирает то, что не менялось, что на монорепозиториях сокращает время билда с 10 минут до 30 секунд.

## 1. Философия: DSL и Build Lifecycle

В отличие от линейного Maven, Gradle строит **DAG (Directed Acyclic Graph)** задач (Tasks).

### Жизненный цикл (Trap for Beginners)

Многие "тормоза" Gradle вызваны непониманием этих фаз:

1. **Initialization:** Gradle читает `settings.gradle(.kts)`, решает, какие проекты участвуют в сборке (multi-module).
2. **Configuration:** **Самая опасная фаза.** Gradle выполняет код *во всех* `build.gradle` скриптах всех модулей, чтобы построить граф задач.
* **Правило:** Никогда не пишите тяжелую логику (IO, сетевые запросы, `Thread.sleep`) в теле скрипта вне блока `doLast {}` или `doFirst {}`. Иначе простой вызов `./gradlew help` будет занимать минуты.


3. **Execution:** Выполнение конкретных задач из графа (например, `compileJava`).

### Kotlin DSL (`build.gradle.kts`)

В 2024+ стандартом стал Kotlin DSL. Он дает автодополнение в IDE и проверку типов. Забудьте про Groovy, если начинаете новый проект.

---

## 2. Управление зависимостями: Configuration Hell

В Maven всё просто (scope). В Gradle конфигураций много, и нужно понимать разницу.

### Основные конфигурации:

1. **`implementation`** (вместо старого `compile`):
* **Суть:** Зависимость доступна вам, но **скрыта** от тех, кто зависит от вашего модуля.
* **Зачем:** Инкапсуляция + Скорость сборки. Если вы поменяли версию внутренней библиотеки в `implementation`, Gradle пересоберет только ваш модуль, но не тех, кто от него зависит.


2. **`api`** (плагин `java-library`):
* **Суть:** Зависимость транзитивно передается всем потребителям. Эквивалент `compile` в Maven.
* **Когда:** Только если вы пишете библиотеку и типы этой зависимости торчат в ваших публичных интерфейсах.


3. **`compileOnly`**:
* Аналог `provided` в Maven. Нужен для компиляции (Lombok), в рантайм не попадает.


4. **`runtimeOnly`**:
* Аналог `runtime` в Maven. (Postgres Driver).



### Version Catalogs (`libs.versions.toml`) — Стандарт Enterprise

Хранить версии в `ext.kotlin_version = '1.9'` — прошлый век.
Сейчас используется единый TOML файл для всего проекта.

**gradle/libs.versions.toml:**

```toml
[versions]
springBoot = "3.2.0"
jackson = "2.15.0"

[libraries]
spring-web = { module = "org.springframework.boot:spring-boot-starter-web", version.ref = "springBoot" }
jackson-databind = { module = "com.fasterxml.jackson.core:jackson-databind", version.ref = "jackson" }

[plugins]
springBoot = { id = "org.springframework.boot", version.ref = "springBoot" }

```

**build.gradle.kts:**

```kotlin
dependencies {
    implementation(libs.spring.web)
    implementation(libs.jackson.databind)
}

```

Это дает централизованное управление версиями во всех микросервисах.

---

## 3. Топ Плагинов для Gradle

В Gradle плагины подключаются через блок `plugins {}`.

### Core & Frameworks

#### 1. `org.springframework.boot` & `io.spring.dependency-management`

Обязательная пара.

* **Boot Plugin:** Создает Fat JAR (`bootJar`), управляет запуском (`bootRun`).
* **Dependency Management:** Эмулирует Maven BOM. Позволяет не писать версии для стандартных библиотек Spring.

```kotlin
plugins {
    id("org.springframework.boot") version "3.2.0"
    id("io.spring.dependency-management") version "1.1.4"
}

```

#### 2. `java-library` vs `java`

* Если у вас микросервис (конечный приложение) — используйте `id("java")`.
* Если у вас **shared-lib** (общая библиотека DTO или Utils) — используйте `id("java-library")`, чтобы получить доступ к `api` конфигурации.

### Code Quality

#### 3. `com.diffplug.spotless` (Must Have)

Maven Checkstyle сложен в настройке. Spotless — это "Format on Save" для CI.
Он может автоматически форматировать код, удалять неиспользуемые импорты, добавлять лицензионные хедеры.

```kotlin
spotless {
    java {
        googleJavaFormat() // Используем стандарт Google
        removeUnusedImports()
    }
}

```

Команда `./gradlew spotlessApply` делает код идеальным.

#### 4. `io.gitlab.arturbosch.detekt` (Для Kotlin)

Если пишете на Kotlin, это стандарт статического анализа (вместо Checkstyle/Spotbugs).

### Shadow & Packaging

#### 5. `com.github.johnrengelman.shadow`

Если вы не используете Spring Boot (который сам делает Fat JAR), а пишете, например, AWS Lambda на чистой Java, этот плагин соберет "Uber-JAR" со всеми зависимостями. Также умеет делать **Relocation** (переименовывание пакетов зависимостей), чтобы избежать конфликтов версий (Shading).

### Security

#### 6. `org.owasp.dependencycheck`

Аналог плагина для Maven. Сканирует зависимости на CVE и генерирует отчет (HTML/JSON). Встраивается в CI пайплайн.

---

## 4. Gradle Wrapper (`gradlew`) — Это закон

Как и в Maven, в корне проекта должны лежать `gradlew` (Shell script), `gradlew.bat` и `gradle/wrapper/gradle-wrapper.properties`.

**Почему это критично:**
Gradle обновляется часто (раз в пару месяцев). Версия 8.0 может сломать скрипт, написанный для 7.6.
Wrapper жестко фиксирует версию Gradle для проекта.

* Команда обновления: `./gradlew wrapper --gradle-version 8.5`

---

## 5. Performance Tuning (Как ускорить билд)

Если Maven просто "медленный", то Gradle можно разогнать.

**`gradle.properties` (в корне проекта или в `~/.gradle/`):**

```properties
# 1. Включаем Демона. Это долгоживущий процесс Java, который держит "разогретый" JIT.
org.gradle.daemon=true

# 2. Параллельное выполнение задач. Если модули независимы, они соберутся одновременно.
org.gradle.parallel=true

# 3. Build Cache. Кеширует результаты задач (скомпилированные классы).
# Если вы поменяли одну строчку, Gradle не будет перекомпилировать весь проект.
org.gradle.caching=true

# 4. Настройка памяти для Демона (не путать с памятью приложения!)
org.gradle.jvmargs=-Xmx2g -XX:+UseZGC

```

### Remote Build Cache (Для команд)

Вы можете поднять сервер (Gradle Enterprise или просто Nginx/Redis), куда CI будет складывать результаты билдов.
**Магия:** Коллега собрал проект. Вы делаете `pull`, запускаете билд, и Gradle **скачивает** уже готовые классы с сервера вместо того, чтобы компилировать их локально. Время сборки падает с минут до секунд.

---

# Дополнительно: Server-Side Rendering (SSR) & WebJars

Когда фронтенд слишком дорогой, а JSP уже умер, на сцену выходят современные шаблонизаторы.

## 1. Thymeleaf: Стандарт Spring Boot

Thymeleaf — это де-факто стандартный шаблонизатор в экосистеме Spring Boot.

### Философия: Natural Templates

Главная фича — **"Натуральные шаблоны"**. Файл `.html` с Thymeleaf валиден и может быть открыт в браузере как статика.
Дизайнер рисует HTML, верстает его, отдает вам. Вы добавляете атрибуты `th:*`, не ломая верстку.

### Use Cases в Банке:

1. **Back-office UI:** Админки, где SEO не нужен, интерактивность минимальна, а скорость разработки важна.
2. **Transactional Emails (Киллер-фича):** Генерация писем о транзакциях ("Ваш перевод на 5000р прошел").
* Thymeleaf работает не только в Web-контексте. Вы можете запустить `TemplateEngine` отдельно, скормить ему контекст (Map с данными) и получить String (HTML письма), который уйдет в SMTP-сервер.



### Прод-нюансы:

* **Производительность:** Thymeleaf использует много рефлексии и памяти. В высоконагруженных публичных страницах он может стать узким местом.
* **Кэширование:** В `application.yaml` для прода обязательно: `spring.thymeleaf.cache=true`. Для разработки: `false` (чтобы видеть изменения без рестарта).

**Пример (Admin Panel):**

```html
<table>
  <tr th:each="user : ${users}">
    <td th:text="${user.id}">123</td> <td th:text="${user.name}">John Doe</td>
  </tr>
</table>

```

---

## 2. Apache FreeMarker: Старая гвардия

FreeMarker — это ветеран. Он старше, жестче, быстрее.

### Философия: Generic Template Engine

В отличие от Thymeleaf, FreeMarker ничего не знает про HTML. Для него это просто текст. Это делает его идеальным для генерации **не-HTML** контента.

### Use Cases:

1. **Code Generation:** Генерация Java-классов, XML-конфигов, SQL-скриптов на лету.
2. **Сложные отчеты:** Где нужна сложная логика форматирования текста, которую неудобно писать в тегах HTML.
3. **Legacy системы:** Многие старые банковские монолиты написаны на FreeMarker (или Velocity).

### FreeMarker vs Thymeleaf

* **Скорость:** FreeMarker обычно компилируется быстрее и потребляет меньше памяти.
* **Синтаксис:** Более похож на JSP/PHP. Ломает HTML-структуру (браузер не отобразит шаблон корректно без обработки).

**Пример:**

```html
<#list users as user>
  <tr>
    <td>${user.id}</td>
    <td>${user.name}</td>
  </tr>
</#list>

```

---

## 3. WebJars: Frontend для бэкендеров

Как подключить Bootstrap или jQuery в Spring Boot проект, не устанавливая Node.js, npm и Webpack?
Ответ: **WebJars**.

### Как это работает

Это клиентские библиотеки, упакованные в `.jar` файлы и выложенные в Maven Central.

**1. Подключение (pom.xml):**

```xml
<dependency>
    <groupId>org.webjars</groupId>
    <artifactId>bootstrap</artifactId>
    <version>5.3.2</version>
</dependency>

```

**2. Магия Spring Boot:**
Spring автоматически маппит содержимое JAR-файлов по пути `/webjars/**`.

**3. Использование в HTML:**

```html
<link rel="stylesheet" href="/webjars/bootstrap/5.3.2/css/bootstrap.min.css">

```

### Locator (Избавляемся от версий)

Чтобы не хардкодить версию `5.3.2` в HTML (и не ломать верстку при обновлении зависимости), подключаем `org.webjars:webjars-locator-core`.
Теперь можно писать так (версия определится сама):

```html
<link rel="stylesheet" href="/webjars/bootstrap/css/bootstrap.min.css">

```

### Verdict Senior-разработчика:

* **Когда использовать:**
* Внутренние тулзы, прототипы, пет-проекты.
* Когда в команде только Java-разработчики и никто не хочет настраивать `npm install`.


* **Когда НЕ использовать:**
* В серьезных публичных интерфейсах.
* WebJars не умеют делать Tree Shaking (выкидывать неиспользуемый JS код), Minification (сжатие) и Bundling так эффективно, как это делают Webpack/Vite. Вы будете отдавать клиенту лишние мегабайты кода.



---