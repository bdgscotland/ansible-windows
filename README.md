# ansible-windows
General purpose Windows Ansible role and Prep scripts for Windows systems to be controlled by Ansible

## Usage
### Preparing the Windows Hosts
You can automate this in various ways. I will cover this at a later date.
The directory RUN_ON_WINDOWS_PRESETUP needs to be downloaded to the Windows hosts. It will also need network access to download the scripts it needs
- Run presetup.cmd as and Administrator account

### Preparing the Ansible controller
In my use case, I have an issue with SSL certificates causing 500 error. To get around this for a playbook, it is a known issue to add a callback.
I'll think of a better way to present this but for now, in the main directory structure:
```
.
--+ hosts
--+ test_windows_playbook.yml
--+ group_vars
--+ host_vars
--+ callback_plugins
| --+ fix-ssl.py
```
#### test_windows_playbook.yml
```yaml
- hosts: windev
  tasks:
    - name: run ipconfig
      raw: ipconfig
      register: ipconfig
    - debug: var=ipconfig
```
#### hosts
```yaml
[windev]
testhost-win-01
```
#### group_vars/windev.yml
```yaml
ansible_ssh_user: svcAnsible
ansible_ssh_pass: ChangeMe12
ansible_ssh_port: 5986
ansible_connection: winrm
ansible_port: 5987
```

## Limitations
Check the issues page for bugs and known limitations within the scripts

