# Шпаргалка по Bubble Tea (Go TUI)

## Оглавление

1. Общее описание и философия
2. Архитектура MVU: Model, Msg, Cmd
3. Жизненный цикл: Init, Update, View
4. Program и опции запуска
5. Обработка событий: клавиатура, мышь, resize
6. Время: тикеры и таймеры
7. Асинхронные команды, Batch/Sequence, контекст
8. Делегация в подмодели (компонентный подход)
9. Популярные компоненты из bubbles
10. Стилизация и макеты с lipgloss
11. Работа с размерами и адаптивные интерфейсы
12. Управление ошибками и логирование
13. Тестирование моделей и команд
14. Паттерны: экраны, роутинг, модалки, мастера
15. Частые рецепты (спиннер, прогресс, список, ввод, viewport)
16. Типичные грабли и советы
17. Полезные опции, функции и утилиты
18. Мини-шаблоны стартовых приложений
19. Рекомендуемые пакеты и заметки по версиям

---

## 1) Общее описание и философия

- Bubble Tea — фреймворк для терминальных интерфейсов на Go, вдохновлён Elm-архитектурой.
- Декларативный подход: состояние в модели, отрисовка — функция от состояния.
- События приходят как сообщения (Msg), обработка — в Update, побочные эффекты — через Cmd.
- Цели: простота, предсказуемость, тестируемость, конкурентность без боли.

---

## 2) Архитектура MVU: Model, Msg, Cmd

- Model — структура состояния (любой тип, чаще struct).
- Msg — событие (интерфейсная метка: любой тип), например tea.KeyMsg, tea.WindowSizeMsg, пользовательские типы.
- Cmd — функция без аргументов, возвращающая Msg (или nil). Используется для асинхронных операций.
- Обновление состояния происходит только в Update на основании входящего Msg.

Мини-пример типов:
````markdown
type model struct {
  count int
  err   error
}

type tickMsg struct{}
type failMsg struct{ err error }
````

---

## 3) Жизненный цикл: Init, Update, View

- Init() tea.Cmd — однократно при запуске. Верните начальные команды (например, таймер/загрузку).
- Update(msg tea.Msg) (tea.Model, tea.Cmd) — сердце приложения.
- View() string — чистая функция рендера; не выполняйте побочных эффектов.

Минимальный скелет:
````markdown
type model struct {
  count int
  quitting bool
}

func (m model) Init() tea.Cmd {
  return tea.Tick(time.Second, func(time.Time) tea.Msg { return tickMsg{} })
}

func (m model) Update(msg tea.Msg) (tea.Model, tea.Cmd) {
  switch msg := msg.(type) {
  case tickMsg:
    m.count++
    return m, tea.Tick(time.Second, func(time.Time) tea.Msg { return tickMsg{} })
  case tea.KeyMsg:
    switch msg.String() {
    case "q", "esc", "ctrl+c":
      m.quitting = true
      return m, tea.Quit
    }
  }
  return m, nil
}

func (m model) View() string {
  if m.quitting {
    return "Пока!\n"
  }
  return fmt.Sprintf("Секунд: %d\nНажмите q для выхода.\n", m.count)
}
````

---

## 4) Program и опции запуска

Создание и запуск:
````markdown
p := tea.NewProgram(model{},
  tea.WithAltScreen(),          // полноэкранный альтернативный буфер
  tea.WithMouseCellMotion(),    // события перемещения мыши по клеткам
  // tea.WithOutput(customWriter) // при необходимости
)
if _, err := p.Run(); err != nil { log.Fatal(err) }
````

Полезные методы/опции:
- tea.WithAltScreen() — «полный экран», безопасное восстановление терминала на выходе.
- tea.WithFPS(fps) — ограничение частоты рендера (редко нужно).
- EnableMouseCellMotion / EnableMouseAllMotion — в старых версиях через методы Program.
- p.Send(msg) — отправка сообщения из другого goroutine (для внешних событий).

---

## 5) Обработка событий: клавиатура, мышь, resize

- Клавиатура: tea.KeyMsg
  - msg.String(): "enter", "esc", "ctrl+c", "up", "down", "left", "right", "backspace", символы.
  - msg.Type: tea.KeyType (KeyEnter, KeyRunes и т.п.)
- Мышь: tea.MouseMsg
  - msg.Type: MouseLeft, MouseRight, MouseWheelUp/Down, MouseMotion
  - msg.X, msg.Y — координаты
- Размеры: tea.WindowSizeMsg
  - msg.Width, msg.Height — символы по ширине/высоте

Пример обработки:
````markdown
case tea.KeyMsg:
  switch msg.String() {
  case "j", "down":
    m.cursor++
  case "k", "up":
    m.cursor--
  case "enter":
    return m, doActionCmd(m.cursor)
  case "q", "ctrl+c":
    return m, tea.Quit
  }

case tea.MouseMsg:
  if msg.Type == tea.MouseWheelDown { m.scroll++ }

case tea.WindowSizeMsg:
  m.width, m.height = msg.Width, msg.Height
````

---

## 6) Время: тикеры и таймеры

- tea.Tick(d, fn) — единичный тик через интервал d.
- tea.Every(d, fn) — периодические тики (в новых версиях Bubble Tea; если нет — перезапускайте Tick в Update).
- Отмена: храните флаги/состояние, игнорируйте лишние тики или используйте контекст в своих Cmd.

Пример:
````markdown
func tickEvery(d time.Duration) tea.Cmd {
  return tea.Tick(d, func(time.Time) tea.Msg { return tickMsg{} })
}
````

---

## 7) Асинхронные команды, Batch/Sequence, контекст

- Cmd — любой побочный эффект, завершающийся Msg.
- tea.Batch(c1, c2, ...) — параллельный запуск нескольких команд; все их Msg вернутся в Update.
- tea.Sequence(c1, c2, ...) — последовательное выполнение (если доступно в вашей версии).
- Отмена/таймауты: создавайте Cmd, принимающие context.Context.

Шаблон загрузки:
````markdown
type loadedMsg struct {
  data string
  err  error
}

func loadDataCmd(ctx context.Context) tea.Cmd {
  return func() tea.Msg {
    // долгая операция
    select {
    case <-time.After(500 * time.Millisecond):
      return loadedMsg{data: "OK"}
    case <-ctx.Done():
      return loadedMsg{err: ctx.Err()}
    }
  }
}
````

---

## 8) Делегация в подмодели (компонентный подход)

- Каждую «часть экрана» оформляйте как подмодель с Init/Update/View.
- Родитель делегирует msg в активные подмодели и агрегирует команды через tea.Batch.

Пример:
````markdown
type parent struct {
  a, b tea.Model
  active int // 0 или 1
}

func (p parent) Init() tea.Cmd {
  return tea.Batch(p.a.Init(), p.b.Init())
}

func (p parent) Update(msg tea.Msg) (tea.Model, tea.Cmd) {
  switch v := msg.(type) {
  case tea.KeyMsg:
    if v.String() == "tab" { p.active = 1 - p.active }
  }
  var cmd tea.Cmd
  if p.active == 0 {
    p.a, cmd = p.a.Update(msg)
  } else {
    p.b, cmd = p.b.Update(msg)
  }
  return p, cmd
}

func (p parent) View() string {
  if p.active == 0 { return p.a.View() }
  return p.b.View()
}
````

---

## 9) Популярные компоненты из bubbles

- textinput — однострочный ввод
- textarea — многострочный ввод
- list — список с делегатом рендера, фильтром
- table — таблица (в новых версиях)
- paginator — пагинация
- spinner — индикатор ожидания
- progress — прогресс-бар
- viewport — прокрутка большого текста
- help — подсказки горячих клавиш

TextInput:
````markdown
ti := textinput.New()
ti.Placeholder = "Введите имя"
ti.Focus()
ti.CharLimit = 64
ti.Width = 20

// В Update:
var cmd tea.Cmd
ti, cmd = ti.Update(msg)
return m, cmd
````

List:
````markdown
items := []list.Item{item("Первый"), item("Второй")}
l := list.New(items, list.NewDefaultDelegate(), 30, 10)
l.Title = "Список"
l.SetShowStatusBar(false)
l.SetFilteringEnabled(true)
````

Spinner:
````markdown
sp := spinner.New()
sp.Spinner = spinner.Dot
// В Update: sp, cmd = sp.Update(msg)
// В View: sp.View()
````

---

## 10) Стилизация и макеты с lipgloss

- Рамки, цвета, отступы, выравнивания.
- Полезные методы: Border, Padding, Margin, Align, Width/Height, Foreground/Background, Bold/Italic.

Пример:
````markdown
var title = lipgloss.NewStyle().Bold(true).Foreground(lipgloss.Color("205"))
var box = lipgloss.NewStyle().Border(lipgloss.RoundedBorder()).Padding(1, 2).Width(40)

func (m model) View() string {
  return box.Render(title.Render("Заголовок") + "\n" + "Контент")
}
````

Лейауты:
- lipgloss.JoinHorizontal/JoinVertical — склейка блоков по горизонтали/вертикали.
- lipgloss.Place — размещение блока в заданных габаритах.

---

## 11) Работа с размерами и адаптивные интерфейсы

- Обрабатывайте tea.WindowSizeMsg и сохраняйте ширину/высоту в модель.
- Передавайте размеры компонентам: viewport.Width/Height, list.SetSize(w, h), table.SetWidth/Height.

Пример:
````markdown
case tea.WindowSizeMsg:
  m.w, m.h = msg.Width, msg.Height
  m.viewport.Width = m.w - 4
  m.viewport.Height = m.h - 6
  m.list.SetSize(m.w/2, m.h-4)
````

Советы:
- Первый WindowSizeMsg приходит после инициализации — избегайте жёсткой верстки до него.
- Используйте lipgloss.Width/Height для корректных измерений строк с цветами.

---

## 12) Управление ошибками и логирование

- Возвращайте ошибки через сообщения: type errMsg struct{ error }.
- Логируйте в файл, чтобы не ломать TUI: tea.LogToFile("debug.log", "app").
- Не печатайте в stdout во время работы TUI; используйте лог или alt-каналы.

---

## 13) Тестирование моделей и команд

- Тестируйте Update как чистую функцию: подайте Msg, проверьте новое состояние.
- Команды можно «исполнить» прямо в тесте: вызовите возвращённый Cmd и обработайте пришедший Msg.
- Для недетерминированных эффектов внедряйте зависимости (функции/интерфейсы) и подменяйте в тестах.

Пример:
````markdown
func TestIncrement(t *testing.T) {
  m := model{}
  mm, _ := m.Update(tea.KeyMsg{Type: tea.KeyRunes, Runes: []rune{'+'}})
  if mm.(model).count != 1 {
    t.Fatalf("want 1")
  }
}
````

---

## 14) Паттерны: экраны, роутинг, модалки, мастера

- Экраны (screen enum): храните currentScreen, делегируйте Update/View соответствующей подмодели.
- Роутинг стеком: []tea.Model, push/pop при навигации назад/вперёд.
- Модалки: флаг modalOpen + подмодель модалки поверх контента; перехватывайте ввод, пока модалка открыта.
- Мастер-диалог (wizard): массив шагов, currentStep, кнопки Next/Back, валидация на шаге.

---

## 15) Частые рецепты

Спиннер во время загрузки:
````markdown
type loadingMsg struct{ done bool }

func loadCmd() tea.Cmd {
  return func() tea.Msg {
    time.Sleep(800 * time.Millisecond)
    return loadingMsg{done: true}
  }
}

case loadingMsg:
  m.loading = !msg.done
````

Прогресс:
````markdown
type progressMsg float64

func tickProgress() tea.Cmd {
  return tea.Tick(100*time.Millisecond, func(time.Time) tea.Msg { return progressMsg(0.05) })
}

case progressMsg:
  m.p = math.Min(1, m.p+float64(msg))
  if m.p < 1 { return m, tickProgress() }
````

Viewport для длинного текста:
````markdown
vp := viewport.New(80, 20)
vp.SetContent(longText)
// В Update: vp, cmd = vp.Update(msg)
````

Список с выбором по Enter:
````markdown
case tea.KeyMsg:
  if msg.String() == "enter" {
    it, ok := m.list.SelectedItem().(item)
    if ok { m.selection = it }
  }
````

TextInput с подтверждением:
````markdown
case tea.KeyMsg:
  if msg.Type == tea.KeyEnter {
    m.submitted = m.input.Value()
  }
````

---

## 16) Типичные грабли и советы

- Не блокируйте Update: тяжелые операции — только в Cmd.
- View должно быть быстрым и детерминированным.
- Следите за фокусом компонентов: input.Focus(), list.SetShowHelp и т.д.
- Если компонент «не обновляется», не забудьте делегировать Update и вернуть cmd.
- При конкуренции команд используйте Batch, не запускайте блокирующие последовательности.
- Учитывайте ширину Unicode: lipgloss.Width, runewidth.
- Для мыши не забудьте включить соответствующие опции Program.
- На старте ширина/высота могут быть нулями до первого WindowSizeMsg.

---

## 17) Полезные опции, функции и утилиты

- tea.Quit — команда завершить приложение.
- tea.Batch — собрать несколько команд.
- tea.Sequence — последовательное выполнение (в нов. версиях).
- tea.Tick / tea.Every — таймеры/тикеры.
- tea.Printf — форматированный вывод в лог (если включён).
- Program.Send — внедрение внешних событий.

---

## 18) Мини-шаблоны стартовых приложений

«Счётчик + выход по q»:
````markdown
package main

import (
  "fmt"
  "log"
  "time"

  tea "github.com/charmbracelet/bubbletea"
)

type model struct{ n int }
type tickMsg struct{}

func (m model) Init() tea.Cmd {
  return tea.Tick(time.Second, func(time.Time) tea.Msg { return tickMsg{} })
}

func (m model) Update(msg tea.Msg) (tea.Model, tea.Cmd) {
  switch v := msg.(type) {
  case tickMsg:
    m.n++
    return m, tea.Tick(time.Second, func(time.Time) tea.Msg { return tickMsg{} })
  case tea.KeyMsg:
    if v.String() == "q" || v.String() == "ctrl+c" {
      return m, tea.Quit
    }
  }
  return m, nil
}

func (m model) View() string {
  return fmt.Sprintf("n = %d\nНажмите q для выхода.\n", m.n)
}

func main() {
  if _, err := tea.NewProgram(model{}).Run(); err != nil {
    log.Fatal(err)
  }
}
````

«Два экрана с переключением tab»:
````markdown
type screen int
const (
  scrList screen = iota
  scrInput
)

type app struct {
  s screen
  list  list.Model
  input textinput.Model
}

func (a app) Init() tea.Cmd {
  return tea.Batch(a.list.Init(), textinput.Blink)
}

func (a app) Update(msg tea.Msg) (tea.Model, tea.Cmd) {
  switch v := msg.(type) {
  case tea.KeyMsg:
    switch v.String() {
    case "tab":
      if a.s == scrList { a.s = scrInput } else { a.s = scrList }
    case "q", "ctrl+c":
      return a, tea.Quit
    }
  }
  var cmd tea.Cmd
  switch a.s {
  case scrList:
    a.list, cmd = a.list.Update(msg)
  case scrInput:
    a.input, cmd = a.input.Update(msg)
  }
  return a, cmd
}

func (a app) View() string {
  switch a.s {
  case scrList:  return a.list.View()
  case scrInput: return a.input.View()
  }
  return ""
}
````

---

## 19) Рекомендуемые пакеты и заметки по версиям

- charmbracelet/bubbletea — ядро TUI.
- charmbracelet/bubbles — готовые UI-компоненты.
- charmbracelet/lipgloss — стилизация строк и лейауты.
- muesli/reflow, mattn/go-runewidth — перенос слов и ширина рун (по необходимости).