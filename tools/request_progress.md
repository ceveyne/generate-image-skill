# `request_progress`

Check, retrieve or cancel `generate_image` renders started with `runInBackground: true`.

**Normal use: pass `requestId`.** That is how you follow one render and how you get its image:

- While the render is queued or running, the result shows its status, progress and remaining time. Do not call it again right away; do other work or wait for the user's next message first.
- Once the render has finished, the same call returns the result a normal `generate_image` call would have returned, including the image. This is the only way to receive the image of a background render. Evaluate and present it as `image-display` describes.
- A failed or cancelled render says so instead of returning an image.

With several renders (a sweep), call it once per `requestId`.

Without `requestId`, the tool lists the status of every background render of this scratchpad. That list contains no images and no results. Use it only to recover a `requestId` you lost, not to collect results.

## Parameters

- **scratchpadFolder** — Scratchpad session folder name, one level under CHAT_WORKING_DIRECTORIES. How to obtain it, and whether it is required, depends on your client — see SKILL.md's 'Chat working directory' section.
- **requestId** — The `requestId` returned by `generate_image`. Omit it to list every background render of this scratchpad (statuses only).
- **cancel** — If true, cancels the render given by `requestId` instead of checking it. A queued render is removed from the queue; a running render is aborted. The result tells what actually happened: cancelled, already finished before the cancel took effect (the result can still be retrieved), or not confirmed after 10 seconds.
