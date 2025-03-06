FROM node:22.14.0-bookworm-slim AS dev

ARG UID=1000
RUN userdel node && useradd __project-name__ --uid ${UID} -l --create-home && mkdir /client && chown __project-name__ /client

WORKDIR /client
USER __project-name__
