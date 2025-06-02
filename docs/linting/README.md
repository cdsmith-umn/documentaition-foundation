# Ansible Lint Configuration Overview

Ansible Lint is a valuable tool for ensuring your Ansible playbooks and roles adhere to best practices and maintain a consistent style. It offers a range of configuration options to tailor its behavior to your specific needs.

## Configuration Files

Ansible Lint primarily uses configuration files to manage its settings. These files allow you to customize rules, specify ignored paths, and define other preferences. The most common configuration file is `~/.ansible-lint`.

## Configuration Options
Here's an overview of the configuration options:

* [enable_list](rules/enable_list/README.md)
* [exclude_paths](rules/exclude_paths/README.md)
* [extra_vars](rules/extra_vars/README.md)
* [kinds](rules/kinds/README.md)
* [loop_var_prefix](rules/loop_var_prefix/README.md)
* [max_block_depth](rules/max_block_depth/README.md)
* [mock_modules](rules/mocks/README.md)
* [mock_roles](rules/mocks/README.md)
* [name_prefix](rules/name_prefix/README.md)
* [offline](rules/offline/README.md)
* [only_builtins_allow_collections](rules/only_builtins_allow/README.md)
* [only_builtins_allow_modules](rules/only_builtins_allow/README.md)
* [profile](rules/profile/README.md)
* [skip_action_validation](rules/skip_action_validation/README.md)
* [skip_list](rules/skip_list/README.md)
* [supported_ansible_also](rules/supported_ansible_also/README.md)
* [task_name_prefix](rules/task_name_prefix/README.md)
* [use_default_rules](rules/use_default_rules/README.md)
* [var_naming_pattern](rules/var_naming_pattern/README.md)
* [warn_list](rules/warn_list/README.md)

Each of these list items corresponds to a specific configuration option or rule within Ansible Lint. For detailed information on how to use these options, please refer to the README.md file in the respective directory.

---

[Return to Main README](../README.md)
