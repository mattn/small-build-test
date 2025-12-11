# small-build-test

Minimal "hello world!" Docker Images in Go, Rust, and Zig.

This repository demonstrates building extremely small Docker container images that simply print **"hello world!"** and exit. Each language (Go, Rust, Zig) uses multi-stage builds in Alpine Linux to compile a statically linked binary, then copies it into a `scratch` image for runtime.

The goal is to compare resulting image sizes and build techniques for minimal, distributable containers (ideal for `scratch`-based deployments).

## Directory Structure

```
.
├── README.md
├── go
│   ├── Dockerfile
│   ├── go.mod
│   └── main.go
├── rust
│   ├── Cargo.lock
│   ├── Cargo.toml
│   ├── Dockerfile
│   └── src
│       └── main.rs
└── zig
    ├── Dockerfile
    └── main.zig
```

## Building and Running

Each subdirectory contains a complete, self-contained example with its own `Dockerfile`.

To build and run an image:

```bash
# For Go
cd go
docker build -t hello-go .
docker run --rm hello-go

# For Rust
cd ../rust
docker build -t hello-rust .
docker run --rm hello-rust

# For Zig
cd ../zig
docker build -t hello-zig .
docker run --rm hello-zig
```

Note on image sizes: Actual compressed and uncompressed sizes vary by architecture and exact compiler versions. Build them locally to measure (e.g., docker images hello-go).

Key Techniques

Static linking: Ensures the binary has no external dependencies, allowing execution in the empty scratch image.
Multi-stage builds: Use Alpine (or official images) for compilation, then discard everything except the final binary.
Optimization flags: Strip debug symbols and optimize for size where possible.

### Go

* Uses CGO_ENABLED=0 for static linking.
* Build flags: -ldflags '-w -s' (strip debug info and symbols).

### Rust

* Targets x86_64-unknown-linux-musl for musl-based static linking.
* Release mode with size optimizations (opt-level=z, LTO, strip).

### Zig

* Zig produces fully static binaries by default.
* Simple single-file build (no build.zig needed for this minimal case).
* Optimization: -O ReleaseSmall --strip.

## License

MIT

## Author

Yasuhiro Matsumoto (a.k.a. mattn)
