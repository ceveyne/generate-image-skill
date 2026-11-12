# 00 · ROUTING — Mode and Model Selection & Prompt Routing

> **How to use:** This file answers the first four routing questions. After the model decision, open **only one** family file (linked in §6).
>
> **Routing chain:** What do I want to achieve? → Which mode do I need? → Which source material is available? → Which model fits best? → How do I write the optimal prompt? (→ family file)
>
> **Pipeline notes (our installation):**
>
> - The agent selects **`mode` + `model`**. `model` is a **name from the tool's `model` description** (e.g. `Krea 2`, `FLUX.2`, `Qwen Image 2.1`; every name stands for a family and the pipeline picks the best installed variant of it, so you never choose between sizes or quantizations), a **model name** (e.g. `Krea 2 Raw`, optionally with a precision such as `(6-bit)`) or a **file name**; `auto` (default) lets the pipeline choose. The `model` description lists what is installed on this machine, each name with the modes it supports, plus the user's own presets (`your presets`). §6 maps every name to its guide. A mode that is not listed for a name is rejected with the alternatives.
> - The tool interface exposes only **`prompt`**; negative prompts are fixed by the pipeline. Models that ignore or need special negative prompts are therefore **not** a selection criterion here.
>
> **How to read the model notes:** The strengths and weaknesses described in this file and in the family files are generalizations from vendor documentation and typical results. They tell you where to _start_, not what a given render looks like. Treat them as a hypothesis before the render and as nothing at all after it: once an image exists, the image is the evidence, and your own reading of it decides — not what these documents say the model usually does. A model rated as weak at something can get it right, and a model rated as strong can get it wrong. Never describe a result by what the model is "known for", and never let the expected outcome stand in for the observed one.

---

## 1. What do I want to achieve? → Mode

| Goal                                                                | Mode                           |
| ------------------------------------------------------------------- | ------------------------------ |
| Image from scratch                                                  | `text2image`                   |
| Image in another / slightly different variant                       | `image2image`                  |
| **Change single elements, keep the rest** (pixel fidelity, regions) | `edit`                         |
| Video from scratch (with audio)                                     | `text2video`                   |
| Bring a still to life (first frame)                                 | `image2video`                  |
| Animate between an exact start **and** end image                    | `image2video` (first+last)     |
| Carry character/style/voice/motion from references into a new scene | `image2video` (reference)      |
| Change an existing video by instruction                             | `image2video` (reference/edit) |

## 2 & 3. Mode + source material → which model can handle it

The agent selects `mode` and `model`. Match the available material to the modes each model supports (model-specific details live in the §6 guides).

| Available material                        | Mode                              | Models                                                                                                                                 |
| ----------------------------------------- | --------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| Text only                                 | `text2image`                      | `Krea 2` · `Z Image` · `FLUX.2` · `FLUX.3` · `Qwen Image` · `Qwen Image 2.1` · `MOSS Image`                    |
| Text only                                 | `text2video`                      | `LTX-2.3` · `MiniMax H3` · `MiniMax H3 [ref2va]`                                                                                                      |
| One image                                 | `image2image`                     | **`Krea 2` (default)** · `Z Image` · `FLUX.2` · `Qwen Image` (only models whose entry in the `model` description lists `image2image`) |
| One image                                 | `edit`                            | **`FLUX.2` (default)** · `FLUX.3` · `Qwen Image 2.1` (identity-critical) · `Qwen Image`                       |
| One image                                 | `image2video` (first frame)       | `LTX-2.3` · `MiniMax H3` · `MiniMax H3 [ref2va]`                                                                                                      |
| 1 canvas + 1 moodboard image              | `image2video` (first+last)        | **`MiniMax H3` only** (images = actual start/end frames)                                                                                  |
| Multiple reference images / video / audio | `edit` (multi-ref)                | `FLUX.3` (up to 10, name each image's role) · `FLUX.2` (name each image's role) · `Qwen Image 2.1` (up to 10) |
| Multiple reference images / video / audio | `image2video` (references)        | **`MiniMax H3 [ref2va]` only** (character/scene/style/motion/camera/voice; ≤12 input files)                                                    |
| Existing video                            | `image2video` (edit/continuation) | **`MiniMax H3 [ref2va]` only**                                                                                                                 |

> Video models (`LTX-2.3`, `MiniMax H3`, `MiniMax H3 [ref2va]`) only support `text2video` and `image2video`. `text2image` / `image2image` are **not available** for them; for a still image use an image model above.

### MiniMax H3 mode map

| Model              | Checkpoint | Image semantics                                  | Possible with                                                                                                                |
| ------------------ | ---------- | ------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------- |
| `MiniMax H3`          | FL2VA      | **Actual frames** (time-anchored)                | T2V (no image) · I2V with **1 canvas image = first frame** · I2V with **1 canvas (first) + 1 moodboard (last)**              |
| `MiniMax H3 [ref2va]`      | Ref2VA     | **References** (identity, style, motion, voice…) | T2V (no image) · I2V with **up to 4 reference images** (canvas + moodboard; **model limit is 9** — 4 is our pipeline choice) |
| Hard limits (both) |            |                                                  | **4–15 s** (integer), 768P local, 2K only via hosted regeneration, prompt ≤ 7000 chars                                       |

**Never** mix the two semantics: frame-anchoring prompts on `MiniMax H3 [ref2va]`, or reference-role prompts on `MiniMax H3`.

## 4. Which model fits best? — Decision rules

**TIER 1 — newest, try first:** MiniMax H3 (video) · FLUX.3 klein (text2image + edit, top prompt adherence) · Qwen-Image-2.1 (unified image + edit) · Krea 2 (text2image, most look-flexible)
**TIER 2 — capable but finicky / legacy:** LTX 2.3 (video) · MOSS Image (text2image; text layout and layout control) · older Qwen generation 2512/2509/2511 — **superseded by 2.1**, which unifies the capabilities of 2512 (T2I) and 2511 (edit) at equal or better quality
**TIER 3 — good-natured & robust, safe fallbacks:** Z-Image Turbo · FLUX.2 klein (**default for `mode: edit`**)

### text2image: look dominates

For `text2image`, the **desired look** overrides all technical decision criteria — it is a **design preference, not a quality marker**. Most look-flexible model: **`Krea 2`** — it can do "beauty shots" like `Z Image` **and** "rough" looks like `FLUX.2`. Only pick a different T2I model for hard technical needs (text rendering, sub-second speed, fixed aesthetic).

### Strong case → model

| Task                                                                                     | First choice                       | Fallback                 | Why                                                                                   |
| ---------------------------------------------------------------------------------------- | ---------------------------------- | ------------------------ | ------------------------------------------------------------------------------------- |
| Character/asset video with references, dialogue, multi-shot, audio                       | **`MiniMax H3 [ref2va]`**                  | `LTX-2.3`                    | Ref2VA: references carry identity/motion/voice; native audio + multi-shot within 15 s |
| Video with exact start **and** end frame                                                 | **`MiniMax H3`** (canvas + moodboard) | `LTX-2.3` (first frame only) | Only FL2VA anchors both frames                                                        |
| Bring one still to life (first frame only)                                               | **`MiniMax H3`**                      | `LTX-2.3`                    | FL2VA first-frame mode                                                                |
| Instruction-based editing of an existing video                                           | **`MiniMax H3 [ref2va]`**                  | –                        | Only Ref2VA does video editing/continuation                                           |
| Atmospheric cinematic, multi-shot cuts, singing/speech, camera language                  | **`LTX-2.3`**                          | `MiniMax H3`                | Best camera/cut language; 2.3 prompt-faithful — but finicky                           |
| Text2image where **look/aesthetic** is the driver                                        | **`Krea 2`**                         | `Z Image` / `FLUX.2`     | Most look-flexible: beauty shots **and** rough looks in one model                     |
| Text2image with **complex multi-element scenes** where exact prompt adherence matters    | **`FLUX.3`**                       | `Qwen Image 2.1`         | Top prompt adherence; needs a full scene caption (or the prompt upsampler)            |
| Text2image with **precise text placement / layout control** (JSON layout, whiteboards, signs) | **`MOSS Image`** | `Qwen Image 2.1` | Layout prompts with bounding boxes; EN+ZH text; see its guide first |
| Text2image with heavy **in-image text** (posters, infographics, slides, bilingual EN/ZH) | **`Qwen Image 2.1`**               | `Qwen Image` (2512)      | Best text rendering in the stack; 2.1 = 2512 capabilities at equal/better quality     |
| **Default `edit`** (style, object swap, "change X keep Y")                               | **`FLUX.2`**                       | `FLUX.3`                 | FLUX.2 is the edit default: good prompt adherence + speed                             |
| **Edit with highest prompt adherence** (complex or multi-part instructions)              | **`FLUX.3`**                       | `FLUX.2`                 | FLUX.3: top prompt adherence, edit quality at least on par with Qwen-Image-2.1        |
| **Edit with stable identity** (person/product must stay the same)                        | **`Qwen Image 2.1`**         | `FLUX.3`                 | 2.1 is the superior choice for identity-preserving edits                              |
| **Edit: pixel fidelity / regional / multi-ref (≤10) / transparency**                     | **`Qwen Image 2.1`**               | `FLUX.3`                 | 2.1: circles/masks, fidelity, up to 10 refs (`FLUX.3` also takes up to 10 refs)       |
| Style discovery, illustration, graphic design, fast variants                             | **`Krea 2`**                         | `Z Image`                | Turbo: fast, aesthetics-strong, 2K                                                    |
| Robust T2I with few demands, sub-second                                                  | **`Z Image`**                      | `FLUX.2`                 | Hard to break, fastest                                                                |

### Strengths / weaknesses — all models

| Model                      | Strengths                                                                                                                                                               | Weaknesses / traps                                                                                                                                 |
| -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| **MiniMax H3 (FL2VA)**     | Exact frame anchoring (first/last/both), native stereo audio, multi-shot ≤15 s, strong instruction following                                                            | **Most rigid syntax** (instruction line + 3 fixed fields, `<d>` tags) — deviations → failure; most work per prompt; **15 s hard cap**              |
| **MiniMax H3 (Ref2VA)**    | Reference generation (character/scene/style/motion/camera/voice), video editing & continuation, ≤9 images + 3 videos + 3 audios                                         | Full 6-section format is heavy; simplified natural language only for 1–3 refs; frame-anchoring is **not** its mode                                 |
| **Qwen-Image-2.1 (Turbo)** | Unified T2I+Edit, **supersedes 2512+2511 at equal/better quality**, best identity stability in edits, up to 10 refs, local regions (circles/masks), native transparency | **Many refs × many variants = time multiplies** (plan batch sizes); prompt controls transparency (can surprise)                                    |
| **Qwen-Image-2512**        | Top text rendering (EN+ZH, layouts, slides)                                                                                                                             | T2I only (no edit); superseded by 2.1 — alternative when 2.1 misbehaves                                                                            |
| **Qwen-Image-Edit 2511**   | Consistent 1-image edits, lighting control, IP preservation, bilingual text edit                                                                                        | Older; multi-ref & regions weaker than 2.1; superseded by 2.1                                                                                      |
| **Qwen-Image-Edit 2509**   | Simple edit instructions                                                                                                                                                | **Finickiest** edit variant (consistency issues) — last resort only                                                                                |
| **Krea 2 (Turbo)**         | **Most look-flexible T2I** (beauty shots → rough looks), fastest style exploration, expressive illustration, 2K, good text rendering, official LLM expander             | Turbo quality (iteration, not final); pixel-fidelity edits are not its thing                                                                       |
| **LTX 2.3**                | Best camera/cut language, multi-shot in prose, strong audio (speech/singing), 2.3 much more prompt-faithful                                                             | **Finickiest model:** prompt length must match clip length, no numeric specs, on-screen text unreliable, chaotic physics = artifacts               |
| **Z-Image Turbo**          | Sub-second, robust, bilingual text rendering, excellent instruction following, "beauty shot" aesthetic                                                                  | 512-token limit; little variation on identical prompt                                                                                              |
| **FLUX.3 klein**           | Top prompt adherence, edit quality at least on par with Qwen-Image-2.1, multi-reference edits with roles (up to 10 refs), in-image text, distilled (16 steps)           | Needs full scene captions (short prompts give generic output; relies on the prompt upsampler when enabled); no bounding-box layout in our pipeline |
| **MOSS Image (SFT)**       | Complex EN+ZH text rendering with automatic line layout, **JSON layout prompts** (`bbox` per element), natural language or tag sequences, 5B single-stream DiT | Needs ≥30–50 steps (not a turbo model); layout prompts are experimental; text accuracy improves with explicit line breaks |
| **FLUX.2 klein**           | **Default for `mode: edit`** — fast; robust, strong lighting understanding, multi-reference edits with roles, Base variant for fine work; predecessor of FLUX.3 klein   | **No prompt upsampling** — vague stays vague; Base ≠ distilled (behaves differently)                                                               |

---

## 5. Two cross-cutting challenges (for agents)

### 5.1 Edits: the image↔prompt contract

Universal principle for all image-based modes — **the material carries identity, the prompt carries the change:**

1. **Never** re-describe the source image in the prompt (the encoders see it; double descriptions collide).
2. Prompt = **delta**: what changes, target state, and (for fidelity models) what explicitly stays ("change X, keep Y").
3. With **multiple** references: **name each image's role in the prompt** (FLUX.2 / FLUX.3: "image 1 / image 2"; Qwen: "the bear on the left…"; MiniMax: `<Picture N>`/`<Subject N>` + time anchor or role).
4. For **regional** edits: separate region and action (Qwen: colored circles + instruction per color).
5. **One** coherent change per prompt; avoid contradictory delta instructions.

→ Model-specific contracts (incl. I2VA/FL2VA/L2VA logic and `<Picture N>` semantics) live in the family files.

### 5.2 Image-to-video: timing & world knowledge

Agents typically underestimate **how long actions take in the real world** — and pack too many beats into a clip. Plausibility reference:

| Action                                         | Plausible duration |
| ---------------------------------------------- | ------------------ |
| Blink                                          | 0.3 s              |
| Head turn / acting beat                        | 0.5–1 s            |
| Speech: ~2–3 words/s → one sentence            | 3–5 s              |
| Sit down / stand up                            | 1–2 s              |
| Door open/close, pour coffee, glass shattering | 1–2 s              |
| Person walking (normal ~1.4 m/s)               | 5 m ≈ 3.5–4 s      |
| Car through frame                              | 2–4 s              |
| Subtle push-in / slow pan                      | 5–8 s              |

**Rules:**

1. **sum(beats + pauses) ≤ ~90 % of clip duration** — the rest is buffer.
2. **Prompt pacing explicitly** ("she pauses", "a beat of silence") — the model won't stretch moments on its own.
3. Dialogue = words ÷ 2.5; a sentence needs its full breath before the camera moves on.
4. More beats than physically fit → "rushed action" (LTX) / unphysical haste (H3).
5. Cuts (LTX multi-shot, H3 `At 00:03.500`): every cut time must leave room for the previous action to complete.
6. **Duration caps:** H3 4–15 s · plan multi-shot density accordingly (2–4 shots comfortable).

→ Full timing integration (cut times, `<cutoff>`, FL2VA paths) in [../models/minimax_h3.md](../models/minimax_h3.md) and [../models/ltx2.3.md](../models/ltx2.3.md).

---

## 6. Names → models inside → guides (after the decision, open only one file)

The agent sees only these **names** in the tool's `model` description (each with its modes, only for what is installed). A name stands for the whole family: the best installed variant is used (newest, fastest, best quantization), so pass a name from the first column and nothing else unless you need one specific model: then pass its **model name** from the second column (or a file name).

| `model` (name)        | Models inside                                                                                              | Modes                                    | Guide                                                   |
| --------------------- | ---------------------------------------------------------------------------------------------------------- | ---------------------------------------- | ------------------------------------------------------- |
| `Krea 2`              | Krea 2 Turbo (**our image2image default**) · Krea 2 Raw                                                     | `text2image`, `image2image`              | [../models/krea_2.md](../models/krea_2.md)              |
| `Z Image`             | Z Image Turbo 1.0 · Z Image Base 1.0                                                                        | `text2image`, `image2image`              | [../models/z_image.md](../models/z_image.md)            |
| `FLUX.2`              | FLUX.2 [klein] 9B KV (**our edit default**) · 9B · 9B Base · 4B · 4B Base · FLUX.2 [dev] (32B, high-end)    | `text2image`, `image2image`, `edit`      | [../models/flux2_9b.md](../models/flux2_9b.md)          |
| `FLUX.3`              | FLUX.3 [klein] 5.6B · FLUX.3 [klein] 5.6B Base                                                              | `text2image`, `edit`                     | [../models/flux3_5b.md](../models/flux3_5b.md)          |
| `Qwen Image 2.1`      | **Qwen Image 2.1 Turbo** (picked first) · Qwen Image 2.1 (the non-turbo model: by file name only)           | `text2image`, `edit`                     | [../models/qwen_image.md](../models/qwen_image.md)      |
| `Qwen Image`          | Qwen Image 2512 (text2image) · Qwen Image Edit 2511 (image2image, edit; our tuned settings)                 | `text2image`, `image2image`, `edit`      | [../models/qwen_image.md](../models/qwen_image.md)      |
| `Qwen Image Edit`     | Qwen Image Edit 2511 · 2509 (edit models only)                                                              | `image2image`, `edit`                    | [../models/qwen_image.md](../models/qwen_image.md)      |
| `MOSS Image`          | MOSS Image SFT · MOSS Image Base                                                                            | `text2image`                             | [../models/moss_image.md](../models/moss_image.md)      |
| `LTX-2.3`             | LTX-2.3 22B [distilled] 1.1 · LTX-2.3 22B [dev]                                                             | `text2video`, `image2video`              | [../models/ltx2.3.md](../models/ltx2.3.md)              |
| `MiniMax H3`          | MiniMax H3 (FL2VA, frame anchoring)                                                                         | `text2video`, `image2video`              | [../models/minimax_h3.md](../models/minimax_h3.md)      |
| `MiniMax H3 [ref2va]` | MiniMax H3 [ref2va] (Ref2VA, references)                                                                    | `text2video`, `image2video`              | [../models/minimax_h3.md](../models/minimax_h3.md)      |

Other families the pipeline knows (e.g. `FLUX.1`, `HiDream I1`, `Wan v2.x`, `Stable Diffusion XL Base`) appear in the `model` description when installed; they have no guide here. `Qwen Image Edit` is generated from the catalog and lists the Edit checkpoints; it supplements `Qwen Image`, which already reaches them with our tuned settings. For new work prefer `Qwen Image 2.1`.

The default for `image2image` is `Krea 2` (Turbo); `FLUX.3` and `Qwen Image 2.1` have no `image2image`. `image2image` is only available where the `model` description lists it: it means "edit without moodboard, one source image, strength below 1", and it exists only for models with a tuned preset. Everything else that changes an existing image is `edit`. For both MiniMax H3 names always name the one you need: the two semantics must never be mixed (§3).

All content from official vendor sources (docs/model cards/blogs, accessed 2026-10-09/10); links in the respective guides.
