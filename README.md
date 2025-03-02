# Ansible Role: node_info

## Overview

The `node_info` role collects and documents system information about a node, including block devices, partitions, network interfaces, and cloud provider metadata. The collected information is compiled into a markdown report.

## Role Structure


## Variables

Defined in `roles/node_info/defaults/main.yaml`:

| Variable                                      | Description                                          | Default Value                                          |
| --------------------------------------------- | ---------------------------------------------------- | ------------------------------------------------------ |
| `node_info_output_path`                       | Path where the node information report will be saved | `{{ playbook_dir }}/output/{{ inventory_hostname }}`   |
| `node_info_output_file`                       | Path and file name of the generated markdown report  | `{{ node_info_output_path }}/host-info.md`             |
| `node_info_metadata_url`                      | Base URL for cloud provider metadata                 | `http://169.254.169.254/latest/meta-data/`             |
| `node_info_metadata_url_block_device_mapping` | URL for block device mapping metadata                | `{{ node_info_metadata_url }}block-device-mapping/`    |
| `node_info_metadata_url_interfaces`           | URL for network interfaces metadata                  | `{{ node_info_metadata_url }}network/interfaces/macs/` |
| `node_info_ns_files`                          | List of files containing name resolution settings    | `/etc/hosts`, `/etc/resolv.conf`, `/etc/nsswitch.conf` |

## Tasks

Defined in `roles/node_info/tasks/main.yaml`:

1. **Ensure destination path exists**

   - Creates the directory to store the node information report.

2. **Enlist block devices**

   - Uses `lsblk` to list block devices and partitions.

3. **Collect partition information**

   - Runs `fdisk -l` to gather partition details.

4. **Collect NVMe device information**

   - Runs `nvme list` to check NVMe devices.

5. **Collect name resolution settings**

   - Reads the contents of name resolution-related files using `cat`.

6. **Collect network interface info (OS level)**

   - Uses Ansible facts to gather MAC addresses, IPv4 addresses, and active status of network interfaces.

7. **Collect cloud provider network interface info**

   - Queries cloud provider metadata for local IPv4 addresses.

8. **Collect cloud provider block device mapping**

   - Retrieves block device mappings from the cloud provider.

9. **Collect detailed block device mapping**

   - Queries additional metadata URLs for block device mapping details.

10. **Generate and store node information as markdown**

    - Uses a Jinja2 template to generate a markdown report.

## Templates

Defined in `roles/node_info/templates/host-info.md.j2`:

The template organizes node information into the following sections:

- **General node information**: Architecture, distribution, hostname, FQDN, root device.
- **Network information**: Interfaces at both OS and cloud provider levels.
- **Name resolution settings**: Contents of `/etc/hosts`, `/etc/resolv.conf`, and `/etc/nsswitch.conf`.
- **Block device information**: Block devices, partitions, NVMe devices, and cloud provider mappings.

## Usage

Include the role in your playbook like this:

```yaml
- name: Collect node information
  hosts: all
  roles:
    - node_info

    
```

The output report will be saved to the path defined by "node_info_output_file varialbe .



