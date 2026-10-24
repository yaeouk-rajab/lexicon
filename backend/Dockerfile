FROM python:3.12-slim

WORKDIR /app

COPY pyproject.toml ./
COPY src ./src
COPY alembic ./alembic
COPY alembic.ini ./

RUN pip install --no-cache-dir .

# fastembed 0.9.0 pins pillow<13, but pillow 12.x has known vulnerabilities fixed
# only in 13.0.0 and the backend uses fastembed's text embeddings only. pip warns
# about the pin and exits 0. Drop this once fastembed allows pillow 13.
RUN pip install --no-cache-dir 'pillow>=13'

EXPOSE 8000

# Migrations always run as the admin/owner role (DATABASE_ADMIN_URL), which
# is also what creates and grants the restricted runtime role ADR-0002
# requires (alembic/versions/0002_adr0002_app_role.py) — the app itself
# then starts up connecting as that restricted role (DATABASE_URL).
CMD ["sh", "-c", "alembic upgrade head && uvicorn lexicon.main:app --host 0.0.0.0 --port 8000"]
