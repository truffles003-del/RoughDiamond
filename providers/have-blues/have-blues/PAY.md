---
name: have-blues
title: "HaveBlues AI Services"
description: "On-demand Python code generation, web scraping, and cited research reports delivered via pay-per-call x402 USDC on Solana. Hermes agent-powered, ~$0.003 internal cost per call."
use_case: "Use for generating production-ready Python scripts with tests and README from a natural-language spec, scraping public web pages and returning structured JSON, or producing markdown research reports with cited sources on demand. Each call pays only for what you consume, with no subscription or commitment."
category: ai_ml
service_url: https://haveblues.pay.sh
version: 0.1.0
---

HaveBlues is a pay-per-call gateway for AI agent services, built on top of the Hermes agent platform. Every call returns a real artifact (Python file + tests + README, scraped JSON, or cited markdown report) rather than a chatbot-style reply.

Three services are exposed:

* **Code Generator** (`POST /v1/code`) — generates production-ready Python 3.9 from a spec, including unittest tests and a README.
* **Web Scraper** (`POST /v1/scrape`) — fetches a public URL and returns structured JSON, with SSRF protection and rate limiting.
* **Research Reports** (`POST /v1/research`) — produces a markdown research report with cited sources from live web search.

All endpoints settle payments in USDC on Solana via the x402 protocol. No account or API key required — clients bring their own wallet and pay per call.

## Spend-aware usage

* Start with the cheapest endpoint (`/v1/scrape` at $0.03) to verify integration before using `/v1/research`.
* Pass a focused, complete spec to `/v1/code` (include sample input/output if possible) to avoid retries.
* For `/v1/research`, state the target word count and audience in the description; default is ~1500 words.
* Provide the target URL directly in the request body for `/v1/scrape` (not embedded in the description).
* Cap request rate client-side — there is no built-in burst limit beyond the public daily LLM spend cap on the provider side.

## Compatibility

Any x402-compatible client can call this service. Reference implementation:

    pay curl -X POST https://haveblues.pay.sh/v1/code \
      -H "Content-Type: application/json" \
      -d '{"title": "CSV deduplicator", "description": "Python script that reads a CSV by email column, removes case-insensitive duplicates, writes to output.csv. Stdlib only."}'

## Pricing

| Endpoint | Price per call |
| --- | --- |
| `POST /v1/code` | $0.05 USDC |
| `POST /v1/scrape` | $0.03 USDC |
| `POST /v1/research` | $0.15 USDC |
| `GET /health` | free |

## Limits

* SSRF protection: only public HTTP/HTTPS targets accepted; private IP ranges (127.0.0.0/8, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16, 169.254.0.0/16) and `file://`/`ftp://`/`javascript:` are refused.
* Daily LLM spend is capped on the provider side; calls beyond the cap return `429 Payment Required` until the next UTC day.
* Sub-skill workspaces (generated files) live on the provider's machine; copies are returned in the JSON response.

## Security notes

This catalog entry does not include wallet addresses, signer paths, or internal file paths. Operator wallet details are configured server-side and never exposed via the public endpoint. Clients should treat the service as untrusted for input validation purposes (the provider validates inputs but agents should still sanitize).