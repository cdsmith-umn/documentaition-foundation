# Ansible Lint: `extra_vars` Configuration Option
The `extra_vars` option allows you to define variables that should be assumed to exist during the linting process. This is particularly useful for satisfying variable references in your Ansible code that might not be defined within the code itself.

## Configuration
You can set this option in your Ansible Lint configuration file (.ansible-lint, .ansible-lint.yml, etc.):

```yaml
extra_vars:
  foo: bar
  multiline_string_variable: |
    line1
    line2
  complex_variable: ":{;\t$()"
```

## Behavior

* `extra_vars`: A dictionary (map) where keys are variable names and values are their corresponding values. These variables are made available to Ansible Lint during its execution.

## Explanation

Ansible Lint evaluates variable references in your Ansible code. If a variable is used but not defined in the same file, Ansible Lint might flag it as undefined. The `extra_vars` option provides values for these "external" variables, preventing false positives.

For example:

* `foo` is a simple string: `bar`.
* `multiline_string_variable` is a multi-line string, using YAML's literal (`|`) syntax to include line breaks.
* `complex_variable` includes special characters, demonstrating that `extra_vars` handles various data types.

By defining these variables in `extra_vars`, you instruct Ansible Lint to assume their existence with the specified values, even if they are not defined in the Ansible code being checked.

## Important Considerations

* **Data Types:** You can define variables of various data types in `extra_vars`, including strings, multi-line strings, and complex values.
* **Avoiding False Positives:** This option is crucial for avoiding false positives when your Ansible code relies on variables defined in external sources, such as:
  * Inventory files
  * Variable files
  * Command-line arguments (`--extra-vars`)
* **Linting Accuracy:** Using `extra_vars` improves the accuracy of Ansible Lint by providing it with the necessary context to evaluate variable references correctly.

## Use Cases with Molecule

In a Molecule testing context, `extra_vars` can be very useful.  Molecule uses scenario-specific variables defined in files like `group_vars/all.yml` or `host_vars/localhost.yml`.  If your Ansible code uses variables that are *only* defined in these Molecule scenario vars files, Ansible Lint will complain about undefined variables.  You can use `extra_vars` to silence these false positives *during linting*.

For example, if you have a variable `domain_name` in your Molecule scenario's `group_vars/all.yml`, and your Ansible role's templates use it, you can add this to your `.ansible-lint.yml`:

```yaml
extra_vars:
  domain_name: "example.com"
```

This ensures Ansible Lint doesn't report an undefined variable error.  This is not a replacement for defining the variable in Molecule, but it allows Ansible Lint to correctly analyze your code.

---

[Return to Main Linting README](../../README.md)
