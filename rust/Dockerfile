FROM rust:alpine AS builder
RUN apk add --no-cache musl-dev
WORKDIR /app
COPY . .
RUN cargo rustc --release -- -C opt-level=z -C lto -C link-args=-Wl,-x,-S

FROM scratch
COPY --from=builder /app/target/release/rust-bin /hello
CMD ["/hello"]
