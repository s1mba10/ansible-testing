# Ansible Playbook: Comprehensive System Configuration and Tuning

## **Purpose**

This playbook orchestrates the following system configuration tasks:
1. **Disk and Partition Encryption:** Secures data using LUKS encryption.
2. **Network Setup:** Renames the active network interface to `net0` (or a specified name).
3. **System Information Gathering:** Displays CPU count and Hyper-Threading (SMT) details.
4. **CPU Tuning:** Optimizes CPU performance by disabling C-states and applying the `latency-performance` profile.

---

## **Usage**

### **Run the Playbook**
Execute the playbook to apply all tasks:
```bash
ansible-playbook playbook.yml ----vault-password-file .ansible/pass-text.txt
```

### **Skip Persistent Changes in CPU Tuning**
To skip rebooting and non-persistent changes for `cpu_tuning`, use:
```bash
ansible-playbook playbook.yml ----vault-password-file .ansible/pass-text.txt --skip-tags persistent
```

---

## **Roles Included**

| Role                 | Description                                                                                 |
|----------------------|---------------------------------------------------------------------------------------------|
| `disk_encryption`    | Encrypts entire disks using LUKS.                                                          |
| `partition_encryption`| Encrypts specific partitions adjacent to the root partition.                               |
| `network_setup`      | Renames the active network interface to `net0` or a specified name.                        |
| `gather_info`        | Collects and displays system information, including CPU count and Hyper-Threading/SMT info.|
| `cpu_tuning`         | Disables CPU C-states and applies the `latency-performance` profile for performance tuning. |

---

## **Customizations**

- **Network Interface Name:** Change `new_network_interface_name` to customize the desired interface name.
- **Skipping Persistent Changes:** Use `--skip-tags persistent` for testing CPU tuning without making changes permanent.

---

## **Testing and Verification**

1. **Encryption:**
   - Verify encrypted mappings with `lsblk`.

2. **Network:**
   - Check the renamed interface using `ip addr show dev net0`.

3. **System Info:**
   - Display CPU count and threading details with `lscpu`.

4. **CPU Tuning:**
   - Confirm the active tuned profile with `tuned-adm active`.

---

## **Notes**

- Ensure privileged access for tasks modifying system configurations.
- Use backups created during execution (e.g., Netplan and GRUB) for recovery if needed.