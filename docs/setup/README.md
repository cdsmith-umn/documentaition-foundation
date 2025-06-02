# Overview of Molecule and Test Execution

This document provides foundational knowledge for setting up Molecule and executing tests. It guides you through Molecule setup on your workstation and demonstrates how to use it for automated Ansible role testing.

<details>
  <summary>Click to expand: What is Molecule?</summary>

## What is Molecule?

Molecule is an open-source testing framework that aids in developing and testing Ansible roles. It provides a structured, repeatable environment to test your roles across multiple operating systems and Ansible versions. Key benefits of using Molecule include:

* **Automated Testing:** Automates creating test environments, running Ansible playbooks against them, and verifying the desired state.
* **Idempotency Testing:** Facilitates testing whether your role runs multiple times without unintended changes.
* **Multiple Providers:** Supports various providers (e.g., Docker, Podman, Vagrant, cloud platforms) to create test instances.
* **Scenario-Based Testing:** Allows you to define different testing scenarios to cover various aspects of your role's functionality.
* **Integration with Linters:** Integrates with Ansible linters (like `ansible-lint`) to ensure code quality.

Using Molecule significantly improves the quality and reliability of your Ansible roles.

</details>

<details>
  <summary>Click to expand: Setting Up and Using Molecule</summary>

## Setting Up and Using Molecule

This section covers Molecule's prerequisites, installation steps, and usage for testing Ansible roles.

### Prerequisites

Before you set up Molecule, ensure you have the following installed and configured:

* **Python:** Molecule requires a Python environment (Python 3.6 or higher is recommended).
* **pip:** The Python package installer, used to install Molecule and its dependencies.
* **Ansible:** Molecule orchestrates Ansible playbooks, so Ansible must be installed.

### Installation Steps

1.  **Install Ansible:**
    If you don't have Ansible, install it using pip:

    ```bash
    pip install ansible
    ```

    Refer to the official Ansible documentation for more detailed installation instructions for your operating system.

2.  **Install Molecule:**
    Install Molecule using pip:

    ```bash
    pip install molecule
    ```

    We recommend installing Molecule within a **Python virtual environment** to avoid conflicts with other Python packages, especially on non-RHEL/CentOS systems.

3.  **Install a Driver (Provider):**
    Molecule drivers manage the test environment. For container-based testing (e.g., the `manage_user_and_group_unit` scenario), you need a containerization tool like Docker or Podman.

    * **For Docker:** Ensure Docker is installed and running on your workstation. Refer to the official Docker documentation for installation instructions.
    * **For Podman (Recommended for non-root containers):** Ensure Podman is installed. Installation instructions vary depending on your operating system. For many Linux distributions:

        ```bash
        sudo dnf install podman  # For Fedora, CentOS Stream, RHEL
        sudo apt install podman  # For Debian, Ubuntu
        ```

### Considerations for Non-RHEL/CentOS Systems (Using `~/venv`)

If you work on a system other than RHEL or CentOS, we highly recommend using a Python virtual environment to manage your Python packages, including Ansible and Molecule. This isolates your testing environment from your system's base Python installation.

1.  **Create a Virtual Environment (if you haven't already):**

    ```bash
    python3 -m venv ~/venv
    ```

2.  **Activate the Virtual Environment:**

    ```bash
    source ~/venv/bin/activate    # On Linux/macOS
    ~/venv/Scripts/activate      # On Windows
    ```

    Once activated, `(venv)` appears at the beginning of your terminal prompt.

3.  **Install Ansible and Molecule within the Virtual Environment:**

    ```bash
    pip install ansible molecule
    ```

4.  **Install a Driver (e.g., Podman or Docker) as mentioned above.**

    Using a virtual environment ensures `~/venv` contains the required Python packages for Molecule and prevents interference with your system's packages. Remember to activate this environment whenever you work with Molecule.

### Using Molecule to Run Tests

Once you set up Molecule, execute tests for your Ansible roles. Following best practices, place the `molecule` directory, which contains your test scenarios, at your Ansible project repository's root. Here's how to use Molecule in this setup:

1.  **Navigate to Your Project Root Directory:**
    Open your terminal and go to your Ansible project repository's root directory (the directory that contains the `roles` directory and the top-level `molecule` directory). For example:

    ```bash
    cd ~/my_ansible_project/
    ```

2.  **Execute Molecule:**
    Run Molecule commands from the project root directory.

    To run all tests, use the command `molecule test --all`.

    To target a specific test scenario for a particular role, use the `-s` (or `--scenario`) flag followed by the name of that scenario.

    Note: Running `molecule test` without the ` --all` flag or a specified scenario (`-s`) results in an error because Molecule does not define a default scenario. **Always use either the ` --all` flag or target a specific scenario using the `-s` flag!**

    For example, if your project has this structure:

    ```
    my_ansible_project/
    ├── roles/
    │   └── manage_user_and_group/
    │       ├── defaults/
    │       ├── handlers/
    │       ├── meta/
    │       ├── tasks/
    │       ├── templates/
    │       └── vars/
    └── molecule/
        └── manage_user_and_group_unit/
            ├── converge.yml
            ├── molecule.yml
            └── verify.yml
    ```

    Then, use `manage_user_and_group_unit` as the scenario path with the `-s` flag.

    Here are some common Molecule commands with examples for this specific scenario:

    * `molecule create -s manage_user_and_group_unit`: This command creates the test environment defined in the `molecule.yml` file within the `manage_user_and_group_unit` scenario of the `manage_user_and_group` role.

    * `molecule converge -s manage_user_and_group_unit`: This command runs the `converge.yml` playbook found in the specified scenario against the created test environment.

    * `molecule verify -s manage_user_and_group_unit`: This command executes the `verify.yml` playbook from the specified scenario to check if the environment is in the desired state.

    * `molecule test -s manage_user_and_group_unit`: This comprehensive command runs the entire test lifecycle (create, converge, idempotency check, verify, destroy) for the specified scenario.

    * `molecule destroy -s manage_user_and_group_unit`: This command tears down the test environment created for the specified scenario.

    * `molecule list`: This command lists all available Molecule scenarios in your project. Note that it doesn't require the `-s` flag as it operates at the project level.

    * `molecule login -s manage_user_and_group_unit`: This command provides you with a shell session inside one of the test instances managed by the specified scenario, useful for debugging.

3.  **Interpreting the Results:**
    Molecule outputs the results of each test stage. A successful test run typically ends with a "PASSED" status for the targeted scenario(s). If assertions in your `verify.yml` fail, the test fails; examine the output to identify the issue.

### Example Workflow (Project Root)

While `molecule test -s manage_user_and_group_unit` provides a convenient way to run the entire test lifecycle, understanding the individual steps helps with debugging and iterative development. Here's a breakdown of the individual commands and their typical use cases:

1.  **Create the test environment:**

    ```bash
    molecule create -s manage_user_and_group_unit
    ```

    Typically, you run this command to provision the test infrastructure (e.g., start a container). Run it individually if the `test` command fails during creation or if you want to keep the environment running for multiple test runs.

2.  **Configure the test environment using the role:**

    ```bash
    molecule converge -s manage_user_and_group_unit
    ```

    This command executes your role's Ansible playbook against the created environment. Developers often run this command repeatedly during development to quickly apply changes to the running test instance.

3.  **Verify the state of the test environment:**

    ```bash
    molecule verify -s manage_user_and_group_unit
    ```

    This command runs your `verify.yml` playbook to assert that the environment is in the desired state after convergence. Run this after `converge` to check if your role has configured the system correctly. If `molecule test` fails at the verify stage, you might run `converge`, then `login`, and then `verify` to debug.

4.  **Run the entire test sequence:**

    ```bash
    molecule test -s manage_user_and_group_unit
    ```

    This command executes the entire lifecycle: `create`, `converge`, `idempotency` check (running `converge` again), `verify`, and `destroy`. This is the most common command for a complete test run; it ensures your role works as expected and is idempotent.

5.  **Clean up the test environment:**

    ```bash
    molecule destroy -s manage_user_and_group_unit
    ```

    This command tears down the test infrastructure. Run this to clean up resources, especially in shared environments or when using resource-intensive drivers. `molecule test` automatically runs this at the end of a successful test run.

Understanding these individual steps gives developers more control over the testing process and helps them effectively debug and develop their Ansible roles using Molecule.

</details>

<details>
  <summary>Click to expand: Code Quality and Linting Overview</summary>

## Code Quality and Linting Overview

Maintaining high code quality is crucial for robust, maintainable Ansible content. This project uses **pre-commit hooks** and **Ansible Lint** to enforce coding standards and best practices.

* **Pre-commit Hooks:** These scripts run automatically before you commit code to Git, ensuring checks (like linting) occur early in the development cycle. For detailed installation and usage instructions, refer to the [Pre-commit Hooks documentation](pre-commit/README.md).
* **Ansible Lint:** This command-line tool checks Ansible playbooks and roles for potential errors, style violations, and best practice adherence. It integrates with Molecule to ensure your Ansible code maintains high quality and consistency. For installation and integration details, see the [Ansible Lint documentation](ansible-lint/README.md).

</details>

---

[Return to Main README](../README.md)
