# Шпаргалка по F# для разработчика C#

## Оглавление
1. Введение: зачем F# и чем он отличается от C#
2. Функциональное мышление: ключевые принципы
3. Синтаксис и базовые конструкции
4. Типы и типовая система
5. Функции: определение, частичное применение, каррирование
6. Иммутабельность и работа с состоянием
7. Коллекции и функции высшего порядка
8. Модули, пространства имён и организация кода
9. Управление потоком: сопоставление с образцом (pattern matching)
10. Вариативные типы: Option, Result, Discriminated Unions
11. Записи, структуры и классы (Record, Struct, Class)
12. Активные шаблоны (Active Patterns)
13. Пайпинг и композиция функций
14. Вычисляемые выражения (Computation Expressions)
15. Асинхронность и параллелизм
16. Работа с побочными эффектами
17. Мутируемые структуры: когда и как
18. Межъязыковая совместимость с C#
19. Тестирование и свойствное тестирование
20. Практические идиомы F#
21. Инструменты и экосистема
22. Cheat Sheet: быстрый справочник по синтаксису и паттернам

---

## 1) Введение: зачем F# и чем он отличается от C#
- F# — это функционально-первый язык на .NET. Он поддерживает ООП и императивный стиль, но поощряет функциональные практики.
- Основные плюсы:
  - Меньше кода для выражения сложной логики.
  - Безопасность типов и иммутабельность по умолчанию.
  - Сопоставление с образцом и варианты типов сильно упрощают обработку состояний.
  - Прекрасно подходит для доменного моделирования, трансформаций данных, конвейеров, DSL, научных вычислений.
- Отличия от C#:
  - Код выражение-ориентирован: почти всё — выражения, возвращающие значения.
  - Иммутабельность по умолчанию; переменные — это значения.
  - Сопоставление с образцом вместо каскадов if/else/switch.
  - DU (Discriminated Unions) вместо наследования для моделирования вариантов.
  - Более лаконичный синтаксис, меньше «шумов» (скобок, точек с запятой).

---

## 2) Функциональное мышление: ключевые принципы
- Иммутабельность: данные не меняются, создаются новые версии.
- Чистые функции: результаты зависят только от входов, без побочных эффектов.
- Функции как значения: передача функций, возвращение функций, композиция.
- Декларативность: описываем «что» делать, а не «как».
- Типы как спецификация домена: DU и Record позволяют формализовать бизнес-правила на уровне типов.

---

## 3) Синтаксис и базовые конструкции

### Объявление значения
- Значения по умолчанию неизменяемые.

````markdown
let x = 42          // Иммутабельное значение
let y = x + 1       // Новое значение на основе x
````

### Функции

````markdown
let add a b = a + b // Функция add принимает два аргумента
let inc = add 1     // Частичное применение: inc b = add 1 b
inc 10              // => 11
````

- Нет скобок вокруг аргументов по умолчанию; пробел — оператор применения.
- Возвращаемое значение — последнее выражение в теле функции.

### Блоки и область видимости

````markdown
let result =
  let a = 10
  let b = 5
  a * b             // Последнее выражение — возвращаемое
````

### if/then/else — выражение

````markdown
let abs x =
  if x >= 0 then x
  else -x
````

---

## 4) Типы и типовая система

- Типы выводятся автоматически (type inference), но можно аннотировать:
  
````markdown
let add (a:int) (b:int) : int = a + b
````

- Базовые типы: int, float, bool, string, decimal, char, unit (аналог void, но значение — () )

- Кортежи (tuples):

````markdown
let pair = (1, "a")
let first, second = pair  // Деконструкция
````

- Списки: неизменяемые, односвязные.

````markdown
let xs = [1;2;3]
let ys = 0 :: xs          // Препенд (константный)
````

- Массивы: изменяемые, фикс-тип, индексируемые.

````markdown
let arr = [|1;2;3|]
arr.[0] <- 42             // Мутация
````

- Словари/карты: Map (иммутабельная), Dictionary (из BCL, изменяемая).

````markdown
let m = Map.empty.Add("a", 1).Add("b", 2)
m.TryFind "a"             // Option<int>
````

---

## 5) Функции: определение, частичное применение, каррирование

- В F# функция многопараметрична на самом деле каррирована: \[f: A -> B -> C\].
- Частичное применение создаёт новые функции.

````markdown
let multiply a b = a * b        // multiply: int -> int -> int
let double = multiply 2         // double: int -> int
double 21                       // => 42
````

- Анонимные функции:

````markdown
let squares = List.map (fun x -> x * x) [1;2;3]
````

- Функции высшего порядка: принимают/возвращают функции.

````markdown
let applyTwice f x = f (f x)
applyTwice ((+) 1) 10           // => 12
````

---

## 6) Иммутабельность и работа с состоянием

- Значения неизменяемые: вместо мутаций — создание новых.
- Для сложных сценариев состояния — использовать DU + Record или вычисляемые выражения.

````markdown
type Counter = { value:int }
let inc c = { c with value = c.value + 1 }
let start = { value = 0 }
start |> inc |> inc              // => { value = 2 }
````

---

## 7) Коллекции и функции высшего порядка

- Основные модули: List, Array, Seq (ленивые последовательности).

````markdown
let xs = [1..5]                       // [1;2;3;4;5]
xs |> List.filter (fun x -> x%2=0)    // [2;4]
xs |> List.map (fun x -> x*x)         // [1;4;9;16;25]
xs |> List.fold (fun acc x -> acc + x) 0  // 15
````

- Seq — ленивые:

````markdown
let squares = seq { for i in 1..5 do yield i*i }
squares |> Seq.take 3 |> Seq.toList   // [1;4;9]
````

- Композиция преобразований:

````markdown
let process =
  List.filter (fun x -> x > 10)
  >> List.map (fun x -> x * 2)
[5;12;20] |> process                  // [24;40]
````

---

## 8) Модули, пространства имён и организация кода

- namespace — для группировки модулей; module — для функций/типов.

````markdown
namespace MyApp

module Math =
  let add a b = a + b
  let square x = x * x

module StringUtils =
  let trim (s:string) = s.Trim()
````

- Открытие модулей:

````markdown
open MyApp.Math
add 2 3    // 5
````

- Файлы в проекте F# компилируются в порядке, указанном в проекте; определения должны быть доступны сверху вниз.

---

## 9) Управление потоком: сопоставление с образцом (pattern matching)

- Основной механизм ветвления, безопасный и выразительный.

````markdown
let describeNumber x =
  match x with
  | 0 -> "zero"
  | 1 | 2 -> "one or two"      // Альтернатива
  | n when n < 0 -> "negative" // Guard
  | _ -> "many"
````

- Сопоставление на кортежах/записях:

````markdown
let comparePair (a,b) =
  match a,b with
  | x,y when x = y -> "equal"
  | _,_ -> "different"
````

---

## 10) Вариативные типы: Option, Result, Discriminated Unions

### Option
- \[Option<'T>\] — безопасное отсутствие значения: Some x | None

````markdown
let tryDivide a b =
  if b = 0 then None else Some (a / b)

match tryDivide 10 2 with
| Some q -> sprintf "Result %d" q
| None -> "Division by zero"
````

### Result
- \[Result<'T,'Err>\] — успех или ошибка: Ok x | Error e

````markdown
type Error = | NotFound | InvalidInput of string

let readUser id =
  if id > 0 then Ok (sprintf "User%d" id)
  else Error (InvalidInput "id must be positive")

match readUser 1 with
| Ok u -> sprintf "Hello %s" u
| Error NotFound -> "No user"
| Error (InvalidInput msg) -> msg
````

### Discriminated Unions (DU)
- Моделируют доменные варианты вместо наследования.

````markdown
type PaymentMethod =
  | Cash
  | Card of number:string * holder:string
  | Transfer of iban:string

let pay pm amount =
  match pm with
  | Cash -> sprintf "Cash: %M" amount
  | Card (n,h) -> sprintf "Card %s (%s): %M" n h amount
  | Transfer iban -> sprintf "IBAN %s: %M" iban amount
````

---

## 11) Записи, структуры и классы (Record, Struct, Class)

### Record
- Имутабельные типы с именованными полями; поддерживают копирование с изменением.

````markdown
type Person = { Name:string; Age:int }

let john = { Name = "John"; Age = 30 }
let olderJohn = { john with Age = 31 }
````

### Struct Record / Struct
- Для значимых типов и оптимизации аллокаций.

````markdown
[<Struct>]
type Point = { X:int; Y:int }
````

### Class
- F# поддерживает классы, конструкторы, методы, свойства.

````markdown
type Counter(initial:int) =
  let mutable value = initial
  member _.Inc() = value <- value + 1
  member _.Value = value

let c = Counter(0)
c.Inc(); c.Value // 1
````

---

## 12) Активные шаблоны (Active Patterns)
- Позволяют создавать настраиваемые «паттерны» для match.

````markdown
let (|Even|Odd|) n = if n % 2 = 0 then Even else Odd

let describe n =
  match n with
  | Even -> "even"
  | Odd -> "odd"
````

- Параметризованные:

````markdown
let (|StartsWith|_|) (prefix:string) (s:string) =
  if s.StartsWith(prefix) then Some s else None

match "FSharp" with
| StartsWith "F" s -> sprintf "%s starts with F" s
| _ -> "no"
````

---

## 13) Пайпинг и композиция функций

- Пайпинг \[|>\] передаёт результат слева в функцию справа.

````markdown
let normalize =
  String.trim
  >> String.lowercase   // условно: представьте функцию

"  Hello " |> normalize
````

- В F#: чаще используем модули, напр. \[String\] из FSharp.Core.Additional (или пишем свои).

Пример с реальными функциями:

````markdown
let toLower (s:string) = s.ToLowerInvariant()
let trim (s:string) = s.Trim()

"  Hello " |> trim |> toLower  // "hello"
````

- Композиция \[>>\] и обратная композиция \[<<\]

````markdown
let normalize = trim >> toLower
normalize "  HI " // "hi"
````

---

## 14) Вычисляемые выражения (Computation Expressions)

- Синтаксический сахар для «монадоподобных» контекстов: async, seq, option, task и др.
- Пример: seq

````markdown
let items = seq {
  yield 1
  yield! [2;3]         // Вставка другой последовательности
  for i in 4..5 do
    yield i
}
items |> Seq.toList     // [1;2;3;4;5]
````

- Пример: option (из библиотеки, либо собственной реализации)

````markdown
let option = 
  // Псевдопример, если есть билдер option
  // позволяет писать let! для извлечения Some
  ()
````

- Пример: async

````markdown
let fetchAsync url = async {
  // имитация асинхронного вызова
  do! Async.Sleep 10
  return sprintf "data from %s" url
}

let program = async {
  let! a = fetchAsync "A"
  let! b = fetchAsync "B"
  return a + " & " + b
}
Async.RunSynchronously program
````

---

## 15) Асинхронность и параллелизм

- F# предоставляет \[async\] вычислимые выражения и функции в \[Async\] модуле.

````markdown
let work i = async {
  do! Async.Sleep 50
  return i*i
}

[1..5]
|> List.map work
|> Async.Parallel      // запускает параллельно
|> Async.RunSynchronously
// => [|1;4;9;16;25|]
````

- Можно использовать \[task\] (из .NET) и \[Task\]-билдеры в современных версиях.

---

## 16) Работа с побочными эффектами

- Принцип: локализовать эффекты на границах.
- Логика чистая, ввод/вывод — на периферии (например, в «порт/адаптер» слоях).
- Для тестируемости — передавать зависимость как функцию/интерфейс, DU моделировать результат.

---

## 17) Мутируемые структуры: когда и как

- Иногда мутация эффективнее (буферы, счётчики, внутренние циклы).
- Используйте mutable или ref, но ограничивайте область видимости.

````markdown
let sum arr =
  let mutable total = 0
  for x in arr do
    total <- total + x
  total
````

- \[ref\] — ячейка с изменяемым значением:

````markdown
let r = ref 0
r := !r + 1
!r // 1
````

---

## 18) Межъязыковая совместимость с C#

- F# и C# свободно вызывают код друг друга в рамках .NET.
- Распространённые моменты:
  - DU компилируются в классы с вложенными типами — удобно читать из C#, но менее естественно.
  - Record — иммутабельные классы с авто-свойствами; из C# видны как типы с get-only и WithMembers через методы.
  - Для удобства публичного API, иногда добавляют \[CLIMutable\] атрибут к Record для поддержания set-свойств (например, сериализация).

````markdown
[<CLIMutable>]
type User = { Name:string; Age:int }
````

---

## 19) Тестирование и свойствное тестирование

- xUnit/NUnit подходят.
- Для свойствного тестирования используйте FsCheck.

Пример простой property:

````markdown
// Свойство: реверс дважды даёт исходный список
let revTwiceIsOriginal (xs:int list) =
  (List.rev (List.rev xs)) = xs
````

---

## 20) Практические идиомы F#

- «Railway oriented programming»: использования Result для конвейера ошибок.

````markdown
let bind f res =
  match res with
  | Ok x -> f x
  | Error e -> Error e

let map f res =
  match res with
  | Ok x -> Ok (f x)
  | Error e -> Error e

let validatePositive x =
  if x > 0 then Ok x else Error "must be positive"

let toString x = Ok (string x)

let pipeline =
  validatePositive
  >> bind toString

pipeline 10 // Ok "10"
pipeline -1 // Error "must be positive"
````

- Доменно-ориентированное проектирование через DU + Record вместо иерархий классов.
- Минимизировать null: использовать Option, Result.
- Использовать сопоставление с образцом для всеобъемлющей обработки вариантов.

---

## 21) Инструменты и экосистема
- Компилятор: входит в .NET SDK, проект F# — .fsproj.
- IDE:
  - Visual Studio
  - JetBrains Rider
  - VS Code + Ionide (очень популярно в F# сообществе)
- Пакеты:
  - FSharp.Core — стандартная библиотека.
  - FsCheck — свойствное тестирование.
  - FSharp.Data — CSV, JSON, XML, HTTP.
  - FAKE — сборка.
  - Saturn, Giraffe — веб-фреймворки.
- Документация: fsharp.org, официальные туториалы.

---

## 22) Cheat Sheet: быстрый справочник

### Объявления
````markdown
let x = 1
let f a b = a + b
let g = f 10      // частичное применение
let inline sq x = x * x  // inline для перформанса/обобщения
````

### Типы
````markdown
type Person = { Name:string; Age:int }
type Payment = Cash | Card of string | Transfer of string
type Tree<'T> = Leaf of 'T | Node of Tree<'T> * Tree<'T>
````

### Pattern matching
````markdown
match value with
| Some x -> ...
| None -> ...
````

### Пайпинг и композиция
````markdown
xs |> List.map f |> List.filter p
let h = f >> g >> k
````

### Коллекции
````markdown
let xs = [1;2;3]
let arr = [|1;2;3|]
let m = Map [ ("a",1); ("b",2) ]
````

### Seq и ленивость
````markdown
let s = seq { for i in 1..10 do yield i*i }
s |> Seq.take 3 |> Seq.toList
````

### Option/Result
````markdown
let tryParseInt s =
  match System.Int32.TryParse s with
  | true, v -> Some v
  | _ -> None

let divide a b =
  if b=0 then Error "zero" else Ok (a/b)
````

### Async
````markdown
let work i = async { do! Async.Sleep 10; return i*i }
[1..5] |> List.map work |> Async.Parallel |> Async.RunSynchronously
````

### Mutable (редко)
````markdown
let mutable count = 0
count <- count + 1
````

### Модули и namespace
````markdown
namespace Demo
module Math =
  let add a b = a + b
````

### Взаимодействие с C#
- Добавляйте \[CLIMutable\] к записям для сериализации.
- DU и Record доступны из C#, но могут выглядеть непривычно.
