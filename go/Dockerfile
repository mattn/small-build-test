FROM golang:alpine AS builder
WORKDIR /app
COPY main.go go.mod .
RUN CGO_ENABLED=0 go build -ldflags '-w -s' -o hello

FROM scratch
COPY --from=builder /app/hello /hello
CMD ["/hello"]
