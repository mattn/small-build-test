# small-build-test

Minimal "hello world!" Docker Images in C, D, Go, Nim, Rust, and Zig.

This repository demonstrates building extremely small Docker container images that simply print **"hello world!"** and exit. Each language (C, D, Go, Nim, Rust, Zig) uses multi-stage builds in Alpine Linux to compile a statically linked binary, then copies it into a `scratch` image for runtime.

The goal is to compare resulting image sizes and build techniques for minimal, distributable containers (ideal for `scratch`-based deployments).

## Directory Structure

```
.
├── README.md
├── c
│   ├── Dockerfile
│   └── main.c
├── d
│   ├── Dockerfile
│   └── main.d
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

# For D
cd d
docker build -t hello-d .
docker run --rm hello-d

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

## Image sizes

```
REPOSITORY                          IMAGE ID       SIZE
hello-zig                           af1a35340886   28.3kB
hello-c                             c389efafe6fe   32.3kB
hello-nim                           2466ac0b4998   38.7kB
hello-rust                          8d6a31448bdb   610kB
hello-go                            1e5c3a771a79   2.19MB
hello-d                             6fbaa97e11a2   6.17MB
```

```
$ docker images --format "table {{.Repository}}\t{{.ID}}\t{{.Size}}" | sort -h -k3
```

## Key Techniques

Static linking: Ensures the binary has no external dependencies, allowing execution in the empty scratch image.
Multi-stage builds: Use Alpine (or official images) for compilation, then discard everything except the final binary.
Optimization flags: Strip debug symbols and optimize for size where possible.

### C

- Uses musl libc on Alpine for fully static linking.
- Compilation flags: `-static -Os -s` (static link, optimize for size, strip symbols).

### D

- Uses `--static` for static linking.
- Compilation flags: `-Oz --release --flto=full -L-Wl,--strip-all` (optimize for size,LTO,strip symbols)

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
- Optimization: `-O ReleaseSmall -fno-unwind-tables -z norelro --strip`.

## License

MIT

## Author

Yasuhiro Matsumoto (a.k.a. mattn)
