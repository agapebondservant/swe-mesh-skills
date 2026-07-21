---
name: rhel10-code-compat
description: Analyzes a codebase's dependencies and assesses their compatibility
  with Red Hat Enterprise Linux 10. Use during code analysis when the
  developer asks to check RHEL 10 support, audit dependencies for RHEL 10 migration, analyze
  library compatibility, or review whether packages will run on RHEL 10.
license: MIT
---

# RHEL 10 Code Compatibility Analysis

Reviews a codebase and produces a JSON report of its libraries and packages
with RHEL 10 compatibility assessments.

## Workflow

1. Locate every dependency file in the codebase and list them before proceeding.

2. For each dependency file found, read it completely.

3. For each dependency file, extract every declared dependency and map it
   to the relevant package in the codebase.

4. Generate the output described in `references/output-schema.md` as flattened
   YAML using the following rules:
   - Flatten all nested structures into a single level using `_` as the separator.
   - List items are indexed numerically: the first library's name is
     `libraries_0_library_name`, the second is `libraries_1_library_name`, etc.
   - Multiline string values must use YAML literal block style (|).
   - Omit any field whose value is null, an empty string, or an empty list —
     except `supported_on_rhel10`, which must always be included even when null.

   Example structure:
   ```
   libraries_0_library_name: spring-boot
   libraries_0_library_version: 3.2.1
   libraries_0_supported_on_rhel10: true
   libraries_1_library_name: resteasy-jackson2-provider
   libraries_1_library_version: 4.0.0.Beta6
   libraries_1_supported_on_rhel10: false
   repo_packages_0_package: com.acme.payments
   repo_packages_0_library: spring-boot
   ```

5. Retrieve the output schema by trying the following in order:
   
     a. Fetch the schema from MLflow at {mlflow_schema_url}. If the fetch
        succeeds, use that schema as the source of truth.                                                                         
     b. If MLflow is unreachable or returns an error, fall back to reading
        references/code_metadata_schema.json if it exists. Otherwise, skip
        this step.                                                                                                      
   If an output schema exists, Validate your output field names against the 
   retrieved schema before writing. Fix any mismatches.

6. Write the YAML to a file named `rhel10-compat-report.txt` in the root of
   the analyzed codebase. If the user specified a different filename or path,
   use that instead. Confirm the file path after writing.
