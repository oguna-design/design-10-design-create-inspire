# Design 10 — "Design. Create. Inspire." 🎨 (CSS values)

Real Figma values, mapped onto **your** wrapper system (`.wrapper`, `.hero-content`, `.image-container`). This one's a **two-column flex layout** (text left, image grid right) — and the right side is your first proper **CSS Grid** (4×4). Flexbox positions the two columns; grid lays out the 16 images.

> No absolute positioning here. `.wrapper` = flex row. `.hero-content` = flex column. `.image-container` = grid. That's the whole layout.

---

## 🎨 Colours

| What | Value |
|---|---|
| Page / `.wrapper` background | `#2f2f2f` (dark grey) |
| `h1` line 1 — `.pink` ("Design.") | `#fec3c7` |
| `h1` line 2 — `.blue` ("Create.") | `#9ad7f3` |
| `h1` line 3 — `.teal` ("Inspire.") | `#6bdfda` |
| `p` subtext | `#ffffff` (white) |
| `.btn` background | `#ffffff` · text `#232323` |
| Image placeholder (behind photos, you won't see it) | `#bea7a7` |

---

## 🔤 Fonts — Poppins (one `@import` at the very top)

```css
@import url("https://fonts.googleapis.com/css2?family=Poppins:wght@400;700&display=swap");
```

| Element | font | weight | size | line-height |
|---|---|---|---|---|
| `h1` (the 3 spans) | Poppins | 700 (Bold) | `72px` | `normal` |
| `p` subtext | Poppins | 400 | `24px` | `normal` |
| `.btn` text | Poppins | 400 | `24px` | `18px` |

---

## 🧱 Layout 1 — `.wrapper` (the two-column flex row)

```css
.wrapper {
  min-height: 100vh;          /* fill the screen so it centres nicely */
  background-color: #2f2f2f;
  display: flex;
  align-items: center;        /* both columns vertically centred */
  justify-content: center;
  gap: 64px;                  /* space between text and grid */
  padding: 40px;
}
```
> Figma puts the text at `left: 132` and the grid at `left: 634`. With flex you don't type those numbers — `justify-content: center` + `gap` places them for you. Same look, no coordinate math.

---

## ◀️ Left column — `.hero-content`

```css
.hero-content {
  display: flex;
  flex-direction: column;
  gap: 24px;                  /* Figma gap between h1, p, button */
  width: 449px;               /* the subtext's width — the column's natural width */
}
```

**The headline** (3 spans, each on its own line → make them `block`):
```css
.hero-content h1 {
  font-family: "Poppins", sans-serif;
  font-weight: 700;
  font-size: 72px;
}
.hero-content h1 span { display: block; }   /* stack the 3 words */
.pink { color: #fec3c7; }
.blue { color: #9ad7f3; }
.teal { color: #6bdfda; }
```
> Spans are `inline` by default (they'd sit side by side). `display: block` makes each take its own line — that's what gives you the 3-line stack.

**Subtext:**
```css
.hero-content p {
  font-family: "Poppins", sans-serif;
  font-size: 24px;
  color: #fff;
}
```

**Button** (white pill):
```css
.btn {
  display: inline-block;      /* so padding + radius behave */
  align-self: flex-start;     /* don't stretch full width — hug the text */
  font-family: "Poppins", sans-serif;
  font-size: 24px;
  color: #232323;
  background-color: #fff;
  padding: 24px 48px;         /* Figma: py 24, px 48 */
  border-radius: 36px;
}
```
> `align-self: flex-start` is the flex trick that stops the button stretching to the full 449px column width — it shrinks to fit "Explore".

---

## ▶️ Right column — `.image-container` (CSS GRID + `.img-box` divs)

16 images in a **4-column grid**. Each image sits in its own `.img-box` div (`172 × 192`, radius `40`) and fills it — the box-wrapper system you use everywhere else.

```css
.image-container {
  display: grid;
  grid-template-columns: repeat(4, 172px);  /* 4 columns, each 172px wide */
  gap: 8px 18px;             /* row-gap 8px, column-gap 18px (Figma's two gaps) */
}
.img-box {
  width: 172px;
  height: 192px;
  border-radius: 40px;
  overflow: hidden;          /* clip the image to the rounded corners */
}
.img-box img {
  width: 100%;               /* fill the box */
  height: 100%;
  object-fit: cover;         /* fill without squashing */
  display: block;            /* kill the phantom gap under the image */
}
```
> The radius + `overflow: hidden` live on the **box**; the img just fills it. Exactly like `.unsplash` in eWatch — one consistent pattern for every image in every design.

> **Two new things worth noticing:**
> 1. `grid-template-columns: repeat(4, 172px)` = "give me 4 columns, each 172px." Drop 16 images in and grid auto-wraps them into 4 rows. No flex-wrap, no counting.
> 2. `gap: 8px 18px` = **row-gap first, column-gap second.** Figma uses a tighter vertical gap (8) than horizontal (18) — grid lets you set them separately in one line. (Flexbox can't split them like this cleanly — that's why grid wins for image walls.)
>
> The images are already saved in **row order** (img-01 = top-left, img-04 = top-right, img-05 = start of row 2…), so the grid reads left-to-right, top-to-bottom and matches the Figma exactly.

---

## 🗺️ Suggested build order

1. `@import` + resets (`* { margin:0; padding:0; box-sizing:border-box }`) + `img { object-fit: cover }`.
2. `.wrapper` — flex row, dark bg, centre it.
3. `.hero-content` — flex column, gap 24 → then `h1` + spans (`block`) → `p` → `.btn`.
4. `.image-container` — `display: grid`, 4 columns, the `8px 18px` gap → size the `img`.
5. Check the grid reads in the right order; tweak `.wrapper` gap if the two halves sit too close/far.

Build your `css/design-10.css` from this and paste it when you want a review — the **grid** (`repeat(4, 172px)` + the two-value `gap`) and **`align-self: flex-start` on the button** are the two things worth getting right here.
