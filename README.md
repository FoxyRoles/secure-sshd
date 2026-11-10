# ansible-secure-sshd

Ansible role for configuring OpenSSH server algorithms, host key types and
Diffie-Hellman moduli, and hiding Debian version information in the SSH banner.

### Requirements

The role expects an existing OpenSSH server installation, host keys and a
`moduli` file. Gather Ansible facts and run with privileges to modify the SSH
configuration and restart the `ssh` service. The configured algorithms must be
supported by the target OpenSSH version.

### Example playbook

```yaml
---
- hosts: server1
  become: true
  roles:
    - role: sunfoxcz.secure-sshd

- hosts: server1
  become: true
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
      sshd_use_settings: my_config
```

### What this role does

 * Sets `KexAlgorithms`, `Ciphers` and `MACs` using the selected profile. The
   default is the bundled `mozilla_modern` profile; `mozilla_intermediate` is
   also available. These are static profiles stored in this repository.
 * Enables existing `ED25519`, `ECDSA` and `RSA` host keys by default and removes
   explicit `DSA` host key entries from `sshd_config`.
 * Removes Diffie-Hellman moduli smaller than 3072 bits (the size field in
   OpenSSH's `moduli` file is one less than the actual bit length).
 * Sets `DebianBanner no` on Debian-family systems to hide Debian version
   information in the SSH identification banner. Other OS families skip this
   task.
 * Restarts the `ssh` service when the configuration or moduli change.

The role does not generate or strengthen host keys. The existing
`tasks/key_strength.yml` file is not imported by the role.

### Optional settings

| Name | Default value | Purpose |
|------|---------------|---------|
| `sshd_key_types` | `ed25519`, `ecdsa`, `rsa`, `dsa` | Host key types managed by the role. |
| `sshd_allowed_key_types` | `ed25519`, `ecdsa`, `rsa` | Managed host key types to enable; other managed types have their explicit entries removed. |
| `sshd_config_directory` | `/etc/ssh` | Directory used for `moduli` and the Debian banner task. |
| `sshd_key_directory` | `/etc/ssh` (`/var/ssh` for SmartOS) | Directory containing existing host keys. |
| `sshd_use_settings` | `mozilla_modern` | Key of the selected algorithm profile. |
| `sshd_available_settings` | See defaults | Profiles containing `KexAlgorithms`, `Ciphers` and `MACs` lists. |
| `sshd_distro_settings` | SmartOS host key directory override | Distribution-specific directory defaults. |

Algorithm and host key configuration tasks currently edit `/etc/ssh/sshd_config`
directly, regardless of `sshd_config_directory`. The restart handler uses the
service name `ssh`.

The moduli filter currently uses a fixed size-field threshold of `3071` (3072
bits). The `moduli_min_size` values in the bundled profiles are not used, so
selecting another profile does not change the moduli threshold.

See all options and exceptions in [default variables](defaults/main.yml).

### License

Licensed under MIT license. See [LICENSE](LICENSE.md) for details.
