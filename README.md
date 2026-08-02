# Taalas

Taalas builds "Hardcore Models" — AI models etched directly into custom silicon rather than executed
as software on general-purpose GPUs, under the tagline "The Model is The Computer." Its first
product, the HC1 technology demonstrator, hard-wires Meta's Llama 3.1 8B into an 815mm2,
53-billion-transistor die on TSMC's 6nm process, publicly demonstrated through the ChatJimmy chatbot.

Taalas exposes that silicon through a hosted inference REST API at `api.taalas.com`, which publishes
two OpenAPI 3.1.0 contracts:

- **Taalas API** (root) — `GET /health`, `GET /models`, `POST /generate` with streaming, tool
  calling, guided JSON/regex generation and logprobs. <https://api.taalas.com/docs>
- **Taalas API v1** (OpenAI-compatible) — `GET /v1/models`, `POST /v1/completions`,
  `POST /v1/chat/completions`. <https://api.taalas.com/v1/docs>

Authentication is `Authorization: Bearer <api-key>`; only `GET /health` is anonymous. API keys are
issued through <https://taalas.com/api-request-form/>, which as of 2026-08-02 states applications are
closed due to demand.

## Links

- Website — <https://taalas.com/>
- Products (HC1) — <https://taalas.com/products/>
- API home — <https://api.taalas.com/>
- Status — <https://status.taalas.com/>
- Mission Log — <https://taalas.com/mission-log/>
- Public demo — <https://chatjimmy.ai/>
- Report a bug — <https://api.taalas.com/bug-report>
- Contact — info@taalas.com

## Artifacts

`openapi/` (2 harvested specs + `_original/`), `authentication/`, `conventions/`, `errors/`,
`data-model/`, `lifecycle/`, `conformance/`, `packages/`, `well-known/`, `mcp/` (candidate),
`llms/`, `overlays/`, `skills/`, `security/`, `agentic-access/`.

No A2A agent card, AsyncAPI/webhook surface, hosted MCP server, first-party SDK, CLI, sandbox,
changelog, trust center or vulnerability-disclosure program was found — those absences are recorded
in the artifacts rather than filled in.
