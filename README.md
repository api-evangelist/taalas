# Taalas

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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
