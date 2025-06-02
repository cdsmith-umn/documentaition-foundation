# Ansible Lint: `use_default_rules` Configuration Option

The `use_default_rules` option controls whether the default rules are used.

## Configuration

You can set this option in your Ansible Lint configuration file (`.ansible-lint`, `.ansible-lint.yml`, etc.):

```yaml
use_default_rules: true
```

## Behavior

* `use_default_rules`: `true`
  * Explicitly enables the default rules. This is the default behavior.
* `use_default_rules`: `false`
  * Disables the default rules. This is typically used in conjunction with the `rulesdir` option to use only custom rules.

## Using Custom Rules

Ansible Lint allows you to specify custom rules by providing a directory (or directories) containing Python files that define these rules. You can use the rulesdir option to specify the location of your custom rule sets:

```yaml
use_default_rules: false
rulesdir:
  - ./path/to/custom_rules/
```

### Explanation

* `use_default_rules: false`: This line explicitly disables the use of Ansible Lint's built-in, default rules.
* `rulesdir: - ./path/to/custom_rules/`: This line specifies that Ansible Lint should look for custom rules in the directory `./path/to/custom_rules/`. It assumes that you have created this directory and populated it with your custom rule files.

### Important Considerations

* If you specify `rulesdir` and **do not** set `use_default_rules: true`, Ansible Lint will *only* use the rules found in the `./path/to/custom_rules/` directory. It will *not* apply any of its default rules.
* * If you specify `rulesdir` and set `use_default_rules: true`, Ansible Lint will use *all* the default rules, and *also* use any custom rules in `rulesdir`, with custom rules *overriding* any default rules that have the same ID.
* Ensure that the path provided to `rulesdir` is correct and relative to the location where you run `ansible-lint`, or is an absolute path.
* Your custom rule files within the `rulesdir` must follow the structure and naming conventions expected by Ansible Lint for custom rules.

### Recommendation

Use the default `use_default_rules: true` (or omit the option) and supplement with custom rules in `rulesdir` only when necessary. This ensures your Ansible code is checked against standard best practices.

---

[Return to Main Linting README](../../README.md)
