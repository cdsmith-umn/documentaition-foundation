#   Ansible Lint: `skip_list` Configuration Option

The `skip_list` option allows you to specify rules or tags to exclude from the linting process.

##   Configuration

You can set this option in your Ansible Lint configuration file (`.ansible-lint`, `.ansible-lint.yml`, etc.):

```yaml
skip_list:
  - skip_this_tag
```

##   Behavior

* `skip_list`: A list of rule IDs or tags that Ansible Lint should ignore. Any rule matching an item in this list will not be applied during the linting process.

##   Explanation

The `skip_list` option provides a way to suppress specific Ansible Lint rules.

**However, using `.ansible-lint-ignore` files is strongly recommended for excluding rules.** They offer significant advantages in terms of clarity and maintainability.

`.ansible-lint-ignore` files offer greater flexibility and context:

* They allow you to specify which rules to skip for specific files or directories.
* They make it clearer why a rule is being skipped, as the ignore directive is located closer to the exempted code.

Here's a basic example of an `.ansible-lint-ignore` file:
```yaml
# .ansible-lint-ignore
playbook.yml  no-jinja-when
role/tasks/main.yml  role-name
```

##   Important Considerations

* **Discouraged Use:** Avoid using `skip_list` whenever possible; use `.ansible-lint-ignore` files instead.
* Hides Violations: Using `skip_list` completely prevents violations from being reported, which can mask underlying problems and increase future maintenance burden.  `.ansible-lint-ignore` files mark skipped rules as ignored but still visible, providing a clear record of what was intentionally skipped.
* `.ansible-lint-ignore` Preference: The `.ansible-lint-ignore` file offers more precise control over ignored rules (file/directory level).

---

[Return to Main Linting README](../../README.md)
