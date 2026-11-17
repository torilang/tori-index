# 6. Scaling

Screens come at 100 %, 125 %, 150 %, 200 % scale. A 36-pixel button that looks fine on a regular monitor is tiny on a 4K laptop screen. shoji handles this by itself.

## Logical pixels

Everything a program passes to shoji is in **logical pixels**: the window size in `shoji.open`, panel coordinates, `ui.space`, font sizes in `ui.styled` and `ui.text_at`, image heights, the sizes in the theme. On screen they are multiplied by the scale:

```
scale = screen DPI / 96 × zoom
```

* **Screen DPI.** On Linux — `Xft.dpi`: the scaling settings of GNOME, KDE and Xfce set it. On Windows — the system DPI from "Settings → Display → Scale". 96 is 100 %, 144 is 150 %, 192 is 200 %.
* **Zoom** is what the user picked, 100 % by default.

At 150 % scale `shoji.open("…", 640, 480)` opens a window of 960×720 real pixels, with the same layout inside, only bigger. Text and rounded corners are not stretched but re-rasterized for the new size, so they stay sharp.

## Zoom

The user changes the zoom by themselves:

* Ctrl + mouse wheel;
* Ctrl + `=` — bigger, Ctrl + `-` — smaller, Ctrl + `0` — 100 %.

A program does it like this:

| Call | What it does |
| :--- | :--- |
| `ui.set_zoom(%)` | set the zoom, from 50 to 400 |
| `ui.zoom` | the current zoom |
| `ui.scale()` | the final scale in percent: DPI × zoom |
| `ui.dpi` | the screen DPI |

```tori
use shoji

fn main() {
    ui := shoji.open("Zoom", 360, 200)
    for ui.frame() {
        ui.panel("", 20, 20, 320, 160)
        ui.label(shoji.cat(shoji.cat("Scale ", shoji.num(ui.scale())), " %"))
        ui.row(3)
        if ui.button("A−") { ui.set_zoom(ui.zoom - 10) }
        if ui.button("100 %") { ui.set_zoom(100) }
        if ui.button("A+") { ui.set_zoom(ui.zoom + 10) }
        ui.end()
    }
}
```

As the scale grows, the logical width of the window (`ui.width()`) shrinks: less fits into the same window. If the layout is responsive (see [chapter 2](02-layout.md#responsive-layout)), at a large zoom the panels rearrange into fewer columns by themselves.

## Screen pixels

Screen pixels are only needed when drawing on the `ui.canvas()` directly (see [chapter 8](08-drawing.md)):

* `ui.px(v)` converts a logical length into screen pixels;
* the mouse in screen pixels is `ui.win.mx`, `ui.win.my`.

Everything else in shoji works in logical pixels by itself.

Next: [Themes →](07-themes.md)
