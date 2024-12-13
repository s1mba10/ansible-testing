# Ansible Playbook: Partition Encryption with LUKS

## **Playbook Purpose**

This Ansible playbook automates the process of encrypting a partition using LUKS (Linux Unified Key Setup) with **LUKS1** encryption. It ensures the partition is properly cleaned, encrypted, and securely managed with the following tasks:

1. Clears the partition to remove residual metadata.
2. Encrypts the specified partition using a LUKS key file from Ansible Vault.
3. Opens and verifies the encrypted partition.
4. Closes the encrypted partition securely after operations.

---

## **Requirements and Dependencies**

### **Supported Systems**
- Linux distributions with support for `cryptsetup` (e.g., Ubuntu, Debian).

### **Required Privileges**
- Root or sudo access is required to perform partition encryption and management tasks.

### **Dependencies**
- `community.crypto` Ansible Collection.

---

## **Role Variables**

The playbook uses the following variables to configure the encryption process:

| Variable              | Source          | Description                                                                                       |
|-----------------------|-----------------|---------------------------------------------------------------------------------------------------|
| `partition_to_encrypt`| Inventory       | The partition to encrypt (e.g., `/dev/xvda14`).                                                  |
| `key_file`            | Vars            | Path to the LUKS key file (e.g., `/root/luks-keyfile`).                                           |
| `luks_key_content`    | Ansible Vault   | The LUKS key content stored securely in Ansible Vault.                                            |
| `encrypted_name`      | Defaults        | Name of the encrypted LUKS device mapping (e.g., `encrypted_partition`).                         |

---

## **Steps Performed**

### **1. Install Required Package**
- Ensures that the `cryptsetup` package is installed on the target system.

### **2. Clear the Partition**
- Clears the first 2 KB of the partition using the `dd` command to remove residual metadata or filesystem signatures.

### **3. Set up LUKS Encryption**
- Configures the partition with LUKS1 encryption using the `community.crypto.luks_device` module.
- Uses a key file securely stored in Ansible Vault for encryption.

### **4. Open the Encrypted Partition**
- Maps the encrypted partition to `/dev/mapper/{{ encrypted_name }}` using the key file.

### **5. Verify Encryption**
- Provides disk information for verification.

### **6. Close the Encrypted Partition**
- Closes the encrypted partition securely.

---

## **Example Playbook Usage**

Create a playbook that includes this role:

```yaml
- hosts: main
  become: yes
  roles:
    - partition_encryption
```

### **Running the Playbook**

Run the playbook with the following command:

```bash
ansible-playbook playbook.yml ----vault-password-file .ansible/pass-text.txt
```

---

## **Notes**

1. **Data Loss Warning**:
   - The playbook clears and encrypts the specified partition, which causes data loss. Ensure any critical data is backed up before running the playbook.

2. **Key Management**:
   - The key file (`key_file`) is essential for accessing the encrypted partition. Secure the key file and avoid storing it on the same machine as the encrypted partition.

3. **Encryption Settings**:
   - The playbook uses **LUKS1** encryption with customizable PBKDF iteration count. Adjust these settings based on your security requirements.
