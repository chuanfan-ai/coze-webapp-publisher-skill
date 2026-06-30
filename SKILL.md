---
name: coze-html-app-builder
description: Turn Coze bots, agents, and workflows into usable web apps, personal websites, single-file HTML tools, demos, or publishable mini-products. Use when the user wants to package a Coze bot/workflow into a page for others to use, provides a Coze Playground URL, bot_id, workflow_id, API key/PAT, screenshots, or asks to call Coze from an HTML/frontend site and optionally publish it to GitHub Pages, Vercel, or a personal website.
---

# Coze Web App Publisher

## Core Rule

Turn Coze capability into a product surface. Separate product intent from API mechanics.

- Before building, collect the three required inputs unless already present in the conversation: (1) the Coze Playground URL with `bot_id` or `workflow_id`, or the raw ID if no URL is available; (2) the user's Coze API key/PAT for local testing; (3) a description, screenshot, prompt excerpt, or existing page that explains what the bot/workflow does.
- Learn what the bot/workflow does from the user, screenshots, the Coze bot/workflow configuration, prompt text, existing page, or the desired user journey.
- Use Coze Playground only to learn how to call APIs: endpoint, method, headers, body shape, stream mode, required IDs, parameters, and response format.
- Do not infer the bot/workflow purpose from Playground; Playground is a generic API console with IDs inserted.
- The deliverable is not "an API demo" by default; it is a small app or website that makes the Coze capability easy for other people to use.

## Default Scope

Build a single-file HTML/CSS/JS app first to validate the product flow, unless the user explicitly asks for a framework or production deployment.

- For local testing, if the user explicitly provides a Coze API key/PAT and accepts frontend embedding, write it directly into the HTML.
- For anything public, deployed, shared, or production-like, warn that the token should move behind a backend proxy or environment variable.
- If the user wants quick public sharing anyway, make the secret entry user-provided in the page instead of committing a token.
- Prefer `coze.cn` endpoints for China Coze unless the user says they use global Coze.
- Do not use Coze CLI unless the user explicitly asks for CLI-driven Coze work.

## Workflow

1. Collect the startup packet.
   - Ask for the Playground URL first. Prefer a URL already containing `bot_id` or `workflow_id`; if the user only has an ID, use the ID and construct the matching Playground/API path after confirming whether it is a bot or workflow.
   - Ask for the user's Coze API key/PAT at the same time. For local single-HTML validation, write it into the HTML only when the user has explicitly provided it for this purpose.
   - Ask for the bot/workflow role: user description, screenshot, prompt excerpt, output example, or an existing page. This is how to decide what UI and which API capabilities to learn.
   - If the user has already supplied these in context, proceed without re-asking.

2. Understand the Coze capability.
   - Extract: target user, expected inputs, expected output format, whether files/images are needed, and whether the page is only for local validation.
   - Translate the Coze capability into a product promise: what does the user get after using this page, and what is the main action on the first screen?

3. Identify required API skills.
   - Chat bot: inspect `v3/chat` Playground.
   - File or image upload: inspect upload file Playground and send files first to get `file_id`.
   - Workflow: inspect workflow run Playground and required input schema.
   - Conversations or async results: inspect detail/retrieve/cancel endpoints as needed.
   - Read `references/playground-checklist.md` before implementing a new Coze API path or when an existing path fails.

4. Design the page around the user's task.
   - Make the first screen usable, not a marketing landing page.
   - Use a clear app structure: input area, optional file/upload area, primary action, progress/error state, result output, and follow-up controls when useful.
   - Map each Coze input to a clear UI control: textareas for long text, file inputs for attachments/images, small inputs for IDs only when the user needs to edit them.
   - Show upload previews, sending state, stop/retry controls when useful, and a readable result area.
   - Render Coze Markdown as real UI, including headings, lists, tables, task lists, blockquotes, code blocks, links, images, and media links.
   - Match the visual style to the product category; avoid generic dashboards unless the workflow is genuinely operational.

5. Implement the API path.
   - Keep constants visible near the script top: `apiUrl`, `fileApiUrl`, token, `botId`/`workflowId`, and `userId`.
   - For images/files, call upload first, then pass returned `file_id` to chat/workflow according to the Playground format.
   - For multimodal chat, use the content shape shown by Playground. Common pattern: `content_type: "object_string"` with JSON-stringified objects containing text and uploaded file references.
   - For streaming, parse server-sent events defensively and append only answer content; filter completion/status events so raw JSON never appears in the UI.

6. Render Markdown safely.
   - Do not hand-roll Markdown with regular expressions except as a last-resort fallback.
   - For single-file HTML, prefer `marked` for GFM parsing plus `DOMPurify` for sanitizing generated HTML.
   - Treat raw HTML from Coze output as untrusted; sanitize before inserting into `innerHTML`.
   - Support standard Markdown images with `![alt](url)`.
   - Markdown has no universal video syntax; add a controlled media layer such as `@[video](url)`, `![video](url)`, or auto-convert links ending in `.mp4`, `.webm`, `.ogg`, `.mov`, `.m4v` into `<video controls>`.
   - Auto-convert audio links ending in `.mp3`, `.wav`, `.ogg`, `.m4a`, `.aac`, `.flac` into `<audio controls>`.
   - Allow only safe media URL protocols such as `http:`, `https:`, and `blob:`; reject `javascript:` and other active protocols.

7. Validate before reporting done.
   - Run a JavaScript syntax check against inline scripts.
   - Smoke test token plus the exact Coze endpoint being used.
   - If files/images are supported, smoke test `files/upload` and confirm a `file_id`.
   - Open or refresh the local HTML in a browser/server and inspect the visible output.
   - Confirm Markdown renders as UI, not raw Markdown or raw streaming JSON.
   - Test at least headings, bold, table, task list, image, and one video/audio link when the Coze output may include rich media.

8. Publish when requested.
   - For GitHub or GitHub Pages, create a clean publish folder rather than pushing a mixed working directory.
   - Never commit a real PAT/API key. Use a runtime input, localStorage, `.env` plus backend proxy, or platform secret depending on deployment mode.
   - Include a README that explains the Coze inputs required to use the app.
   - Verify the remote visibility, pushed branch, and published URL if a hosting step is included.

## Debugging Rules

- If Markdown display is broken, fix the render layer only; do not change token or API flow unless the API itself is failing.
- If chat works but upload fails, inspect file upload permissions and endpoint region before redesigning the app.
- If a Playground `cztei_...` token works briefly but later fails, prefer a user-created PAT with the needed permissions.
- If a `pat_...` token fails, check PAT status, expiry, workspace access, permission scopes, API base domain, and whether the bot/workflow is in the selected workspace.
- Preserve the last known working path. Make small, testable changes and avoid rewriting the whole page during troubleshooting.

## Useful Assets

- `assets/single-html-template.html`: copy as a starting point when turning a Coze bot/workflow into a small web app.
- `references/playground-checklist.md`: read when choosing or debugging Coze API endpoints.
