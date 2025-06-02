# Ansible Lint: `mock_modules` and `mock_roles` Configuration Options

The `mock_modules` and `mock_roles` options allow you to specify modules and roles that Ansible Lint should treat as existing. By default, Ansible Lint analyzes all eligible Ansible files, such as playbooks and role files.

## Configuration

You can set these options in your Ansible Lint configuration file (`.ansible-lint`, `.ansible-lint.yml`, etc.):

```yaml
mock_modules:
  - "some_module"
  - "some_namespace.some_collection.some_module"

mock_roles:
  - "some_role"
  - "some_namespace.some_collection.some_role"
```

## Behavior

* `mock_modules`: A list of module names that Ansible Lint should treat as existing.
* `mock_roles`: A list of role names that Ansible Lint should treat as existing.

## Explanation

Ansible Lint, during its checks, attempts to find and verify ("resolve") the modules and roles used in your Ansible code. In some situations, Ansible Lint might encounter modules or roles that it cannot find. This can lead to false positives or errors, especially when dealing with:
* **Custom Modules or Roles:** Modules or roles developed internally within your organization.
* **Dynamically Provided Modules or Roles:** Modules or roles that are not installed in standard locations or whose locations are determined at runtime.
* **Dependencies not Available During Linting:** Roles or collections that are not installed in the environment where linting is being performed (e.g., in a CI/CD pipeline step that only checks syntax).

The `mock_modules` and `mock_roles` options provide a way to tell Ansible Lint to skip the existence check for these modules or roles, thus preventing errors and allowing Ansible Lint to complete its analysis. Instead of attempting to find them, Ansible Lint will assume they exist and proceed with the rest of the linting process.

For example, if you are using a custom module named `my_company.special_module` and Ansible Lint is run in an environment where this module is not installed, you would add `my_company.special_module` to the `mock_modules` list. This prevents Ansible Lint from reporting an error about the missing module.

## Important Considerations

* **Bypass Existence Checks:** These options only affect whether Ansible Lint checks for the *existence* of the specified modules or roles. They do not affect any other linting rules that might apply to the code that *uses* those modules or roles.
* **Fully Qualified Names:** For modules or roles within collections, you should use their fully qualified collection name (FQCN), e.g., `my_namespace.my_collection.my_module`. This ensures that Ansible Lint correctly identifies the module or role.
* **Minimal Use:** Use these options sparingly. Mocking too many modules or roles can hide real problems in your Ansible code. It's generally better to configure your linting environment to have the necessary dependencies available.
* `ansible-playbook --syntax-check`: These options are particularly useful when running `ansible-playbook --syntax-check`, which Ansible Lint uses internally. The syntax check often fails if it cannot resolve all modules and roles.

---

[Return to Main Linting README](../../README.md)
