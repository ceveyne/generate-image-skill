---
name: generate-image
description: Generate an image or video using the image pipeline. Made for generic MCP clients.
---

# Image Pipeline

One subfile per tool under `tools/`. File name = tool name + `.md`. Each file contains the full tool description and all parameter descriptions. Open only the file(s) for the tool(s) you are about to call in this session; use `search_file_line` in `tools/` to locate details (e.g. a parameter name).

This skill documents only the tools of the `generate-image` plugin. The other image-pipeline plugins (`process-image`, `analyse-image`, `find-images`) each ship their own separate skill with the same structure — if one of them is not installed, its tools simply don't appear in your tool list.

## Tools

| Tool             | File                                                 | Search keywords                                                                            |
| ---------------- | ---------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| `generate_image` | [`tools/generate_image.md`](tools/generate_image.md) | text2image, image2image, edit, video, numFrames, model preset, canvas, moodboard, variants |
| `upscale`        | [`tools/upscale.md`](tools/upscale.md)               | resolution, scaleFactor, re-render, full canvas                                            |

## Modes & models

Before choosing `mode`/`model` in `generate_image`, read [`modes/mode-model-selection.md`](modes/mode-model-selection.md) — it routes goal → mode → model in one page. Per-model prompting guides (official vendor docs, sources cited) live under `models/`: [`flux.md`](models/flux.md), [`krea2.md`](models/krea2.md), [`ltx.md`](models/ltx.md), [`minimax.md`](models/minimax.md), [`qwen-image.md`](models/qwen-image.md), [`z-image.md`](models/z-image.md).

## Chat working directory

`scratchpadFolder` is optional. If your client exposes a working/session folder concept, pass it as `scratchpadFolder`; otherwise omit the parameter and the server uses its configured default working directory.

## Attachment references

Attachments are referenced by their filename (basename) or an absolute path. This client does not support `aN` attachment handles.

## Related skills

**Required reading before presenting any result:** `image-display` (installed alongside this plugin, if your client supports skills) is the backbone for every image/video workflow on this client — it explains how to verify whether a generated image/video is actually visible to you and to the user, which is not automatic and not the same thing.
