# Miruro API v3

Miruro API v3 is a lightweight FastAPI service that retrieves and normalizes upstream media data for API consumers. The service is designed for asynchronous HTTP access, browser-aware upstream requests, and container-friendly deployment.

## Technology Stack

| Layer | Technology | Role |
|---|---|---|
| Runtime | Python 3.12 | Application runtime, pinned by `Dockerfile` |
| API framework | FastAPI | HTTP application and route definitions in `api.py` |
| Server | Uvicorn | ASGI process used by `start.sh` and the container |
| HTTP clients | HTTPX and `curl_cffi` | Upstream requests and browser-like request behavior |
| TLS/browser support | ViperTLS with Chromium installation | Upstream compatibility where browser fingerprints are required |
| Configuration | `python-dotenv` | Environment-based local configuration |
| Deployment | Docker, Railway/Render-compatible port handling, Vercel configuration | Reproducible and platform-aware deployment |

## Run Locally

Create a virtual environment, install the dependencies, and start the API:

```bash
python3.12 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
./start.sh
```

The service listens on the `PORT` environment variable and falls back to port `8080`. The Docker image installs the required Chromium system dependencies and starts Uvicorn on `0.0.0.0`.

## Repository Layout

`api.py` contains the FastAPI application. `requirements.txt` defines the Python dependencies, `start.sh` provides the local startup command, `Dockerfile` describes the container image, and the deployment configuration files document platform-specific runtime expectations.

## Contributions

Please read [CONTRIBUTION.md](CONTRIBUTION.md) before opening a change. Changes should preserve the API contract, avoid leaking upstream credentials, and include a clear validation note.

## Disclaimer

Use the service responsibly and comply with the terms, rate limits, and applicable policies of every upstream source accessed through it.
