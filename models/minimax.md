# MiniMax H3 — Prompt Guide (official)

**Priority: TIER 1 (newest video model, strongest all-rounder)** · Difficulty: **high** (most rigid syntax in the stack)

**Prompt language:** English. Dialogue/lyrics/on-screen text: verbatim in original language (11 stably supported dialogue languages: EN, DE, FR, ES, IT, PT, RU, AR, ZH, JA, KO).

---

## The Two Checkpoints — Read First

H3 is released as **two task-specific checkpoints**. The semantics of input images differ completely:

| | `minimax` presets — **FL2VA** | `minimax-ref` presets — **Ref2VA** |
|---|---|---|
| File | `minimax_h3_fl2va_q6p.ckpt` | `minimax_h3_ref2va_q6p.ckpt` |
| What input images are | **Actual frames** — anchored to a timecode in the target video | **References** — identity, style, scene, motion, camera, voice, storyboard, edit source |
| Accepted inputs | 0, 1, or 2 images | ≤9 images · ≤3 videos (2–15 s each, ≤15 s total) · ≤3 audio (2–15 s each, ≤15 s total, always alongside an image/video) · **≤12 files total mixed** |
| Modes | T2V · I2V first frame (canvas) · I2V last frame · I2V first+last (canvas + moodboard) | T2V · Reference-to-Video (character/scene/style/motion/camera/voice/storyboard) · video editing · video continuation |
| Prompt format | Instruction line + 3 core fields | 6 sections (see Ref2VA guide below) |
| Never | Use 3+ images or reference-video/audio inputs (unsupported by this checkpoint) | Rely on images as exact frame anchors without declaring them as `<Picture N>` anchors |

### Hard Limits (official, model + API — same for open weights)

| Item | Limit |
|---|---|
| **Output duration** | **4–15 seconds, integer values only** → no long-form beyond 15 s; multi-shot must fit inside it |
| Output resolution | 768P (open weights); 2K only via hosted H3-Regenerate-2K pass |
| Frame rate / audio | 24 fps · 32 kHz stereo |
| First/last-frame images | 0, 1, or 2; width/height 256–5760 px; aspect ratio 2:5 – 5:2 |
| Reference images | **≤ 9** (our "4" is a pipeline choice = 1 canvas + moodboard slots, **not** a model limit) |
| Reference videos | ≤3 clips, 2–15 s each, ≤15 s total |
| Reference audio | ≤3 clips, 2–15 s each, ≤15 s total |
| Mixed inputs | ≤12 files across all types |
| Prompt length | ≤ 7000 characters |

---

## Part A — FL2VA (the `minimax` presets)

### In 30 seconds
1. Final prompt = rigid structure: optional instruction line (always line 1 + one blank line), then **exactly three core fields** in fixed order.
2. Deviations from the syntax (wrong instruction line, missing field, wrong order) are the #1 failure mode — **check against the templates in this file before generating.**
3. `integrated_multimodal_description` is the main body: everything visible/audible along the timeline, shot by shot.
4. Dialogue only inside `<d>[Language] verbatim</d>` blocks; speaker definition **outside** the block.
5. On-screen text always in double English quotes, original text verbatim.

### The three core fields (order is mandatory)
```text
integrated_multimodal_description: [Shot 1] ...

overall_soundscape: ...

non_diegetic_music: ...
```
| Field | Content | Rules |
|---|---|---|
| `integrated_multimodal_description` | Visuals, actions, shots, speakers, dialogue, singing, diegetic audio along the timeline | Open `[Shot 1]` with style + composition: `[Shot 1] Live-action, cinematic, a medium-wide shot frames...` · Styles: Cinematic, live-action, 2D-animated, 3D CG, claymation, watercolor, vintage film |
| `overall_soundscape` | Ambience, physical action sounds, non-verbal human sounds (rain, footsteps, breathing, laughter) | 1–4 sentences, one paragraph. Do **not** repeat dialogue/singing/diegetic music here. `N/A` only if complete silence was explicitly requested |
| `non_diegetic_music` | Music only the audience hears | 1–3 sentences: instrumentation, tempo, rhythm, dynamics. No abstract mood words. `N/A` if none |

### Instruction line (mode-specific, use verbatim)
| Mode | First line of the prompt |
|---|---|
| **T2VA** | none — start directly with the three fields |
| **I2VA** (first frame) | `For the target video, at 0.00 seconds into the target video, <Picture 1> (from [Shot 1]) is fully referenced.` |
| **FL2VA** (first + last) | `How the reference pictures align with the target video — Picture 1 (from Shot 1) aligns with the 0.00-second mark of the target video; Picture 2 (from Shot N) aligns with the S.SS-second mark of the target video.` |
| **L2VA** (last frame) | `How the reference pictures align with the target video — <Picture 1> (from [Shot N]) aligns with the S.SS-second mark of the target video.` |

`N` = index of the final shot, `S.SS` = effective video duration with **exactly two decimals** (e.g. `8.00`).

### Shot rules
- Shot 1: **no** timestamp. Later shots: `[Shot 2] At 00:03.500, the camera cuts to...` — cut times strictly increasing, inside the video duration.
- Cut phrasing: „the camera cuts to", „the shot transitions to", „the shot changes to". Cross-dissolve/fade/wipe only on explicit request.
- A cut must bring **new information** (subject, space, state, viewpoint, time). Only slight distance/angle change → camera motion instead of a cut.
- **Camera motion = motion type + amplitude + speed** as a natural sentence:
  - `The camera pushes in with small amplitude at slow speed toward the folded letter in her hands.`
  - `The camera pans right with large amplitude at fast speed, revealing the open doorway.`
  - `The camera holds a static shot as the runner exits the frame.`

### Speakers, dialogue, singing
- Speaker ID (S1, S2…) + identity **outside** `<d>`; inside the block only language tag + verbatim content (every word and punctuation mark preserved — no translation, no rephrasing):
  `The young woman with a quiet, breathy voice (S1) says: <d>[English] I get off at the next station.</d>`
- Establish speakers stably on first appearance: age, gender, on/off-screen, pitch, timbre, rate, accent.
- Voiceover: exactly `says in an off-screen voiceover`, immediately followed by `while his lips remain completely closed`.
- Dialogue crossing a cut: `<scenetrans>` at both points + „continues seamlessly across the cut". Speech truncated at video end: `<cutoff>`.

### On-screen text
Signs, banners, neon, subtitles: original text **verbatim** in double English quotes.
`A red neon sign reading "营业中" glows above the doorway.`

### Image↔Prompt contract (FL2VA)
> The reference carries the **identity**, the prompt carries the **action and the path.**

- **I2VA:** `<Picture 1>` **IS** the actual frame at 0.00 s and belongs to `[Shot 1]`. Structure: **first-frame anchor → action onset → continuous development → result/reaction.** First confirm style, subjects, composition, scene anchors of the image, then describe what happens next. Character identity, clothing, colors, key objects, spatial relationships must stay consistent.
- **FL2VA:** both images anchor start and end. The body describes **only the motion path between them** — never two static image descriptions. Focus on subject motion, pose changes, object handling, composition/light evolution. Prefer **single shot** (continuous interpolation); multi-shot only if explicit. The last image must be reached exactly in the final `[Shot N]`. Structure: **first-frame state → observable intermediate changes → progressively narrowing differences → last-frame state.**
- **L2VA:** infer a plausible preceding state → explicit action/transition path → gradual convergence → landing exactly on the image in the final shot.

### Official I2VA example (complete)
```text
For the target video, at 0.00 seconds into the target video, <Picture 1> (from [Shot 1]) is fully referenced.

integrated_multimodal_description: [Shot 1] Live-action, cinematic, the young woman shown in <Picture 1> remains beside the rain-covered train window, preserving her appearance, clothing, seat position, and the carriage layout. The camera trucks right with small amplitude at slow speed as she lifts her gaze from the folded letter toward the passing city lights. Her reflection moves across the glass while the quiet, breathy young woman (S1) says: <d>[English] I get off at the next station.</d> She folds the letter along its existing crease.

overall_soundscape: The train wheels produce a steady metallic rhythm beneath a low ventilation hum. Rain ticks against the window while paper rustles softly in her hands.

non_diegetic_music: Sustained cello notes at a slow tempo with widely spaced piano tones, gradually decreasing in volume.
```

### Official FL2VA example (8 s, single shot)
```text
How the reference pictures align with the target video — Picture 1 (from Shot 1) aligns with the 0.00-second mark of the target video; Picture 2 (from Shot 1) aligns with the 8.00-second mark of the target video.

integrated_multimodal_description: [Shot 1] Live-action, cinematic, a rain-soaked cyclist begins in the position and framing established by Picture 1, holding a closed black umbrella beside a silver bicycle. The camera pulls out with small amplitude at slow speed as she releases the bicycle handle, raises the umbrella above her shoulder, and presses the runner upward until the canopy opens. Water rolls from the expanding fabric while she steps beneath it, rotates the handle into the final angle, and settles into the pose, spacing, and composition established by Picture 2 at the end of the shot.
```

---

## Part B — Ref2VA (the `minimax-ref` presets)

> Official guide: `VIDEO_PROMPT_WRITING_GUIDE_ref_en.md` (same HF repo). It defines the full-reference rewrite format (the Context Intermediate Representation H3-Base consumes). MiniMax strongly recommends running H3-Context-IR or following this guide to build your own context preprocessing.

### The six sections (fixed order)
| # | Section | Purpose |
|---|---|---|
| 1 | `subject_definitions` | Define referenced content + its reference labels |
| 2 | `summary` | Task type, target video, main reference relationships (one short paragraph) |
| 3 | `retention_analysis` | How each referenced item is preserved / transferred / copied / referenced (one line per label) |
| 4 | `detailed_description` | Visuals, actions, shots, sound, dialogue in **playback order** (the main body) |
| 5 | `overall_soundscape` | Ambience + physical sounds (same rules as FL2VA) |
| 6 | `non_diegetic_music` | Audience-only score (same rules as FL2VA) |

### The four reference label types
| Label | Meaning |
|---|---|
| `<Subject N>` | Reusable visible content abstracted from reference assets: people, animals, objects, scenes, clothing, props, styles, actions, expressions, poses |
| `<Picture N>` | A reference image used as a concrete **target frame or shot-planning anchor** (first frame, keyframe, last frame, storyboard) |
| `<Video N>` | A reference video providing a **whole-video** relationship: edit source, continuation start, or camera/cut/rhythm/temporal structure |
| `<Audio N>` | An audio signal that is copied or referenced (timbre, music style, dialogue content, rhythm) |

Rules: a label keeps the **same meaning across all sections**. One subject may come from multiple assets; one asset may provide multiple subjects. If `<Picture N>`/`<Video N>` only identifies the source of another item, cite it inside that item's definition — no separate line. `<Video N>` and `<Audio N>` are numbered **independently** (same source video may be `<Video 1>` and `<Audio 2>`).

```text
<Subject 1> is the young woman in <Picture 1>, with long dark hair, a blue cardigan, and a thin silver necklace.
<Subject 1> is the woman whose appearance comes from <Picture 1> and whose walking motion comes from <Video 1>.
<Picture 2> is the first frame of [Shot 1], showing a woman seated beside a café window.
<Picture 3> is a storyboard reference for [Shot 1] and [Shot 2], defining their viewpoint, subject placement, and shot order.
<Video 1> is the source video for the target video edit.
<Audio 1> is the voice-timbre reference for <Subject 1> (S1).
```

### `summary` — task-type prefix
Start with the bracketed task type, chosen by the actual role of each reference:
- `[reference generation]` — asset guides character/scene/style/action/camera/storyboard without being a concrete frame or the edited source
- `[video editing]` — a reference video is directly edited (then: „The target video is an edited version of `<Video 1>`.")
- `[video continuation]` — continues from the end of a source video
- `+ audio reuse` / `+ audio reference` — original audio stays audible / only referenced
- Combinations: `[video editing + reference generation + audio reuse]`

Do **not** introduce new labels in the summary. A reference video providing only camera/cuts/rhythm → `reference generation`, not `video editing`.

### `retention_analysis` — fixed relationship markers
One line per label:
| Visible (`<Subject N>` / `<Picture N>` / `<Video N>`) | Audio (`<Audio N>`) |
|---|---|
| `fully_preserved` | `fully_copy` |
| `partially_preserved` | `partially_copy` |
| `attribute_transfer` (characteristics transferred to a different target subject) | `reference` (timbre/rhythm/style referenced, signal not copied) |
| `weak_reference` (broad similarity only) | `weak_reference` |

```text
<Subject 1> (appears in [Shot 1], [Shot 3]): fully_preserved - the blonde woman's identity, long hair, and light-pink shirt are retained.
<Picture 2> ([Shot 1] first frame): fully_preserved - ...
<Video 1> (cut and pacing structure): weak_reference - ...
<Audio 2>: reference - the target speaker follows <Audio 2>'s voice timbre and measured delivery without copying the original signal.
```
Choose markers only within the role defined in `subject_definitions`; new actions/backgrounds/plot in the target are **not** fidelity losses. No `(Sx)` in this section.

### `detailed_description` — the main body
- Same shot/camera/speaker/dialogue mechanics as FL2VA (`[Shot 1]` no timestamp, `[Shot N] At MM:SS.mmm`, `<d>` blocks, `(Sx)` IDs, `<scenetrans>`/`<cutoff>`).
- Differences: main field is `detailed_description` (not `integrated_multimodal_description`); **style is established in 1–2 English sentences BEFORE `[Shot 1]`**; reference labels are inserted at first appearance and reused without redefinition.
- **Length: 350–500 English words** for generation tasks; dialogue-dense content prioritizes fitting the complete spoken timeline over word count. Editing tasks scale with source complexity.
- Frame anchors in natural phrasing: `the shot begins from <Picture 1>` · `the shot's keyframe corresponds to <Picture 2>` · `the shot ends on <Picture 3>`.
- Speaking referenced subject: `<Subject 2> (S1) turns toward the woman and says, <d>[English] Last summer, I went to my grandfather's house.</d>` — `<Subject N>` = referenced identity, `(Sx)` = actual speaker.
- Voice cues inside directly reused BGM: use `<Audio N>` as source, **do not invent an `(Sx)`**.
- Reused reference dialogue/lyrics: exact source words + original language inside `<d>`; `[unclear]` for unintelligible spans; clean decorative punctuation.
- If only timbre/rhythm/emotion is referenced — **do not carry the original dialogue over** into the target.

### Image↔Prompt contract (Ref2VA)
> References carry **attributes** (identity, look, motion, voice, style), the prompt carries the **new scene, the composition, and what to preserve.**

1. **Every reference gets exactly one clear role**, named explicitly: identity / clothing / product / setting / style / motion / camera / timing / voice / sound. (Unassigned roles → model copies the wrong part of the image.)
2. One asset may guide several related elements — name each role.
3. **Protect what must stay consistent** by naming it (character identity, wardrobe, key props).
4. Write actions in playback order; keep shot count realistic for the duration; one main camera idea per shot.
5. Character sheets / storyboards work as single reference images — define which shots the storyboard maps to (`<Picture N>` anchor).
6. Voice referencing: map the voice of a reference video/audio to one character, keep the same speaker ID.

### Simplified mode (practical, fewer references)
For 1–3 reference images in local runs, natural language works (per ComfyUI official templates): identify assets as `Image 1`, `Image 2`, …; give each one role; describe the target video in playback order. Use the full 6-section format when references, editing, or audio relationships get complex — it is what the official pipeline (H3-Context-IR) produces.

---

## Video timing (agent notes)

Cut times and action pacing must be **physically plausible**. Reference values:

| Action | Plausible duration |
|---|---|
| Blink / head turn | 0.3 / 0.5–1 s |
| Speech | ~2–3 words/s → one sentence ≈ 3–5 s |
| Sit down / stand up | 1–2 s |
| Door open/close, pour coffee, glass shattering | 1–2 s |
| Person walking | ~1.4 m/s normal → 5 m ≈ 3.5–4 s |
| Car through frame | 2–4 s |
| Subtle push-in / slow pan | 5–8 s |

Rules: (1) sum of beats + pauses ≤ ~90 % of clip duration. (2) Place cut times so every shot has room for its full action (no 0.5 s cut while a 4 s sentence is still running). (3) Dialogue length = words ÷ ~2.5; `<cutoff>` if a line intentionally ends with the video. (4) Max 15 s total — plan multi-shot density accordingly (2–4 shots is comfortable).

**Do / Don't (both checkpoints):**
- ✅ Fixed field order · verbatim instruction lines · verbatim dialogue in `<d>` · camera = type+amplitude+speed · explicit reference roles (Ref2VA)
- ❌ Free-form text without fields · dialogue outside `<d>` · translated dialogue · `mood: epic` in `non_diegetic_music` · cut times outside duration · >9 reference images · expecting >15 s output

**Sources:** [MiniMax-H3 model card (HF)](https://huggingface.co/MiniMaxAI/MiniMax-H3) · [FL2VA Prompt Writing Guide](https://huggingface.co/MiniMaxAI/MiniMax-H3/blob/main/docs/VIDEO_PROMPT_WRITING_GUIDE_base_en.md) · [Ref2VA Prompt Writing Guide](https://huggingface.co/MiniMaxAI/MiniMax-H3/blob/main/docs/VIDEO_PROMPT_WRITING_GUIDE_ref_en.md) · [MiniMax API Docs — Video Generation](https://platform.minimax.io/docs/guides/video-generation) · [MiniMax-H3 GitHub](https://github.com/MiniMax-AI/MiniMax-H3) · accessed 2026-10-10
