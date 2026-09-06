ARG GO_VERSION=1.26.7
ARG ALPINE_VERSION=3.24.1

FROM golang:${GO_VERSION}-alpine AS builder

WORKDIR /src

# Зависимости кэшируются отдельно от исходного кода.
COPY go.mod go.sum ./
RUN go mod download && go mod verify

COPY cmd/ ./cmd/
COPY internal/ ./internal/

RUN CGO_ENABLED=0 GOOS=linux go build \
    -mod=readonly -trimpath -ldflags="-s -w" \
    -o /out/backend ./cmd/api

FROM alpine:${ALPINE_VERSION} AS runtime

RUN apk upgrade --no-cache libcrypto3 libssl3 \
    && addgroup -S -g 10001 app \
    && adduser -S -D -H -u 10001 -G app app

WORKDIR /app

COPY --from=builder /out/backend /app/backend

USER 10001:10001

EXPOSE 8081

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD wget -q -O /dev/null http://127.0.0.1:8081/health || exit 1

ENTRYPOINT ["/app/backend"]
