<div align="center">
  <img src="https://www.miruro.to/assets/logo-Dnw3w3dS.png?v=1.12.0" alt="Miruro API" width="150" style="border-radius: 20%; box-shadow: 0 0 20px rgba(56, 189, 248, 0.5);">
  <br><br>
  
  # Miruro API v3.0
  
  **The ultimate, decrypted, and fully reverse-engineered native Python backend for Miruro.**
  
  [https://github.com/walterwhite-69/Miruro-API](https://github.com/walterwhite-69/Miruro-API)
</div>

<br>

---

## ✅ Supported Platforms

| Platform | Mode | Notes |
|---|---|---|
| **Railway** | ViperTLS (auto-solve) | Docker-based. Recommended. `railway.toml` included. |
| **Render** | ViperTLS (auto-solve) | Docker-based. `render.yaml` included. |
| **VPS / Docker** | ViperTLS (auto-solve) | Best option. Full headless Chromium bypass, zero manual steps. |
| **Replit** | ViperTLS (auto-solve) | Uses `start.sh` with NixOS library paths pre-configured. |
| **Local** | ViperTLS (auto-solve) | Just `pip install -r requirements.txt && uvicorn api:app`. |
| ~~Vercel~~ | ❌ Not supported | miruro.tv's Cloudflare blocks AWS/Vercel datacenter IPs at the network level. No workaround exists. |

---

## Cloudflare Bypass — How It Works

The pipe endpoint at `miruro.tv/api/secure/pipe` is behind Cloudflare. This API uses **ViperTLS** with headless Chromium to solve the challenge once at startup. The resulting `cf_clearance` cookie is reused for every subsequent request — no browser overhead after the first solve.

| Layer | Tool | What it does |
|---|---|---|
| TLS fingerprint | ViperTLS | Spoofs JA3/JA4 + HTTP/2 frame order to look like Chrome 145 |
| JS challenge | Headless Chromium | Solves once at startup, caches `cf_clearance` cookie |
| Cookie reuse | Persistent session | One `AsyncClient` lives for the server lifetime |
| Cookie lifetime | **1 year** | miruro.tv's CF config issues 365-day clearance cookies |
| Expiry handling | Auto re-solve | On 403, re-warms under a lock; other requests queue and retry |

> **Why not Vercel?** miruro.tv's Cloudflare configuration blocks requests originating from AWS and other major cloud datacenter IP ranges at the network level. This happens before any TLS or cookie check — there is no bypass possible on serverless platforms that share these IP pools.

---

## Deploy on Railway / Render

Use the included `Dockerfile`. Both platforms detect it automatically.

```bash
railway up
# or connect the GitHub repo in the Railway dashboard
```

`railway.toml` and `render.yaml` are committed for one-click deploys. Chromium is installed automatically via `playwright install-deps` — no manual configuration needed.

---

## Setup (Local / VPS)

```bash
git clone https://github.com/walterwhite-69/Miruro-API.git
cd Miruro-API
pip install -r requirements.txt
uvicorn api:app --host 0.0.0.0 --port 8000
```

Then open `http://localhost:8000/` for the interactive API reference.

> **Windows users:** If you see a `RuntimeWarning` about `Proactor event loop`, that's a harmless `curl_cffi` warning — the API works fine.

<br>

## What This Does

Miruro's frontend communicates with its backend through a `secure/pipe` tunnel that base64-encodes, gzip-compresses, and encrypts every request. This project bypasses all of that and gives you simple, direct REST endpoints to:

1. **Search & filter** anime with full AniList metadata
2. **Get complete anime info** — characters, staff, relations, recommendations, trailer, stats, and all metadata in one request
3. **Browse collections** — trending, popular, upcoming, recent, schedule, and spotlight — all paginated
4. **List episodes** with decoded episode IDs from multiple providers
5. **Get M3U8 streaming URLs** for any episode
6. **Autocomplete** search suggestions for dropdown UIs

<br>

## All Endpoints

### 🔍 Search & Discovery

| Endpoint | Description | Params |
|---|---|---|
| `GET /search?query={name}` | Full-text anime search with rich metadata (20+ fields per result) | `query` (required), `page`=1, `per_page`=20 |
| `GET /suggestions?query={name}` | Lightweight autocomplete for dropdowns — returns id, title, poster, format, status, year. Max 8 results. | `query` (required) |
| `GET /filter` | Advanced browse/filter by any combination of genre, tag, year, season, format, status, sort | All optional — see below |

#### Filter Parameters

| Param | Values |
|---|---|
| `genre` | Action, Romance, Comedy, Drama, Fantasy, Sci-Fi, etc. |
| `tag` | Isekai, Time Skip, Reincarnation, etc. |
| `year` | 2025, 2024, etc. |
| `season` | WINTER · SPRING · SUMMER · FALL |
| `format` | TV · MOVIE · OVA · ONA · SPECIAL |
| `status` | RELEASING · FINISHED · NOT_YET_RELEASED · CANCELLED |
| `sort` | SCORE_DESC · POPULARITY_DESC · TRENDING_DESC · START_DATE_DESC |
| `page` / `per_page` | Pagination (defaults: 1 / 20, max per_page: 50) |

---

### 📊 Collections (All Paginated)

| Endpoint | Description |
|---|---|
| `GET /trending` | Currently trending anime |
| `GET /popular` | Most popular anime of all time |
| `GET /upcoming` | Most anticipated upcoming anime |
| `GET /recent` | Currently airing / this season's anime |
| `GET /spotlight` | Curated "What's Hot" list (trending + popular) |
| `GET /schedule` | Airing schedule for the next week |

All collection endpoints accept `page` and `per_page` query params and return:

```json
{
  "page": 1,
  "perPage": 20,
  "total": 5000,
  "hasNextPage": true,
  "results": [ ... ]
}
```

---

### 📖 Anime Details

| Endpoint | Description |
|---|---|
| `GET /info/{anilist_id}` | **Complete anime page** — everything in one request |
| `GET /anime/{id}/characters` | Paginated character list with voice actors |
| `GET /anime/{id}/relations` | All related media (sequels, prequels, side stories, spin-offs) |
| `GET /anime/{id}/recommendations` | Community recommendations sorted by rating |

---

### ▶️ Streaming (3-Step Flow)

#### Step 1: Get Episodes — `GET /episodes/{anilist_id}`

Returns all episodes from multiple providers (kiwi, arc, zoro, hop, etc.) organized by audio type.

```json
{
  "providers": {
    "kiwi": {
      "episodes": {
        "sub": [
          { "id": "watch/kiwi/178005/sub/animepahe-1", "number": 1, "title": "..." }
        ]
      }
    }
  }
}
```

#### Step 2: Get Stream — `GET /{id}`

Take the `id` from Step 1 directly as the URL path.

**Example:** `GET /watch/kiwi/178005/sub/animepahe-1`

```json
{
  "streams": [{ "url": "https://.../master.m3u8", "type": "hls", "quality": "1080p" }],
  "subtitles": [{ "file": "https://...", "label": "English", "kind": "captions" }],
  "intro": { "start": 0, "end": 90 },
  "outro": { "start": 1300, "end": 1420 }
}
```

#### Step 3: Play

Feed `streams[0].url` into any HLS player (Video.js, hls.js, VLC, mpv).

<br>

## Disclaimer

This project is for educational purposes and API integrity research only. The author takes absolutely zero responsibility for network usage. Code contains zero skiddable artifacts.

<br>

**Author:** Walter | **GitHub:** [walterwhite-69](https://github.com/walterwhite-69)
