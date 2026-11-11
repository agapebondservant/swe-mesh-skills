# Output Schema

## runtime_stack

One entry per protobuf/gRPC runtime component the application depends on. Covers
the `protoc` compiler, gRPC runtime, code-generation plugins, and the underlying
language runtime. Derive versions from build configuration, plugin declarations,
Dockerfiles, or lock files (e.g., `buf.lock`, `build.gradle` protobuf block,
`pom.xml` `protobuf-maven-plugin` configuration, `package.json` dependencies,
`go.mod` module versions):

- `name` — the software component (e.g., protoc, gRPC, Java, Python, Node.js, Go, Rust)
- `runtime_version` — the detected version (e.g., 25.1, 1.60.0, OpenJDK 21, Python 3.12)
- `vulnerabilities` — list of CVEs associated with this version, if any
- `eol` — whether this version is end-of-life (true, false, or null if unknown)

## libraries

One entry per protobuf/gRPC dependency:

- `library_name` — name of the library (e.g., grpc-java, protobuf-java, grpcio, @grpc/grpc-js, tonic, prost, google.golang.org/grpc)
- `library_version` — version of the library
- `library_version_source` — the file path and line or snippet from which the version of the library was deduced
- `vulnerabilities` — list of CVEs associated with the library version if any
- `library_runtime_version` — the runtime this library requires (e.g., OpenJDK 21, Python 3.12); must match a `runtime_version` in the `runtime_stack` array
- `deprecated` — whether or not the library is deprecated
- `opensource` — whether or not the library is open source

## repo_packages

One entry per proto package in the current codebase:

- `package` — the proto package namespace as declared in the `.proto` file's
  `package` directive (e.g., `com.acme.orders.v1`, `myapp.users.v2`), or
  the `.proto` file path if no package is declared
- `library` — name of the gRPC library used by the generated code for this
  proto package. Must be included in the list of libraries above
- `reason` — reasoning used to derive the library, including which `.proto`
  files define services or messages in this package, what code-generation
  plugin produces the stubs, and which gRPC runtime library the generated
  code depends on
