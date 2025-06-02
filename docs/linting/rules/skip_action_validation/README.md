# Ansible Lint: `skip_action_validation` Configuration Option

The `skip_action_validation` option controls whether Ansible Lint performs detailed validation of the actions used in your Ansible tasks.

## Configuration

You can set this option in your Ansible Lint configuration file (.ansible-lint, .ansible-lint.yml, etc.):

```yaml
skip_action_validation: false
```

## Behavior

* `skip_action_validation`: `false`  (default): Ansible Lint performs in-depth validation of the actions used in your tasks. This includes checking the validity of module names, arguments, and their types.
* `skip_action_validation`: `true`: Ansible Lint skips detailed action validation, relying on Ansible's built-in syntax checking for basic validity.

## Explanation

Ansible Lint, by default, provides more thorough checking of your Ansible tasks than Ansible's basic syntax check. This includes:

* **Module Name Validation:** Ensures you use valid Ansible module names.
* **Argument Validation:** Checks that you provide correct arguments to the modules.
* **Argument Type Validation:**  Verifies the arguments you provide are of the correct type (e.g., `string`, `integer`, `list`).

Setting `skip_action_validation`: `true` disables this detailed checking.  When disabled, Ansible Lint will still catch basic syntax errors that Ansible itself would catch when you run the playbook.  However, it might miss more subtle errors related to incorrect module usage or argument types.

The documentation states this option is not typically needed because Ansible's syntax check covers most of the same ground.  However, there might be cases where disabling this validation could be useful if you are working with highly dynamic or unusual Ansible code.

## Important Considerations

* **Default Behavior:** Ansible Lint performs action validation.
* **Redundancy:** There is some overlap between the checks performed by Ansible Lint's action validation and Ansible's own syntax checking.
* **Potential Use Cases for `true`:** While not generally recommended, setting `skip_action_validation: true` might be useful in situations with very dynamic or non-standard Ansible code where the extra validation causes issues.  However, this should be done with caution, as it could allow some errors to go undetected.

## Use Case: Highly Dynamic Module Usage

Imagine your team employs very complex Jinja2 templating or variable transformations to construct module arguments.

For example, you might have a task where the module and its parameters are determined by deeply nested conditional logic based on facts gathered from the target system.

```yaml
- name: Dynamically configure system
  #  VERY complex logic to determine module and arguments
  module_name: "{{  'service' if ansible_os_family == 'Debian' else 'systemd'  }}"
  module_args:
    name: " {{  'apache2' if ansible_distribution == 'Ubuntu' else 'httpd'  }} "
    state: " {{  'restarted' if some_variable else 'started'  }} "
```

In this case, Ansible Lint might struggle to determine the actual module being called (`service` or `systemd`) and the valid arguments for that module. This could lead to false positives, where Ansible Lint flags the task as having invalid arguments, even though the generated Ansible code would run correctly.

If your team uses this kind of highly dynamic approach, setting `skip_action_validation: true` might be a way to avoid these false positives. However, the trade-off is that you'd also lose the benefit of Ansible Lint's validation for those tasks, potentially masking real issues.

---

[Return to Main Linting README](../../README.md)
