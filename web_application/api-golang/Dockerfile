ARG GO_VERSION=1.19
ARG ALPINE_VERSION=3.19

FROM golang:${GO_VERSION}-bullseye AS base

WORKDIR /app

COPY go.mod go.sum ./

RUN --mount=type=cache,target=/go/pkg/mod \
    --mount=type=cache,target=/root/.cache/go-build \
    go mod download && go mod verify

FROM base AS dev

RUN go install github.com/cosmtrek/air@v1.49.0 && \
    go install github.com/go-delve/delve/cmd/dlv@v1.21.2

COPY . .

CMD ["air", "-c", ".air.toml"]

FROM base AS build

ARG VERSION=dev
ARG COMMIT_SHA=unknown
ARG BUILD_DATE

RUN useradd -u 1001 gin

COPY . .

RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build \
    -ldflags="-w -s" \
    -trimpath \
    -o healthcheck ./healthcheck/healthcheck.go

RUN --mount=type=cache,target=/go/pkg/mod \
    --mount=type=cache,target=/root/.cache/go-build \
    CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build \
    -ldflags="-w -s -X main.Version=${VERSION} -X main.CommitSHA=${COMMIT_SHA} -X main.BuildDate=${BUILD_DATE}" \
    -trimpath \
    -o api-golang .

FROM alpine:${ALPINE_VERSION} AS production

RUN apk add --no-cache tzdata

RUN addgroup -S -g 1001 go && adduser -S -D -u 1001 -G go gin

WORKDIR /app

COPY --from=build --chown=gin:go /app/api-golang .

COPY --from=build --chown=gin:go /app/healthcheck .

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD ["./healthcheck"]

USER gin

ENV GIN_MODE=release

EXPOSE 8080

CMD ["./api-golang"]

FROM scratch AS scratch-runtime

WORKDIR / 

COPY --from=build /etc/passwd /etc/passwd

COPY --from=build /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/

COPY --from=build /usr/share/zoneinfo /usr/share/zoneinfo

COPY --from=build --chown=gin:gin /app/healthcheck/healthcheck healthcheck

COPY --from=build --chown=gin:gin /app/api-golang api-golang

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD ["./healthcheck"]

USER gin 

ENV GIN_MODE=release

EXPOSE 8080

CMD ["./api-golang"]

FROM gcr.io/distroless/static-debian12:nonroot AS distroless-runtime

WORKDIR /app 

COPY --from=build --chown=nonroot:nonroot /app/api-golang .

COPY --from=build --chown=nonroot:nonroot /app/healthcheck .

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD ["./healthcheck"]

USER nonroot

ENV GIN_MODE=release

EXPOSE 8080

CMD ["./api-golang"]



