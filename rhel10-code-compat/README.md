
---

## RHEL 10 Code Compatibility

### `rhel10-code-compat`

Analyzes a codebase and produces a JSON report of its libraries and packages with RHEL 10 compatibility assessments.

**Trigger phrases:**
- "Check RHEL 10 support for this codebase"
- "Audit dependencies for RHEL 10 migration"
- "Which libraries are compatible with RHEL 10?"
- "Review whether packages will run on RHEL 10"

**Example usage:**

```
Analyze this project for RHEL 10 compatibility
```

The agent will:
1. Load the output schema (from MLflow or references/output.md)
2. Locate and read all dependency files and runtime version files
3. Detect the runtime version per language (JDK 21, Python 3.12, etc) from version files and build config
4. Extract all declared dependencies and map them to codebase packages
5. Emit a JSON report with no fences

**Output shape:**

```json
{
  "runtime_stack": [
    {
      "name": "Java",
      "runtime_version": "OpenJDK 1.8",
      "eol": true
    }
  ],
  "libraries": [
    {
      "library_name": "resteasy-jackson2-provider",
      "library_version": "4.0.0.Beta6",
      "library_version_source": "pom.xml:28",
      "library_runtime_version": "OpenJDK 1.8",
      "supported_on_rhel10": false,
      "supported_on_rhel10_reason": "Pre-release version (Beta6) is not production-supported. Runtime targets JDK 1.8 which is not available on RHEL 10 (ships JDK 21/25 only).",
      "opensource": true
    }
  ],
  "repo_packages": [
    {
      "package": "com.example.api.rest",
      "library": "resteasy-jackson2-provider",
      "reason": "Package imports org.jboss.resteasy.plugins.providers.jackson found in resteasy-jackson2-provider"
    }
  ]
}
```