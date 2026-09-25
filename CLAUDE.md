# Assistant to Fear of Landing: social posting

Sylvia Wrigley writes a weekly article for Fear of Landing (https://fearoflanding.com,
no www). A skill (`fear-of-landing-social`) drafts platform-specific social posts; Sylvia
revises them; this project posts the final versions.

## Rules

- **Never post without an explicit yes from Sylvia in the current session**, for the
  exact text. Show what will go out, per platform, then wait.
- Never print, log or ask for tokens or passwords. Credentials live in the
  "Social Media" cloud environment, not in this repo.
- If a request fails, show the error. Don't improvise workarounds (other hosts,
  other auth methods) without asking.
- Sylvia has explained this setup once already; read this file instead of asking her
  to re-explain it.

## Platforms

| Platform | Status | How auth works |
|---|---|---|
| Mastodon | Working | Environment API credential (Bearer) on `wandering.shop`; proxy adds it, you never see it |
| Facebook | Working, app is Live | Environment API credential (Bearer) on `graph.facebook.com`; Page token, never expires |
| Bluesky | Login works; first post in progress | App password in env vars `BSKY_HANDLE`, `BSKY_APP_PASSWORD` |
| X | Not started | |
| LinkedIn | Skipped on purpose | |

### Mastodon
- Instance: `wandering.shop`
- Post: `POST https://wandering.shop/api/v1/statuses` with `status=...`
- Mastodon builds link preview cards itself.

### Facebook
- Posts to the Fear of Landing **Page**, id `113992208629070`, via Meta app "Janet".
- Check: `GET https://graph.facebook.com/v26.0/me` → name "Fear of Landing".
- Post: `POST https://graph.facebook.com/v26.0/113992208629070/feed` with `message=...&link=...`
- Janet was in development mode at first, which hid posts from the public. It is now
  Live. If posts ever vanish for logged-out viewers, check the app mode first.
- The token's "Data access expires" date is around 24 Dec 2026. Unknown whether that
  affects posting; if Facebook starts failing around then, that's the first suspect.

### Bluesky
- Log in: `POST https://bsky.social/xrpc/com.atproto.server.createSession` with
  `{"identifier": $BSKY_HANDLE, "password": $BSKY_APP_PASSWORD}`.
  The response's DID document may point at a separate PDS host (`*.bsky.network`);
  use that for later calls.
- Links need facets (UTF-8 byte offsets) or they won't be clickable.
- Link cards aren't automatic: fetch the article's og:title / og:description / og:image,
  upload the image with `com.atproto.repo.uploadBlob`, and attach an
  `app.bsky.embed.external` embed.
- Bluesky rejects card images over 1 MB (article screenshots are often ~1.6 MB).
  Always shrink the card image to a JPEG under 1 MB before uploading; no need to ask.
- Allowed network domains: `bsky.social`, `*.bsky.network`, `fearoflanding.com`.
