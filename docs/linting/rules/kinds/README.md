# Ansible Lint: `kinds` Configuration Option

The `kinds` option allows you to define additional patterns for identifying different types of Ansible files. This customization extends Ansible Lint's default file type recognition.

## Configuration

You can set the `kinds` option within your Ansible Lint configuration file (e.g., `.ansible-lint`, `.ansible-lint.yml`):

```yaml
kinds:
  # Example for playbook files in an 'examples' directory
  - playbook: "**/examples/*.{yml,yaml}"

  # Specific pattern for a Galaxy metadata file
  - galaxy: "**/folder/galaxy.yml"

  # Identifying task files within a 'tasks' directory
  - tasks: "**/tasks/*.yml"

  # Pattern for variable definition files
  - vars: "**/vars/*.yml"

  # Recognizing Ansible role metadata files
  - meta: "**/meta/main.yml"

  # A custom kind for files with the '.yaml-too' extension
  - yaml: "**/*.yaml-too"
```

## Behavior

* `kinds`: A list of `kind: pattern` mappings. Ansible Lint uses these patterns, in the order they appear, to determine the "kind" of each file it encounters. The first matching pattern dictates the file's kind.

## Explanation

Ansible Lint relies on understanding the type of each file (playbook, tasks, vars, etc.) to apply the most relevant linting rules. While it has built-in logic for common Ansible file structures, the `kinds` option provides a flexible way to extend or refine this identification process.

By defining your own `kind: pattern` pairs, you can cater to project-specific directory layouts or custom file naming conventions. The `pattern` uses glob-style matching to identify file paths.

For instance, if your playbooks are consistently located in an `examples` subdirectory, the `playbook: "**/examples/*.{yml,yaml}"` entry ensures they are correctly recognized as playbooks, regardless of the top-level directory structure (`**`).

Similarly, if you have custom YAML files (like those ending in .yaml-too in the example), you can define a new kind (yaml in this case) to ensure Ansible Lint considers them during its analysis, potentially with a specific set of rules if configured elsewhere.

## Important Considerations

* **Order Matters:** The order of entries in the `kinds` list is significant. Ansible Lint evaluates these patterns sequentially, and the first match determines the file's kind.
* **Extending Default Behavior:** The `kinds` option adds to Ansible Lint's default kind detection; it doesn't entirely replace it unless your patterns are very broad and match standard Ansible file locations.
* **Glob Patterns:** The patterns used are standard glob patterns, allowing for flexible matching of file paths. Refer to glob documentation for more advanced pattern syntax.

## Use Case

Let's explore a practical scenario where the `kinds` option proves particularly useful.

Imagine you're working on a large Ansible project with a slightly unconventional directory structure for your roles. Instead of the standard `roles/your_role/tasks/main.yml`, you've organized your role's main tasks within a subdirectory named `entrypoints`, like this:

```
project_root/
├── playbooks/
│   └── site.yml
└── custom_roles/
    └── webserver/
        └── entrypoints/
            └── main.yml
        └── vars/
            └── main.yml
        └── handlers/
            └── main.yml
```

In this setup, Ansible Lint, with its default assumptions, might not automatically recognize `custom_roles/webserver/entrypoints/main.yml` as the main tasks file for the `webserver` role. This could lead to certain task-specific linting rules not being applied to this critical file.

Here's how you could leverage the `kinds` option to address this:

By adding the following to your `.ansible-lint` configuration file:

```yaml
kinds:
  - tasks: "**/custom_roles/*/entrypoints/main.yml"
```

You are explicitly telling Ansible Lint: "Any file named main.yml that is located within an `entrypoints` directory, which is itself inside a directory within `custom_roles`, should be treated as a set of `tasks`."

### Benefits in this Use Case:

* **Correct Identification:** Ansible Lint will now correctly identify the main tasks file for your `webserver` role (and any other roles following this structure) as a tasks file.
* **Accurate Linting:** Task-specific linting rules will now be applied to these files, ensuring they adhere to your project's standards and best practices.
* **Seamless Integration:** You can maintain your preferred directory structure while still benefiting from the full capabilities of Ansible Lint.
* **Improved Consistency:** By explicitly defining this pattern, you ensure that Ansible Lint consistently recognizes your task files across all roles that follow this custom structure.

Without the `kinds` option, you might have to either restructure your project to fit Ansible Lint's defaults or potentially miss out on valuable linting checks for your main task files. The `kinds` option provides the flexibility to adapt the tool to your project's unique organization.

---

[Return to Main Linting README](../../README.md)
