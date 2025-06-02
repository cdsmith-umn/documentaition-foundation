# Testing OS Management Role with Molecule

This document explains the automated tests for the `os_management` Ansible role, specifically within the `os_management_unit` Molecule scenario. These tests ensure the reliability and correctness of this role's OS management capabilities on RHEL9-based systems.

## What This Testing Scenario Validates

The `os_management_unit` scenario focuses on validating the core functionality of the `os_management` Ansible role on RHEL9 systems. It aims to confirm that the role can:

* Import RPM keys.
* Configure DNF repositories.
* Install and remove OS packages.

By running this scenario, we automatically verify that the `os_management` role functions as expected in a standard RHEL9 environment.

<details>
  <summary>Click to expand: Prerequisites for Running These Tests</summary>

Prerequisites for Running These Tests

To execute the tests within the `os_management_unit` scenario, you need the following tools installed and configured on your system:

* Python: Molecule relies on a Python environment.
* pip: The Python package installer.
* Ansible: Molecule orchestrates Ansible playbooks.
* Molecule: The Molecule package itself (`pip install molecule`).
* A Containerization Tool (e.g., Podman): This scenario uses Podman to create and manage the RHEL9 test environment. This is configured in the scenario's Molecule settings.
* rhel9_base_image:latest: A custom Podman/Docker image tagged rhel9_base_image:latest that is configured for RHEL9 compatibility, with SELinux disabled, and capable of accessing your organization's subscription-gated DNF repositories (if applicable).
</details>

## `molecule.yml` Configuration

The `molecule.yml` file in the `molecule/os_management_unit/` directory defines how Molecule will test the `os_management` Ansible role in this specific unit testing scenario.

<details>
  <summary>Click to expand: Required Scenario Settings</summary>

### `scenario`

```yaml
scenario:
  name: os_management_unit
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
  # ... (other settings from global molecule.yml might be inherited or overridden)
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

* Specifies the verifier (`ansible`). This setting is optional at the scenario level. If not defined here, it will inherit the verifier configuration from the global `molecule.yml` file (if one exists). You can explicitly define it here to override the global setting for this specific `os_management_unit` scenario if needed.

Molecule offers various other optional settings within each section (`scenario`, `driver`, `platforms`, `provisioner`, `verifier`). Refer to the official Molecule documentation for a comprehensive list of all available settings and their usage.
</details>

<details>
  <summary>Click to expand: Configuring the Test Environment</summary>

## `converge.yml` - Configuring the Test Environment

The `converge.yml` playbook is executed by Molecule to bring the test environment (the RHEL 9 container in this case) to the state required for testing the `os_management` role. Here's a breakdown of its contents:

```yaml
---
- name: Converge os_management
  hosts: all
  become: true
  gather_facts: true
  vars_files:
    - vars/main.yml
  tasks:
    - name: Include the os_management role
      ansible.builtin.include_role:
        name: os_management
```

* **`- name: Converge os_management`**: This defines a play with a descriptive name.
    * **`vars_files:`**: Includes the variables defined in `vars/main.yml`.
    * **`Include the os_management role`**: This task uses the `ansible.builtin.include_role` module to include and execute the `os_management` Ansible role.

This `converge.yml` playbook's purpose is to invoke the `os_management` role on the test container, allowing the role to perform its configuration tasks based on its internal logic and any variables defined in the scenario's `vars/main.yml` file (which become available due to the `import_role`).
</details>

<details>
  <summary>Click to expand: Defining Test Variables</summary>

## `vars/main.yml`

The `vars/main.yml` file within the `molecule/os_management_unit/` scenario defines the variables that will be used by the `os_management` role during the converge phase (`converge.yml`). These variables dictate the desired state of the rpm keys, dnf repos, and packages being managed in the test environment.

```yaml
os_management_manage_rpm_keys: true
os_management_rpm_keys:
  - key: "https://www.elrepo.org/RPM-GPG-KEY-elrepo.org"
    state: "present"
os_management_manage_dnf_repos: true
os_management_dnf_repos:
  - name: "epel"
    description: "Extra Packages for Enterprise Linux"
    baseurl: "https://mirrors.fedoraproject.org/metalink?repo=epel-9&arch=$basearch"
    mirrorlist: "https://mirrors.fedoraproject.org/metalink?repo=epel-9&arch=$basearch"
    enabled: true
    gpgcheck: true
    gpgkey:
      - "https://dl.fedoraproject.org/pub/epel/RPM-GPG-KEY-EPEL-9"
    state: "present"
  - name: "remi"
    description: "Remi's RPM repository for Enterprise Linux 9"
    baseurl: "https://rpms.remirepo.net/enterprise/9/$basearch/"
    enabled: true
    gpgcheck: true
    gpgkey:
      - "https://rpms.remirepo.net/enterprise/RPM-GPG-KEY-remi"
    state: "present"
os_management_manage_os_packages: true
os_management_packages:
  - name: "httpd"
    state: "present"
  - name: "gzip"
    state: "absent"
os_management_update_os_packages: false
os_management_autoremove_unused_dependencies: false
```

* `os_management_manage_rpm_keys`: Boolean to control RPM key management.
* `os_management_rpm_keys`: This variable is a list of RPM GPG keys that the `os_management` role should manage. In this case, it includes the key for ELRepo.
* `os_management_manage_dnf_repos`: Boolean to control DNF repository management.
* `os_management_dnf_repos`: This variable is a list of DNF repository definitions. Each definition includes the repository's name, description, base URL, mirrorlist (if applicable), GPG key settings, and enabled status. Here, it defines repositories for EPEL and Remi.
* `os_management_manage_os_packages`: Boolean to control OS package management.
* `os_management_packages`: This variable is a list of packages that the `os_management` role should manage. For each package, it specifies the name and desired state. In this example, `httpd` should be installed (`present`), while `gzip` should be removed (`absent`).
* `os_management_update_os_packages`: Boolean to control OS package updates.
* `os_management_autoremove_unused_dependencies`: Boolean to control removal of unused dependencies.

By defining these variables, you are instructing Molecule and the Ansible role about the specific rpm keys, dnf repos, and package configuration to test. The converge.yml playbook will apply the os_management role using these variables, and the subsequent verify.yml playbook will assert that the system is configured as specified.
</details>

<details>
  <summary>Click to expand: Asserting the Desired State</summary>

## `verify.yml` - Asserting the Desired State

The `verify.yml` playbook is executed by Molecule after the `converge.yml` playbook to assert that the system has reached the desired state.

```yaml
---
- name: Verify os_management
  hosts: all
  become: true
  gather_facts: false
  vars_files:
    - vars/main.yml # Ensure vars are loaded for verification

  tasks:
    - name: Check if ELRepo RPM key is installed
      command: rpm -q gpg-pubkey-*
      register: installed_keys
      changed_when: false
    - assert:
        that:
          # The key ID for RPM-GPG-KEY-elrepo.org is 00A02D53.
          # We check for its presence in the output of rpm -q gpg-pubkey-*.
          - "'00a02d53' in installed_keys.stdout"

    - name: Check if EPEL DNF repo is configured
      command: cat /etc/yum.repos.d/epel.repo
      register: epel_config
      changed_when: false
    - assert:
        that:
          - "'[epel]' in epel_config.stdout"
          - "'description=Extra Packages for Enterprise Linux' in epel_config.stdout"
          - "'baseurl=https://mirrors.fedoraproject.org/metalink?repo=epel-9&arch=$basearch' in epel_config.stdout"
          - "'mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=epel-9&arch=$basearch' in epel_config.stdout"
          - "'enabled=1' in epel_config.stdout"
          - "'gpgcheck=1' in epel_config.stdout"

    - name: Check if Remi DNF repo is configured
      command: cat /etc/yum.repos.d/remi.repo
      register: remi_config
      changed_when: false
    - assert:
        that:
          - "'[remi]' in remi_config.stdout"
          - "'description=Remi''s RPM repository for Enterprise Linux 9' in remi_config.stdout"
          - "'baseurl=https://rpms.remirepo.net/enterprise/9/$basearch/' in remi_config.stdout"
          - "'enabled=1' in remi_config.stdout"
          - "'gpgcheck=1' in remi_config.stdout"

    - name: Check if httpd package is installed
      command: rpm -q httpd
      register: httpd_check
      changed_when: false
      failed_when: httpd_check.rc > 1
    - assert:
        that:
          - httpd_check.rc == 0

    - name: Check if gzip package is NOT installed
      command: rpm -q gzip
      register: gzip_check
      changed_when: false
      failed_when: gzip_check.rc == 0
    - assert:
        that:
          - gzip_check.rc == 1
```

* `- name: Verify`: Defines a play with a descriptive name.
* `hosts: all`: Targets all hosts.
* `become: true`: Executes tasks as root.
* `gather_facts: false`:  Skips fact gathering.
* `tasks`: A list of tasks to execute. These tasks use the command module to execute `rpm -q` and `cat` commands, and the assert module to verify the state of packages and repository configurations on the system.
* The `rpm -q` commands check if the specified packages are installed. The `register` keyword saves the output of the command.
* `changed_when: false` prevents Ansible from reporting a change if the command output changes.
* `failed_when: ...` conditions are used to control when the task itself is marked as failed.
* The `assert` module checks the return code or content of the previous commands to verify the expected state. For example, it checks that the return code is `0` for `httpd` (meaning it is installed) and `1` for `gzip` (meaning it is not installed). It also asserts specific lines within the .repo files for epel and remi.

If any of the assertions fail, the `molecule test` run will fail, indicating that the `os_management` role did not configure the system as expected.

</details>

---

[Return to Unit Testing README](../README.md)
