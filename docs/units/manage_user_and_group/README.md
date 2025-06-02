# Testing User and Group Management Role with Molecule

This document explains the automated tests for the `manage_user_and_group` Ansible role, specifically within the `manage_user_and_group_unit` Molecule scenario. These tests ensure the reliability and correctness of this role's user and group management capabilities on Linux-based systems, based on variables defined in the scenario's `vars/main.yml` file.

## What This Testing Scenario Validates

The `manage_user_and_group_unit` scenario focuses on validating the core functionality of the `manage_user_and_group` Ansible role on Linux systems. It aims to confirm that the role can:

* Create, modify, and delete user accounts based on variables defined in the scenario's `vars/main.yml` file.
* Create, modify, and delete user groups based on variables defined in the scenario's `vars/main.yml` file.
* Ensure users can be made members of specified groups, as configured in the scenario's `vars/main.yml` file.
* Handle different states (e.g., ensuring a user or group is present or absent), controlled by variables in the scenario's `vars/main.yml` file.

By running this scenario, we automatically verify that the `manage_user_and_group` role functions as expected in a standard Linux environment using the specific configurations provided in the `vars/main.yml` file within this scenario.

<details>
  <summary>Click to expand: Prerequisites for Running These Tests</summary>

## Prerequisites for Running These Tests

To execute the tests within the `manage_user_and_group_unit` scenario, you need the following tools installed and configured on your system:

* **Python:** Molecule relies on a Python environment.
* **pip:** The Python package installer.
* **Ansible:** Molecule orchestrates Ansible playbooks.
* **Molecule:** The Molecule package itself (`pip install molecule`).
* **A Containerization Tool (e.g., Docker, Podman):** This scenario uses Podman (a containerization tool) to create and manage the Linux test environment. This is configured in the scenario's Molecule settings.

</details>

## `molecule.yml` Configuration

The `molecule.yml` file in the `molecule/manage_user_and_group_unit/` directory defines how Molecule will test the `manage_user_and_group` Ansible role in this specific unit testing scenario.

<details>
  <summary>Click to expand: Required Scenario Settings</summary>

### `scenario`

```yaml
scenario:
  name: manage_user_and_group_unit
```

  * **`name`**: This parameter **is required** and specifies the name of the Molecule scenario.

### `driver`

```yaml
driver:
  name: podman
```

  * **`name`**: This parameter **is required** and defines the driver used to create and manage the test environment (Podman).

### `platforms`

```yaml
platforms:
  - name: rhel9
    image: rhel9_base_image:latest
    command: /sbin/init
    privileged: true
    ports:
      - "2225:22"
    image_pull_policy: "never"
```

  * **`name`**: This parameter **is required** and is the name of the platform (`rhel9`).
  * **`image`**: This parameter **is required** and specifies the container image used (`rhel9_base_image:latest`).

    The other settings under `platforms` (`command`, `privileged`, `ports`, `image_pull_policy`) are optional but are commonly used to configure the test container appropriately.

### `provisioner`

```yaml
provisioner:
  name: ansible
  env:
    ANSIBLE_ROLES_PATH: ../../roles/
    # ANSIBLE_VERBOSITY: 5
```

  * **`name`**: This parameter **is required** and specifies the provisioner (`ansible`).
  * **`env`**: This parameter is optional but is often used to define environment variables for Ansible, such as `ANSIBLE_ROLES_PATH` (which is crucial for Ansible to find your roles).

</details>

<details>
  <summary>Click to expand: Optional Scenario Settings</summary>

### `dependency`

```yaml
dependency:
  name: galaxy
```

* This section configures how Molecule manages dependencies required for your tests. Here, it specifies that Ansible Galaxy will be used (`name: galaxy`). This is optional; you might have no dependencies or manage them in other ways.

### `provisioner`

```yaml
provisioner:
  lint: ansible-lint
  remote_user: ansible
  connection_options:
    ansible_ssh_pass: "password"
```

* While the `name: ansible` is a required setting at the scenario level under the `provisioner` section, the following settings defined in the global configuration are optional and will be inherited if not specified in the scenario's `molecule.yml`:
    * **`lint`**: Specifies the linter to use for Ansible code. Here, `ansible-lint` is configured. You might override this in a specific scenario if needed.
    * **`remote_user`**: Defines the user to connect to the test instance with via SSH. The global default is `ansible`. You might need to change this per platform or scenario.
    * **`connection_options`**: Allows you to define specific SSH connection parameters. The global configuration sets the SSH password to `password` (`ansible_ssh_pass: "password"`). You might need to adjust these options based on your test environment.

### `verifier`

```yaml
verifier:
  name: ansible
```

* Specifies the verifier (`ansible`). This setting is optional at the scenario level. If not defined here, it will inherit the verifier configuration from the global `molecule.yml` file (if one exists). You can explicitly define it here to override the global setting for this specific `manage_user_and_group_unit` scenario if needed.

Molecule offers various other optional settings within each section (`scenario`, `driver`, `platforms`, `provisioner`, `verifier`). Refer to the official Molecule documentation for a comprehensive list of all available settings and their usage.
</details>

<details>
  <summary>Click to expand: Configuring the Test Environment</summary>

## `converge.yml` - Configuring the Test Environment

The `converge.yml` playbook is executed by Molecule to bring the test environment (the RHEL 9 container in this case) to the state required for testing the `manage_user_and_group` role. Here's a breakdown of its contents:

```yaml
---
- name: Converge manage_user_and_group
  hosts: all
  become: true
  tasks:
    - name: Import the manage_user_and_group role
      ansible.builtin.import_role:
        name: "manage_user_and_group"
```

* **`- name: Converge manage_user_and_group`**: This defines a play with a descriptive name.
    * **`vars_files:`**: Includes the variables defined in `vars/main.yml`.
    * **`Include the manage_user_and_group role`**: This task uses the `ansible.builtin.include_role` module to include and execute the `manage_user_and_group` Ansible role.

This `converge.yml` playbook's purpose is to invoke the `manage_user_and_group` role on the test container, allowing the role to perform its configuration tasks based on its internal logic and any variables defined in the scenario's `vars/main.yml` file (which become available due to the `import_role`).

</details>

<details>
  <summary>Click to expand: Defining Test Variables</summary>

## `vars/main.yml`

The `vars/main.yml` file within the `molecule/manage_user_and_group_unit/` scenario defines the variables that will be used by the `manage_user_and_group` role during the convergence phase (`converge.yml`). These variables dictate the desired state of the user and group being managed in the test environment.

```yaml
---
masquerade_state: present
masquerade_user: "moleculeAdm"
masquerade_user_group: "moleculeAdm"
```

* **`masquerade_state`**: This variable defines the desired state of the user and group. In this case, it is set to `present`, indicating that the test should ensure the user and group exist. The `manage_user_and_group` role uses this variable to determine whether to create or remove the user and group.

* **`masquerade_user`**: This variable specifies the name of the user that the `manage_user_and_group` role should manage. Here, the user's name is set to `"moleculeAdm"`.

* **`masquerade_user_group`**: This variable defines the name of the primary group that the `manage_user_and_group` role should manage and potentially assign to the user defined by `masquerade_user`. In this configuration, the group's name is also set to `"moleculeAdm"`.

By defining these variables, you are telling Molecule and the Ansible role the specific user and group configuration you want to test in this unit scenario. The `converge.yml` playbook will apply the `manage_user_and_group` role using these variables, and the subsequent `verify.yml` playbook will assert that the user and group exist in the specified state.

</details>

<details>
  <summary>Click to expand: Asserting the Desired State</summary>

## `verify.yml` - Asserting the Desired State

The `verify.yml` playbook is executed by Molecule after the `converge.yml` playbook to assert that the system has reached the desired state.

```yaml
---
- name: Verify create user and group
  hosts: all
  vars_files:
    - vars/main.yml
  tasks:
    - name: "Get '{{ masquerade_user_group }}' user group state"
      group:
        name: "{{ masquerade_user_group }}"
      register: group_state

    - name: "Check that '{{ masquerade_user_group }}' user group exists"
      assert:
        that:
          - group_state is defined
          - group_state.state == "present"
        fail_msg: "User group {{ masquerade_user_group }} is not present"
        success_msg: "User group {{ masquerade_user_group }} is present"

    - name: "Get '{{ masquerade_user }}' user state"
      user:
        name: "{{ masquerade_user }}"
      register: user_state

    - name: "Check that '{{ masquerade_user }}' user exists"
      assert:
        that:
          - user_state is defined
          - user_state.state == "present"
        fail_msg: "User {{ masquerade_user }} is not present"
        success_msg: "User {{ masquerade_user }} is present"

    - name: "Check that '{{ masquerade_user }}' user in '{{ masquerade_user_group }}' group"
      assert:
        that:
          - user_state.group is defined
          - user_state.group == group_state.gid
        fail_msg: "User {{ masquerade_user }} is not in {{ masquerade_user_group }} group"
        success_msg: "User {{ masquerade_user }} is in {{ masquerade_user_group }} group"
```

* **`vars_files:`**: Includes the variables defined in `vars/main.yml`.
* **`Get '{{ masquerade_user_group }}' group state`**: Gathers information about the specified group and registers it in `group_state`.
* **`Check that '{{ masquerade_user_group }}' user group exists`**: Checks if the `group_state` indicates the group is present.
* **`Get '{{ masquerade_user }}' user state`**: Gathers information about the specified user and registers it in `user_state`.
* **`Check that '{{ masquerade_user }}' user exists`**: Checks if the `user_state` indicates the user is present.
* **`Check that '{{ masquerade_user }}' user in '{{ masquerade_user_group }}' group`**: Checks if the `user_state` indicates the user belongs to the expected primary group.

If any of the assertions fail, the `molecule test` run will fail.

</details>

---

[Return to Unit Testing README](../README.md)
