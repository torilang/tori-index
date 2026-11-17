# 4. Text, fonts and languages

## Fonts

Text in shoji is vector: font outlines are rasterized with anti-aliasing for the size you need, once, and then the glyph comes from the cache. So text is sharp at any size and scale.

The built-in font is a subset of Noto Sans (Latin, Cyrillic, common symbols); it's embedded in the package and works without any files. Your own font:

```tori
use shoji

fn main() {
    ui := shoji.open("Fonts", 420, 220)
    fira := shoji.load_font("FiraSans-Book.ttf")
    bold := shoji.load_font("FiraSans-Bold.ttf")
    ui.set_font(fira)                      // null is ignored: the built-in one stays
    ui.set_bold(bold)
    for ui.frame() {
        ui.panel("Fira Sans", 20, 20, 380, 180)
        ui.label("The main font")
        ui.styled("Big and colored", bold, 28, ui.th.accent)
        ui.end()
    }
}
```

| Call | What it does |
| :--- | :--- |
| `shoji.load_font(path) -> *Font` | a font from a file or `null` |
| `shoji.default_font()` | the built-in Noto Sans |
| `ui.set_font(f)` | the main font: captions, buttons, fields |
| `ui.set_bold(f)` | the heading font: `heading`, panel titles |
| `ui.font`, `ui.bold` | the current fonts |

Formats:

* `.ttf` — TrueType (`glyf` outlines, composite glyphs included);
* `.otf` — OpenType with CFF outlines, CID fonts like Noto Sans CJK included;
* `.ttc` — a collection: the first font is used.

The file isn't read whole but mapped into memory: even a 20 MB font opens instantly, and only the glyphs you need end up in memory.

## Fallback fonts

If a font lacks a character, shoji looks for it in turn:

1. in the fallback fonts you gave it: `shoji.fallback(f)` (up to 32);
2. in system fonts — by the character's script;
3. if it's found nowhere, it draws the replacement character `�` or `?`.

The system fonts shoji tries:

| Script | Linux | Windows |
| :--- | :--- | :--- |
| Chinese, Japanese, Korean | Noto Sans CJK, WenQuanYi, Droid Sans Fallback | Microsoft YaHei, Yu Gothic, Malgun Gothic, SimSun, MS Gothic, Meiryo |
| Arabic, Hebrew | Noto Sans Arabic / Hebrew, DejaVu Sans | Segoe UI, Arial, Tahoma |
| Devanagari, Bengali, Tamil | Noto Sans Devanagari / Bengali / Tamil | Nirmala UI, Mangal, Vrinda, Latha |
| Thai | Noto Sans Thai | Leelawadee UI, Tahoma |
| Armenian, Georgian | Noto Sans Armenian / Georgian, DejaVu Sans | Segoe UI, Sylfaen |
| everything else | Noto Sans, DejaVu Sans, Noto Sans Symbols | Segoe UI, Arial, Segoe UI Symbol |

On Linux they are looked up in the usual font folders: `/usr/share/fonts/noto`, `/usr/share/fonts/truetype/…`, `/usr/share/fonts/google-noto…`, `/usr/share/fonts/TTF` and others. Each file is opened at most once, and only when a character of its script is needed. To turn off the system lookup: `shoji.system_fonts(false)`.

A glyph from a fallback font sits on the same baseline and at the same size as the main text.

## Input in any language

`ui.input` takes the typed characters from the window (`ui.win.text`), and the window gets them with the layout already applied. So a field accepts everything you can type on a keyboard:

* any layouts and switching between them: English, German, Russian, Ukrainian, Greek, Hebrew…;
* Shift, Caps Lock, Num Lock;
* AltGr: `@` and `€` on a German layout, `ł` on a Polish one;
* dead keys: ´ then e is é, ¨ then u is ü;
* on Windows also input methods (IME) for Chinese, Japanese and Korean.

The cursor and Backspace move by characters, not bytes: «ж» or «é» is erased whole.

## Limitations

* Text runs left to right, without kerning or ligatures. Latin, Cyrillic, Greek, Armenian, Georgian, Chinese, Japanese and Korean look right.
* There's no complex script shaping: Arabic letters don't join, Hebrew and Arabic aren't reordered right to left, and in Devanagari and other Indic scripts the signs aren't reordered.
* There are no input methods on Linux (fcitx, ibus): CJK characters can be shown there but not typed.

## Text width

* `ui.text_width(font, size, s)` — string width in logical pixels (`font` — `null` for the main one).
* `shoji.text_width(font, size, s)` — the same in screen pixels, without the scale.

Next: [Images →](05-images.md)
