# Installing Pre-commit Hooks

Pre-commit hooks are scripts that automatically run before you commit code in Git. They enforce code quality and standards by running checks (like linting) and prevent commits if any checks fail. This project uses `pre-commit` to manage them.

### Prerequisites

* **Python:** Pre-commit is a Python application.
* **pip:** The Python package installer.

### Installation

1.  **Install pre-commit:**

    ```bash
    pip install pre-commit
    ```

    We recommend installing `pre-commit` in a virtual environment. If you use a virtual environment for your Ansible development (as described in the "Setting Up Molecule" section in the main overview document), you can install it there. Otherwise, create a separate virtual environment specifically for your Git hooks.

2.  **Create a `.pre-commit-config.yaml` file:**
    Create a `.pre-commit-config.yaml` file in your Git repository's root directory if it doesn't already exist. This file configures the hooks `pre-commit` should run. Below is a sample `.pre-commit-config.yaml` for this project.

3.  **Configure `.pre-commit-config.yaml`:**
    Add the following configuration to your `.pre-commit-config.yaml` file:

    ```yaml
    repos:
    -   repo: [https://github.com/pre-commit/pre-commit-hooks](https://github.com/pre-commit/pre-commit-hooks)
        rev: v4.9.0
        hooks:
        -   id: trailing-whitespace
        -   id: end-of-file-fixer
        -   id: check-yaml
            exclude: ^docs/  # Exclude the docs directory.
        -   id: check-added-large-files
    -   repo: [https://github.com/ansible/ansible-lint](https://github.com/ansible/ansible-lint)
        rev: v6.24.1
        hooks:
        -   id: ansible-lint
            exclude: ^docs/ #Exclude the docs directory.
    ```

    This configuration directs `pre-commit` to use two hook repositories: one for general checks and one for Ansible Lint. It then configures specific hooks from these repositories.

4.  **Install the pre-commit hooks:**

    Run the following command in the root of your Git repository:

    ```bash
    pre-commit install
    ```

    This command sets up the `pre-commit` hooks in your local Git repository.

### Usage

Once you install `pre-commit` and set up the hooks, they run automatically whenever you attempt to commit changes using `git commit`. If any hook finds an issue (e.g., trailing whitespace, YAML syntax error, Ansible Lint error), it blocks the commit and displays an error message. Fix the issues, then try committing again.

To bypass the `pre-commit` hooks (not recommended, except in exceptional circumstances), use the `--no-verify` option with `git commit`:

    ```bash
    git commit --no-verify
    ```

---

[Return to Setup README](../README.md)
