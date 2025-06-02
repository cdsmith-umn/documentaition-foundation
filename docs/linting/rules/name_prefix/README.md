## Ansible Lint: `name[prefix]` Example

This example demonstrates how the `name[prefix]` rule affects Ansible code by enforcing a consistent naming convention for tasks and handlers.

### Configuration in `.ansible-lint.yml`

First, you configure Ansible Lint in your `.ansible-lint.yml` file:

```yaml
enable_list:
  - name[prefix]
name_prefix: "deploy_webapp_"
```

This configuration tells Ansible Lint that all task and handler names should start with "deploy_webapp_".

### Example Ansible Playbook

Here's an example of an Ansible playbook before applying the rule consistently:

```
- name: Set up web app
  hosts: webservers
  tasks:
    - name: Install dependencies  # Violates the rule
      apt:
        name:
          - apache2
          - libapache2-mod-wsgi
        state: present
    - name: Configure Apache    # Violates the rule
      template:
        src: templates/vhost.conf.j2
        dest: /etc/apache2/sites-available/myapp.conf
      notify: Restart Apache
    - name: Enable site      # Violates the rule
      command: a2ensite myapp.conf
    - name: Restart Apache  # This is a handler, and it violates the rule
      service:
        name: apache2
        state: restarted
      listen: Restart Apache
```

### Ansible Lint Output

When you run ansible-lint on this playbook, it will report violations for all the name fields:

```
[WARNING]: Task name does not begin with required prefix 'deploy_webapp_': Install dependencies
...
[WARNING]: Task name does not begin with required prefix 'deploy_webapp_': Configure Apache
...
[WARNING]: Task name does not begin with required prefix 'deploy_webapp_': Enable site
...
[WARNING]: Handler name does not begin with required prefix 'deploy_webapp_': Restart Apache
```

### Corrected Ansible Playbook

Here's the corrected Ansible playbook, adhering to the name[prefix] rule:
```
- name: Set up web app
  hosts: webservers
  tasks:
    - name: deploy_webapp_install_dependencies
      apt:
        name:
          - apache2
          - libapache2-mod-wsgi
        state: present
    - name: deploy_webapp_configure_apache
      template:
        src: templates/vhost.conf.
```

---

[Return to Main Linting README](../../README.md)
