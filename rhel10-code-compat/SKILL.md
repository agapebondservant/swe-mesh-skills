---
name: rhel10-code-compat
description: Analyzes a codebase's dependencies and assesses their compatibility
  with Red Hat Enterprise Linux 10. Use during code analysis when the
  developer asks to check RHEL 10 support, audit dependencies for RHEL 10 migration, analyze
  library compatibility, or review whether packages will run on RHEL 10.
license: MIT
parameters:
  mlflow_schema_url:
    description: URL of the output schema artifact in MLflow. Used in Step 1 to
      validate output field names. Omit if MLflow is not available; the skill
      will fall back to references/output.md.
    required: false
---

# RHEL 10 Code Compatibility Analysis

Reviews a codebase and produces a JSON report of its libraries and packages
with RHEL 10 compatibility assessments.

## Workflow

1. Load the output schema by trying the following in order:

   a. If {mlflow_schema_url} was provided by the caller, fetch the schema from
      that URL. If the fetch succeeds, use that schema as the source of truth.
   b. If {mlflow_schema_url} was not provided, or MLflow is unreachable or
      returns an error, fall back to reading `references/output.md` if it exists.
      Otherwise, skip schema validation.

2. Locate every dependency file and runtime version file in the codebase and
   list them before proceeding. Dependency files include (but are not limited to):
   `pom.xml`, `build.gradle`, `requirements.txt`, `pyproject.toml`, `package.json`,
   `bower.json`, `go.mod`, `Gemfile`, `Cargo.toml`. Runtime version files include
   (but are not limited to): `.nvmrc`, `.python-version`, `.java-version`,
   `.tool-versions`, `Dockerfile`, `.ruby-version`.

3. For each file found, read it completely.

4. For each dependency file, extract every declared dependency and map it to the
   relevant package in the codebase. For each runtime version file and build
   configuration, detect each software component (language runtime, framework,
   database, middleware) and its version; produce one `runtime_stack` entry per
   component. For each `runtime_stack` entry, assess known CVEs (`vulnerabilities`)
   and whether the version is end-of-life (`eol`). Set `library_runtime_version`
   on each library to the runtime it requires (e.g., OpenJDK 21 for a Java library,
   Python 3.12 for a Python library).

5. Generate the output described in `references/output.md` as a JSON object
   with the following rules:
   - Omit any field whose value is null, an empty string, or an empty list —
     except `supported_on_rhel10` and `eol`, which must always be included even when null.
   - If a schema was loaded in Step 1, validate your output field names against
     it and fix any mismatches before writing.
   - Output must be valid JSON parseable by `json.loads()`. Do not use YAML format.
     WRONG (YAML): `runtime_stack:\n- name: Java\n  runtime_version: OpenJDK 21`
     CORRECT (JSON): `{"runtime_stack": [{"name": "Java", "runtime_version": "OpenJDK 21", "eol": false}]}`
   - Do not include any markdown formatting, backticks, or preamble.

   Example structure:
   ```json
   {
     "runtime_stack": [
       {
         "name": "Java",
         "runtime_version": "OpenJDK 21",
         "eol": false
       },
       {
         "name": "Angular",
         "runtime_version": "17.3",
         "eol": false
       },
       {
         "name": "MySQL",
         "runtime_version": "8.0",
         "eol": false
       }
     ],
     "libraries": [
       {
         "library_name": "spring-boot",
         "library_version": "3.2.1",
         "library_version_source": "pom.xml:12",
         "library_runtime_version": "OpenJDK 21",
         "deprecated": false,
         "vulnerabilities": ["CVE-2021-44228"],
         "supported_on_rhel10": true,
         "supported_on_rhel10_reason": "Spring Boot 3.2.1 is compatible with OpenJDK 21 which ships with RHEL 10",
         "opensource": true
       },
       {
         "library_name": "resteasy-jackson2-provider",
         "library_version": "4.0.0.Beta6",
         "library_version_source": "pom.xml:28",
         "library_runtime_version": "OpenJDK 21",
         "deprecated": false,
         "supported_on_rhel10": false,
         "supported_on_rhel10_reason": "Beta version not supported in production RHEL 10 environments",
         "opensource": true
       }
     ],
     "repo_packages": [
       {
         "package": "com.acme.payments",
         "library": "spring-boot",
         "reason": "Uses Spring Boot's @RestController and @Service annotations for REST API implementation"
       }
     ]
   }
   ```

6. Write the JSON to a file named `rhel10-compat-report.txt` in the root of
   the analyzed codebase. If the user specified a different filename or path,
   use that instead. Confirm the file path after writing.
