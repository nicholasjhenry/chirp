# https://www.shanesveller.com/blog/2018/10/28/kubernetes-native-phoenix-apps-part-1/

FROM elixir:1.10.3-alpine as builder
RUN apk add --no-cache \
    gcc \
    git \
    make \
    musl-dev
RUN mix local.rebar --force && \
    mix local.hex --force
WORKDIR /app
ENV MIX_ENV=prod

FROM builder as deps
COPY mix.* /app/
RUN mix do deps.get --only prod, deps.compile

FROM node:14.4.0-alpine3.12 as frontend
WORKDIR /app
COPY assets/package*.json /app/
COPY --from=deps /app/deps/phoenix /deps/phoenix
COPY --from=deps /app/deps/phoenix_html /deps/phoenix_html
COPY --from=deps /app/deps/phoenix_live_view /deps/phoenix_live_view
RUN npm ci
COPY assets /app
RUN npm run deploy

FROM deps as releaser
COPY . /app/
COPY --from=frontend /priv/static apps/chirp_web/priv/static
RUN MIX_ENV=prod mix do phx.digest, release

FROM alpine:3.11 as runner
RUN apk add --no-cache -U bash libssl1.1

WORKDIR /app
COPY --from=releaser /app/_build/prod/rel/chirp /app

RUN \
  adduser -s /bin/sh -u 1001 -G root -h /app -S -D default && \
  chown -R 1001:0 /app
USER default

EXPOSE 4000
ENTRYPOINT ["/app/bin/chirp"]
CMD ["start"]