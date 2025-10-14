# Ansible Role: rhdc.container.ramalama

[![Ansible Lint](https://github.com/swyguehub/rhdc-ansible/actions/workflows/ansible-lint-container.yml/badge.svg)](https://github.com/swyguehub/rhdc-ansible/actions/workflows/ansible-lint-container.yml) <!-- Assuming a lint workflow exists for the container collection -->

Installs and configures the [RamaLama CLI](https://github.com/containers/ramalama) for AI workloads, with optional NVIDIA GPU integration using Podman and CDI.

## Requirements

-   Ansible 2.12 or higher.
-   Target host OS: RHEL 8/9, Fedora 39/40 (or compatible).
-   If using GPU support (`ramalama_gpu_enabled: true`):
    -   NVIDIA drivers must be installed and functional (`nvidia-smi` works).
    -   The `nvidia-container-toolkit` package is required (installed by this role).
    -   A prerequisite role (like `rhdc.system.nvidia_gpu`) should handle driver installation.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

| Variable                 | Default           | Description                                                              |
| :----------------------- | :---------------- | :----------------------------------------------------------------------- |
| `ramalama_install_method`| `pip`             | Installation method: `pip` (from PyPI) or `source` (build from GitHub).  |
| `ramalama_version`       | `latest`          | Version of RamaLama to install (tag name, version number, or `latest`). |
| `ramalama_install_dir`   | `/usr/local/bin`  | Directory to install the RamaLama CLI binary.                          |
| `ramalama_config_dir`    | `/etc/ramalama`   | Directory for RamaLama configuration files (`ramalama.conf`).            |
| `ramalama_gpu_enabled`   | `true`            | If `true`, configure NVIDIA GPU integration via CDI.                     |
| `ramalama_test_enabled`  | `true`            | If `true`, run basic CLI and GPU (if enabled) tests after installation. |
| `ramalama_cleanup`       | `false`           | If `true`, include tasks to remove RamaLama binaries, configs, etc. (Not yet implemented) |
| `ramalama_venv_path`     | `""`              | Python virtual environment path (only used for pip installation). If empty, will install system-wide. |

## Dependencies

-   `rhdc.system.nvidia_gpu` (or equivalent): Required when `ramalama_gpu_enabled` is `true` to ensure NVIDIA drivers are properly set up beforehand.

## Example Playbook

```yaml
---
- name: Install and configure RamaLama with GPU support
  hosts: ai_servers
  become: false  # Following the principle of least privilege
  collections:
    - rhdc.container # Make sure collection is available
  roles:
    # Ensure NVIDIA drivers are ready first if using GPU
    - role: rhdc.system.nvidia_gpu
      when: ramalama_gpu_enabled | default(true)

    - role: rhdc.container.ramalama
      vars:
        # Installation method - 'auto', 'package', 'pip', or 'source'
        # 'auto' will use package manager on Fedora, pip on RHEL
        ramalama_install_method: auto

        # Version specification (ignored for package installation)
        ramalama_version: latest

        # GPU integration
        ramalama_gpu_enabled: true

        # Testing
        ramalama_test_enabled: true
```

### Alternative Installation Methods

#### Package Manager Installation (Fedora)

```yaml
- role: rhdc.container.ramalama
  vars:
    ramalama_install_method: package
    ramalama_gpu_enabled: true
```

#### PyPI Installation (RHEL)

```yaml
- role: rhdc.container.ramalama
  vars:
    ramalama_install_method: pip
    ramalama_version: latest
    ramalama_gpu_enabled: true
    # Optional: Use a virtual environment
    # ramalama_venv_path: "/opt/ramalama-venv"
```

#### Build from Source

```yaml
- role: rhdc.container.ramalama
  vars:
    ramalama_install_method: source
    ramalama_version: "v0.1.0"  # Specific version tag from GitHub
    ramalama_gpu_enabled: true
```

## Functionality

### Installation Methods

#### Method 1: Auto Installation (Default)
1. **Detect Distribution:** Automatically selects the appropriate installation method:
   - For Fedora: Uses package manager (dnf)
   - For RHEL and other distributions: Uses pip

#### Method 2: Package Manager Installation
1. **Install Podman:** Ensures `podman` is installed.
2. **Install RamaLama:** Uses the system package manager to install `python3-ramalama`.
3. **Configure RamaLama:** Creates `{{ ramalama_config_dir }}/ramalama.conf` and sets `gpu = true` if `ramalama_gpu_enabled` is true.

#### Method 3: PyPI Installation
1. **Install Podman:** Ensures `podman` is installed.
2. **Install Python Dependencies:** Installs `python3-pip`, `python3-setuptools`, and `python3-wheel`.
3. **Install RamaLama:** Uses pip to install the specified version from PyPI.
4. **Configure RamaLama:** Creates `{{ ramalama_config_dir }}/ramalama.conf` and sets `gpu = true` if `ramalama_gpu_enabled` is true.

#### Method 4: Source Installation
1. **Install Podman:** Ensures `podman` is installed.
2. **Install Build Dependencies:** Installs `git`, `golang`, and `make`.
3. **Install RamaLama:** Clones the specified version from GitHub, builds the binary, and installs it to `ramalama_install_dir`.
4. **Configure RamaLama:** Creates `{{ ramalama_config_dir }}/ramalama.conf` and sets `gpu = true` if `ramalama_gpu_enabled` is true.
5.  **Configure GPU (if enabled):**
    -   Installs `nvidia-container-toolkit`.
    -   Generates the NVIDIA CDI specification (`/etc/cdi/nvidia.yaml`).
    -   Removes the older OCI hook (`/usr/share/containers/oci/hooks.d/oci-nvidia-hook.json`).
6.  **Test (if enabled):**
    -   Checks if `ramalama --version` runs.
    -   If GPU is enabled, runs `podman run --device=nvidia.com/gpu=all ... nvidia-smi` to verify GPU access via CDI.

## License

Apache-2.0

## Author Information

Rodhouse Datacenter Team
