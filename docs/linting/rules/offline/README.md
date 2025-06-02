# Ansible Lint: `offline` Configuration Option

The `offline` option disables the installation of requirements.yml and schema refreshing.

## Configuration

You can set this option in your Ansible Lint configuration file (.ansible-lint, .ansible-lint.yml, etc.):

```yaml
offline: true
```

## Behavior
* `offline`: `true`
  * Disables the automatic installation of any roles or collections listed in a requirements.yml file.
  * Disables the refreshing of the schema used for validating Ansible files.
* `offline`: `false` (default)
  * Allows Ansible Lint to install roles/collections from requirements.yml and refresh the schema.

## Explanation

By default, Ansible Lint may attempt operations requiring network access:

* **Installing Roles/Collections:** If your Ansible project includes a `requirements.yml` file, Ansible Lint may try to install the listed roles or collections.
* **Schema Refreshing:** Ansible Lint uses a schema to validate the structure and syntax of Ansible files (e.g., playbooks), and this schema may be updated periodically.

The `offline` option allows you to disable these network-dependent operations. This is useful in environments where network access is limited, unavailable, or undesirable during the linting process.

## Important Considerations

* **No Dependency Installation:** When `offline: true`, Ansible Lint will not attempt to install roles or collections from requirements.yml. This means you must ensure that all required dependencies are already installed in your environment before running Ansible Lint. If the required roles/collections are not present, Ansible Lint may report errors.
* **Stale Schema:** Disabling schema refreshing means that Ansible Lint will use the schema that is currently available in your environment. If this schema is outdated, Ansible Lint might not be able to correctly validate newer Ansible syntax or features.
* **Default Behavior:** The default behavior of Ansible Lint is to allow network access for dependency installation and schema refreshing.
* **Environments Where Offline is Useful:**
  * Air-gapped environments: Systems with no internet connection.
  * Consistent Linting: Ensuring consistent linting results across environments by preventing schema updates.
  * Speed: Potentially speeding up the linting process by skipping network operations.
  * CI/CD Optimization: Avoiding unnecessary network calls in CI/CD pipelines.

---

[Return to Main Linting README](../../README.md)
