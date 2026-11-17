# Reference

All sizes and coordinates in `ui.…` calls are logical pixels (see [chapter 6](06-scale.md)); coordinates are from the top of the page. The functions of the `draw` and `font` modules work in screen pixels.

## The `shoji` package

| Function | What it does |
| :--- | :--- |
| `open(title, w, h) -> *Ui` | a `w`×`h` window and a UI for it; the window is resizable |
| `attach(win) -> *Ui` | a UI for a window from the built-in `window()` |
| `load_font(path) -> *Font` | `.ttf`, `.otf`, `.ttc` or `null` |
| `default_font() -> *Font` | the built-in Noto Sans |
| `fallback(f)` | a fallback font for missing characters (up to 32) |
| `system_fonts(bool)` | look up missing characters in system fonts (yes by default) |
| `load_image(path) -> *Image` | a PNG or `null` |
| `image_error() -> str` | why an image failed to open |
| `text_new(s) -> *Text` | a string for a text field (up to 255 bytes) |
| `num(v) -> str` | a number as a string (lives for a few frames) |
| `cat(a, b) -> str` | joining (lives for a few frames) |
| `count(options) -> i64` | how many options a `\|`-separated string has |
| `item(options, k) -> str` | the `k`-th option |
| `text_width(font, size, s) -> i64` | string width in screen pixels |

## Frame and window

| Call | What it does |
| :--- | :--- |
| `ui.frame() -> bool` | finish the frame, show it, process input, start a new one; `false` — the window was closed |
| `ui.begin()`, `ui.finish()` | start and end a frame by hand (several windows) |
| `ui.width()`, `ui.height()` | the window size |
| `ui.page_height(h)` | the page height: whatever is below the window scrolls |
| `ui.time() -> i64` | ms since the window opened |
| `ui.mouse_x()`, `ui.mouse_y()` | the mouse |
| `ui.key(k)`, `ui.held(k)` | the key was pressed in this frame, is held down |

## Scaling

| Call | What it does |
| :--- | :--- |
| `ui.scale() -> i64` | the scale, %: DPI / 96 × zoom |
| `ui.set_zoom(%)` | zoom from 50 to 400 |
| `ui.px(v) -> i64` | a logical length in screen pixels |

## Look

| Call | What it does |
| :--- | :--- |
| `ui.dark()`, `ui.light()` | a ready-made theme |
| `ui.accent(color)` | the accent color |
| `ui.set_font(f)`, `ui.set_bold(f)` | the main font, the heading font |

## Layout

| Call | What it does |
| :--- | :--- |
| `ui.panel(title, x, y, w, h)` | a panel; the widgets inside form a column until `end()` |
| `ui.area(x, y, w)` | a column without a background until `end()` |
| `ui.end()` | the end of a panel or column |
| `ui.row(n)` | the next `n` widgets go in a row |
| `ui.space(h)` | spacing |
| `ui.next(h) -> Rect` | room for your own widget |

## Widgets

| Call | Result |
| :--- | :--- |
| `ui.label(s)`, `ui.note(s)`, `ui.heading(s)` | text, a caption, a heading |
| `ui.para(s)` | a paragraph with word wrapping |
| `ui.styled(s, font, size, color)` | text in any font |
| `ui.separator()` | a line |
| `ui.button(s) -> bool` | a button: pressed |
| `ui.primary(s) -> bool` | a button in the accent color |
| `ui.image_button(img, s) -> bool` | a button with an image |
| `ui.checkbox(s, &b) -> bool` | a checkbox: changed |
| `ui.toggle(s, &b) -> bool` | a toggle: changed |
| `ui.radio(s, &v, value) -> bool` | an option: picked |
| `ui.slider(&v, lo, hi) -> bool` | a slider: changed |
| `ui.progress(v, max)` | a progress bar |
| `ui.dropdown(&v, options) -> bool` | a dropdown: picked |
| `ui.tabs(&v, options) -> bool` | tabs: switched |
| `ui.input(t, hint) -> bool` | a text field: the text changed |
| `ui.image(img, h, radius)` | an image |

## Drawing

| Call | What it does |
| :--- | :--- |
| `ui.rect(x, y, w, h, radius, color)` | a rounded rectangle |
| `ui.text_at(x, y, s, font, size, color) -> width` | text |
| `ui.picture(img, x, y, w, h, radius)` | an image into a rectangle |
| `ui.text_width(font, size, s) -> i64` | string width |
| `ui.canvas() -> *Canvas` | a canvas for `shoji/draw` and `shoji/font` |

## `Ui` fields

| Field | What it holds |
| :--- | :--- |
| `win` | the window (`*Window`) |
| `th` | the theme (`Theme`), see [chapter 7](07-themes.md) |
| `font`, `bold` | the fonts |
| `zoom`, `dpi` | the zoom, %, and the screen DPI |
| `scroll` | the page scroll |
| `focus` | the number of the text field with the cursor, `0` — none |

## Types

| Type | Fields |
| :--- | :--- |
| `Rect` | `x`, `y`, `w`, `h` |
| `Text` | `n` — length in bytes; `get()`, `set(s)` |
| `draw.Image` | `w`, `h`, `px` (`0xAARRGGBB`) |
| `draw.Canvas` | `px` (`0xRRGGBB`), `w`, `h`, the clip `cx0`, `cy0`, `cx1`, `cy1` |
| `font.Font` | a font: passed to `set_font`, `styled`, `text_at` |

## Package modules

| Module | What's inside |
| :--- | :--- |
| `shoji` | the UI: `Ui`, themes, layout, widgets |
| `shoji/draw` | the canvas, rounded corners, shadows, the anti-aliasing rasterizer, images |
| `shoji/font` | TrueType and OpenType fonts, fallback fonts, the glyph cache, text |
| `shoji/png` | the PNG decoder: `png.decode_png(bytes) -> *Image` |
| `shoji/sys` | memory and files: `sys.read_file`, `sys.write_file`, `sys.map_file`, `sys.ms` |
