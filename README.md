# A comprehensive Windows Privilege-Escalation Benchmark

This is a simple benchmark for windows privilege escalation attacks, i.e., scenarios where the attacker is a low-privilege user and tries to become a user with administrative privileges.

This benchmark ful-fills the following requirements

- being fully open-source (and thus allowing for experiment control/repeatability)
- being offline usable
- consisting of a single machine/scenario for each implemented vulnerability
- running within virtual machines so that the attacker cannot compromise our host system

## Setup Instructions

This depends upon the following packages being installed

- `ansible`
- `ansible windows`, install through `ansible-galaxy collection install ansible.windows`
- `ansible windows community`, install through `ansible-galaxy collection install community.windows`
- basic compiler tools (`gcc`, `make`, `gawk`)
- `libvirt`, `libvirt-daemon-system` and `libvirt-dev`
- `vagrant`
- the vagrant libvirt plugin (`vagrant plugin install vagrant-libvirt` after vagrant was installed)

Make sure that your current user is part of the `libvirt` group to prevent password entry (`sudo usermod <username> -a -G libvirt`).

Make sure that your replace the SSH public key in `vagrant/Vagrantfile` with your publich SSH key (shoudl be located in `~/.ssh/id_rsa.pub`).

With that you should be able to call `./create_and_start_vms.sh` (this can be resource intensive)

You can start a single testcase with `vagrant up test-i` and then `ansible-playbook -i hosts.ini tasks.yaml --limit <ansible_task>`

## Supported Windows Priv-Escalation Vulnerabilities

| ansible task | vulnerability |
| --- | --- |
| `admin_weak_password` | the `Administrator` has a weak password |
| `admin_password_reuse` | user `lowpriv` has the same password as the `Administrator` |
| `vuln_admin_password_powershell_historysudo_gtfo` | `Administrator`'s password is in the `powershell` history |
| `admin_password_unattended_file` | `Administrator`'s password is in an unattended installation answer file |
| `scheduled_task` | `lowpriv` can change the executable of a scheduled task |
| `misconfigured_service` | `lowpriv` can perform an unqouted service path attack |
| `kernel_exploit` | the system is vulnerable to the `EternalBlue` kernel exploit |
| `misconfigured_service_2` | `lowpriv` can change the executable of a windows service |

## How to contribute additional testcases?

We are more than happy to add new test-cases, to do this please

- look at `tasks.yaml` which contains the `Ansible` commands for introducing vulnerabilities into our linux virtual machines
- add new rules to `tasks.yaml`
- create pull request (: thank you!