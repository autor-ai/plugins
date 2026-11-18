# Design craft

What separates a design people keep from one they regenerate. Use it for templates, sketch renders and format adaptations. The brand kit always wins over this file: its tokens, fonts, guidance and voice are the brief.

## Contents

- Start from the brand
- Hierarchy
- Type
- Layout and spacing
- Color and contrast
- Images
- Copy that varies
- Platform safe zones
- Looks to avoid
- Check before you show

## Start from the brand

- Use the kit's colors, fonts and logo files only. If the kit has no font files, say so instead of loading a similar web font.
- Read the kit's design guidance and voice before the first draft, not after the review.
- Match the brand's existing work when the kit links to it: density, corner radius, photo style, how much it uses its accent color.

## Hierarchy

- One thing is clearly first. Usually the headline or the product image, never both at the same size.
- Three levels are enough for most formats: the message, the support, the small print (logo, call to action, legal).
- Make the difference between levels obvious: a headline 2–3× the body size, not 1.2×.
- The call to action looks tappable on social formats and clickable in email: a button shape or a clear underline, not plain text.

## Type

- Two families at most, usually the kit's heading and body fonts. Weight and size do the rest.
- Use a scale, not one-off sizes. For a 1080px-wide frame: around 96–140px headlines, 40–56px subheads, 28–36px body, 22–26px small print. Scale the set together for other sizes.
- Tighten large headlines (`letter-spacing: -0.01em` to `-0.03em`, `line-height: 0.95–1.1`). Keep body text at `line-height: 1.3–1.5`.
- Keep lines readable: about 20–45 characters per line for social copy and 50–75 for email body.
- Avoid widows and awkward breaks in headlines with `text-wrap: balance`.

## Layout and spacing

- Pick a margin and keep it: around 6–8% of the frame's width on social formats. Nothing important touches the edge.
- Space with a few steps from a small set (for example 8, 16, 24, 40, 64px), not ad hoc values.
- Align to edges and to each other. A layout with three alignments looks accidental; one or two looks designed.
- Leave real empty space. Filling every corner reads as an ad template.
- Group related things closer together than unrelated ones.

## Color and contrast

- Text needs 4.5:1 contrast against what is behind it (3:1 for text over 40px). Check text over photos at the busiest part of the image.
- Over photos, use a scrim or gradient behind the text, or place the text where the image is calm.
- Use the accent color for one or two things that matter, not as a background for everything.
- Dark and light variants are separate choices: check both if the template has a theme field.

## Images

- Choose crops for the message: faces and products away from where text sits.
- Expect other photos: a template must still work when the person swaps in a different crop, shape or subject. Set a sensible default `#autor-position` and use `object-fit: cover`.
- Never stretch an image or a logo. Logos keep their clear space and sit on a background they were made for.

## Copy that varies

- Design for the longest realistic copy, not the placeholder. Before publishing, try a long headline, a short one and a two-line one.
- Use CSS that adapts: `text-wrap: balance`, `line-clamp`, `font-size: clamp(…)` or fitted font sizes, `min-height` instead of fixed heights.
- Lists (array fields) must look right with 1 item and with the most you expect.
- Numbers, prices and dates need room for their longest form.

## Platform safe zones

Keep text and logos out of the areas apps cover. The numbers are approximate and the apps change them, so leave a margin beyond them:

| Format | Size | Keep clear |
| --- | --- | --- |
| Instagram and Facebook story or reel | 1080×1920 | Top 250px and bottom 340px |
| TikTok | 1080×1920 | Top 160px, bottom 480px, right 140px |
| Instagram feed | 1080×1350 or 1080×1080 | The center square stays readable when the grid crops 4:5 to 3:4 |
| LinkedIn and X image | 1200×627 or 1600×900 | Nothing small: these show at under 600px wide |
| Email | 600px wide | Key message in the top 300px; real text, not text inside images |

## Looks to avoid

These make a design look machine-made:

- A purple-to-blue gradient, or any gradient the brand doesn't use.
- Everything centered, at the same weight, with an emoji.
- Glassmorphism cards, glow and drop shadows on everything.
- Stock 3D shapes, generic blobs and decorative icons that say nothing.
- Every element in a rounded card.
- Three options that are the same layout in different colors.

## Check before you show

Preview every frame, then check:

- [ ] Is it obviously this brand, with the logo off?
- [ ] Is there one clear first thing?
- [ ] Does anything overflow, clip, or touch an edge?
- [ ] Is all text readable at the size people see it (a phone, a feed thumbnail)?
- [ ] Does it survive the longest copy and a different photo?
- [ ] Is it clear of the platform's safe zones?

Fix what fails before showing it. Tell the person what you could not check.
