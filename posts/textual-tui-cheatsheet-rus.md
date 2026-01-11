# Шпаргалка по Textual (Textualize)

## Оглавление
1. Введение: что такое Textual
2. Установка и быстрый старт
3. Базовая архитектура: App, Widget, Compose, Messages
4. Структура проекта и организация кода
5. Базовые виджеты (Static, Button, Checkbox, Input, TextArea, Header/Footer/Log)
6. Расширенные виджеты (Tabs, TabbedContent, DataTable, Tree, DirectoryTree, ListView, ProgressBar, LoadingIndicator, Tooltip, Markdown)
7. Контейнеры и Layout (Horizontal/Vertical, ScrollView, ContentSwitcher, Grid, Dock)
8. Textual CSS (TCSS): селекторы, свойства, переменные, состояния
9. События, сообщения и Actions: обработчики, бинды, командная палитра
10. Состояние, реактивность и вычисляемые значения (reactive, watch_*, computed)
11. Навигация: Screen, ModalScreen, push_screen_wait
12. Ввод: клавиатура, мышь, фокус, доступность
13. Асинхронность: таймеры, воркеры, безопасные обновления UI
14. Таблицы, деревья, списки, вкладки: практические приёмы
15. Формы и ввод: валидация, UX-паттерны
16. Логирование, девтулзы, инспектор, отладка
17. Тестирование: Pilot/AppTest, советы
18. Паттерны и лучшие практики
19. Частые ошибки и анти-паттерны
20. Полезные сниппеты (расширенные, с комментариями)
21. Производительность: рекомендации
22. Дополнительно: плагины, темы, локализация
23. Ресурсы и ссылки

---

## 1) Введение: что такое Textual
- Textual — фреймворк для TUI (Text-based UI) в терминале на Python.
- Построен поверх Rich, поэтому поддерживает цвета, стили, эмодзи, мышь, truecolor, Unicode.
- Декларативное построение интерфейса через compose(), стили через TCSS, состояние — через реактивность.

Зачем использовать:
- Быстрое прототипирование интерактивных CLI-панелей и инструментов.
- Кроссплатформенность и дружелюбность к терминалу.
- Современная модель разработки (аналог web: DOM/React + CSS).

---

## 2) Установка и быстрый старт
- Требования: Python 3.8+.
- Установка: pip install textual
- Быстрый пример с комментариями:

````python
# Импортируем базовые элементы приложения и виджеты
from textual.app import App, ComposeResult
from textual.widgets import Header, Footer, Static

class HelloApp(App):
    # Можно хранить стили прямо строкой (для простоты примера),
    # но в реальном проекте лучше вынести в отдельный .tcss файл
    CSS = """
    Screen { align: center middle; }          /* Центрируем контент по вертикали и горизонтали */
    Static { border: solid green; padding: 1 2; }  /* Рамка и отступы для блока текста */
    """

    # compose() декларативно создает дерево виджетов
    def compose(self) -> ComposeResult:
        yield Header()                         # Верхняя панель с заголовком и (опц.) часами
        yield Static("Привет, Textual!")       # Простой текстовый виджет
        yield Footer()                         # Нижняя панель с подсказками по клавишам/действиям

if __name__ == "__main__":
    HelloApp().run()                           # Точка входа
````

Советы:
- Начинайте с маленького приложения и по шагам добавляйте виджеты и стили.
- Запускайте с devtools, чтобы видеть логи и инспектор.

---

## 3) Базовая архитектура: App, Widget, Compose, Messages
- App: корневой класс, управляет жизненным циклом, экранами, биндами, глобальными событиями.
- Widget: атомарная единица UI; может быть простым (Static) или сложным (DataTable).
- compose(): декларативно объявляет детей виджета/приложения через yield.
- Messages/Events: система событий (on_* обработчики) и пользовательских сообщений для общения между виджетами.
- Actions: методы action_* вызываются через бинды и командную палитру.

Ключевые хуки:
- on_mount(self): вызывается после монтирования в дерево, удобно стартовать таймеры/воркеры.
- on_key(self, event): глобальный перехват клавиш (учитывая фокус и приоритет).
- on_xxx_event(self, event): типизированные обработчики виджетов (например, on_button_pressed).

---

## 4) Структура проекта и организация кода
Рекомендуемая структура:
- app.py — главный класс App и/или точка входа.
- screens/ — экраны (Screen, ModalScreen), если навигация сложная.
- widgets/ — кастомные виджеты (инкапсулируйте логику и стили).
- styles/ — TCSS файлы (app.tcss, темы, модули стилей).
- services/ — бизнес-логика, I/O, клиенты API (не смешивайте с виджетами).
- tests/ — автотесты (используйте textual.testing).

Пример дерева:
- myapp/
  - app.py
  - screens/
    - main.py
    - settings.py
  - widgets/
    - sidebar.py
    - status_badge.py
  - styles/
    - app.tcss
    - dark.tcss
  - services/
    - api.py
  - tests/

---

## 5) Базовые виджеты

### Static / Label
````python
from textual.widgets import Static

class Badge(Static):
    # Простой "бейдж", который показывает статус
    def on_mount(self):
        self.update("Build: OK")  # .update меняет содержимое рендеринга
````

### Button
````python
from textual.widgets import Button

def compose(self):
    # id и classes помогут в обработчиках и стилизации (через TCSS)
    yield Button("Сохранить", id="save", classes="primary")

def on_button_pressed(self, e: Button.Pressed):
    # Обработчик событий для всех кнопок; различаем их по id
    if e.button.id == "save":
        self.log("Saving...")  # Выведет в девтулзы/лог панель
````

### Checkbox / Switch / RadioSet
````python
from textual.widgets import Checkbox, Switch, RadioSet, RadioButton

def compose(self):
    yield Checkbox("Включить", value=True, id="cb")
    yield Switch(id="sw", value=False)  # Тумблер
    # Группа радиокнопок (один выбор)
    with RadioSet(id="theme"):
        yield RadioButton("Светлая", value=True)
        yield RadioButton("Тёмная")

def on_checkbox_changed(self, e: Checkbox.Changed):
    self.log("Checkbox:", e.value)  # True/False

def on_switch_changed(self, e: Switch.Changed):
    self.log("Switch:", e.value)  # True/False

def on_radio_set_changed(self, e: RadioSet.Changed):
    # e.pressed — RadioButton, у него есть .label или .id
    self.log("Radio:", e.pressed.label)
````

### Input / TextArea
````python
from textual.widgets import Input, TextArea

def compose(self):
    yield Input(placeholder="Введите имя", id="name")
    yield TextArea(id="bio", text="Описание...")

def on_input_submitted(self, e: Input.Submitted):
    # Срабатывает по Enter; удобно отправлять формы
    self.log("Submitted:", e.value)

def on_text_area_changed(self, e: TextArea.Changed):
    # Показываем динамику ввода
    self.log("TextArea length:", len(e.control.text))
````

### Header / Footer / Log
````python
from textual.widgets import Header, Footer, Log

def compose(self):
    yield Header(show_clock=True)         # Вкл. часы в заголовке
    yield Log(id="log", highlight=True)   # Лог с подсветкой синтаксиса
    yield Footer()                        # Показывает бинды/подсказки
````

Совет:
- Log полезен как "консоль в консоли": self.query_one("#log", Log).write("...").

---

## 6) Расширенные виджеты

### Tabs / TabbedContent
````python
from textual.widgets import Tabs, TabbedContent, TabPane, Tab, Static

def compose(self):
    # Tabs управляет активной вкладкой; TabbedContent — содержимым
    yield Tabs(
        Tab("Обзор", id="tab_overview"),
        Tab("Настройки", id="tab_settings"),
        active="tab_overview",        # id активной вкладки
        id="tabs",
    )
    with TabbedContent(id="content"):
        # Каждая панель — содержимое вкладки
        with TabPane("Обзор", id="pane_overview"):
            yield Static("Добро пожаловать")
        with TabPane("Настройки", id="pane_settings"):
            yield Static("Параметры тут")

def on_tabs_tab_activated(self, e: Tabs.TabActivated):
    # Реакция на переключение вкладки (например, ленивая загрузка)
    self.log("Activated:", e.tab.id)
````

### DataTable
````python
from textual.widgets import DataTable

def compose(self):
    table = DataTable(zebra_stripes=True, id="table")
    table.add_columns("ID", "Name", "Status")               # Заголовки
    table.add_rows([("1", "Alpha", "Open"), ("2", "Beta", "Closed")])
    yield table

def on_data_table_row_selected(self, e: DataTable.RowSelected):
    # Определяем текущую строку курсора и читаем данные
    row = e.cursor_row
    row_data = e.data_table.get_row(row)                    # Возвращает кортеж/список значений
    self.log(f"Row {row}: {row_data}")
````

Полезные методы:
- update_cell(row, col, value), remove_row, clear, sort(column), cursor_type.

### Tree / DirectoryTree
````python
from textual.widgets import Tree, DirectoryTree

def compose(self):
    tree = Tree("Корень", id="tree")
    node = tree.root.add("Узел 1")      # Добавляем дочерний узел
    node.add_leaf("Лист А")             # Листовой элемент
    yield tree
    yield DirectoryTree(".", id="fs")   # Файловое дерево корня "."

def on_tree_node_selected(self, e: Tree.NodeSelected):
    # e.node — выбранный узел (имеет label, data и т.п.)
    self.log("Selected node:", e.node.label)
````

Ленивая подгрузка:
- Реализуйте в on_tree_node_expanded: при первом раскрытии узла добавляйте детей.

### ListView / ListItem
````python
from textual.widgets import ListView, ListItem, Label

def compose(self):
    items = [ListItem(Label(f"Элемент {i}")) for i in range(5)]
    yield ListView(*items, id="list")

def on_list_view_selected(self, e: ListView.Selected):
    # e.index — индекс выбранного элемента
    self.log("Selected index:", e.index)
````

### ProgressBar
````python
from textual.widgets import ProgressBar

def compose(self):
    # total — максимум прогресса
    yield ProgressBar(id="pb", total=100)

def on_mount(self):
    pb = self.query_one("#pb", ProgressBar)
    # set_interval запускает периодический коллбэк
    self.set_interval(0.05, lambda: pb.advance(1))  # advance(+1) до достижения total
````

### LoadingIndicator / Busy
````python
from textual.widgets import LoadingIndicator, Static

def compose(self):
    yield Static("Загрузка:")
    yield LoadingIndicator(id="spinner")  # Анимированный спиннер
````

### Tooltip
````python
from textual.widgets import Button

def compose(self):
    btn = Button("Наведи", id="hover")
    btn.tooltip = "Подсказка"  # Включаем тултип коротко
    yield btn
````

### Markdown
````python
from textual.widgets import Markdown

def compose(self):
    # Рендер Markdown в терминале (заголовки, списки, выделение)
    yield Markdown("# Заголовок\n\n- Пункт 1\n- Пункт 2")
````

---

## 7) Контейнеры и Layout

### Horizontal / Vertical / ScrollableContainer
````python
from textual.containers import Horizontal, Vertical, ScrollableContainer
from textual.widgets import Static

def compose(self):
    # Горизонтальный контейнер: дети расположены в ряд
    with Horizontal():
        with Vertical(id="sidebar"):
            yield Static("Меню 1")
            yield Static("Меню 2")
        # Прокручиваемая область контента
        with ScrollableContainer(id="content"):
            for i in range(50):
                yield Static(f"Строка {i}")
````

### ContentSwitcher (переключение секций)
````python
from textual.containers import ContentSwitcher
from textual.widgets import Button, Static

def compose(self):
    yield Button("Показать A", id="btn_a")
    yield Button("Показать B", id="btn_b")
    with ContentSwitcher(initial="a", id="switcher"):
        yield Static("Секция A", id="a")
        yield Static("Секция B", id="b")

def on_button_pressed(self, e: Button.Pressed):
    # Меняем текущую секцию по id
    sw = self.query_one("#switcher", ContentSwitcher)
    sw.current = "a" if e.button.id == "btn_a" else "b"
````

### Grid и Dock (через TCSS)
Пример Grid в TCSS:
````css
Screen {
  layout: grid;              /* Включаем Grid Layout */
  grid-size: 3 2;            /* 3 колонки x 2 строки */
  grid-gutter: 1 1;          /* Отступы между ячейками */
}
#sidebar { grid-column: 1; grid-row: 1 / span 2; }   /* Высокая левая колонка */
#main    { grid-column: 2 / span 2; grid-row: 1; }   /* Верхняя правая область */
#footer  { grid-column: 2 / span 2; grid-row: 2; }   /* Нижняя правая область */
````

Пример Dock (старый, но полезный паттерн):
- dock: left|right|top|bottom — “пристыковка” краевых элементов; остаток занимает центр.

---

## 8) Textual CSS (TCSS): селекторы, свойства, переменные, состояния

### Селекторы
- По типу: Button { ... }
- По классу: .primary { ... }
- По id: #save { ... }
- Иерархия: Screen > #sidebar Button.primary { ... }

### Частые свойства
- layout: horizontal|vertical|grid
- width/height: auto|N|%|min-content|max-content
- min-width/height, max-width/height
- margin, padding, border, border-title
- color, background, text-style (bold, italic, dim)
- align, align-horizontal, align-vertical
- overflow: auto|hidden|scroll
- gap: расстояние между детьми
- layer: управление слоями (оверлеи)

### Переменные
````css
:root { --accent: dodgerblue; }
Button.primary { background: var(--accent); color: white; }
````

### Состояния (псевдоклассы)
- :hover, :focus, :disabled, :active

Пример TCSS для приложения:
````css
Screen { layout: horizontal; }
#sidebar { width: 30; border: round #666; padding: 1; gap: 1; }
#content { flex: 1; padding: 1; overflow: auto; }
Button.primary { background: dodgerblue; color: white; }
Button.primary:hover { background: royalblue; }
````

---

## 9) События, сообщения и Actions

### Бинды и Actions
````python
from textual.app import App
from textual.binding import Binding

class BindApp(App):
    # Binding(клавиша, имя_действия, подсказка)
    BINDINGS = [
        Binding("q", "quit", "Выход"),
        Binding("r", "reload", "Перезагрузить"),
        Binding("ctrl+p", "palette", "Палитра"),
    ]

    def action_quit(self): self.exit()            # Выход из приложения
    def action_reload(self): self.refresh()       # Перерисовать текущий экран
    def action_palette(self): self.open_command_palette()  # Открыть палитру команд
````

### Кастомные сообщения (Message)
````python
from textual.message import Message
from textual.widgets import Static

class ValueChanged(Message):
    # Кастомное сообщение с полезной нагрузкой (value)
    def __init__(self, sender, value:int) -> None:
        self.value = value
        super().__init__(sender)

class Counter(Static):
    # Виджет, который инкрементирует значение по клику и оповещает
    def __init__(self): 
        super().__init__("0")
        self._v = 0

    def on_click(self):
        self._v += 1
        self.update(str(self._v))
        # post_message отправляет сообщение вверх по дереву (bubble)
        self.post_message(ValueChanged(self, self._v))

# Обработчик в родителе:
def on_value_changed(self, e: ValueChanged):
    self.log("Counter:", e.value)
````

---

## 10) Состояние, реактивность и вычисляемые значения

Почему reactive:
- Изменение reactive-поля автоматически инициирует обновление (refresh/render) и вызывает watch_*-наблюдатели.
- computed кэширует вычисления на основе reactive-полей.

````python
from textual.widget import Widget
from textual.reactive import reactive, computed

class Gauge(Widget):
    value = reactive(0)  # int 0..10 — реактивное состояние

    @computed
    def percent(self) -> int:
        # Вычисляем значение на основе reactive-поля
        return self.value * 10

    def render(self):
        # Рендер зависит только от состояния — детерминированно и предсказуемо
        blocks = "█" * self.value + "░" * (10 - self.value)
        return f"[{blocks}] {self.percent}%"

    def watch_value(self, old, new):
        # Вызывается при каждом изменении value
        self.refresh()  # Просим перерисовать виджет
````

Советы:
- Минимизируйте количество reactive-полей — держите только то, что реально влияет на UI.
- Не делайте I/O внутри render() — только вычисления и форматирование.

---

## 11) Навигация: Screen, ModalScreen, push_screen_wait
````python
from textual.app import App, ComposeResult
from textual.screen import Screen
from textual.widgets import Button, Static

class Confirm(Screen[bool]):
    # Modal-подобный экран подтверждения (возвращает bool)
    def compose(self) -> ComposeResult:
        yield Static("Удалить?")
        yield Button("Да", id="yes")
        yield Button("Нет", id="no")

    def on_button_pressed(self, e: Button.Pressed):
        # dismiss завершает экран и возвращает результат в push_screen_wait
        self.dismiss(e.button.id == "yes")

class MyApp(App):
    async def action_delete(self):
        # Ожидаем результат модального экрана
        ok = await self.push_screen_wait(Confirm())
        if ok:
            self.log("Deleted!")

if __name__ == "__main__":
    MyApp().run()
````

Примечание:
- push_screen/pop_screen — стек экранов.
- switch_screen — замена текущего экрана (без стека).

---

## 12) Ввод: клавиатура, мышь, фокус, доступность
````python
def on_key(self, e):
    # Глобальная обработка клавиш; учтите, что у фокусируемых виджетов приоритет
    if e.key == "escape":
        self.exit()

def on_mouse_move(self, e):
    # Доступна поддержка координат мыши, wheel, нажатий
    self.log(f"mouse: {e.x},{e.y}")
````

Советы:
- Используйте BINDINGS для декларативных горячих клавиш.
- Следите за фокусом: некоторые виджеты перехватывают события (Input, TextArea).
- Footer показывает подсказки по биндам — улучшает доступность.

---

## 13) Асинхронность: таймеры, воркеры, безопасные обновления UI
````python
class AsyncApp(App):
    async def on_mount(self):
        # Периодический таймер (tick каждые 1.0с)
        self.set_interval(1.0, self.tick)

        # Воркеры — безопасный запуск асинхронной задачи "в фоне"
        # exit_on_error=False — не валить приложение при исключении
        self.job = self.run_worker(self.fetch_data(), group="io", exit_on_error=False)

    def tick(self):
        # Обновлять UI из таймера можно напрямую (он в главном потоке)
        self.query_one("#status", Static).update("tic")

    async def fetch_data(self):
        # Имитация сетевого/файлового I/O
        import asyncio
        await asyncio.sleep(2)
        # Возвращаемся в UI-поток через call_from_thread
        self.call_from_thread(lambda: self.query_one("#status", Static).update("Готово"))
````

Важно:
- Никогда не блокируйте главный поток (никаких time.sleep, requests без async).
- Любые обновления UI из фоновых задач — через call_from_thread/call_soon_threadsafe.

---

## 14) Таблицы, деревья, списки, вкладки: практические приёмы

### DataTable: обновление и сортировка
````python
table = self.query_one("#table", DataTable)
table.update_cell(0, 2, "In Progress")  # Обновить ячейку [row=0,col=2]
table.sort("Name")                       # Сортировать по колонке "Name"
````

### Tree: ленивая загрузка
````python
def on_tree_node_expanded(self, e: Tree.NodeExpanded):
    node = e.node
    if not node.children:               # Добавляем детей только один раз
        child = node.add("Подузел 1")
        child.add_leaf("Лист X")
````

### Tabs: синхронизация с TabbedContent
- Держите стабильные id: tab_overview <-> pane_overview и т.п.
- Обрабатывайте on_tabs_tab_activated для переключения данных по требованию.

---

## 15) Формы и ввод: валидация, UX-паттерны

### Валидация через CSS-класс
````python
from textual.widgets import Input

def on_input_changed(self, e: Input.Changed):
    # Простая проверка длины поля
    ok = len(e.value.strip()) >= 3
    # set_class(flag, class_name): применить/снять CSS-класс
    e.input.set_class(not ok, "error")
````

````css
/* В TCSS подсвечиваем некорректные поля */
Input.error { border: heavy red; }
````

### Горячая отправка и сбор данных формы
````python
from textual.widgets import Input, Button

def compose(self):
    yield Input(placeholder="Имя", id="name")
    yield Input(placeholder="Email", id="email")
    yield Button("Сохранить", id="submit", classes="primary")

def on_input_submitted(self, e: Input.Submitted):
    # Enter отправляет форму
    self.action_save()

def on_button_pressed(self, e: Button.Pressed):
    if e.button.id == "submit":
        self.action_save()

def action_save(self):
    name = self.query_one("#name", Input).value.strip()
    email = self.query_one("#email", Input).value.strip()
    self.log(f"Saving: name={name}, email={email}")
````

---

## 16) Логирование, девтулзы, инспектор, отладка
- self.log("...") — лог сообщения (видно в devtools).
- Запуск с девтулзами:
  - App.run(devtools=True) или textual run --dev app:MyApp
- Инспектор (дерево, стили, слои):
  - Открывается горячей клавишей (обычно Ctrl+T или указанной в документации вашей версии).

Отладка:
- Логируйте события on_* для сложных виджетов.
- Проверяйте селекторы TCSS через инспектор (видно, какие правила применены).

---

## 17) Тестирование
````python
import pytest
from textual.testing import AppTest
from app import MyApp

@pytest.mark.asyncio
async def test_flow():
    # AppTest поднимает приложение и дает "пилота" для действий
    async with AppTest(MyApp()) as pilot:
        await pilot.press("r")       # Нажать клавишу "r"
        await pilot.pause()          # Дать времени обработать события
        # Проверяем текст в виджете #status (пример)
        status = pilot.app.query_one("#status")
        assert "Готово" in status.renderable.plain
````

Советы:
- Используйте pilot.click(x, y) и pilot.press("ctrl+p") для разных сценариев.
- Разбивайте тесты по фичам; избегайте больших интеграционных монолитов.

---

## 18) Паттерны и лучшие практики
- Разделение ответственности: UI-виджеты отдельно, бизнес-логика — в services/.
- Минимум глобального состояния: обмен через Message/Events.
- Реактивность в помощь: используйте reactive/watch_* вместо ручного refresh().
- Все I/O — асинхронно (run_worker, asyncio).
- Именуйте id/classes последовательно — упростит стилизацию и тестирование.
- Темы и переменные TCSS — для единообразного оформления.

---

## 19) Частые ошибки и анти-паттерны
- Блокировка UI (time.sleep, requests): замените на asyncio.sleep, aiohttp, воркеры.
- Прямое обновление UI из воркера: используйте call_from_thread/call_soon.
- Слишком много refresh(): полагайтесь на reactive/watch_*.
- Путаница id/classes или селекторов: проверяйте инспектором.
- Конфликты биндов/фокус: просматривайте BINDINGS и проверяйте активный фокус.

---

## 20) Полезные сниппеты (расширенные, с комментариями)

### A) Каркас приложения: сайдбар + контент + стили
````python
from textual.app import App, ComposeResult
from textual.widgets import Header, Footer, Button, Static
from textual.containers import Vertical, Horizontal

class MainApp(App):
    # Лучше хранить стили в отдельном файле
    CSS_PATH = "styles/app.tcss"

    def compose(self) -> ComposeResult:
        yield Header()
        # Горизонтальный сплит: слева — сайдбар, справа — контент
        with Horizontal():
            with Vertical(id="sidebar"):
                yield Button("Обновить", id="refresh", classes="primary")
                yield Button("Выход", id="quit")
            with Vertical(id="content"):
                yield Static("Загрузка...", id="status")
        yield Footer()

    def on_button_pressed(self, e: Button.Pressed):
        if e.button.id == "quit":
            self.exit()                        # Аккуратно завершаем приложение
        elif e.button.id == "refresh":
            # Планируем обновление данных (асинхронно)
            self.call_after_refresh(self.load_data)

    async def load_data(self):
        # Имитация долгой операции; выносим в воркер
        self.log("Refreshing...")
        await self.run_worker(self._fetch(), exclusive=True)

    async def _fetch(self):
        import asyncio
        await asyncio.sleep(1)                 # Имитация I/O
        # Возврат в UI-поток: безопасно обновляем интерфейс
        self.call_from_thread(
            lambda: self.query_one("#status", Static).update("Данные обновлены")
        )

if __name__ == "__main__":
    MainApp().run()
````

styles/app.tcss:
````css
Screen {
  layout: vertical;             /* Весь экран — вертикальная колонка */
}
#sidebar {
  width: 30;                    /* Фиксированная ширина боковой панели */
  border: round #666;
  padding: 1;
  gap: 1;                       /* Расстояние между кнопками */
}
#content {
  flex: 1;                      /* Занимаем всё оставшееся пространство */
  padding: 1;
}
Button.primary {
  background: dodgerblue;       /* Основной цвет акцентных кнопок */
  color: white;
}
Button.primary:hover { background: royalblue; }
````

Комментарий:
- exclusive=True предотвращает запуск параллельных копий одной и той же работы.
- call_after_refresh откладывает вызов до ближайшего кадра, чтобы избежать лишних перерисовок.

### B) DataTable с выбором строк и контекстным действием
````python
from textual.app import App, ComposeResult
from textual.widgets import DataTable, Footer
from textual.binding import Binding

class TableApp(App):
    BINDINGS = [
        Binding("delete", "remove", "Удалить строку"),  # Покажется в Footer
    ]

    def compose(self) -> ComposeResult:
        t = DataTable(zebra_stripes=True, id="table")
        t.add_columns("ID", "Name", "Status")
        t.add_rows([("1", "Alpha", "Open"), ("2", "Beta", "Closed")])
        yield t
        yield Footer()

    def on_data_table_row_selected(self, e: DataTable.RowSelected):
        # Срабатывает при изменении выбранной строки (стрелками/кликом)
        self.log("Row selected:", e.cursor_row)

    def action_remove(self):
        # Удаляем текущую строку
        t = self.query_one("#table", DataTable)
        row = t.cursor_row
        if row is not None:
            t.remove_row(row)
            self.log(f"Removed row {row}")
````

### C) Фильтр списка в реальном времени
````python
from textual.app import App, ComposeResult
from textual.widgets import Input, ListView, ListItem, Label

class FilterList(App):
    items = [f"Item {i}" for i in range(50)]

    def compose(self) -> ComposeResult:
        yield Input(placeholder="Фильтр...", id="filter")
        # Сохраняем ссылку на ListView для быстрых обновлений
        self.lv = ListView(*(ListItem(Label(x)) for x in self.items), id="lv")
        yield self.lv

    def on_input_changed(self, e: Input.Changed):
        # Простой регистронезависимый фильтр
        q = e.value.lower()
        self.lv.clear()
        for x in self.items:
            if q in x.lower():
                self.lv.append(ListItem(Label(x)))
````

### D) Модульный кастомный виджет с реактивностью (StatusBadge)
````python
from textual.widgets import Static
from textual.reactive import reactive

class StatusBadge(Static):
    status = reactive("ok")  # ok|warn|error

    def on_mount(self):
        self.update(self.status)  # Первый рендер
        self._apply_style()

    def watch_status(self, old, new):
        # Изменение статуса => обновляем текст и классы
        self.update(new)
        self._apply_style()

    def _apply_style(self):
        # Сбрасываем предыдущие классы и применяем текущий
        for cls in ("ok", "warn", "error"):
            self.set_class(False, cls)
        self.set_class(True, self.status)
````

TCSS:
````css
StatusBadge { padding: 0 1; border: round #666; }
StatusBadge.ok { background: green; color: black; }
StatusBadge.warn { background: yellow; color: black; }
StatusBadge.error { background: red; color: white; }
````

### E) Панель статуса + прогресс фоновой задачи
````python
from textual.app import App, ComposeResult
from textual.widgets import Static, ProgressBar, Button
from textual.containers import Vertical
import asyncio

class LoaderApp(App):
    def compose(self) -> ComposeResult:
        with Vertical():
            yield Static("Статус: Ожидание", id="status")
            yield ProgressBar(total=100, id="pb")
            yield Button("Запустить", id="run")

    def on_button_pressed(self, e: Button.Pressed):
        if e.button.id == "run":
            # run_worker гарантирует, что UI не зависнет
            self.run_worker(self._job(), exclusive=True)

    async def _job(self):
        pb = self.query_one("#pb", ProgressBar)
        status = self.query_one("#status", Static)
        for _ in range(100):
            await asyncio.sleep(0.03)       # Имитация прогресса
            # Обновляем UI-поток из воркера безопасно
            self.call_from_thread(pb.advance, 1)
        self.call_from_thread(status.update, "Статус: Готово")
````

### F) Маршрутизация разделов через ContentSwitcher
````python
from textual.containers import ContentSwitcher
from textual.widgets import Button, Static

def compose(self):
    # Две кнопки управляют "роутером"
    yield Button("Домой", id="home")
    yield Button("Настройки", id="settings")
    with ContentSwitcher(initial="home", id="router"):
        yield Static("Главная", id="home")
        yield Static("Параметры", id="settings")

def on_button_pressed(self, e: Button.Pressed):
    # Устанавливаем текущий "маршрут" по id
    self.query_one("#router", ContentSwitcher).current = e.button.id
````

---

## 21) Производительность: рекомендации
- Сведите к минимуму ручные refresh(): пусть reactive/watch_* управляют перерисовкой.
- Для больших наборов данных используйте DataTable (виртуализация строк).
- Обновляйте только ту часть UI, которая реально изменилась (точечные update()).
- Все длительные операции — через воркеры; UI обновляйте через call_from_thread.

---

## 22) Дополнительно: плагины, темы, локализация
- Темизация через переменные TCSS и отдельные файлы тем (например, dark.tcss).
- Локализация: храните строки в словарях/файлах, меняйте в рантайме через reactive.
- Плагинообразность: выделяйте виджеты в независимые модули с публичными API.

---

## 23) Ресурсы и ссылки
- Документация: textual.textualize.io
- Репозиторий и примеры: github.com/Textualize/textual
- Сообщество и помощь: Discord Textualize