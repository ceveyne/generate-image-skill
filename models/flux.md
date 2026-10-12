# FLUX.2 [klein] — Prompt Guide (official)

**Priority: TIER 3 (good-natured & robust)** · Difficulty: **low**

| Model (`model` param) | Supported modes                     |
| --------------------- | ----------------------------------- |
| `flux`                | `text2image`, `image2image`, `edit` |

**Prompt language:** English.

---

## In 30 seconds

1. **"Like a novelist, not a search engine."** Flowing prose, no keyword lists.
2. **No prompt upsampling** on [klein] — what you write is what you get. Write deliberately descriptive.
3. **Light is the most important element** — describe it like a photographer: source, quality, direction, temperature, interaction.
4. **Word order = priority:** most important first (main subject → key action → style → context → secondary details).
5. Lengths: 10–30 words (concepts) · 30–80 (production) · 80–300+ (complex). No filler — every sentence must deliver visual information.

## Text prompts

### Basic structure

`Subject → Setting → Details → Lighting → Atmosphere`

> "A weathered fisherman in his late sixties **stands at the bow of a small wooden boat**, wearing a salt-stained wool sweater, hands gripping frayed rope. **Golden hour sunlight filters through morning mist**, creating a sense of quiet determination and solitude."

### Light (biggest quality lever)

Five dimensions: **source** (natural/artificial/ambient) · **quality** (soft/harsh/diffused/direct) · **direction** (side/back/overhead/fill) · **temperature** (warm/cool/golden/blue) · **interaction** (catches, filters, reflects on surfaces).

- Good: "soft, diffused light from a large window camera-left, creating gentle shadows that define the subject's features"
- Examples: "dramatic side lighting creating deep shadows and highlights" · "golden hour backlighting with lens flare" · "overcast light creating even, shadow-free illumination"

### Optional at the end: style/mood tags

```
[Scene]. Style: Country chic meets luxury lifestyle editorial.
Mood: Serene, romantic, grounded.
```

or a camera reference: `Shot on 35mm film (Kodak Portra 400) with shallow depth of field—subject razor-sharp, background softly blurred.`

### Sensory details

Textures, reflections, atmosphere: "Flaky croissant layers catching soft diffused light" > "croissant on table".

---

## Image↔Prompt contract (Edit / Multi-Reference)

> **The reference images carry ALL visual details. The prompt only describes what changes, or how elements are combined — never what the images look like.**

### Rules

1. **Delta phrasing:** what changes + target state.
2. **Always pair "change X" with "keep Y the same"** (pose, framing, face, clothing…).
3. **One edit instruction per prompt** (or clearly separated, non-colliding ones).
4. **Text in quotation marks** (also for inserting/changing text).
5. **Multiple references: name the role of each image** ("Change image 1 to match the style of image 2").
6. Base variant (model: `custom`, mode `edit`): undistilled, higher diversity — for fine control and as a fine-tuning base; results can differ (more robust in details, slower) than the distilled 9B default.

### Patterns

| Edit type           | Prompt pattern               | Example                                       |
| ------------------- | ---------------------------- | --------------------------------------------- |
| Style transfer      | "Turn into [style]"          | "Reskin this into a realistic mountain vista" |
| Object swap         | "Replace [X] with [Y]"       | "Replace the bike with a rearing black horse" |
| Element replacement | "Replace all [X] with [Y]"   | "Replace all the feathers with rose petals"   |
| Add                 | "Add [X] to [location]"      | "Add small goblins climbing the right wall"   |
| Environment change  | "Change [aspect] to [state]" | "Change the season to winter"                 |

### Good / Bad

- ✅ "Add dramatic storm clouds to the sky" · "Change her dress from blue to deep burgundy, keeping the pose and lighting unchanged" · "Age this portrait by 30 years" · "Change image 1 to match the style of image 2"
- ❌ "Make it better" · "Improve the lighting" · "Make it more professional" · "Fix the image"

**Do / Don't:**

- ✅ Prose · subject first · explicit light · sensory details · "change X, keep Y" · roles for reference images
- ❌ Keyword lists · vague edit commands · re-describing the reference image in the prompt · multiple contradictory changes in one prompt

**Sources:** [Prompting Guide — FLUX.2 [klein] (BFL)](https://docs.bfl.ai/guides/prompting_guide_flux2_klein) · [FLUX Prompting Guide (family)](https://docs.bfl.ml/guides/prompting_summary) · accessed 2026-10-09 — **⚠ re-verified 2026-10-10: both URLs now 404/redirect.** BFL has since restructured its docs around FLUX 3; the [klein]-specific guide no longer resolves and the family guide now documents FLUX 3 Image only (with a pointer to "model sections in Prompt Reference" for FLUX.2/Kontext). The current live FLUX.2 guide is [docs.bfl.ai/guides/prompting_guide_flux2](https://docs.bfl.ai/guides/prompting_guide_flux2) (titled "FLUX.2 [pro] & [max]", not klein-specific). This file's content was not found to contradict that page, but it should be re-verified against current BFL docs next time this model is touched.
