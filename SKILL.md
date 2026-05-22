---
name: wechat-draft-publisher
description: Create, polish, and sync WeChat Official Account article drafts from source documents, local images, and final HTML/Markdown, including Feishu-to-WeChat conversion, image mapping, draft upload, and preflight checks.
metadata:
  short-description: WeChat draft authoring and upload workflow
---

# WeChat Draft Publisher

Use this skill when the user wants to turn a source article, doc, or outline into a polished WeChat Official Account draft and sync it to the draft box.

## What This Skill Covers

- Turn a source doc or Markdown draft into WeChat-friendly article content.
- Replace document image placeholders with local image files in the correct order.
- Prepare cover art, body images, and short captions for public-account editing.
- Upload article images and create a WeChat draft.
- Catch the common breakpoints before upload: title length, missing cover, broken image order, and API IP whitelist issues.

## Default Workflow

1. Collect the source of truth.
   - Prefer one canonical article source: Feishu doc export, Markdown draft, or an existing HTML artifact.
   - Treat pasted images and local asset paths as the image source of truth.

2. Resolve the image map.
   - Match each image placeholder to one local file path.
   - Keep the article image order stable.
   - Use a single cover image unless the user explicitly wants a different cover.

3. Preflight the article.
   - Check title length.
   - Check that the body title does not duplicate the WeChat title if the publishing tool enforces a limit.
   - Check that every image placeholder resolves to an existing file.
   - Check that local image dimensions and size are reasonable for upload.

4. Prepare WeChat-safe output.
   - Keep paragraphs short.
   - Prefer clear section headings.
   - Use simple tables only when needed.
   - Avoid unsupported or overly complex HTML/CSS.

5. Upload and create the draft.
   - Upload body images to the WeChat article-image endpoint.
   - Upload cover art as the required thumbnail material.
   - Create the draft, then verify the draft returned by the API.

## Practical Rules

- Use local file paths for all images until the upload step.
- Do not assume a browser login session can authorize WeChat API calls; API calls depend on the API IP whitelist.
- If the API returns an IP whitelist error, stop and ask the user to add the current outbound IP before retrying.
- If the API rejects the draft title, shorten the API title but keep the article body heading intact when possible.
- If the upload tool times out, preserve the prepared Markdown/HTML artifact and retry only the upload phase.

## When To Be Careful

- Do not publish the article unless the user explicitly asks for publishing.
- Do not delete or overwrite existing drafts without confirmation.
- If the user asks to use images from a folder, confirm the exact folder only if there is ambiguity.
- If the user wants browser-side editing instead of API upload, switch to a browser workflow and keep the upload steps manual.

## Recommended Outputs

- A final Markdown file with the resolved image order.
- A WeChat-safe HTML artifact for upload or inspection.
- A draft metadata summary with title, cover, and draft id/media id.

