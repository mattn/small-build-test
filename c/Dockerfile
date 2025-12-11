FROM alpine:latest AS builder

RUN apk add --no-cache build-base musl-dev

COPY main.c .
RUN gcc -static -Os -s -o hello main.c

FROM scratch
COPY --from=builder /hello /hello
CMD ["/hello"]
