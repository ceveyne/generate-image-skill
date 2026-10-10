---
name: generate-image
description: Generate an image or video using the image pipeline. Made for LM Studio Bionic.
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

`scratchpadFolder` is required. Before calling a tool: call `get_scratchpad_folder`, then pass its return value unchanged as `scratchpadFolder` — e.g. if `get_scratchpad_folder` returns "1a2b3c", call the tool with `scratchpadFolder="1a2b3c"`.

## Attachment references

Attachments the user added to the chat are referenced by their filename (basename) in the scratchpad folder. This client does not support `aN` attachment handles.

## Related skills

`image-display` (installed alongside this plugin) explains how to verify whether a generated image/video is actually visible to you and to the user on this client.
