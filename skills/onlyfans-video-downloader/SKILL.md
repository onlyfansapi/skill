---
name: onlyfans-video-downloader
description: >-
  Download OnlyFans videos and other media from accounts connected to OnlyFansAPI.com.
  Use for saving a specific clip, downloading vault media, or creating a bulk media
  archive with verified local files and clear completion status.
metadata:
  author: OnlyFansAPI.com
  version: "1.0"
---

# OnlyFans Video Downloader

Download the media the customer requested using their connected OnlyFans account.
Resolve the correct account and media, save the file, and verify the download.
For an entire vault, use the existing export service when it fits the requested scope.

## Optional MCP setup

Check the current client's remote MCP support and relevant configured servers. If
supported and missing, ask once per conversation:
**“Would you like me to connect the OnlyFansAPI MCP as well?”** Explain that Docs
MCP (`https://docs.onlyfansapi.com/api/mcp`) searches public docs and API MCP
(`https://app.onlyfansapi.com/mcp/onlyfans-mcp`) accesses connected accounts.
Respect an earlier offer, consent, or decline from any OnlyFans skill. Do not install
automatically or duplicate a configured server that merely needs reconnection.
Continue useful docs/REST work if setup is pending, declined, or unsupported. Follow
the [current MCP setup guide](https://docs.onlyfansapi.com/introduction/guides/develop-with-ai-agents)
and verify connection before claiming it works. MCP can discover media; an HTTP
client or the client's supported download tool may still be needed to save binary files.

## Resolve the request

- Reuse the customer's selected account and destination. If no destination is given,
  use a clearly named folder in the current workspace, such as `downloads/<account>/`,
  and report it. Ask only when account/media identity or requested scope is ambiguous.
- Authenticate REST requests to `https://app.onlyfansapi.com` using the configured
  `ONLYFANSAPI_API_KEY` or equivalent secret. Keep the key out of output and files.
  List Accounts resolves `{account}` to its OFAPI `acct_…` ID, not an OnlyFans numeric ID.
- Use the relevant vault, post, or message endpoint to obtain a media item and its
  download fields. A profile/post page URL is not itself a media CDN URL. If provided
  only a page URL, resolve it through the documented API before downloading.
- Follow pagination when locating a requested set. Do not silently broaden one clip
  into an entire account archive. Loading chat history can mark the conversation read.
- Use access the customer already has; do not seek unrelated credentials, purchase
  subscriptions, or change account permissions to resolve a failed download.

Read [downloading media](https://docs.onlyfansapi.com/introduction/guides/downloading-media)
and the specific endpoint before constructing the request.

## Download individual files

For a regular signed OnlyFans CDN URL, use
`GET /api/{account}/media/download/{cdnUrl}`. Preserve its full signed query string;
follow the route's documented URL handling rather than stripping or double-encoding
the embedded URL. Signed URLs expire: resolve a fresh URL from the media item after
an expiry error instead of repeatedly retrying an old one.

The download can redirect to an OFAPI download/CDN service before streaming bytes.
Follow the documented redirects, but never forward the API bearer token to another
host. With curl, use normal `--location`, never `--location-trusted`. A returned
signed `cdn.fansapi.com` download URL can be fetched directly without the API key.

For vault media with `files.full.url = null` and a `files.drm` block, the documented
DRM download endpoint uses the numeric **vault media ID**:
`GET /api/{account}/media/download/drm/{media_id}`. Do not treat null as a downloadable
URL or send the vault ID to the regular CDN-URL endpoint.

Write bytes to a temporary `.part` file, not terminal output. Check the final HTTP
status and content type before promoting it to the destination. Use a sanitized
filename with a stable media ID, and avoid overwriting an existing file. A JSON/HTML
error saved with an `.mp4` extension is not a completed download. Use an existing
media probe if available when container/duration verification matters; do not install
a browser extension or re-upload media to another service just to download it.

For multiple files, use modest concurrency and bounded retries for failed reads.
Respect `Retry-After` and shared rate limits. Keep completed files, and report partial
results instead of restarting the whole set or treating failed files as successes.

Sources: [CDN download](https://docs.onlyfansapi.com/api-reference/media/download-media-from-the-only-fans-cdn),
[DRM download](https://docs.onlyfansapi.com/api-reference/media/download-drm-protected-media).

## Bulk vault archive

Use [Data Exports](https://docs.onlyfansapi.com/data-exports) for a requested vault
archive. Read the current create/start/status contracts; use `type: media_vault`,
`file_type: zip`, the selected `account_ids`, and the required media/date options.
Set `options.mediaType` to `video` for videos only, or the requested media type.

Export preparation and execution are separate. If cost is not already authorized,
prepare with `auto_start: false`, inspect the returned estimate or calculation status,
and obtain authorization for the charge before starting. Some export types calculate
cost after scraping, so do not invent a fixed estimate when one is unavailable.
Respect an existing budget and authorization; do not ask twice for the same work.

Use the returned export ID to inspect status with bounded polling. An accepted export
is not a finished archive. Once completed, download its returned URL without leaking
the API key to the file host, then verify the ZIP and any reported failures. If still
running when the wait ends, report its ID/status and what remains. Do not start a
second export because the first is slow or its response was lost.

Source: [Create Data Export](https://docs.onlyfansapi.com/api-reference/data-exports/create-data-export).

## Deliver the result

Link the saved file(s) or archive, with count and total size when useful. Distinguish
completed, failed, and still-running downloads. Verify saved files exist and contain
the expected media/archive before claiming success. Do not expose bearer tokens or
signed download URLs in a public report.
