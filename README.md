# MiniMax H3 Spicy API Examples with Muapi

Compare the eight MiniMax H3 Spicy video endpoints on Muapi — H3, H3 Max and H3 Max Turbo, each in its relaxed-moderation "Spicy" tier — across text-to-video, image-to-video and reference-to-video. This guide covers Muapi endpoints only; it does not document MiniMax's direct API. Spicy tiers apply relaxed content moderation, but you remain responsible for complying with Muapi's [terms](https://muapi.ai/terms) and applicable law.

## Related Projects

- [MuAPI](https://muapi.ai) — Unified API for image, video, and audio generation across hundreds of AI models.
- [MiniMax H3 API](https://github.com/Anil-matcha/MiniMax-H3-API) — Python SDK for the standard (non-Spicy) MiniMax H3 endpoints.
- [MiniMax Media API](https://github.com/Anil-matcha/MiniMax-Media-API) — MiniMax video, speech and music endpoints compared on Muapi.
- [Awesome MiniMax H3 prompts](https://github.com/Anil-matcha/awesome-minimax-h3-prompts) — prompt library for MiniMax H3 video.
- [MiniMax H3 ComfyUI](https://github.com/Anil-matcha/minimax-h3-comfyui) — ComfyUI nodes for MiniMax H3.
- [AI Video API](https://github.com/Anil-matcha/AI-Video-API) — examples for video-generation APIs.

## Overview

- [MiniMax H3 Spicy API landing page](https://muapi.ai/minimax-h3-spicy)
- [Muapi API documentation](https://muapi.ai/docs/api-reference)
- [Video generation docs](https://muapi.ai/docs/video-generation)
- [Create or manage an API key](https://muapi.ai/access-keys)
- [Muapi website](https://muapi.ai)

Requests go to `https://api.muapi.ai/api/v1/{endpoint}` with an `x-api-key` header. A submission returns a `request_id`; poll `GET /api/v1/predictions/{request_id}/result` until the task is completed. Generated video URLs are returned in the result.

## Endpoint comparison

| Tier | Workflow | Muapi endpoint | Resolutions | Price (per generated second) |
|---|---|---|---|---:|
| H3 Spicy | Text-to-video | `minimax-h3-text-to-video-spicy` | 480p, 768p, 2K | $0.058 / $0.115 / $0.161 |
| H3 Spicy | Image-to-video | `minimax-h3-image-to-video-spicy` | 480p, 768p, 2K | $0.058 / $0.115 / $0.161 |
| H3 Spicy | Reference-to-video | `minimax-h3-reference-to-video-spicy` | 480p, 768p, 2K | $0.058 / $0.115 / $0.161 + reference charges |
| H3 Max Spicy | Text-to-video | `minimax-h3-max-spicy-text-to-video` | 480p, 768p | $0.0575 / $0.092 |
| H3 Max Spicy | Image-to-video | `minimax-h3-max-spicy-image-to-video` | 480p, 768p | $0.0575 / $0.092 |
| H3 Max Spicy | Reference-to-video | `minimax-h3-max-spicy-reference-to-video` | 480p, 768p | $0.0575 / $0.092 + reference charges |
| H3 Max Turbo Spicy | Text-to-video | `minimax-h3-max-turbo-spicy-text-to-video` | 480p, 768p | $0.029 / $0.046 |
| H3 Max Turbo Spicy | Image-to-video | `minimax-h3-max-turbo-spicy-image-to-video` | 480p, 768p | $0.029 / $0.046 |

All eight endpoints output video with native audio, durations of 5–15 seconds (the H3 Spicy image-to-video endpoint also accepts 3–15), and are billed per generated second at the selected resolution. Prices are Muapi catalog values as of this writing, not MiniMax direct rates, and can change — confirm the current price on the endpoint's playground page before estimating production cost. Spicy rates sit about 15% above the matching standard H3 / H3 Max / H3 Max Turbo rates.

Reference-to-video billing: on base H3 Spicy the first five reference images and all reference audio are included, each additional image is $0.0575, and reference video is billed per second at the output rate. Check the endpoint's playground page for the exact Max reference billing.

## Request schemas

Required fields are marked with `*`.

| Field | T2V (all tiers) | I2V H3 Spicy | I2V Max / Turbo Spicy | Reference-to-video |
|---|---|---|---|---|
| `prompt` | `*` string | optional string | `*` string | `*` string, can cite "Image 1", "Video 1" |
| `image_url` | — | `*` start frame | start frame (see notes) | — |
| `last_image` | — | optional end frame | — | — |
| `end_image_url` | — | — | optional end frame | — |
| `reference_images` | — | — | — | up to 9 URLs |
| `reference_videos` | — | — | — | up to 3 URLs, 2–15 s each, 15 s combined |
| `reference_audios` | — | — | — | up to 3 URLs, 2–15 s each; not usable alone |
| `aspect_ratio` | `21:9`, `16:9`, `4:3`, `1:1`, `3:4`, `9:16` (default `16:9`) | follows the start image | follows the start image | the T2V ratios or `adaptive` (default) |
| `resolution` | tier-specific (default `768p`) | `480p`/`768p`/`2k` (default `480p`) | `480p`/`768p` (default `768p`) | tier-specific (default `768p`) |
| `duration` | 5–15 (default 5) | 3–15 (default 5) | 5–15 (default 5) | 5–15 (default 5) |
| `aigc_watermark` | H3 Spicy only, optional boolean | optional boolean | — | H3 Spicy only, optional boolean |
| `seed` | — | optional integer (negative = random) | — | — |

Notes:

- The end-frame field is named `last_image` on the H3 Spicy image-to-video endpoint but `end_image_url` on the Max and Turbo Spicy image-to-video endpoints. Do not copy payloads between them unchanged.
- Reference-to-video needs at least one reference image or video; reference audio cannot be sent alone. On Max, no more than 12 reference files in total.
- The Max and Turbo Spicy image-to-video schema lists only `prompt` as required, and describes `end_image_url` as usable on its own for end-keyframe generation; send `image_url` for a normal start-frame animation.
- The output canvas for image-to-video follows the input image.

## Choosing an endpoint

1. **Input you have:** a prompt only (text-to-video), a still to animate (image-to-video), or reusable subject/style/motion assets (reference-to-video).
2. **Resolution needs:** only the base H3 Spicy tier offers 2K; Max and Turbo Spicy stop at 768p.
3. **Cost and speed:** Turbo is the cheapest and fastest tier; Max sits in the middle; base H3 Spicy costs the most per second and is the only tier with 2K and `aigc_watermark`.
4. **Control:** image-to-video accepts an optional end frame for keyframe-style interpolation; reference-to-video can combine up to 9 images, 3 videos and 3 audio clips.
5. **Evaluate on your own prompts.** This repo makes no quality or benchmark claims; compare motion, prompt adherence and audio on representative samples before committing a tier.
6. **Failure handling:** submissions are asynchronous. Poll and handle both completed and failed statuses explicitly.

## Quick start with cURL

```bash
export MUAPI_API_KEY="your-api-key"
```

Text-to-video on H3 Max Spicy:

```bash
curl -X POST "https://api.muapi.ai/api/v1/minimax-h3-max-spicy-text-to-video" \
  -H "x-api-key: ${MUAPI_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{"prompt":"A slow dolly shot of a dancer in a neon-lit club, shallow depth of field","aspect_ratio":"16:9","resolution":"768p","duration":5}'
```

Image-to-video on base H3 Spicy:

```bash
curl -X POST "https://api.muapi.ai/api/v1/minimax-h3-image-to-video-spicy" \
  -H "x-api-key: ${MUAPI_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{"image_url":"https://example.com/first-frame.jpg","prompt":"A bold camera orbit around a glass sculpture as sunlight flashes across the surface","resolution":"480p","duration":5}'
```

Reference-to-video on H3 Max Spicy:

```bash
curl -X POST "https://api.muapi.ai/api/v1/minimax-h3-max-spicy-reference-to-video" \
  -H "x-api-key: ${MUAPI_API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{"prompt":"The character from the reference image walks through a rainy street at night","reference_images":["https://example.com/character.jpg"],"resolution":"768p","duration":5}'
```

Each call returns `{"request_id":"REQUEST_ID"}`. Poll it:

```bash
curl "https://api.muapi.ai/api/v1/predictions/REQUEST_ID/result" \
  -H "x-api-key: ${MUAPI_API_KEY}"
```

## Python example

Requires Python 3.9+ and `requests`:

```bash
python -m pip install -r requirements.txt
export MUAPI_API_KEY="your-api-key"
python examples/submit.py h3-t2v      # base H3 Spicy text-to-video
python examples/submit.py max-i2v     # H3 Max Spicy image-to-video
python examples/submit.py turbo-t2v   # H3 Max Turbo Spicy text-to-video
```

Available job names: `h3-t2v`, `h3-i2v`, `h3-ref`, `max-t2v`, `max-i2v`, `max-ref`, `turbo-t2v`, `turbo-i2v`. Replace the placeholder image URLs in `examples/submit.py` for the image and reference jobs. The script submits, polls every five seconds and prints the result JSON; it does not download media.

## JavaScript example

Node 18+:

```js
const BASE = "https://api.muapi.ai/api/v1";
const headers = { "x-api-key": process.env.MUAPI_API_KEY, "Content-Type": "application/json" };

const submit = await fetch(`${BASE}/minimax-h3-max-turbo-spicy-text-to-video`, {
  method: "POST",
  headers,
  body: JSON.stringify({
    prompt: "A slow dolly shot of a dancer in a neon-lit club, shallow depth of field",
    aspect_ratio: "16:9",
    resolution: "480p",
    duration: 5,
  }),
});
const { request_id } = await submit.json();

for (;;) {
  const res = await fetch(`${BASE}/predictions/${request_id}/result`, { headers });
  const body = await res.json();
  if (["completed", "succeeded", "success", "failed", "error", "cancelled"].includes(String(body.status).toLowerCase())) {
    console.log(body);
    break;
  }
  await new Promise((r) => setTimeout(r, 5000));
}
```

## Playground and API reference per endpoint

- [H3 Spicy text-to-video](https://muapi.ai/playground/minimax-h3-text-to-video-spicy/api)
- [H3 Spicy image-to-video](https://muapi.ai/playground/minimax-h3-image-to-video-spicy/api)
- [H3 Spicy reference-to-video](https://muapi.ai/playground/minimax-h3-reference-to-video-spicy/api)
- [H3 Max Spicy text-to-video](https://muapi.ai/playground/minimax-h3-max-spicy-text-to-video/api)
- [H3 Max Spicy image-to-video](https://muapi.ai/playground/minimax-h3-max-spicy-image-to-video/api)
- [H3 Max Spicy reference-to-video](https://muapi.ai/playground/minimax-h3-max-spicy-reference-to-video/api)
- [H3 Max Turbo Spicy text-to-video](https://muapi.ai/playground/minimax-h3-max-turbo-spicy-text-to-video/api)
- [H3 Max Turbo Spicy image-to-video](https://muapi.ai/playground/minimax-h3-max-turbo-spicy-image-to-video/api)

## License

MIT
