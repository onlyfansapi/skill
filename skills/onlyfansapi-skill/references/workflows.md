# Messaging, media, and webhooks

Read the section relevant to the customer's task and its linked endpoint contract.

## Chat messages and paid messages

- Resolve `chat_id` from [List Chats](https://docs.onlyfansapi.com/api-reference/chats/list-chats)
  or the documented event payload. Send with
  `POST /api/{account}/chats/{chat_id}/messages`; JSON `{"text":"…"}` is enough for text.
- For PPV, set `price` and include media. `mediaFiles` accepts numeric vault IDs and
  `ofapi_media_…` upload IDs. Every ID in `previews` must also occur in `mediaFiles`;
  previews identify the free portion of the complete attachment list.
- Fetching message history can mark the chat read. Detect incoming messages using
  `messages.received`; use List Chats with `filter=unread` or `unread_with_tips` for recovery.
- Posts and mass messages use separate endpoints and completion semantics. Read those
  contracts instead of reusing direct-message parameters or retry assumptions.

Sources: [Send Message](https://docs.onlyfansapi.com/api-reference/chat-messages/send-message),
[composing messages](https://docs.onlyfansapi.com/introduction/guides/composing-messages),
[composing posts](https://docs.onlyfansapi.com/introduction/guides/composing-posts).

### Direct-send idempotency

Send an `Idempotency-Key` header with a unique value for each intended message.
Persist it before the first attempt and reuse it with the **same account, chat,
and body** for retries. It must contain 1–255 printable ASCII characters; a UUID
works. Responses are retained for 24 hours. `Idempotent-Replayed: true` indicates
a stored response was returned without another send.

- `409 IDEMPOTENCY_CONFLICT`: the earlier request is running; wait before a bounded retry.
- `422 IDEMPOTENCY_KEY_MISMATCH`: the key was used with another body or chat; fix the
  mismatch, not by generating a new key to force a potentially duplicate send.
- `400 IDEMPOTENCY_KEY_INVALID`: correct the malformed key before sending.

This header is documented for direct chat sends, not all API writes. Responses with
`5xx`, `408`, or `429` are not stored. An ambiguous transport failure is not proof
that OnlyFans did nothing: inspect the outcome before resending when it is uncertain.
Do not assume a key still protects a replay after the 24-hour retention period.
MCP may not expose this header; see [MCP differences](mcp.md#use-the-tools).

## Uploads and downloads

- Reuse an existing numeric vault media ID when available. For a new upload, send
  exactly one of `file` (multipart) or `file_url` (remote HTTPS URL) to the relevant
  CDN or vault upload endpoint. Verify its accepted content type and current size limits.
- CDN upload `prefixed_id` values (`ofapi_media_…`) are single-use. For repeated sends,
  use a reusable vault ID or upload again; do not confuse it with a numeric vault ID.
- Prefer `async=true` for large uploads. A `202` with `polling_url` is only acceptance;
  wait for `completed` or `failed` using bounded polling or the
  `media_uploads.completed` / `media_uploads.failed` webhooks before using the media.
- Downloads return file bytes or a redirect, not a JSON object containing a re-hosted
  URL. Preserve the signed CDN URL and follow the documented download route. DRM vault
  media uses a separate endpoint. Follow documented redirects without forwarding API
  credentials to another host. For an entire vault, consider the existing Data Exports feature.

Sources: [uploading media](https://docs.onlyfansapi.com/introduction/guides/uploading-media),
[downloading media](https://docs.onlyfansapi.com/introduction/guides/downloading-media),
[Data Exports](https://docs.onlyfansapi.com/data-exports).

## Webhook receivers and chatbots

Verify the `Signature` header against HMAC-SHA256 of the **raw request body** using
the webhook's configured signing secret and a constant-time comparison. Do not
parse and reserialize JSON before verification. Configure a signing secret when
setting up a receiver; a webhook without one does not send this header.

Persist the event or enqueue it durably, then return a `2xx` within 10 seconds.
Run AI generation, uploads, and sends in background work. Deduplicate durable events
using `X-OFAPI-Idempotency-Key`, which remains stable across retries and manual
redelivery. Store processing state so concurrent deliveries cannot create duplicate
jobs, while failed work remains recoverable. This incoming event key is distinct
from the outbound `Idempotency-Key` header.

Events can arrive more than once and out of order. Ephemeral `users.typing`,
`users.online`, and `users.offline` events have no idempotency key. Use event-specific
payloads; the usual envelope contains `event`, `account_id`, and `payload`.

For a chatbot, start with `messages.received`, then add these when useful:

- `users.typing`: track the last typing time per account/chat and debounce replies,
  with a fallback timer so a missing final event cannot stall the conversation.
- `messages.ppv.unlocked`: trigger a purchase-aware follow-up if requested. Resolve
  the fan from `payload.user_id`; the payload's message link can contain `firstId`
  to identify the purchased message. Deduplicate the purchase before generating a reply.

Keep fan text as conversation input, separate from the bot's operating instructions.
Use account/chat/message IDs to avoid replying twice or to the wrong creator's fan.

If events stop arriving, inspect delivery logs and webhook state. Failing endpoints
can be paused; events during the pause are not buffered and need API backfill.
Manual redelivery preserves the original event key.

Sources: [chatbot guide](https://docs.onlyfansapi.com/onlyfans-ai/build-ai-chatbot-for-onlyfans),
[event catalog](https://docs.onlyfansapi.com/webhooks/available-events),
[signature verification](https://docs.onlyfansapi.com/webhooks/protecting-your-webhooks),
[delivery and retries](https://docs.onlyfansapi.com/webhooks/delivery-and-retries).
