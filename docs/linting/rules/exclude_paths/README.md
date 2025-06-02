# Ansible Lint: `exclude_paths` Configuration Option

The `exclude_paths` option allows you to specify file paths or directories that Ansible Lint should ignore. By default, Ansible Lint checks all eligible files (typically Ansible playbooks and related YAML files).

## Configuration

You can set this option in your Ansible Lint configuration file (`.ansible-lint`, `.ansible-lint.yml`, etc.):

```yaml
exclude_paths:
  - "roles/external_role/"
  - "tests/"
  - "my_playbook.yml"
```

## Behavior

* `exclude_paths`: A list of file paths or directories that Ansible Lint should ignore. Any file or directory matching an item in this list will not be checked during the linting process.

## Explanation

Ansible Lint checks all eligible files (typically YAML files like playbooks, role files, and variable files) in your Ansible project by default. The `exclude_paths` option allows you to control which files and directories Ansible Lint examines, preventing it from checking specific items such as third-party roles, test directories, or files that are intentionally not meant to follow standard linting rules.

For example, if you use roles from Ansible Galaxy and place them in a directory like `roles/external_roles/`, you can add `roles/external_roles/` to the `exclude_paths` list. This prevents Ansible Lint from checking these external roles, which you might not intend to modify or which might not adhere to your project's linting standards.

## Important Considerations

* **Relative Paths:** Paths in `exclude_paths` are relative to the location of the Ansible Lint configuration file.
* **Recursive Exclusion:** Excluding a directory will also exclude all its subdirectories and files.
* **Default Behavior:** If `exclude_paths` is not defined, Ansible Lint will check all eligible files (typically Ansible playbooks and related YAML files).

---

[Return to Main Linting README](../../README.md)
