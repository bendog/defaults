---
apply: always
---

# Requirements Document for Textual-based TUI Applications

## 1. Introduction

### 1.1 Purpose

This document defines the requirements and best practices for developing Terminal User Interface (TUI) applications using the Textual framework.

### 1.2 Scope

The document covers functional and non-functional requirements for creating TUI applications based on Textual's capabilities and design patterns from the official guide at <https://textual.textualize.io/guide/>.

### 1.3 Background

Textual is a Python framework for building sophisticated terminal user interfaces with modern features like CSS styling, reactive programming, and composable widgets. Applications should follow Textual's reactive and event-driven architecture.

## 2. Core Architecture Requirements

### 2.1 Application Structure

#### 2.1.1 App Class

- Every Textual app must inherit from `textual.app.App`
- The app should define a `compose()` method that returns widgets
- Use `app.run()` to start the application
- Define CSS styling via `CSS_PATH` class variable or `CSS` class variable for inline styles

#### 2.1.2 Screens

- Use `Screen` for different views/pages in your application
- Screens should inherit from `textual.screen.Screen`
- Screens have their own `compose()` method
- Navigate between screens using `app.push_screen()`, `app.pop_screen()`, or `app.switch_screen()`
- Use modal screens for dialogs and overlays

#### 2.1.3 Widgets

- All UI components are widgets
- Widgets should be composable - larger widgets built from smaller ones
- Custom widgets should inherit from `Widget` or specialized widget classes
- Override `compose()` for container widgets that contain other widgets

### 2.2 Composition Pattern

#### 2.2.1 The compose() Method

- Must return `ComposeResult` type hint
- Use `yield` to add widgets (not `return`)
- Widgets are yielded in the order they should appear
- Can yield multiple widgets of the same or different types

```python
def compose(self) -> ComposeResult:
    yield Header(show_clock=True)
    yield DataTable(id="my-table")
    yield Footer()
```

#### 2.2.2 Containers

- Use `Container` from `textual.containers` to group widgets
- Containers help with layout and styling
- Common containers: `Container`, `Vertical`, `Horizontal`, `Grid`, `ScrollableContainer`
- Containers can be nested

### 2.3 Lifecycle Methods

- `on_mount()`: Called when widget is added to the app (good for initialization)
- `on_unmount()`: Called when widget is removed
- `on_show()`: Called when widget becomes visible
- `on_hide()`: Called when widget is hidden
- Always call parent lifecycle methods when overriding: `await super().on_mount()`

## 3. Widget Requirements

### 3.1 Built-in Widgets

#### 3.1.1 Common Widgets

- `Header`: App header with title and optional clock
- `Footer`: Shows key bindings
- `Static`: Display static or dynamic text content
- `Label`: Simple text label
- `Button`: Clickable button with callback
- `Input`: Single-line text input
- `TextArea`: Multi-line text editor
- `DataTable`: Tabular data display with selection
- `ListView`: Scrollable list of items
- `Tree`: Hierarchical tree structure
- `OptionList`: Selectable list of options
- `Select`: Dropdown selection widget

#### 3.1.2 Widget IDs

- Assign unique IDs to widgets using `id` parameter
- Use IDs to query specific widgets: `self.query_one("#my-id", WidgetType)`
- IDs are referenced in CSS with `#` prefix
- IDs should be lowercase with hyphens (kebab-case)

#### 3.1.3 Widget Classes

- Assign CSS classes using `classes` parameter
- Multiple classes can be space-separated: `classes="primary disabled"`
- Classes are referenced in CSS with `.` prefix
- Use classes for styling groups of similar widgets

### 3.2 DataTable Widget

- Add columns with `add_column(label, key=...)`
- Add rows with `add_row(*cells, key=...)`
- Access rows via `table.rows` dictionary
- Update cells with `update_cell(row_key, column_key, value)`
- Use `cursor_row` and `cursor_column` for current position
- Row keys should be unique and meaningful (not just indices)
- Handle selection via custom logic or reactive variables

### 3.3 Custom Widgets

- Create custom widgets by inheriting from `Widget` or `Static`
- Override `compose()` for container widgets
- Override `render()` for custom rendering (returns renderable objects)
- Use reactive attributes for state that should trigger updates
- Document widget parameters and usage

## 4. CSS Styling Requirements

### 4.1 CSS File Organization

#### 4.1.1 File Structure

- Store CSS in separate `.tcss` files (Textual CSS)
- Reference via `CSS_PATH = "filename.tcss"` in App class
- Can also use inline CSS with `CSS = """..."""` class variable
- CSS files should be in the same directory as the Python module or specified with relative path

#### 4.1.2 Selector Types

- Element selectors: `DataTable { ... }` (applies to all DataTables)
- ID selectors: `#my-id { ... }` (applies to specific widget)
- Class selectors: `.my-class { ... }` (applies to widgets with that class)
- Descendant selectors: `Container > Button { ... }` (direct children)
- Pseudo-classes: `Button:hover { ... }`, `.datatable--selected { ... }`

### 4.2 Layout Properties

#### 4.2.1 Sizing

- Use fractional units for flexible sizing: `height: 1fr;`, `width: 2fr;`
- Fixed units: `height: 10;` (rows/cells), `width: 50;` (columns/cells)
- Percentages: `height: 50%;`, `width: 100%;`
- Auto sizing: `height: auto;`, `width: auto;`

#### 4.2.2 Layout Direction

- `layout: vertical;` (default) - stack widgets vertically
- `layout: horizontal;` - arrange widgets side by side
- `layout: grid;` - grid layout with rows and columns
- Use `grid-size` for grid dimensions: `grid-size: 2 3;` (2 columns, 3 rows)

#### 4.2.3 Spacing

- `padding: top right bottom left;` - internal spacing
- `margin: top right bottom left;` - external spacing
- Can use shorthand: `padding: 1 2;` (vertical horizontal)
- Single value: `padding: 1;` (all sides)

### 4.3 Visual Styling

#### 4.3.1 Colors and Backgrounds

- Use design tokens for consistent theming: `$primary`, `$secondary`, `$background`, `$surface`
- Color variations: `$accent-darken-1`, `$accent-darken-2`, `$accent-lighten-1`
- Direct colors: `color: red;`, `background: blue;`
- ANSI colors: `color: ansi_red;`

#### 4.3.2 Borders

- `border: solid green;` - border with style and color
- `border: panel red;` - use border style "panel"
- Border styles: `none`, `solid`, `dashed`, `dotted`, `double`, `panel`, `thick`, `rounded`
- Individual sides: `border-top`, `border-right`, `border-bottom`, `border-left`

#### 4.3.3 Text Styling

- `text-align: left | center | right;`
- `text-style: bold;`, `text-style: italic;`, `text-style: underline;`
- Combine styles: `text-style: bold italic;`
- `color: <color>;` for text color

### 4.4 Scrolling and Overflow

- `overflow-y: auto;` - enable vertical scrolling when content exceeds height
- `overflow-x: auto;` - enable horizontal scrolling
- `scrollbar-gutter: stable;` - reserve space for scrollbar

## 5. Event Handling Requirements

### 5.1 Event Methods

#### 5.1.1 Naming Convention

- Event handlers use pattern: `on_<widget>_<event>()`
- Widget can be widget type or ID
- Examples: `on_button_pressed()`, `on_input_changed()`, `on_data_table_row_selected()`

#### 5.1.2 Event Parameters

- Handler receives event object with relevant data
- Access event properties: `event.button`, `event.value`, etc.
- Event objects have `stop()` and `prevent_default()` methods
- Events bubble up unless stopped

#### 5.1.3 Common Events

- `Pressed`: Button or key press
- `Changed`: Input value change
- `Clicked`: Mouse click
- `Selected`: Item selection
- `Mounted`: Widget mounted
- `Focus`, `Blur`: Focus changes

### 5.2 Message Handling

- Messages are Textual's event system
- Handle with `on_<message_type>()` methods
- Can post custom messages with `self.post_message(MyMessage())`
- Messages are classes inheriting from `Message`

### 5.3 Reactive Attributes

#### 5.3.1 Definition

- Use `reactive()` to create reactive attributes
- Changes trigger automatic UI updates
- Example: `count: reactive[int] = reactive(0)`

#### 5.3.2 Watchers

- Watch reactive changes with `watch_<attribute>()` methods
- Watchers receive old and new values
- Example: `def watch_count(self, old_value: int, new_value: int) -> None:`

#### 5.3.3 Compute Methods

- Compute derived values with `def compute_<attribute>() -> type:`
- Automatically recompute when dependencies change
- Use `@property` for simple derived values

## 6. Key Bindings Requirements

### 6.1 Binding Definition

#### 6.1.1 BINDINGS Class Variable

```python
BINDINGS = [
    Binding("q", "quit", "Quit"),
    Binding("space", "toggle", "Toggle"),
    Binding("enter", "confirm", "Confirm"),
]
```

- First parameter: key(s) to bind
- Second parameter: action name (without "action_" prefix)
- Third parameter: description shown in footer

#### 6.1.2 Key Notation

- Single characters: `"a"`, `"q"`, `"1"`
- Special keys: `"enter"`, `"escape"`, `"space"`, `"tab"`, `"backspace"`
- Function keys: `"f1"`, `"f2"`, etc.
- Modifiers: `"ctrl+c"`, `"shift+up"`, `"ctrl+shift+a"`

### 6.2 Action Methods

- Define actions with `action_<name>()` methods
- Example: `def action_quit(self) -> None:`
- Actions can be async: `async def action_refresh(self) -> None:`
- Actions are called when bound key is pressed

### 6.3 Binding Priority

- Bindings are checked from focused widget up to app
- Child widget bindings override parent bindings
- Use `priority=True` in Binding for higher priority

## 7. Asynchronous Programming Requirements

### 7.1 Async Methods

- Textual is fully async-compatible
- Use `async def` for methods that need to await
- Common async operations: API calls, file I/O, delays
- Use `await asyncio.sleep()` for delays without blocking UI

### 7.2 Running Async Tasks

- Use `self.run_worker()` to run background tasks
- Workers run concurrently without blocking UI
- Example: `self.run_worker(self.fetch_data(), exclusive=True)`
- Use `exclusive=True` to cancel previous worker of same group

### 7.3 Updating UI from Async Code

- Use `self.call_from_thread()` if updating from different thread
- Most async code in Textual runs in same thread, so direct updates are safe
- Use `self.call_later()` to schedule updates for later

## 8. Best Practices

### 8.1 Code Organization

- Separate concerns: one file per screen or major widget
- Keep `compose()` methods simple and readable
- Extract complex logic into separate methods
- Use type hints consistently

### 8.2 State Management

- Use reactive attributes for widget state
- Keep app-level state in the App class
- Pass data to screens via `__init__` parameters
- Use messages for cross-widget communication

### 8.3 Styling Guidelines

- Prefer external CSS files over inline styles
- Use design tokens for colors to support theming
- Keep styling consistent across the app
- Use meaningful class names that describe purpose

### 8.4 Performance

- Avoid expensive operations in `render()` or `compose()`
- Use workers for long-running operations
- Throttle frequent updates with reactive attributes
- Profile with Textual's built-in tools

### 8.5 Testing

- Test individual widgets in isolation
- Use `async with app.run_test() as pilot:` for testing
- Test key bindings and user interactions
- Mock external dependencies

## 9. Common Patterns

### 9.1 Multi-Screen Navigation

```python
# Push a new screen (can go back)
self.push_screen(DetailScreen(item))

# Switch to a screen (replaces current)
self.switch_screen(LoginScreen())

# Pop current screen (go back)
self.pop_screen()
```

### 9.2 Modal Dialogs

- Use `self.push_screen()` with a screen that has modal appearance
- Handle response with callbacks or return values
- Use `dismiss()` in screen to close it

### 9.3 Dynamic Content Updates

```python
# Update widget content
status = self.query_one("#status", Static)
status.update("New content")

# Update multiple times
for item in items:
    status.update(f"Processing {item}...")
    await asyncio.sleep(0.5)
```

### 9.4 Query Selectors

```python
# Query one widget by ID
table = self.query_one("#data-table", DataTable)

# Query all widgets by type
buttons = self.query(Button)

# Query by class
items = self.query(".list-item")

# Complex queries
panels = self.query("Container > Panel")
```

## 10. Error Handling

### 10.1 Exception Handling

- Wrap risky operations in try-except blocks
- Log errors appropriately
- Show user-friendly error messages
- Provide recovery options when possible

### 10.2 Validation

- Validate user input before processing
- Provide immediate feedback on invalid input
- Use Input validators for real-time validation
- Show clear error messages

## 11. Accessibility

### 11.1 Keyboard Navigation

- Ensure all functionality is keyboard accessible
- Provide sensible tab order
- Document keyboard shortcuts
- Support standard shortcuts when possible

### 11.2 Screen Readers

- Use meaningful widget IDs and labels
- Provide descriptions for complex widgets
- Ensure text contrast is sufficient
- Test with screen readers when possible

## 12. Common Anti-Patterns to Avoid

### 12.1 What NOT to Do

- ❌ Don't call `render()` directly - let Textual handle it
- ❌ Don't modify widgets before they're mounted
- ❌ Don't use blocking operations without `await`
- ❌ Don't create widgets in `__init__` - use `compose()`
- ❌ Don't use indices to track DataTable rows - use row keys
- ❌ Don't forget to call `super()` in lifecycle methods
- ❌ Don't use `time.sleep()` - use `await asyncio.sleep()`
- ❌ Don't create circular dependencies between widgets
- ❌ Don't ignore event bubbling - stop events when appropriate

### 12.2 Memory Management

- Remove event handlers when widgets are destroyed
- Cancel workers when no longer needed
- Don't hold references to removed widgets
- Clean up resources in `on_unmount()`

## 13. Development Workflow

### 13.1 Development Mode

- Run with `--dev` flag for development console
- Use `self.log()` for debug output
- Enable CSS live reload during development
- Use Textual console for debugging

### 13.2 Debugging

- Use `log()` method for debugging messages
- Check Textual console for errors and warnings
- Use breakpoints in event handlers
- Test individual widgets in isolation

## 14. Examples

### 14.1 Complete App Structure

```python
from textual.app import App, ComposeResult
from textual.widgets import Header, Footer, DataTable
from textual.binding import Binding

class MyApp(App):
    CSS_PATH = "app.tcss"

    BINDINGS = [
        Binding("q", "quit", "Quit"),
    ]

    def compose(self) -> ComposeResult:
        yield Header(show_clock=True)
        yield DataTable(id="data-table")
        yield Footer()

    def on_mount(self) -> None:
        table = self.query_one(DataTable)
        table.add_column("Name", key="name")
        table.add_column("Value", key="value")
        table.add_row("Item 1", "100", key=1)

if __name__ == "__main__":
    MyApp().run()
```

### 14.2 Custom Widget Example

```python
from textual.widget import Widget
from textual.reactive import reactive

class Counter(Widget):
    count: reactive[int] = reactive(0)

    def render(self) -> str:
        return f"Count: {self.count}"

    def on_click(self) -> None:
        self.count += 1
```

### 14.3 Screen Navigation Example

```python
class ProcessingScreen(Screen):
    def __init__(self, items: list, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.items = items

    def compose(self) -> ComposeResult:
        yield Header()
        yield Static("Processing...", id="status")
        yield Footer()

    def on_mount(self) -> None:
        self.run_worker(self.process_items())

    async def process_items(self) -> None:
        status = self.query_one("#status", Static)
        for item in self.items:
            status.update(f"Processing {item}...")
            await asyncio.sleep(0.5)
        status.update("Done!")
```

## 15. References

1. Textual Official Documentation: <https://textual.textualize.io/>
2. Textual Guide: <https://textual.textualize.io/guide/>
3. Textual Widget Gallery: <https://textual.textualize.io/widget_gallery/>
4. Textual CSS Documentation: <https://textual.textualize.io/styles/>
5. Textual GitHub Repository: <https://github.com/Textualize/textual>
