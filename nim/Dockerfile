FROM alpine:latest AS builder

RUN apk add --no-cache nim gcc musl-dev

COPY main.nim .
RUN nim c --opt:size --gc:arc --define:release --define:lto --define:strip -d:danger -o:hello main.nim

FROM scratch
COPY --from=builder /hello /hello
CMD ["/hello"]
