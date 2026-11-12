# `request_progress`

Check, retrieve or cancel `generate_image` renders started with `runInBackground: true`.

**Without `requestId`** (recommended): lists the status of every background render of this scratchpad. The list contains no images and no results, so it never replaces retrieving a render.

**With `requestId`**: retrieves that one render.

- While the render is queued or running, this only repeats its status. Do not call it again right away — use the overview, wait for the user's next message or do other work.
- Once the render has finished, the same call returns the result a normal `generate_image` call would have returned: generation details and the image. This is the only way to receive the image of a background render. Evaluate and present it as `image-display` describes.
- A failed or cancelled render says so instead of returning an image.

With several renders (a sweep), call it once per `requestId` to retrieve each result.

Renders run one after another: a queued render waiting for an earlier one is expected, not an error.

## Parameters

- **scratchpadFolder** — Scratchpad session folder name, one level under CHAT_WORKING_DIRECTORIES. How to obtain it, and whether it is required, depends on your client — see SKILL.md's 'Chat working directory' section.
- **requestId** — The `requestId` returned by `generate_image`. Omit it to list every background render of this scratchpad (statuses only).
- **cancel** — If true, cancels the render given by `requestId` instead of checking it. A queued render is removed from the queue; a running render is aborted. The result tells what actually happened: cancelled, already finished before the cancel took effect (the result can still be retrieved), or not confirmed after 10 seconds.
