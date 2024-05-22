ARG GO_VERSION=1.22.2

FROM golang:${GO_VERSION}-alpine AS build

ARG UPX_VERSION=4.2.1-r0

RUN apk update && apk add --no-cache ca-certificates upx=$UPX_VERSION tzdata && update-ca-certificates

WORKDIR /app

COPY go.mod go.sum /app/
RUN go mod download

RUN adduser \
  --disabled-password \
  --gecos "" \
  --home "/" \
  --shell "/sbin/nologin" \
  --no-create-home \
  --uid 64000 \
  appuser

COPY . .
RUN go build -buildvcs=false -tags netgo -trimpath -tags netgo -ldflags="-w -s" -o ./app .

RUN upx --best --lzma app

FROM scratch

COPY --from=build /etc/passwd /etc/passwd
COPY --from=build /etc/group /etc/group
COPY --from=build /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/
COPY --from=build /usr/share/zoneinfo /usr/share/zoneinfo
COPY --from=build --chown=appuser:appuser /app/app /app/app

USER appuser:appuser

ENTRYPOINT ["/app/app"]
