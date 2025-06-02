# Ansible Lint: `var_naming_pattern` Configuration Option

The `var_naming_pattern` option enforces a naming convention for variables, in addition to Ansible's own requirements (such as avoiding Python keywords).

## Configuration

You can set this option in your Ansible Lint configuration file (`.ansible-lint`, `.ansible-lint.yml`, etc.):

```yaml
var_naming_pattern: "^[a-z_][a-z0-9_]*$"
```

## Behavior

* `var_naming_pattern`: Specifies a regular expression pattern that variable names must match.
    * The default pattern `\"^[a-z_][a-z0-9_]*$\"` enforces a stricter naming convention than Ansible's default, by:
        * Allowing variable names to start with an underscore (`_`).
        * Disallowing the use of uppercase letters.

## Explanation

The `var_naming_pattern` option lets you enforce a consistent and stricter variable naming convention in your Ansible code. This improves readability, simplifies maintenance, and prevents errors caused by inconsistent naming.

The default pattern ensures that variable names:

* Start with a lowercase letter or an underscore.
* Contain only lowercase letters, numbers, and underscores.

To disable this check, you can add `var-naming` to the `skip_list` in your Ansible Lint configuration:
```yaml
skip_list:
  - var-naming
```

## Important Considerations

* This option applies to all variable names in your Ansible code (playbooks, roles, etc.).
* The value is a regular expression, providing flexibility in defining your naming convention.
* Ensure your regular expression is correct to avoid unintended errors or overly restrictive rules.
* Using a consistent `var_naming_pattern` can significantly improve code readability and collaboration, especially in large projects.

---

[Return to Main Linting README](../../README.md)
