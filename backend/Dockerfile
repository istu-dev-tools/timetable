# Build context: backend/ (the module is self-contained; go.work is not needed).
FROM golang:1.26-alpine AS build
WORKDIR /src

COPY go.mod go.sum* ./
RUN go mod download

COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -trimpath -ldflags="-s -w" -o /out/server ./cmd/server

FROM alpine:3.22
RUN apk add --no-cache ca-certificates tzdata \
    && addgroup -S -g 10001 timetable \
    && adduser -S -D -H -u 10001 -G timetable timetable
COPY --from=build --chown=timetable:timetable /out/server /server
ENV HTTP_ADDR=:8080
EXPOSE 8080
USER timetable:timetable
ENTRYPOINT ["/server"]
