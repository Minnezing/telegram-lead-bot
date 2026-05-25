# Install dependencies and create a virtual environment.
FROM python:3.12-slim as builder

RUN  --mount=type=cache,target=/var/cache/apt \
    apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN --mount=type=cache,target=/root/.cache/pip \
    python -m pip install --upgrade pip uv

WORKDIR /app

COPY pyproject.toml uv.lock ./

RUN --mount=type=cache,target=/root/.cache/uv \
    uv sync --frozen


# Final stage to run the application.
FROM python:3.12-slim

WORKDIR /app

RUN useradd -m -u 1000 botuser && chown -R botuser:botuser /app
USER botuser

COPY --from=builder --chown=botuser:botuser /app/.venv ./.venv
COPY --chown=botuser:botuser bot/ .

ENV PYTHONUNBUFFERED=1
ENV PYTHONPATH=/app
ENV PATH="/app/.venv/bin:$PATH"

CMD ["python", "main.py"]