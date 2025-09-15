FROM node:24.5.0-bookworm-slim AS client

# Install client dependencies
COPY client/package.json client/package-lock.json /client/
RUN cd /client && npm install

# Copy the source code of the client into the container.
COPY client /client

# Build the client
RUN cd /client && npm run build

FROM python:3.13.5-slim-bookworm AS base

RUN apt update -y \
    && apt install -y --no-install-recommends \
    # Required to build psycopg2
    build-essential \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*

ENV PATH=/server/.venv/bin:$PATH \
    PYTHONPATH=/server \
    PYTHONUNBUFFERED=1 \
    DJANGO_SETTINGS_MODULE=__project_name__.settings \
    PORT=8000

# Add user that will be used in the container
ARG UID=1000
RUN useradd __project-name__ --uid ${UID} --create-home && mkdir /server && chown -R __project-name__ /server

# Install uv
COPY --from=ghcr.io/astral-sh/uv:0.8.15 /uv /uvx /bin/

# Use user "__project-name__" to run the build commands below and the server itself.
USER __project-name__

# Use /server folder as a directory where the source code is stored.
WORKDIR /server

FROM base AS prod

# Copy the source code of the project into the container.
COPY --chown=__project-name__ server .

# Install Python dependencies
RUN uv sync

# Copy the client bundle from the client target
COPY --chown=__project-name__ --from=client /client/dist /client

# Collect static files
ENV VITE_BUNDLE_DIR=/client
RUN DJANGO_SECRET_KEY=secret django-admin collectstatic --noinput --clear

CMD gunicorn -w 4 --threads 2 __project_name__.wsgi:application

FROM base AS dev
