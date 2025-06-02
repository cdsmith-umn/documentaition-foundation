# Unit Testing Scenarios

This directory contains individual Molecule scenarios designed for unit testing specific aspects of Ansible roles within this project. Each scenario focuses on a particular set of functionalities, ensuring granular and isolated testing.

To learn more about a specific unit test scenario, click on its link below.

## Available Unit Test Scenarios

* [Basic Provisioning Unit Test](basic_provisioning/README.md)
This scenario validates the fundamental ability to create and access a basic Linux environment using Molecule. It confirms that Molecule can provision a container based on the specified image and that the container is running and accessible.

* [Manage User and Group Unit Test](manage_user_and_group/README.md)
This scenario validates the Ansible role's ability to create, modify, and delete user accounts and groups on Linux systems, including managing user membership in groups and handling different states (present/absent).

* [OS Management Unit Test](os_management/README.md)
This scenario validates the operating system management capabilities of the Ansible role. This includes managing RPM keys, configuring DNF repositories, and installing/removing OS packages.

---

[Return to Main README](../README.md)
