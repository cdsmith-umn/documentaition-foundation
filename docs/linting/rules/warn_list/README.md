# Ansible Lint: `warn_list` Configuration Option

The `warn_list` option allows you to specify rules or tags for which violations should be reported as warnings instead of errors.

## Configuration

You can set this option in your Ansible Lint configuration file (`.ansible-lint`, `.ansible-lint.yml`, etc.):

```yaml
warn_list:
  - "skip_this_tag"
  - "experimental"
  # - "role-name"
  # - "yaml[document-start]"
  # you can also use sub-rule matches
```

## Behavior

* `warn_list`: A list of rule IDs or tags. Violations of the specified rules or tags will be downgraded from their default severity of error to a warning.

## Explanation

Ansible Lint categorizes rule violations as either errors or warnings. Errors typically indicate more severe problems that should be addressed, while warnings suggest potential issues or deviations from best practices.

The `warn_list` option provides a mechanism to control this categorization. By adding a rule or tag to `warn_list`, you instruct Ansible Lint to report any violations of that rule or tag as a warning rather than an error. This can be useful in situations where:

* You want to gradually adopt a new rule: You can add the rule to `warn_list` to be notified of violations without immediately failing your linting process.
* You have specific exceptions: You might have a specific case where a rule violation is acceptable, and you want to acknowledge it as a warning rather than an error.
* You are dealing with experimental rules: As shown in the example, you might want to be aware of experimental rule violations but not have them block your workflow.

## Important Considerations

* Order of precedence: If a rule or tag is present in both `warn_list` and `skip_list`, `skip_list` takes precedence, and the rule will be ignored altogether.
* Rule IDs and Tags: You can specify rules either by their unique ID (e.g., `rule-name`) or by tag (e.g., `experimental`).
* Sub-rule matching: As the commented-out example shows, it's possible to use more specific matching for certain rules, like `yaml[document-start]`.
* Default behavior: Ansible Lint classifies rule violations as errors or warnings based on its built-in rules; the `warn_list` option allows you to override this default behavior.
* Impact on exit code: Downgrading an error to a warning using `warn_list` will change Ansible Lint's exit code. By default, Ansible Lint will exit with a non-zero code if any errors are found. If you use `warn_list` to convert all errors to warnings, Ansible Lint will exit with a zero code (indicating success), even if there are rule violations.

---

[Return to Main Linting README](../../README.md)
