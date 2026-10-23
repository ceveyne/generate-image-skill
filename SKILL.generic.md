---
name: generate-image
description: Generate an image or video using the image pipeline. Made for generic MCP clients.
---

# Image Pipeline

One subfile per tool under `tools/`. File name = tool name + `.md`. Each file contains the full tool description and all parameter descriptions. Open only the file(s) for the tool(s) you are about to call in this session; use `search_file_line` in `tools/` to locate details (e.g. a parameter name).

This skill documents only the tools of the `generate-image` plugin. The other image-pipeline plugins (`process-image`, `analyse-image`, `find-images`) each ship their own separate skill with the same structure — if one of them is not installed, its tools simply don't appear in your tool list.

## Tools

| Tool               | File                                                     | Search keywords                                                                            |
| ------------------ | -------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| `generate_image`   | [`tools/generate_image.md`](tools/generate_image.md)     | text2image, image2image, edit, video, numFrames, model preset, canvas, moodboard, variants |
| `upscale`          | [`tools/upscale.md`](tools/upscale.md)                   | resolution, scaleFactor, re-render, full canvas                                            |
| `request_progress` | [`tools/request_progress.md`](tools/request_progress.md) | requestId, runInBackground, sweep, queue, progress, cancel, retrieve result                |

## Modes & models

Before choosing `mode`/`model` in `generate_image`, read [`modes/mode-model-selection.md`](modes/mode-model-selection.md) — it routes goal → mode → model in one page. Per-model prompting guides (official vendor docs, sources cited) live under `models/`: [`flux.md`](models/flux.md), [`flux-3.md`](models/flux-3.md), [`krea2.md`](models/krea2.md), [`ltx.md`](models/ltx.md), [`minimax.md`](models/minimax.md), [`qwen-image.md`](models/qwen-image.md), [`z-image.md`](models/z-image.md).

## Background renders and sweeps

Set `runInBackground: true` on `generate_image` and the call returns at once with a `requestId` instead of waiting for the image. Draw Things renders the submitted jobs one after another, in the order you submitted them.

**Sweeps are the main use.** Whenever one request should yield several results — different prompts, models, seeds, sizes, or the same prompt as several takes — submit each render as its own `generate_image` call with `runInBackground: true`, all in a row without waiting in between, and keep every `requestId`. The jobs queue up, and you can tell the user what is running while they render.

Then retrieve **each** render with `request_progress(requestId)`:

- While it runs, you get its progress and remaining time. Do not call it again right away; do other work or wait for the user first.
- Once it has finished, the same call returns the result a normal `generate_image` call would have returned, including the image.
- To drop a render you no longer want, call `request_progress(requestId, cancel: true)`. A queued render is removed, a running one is aborted.

Background results are never shown automatically and are easy to forget: the task is only done when every `requestId` has been retrieved, checked and presented as `image-display` describes. `request_progress` without a `requestId` is the standard way to see what is currently running in this chat: it lists the status of all background renders, but contains no images and never replaces retrieving a result. Pass a `requestId` for the exact state of one render — current status, and for a finished one its result with generation details and image.

## Chat working directory

`scratchpadFolder` is optional. If your client exposes a working/session folder concept, pass it as `scratchpadFolder`; otherwise omit the parameter and the server uses its configured default working directory.

## Attachment references

Attachments are referenced by their filename (basename) or an absolute path. This client does not support `aN` attachment handles.

## Related skills

**Required reading before presenting any result:** `image-display` (installed alongside this plugin, if your client supports skills) is the backbone for every image/video workflow on this client — it explains how to verify whether a generated image/video is actually visible to you and to the user, which is not automatic and not the same thing.
