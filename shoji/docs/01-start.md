# 1. Getting started

## Installing

In the project folder:

```bash
nest install shoji
```

nest downloads the package to `~/.tori/packs/shoji` (on Windows — `%USERPROFILE%\.tori\packs\shoji`) and adds it to the project's `nest.toml`. After that `tori run main.tr` and `tori bld main.tr` find shoji by themselves.

To modify the package itself for your project, install it as a copy next to `src`: `nest install shoji --local`.

## The first window

```tori
use shoji

fn main() {
    ui := shoji.open("Hello", 480, 320)
    var n = 0
    for ui.frame() {
        ui.panel("Counter", 20, 20, 440, 280)
        if ui.primary("More") { n += 1 }
        ui.label(shoji.cat("Pressed: ", shoji.num(n)))
        ui.end()
    }
}
```

* `shoji.open(title, w, h)` opens a window and returns a `*Ui` — everything else goes through it. The size is in logical pixels: on a screen at 150 % scale the window is one and a half times larger (see [chapter 6](06-scale.md)). The window can be resized with the mouse.
* `for ui.frame() { … }` is the frame loop. It ends when the window is closed.
* `ui.panel(…)` … `ui.end()` is a panel; the widgets inside it stack in a column.
* `ui.primary("More")` draws a button and answers right away whether it was pressed in this frame.

## Immediate mode

shoji has no widget objects that live on their own, and no callbacks. Every frame the program describes the window anew from top to bottom, and every widget:

1. draws itself;
2. looks at the mouse and keyboard;
3. returns the answer right away: whether the button was pressed, whether the checkbox changed.

The state is your own variables. A widget gets a pointer and changes the variable itself:

```tori
use shoji

fn main() {
    ui := shoji.open("Settings", 360, 220)
    var sound = true
    var volume = 70
    for ui.frame() {
        ui.panel("Sound", 20, 20, 320, 180)
        ui.toggle("On", &sound)
        if sound { ui.slider(&volume, 0, 100) }      // a widget can simply not be drawn
        ui.end()
    }
}
```

This gives a few simple rules:

* **To show or hide a widget**, use a plain `if`.
* **To repeat a widget**, use a plain `for`.
* **To find out what happened**, check what the widget returned: `if ui.button("OK") { … }`.

Widgets are told apart by the order they are drawn in during a frame. If a text field has focus and another widget above it keeps appearing and disappearing, the focus may jump to a neighbor. So it's better to put text fields above whatever changes.

## The frame loop

In one call `ui.frame()`:

1. finishes the previous frame (on top of everything — an open dropdown list, the scrollbar);
2. shows it in the window and waits about 1/60 of a second;
3. processes the mouse and keyboard;
4. starts a new frame: fills the window with the theme background.

It returns `false` when the window has been closed.

The whole frame is redrawn every time, but it's fast: font glyphs, rounded corners and scaled images come from caches.

### Several windows

Each window has its own `*Ui`. `ui.frame()` doesn't fit here: it calls the built-in `frame()` itself, which shows and processes all windows at once. So the frame is driven by hand:

```tori
use shoji

fn main() {
    a := shoji.open("First", 320, 200)
    b := shoji.open("Second", 320, 200)
    var n = 0
    for frame() {
        if !a.win.open || !b.win.open { break }
        a.begin()
        a.panel("", 20, 20, 280, 160)
        if a.button("Plus") { n += 1 }
        a.end()
        a.finish()

        b.begin()
        b.panel("", 20, 20, 280, 160)
        b.label(shoji.num(n))
        b.end()
        b.finish()
    }
}
```

`shoji.attach(win)` gives a `*Ui` for a window opened with the built-in `window()`.

## Strings on the fly

`shoji.num(v)` turns a number into a string, `shoji.cat(a, b)` joins two strings. The result lives in a 64 KB ring buffer for a few frames. That's enough to show a string right away, but you can't keep it for long: for that, copy it into your own buffer or into a `Text` (see [chapter 3](03-widgets.md#text-field)).

Next: [Layout →](02-layout.md)
