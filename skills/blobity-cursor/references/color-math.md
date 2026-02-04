# Color Math: mix-blend-mode: difference

## The Formula

`mix-blend-mode: difference` computes each RGB channel independently:

```
result = |page_pixel - canvas_pixel|
```

Where:

- `page_pixel` = the color of the page content underneath
- `canvas_pixel` = the color Blobity draws on its canvas (the `color` option)
- `result` = the final visible color the user sees

## Implications

| Page bg           | Canvas color      | Result            | Visual                 |
| ----------------- | ----------------- | ----------------- | ---------------------- |
| `#ffffff` (white) | `#ffffff` (white) | `#000000` (black) | Full inversion — harsh |
| `#ffffff` (white) | `#000000` (black) | `#ffffff` (white) | Invisible — no effect  |
| `#0d1117` (dark)  | `#ffffff` (white) | `#f2eee8` (light) | Clean inversion ✓      |
| `#0d1117` (dark)  | `#000000` (black) | `#0d1117` (same)  | Invisible — no effect  |
| `#ffffff` (white) | `#190a11` (dark)  | `#e6f5ee` (mint)  | Soft tint ✓            |

**Key insight**: to get a **visible** result, the canvas color must differ significantly from the page background. To get a **specific** result color, you need to reverse-calculate the canvas color.

## Reverse Formula

Given a desired result color on a known background, calculate the needed canvas color:

```
canvas_color = |page_bg - desired_result|
```

### Example: Soft Mint on White Background

```
Target:  #e6f5ee (soft mint green)
Page bg: #ffffff (white)

canvas_color = |#ffffff - #e6f5ee|
  R: |0xff - 0xe6| = 0x19 = 25
  G: |0xff - 0xf5| = 0x0a = 10
  B: |0xff - 0xee| = 0x11 = 17

Canvas color: #190a11
```

Verify: `|#ffffff - #190a11| = #e6f5ee` ✓

### Example: Warm Peach on White Background

```
Target:  #f0e0d8 (warm peach)
Page bg: #ffffff (white)

canvas_color = |#ffffff - #f0e0d8|
  R: |0xff - 0xf0| = 0x0f
  G: |0xff - 0xe0| = 0x1f
  B: |0xff - 0xd8| = 0x27

Canvas color: #0f1f27
```

### Example: Cool Lavender on White Background

```
Target:  #ece0f5 (cool lavender)
Page bg: #ffffff (white)

canvas_color = |#ffffff - #ece0f5|
  R: |0xff - 0xec| = 0x13
  G: |0xff - 0xe0| = 0x1f
  B: |0xff - 0xf5| = 0x0a

Canvas color: #131f0a
```

## Lookup Table: Light Mode Canvas Colors

Pre-calculated `color` values for white (`#ffffff`) backgrounds:

| Desired Hover Tint | Canvas Color | Hex Result |
| ------------------ | ------------ | ---------- |
| Soft mint green    | `#190a11`    | `#e6f5ee`  |
| Cool sky blue      | `#190a19`    | `#e6f5e6`  |
| Warm peach         | `#0f1f27`    | `#f0e0d8`  |
| Pale lavender      | `#131f0a`    | `#ece0f5`  |
| Light rose         | `#0a1919`    | `#f5e6e6`  |
| Neutral light gray | `#141414`    | `#ebebeb`  |
| Ice blue           | `#001919`    | `#ffe6e6`  |
| Barely visible     | `#0a0a0a`    | `#f5f5f5`  |
| Medium gray        | `#333333`    | `#cccccc`  |
| Pure black (harsh) | `#ffffff`    | `#000000`  |

## Tooltip Text Color

The same formula applies to `fontColor` — the tooltip text is drawn on the canvas and blended with the page:

| Mode               | fontColor on Canvas | Page bg   | Result            | Visibility                                |
| ------------------ | ------------------- | --------- | ----------------- | ----------------------------------------- |
| Dark mode          | `#0d1117`           | `#0d1117` | `#000000` (black) | Black text on light tooltip bg ✓          |
| Light mode         | `#000000`           | `#ffffff` | `#ffffff` (white) | White text on dark tooltip bg ✓           |
| Light mode (wrong) | `#ffffff`           | `#ffffff` | `#000000` (black) | Black text on dark tooltip bg ✗ invisible |

**Rule**: light mode fontColor should be `#000000`, dark mode should be close to the page background color.

## On Non-White Backgrounds

If your light theme cards have a slightly off-white background (e.g., `#f8fafc`), recalculate:

```
Target: #e6f5ee on #f8fafc bg

canvas_color = |#f8fafc - #e6f5ee|
  R: |0xf8 - 0xe6| = 0x12
  G: |0xfa - 0xf5| = 0x05
  B: |0xfc - 0xee| = 0x0e

Canvas color: #12050e
```

The difference from the pure-white calculation (`#190a11`) is small, so `#190a11` is a safe universal default for light mode.

## Quick Decision Guide

```
Dark background → color: '#ffffff' (always works)
Light background → calculate from target tint, or use '#190a11' (mint default)
Want stronger tint → use canvas color further from page bg
Want subtler tint → use canvas color closer to #000000
Want invisible → use same color as page bg
```
