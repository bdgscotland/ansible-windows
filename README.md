# ansible-windows
General purpose Windows Ansible role and Prep scripts for Windows systems to be controlled by Ansible

## Usage
### Preparing the Windows Hosts
You can automate this in various ways. I will cover this at a later date.
The directory RUN_ON_WINDOWS_PRESETUP needs to be downloaded to the Windows hosts. It will also need network access to download the scripts it needs
- Run presetup.cmd as an Administrator account

### Preparing the Ansible controller
In my use case, I have an issue with SSL certificates causing 500 error. To get around this for a playbook, it is a known issue to add a callback.
I'll think of a better way to present this but for now, in the main directory structure:

#### Directory structure of Ansible directory
```
--+ hosts
--+ test_windows_playbook.yml
--+ group_vars
--+ host_vars
--+ callback_plugins
| --+ fix-ssl.py
```
#### callback_plugins/fix-ssl.py
```python
import ssl
if hasattr(ssl, '_create_default_https_context') and hasattr(ssl, '_create_unverified_context'):
    ssl._create_default_https_context = ssl._create_unverified_context

class CallbackModule(object):
    pass
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
### Run the playbook
```
$ ansible-playbook -i hosts buildserver_win.yml 

PLAY [windev] ***************************************************************** 

GATHERING FACTS *************************************************************** 
ok: [testhost-win-01]

TASK: [run ipconfig] ********************************************************** 
ok: [testhost-win-01]

TASK: [debug var=ipconfig] **************************************************** 
ok: [testhost-win-01] => {
    "var": {
        "ipconfig": {
            "invocation": {
                "module_args": "ipconfig", 
                "module_complex_args": {}, 
                "module_name": "raw"
            }, 
            "rc": 0, 
            "stderr": "", 
            "stdout": "\r\nWindows IP Configuration\r\n\r\n\r\nEthernet adapter Ethernet:\r\n\r\n   Connection-specific DNS Suffix  . : \r\n   Link-local IPv6 Address . . . . . : fe80::548d:db75:21ad:717d%12\r\n   IPv4 Address. . . . . . . . . . . : 10.10.9.37\r\n   Subnet Mask . . . . . . . . . . . : 255.255.252.0\r\n   Default Gateway . . . . . . . . . : 10.10.10.1\r\n\r\nTunnel adapter isatap.{09F26B0B-E53F-42CF-BA7D-F125637D996A}:\r\n\r\n   Media State . . . . . . . . . . . : Media disconnected\r\n   Connection-specific DNS Suffix  . : \r\n\r\nTunnel adapter Teredo Tunneling Pseudo-Interface:\r\n\r\n   Connection-specific DNS Suffix  . : \r\n   IPv6 Address. . . . . . . . . . . : 2001:0:9d38:6abd:309d:5437:fbf0:b625\r\n   Link-local IPv6 Address . . . . . : fe80::309d:5437:fbf0:b625%23\r\n   Default Gateway . . . . . . . . . : ::\r\n", 
            "stdout_lines": [
                "", 
                "Windows IP Configuration", 
                "", 
                "", 
                "Ethernet adapter Ethernet:", 
                "", 
                "   Connection-specific DNS Suffix  . : ", 
                "   Link-local IPv6 Address . . . . . : fe80::548d:db75:21ad:717d%12", 
                "   IPv4 Address. . . . . . . . . . . : 10.10.9.37", 
                "   Subnet Mask . . . . . . . . . . . : 255.255.252.0", 
                "   Default Gateway . . . . . . . . . : 10.10.10.1", 
                "", 
                "Tunnel adapter isatap.{09F26B0B-E53F-42CF-BA7D-F125637D996A}:", 
                "", 
                "   Media State . . . . . . . . . . . : Media disconnected", 
                "   Connection-specific DNS Suffix  . : ", 
                "", 
                "Tunnel adapter Teredo Tunneling Pseudo-Interface:", 
                "", 
                "   Connection-specific DNS Suffix  . : ", 
                "   IPv6 Address. . . . . . . . . . . : 2001:0:9d38:6abd:309d:5437:fbf0:b625", 
                "   Link-local IPv6 Address . . . . . : fe80::309d:5437:fbf0:b625%23", 
                "   Default Gateway . . . . . . . . . : ::"
            ]
        }
    }
}

PLAY RECAP ******************************************************************** 
testhost-win-01              : ok=3    changed=0    unreachable=0    failed=0   
```


## Limitations
Check the issues page for bugs and known limitations within the scripts

