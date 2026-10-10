# `generate_image`

Generate an image or video using Draw Things.

Sources (canvas/moodboard): pN (picture), iN (image, including a file this tool generated earlier), a filename in the scratchpad folder, or an absolute accessible path. A file the user attached to the chat is referenced by its filename (basename) — on clients that support attachment handles (see SKILL.md), by its `aN` handle instead.

Pick a model with the `model` parameter (e.g. `qwen-image-2.1`); the full preset list is in its schema. Omit it to let `auto` choose. Unsure which `mode`/`model` fits your goal? See [`../modes/mode-model-selection.md`](../modes/mode-model-selection.md) (routing) and the matching guide under [`../models/`](../models/) (per-model prompting guide, official vendor sources).

## Parameters

- **scratchpadFolder** — Scratchpad session folder name, one level under CHAT_WORKING_DIRECTORIES. How to obtain it, and whether it is required, depends on your client — see SKILL.md's 'Chat working directory' section.
- **prompt** — Image description (mode: 'text2image') OR description of desired changes (mode: 'image2image').
- **width** — Width in pixels (max 2048). Has sensible default.
- **height** — Height in pixels (max 2048). Has sensible default.
- **imageFormat** — Aspect ratio shorthand. Override if context suggests. '16:9' yields 1024×576 (video-optimized).
- **model** — Model preset. One of: auto (default), z-image, krea, qwen-image, qwen-image-2.1, flux, ltx, minimax, minimax-ref, custom, or a user-defined entry from custom_configs.json. 'auto' selects the best model for the mode. Video modes (text2video/image2video) require a video-capable model: ltx, minimax, or minimax-ref.
- **variants** — Number of images (1-4). Default is 1.
- **numFrames** — Number of video frames. Must be a multiple of 32 (or multiple of 32 + 1). Default: 1 (image). Silently ignored for non-video modes.
- **mode** — Generation mode. 'text2video'/'image2video' require a video-capable model (e.g. 'ltx'). Required when sources exist.
- **canvas** — Primary source image. An attachment the user added to the chat is referenced by its filename (basename) — or, on clients that support attachment handles (see SKILL.md), by its `aN` handle. Generated images, pictures and variants use notations 'i3', 'p1', 'v2'. Digit-only allowed only when unambiguous. Also accepts a filename in the scratchpad folder or an absolute path. Tolerates single-item array input. Accepted: pattern ^([avpi]|[avpi]?[1-9]\d\*)$ or any string.
- **moodboard** — Additional style references for image2image/edit modes. Array of source notations (same format as canvas).
