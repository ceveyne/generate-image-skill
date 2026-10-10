# 00 · ROUTING — Mode and Model Selection & Prompt Routing

> **How to use:** This file answers the first four routing questions. After the model decision, open **only one** family file (linked in §6).
>
> **Routing chain:** What do I want to achieve? → Which mode do I need? → Which source material is available? → Which model fits best? → How do I write the optimal prompt? (→ family file)
>
> **Pipeline notes (our installation):**
>
> - The agent selects **`mode` + `model`** — there is **no preset selection** (presets are an internal pipeline detail).
> - The tool interface exposes only **`prompt`**; negative prompts are fixed by the pipeline. Models that ignore or need special negative prompts are therefore **not** a selection criterion here.

---

## 1. What do I want to achieve? → Mode

| Goal                                                                | Mode                           |
| ------------------------------------------------------------------- | ------------------------------ |
| Image from scratch                                                  | `text2image`                   |
| Image in another style / new variant                                | `image2image`                  |
| **Change single elements, keep the rest** (pixel fidelity, regions) | `edit`                         |
| Video from scratch (with audio)                                     | `text2video`                   |
| Bring a still to life (first frame)                                 | `image2video`                  |
| Animate between an exact start **and** end image                    | `image2video` (first+last)     |
| Carry character/style/voice/motion from references into a new scene | `image2video` (reference)      |
| Change an existing video by instruction                             | `image2video` (reference/edit) |

## 2 & 3. Mode + source material → which model can handle it

The agent selects `mode` and `model`. Match the available material to the modes each model supports (model-specific details live in the §6 family files).

| Available material                        | Mode                              | Models                                                                              |
| ----------------------------------------- | --------------------------------- | ----------------------------------------------------------------------------------- |
| Text only                                 | `text2image`                      | `krea` · `z-image` · `flux` · `qwen-image` · `qwen-image-2.1` · `ltx`               |
| Text only                                 | `text2video`                      | `ltx` · `minimax` · `minimax-ref`                                                   |
| One image                                 | `image2image`                     | `krea` · `z-image` · `flux` · `qwen-image` · `qwen-image-2.1` · `ltx`               |
| One image                                 | `edit`                            | **`flux` (default)** · `qwen-image-2.1` (identity-critical) · `qwen-image`          |
| One image                                 | `image2video` (first frame)       | `ltx` · `minimax` · `minimax-ref`                                                   |
| 1 canvas + 1 moodboard image              | `image2video` (first+last)        | **`minimax` only** (images = actual start/end frames)                               |
| Multiple reference images / video / audio | `edit` (multi-ref)                | `flux` (name each image's role) · `qwen-image-2.1` (up to 10)                       |
| Multiple reference images / video / audio | `image2video` (references)        | **`minimax-ref` only** (character/scene/style/motion/camera/voice; ≤12 input files) |
| Existing video                            | `image2video` (edit/continuation) | **`minimax-ref` only**                                                              |

### MiniMax H3 mode map

| Model              | Checkpoint | Image semantics                                  | Possible with                                                                                                                |
| ------------------ | ---------- | ------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------- |
| `minimax`          | FL2VA      | **Actual frames** (time-anchored)                | T2V (no image) · I2V with **1 canvas image = first frame** · I2V with **1 canvas (first) + 1 moodboard (last)**              |
| `minimax-ref`      | Ref2VA     | **References** (identity, style, motion, voice…) | T2V (no image) · I2V with **up to 4 reference images** (canvas + moodboard; **model limit is 9** — 4 is our pipeline choice) |
| Hard limits (both) |            |                                                  | **4–15 s** (integer), 768P local, 2K only via hosted regeneration, prompt ≤ 7000 chars                                       |

**Never** mix the two semantics: frame-anchoring prompts on `minimax-ref`, or reference-role prompts on `minimax`.

## 4. Which model fits best? — Decision rules

**TIER 1 — newest, try first:** MiniMax H3 (video) · Qwen-Image-2.1 (unified image + edit) · Krea 2 (text2image, most look-flexible)
**TIER 2 — capable but finicky / legacy:** LTX 2.3 (video) · older Qwen generation 2512/2509/2511 — **superseded by 2.1**, which unifies the capabilities of 2512 (T2I) and 2511 (edit) at equal or better quality
**TIER 3 — good-natured & robust, safe fallbacks:** Z-Image Turbo · FLUX.2 klein (**default for `mode: edit`**)

### text2image: look dominates

For `text2image`, the **desired look** overrides all technical decision criteria — it is a **design preference, not a quality marker**. Most look-flexible model: **`krea`** — it can do "beauty shots" like `z-image` **and** "rough" looks like `flux`. Only pick a different T2I model for hard technical needs (text rendering, sub-second speed, fixed aesthetic).

### Strong case → model

| Task                                                                                     | First choice                       | Fallback                 | Why                                                                                   |
| ---------------------------------------------------------------------------------------- | ---------------------------------- | ------------------------ | ------------------------------------------------------------------------------------- |
| Character/asset video with references, dialogue, multi-shot, audio                       | **`minimax-ref`**                  | `ltx`                    | Ref2VA: references carry identity/motion/voice; native audio + multi-shot within 15 s |
| Video with exact start **and** end frame                                                 | **`minimax`** (canvas + moodboard) | `ltx` (first frame only) | Only FL2VA anchors both frames                                                        |
| Bring one still to life (first frame only)                                               | **`minimax`**                      | `ltx`                    | FL2VA first-frame mode                                                                |
| Instruction-based editing of an existing video                                           | **`minimax-ref`**                  | –                        | Only Ref2VA does video editing/continuation                                           |
| Atmospheric cinematic, multi-shot cuts, singing/speech, camera language                  | **`ltx`**                          | `minimax`                | Best camera/cut language; 2.3 prompt-faithful — but finicky                           |
| Text2image where **look/aesthetic** is the driver                                        | **`krea`**                         | `z-image` / `flux`       | Most look-flexible: beauty shots **and** rough looks in one model                     |
| Text2image with heavy **in-image text** (posters, infographics, slides, bilingual EN/ZH) | **`qwen-image-2.1`**               | `qwen-image` (2512)      | Best text rendering in the stack; 2.1 = 2512 capabilities at equal/better quality     |
| **Default `edit`** (style, object swap, "change X keep Y")                               | **`flux`**                         | `qwen-image-2.1`         | FLUX is the edit default: excellent prompt adherence + speed                          |
| **Edit with stable identity** (person/product must stay the same)                        | **`qwen-image-2.1`**               | `flux`                   | 2.1 is the superior choice for identity-preserving edits                              |
| **Edit: pixel fidelity / regional / multi-ref (≤10) / transparency**                     | **`qwen-image-2.1`**               | `flux`                   | 2.1: circles/masks, fidelity, up to 10 refs                                           |
| Style discovery, illustration, graphic design, fast variants                             | **`krea`**                         | `z-image`                | Turbo: fast, aesthetics-strong, 2K                                                    |
| Robust T2I with few demands, sub-second                                                  | **`z-image`**                      | `flux`                   | Hard to break, fastest                                                                |

### Strengths / weaknesses — all models

| Model                    | Strengths                                                                                                                                                                      | Weaknesses / traps                                                                                                                    |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------- |
| **MiniMax H3 (FL2VA)**   | Exact frame anchoring (first/last/both), native stereo audio, multi-shot ≤15 s, strong instruction following                                                                   | **Most rigid syntax** (instruction line + 3 fixed fields, `<d>` tags) — deviations → failure; most work per prompt; **15 s hard cap** |
| **MiniMax H3 (Ref2VA)**  | Reference generation (character/scene/style/motion/camera/voice), video editing & continuation, ≤9 images + 3 videos + 3 audios                                                | Full 6-section format is heavy; simplified natural language only for 1–3 refs; frame-anchoring is **not** its mode                    |
| **Qwen-Image-2.1**       | Unified T2I+Edit, **supersedes 2512+2511 at equal/better quality**, best identity stability in edits, up to 10 refs, local regions (circles/masks), native transparency        | **Many refs × many variants = time multiplies** (plan batch sizes); prompt controls transparency (can surprise)                       |
| **Qwen-Image-2512**      | Top text rendering (EN+ZH, layouts, slides)                                                                                                                                    | T2I only (no edit); superseded by 2.1 — alternative when 2.1 misbehaves                                                               |
| **Qwen-Image-Edit 2511** | Consistent 1-image edits, lighting control, IP preservation, bilingual text edit                                                                                               | Older; multi-ref & regions weaker than 2.1; superseded by 2.1                                                                         |
| **Qwen-Image-Edit 2509** | Simple edit instructions                                                                                                                                                       | **Finickiest** edit variant (consistency issues) — last resort only                                                                   |
| **Krea 2 (Turbo)**       | **Most look-flexible T2I** (beauty shots → rough looks), fastest style exploration, expressive illustration, 2K, good text rendering, official LLM expander                    | Turbo quality (iteration, not final); pixel-fidelity edits are not its thing                                                          |
| **LTX 2.3**              | Best camera/cut language, multi-shot in prose, strong audio (speech/singing), 2.3 much more prompt-faithful                                                                    | **Finickiest model:** prompt length must match clip length, no numeric specs, on-screen text unreliable, chaotic physics = artifacts  |
| **Z-Image Turbo**        | Sub-second, robust, bilingual text rendering, excellent instruction following, "beauty shot" aesthetic                                                                         | 512-token limit; little variation on identical prompt                                                                                 |
| **FLUX.2 klein**         | **Default for `mode: edit`** — best prompt adherence + speed in the stack; robust, strong lighting understanding, multi-reference edits with roles, Base variant for fine work | **No prompt upsampling** — vague stays vague; Base ≠ distilled (behaves differently)                                                  |

---

## 5. Two cross-cutting challenges (for agents)

### 5.1 Edits: the image↔prompt contract

Universal principle for all image-based modes — **the material carries identity, the prompt carries the change:**

1. **Never** re-describe the source image in the prompt (the encoders see it; double descriptions collide).
2. Prompt = **delta**: what changes, target state, and (for fidelity models) what explicitly stays ("change X, keep Y").
3. With **multiple** references: **name each image's role in the prompt** (FLUX: "image 1 / image 2"; Qwen: "the bear on the left…"; MiniMax: `<Picture N>`/`<Subject N>` + time anchor or role).
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

→ Full timing integration (cut times, `<cutoff>`, FL2VA paths) in [../models/minimax.md](../models/minimax.md) and [../models/ltx.md](../models/ltx.md).

---

## 6. Family guides (after the decision, only one file)

| Family                          | File                                               |
| ------------------------------- | -------------------------------------------------- |
| MiniMax H3 (FL2VA + Ref2VA)     | [../models/minimax.md](../models/minimax.md)       |
| Qwen-Image (2512/2509/2511/2.1) | [../models/qwen-image.md](../models/qwen-image.md) |
| Krea 2 (Turbo)                  | [../models/krea2.md](../models/krea2.md)           |
| LTX 2.3                         | [../models/ltx.md](../models/ltx.md)               |
| Z-Image Turbo                   | [../models/z-image.md](../models/z-image.md)       |
| FLUX.2 klein                    | [../models/flux.md](../models/flux.md)             |

All content from official vendor sources (docs/model cards/blogs, accessed 2026-10-09/10); links in the respective family files.
