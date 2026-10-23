# FLUX.3 [klein] 5.6B — Prompt Guide (official)

**Priority: TIER 3 (good-natured & robust)** · Difficulty: **low–medium**

| Model (`model` param)     | Supported modes                     |
| ------------------------- | ----------------------------------- |
| `flux-3` (`flux-3-klein`) | `text2image`, `image2image`, `edit` |

**Prompt language:** English.

---

## In 30 seconds

1. **Full scene captions, not short prompts.** The model is trained on long captions (medium/style, scene, lighting and camera, each subject with colors, materials and position in the frame). A short prompt sent as typed gives generic output; a one-line edit ("make it blue") reframes the whole shot.
2. **Prompt upsampling is part of the model's design.** Our pipeline runs it automatically when _Use Prompt Upsampling_ is ON (upsampler `flux-3-image-klein-pup`; the upsampled caption replaces your prompt 1:1). Write a clear, specific request either way — with upsampling OFF, what you write is what the model gets, so write the full caption yourself.
3. **Caption order = priority:** main subject → key action → style/medium → setting → lighting and camera → secondary details.
4. **Text in the image:** quote it exactly and say where it goes: `a paper poster on the wall that reads "OPEN LATE"`.
5. **Up to 10 reference images**, referred to by position ("image 1", "image 2", …) or by what they show.

## Text prompts

### Basic structure

`Medium/style → Subject (colors, materials, position) → Setting → Lighting & camera → Atmosphere`

> "Two red pandas resting on bamboo beams, one looking relaxed and the other asleep. Their reddish-brown fur and white facial markings stand out against the soft, blurred natural background, suggesting a peaceful, sunny environment."

> "A two-color risograph narrative illustration with porous ink, soft misregistration, and cheerful simple shapes: a rabbit rides a pumpkin along a winding path through three layers of rolling hills. No text."

### Describe the subject and the frame

Name each subject with its colors, materials and where it sits in the frame; state framing and light (source, quality, direction, temperature). State the medium explicitly for non-photographic looks ("hand-needlefelted wool miniature", "stained-glass illustration", "strict pixel art, 16-color palette").

### Style: describe attributes, not names

For a style, describe the attributes (palette, stroke, texture, material) rather than naming an artwork or artist.

### Typography & design

- Quote every visible word exactly; give title and supporting copy separately and say where each goes.
- Finished design pieces work as a structured brief: art direction (palette, texture, medium) → visual subject → exact title → exact supporting copy → layout constraints ("keep important text at least 5 percent from all edges", "use only the supplied copy").
- Rendered text can still be inaccurate or distorted — keep it short, check the result.

### No text wanted

Say so explicitly ("No text.") — otherwise the model may add lettering.

---

## Image↔Prompt contract (Edit / Multi-Reference)

> **The reference images carry ALL visual details. The prompt only describes what changes, or how elements are combined — never what the images look like.**

For edits the upsampler (or the model's own caption style) writes a complete caption of the reference with the edit folded in. Reference images anchor content; they do **not** lock structure — edits can change content outside the requested region. The output takes the aspect ratio of **image 1**.

### Rules

1. **Delta phrasing:** what changes + target state.
2. **Name the element precisely** enough that only one thing in the image matches ("change the color of only one bird in the middle to #e01075").
3. **Pair "change X" with "keep Y unchanged"** (composition, other elements, lighting, background).
4. **One edit instruction per prompt** (or clearly separated, non-colliding ones).
5. **Text in quotation marks** (also for inserting/changing text).
6. **Multiple references: name the role of each image by position** ("replace the cartridge from image 1 with the van in image 2"). For composites, say how the images relate ("the person from image 1 wears the jacket from image 2 and stands in the kitchen from image 3").
7. Hex colors work as targets (`#CC5500`).
8. Put the image that defines the output shape **first**.

### Patterns

| Edit type              | Prompt pattern                                                          | Example                                                                                                                                                                    |
| ---------------------- | ----------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Recolor one element    | "Change the [element] color to [color]"                                 | "Change the Lamp color to #34eb61"                                                                                                                                         |
| Background change      | "Now they are in [new setting]"                                         | "Now they are in a snowy Japanese landscape with a Japanese Maple tree."                                                                                                   |
| Texture to product     | "Create a [product] using the exact texture from the uploaded image, …" | "Create a blanket using the exact texture from the uploaded image, photographed in a clean studio setup."                                                                  |
| Object swap (2 refs)   | "Replace [X] from image 1 with [Y] in image 2, …"                       | "replace the cartridge from image 1 with the van in image 2, adjust the lighting on the van to integrate well within image 1"                                              |
| Compose (several refs) | "Use image 1 as the main image. Insert [X] from image 2 …"              | "Use image 1 as the main image. Insert the lake from image 2 on the left side of image 1, and make sure it blends naturally with the surroundings"                         |
| Furnish a scene        | "Furnish the exact room in image 1 with: [item] from image 2 …"         | "Furnish the exact room in image 1 with the following items: the bed from image 2 on the left side of the room near the wall. on top of the bed the cushion from image 3." |

Full single-element recolor with explicit preservation:

> "In this image, change the short-eared owl in flight with brown and tan mottled feathers and yellow eyes to: a short-eared owl in flight with striking snowy white and charcoal grey mottled feathers and bright yellow eyes. Keep everything else in the image — composition, other elements, lighting, background — exactly unchanged."

### Good / Bad

- ✅ "Change the color of only one bird in the middle to #e01075" · "Replace the cartridge from image 1 with the van in image 2, adjust the lighting on the van to integrate well within image 1" · "Add dramatic storm clouds to the sky, keep everything else unchanged"
- ❌ "Make it better" · "Make it blue" (reframes the shot) · "Fix the image" · re-describing the reference image

**Do / Don't:**

- ✅ Full scene caption (T2I) · subject, medium and light explicit · exact quoted text · "change X, keep Y" · precise element names · roles by image position
- ❌ Keyword lists · short bare prompts · vague edit commands · re-describing the reference image · multiple contradictory changes in one prompt

### Not available in this pipeline

- **Bounding-box layout** (BFL feature: JSON box list appended to the caption, `<id>` tags, `from`/`src_bbox`/`tgt_bbox` for box edits) has no parameter in `generate_image`. Do not write box syntax into prompts.
- The distilled Klein runs 16 steps without CFG (fixed by the preset). The undistilled **Base** variant (50 steps, CFG) is for fine-tuning/research and is only reachable via `model: custom`.

**Sources:** [FLUX 3 Image Klein — model card (BFL, Hugging Face)](https://huggingface.co/black-forest-labs/flux-3-image-klein) · [FLUX 3 Text to Image (BFL docs)](https://docs.bfl.ai/flux_3/flux3_image_generate) · [FLUX 3 Image Editing (BFL docs)](https://docs.bfl.ai/flux_3/flux3_image_layout) · [black-forest-labs/flux-3-image-klein (reference code)](https://github.com/black-forest-labs/flux-3-image-klein) · accessed 2026-10-19.
