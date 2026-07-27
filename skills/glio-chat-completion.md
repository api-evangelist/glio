---
name: Call an LLM through Glio (OpenAI-compatible)
description: Use Glio's OpenAI-compatible chat-completions and embeddings endpoints to call multiple LLM providers behind one key.
api: openapi/glio-openapi-original.json
operations: [chatCompletions, embeddings]
---

# Call an LLM through Glio (OpenAI-compatible)

Glio exposes OpenAI-compatible `/v1/chat/completions` and `/v1/embeddings`
endpoints, so any OpenAI SDK works by pointing the base URL at
`https://api.glio.io` and using a Glio key. Billing is pay-per-use in GL tokens.

## Auth
`Authorization: Bearer <API_KEY>`. See `authentication/glio-authentication.yml`.

## Steps
1. **Chat completion** — `chatCompletions` (`POST /v1/chat/completions`) with
   `{"model": "claude-opus-4-8", "messages": [...], "stream": false}`. Set
   `"stream": true` for token streaming. Model aliases include
   `claude-opus-4-8`, `claude-sonnet-4-6`, and other text models listed in
   `llms/glio-llms.txt`.
2. **Embeddings** — `embeddings` (`POST /v1/embeddings`) with
   `{"model": "<alias>", "input": "text or text[]"}`.

## Rules
- These are **synchronous** endpoints (unlike the async job API): a `200`
  returns the completion/embedding directly.
- Respect `429 Rate limit exceeded` with backoff; handle `500 Provider error`
  (an upstream model provider failed) by retrying or switching model.
- See `errors/glio-problem-types.yml` and `conventions/glio-conventions.yml`.
