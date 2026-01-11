- [Глава 1. Kotlin: Фундамент Modern Android](#глава-1-kotlin-фундамент-modern-android)
    - [1. Null Safety (Безопасность типов)](#1-null-safety-безопасность-типов)
    - [2. Лямбды и функции высшего порядка](#2-лямбды-и-функции-высшего-порядка)
    - [3. Scope Functions (Функции области видимости)](#3-scope-functions-функции-области-видимости)
    - [4. Классы данных (Sealed Classes \& Data Classes)](#4-классы-данных-sealed-classes--data-classes)
    - [5. Делегаты (by lazy)](#5-делегаты-by-lazy)
    - [✅ Итог Главы 1](#-итог-главы-1)
- [Глава 2. Android Core: Lifecycle, Context и Manifest](#глава-2-android-core-lifecycle-context-и-manifest)
    - [1. Activity Lifecycle (Жизненный цикл Активити)](#1-activity-lifecycle-жизненный-цикл-активити)
      - [Пример кода (с логированием):](#пример-кода-с-логированием)
    - [2. Главная боль: Configuration Changes (Поворот экрана)](#2-главная-боль-configuration-changes-поворот-экрана)
    - [3. Context (Контекст)](#3-context-контекст)
    - [4. AndroidManifest.xml](#4-androidmanifestxml)
    - [✅ Итог Главы 2](#-итог-главы-2)
- [Глава 3. Введение в Jetpack Compose: Декларативный UI](#глава-3-введение-в-jetpack-compose-декларативный-ui)
    - [1. Смена мышления: Императивный vs Декларативный](#1-смена-мышления-императивный-vs-декларативный)
    - [2. Первая @Composable функция](#2-первая-composable-функция)
    - [3. Базовые контейнеры (Layouts)](#3-базовые-контейнеры-layouts)
    - [4. Modifiers (Модификаторы) — Суперсила Compose](#4-modifiers-модификаторы--суперсила-compose)
    - [5. Практика: Создаем карточку товара](#5-практика-создаем-карточку-товара)
    - [6. Preview (Предпросмотр)](#6-preview-предпросмотр)
    - [✅ Итог Главы 3](#-итог-главы-3)
- [Глава 4. State Management (Управление состоянием) в Compose](#глава-4-state-management-управление-состоянием-в-compose)
    - [1. Проблема: Как обновить текст?](#1-проблема-как-обновить-текст)
    - [2. Магическая тройка: State, Remember, Recomposition](#2-магическая-тройка-state-remember-recomposition)
      - [Пример: Счетчик нажатий](#пример-счетчик-нажатий)
    - [3. `remember` vs `rememberSaveable`](#3-remember-vs-remembersaveable)
    - [4. State Hoisting (Поднятие состояния)](#4-state-hoisting-поднятие-состояния)
      - [Пример State Hoisting:](#пример-state-hoisting)
    - [5. Поля ввода (TextField)](#5-поля-ввода-textfield)
    - [✅ Итог Главы 4](#-итог-главы-4)
- [Глава 5. Списки (Lazy Lists)](#глава-5-списки-lazy-lists)
    - [1. Почему не обычный Column?](#1-почему-не-обычный-column)
    - [2. Основы LazyColumn](#2-основы-lazycolumn)
    - [3. Отображение реальных данных (`items`)](#3-отображение-реальных-данных-items)
    - [4. Кликабельность элементов](#4-кликабельность-элементов)
    - [5. LazyRow и Grids](#5-lazyrow-и-grids)
    - [6. Оптимизация производительности](#6-оптимизация-производительности)
    - [✅ Итог Главы 5](#-итог-главы-5)
- [Глава 6. Архитектура MVVM (Model - View - ViewModel)](#глава-6-архитектура-mvvm-model---view---viewmodel)
    - [1. Зачем это нужно?](#1-зачем-это-нужно)
    - [2. Три компонента MVVM](#2-три-компонента-mvvm)
    - [3. Реализация ViewModel в коде](#3-реализация-viewmodel-в-коде)
      - [Шаг 1: Описываем Состояние (State)](#шаг-1-описываем-состояние-state)
      - [Шаг 2: Создаем ViewModel](#шаг-2-создаем-viewmodel)
      - [Шаг 3: Связываем с UI (Compose)](#шаг-3-связываем-с-ui-compose)
    - [4. Unidirectional Data Flow (UDF)](#4-unidirectional-data-flow-udf)
    - [✅ Итог Главы 6](#-итог-главы-6)
- [Глава 7. Асинхронность и Coroutines (Корутины)](#глава-7-асинхронность-и-coroutines-корутины)
    - [1. Главный поток (Main Thread)](#1-главный-поток-main-thread)
    - [2. Ключевое слово `suspend`](#2-ключевое-слово-suspend)
    - [3. Dispatchers (Диспетчеры) — Где выполнять работу?](#3-dispatchers-диспетчеры--где-выполнять-работу)
    - [4. ViewModelScope — Безопасный запуск](#4-viewmodelscope--безопасный-запуск)
    - [5. Практика: Исправляем загрузку пользователей](#5-практика-исправляем-загрузку-пользователей)
    - [6. Async / Await — Параллельное выполнение](#6-async--await--параллельное-выполнение)
    - [✅ Итог Главы 7](#-итог-главы-7)
- [Глава 8. Работа с сетью: Retrofit \& Parsing JSON](#глава-8-работа-с-сетью-retrofit--parsing-json)
    - [1. Почему Retrofit?](#1-почему-retrofit)
    - [2. Подготовка (Manifest и Gradle)](#2-подготовка-manifest-и-gradle)
    - [3. Модель данных (Data Model)](#3-модель-данных-data-model)
    - [4. Описание API (Интерфейс)](#4-описание-api-интерфейс)
    - [5. Создание экземпляра Retrofit (Сборка)](#5-создание-экземпляра-retrofit-сборка)
    - [6. Использование во ViewModel](#6-использование-во-viewmodel)
    - [✅ Итог Главы 8](#-итог-главы-8)
- [Глава 9. Dependency Injection (Hilt)](#глава-9-dependency-injection-hilt)
    - [1. Что такое Hilt и зачем он нужен?](#1-что-такое-hilt-и-зачем-он-нужен)
    - [2. Настройка проекта](#2-настройка-проекта)
    - [3. @HiltAndroidApp — Корень всего](#3-hiltandroidapp--корень-всего)
    - [4. Как учить Hilt создавать объекты?](#4-как-учить-hilt-создавать-объекты)
      - [Способ А: Constructor Injection (Простой)](#способ-а-constructor-injection-простой)
      - [Способ Б: Modules (Сложный, но мощный)](#способ-б-modules-сложный-но-мощный)
    - [5. Внедрение во ViewModel (`@HiltViewModel`)](#5-внедрение-во-viewmodel-hiltviewmodel)
    - [6. Внедрение в UI (`@AndroidEntryPoint`)](#6-внедрение-в-ui-androidentrypoint)
    - [7. Scopes (Области видимости) — Важная теория](#7-scopes-области-видимости--важная-теория)
    - [✅ Итог Главы 9](#-итог-главы-9)
- [Глава 10. Локальная база данных (Room)](#глава-10-локальная-база-данных-room)
    - [1. Что такое Room?](#1-что-такое-room)
    - [2. Шаг 1: Entity (Таблица)](#2-шаг-1-entity-таблица)
    - [3. Шаг 2: DAO (Запросы)](#3-шаг-2-dao-запросы)
    - [4. Шаг 3: Database (Сборка)](#4-шаг-3-database-сборка)
    - [5. Шаг 4: Подключаем через Hilt (DI)](#5-шаг-4-подключаем-через-hilt-di)
    - [6. Single Source of Truth (Единственный источник правды)](#6-single-source-of-truth-единственный-источник-правды)
    - [7. TypeConverters (Если данные сложные)](#7-typeconverters-если-данные-сложные)
    - [✅ Итог Главы 10](#-итог-главы-10)
- [Глава 11. Навигация (Jetpack Navigation Compose)](#глава-11-навигация-jetpack-navigation-compose)
    - [1. Концепция: Single Activity](#1-концепция-single-activity)
    - [2. Подготовка](#2-подготовка)
    - [3. Маршруты (Routes)](#3-маршруты-routes)
    - [4. NavController и NavHost](#4-navcontroller-и-navhost)
    - [5. Передача данных между экранами](#5-передача-данных-между-экранами)
    - [6. Навигация и MVVM](#6-навигация-и-mvvm)
      - [Пример правильной архитектуры ("Event Bubbling"):](#пример-правильной-архитектуры-event-bubbling)
    - [7. Bottom Navigation (Нижнее меню)](#7-bottom-navigation-нижнее-меню)
    - [8. Type Safe Navigation (Новый стандарт 2024+)](#8-type-safe-navigation-новый-стандарт-2024)
    - [✅ Итог Главы 11](#-итог-главы-11)
- [Глава 12. Ресурсы, Темизация и Material Design 3](#глава-12-ресурсы-темизация-и-material-design-3)
    - [1. Ресурсы в Android (Resources)](#1-ресурсы-в-android-resources)
    - [2. Material Design 3 (M3)](#2-material-design-3-m3)
    - [3. Настройка Темы (`Theme.kt`)](#3-настройка-темы-themekt)
    - [4. Семантические цвета (Semantic Colors)](#4-семантические-цвета-semantic-colors)
    - [5. Практика: Адаптивный UI](#5-практика-адаптивный-ui)
    - [6. Локализация (Localization)](#6-локализация-localization)
    - [7. Иконки](#7-иконки)
    - [✅ Итог Главы 12](#-итог-главы-12)
- [Глава 13. Тестирование (Unit \& UI Testing)](#глава-13-тестирование-unit--ui-testing)
    - [1. Пирамида тестирования](#1-пирамида-тестирования)
    - [2. Инструменты (Dependencies)](#2-инструменты-dependencies)
    - [3. Unit Testing: Тестируем ViewModel](#3-unit-testing-тестируем-viewmodel)
      - [Шаг 1: Правило для корутин](#шаг-1-правило-для-корутин)
      - [Шаг 2: Тест для ViewModel](#шаг-2-тест-для-viewmodel)
    - [4. UI Testing: Тестируем Compose](#4-ui-testing-тестируем-compose)
    - [5. Что такое Flaky Tests?](#5-что-такое-flaky-tests)
    - [✅ Итог Главы 13](#-итог-главы-13)
- [Глава 14. Продвинутая сборка: Gradle, Multi-module и CI/CD](#глава-14-продвинутая-сборка-gradle-multi-module-и-cicd)
    - [1. Зачем разбивать проект на модули?](#1-зачем-разбивать-проект-на-модули)
    - [2. Структура модулей (Clean Architecture)](#2-структура-модулей-clean-architecture)
    - [3. Управление зависимостями: Version Catalogs (TOML)](#3-управление-зависимостями-version-catalogs-toml)
    - [4. Build Variants (Типы сборок)](#4-build-variants-типы-сборок)
    - [5. CI/CD (Continuous Integration / Delivery)](#5-cicd-continuous-integration--delivery)
- [Глава 15. Гарантированная фоновая работа (WorkManager)](#глава-15-гарантированная-фоновая-работа-workmanager)
    - [1. Проблема: Coroutines vs Реальность](#1-проблема-coroutines-vs-реальность)
    - [2. Когда использовать?](#2-когда-использовать)
    - [3. Создание Worker (Рабочего)](#3-создание-worker-рабочего)
    - [4. Запуск задачи (WorkRequest)](#4-запуск-задачи-workrequest)
    - [5. Периодические задачи (PeriodicWorkRequest)](#5-периодические-задачи-periodicworkrequest)
    - [6. WorkManager + Hilt (HiltWorker)](#6-workmanager--hilt-hiltworker)
    - [✅ Итог Главы 15](#-итог-главы-15)
- [Глава 16. Разрешения (Permissions) и Уведомления (Notifications)](#глава-16-разрешения-permissions-и-уведомления-notifications)
    - [1. Философия разрешений в Modern Android](#1-философия-разрешений-в-modern-android)
    - [2. Запрос разрешений в Compose](#2-запрос-разрешений-в-compose)
    - [3. Анатомия Уведомления](#3-анатомия-уведомления)
    - [4. Создание Канала (Notification Channel)](#4-создание-канала-notification-channel)
    - [5. PendingIntent — Реакция на нажатие](#5-pendingintent--реакция-на-нажатие)
    - [6. Типы уведомлений (Senior Level)](#6-типы-уведомлений-senior-level)
    - [7. Push Notifications (Firebase / FCM)](#7-push-notifications-firebase--fcm)
    - [✅ Итог Главы 16](#-итог-главы-16)
- [Глава 17. Производительность (Performance) и Оптимизация](#глава-17-производительность-performance-и-оптимизация)
    - [1. Что такое "Лаги" (Jank)?](#1-что-такое-лаги-jank)
    - [2. Утечки памяти (Memory Leaks)](#2-утечки-памяти-memory-leaks)
      - [Классический пример утечки:](#классический-пример-утечки)
    - [3. Инструмент №1: LeakCanary](#3-инструмент-1-leakcanary)
    - [4. Инструмент №2: Android Profiler](#4-инструмент-2-android-profiler)
    - [5. R8 и ProGuard (Сжатие кода)](#5-r8-и-proguard-сжатие-кода)
    - [6. Baseline Profiles (Ускорение запуска)](#6-baseline-profiles-ускорение-запуска)
    - [✅ Итог Главы 17](#-итог-главы-17)



# Глава 1. Kotlin: Фундамент Modern Android

В современной разработке (Compose, Coroutines) мы пишем в функциональном стиле. Без понимания лямбд, scope-функций и null-безопасности код на Compose будет казаться магией.

### 1. Null Safety (Безопасность типов)

В Java `NullPointerException` — главная причина крашей. В Kotlin система типов заставляет нас обрабатывать `null` на этапе компиляции.

**Ключевые инструменты:**

* `?` — переменная может хранить null.
* `?.` — безопасный вызов (выполнить, только если не null).
* `?:` — Elvis-оператор (значение по умолчанию).

```kotlin
// Модель данных пользователя, где email может отсутствовать
data class User(val name: String, val email: String?)

fun printUserEmail(user: User?) {
    // 1. Safe Call (?.):
    // Если user == null, то user?.email вернет null, и программа НЕ упадет.
    val email = user?.email
    
    // 2. Elvis Operator (?:):
    // Если выражение слева равно null, берем то, что справа.
    // Часто используется для установки значений по умолчанию в UI.
    val textToDisplay = user?.email ?: "Email не указан"
    
    println(textToDisplay)

    // 3. Not-null assertion (!!):
    // "Я мамой клянусь, тут не null".
    // ⚠️ В Android разработке старайтесь ИЗБЕГАТЬ этого оператора. 
    // Это гарантированный краш, если вы ошиблись.
    // val unsafeEmail = user!!.email 
}

```

### 2. Лямбды и функции высшего порядка

Это **сердце Jetpack Compose**. Весь UI в Compose строится на функциях, которые принимают другие функции (лямбды) в качестве параметров (например, обработка клика или отрисовка содержимого).

**Концепция:** Функция может быть передана в другую функцию как переменная.

```kotlin
// Обычная функция
fun sum(a: Int, b: Int): Int {
    return a + b
}

// Лямбда-выражение (анонимная функция)
// Тип переменной: (Int, Int) -> Int
val sumLambda: (Int, Int) -> Int = { a, b -> 
    a + b 
}

// ФУНКЦИЯ ВЫСШЕГО ПОРЯДКА
// Принимает другую функцию (operation) как аргумент.
// Это база для таких вещей, как onClick в Compose.
fun calculate(a: Int, b: Int, operation: (Int, Int) -> Int): Int {
    return operation(a, b)
}

fun main() {
    // Передаем лямбду в функцию
    val result = calculate(10, 5) { x, y -> 
        // Последняя строка в лямбде — это возвращаемое значение
        x * y 
    }
    // Обратите внимание: если лямбда идет последним аргументом, 
    // ее можно вынести за скобки (). Это называется Trailing Lambda syntax.
    // Именно поэтому в Compose мы пишем Column { ... }, а не Column({ ... })
}

```

### 3. Scope Functions (Функции области видимости)

Эти 5 функций (`let`, `run`, `with`, `apply`, `also`) позволяют писать код компактнее. На собеседованиях на Middle/Senior спрашивают разницу между ними.

| Функция   | Context Object | Return Value     | Для чего чаще всего используется в Android              |
| --------- | -------------- | ---------------- | ------------------------------------------------------- |
| **let**   | `it`           | Результат лямбды | Проверка на null (`object?.let { ... }`)                |
| **apply** | `this`         | Сам объект       | Настройка объекта (инициализация View, Intent, Builder) |
| **with**  | `this`         | Результат лямбды | Группировка вызовов методов одного объекта              |
| **also**  | `it`           | Сам объект       | Доп. действие (логирование) без изменения объекта       |
| **run**   | `this`         | Результат лямбды | Вычисление блока кода и возврат результата              |

**Примеры из реальной жизни:**

```kotlin
class MyFragment {

    fun setupUI() {
        // APPLY: Идеально для настройки объектов.
        // Мы обращаемся к свойствам TextView напрямую, без повторения имени переменной.
        val titleView = TextView(context).apply {
            text = "Привет, Андроид"
            textSize = 20f
            setTextColor(Color.BLACK)
            // this.text - "this" подразумевается
        }

        // LET: Идеально для null-check.
        // Блок выполнится только если getUer() вернет не null.
        getUser()?.let { user -> 
            // Здесь user уже точно не null.
            // Имя переменной можно сменить с "it" на "user" для читаемости.
            titleView.text = user.name
        }
        
        // ALSO: Сделать что-то "заодно", не ломая цепочку вызовов.
        val intent = Intent(context, SecondActivity::class.java).also {
            println("Создан интент для перехода на SecondActivity: $it")
        }
    }
    
    fun getUser(): User? = null // заглушка
}

```

### 4. Классы данных (Sealed Classes & Data Classes)

В архитектуре **MVI/MVVM** мы постоянно передаем состояние экрана (`State`). Для этого используются именно эти классы.

**Sealed Class (Изолированный класс):**
Это "Enum на стероидах". Позволяет ограничить иерархию классов. Идеально для описания состояний экрана (Загрузка, Ошибка, Успех).

```kotlin
// Описываем все возможные состояния UI
sealed class UiState {
    // Объект (Singleton), так как у него нет полей (состояние одно для всех)
    data object Loading : UiState() 
    
    // Data class, так как нам нужно передать данные (список новостей)
    data class Success(val news: List<String>) : UiState()
    
    // Data class для передачи ошибки
    data class Error(val message: String) : UiState()
}

// Пример использования в ViewModel или UI
fun handleState(state: UiState) {
    // Компилятор Kotlin заставит обработать ВСЕ ветки when,
    // потому что класс sealed (замкнутый). Else писать не нужно!
    when (state) {
        is UiState.Loading -> showProgressBar()
        is UiState.Success -> showList(state.news)
        is UiState.Error -> showErrorMessage(state.message)
    }
}

```

### 5. Делегаты (by lazy)

В Android мы часто хотим отложить создание тяжелых объектов до момента, когда они реально понадобятся (оптимизация старта приложения).

```kotlin
// Переменная heavyObject будет инициализирована ТОЛЬКО при первом обращении к ней.
// При повторных обращениях вернется уже созданный объект.
val heavyObject: HeavyCalculator by lazy {
    println("Инициализация...")
    HeavyCalculator() 
}

```

---

### ✅ Итог Главы 1

Мы разобрали базу языка, на которой строится Android:

1. **Null Safety** спасает от крашей.
2. **Лямбды** позволяют писать декларативный UI (Compose).
3. **Scope Functions** делают код чище.
4. **Sealed Classes** — стандарт для управления состоянием (State Management).

---

# Глава 2. Android Core: Lifecycle, Context и Manifest

### 1. Activity Lifecycle (Жизненный цикл Активити)

**Activity** — это один экран приложения (в классическом понимании). Даже если мы пишем на Compose (где все в одной Activity), сама Activity никуда не делась. Она служит контейнером.

Система вызывает у Activity определенные методы, когда меняется ее состояние (юзер свернул приложение, развернул, повернул экран).

**Основные колбэки (Callbacks):**

1. **onCreate()**: Вызывается при создании.
* *Что делаем:* Инициализируем UI (setContent), переменные, ViewModel.
* *Важно:* Вызывается только один раз за жизнь экземпляра.


2. **onStart()**: Activity стала видимой, но еще не активной (юзер не может нажать).
3. **onResume()**: Activity **на переднем плане** и готова к взаимодействию.
* *Что делаем:* Запускаем анимации, камеру, GPS, слушаем сенсоры.


4. **onPause()**: Фокус потерян (например, поверх всплыло полупрозрачное окно или юзер начал сворачивать апп).
* *Что делаем:* Останавливаем видео, сохраняем легкие данные.


5. **onStop()**: Activity **полностью невидима**.
* *Что делаем:* Останавливаем тяжелые операции, сетевые запросы UI.


6. **onDestroy()**: Финиш. Система уничтожает Activity (или юзер нажал "Назад").
* *Что делаем:* Очищаем ресурсы (если не очистили ранее), закрываем соединения с БД.



#### Пример кода (с логированием):

```kotlin
class MainActivity : ComponentActivity() {

    // 1. Самый важный метод. Входная точка.
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        Log.d("Lifecycle", "onCreate: Экран создается. Грузим UI.")
        
        // В Modern Android здесь мы задаем Compose контент
        setContent {
            MyAppTheme {
                MainScreen() 
            }
        }
    }

    // 2. Экран стал виден
    override fun onStart() {
        super.onStart()
        Log.d("Lifecycle", "onStart: Видим пользователю.")
    }

    // 3. Можно взаимодействовать (кликать)
    override fun onResume() {
        super.onResume()
        Log.d("Lifecycle", "onResume: Активен. Запускаем анимации.")
    }

    // 4. Частичная потеря фокуса или начало ухода в фон
    override fun onPause() {
        super.onPause()
        Log.d("Lifecycle", "onPause: На паузе. Сэйвим данные.")
    }

    // 5. Полный уход в фон
    override fun onStop() {
        super.onStop()
        Log.d("Lifecycle", "onStop: Невидим. Освобождаем тяжелые ресурсы.")
    }

    // 6. Уничтожение
    override fun onDestroy() {
        super.onDestroy()
        Log.d("Lifecycle", "onDestroy: Прощай, жестокий мир.")
    }
}

```

### 2. Главная боль: Configuration Changes (Поворот экрана)

Когда вы поворачиваете телефон, Android **уничтожает** вашу Activity (`onDestroy`) и **создает заново** (`onCreate`), чтобы загрузить ресурсы для новой ориентации (например, другой layout).

**Проблема:** Все обычные переменные внутри Activity сбрасываются.
**Решение:**

1. **ViewModel** (изучим в блоке Архитектуры) — переживает поворот экрана.
2. `rememberSaveable` (в Compose) — сохраняет состояние UI.

### 3. Context (Контекст)

Это "ручка", через которую мы получаем доступ к ресурсам системы (файлам, базам данных, темам, запуску других экранов).

В Android есть два основных типа контекста. Путать их — значит создать **Memory Leak** (утечку памяти).

| Тип           | Application Context                                                                                               | Activity Context                          |
| ------------- | ----------------------------------------------------------------------------------------------------------------- | ----------------------------------------- |
| **Жизнь**     | Живет пока живет приложение.                                                                                      | Живет пока открыт экран.                  |
| **Доступ**    | `applicationContext`                                                                                              | `this` (внутри Activity)                  |
| **Для чего**  | Синглтоны, базы данных, Analytics.                                                                                | Создание UI, Диалогов, Тостов, навигация. |
| **Опасность** | Если Activity Context передать в объект, который живет долго (Singleton), Activity никогда не удалится из памяти. | Безопасно для UI.                         |

```kotlin
// Пример правильного использования
class MyRepository(private val context: Context) {
    // В репозитории (который живет долго) лучше хранить Application Context,
    // чтобы не удерживать ссылку на закрытую Activity.
}

```

### 4. AndroidManifest.xml

Паспорт вашего приложения. Файл, который система читает ПЕРЕД запуском кода.

Что там важно знать:

* `<uses-permission>`: Запрос прав (Интернет, Камера, Геолокация).
* `<application>`: Глобальные настройки (иконка, тема).
* `<activity>`: Регистрация экранов.
* `intent-filter`: Говорит системе, что эта Activity — главная (Launcher), запускается первой.



```xml
<manifest ...>
    <uses-permission android:name="android.permission.INTERNET" />

    <application ... >
        <activity android:name=".MainActivity" android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
</manifest>

```

---

### ✅ Итог Главы 2

Вы узнали правила игры, которые устанавливает Android:

1. **Lifecycle**: Приложение не контролирует свою жизнь, это делает ОС. Мы лишь реагируем на колбэки (`onCreate`, `onResume`, `onPause`).
2. **Поворот экрана**: Пересоздает Activity. Данные теряются, если их не сохранить (ViewModel/rememberSaveable).
3. **Context**: Доступ к системе. Не храните ссылку на Activity в статических переменных (Singletone)!
4. **Manifest**: Конфигурация и права доступа.

---

# Глава 3. Введение в Jetpack Compose: Декларативный UI

### 1. Смена мышления: Императивный vs Декларативный

Раньше (в XML) мы использовали **Императивный подход**: мы говорили системе *КАК* менять UI.

> "Найди TextView, затем поменяй его текст, затем сделай его видимым."

Compose использует **Декларативный подход**: мы описываем, *ЧТО* мы хотим видеть при определенном состоянии данных.

> "Если данные загружены — покажи список. Если ошибка — покажи текст ошибки."

**Формула UI в Compose:**



Интерфейс — это результат выполнения функции от текущего состояния данных. Когда данные меняются, функция перезапускается (это называется **Recomposition**) и рисует новый UI.

### 2. Первая @Composable функция

В Compose интерфейс строится из обычных функций Kotlin, помеченных аннотацией `@Composable`.

**Правила:**

1. Функция должна быть помечена `@Composable`.
2. Имя функции пишется с **БольшойБуквы** (PascalCase), как классы (потому что мы создаем сущности UI).
3. Функция ничего не возвращает (`Unit`), она "эмитит" (излучает) UI.

```kotlin
// Простая функция, которая рисует текст
@Composable
fun Greeting(name: String) {
    // Text - это стандартный Composable элемент (аналог TextView)
    Text(text = "Привет, $name!")
}

```

### 3. Базовые контейнеры (Layouts)

В Compose нет огромного количества контейнеров. Основных "китов" всего три. С их помощью можно построить 90% интерфейсов.

1. **Column (Столбец)**: Размещает элементы вертикально (сверху вниз). Аналог `LinearLayout (vertical)`.
2. **Row (Строка)**: Размещает элементы горизонтально (слева направо). Аналог `LinearLayout (horizontal)`.
3. **Box (Коробка)**: Кладет элементы друг на друга (стопкой). Аналог `FrameLayout`.

```kotlin
@Composable
fun UserProfile() {
    // Рисуем рамку (Box можно использовать как подложку)
    Box {
        // Внутри располагаем элементы горизонтально
        Row {
            // Картинка (заглушка иконки)
            Icon(imageVector = Icons.Default.Person, contentDescription = null)
            
            // Рядом располагаем тексты вертикально
            Column {
                Text(text = "Алексей")
                Text(text = "Android Developer")
            }
        }
    }
}

```

### 4. Modifiers (Модификаторы) — Суперсила Compose

Как задать отступы, цвет фона, размер, кликабельность? В XML для этого были десятки атрибутов. В Compose есть один универсальный инструмент — **Modifier**.

Модификатор передается аргументом почти в любой Composable элемент.

**⚠️ Критически важно: Порядок имеет значение!**
В Compose модификаторы применяются последовательно, как слои лука.

```kotlin
@Composable
fun ModifierExample() {
    Text(
        text = "Hello World",
        modifier = Modifier
            .background(Color.Yellow) // 1. Сначала красим фон самого текста
            .padding(16.dp)           // 2. Делаем отступ ВОКРУГ текста (внутри желтого)
            .background(Color.Red)    // 3. Красим фон ВОКРУГ отступа (красная рамка)
            .padding(8.dp)            // 4. Еще один отступ снаружи
            .clickable { /* код клика */ } // 5. Делаем кликабельным ВСЁ, что внутри
    )
}

```

*Если вы поменяете `.padding()` и `.background()` местами, результат будет визуально другим.*

### 5. Практика: Создаем карточку товара

Давайте соберем реальный компонент с использованием всего изученного.

```kotlin
@Composable
fun ProductCard(
    productName: String,
    price: String,
    isOnSale: Boolean
) {
    // Card - готовый компонент с тенью и скругленными углами
    Card(
        modifier = Modifier
            .fillMaxWidth() // Занять всю ширину родителя
            .padding(16.dp), // Внешний отступ
        elevation = CardDefaults.cardElevation(defaultElevation = 4.dp)
    ) {
        // Внутри карточки элементы идут вертикально
        Column(
            modifier = Modifier
                .padding(16.dp) // Внутренний отступ (padding внутри карточки)
        ) {
            // Заголовок
            Text(
                text = productName,
                style = MaterialTheme.typography.titleLarge
            )
            
            // Разделитель (пробел высотой 8dp)
            Spacer(modifier = Modifier.height(8.dp))
            
            // Цена и значок "Скидка" в одной строке
            Row(
                verticalAlignment = Alignment.CenterVertically // Выровнять по центру вертикально
            ) {
                Text(
                    text = price,
                    color = Color.Blue,
                    fontWeight = FontWeight.Bold
                )
                
                // Если есть скидка, рисуем значок
                if (isOnSale) {
                    Spacer(modifier = Modifier.width(8.dp)) // Отступ между ценой и значком
                    
                    // Box для красного фона значка
                    Box(
                        modifier = Modifier
                            .background(Color.Red, shape = RoundedCornerShape(4.dp))
                            .padding(horizontal = 6.dp, vertical = 2.dp)
                    ) {
                        Text(text = "SALE", color = Color.White, fontSize = 10.sp)
                    }
                }
            }
        }
    }
}

```

### 6. Preview (Предпросмотр)

В Compose не нужно каждый раз запускать эмулятор, чтобы увидеть верстку. Используйте аннотацию `@Preview`.

```kotlin
@Preview(showBackground = true)
@Composable
fun ProductCardPreview() {
    // Этот код виден только в Android Studio (Split/Design window)
    ProductCard(
        productName = "MacBook Pro",
        price = "$2000",
        isOnSale = true
    )
}

```

---

### ✅ Итог Главы 3

1. **Compose** — это функции, а не объекты.
2. UI строится на вложенности `Column`, `Row`, `Box`.
3. **Modifier** управляет внешним видом и расположением. Порядок вызова функций в Modifier важен.
4. Мы можем использовать обычные конструкции Kotlin (`if`, `for`) прямо внутри UI кода для логики отображения.

---

# Глава 4. State Management (Управление состоянием) в Compose

### 1. Проблема: Как обновить текст?

В старом Android (XML) мы делали так: `textView.text = "Новый текст"`.
В Compose **нет сеттеров**. Вы не можете обратиться к текстовому полю и изменить его свойство.

**Принцип Recomposition (Рекомпозиция):**
Чтобы изменить UI, нужно вызвать Composable-функцию **заново** с новыми данными.
Но как заставить функцию перезапуститься?

### 2. Магическая тройка: State, Remember, Recomposition

Compose следит за специальными объектами типа `State`. Как только значение внутри `State` меняется, Compose автоматически находит все функции, которые используют это значение, и запускает их заново.

#### Пример: Счетчик нажатий

Давайте напишем код, который **НЕ будет работать**, чтобы понять проблему:

```kotlin
@Composable
fun BrokenCounter() {
    // ОШИБКА: Обычная переменная.
    // При рекомпозиции (перерисовке) функция запускается с начала,
    // и count снова становится 0.
    var count = 0 

    Button(onClick = { count++ }) {
        Text("Нажато $count раз") // Всегда будет показывать "0"
    }
}

```

**А теперь исправим это:**

```kotlin
@Composable
fun WorkingCounter() {
    // 1. mutableStateOf - создает "наблюдаемую" коробку с данными.
    // 2. remember - говорит Compose: "Сохрани этот объект в памяти между перерисовками".
    // 3. by - делегат (syntax sugar), позволяет читать count как Int, а не как State<Int>.
    var count by remember { mutableStateOf(0) }

    Button(onClick = { 
        count++ // Изменяем State -> Compose видит это -> Запускает Recomposition
    }) {
        Text("Нажато $count раз") // Теперь цифра обновляется!
    }
}

```

### 3. `remember` vs `rememberSaveable`

Мы уже знаем из Главы 2, что при повороте экрана Activity уничтожается.

* `remember` хранит данные, пока Composable функция находится на экране. При повороте экрана данные **потеряются** (сбросятся в 0).
* `rememberSaveable` сохраняет данные в `Bundle` (системное хранилище), поэтому они переживают поворот экрана и смерть процесса.

**Правило:** Для ввода пользователя (текст в поле, чекбоксы, счетчики) используйте `rememberSaveable`.

### 4. State Hoisting (Поднятие состояния)

Это главный архитектурный паттерн в Compose.

**Проблема:** Если мы засунем состояние (`var count by ...`) внутрь кнопки, то другие части экрана не узнают об этом числе. А если нам нужно показать это число в заголовке экрана, а менять его в кнопке внизу?

**Решение:** Мы "поднимаем" состояние вверх, к общему родителю.

* **Родитель** владеет состоянием.
* **Ребенок** получает данные как параметр и шлет "события" (лямбды) наверх.

Это называется **Unidirectional Data Flow (Однонаправленный поток данных)**:
⬇️ **Данные (State)** текут вниз.
⬆️ **События (Events)** летят наверх.

#### Пример State Hoisting:

```kotlin
// 1. STATEFUL (Умный) компонент
// Он владеет состоянием и логикой
@Composable
fun CounterScreen() {
    // Состояние живет здесь
    var count by rememberSaveable { mutableStateOf(0) }

    Column(
        modifier = Modifier.fillMaxSize(),
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        // Мы передаем данные вниз
        Text(text = "Общий счет: $count", style = MaterialTheme.typography.displayMedium)
        
        Spacer(modifier = Modifier.height(16.dp))
        
        // Передаем событие вниз
        CounterButton(
            currentCount = count,
            onIncrement = { count++ } // Лямбда: что делать при клике
        )
    }
}

// 2. STATELESS (Глупый) компонент
// Он просто рисует то, что ему дали, и сообщает о кликах.
// Такие компоненты легко переиспользовать и тестировать.
@Composable
fun CounterButton(
    currentCount: Int,      // Данные (State)
    onIncrement: () -> Unit // Событие (Event)
) {
    Button(onClick = { onIncrement() }) {
        Text("Увеличить (сейчас $currentCount)")
    }
}

```

### 5. Поля ввода (TextField)

Работа с текстом в Compose идеально демонстрирует State Hoisting. `TextField` сам по себе не хранит текст, который вы печатаете! Вы должны сами обновлять его состояние.

```kotlin
@Composable
fun SimpleInput() {
    // Храним текст, который ввел пользователь
    var text by remember { mutableStateOf("") }

    TextField(
        value = text, // 1. Что показывать? Текущее состояние.
        onValueChange = { newText -> 
            // 2. Пользователь нажал клавишу. Пришло событие с новым текстом.
            // Мы ОБЯЗАНЫ обновить наш state, иначе в поле ничего не появится.
            text = newText 
        },
        label = { Text("Введите имя") }
    )
}

```

---

### ✅ Итог Главы 4

1. **Recomposition**: Compose перерисовывает UI при изменении данных.
2. **State**: Используйте `mutableStateOf` для создания наблюдаемых переменных.
3. **Remember**: Используйте `remember`, чтобы переменная не сбрасывалась при перерисовке.
4. **RememberSaveable**: Используйте, чтобы пережить поворот экрана.
5. **State Hoisting**: Делайте компоненты "глупыми" (Stateless), передавая данные в параметрах, а действия — в лямбдах. Это основа чистой архитектуры.

---

# Глава 5. Списки (Lazy Lists)

### 1. Почему не обычный Column?

Если вы просто поместите 1000 элементов в `Column` и добавите `.verticalScroll()`, приложение начнет тормозить или упадет с ошибкой `OutOfMemory`. Почему?
Потому что `Column` создает и рендерит **ВСЕ** элементы сразу, даже те, которые находятся далеко за пределами экрана.

**LazyColumn** (ленивая колонка) работает умно: она создает только те элементы, которые **видны на экране сейчас**. Когда вы скроллите вниз, старые элементы сверху уничтожаются (или переиспользуются), а новые снизу создаются.

### 2. Основы LazyColumn

Синтаксис отличается от обычного `Column`. Вместо прямого вложения элементов мы используем DSL-блок (Domain Specific Language).

```kotlin
@Composable
fun SimpleList() {
    LazyColumn(
        modifier = Modifier.fillMaxSize(), // Растягиваем на весь экран
        contentPadding = PaddingValues(16.dp), // Отступы по краям списка
        verticalArrangement = Arrangement.spacedBy(8.dp) // Отступы МЕЖДУ элементами
    ) {
        // 1. Одиночный элемент (как Header)
        item {
            Text(
                text = "Список фруктов",
                style = MaterialTheme.typography.headlineMedium
            )
        }

        // 2. Список элементов (из массива/листа)
        items(50) { index ->
            Text("Элемент #$index")
        }
    }
}

```

### 3. Отображение реальных данных (`items`)

Обычно у нас есть список объектов (Data Classes). Для этого используется функция `items()` (обратите внимание на импорт: `import androidx.compose.foundation.lazy.items`).

Допустим, у нас есть модель:

```kotlin
data class Fruit(val id: Int, val name: String, val price: Int)

val fruits = listOf(
    Fruit(1, "Яблоко", 100),
    Fruit(2, "Банан", 150),
    Fruit(3, "Апельсин", 200),
    // ... еще 100 фруктов
)

```

Верстка списка:

```kotlin
@Composable
fun FruitList(fruitList: List<Fruit>) {
    LazyColumn {
        items(
            items = fruitList,
            // ОПТИМИЗАЦИЯ: Указываем уникальный ключ для каждого элемента.
            // Это помогает Compose не перерисовывать весь список при удалении/перемещении элемента.
            key = { fruit -> fruit.id } 
        ) { fruit ->
            // Здесь мы вызываем Composable для отрисовки ОДНОЙ строки
            FruitItem(fruit = fruit)
        }
    }
}

@Composable
fun FruitItem(fruit: Fruit) {
    Row(
        modifier = Modifier
            .fillMaxWidth()
            .background(Color.LightGray)
            .padding(16.dp),
        horizontalArrangement = Arrangement.SpaceBetween
    ) {
        Text(text = fruit.name)
        Text(text = "${fruit.price} ₽", fontWeight = FontWeight.Bold)
    }
}

```

### 4. Кликабельность элементов

Помните про State Hoisting? Не обрабатывайте клик внутри элемента списка. Передавайте событие наверх.

```kotlin
@Composable
fun FruitListScreen() {
    // В реальном приложении этот список придет из ViewModel
    val fruits = remember { getSampleFruits() } 

    LazyColumn {
        items(fruits, key = { it.id }) { fruit ->
            FruitItem(
                fruit = fruit,
                onItemClick = { clickedFruit ->
                    Log.d("List", "Нажали на: ${clickedFruit.name}")
                }
            )
        }
    }
}

@Composable
fun FruitItem(
    fruit: Fruit, 
    onItemClick: (Fruit) -> Unit // Лямбда для клика
) {
    Row(
        modifier = Modifier
            .fillMaxWidth()
            .clickable { onItemClick(fruit) } // Передаем кликнутый объект наверх
            .padding(16.dp)
    ) {
        // ... содержимое
    }
}

```

### 5. LazyRow и Grids

* **LazyRow**: Работает точно так же, как `LazyColumn`, но список горизонтальный.
* **LazyVerticalGrid**: Сетка (таблица).

Пример сетки (галерея фото):

```kotlin
LazyVerticalGrid(
    // GridCells.Fixed(3) - 3 колонки фиксированной ширины
    // GridCells.Adaptive(128.dp) - поместить столько колонок, сколько влезет (мин ширина 128dp)
    columns = GridCells.Adaptive(minSize = 128.dp), 
    contentPadding = PaddingValues(8.dp)
) {
    items(photos) { photo ->
        PhotoItem(photo)
    }
}

```

### 6. Оптимизация производительности

Списки — самое "узкое" место по производительности.

1. **Всегда используйте `key**`: Без ключей Compose может путаться при скролле и пересоздавать элементы лишний раз.
2. **Не делайте тяжелых вычислений в `item**`: Не нужно форматировать даты или фильтровать списки прямо внутри `LazyColumn`. Делайте это заранее или во ViewModel.
3. **Загрузка картинок**: Используйте библиотеку **Coil**. Она автоматически кэширует картинки и освобождает память, когда элемент уходит с экрана.

```kotlin
// Пример с Coil
AsyncImage(
    model = "https://example.com/image.jpg",
    contentDescription = null,
    modifier = Modifier.size(100.dp)
)

```

---

### ✅ Итог Главы 5

1. Используйте **LazyColumn** для вертикальных списков и **LazyRow** для горизонтальных.
2. `item {}` — для одиночных блоков (заголовки).
3. `items(list) {}` — для генерации списка из данных.
4. Всегда задавайте параметр `key` в `items` для плавной работы анимаций и скролла.

---

# Глава 6. Архитектура MVVM (Model - View - ViewModel)

### 1. Зачем это нужно?

В Android есть фундаментальная проблема: **Activity/Composable умирают часто**. Повернули экран — Activity умерла. Сменили язык — умерла. Система убила процесс — умерла.

Если вы храните данные (список загруженных пользователей) прямо в UI, то при повороте экрана вы будете загружать их заново. Это трата трафика и времени пользователя.

**Решение:** Вынести данные и логику в отдельный класс, который живет дольше, чем экран. Этот класс называется **ViewModel**.

### 2. Три компонента MVVM

1. **Model (Модель)**: Слой данных. Это ваши Data Classes, Базы данных (Room) и Сеть (Retrofit). Модель ничего не знает об экране. Она просто отдает данные ("Вот список пользователей").
2. **View (Представление)**: Ваш UI (Jetpack Compose). Он **глупый**. Он ничего не решает. Он просто:
* Подписывается на данные из ViewModel.
* Рисует их.
* Передает нажатия (клики) во ViewModel.


3. **ViewModel (Вью-Модель)**: Посредник.
* Хранит состояние экрана (`State`).
* **Переживает поворот экрана.**
* Принимает действия от View ("Юзер нажал кнопку"), обрабатывает их (обращается к Model) и обновляет State.



### 3. Реализация ViewModel в коде

Для работы нам понадобится библиотека (обычно уже включена в новые проекты):
`implementation("androidx.lifecycle:lifecycle-viewmodel-compose:2.6.x")`

#### Шаг 1: Описываем Состояние (State)

Вместо кучи разрозненных переменных (`isLoading`, `userList`, `errorText`), мы создаем один `data class`, описывающий всё состояние экрана сразу.

```kotlin
// Состояние экрана "Список пользователей"
data class UsersUiState(
    val isLoading: Boolean = false,
    val users: List<String> = emptyList(),
    val errorMessage: String? = null
)

```

#### Шаг 2: Создаем ViewModel

Мы используем `StateFlow`. Это современная замена устаревшему `LiveData`.

**Паттерн "Backing Property" (Скрытое свойство):**
Мы создаем две версии переменной состояния:

1. `private val _uiState` (Mutable) — чтобы менять состояние можно было **только** внутри ViewModel.
2. `val uiState` (Immutable) — публичная версия, которую View может **только читать**.

```kotlin
import androidx.lifecycle.ViewModel
import kotlinx.coroutines.flow.MutableStateFlow
import kotlinx.coroutines.flow.StateFlow
import kotlinx.coroutines.flow.asStateFlow
import kotlinx.coroutines.flow.update

class UsersViewModel : ViewModel() {

    // 1. Закрытое изменяемое состояние. Начальное значение - всё пусто.
    private val _uiState = MutableStateFlow(UsersUiState())
    
    // 2. Открытый поток для чтения (View подпишется на него)
    val uiState: StateFlow<UsersUiState> = _uiState.asStateFlow()

    // Симуляция загрузки данных (в реальности тут будет запрос в сеть)
    fun loadUsers() {
        // Ставим флаг загрузки
        _uiState.update { currentState ->
            currentState.copy(isLoading = true, errorMessage = null)
        }

        // ... тут должна быть асинхронная работа (Coroutines), 
        // пока сделаем вид, что данные пришли мгновенно
        val fakeUsers = listOf("Alice", "Bob", "Charlie")

        // Обновляем состояние: убираем загрузку, сохраняем список
        _uiState.update { 
            it.copy(isLoading = false, users = fakeUsers)
        }
    }
    
    fun deleteUser(user: String) {
        // Логика удаления
        _uiState.update { state ->
             val newList = state.users - user
             state.copy(users = newList)
        }
    }
}

```

#### Шаг 3: Связываем с UI (Compose)

В Composable функции мы получаем экземпляр ViewModel и "слушаем" поток данных.

```kotlin
import androidx.lifecycle.viewmodel.compose.viewModel

@Composable
fun UsersScreen(
    // viewModel() - специальная функция, которая либо создаст новую VM,
    // либо вернет существующую, если был поворот экрана.
    viewModel: UsersViewModel = viewModel() 
) {
    // 1. Подписываемся на StateFlow.
    // collectAsState преобразует Flow в Compose State.
    // Теперь любое изменение в VM автоматически перерисует этот экран.
    val state by viewModel.uiState.collectAsState()

    // 2. Рисуем UI в зависимости от состояния
    if (state.isLoading) {
        CircularProgressIndicator() // Крутилка
    } else {
        Column {
            Button(onClick = { viewModel.loadUsers() }) {
                Text("Загрузить пользователей")
            }
            
            LazyColumn {
                items(state.users) { user ->
                    UserRow(
                        name = user, 
                        onDeleteClick = { viewModel.deleteUser(user) }
                    )
                }
            }
        }
    }
}

```

### 4. Unidirectional Data Flow (UDF)

Обратите внимание на поток данных в коде выше. Это ключевой принцип современной разработки:

1. **Event**: Пользователь нажал кнопку -> Вызвался метод `viewModel.loadUsers()`.
2. **Update**: ViewModel сходила за данными и обновила `_uiState`.
3. **State**: Новое состояние прилетело в UI через `collectAsState()`.
4. **UI**: Экран перерисовался.

UI никогда не меняет данные сам. Он просит об этом ViewModel.

---

### ✅ Итог Главы 6

1. **MVVM** разделяет логику (ViewModel) и отображение (View).
2. **ViewModel** живет дольше, чем экран, и хранит данные при повороте устройства.
3. **UiState** (Data Class) описывает всё, что происходит на экране.
4. **StateFlow** — это "труба", по которой данные текут из ViewModel в UI.

---

# Глава 7. Асинхронность и Coroutines (Корутины)

### 1. Главный поток (Main Thread)

Представьте, что ваше приложение — это однополосная дорога. По ней едут машины (отрисовка кадров UI). Если на дорогу выйдет грузовик и сломается (тяжелая операция: чтение файла, запрос в сеть), пробка образуется мгновенно. Экран застынет.

Чтобы этого избежать, мы должны "убрать грузовик" на соседнюю полосу (фоновый поток), а когда он доставит груз, вернуть результат на главную дорогу.

**Корутины** — это "легкие потоки". Они позволяют писать асинхронный код так, будто он выполняется последовательно, без лапши из колбэков (`callback hell`).

### 2. Ключевое слово `suspend`

Если вы видите функцию с пометкой `suspend` — это значит, что эта функция может **приостановить** свое выполнение, не блокируя поток.

> Аналогия: Вы варите суп (Main Thread). Вам нужно подождать, пока закипит вода.
> **Blocking (Java Thread):** Вы стоите и смотрите на кастрюлю 10 минут, ничего больше не делая. Кухня парализована.
> **Suspending (Coroutines):** Вы ставите таймер и уходите резать овощи. Вы (поток) свободны для других дел. Когда вода закипит, вы вернетесь к кастрюле.

### 3. Dispatchers (Диспетчеры) — Где выполнять работу?

Корутины должны знать, на каком пуле потоков им работать.

| Диспетчер               | Для чего нужен                   | Примеры                                                          |
| ----------------------- | -------------------------------- | ---------------------------------------------------------------- |
| **Dispatchers.Main**    | Работа с UI                      | Обновление текста, анимации, вызов `setValue`.                   |
| **Dispatchers.IO**      | Ввод/Вывод данных (Input/Output) | Запросы к серверу (Retrofit), работа с БД (Room), чтение файлов. |
| **Dispatchers.Default** | Тяжелые вычисления (CPU)         | Обработка фото, парсинг большого JSON, сложные алгоритмы.        |

### 4. ViewModelScope — Безопасный запуск

Корутину нельзя запустить "в воздухе". Ей нужен **Scope** (Область жизни).
Если мы запустим корутину во ViewModel, а пользователь закроет экран, корутина должна **отмениться**, чтобы не тратить батарею и память.

Для этого в Android есть готовый `viewModelScope`. Он автоматически отменяет все запущенные задачи, когда ViewModel умирает (`onCleared`).

### 5. Практика: Исправляем загрузку пользователей

Вернемся к примеру из прошлой главы и напишем его правильно.

```kotlin
class UsersViewModel : ViewModel() {

    private val _uiState = MutableStateFlow(UsersUiState())
    val uiState = _uiState.asStateFlow()

    fun loadUsers() {
        // 1. Запускаем корутину в скоупе ViewModel (на Main Thread по умолчанию)
        viewModelScope.launch {
            
            // Ставим загрузку (это быстро, можно на Main)
            _uiState.update { it.copy(isLoading = true) }

            try {
                // 2. СМЕНА ПОТОКА.
                // withContext приостанавливает функцию loadUsers,
                // переключается на IO поток, выполняет блок кода,
                // и возвращает результат обратно.
                val usersFromNetwork = withContext(Dispatchers.IO) {
                    // Имитация долгого запроса (2 секунды)
                    delay(2000) 
                    // Возвращаем результат
                    listOf("Alice", "Bob", "Charlie") 
                }

                // 3. Мы снова на Main Thread (автоматически после withContext).
                // Можем безопасно обновлять UI.
                _uiState.update { 
                    it.copy(isLoading = false, users = usersFromNetwork) 
                }

            } catch (e: Exception) {
                // Если пропал интернет или сервер упал
                _uiState.update { 
                    it.copy(isLoading = false, errorMessage = e.message) 
                }
            }
        }
    }
}

```

### 6. Async / Await — Параллельное выполнение

Иногда нужно сделать два запроса одновременно (например, загрузить профиль юзера и список его друзей), а не ждать их по очереди.

* `launch` — "Запустил и забыл". Возвращает `Job`.
* `async` — "Запустил и жду результат". Возвращает `Deferred` (отложенный результат).

```kotlin
fun loadDashboard() {
    viewModelScope.launch {
        // Запускаем две задачи ПАРАЛЛЕЛЬНО
        val profileDeferred = async(Dispatchers.IO) { api.getProfile() }
        val friendsDeferred = async(Dispatchers.IO) { api.getFriends() }

        // await() приостановит эту корутину, пока результат не будет готов.
        // Общее время выполнения будет равно времени самой долгой задачи,
        // а не сумме времен (как было бы при последовательном вызове).
        val profile = profileDeferred.await()
        val friends = friendsDeferred.await()

        _uiState.update { it.copy(profile = profile, friends = friends) }
    }
}

```

---

### ✅ Итог Главы 7

1. **Главный поток** священен. Не блокируйте его.
2. **Suspend функции** позволяют писать асинхронный код в синхронном стиле.
3. **Dispatchers.IO** — ваш лучший друг для работы с сетью и БД.
4. **viewModelScope** — безопасное место для запуска корутин. При закрытии экрана все запросы отменяются автоматически.
5. **withContext** используется для переключения между потоками внутри одной корутины.

---

# Глава 8. Работа с сетью: Retrofit & Parsing JSON

### 1. Почему Retrofit?

Внутри Android есть встроенные средства для работы с сетью (`HttpURLConnection`), но пользоваться ими — это пытка. Нужно вручную открывать соединение, читать байты, превращать их в строку, а потом парсить JSON.

**Retrofit** делает все это за вас:

1. Вы описываете API как **обычный Kotlin интерфейс**.
2. Retrofit автоматически генерирует код для запросов.
3. Он сам превращает JSON-ответ сервера в готовые объекты Kotlin (Data Classes).

### 2. Подготовка (Manifest и Gradle)

**Шаг 1. Разрешение на Интернет**
Без этой строчки в `AndroidManifest.xml` приложение упадет при первой же попытке выйти в сеть.

```xml
<manifest ...>
    <uses-permission android:name="android.permission.INTERNET" />
    </manifest>

```

**Шаг 2. Зависимости (build.gradle.kts)**
Нам нужны сам Retrofit и **Converter** (библиотека, которая переводит JSON в объекты). Самый популярный конвертер — Gson (от Google), но современный стандарт — Kotlin Serialization или Moshi. Для простоты начнем с Gson.

```kotlin
dependencies {
    // Retrofit
    implementation("com.squareup.retrofit2:retrofit:2.9.0")
    // Конвертер для JSON (Gson)
    implementation("com.squareup.retrofit2:converter-gson:2.9.0")
    // OkHttp Logging (чтобы видеть запросы в логах - мастхэв для отладки)
    implementation("com.squareup.okhttp3:logging-interceptor:4.11.0")
}

```

### 3. Модель данных (Data Model)

Допустим, сервер возвращает нам такой JSON при запросе профиля:

```json
{
  "id": 101,
  "username": "alex_coder",
  "avatar_url": "https://example.com/img.png",
  "is_admin": false
}

```

Мы создаем Data Class. Используем аннотацию `@SerializedName`, если имена полей в JSON (snake_case) отличаются от имен в Kotlin (camelCase).

```kotlin
import com.google.gson.annotations.SerializedName

data class UserProfile(
    val id: Int,
    
    // В JSON поле называется "username", мапим его в переменную name
    @SerializedName("username") 
    val name: String,
    
    @SerializedName("avatar_url")
    val avatarUrl: String?, // Может быть null, если аватарки нет
    
    @SerializedName("is_admin")
    val isAdmin: Boolean
)

```

### 4. Описание API (Интерфейс)

Мы создаем интерфейс и размечаем методы аннотациями HTTP (`@GET`, `@POST`, `@PUT`, `@DELETE`).
**Важно:** Мы делаем функции `suspend`, чтобы Retrofit понимал, что мы используем Coroutines.

```kotlin
import retrofit2.http.GET
import retrofit2.http.Path
import retrofit2.http.Query

interface ApiService {

    // 1. Простой GET запрос: https://api.example.com/users/profile
    @GET("users/profile")
    suspend fun getMyProfile(): UserProfile

    // 2. Запрос с динамическим путем: https://api.example.com/users/101
    @GET("users/{id}")
    suspend fun getUserById(@Path("id") userId: Int): UserProfile

    // 3. Запрос с параметрами поиска: https://api.example.com/search?q=kotlin&page=1
    @GET("search")
    suspend fun searchUsers(
        @Query("q") query: String,
        @Query("page") page: Int
    ): List<UserProfile>
}

```

### 5. Создание экземпляра Retrofit (Сборка)

Обычно этот код пишется один раз в **DI модуле** (Hilt/Dagger). Но пока мы не изучили DI, создадим Singleton объект.

```kotlin
import okhttp3.OkHttpClient
import okhttp3.logging.HttpLoggingInterceptor
import retrofit2.Retrofit
import retrofit2.converter.gson.GsonConverterFactory

object RetrofitClient {
    private const val BASE_URL = "https://api.example.com/"

    // Настраиваем HTTP клиент (OkHttp)
    private val okHttpClient = OkHttpClient.Builder()
        .addInterceptor(HttpLoggingInterceptor().apply {
            // Логировать тело запроса и ответа (полезно при отладке)
            level = HttpLoggingInterceptor.Level.BODY 
        })
        .build()

    // Создаем сам Retrofit
    val api: ApiService by lazy {
        Retrofit.Builder()
            .baseUrl(BASE_URL)
            .client(okHttpClient) // Подключаем наш клиент с логами
            .addConverterFactory(GsonConverterFactory.create()) // Подключаем парсер JSON
            .build()
            .create(ApiService::class.java) // Создаем реализацию интерфейса
    }
}

```

### 6. Использование во ViewModel

Теперь соединяем всё вместе. ViewModel вызывает метод `api`, получает данные и кладет их в `State`.

```kotlin
class ProfileViewModel : ViewModel() {

    private val _uiState = MutableStateFlow<UiState>(UiState.Loading)
    val uiState = _uiState.asStateFlow()

    fun loadProfile() {
        viewModelScope.launch {
            _uiState.value = UiState.Loading
            
            try {
                // ВЫЗОВ СЕТИ
                // Нам НЕ нужно переключаться на Dispatchers.IO вручную.
                // Retrofit (версии 2.6.0+) делает это под капотом для suspend функций.
                val profile = RetrofitClient.api.getMyProfile()
                
                _uiState.value = UiState.Success(profile)
                
            } catch (e: Exception) {
                // Обработка ошибок (нет интернета, 404, 500)
                _uiState.value = UiState.Error("Ошибка загрузки: ${e.message}")
            }
        }
    }
}

```

---

### ✅ Итог Главы 8

1. **Retrofit** превращает HTTP API в Kotlin Interface.
2. **Gson/Moshi/KotlinX** превращают JSON текст в объекты Kotlin.
3. В `ApiService` используем `suspend` функции, чтобы работать с корутинами.
4. **OkHttp Interceptor** — незаменимая вещь. Он позволяет видеть в Logcat (вкладка логов в Android Studio) всё, что отправляется и приходит от сервера.

---

# Глава 9. Dependency Injection (Hilt)

### 1. Что такое Hilt и зачем он нужен?

Hilt — это библиотека, которая берет на себя создание и хранение объектов. Она строит "Граф зависимостей" (Dependency Graph).

Представьте, что ваше приложение — это автомобиль.

* **Без DI:** Двигатель сам создает поршни, поршни сами плавят металл... Хаос.
* **С DI (Hilt):** Есть сборочный конвейер. Он создает поршни, вставляет их в двигатель, а готовый двигатель ставит в машину. Машина просто "получает" двигатель и едет.

### 2. Настройка проекта

Hilt требует настройки Gradle.

**build.gradle.kts (Project level):**

```kotlin
plugins {
    // ...
    id("com.google.dagger.hilt.android") version "2.50" apply false
}

```

**build.gradle.kts (Module: app):**

```kotlin
plugins {
    id("kotlin-kapt") // Или ksp, но kapt для Hilt пока стабильнее/привычнее
    id("com.google.dagger.hilt.android")
}

dependencies {
    implementation("com.google.dagger.hilt.android:2.50")
    kapt("com.google.dagger.hilt.android.compiler:2.50")
}

```

### 3. @HiltAndroidApp — Корень всего

Первое, что нужно сделать — создать класс `Application` и повесить аннотацию. Это запускает генерацию кода Hilt.

```kotlin
@HiltAndroidApp
class MyApplication : Application() {
    // Этот класс должен быть прописан в AndroidManifest.xml:
    // <application android:name=".MyApplication" ... >
}

```

### 4. Как учить Hilt создавать объекты?

Есть два способа объяснить Hilt, как создать объект.

#### Способ А: Constructor Injection (Простой)

Если класс написали ВЫ, просто добавьте `@Inject` перед конструктором.

```kotlin
// Мы сами написали этот класс, поэтому можем добавить @Inject
class AnalyticsService @Inject constructor() {
    fun trackEvent(name: String) { /* ... */ }
}

```

#### Способ Б: Modules (Сложный, но мощный)

Если класс **чужой** (например, `Retrofit`, `OkHttpClient`, `RoomDatabase`), вы не можете залезть в библиотеку и дописать `@Inject`.
Для этого создаются **Модули** (`@Module`). Это инструкции по сборке.

Давайте перепишем наш `RetrofitClient` из прошлой главы на Hilt.

```kotlin
@Module
// InstallIn говорит: "Где будут жить эти объекты?"
// SingletonComponent::class -> живут, пока живет приложение (единственные экземпляры).
@InstallIn(SingletonComponent::class)
object NetworkModule {

    @Provides
    @Singleton // Создать один раз и переиспользовать
    fun provideOkHttpClient(): OkHttpClient {
        return OkHttpClient.Builder()
            .addInterceptor(HttpLoggingInterceptor().setLevel(HttpLoggingInterceptor.Level.BODY))
            .build()
    }

    @Provides
    @Singleton
    fun provideRetrofit(okHttpClient: OkHttpClient): Retrofit {
        // Hilt сам найдет, как создать okHttpClient (через метод выше) и передаст сюда
        return Retrofit.Builder()
            .baseUrl("https://api.example.com/")
            .client(okHttpClient)
            .addConverterFactory(GsonConverterFactory.create())
            .build()
    }

    @Provides
    @Singleton
    fun provideApiService(retrofit: Retrofit): ApiService {
        return retrofit.create(ApiService::class.java)
    }
}

```

### 5. Внедрение во ViewModel (`@HiltViewModel`)

Теперь самое приятное. Нашей ViewModel больше не нужно знать, как создается `Retrofit`. Она просто просит `ApiService` в конструкторе.

```kotlin
@HiltViewModel
class UsersViewModel @Inject constructor(
    private val api: ApiService // Hilt сам найдет это в NetworkModule и положит сюда
) : ViewModel() {

    fun loadUsers() {
        viewModelScope.launch {
            // Используем api. Никаких синглтонов RetrofitClient.api!
            val users = api.getUsers()
        }
    }
}

```

### 6. Внедрение в UI (`@AndroidEntryPoint`)

Чтобы экран (Activity или Fragment) мог получить ViewModel с зависимостями, его нужно пометить аннотацией.

```kotlin
@AndroidEntryPoint // <--- ОБЯЗАТЕЛЬНО! Без этого упадет.
class MainActivity : ComponentActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            // Hilt знает, как создать эту VM и внедрить в нее ApiService
            val viewModel: UsersViewModel = viewModel() 
            
            UsersScreen(viewModel)
        }
    }
}

```

### 7. Scopes (Области видимости) — Важная теория

Hilt позволяет управлять временем жизни объектов.

| Компонент Hilt                | Scope (Аннотация)         | Время жизни                                |
| ----------------------------- | ------------------------- | ------------------------------------------ |
| **SingletonComponent**        | `@Singleton`              | Все время работы приложения (БД, Сеть)     |
| **ActivityRetainedComponent** | `@ActivityRetainedScoped` | Переживает поворот экрана (для ViewModel)  |
| **ActivityComponent**         | `@ActivityScoped`         | Пока жива Activity (Навигатор, UI-хелперы) |
| **FragmentComponent**         | `@FragmentScoped`         | Пока жив Фрагмент                          |

**Почему это важно:** Если вы пометите объект `@Singleton`, он останется в памяти навсегда, даже если он нужен был на 5 минут. Если не пометите ничем — Hilt будет создавать новый экземпляр при каждом запросе (New Instance).

---

### ✅ Итог Главы 9

1. **Hilt** избавляет нас от ручного создания объектов.
2. `@HiltAndroidApp` — ставим на класс Application.
3. `@AndroidEntryPoint` — ставим на Activity/Fragment.
4. `@Inject` в конструктор — для своих классов.
5. **Modules (`@Module`, `@Provides`)** — для сторонних библиотек (Retrofit, Room).
6. `@HiltViewModel` — магия для ViewModel.

---

# Глава 10. Локальная база данных (Room)

### 1. Что такое Room?

Room — это не сама база данных. Это **обертка (ORM)** над старой доброй SQL-базой данных **SQLite**, которая встроена в каждый Android-телефон.

SQLite писать вручную больно (нужно писать SQL-запросы строками, парсить курсоры). Room позволяет работать с базой как с обычными объектами Kotlin, а SQL-код генерирует за вас.

**Архитектура Room состоит из трех компонентов:**

1. **Entity (Сущность)**: Таблица в базе данных.
2. **DAO (Data Access Object)**: Интерфейс с методами для чтения/записи (SQL-запросы).
3. **Database**: Класс-точка входа, который хранит базу и отдает DAO.

### 2. Шаг 1: Entity (Таблица)

Превращаем наш Data Class в таблицу базы данных.

**Важно:** Обычно не смешивают модели API (из Retrofit) и модели БД. Лучше создать отдельный класс `UserEntity` и мапить их друг в друга. Но для простоты примера используем один.

```kotlin
import androidx.room.Entity
import androidx.room.PrimaryKey

// @Entity говорит, что это таблица с именем "users"
@Entity(tableName = "users")
data class UserEntity(
    // @PrimaryKey - уникальный ключ. autoGenerate = false, так как ID приходит с сервера.
    @PrimaryKey 
    val id: Int,
    
    val name: String,
    
    // В базе имена колонок часто пишут в snake_case
    @androidx.room.ColumnInfo(name = "avatar_url")
    val avatarUrl: String?
)

```

### 3. Шаг 2: DAO (Запросы)

Здесь мы описываем, что мы хотим делать с данными.

**Магия Flow:**
Если метод возвращает `Flow<List<UserEntity>>`, Room будет **автоматически** присылать новый список каждый раз, когда данные в базе изменятся. Вам не нужно делать повторный запрос `getUsers()`!

```kotlin
import androidx.room.Dao
import androidx.room.Insert
import androidx.room.OnConflictStrategy
import androidx.room.Query
import kotlinx.coroutines.flow.Flow

@Dao
interface UserDao {

    // Чтение всех юзеров. 
    // Возвращаем Flow -> мы подпишемся на обновления таблицы.
    @Query("SELECT * FROM users")
    fun getAllUsers(): Flow<List<UserEntity>>

    // Запись списка.
    // OnConflictStrategy.REPLACE -> Если юзер с таким ID уже есть, обновить его данные.
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertAll(users: List<UserEntity>)

    // Удаление всего (для очистки кэша)
    @Query("DELETE FROM users")
    suspend fun clearAll()
}

```

### 4. Шаг 3: Database (Сборка)

Абстрактный класс, который наследуется от `RoomDatabase`.

```kotlin
import androidx.room.Database
import androidx.room.RoomDatabase

// Указываем все сущности (таблицы) и версию базы (нужна для миграций)
@Database(entities = [UserEntity::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    // Абстрактный метод, который вернет нам реализацию DAO
    abstract fun userDao(): UserDao
}

```

### 5. Шаг 4: Подключаем через Hilt (DI)

Помните, мы не создаем объекты руками? Добавим базу данных в наш DI модуль.

```kotlin
@Module
@InstallIn(SingletonComponent::class)
object DatabaseModule {

    @Provides
    @Singleton
    fun provideDatabase(@ApplicationContext context: Context): AppDatabase {
        return Room.databaseBuilder(
            context,
            AppDatabase::class.java,
            "my_app_database.db"
        )
        .fallbackToDestructiveMigration() // ВНИМАНИЕ: При смене версии БД старая удалится (для разработки ок)
        .build()
    }

    // Предоставляем DAO отдельно, чтобы в репозитории не просить всю базу
    @Provides
    fun provideUserDao(database: AppDatabase): UserDao {
        return database.userDao()
    }
}

```

### 6. Single Source of Truth (Единственный источник правды)

Теперь самое главное архитектурное правило Senior-разработчика: **Repository Pattern**.

UI не должен знать, откуда берутся данные (из сети или из базы). Репозиторий скрывает эту логику.
**Идеальная схема:**

1. UI подписывается на базу данных (через `Flow`).
2. Репозиторий запрашивает свежие данные из сети.
3. Репозиторий сохраняет данные в базу.
4. База данных автоматически уведомляет UI о новых данных.

```kotlin
class UserRepository @Inject constructor(
    private val api: ApiService,
    private val dao: UserDao
) {
    // 1. Источник данных для UI - всегда локальная база!
    // UI получит данные мгновенно (если они были в кэше).
    val users: Flow<List<UserEntity>> = dao.getAllUsers()

    // 2. Метод обновления данных
    suspend fun refreshUsers() {
        try {
            // Качаем с сети
            val remoteUsers = api.getUsers()
            
            // Мапим Network Model -> Database Entity (если классы разные)
            val entities = remoteUsers.map { 
                UserEntity(it.id, it.name, it.avatarUrl) 
            }
            
            // Сохраняем в базу. 
            // В ЭТОТ МОМЕНТ сработает Flow выше, и UI обновится сам!
            dao.insertAll(entities)
            
        } catch (e: Exception) {
            // Ошибка сети. Но UI не пустой, там старые данные из кэша.
            // Можно отправить ошибку в UI через отдельный StateFlow/Channel.
        }
    }
}

```

### 7. TypeConverters (Если данные сложные)

SQLite умеет хранить только примитивы (Int, String, Double). А что, если у юзера есть список тегов `val tags: List<String>`? SQLite упадет.

Нужен **TypeConverter**, который превратит `List<String>` в одну строку (например, JSON) при записи и обратно при чтении.

```kotlin
class Converters {
    @TypeConverter
    fun fromList(list: List<String>): String {
        return list.joinToString(",") // "tag1,tag2,tag3"
    }

    @TypeConverter
    fun toList(data: String): List<String> {
        return data.split(",")
    }
}
// Не забудьте добавить @TypeConverters(Converters::class) над классом AppDatabase

```

---

### ✅ Итог Главы 10

1. **Room** позволяет хранить данные локально и работать офлайн.
2. **Flow** в DAO делает базу реактивной: база сама сообщает об изменениях.
3. **Repository Pattern**: Сеть только обновляет базу. UI читает только из базы. Это обеспечивает мгновенный показ контента (Offline First).
4. **Hilt** создает экземпляр базы данных как Singleton.

---

# Глава 11. Навигация (Jetpack Navigation Compose)

### 1. Концепция: Single Activity

В современном Android используется архитектура **Single Activity**. У вас есть только одна `MainActivity`. Внутри нее находится контейнер (`NavHost`), который меняет содержимое экрана в зависимости от "маршрута" (Route).

Маршруты в Compose работают как URL в браузере:

* `"home"` — главный экран.
* `"users"` — список пользователей.
* `"users/123"` — детали пользователя с ID 123.

### 2. Подготовка

Добавляем зависимость в `build.gradle.kts`:
`implementation("androidx.navigation:navigation-compose:2.7.x")`

### 3. Маршруты (Routes)

Чтобы не писать строки `"home"` вручную по всему коду (и делать опечатки), создадим **Sealed Class**. Это хорошая практика (Best Practice).

```kotlin
// Файл: Screen.kt

sealed class Screen(val route: String) {
    // Простой маршрут
    data object UserList : Screen("user_list")
    
    // Маршрут с аргументом. {userId} - это плейсхолдер.
    data object UserDetails : Screen("user_details/{userId}") {
        // Вспомогательная функция, чтобы удобно подставлять ID
        fun createRoute(userId: Int) = "user_details/$userId"
    }
}

```

### 4. NavController и NavHost

В `MainActivity` мы настраиваем схему навигации.

* **NavController**: Объект-дирижер. Он знает, где мы сейчас, и умеет переходить (`Maps`) или возвращаться назад (`popBackStack`).
* **NavHost**: Контейнер, где отображаются экраны.

```kotlin
@Composable
fun MainApp() {
    // 1. Создаем контроллер. Он должен создаваться в корне иерархии.
    val navController = rememberNavController()

    // 2. Описываем граф навигации
    NavHost(
        navController = navController,
        startDestination = Screen.UserList.route // С чего начать?
    ) {
        
        // ЭКРАН 1: Список пользователей
        composable(route = Screen.UserList.route) {
            // Передаем контроллер вниз, чтобы экран мог вызвать навигацию
            UserListScreen(
                onUserClick = { userId ->
                    // Навигация: подставляем ID в маршрут
                    navController.navigate(Screen.UserDetails.createRoute(userId))
                }
            )
        }

        // ЭКРАН 2: Детали (принимает аргумент)
        composable(
            route = Screen.UserDetails.route,
            // Описываем аргументы, чтобы Compose знал, что userId - это Int
            arguments = listOf(navArgument("userId") { type = NavType.IntType })
        ) { backStackEntry ->
            // Достаем аргумент из "рюкзака" (backStackEntry)
            val userId = backStackEntry.arguments?.getInt("userId") ?: 0
            
            UserDetailsScreen(
                userId = userId,
                onBackClick = { 
                    navController.popBackStack() // Назад
                }
            )
        }
    }
}

```

### 5. Передача данных между экранами

Новички часто пытаются передать целый объект `User` (с именем, фото, биографией) в аргументах навигации.
**⛔️ Так делать нельзя!**

1. Маршрут имеет лимит по длине (как URL).
2. Если Android убьет процесс и восстановит его, большой объект может потеряться или вызвать переполнение памяти.

**✅ Правильный подход (SSOT):**
Передавайте только **ID** (`userId`).
Экран деталей (`UserDetailsScreen`) должен получить этот ID, передать его во `ViewModel`, а та загрузит полные данные из Базы Данных или Сети (как мы делали в прошлых главах).

### 6. Навигация и MVVM

**Вопрос на миллион:** Кто должен вызывать `navController.navigate()`?

1. **Вариант А:** Передать `navController` прямо во `ViewModel`.
* ❌ **Плохо.** ViewModel не должна знать о View и Android-компонентах. Это ломает Unit-тесты и вызывает утечки памяти.


2. **Вариант Б:** Обрабатывать навигацию в UI (Composable).
* ✅ **Хорошо.** ViewModel шлет событие "Нужно перейти", а UI реагирует.



#### Пример правильной архитектуры ("Event Bubbling"):

```kotlin
// Экран списка (UI)
@Composable
fun UserListScreen(
    onUserClick: (Int) -> Unit, // Лямбда для навигации (колбэк)
    viewModel: UserListViewModel = hiltViewModel()
) {
    val state by viewModel.uiState.collectAsState()

    LazyColumn {
        items(state.users) { user ->
            UserRow(
                user = user,
                // UI просто сообщает наверх: "Кликнули". 
                // Он не знает, куда это приведет.
                onClick = { onUserClick(user.id) } 
            )
        }
    }
}

```

### 7. Bottom Navigation (Нижнее меню)

Классическое меню снизу делается через `Scaffold`.

```kotlin
@Composable
fun AppWithBottomBar() {
    val navController = rememberNavController()

    Scaffold(
        bottomBar = {
            NavigationBar {
                // ... элементы меню, вызывающие navController.navigate()
            }
        }
    ) { innerPadding ->
        // NavHost должен учитывать отступы (innerPadding), 
        // иначе контент перекроется нижним меню.
        NavHost(
            navController = navController,
            startDestination = "home",
            modifier = Modifier.padding(innerPadding)
        ) {
            // ... экраны
        }
    }
}

```

### 8. Type Safe Navigation (Новый стандарт 2024+)

*Примечание для уровня Senior.*
Начиная с версии Navigation 2.8.0, Google рекомендует использовать **Type Safe Navigation** (на базе Kotlin Serialization). Вместо строк мы передаем объекты.

```kotlin
// Вместо строк "user/123" мы используем Serializable объекты
@Serializable
data class Profile(val id: Int)

// В NavHost:
composable<Profile> { backStackEntry ->
    val profile: Profile = backStackEntry.toRoute()
    // ...
}

// Навигация:
navController.navigate(Profile(id = 123))

```

*Это устраняет ошибки опечаток в строках маршрутов.*

---

### ✅ Итог Главы 11

1. **NavHost** — карта вашего приложения.
2. **NavController** — водитель.
3. Используйте **Sealed Classes** для хранения маршрутов (или Type Safe объекты).
4. Передавайте между экранами только **минимальные данные (ID)**.
5. **ViewModel** не должна держать ссылку на `NavController`. Используйте лямбды или каналы событий (Channels).

---

# Глава 12. Ресурсы, Темизация и Material Design 3

### 1. Ресурсы в Android (Resources)

Даже в Modern Android мы продолжаем хранить статические данные (строки, картинки) в папке `res`. Это позволяет системе автоматически подставлять нужные ресурсы в зависимости от конфигурации (язык устройства, размер экрана).

**Основные папки:**

* `res/values/strings.xml`: Тексты. **Никогда** не пишите текст хардкодом в коде (`Text("Привет")`). Используйте ресурсы! Это ключ к локализации.
* `res/drawable`: Векторные и растровые картинки.
* `res/mipmap`: Иконки приложения (для лаунчера).

**Доступ к ресурсам в Compose:**
Вместо `R.string.hello` (как ID) мы используем helper-функции:

```kotlin
// Текст
Text(text = stringResource(id = R.string.hello_world))

// Цвет (если он задан в colors.xml, хотя в Compose лучше использовать Theme)
val c = colorResource(id = R.color.teal_200)

// Картинка
Image(
    painter = painterResource(id = R.drawable.ic_logo),
    contentDescription = stringResource(R.string.logo_desc)
)

```

### 2. Material Design 3 (M3)

Compose "из коробки" заточен под **Material Design 3** (он же Material You).
Главная фишка M3 — **Dynamic Colors**. Если включить эту опцию, приложение будет окрашиваться в цвета обоев пользователя (на Android 12+).

Тема в Compose состоит из трех китов:

1. **Color Scheme** (Цветовая схема).
2. **Typography** (Шрифты).
3. **Shapes** (Формы/Закругления).

### 3. Настройка Темы (`Theme.kt`)

Когда вы создаете проект в Android Studio, она генерирует файл `ui/theme/Theme.kt`. Давайте разберем его.

```kotlin
// Определяем темную палитру
private val DarkColorScheme = darkColorScheme(
    primary = Purple80,
    secondary = PurpleGrey80,
    tertiary = Pink80,
    background = Color(0xFF1C1B1F) // Почти черный
)

// Определяем светлую палитру
private val LightColorScheme = lightColorScheme(
    primary = Purple40,
    secondary = PurpleGrey40,
    tertiary = Pink40,
    background = Color(0xFFFFFBFE) // Почти белый
)

@Composable
fun MyAppTheme(
    // Автоматически определяем, включена ли темная тема в системе
    darkTheme: Boolean = isSystemInDarkTheme(),
    // Включать ли динамические цвета (Android 12+)
    dynamicColor: Boolean = true,
    content: @Composable () -> Unit
) {
    // Логика выбора цветов (динамические, темные или светлые)
    val colorScheme = when {
        dynamicColor && Build.VERSION.SDK_INT >= Build.VERSION_CODES.S -> {
            val context = LocalContext.current
            if (darkTheme) dynamicDarkColorScheme(context) else dynamicLightColorScheme(context)
        }
        darkTheme -> DarkColorScheme
        else -> LightColorScheme
    }

    // Обертка MaterialTheme передает эти настройки всем вложенным элементам
    MaterialTheme(
        colorScheme = colorScheme,
        typography = Typography,
        content = content
    )
}

```

### 4. Семантические цвета (Semantic Colors)

**Главная ошибка новичка:** Использование `Color.Black` или `Color.White`.
Если вы напишете `Color.Black` для текста, то в темной теме (где фон черный) текст исчезнет.

**Правило:** Используйте цвета из темы (`MaterialTheme.colorScheme`). Они семантические (смысловые).

| Имя цвета        | Значение             | Пример использования            |
| ---------------- | -------------------- | ------------------------------- |
| **Primary**      | Основной цвет бренда | Фон главной кнопки (FAB)        |
| **OnPrimary**    | Цвет *на* основном   | Текст внутри главной кнопки     |
| **Background**   | Фон экрана           | Подложка всего экрана           |
| **OnBackground** | Цвет *на* фоне       | Основной текст статьи           |
| **Surface**      | Цвет поверхностей    | Карточки, BottomSheet, Меню     |
| **Error**        | Цвет ошибки          | Красный текст ошибки или иконка |

### 5. Практика: Адаптивный UI

Давайте перепишем нашу карточку товара из Главы 3, чтобы она поддерживала темную тему автоматически.

```kotlin
@Composable
fun ThemedProductCard(title: String) {
    Card(
        // Используем цвета из темы для фона карточки (Surface)
        colors = CardDefaults.cardColors(
            containerColor = MaterialTheme.colorScheme.surface, 
        ),
        elevation = CardDefaults.cardElevation(4.dp)
    ) {
        Column(modifier = Modifier.padding(16.dp)) {
            Text(
                text = title,
                // Используем стиль заголовка из темы (Typography)
                style = MaterialTheme.typography.headlineMedium,
                // Цвет текста должен быть "OnSurface", так как фон "Surface".
                // Compose обычно подставляет его сам, но можно явно:
                color = MaterialTheme.colorScheme.onSurface
            )
            
            Text(
                text = "Описание товара...",
                style = MaterialTheme.typography.bodyMedium,
                // Для вторичного текста можно использовать прозрачность
                color = MaterialTheme.colorScheme.onSurface.copy(alpha = 0.7f)
            )
            
            Spacer(modifier = Modifier.height(8.dp))
            
            Button(
                onClick = {},
                // Кнопка автоматически возьмет цвет Primary
            ) {
                Text(
                    text = "Купить",
                    // Текст автоматически возьмет цвет OnPrimary
                )
            }
        }
    }
}

```

*Если вы переключите телефон в темный режим, `MaterialTheme.colorScheme.surface` станет темно-серым, а `onSurface` — белым. Магия!*

### 6. Локализация (Localization)

Чтобы приложение заговорило на другом языке (например, на английском):

1. В `res` создайте папку `values-en` (для английского).
2. Скопируйте туда `strings.xml`.
3. Переведите значения.

**values/strings.xml (Русский - дефолтный):**

```xml
<string name="buy_button">Купить</string>

```

**values-en/strings.xml (Английский):**

```xml
<string name="buy_button">Buy</string>

```

В коде ничего менять не нужно. `stringResource(R.string.buy_button)` сам выберет нужный файл.

### 7. Иконки

В Compose есть встроенный набор иконок Material.
`implementation("androidx.compose.material:material-icons-extended:1.5.x")`

```kotlin
Icon(
    imageVector = Icons.Default.ShoppingCart, // Встроенная векторная иконка
    contentDescription = null,
    tint = MaterialTheme.colorScheme.primary // Красим иконку в цвет бренда
)

```

---

### ✅ Итог Главы 12

1. **Никакого хардкода:** Тексты в `strings.xml`, цвета и размеры через `MaterialTheme`.
2. **Семантические цвета:** Используйте `background` и `onBackground`, а не `White` и `Black`.
3. **Темная тема:** Работает автоматически, если вы соблюдаете пункт 2.
4. **Типографика:** Используйте стили (`headline`, `body`, `label`) вместо ручного задания `fontSize = 24.sp` везде. Это позволит менять шрифт во всем приложении в одном месте (`Type.kt`).

---

# Глава 13. Тестирование (Unit & UI Testing)

### 1. Пирамида тестирования

В Android, как и везде, существует пирамида тестирования.

1. **Unit Tests (70%)**: Маленькие, быстрые тесты логики. Запускаются на компьютере (JVM). Не требуют эмулятора.
2. **Integration Tests (20%)**: Проверка связки компонентов (например, Room + DAO).
3. **UI / E2E Tests (10%)**: Медленные тесты. Эмулятор "нажимает" на кнопки. Проверяют весь путь пользователя.

### 2. Инструменты (Dependencies)

В `build.gradle.kts` (Module: app) добавляем библиотеки.

```kotlin
dependencies {
    // --- UNIT TESTS (src/test) ---
    // JUnit 4 (или 5) - движок для запуска тестов
    testImplementation("junit:junit:4.13.2")
    // Mockk - библиотека для создания фейковых объектов (наш бро)
    testImplementation("io.mockk:mockk:1.13.8")
    // Coroutines Test - для управления временем в корутинах
    testImplementation("org.jetbrains.kotlinx:kotlinx-coroutines-test:1.7.3")

    // --- UI TESTS (src/androidTest) ---
    // Правила для тестирования Compose
    androidTestImplementation("androidx.compose.ui:ui-test-junit4:1.5.1")
    // Чтобы видеть дерево компонентов в логах
    debugImplementation("androidx.compose.ui:ui-test-manifest:1.5.1")
}

```

---

### 3. Unit Testing: Тестируем ViewModel

Самое важное — протестировать бизнес-логику во ViewModel.

**Проблема:** ViewModel использует `viewModelScope`, который привязан к `Dispatchers.Main` (Главный поток Android). В Unit-тестах нет Android, нет Main потока. Тест упадет.
**Решение:** Нам нужно подменить Main диспетчер на тестовый.

#### Шаг 1: Правило для корутин

Создадим вспомогательный класс (TestRule), который будет переключать потоки перед тестом.

```kotlin
// MainDispatcherRule.kt (в папке src/test)
import kotlinx.coroutines.Dispatchers
import kotlinx.coroutines.test.*
import org.junit.rules.TestWatcher
import org.junit.runner.Description

class MainDispatcherRule(
    val testDispatcher: TestDispatcher = UnconfinedTestDispatcher()
) : TestWatcher() {
    override fun starting(description: Description) {
        // Подменяем Main диспетчер на тестовый
        Dispatchers.setMain(testDispatcher)
    }

    override fun finished(description: Description) {
        // Возвращаем все как было
        Dispatchers.resetMain()
    }
}

```

#### Шаг 2: Тест для ViewModel

Допустим, у нас есть `UsersViewModel`, которая берет данные из `UserRepository`.
Мы не хотим делать реальные запросы в сеть в тесте. Мы **замокаем** (mock) репозиторий.

```kotlin
import io.mockk.*
import kotlinx.coroutines.test.runTest
import org.junit.Assert.assertEquals
import org.junit.Rule
import org.junit.Test

class UsersViewModelTest {

    // Подключаем наше правило для корутин
    @get:Rule
    val mainDispatcherRule = MainDispatcherRule()

    // Создаем фейк репозитория
    private val repository = mockk<UserRepository>()

    @Test
    fun `when loadUsers success - state updates to Success`() = runTest {
        // 1. GIVEN (Дано)
        // Учим мок: "Когда вызовут getUsers(), верни список [Alice]"
        val fakeUsers = listOf(UserEntity(1, "Alice", null))
        coEvery { repository.getUsers() } returns fakeUsers

        // Создаем тестируемую ViewModel
        val viewModel = UsersViewModel(repository)

        // 2. WHEN (Действие)
        viewModel.loadUsers()

        // 3. THEN (Проверка)
        // Проверяем, что в State теперь лежат наши данные
        val currentState = viewModel.uiState.value
        assertEquals(false, currentState.isLoading)
        assertEquals(fakeUsers, currentState.users)
        
        // Проверяем, что метод репозитория действительно вызывался 1 раз
        coVerify(exactly = 1) { repository.getUsers() }
    }
    
    @Test
    fun `when loadUsers fails - state contains error`() = runTest {
        // 1. Учим мок выбрасывать ошибку
        coEvery { repository.getUsers() } throws RuntimeException("No Internet")
        
        val viewModel = UsersViewModel(repository)
        
        // 2. Действие
        viewModel.loadUsers()
        
        // 3. Проверка
        val currentState = viewModel.uiState.value
        assertEquals("No Internet", currentState.errorMessage)
    }
}

```

---

### 4. UI Testing: Тестируем Compose

UI тесты лежат в папке `src/androidTest`. Они запускаются на эмуляторе.

В Compose тестирование построено на **Semantics Tree** (Семантическое дерево). Мы ищем элементы не по ID, а по тексту, описанию или тегу.

**Пример:** Проверим, что при старте показывается кнопка, и при клике на нее появляется текст.

```kotlin
import androidx.compose.ui.test.*
import androidx.compose.ui.test.junit4.createComposeRule
import org.junit.Rule
import org.junit.Test

class UserScreenTest {

    // Правило, которое запускает Compose контент
    @get:Rule
    val composeTestRule = createComposeRule()

    @Test
    fun myFirstComposeTest() {
        // 1. Запускаем наш Composable экран
        composeTestRule.setContent {
            // Можно передать фейковую ViewModel или просто UI-компонент
            MyAppTheme {
                UserListScreen( /* ... */ )
            }
        }

        // 2. Проверяем, что заголовок виден
        // onNodeWithText находит элемент с текстом "Users"
        composeTestRule.onNodeWithText("Users").assertIsDisplayed()

        // 3. Находим кнопку по тексту и кликаем
        composeTestRule.onNodeWithText("Refresh").performClick()

        // 4. Проверяем, что появилась крутилка загрузки
        // Для этого в коде UI нужно добавить modifier.testTag("loading_wheel")
        composeTestRule.onNodeWithTag("loading_wheel").assertExists()
    }
}

```

**Важно про testTag:**
Иногда искать по тексту неудобно (текст меняется при локализации). Лучше использовать `testTag`.

В коде UI:

```kotlin
CircularProgressIndicator(
    modifier = Modifier.testTag("loading_wheel") 
)

```

В тесте:

```kotlin
onNodeWithTag("loading_wheel").assertIsDisplayed()

```

---

### 5. Что такое Flaky Tests?

Уровень Senior — понимать проблемы тестов.
**Flaky Test (Моргающий тест)** — это тест, который то проходит, то падает, хотя код не менялся.
Причины:

* Анимации (тест кликнул, пока кнопка выезжала).
* Медленная сеть (в UI тестах лучше использовать фейковые данные, а не реальный интернет).
* Асинхронность (тест проверил результат раньше, чем корутина завершилась).

В Compose тесты **синхронизированы** с UI. Тест автоматически ждет (`idle`), пока все анимации и отрисовки закончатся, прежде чем выполнить следующую команду. Это делает их стабильнее, чем старый Espresso.

---

### ✅ Итог Главы 13

1. **Пирамида:** Много Unit-тестов, мало UI-тестов.
2. **Mockk:** Используйте `mockk`, чтобы изолировать класс. Если тестируете ViewModel, замокайте Repository.
3. **Coroutines:** Используйте `runTest` и подменяйте `Dispatchers.Main` с помощью `TestRule`.
4. **Compose Rule:** Используйте `onNodeWithText/Tag` для поиска элементов и `performClick` для действий.

---

# Глава 14. Продвинутая сборка: Gradle, Multi-module и CI/CD

### 1. Зачем разбивать проект на модули?

По умолчанию Android-проект — это **Монолит** (один модуль `app`).
**Проблемы монолита:**

1. **Долгая сборка:** Изменили одну строчку — Gradle пересобирает всё приложение.
2. **Спутанность кода:** Можно случайно использовать класс из базы данных прямо во View, нарушая архитектуру.
3. **Конфликты:** Разработчикам сложнее работать параллельно над разными фичами.

**Решение — Multi-module:**
Мы разбиваем код на независимые библиотеки (модули).

* Изменили код в модуле "Профиль"? Gradle пересоберет только его и главный модуль `app`. Остальные 20 модулей (Чат, Каталог, Настройки) пересобираться не будут. Это экономит часы времени.

### 2. Структура модулей (Clean Architecture)

Обычно модули делят по слоям или по фичам. Самый популярный подход — смешанный.

1. **`:app`** — Главный модуль. Он "глупый". Он просто знает про все остальные модули и связывает их (Dependency Injection graph).
2. **`:core`** (Ядро) — Общие компоненты, которые нужны всем.
* `:core:network` (Retrofit, OkHttp)
* `:core:database` (Room)
* `:core:ui` (Theme, общие кнопки, ресурсы)
* `:core:utils` (Extensions, DateFormatter)


3. **`:feature`** (Фичи) — Экраны приложения. **Фичи не должны зависеть друг от друга!**
* `:feature:auth` (Экран логина)
* `:feature:home` (Главная лента)
* `:feature:profile` (Профиль)



> **Правило:** `:feature` зависит от `:core`. `:app` зависит от `:feature`.
> Если `:feature:home` хочет открыть `:feature:profile`, она делает это через интерфейс навигации (Navigator), который реализован в `:app`.

### 3. Управление зависимостями: Version Catalogs (TOML)

До 2022 года версии библиотек дублировались в каждом `build.gradle` файле. Обновлять их было адом.
Сейчас стандарт — **Version Catalogs** (`libs.versions.toml`). Это единый файл, где прописаны все версии.

**Файл: `gradle/libs.versions.toml**`

```toml
[versions]
# Здесь задаем версии один раз
kotlin = "1.9.0"
coreKtx = "1.10.1"
retrofit = "2.9.0"
room = "2.6.0"

[libraries]
# Группируем зависимости
androidx-core-ktx = { group = "androidx.core", name = "core-ktx", version.ref = "coreKtx" }
retrofit-core = { group = "com.squareup.retrofit2", name = "retrofit", version.ref = "retrofit" }
room-runtime = { group = "androidx.room", name = "room-runtime", version.ref = "room" }

[bundles]
# Можно объединять библиотеки в пачки
retrofit = ["retrofit-core", "retrofit-gson"]

```

**Использование в `build.gradle.kts` (любого модуля):**

```kotlin
dependencies {
    // Обращаемся через libs
    implementation(libs.androidx.core.ktx)
    
    // Подключаем сразу пачку (retrofit + gson)
    implementation(libs.bundles.retrofit)
}

```

### 4. Build Variants (Типы сборок)

В реальной работе у вас всегда есть как минимум два окружения:

1. **Dev (Develop)**: Тестовый сервер, логи включены, приложение имеет суффикс `.dev` (чтобы можно было поставить рядом с боевым).
2. **Prod (Production)**: Боевой сервер, логи выключены, R8 обфускация (защита кода).

Это настраивается через `buildTypes` и `productFlavors`.

```kotlin
// build.gradle.kts (:app)
android {
    // ...
    buildTypes {
        getByName("release") {
            isMinifyEnabled = true // Включить R8 (сжатие и обфускация)
            proguardFiles(getDefaultProguardFile("proguard-android.txt"), "proguard-rules.pro")
        }
        getByName("debug") {
            applicationIdSuffix = ".debug" // com.example.app.debug
        }
    }

    flavorDimensions += "environment"
    productFlavors {
        create("dev") {
            dimension = "environment"
            // Переменная доступна в коде через BuildConfig.BASE_URL
            buildConfigField("String", "BASE_URL", "\"https://dev-api.example.com/\"")
        }
        create("prod") {
            dimension = "environment"
            buildConfigField("String", "BASE_URL", "\"https://api.example.com/\"")
        }
    }
}

```

### 5. CI/CD (Continuous Integration / Delivery)

Senior не собирает APK руками на своем ноутбуке, чтобы скинуть его тестировщику в Telegram. Это делает робот.

**CI (Integration):**
Каждый раз, когда вы делаете `git push`, облако (GitHub Actions, GitLab CI):

1. Запускает `./gradlew lint` (проверка стиля кода).
2. Запускает `./gradlew test` (Unit тесты).
3. Если тесты упали — ваш код не попадет в главную ветку.

**CD (Delivery):**
Когда вы ставите тег версии (v1.0):

1. Облако собирает Release Bundle (.aab).
2. Подписывает его секретным ключом (который не хранится в репозитории).
3. Автоматически загружает в Google Play Console во внутреннее тестирование.

---

# Глава 15. Гарантированная фоновая работа (WorkManager)

### 1. Проблема: Coroutines vs Реальность

Вы запустили загрузку фото в корутине (`viewModelScope.launch`). Пользователь смахнул приложение из недавних.
**Результат:** Процесс убит. Загрузка прервалась. Файл битый.

**WorkManager** — это библиотека, которая гарантирует, что задача выполнится, даже если приложение убито или устройство перезагрузилось.

### 2. Когда использовать?

* ✅ Отправка логов/аналитики.
* ✅ Резервное копирование базы данных.
* ✅ Синхронизация данных с сервером.
* ✅ Загрузка/Выгрузка тяжелых файлов.
* ⛔️ **Не использовать** для мгновенных действий (например, оплата в магазине). WorkManager не гарантирует *мгновенный* запуск, он гарантирует *конечный результат*.

### 3. Создание Worker (Рабочего)

Рабочий класс описывает саму задачу.

```kotlin
import android.content.Context
import androidx.work.CoroutineWorker
import androidx.work.WorkerParameters

// Используем CoroutineWorker, чтобы внутри можно было запускать suspend функции
class UploadWorker(
    context: Context,
    params: WorkerParameters
) : CoroutineWorker(context, params) {

    override suspend fun doWork(): Result {
        // Получаем входные данные
        val imageUri = inputData.getString("IMAGE_URI") ?: return Result.failure()

        return try {
            // Имитация тяжелой работы
            uploadImage(imageUri)
            
            // Успех!
            Result.success()
        } catch (e: Exception) {
            // Ошибка. WorkManager попробует перезапустить задачу позже (Backoff Policy)
            if (runAttemptCount < 3) {
                Result.retry()
            } else {
                Result.failure()
            }
        }
    }

    private suspend fun uploadImage(uri: String) {
        // ... код Retrofit или другой логики
    }
}

```

### 4. Запуск задачи (WorkRequest)

Мы можем задать условия (Constraints). Например: "Запускать только когда есть Wi-Fi и телефон на зарядке".

```kotlin
// Во ViewModel или Repository
fun startUpload(imageUri: String, context: Context) {
    
    // 1. Условия запуска
    val constraints = Constraints.Builder()
        .setRequiredNetworkType(NetworkType.UNMETERED) // Только Wi-Fi
        .setRequiresBatteryNotLow(true) // Не сажать батарею
        .build()

    // 2. Входные данные
    val data = workDataOf("IMAGE_URI" to imageUri)

    // 3. Создаем запрос (OneTime - одноразовый)
    val uploadWorkRequest = OneTimeWorkRequest.Builder(UploadWorker::class.java)
        .setConstraints(constraints)
        .setInputData(data)
        // Если ошибка - повторить через 10 секунд, затем через 20... (Exponential)
        .setBackoffCriteria(BackoffPolicy.EXPONENTIAL, 10, TimeUnit.SECONDS)
        .build()

    // 4. Ставим в очередь
    WorkManager.getInstance(context).enqueue(uploadWorkRequest)
}

```

### 5. Периодические задачи (PeriodicWorkRequest)

Если нужно синхронизировать данные каждые 15 минут (минимальный интервал в Android).

```kotlin
val syncWork = PeriodicWorkRequest.Builder(
    SyncWorker::class.java,
    15, TimeUnit.MINUTES // Повторять каждые 15 мин
).build()

// enqueueUniquePeriodicWork гарантирует, что не создастся 10 дублей одной задачи
WorkManager.getInstance(context).enqueueUniquePeriodicWork(
    "MySyncJob",
    ExistingPeriodicWorkPolicy.KEEP, // Если уже есть такая задача - не трогать её
    syncWork
)

```

### 6. WorkManager + Hilt (HiltWorker)

Так как `Worker` создается системой Android, а не нами, мы не можем просто так написать `@Inject` в конструкторе.
Нужна специальная аннотация `@HiltWorker` и настройка в Application классе.

**Шаг 1. Аннотация рабочего**

```kotlin
@HiltWorker
class SyncWorker @AssistedInject constructor(
    @Assisted appContext: Context,
    @Assisted workerParams: WorkerParameters,
    private val repository: UserRepository // Теперь можно инжектить!
) : CoroutineWorker(appContext, workerParams) { ... }

```

**Шаг 2. Application Class**

```kotlin
@HiltAndroidApp
class MyApplication : Application(), Configuration.Provider {

    @Inject lateinit var workerFactory: HiltWorkerFactory

    override fun getWorkManagerConfiguration(): Configuration {
        return Configuration.Builder()
            .setWorkerFactory(workerFactory)
            .build()
    }
}

```

---

### ✅ Итог Главы 15

1. **WorkManager** — единственный надежный способ выполнения отложенных задач.
2. **Constraints** позволяют беречь батарею и трафик пользователя.
3. **Result.retry()** автоматически перезапускает упавшие задачи с умной задержкой.
4. Для внедрения зависимостей (Hilt) требуется `@HiltWorker`.

---

# Глава 16. Разрешения (Permissions) и Уведомления (Notifications)

### 1. Философия разрешений в Modern Android

Раньше (до Android 6.0) мы просто писали разрешения в Manifest, и при установке пользователь соглашался со всем сразу. Сейчас используются **Runtime Permissions**.

Разрешения делятся на два типа:

1. **Normal (Обычные):** Интернет, Bluetooth, Вибрация. Система дает их автоматически, если они есть в манифесте.
2. **Dangerous (Опасные):** Камера, Геолокация, Контакты, Микрофон, и (с Android 13) **Уведомления**. Их нужно запрашивать **явно** во время работы приложения.

### 2. Запрос разрешений в Compose

В Compose нет метода `requestPermissions`, как в Activity. Мы используем **Activity Result API** через `rememberLauncherForActivityResult`.

Допустим, мы хотим отправить уведомление (Android 13+ требует разрешения `POST_NOTIFICATIONS`).

```kotlin
// AndroidManifest.xml
<uses-permission android:name="android.permission.POST_NOTIFICATIONS" />

```

```kotlin
// NotificationPermissionScreen.kt
@Composable
fun NotificationPermissionScreen() {
    val context = LocalContext.current
    
    // 1. Создаем Лаунчер. Это колбэк, который сработает, когда юзер нажмет "Да" или "Нет".
    val permissionLauncher = rememberLauncherForActivityResult(
        contract = ActivityResultContracts.RequestPermission(),
        onResult = { isGranted ->
            if (isGranted) {
                Toast.makeText(context, "Спасибо!", Toast.LENGTH_SHORT).show()
            } else {
                Toast.makeText(context, "Без уведомлений вы пропустите важное :(", Toast.LENGTH_LONG).show()
            }
        }
    )

    Column(
        modifier = Modifier.fillMaxSize(),
        verticalArrangement = Arrangement.Center,
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        Button(onClick = {
            // 2. Проверяем версию Android (на старых версиях разрешение не нужно)
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.TIRAMISU) {
                // 3. Проверяем, может оно уже есть?
                val hasPermission = ContextCompat.checkSelfPermission(
                    context, 
                    Manifest.permission.POST_NOTIFICATIONS
                ) == PackageManager.PERMISSION_GRANTED

                if (!hasPermission) {
                    // Запускаем системный диалог
                    permissionLauncher.launch(Manifest.permission.POST_NOTIFICATIONS)
                }
            }
        }) {
            Text("Включить уведомления")
        }
    }
}

```

> **Совет Senior-разработчика:** Никогда не запрашивайте права сразу при запуске приложения. Это раздражает. Запрашивайте их контекстно: "Чтобы отсканировать QR-код, нам нужен доступ к камере".

### 3. Анатомия Уведомления

Уведомление — это сложная структура.

Чтобы отправить уведомление, нужно знать три понятия:

1. **Channel (Канал):** Обязателен с Android 8.0. Позволяет пользователю отключать *типы* уведомлений (например, "Рекламу" отключить, а "Заказы" оставить).
2. **Builder:** Конструктор внешнего вида.
3. **Manager:** Системный сервис для отправки.

### 4. Создание Канала (Notification Channel)

Каналы создаются один раз при старте приложения (обычно в `Application.onCreate` или DI модуле).

```kotlin
fun createNotificationChannel(context: Context) {
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
        val name = "Заказы"
        val descriptionText = "Уведомления о статусе доставки"
        val importance = NotificationManager.IMPORTANCE_DEFAULT // Со звуком, но без всплывания поверх всего
        
        val channel = NotificationChannel("ORDERS_CHANNEL_ID", name, importance).apply {
            description = descriptionText
        }
        
        // Регистрация канала в системе
        val notificationManager: NotificationManager =
            context.getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
        notificationManager.createNotificationChannel(channel)
    }
}

```

### 5. PendingIntent — Реакция на нажатие

Уведомление бесполезно, если по клику ничего не происходит.
**PendingIntent** — это "отложенное намерение". Это токен, который мы отдаем системе, говоря: *"Если юзер нажмет сюда, запусти вот эту Activity от моего имени"*.

**Важно:** С Android 12 обязательно указывать флаг `FLAG_IMMUTABLE` или `FLAG_MUTABLE`. Без этого приложение упадет.

```kotlin
fun showNotification(context: Context) {
    // 1. Куда переходить при клике?
    val intent = Intent(context, MainActivity::class.java).apply {
        flags = Intent.FLAG_ACTIVITY_NEW_TASK or Intent.FLAG_ACTIVITY_CLEAR_TASK
        putExtra("screen_route", "orders") // Передаем данные для навигации
    }
    
    val pendingIntent: PendingIntent = PendingIntent.getActivity(
        context, 
        0, 
        intent, 
        PendingIntent.FLAG_IMMUTABLE // <--- Критично важно для Android 12+
    )

    // 2. Строим уведомление
    val builder = NotificationCompat.Builder(context, "ORDERS_CHANNEL_ID")
        .setSmallIcon(R.drawable.ic_notification) // Маленькая иконка в статус баре (должна быть белой с прозрачностью!)
        .setContentTitle("Ваш заказ в пути!")
        .setContentText("Курьер будет у вас через 15 минут.")
        .setPriority(NotificationCompat.PRIORITY_DEFAULT)
        .setContentIntent(pendingIntent) // Привязываем клик
        .setAutoCancel(true) // Убрать уведомление после клика

    // 3. Показываем (нужна проверка прав)
    with(NotificationManagerCompat.from(context)) {
        if (ActivityCompat.checkSelfPermission(
                context,
                Manifest.permission.POST_NOTIFICATIONS
            ) == PackageManager.PERMISSION_GRANTED
        ) {
            // ID (101) нужен, чтобы потом можно было обновить или удалить это уведомление
            notify(101, builder.build()) 
        }
    }
}

```

### 6. Типы уведомлений (Senior Level)

* **Foreground Service Notification:** Несмахиваемое уведомление (Музыкальный плеер, Навигатор).
* **Progress Notification:** С полоской загрузки (скачивание файла).
* **Expandable Notification:** Можно развернуть и увидеть картинку или длинный текст.
* **Call Style:** Уведомление о звонке с кнопками "Принять" и "Сбросить".

### 7. Push Notifications (Firebase / FCM)

То, что мы делали выше — это **Локальные** уведомления (Local Notifications). Приложение само их создает.
Но чаще уведомления приходят с сервера (маркетинг, чаты).

Для этого используется **Firebase Cloud Messaging (FCM)**.

1. Приложение получает уникальный **FCM Token**.
2. Отправляет токен на ваш бэкенд.
3. Бэкенд шлет JSON на сервера Google.
4. Google будит телефон и доставляет пуш.

В коде это обрабатывается через сервис:

```kotlin
class MyFirebaseMessagingService : FirebaseMessagingService() {
    
    // Пришел новый токен (старый протух) - отправь на бэкенд
    override fun onNewToken(token: String) {
        sendRegistrationToServer(token)
    }

    // Пришло сообщение, пока приложение открыто
    override fun onMessageReceived(remoteMessage: RemoteMessage) {
        // Показать уведомление вручную (как в пункте 5)
    }
}

```

---

### ✅ Итог Главы 16

1. **Runtime Permissions**: Всегда проверяйте права перед действием. Используйте `rememberLauncherForActivityResult` в Compose.
2. **Channels**: Без создания канала уведомление не покажется на Android 8+.
3. **PendingIntent**: Используйте `FLAG_IMMUTABLE`.
4. **Icons**: Иконка уведомления должна быть монохромной (белой на прозрачном фоне), иначе Android отобразит просто белый квадрат.

---

# Глава 17. Производительность (Performance) и Оптимизация

### 1. Что такое "Лаги" (Jank)?

Экран телефона обновляется обычно 60 раз в секунду (60 Hz).


У вашего кода есть **всего 16 миллисекунд**, чтобы подготовить кадр. Если вы задумались на 20 мс (например, сортируете большой список в Main Thread), телефон не успеет отрисовать кадр. Пользователь увидит "фриз" (замирание). Это называется **Jank**.

**Как избежать:**

* Все тяжелое — в `Dispatchers.IO` / `Dispatchers.Default`.
* В `LazyColumn` используйте `key`, чтобы не перерисовывать лишнее.
* Используйте **R8** (см. пункт 4).

### 2. Утечки памяти (Memory Leaks)

Это самая коварная ошибка.
**Суть:** Вы закрыли экран (Activity), но какой-то другой объект (например, Синглтон или фоновый поток) держит ссылку на этот экран.
**Итог:** Сборщик мусора (Garbage Collector) не может удалить Activity из памяти. Память забивается. Приложение падает с `OutOfMemoryError` (OOM).

#### Классический пример утечки:

```kotlin
object SingletonCache {
    // ОШИБКА: Храним ссылку на Context или View вечно
    var storedContext: Context? = null 
}

class MainActivity : Activity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // Activity передает ссылку на себя в вечный объект
        SingletonCache.storedContext = this 
    }
    // Когда MainActivity закроется, SingletonCache все еще держит её.
    // GC не может очистить память.
}

``` 

### 3. Инструмент №1: LeakCanary

Вам не нужно искать утечки глазами. Есть библиотека от Square, которая делает это автоматически.

**Подключение (build.gradle.kts :app):**

```kotlin
dependencies {
    // debugImplementation означает, что библиотека будет ТОЛЬКО в дебаг-версии.
    // В релиз она не попадет (и не будет пугать пользователей).
    debugImplementation("com.squareup.leakcanary:leakcanary-android:2.12")
}

```

**Как это работает:**

1. Вы запускаете приложение.
2. Ходите по экранам, открываете/закрываете их.
3. Если LeakCanary замечает, что закрытая Activity не удалилась из памяти, он присылает **уведомление с иконкой желтой птички**.
4. Нажимаете на него — видите полный путь (Trace), кто именно держит ссылку.

### 4. Инструмент №2: Android Profiler

Встроенный инструмент в Android Studio. (View -> Tool Windows -> Profiler).

Он показывает графики в реальном времени:

1. **CPU:** Насколько загружен процессор. Если график постоянно "в полке", телефон греется и жрет батарею.
2. **Memory:** Сколько RAM занято. Можно нажать "Capture Heap Dump" и посмотреть, какие объекты занимают больше всего места (обычно это Bitmap/Картинки).
3. **Energy:** Расход батареи.

### 5. R8 и ProGuard (Сжатие кода)

Когда вы собираете **Release** версию, включается компилятор R8. Он делает три вещи:

1. **Shrinking (Сжатие):** Удаляет неиспользуемые классы и методы. (Если вы подключили библиотеку на 5 МБ, а используете одну функцию, R8 выкинет остальное).
2. **Obfuscation (Обфускация):** Переименовывает классы в `a.b.c`, методы в `f()`. Это уменьшает размер кода и защищает от реверс-инжиниринга (взломщикам сложнее читать код).
3. **Optimization:** Оптимизирует инструкции кода.

**Включение в build.gradle.kts:**

```kotlin
buildTypes {
    release {
        // Включает R8
        isMinifyEnabled = true 
        isShrinkResources = true // Удаляет неиспользуемые картинки/xml
        
        // Правила ProGuard. Если R8 случайно удалил нужный класс (например, используемый через Reflection),
        // нужно прописать исключение в файле proguard-rules.pro
        proguardFiles(getDefaultProguardFile("proguard-android-optimize.txt"), "proguard-rules.pro")
    }
}

```

### 6. Baseline Profiles (Ускорение запуска)

*Тема для настоящего Senior.*
Приложения на Android компилируются "на лету" (JIT - Just In Time). При первом запуске система тратит время на анализ кода, поэтому старт медленный.

**Baseline Profile** — это файл, который говорит Android: *"Вот эти методы будут нужны сразу при запуске. Скомпилируй их заранее (AOT - Ahead Of Time)"*.

**Результат:** Холодный старт ускоряется на **30-40%**.

Как сгенерировать:

1. Создать модуль `Benchmark`.
2. Написать тест, который открывает приложение.
3. Запустить генератор.
4. Файл `baseline-prof.txt` появится в `src/main`.

---

### ✅ Итог Главы 17

1. **16 мс** — ваш бюджет на кадр. Не блокируйте Main Thread.
2. **LeakCanary** — мастхэв в любом проекте. Подключайте через `debugImplementation`.
3. **Bitmap** — главные пожиратели памяти. Следите за их размером (используйте Coil/Glide).
4. **isMinifyEnabled = true** — обязательно для релиза.
5. **Baseline Profiles** — современный стандарт оптимизации стартапа.