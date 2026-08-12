# Contributing to Miruro API v3

Contributions are welcome when they improve reliability, maintainability, or compatibility without exposing secrets or creating unnecessary load on upstream services.

## Development Workflow

Use Python 3.12, create an isolated virtual environment, and install `requirements.txt`. Keep configuration in environment variables or a local `.env` file that is never committed. Do not hard-code tokens, cookies, private endpoints, or personal data.

## Validation

Run the service locally with `./start.sh` and verify the changed endpoint behavior. If the container path is affected, build the image with Docker and confirm that the configured `PORT` is honored. Keep changes to request handling explicit because upstream behavior can change independently.

## Pull Requests

Explain the problem, the implementation, the validation performed, and any upstream assumptions. Include representative request and response examples when an API contract changes. Avoid committing generated browser binaries or local environment files.
