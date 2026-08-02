---
name: Generate text with the Taalas-native /generate operation
description: >-
  Use the Taalas-native inference surface for guided/structured generation (JSON Schema or regex
  constrained), LoRA adapter awareness, logprobs and hardware-oriented token controls that the
  OpenAI-compatible surface does not expose.
api: openapi/taalas-inference-api-openapi.yml
operations:
  - health_health_get
  - models_models_get
  - generate_generate_post
generated: '2026-08-02'
method: generated
source: openapi/taalas-inference-api-openapi.yml, conventions/taalas-conventions.yml
---

# Generate text with the Taalas-native /generate operation

The root surface at `https://api.taalas.com` is not an OpenAI clone — it exposes controls specific to
running a model that is physically etched into silicon. Reach for it when you need constrained
generation or adapter/queue awareness; use `/v1/chat/completions` otherwise.

## Before you start

- Auth is `Authorization: Bearer <api-key>`; see `authentication/taalas-authentication.yml`. Only
  `GET /health` is anonymous.
- The published spec declares no `securitySchemes`, no `401`, and empty (`{}`) 200 response schemas.
  Treat generated clients as request-side only.

## Steps

1. **Probe the hardware.** `GET /health` (`health_health_get`) — anonymous. Read `queue_size` to see
   how deep the shared inference queue is and `current_adapter` to see which LoRA adapter is loaded
   (`"none"` if the base model is serving). There is no other capacity signal published.
2. **Confirm the model.** `GET /models` (`models_models_get`) with your bearer token. The default
   published in `GenerateRequest.model` is `llama3.1-8B`.
3. **Call generation.** `POST /generate` (`generate_generate_post`) with a `GenerateRequest`. Only
   `prompt` is required — it is typed as an array of objects, with no item schema published, so mirror
   whatever shape the docs render at <https://api.taalas.com/docs> rather than guessing.
4. **Control output length with the Taalas fields, not the OpenAI ones.** This surface uses
   `min_output_tokens` and `max_output_tokens` (not `max_tokens`), and `topk` capped at 8 (not
   `top_p`). `temperature` is 0–2 as usual. `ignore_eos: true` runs past the stop token until the
   output limit is hit — useful for benchmarking throughput, wasteful otherwise.
5. **Constrain the output when you need machine-parseable results.** Three published mechanisms:
   `guided_json` (a JSON Schema), `guided_regex` (a regex pattern) and `response_format` (JSON object
   output). Prefer `guided_json` when the consumer is code — it is the only one that pins the shape.
6. **Call tools.** `tools` is an array of untyped objects on this surface (the v1 surface types them
   properly). `tool_choice` steers selection; `tool_name` and `context_for_tool` carry a routing
   decision and its relevant context from an upstream router model.
7. **Inspect confidence.** Set `logprobs: true` and `top_logprobs` (0–20) to get per-token log
   probabilities.
8. **Stream.** `stream: true` plus `stream_options: {include_usage: true}` for a final usage chunk.
9. **Handle failures.** `422` → `HTTPValidationError` with `detail[].loc` naming the rejected field.
   `401` → `{"detail": "Unauthorized"}` (no header) or `{"detail": "Unauthorized: Invalid API Key"}`
   (bad key).

## Rules

- **No idempotency contract exists.** Do not auto-retry `/generate`; a retry is a new generation.
- **`additionalProperties: true`** — unknown fields are accepted without error. A 200 never proves a
  parameter was honoured.
- **Do not treat `/generate` and `/v1/completions` as interchangeable.** They take different token
  parameters and different prompt types, and only `/generate` supports guided generation.
- `system_prompt_version` selects a backend system prompt (default `offline`); leave it alone unless
  Taalas tells you which values are valid — the spec publishes none.
