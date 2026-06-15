### Executive Summary — roboshop-ansible

The `roboshop-ansible` repository is intended to serve as a centralized platform for learning Ansible basics and implementing Configuration Management for all Roboshop components within the `roboshop/` directory.

1. **Modern Configuration Management with Ansible**
   Ansible enables infrastructure and application automation in a scalable, declarative, and platform-independent manner, reducing the operational complexity associated with traditional Bash scripting.

2. **Supports Both Push and Pull Automation Models**
   Ansible is one of the few Configuration Management tools that supports both:

   * **Push Mechanism** — where a centralized Ansible control node connects to target servers over SSH (port 22) and pushes configurations remotely.
   * **Pull Mechanism** — where managed nodes independently pull configurations from a Git repository and apply them locally.

3. **Push Mechanism Characteristics**
   Push-based automation requires:

   * Ansible installed on the central control node.
   * Network connectivity between the control node and managed servers.
   * Proper SSH authentication and credentials management, commonly through a dedicated `ansible-user`.
   * Stable or known server IPs and network accessibility.

4. **Pull Mechanism Characteristics**
   Pull-based automation requires:

   * Ansible installed directly on the managed node.
   * Connectivity from the node to the Git repository containing automation code.
   * Distributed execution capability without depending on centralized inbound connectivity.

5. **Declarative Automation Using YAML Playbooks**
   Unlike Bash, where automation logic is written as imperative scripts, Ansible uses **Playbooks** written in YAML (Yet Another Markup Language). YAML-based automation is declarative, human-readable, easier to maintain, and better suited for large-scale infrastructure management.


# Executive Summary — Introduction to Ansible

## What is Ansible?

Ansible is an open-source automation and configuration management tool used for:

* Server provisioning
* Application deployment
* Configuration management
* Infrastructure automation

It is agentless, meaning no software needs to be installed on target servers. It primarily uses SSH for communication.

---

# Installing Ansible

## Core Concept

Ansible is a Python-based application and is commonly installed using Python package managers like `pip`.

### Default Installation via DNF

```bash
dnf install ansible -y
```

* This usually installs **ansible-core** from the OS repository.
* On RHEL/Rocky/AlmaLinux 9, this often provides:

  * `ansible-core 2.14`
  * Equivalent to Red Hat Ansible Automation Platform version 7-era tooling.

---

## Installing Latest Ansible via PyPI

[PyPI Ansible Package](https://pypi.org/project/ansible/?utm_source=chatgpt.com)

### Why PyPI?

PyPI provides the latest upstream Ansible versions faster than OS repositories.

### Python Requirement

Latest Ansible releases require newer Python versions (Python 3.12+ recommended).

### Suggested Installation Steps

```bash
dnf install python3.12 -y

pip3.12 install ansible
```

### Important Correction

The original note mentions:

```bash
dnf remove python -y
```

This is risky and not recommended because:

* System Python is required by the operating system.
* Removing it can break package management and core OS tools.

Instead:

* Install Python 3.12 alongside the existing version.
* Use `pip3.12`.

---

# Inventory in Ansible

Ansible manages servers using an **Inventory** file.

The inventory contains:

* IP addresses
* Hostnames
* Groups of servers

Example:

```ini
[dev]
172.31.22.44

[prod]
172.31.22.26
```

---

# How Ansible Works

Ansible operates mainly using:

1. **Ad-hoc Commands**
2. **Playbooks**

---

# 1) Ad-hoc Commands

Ad-hoc commands are:

* One-line commands
* Used for quick operations
* Best for testing or small tasks

## Example: Ping Servers

```bash
ansible -i inventory all \
-e ansible_user=ec2-user \
-e ansible_password=DevOps321 \
-m ansible.builtin.ping
```

### Key Components

| Component      | Meaning                |
| -------------- | ---------------------- |
| `-i inventory` | Inventory file         |
| `all`          | All hosts in inventory |
| `-e`           | Extra variables        |
| `-m`           | Module name            |

---

## Using Groups

```bash
ansible -i inventory prod \
-e ansible_user=ec2-user \
-e ansible_password=DevOps321 \
-m ansible.builtin.ping
```

Here:

* `prod` refers to a server group in inventory.

---

## Installing Packages via Ad-hoc Command

```bash
ansible -i inventory dev \
-e ansible_user=ec2-user \
-e ansible_password=DevOps321 \
-m ansible.builtin.package \
-a "name=nginx state=present" \
--become
```

### What this does

* Targets all servers in the `dev` group
* Installs `nginx`
* `--become` executes commands with elevated privileges (sudo/root)

---

# 2) Playbooks

Playbooks are YAML-based automation files that allow:

* Multiple tasks
* Sequential execution
* Reusability
* Infrastructure as Code (IaC)

Example capabilities:

* Install packages
* Configure services
* Deploy applications
* Restart services

---

# Key Takeaways

* Ansible is an agentless automation tool built on Python.
* Latest versions are best installed via PyPI using modern Python versions.
* Inventory files define managed servers and groups.
* Ad-hoc commands are suitable for quick operations.
* Playbooks provide scalable and reusable automation workflows.
* Modules are the building blocks of Ansible automation.


# Executive Summary — YAML & Ansible Playbooks

## Introduction to YAML

YAML (Yet Another Markup Language) is a human-readable data serialization language widely used in Ansible playbooks and configuration management.

YAML files typically use:

* `.yml`
* `.yaml`

extensions.

Unlike Bash scripting, YAML is **indentation-sensitive**, meaning spacing must remain consistent throughout the file.

---

# Core YAML Concepts

## 1) Dictionary

A single key associated with a single value.

```yaml id="3a5xwr"
name: Martin D'vloper
job: Developer
```

---

## 2) List

A key associated with multiple values.

```yaml id="mxyjhn"
skills:
  - lisp
  - fortran
  - erlang
```

---

## 3) Map

A structure containing multiple key-value pairs grouped together.

```yaml id="p6j3u7"
employee:
  name: Martin
  role: Developer
  experience: 5
```

---

# Understanding Ansible Playbooks

## What is a Playbook?

A playbook is a YAML file containing a **list of plays**.

---

## What is a Play?

A play is a collection of **tasks** executed against a set of servers.

---

## What is a Task?

A task represents a single operation that Ansible executes, such as:

* Installing packages
* Starting services
* Creating files
* Deploying applications

---

# Running an Ansible Playbook

Example command:

```bash id="9mx0oz"
ansible-playbook -i inventory \
-e ansible_user=ec2-user \
-e ansible_password=DevOps321 \
01-playbook.yaml
```

### Key Parameters

| Parameter          | Purpose                  |
| ------------------ | ------------------------ |
| `-i inventory`     | Specifies inventory file |
| `-e`               | Passes extra variables   |
| `ansible-playbook` | Executes playbook files  |

---

# Variable Types in Ansible

Ansible supports multiple variable scopes.

## 1) Play-Level Variables

Variables defined at the play scope.

## 2) Task-Level Variables

Variables defined specifically for a task.

## 3) Command-Line Variables (CLI Variables)

Variables passed during runtime using `-e`.

Example:

```bash id="m3x7ld"
ansible-playbook -i inventory \
-e ansible_user=ec2-user \
-e ansible_password=DevOps321 \
03-cliVar.yml \
-e URL=url.google.com
```

---

# Variable Precedence

Ansible resolves variables using the following priority order:

```text id="w2dn11"
CLI Variables > Task Variables > Play Variables
```

Command-line variables override all lower-level variables.

---

# Understanding Ansible Facts

## What are Facts?

Facts are system properties automatically gathered by Ansible before playbook execution.

Examples include:

* IP address
* Hostname
* OS version
* CPU details
* Memory information

When a playbook targets multiple servers, Ansible gathers facts from each server individually.

---

# Gathering Facts

## Viewing Gathered Facts

```bash id="p8j2qw"
ansible -i inventory prod \
-e ansible_user=ec2-user \
-e ansible_password=DevOps321 \
-m ansible.builtin.gather_facts
```

---

## Controlling Fact Gathering

By default:

* Ansible gathers facts automatically.

This behavior can be disabled:

```yaml id="2xjlwm"
gather_facts: no
```

### Why Disable Fact Gathering?

* Faster playbook execution
* Reduced overhead
* Useful when system information is unnecessary

### Important Note

If fact gathering is disabled:

* Accessing facts inside the playbook will result in failures because the facts were never collected.

---

# Key Takeaways

* YAML is the foundational language for Ansible playbooks.
* Proper indentation is critical in YAML.
* Ansible playbooks are structured as:

  * Playbook → Plays → Tasks
* Variables can be defined at multiple levels with defined precedence.
* Facts provide detailed server metadata automatically.
* Fact gathering can be enabled or disabled depending on automation requirements.


````markdown
# Ignoring Task Failures in Ansible

In `ansible-playbook`, if a particular task fails, the subsequent tasks will **not** be executed by default.

If you want to ignore the failure of a specific task, or if you do not care whether the task succeeds or fails, you can use the `ignore_errors` option for that task.

Example:

```yaml
- name: Remove a file if it exists
  ansible.builtin.file:
    path: /tmp/sample.txt
    state: absent
  ignore_errors: yes
````

---

# What is Ansible Vault?

`ansible-vault` helps encrypt sensitive information such as passwords, API keys, and secrets.

It converts plain-text secrets into an encrypted format, which can only be decrypted using a vault password.

Example use cases:

* Database passwords
* SSH credentials
* API tokens
* Cloud secrets

One important limitation of `ansible-vault` is that the encrypted data is primarily understandable only by Ansible itself. If multiple tools or platforms need access to the same secrets, this approach becomes less flexible.

Because of this, most organizations prefer a **centralized secret management solution** instead of a decentralized approach.

Popular centralized secret management tools include:

* AWS Secrets Manager
* HashiCorp Vault
* Azure Key Vault
* Google Secret Manager

These tools allow multiple applications, platforms, and automation tools to securely access secrets from a common location.

---

# Encrypting Secrets Using Ansible Vault

## Encrypt a Specific String

```bash
ansible-vault encrypt_string 'robo@1'
```

You will be prompted to enter a vault password.

Example output:

```yaml
!vault |
          $ANSIBLE_VAULT;1.1;AES256
          6438616537396239343964366637313962...
```

You can directly place this encrypted value inside your playbooks or variable files.

---

# Running a Playbook with Vault Password

```bash
ansible-playbook -i inventory \
  -e ansible_user=ec2-user \
  -e ansible_password=DevOps321 \
  10-secrets.yaml \
  --ask-vault-pass
```

While executing the playbook, Ansible will prompt for the vault password in order to decrypt the encrypted secrets.

---

# Important Note About Ansible Vault

With `ansible-vault`, tracking and auditing secret access becomes difficult compared to enterprise-grade centralized secret management systems.

Modern secret management solutions provide:

* Access control
* Audit logging (AAA: Authentication, Authorization, Accounting)
* Secret rotation
* Fine-grained permissions
* Centralized governance

This is one of the reasons why teams often prefer tools like:

* HashiCorp Vault
* AWS Secrets Manager
* Azure Key Vault
* Google Secret Manager

```
```


## Ansible Roles

Writing playbooks on their own can be difficult to manage, and it is often unclear which files are used by which playbook.
You may not know which variable file is used by a given playbook.

The best hardened approach to handle playbooks is using Ansible Roles.

Ansible roles provide a standard way of organizing playbooks, associated variable files, templates, and other files.
With roles, you can write once and reuse multiple times, which helps keep the code DRY.

Here is the documented way of using Ansible Roles. Names are strictly enforced, and `tasks/` should contain `main.yml` to execute by default.

How to run a specific role?

```bash
ansible-playbook -i inv-dev -e ansible_user=ec2-user -e ansible_password=DevOps321 roboshop.yml
```

The roles to run are described in the playbook under the `roles:` section.

Example role structure:

```text
roles/
    common/               # this hierarchy represents a "role"
        tasks/            #
            main.yml      #  <-- tasks file can include smaller files if warranted
        handlers/         #
            main.yml      #  <-- handlers file
        templates/        #  <-- files for use with the template resource
            ntp.conf.j2   #  <------- templates end in .j2
        files/            #
            bar.txt       #  <-- files for use with the copy resource
            foo.sh        #  <-- script files for use with the script resource
        vars/             #
            main.yml      #  <-- variables associated with this role
        defaults/         #
            main.yml      #  <-- default lower-priority variables for this role
        meta/             #
            main.yml      #  <-- role dependencies
        library/          # roles can also include custom modules
        module_utils/     # roles can also include custom module_utils
        lookup_plugins/   # or other types of plugins, like lookup in this case
```

> We completed the project in a hardwired way and it did not meet the NFRs:
> 1) Code was repeated, so the code was WET.
>    I want to identify common patterns and make the code DRY by writing a role with shared patterns and calling it.
> 2) Re-run should work, and it does.
> 3) Infrastructure was created manually and I want that automated (we do that with Terraform).
> 4) We are running the Ansible playbook from the workstation; we want it to run from a UI (CI framework).

> When calling a role, only the tasks in `main.yml` are executed.
> Can we execute the tasks in `tasks/nodejs.yml` from a role `common`? Yes.
> That is called including the role, which can be done as follows:

```yaml
- name: Run tasks/other.yml instead of main
  ansible.builtin.include_role:
    name: myrole
    tasks_from: other
```

Dry status:
- NodeJS components are 100% dry.
- Java components are 100% dry.
- Python components are 100% dry.

> Next concepts are :
  1) ansible-pull:
       We can login to the server that needs ansible configuriaton management, we can direccly point the command to the playbook on git and run the playbook directly.

       ansible-pull expects ansible to be installed on the servers you're running the command.

       you don't need to maintain separate server to act as a ansible-node.

  2) Role Dependency:
        This ensures whenever we run a role-x and if it needs role-y to be executed first then it ensure that role-y to be run first.

        Let's say for shipping component, mysql is dependent and if you run shipping without mysql , shipping wll fail.

        We can create dependency  in the shipping statng mySql as dependeent role, whenever you run shipping mysql wll be executed first and that dependency s configured in the meta/main.yml


How to run ansible-pull ?
  To run ansible-pull., here are the instructions:
    1) Log in to the server that needs Ansible Configuration Management
    2) Ensure ansible is installed on the servre  ( If not install it )
    3) Then run the ansible-pull using the below command, no need of auth or anything ( At this point of time, the only endPoint supported by ansible pull is from GIT )

```
 $ ansible-pull -U https://github.com/B60-CloudDevOps/roboshop-ansible.git roboshop/roboshop-pull.yml -e env=dev -e component=mongodb
```

What are the areas of improvements still ?
  1) Infrastructure creation is still manual.
  2) I want less toil ( Toil: Manual Actions )

More Toil = More Manual Actions = More Mistakes = More Outages = Less reputation & revenus loss.
