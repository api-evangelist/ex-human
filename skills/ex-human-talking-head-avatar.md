---
name: Generate a talking-head avatar video
description: Turn a character image into an animated AI Human and produce lip-synced talking-head videos from either text (with Ex-Human TTS) or your own audio file.
api: openapi/ex-human-openapi.yml
operations:
  - create_bot_v3_animations_v3_create_bot_post
  - generate_lipsync_v3_animations_v3_generate_lipsync_post
  - generate_lipsync_from_audio_v3_animations_v3_generate_lipsync_from_audio_post
  - generate_speech_tts_v1_generate_speech_post
---

# Generate a talking-head avatar video

Produce a lip-synced AI Human that speaks — from text or from your own audio.

## Auth
Bearer token from https://admin.exh.ai/ as `Authorization: Bearer <token>`; base URL `https://api.exh.ai`.

## Steps
1. **Create an idle animation** — `POST /animations/v3/create_bot` (`create_bot_v3_animations_v3_create_bot_post`). Send a character image; the response includes an `idle_url` video of the animated AI Human at rest.
2. **Talk from text** — `POST /animations/v3/generate_lipsync` (`generate_lipsync_v3_animations_v3_generate_lipsync_post`) to generate a lip-synced video of the AI Human speaking supplied text.
3. **Talk from audio** — alternatively `POST /animations/v3/generate_lipsync_from_audio` (`generate_lipsync_from_audio_v3_animations_v3_generate_lipsync_from_audio_post`) with an mp3 or wav file to drive the lip-sync from your own audio.
4. **Synthesize the voice (optional)** — `POST /tts/v1/generate_speech` (`generate_speech_tts_v1_generate_speech_post`) converts text to a streaming WAV using a named `voice_name` (e.g. `Carolyn`); feed that WAV into the audio-driven lip-sync for full control over the spoken voice. Text is limited to 400 chars per call (contact support@exh.ai to raise it).

## Rules
- Validation failures return HTTP 422 `HTTPValidationError` — see `errors/ex-human-problem-types.yml`.
- Text-to-speech returns raw streaming WAV audio, not JSON.
- Pass `X-Request-Id` to correlate multi-step jobs in logs (`conventions/ex-human-conventions.yml`).
