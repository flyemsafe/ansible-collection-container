Role Name
=========

A brief description of the role goes here.

Requirements
------------

| Name                        | Description                                        | Required | Default |
|-----------------------------|----------------------------------------------------|----------|---------|
| podman_owner                | Username/owner for rootless Podman config           | yes      |         |
| podman_graphroot            | Path for Podman image storage (graphroot)           | yes      |         |
| podman_runroot              | Path for Podman runroot                            | yes      |         |
| podman_volume_path          | Path for Podman volumes                            | yes      |         |
| podman_install              | Install Podman and dependencies?                   | no       | true    |
| podman_enable_user_socket   | Enable user-level systemd podman.socket?            | no       | false   |
| podman_storage_conf_extra   | Extra storage.conf options (dict); by default, driver = "overlay" is enforced unless overridden. | no       | {}      |
| podman_containers_conf_extra| Extra containers.conf options (dict)               | no       | {}      |

## Usage Example

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
        # To override the default storage driver:
        # podman_storage_conf_extra:
        #   driver: '"overlay2"'  # must be quoted for ini
```

**Note:**
- The role will ensure `/opt/podman/containers` exists, is owned by `podman_owner`, and has the correct SELinux context for rootless Podman.
- By default, `driver = "overlay"` is set in storage.conf unless overridden via `podman_storage_conf_extra`.
- For troubleshooting permission errors on `/opt/podman/containers`, see the deployment guide or role docs.

See `docs/podman.md` for full documentation, edge cases, and advanced usage.
Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: username.rolename, x: 42 }

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
