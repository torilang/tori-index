# shoji

User interfaces for [Tori](https://github.com/torilang/tori): panels, buttons, checkboxes, toggles, sliders, dropdowns, text fields, tabs, text in vector fonts, PNG images, rounded corners and soft shadows. HiDPI scaling (100 %, 150 %, 200 %…), resizable windows, input in any language. Works on Linux and Windows: everything is drawn in software straight into the pixels of a `window()`, with no GTK, Qt or any other library.

![showcase](preview/screenshot.png)

**Documentation:** [docs/](docs/README.md) — getting started, layout, widgets, fonts and languages, images, scaling, themes, drawing and the full reference.

```bash
nest install shoji
```

```tori
use shoji

fn main() {
    ui := shoji.open("Hello", 480, 320)
    var n = 0
    var on = true
    for ui.frame() {
        ui.panel("Counter", 20, 20, 440, 280)
        if ui.primary("More") { n += 1 }
        ui.label(shoji.cat("Pressed: ", shoji.num(n)))
        ui.toggle("Dark theme", &on)
        ui.end()
    }
}
```

## Showcase

```bash
cd ~/.tori/packs/shoji/preview      # Windows: %USERPROFILE%\.tori\packs\shoji\preview
tori run main.tr
```

It has every widget, dark and light themes, accent colors, custom fonts (Fira Sans) and images. The window is resizable: panels flow into three, two or one column, and whatever doesn't fit scrolls with the wheel. Ctrl + wheel changes the scale.

## How it works

**Immediate mode.** Every frame the program describes the window anew, and a widget answers right away what was done to it: `if ui.button("OK") { … }`. There are no callbacks and no widget objects; the state is your own variables: `ui.checkbox("Sound", &sound)` flips `sound` by itself.

**The loop.** `for ui.frame() { … }` shows the previous frame, waits ~1/60 s, processes the mouse and keyboard and starts a new frame. The loop ends when the window is closed.

**Layout.** Widgets stack in a column inside `ui.panel(…)` / `ui.area(…)` until `ui.end()`. `ui.row(n)` puts the next `n` widgets in a row, sharing the width equally.

**Scaling.** All coordinates and sizes are in logical pixels. On screen they are multiplied by the scale: the system DPI (on Linux — `Xft.dpi`, set by the GNOME and KDE scaling settings; on Windows — the system one) × the user's zoom. Zoom is Ctrl + wheel, Ctrl + `=` / `-` / `0` or `ui.set_zoom(150)`. Fonts and rounded corners are re-rasterized for the size rather than stretched, so they stay sharp.

**Responsive.** The window from `shoji.open` is resizable. `ui.width()` and `ui.height()` are its size in logical pixels; you can rearrange the layout from them every frame. `ui.page_height(h)` sets the page height: if it's taller than the window, the page scrolls with the wheel, PgUp/PgDn and the bar on the right.

**Fonts.** Anti-aliased TrueType and OpenType vector fonts: `.ttf`, `.otf` (CFF outlines) and `.ttc` collections. A glyph is rasterized once per size and then taken from the cache. Noto Sans is built in: Latin, Cyrillic and common symbols. Load your own with `shoji.load_font("file.ttf")`. When a font lacks a character, shoji looks in the fallbacks: first your own (`shoji.fallback(f)`), then the system fonts — Noto, DejaVu, WenQuanYi on Linux, Segoe UI, Microsoft YaHei, Malgun Gothic and others on Windows. So Greek, Georgian, Armenian, Hebrew, Thai, Chinese, Japanese and Korean text shows up with no setup.

**Input in any language.** A text field takes the typed characters from the window (`win.text`) with the layout already applied: English, German, Russian, Greek or any other, Caps Lock, AltGr, dead keys (´ + e = é). Text is selected with the mouse or Shift + arrows, and Ctrl+A / C / X / V work with the system clipboard. On Windows input methods (IME) for Chinese, Japanese and Korean work too.

**Images.** PNG of any color type, depth 1–16 bits, with transparency; interlaced files are not supported. Scaling is bilinear, and the final size is cached.

## Reference

### Window and frame

| Call | What it does |
| :--- | :--- |
| `shoji.open(title, w, h) -> *Ui` | a `w`×`h` logical-pixel window and a UI for it; the window is resizable |
| `shoji.attach(win) -> *Ui` | a UI for an already open `window()` |
| `ui.frame() -> bool` | the next frame; `false` — the window was closed |
| `ui.begin()`, `ui.finish()` | the same by hand, if you have several windows and call `frame()` yourself |
| `ui.dark()`, `ui.light()` | the theme |
| `ui.accent(0xRRGGBB)` | the accent color |
| `ui.set_font(f)`, `ui.set_bold(f)` | the main font and the heading font |
| `ui.th` | the whole theme (`Theme`): colors, `radius`, `size`, `title`, `pad`, `gap`, `h` — all changeable |
| `ui.time()` | ms since the window opened |
| `ui.width()`, `ui.height()` | the window size in logical pixels |
| `ui.scale()` | the scale, %: DPI × zoom |
| `ui.set_zoom(%)`, `ui.zoom` | the user's zoom (100 — as designed, 50 to 400) |
| `ui.page_height(h)` | the page height: whatever is below the window scrolls |
| `ui.px(v)` | logical pixels -> screen pixels (for `ui.canvas()`) |
| `ui.mouse_x()`, `ui.mouse_y()`, `ui.key(KEY_…)`, `ui.held(KEY_…)` | raw input |

### Layout

| Call | What it does |
| :--- | :--- |
| `ui.panel(title, x, y, w, h)` | a panel with rounded corners and a shadow; the title may be `""` |
| `ui.area(x, y, w)` | a column without a background |
| `ui.end()` | the end of a panel or column |
| `ui.row(n)` | the next `n` widgets go in a row |
| `ui.space(px)` | spacing |
| `ui.next(h) -> Rect` | room for your own widget of height `h` (logical pixels) |

### Widgets

| Call | Result |
| :--- | :--- |
| `ui.label(s)`, `ui.note(s)`, `ui.heading(s)` | text, a dimmed caption, a heading |
| `ui.para(s)` | a paragraph with word wrapping |
| `ui.styled(s, font, size, color)` | text in any font (`null` — the main one) |
| `ui.button(s) -> bool` | a button; `true` — it was pressed |
| `ui.primary(s) -> bool` | a button in the accent color |
| `ui.image_button(img, s) -> bool` | a button with an image; `s == ""` — a square one, image only |
| `ui.checkbox(s, &b) -> bool` | a checkbox; `true` — it changed |
| `ui.toggle(s, &b) -> bool` | a toggle |
| `ui.radio(s, &v, value) -> bool` | one of several options |
| `ui.slider(&v, lo, hi) -> bool` | a slider with the value on the right |
| `ui.progress(v, max)` | a progress bar |
| `ui.dropdown(&v, "A\|B\|C") -> bool` | a dropdown; `v` — the index of the selected item |
| `ui.tabs(&v, "A\|B\|C") -> bool` | tabs |
| `ui.input(t, hint) -> bool` | a text field in any language with selection and Ctrl+A / C / X / V; `t := shoji.text_new("")`, the text is `t.get()` (up to 255 bytes of UTF-8) |
| `ui.image(img, h, radius)` | an image of height `h` with rounded corners |
| `ui.separator()` | a line |

### Direct drawing

Coordinates are logical, from the top of the page (scrolling included).

| Call | What it does |
| :--- | :--- |
| `ui.rect(x, y, w, h, radius, color)` | a rounded rectangle |
| `ui.text_at(x, y, s, font, size, color) -> width` | text (`font` — `null` for the main one) |
| `ui.picture(img, x, y, w, h, radius)` | an image into a rectangle |
| `ui.text_width(font, size, s)` | string width in logical pixels |
| `ui.canvas()` | a canvas for `shoji/draw`: `draw.poly`, `draw.line`, `draw.shadow`… — in screen pixels, convert with `ui.px` |

### Fonts, images, strings

| Call | What it does |
| :--- | :--- |
| `shoji.load_font(path) -> *Font` | `.ttf`, `.otf`, `.ttc` or `null` |
| `shoji.default_font()` | the built-in Noto Sans |
| `shoji.fallback(f)` | a fallback font for missing characters |
| `shoji.system_fonts(false)` | don't look up missing characters in system fonts |
| `shoji.text_width(font, size, s)` | string width in screen pixels |
| `shoji.load_image(path) -> *Image` | `.png` or `null`; why — `shoji.image_error()` |
| `shoji.num(v)`, `shoji.cat(a, b)` | a number as a string and string joining (the strings live for a few frames) |

## Limitations

* Text runs left to right, without kerning, ligatures or complex script shaping: Arabic letters don't join, Hebrew and Arabic aren't reordered right to left, and Devanagari and other Indic scripts come out without reordering. Latin, Cyrillic, Greek, Armenian, Georgian, Chinese, Japanese and Korean look right.
* There are no input methods on Linux (fcitx, ibus): CJK characters can only be typed on Windows through IME, but they display everywhere.

## Modules

| File | What's inside |
| :--- | :--- |
| `src/lib.tr` | `Ui`, themes, layout, widgets |
| `src/draw.tr` | the canvas, rounded corners, shadows, the anti-aliasing rasterizer, images |
| `src/font.tr` | TrueType and OpenType: `cmap` (formats 4 and 12), `glyf` (composite glyphs included), CFF (Type 2), `.ttc`, fallback fonts, the glyph cache |
| `src/png.tr` | inflate and the PNG decoder |
| `src/font_data.tr` | the built-in font, generated by `tools/fontpack.tr` |
| `src/sys.tr` | memory, files, memory-mapped files |

To rebuild the built-in font from another `.ttf`:

```bash
cd tools && tori run fontpack.tr font.ttf ../src/font_data.tr
```

`fontpack` reads `/proc/self/cmdline`, so it runs on Linux.

## Licenses

The code is MIT. The built-in font is a subset of Noto Sans (SIL Open Font License 1.1, [OFL-NotoSans.txt](OFL-NotoSans.txt)). The showcase fonts, Fira Sans, are SIL OFL 1.1 ([preview/OFL-FiraSans.txt](preview/OFL-FiraSans.txt)).
