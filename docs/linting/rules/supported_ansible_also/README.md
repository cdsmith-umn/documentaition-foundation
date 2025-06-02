# Ansible Lint: `supported_ansible_also` Configuration Option

The `supported_ansible_also` option allows you to specify additional Ansible versions that your content is compatible with, beyond the default supported version. This is useful for ensuring your linting process considers compatibility with a broader range of Ansible versions.

## Configuration

You can set this option in your Ansible Lint configuration file (`.ansible-lint`, `.ansible-lint.yml`, etc.):

```yaml
supported_ansible_also:
  - "2.18"
  - "2.19"
```

## Behavior

`supported_ansible_also`: A list of strings, where each string represents an Ansible version. Ansible Lint will also consider these versions as supported.

## Explanation

Ansible Lint, by default, checks your Ansible content for compatibility with a specific Ansible version. However, your content might also work with older or newer versions. The `supported_ansible_also` option allows you to inform Ansible Lint about these additional compatible versions.

This is particularly helpful in scenarios where you maintain Ansible content that needs to support multiple Ansible versions. By specifying these versions, you ensure that Ansible Lint doesn't flag compatibility issues that are not relevant to your use case.

For example, if your content works with Ansible 2.18 and 2.19 in addition to the default supported version, you can configure `supported_ansible_also` as shown in the example above. Ansible Lint will then consider your content compatible with those versions as well.

## Important Considerations

* **List of Strings:** The `supported_ansible_also` option expects a list of strings, where each string represents a valid Ansible version number (e.g., "2.18", "2.19", "2.20").
* **Version Format:** Ensure that the version strings adhere to the standard Ansible versioning format.
* **Default Version:** This option does not replace the default supported Ansible version; it only adds *additional* supported versions.
* **Compatibility Checks:** Ansible Lint uses this information to adjust its compatibility checks, potentially suppressing warnings or errors that would be relevant only to unsupported versions.
* **Testing:** While this option can help avoid false positives during linting, it's still crucial to test your Ansible content against all the Ansible versions you intend to support.

---

[Return to Main Linting README](../../README.md)
