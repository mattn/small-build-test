FROM alpine AS builder
RUN apk add --no-cache zig
WORKDIR /app
COPY main.zig .
RUN zig build-exe main.zig -O ReleaseSmall -fstrip -femit-bin=hello

FROM scratch
COPY --from=builder /app/hello /app/hello
CMD ["/app/hello"]
