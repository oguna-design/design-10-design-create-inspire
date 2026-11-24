# Design 10: Responsive, Unguided 🎯

**This time you drive.** Each stage tells you **what should be true when you're done**, not how to get there. Under every stage there's a collapsed **Answer key**. Only open it if you're stuck or when you want to check your work.

> Rule of the day: **add a breakpoint where *your* design breaks**, not where a device list says. You're not required to use 1200 / 640 this time. Let the page tell you.

**Files:**
- HTML: `design-10.html`
- Your original CSS: `css/design-10.css` (don't touch this, it's your backup)

**Test widths for the end:** 1440 · 1100 · 900 · 768 · 640 · 390 · 360

---

## Stage 0: Setup

**Done when:** you have `css/design-10-responsive.css` (a copy of your CSS) and the HTML links to the copy instead of the original. The page should look exactly the same as before.

<details>
<summary>Answer key</summary>

1. Duplicate `css/design-10.css` and rename the copy `design-10-responsive.css`.
2. In `design-10.html`, change the link:

```html
<link rel="stylesheet" href="css/design-10-responsive.css" />
```

**Why:** if you break something, you still have the working desktop version.

</details>

---

## Stage 1: Look first (no code)

Open DevTools → device toolbar (`Ctrl + Shift + M`) and drag the width slowly from 1440 down to 360.

**Done when:** you've written down **every width where something looks wrong**, what goes wrong, and your guess at **which CSS line causes it**.

<details>
<summary>Answer key</summary>

What breaks, in the order you'll see it:

| Around | What goes wrong | The line causing it |
|---|---|---|
| ~1100px | The images turn into tall **pills** | `.img-box { height: 12rem; }` |
| ~768px | The pills turn into **circles** | `.img-box { border-radius: 40px; }` |
| ~768px | The text column gets squeezed into a narrow strip | the `.wrapper` flex row never stacks |
| ~390px | Total collapse: the headline overlaps the images and the paragraph is one word per line | same as above, plus the 60px headline is too big |

**Why the pills:** the grid columns use `1fr`, so the **width** shrinks with the screen. The **height** is locked at `12rem` (192px), so it can't shrink. When width shrinks and height doesn't, you get a pill.

**Why the circles:** a 40px corner on a 200px box is a soft corner. A 40px corner on an 80px box is half the box, which makes a circle. The radius doesn't scale with the box.

**The big lesson:** the first two bugs aren't media query problems. They come from **fixed sizes in your desktop CSS**. Fix the cause first (Stage 2), and you'll need fewer breakpoints.

</details>

---

## Stage 2: Fix the root cause (no media query)

**Done when:** the image boxes keep **the same shape** at every width (no pills, no circles), and the desktop at 1440 still looks the same as before.

Hint, only if you want one: you've used this exact property on design 12's wrapper.

<details>
<summary>Answer key</summary>

In your normal `.img-box` rule (not inside a media query):

```css
.img-box {
  width: auto;
  aspect-ratio: 10 / 11;   /* replaces height: 12rem */
  border-radius: 22%;      /* replaces 40px */
  overflow: hidden;
}
```

**Line by line:**
- `aspect-ratio: 10 / 11` means "however wide you are, be 1.1× that tall." At 1440 each box is about 174px wide × 192px tall, and 174 / 192 ≈ 10 / 11. The desktop looks identical, but now the height **follows** the width instead of being locked.
- **Delete** `height: 12rem`. If you keep it, the fixed height wins over the aspect ratio and you're back to pills.
- `border-radius: 22%` gives a corner that's a **percentage of the box**, so it shrinks with it. 40px ÷ 174px ≈ 22%.

**Check:** drag the width again. The pills and circles are gone. The text squeeze at 768 and the collapse at 390 are still there, and that's what media queries are for.

</details>

---

## Stage 3: The stacking breakpoint

Drag the width down again with the Stage 2 fix in place. Find the width where **two side-by-side columns stop making sense** (the text column gets too narrow or the images get too small).

**Done when:**
- below your breakpoint, the text sits **on top** and the image grid sits **underneath**, both full width
- above your breakpoint, nothing changed
- bonus: on the stacked layout, "Design. Create. Inspire." sits on **one line** (wrapping if it has to), not three

<details>
<summary>Answer key</summary>

**Where:** about **900px**. Below that, the paragraph gets squeezed to ~200px and the images shrink to ~70px. Anything from 860 to 960 is a correct answer. What matters is that you picked it by looking, not from a list.

```css
/* ============== TABLET: 900px ============== */
@media (max-width: 900px) {
  .wrapper {
    flex-direction: column;
    align-items: stretch;
    gap: 48px;
  }
  .hero-content,
  .image-container {
    width: 100%;
  }
  .hero-content h1 {
    flex-direction: row;
    flex-wrap: wrap;
    column-gap: 16px;
    font-size: 52px;
    line-height: 1.2;
  }
  .hero-content p {
    max-width: 520px;
  }
}
```

**Line by line:**
- `flex-direction: column` flips the main axis, so the two children stack top to bottom.
- `align-items: stretch`: your desktop has `align-items: center`, which in a column centers the children **horizontally** and shrinks them. `stretch` makes them full width.
- `width: 100%` overrides your desktop `calc(40% - 64px)` and `calc(60% - 64px)`. Those widths were made for side by side. Stacked, each child gets the whole row.
- `gap: 48px`: on desktop the 64px gap ran **left to right**. Now it runs top to bottom, and 48px is enough.
- **h1:** it was already a flex column (one word per line). `flex-direction: row` puts the words side by side, `flex-wrap: wrap` lets "Inspire." drop down if it doesn't fit, and `column-gap` adds the space between words. You're reusing your flexbox drills on a headline.
- `max-width: 520px` on the paragraph: full width at 900 gives lines that are too long to read comfortably. This keeps it at a readable width.

**Notice:** the grid itself needed **zero changes**. `repeat(4, 1fr)` plus the aspect-ratio from Stage 2 already scale. That's the payoff of fixing the root cause first.

</details>

---

## Stage 4: Phone polish

Go down to 390 and 360.

**Done when:**
- the headline fits comfortably (two lines is fine)
- the page uses the full phone width, without the big 40px side padding or the grey/white strip above
- the button and text feel phone-sized, not desktop-sized
- the gaps between the images look tighter

<details>
<summary>Answer key</summary>

```css
/* ============== PHONE: 640px ============== */
@media (max-width: 640px) {
  .wrapper {
    margin: 0 auto;
    padding: 40px 16px;
    gap: 36px;
  }
  .hero-content {
    gap: 20px;
  }
  .hero-content h1 {
    font-size: 40px;
    column-gap: 10px;
  }
  .hero-content p {
    width: 100%;
  }
  .hero-content .btn {
    font-size: 16px;
    padding: 14px 32px;
  }
  .image-container {
    gap: 8px;
  }
}
```

**Line by line:**
- `margin: 0 auto` removes the 2rem top and bottom margin, so there's no white strip above the dark card on a phone.
- `padding: 40px 16px`: 16px sides is the phone standard (same as design 12). Every pixel of width counts at 360.
- `h1 font-size: 40px` gives "Design. Create." on line 1 and "Inspire." on line 2 at 390. At 52px it would be three cramped lines.
- `p width: 100%` replaces your desktop `width: 90%`, which wastes 10% of an already narrow screen.
- `.btn`: smaller text and padding, same as design 12's phone button.
- `.image-container gap: 8px`: your desktop `10px 18px` gap is too much space between 80px images. One value means rows and columns both get 8px.

</details>

---

## Stage 5: Final test

**Done when:** at **1440 · 1100 · 900 · 768 · 640 · 390 · 360**:
- there's no horizontal scroll
- nothing overlaps
- the images are never pills or circles
- 1440 looks like the original design

<details>
<summary>Answer key</summary>

What you should see:

| Width | Layout |
|---|---|
| 1440 / 1100 | two columns, same as the original desktop |
| 900 and below | stacked: one-line headline, paragraph, button, then the 4×4 grid full width |
| 640 and below | same stack, tighter padding, 40px headline on two lines, 8px image gaps |

**Your final file has 2 media queries (900 + 640) plus 2 base-CSS fixes.** That's the whole job.

</details>

---

## 🏆 Bonus: your first grid breakpoint (optional)

The new skill this design can teach is changing **the grid itself** at a breakpoint.

**Try it:** in your 640 query, make the grid **2 columns** instead of 4. Then compare: 4 small columns (short page, looks like a mosaic) or 2 big columns (bigger images, very long scroll). It's a designer's choice, and there's no wrong answer. Pick one and say why.

<details>
<summary>Answer key</summary>

```css
.image-container {
  grid-template-columns: repeat(2, 1fr);
}
```

That one line turns 4 columns into 2. The 16 images flow into 8 rows automatically, because grid places the items for you.

My pick: keep **4 columns**. With 2 columns the page grows to about 8 rows of 170px images, and the visitor has to scroll a long way to see the whole wall. The mosaic look is the point of the design. You might disagree, and that's fine as long as you can say why.

</details>

---

When you're done, tell me and I'll render your version at all 7 widths and review it.
