# Ansible Lint: Installation and Integration

This document guides you through installing Ansible Lint and explains its integration with your Ansible Molecule testing workflow to ensure code quality.

### Installing Ansible Lint

Before you begin, ensure you have Ansible, Molecule, Python, and pip installed, and that you are operating within a virtual environment.

1.  **Activate your virtual environment:**

    * Activate your virtual environment if you haven't already. The specific command depends on how you created it (e.g., using `venv` or `virtualenv`). A common method is:

        ```bash
        source <your_virtual_environment_name>/bin/activate
        ```

        Replace `<your_virtual_environment_name>` with the actual name or path of your virtual environment. In this case, it's likely `venv`.

2.  **Install Ansible Lint using pip:**

    * Use pip to install Ansible Lint. We generally recommend installing `yamllint` alongside Ansible Lint, as Ansible Lint uses it to validate YAML file syntax.

        ```bash
        pip install ansible-lint yamllint
        ```

        To install the exact versions of all dependencies used to test a specific Ansible Lint version, optionally add `[lock]`. This works with Python 3.10 or newer.

        ```bash
        pip install "ansible-lint[lock]"
        ```

3.  **Verify the installation (Optional):**

    * Verify the installation by checking Ansible Lint's version:

        ```bash
        ansible-lint --version
        ```

    Ansible Lint is now installed in your virtual environment!

---

### Integration with Molecule

Molecule integrates with Ansible Lint, a command-line tool that checks Ansible playbooks and roles for potential errors, style violations, and best practice adherence. Integrating Ansible Lint into your Molecule workflow ensures high quality and consistent Ansible code.

By default, Molecule does not automatically run Ansible Lint. However, our Molecule global scope configuration already enables Ansible Lint.

#### Benefits of Using Ansible Lint with Molecule

* **Early Error Detection:** Catch errors and potential issues in your Ansible code before they impact your tests or production environments.
* **Code Consistency:** Enforce a consistent style across your Ansible projects, making them easier to read and maintain.
* **Best Practice Enforcement:** Ensure your Ansible code follows recommended best practices, leading to more robust and reliable automation.

#### Basic Configuration

Configure Ansible Lint using files like `~/.ansible-lint.yml`. These files allow you to customize rule sets, exclude paths, and tailor linting to your project's needs. Common configuration options include:

* **Rule Customization:** Enable, disable, or modify specific linting rules.
* **Excluding Paths:** Ignore certain directories or files from linting.

For summaries of each of Ansible Lint's configuration options, refer to our [Ansible Lint Configuration Overview](../../linting/README.md) and the individual summaries for each option that are linked from there.

For a comprehensive overview of Ansible Lint's configuration options, refer to the official [Ansible Lint Configuration](https://ansible.readthedocs.io/projects/lint/configuring/) documentation.

---

[Return to Setup README](../README.md)
