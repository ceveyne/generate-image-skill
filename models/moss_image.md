# MOSS Image (SFT) — Prompt Guide (official)

**Priority: TIER 2 (new; text layout and layout control are its edge)** · Difficulty: **low–medium** — natural language works; layout prompts are experimental

| Model (`model` param) | Supported modes |
| --------------------- | --------------- |
| `MOSS Image`          | `text2image`    |

The name picks **MOSS Image SFT** (the supervised fine-tune; clearer text grouping and a more organized layout than the base model). **MOSS Image Base** is the untuned base for LoRA training — pass its name only on purpose. No `image2image` / `edit` (text-to-image only).
**Prompt language:** English recommended (most training data); Chinese and other languages work, EN+ZH text rendering is a core strength.

---

## In 30 seconds

1. **Not a turbo model.** 5B single-stream DiT; official sampling is **50 steps, CFG 3, shift 5, Euler**. The pipeline applies the Draw Things recommended settings (about 30 steps, CFG 3, shift 5) — expect normal, not sub-second, render times. You only write the prompt.
2. **Three input formats:** natural language (default choice), tag sequences, and **JSON layouts** (per-element boxes). Mixing natural language and structured input is allowed.
3. **Text in images:** put the exact text in quotes; the model lays it out and breaks lines itself. To control line breaks, put **real line breaks** (or `\n`) into the prompt.
4. **Short prompts are weak:** CFG 3 is enough for long prompts; very short prompts benefit from more guidance. Prefer a full scene description.
5. **Resolution:** best at **1M, 1.5M or 2M pixels** (multiples of 16); works from 0.25M to 4M, any aspect ratio.
6. **No negative prompt** in our interface (the official default is empty anyway).

---

## Natural-language prompts

Describe the scene first, then the text that must appear and where it is written. Example (official):

> "A wide view of a refrigerator beside a kitchen counter with a fruit bowl. A small sticky note on the refrigerator door is handwritten in Chinese:
> "记得买牛奶和鸡蛋！
> 牛奶两盒，鸡蛋一打。
> 顺便带一袋面包。""

Rules:

- **One block of prose** for the scene, then the exact wording of any text in quotation marks, **one line per line of text** if the layout matters.
- Say **where and how** the text appears (handwritten note, whiteboard, poster title, sign) — placement words are used.
- State style/medium and lighting in words; the model has no separate style switches.
- Chinese prompts are first-class: a whole Chinese description with Chinese quoted text is fine.

## Tag sequences

For illustration / character work the order **Quality → Score → Meta → Year → Character → Series → Artist → General** gives the best results:

`best quality, highres, hatsune miku, vocaloid, @foo bar, 1girl, blue hair`

Official effective negative tags (not available through our interface): `nsfw, worst quality, bad anatomy, watermark, jpeg artifact`.

## Layout prompts (experimental)

Pass a JSON object **as the prompt text** to fix what is rendered where. The boxes only make sense for the **exact output size you generate**, so choose `imageFormat` (or width/height) first and compute the coordinates for it.

```json
{
  "summary": {
    "meta": ["poster"],
    "canvas": "single",
    "caption": "A minimalist travel poster with a cream background, a blue mountain illustration, and the title EXPLORE at the top."
  },
  "items": [
    { "label": "text", "caption": "Large dark-blue uppercase title centered at the top.", "text": "EXPLORE", "bbox": [-416, -384, -288, 384] },
    { "label": "object", "caption": "A flat blue mountain illustration in the lower center.", "bbox": [-192, -288, 384, 288] }
  ],
  "bbox_coord_space": "abs_centralized",
  "bbox_format": "yxyx"
}
```

- `summary.caption`: the whole scene and style. `items[]`: `label` (`text` or `object`), `caption` (what it looks like), `bbox`, and `text` (the literal content, for `label: "text"`).
- `bbox_format: "yxyx"` → `[y_min, x_min, y_max, x_max]`. `bbox_coord_space: "abs_centralized"` → **pixels with the canvas center as origin** (x to the right, y downward). A 1024×1024 canvas spans −512…+512 on both axes; for width W and height H subtract W/2 from x and H/2 from y of conventional top-left coordinates.
- Absolute pixels, not relative values; keep the generation size identical to the one the coordinates were written for.
- Our `imageFormat` sizes: `square` 1024×1024 · `landscape` 1024×768 · `portrait` 768×1024 · `16:9` 1024×576.
- Not verified in our pipeline: the **prompt upsampler** (when enabled) may rewrite a JSON prompt. Look at the result and say so if the layout was not followed.

**Do / Don't:**

- ✅ Full scene description · exact quoted text · real line breaks for line control · boxes in `abs_centralized` pixels for the generated size · SFT for finished images
- ❌ Very short prompts with high expectations · layout coordinates for a different canvas size · expecting turbo speed · using the Base model for finished work

**Sources:** [MOSS-Image-SFT — model card](https://huggingface.co/OpenMOSS-Team/MOSS-Image-SFT) · [MOSS-Image — USERGUIDE.md (inference parameters, prompt guidelines, layout prompts)](https://github.com/OpenMOSS/MOSS-Image/blob/main/USERGUIDE.md) · accessed 2026-11-01
