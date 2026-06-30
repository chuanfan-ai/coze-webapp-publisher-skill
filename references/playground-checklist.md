# Playground Checklist

Use this checklist when a user wants to turn a Coze bot/workflow into a web app, personal website tool, shareable HTML page, or publishable mini-product.

## Startup Packet

Before inspecting API details, make sure these are present:

- Coze Playground URL containing `bot_id` or `workflow_id`; if unavailable, collect the raw ID and determine whether it is a bot or workflow
- user's Coze API key/PAT for local testing
- description, screenshot, prompt excerpt, output example, or existing page explaining what the bot/workflow does

## First: Understand Purpose Outside Playground

Playground does not describe the bot/workflow product purpose. Get that from:

- user description
- bot/workflow configuration screenshots
- prompt/persona/task text
- existing HTML prototype
- sample inputs and expected outputs

Convert that purpose into a page concept:

- who will use it
- what they upload or type
- what the single main action is
- what result they expect
- whether the result should be copied, downloaded, refined, or continued

## Then: Learn API Mechanics In Playground

For each required capability, capture:

- endpoint URL and region domain
- HTTP method
- required headers
- body schema
- query parameters
- whether response is streaming or non-streaming
- required `bot_id`, `workflow_id`, `conversation_id`, `user_id`, or app identifiers
- exact format for text, images, files, and custom parameters
- success response fields that the page should read
- error response fields that the page should show clearly

## Common Coze HTML Patterns

### Chat

Use the Chat API when the page talks to a bot/agent:

- send `bot_id`
- send stable `user_id`
- send `stream: true` for live output when useful
- send user text in `additional_messages`
- render answer chunks incrementally

### Upload Before Chat

Use file upload when users attach screenshots, resumes, PDFs, Word files, or images:

- upload each file to the upload endpoint
- store returned `file_id`
- pass `file_id` to chat/workflow in the shape required by Playground
- preview selected files locally before upload when useful

### Workflow

Use workflow run APIs when the Coze side is a workflow rather than a bot:

- map HTML controls to workflow input parameters
- keep parameter names identical to Playground
- if workflow returns structured JSON, format it into readable UI sections

## Validation Checklist

Before saying the work is done:

- token/PAT is present and current for local testing
- endpoint region matches the token and workspace
- upload endpoint works if attachments are supported
- chat/workflow endpoint returns success
- page script syntax is valid
- browser output shows readable content
- Markdown headings, lists, tables, task lists, blockquotes, code blocks, links, and images render correctly
- video/audio links or custom media syntax render as playable `<video>` / `<audio>` elements when the product needs rich media
- generated HTML is sanitized before being inserted into the DOM
- no raw SSE event JSON appears in the answer
- error messages tell the user whether the failing part is token, upload, chat/workflow, CORS, or parsing
- if publishing publicly, no real PAT/API key is committed

## Markdown Rendering Recommendation

For single-file HTML apps, prefer this stack:

- `marked` for GFM-style Markdown parsing
- `DOMPurify` to sanitize the generated HTML
- a small media post-processor for videos/audio because Markdown itself does not define a universal video syntax

Do not rely on regex-only Markdown rendering for Coze output. It breaks on nested lists, tables, code fences, images, escaped characters, and mixed media.

## Production Reminder

For local validation, the user may ask to put the PAT directly in HTML. For public deployment, move secrets to a backend proxy or serverless function and keep the browser token-free.
