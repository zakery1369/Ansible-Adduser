## Adduser with Ansible in Debian
![Ansible Adduser](https://raw.githubusercontent.com/zakery1369/pics/master/Ansible-Debian.png)

This Ansible creates your desired user along with the public key and also places this user in the sudo group and add user to AllowUsers in ssh config.

### Customizing files

1.Change the remote_user in adduser.yaml.

```bash
##adduser.yaml

- name: Create user and add SSH key
  hosts: hosts
  remote_user: root
  become: yes


  vars_files:
  - values.yaml

  tasks:
  - name: Add the user
    ansible.builtin.user:
      name: "{{ username }}"
      password: "{{ password }}"
      shell: /bin/bash
      groups: sudo
      createhome: yes
     
  - name: Add the SSH public key
    authorized_key:
      user: "{{ username }}"
      key: "{{ lookup('file', ssh_public_key) }}"
      state: present


  - name: Add user to AllowUsers in sshd_config
    ansible.builtin.shell:
      cmd: "sed -i -e '/^AllowUsers/ s/$/ {{ username }}/' /etc/ssh/sshd_config"

  - name: Restart SSH server
    ansible.builtin.service:
      name: ssh
      state: restarted
```

2.Add the public key in id_rsa.pub.

```bash
##id_rsa.pub

ssh-rsa AAANZ5cZSQyc2EAANDAQA.....
```

3.Update the IP address and port in the inventory.

```bash
##inventory

[hosts]
192.168.100.20:4422
```
4.Change the username and password and public key path in values.yaml.

```bash
##values.yaml

username: YourUserName
password: $2y$10$G0l.owcCMBOtgmr9C/.55OBzliuFiHxD1wuDXuJsmIHJCWIXwFTxq
ssh_public_key: "/path/to/publik/key/id_rsa.pub"
```

> [!CAUTION]
> The password must be hashed. default password is 14789632
