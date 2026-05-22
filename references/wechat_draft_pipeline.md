# WeChat Draft Pipeline Notes

## Inputs

- A source article in Markdown, Feishu export, or structured outline.
- A local image folder with final art assets.
- Optional cover image.

## Output Targets

- Local Markdown artifact.
- Local WeChat-safe HTML artifact.
- WeChat draft box entry.

## Common Failure Modes

- API IP not whitelisted.
- Missing cover image.
- Image placeholder order mismatch.
- Title too long for the publishing API.
- Unsupported HTML in the body content.

## Minimal Acceptance Checklist

- The title is correct.
- The body image order matches the article flow.
- The cover is present and readable.
- The draft API returns a media id.
- The draft can be found in the account draft box.

