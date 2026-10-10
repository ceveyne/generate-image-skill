# LTX 2.3 (22B) — Prompt Guide (official)

**Priority: TIER 2 — the most difficult and finicky model in the stack** · Difficulty: **very high**

| Preset | Role |
|---|---|
| `text2video.ltx` (+ `auto`/`custom`) | Text-to-Video (with audio) |
| `image2video.ltx` (+ `auto`/`custom`) | Image-to-Video (first frame, with audio) |
| `text2image.ltx` / `image2image.ltx` | LTX-2 can also do stills |

**Prompt language:** English.

---

## In 30 seconds

1. **"Write like a shot description for a cinematographer."** One flowing paragraph, present tense, ~4–8 sentences per shot.
2. **LTX 2.3 is more prompt-sensitive than its predecessors:** long, detailed prompts consistently perform better; pacing, pauses and emotional beats can be directed granularly.
3. **Prompt length = video length.** Short prompt for a long video → the model rushes through the action. 8–10 s need long prompts.
4. **No numeric micro-control** ("3 birds at 45°", "2°/s pan") — natural language.
5. **Show emotion physically**, never as a label. Dialogue in quotation marks, in short segments with direction between the lines.

## The 6 core elements (all in every prompt)

1. **Establish the shot** — cinematic terms fitting the genre, shot scale (wide / medium / close-up).
2. **Set the scene** — lighting, color palette, surface textures, atmosphere.
3. **Action** — natural progression; every sentence a verb that does something (walks, turns, exhales, reaches). Describing only appearance = giving the model nothing to animate.
4. **Characters** — age, hair, clothing, identifying marks; **emotion as a physical cue** (✗ "sad" → ✓ "eyes widen, voice cracks").
5. **Camera movement** — how and when; describe how the subject looks **after** the movement.
6. **Audio** — ambience, music, speech; dialogue in "quotation marks", specify language/accent.

## Structure & style

- **Single take:** one flowing paragraph, present tense, detail level adapted to shot scale (close-ups more detail than wides), camera relative to the subject.
- **Multi-shot:** one **chronological prose** — no shot lists, no sluglines (unless the cut is additionally described in prose). At every cut:
  1. name the cut ("A hard cut transitions to…", "The image dissolves into…")
  2. re-establish the new shot (scale, angle, content, light change if any)
  3. restore identity ("the woman in the red coat, earlier at the table, now…")
  4. state audio continuity ("the piano score continues across the cut" / "the dialogue drops; only wind remains")
  - **2–4 shots** per generation; each shot with a clear job (establish → detail → reaction); chronological ("Initially…", "A moment later…", "Simultaneously…").
- **Prompt pacing explicitly:** "she pauses", "a beat of silence", "a slow beat".
- **I2V:** focus on **motion** — do **not** re-describe static elements of the start image; describe the transition from stillness to motion, camera movement, and sounds. Prefer a single continuous take.
- **Audio:** 2.3 has better audio quality → audio prompts are worth it: acoustic environment, voice qualities, ambient sounds.

### Official dialogue example (2.3: segments + direction)
> A middle-aged man with greying hair speaks in a sad, slow-paced voice, "I remember after you kids came along..." He pauses and looks to the side, then continues, "your mom..." His eyes widen momentarily. He finishes with a cracking voice, "said something to me I never quite understood." The camera slowly zooms into his face. The audio is crisp with faint room tone.

---

## Image↔Prompt contract (i2v)

> The input image **IS** frame 0. It already defines **composition, lighting, subject, style** — the prompt only adds **time**.

- Describe: what the subject does next, how the camera moves, which sounds emerge.
- **Never** re-describe the image content ("a woman in a red coat standing at..." ✗ — the image already shows it).
- Keep identity anchoring short ("the woman from the first frame"), spend the rest of the length on motion.
- If the image already "begins" a motion (e.g., a half-opened wardrobe), describe the transition from the current state.

---

## Video timing (agent note)

LTX derives pacing from prompt density — but physics stays physics. Plausibility reference:

| Action | Plausible duration |
|---|---|
| Blink / head turn | 0.3 / 0.5–1 s |
| Speech | ~2–3 words/s → one sentence ≈ 3–5 s |
| Person: sit down/stand up, door open/close, pour coffee | 1–2 s |
| Person walking | ~1.4 m/s normal → 5 m ≈ 3.5–4 s |
| Car through the frame | 2–4 s |
| Subtle push-in | 5–8 s |

**Rules:**
1. Beats + pauses ≤ ~90 % of clip duration — the rest is buffer.
2. A sentence of dialogue needs its full breath in the prompt before the camera moves on.
3. Packing more beats into a clip than physically fit → model "rushed through the action".

## What LTX 2.3 is good at / what to avoid

| Good | Avoid |
|---|---|
| Kinetic compositions, shallow DoF, natural motion | On-screen text/logos (unreliable → do it in post) |
| Emotional single-subject moments, facial acting | Chaos physics (dancing is OK) |
| Atmosphere: fog, golden hour, rain, reflections | Overloaded frames (too many characters/actions) |
| Clear camera language ("slow dolly in") | Contradictory lighting / contradictory instructions |
| Speech & singing, multiple languages | |

**Common mistakes (official):** too vague ("A nice video of nature") · over-engineered (degree numbers) · duration mismatch (10 words for 10 s) · contradictory directions ("still lake with dramatic waves crashing").

**Useful markers:** camera: follows, tracks, pans across, circles around, tilts upward, pushes in/pulls back, overhead, handheld, over-the-shoulder, wide establishing, static frame · film: film grain, lens flares, slow motion, time-lapse, freeze-frame, fade in/out · sound: whisper/mutter/shout, "resonant voice with gravitas", "forest ambience with birds".

**Do / Don't:**
- ✅ Prose paragraph, present tense, physical cues, quoted dialogue segments, cut named in prose, prompt length ≈ clip length
- ❌ Shot lists/sluglines without a prose cut, numeric specifications, emotion labels, re-describing the start image in i2v, foreign tag syntax (don't copy Kling/Seedance formats unverified)

**Sources:** [LTX-2.3 Prompt Guide (Blog)](https://ltx.io/blog/ltx-2-3-prompt-guide) · [LTX Prompting Guide (Docs)](https://docs.ltx.io/open-source-model/usage-guides/prompting-guide) · accessed 2026-10-09
