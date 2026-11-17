# 8. Drawing

## The simple way: in logical coordinates

These calls draw right away, on top of whatever is already drawn in the frame. The coordinates are logical, from the top of the page, just like for panels:

| Call | What it draws |
| :--- | :--- |
| `ui.rect(x, y, w, h, radius, color)` | a filled rectangle with (anti-aliased) rounded corners |
| `ui.text_at(x, y, s, font, size, color) -> width` | a line of text; `y` is the top of the line, `font` — `null` for the main one. Returns the width in logical pixels |
| `ui.picture(img, x, y, w, h, radius)` | an image stretched into a rectangle |

Inside a panel the drawing is clipped to its edge, outside — to the window.

```tori
use shoji

fn main() {
    ui := shoji.open("Drawing", 400, 240)
    for ui.frame() {
        th := &ui.th
        ui.rect(20, 20, 360, 200, 16, th.panel)
        ui.rect(40, 40, 60, 60, 30, 0xE8452C)            // radius of half the size — a circle
        w := ui.text_at(120, 50, "Hello", null, 28, th.text)
        ui.text_at(120 + w + 8, 62, "world", null, 16, th.dim)
    }
}
```

## The canvas and the `draw` module

For everything else there's the window canvas and the `shoji/draw` module:

```tori
use shoji
use shoji/draw
```

`ui.canvas()` returns a `*draw.Canvas`: the window pixels `px` (`0xRRGGBB`), the size `w`, `h` and the clip rectangle. The `draw` functions work in **screen pixels**: `ui.px(v)` converts logical lengths, and a logical page `y` becomes `ui.px(y - ui.scroll)`.

| Function | What it does |
| :--- | :--- |
| `draw.fill(c, x, y, w, h, color)` | fill a rectangle |
| `draw.fill_a(c, x, y, w, h, color, a)` | the same with opacity `a` from 0 to 255 |
| `draw.dot(c, x, y, color, a)` | a single pixel |
| `draw.rrect(c, x, y, w, h, radius, color, a)` | a rounded rectangle with anti-aliased corners |
| `draw.rrect_box(c, x, y, w, h, radius, fill, border, t)` | a rounded rectangle with a border of thickness `t` |
| `draw.shadow(c, x, y, w, h, radius, size)` | a soft shadow around a rectangle; drawn before the rectangle itself |
| `draw.line(c, x0, y0, x1, y1, t, color)` | an anti-aliased segment of thickness `t` (the coordinates are `f32`) |
| `draw.poly(c, xs, ys, n, color, a)` | a filled anti-aliased polygon: `n` vertices, `xs`, `ys` are pointers to `f32` |
| `draw.image(c, img, x, y, w, h, radius, a)` | an image into a rectangle |
| `draw.mask(c, x, y, m, mw, mh, color, a)` | an `mw`×`mh` opacity mask (bytes 0–255) in one color |
| `draw.clip(c, x, y, w, h)` | narrow the clip (it never grows wider than the current one) |
| `draw.noclip(c)` | remove the clip: the whole window |
| `draw.mix(bg, color, a)` | a color mix: `a`/255 of `color` |
| `draw.rgb(r, g, b)` | a color from 0–255 channels |

A widget of your own is `ui.next` (room in the layout) plus drawing on the canvas:

```tori
use shoji
use shoji/draw

fn main() {
    ui := shoji.open("Star", 360, 300)
    var xs: [10]f32
    var ys: [10]f32
    for ui.frame() {
        ui.panel("Your own widget", 20, 20, 320, 260)
        r := ui.next(180)                                  // a logical rectangle
        cx := @as(f32, ui.px(r.x + r.w / 2))
        cy := @as(f32, ui.px(r.y + r.h / 2 - ui.scroll))
        big := @as(f32, ui.px(80))
        small := @as(f32, ui.px(34))
        for i in 0..10 {                                   // the star's vertices
            var k = small
            if i % 2 == 0 { k = big }
            a := @as(f32, i) * 0.6283185 - 1.5707963
            xs[i] = cx + k * cos(a)
            ys[i] = cy + k * sin(a)
        }
        draw.poly(ui.canvas(), &xs[0], &ys[0], 10, ui.th.accent, 255)
        ui.end()
    }
}

// sine and cosine by a Taylor series: good enough for the vertices
fn sin(x: f32) -> f32 {
    var v = x
    for v > 3.1415927 { v -= 6.2831853 }
    for v < -3.1415927 { v += 6.2831853 }
    v2 := v * v
    return v * (1.0 - v2 / 6.0 * (1.0 - v2 / 20.0 * (1.0 - v2 / 42.0 * (1.0 - v2 / 72.0))))
}
fn cos(x: f32) -> f32 { return sin(x + 1.5707963) }
```

## Text on the canvas

The `shoji/font` module draws text in screen pixels:

| Function | What it does |
| :--- | :--- |
| `font.text(c, f, size, x, y, color, a, s) -> width` | a line of text; `y` is the top of the line |
| `font.width(f, size, s)` | the string width |
| `font.line_h(f, size)` | the line height |
| `font.ascent(f, size)` | from the top of the line to the baseline |
| `font.fit(f, size, s, w)` | how many bytes of `s` fit into width `w` (on character boundaries) |

The size is in screen pixels: `ui.px(16)` for a logical 16.

Next: [Reference →](09-reference.md)
