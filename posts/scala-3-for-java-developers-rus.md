# Scala 3 для Java разработчика

- [Часть 1: Основы и Синтаксис](#часть-1-основы-и-синтаксис)
  - [1.1. Базовый синтаксис и Переменные](#11-базовый-синтаксис-и-переменные)
    - [1. Объявление переменных: `val` vs `var`](#1-объявление-переменных-val-vs-var)
    - [2. Новый синтаксис: Отступы (Significant Indentation)](#2-новый-синтаксис-отступы-significant-indentation)
    - [3. Top-level Definitions](#3-top-level-definitions)
    - [4. Строки и Интерполяция](#4-строки-и-интерполяция)
      - [s-interpolator (Substitution)](#s-interpolator-substitution)
      - [f-interpolator (Formatting)](#f-interpolator-formatting)
      - [raw-interpolator](#raw-interpolator)
      - [Multiline Strings (Многострочные строки)](#multiline-strings-многострочные-строки)
    - [Итоговое сравнение (Java -\> Scala)](#итоговое-сравнение-java---scala)
  - [1.2. Система типов (Basics)](#12-система-типов-basics)
    - [1. Единая иерархия: `Any`](#1-единая-иерархия-any)
      - [A. `AnyVal` (Аналог примитивов)](#a-anyval-аналог-примитивов)
      - [B. `AnyRef` (Аналог `java.lang.Object`)](#b-anyref-аналог-javalangobject)
    - [2. `Unit` vs `void`](#2-unit-vs-void)
    - [3. Нижние типы: `Null` и `Nothing`](#3-нижние-типы-null-и-nothing)
      - [`Null`](#null)
      - [`Nothing`](#nothing)
    - [4. Type Inference (Вывод типов)](#4-type-inference-вывод-типов)
    - [Итог раздела 1.2](#итог-раздела-12)
  - [1.3. Управление потоком (Control Structures)](#13-управление-потоком-control-structures)
    - [1. `if` / `else` — это выражение](#1-if--else--это-выражение)
    - [2. Циклы и For-Comprehensions](#2-циклы-и-for-comprehensions)
      - [`while`](#while)
      - [`for` — это не цикл](#for--это-не-цикл)
    - [3. Pattern Matching (`match`) — Switch на стероидах](#3-pattern-matching-match--switch-на-стероидах)
    - [Итог раздела 1.3](#итог-раздела-13)
- [Часть 2: Объектно-Ориентированное Программирование](#часть-2-объектно-ориентированное-программирование)
  - [2.1. Классы и Объекты](#21-классы-и-объекты)
    - [1. Классы и Первичный конструктор](#1-классы-и-первичный-конструктор)
    - [2. Objects (Синглтоны)](#2-objects-синглтоны)
    - [3. Companion Objects (Компаньоны)](#3-companion-objects-компаньоны)
    - [4. Метод `apply`: Магия отсутствия `new`](#4-метод-apply-магия-отсутствия-new)
    - [Итог раздела 2.1](#итог-раздела-21)
  - [2.2. Case Classes \& Enums (Моделирование данных)](#22-case-classes--enums-моделирование-данных)
    - [1. Case Classes (Умные DTO)](#1-case-classes-умные-dto)
      - [Метод `copy` (Замена Сеттеров)](#метод-copy-замена-сеттеров)
    - [2. Enums и ADT (Алгебраические Типы Данных)](#2-enums-и-adt-алгебраические-типы-данных)
      - [А. Простой Enum (как в Java)](#а-простой-enum-как-в-java)
      - [Б. Параметризованный Enum (как в Java)](#б-параметризованный-enum-как-в-java)
      - [В. ADT (Sum Types) — The Game Changer](#в-adt-sum-types--the-game-changer)
    - [3. Sealed Traits (Классический подход)](#3-sealed-traits-классический-подход)
    - [Итог раздела 2.2](#итог-раздела-22)
  - [2.3. Трейты (Traits)](#23-трейты-traits)
    - [1. Трейты как Интерфейсы](#1-трейты-как-интерфейсы)
    - [2. Трейты со Состоянием (State)](#2-трейты-со-состоянием-state)
    - [3. Множественное наследование и Mixins](#3-множественное-наследование-и-mixins)
    - [4. Проблема Ромба и Линеаризация (Linearization)](#4-проблема-ромба-и-линеаризация-linearization)
    - [5. Параметры трейтов (Trait Parameters) — Scala 3 Feature](#5-параметры-трейтов-trait-parameters--scala-3-feature)
    - [Итог раздела 2.3](#итог-раздела-23)
  - [2.4. Модификаторы доступа и наследования](#24-модификаторы-доступа-и-наследования)
    - [1. Модификаторы доступа (Visibility)](#1-модификаторы-доступа-visibility)
      - [Scoped Private (`private[x]`)](#scoped-private-privatex)
    - [2. Модификаторы наследования (Scala 3)](#2-модификаторы-наследования-scala-3)
      - [A. `final` (Как в Java)](#a-final-как-в-java)
      - [B. `sealed` (Изолированный)](#b-sealed-изолированный)
      - [C. `open` (Новинка Scala 3)](#c-open-новинка-scala-3)
    - [Итог раздела 2.4](#итог-раздела-24)
- [Часть 3: Функциональное Программирование (FP Core)](#часть-3-функциональное-программирование-fp-core)
  - [3.1. Функции как объекты первого класса](#31-функции-как-объекты-первого-класса)
    - [1. Типы функций (Function Types)](#1-типы-функций-function-types)
    - [2. Анонимные функции (Лямбды)](#2-анонимные-функции-лямбды)
      - [Placeholder Syntax (`_`) — Сахар](#placeholder-syntax-_--сахар)
    - [3. Функции высшего порядка (HOF)](#3-функции-высшего-порядка-hof)
    - [4. Методы (`def`) vs Функции (`val`)](#4-методы-def-vs-функции-val)
    - [5. Каррирование (Currying) и Множественные списки параметров](#5-каррирование-currying-и-множественные-списки-параметров)
    - [Итог раздела 3.1](#итог-раздела-31)
  - [3.2. Работа с коллекциями (Immutable Collections)](#32-работа-с-коллекциями-immutable-collections)
    - [1. Иерархия и основные типы](#1-иерархия-и-основные-типы)
      - [A. `List` (Связанный список)](#a-list-связанный-список)
      - [B. `Vector` (Замена ArrayList)](#b-vector-замена-arraylist)
      - [C. `Set` и `Map`](#c-set-и-map)
    - [2. Операции: Богатый API](#2-операции-богатый-api)
    - [3. flatMap (Уплощение)](#3-flatmap-уплощение)
    - [4. Свертка: foldLeft](#4-свертка-foldleft)
    - [5. Конструктор списка (`::`) и операторы](#5-конструктор-списка--и-операторы)
    - [6. LazyList (Аналог Java Stream)](#6-lazylist-аналог-java-stream)
    - [7. Java Converters (Интероп)](#7-java-converters-интероп)
    - [Итог раздела 3.2](#итог-раздела-32)
  - [3.3. Pattern Matching (Сопоставление с образцом)](#33-pattern-matching-сопоставление-с-образцом)
    - [1. Основы и Типизированные паттерны](#1-основы-и-типизированные-паттерны)
    - [2. Деконструкция Case Classes (Deep Matching)](#2-деконструкция-case-classes-deep-matching)
    - [3. Guards (Условия в паттернах)](#3-guards-условия-в-паттернах)
    - [4. Паттерны в списках (Recursion Friendly)](#4-паттерны-в-списках-recursion-friendly)
    - [5. Sealed Trait Exhaustiveness Check](#5-sealed-trait-exhaustiveness-check)
    - [6. Unapply (Магия под капотом)](#6-unapply-магия-под-капотом)
    - [7. Pattern Matching не только в `match`](#7-pattern-matching-не-только-в-match)
    - [Итог раздела 3.3](#итог-раздела-33)
  - [3.4. Обработка ошибок без исключений](#34-обработка-ошибок-без-исключений)
    - [1. `Option[T]` — Замена Null](#1-optiont--замена-null)
    - [2. `Try[T]` — Изоляция исключений](#2-tryt--изоляция-исключений)
    - [3. `Either[L, R]` — Логические ошибки](#3-eitherl-r--логические-ошибки)
    - [4. For-Comprehension с Монадами (Happy Path)](#4-for-comprehension-с-монадами-happy-path)
    - [Итог раздела 3.4](#итог-раздела-34)
- [Часть 4: Контекстные Абстракции (Implicits 2.0)](#часть-4-контекстные-абстракции-implicits-20)
    - [4.1. Given и Using (DI на уровне компилятора)](#41-given-и-using-di-на-уровне-компилятора)
      - [1. `given` (Провайдер)](#1-given-провайдер)
      - [2. `using` (Потребитель)](#2-using-потребитель)
    - [4.2. Extension Methods (Методы расширения)](#42-extension-methods-методы-расширения)
    - [4.3. Паттерн Type Class (Тайпклассы)](#43-паттерн-type-class-тайпклассы)
    - [4.4. Scala 2 Legacy (Словарь переводчика)](#44-scala-2-legacy-словарь-переводчика)
    - [Итог раздела 4](#итог-раздела-4)
- [Часть 5: Продвинутая система типов (Application Level)](#часть-5-продвинутая-система-типов-application-level)
  - [5.1. Дженерики и Вариантность](#51-дженерики-и-вариантность)
    - [1. Инвариантность (`[T]`) — Как в Java](#1-инвариантность-t--как-в-java)
    - [2. Ковариантность (`[+T]`) — Producer](#2-ковариантность-t--producer)
    - [3. Контравариантность (`[-T]`) — Consumer](#3-контравариантность--t--consumer)
  - [5.2. Алгебра типов (Scala 3)](#52-алгебра-типов-scala-3)
    - [1. Union Types (Объединение `|`) — "ИЛИ"](#1-union-types-объединение---или)
    - [2. Intersection Types (Пересечение `&`) — "И"](#2-intersection-types-пересечение---и)
  - [5.3. Opaque Type Aliases (Zero-cost Wrappers)](#53-opaque-type-aliases-zero-cost-wrappers)
    - [Итог раздела 5](#итог-раздела-5)
- [Часть 6: Метапрограммирование (Light)](#часть-6-метапрограммирование-light)
  - [6.1. Inline (Встраивание кода)](#61-inline-встраивание-кода)
    - [1. Оптимизация производительности](#1-оптимизация-производительности)
    - [2. `inline` параметры](#2-inline-параметры)
    - [3. Compile-time Logic (`inline match`)](#3-compile-time-logic-inline-match)
  - [6.2. Type Class Derivation (Автогенерация кода)](#62-type-class-derivation-автогенерация-кода)
    - [Пример: JSON Encoder](#пример-json-encoder)
    - [Как это работает?](#как-это-работает)
    - [Итог раздела 6](#итог-раздела-6)
- [Часть 7: Инфраструктура и Сборка (sbt)](#часть-7-инфраструктура-и-сборка-sbt)
  - [7.1. Структура проекта](#71-структура-проекта)
  - [7.2. Файл `build.sbt`](#72-файл-buildsbt)
  - [7.3. Управление зависимостями (`%` vs `%%`)](#73-управление-зависимостями--vs-)
      - [Синтаксис:](#синтаксис)
  - [7.4. Основные команды (Interactive Mode)](#74-основные-команды-interactive-mode)
      - [Continuous Execution (`~`)](#continuous-execution-)
    - [Итог раздела 7](#итог-раздела-7)
- [Часть 8: Продвинутый Java Interop](#часть-8-продвинутый-java-interop)
  - [Совместная жизнь с Java](#совместная-жизнь-с-java)
    - [8.1. Конвертация коллекций](#81-конвертация-коллекций)
    - [8.2. Лямбды и SAM (Functional Interfaces)](#82-лямбды-и-sam-functional-interfaces)
    - [8.3. Checked Exceptions](#83-checked-exceptions)
    - [8.4. Ключевые слова (Escaping)](#84-ключевые-слова-escaping)
    - [8.5. Опция `Explicit Nulls` (Scala 3)](#85-опция-explicit-nulls-scala-3)
    - [Итог раздела 8](#итог-раздела-8)
- [Часть 9: Магия Типов (Library Author Level)](#часть-9-магия-типов-library-author-level)
  - [9.1. Типы высшего порядка (Higher-Kinded Types — HKT)](#91-типы-высшего-порядка-higher-kinded-types--hkt)
    - [Проблема](#проблема)
    - [Решение: `F[_]`](#решение-f_)
  - [9.2. Match Types (Типы как функции) — Scala 3](#92-match-types-типы-как-функции--scala-3)
  - [9.3. Dependent Object Types (Зависимые типы)](#93-dependent-object-types-зависимые-типы)
- [Часть 10: Многопоточность и Асинхронность](#часть-10-многопоточность-и-асинхронность)
  - [10.1. Futures (Стандартная библиотека)](#101-futures-стандартная-библиотека)
    - [1. Создание и ExecutionContext](#1-создание-и-executioncontext)
    - [2. Callbacks vs Combinators](#2-callbacks-vs-combinators)
    - [3. Блокировка (Await)](#3-блокировка-await)
  - [10.2. Promises (Управление Future)](#102-promises-управление-future)
  - [10.3. Параллельные коллекции](#103-параллельные-коллекции)
  - [10.4. Экосистема: ZIO и Cats Effect (The Real World)](#104-экосистема-zio-и-cats-effect-the-real-world)
    - [Пример на ZIO (Концептуально)](#пример-на-zio-концептуально)
  - [10.5. Модель Акторов (Akka / Pekko)](#105-модель-акторов-akka--pekko)
    - [Итог раздела 10](#итог-раздела-10)
- [Часть 11: Практические паттерны и Тестирование](#часть-11-практические-паттерны-и-тестирование)
  - [11.1. Хвостовая рекурсия (`@tailrec`)](#111-хвостовая-рекурсия-tailrec)
  - [11.2. Управление ресурсами (`Using`)](#112-управление-ресурсами-using)
  - [11.3. Равенство (`==` vs `eq`)](#113-равенство--vs-eq)
  - [11.4. Делегирование (`export`) — Scala 3](#114-делегирование-export--scala-3)
  - [11.5. Тестирование (ScalaTest \& Property-Based)](#115-тестирование-scalatest--property-based)
    - [1. Стили тестов](#1-стили-тестов)
    - [2. Property-Based Testing (ScalaCheck)](#2-property-based-testing-scalacheck)



# Часть 1: Основы и Синтаксис

## 1.1. Базовый синтаксис и Переменные

В Java мы привыкли к многословности: `private final String variable = ...;`. В Scala 3 мы стремимся к максимальной выразительности.

### 1\. Объявление переменных: `val` vs `var`

Это первое правило Scala: **всегда используйте `val`, если нет веской причины использовать `var`**.

  * **`val` (Value)**: Неизменяемая ссылка. Аналог `final` переменной в Java. После присвоения переопределить нельзя. Это потокобезопасно и предсказуемо.
  * **`var` (Variable)**: Изменяемая переменная. Аналог обычной переменной в Java.

<!-- end list -->

```scala
// --- Java Style Mental Model ---
// final int x = 10;
// int y = 20;

// --- Scala 3 ---

// 1. Типы указывать необязательно (Type Inference работает в 99% случаев)
val x = 10         // Компилятор знает, что это Int
var y = 20         // Компилятор знает, что это Int

// 2. Можно указать тип явно (полезно для публичных API)
val greeting: String = "Hello, Scala"

// 3. Попытка изменения
// x = 11          // Ошибка компиляции: Reassignment to val
y = 21             // OK, так как это var

// 4. Блочная инициализация (очень мощная фича)
// В Scala всё есть выражение (об этом позже), даже блоки кода {}.
val computedValue = {
  val a = 5
  val b = 10
  a + b // Последняя строка блока возвращается как значение
} 
// computedValue теперь равно 15
```

### 2\. Новый синтаксис: Отступы (Significant Indentation)

Scala 3 вводит опциональный синтаксис без фигурных скобок (как в Python). Это называется **braceless syntax**. Хотя скобки `{}` всё ещё поддерживаются, сообщество и гайдлайны мигрируют на отступы.

**Правила:**

  * Вместо открывающей `{` используется двоеточие `:` (в некоторых конструкциях) или просто перенос строки с отступом.
  * Ключевое слово `then` в `if`-выражениях.
  * Ключевое слово `do` в циклах (иногда).

<!-- end list -->

```scala
// === Java / Scala 2 Style (Braces) ===
if (x > 0) {
    println("Positive");
} else {
    println("Negative");
}

// === Scala 3 Style (Indentation) ===

// Условие
if x > 0 then
  println("Positive") // Отступ (обычно 2 пробела) важен!
else
  println("Negative")

// Определение метода (подробнее в разделе функций)
def calculate(a: Int, b: Int): Int =
  val result = a * b
  result + 1   // Возврат значения (return писать не нужно)
```

### 3\. Top-level Definitions

В Java *всё* должно жить внутри класса. Даже `public static void main`.
В Scala 3 вы можете писать методы, переменные и типы прямо в корне файла.

*Файл: `MyUtils.scala`*

```scala
package com.example.utils

// Эта переменная видна везде в пакете com.example.utils
val Version = "1.0.0" 

// Этот метод можно импортировать и вызывать без создания экземпляра класса
def printHello(): Unit = 
  println(s"Hello from version $Version")

// В Java компилятор создаст для этого скрытый класс "MyUtils$package",
// но вам об этом думать не нужно.
```

### 4\. Строки и Интерполяция

Забудьте про `String.format` или конкатенацию через `+`. Scala предлагает интерполяторы: префиксы перед строкой, которые меняют способ её обработки.

#### s-interpolator (Substitution)

Самый частый вариант. Позволяет вставлять переменные (`$var`) и выражения (`${expr}`) прямо в строку.

```scala
val name = "Developer"
val age = 30

// Простое внедрение
println(s"Hello, $name") 

// Внедрение выражения (нужны фигурные скобки)
println(s"Next year you will be ${age + 1}") 

// Экранирование кавычек внутри
println(s"He said: \"Scala is cool\"")
```

#### f-interpolator (Formatting)

Аналог `printf` в C/Java. Позволяет форматировать числа, даты и т.д.

```scala
val height = 1.856
val money = 100

// %2.2f — 2 знака после запятой
// %04d — целое число, дополненное нулями до 4 знаков
println(f"Height: $height%2.2f, Money: $money%04d")
// Вывод: Height: 1.86, Money: 0100
```

#### raw-interpolator

Игнорирует escape-последовательности (как `\n`, `\t`). Полезно для регулярных выражений.

```scala
// В обычном s-строке \n перенесет строку
// В raw-строке \n останется символами
val regex = raw"(\d{3})-\w+" 
// Вывод: (\d{3})-\w+
```

#### Multiline Strings (Многострочные строки)

Используются тройные кавычки `"""`. В Scala 3 они стали умнее и умеют удалять отступы с помощью `stripMargin` (старый стиль) или автоматически (новый стиль).

```scala
val json = """
  {
    "key": "value",
    "list": [1, 2, 3]
  }
  """
// Scala сама понимает, что отступ слева нужно игнорировать
```

-----

### Итоговое сравнение (Java -\> Scala)

| Концепция        | Java                                   | Scala 3                              |
| :--------------- | :------------------------------------- | :----------------------------------- |
| Константа        | `final int x = 1;`                     | `val x = 1`                          |
| Переменная       | `int x = 1;`                           | `var x = 1`                          |
| Блоки кода       | `{ ... }`                              | Отступы (Indentation)                |
| main метод       | В классе: `public static void main...` | Просто метод `@main def run() = ...` |
| Точка с запятой  | Обязательна `;`                        | Не нужна (ставится компилятором)     |
| Возврат значения | `return x;`                            | Просто `x` (последнее выражение)     |

-----

## 1.2. Система типов (Basics)

В Java мир разделен на две части: **примитивы** (`int`, `boolean`, `double`) и **объекты** (`Integer`, `String`, `MyClass`). Это создает боль (вспомните `int` vs `Integer`, массивы vs коллекции, необходимость стримов маппить через `mapToInt`).

В Scala **всё является объектом**. Иерархия типов едина.

### 1\. Единая иерархия: `Any`

Вершиной всего является тип **`Any`**. У него есть два прямых наследника:

#### A. `AnyVal` (Аналог примитивов)

Сюда входят встроенные типы значений: `Int`, `Double`, `Boolean`, `Char`, `Long`, `Byte`, `Short`, `Float` и `Unit`.

  * **Важно для перфоманса:** Scala — это JVM язык. Во время компиляции Scala пытается (и очень успешно) оптимизировать `Int` до жабовского примитива `int`. Вы получаете удобство методов (как у объекта) и скорость (как у примитива).
  * Вызов `10.toString()` валиден, потому что `10` — это `Int` (объект).

#### B. `AnyRef` (Аналог `java.lang.Object`)

Сюда попадают все остальные классы: `String`, `List`, ваши пользовательские классы.

  * В контексте JVM `AnyRef` напрямую мапится на `java.lang.Object`.

<!-- end list -->

```scala
val list: List[Any] = List(
  1,              // Int (AnyVal)
  "String",       // String (AnyRef)
  true,           // Boolean (AnyVal)
  (x: Int) => x   // Function (AnyRef)
)
// Это невозможно в Java без боксинга в Object. 
// В Scala это естественно, так как у всех общий предок Any.
```

-----

### 2\. `Unit` vs `void`

В Java `void` — это ключевое слово, означающее "ничего не возвращает". Это создает проблемы в дженериках (нельзя написать `Function<String, void>`).

В Scala **каждое** выражение возвращает значение. Если функция делает только сайд-эффект (например, печатает в консоль), она возвращает значение типа **`Unit`**.

  * **Тип:** `Unit`
  * **Значение:** `()` (пустые скобки).

<!-- end list -->

```scala
// Java: public void log(String msg) { ... }

// Scala:
def log(msg: String): Unit = 
  println(msg) 

val result = log("Test")
// result имеет тип Unit и равен ()
println(result) // Выведет: ()
```

*Это позволяет использовать методы типа `void` в дженериках без костылей.*

-----

### 3\. Нижние типы: `Null` и `Nothing`

Внизу иерархии (см. диаграмму выше мысленно) находятся типы, которые наследуются **от всего**.

#### `Null`

  * Это тип значения `null`.
  * Технически наследуется от всех `AnyRef`.
  * **Scala Way:** Мы стараемся избегать `null`. В Scala 3 есть опция компилятора (Explicit Nulls), которая делает `String` несовместимым с `null` (нужно писать `String | Null`), но по дефолту совместимость для интеропа с Java сохранена.

#### `Nothing`

Это самый сложный концепт для новичков. `Nothing` — это подтип **абсолютно всех типов** (и `Int`, и `String`, и `List`).

  * **Зачем?** Чтобы обозначить выражение, которое **никогда** не вернет управление нормально (выбросит исключение или зациклится).

<!-- end list -->

```scala
def fail(msg: String): Nothing = throw new RuntimeException(msg)

// Почему это работает?
// Переменная x должна быть Int.
// Функция fail возвращает Nothing.
// Так как Nothing — подтип Int, компилятор счастлив.
val x: Int = if (condition) 10 else fail("Error")
```

*Если бы `fail` возвращал `Unit`, код выше не скомпилировался бы, так как `Unit` не является `Int`.*

-----

### 4\. Type Inference (Вывод типов)

Scala компилятор очень умен. Вам не нужно писать типы везде.

**Когда типы можно опустить:**

  * Локальные переменные.
  * Возвращаемые значения простых методов.

**Когда типы НУЖНО писать (Best Practices):**

1.  **Публичные методы API.** (Чтобы код был читаемым для людей и чтобы случайно не изменить контракт, вернув другой тип).
2.  **Рекурсивные методы.** (Компилятор не может вывести тип, пока не проанализирует тело, а тело вызывает метод — замкнутый круг).
3.  **Когда вывод неоднозначен** (например, при наследовании).

<!-- end list -->

```scala
// Плохо (для публичного API):
def calculate(x: Int) = x * 1.5 
// Возвращает Double, но читателю придется лезть в код, чтобы понять это.

// Хорошо:
def calculatePublic(x: Int): Double = x * 1.5

// Рекурсия (тип обязателен):
def factorial(n: Int): Int = 
  if n == 0 then 1 else n * factorial(n - 1)
```

-----

### Итог раздела 1.2

1.  **`Any`** — босс всех типов.
2.  **`AnyVal`** — примитивы (эффективны), **`AnyRef`** — ссылки.
3.  **`Unit`** вместо `void` (это реальный объект `()`).
4.  **`Nothing`** — означает "здесь все сломалось" или "потока управления нет", подходит к любому типу.

-----

## 1.3. Управление потоком (Control Structures)

### 1\. `if` / `else` — это выражение

В Java у нас есть обычный `if` (инструкция) и тернарный оператор `? :` (выражение).
В Scala **тернарного оператора нет**. Он не нужен, потому что `if` сам по себе возвращает значение.

```scala
val x = 10

// Java: String result = (x > 0) ? "Positive" : "Negative";

// Scala 3:
val result = if x > 0 then "Positive" else "Negative"

// Поскольку это выражение, тип переменной result выводится автоматически (String).
```

**Нюанс с типами:**
Если ветки возвращают разные типы, Scala найдет их ближайшего общего предка (LUB — Least Upper Bound).

```scala
// "Positive" — String
// -1 — Int
// Общий предок String и Int — Any
val mixed: Any = if x > 0 then "Positive" else -1 
```
-----

### 2\. Циклы и For-Comprehensions

Это самый частый момент путаницы ("Gotcha") для Java-разработчиков.

#### `while`

Он есть, он работает как в Java, возвращает `Unit`. Используется крайне редко, только в алго-задачах или очень низкоуровневом коде для оптимизации.

#### `for` — это не цикл

То, что выглядит как цикл `for`, в Scala называется **For-Comprehension**. Это синтаксический сахар для цепочки вызовов методов `map`, `flatMap`, `filter` и `foreach`.

**А. Аналог цикла `foreach` (Side-effects)**
Если вы хотите просто пройтись по коллекции и что-то сделать (не создавая новую), используйте ключевое слово `do`.

```scala
val numbers = List(1, 2, 3)

// Java: for(int i : numbers) { System.out.println(i); }

// Scala:
for i <- numbers do println(i)
// "i <- numbers" называется Генератором.
```

**Б. Создание новой коллекции (`yield`)**
Это киллер-фича. Ключевое слово `yield` собирает результаты каждой итерации в новую коллекцию. Это прямой аналог `stream().map().collect()` в Java, но без бойлерплейта.

```scala
val numbers = List(1, 2, 3, 4, 5)

// Java Stream API:
// List<Integer> doubled = numbers.stream()
//     .filter(i -> i % 2 == 0)
//     .map(i -> i * 2)
//     .collect(Collectors.toList());

// Scala For-Comprehension:
val doubled = for 
  i <- numbers if i % 2 == 0 // Guard (фильтр)
yield 
  i * 2                      // Map (трансформация)

// Результат: List(4, 8)
```

**Вложенные циклы:**
В Java это "Arrow Anti-pattern" (лесенка вправо). В Scala это выглядит плоско и элегантно.

```scala
val chars = List('a', 'b')
val nums = List(1, 2)

// Декартово произведение
val combinations = for 
  c <- chars
  n <- nums
yield s"$c$n"

// Результат: List("a1", "a2", "b1", "b2")
// Под капотом это превращается в chars.flatMap(c => nums.map(n => ...))
```

-----

### 3\. Pattern Matching (`match`) — Switch на стероидах

`match` — это одно из самых мощных выражений в Scala. Это не просто проверка значений, это **деструктуризация** данных.

  * Возвращает значение (как и `if`).
  * Нет `break` (нет проваливания в следующий case).
  * Если ни один `case` не подошел и нет `case _` (default), упадет `MatchError`.

<!-- end list -->

```scala
val status = 404

val message = status match
  case 200 => "OK"
  case 404 => "Not Found"
  case 500 => "Server Error"
  case _   => "Unknown" // Аналог default
```

**Pattern Matching с типами (instanceof + cast в одном флаконе):**

```scala
val obj: Any = "Hello"

val description = obj match
  case s: String => s"It is a String of length ${s.length}" // s уже скастовано к String
  case i: Int    => s"It is an Integer: $i"
  case _         => "Something else"
```

**Guards (Условия в кейсах):**
Вы можете добавлять `if` прямо в pattern matching.

```scala
val response = 404

val typeOfError = response match
  case code if code >= 400 && code < 500 => "Client Error"
  case code if code >= 500               => "Server Error"
  case _                                 => "Success or Unknown"
```

-----

### Итог раздела 1.3

1.  **`if/else`** возвращает значение. Тернарного оператора нет.
2.  **`for`** с `yield` создает новую коллекцию (аналог `map`). Без `yield` — просто цикл.
3.  **`match`** заменяет `switch` и `instanceof`, позволяет писать сложную логику ветвления лаконично.

-----

# Часть 2: Объектно-Ориентированное Программирование

## 2.1. Классы и Объекты

В Java класс часто превращается в "свалку": поля, конструкторы, геттеры/сеттеры, статические методы, методы экземпляра — всё в одной куче.
Scala разделяет эти понятия:

1.  **`class`** — описание того, из чего состоит объект и как он себя ведет (blueprint).
2.  **`object`** — то, что существует в единственном экземпляре (синглтон/статика).

### 1\. Классы и Первичный конструктор

В Scala **конструктор — это и есть тело класса**. Вам не нужно создавать отдельный метод с именем класса. Параметры конструктора указываются сразу после имени класса.

**Java (Mental Model):**

```java
public class User {
    private final String name;
    private int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
        System.out.println("User created");
    }
    // + Getters/Setters...
}
```

**Scala 3:**

```scala
// 1. Параметры класса — это аргументы конструктора.
// val — создает неизменяемое поле + геттер (public по дефолту).
// var — создает поле + геттер + сеттер.
// Без val/var — это просто аргумент конструктора (не становится полем класса, если не используется в методах).
class User(val name: String, var age: Int) {
  
  // 2. Тело класса — это и есть тело конструктора.
  // Этот код выполняется при создании экземпляра (new User).
  println(s"User $name created with age $age")

  // 3. Методы
  def greet(): String = s"Hi, I'm $name"
  
  // 4. Перегрузка конструкторов (Auxiliary Constructors)
  // В Scala это нужно реже из-за параметров по умолчанию (см. ниже),
  // но если нужно, используют 'def this'.
  def this(name: String) = this(name, 18) // Делегирование в основной
}

// Использование:
val u = User("Alice", 25) // В Scala 3 слово 'new' часто можно опускать (см. apply ниже)
println(u.name)           // Доступ к геттеру
u.age = 26                // Доступ к сеттеру (т.к. var)
// u.name = "Bob"         // Ошибка компиляции (т.к. val)
```

**Параметры по умолчанию (Default Arguments):**
Убийца паттерна "Telescoping Constructor" (когда у вас 10 конструкторов с разным кол-вом параметров).

```scala
class Config(host: String = "localhost", port: Int = 8080)

val c1 = Config()             // localhost, 8080
val c2 = Config("127.0.0.1")  // 127.0.0.1, 8080
val c3 = Config(port = 9000)  // localhost, 9000 (Named arguments!)
```

-----

### 2\. Objects (Синглтоны)

В Scala нет ключевого слова `static`. Вообще. Вместо этого есть `object`.
`object` объявляет класс и сразу создает его единственный экземпляр.

```scala
object DatabaseConnection {
  private val url = "jdbc:..."
  
  def connect(): Unit = println(s"Connecting to $url")
}

// Использование (как статика в Java):
DatabaseConnection.connect()
```

*Это потокобезопасно и лениво (инициализируется при первом обращении).*

-----

### 3\. Companion Objects (Компаньоны)

Это замена статической части обычного класса.
Если у вас есть `class X` и `object X` **в одном файле**, они называются **компаньонами**.

  * **Суперспособность:** Они имеют доступ к `private` полям друг друга.

<!-- end list -->

```scala
class Circle(private val radius: Double) {
  import Circle._ // Импорт статики из компаньона
  
  def area: Double = calculateArea(radius)
}

object Circle {
  private val Pi = 3.14159
  
  // Приватный метод компаньона, но класс Circle его видит
  private def calculateArea(r: Double): Double = Pi * r * r
}
```
-----

### 4\. Метод `apply`: Магия отсутствия `new`

Вы заметите, что в Scala редко пишут `new List(...)` или `new User(...)`. Обычно пишут просто `List(...)`.
Это работает благодаря специальному методу `apply` в объекте-компаньоне.

**Правило:** Вызов объекта как функции `Object()` компилятор превращает в `Object.apply()`.

```scala
class Person(val name: String)

object Person {
  // Фабричный метод
  def apply(name: String): Person = new Person(name)
}

// Использование:
val p1 = Person.apply("Bob") // Явный вызов
val p2 = Person("Bob")       // Синтаксический сахар (выглядит как конструктор)
```

> **Scala 3 Update:** В Scala 3 компилятор научился генерировать этот метод автоматически для обычных классов, если вы их вызываете без `new`. Но понимать механизм `apply` критически важно, так как он используется в коллекциях (`List(1,2)`), функциях и многих библиотеках.

-----

### Итог раздела 2.1

1.  **Первичный конструктор** объявляется прямо в заголовке класса: `class User(val id: Int)`.
2.  **`val`/`var` в конструкторе** автоматически создают поля и геттеры/сеттеры.
3.  **`static` не существует**. Всё, что должно быть статическим, выносится в `object` (компаньон).
4.  **`apply`** позволяет создавать объекты без слова `new`, имитируя вызов функции.

-----

## 2.2. Case Classes & Enums (Моделирование данных)

### 1\. Case Classes (Умные DTO)

Обычные классы в Scala (`class`) предназначены для инкапсуляции состояния и поведения (сервисы, контроллеры, connection pools).
**Case Classes** предназначены для хранения **неизменяемых данных** (DTO, Events, Messages, Configs).

Когда вы добавляете слово `case` перед `class`, компилятор делает за вас огромную работу.

```scala
// Однострочное определение
case class User(id: Long, name: String, email: String)
```

**Что вы получаете бесплатно (The Magic):**

1.  **Immutability:** Все параметры конструктора автоматически становятся `public val`.
2.  **No `new`:** Автоматически генерируется метод `apply`, поэтому `new User(...)` писать не нужно.
3.  **Value Equality:** Генерируются `equals` и `hashCode` **по полям**, а не по ссылке. Два разных объекта с одинаковыми данными равны.
4.  **Readable String:** Генерируется красивый `toString` (`User(1, "Bob", ...)`).
5.  **Pattern Matching:** Генерируется метод `unapply`, позволяющий разбирать объект в `match` (об этом в разделе 3.3).
6.  **Copying:** Генерируется метод `copy`.

#### Метод `copy` (Замена Сеттеров)

Так как все поля `val`, вы не можете сделать `user.name = "Alice"`. Как же изменить поле?
Вы создаете **копию** объекта с измененным полем. Это база функционального подхода: старый объект остается неизменным (безопасно для многопоточности), новый содержит изменения.

```scala
val user1 = User(1, "Bob", "bob@gmail.com")

// Хотим сменить email.
// Метод copy принимает именованные аргументы для тех полей, которые нужно изменить.
val user2 = user1.copy(email = "bob_new@gmail.com")

println(user1) // User(1, Bob, bob@gmail.com) — старый жив и здоров
println(user2) // User(1, Bob, bob_new@gmail.com) — новый создан
```

-----

### 2\. Enums и ADT (Алгебраические Типы Данных)

В Java `enum` — это просто список констант. В Scala 3 `enum` — это полноценный инструмент для создания **ADT (Algebraic Data Types)**.

ADT описывают данные через "И" (Product types, как case class: имя И возраст) и "ИЛИ" (Sum types: Успех ИЛИ Ошибка).

#### А. Простой Enum (как в Java)

```scala
enum Color:
  case Red, Green, Blue

// Использование
val c = Color.Red
```

#### Б. Параметризованный Enum (как в Java)

Enum может иметь конструктор и методы.

```scala
enum Planet(mass: Double, radius: Double):
  private final val G = 6.67300E-11
  
  def surfaceGravity: Double = G * mass / (radius * radius)
  
  case Earth extends Planet(5.976e+24, 6.37814e6)
  case Mars  extends Planet(6.421e+23, 3.3972e6)
```

#### В. ADT (Sum Types) — The Game Changer

Вот здесь Scala уходит далеко вперед. Элементы enum-а могут быть **разными по структуре**. Это позволяет моделировать события или состояния, которые не укладываются в плоскую таблицу.

В Java для этого приходится использовать `sealed interface` + кучу `record` или классов (начиная с Java 17). В Scala это лаконично.

*Пример: Модель команд для UI*

```scala
enum Command:
  // Просто синглтон (без данных)
  case Quit 
  
  // Содержит данные (Product type)
  case Move(x: Int, y: Int) 
  
  // Содержит сложные данные
  case Write(text: String, mode: String)

// Как с этим работать? Только через Pattern Matching!
def execute(cmd: Command): Unit = cmd match
  case Command.Quit => 
    println("Bye!")
  case Command.Move(x, y) => 
    println(s"Moving to $x, $y")
  case Command.Write(text, _) => 
    println(s"Writing: $text")
```

Это обеспечивает **Compile-time safety**: если вы добавите новую команду в Enum, компилятор в месте `match` скажет: "Эй, ты забыл обработать новый кейс\!". В Java `switch` это тоже пытается делать, но в Scala это основа языка.

-----

### 3\. Sealed Traits (Классический подход)

До Scala 3 (и иногда сейчас для сложной иерархии) вместо `enum` использовали комбинацию `sealed trait` и `case class`. Вы часто встретите это в существующем коде.

  * **`sealed`**: Означает, что все наследники этого трейта должны находиться **в этом же файле**. Это позволяет компилятору знать *все* возможные варианты (exhaustiveness check).

<!-- end list -->

```scala
// То же самое, что enum Command выше, но в синтаксисе Scala 2 / Classic
sealed trait Command
object Command {
  case object Quit extends Command
  case class Move(x: Int, y: Int) extends Command
  case class Write(text: String) extends Command
}
```

*В Scala 3 лучше использовать `enum`, так как это более краткий сахар над той же самой концепцией.*

-----

### Итог раздела 2.2

| Концепция        | Java                                                 | Scala 3                         |
| :--------------- | :--------------------------------------------------- | :------------------------------ |
| Неизменяемый DTO | `record User(String name) {}`                        | `case class User(name: String)` |
| Изменение полей  | Сеттеры (Mutation) или Wither-методы (boilerplate)   | Метод `copy` (из коробки)       |
| Enum с данными   | Сложная иерархия классов или `sealed` интерфейсы     | `enum` с параметрами (ADT)      |
| Сравнение        | `equals()` нужно переопределять (до Java 16 Records) | По значению (из коробки)        |

-----

## 2.3. Трейты (Traits)

В Java у вас есть четкое разделение:

  * **Interface**: Контракт, дефолтные методы (с Java 8), никакой памяти (стейта), только статические константы.
  * **Abstract Class**: Контракт, методы, полноценный стейт, конструкторы, но наследоваться можно только от одного.

В Scala **Trait (Трейт)** объединяет лучшее из обоих миров. Это интерфейсы, которые могут содержать поля, методы и поведение, и их можно "подмешивать" (mix-in) во множество классов.

### 1\. Трейты как Интерфейсы

Базовое использование идентично Java-интерфейсам.

```scala
trait Logger {
  // Абстрактный метод
  def log(msg: String): Unit 
  
  // Реализованный метод (как default в Java)
  def info(msg: String): Unit = log(s"[INFO] $msg")
}

class ConsoleLogger extends Logger {
  // override обязателен при переопределении конкретных методов,
  // но опционален при реализации абстрактных (в Scala 3).
  // Для чистоты кода лучше писать override всегда.
  override def log(msg: String): Unit = println(msg)
}
```

### 2\. Трейты со Состоянием (State)

В Java интерфейсах вы не можете объявить `private int counter;`. В трейтах Scala — можете.

```scala
trait IdGenerator {
  private var counter = 0 // Поле состояния внутри трейта!
  
  def nextId(): Int = {
    counter += 1
    counter
  }
}

class UserParams extends IdGenerator {
  val id = nextId() // Работает
}
```

*Под капотом Scala компилятор хитро вплетает эти поля в байт-код целевого класса.*

-----

### 3\. Множественное наследование и Mixins

В Java класс делает `implements A, B`. В Scala класс "расширяет" (extends) один трейт/класс и "подмешивает" (with) остальные.

В Scala 3 синтаксис стал проще, можно использовать запятые, но ключевое слово `with` всё ещё используется для создания типов на лету.

```scala
trait A
trait B
class Base

// Класс C наследует Base и подмешивает A и B
class C extends Base, A, B 
// В Scala 2 это выглядело бы как: extends Base with A with B
```

### 4\. Проблема Ромба и Линеаризация (Linearization)

Самый сложный вопрос на собеседованиях по C++ или Java (с дефолтными методами): **Что будет, если класс наследует два трейта, у которых есть метод с одинаковой сигнатурой?**

Java заставит вас переопределить метод и явно выбрать `InterfaceA.super.method()`.
Scala решает это автоматически через механизм **Линеаризации классов**.

**Правило "Stackable Modifications":**
При вызове `super` Scala вызывает метод не "родителя" в иерархии наследования, а "следующего справа налево" трейта в списке смешивания.

```scala
trait Writer {
  def write(msg: String): Unit = println(msg)
}

trait UpperCase extends Writer {
  // super.write вызовет метод следующего трейта в цепочке
  override def write(msg: String): Unit = super.write(msg.toUpperCase)
}

trait Trim extends Writer {
  override def write(msg: String): Unit = super.write(msg.trim)
}

// ВНИМАНИЕ НА ПОРЯДОК:
// Сначала выполняется Trim (последний подмешанный), потом UpperCase, потом Writer.
class MyWriter extends Writer, UpperCase, Trim

val w = MyWriter()
w.write("  hello  ")
// Логика:
// 1. Вызов MyWriter.write (наследован от Trim)
// 2. Trim делает trim ("hello") -> вызывает super.write
// 3. super для Trim в этой цепочке — это UpperCase! (а не Writer)
// 4. UpperCase делает toUpperCase ("HELLO") -> вызывает super.write
// 5. Writer печатает "HELLO"
```

Это позволяет строить **цепочки обязанностей** (Decorator Pattern) просто через наследование, без создания классов-оберток.

-----

### 5\. Параметры трейтов (Trait Parameters) — Scala 3 Feature

В Scala 2 трейты не могли иметь параметров конструктора. Это была главная причина использовать абстрактные классы.
В Scala 3 это ограничение снято.

```scala
// Теперь трейт выглядит как абстрактный класс
trait Greeter(val greeting: String) {
  def greet(name: String): Unit = println(s"$greeting, $name")
}

class EnglishGreeter extends Greeter("Hello")
class RussianGreeter extends Greeter("Привет")
```

**Важное отличие от Абстрактного Класса:**
Если вы наследуете несколько трейтов с параметрами, правила передачи аргументов становятся сложнее, но архитектурно трейты гибче, так как их можно смешивать, а абстрактный класс — только один.

-----

### Итог раздела 2.3

1.  **Трейты** = Интерфейсы + Реализация + Состояние.
2.  **Множественное наследование** работает через Mixins.
3.  **Линеаризация** определяет порядок вызова `super` (справа налево). Это позволяет наслаивать поведение (как декораторы).
4.  **Trait Parameters** в Scala 3 позволяют передавать аргументы в трейты, почти убивая необходимость в абстрактных классах.

-----

## 2.4. Модификаторы доступа и наследования

В Java система доступа довольно запутанная: `public` (везде), `private` (класс), `protected` (наследники + пакет) и package-private (дефолт, только пакет).

Scala упрощает правила доступа, но усложняет (в хорошем смысле) правила наследования.

### 1\. Модификаторы доступа (Visibility)

В Scala **по умолчанию всё `public`**. Вам не нужно писать `public class` или `public void`.

| Модификатор    | Scala                           | Java Comparison                                  |
| :------------- | :------------------------------ | :----------------------------------------------- |
| (нет)          | **Public** (видно везде)        | `public`                                         |
| `private`      | Только этот класс (и компаньон) | `private`                                        |
| `protected`    | **Только наследники**           | `protected` (но **без** доступа внутри пакета\!) |
| `private[pkg]` | Видно внутри пакета `pkg`       | package-private (default in Java)                |

#### Scoped Private (`private[x]`)

Это киллер-фича для модульности. Вы можете открыть доступ к методу не всему миру, а только конкретному пакету (и его подпакетам). Это позволяет создавать "публичные API модуля", скрывая кишки реализации, даже если они разбросаны по разным файлам внутри пакета.

```scala
package com.company.core

class Engine {
  // Видно только внутри класса Engine
  private val secret = 42
  
  // Видно любому классу в пакете com.company.core (и sub-packages)
  // Например, класс com.company.core.utils.Helper увидит это поле.
  // Но класс com.company.ui.Window — нет.
  private[core] val sharedState = "Internal logic"
  
  // Видно только наследникам (не видно соседям по пакету!)
  protected val legacyConfig = true
}
```

-----

### 2\. Модификаторы наследования (Scala 3)

В Java мы часто забываем писать `final`, и классы остаются открытыми для наследования по умолчанию. Это приводит к "Fragile Base Class Problem": кто-то наследуется от вашего класса, переопределяет метод, ломает инварианты, а вы потом боитесь обновить библиотеку.

Scala 3 меняет правила игры, вводя строгую политику расширения.

#### A. `final` (Как в Java)

Класс нельзя наследовать. Точка.

  * **Best Practice:** Делайте классы `final` по умолчанию, если не планируете иного.

#### B. `sealed` (Изолированный)

Класс можно наследовать, но **только в том же файле**.

  * Это гарантия для компилятора, что он знает *всех* наследников.
  * Используется для Enums, ADT и жестких иерархий.

#### C. `open` (Новинка Scala 3)

Класс **явно** разрешен для наследования кем угодно (в любом файле).

  * Это сигнал разработчику: "Автор этого класса подумал о наследовании, предусмотрел хуки и гарантирует, что `override` ничего не сломает".
  * Если вы попробуете наследоваться от обычного класса (не `open` и не `final`) в другом файле, компилятор выдаст предупреждение (при включенной опции `-source:future`), требуя явного согласия.

<!-- end list -->

```scala
// 1. Запрещено наследовать
final class DatabaseConnection

// 2. Можно наследовать только здесь (для pattern matching)
sealed trait Event
case class Login(user: String) extends Event
case class Logout(user: String) extends Event

// 3. Спроектирован для расширения (например, базовый контроллер фреймворка)
open class BaseController {
  def handleRequest(): Unit = ???
}

// 4. Обычный класс
class UserUtils
// Если кто-то в другом файле напишет "class MyUtils extends UserUtils",
// компилятор может ругаться: "Ad-hoc extension of non-open class".
```

-----

### Итог раздела 2.4

1.  **Public по умолчанию:** Меньше шума.
2.  **`protected` строже:** Только наследники, пакет не видит.
3.  **`private[package]`:** Точный контроль видимости внутри модулей (лучше, чем Java package-private).
4.  **Наследование:**
      * `final`: Запрещено.
      * `sealed`: Ограничено файлом (для ADT).
      * `open`: Разрешено везде (explicit design).

-----

# Часть 3: Функциональное Программирование (FP Core)

## 3.1. Функции как объекты первого класса

Фраза "First-class citizen" означает, что функции можно делать всем тем же, что и обычными переменными:

1.  Присваивать переменным.
2.  Передавать как аргументы в другие функции.
3.  Возвращать из функций.

В Java есть функциональные интерфейсы (`Function<T, R>`, `Predicate<T>`, `Consumer<T>`).
В Scala есть **функциональные типы**.

### 1\. Типы функций (Function Types)

Синтаксис типов в Scala предельно чист. Стрелка `=>` — это всё, что вам нужно.

  * **Java:** `Function<String, Integer>`

  * **Scala:** `String => Int`

  * **Java:** `BiFunction<Integer, Integer, String>`

  * **Scala:** `(Int, Int) => String`

  * **Java:** `Consumer<String>` (принимает, ничего не возвращает)

  * **Scala:** `String => Unit`

<!-- end list -->

```scala
// Объявление переменной, которая хранит функцию
val stringLength: String => Int = str => str.length

// Вызов
val len = stringLength("Scala") // 5
```

-----

### 2\. Анонимные функции (Лямбды)

Синтаксис похож на Java, но используется `=>` вместо `->`.

```scala
// Java: (x) -> x + 1
val increment = (x: Int) => x + 1

// Если типы понятны из контекста, их можно опустить
val add = (a: Int, b: Int) => a + b
```

#### Placeholder Syntax (`_`) — Сахар

Если параметр используется в теле функции **один раз**, его можно не именовать, а заменить на `_`.

```scala
val numbers = List(1, 2, 3)

// Полная версия:
numbers.map(x => x + 1)

// Сокращенная версия (Placeholder):
// Первый _ заменяет первый аргумент
numbers.map(_ + 1) 

// Для двух аргументов:
// (a, b) => a + b превращается в:
val sum: (Int, Int) => Int = _ + _ 
// Первый _ — это первый аргумент, второй _ — это второй аргумент.
```

> **Важно:** `_` работает позиционно. `_ + _` развернется в `(x, y) => x + y`, а не в `x => x + x`.

-----

### 3\. Функции высшего порядка (HOF)

Это функции, которые принимают другие функции. Вы будете писать их реже, чем использовать, но понимать сигнатуру нужно.

```scala
// Метод принимает:
// 1. Число a
// 2. Число b
// 3. Функцию operation, которая знает, как превратить два Int в Int
def calculate(a: Int, b: Int, operation: (Int, Int) => Int): Int =
  operation(a, b)

// Использование:
calculate(10, 5, (x, y) => x + y)       // 15
calculate(10, 5, _ * _)                 // 50 (умножение через placeholder)
calculate(10, 5, (x, y) => Math.max(x,y)) // 10
```

-----

### 4\. Методы (`def`) vs Функции (`val`)

Это тонкий момент.

  * **`def` (Метод):** Это часть класса/объекта. Аналог метода в Java. Он не является объектом сам по себе (пока вы его не превратите). Он вычисляется каждый раз при вызове.
  * **`val` (Функция):** Это объект (экземпляр трейта `FunctionN`). Он создается при инициализации переменной.

**Eta-expansion (Превращение метода в функцию):**
В Java вы используете `::` (method reference), чтобы передать метод туда, где ждут лямбду. Scala делает это автоматически или через `_`.

```scala
// Это метод (часть класса)
def isEvenMethod(i: Int): Boolean = i % 2 == 0

val nums = List(1, 2, 3, 4)

// 1. Автоматическая конвертация (Eta-expansion)
// map ожидает функцию (Int => B). Мы передаем имя метода.
// Компилятор сам создает лямбду x => isEvenMethod(x)
val evens = nums.filter(isEvenMethod)

// 2. Ручная конвертация (иногда нужна для отладки или сложного кода)
val predicateFunc: Int => Boolean = isEvenMethod _
```

-----

### 5\. Каррирование (Currying) и Множественные списки параметров

В Scala метод может иметь **несколько** списков параметров `()()`.
Это позволяет передавать аргументы по частям (частичное применение).

```scala
// Обычный метод
def add(x: Int, y: Int): Int = x + y

// Каррированный метод (два списка параметров)
def addCurried(x: Int)(y: Int): Int = x + y

// Использование:
val result = addCurried(10)(5) // 15

// Зачем? Частичное применение!
// Мы фиксируем первый аргумент (10) и получаем функцию, ожидающую второй аргумент.
val addTen: Int => Int = addCurried(10)

println(addTen(5)) // 15
println(addTen(20)) // 30
```

**Практическая польза:**
Это часто используется для создания DSL и передачи контекста.
Например, `list.foldLeft(0)(_ + _)` — здесь `0` это начальное значение (первая скобка), а функция сложения — это вторая скобка.

-----

### Итог раздела 3.1

1.  **Типы функций:** `String => Int` понятнее, чем `Function<String, Integer>`.
2.  **Placeholders:** `_ + 1` делает код очень компактным.
3.  **HOF:** Функции передаются как аргументы повсеместно.
4.  **Currying:** `def foo(a: Int)(b: Int)` позволяет создавать новые функции путем фиксации части аргументов.

-----

## 3.2. Работа с коллекциями (Immutable Collections)

Пакет `scala.collection.immutable` импортируется по умолчанию. Когда вы пишете `List` или `Map`, вы получаете неизменяемую версию.

### 1\. Иерархия и основные типы

В отличие от Java, где `List` — это интерфейс, а `ArrayList` — реализация, в Scala часто используют конкретные фабрики трейтов.

#### A. `List` (Связанный список)

Это **не** ArrayList. Это классический односвязный список (LinkedList): `Head` (элемент) + `Tail` (ссылка на остаток списка).

  * **Плюсы:** Моментальное добавление в начало (`O(1)`), идеален для рекурсии и паттерн-матчинга.
  * **Минусы:** Медленный доступ по индексу (`O(n)`).
  * **Аналог Java:** Нет прямого аналога в стандартном использовании (Java `LinkedList` ужасен по памяти), это ближе к структуре Lisp.

#### B. `Vector` (Замена ArrayList)

Если вам нужен случайный доступ по индексу (random access), используйте `Vector`.

  * Это широкое дерево (trie). Доступ к элементу фактически константный (эффективно `O(1)`).
  * Иммутабельный, но операции обновления очень быстры благодаря sharing-структуре (новый вектор переиспользует большую часть старого).

#### C. `Set` и `Map`

Работают так же, как в Java, но неизменяемы.
`Map` — это набор пар (Tuple2).

```scala
// Создание коллекций (метод apply работает везде)
val list = List(1, 2, 3)
val vector = Vector(1, 2, 3)
val set = Set(1, 2, 3, 1) // Set(1, 2, 3)

// Синтаксис Map: ключ -> значение
val map = Map("Java" -> 1995, "Scala" -> 2004)
```

-----

### 2\. Операции: Богатый API

В Java (до Streams) операции были бедными. В Scala методы `map`, `filter` и т.д. встроены прямо в коллекции.

> **Важное отличие от Java Streams:**
> В Java `list.stream().map(...).collect(...)` — это **ленивая** цепочка. Ничего не происходит до терминальной операции.
> В Scala `list.map(...)` — это **жадная (eager)** операция. Она сразу создает новый `List`.

```scala
val nums = List(1, 2, 3, 4, 5, 6)

// 1. map (Трансформация)
val doubled = nums.map(_ * 2) // List(2, 4, 6...)

// 2. filter (Выборка)
val evens = nums.filter(_ % 2 == 0) // List(2, 4, 6)

// 3. Цепочки (Chain)
// В отличие от Java, здесь на каждом шаге создается промежуточная коллекция.
// Для огромных данных это может быть накладно по памяти (см. LazyList ниже).
val result = nums
  .filter(_ > 2)
  .map(x => s"Number: $x")
  .mkString(", ") // Аналог Collectors.joining

// 4. find (Возвращает Option)
val firstEven: Option[Int] = nums.find(_ % 2 == 0) // Some(2)
```

-----

### 3\. flatMap (Уплощение)

Король монад. Если ваша функция возвращает коллекцию, `map` даст вам `List[List[String]]`. `flatMap` даст плоский `List[String]`.

```scala
val phrases = List("Hello World", "Scala is great")

// map: List(Array(Hello, World), Array(Scala, is, great))
val wordsBad = phrases.map(_.split(" ")) 

// flatMap: List(Hello, World, Scala, is, great)
val wordsGood = phrases.flatMap(_.split(" "))
```

-----

### 4\. Свертка: foldLeft

Аналог `reduce` в Java, но мощнее, так как позволяет менять возвращаемый тип (например, свернуть список чисел в строку или в Map).
В Scala есть `foldLeft` и `foldRight`. **Всегда используйте `foldLeft`** (он оптимизирован под хвостовую рекурсию и не переполнит стек).

```scala
val prices = List(10, 20, 30)

// Аргументы: (Начальное значение) (Функция аккумуляции)
// acc — накопленный результат, curr — текущий элемент
val total = prices.foldLeft(0) { (acc, curr) => 
  acc + curr 
}
// Короткая запись: prices.foldLeft(0)(_ + _)
```

-----

### 5\. Конструктор списка (`::`) и операторы

Вы часто увидите такие "иероглифы". В Scala любой метод, заканчивающийся на `:`, право-ассоциативен (вызывается на объекте справа).

  * `::` (произносится "cons") — добавить элемент в **голову** списка.
  * `Nil` — пустой список.

<!-- end list -->

```scala
// Эти две записи эквивалентны:
val list1 = List(1, 2, 3)
val list2 = 1 :: 2 :: 3 :: Nil 

// Как это читается компилятором:
// Nil.::(3).::(2).::(1) -> создается список с конца.
```

**Добавление элементов (создание новых коллекций):**

  * `elem +: list` — добавить в начало (Prepend).
  * `list :+ elem` — добавить в конец (Append). *Внимание: для List это O(N), используйте Vector\!*
  * `list1 ++ list2` — объединение двух коллекций.

-----

### 6\. LazyList (Аналог Java Stream)

Если вам нужна ленивость (как в Java Streams) или работа с бесконечными последовательностями, используйте `LazyList` (в старых версиях Scala он назывался `Stream`).

Элементы вычисляются только тогда, когда вы к ним обращаетесь.

```scala
// Бесконечный поток, начиная с 1
val naturals: LazyList[Int] = LazyList.from(1)

// Вычисления не происходят, пока мы не попросим результат
val first10Evens = naturals
  .filter(_ % 2 == 0)
  .map(_ * 2)
  .take(10) // Взять первые 10
  .toList   // Принудительное вычисление (Terminal operation)
```

-----

### 7\. Java Converters (Интероп)

Вы будете постоянно гонять коллекции туда-сюда между библиотеками Java и кодом Scala.
Для этого есть `scala.jdk.CollectionConverters`.

```scala
import scala.jdk.CollectionConverters._

// Java -> Scala
val javaList = new java.util.ArrayList[String]()
javaList.add("Java")

val scalaList = javaList.asScala.toList // Превратили в иммутабельный Scala List

// Scala -> Java
val myScalaMap = Map("key" -> "value")
val myJavaMap = myScalaMap.asJava // Получили java.util.Map
```

-----

### Итог раздела 3.2

1.  **Immutability:** Списки не меняются. Хотите изменить — создавайте новый (это дешево благодаря разделению структуры данных в памяти).
2.  **List** — для головы/хвоста, **Vector** — для индексов.
3.  **Eager Operations:** Методы `map`/`filter` выполняются сразу.
4.  **LazyList:** Для ленивых вычислений (аналог Stream API).
5.  **Interoperability:** `.asJava` / `.asScala` спасают жизнь.

-----

## 3.3. Pattern Matching (Сопоставление с образцом)

Механизм PM состоит из двух фаз:

1.  **Check:** Подходит ли объект под шаблон? (Тип, константа, структура).
2.  **Bind:** Если подходит, извлечь данные из объекта в переменные.

### 1\. Основы и Типизированные паттерны

`match` — это выражение. Оно возвращает результат.

```scala
val result = obj match
  // 1. Константа
  case 0 => "Zero"
  case true => "True"
  
  // 2. Типизированный паттерн (instanceof + cast)
  // Переменная 's' доступна только справа от => и уже имеет тип String
  case s: String => s"Text of length ${s.length}"
  
  // 3. Wildcard (аналог default)
  case _ => "Something else"
```

### 2\. Деконструкция Case Classes (Deep Matching)

Это самая частая операция. Мы "матчим" структуру объекта. Это обратная операция конструктору.

```scala
case class User(name: String, age: Int, role: String)

val user = User("Alice", 25, "Admin")

val message = user match
  // Паттерн: Имя любое (bind to n), возраст любой (bind to a), роль ОБЯЗАТЕЛЬНО "Admin"
  case User(n, a, "Admin") => 
    s"Welcome, Administrator $n ($a)"
    
  // Паттерн: Имя "Bob", остальное неважно
  case User("Bob", _, _) => 
    "Hi Bob!"
    
  // Паттерн: Любой другой юзер
  case User(n, _, r) => 
    s"User $n is just a $r"
```

*Обратите внимание: мы не вызывали `user.getName()`. Мы разобрали объект прямо в сигнатуре `case`.*

### 3\. Guards (Условия в паттернах)

Иногда одной структуры мало, нужно проверить условие. Используйте `if` прямо в кейсе.

```scala
val user = User("Alice", 17, "User")

user match
  // Сматчить структуру User, извлечь age, НО войти сюда только если age < 18
  case User(name, age, _) if age < 18 => 
    s"Access denied for $name. Come back in ${18 - age} years."
    
  case _ => "Access granted"
```

### 4\. Паттерны в списках (Recursion Friendly)

Здесь раскрывается мощь связных списков. Мы можем матчить голову и хвост.

```scala
val list = List(1, 2, 3)

val description = list match
  // Пустой список
  case Nil => "Empty"
  
  // Список ровно из одного элемента
  case List(x) => s"Single element: $x"
  
  // Список ровно из двух элементов (конкретных)
  case List(1, 2) => "One and Two"
  
  // Голова (первый элемент) и Хвост (остальной список)
  // Если list = List(1, 2, 3), то head = 1, tail = List(2, 3)
  case head :: tail => s"Starts with $head, rest has size ${tail.size}"
```

Это стандартный паттерн для написания рекурсивных функций в FP:

```scala
def sum(list: List[Int]): Int = list match
  case Nil => 0               // Базовый случай
  case head :: tail => head + sum(tail) // Рекурсивный шаг
```

### 5\. Sealed Trait Exhaustiveness Check

Если вы матчитесь по `sealed trait` или `enum`, компилятор знает всех наследников. Если вы забудете обработать какой-то вариант, компилятор выдаст **Warning** (в Scala 3 часто Error).

```scala
sealed trait Status
case object Active extends Status
case object Inactive extends Status
case object Suspended extends Status // Допустим, добавили новый статус

def handle(s: Status) = s match
  case Active => "Go"
  case Inactive => "Stop"
  // Компилятор закричит: "Match is not exhaustive! It would fail on pattern case: Suspended"
```

*Это спасает от багов при расширении бизнес-логики.*

### 6\. Unapply (Магия под капотом)

Как Scala понимает, как разобрать объект `User(n, a)`?
Для этого используется метод `unapply` в объекте-компаньоне.

  * **`apply` (Конструктор):** `(String, Int) -> User`
  * **`unapply` (Деструктор):** `User -> Option[(String, Int)]`

Если `unapply` возвращает `Some(tuple)`, паттерн совпал и переменные заполняются из кортежа. Вы можете писать свои экстракторы для чего угодно (например, парсинг строки email на user и domain).

```scala
object Email:
  def unapply(str: String): Option[(String, String)] = 
    val parts = str.split("@")
    if parts.length == 2 then Some(parts(0), parts(1)) else None

// Использование
"user@example.com" match
  case Email(user, domain) => println(s"Domain is $domain")
  case _ => println("Not an email")
```

### 7\. Pattern Matching не только в `match`

Это то, что Java-разработчики часто упускают. Деструктуризация работает везде\!

**А. При присваивании переменной:**

```scala
val pair = (10, "Scala")
// Распаковка кортежа сразу в переменные
val (number, text) = pair 
```

**Б. В цикле `for`:**

```scala
val map = Map("a" -> 1, "b" -> 2)

// Итерация сразу по ключу и значению
for (key, value) <- map do
  println(s"$key = $value")
```

**В. В блоке `catch`:**
Обработка исключений в Scala — это тоже Pattern Matching.

```scala
try
  dangerousMethod()
catch
  case e: IOException => println("IO Error")
  case e: NumberFormatException => println("Bad number")
```

-----

### Итог раздела 3.3

1.  **`match`** мощнее `switch`: он разбирает объекты, проверяет типы и условия.
2.  **Destructuring:** Вы можете доставать данные из глубин объекта (`case User(Address(City(name), _))`) одной строкой.
3.  **Exhaustiveness Check:** Компилятор следит, чтобы вы обработали все варианты `sealed` типов.
4.  **Everywhere:** Деструктуризация работает в `val`, `for` и `catch`.

-----

## 3.4. Обработка ошибок без исключений

Scala предлагает три основных типа-контейнера (Монады) для разных сценариев.

### 1\. `Option[T]` — Замена Null

Используется, когда значение может отсутствовать, и нам **не важно почему**. Это просто "есть" или "нет".

  * **`Some(value)`**: Значение есть.
  * **`None`**: Значения нет.

В Java есть `Optional`, но в Scala `Option` глубоко интегрирован в язык и коллекции.

```scala
val users = Map(1 -> "Alice", 2 -> "Bob")

// Метод get у Map возвращает Option[String]
val user1: Option[String] = users.get(1) // Some("Alice")
val user3: Option[String] = users.get(3) // None

// ПЛОХО (Java Style):
if (user1.isDefined) println(user1.get)

// ХОРОШО (Scala Style - map/getOrElse):
// Мы работаем с контейнером, не доставая значение явно.
val greeting = user1
  .map(name => s"Hello, $name") // Выполнится только если там Some
  .filter(_.length > 5)         // Доп. условие
  .getOrElse("Unknown User")    // Если в итоге None — вернуть дефолт

println(greeting)
```

### 2\. `Try[T]` — Изоляция исключений

Используется, когда мы вызываем **небезопасный код** (например, Java-библиотеку или ввод-вывод), который может бросить Exception.

  * **`Success(value)`**: Все прошло успешно.
  * **`Failure(exception)`**: Перехвачено исключение.

`Try` работает как `try-catch`, но упаковывает результат в объект, который можно передавать дальше.

```scala
import scala.util.{Try, Success, Failure}

def parseConfig(input: String): Try[Int] = Try {
  // Этот код выполняется "внутри защиты".
  // Если Integer.parseInt бросит NumberFormatException,
  // Try перехватит его и вернет Failure.
  Integer.parseInt(input) 
}

val result = parseConfig("123") // Success(123)
val error  = parseConfig("abc") // Failure(java.lang.NumberFormatException...)

// Обработка результата через Pattern Matching
error match
  case Success(code) => println(s"Config loaded: $code")
  case Failure(ex)   => println(s"Parsing failed: ${ex.getMessage}")

// Или через recover (аналог catch блока, который возвращает дефолт)
val safeValue = error.recover {
  case _: NumberFormatException => 0
}.get // Вернет 0
```

### 3\. `Either[L, R]` — Логические ошибки

Используется для **валидации и бизнес-логики**, когда нам **важно знать причину** ошибки, и это не обязательно Exception (например, "Недостаточно средств", "Пользователь уже существует").

  * **`Left(value)`**: Ошибка (обычно).
  * **`Right(value)`**: Успех (Правильный результат).
  * *Мнемоника:* "Right is right" (Правый — правильный).

В Scala 2.13+ `Either` является "Right-biased" (смещенным вправо). Это значит, что методы `map` и `flatMap` работают с правой стороной. Если там `Left`, они просто пропускают вычисление (short-circuit).

```scala
case class Error(code: Int, msg: String)
case class User(id: Int, name: String)

// Метод возвращает ИЛИ Ошибку, ИЛИ Юзера
def register(name: String): Either[Error, User] =
  if name.isEmpty then
    Left(Error(400, "Name is empty"))
  else if name == "admin" then
    Left(Error(403, "Admin reserved"))
  else
    Right(User(1, name))

// Цепочка вычислений (Railway Oriented Programming)
val result = register("Bob")
  .map(user => user.copy(name = user.name.toUpperCase)) // Меняем юзера, если он есть
  .map(user => s"Created user ${user.name}")            // Превращаем в сообщение

// Результат:
// Если register вернул Right(User), цепочка пройдет до конца.
// Если register вернул Left(Error), map-ы пропустятся, и result будет Left(Error).

// Финальная обработка
result match
  case Right(msg) => println(msg)
  case Left(err)  => println(s"Error ${err.code}: ${err.msg}")
```

### 4\. For-Comprehension с Монадами (Happy Path)

Это самый мощный паттерн. Представьте, что у вас есть 3 зависимых операции, каждая из которых может упасть. В Java это вложенные `if` или `try-catch` ад.
В Scala это выглядит как линейный код.

```scala
def findUser(id: Int): Option[User] = ...
def getProfile(user: User): Option[Profile] = ...
def getEmail(profile: Profile): Option[String] = ...

// Выглядит как императивный код, но это монадическая цепочка flatMap-ов
val email: Option[String] = for
  user    <- findUser(1)          // Если None -> стоп и возврат None
  profile <- getProfile(user)     // Если None -> стоп и возврат None
  email   <- getEmail(profile)    // Если None -> стоп и возврат None
yield
  email.toLowerCase

// Если хоть один шаг вернул None, результатом всего выражения будет None.
// Если все вернули Some, мы дойдем до yield.
```

-----

### Итог раздела 3.4

1.  **`Option`**: "Может быть, а может и нет". Замена `null`.
2.  **`Try`**: "Попробовать выполнить, перехватить Exception". Замена `try-catch` для интеропа.
3.  **`Either`**: "Или ошибка A, или результат B". Основа для обработки бизнес-ошибок без исключений.
4.  **Railway Oriented Programming**: Ошибки — это часть нормального потока данных, которые мы обрабатываем через `map`/`flatMap` или `for`, не прерывая стек вызовов.

-----

# Часть 4: Контекстные Абстракции (Implicits 2.0)

Суть концепции: есть параметры, которые мы не хотим передавать явно в каждой функции (конфигурации, транзакции, контекст выполнения, способы сортировки). Мы хотим, чтобы компилятор сам нашел подходящее значение в области видимости и подставил его.

### 4.1. Given и Using (DI на уровне компилятора)

#### 1\. `given` (Провайдер)

Ключевое слово `given` определяет значение, которое будет использоваться как "каноническое" для данного типа в текущей области видимости.

```scala
// Мы говорим: "Если кто-то попросит String в контексте, дай ему это"
given defaultGreeting: String = "Hello, Context!"

// Имя 'defaultGreeting' можно опустить, если оно не нужно явно
given Int = 10 
```

#### 2\. `using` (Потребитель)

Ключевое слово `using` в списке параметров функции означает: "Не заставляй пользователя писать этот аргумент. Найди подходящий `given` сам".

```scala
// Функция требует, чтобы в контексте была строка
def greet(name: String)(using greeting: String): Unit =
  println(s"$greeting, $name")

// Использование:

// Вариант А (Магия): 
// Компилятор видит 'given defaultGreeting' выше и подставляет его скрыто.
greet("Bob") // Выведет: Hello, Context!, Bob

// Вариант Б (Явная передача):
// Можно передать аргумент явно, перекрыв неявный поиск.
greet("Alice")(using "Hi") // Выведет: Hi, Alice
```

**Аналогия с Spring:**

  * `given` — это `@Bean`.
  * `using` — это `@Autowired` в конструкторе.
  * Разница: Spring падает в Runtime, если бин не найден. Scala не скомпилируется (Compile-time safety).

-----

### 4.2. Extension Methods (Методы расширения)

В Java вы создаете `StringUtils` со статическими методами (`StringUtils.capitalize(str)`).
В Scala вы добавляете метод прямо в класс `String`, даже если он финальный и чужой (из JDK).

```scala
// Расширяем класс String
extension (s: String)
  def isEmail: Boolean = s.contains("@")
  def makePolite: String = s + ", please"

// Использование
val mail = "bob@test.com"

// Выглядит как родной метод!
if mail.isEmail then
  println(mail.makePolite) 
```

*Под капотом это превращается в статический вызов, но синтаксически это ООП.*

-----

### 4.3. Паттерн Type Class (Тайпклассы)

Это "Святой Грааль" Scala-разработки. Тайпклассы объединяют `given` и `extension methods`, чтобы реализовать **Ad-hoc полиморфизм**.

**Проблема:** У нас есть интерфейс `JsonSerializer`. Мы хотим научить стандартный `java.time.Instant` сериализоваться в JSON.

  * **Java (Наследование):** Невозможно. Мы не можем заставить `Instant` имплементировать наш интерфейс `JsonSerializer`, так как класс `Instant` написан не нами. Мы вынуждены писать адаптеры.
  * **Scala (Type Class):** Мы создаем реализацию *отдельно* от класса и подаем её через контекст.

[]

```scala
// 1. Сам Тайпкласс (Интерфейс поведения)
trait JsonSerializer[T]:
  def toJson(value: T): String

// 2. Extension method, который "активирует" поведение для любого T,
// для которого найдется JsonSerializer
extension [T](value: T)(using serializer: JsonSerializer[T])
  def toJson: String = serializer.toJson(value)

// 3. Реализация для Int (Given instance)
given JsonSerializer[Int] with
  def toJson(value: Int): String = value.toString

// 4. Реализация для String
given JsonSerializer[String] with
  def toJson(value: String): String = s"\"$value\""

// 5. Реализация для нашего класса User
case class User(name: String)
given JsonSerializer[User] with
  def toJson(u: User): String = s"{name: ${u.name}}"

// ИСПОЛЬЗОВАНИЕ:
val i = 123
val s = "hello"
val u = User("Bob")

// Магия: метод toJson появился у всех этих типов!
println(i.toJson) 
println(s.toJson)
println(u.toJson)

// println(true.toJson) // ОШИБКА КОМПИЛЯЦИИ: No given instance of JsonSerializer[Boolean] was found.
```

Это позволяет добавлять поведение к классам **без изменения их исходного кода**.

**Context Bounds (Синтаксический сахар):**
Вместо `(using serializer: JsonSerializer[T])` можно писать короче:

```scala
// T : JsonSerializer означает "Для типа T должен существовать given JsonSerializer"
def printJson[T : JsonSerializer](obj: T): Unit =
  println(obj.toJson)
```

-----

### 4.4. Scala 2 Legacy (Словарь переводчика)

Вы будете встречать старый код. Вот как переводить понятия:

| Концепция         | Scala 2 (Old)                           | Scala 3 (New)                   |
| :---------------- | :-------------------------------------- | :------------------------------ |
| Неявное значение  | `implicit val x = ...`                  | `given x: T = ...`              |
| Неявный параметр  | `(implicit x: T)`                       | `(using x: T)`                  |
| Метод расширения  | `implicit class Ops(x: T) { ... }`      | `extension (x: T) ...`          |
| Конвертация типов | `implicit def strToInt(s: String): Int` | `given Conversion[String, Int]` |

**Совет:** Если видите слово `implicit` в новом коде Scala 3 — скорее всего, автор делает что-то не так или использует устаревшие практики (кроме редких продвинутых случаев).

-----

### Итог раздела 4

1.  **Given/Using** — это механизм автоматической передачи параметров (контекста) компилятором.
2.  **Extension Methods** позволяют добавлять методы (`.toJson`, `.toDatabase`) в чужие классы.
3.  **Type Classes** — это способ реализации интерфейсов "снаружи" класса. Это гибче, чем наследование.
4.  **Безопасность:** Если компилятор не найдет нужный `given`, он выдаст ошибку сборки, а не Runtime Exception (как в Spring).

-----

# Часть 5: Продвинутая система типов (Application Level)

## 5.1. Дженерики и Вариантность

Синтаксис дженериков прост: квадратные скобки `[T]` вместо угловых `<T>`.
Но магия кроется в значках `+` и `-` перед именем типа.

### 1\. Инвариантность (`[T]`) — Как в Java

Если вы пишете `class Box[T]`, то `Box[String]` и `Box[Any]` — это **совершенно разные, несовместимые типы**. Вы не можете присвоить один другому.

Это поведение по умолчанию для **мутабельных** структур (как `Array` или `var` внутри класса), чтобы предотвратить ошибки.

```scala
class Box[T](var value: T)

val stringBox = new Box[String]("Hello")
// val anyBox: Box[Any] = stringBox 
// ОШИБКА КОМПИЛЯЦИИ! 
// Если бы это разрешили, мы могли бы сделать: anyBox.value = 123
// И тогда stringBox (который думает, что там String) сломался бы при чтении.
```

### 2\. Ковариантность (`[+T]`) — Producer

Мы говорим: "Если `String` является подтипом `Any`, то и `List[String]` является подтипом `List[Any]`".

Это безопасно только если мы **только читаем** данные (Producer) и никогда их не меняем.
Именно поэтому `List`, `Vector`, `Option` в Scala ковариантны.

  * **Синтаксис:** `+` означает "следует за иерархией типа".
  * **Java аналог:** `? extends T` (Use-site variance), но в Scala это задается один раз при объявлении класса (Declaration-site variance).

<!-- end list -->

```scala
// Immutable контейнер (только чтение)
class Container[+T](val element: T)

val strings: Container[String] = new Container("Test")
val objects: Container[Any] = strings // РАБОТАЕТ!

// Это позволяет писать универсальные методы:
def printAnything(c: Container[Any]) = println(c.element)

printAnything(strings) // Работает, так как Container[String] <: Container[Any]
```

### 3\. Контравариантность (`[-T]`) — Consumer

Это взрывает мозг новичкам, но это логично.
Если у вас есть `Printer[Any]` (умеет печатать всё), можете ли вы использовать его там, где нужен `Printer[String]`?
**Да\!** Если он печатает *всё*, то строку он точно напечатает.

Отношение инвертируется: `Printer[Any]` является **подтипом** `Printer[String]`.

  * **Синтаксис:** `-` означает "идет против иерархии типа".
  * **Где используется:** Обработчики событий, сериализаторы, функции. `Function1[-A, +B]` — функция контравариантна по аргументу и ковариантна по результату.

<!-- end list -->

```scala
trait Printer[-T]:
  def print(item: T): Unit

val anyPrinter: Printer[Any] = item => println(item)
val stringPrinter: Printer[String] = anyPrinter // РАБОТАЕТ!

// Почему это важно?
// Если библиотека просит "дай мне принтер для строк", 
// я могу дать ей свой мощный принтер для всего.
```

-----

## 5.2. Алгебра типов (Scala 3)

В Scala 3 типы можно складывать и умножать. Это делает моделирование данных невероятно точным.

### 1\. Union Types (Объединение `|`) — "ИЛИ"

Переменная может содержать значение типа A **или** типа B.
Это не то же самое, что `Either`. `Either` — это *класс-обертка* (`Left/Right`). `Union Type` — это *тип самой ссылки*, без оберток (Erasure type).

**Кейс:** Работа с API, которое возвращает "Строку или Число".

```scala
// Тип id может быть String ИЛИ Int
def lookup(id: String | Int): Unit = 
  // Компилятор заставляет обработать оба варианта
  id match
    case s: String => println(s"Searching by name: $s")
    case i: Int    => println(s"Searching by ID: $i")

lookup("Bob") // OK
lookup(123)   // OK
// lookup(true) // Ошибка компиляции
```

Также это замена `Checked Exceptions` в сигнатуре:

```scala
// Метод возвращает User или Error (без Either)
def getUser(id: Int): User | String =
  if id > 0 then User(id) else "Not Found"
```

### 2\. Intersection Types (Пересечение `&`) — "И"

Тип, который объединяет контракты нескольких типов одновременно.

```scala
trait Resetable:
  def reset(): Unit

trait Closeable:
  def close(): Unit

// service должен реализовывать И Resetable, И Closeable
def shutdown(service: Resetable & Closeable): Unit =
  service.reset()
  service.close()
```

-----

## 5.3. Opaque Type Aliases (Zero-cost Wrappers)

**Проблема:** В Java/Scala мы часто используем примитивы для идентификаторов (`userId: String`, `orderId: String`). Легко перепутать аргументы местами.
Создавать класс-обертку `case class UserId(id: String)` — это накладно по памяти (создается объект в куче).

**Решение Scala 3:** `opaque type`.
Это **новый тип** для компилятора, но **обычный примитив** в Runtime (в байт-коде). Абсолютный zero-overhead.

```scala
object Domain:
  // 1. Объявляем типы
  opaque type UserID = Int
  opaque type OrderID = Int

  // 2. Методы для создания (валидации) и извлечения
  // Внутри этого объекта UserID и Int — это одно и то же.
  object UserID:
    def apply(id: Int): UserID = id // Можно добавить проверки (id > 0)
    
    // Extension method, чтобы достать значение обратно
    extension (u: UserID) def toInt: Int = u 

import Domain._

val uid: UserID = UserID(100)
val oid: OrderID = ??? // OrderID(100) - представим, что есть такой метод

// val x: Int = uid      // ОШИБКА: UserID != Int (снаружи модуля)
// val test: UserID = oid // ОШИБКА: UserID != OrderID (хотя внутри оба Int)

// В рантайме это просто: int u = 100;
```

Это идеальный способ делать **Value Objects** (DDD) с производительностью примитивов.

-----

### Итог раздела 5

1.  **Covariance (`+T`):** `List[String]` -\> `List[Any]`. Для неизменяемых данных (Output).
2.  **Contravariance (`-T`):** `Printer[Any]` -\> `Printer[String]`. Для потребителей данных (Input).
3.  **Union Types (`A | B`):** Гибкость типов без оберток `Either`.
4.  **Opaque Types:** Безопасность типов без нагрузки на GC.

-----

-----

# Часть 6: Метапрограммирование (Light)

## 6.1. Inline (Встраивание кода)

Ключевое слово `inline` — это приказ компилятору: "Не вызывай этот метод, а скопируй его тело прямо в место вызова". Это похоже на макросы в C++, но полностью типобезопасно.

### 1\. Оптимизация производительности

Представьте логирование. В Java мы часто пишем проверки `if (log.isDebugEnabled())`, чтобы не тратить ресурсы на склейку строк, если логи выключены.

В Scala 3 это делается прозрачно:

```scala
// Обычный метод (вызов метода, создание стека, вычисление аргументов)
def logNormal(msg: String): Unit = println(msg)

// Inline метод
inline def logFast(msg: String): Unit = 
  // Представим, что это константа времени компиляции или глобальный флаг
  val debugEnabled = false 
  
  if debugEnabled then println(msg)

// Использование:
val user = "Alice"
logFast(s"User: $user") 

// ЧТО СДЕЛАЕТ КОМПИЛЯТОР:
// 1. Он подставит тело метода.
// 2. Он увидит: if (false) println(...)
// 3. Он выполнит Dead Code Elimination.
// 4. В байт-коде НЕ БУДЕТ НИЧЕГО. Ни склейки строк, ни вызова метода. Zero overhead.
```

### 2\. `inline` параметры

Можно инлайнить не только методы, но и передаваемые функции (лямбды), чтобы избежать создания объектов `FunctionN` в куче. Это критично для горячих циклов.

```scala
// Стандартные методы типа foreach уже используют inline
inline def repeat(n: Int)(f: => Unit): Unit =
  var i = 0
  while i < n do
    f
    i += 1

// Вызов:
repeat(5) { println("Hi") }
// В байт-коде это развернется в обычный цикл while, без создания объектов-лямбд.
```

### 3\. Compile-time Logic (`inline match`)

Вы можете писать код, который вычисляется и сворачивается во время компиляции.

```scala
import scala.compiletime.ops.int._

inline def power(n: Int, k: Int): Int =
  inline if k == 0 then 1
  else inline if k == 1 then n
  else n * power(n, k - 1)

// Использование
val x = power(10, 3)
// Компилятор видит константы 10 и 3.
// Он развернет рекурсию ПРИ КОМПИЛЯЦИИ: 10 * 10 * 10
// В байт-коде будет просто число 1000.
```

-----

## 6.2. Type Class Derivation (Автогенерация кода)

Самая частая рутина в Java — написание мапперов, сериализаторов, `equals/hashCode` (если без ломбока).
В Scala 3 вы можете попросить компилятор: "Сгенерируй мне реализацию этого интерфейса для моего класса, основываясь на его структуре".

Ключевое слово: **`derives`**.

### Пример: JSON Encoder

Допустим, у нас есть библиотека (типа Circe или Zio-Json), которая определяет тайпкласс `JsonEncoder`.

```scala
// 1. Наша модель данных
// Нам не нужно вешать аннотации на каждое поле.
// Мы просто говорим: "Этот класс умеет кодироваться в JsonEncoder"
case class User(name: String, age: Int) derives JsonEncoder

// 2. Использование
val u = User("Bob", 42)
println(u.toJson) // {"name":"Bob", "age":42}
```

### Как это работает?

Когда вы пишете `derives JsonEncoder`, компилятор ищет метод `derived` в объекте-компаньоне трейта `JsonEncoder`.
Этот метод `derived` использует продвинутые механизмы Scala 3 (Mirror), чтобы проитерировать поля вашего кейс-класса и сгенерировать код сериализации для каждого поля.

**Вы можете писать свои деривации.** Это сложнее (Library Author level), но использование — элементарно.

**Пример встроенной деривации:**
В стандартной библиотеке есть тайпкласс `CanEqual`. В Scala 3 есть режим "Strict Equality" (строгое равенство), запрещающий сравнивать `Int == String` (что в Java/Scala 2 всегда `false`, но компилируется).

```scala
import scala.language.strictEquality

// Мы явно говорим, что Point можно сравнивать (генерируется инстанс CanEqual)
case class Point(x: Int, y: Int) derives CanEqual

val p = Point(1, 2)
p == Point(1, 2) // OK

// p == "String" // ОШИБКА КОМПИЛЯЦИИ! 
// Компилятор не нашел доказательства, что Point можно сравнивать со String.
```

-----

### Итог раздела 6

1.  **`inline`**: Инструмент оптимизации. Позволяет убирать абстракции во время компиляции, получая производительность рукописного спагетти-кода при сохранении чистоты архитектуры.
2.  **`derives`**: Замена Java Annotation Processors. Позволяет одной фразой сгенерировать сложную логику (JSON, XML, DB Mappers) для ваших классов данных.

-----

# Часть 7: Инфраструктура и Сборка (sbt)

## 7.1. Структура проекта

Хорошая новость: структура папок идентична Maven/Gradle. sbt следует соглашению "Convention over Configuration".

  * **`build.sbt`**: Главный файл описания сборки (в корне).
  * **`project/build.properties`**: Указывает версию самого sbt (критично для воспроизводимости).
  * **`project/plugins.sbt`**: Здесь подключаются плагины (для создания fat-jar, Docker-образов, линтеров).
  * **`src/main/scala`**: Здесь лежит ваш Scala код.
  * **`src/main/java`**: Здесь может лежать Java код (sbt умеет компилировать оба языка вместе).

## 7.2. Файл `build.sbt`

В Maven у вас есть теги. В sbt у вас есть **настройки (Settings)**.
`build.sbt` — это список выражений, разделенных пустыми строками.

Оператор присваивания — **`:=`**.

```scala
val scala3Version = "3.3.1"

lazy val root = project
  .in(file("."))
  .settings(
    // Метаданные
    name := "my-scala-app",
    version := "0.1.0-SNAPSHOT",
    
    // Версия компилятора (обязательно)
    scalaVersion := scala3Version,
    
    // Опции компилятора (флаги javac/scalac)
    scalacOptions ++= Seq(
      "-deprecation", // Предупреждать об устаревшем коде
      "-encoding", "UTF-8"
    ),

    // Основная библиотека зависимостей
    libraryDependencies += "org.scalameta" %% "munit" % "0.7.29" % Test
  )
```

## 7.3. Управление зависимостями (`%` vs `%%`)

Это **самый важный момент** для Java-разработчика.

В Java бинарная совместимость гарантируется годами. В Scala **бинарная совместимость ломается между мажорными версиями** (2.12 не совместима с 2.13). Библиотеки компилируются под конкретную версию языка.

#### Синтаксис:

  * `groupID % artifactID % version` — для **Java** библиотек (Spring, Commons, Jackson).
  * `groupID %% artifactID % version` — для **Scala** библиотек.

**Магия `%%`:**
Когда вы пишете двойной процент `%%`, sbt автоматически добавляет версию Scala к имени артефакта.

```scala
// 1. Обычная Java либа
// Скачает: commons-lang3-3.12.0.jar
libraryDependencies += "org.apache.commons" % "commons-lang3" % "3.12.0"

// 2. Scala либа (используем %%)
// Если scalaVersion := "3.3.1", то sbt скачает:
// cats-core_3-2.9.0.jar (обратите внимание на суффикс _3)
libraryDependencies += "org.typelevel" %% "cats-core" % "2.9.0"
```

> **Совет:** Всегда используйте `%%` для Scala-библиотек. Если вы напишете `%`, вы получите `ClassNotFoundException` в Runtime, так как скачается не тот артефакт или он вообще не найдется.

## 7.4. Основные команды (Interactive Mode)

В отличие от Maven, sbt запускается долго (грузит JVM, компилятор), но потом работает **очень быстро**. Поэтому sbt обычно запускают в интерактивном режиме.

Запустите `sbt` в терминале, и вы попадете в консоль sbt:

1.  **`compile`**: Инкрементальная компиляция (умная: пересобирает только измененные файлы и их зависимости).
2.  **`run`**: Запуск `main` класса.
3.  **`test`**: Запуск тестов.
4.  **`console`**: **Scala REPL (Read-Eval-Print-Loop)**. Это киллер-фича.
      * Запускает интерактивную консоль Scala, в которую **уже загружены все классы вашего проекта и все зависимости**.
      * Идеально для экспериментов с кодом ("А что вернет этот метод?") без написания `public static void main`.
5.  **`reload`**: Перезагрузить `build.sbt` без перезапуска sbt.

#### Continuous Execution (`~`)

Добавьте тильду `~` перед любой командой. sbt перейдет в режим наблюдения за файлами.

  * `~compile`: Автоматически компилирует при каждом сохранении файла (Ctrl+S).
  * `~test`: Прогоняет тесты при каждом изменении.

-----

### Итог раздела 7

1.  **`build.sbt`** — это скрипт конфигурации на Scala.
2.  **`%%`** используется для Scala-зависимостей (добавляет суффикс версии языка).
3.  **`sbt console`** — лучший друг разработчика для отладки и экспериментов.
4.  **`~compile`** — режим "watch" для мгновенной обратной связи.

-----

# Часть 8: Продвинутый Java Interop

## Совместная жизнь с Java

Scala создавалась так, чтобы иметь **бесшовную совместимость** с Java. Вы можете использовать *любую* Java-библиотеку (Spring, Hibernate, Kafka Client) прямо в Scala коде. Однако, поскольку у Scala свои коллекции и система типов, есть места "стыковки", о которых нужно знать.

### 8.1. Конвертация коллекций

Это сценарий №1. У вас есть Java-метод, возвращающий `java.util.List<String>`, а вы хотите работать с ним как с `scala.List[String]` (делать `map`, `filter` и т.д.).

В Scala 2.13+ и Scala 3 стандартный способ — это объект **`scala.jdk.CollectionConverters`**.

```scala
import java.util.{ArrayList, HashMap}
// Магический импорт, добавляющий extension methods .asJava и .asScala
import scala.jdk.CollectionConverters._ 

// --- SCENARIO 1: Java -> Scala ---
val javaList = new ArrayList[String]()
javaList.add("A")
javaList.add("B")

// Превращаем в Scala Buffer (мутабельный список, обертка над Java List)
val scalaBuffer = javaList.asScala 

// Превращаем в иммутабельный Scala List (копирование данных!)
val scalaList = javaList.asScala.toList 

// Теперь доступен весь FP API
scalaList.map(_.toLowerCase)


// --- SCENARIO 2: Scala -> Java ---
val scalaMap = Map("key" -> "value")

// Превращаем в java.util.Map (для передачи в Java API)
val javaMap = scalaMap.asJava

// ВНИМАНИЕ:
// scalaMap.asJava возвращает "View" (представление).
// Если Java-код попытается сделать javaMap.put(..), упадет UnsupportedOperationException,
// потому что исходная Scala Map иммутабельна.
// Если нужен изменяемый Java Map, копируйте: new HashMap(scalaMap.asJava)
```

### 8.2. Лямбды и SAM (Functional Interfaces)

Java 8 ввела SAM (Single Abstract Method) интерфейсы (`Runnable`, `Callable`, `Predicate`).
Scala 3 поддерживает **автоматическую конвертацию** лямбд Scala в SAM интерфейсы Java.

```scala
// Java метод: public void executeTask(Runnable r) { ... }
def executeTask(r: Runnable): Unit = r.run()

// Scala вызов:
// Компилятор видит, что executeTask ждет Runnable (метод run: () -> void).
// Он автоматически превращает лямбду () => ... в Runnable.
executeTask(() => println("Running!"))
```

**Нюанс с `Void` vs `Unit`:**
В Java `Void` — это тип (хоть и странный), а `void` — отсутствие типа.
В Scala `Unit` — это реальный объект `()`.

Обычно это прозрачно, но иногда при работе с дженериками (например `CompletableFuture<Void>`) приходится писать:

```scala
import java.util.concurrent.CompletableFuture

// Java ждет Void? Возвращаем null (так как Void instace - это null в Java мире)
val future: CompletableFuture[Void] = 
  CompletableFuture.supplyAsync(() => {
    println("Async work")
    null: Void 
  })
```

### 8.3. Checked Exceptions

В Scala **нет проверяемых исключений** (Checked Exceptions).
Компилятор Scala не заставит вас писать `try-catch` или `throws`, даже если вы вызываете Java-метод, который объявляет `throws IOException`.

Это палка о двух концах: код чище, но можно забыть обработать ошибку.

**Как с этим жить (Best Practice):**
Используйте `Try` для оборачивания вызовов Java-методов.

```scala
import java.io.{File, FileInputStream}
import scala.util.Try

val file = new File("config.txt")

// Java заставила бы писать try-catch здесь. Scala - нет.
// Опасно: это может упасть в Runtime.
// val stream = new FileInputStream(file) 

// Безопасно: Оборачиваем в Try
val maybeStream = Try(new FileInputStream(file))

maybeStream match
  case scala.util.Success(stream) => /* работаем */
  case scala.util.Failure(ex)     => println("File not found")
```

**Аннотация `@throws` (Scala -\> Java):**
Если вы пишете библиотеку на Scala, которую будут вызывать из Java, и вы хотите, чтобы Java-компилятор заставлял пользователя ловить исключение, используйте аннотацию.

```scala
import java.io.IOException

class ScalaReader:
  @throws[IOException] // Добавляет 'throws IOException' в байт-код
  def read(): String = throw new IOException("Boom")
```

### 8.4. Ключевые слова (Escaping)

Иногда в Java методах используются имена, которые являются ключевыми словами в Scala (например, `match`, `type`, `val`, `object`).
Используйте обратные кавычки **`     `**, чтобы вызвать их.

```scala
// Java: public String type() { return "JSON"; }
val obj = new JavaClass()

// obj.type() // Ошибка парсера Scala
val t = obj.`type`() // OK
```

### 8.5. Опция `Explicit Nulls` (Scala 3)

По умолчанию `String` из Java приходит в Scala как `String!` (платформенный тип), который Scala трактует как просто `String`. Но там может быть `null`.

В Scala 3 можно включить опцию компилятора `-Yexplicit-nulls`.
Тогда все ссылочные типы из Java будут видеться как `String | Null`. Вам придется явно проверять их или "кастовать".

```scala
// С включенной опцией explicit-nulls:
val javaStr: String | Null = System.getProperty("foo")

// javaStr.length // Ошибка: может быть null

// Решение:
if javaStr != null then javaStr.length
// Или extension method .nn (not null cast)
javaStr.nn.length 
```

*Примечание: Эту опцию пока используют редко из-за сложности миграции, но знать о ней полезно.*

-----

### Итог раздела 8

1.  **Коллекции:** Используйте `.asScala` для чтения и `.asJava` для передачи в Java.
2.  **Лямбды:** Scala-функции автоматически конвертируются в Java Functional Interfaces.
3.  **Exceptions:** Scala игнорирует Checked Exceptions. Оборачивайте небезопасный Java-код в `Try`.
4.  **Keywords:** Если имя метода совпадает с ключевым словом Scala, используйте кавычки: `` `method` ``.

-----

# Часть 9: Магия Типов (Library Author Level)

## 9.1. Типы высшего порядка (Higher-Kinded Types — HKT)

### Проблема

Представьте, вы хотите написать метод `transform`, который берет контейнер с данными, функцию и применяет её.

  * Если вы передали `List[Int]`, вы хотите получить `List[String]`.
  * Если вы передали `Option[Int]`, вы хотите получить `Option[String]`.

В Java вы не можете написать один метод для этого. Вам придется писать перегрузки для `List`, `Set`, `Optional`. Вы не можете сказать `C<T>`, где `C` — это переменная типа контейнера.

### Решение: `F[_]`

В Scala вы можете принять "Конструктор типа" как параметр.

  * **`Int`**: Это Тип (Kind: `*`). У него есть значения (1, 2).
  * **`List`**: Это **не** Тип. Это Конструктор типа (Kind: `* -> *`). У него нет значений. Значения есть у `List[Int]`.
  * **`Map`**: Это Конструктор с двумя дырками (Kind: `* -> * -> *`).

<!-- end list -->

```scala
// Интерфейс, описывающий "Нечто, что можно маппить" (Functor)
// F[_] означает: F - это не просто тип, это "контейнер", требующий одного аргумента.
trait Mappable[F[_]]:
  def map[A, B](fa: F[A])(f: A => B): F[B]

// Реализация для List
given Mappable[List] with
  def map[A, B](list: List[A])(f: A => B): List[B] = list.map(f)

// Реализация для Option
given Mappable[Option] with
  def map[A, B](opt: Option[A])(f: A => B): Option[B] = opt.map(f)

// УНИВЕРСАЛЬНЫЙ МЕТОД
// Работает и с List, и с Option, и с Future (если есть given)
def transform[F[_] : Mappable, A, B](container: F[A], func: A => B): F[B] =
  // summon достает given Mappable из контекста
  summon[Mappable[F]].map(container)(func)

// Тест
val l = transform(List(1, 2, 3), x => s"Num $x") // List("Num 1", ...)
val o = transform(Option(1), x => s"Num $x")    // Some("Num 1")
```

**Зачем это вам?**
Когда вы начнете использовать библиотеки функционального программирования (например, **Cats Effect** или **ZIO**), вы увидите сигнатуры типа:
`def logic[F[_]: Monad](request: Request): F[Response]`

Это значит: "Моя бизнес-логика не привязана к конкретному движку. Запусти меня хоть на `IO` (асинхронно), хоть на `Id` (синхронно для тестов), хоть на `Future`".

-----

## 9.2. Match Types (Типы как функции) — Scala 3

В Scala 3 вы можете писать логику `if-else` или `switch` **на уровне типов**. Это позволяет вычислять возвращаемый тип метода в зависимости от входного типа на этапе компиляции.

Представьте функцию, которая достает первый элемент.

  * Если вход `String` -\> результат `Char`.
  * Если вход `Array[T]` -\> результат `T`.
  * Иначе -\> результат `Any`.

<!-- end list -->

```scala
// Определение Match Type
type Elem[X] = X match
  case String   => Char
  case Array[t] => t
  case Iterable[t] => t
  case _        => Any

// Использование в методе
// Компилятор знает, что если x - String, то возврат - Char
def firstElement[X](x: X): Elem[X] = x match
  case s: String      => s.charAt(0)      // Elem[String] = Char
  case a: Array[_]    => a(0)             // Elem[Array[T]] = T
  case i: Iterable[_] => i.head           // Elem[Iterable[T]] = T
  case _              => x                // Fallback

val c: Char = firstElement("Hello") // Тип вывелся как Char!
val i: Int  = firstElement(Array(1, 2, 3)) // Тип вывелся как Int!
```

-----

## 9.3. Dependent Object Types (Зависимые типы)

Это концепция, где **тип зависит от значения переменной**.
В Java `Inner` класс привязан к `Outer` классу, но не к его инстансу. В Scala `v1.Inner` и `v2.Inner` — это **разные** типы.

Это используется для создания строгих доказательств в коде.

```scala
trait Dep:
  type V
  val value: V

def magic(d: Dep): d.V = d.value // Возвращаемый тип зависит от аргумента d!

// Реализация 1
val intDep = new Dep:
  type V = Int
  val value = 10

// Реализация 2
val strDep = new Dep:
  type V = String
  val value = "Scala"

val i: Int = magic(intDep)    // Компилятор знает, что тут Int
val s: String = magic(strDep) // Компилятор знает, что тут String
```

-----

# Часть 10: Многопоточность и Асинхронность

В Java мы привыкли к `Thread`, `ExecutorService` и `synchronized`.
В Scala:

  * **Никаких `synchronized`**: Мы избегаем изменяемого состояния (Shared Mutable State), поэтому блокировки не нужны.
  * **Никаких явных `Thread`**: Мы работаем с абстракциями (`Future`, `Task`, `IO`).
  * **Принцип:** "Не общайтесь, разделяя память; разделяйте память, общаясь" (Do not communicate by sharing memory; share memory by communicating).

## 10.1. Futures (Стандартная библиотека)

`scala.concurrent.Future` — это контейнер для значения, которое *появится* в будущем.

  * **Аналог Java:** `CompletableFuture` (но `Future` в Scala запускается сразу при создании, он "Eager").
  * **ExecutionContext:** Аналог `ExecutorService`. Движок, на котором выполняются потоки. Без него ничего не заработает.

### 1\. Создание и ExecutionContext

```scala
import scala.concurrent.{Future, Await, Promise}
import scala.concurrent.duration._
import scala.util.{Success, Failure}

// 1. Нам нужен пулл потоков.
// global — это ForkJoinPool по умолчанию (аналог commonPool в Java)
import scala.concurrent.ExecutionContext.Implicits.global

// 2. Запуск вычисления (сразу летит в тредпул)
val fastFuture: Future[String] = Future {
  // Тело выполняется в другом потоке
  Thread.sleep(500)
  "Result from thread"
}
```

### 2\. Callbacks vs Combinators

В Java `CompletableFuture` вы используете `thenApply`, `thenCompose`.
В Scala вы используете те же `map`, `flatMap`, что и для коллекций\!

```scala
def getUser(id: Int): Future[String] = Future {
  Thread.sleep(100)
  "User" + id
}

def getEmail(user: String): Future[String] = Future {
  Thread.sleep(100)
  user + "@mail.com"
}

// ПЛОХО (Callback Hell - как в старом JS):
getUser(1).onComplete {
  case Success(user) => 
    getEmail(user).onComplete {
      case Success(email) => println(email)
      case Failure(e) => e.printStackTrace()
    }
  case Failure(e) => e.printStackTrace()
}

// ХОРОШО (For-Comprehension):
// Это выглядит как синхронный код, но работает асинхронно!
val resultFuture: Future[String] = for
  user  <- getUser(1)          // flatMap (ждем результат)
  email <- getEmail(user)      // map (используем результат)
yield
  s"Sent to $email"

// resultFuture содержит результат цепочки или первую ошибку.
```

### 3\. Блокировка (Await)

В конце мира (например, в `main` методе или в тестах) нам иногда нужно заблокировать поток и подождать результат.
В бизнес-логике это **анти-паттерн**.

```scala
// Аналог javaFuture.get()
val result = Await.result(resultFuture, 5.seconds)
println(result)
```

-----

## 10.2. Promises (Управление Future)

Если `Future` — это доступ "только для чтения" (интерфейс для потребителя), то `Promise` — это "рычаг управления" (интерфейс для продюсера). Вы можете создать `Promise`, отдать кому-то его `future`, а потом "закомплитить" его.

Полный аналог `CompletableFuture` (где можно вызвать `.complete()`).

```scala
val p = Promise[String]()
val f: Future[String] = p.future

// В другом потоке...
new Thread(() => {
  Thread.sleep(1000)
  p.success("Manual completion") // Или p.failure(exception)
}).start()
```

-----

## 10.3. Параллельные коллекции

Если у вас есть `List` и вы хотите обработать его элементы параллельно, используя все ядра CPU.
В Java: `list.parallelStream()`.
В Scala: `.par`.

*Требует отдельной зависимости:* `"org.scala-lang.modules" %% "scala-parallel-collections" % "1.0.4"`

```scala
import scala.collection.parallel.CollectionConverters._

val list = (1 to 1000000).toList

// Обработка в несколько потоков
val result = list.par.map(_ * 2).filter(_ % 3 == 0).seq // .seq возвращает обычный List
```

-----

## 10.4. Экосистема: ZIO и Cats Effect (The Real World)

Это то, ради чего многие переходят на Scala.
Стандартные `Future` имеют проблемы:

1.  **Eager:** Они запускаются сразу. Их нельзя "описать" и запустить потом.
2.  **Memoization:** Они запоминают результат. Нельзя перезапустить (retry) тот же объект `Future`.
3.  **Thread Blocking:** Под капотом это всё еще потоки ОС (до Java 21).

Современные библиотеки используют **Fibers** (Зеленые потоки / Виртуальные потоки).
Это супер-легковесные потоки (можно создать миллион), которые управляются Runtime-ом библиотеки, а не ОС.

### Пример на ZIO (Концептуально)

ZIO — это библиотека, где всё является описанием работы (Blueprint).

```scala
// ZIO[Environment, Error, Value]
// Это НЕ запущенный поток. Это описание: "Я могу напечатать строку".
val program: ZIO[Any, Nothing, Unit] = 
  ZIO.log("Hello") *> ZIO.sleep(1.second) *> ZIO.log("World")

// Запуск (обычно делается один раз в main)
// Runtime создаст Fibers и выполнит это.
Unsafe.unsafe { implicit unsafe =>
  Runtime.default.unsafe.run(program)
}
```

**Киллер-фичи ZIO/Cats:**

  * **Cancellation:** Если пользователь закрыл браузер, вся цепочка вычислений (DB, Http) отменяется мгновенно, освобождая ресурсы. С `Future` или Java Threads это сделать сложно (нужно проверять `isInterrupted`).
  * **Resource Management:** Аналог `try-with-resources`, но асинхронный и композируемый (`ZIO.acquireRelease`).
  * **Concurrency:** `race`, `zipPar` (параллельное выполнение).

<!-- end list -->

```scala
// Взять данные из двух источников параллельно и объединить
// Если один упадет — второй отменится автоматически.
val combined = userZIO.zipPar(ordersZIO)
```

-----

## 10.5. Модель Акторов (Akka / Pekko)

Другой подход к конкурентности — **Actor Model**.
Это не про "потоки", а про "объекты, которые общаются письмами".

  * **Actor:** Объект, у которого есть скрытое состояние (state) и почтовый ящик (mailbox).
  * **Message Passing:** Вы не вызываете методы актора. Вы посылаете ему сообщение `actor ! "Hello"`.
  * **No Locks:** Актор обрабатывает сообщения по одному (Single Threaded illusion). Внутри обработки сообщения вам не нужны `synchronized`, так как никто другой не может трогать стейт в этот момент.

> *Примечание:* После смены лицензии Akka, сообщество поддерживает Open Source форк — **Apache Pekko**.

-----

### Итог раздела 10

1.  **`Future`** — база. Используйте для простых асинхронных задач. Работает через коллбэки, но мы прячем их за `for-comprehension` (`flatMap`).
2.  **`ExecutionContext`** — обязательный неявный параметр для работы `Future` (это ваш Thread Pool).
3.  **Избегайте `Await`** — блокировка убивает смысл асинхронности.
4.  **ZIO / Cats Effect** — промышленный стандарт для сложной конкурентности. Используют "зеленые потоки" (Fibers) и позволяют писать асинхронный код как обычный императивный, с гарантированным управлением ресурсами.

-----

# Часть 11: Практические паттерны и Тестирование

## 11.1. Хвостовая рекурсия (`@tailrec`)

В FP мы редко используем `while`. Мы используем рекурсию. Но в Java рекурсия опасна переполнением стека.
Scala компилятор умеет оптимизировать **хвостовую рекурсию** (когда рекурсивный вызов — это *последнее* действие функции) в обычный цикл `while` на уровне байт-кода.

Аннотация `@tailrec` гарантирует, что оптимизация сработала. Если нет — будет ошибка компиляции.

```scala
import scala.annotation.tailrec

// Обычная рекурсия (ОПАСНО)
// 1 + sum(99) — последнее действие это сложение, а не вызов.
// Стек растет: 1 + (2 + (3 + ...))
def sumUnsafe(n: Int): Int = 
  if n == 0 then 0 else n + sumUnsafe(n - 1)

// Хвостовая рекурсия (БЕЗОПАСНО)
// Мы используем аккумулятор.
def sumSafe(n: Int): Int =
  @tailrec
  def loop(current: Int, acc: Int): Int =
    if current == 0 then acc
    else loop(current - 1, acc + current) // Вызов loop — это последнее действие!
  
  loop(n, 0)

// sumSafe(1000000) // Работает, не падает, память O(1)
```

-----

## 11.2. Управление ресурсами (`Using`)

В Java есть `try-with-resources`. В Scala 2.13+ / 3 появился объект `Using`.
Он работает с любым объектом, реализующим `AutoCloseable`.

```scala
import scala.util.{Using, Try}
import java.io.{BufferedReader, FileReader}

val lines: Try[List[String]] = Using(new BufferedReader(new FileReader("file.txt"))) { reader =>
  // Внутри этого блока ресурс открыт
  // Iterator.continually читает, пока не null
  Iterator.continually(reader.readLine()).takeWhile(_ != null).toList
}

// Когда блок заканчивается (или падает), reader.close() вызывается автоматически.
// Результат обернут в Try, так как закрытие или чтение может упасть.
```

-----

## 11.3. Равенство (`==` vs `eq`)

В Java:

  * `==` сравнивает ссылки (для объектов) или значения (для примитивов).
  * `.equals()` сравнивает содержимое.

В Scala:

  * **`==`** — это **всегда** сравнение содержимого\! (Оно вызывает `.equals()` под капотом, но безопасно для `null`).
  * **`eq`** — это сравнение ссылок (Reference Equality).

<!-- end list -->

```scala
val str1 = new String("Hello")
val str2 = new String("Hello")

// Java: str1 == str2 (false)
// Scala:
str1 == str2  // true (Сравнивает содержимое)
str1 eq str2  // false (Сравнивает ссылки)

// Null safety
val s: String = null
s == "test" // false (Не падает с NPE, Scala делает проверку null за вас)
```

-----

## 11.4. Делегирование (`export`) — Scala 3

В ООП мы часто используем композицию вместо наследования. Но это заставляет писать методы-делегаты.
Scala 3 вводит ключевое слово `export`, чтобы "пробросить" методы внутреннего объекта наружу.

```scala
class Printer:
  def print(s: String) = println(s)
  def scan() = println("Scanning...")

class OfficeMachine:
  val printer = new Printer()
  
  // Экспортируем метод print. Теперь у OfficeMachine есть метод print!
  // Метод scan не экспортируем.
  export printer.print

val machine = new OfficeMachine()
machine.print("Document") // Вызывает printer.print("Document")
```

-----

## 11.5. Тестирование (ScalaTest & Property-Based)

В Scala есть свои фреймворки. Самый популярный — **ScalaTest**.

### 1\. Стили тестов

ScalaTest поддерживает разные стили (BDD, TDD). Самый популярный — `AnyFlatSpec` или `AnyWordSpec`.

```scala
import org.scalatest.flatspec.AnyFlatSpec
import org.scalatest.matchers.should.Matchers

class CalculatorSpec extends AnyFlatSpec with Matchers {
  
  "A Calculator" should "add two numbers correctly" in {
    val calc = new Calculator()
    calc.add(2, 3) shouldBe 5 // DSL утверждений (Matchers)
  }
  
  it should "handle division by zero" in {
    assertThrows[ArithmeticException] {
      10 / 0
    }
  }
}
```

### 2\. Property-Based Testing (ScalaCheck)

Это то, чего обычно нет в Java. Вместо того чтобы писать `add(2, 3) == 5`, вы описываете **свойства**.
Фреймворк сам генерирует 100 случайных тестов, пытаясь сломать ваш код (например, подсовывая `0`, `Int.MaxValue`, `-1`).

```scala
import org.scalatestplus.scalacheck.ScalaCheckPropertyChecks
import org.scalatest.flatspec.AnyFlatSpec

class StringSpec extends AnyFlatSpec with ScalaCheckPropertyChecks {
  
  "Concatenation" should "always be longer than inputs" in {
    // forAll генерирует случайные строки a и b
    forAll { (a: String, b: String) =>
      (a + b).length should be >= a.length
      (a + b).length should be >= b.length
    }
  }
}
```

-----