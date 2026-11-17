# 7. Themes

The look of the interface is set by the theme — `ui.th`, a `Theme` struct. You can change it at any time, even in the middle of a frame: the following widgets will be drawn the new way.

## Ready-made themes

| Call | What it does |
| :--- | :--- |
| `ui.dark()` | the dark theme (the default) |
| `ui.light()` | the light theme; the accent color is kept |
| `ui.accent(0xRRGGBB)` | the accent color: main buttons, checked boxes, sliders, the border of a focused field |

```tori
use shoji

fn main() {
    ui := shoji.open("Themes", 380, 220)
    var theme = 0
    for ui.frame() {
        ui.panel("Look", 20, 20, 340, 180)
        if ui.tabs(&theme, "Dark|Light") {
            if theme == 0 { ui.dark() } else { ui.light() }
            ui.accent(0x2F8FE0)
        }
        ui.primary("An accent-colored button")
        ui.end()
    }
}
```

`ui.dark()` resets the accent to the default red, so set your own accent again after it.

## Theme fields

Colors are `0xRRGGBB`:

| Field | Where |
| :--- | :--- |
| `bg` | the window background |
| `panel` | the background of panels and dropdown lists |
| `border` | borders, the track of a toggle that's off, lines |
| `text` | the main text |
| `dim` | `note` captions, field hints, slider values |
| `accent` | the accent |
| `on_accent` | text and check marks on the accent |
| `button` | the background of buttons and the selected tab |
| `hover` | the option under the mouse in a dropdown list |
| `press` | reserved: shoji darkens pressed buttons from their own color |
| `field` | the background of text fields, slider tracks and tabs |

Sizes are in logical pixels:

| Field | What it sets | Dark and light |
| :--- | :--- | :--- |
| `radius` | the corner radius | 10 |
| `size` | the main text size | 16 |
| `title` | the heading size | 22 |
| `pad` | the padding inside a panel | 16 |
| `gap` | the gap between widgets | 10 |
| `h` | the height of buttons, fields, lists | 36 |

```tori
ui.th.radius = 4            // almost square corners
ui.th.h = 44                // taller buttons
ui.th.bg = 0x0E1A12         // your own background
```

The ready-made themes overwrite every field, so make your own changes after `ui.dark()` or `ui.light()`.

Next: [Drawing →](08-drawing.md)
