# Container Management Role

This role provides tasks for managing containers, including checking status, starting, stopping, and restarting containers using podman or docker. For Podman operations, this role uses the `containers.podman` collection modules for improved reliability and functionality.

## Requirements

- Ansible 2.9 or higher
- Podman or Docker installed on the target system
- `containers.podman` collection installed when using Podman (can be installed with `ansible-galaxy collection install containers.podman`)

## Role Variables

### Main Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `container_action` | Action to perform on containers (status, start, stop, restart) | `status` |
| `container_debug` | Enable debug output | `false` |
| `container_engine` | Container engine to use (podman, docker) | `podman` |

### Container Options

| Variable | Description | Default |
|----------|-------------|---------|
| `container_stop_timeout` | Seconds to wait for graceful stop | `10` |
| `container_force_stop` | Whether to force stop if graceful stop fails | `false` |

## Dependencies

None

## Example Playbook

```yaml
- name: Manage Containers
  hosts: container_hosts
  become: false
  gather_facts: true
  
  tasks:
    - name: Get container status
      ansible.builtin.import_role:
        name: rhdc.system.container_management
      vars:
        container_action: "status"
        container_debug: true
        
    - name: Stop all containers
      ansible.builtin.import_role:
        name: rhdc.system.container_management
      vars:
        container_action: "stop"
        container_engine: "podman"
        
    - name: Start all containers
      ansible.builtin.import_role:
        name: rhdc.system.container_management
      vars:
        container_action: "start"
        container_engine: "podman"
```

## License

MIT

## Author Information

Rodhouse Datacenter Team
