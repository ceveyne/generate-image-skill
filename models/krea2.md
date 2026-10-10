# Krea 2 (Turbo) — Prompt Guide (official)

**Priority: TIER 1 (newest image model after H3/Qwen-2.1)** · Difficulty: **low–medium**

| Preset             | Role                             |
| ------------------ | -------------------------------- |
| `text2image.krea`  | Text-to-Image, up to 2K          |
| `image2image.krea` | Image-to-Image / style reference |

**Prompt language:** English.

---

## In 30 seconds

1. **Natural language** — no keyword lists, no comma spam.
2. **Long, detailed prompts = best results**; it also works with minimal prompt engineering (Turbo is built for fast iteration).
3. **Text rendering:** wrap the words to be rendered in **quotation marks** (`a sign reading "OPEN LATE"`).
4. Official LLM helper: use `expansion.txt` from the Krea-2 repo as a system prompt for your own LLM-based prompt expansion.
5. Turbo (≈4 s per generation) is the **exploration model**: style discovery, batch comparisons, then polish with stronger models.

---

## Text prompts

### Structure (1–2 coherent paragraphs, officially recommended order)

`Main subject + pose/action → appearance, clothing & details → props & materials → composition, framing & perspective → environment & background → light, color palette & mood → aesthetics & medium`

Rules:

- **Start directly with the subject.** Never meta phrases ("In this image…", "The photo shows…").
- **Readable text** in the image: transcribe verbatim + put in quotation marks.
- **Style: clear but not restrictive** — Krea 2 has strong aesthetic understanding and benefits from a clear style direction (photoreal, digital painting, stylized illustration, cinematic still, editorial fashion, concept art).
- For complex UI/graphic design: describe top-down, per element shape, color, relative size, exact position (top-left, center-right), text content + font style.

### Official examples

- `immense rocket launch exhaust as seen from extremely close up` (short works too)
- `vintage analog collage, central irregularly shaped snowy mountain range with a section featuring distinct wavy edges, structured within a 12x16 grid of square tiles, composition fragments the subject by alternating tiles with solid azure blue background squares, thin white grid lines, grainy paper texture, retro aesthetic of mid-century print`

### Prompting tips (official Krea docs)

| Tip                                | Example                                             |
| ---------------------------------- | --------------------------------------------------- |
| Keep the subject clear             | "A streetwear poster for a neon running shoe"       |
| Style language early in the prompt | "risograph print, limited palette, bold silhouette" |
| Generate batches                   | compare quickly, refine the strongest direction     |

---

## Image↔Prompt contract (i2i)

> The reference image carries the **visual language** (style, palette, texture, mood); the prompt describes the **new content.**

- `image2image.krea`: input image = base/style source. The prompt phrases **what** should be created, not what the input looks like ("in the exact graphic style of the reference, but depicting...").
- `strength` is fixed per model in our pipeline, not a per-task dial — don't expect to tune it per prompt.
- Typical mistake: the prompt re-describes the reference image (content + style doubled) → confusion. Rule: **prompt = new scene, reference = how it looks.**
- **Krea 2 has no `edit` mode.** For precise, regional editing (change one element, rest pixel-identical), use Qwen-Image-2.1/2511 or FLUX.2 klein instead.

**Do / Don't:**

- ✅ Flowing prose · quoted render texts · style direction set early · batches for exploration
- ❌ Danbooru tags · meta introductions · edit tasks (not supported by this model)

**Sources:** [krea-2/docs/prompting.md (official, GitHub)](https://github.com/krea-ai/krea-2/blob/main/docs/prompting.md) · [System prompt discussion (HF)](https://huggingface.co/krea/Krea-2-Turbo/discussions/4) · [Krea 2 Turbo Docs](https://www.krea.ai/docs/user-guide/features/krea-2-turbo) · accessed 2026-10-09
