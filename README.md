**sftp_user**
==============

An Ansible role to create an account that can only use SFTP on Debian.

Requirements
------------

- Debian with OpenSSH server, `ssh.service` running, and `/etc/ssh/sshd_config.d/` included by `sshd_config`.
- Ansible Core 2.18 or newer.
- The `ansible.posix` collection (`authorized_key` and `acl` modules).
- Privileged access to the managed host.

Role Variables
--------------

| Variable | Default | Description |
|---|---|---|
| `sftp_user_username` | `""` | Name of the account. Required. |
| `sftp_user_system` | `true` | Create a system account. Cannot be changed once the account exists. |
| `sftp_user_shell` | `/usr/sbin/nologin` | Login shell of the account. |
| `sftp_user_ssh_public_key` | `""` | Public key allowed to connect. Required. |
| `sftp_user_ssh_key_options` | `""` | Options written before the key in `authorized_keys`, for example a `from=` restriction. |
| `sftp_user_groups` | `[]` | Supplementary groups. Add here the group your sshd requires in `AllowGroups`, if any. |
| `sftp_user_readonly` | `true` | Run the SFTP server in read-only mode (`internal-sftp -R`). |
| `sftp_user_read_paths` | `[]` | Paths the account gets ACL access to, for existing and future files. |
| `sftp_user_permission_scope` | `rX` | ACL permissions given on those paths. |

The role writes `/etc/ssh/sshd_config.d/70-sftp-user-<name>.conf`, validates it with `sshd -t` and reloads sshd when it changes.

Dependencies
------------

No additional Ansible roles are required.

Example Playbook
----------------

```yaml
---
- name: Create a read-only SFTP account for backups
  hosts: app_servers
  become: true

  roles:
    - role: sftp_user
      vars:
        sftp_user_username: backup
        sftp_user_ssh_public_key: "{{ lookup('ansible.builtin.file', 'files/backup.pub') }}"
        sftp_user_ssh_key_options: 'from="192.0.2.10"'
        sftp_user_read_paths:
          - /srv/data
```

License
-------

MIT

Author Information
------------------

TheWolfPaws
