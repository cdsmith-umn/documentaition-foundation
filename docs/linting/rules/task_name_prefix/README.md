# Ansible Lint: `task_name_prefix` Configuration Option

The `task_name_prefix` option allows you to customize the required prefix for task names used with the `name[prefix]` rule, which enforces a consistent naming convention.

## Configuration

You can set this option in your Ansible Lint configuration file (`.ansible-lint`, `.ansible-lint.yml`, etc.):

```yaml
task_name_prefix: "{stem} | "
```

## Behavior

* `task_name_prefix`: A string specifying the required prefix for task names. The special variable `{stem}` is replaced with the filename (without extension) of the file containing the task; if `{stem}` is not included, the prefix is a fixed string.

## Explanation

The `name[prefix]` rule checks whether task names start with a specific prefix, helping to maintain a consistent and organized structure in your playbooks and roles, and improving readability. The `task_name_prefix` option defines this prefix. By using the `{stem}` variable, you can automatically include the name of the file containing the task in the prefix, which is useful for identifying the origin of a task, especially in larger projects.

For example, with `task_name_prefix: \"{stem} | \"`, a task in `my_task.yml` must start with `"my_task | "`, while setting `task_name_prefix: \"TASK | \"` requires all tasks to start with `"TASK | "`, regardless of their file.

## Important Considerations

* **String Value:** The `task_name_prefix` option must be a string value.
* **`{stem}` Substitution:** The special variable `{stem}` is a placeholder that Ansible Lint replaces with the filename containing the task. This allows for dynamic, file-specific prefixes.
* **Rule Dependency:** This option has no effect unless the `enable_list.name[prefix]` rule is also enabled. (See [enable_list](../enable_list/README.md) for more.) The `name[prefix]` rule is what enforces the defined prefix.
* **Consistent Naming:** Use `task_name_prefix` to establish and enforce a uniform task naming convention across your Ansible projects. Consistent naming improves playbook and role organization, making them easier to navigate and understand.
* **Enhanced Readability:** A well-chosen prefix, especially when combined with `{stem}`, can significantly enhance the readability and maintainability of your Ansible code. It provides context about the task's origin and purpose.
* **Best Practice:** While useful, avoid overly complex prefixes. Aim for a balance between providing helpful context and keeping task names concise.

## Example Use Case

Let's say you're working on a large Ansible project with multiple roles and playbooks, and you want to enforce a consistent naming convention for your tasks to improve readability and maintainability. You decide that all task names should start with the name of the file they're defined in, followed by a separator.

Here's how you can use the `task_name_prefix` option to achieve this:

1. Set the `task_name_prefix` in your `.ansible-lint` configuration file:

```yaml
# .ansible-lint.yml
task_name_prefix: "{stem} | "
```

2. Apply the `name[prefix]` rule:

Ensure that the `name[prefix]` rule is enabled in your Ansible Lint configuration. This rule, combined with the `task_name_prefix` option, will enforce the prefix.

3. Example Task Files:

Now, consider these example task files:

* `roles/webserver/tasks/install_apache.yml`:

```yaml
- name: install_apache.yml | Install Apache
  apt:
    name: apache2
    state: present
```

* `roles/webserver/tasks/configure_firewall.yml`:

```yaml
- name: TASK | Open port 80
  ufw:
    rule: allow
    port: '80'
```

4. Ansible Lint Behavior:

With the `task_name_prefix` set as `"{stem} | "`, Ansible Lint will check if the task names adhere to the specified prefix.

In `install_apache.yml`, the task name `"install_apache.yml | Install Apache"` is valid because it starts with `"install_apache.yml | "`.

In `configure_firewall.yml`, the task name `"TASK | Open port 80"` is not valid because it starts with "TASK | " when it should start with `"configure_firewall.yml |"`.

## Benefits:

* **Clarity:** It's immediately clear from the task name which file defines the task. This is especially helpful when debugging or navigating large playbooks.
* **Consistency:** Enforces a uniform naming convention across all your roles and playbooks.
* **Maintainability:** Makes it easier for developers to understand the codebase and follow naming conventions.
* **Automation:** Ansible Lint automatically checks for compliance, ensuring that the naming convention is consistently followed.

In summary, the `task_name_prefix` option, combined with the `name[prefix]` rule, allows you to automate the enforcement of a clear and consistent task naming convention, significantly improving the organization and maintainability of your Ansible code.

---

[Return to Main Linting README](../../README.md)
