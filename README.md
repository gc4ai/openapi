# gc4ai/openapi — the gc4.ai API contract (source of truth)

This repository holds **`openapi.json`**, the canonical OpenAPI 3.1 description of the
public [gc4.ai](https://gc4.ai) API. It is the single source of truth that **every**
gc4.ai SDK generator consumes.

> **Do not hand-edit downstream SDKs.** They are generated from this contract. To
> change an SDK surface, change the API + re-emit this spec, then regenerate.

## What's in the contract

- **3 operations:** `chat.send` (`POST /v1/chat/completions`), `completions.create`
  (`POST /v1/completions`), `models.list` (`GET /v1/models`).
- **OpenAI-compatible** request and response shapes.
- **Auth:** HTTP Bearer (`Authorization: Bearer <gc4.ai API key>`).
- **Server:** `https://gc4.ai`.
- **Streaming:** Server-Sent Events keyed on the request's `stream` field. Each event
  is `data: <json>`; the terminal event carries the `usage` frame (incl. `cost`); the
  stream ends with the literal `data: [DONE]` sentinel. The streamed chunk is a dual
  shape (`{choices[].delta}` | `{response}`) that clients normalize. A mid-stream
  provider failure stays **HTTP 200** with an in-band `error` + `finish_reason:"error"`.

## Generated SDKs (all generated from this spec)

| Repo                  | Language / artifact                          |
|-----------------------|----------------------------------------------|
| `gc4ai/typescript-sdk`| Premium TypeScript SDK + MCP server (Speakeasy) |
| `gc4ai/python-sdk`    | Python client (`openapi-python-client`)      |
| `gc4ai/go-sdk`        | Go client config (`oapi-codegen`)            |
| `gc4ai/cli`           | `gc4` command-line client                    |
| `gc4ai/rust-sdk`      | Rust (WIP — see that repo's FR-55 blocker)   |

## Usage (raw)

```bash
curl https://gc4.ai/v1/chat/completions \
  -H "Authorization: Bearer $GC4AI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model":"meta/llama-3.1-8b","messages":[{"role":"user","content":"Hello"}],"max_tokens":16}'
```

Validate / lint the spec with any OpenAPI 3.1 tool, e.g.:

```bash
npx @redocly/cli lint openapi.json
```

## License

[Apache-2.0](./LICENSE) — Copyright 2026 PRVN Labs, LLC (gc4.ai).
