# `generate_image`

Generate an image or video using Draw Things.

Sources (canvas/moodboard): pN (picture), iN (image, including a file this tool generated earlier), a filename in the scratchpad folder, or an absolute accessible path. A file the user attached to the chat is referenced by its filename (basename) — on clients that support attachment handles (see SKILL.md), by its `aN` handle instead.

Pick a model with the `model` parameter: a name from the list in its schema (e.g. `Qwen Image 2.1`; it stands for the family, the best installed variant is used), a model name (e.g. `Krea 2 Raw`) or a file name. The schema lists the names installed on this machine with their modes, plus the user's own presets. Omit it to let `auto` choose. Unsure which `mode`/`model` fits your goal? See [`../modes/mode-model-selection.md`](../modes/mode-model-selection.md) (routing, and §6: name → guide) and the matching guide under [`../models/`](../models/) (per-model prompting guide, official vendor sources).

## Parameters

- **scratchpadFolder** — Scratchpad session folder name, one level under CHAT_WORKING_DIRECTORIES. How to obtain it, and whether it is required, depends on your client — see SKILL.md's 'Chat working directory' section.
- **prompt** — Image description (mode: 'text2image') OR description of desired changes (mode: 'image2image').
- **width** — Width in pixels (max 2048). Has sensible default.
- **height** — Height in pixels (max 2048). Has sensible default.
- **imageFormat** — Aspect ratio shorthand. Override if context suggests. '16:9' yields 1024×576 (video-optimized).
- **model** — Model: `auto` (default) picks the best installed model for the mode; otherwise a name from the list in the tool schema, a model name or a file name. A mode that is not listed for a name is rejected with the alternatives. Video modes (text2video/image2video) require a video model.
- **variants** — Number of images (1-4). Default is 1.
- **numFrames** — Number of video frames. Must be a multiple of 32 (or multiple of 32 + 1). Default: 1 (image). Silently ignored for non-video modes.
- **mode** — Generation mode. 'text2video'/'image2video' require a video model (e.g. 'LTX-2.3'). Required when sources exist.
- **canvas** — Primary source image. An attachment the user added to the chat is referenced by its filename (basename) — or, on clients that support attachment handles (see SKILL.md), by its `aN` handle. Generated images, pictures and variants use notations 'i3', 'p1', 'v2'. Digit-only allowed only when unambiguous. Also accepts a filename in the scratchpad folder or an absolute path. Tolerates single-item array input. Accepted: pattern ^([avpi]|[avpi]?[1-9]\d\*)$ or any string.
- **moodboard** — Additional style references for image2image/edit modes. Array of source notations (same format as canvas).
- **runInBackground** — If true, returns immediately with a `requestId` instead of waiting for the render to finish; the render continues in the background. Use it for sweeps: one call per render, all in a row, then retrieve each result with `request_progress(requestId)` (see [`request_progress.md`](request_progress.md)). The call itself returns no image, and nothing is displayed automatically. Default false (synchronous).
