# syntax=docker/dockerfile:1
FROM alpine:latest AS builder
RUN apk add --no-cache ldc gcc musl-dev
WORKDIR /app
COPY main.d .
RUN ldc2 -Oz --release --boundscheck=off --platformlib= --flto=full --static -L-Wl,-z,pack-relative-relocs -L-Wl,--build-id=none, -L-Wl,-z,norelro -L-Wl,--strip-all -of=/app/hello main.d

FROM scratch
COPY --from=builder /app/hello /hello
CMD [ "/hello" ]
