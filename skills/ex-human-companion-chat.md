---
name: Build a multimodal AI companion chat
description: Hold an ongoing, emotionally intelligent conversation with an Ex-Human character that can reply with text and images, offer smart replies, and remember the chat long-term.
api: openapi/ex-human-openapi.yml
operations:
  - get_response_chatbot_v3_response_post
  - get_smart_replies_chatbot_v2_get_smart_replies_post
  - delete_memory_chatbot_v3_memory_delete
---

# Build a multimodal AI companion chat

Use the Ex-Human chatbot to power an AI companion that talks like a human across text and images.

## Auth
Create a Bearer token at https://admin.exh.ai/ and send `Authorization: Bearer <token>` on every request. Base URL is `https://api.exh.ai`. Optionally pass an `X-Request-Id` header to correlate requests in logs.

## Steps
1. **Send a turn** — `POST /chatbot/v3/response` (`get_response_chatbot_v3_response_post`). Provide `bot_profile` (a `CharacterProfile` with at least `name`, plus `description`/`appearance`/`pronoun` to shape personality), the running `context` array of prior `{turn, message}` turns (max 40; send an empty list to get the bot's greeting), and optional `user_profile`. Read `response` (text) and optional `image_response` from the reply.
2. **Enable long-term memory** — set both `bot_profile.id` and `user_profile.id` and keep `chat_settings.enable_memory: true`. Memory begins accumulating after ~20 messages and lets the character recall context beyond the 40-message window.
3. **Tune behavior** — use `chat_settings.model_name` (`roleplay` or `realistic_chat`), `chat_settings.tasks` (up to 5 instruction strings), and `image_settings` (`model_name: persona-2`, `usage_mode`, `allow_nsfw`) to control text and in-chat image generation.
4. **Offer smart replies** — `POST /chatbot/v2/get_smart_replies` (`get_smart_replies_chatbot_v2_get_smart_replies_post`) to suggest possible user responses for the current dialog context.
5. **Reset memory** — `DELETE /chatbot/v3/memory` (`delete_memory_chatbot_v3_memory_delete`) to clear the stored long-term memory for a chat.

## Rules
- Validation failures return HTTP 422 with an `HTTPValidationError` (`detail[]` of loc/msg/type) — see `errors/ex-human-problem-types.yml`.
- No idempotency key is supported; do not retry non-idempotently on ambiguous failures (`conventions/ex-human-conventions.yml`).
- Keep each `message` ≤ 2000 chars and `context` ≤ 40 turns.
