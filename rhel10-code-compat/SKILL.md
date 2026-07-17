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

4. Generate the output described in `references/output-schema.md` as pure JSON
   with no fences or ticks.

5. Retrieve the output schema by trying the following in order:
   
     a. Fetch the schema from MLflow at {mlflow_schema_url}. If the fetch
        succeeds, use that schema as the source of truth.                                                                         
     b. If MLflow is unreachable or returns an error, fall back to reading
        references/code_metadata_schema.json if it exists. Otherwise, skip
        this step.                                                                                                      
   If an output schema exists, Validate your output field names against the 
   retrieved schema before writing. Fix any mismatches.

6. Write the JSON to a file named `rhel10-compat-report.json` in the root of
   the analyzed codebase. If the user specified a different filename or path,
   use that instead. Confirm the file path after writing.
