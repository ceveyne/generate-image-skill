# Qwen-Image — Prompt Guide (2512 · Edit 2509 · Edit 2511 · 2.1 · 2.1 Turbo)

**Priority: TIER 1 (Qwen-Image-2.1) and TIER 2 (2512/2509/2511 — older, partly finicky)**

| Model (`model` param)  | Supported modes                     | Notes                                                                                                                            |
| ---------------------- | ----------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| `qwen-image`           | `text2image`, `image2image`, `edit` | Text-to-Image via 2512 checkpoint; edit/image2image via 2509 or 2511 checkpoint (strong text rendering / great edit consistency) |
| `qwen-image-2.1`       | `text2image`, `edit`                | Unified T2I + Edit, up to 10 refs, transparency, local editing; no image2image (no viable strength<1 variant)                    |
| `qwen-image-2.1-turbo` | `text2image`, `edit`                | Same architecture and capabilities as 2.1, accelerated to 8 steps; no image2image (no viable strength<1 variant)                 |

**Prompt language:** English **or Chinese** (both first-class, mixing within a prompt is allowed).

---

## In 30 seconds

1. **Natural-language, detail-rich prose** — no tag spam. The official examples are flowing descriptions.
2. **In-image text = killer feature** (EN+ZH, multi-line, paragraphs): quote the texts plus **position, font, size, color, layout relationship** in the prose.
3. **Edits understand simple direct instructions** ("Turn this cat into a dog") — thanks to the VLM text encoder, far more flexible than older guides suggest.
4. **Choose 2.1 first for everything** — it unifies the capabilities of 2512 (T2I) and 2511 (edit) at equal or better quality, and is the superior choice for stable identities in edits. Older models only as alternatives; 2509 as last resort. Use **2.1 Turbo** when speed matters (8 steps, saved sampling schedule, CFG=1) — same architecture and capabilities as 2.1, so prompting rules below apply unchanged.
5. Negative prompts: 2512 supports them, Edit-2511 officially runs with an empty one — in our pipeline negative prompts are fixed, so this does not affect selection.

---

## Text-to-Image (2512)

### Structure

`Subject + age/appearance → clothing/details → pose → environment/context → lighting → medium/image quality`

Official pattern (from the 2512 model card):

> "A 20-year-old East Asian girl with delicate, charming features and large, bright brown eyes—expressive and lively... She wears a modern, cute dress or relaxed outfit in bright, soft colors—lightweight fabric, minimalist cut. She stands indoors at an anime convention, surrounded by banners, posters, or stalls. Lighting is typical indoor illumination—no staged lighting—and the image resembles a casual iPhone snapshot: unpretentious composition, yet brimming with vivid, fresh, youthful charm."

### Text rendering (strength!)

- Put text in **quotation marks**, describe each text element with **position + style + layout**:
  `At the very center, the title "Habits for Emotional Wellbeing" appears clearly... In the left upper section, "Practice Mindfulness" appears next to a minimalist lotus flower icon, with the short sentence "Be present, observe without judging, accept without resisting".`
- Complex layouts (posters, slides, infographics, timelines): position each element individually (top left, center, "moving downward", "bottom right corner") — the model lays out the composition exactly as specified.
- Bilingual: mix languages freely; Chinese is rendered especially reliably.

### Parameters (official quick start)

50 steps · `true_cfg_scale=4.0` · Native resolutions: 1:1 1328×1328, 16:9 1664×928, 9:16 928×1664, 4:3 1472×1104, 3:4 1104×1472, 3:2 1584×1056, 2:3 1056×1584.
Official negative prompt (anti-AI look, CH): `低分辨率，低画质，肢体畸形，手指畸形，画面过饱和，蜡像感，人脸无细节，过度光滑，画面具有AI感。构图混乱。文字模糊，扭曲。`

---

## Image↔Prompt contract (Edits: 2509 / 2511 / 2.1)

> The input image is the **base**, the prompt is the **instruction**. Local edits change exactly the specified region — **all other regions are kept pixel-identical**.

### The two levels (important for instruction phrasing)

| Level                       | What works                                                                           | Phrasing                                                                                  |
| --------------------------- | ------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------- |
| **Appearance edit** (local) | Add/remove/change an element, rest unchanged                                         | "Remove the watch", "Change the hair to black", "Add a diver to the area marked in white" |
| **Semantic edit** (global)  | IP preservation, object rotation, style transfer — pixels may change, semantics stay | "Turn this cat into a dog", "Same capybara, but in a watercolor style"                    |

### Rules

1. **Direct and imperative.** One clear instruction is enough; over-describing the source image is unnecessary (the VLM encoder sees it itself).
2. **Multiple images:** describe roles and layout in the prompt:
   `The magician bear is on the left, the alchemist bear is on the right, facing each other in the central park square.`
   → Reference images = content source, prompt = **composition + relationship** between them.
3. **Region control:** circles (colored circles, multiple at once), painted annotations, or original + separate mask (two inputs):
   `Remove the metal watch in the blue circle, change the hair in the red circle to black, and replace the area in the green circle with gray short-sleeved linen pajamas.`
4. **Text edit (EN+ZH):** add/remove/change text directly — the original font/size/style is preserved. Misspelled lettering can be fixed **in a chain**: box the region → "fix the character in the red box" → zoom in tighter on remaining errors and repeat.
5. **2.1-specific:**
   - **Transparency:** the prompt decides whether a normal or transparent image comes out ("transparent background", "PNG with transparency"). Transparent images are directly editable, including text on transparent LAYERS.
   - **Fidelity:** explicitly protect people & products (text, textures, shapes) across scene changes: "preserve the product's text, textures, and shape".
   - **Up to 10 reference images** in one composition (e.g., 6 portraits → group photo).
   - **Panoramas, infographics, storyboards** and sequential animation (edit in sequence, rest of the scene preserved).

### Official edit example (2511, multi-image)

```python
image1, image2  # two reference images
prompt = "The magician bear is on the left, the alchemist bear is on the right, facing each other in the central park square."
# 40 steps, true_cfg_scale=4.0, guidance_scale=1.0, negative_prompt=""
```

**Do / Don't:**

- ✅ "Change her dress from blue to burgundy, keep everything else unchanged" · quoted + positioned text · region + action separated
- ❌ Re-describing the start image in the prompt (the encoder sees it) · vague "make it better" · requesting a global style change inside an appearance edit (level mixing)

---

## Model choice within the family

| Task                                                                   | Model                                           |
| ---------------------------------------------------------------------- | ----------------------------------------------- |
| T2I: text posters, bilingual, maximum quality                          | **2.1** (2512 as alternative)                   |
| Edits with >1 reference, local regions, transparency, product fidelity | **2.1** (TIER 1)                                |
| Edits with stable identity (person/product must stay the same)         | **2.1**                                         |
| Classic 1-image edits, lighting control, IP preservation               | **2.1** (2511 as alternative; 2509 last resort) |
| Speed-critical generation/editing (8 steps instead of 40)              | **2.1 Turbo** — identical capabilities to 2.1   |

**Sources:** [Qwen-Image-2.1 Blog](https://qwen.ai/blog?id=qwen-image-2.1) · [Qwen-Image-2512 Model Card](https://huggingface.co/Qwen/Qwen-Image-2512) · [Qwen-Image Blog](https://qwenlm.github.io/blog/qwen-image/) · [Qwen-Image-Edit Blog](https://qwenlm.github.io/blog/qwen-image-edit/) · [Qwen-Image-Edit-2511 Model Card](https://huggingface.co/Qwen/Qwen-Image-Edit-2511) · [Qwen-Image-2.1-Turbo Model Card](https://huggingface.co/Qwen/Qwen-Image-2.1-Turbo) · accessed 2026-10-12
