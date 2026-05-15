# Dockerfile template for Python projects using uv.
#
# Usage:
#   1. Copy to your project root as "Dockerfile"
#   2. Replace <PROJECT_MODULE> with your package's import name (e.g. my_project)
#   3. Adjust EXPOSE port and CMD entry point as needed
#   4. Build: docker build -t <image-name> .
#   5. Run:   docker run --env-file .env -p 8000:8000 <image-name>

# ---- Build stage ----
FROM python:3.12-slim AS builder

WORKDIR /app

RUN pip install --no-cache-dir uv

COPY pyproject.toml uv.lock ./
RUN uv sync --frozen --no-dev --no-editable

# ---- Runtime stage ----
FROM python:3.12-slim AS runtime

RUN groupadd --gid 1001 appuser && \
    useradd --uid 1001 --gid appuser --shell /bin/bash --create-home appuser

WORKDIR /app

COPY --from=builder --chown=appuser:appuser /app/.venv /app/.venv
COPY --chown=appuser:appuser src/ ./src/

USER appuser

ENV PATH="/app/.venv/bin:$PATH" \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

EXPOSE 8000

CMD ["python3", "-m", "uvicorn", "src.<PROJECT_MODULE>.app:app", \
     "--host", "0.0.0.0", "--port", "8000"]
