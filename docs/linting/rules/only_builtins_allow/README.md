# Ansible Lint: `only_builtins_allow_collections` and `only_builtins_allow_modules` Configuration Options

The `only_builtins_allow_collections` and `only_builtins_allow_modules` options allow you to specify collections and modules that the `only-builtins` rule should treat as allowed. By default, the `only-builtins` rule restricts the use of modules to only the core Ansible modules.

## Configuration

You can set these options in your Ansible Lint configuration file (.ansible-lint, .ansible-lint.yml, etc.):

```yaml
only_builtins_allow_collections:
  - "example_ns.example_collection"

only_builtins_allow_modules:
  - "example_module"
  - "another_namespace.another_collection.another_module"
```

## Behavior

* `only_builtins_allow_collections`: A list of collection names that the `only-builtins` rule should treat as allowed.
* `only_builtins_allow_modules`: A list of module names that the `only-builtins` rule should treat as allowed.

## Explanation

Ansible Lint's `only-builtins` rules restricts the use of modules to a specific set, often the core Ansible modules. This rule aims to enforce the use of only the most basic and widely available modules.

However, in some situations, you might need to use modules or collections that are not considered "built-ins." This can include:

* **Modules in Officially Supported Collections:** Modules provided by Red Hat or the Ansible community that, while not in the core Ansible package, are still considered reliable and well-maintained.
* **Custom Modules or Collections:** Modules or collections developed internally within your organization.

The `only_builtins_allow_collections` and `only_builtins_allow_modules` options provide a way to tell Ansible Lint that these additional collections and modules are acceptable, even when the `only-builtins` rule is active. Ansible Lint will then allow the use of modules from these specified collections and the listed modules, without triggering a violation of the `only-builtins` rule.

For example, if you are using a module named `my_company.special_collection.special_module` and the `only-builtins` rule is enabled, you would add `my_company.special_collection` to the `only_builtins_allow_collections` list, or `my_company.special_collection.special_module` to `only_builtins_allow_modules`. This prevents Ansible Lint from reporting an error about using a non-builtin module.

## Important Considerations

* **Extending Allowed Modules:** These options extend the set of modules considered "allowed" by the `only-builtins` rule. They do not affect any other linting rules.
* **Fully Qualified Names:** For collections and modules within collections, you should use their fully qualified collection name (FQCN), e.g., `my_namespace.my_collection.my_module`. This ensures Ansible Lint correctly identifies the collection or module.
* **Rule-Specific:** These options only affect the behavior of the `only-builtins` rule.
* **Careful Use:** Use these options judiciously. Overly broad exceptions can weaken the effectiveness of the `only-builtins` rule and may lead to your Ansible code being less portable. It's generally better to use built-in modules where possible and only allow exceptions for truly necessary non-built-in modules and collections.

---

[Return to Main Linting README](../../README.md)
