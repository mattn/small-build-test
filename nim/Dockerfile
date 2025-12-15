FROM alpine:latest AS builder

RUN apk add --no-cache nim gcc musl-dev

COPY main.nim .
RUN nim c --opt:size --mm:arc --d:release --d:lto --d:strip -d:danger --panics:on --exceptions:quirky --passC:-fno-asynchronous-unwind-tables --passL:-no-pie --passL:-static -o:hello main.nim

FROM scratch
COPY --from=builder /hello /hello
CMD ["/hello"]
