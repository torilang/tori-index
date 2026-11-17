# 2. Layout

All coordinates and sizes in shoji are in **logical pixels** from the top-left corner of the page. How they turn into screen pixels is covered in [chapter 6](06-scale.md); for now you can treat them as ordinary pixels.

## The column

Widgets stack in a column from top to bottom inside a **panel** or an **area**:

| Call | What it does |
| :--- | :--- |
| `ui.panel(title, x, y, w, h)` | a panel with rounded corners, a border and a shadow. The title may be `""`. Everything inside is clipped to its edge |
| `ui.area(x, y, w)` | a column of width `w` with no background and no clipping |
| `ui.end()` | the end of a panel or area: the layout continues as it was before it |

A widget's width is the whole column width minus the padding (`th.pad` for a panel). Between widgets there's a `th.gap` gap. Panels and areas nest up to 16 levels deep.

Outside of all panels there's a column too: it spans the whole window with `th.pad` padding.

## Rows

`ui.row(n)` puts the next `n` widgets in a row, splitting the column width equally. After the `n`-th widget the layout goes back to a column. The row height is that of the tallest widget.

```tori
use shoji

fn main() {
    ui := shoji.open("Rows", 420, 200)
    for ui.frame() {
        ui.panel("Dialog", 20, 20, 380, 160)
        ui.label("Save changes?")
        ui.row(3)
        ui.primary("Yes")
        ui.button("No")
        ui.button("Cancel")
        ui.end()
    }
}
```

`ui.row(n)` can also be called in the middle of a row: the unfilled slots are simply dropped, and the next row starts on a new line.

## Spacing and room of your own

| Call | What it does |
| :--- | :--- |
| `ui.space(h)` | skip `h` vertically |
| `ui.next(h) -> Rect` | take room for your own widget of height `h`: a rectangle `x`, `y`, `w`, `h` in logical pixels |

`ui.next` lets you put something of your own into the column, a drawing for example (see [chapter 8](08-drawing.md)):

```tori
use shoji

fn main() {
    ui := shoji.open("Your own room", 360, 240)
    for ui.frame() {
        ui.panel("Stripes", 20, 20, 320, 200)
        r := ui.next(60)
        for i in 0..6 { ui.rect(r.x + i * 50, r.y, 40, r.h, 8, 0x2F8FE0) }
        ui.note("below — ordinary widgets again")
        ui.end()
    }
}
```

## Responsive layout

The window from `shoji.open` is resizable. Its current size in logical pixels is `ui.width()` and `ui.height()`. The layout is described every frame, so adapting to the size is just computing the coordinates from these numbers:

```tori
use shoji

fn main() {
    ui := shoji.open("Columns", 900, 400)
    for ui.frame() {
        W := ui.width()
        var cols = 1
        if W >= 700 { cols = 3 } else if W >= 480 { cols = 2 }
        pw := (W - 20 * (cols + 1)) / cols               // panel width
        for k in 0..3 {
            x := 20 + (k % cols) * (pw + 20)
            y := 20 + (k / cols) * 180
            ui.panel(shoji.cat("Panel ", shoji.num(k + 1)), x, y, pw, 160)
            ui.button("Button")
            ui.end()
        }
    }
}
```

## Page scrolling

If everything doesn't fit in the window, tell shoji the page height:

```tori
ui.page_height(1200)
```

While the page is taller than the window, it scrolls with the mouse wheel, the PgUp and PgDn keys (when no text field has focus) and the bar on the right: you can drag it with the mouse or click beside the thumb. The height can be set every frame — for example, computed from the number of panel rows, as in the [showcase](../preview/main.tr).

All coordinates are from the top of the page, not the window: scrolling shifts them by itself. How far the page is scrolled right now is in `ui.scroll` (logical pixels).

Next: [Widgets →](03-widgets.md)
