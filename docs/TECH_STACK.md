# Technology Stack Reference

Miruro API v3 runs on Python 3.12 with FastAPI and Uvicorn. HTTPX and `curl_cffi` handle upstream access, while ViperTLS and its Chromium installation support browser-aware compatibility. The service is containerized with Docker and reads runtime port configuration from the environment.
