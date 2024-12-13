# Ansible Playbook: Disk Encryption with LUKS

## **Playbook Purpose**

This Ansible playbook automates the process of encrypting a disk using LUKS (Linux Unified Key Setup), creating a secure and persistent encrypted volume. The playbook performs the following tasks:
- Installs the required `cryptsetup` package.
- Uses a LUKS key file securely stored in Ansible Vault.
- Formats the specified disk with LUKS encryption.
- Creates an encrypted mapping and provides debug information about the disk encryption.

---

## **Requirements and Dependencies**

### **Supported Systems**
- Linux distributions with support for `cryptsetup` (e.g., Ubuntu, Debian).

### **Required Privileges**
- Root or sudo access is required to perform disk encryption and mounting operations.

### **Dependencies**
- `community.crypto` Ansible Collection.

---

## **Role Variables**

The playbook uses the following variables to configure the disk encryption process:

| Variable          | Source          | Description                                                                                       |
|-------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `disk_to_encrypt` | Inventory       | The path to the disk to be encrypted (e.g., `/dev/xvdf`).                                          |
| `key_file`        | Vars            | Path to the LUKS key file (e.g., `/root/luks-keyfile`).                                           |
| `luks_key_content`| Ansible Vault   | The LUKS key content stored securely in Ansible Vault.                                            |
| `encrypted_name`  | Defaults        | Name of the encrypted LUKS device mapping (e.g., `encrypted_disk`).                              |

---

## **Steps Performed**

### **1. Ensure `cryptsetup` is Installed**
- Installs the `cryptsetup` package if not already present.

### **2. Create a LUKS Key File from Ansible Vault**
- Copies the LUKS key file content from Ansible Vault to the target system.
- Sets restrictive permissions on the key file (`0600`).

### **3. Encrypt the Disk**
- Checks if the disk is already LUKS-encrypted.
- If not, formats the disk with LUKS encryption using the `community.crypto.luks_device` module.

### **4. Map and Open the Encrypted Disk**
- Maps the encrypted disk to `/dev/mapper/{{ encrypted_name }}` using the LUKS key file.

### **5. Debug Information**
- Provides disk information and an encryption dump for debugging purposes.

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
ansible-playbook playbook.yml ----vault-password-file .ansible/pass-text.txt
```

---

## **Notes**
- **Data Loss Warning:** This playbook formats the specified disk. Ensure that the disk does not contain any critical data before running.
- **Key Management:** Secure the `luks_key_content` in Ansible Vault. This key is essential for accessing the encrypted disk.
- **Debug Information:** Disk and encryption information are output for verification and troubleshooting. The sensitive encryption dump is commented out by default to prevent accidental exposure.