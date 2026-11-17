# shoji documentation

shoji is a UI toolkit for Tori: windows with panels, buttons, text fields, lists, images and vector text. It runs on Linux and Windows from the same source, with no GTK, Qt or other libraries: everything is drawn in software into the window's pixels.

Start with the first chapter; the rest can be read in any order.

| Chapter | About |
| :--- | :--- |
| [1. Getting started](01-start.md) | installing, the first window, the frame loop, how immediate mode works |
| [2. Layout](02-layout.md) | panels, columns, rows, spacing, room for your own widget, responsive layout and scrolling |
| [3. Widgets](03-widgets.md) | buttons, checkboxes, toggles, sliders, lists, tabs, text fields, text |
| [4. Text, fonts and languages](04-text.md) | custom fonts, fallback and system fonts, input in any language, limitations |
| [5. Images](05-images.md) | PNG, images in the layout, image buttons, images anywhere |
| [6. Scaling](06-scale.md) | logical pixels, screen DPI, the user's zoom |
| [7. Themes](07-themes.md) | dark and light themes, the accent, your own colors and sizes |
| [8. Drawing](08-drawing.md) | rectangles, text and images anywhere, the canvas and the `draw` module |
| [Reference](09-reference.md) | every function and field in one list |

A short overview with a screenshot is in the [package README](../README.md); a live example of everything at once is the [showcase](../preview/main.tr).
