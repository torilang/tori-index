# 3. Widgets

All widgets go into the current column or row (see [chapter 2](02-layout.md)). A widget that changes something returns a `bool`: `true` — it was pressed or changed in this very frame.

## Text

| Call | What it draws |
| :--- | :--- |
| `ui.label(s)` | a line in the main font |
| `ui.note(s)` | a smaller dimmed caption |
| `ui.heading(s)` | a heading in the bold font |
| `ui.para(s)` | a paragraph: wrapped by words to the column width, `\n` is a new line |
| `ui.styled(s, font, size, color)` | a line in any font, size and color; `font` — `null` for the main one |
| `ui.separator()` | a thin line |

Strings are UTF-8, in any language (see [chapter 4](04-text.md)).

## Buttons

| Call | What it does |
| :--- | :--- |
| `ui.button(s) -> bool` | an ordinary button |
| `ui.primary(s) -> bool` | the main button, in the accent color |
| `ui.image_button(img, s) -> bool` | a button with an image left of the text; with `s == ""` — a square one, image only |

A button fires when the mouse button is released over that same button: press, move away, release — doesn't count.

```tori
use shoji

fn main() {
    ui := shoji.open("Buttons", 360, 200)
    var n = 0
    for ui.frame() {
        ui.panel("", 20, 20, 320, 160)
        ui.row(2)
        if ui.primary("Plus") { n += 1 }
        if ui.button("Reset") { n = 0 }
        ui.heading(shoji.num(n))
        ui.end()
    }
}
```

## Checkboxes, toggles, options

| Call | What it does |
| :--- | :--- |
| `ui.checkbox(s, &b) -> bool` | a checkbox; a click flips `b` |
| `ui.toggle(s, &b) -> bool` | the same as a toggle switch |
| `ui.radio(s, &v, value) -> bool` | one option out of several: a click sets `v = value`, the one with `v == value` is marked |

`checkbox` and `toggle` return `true` if `b` changed; `radio` — if it was clicked.

```tori
use shoji

fn main() {
    ui := shoji.open("Choice", 360, 260)
    var hints = true
    var quality = 1
    for ui.frame() {
        ui.panel("Settings", 20, 20, 320, 220)
        ui.checkbox("Hints", &hints)
        ui.note("Quality")
        ui.radio("Low", &quality, 0)
        ui.radio("Medium", &quality, 1)
        ui.radio("High", &quality, 2)
        ui.end()
    }
}
```

## Numbers

| Call | What it does |
| :--- | :--- |
| `ui.slider(&v, lo, hi) -> bool` | a slider for the integer `v` from `lo` to `hi`, the value on the right. Drag it with the mouse; `true` while the value is changing |
| `ui.progress(v, max)` | a progress bar: `v` out of `max` filled |

## Lists and tabs

The options are written as one string separated by `|`: `"English|Deutsch|日本語"`. The index of the selected one is in an `i64` variable, starting from zero.

| Call | What it does |
| :--- | :--- |
| `ui.dropdown(&v, options) -> bool` | a dropdown list. Opens on a click and is drawn on top of everything; if there's no room below, it opens upwards. `true` — an option was picked |
| `ui.tabs(&v, options) -> bool` | tabs in a row. `true` — switched |

`shoji.count(options)` is the number of options, `shoji.item(options, k)` is the `k`-th of them.

```tori
use shoji

fn main() {
    ui := shoji.open("Tabs", 380, 240)
    var tab = 0
    var lang = 0
    for ui.frame() {
        ui.panel("", 20, 20, 340, 200)
        ui.tabs(&tab, "General|Language")
        if tab == 0 {
            ui.label("General settings")
        } else {
            ui.dropdown(&lang, "English|Deutsch|日本語")
            ui.note(shoji.cat("selected: ", shoji.item("English|Deutsch|日本語", lang)))
        }
        ui.end()
    }
}
```

## Text field

A field's text is kept in a `*Text` — a string of up to 255 bytes of UTF-8:

| Call | What it does |
| :--- | :--- |
| `shoji.text_new(s) -> *Text` | a new string with initial text |
| `t.get() -> str` | the current text |
| `t.set(s)` | replace the text |
| `t.n` | length in bytes |
| `ui.input(t, hint) -> bool` | a text field; the hint shows in gray while the field is empty. `true` — the text changed |

A click on the field puts the cursor in it; a click elsewhere, Enter or Esc removes it. ←, →, Home, End, Backspace and Delete work. You can type in any language and any layout (see [chapter 4](04-text.md#input-in-any-language)).

```tori
use shoji

fn main() {
    ui := shoji.open("Name", 360, 200)
    name := shoji.text_new("")
    for ui.frame() {
        ui.panel("Introductions", 20, 20, 320, 160)
        ui.input(name, "What's your name?")
        if name.n > 0 { ui.label(shoji.cat(shoji.cat("Hello, ", name.get()), "!")) }
        ui.end()
    }
}
```

`ui.focus` is the number of the widget with the cursor, `0` — none. Set `ui.focus = 0` to remove the cursor from code.

## Images

`ui.image(img, h, radius)` and `ui.image_button(img, s)` are covered in [chapter 5](05-images.md).

## Raw keyboard and mouse

| Call | What it gives |
| :--- | :--- |
| `ui.key(KEY_…)` | the key was pressed in this frame: `ui.key(KEY_ESC)`, `ui.key('s')` |
| `ui.held(KEY_…)` | the key is held down |
| `ui.mouse_x()`, `ui.mouse_y()` | the mouse in logical page coordinates |
| `ui.time()` | milliseconds since the window opened — for animations |
| `ui.win` | the window itself (`*Window`): `ui.win.text`, `ui.win.mb`, `ui.win.wheel` and the other fields of the built-in window |

Key codes are physical keys, as with the built-in `window()`: in a Russian layout the key labeled «Ф» is still `'a'`. shoji takes Ctrl + `=`, `-`, `0` and Ctrl + wheel for zoom.

Next: [Text, fonts and languages →](04-text.md)
