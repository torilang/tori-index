# 5. Images

## Loading

```tori
img := shoji.load_image("photo.png")
if img == null { say("couldn't open it: ", shoji.image_error()) }
```

| Call | What it does |
| :--- | :--- |
| `shoji.load_image(path) -> *Image` | a PNG image or `null` |
| `shoji.image_error() -> str` | why the last load failed: "no such file", "not a PNG", "corrupted data"… |
| `img.w`, `img.h` | the size in pixels |
| `img.px` | the pixels, `0xAARRGGBB`, `w * h` of them row by row |

PNG of any color type (gray, color, paletted, with transparency) and depth from 1 to 16 bits is supported. Interlaced PNG is not.

Load an image once, before the frame loop, not every frame.

## In the layout

| Call | What it does |
| :--- | :--- |
| `ui.image(img, h, radius)` | an image of height `h`; the width follows the aspect ratio but is no wider than the column (then the height shrinks too). The corners are rounded with the radius |
| `ui.image_button(img, s) -> bool` | a button with an image and a caption; `s == ""` — a square button with just the image |

```tori
use shoji

fn main() {
    ui := shoji.open("Images", 420, 360)
    photo := shoji.load_image("photo.png")
    for ui.frame() {
        ui.panel("Photo", 20, 20, 380, 320)
        ui.image(photo, 180, 16)
        ui.row(3)
        ui.image(photo, 60, 8)
        ui.image(photo, 60, 30)
        ui.image(photo, 60, 0)
        ui.end()
    }
}
```

`null` instead of an image draws nothing, so the program won't crash if the file is missing.

## Anywhere

`ui.picture(img, x, y, w, h, radius)` stretches an image into a `w`×`h` rectangle (logical pixels, from the top of the page) — a logo in the header, for example:

```tori
ui.picture(logo, 24, 18, 44, 44, 0)
```

## Quality and speed

An image is scaled bilinearly, respecting transparency, and the finished copy of the needed size is remembered (up to 64 copies). So the same image at the same size costs a plain pixel copy each frame. A new size — after a scale change, for example — is computed once.

Next: [Scaling →](06-scale.md)
