# ansible-secure-sshd

Ansible role for securing SSHD daemon.

### Example playbook

```yaml
---
- hosts: server1
  roles:
    - role: sunfoxcz.secure-sshd

- hosts: server1
  roles:
    - role: sunfoxcz.secure-sshd
      sshd_allowed_key_types:
        - ed25519
      sshd_available_settings:
        my_config:
          KexAlgorithms:
            - curve25519-sha256@libssh.org
          Ciphers:
            - chacha20-poly1305@openssh.com
          MACs:
            - hmac-sha2-512-etm@openssh.com
          moduli_min_size: 4095
      sshd_use_settings: my_config
```

### What this role does

 * Sets `KexAlgorithms` to Mozilla Moders by default.
 * Sets `Ciphers` to Mozilla Moders by default.
 * Sets `MACs` to Mozilla Moders by default.
 * Enables `RSA` and `ED25519` keys.
 * Disables `ECDSA` and `DSA` keys.
 * Removes Diffie-Hellman Algorithm moduli weaker than `3072` bits by default.

### Optional settings

| Name                   | Default Value                       |
|------------------------|-------------------------------------|
| sshd_allowed_key_types | `ed25519`, `ecdsa`, `rsa`           |
| sshd_config_directory  | `/etc/ssh`                          |
| sshd_key_directory     | `/etc/ssh` (`/var/ssh` for SmartOS) |
| sshd_use_settings      | `mozilla_modern`                    |

See all options and exceptions in [default variables](defaults/main.yml).

### License

Licensed under MIT license. See [LICENSE](LICENSE.md) for details.
