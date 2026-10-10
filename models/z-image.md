# Z-Image Turbo 1.0 — Prompt Guide (official)

**Priority: TIER 3 (good-natured & robust)** · Difficulty: **low** — but with two traps (negative prompt, token limit)

| Preset | Role |
|---|---|
| `text2image.z-image` (+ `text2image.auto`) | Text-to-Image (6B S3-DiT, 8 NFEs, sub-second) |
| `image2image.z-image` | Image-to-Image (style/base transfer) |

Note: **Z-Image-Edit is a separate model and not installed** — use Qwen or FLUX for regional edits.
**Prompt language:** English **or Chinese** — both first-class, excellent bilingual text rendering.

---

## In 30 seconds

1. **NO negative prompt!** Distilled few-step model without Classifier-Free Guidance: `guidance_scale=0.0`, `negative_prompt` is **completely ignored**. All constraints go into the main prompt as positive instructions ("plain background, no text, no watermark, no logos, no extra people").
2. **Long, detailed prompts** are officially recommended — sweet spot **80–250 words**, precise. Long + poetic = often worse.
3. **512-token limit** (≈400 words) in the online default — longer prompts are silently truncated. Local: `max_sequence_length=1024`.
4. **Few steps:** 8–12 (official example: 9). Native 1024×1024.
5. **Little variation for the same prompt/seed** — for variants change the prompt content, not just the seed.

---

## Text prompts

### Camera-first structure (proven, as flowing prose — no tags)
`Composition (shot type, camera angle) → subject + concrete features → clothing → environment/background → lighting → mood → style/medium → technical notes → constraints`

Example skeleton:
> "Medium close-up at eye level. A young woman with short auburn hair, freckles, wearing a wool turtleneck, stands in a sunlit Victorian conservatory. Warm golden-hour light through glass panels, soft shadows. Photorealistic editorial photography, 85mm lens, shallow depth of field. Plain, uncluttered background, no text, no logos, no extra people."

Rules:
- **Natural-language sentences** — the Qwen3-4B text encoder reads language, not Danbooru tags.
- **Specify camera details** (angle, focal length, DoF) — measurably increases control.
- **Text rendering:** texts in quotation marks; especially reliable for EN+ZH, including complex lettering.
- **Official workflow:** write the prompt yourself → optional LLM enhancer (official PE template: `pe.py` in the HF Space) → **read and edit the result** (remove ornamentation, add constraints).
- **Fix the seed** when iterating on the prompt (shows the prompt effect, not randomness); randomize when exploring.

**Do / Don't:**
- ✅ Precise 80–250 words · camera language · constraints phrased positively · quoted render texts · 8–12 steps
- ❌ Filling the negative-prompt field (acts like a placebo) · CFG > 0 (brings nothing) · >512 tokens without raising the limit · tag spam

**Sources:** [Z-Image-Turbo — PROMPTING GUIDE (official answer, HF discussion #8)](https://huggingface.co/Tongyi-MAI/Z-Image-Turbo/discussions/8) · [Z-Image GitHub](https://github.com/Tongyi-MAI/Z-Image) · accessed 2026-10-09
