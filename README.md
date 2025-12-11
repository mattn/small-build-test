# small-build-test

Minimal "hello world!" Docker Images in C, Go, Nim, Rust, and Zig.

This repository demonstrates building extremely small Docker container images that simply print **"hello world!"** and exit. Each language (C, Go, Nim, Rust, Zig) uses multi-stage builds in Alpine Linux to compile a statically linked binary, then copies it into a `scratch` image for runtime.

The goal is to compare resulting image sizes and build techniques for minimal, distributable containers (ideal for `scratch`-based deployments).

## Directory Structure

```
.
├── README.md
├── c
│   ├── Dockerfile
│   └── main.c
├── go
│   ├── Dockerfile
│   ├── go.mod
│   └── main.go
├── nim
│   ├── Dockerfile
│   └── main.nim
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
# For C
cd c
docker build -t hello-c .
docker run --rm hello-c

# For Go
cd go
docker build -t hello-go .
docker run --rm hello-go

# For Nim
cd nim
docker build -t hello-nim .
docker run --rm hello-nim

# For Rust
cd rust
docker build -t hello-rust .
docker run --rm hello-rust

# For Zig
cd zig
docker build -t hello-zig .
docker run --rm hello-zig
```

Note on image sizes: Actual compressed and uncompressed sizes vary by architecture and exact compiler versions. Build them locally to measure (e.g., docker images hello-go).

## Imaeg sizes

```
IMAGE               ID             DISK USAGE   CONTENT SIZE   EXTRA
hello-c:latest      1b8ad032a535       32.3kB         7.69kB
hello-go:latest     a271d8efdbc9       2.19MB          685kB
hello-nim:latest    6ee5577fd693         35kB         10.4kB
hello-rust:latest   fddb2fc1f9c7        615kB          206kB
hello-zig:latest    7f05505c80fe       28.9kB         8.45kB
```

## Key Techniques

Static linking: Ensures the binary has no external dependencies, allowing execution in the empty scratch image.
Multi-stage builds: Use Alpine (or official images) for compilation, then discard everything except the final binary.
Optimization flags: Strip debug symbols and optimize for size where possible.

### C
- Uses musl libc on Alpine for fully static linking.
- Compilation flags: `-static -Os -s` (static link, optimize for size, strip symbols).

### Go
- Uses `CGO_ENABLED=0` for static linking.
- Build flags: `-ldflags '-w -s'` (strip debug info and symbols).

### Nim
- Uses Nim's C backend with musl on Alpine for fully static linking.
- Compilation flags: `--opt:size --gc:arc -d:release -d:lto -d:strip -d:danger` (optimize for size, lightweight ARC garbage collector, LTO, strip symbols, disable runtime checks).
- Produces extremely small static binaries, often comparable to C or Zig.

### Rust
- Targets `x86_64-unknown-linux-musl` for musl-based static linking.
- Release mode with size optimizations (`opt-level=z`, LTO, strip).

### Zig
- Zig produces fully static binaries by default.
- Simple single-file build (no `build.zig` needed for this minimal case).
- Optimization: `-O ReleaseSmall --strip`.

## License

MIT

## Author

Yasuhiro Matsumoto (a.k.a. mattn)
