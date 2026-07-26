---
name: Animate an image into video (create-then-poll)
description: Generate a personalized image, then submit an image-to-video animation job, poll it to completion, and optionally prolong the resulting clip.
api: openapi/ex-human-openapi.yml
operations:
  - generate_gallery_image_image_v1_generate_gallery_image_post
  - generate_avatar_image_v1_generate_avatar_post
  - create_animation_job_animations_v3_jobs_post
  - get_animation_job_animations_v3_jobs__task_id__get
  - create_prolong_animation_job_animations_v3_prolong_jobs_post
---

# Animate an image into video (create-then-poll)

Create a personalized image and turn it into an animated video using Ex-Human's asynchronous job API.

## Auth
Bearer token from https://admin.exh.ai/ as `Authorization: Bearer <token>`; base URL `https://api.exh.ai`.

## Steps
1. **Make the source image (optional)** — `POST /image/v1/generate_gallery_image` (`generate_gallery_image_image_v1_generate_gallery_image_post`) for a gallery-style image from an identity image + prompt, or `POST /image/v1/generate_avatar` (`generate_avatar_image_v1_generate_avatar_post`) for an avatar from a text description.
2. **Submit the animation job** — `POST /animations/v3/jobs` (`create_animation_job_animations_v3_jobs_post`) with a base64-encoded image and a prompt. The response returns a `task_id`.
3. **Poll for the result** — `GET /animations/v3/jobs/{task_id}` (`get_animation_job_animations_v3_jobs__task_id__get`) until the job reports completion; the finished response carries the output video.
4. **Prolong the clip (optional)** — `POST /animations/v3/prolong_jobs` (`create_prolong_animation_job_animations_v3_prolong_jobs_post`) with a source video URL and prompt to extend the video; it returns a new `task_id` you poll the same way.

## Rules
- This is an asynchronous create-then-poll flow — never assume synchronous completion; always poll the `task_id` (`conventions/ex-human-conventions.yml`).
- No idempotency key is supported; a retried `POST /animations/v3/jobs` starts a new job. Track your `task_id`s to avoid duplicate work.
- Validation failures return HTTP 422 `HTTPValidationError` — see `errors/ex-human-problem-types.yml`.
