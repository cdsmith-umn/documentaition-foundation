# Ansible Lint: `max_block_depth` Configuration Option

The `max_block_depth` option allows you to control the maximum allowed nesting depth of blocks within your Ansible code. This helps prevent overly complex and difficult-to-read structures.

## Configuration

You can set this option in your Ansible Lint configuration file (`.ansible-lint`, `.ansible-lint.yml`, etc.):

```yaml
max_block_depth: 20
```

## Behavior

* `max_block_depth`: An integer that specifies the maximum allowed depth of nested blocks. A "block" refers to a structural element in Ansible code, such as a `block` within a task, a conditional statement (`when`), or a loop (`loop`).

## Explanation

Ansible playbooks and roles can contain nested control structures. While some nesting is necessary, excessive nesting can make code hard to follow and debug.

The `max_block_depth` option sets a limit to how deeply nested these structures can be. Ansible Lint raises an error if the nested depth in your code exceeds this limit.

For example, with `max_block_depth: 20`, Ansible Lint allows nesting up to 20 levels deep. If you have a `block` inside a `when` condition, which is inside another `block`, and so on, Ansible Lint counts each level of nesting. If that count goes above 20, a linting error is raised.

This option helps you enforce a reasonable level of complexity in your Ansible code.

## Important Considerations

* **Integer Value:** The `max_block_depth` option must be an integer.
* **Default Value:** Ansible Lint has a default value of `20` for `max_block_depth`. Setting this option in your configuration file overrides this default.
* **Error Reporting:** Ansible Lint reports an error if the maximum depth is exceeded.
* **Readability:** This option directly impacts the readability and maintainability of your code. Lower values generally lead to more readable code.
* **Balance:** Choose a value that allows for necessary nesting while preventing excessive complexity.

---

[Return to Main Linting README](../../README.md)
