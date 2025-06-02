# Testing Basic Provisioning with Molecule

This document explains the automated tests for the basic provisioning of a Linux-based system using Molecule, specifically within the `basic_provisioning` scenario. These tests ensure that a minimal Linux environment can be provisioned and is reachable.

## What This Testing Scenario Validates

The `basic_provisioning` scenario validates the fundamental ability to create and access a basic Linux environment using the configured Molecule settings. It aims to confirm that:

* Molecule can create a container based on the specified image (`rhel9_base_image:latest`).
* The container runs and is accessible (verified by attempting to ping localhost).

By running this scenario, we automatically verify that the basic infrastructure for testing Ansible roles with Molecule functions correctly. This scenario serves as a foundational check before testing more specific role functionalities.

<details>
  <summary>Click to expand: Prerequisites for Running These Tests</summary>

## Prerequisites for Running These Tests

To execute the tests within the `basic_provisioning` scenario, you need the following tools installed and configured on your system:

* **Python:** Molecule relies on a Python environment.
* **pip:** The Python package installer.
* **Ansible:** Molecule orchestrates Ansible playbooks.
* **Molecule:** The Molecule package itself (`pip install molecule`).
* **A Containerization Tool (e.g., Docker, Podman):** This scenario uses Podman to create and manage the Linux test environment. This is configured in the scenario's Molecule settings.

</details>

## `molecule.yml` Configuration

The `molecule.yml` file in the `molecule/basic_provisioning/` directory defines how Molecule will test the basic provisioning in this specific scenario.

<details>
  <summary>Click to expand: Required Scenario Settings</summary>

### `scenario`

```yaml
scenario:
  name: basic_provisioning
```

  * **`name`**: This **required** parameter specifies the name of the Molecule scenario.

### `driver`

```yaml
driver:
  name: podman
```

  * **`name`**: This **required** parameter defines the driver used to create and manage the test environment (Podman).

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

  * **`name`**: This **required** parameter is the name of the platform (`rhel9`).

  * **`image`**: This **required** parameter specifies the container image used (`rhel9_base_image:latest`).

    The other settings under `platforms` (`command`, `privileged`, `ports`, `image_pull_policy`) are optional but commonly configure the test container appropriately.

### `provisioner`

```yaml
provisioner:
  env:
    ANSIBLE_ROLES_PATH: ../../roles/
```

  * **`env`**: This optional parameter often defines environment variables for Ansible, such as `ANSIBLE_ROLES_PATH` (crucial for Ansible to find your roles, though not explicitly used in this minimal convergence).

</details>

<details>
  <summary>Click to expand: Configuring the Test Environment</summary>

## `converge.yml` - Configuring the Test Environment

Molecule executes the `converge.yml` playbook to bring the test environment (the RHEL 9 container) to a basic running state. Here's a breakdown of its contents:

```yaml
---
- name: Converge
  hosts: all
  become: true
  tasks:
    - name: No-op converge
      debug:
        msg: "Basic environment provisioning successful."
```

  * **`- name: Converge`**: This defines a play with a descriptive name.
      * **`hosts: all`**: Specifies that the tasks within this play will run on all hosts defined in the Molecule inventory (the RHEL 9 container).
      * **`become: true`**: Instructs Ansible to execute tasks with elevated privileges (though not strictly necessary for this `debug` task).
      * **`tasks`**: Contains the tasks to be executed.
          * **`- name: No-op converge`**: This task uses the `ansible.builtin.debug` module to output a simple message, indicating that the convergence step has been reached successfully. In a more complex scenario, this is where the Ansible role would be applied.

This `converge.yml` playbook's purpose is primarily to ensure the test environment runs and that Ansible can successfully execute a playbook against it.

</details>

<details>
  <summary>Click to expand: Asserting the Basic State</summary>

## `verify.yml` - Asserting the Basic State

Molecule executes the `verify.yml` playbook after the `converge.yml` playbook to assert that the basic test environment is reachable.

```yaml
---
- name: Verify
  hosts: all
  become: true
  tasks:
    - name: Ping the instance (optional, might require `iputils-ping` installed in your base image)
      command: ping -c 1 localhost
      register: ping_result
      ignore_errors: true

    - assert:
        that: ping_result.rc == 0 or ping_result.failed
        msg: "Basic instance is reachable."
```

This `verify.yml` playbook uses Ansible modules to check the basic state of the test environment:

  * **`Ping the instance`**: This task attempts to ping the localhost within the container. The `ignore_errors: true` ensures the playbook doesn't fail if `ping` is not available. The task registers the result in `ping_result`.
  * **`assert`**: This task checks if the ping was successful (`rc == 0`) or if it failed (meaning the instance is still considered "reachable" in this basic context, as the goal is just to have a running instance). The `msg` provides feedback on the assertion.

If the assertion fails, the `molecule test` run will indicate a failure, suggesting that the basic test environment could not be established.

</details>

---

[Return to Unit Testing README](../README.md)
