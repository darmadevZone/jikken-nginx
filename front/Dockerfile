FROM oven/bun:1 AS base
WORKDIR /front
COPY . .
RUN bun install
CMD ["/bin/bash", "-c","bun dev" ]

