# Ansible Collection - rhdc.containers

This collection provides roles and plugins for container management on Fedora and similar systems, with a focus on rootless Podman and SELinux compatibility.

## Included Roles

- **podman**: Installs Podman and dependencies, configures rootless storage and containers configuration for a specified user, and enables user-level podman.socket. See [`docs/podman.md`](docs/podman.md) for complete documentation and usage.

## Documentation

- Full documentation for each role is available in the `docs/` directory.
- Role-level README files are available in each role's directory.

## Quick Usage Example

```yaml
- hosts: all
  roles:
    - role: rhdc.containers.podman
      vars:
        podman_owner: "radmin"
        podman_graphroot: "/opt/podman/images"
        podman_runroot: "/opt/podman/runroot"
        podman_volume_path: "/opt/podman/volumes"
        podman_enable_user_socket: true
```

## Support & Contributions

- Please see the `galaxy.yml` for authorship and licensing information.
- Issues and contributions are welcome via the repository.
