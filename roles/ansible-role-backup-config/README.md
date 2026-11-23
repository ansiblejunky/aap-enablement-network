# Ansible Role - ansible-role-backup-config

Backup network configurations into Git repository.

## Variables

Ansible Role has public variables [here](./defaults/main.yml) that have default values but can be overridden.

## Examples

Example of how to use the role (for instance, with variables passed in as parameters):

```yaml
---
- hosts: servers
  roles:
      - { role: username.rolename, x: 42 }
```

## Extras

Consider using [git modules](https://github.com/lvrfrc87/git-acp-ansible)

## License

BSD

## Author Information

John Wadleigh
