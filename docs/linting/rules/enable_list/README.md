# Ansible Lint: `enable_list` Configuration Option

The `enable_list` option allows you to activate optional rules, specifically those with the `opt-in` tag. By default, Ansible Lint does not run these rules.

## Configuration

You can set this option in your Ansible Lint configuration file (`.ansible-lint`, `.ansible-lint.yml`, etc.):

```yaml
enable_list:
  - args
  - empty-string-compare # opt-in
  - no-log-password # opt-in
  - no-same-owner # opt-in
  - name[prefix] # opt-in
  - galaxy-version-incorrect # opt-in
  - yaml
```

## Behavior

* `enable_list`: A list of rule IDs or tags. Any rule whose ID or tag is included in this list, and which is marked as an opt-in rule, will be enabled during the linting process.

## Explanation

Ansible Lint has certain rules that are not enabled by default because they might be more opinionated or have the potential for false positives in some cases. These rules are tagged as `opt-in`.  If `enable_list` is not defined, these `opt-in` rules are skipped. The `enable_list` option lets you explicitly specify which of these optional rules you want Ansible Lint to enforce.

For example, the `no-log-password` rule, which checks for the use of passwords in `no_log` directives, is an `opt-in` rule. If you want Ansible Lint to check for this, you must add `no-log-password` to your `enable_list`.

The `yaml` entry in the example configuration is a special case.  Ansible Lint uses the `yamllint` library to validate YAML syntax.  By default, Ansible Lint skips these YAML checks if `yamllint` is not installed.  Adding `yaml` to `enable_list` makes Ansible Lint attempt the YAML checks even if `yamllint` is missing.  This ensures that YAML validation is performed if `yamllint` is available in your environment.

## Important Considerations

* **Opt-in Rules:** This option is specifically for rules tagged as `opt-in`.
* **Explicit Enabling:** You must explicitly list the rule IDs or tags in the `enable_list` to activate them.  Omitting a rule, even if it has the `opt-in` tag, means it will not be checked.
* **YAML Checks:** The `yaml` entry changes Ansible Lint's default behavior regarding the `yamllint` library, ensuring YAML validation is attempted when possible.

---

[Return to Main Linting README](../../README.md)
