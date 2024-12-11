# Ansible Playbook: Disk Encryption with LUKS

## **Playbook Purpose**

This Ansible playbook automates the process of encrypting a disk using LUKS (Linux Unified Key Setup), creating a secure and persistent encrypted volume. The playbook performs the following tasks:
- Installs the required `cryptsetup` package.
- Generates a secure LUKS key file.
- Formats the specified disk with LUKS encryption.
- Creates an encrypted mapping, formats the volume, and mounts it to a specified location.

---

## **Requirements and Dependencies**

### **Supported Systems**
- Linux distributions with support for `cryptsetup` (e.g., Ubuntu, Debian).

### **Required Privileges**
- Root or sudo access is required to perform disk encryption and mounting operations.
---

## **Role Variables**

The playbook uses the following variables to configure the disk encryption process:

| Variable          | Source       | Description                                                                                       |
|-------------------|--------------|---------------------------------------------------------------------------------------------------|
| `disk_to_encrypt` | Inventory    | The path to the disk to be encrypted (e.g., `/dev/xvdf`).                                          |
| `key_file`        | Vars         | Path to the LUKS key file (e.g., `/root/luks-keyfile`).                                           |
| `encrypted_name`  | Defaults     | Name of the encrypted LUKS device mapping (e.g., `encrypted_disk`).                                 |
| `mount_point`     | Defaults     | The directory where the encrypted disk will be mounted (e.g., `/mnt/encrypted`).                    |
---
## **Steps Performed**

### **1. Ensure `cryptsetup` is Installed**
- Installs the `cryptsetup` package if not already present.

### **2. Generate and Secure a LUKS Key File**
- Creates a key file using `/dev/urandom`.
- Sets restrictive permissions on the key file (`0600`).

### **3. Encrypt the Disk**
- Checks if the disk is already LUKS-encrypted.
- If not, formats the disk with LUKS encryption using the generated key file.

### **4. Map and Open the Encrypted Disk**
- Maps the encrypted disk to `/dev/mapper/{{ encrypted_name }}`.
- Verifies that the mapping is successful.

### **5. Format the Encrypted Volume**
- Checks if a filesystem exists on the encrypted device.
- If not, creates an `ext4` filesystem.

### **6. Mount the Encrypted Disk**
- Creates the specified mount point directory.
- Mounts the encrypted disk at the `mount_point`.
---
## **Example Playbook Usage**

Create a playbook that includes this role:

```yaml
- hosts: main
  become: yes
  roles:
    - { role: disk_encryption }
```

### **Running the Playbook**

Run the playbook with the following command:

```bash
ansible-playbook playbook.yaml
```
---

## **Notes**
- **Data Loss Warning:** This playbook formats the specified disk. Ensure that the disk does not contain any critical data before running.
- **Key Management:** Secure the `key_file` as it is essential for accessing the encrypted disk. It is not recommended to keep key_file on machine with encrypted disk.