# RHDC.Container Collection

This collection provides roles for managing containers using Podman, focusing on rootless containers with proper SELinux contexts.

## Roles

- **container_management**: Manage container lifecycle (status, start, stop) using containers.podman modules

## Installation

```bash
ansible-galaxy collection install /path/to/ansible-collection-container -f
```

## Usage

See the documentation in the `docs` directory for detailed usage instructions.

## Requirements

- Ansible 2.9 or higher
- Podman installed on target systems
- containers.podman collection (can be installed with `ansible-galaxy collection install containers.podman`)
- SELinux properly configured for container operations (if SELinux is enabled)
