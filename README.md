# HaveBlues AI Services — pay.sh catalog entry

On-demand AI agent services (Python code generation, web scraping, cited research) 
delivered via x402 USDC payments on Solana. Pay per call, no account required.

## What this repo contains

- `PAY.md` — catalog discovery entry (consumed by `pay skills search`)
- `README.md` — this file

The runtime gateway spec (`have-blues.yml`) and the operator wallet are **not** 
included here for security reasons. Those live on the gateway operator's machine 
and are referenced as `--recipient` / `--signer-path` flags when running.

## Adding this to your pay.sh installation

    pay skills add <this-repo-url>

After that, our services are searchable via:

    pay skills search "have-blues"
    pay skills search "code generator"
    pay skills search "research"

## Endpoints (exposed at https://haveblues.pay.sh)

| Method | Path | Price | Description |
| --- | --- | --- | --- |
| POST | /v1/code | $0.05 USDC | Python code + tests + README |
| POST | /v1/scrape | $0.03 USDC | Web scraper with SSRF protection |
| POST | /v1/research | $0.15 USDC | Markdown report with citations |
| GET | /health | free | Health check |

## Calling from another agent

Any x402-compatible client works. Quickest test:

    pay curl -X POST https://haveblues.pay.sh/v1/code \
      -H "Content-Type: application/json" \
      -d '{"title": "CSV deduplicator", "description": "Python script that reads a CSV by email column, removes case-insensitive duplicates, writes to output.csv. Stdlib only."}'

The `pay` CLI auto-handles the 402 challenge, signs the payment, retries the request, 
and returns the response.

## License

MIT