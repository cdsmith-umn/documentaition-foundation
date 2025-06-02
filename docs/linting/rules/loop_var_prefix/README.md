# Ansible Lint: `loop_var_prefix` Configuration Option

The `loop_var_prefix` option enables checking loop variable prefixes in roles.

## Configuration

You can set this option in your Ansible Lint configuration file (`.ansible-lint`, `.ansible-lint.yml`, etc.):

```yaml
loop_var_prefix: "^(__|{role}_)"
```

## Behavior

This option enforces a naming convention for loop variables within Ansible roles to prevent namespace collisions and unintended variable overwrites.

* `loop_var_prefix`: Specifies a regular expression pattern for loop variable names.
  * The default pattern `\"^(__|{role}_)\"` allows:
    * Variables starting with a double underscore (e.g., `__item`).
    * Variables starting with the role name followed by an underscore (e.g., `install_pm2_item` in the `install_pm2` role).

## Explanation

The `loop_var_prefix` option helps ensure proper scoping of loop variables within roles, preventing accidental data overwrites and reducing variable name conflicts.

For example, with the default setting:

* `__item` and `install_pm2_item` are valid loop variable names.
* `item` and `_item` are invalid.

## Important Considerations

* This option applies to loop variables in Ansible roles.
* The value is a regular expression, allowing flexible naming.
* Ensure your regular expression is correct.
* Consistent use of `loop_var_prefix` improves code maintainability and reduces errors in complex playbooks.

## Recommendation

It is recommended to use `loop_var_prefix` to enforce consistent and role-specific naming conventions for loop variables, improving code reliability and maintainability.

---

[Return to Main Linting README](../../README.md)
