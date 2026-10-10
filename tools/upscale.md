# `upscale`

Re-render the canvas at a higher resolution via Draw Things image2image. No crop — uses the full canvas as-is.

Sources (canvas/moodboard): pN (picture), iN (image, including a file this tool generated earlier), a filename in the scratchpad folder, or an absolute accessible path. A file the user attached to the chat is referenced by its filename (basename) — on clients that support attachment handles (see SKILL.md), by its `aN` handle instead.

For `model` choice, see the per-model prompting guides under [`../models/`](../models/) (official vendor sources).

## Parameters

- **scratchpadFolder** — Scratchpad session folder name, one level under CHAT_WORKING_DIRECTORIES. How to obtain it, and whether it is required, depends on your client — see SKILL.md's 'Chat working directory' section.
- **prompt** — Description of desired changes to apply during re-rendering. Default: empty (preserve content).
- **width** — Output width in pixels (max 2048). Has sensible default.
- **height** — Output height in pixels (max 2048). Has sensible default.
- **scaleFactor** — Scale factor applied to the canvas dimensions. E.g. 2 doubles the resolution. Mutually exclusive with width/height.
- **model** — Model preset for image2image re-rendering. Default 'auto'.
- **canvas** — Source image. An attachment the user added to the chat is referenced by its filename (basename) — or, on clients that support attachment handles (see SKILL.md), by its `aN` handle. Generated images, pictures and variants use notations 'i3', 'p1', 'v2'. Also accepts a filename in the scratchpad folder or an absolute path. Tolerates single-item array input.
- **imageFormat** — Target image format / aspect ratio preset (e.g. '16:9', 'portrait'). Resolved to concrete pixel dimensions.
