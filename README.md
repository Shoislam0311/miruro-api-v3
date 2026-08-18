<div align="center">

# Miruro API V3

A small FastAPI service for browser-aware upstream retrieval.

<a href="https://miruro-api-v3.onrender.com/">Live deployment</a>
&nbsp; · &nbsp;
<a href="https://github.com/Shoislam0311/miruro-api-v3">Source</a>
&nbsp; · &nbsp;
<a href="CONTRIBUTION.md">Contribution guide</a>

</div>

## What it does

Miruro API V3 is a small FastAPI service for browser-aware upstream retrieval. It keeps the request boundary in one place and is configured to run on several deployment targets, including Render, Railway, Vercel, Docker, and Replit.

The project is a public fork. If you use it, check the upstream service's terms and make sure your request volume and content access are allowed.

## Request path

```text
client request
      │
      ▼
FastAPI + Uvicorn
      │
      ├── HTTPX / curl_cffi
      ├── ViperTLS + Chromium support
      └── normalized upstream response
```

## Stack

| Part | Used here |
|:--|:--|
| Runtime | Python 3.12 |
| API | FastAPI |
| Server | Uvicorn |
| Requests | HTTPX, curl_cffi |
| Browser compatibility | ViperTLS and Chromium |
| Configuration | python-dotenv |
| Delivery | Docker with Railway/Render-compatible `PORT` handling |

## Try the deployment

<a href="https://miruro-api-v3.onrender.com/"><img src="https://img.shields.io/badge/OPEN%20LIVE%20DEPLOYMENT-Render-3B82F6?style=for-the-badge&logo=render&logoColor=white" alt="Open Miruro API live deployment" /></a>

The service uses `PORT` when it is provided and defaults to `8080`.

## Run locally

```bash
python3.12 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
./start.sh
```

To run the container:

```bash
docker build -t miruro-api-v3 .
docker run --rm -p 8080:8080 miruro-api-v3
```

## Before changing request behavior

Keep credentials out of Git. Read [CONTRIBUTION.md](CONTRIBUTION.md) before changing request behavior, and avoid unnecessary traffic to upstream services. The Docker image installs the browser dependencies needed by the ViperTLS path.

<div align="center"><sub>Python · FastAPI · Docker · Render · Railway</sub></div>
