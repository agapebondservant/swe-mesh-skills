# Output Schema

## runtime_stack

One entry per software component the application depends on at runtime. Covers language
runtimes, frameworks, databases, and middleware. Derive versions from version files,
build configuration, or runtime declarations (e.g., `.nvmrc`, `pom.xml` `<java.version>`,
`pyproject.toml` `requires-python`, `Dockerfile` base image, `application.yaml` datasource):

- `name` — the software component (e.g., Java, Python, Node.js, Angular, MySQL, PostgreSQL)
- `runtime_version` — the detected version (e.g., OpenJDK 21, Python 3.12, Node.js 22, 17.3, 8.0)
- `vulnerabilities` — list of CVEs associated with this version, if any
- `eol` — whether this version is end-of-life (true, false, or null if unknown)

## libraries

One entry per dependency:

- `library_name` — name of the library represented as a file name or dependency package
- `library_version` — version of the library
- `library_version_source` — the file path and line or snippet from which the version of the library was deduced
- `vulnerabilities` - list of CVEs associated with the library version if any
- `library_runtime_version` — the runtime this library requires (e.g., OpenJDK 21, Python 3.12); must match a `runtime_version` in the `runtime_stack` array
- `deprecated` — whether or not the library is deprecated
- `supported_on_rhel10` — whether or not the library is supported on RHEL 10. 
  Use your knowledge of the RHEL 10 environment to assess compatibility.  
  Consider factors including but not limited to: runtime version 
  compatibility (e.g. JDK 21/25, Python 3.12, Node.js 22, Go 1.23, Ruby 3.3), 
  native OS dependencies, RHEL 10 system crypto policy, library version. 
  Set to true, false, or null if you cannot determine compatibility with 
  confidence. Do not guess.
- `supported_on_rhel10_reason` — justification for why the library is
  supported on RHEL 10 or not. If null, state what information would be needed to make the determination.
- `opensource` — whether or not the library is open source

## repo_packages

  One entry per package in the current codebase:

- `package` — Fully qualified language-specific namespace or directory that
  groups related modules or classes,
  or file path for Javascript/Typescript. Stick to packages in the current codebase
- `library` — name of the library imported by the package. Must be included
  in the list of libraries above
- `reason` — reasoning used to derive the library imported by the package