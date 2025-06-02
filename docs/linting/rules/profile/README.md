# Ansible Lint: Profile Configuration Option

Ansible Lint provides a `profile` configuration option to help you enforce different levels of rule strictness in your Ansible code. This allows you to tailor the linting process to your specific needs and development stage.

## Profile Options

The following profiles are available, ordered from least to most stringent:

* `min`: Ensures that your Ansible code can be loaded by Ansible. This profile includes the most essential rules to prevent fatal errors.
* `basic`: Prevents common coding issues and enforces standard styles and formatting.
* `moderate`: Enforces best practices to improve the readability and maintainability of your Ansible code.
* `safety`: Helps you avoid potentially risky module calls that could lead to non-deterministic outcomes or security vulnerabilities.
* `shared`: Enforces best practices for packaging and publishing Ansible content (roles, collections) on Ansible Galaxy or Automation Hub.
* `production`: Enforces the strictest rules, ensuring your Ansible code meets the requirements for inclusion in Red Hat Ansible Automation Platform (AAP) as validated or certified content. This profile is recommended for content intended for wide distribution and enterprise use.

## Profile Hierarchy

Each profile in this list includes all the rules from the profiles above it. For example:

* The `basic` profile includes all the rules from the `min` profile.
* The `moderate` profile includes all the rules from the `basic` profile, and so on.

## Using Profiles

You can specify a profile in your Ansible Lint configuration file (`.ansible-lint` or `.ansible-lint.yml`) using the `profile` option:

```yaml
profile: production
```

Alternatively, you may specify a profile from the command line using the `--profile` option:

```bash
ansible-lint --profile production
```

## Special Profile: `default`

The `default` profile is a special case, and its exact composition is unclear. It appears to be a combination of the `min` and `basic` profiles, but it may not be strictly equivalent to either. While `basic` is a superset of `min`, `default` may include a specific selection of rules from both.

## Choosing a Profile

The choice of which profile to use depends on your specific needs and the context of your Ansible project:

* For basic syntax checking and to ensure your playbooks can be parsed, use `min`.
* For general code quality, style consistency, and to avoid common errors, use `basic` or `moderate`.
* For code that will be shared or distributed, use `shared` or `production`.
* For production environments and to enforce the highest standards, use `production`.

---

[Return to Main Linting README](../../README.md)
