---
name: Run chat completions on Taalas HC1 silicon
description: >-
  Call the Taalas OpenAI-compatible /v1 surface for multi-turn chat completion against hard-wired
  Llama 3.1 8B silicon, including streaming, multimodal message content and function calling.
api: openapi/taalas-inference-v1-api-openapi.yml
operations:
  - chat_completions_chat_completions_post
  - models_models_get
generated: '2026-08-02'
method: generated
source: openapi/taalas-inference-v1-api-openapi.yml, conventions/taalas-conventions.yml
---

# Run chat completions on Taalas HC1 silicon

Taalas serves an OpenAI-compatible surface at `https://api.taalas.com/v1`. If you already speak
OpenAI Chat Completions, you change the base URL and the key — nothing else.

## Before you start

- You need a Taalas API key. Keys are issued through the request form at
  <https://taalas.com/api-request-form/>, which as of 2026-08-02 states applications are closed due
  to demand. There is no self-serve signup. Contact <info@taalas.com>.
- Send it as `Authorization: Bearer <api-key>`. No other header is accepted — `X-API-Key`,
  `api-key` and `X-Api-Token` all fail with the generic `{"detail":"Unauthorized"}`, while a bad
  bearer token returns the distinguishable `{"detail":"Unauthorized: Invalid API Key"}`.
- The published OpenAPI declares no `securitySchemes` and no `401` response, so do not expect your
  generated client to handle auth for you — wire it yourself.

## Steps

1. **Check the service is up before you spend a request.** `GET https://api.taalas.com/health`
   (`health_health_get`) is the one anonymous operation. It returns `status`, `queue_size` (the depth
   of the shared inference queue) and `current_adapter` (the loaded LoRA adapter, or `"none"`). A
   rising `queue_size` is the only published capacity signal — there are no rate-limit headers.
2. **Discover the model name.** `GET https://api.taalas.com/v1/models` (`models_models_get` in the v1
   spec) with your bearer token. The response schema is not declared in the spec, so read the actual
   body rather than trusting a generated type. The Taalas-native default published in the root spec
   is `llama3.1-8B`.
3. **Send the chat completion.** `POST https://api.taalas.com/v1/chat/completions`
   (`chat_completions_chat_completions_post`) with a `V1ChatCompletionsRequest`. Only `model` is
   required by the schema; supply `messages` as an array of `Message` objects (`role` required,
   `content` a string or an array of `TextContent` / `ImageContent` parts).
4. **Tune with the OpenAI-shaped knobs.** `temperature` 0–2, `top_p` 0–1, `n` >= 1, `max_tokens` /
   `max_completion_tokens`, `presence_penalty` and `frequency_penalty` -2–2, `logit_bias`,
   `top_logprobs` 0–20, `stop`, `user`. Values outside those bounds return `422` — the schema
   enforces them.
5. **Stream when latency is the point.** Set `stream: true`. Add
   `stream_options: {include_usage: true}` to receive usage statistics as a final chunk. The
   published contract does not describe the streaming transport, so detect it from the response
   `content-type` rather than assuming SSE.
6. **Call tools.** Pass `tools` as an array of `Tool` objects
   (`{type: "function", function: {name, description, parameters}}`, where `parameters` is a
   `ParametersDefinition` with `type`, `description`, `properties`, `required`) and steer with
   `tool_choice`.
7. **Handle failures.** `422` returns `HTTPValidationError` — `detail[]` of `{loc, msg, type}`;
   `loc` names the field that failed. `401` returns `{"detail": "..."}`. Both are plain
   `application/json`, not RFC 9457 `application/problem+json`.

## Rules

- **Do not retry blindly.** Taalas documents no idempotency key and generative POSTs are not
  idempotent — a retry produces a new, billable, different completion.
- **Unknown fields are tolerated, not honoured.** `V1ChatCompletionsRequest` sets
  `additionalProperties: true`, so an unsupported OpenAI parameter is accepted silently rather than
  rejected. Never infer support from a 200.
- **Do not depend on the 200 response shape from the spec.** Success responses are declared with an
  empty schema in the published contract.
- Report defects at <https://api.taalas.com/bug-report> (the form asks for your API key); service
  status is at <https://status.taalas.com/>.
