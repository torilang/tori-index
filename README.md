# tori-index

The package index for [Tori](https://github.com/torilang/tori). The `nest` package manager takes packages from here:

```bash
nest install math
```

## Layout

One folder is one package; the folder name is the package name (Latin letters, digits and `_`, not starting with a digit). The folder holds:

* `pack.toml` — who and what:

  ```toml
  [package]
  name = "math"
  author = "your-nickname"
  version = "0.1.0"
  description = "what it does"
  license = "MIT"
  ```

* the package code: `lib.tr` (that's `use name`) and other modules (`use name/module`), in the folder root or in `src/`;
* `nest.toml` with `[deps]`, if the package depends on others.

If the code lives in its own repository, the folder holds only a `pack.toml` with a `git = "https://github.com/…"` line.

## Packages

| Package | What it is |
| :--- | :--- |
| [`math`](math) | integer math: `abs`, `min`, `max`, `pow`, `isqrt`, `gcd`, `lcm` |
| [`shoji`](shoji) | user interfaces: panels, buttons, text fields, TrueType/OpenType fonts, PNG, HiDPI scaling, input in any language |

## Adding a package

Fork the repository, add your package's folder and open a pull request.
