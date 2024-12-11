# Ansible Role: Partition Encryption with LUKS

## **Role Purpose**

This Ansible role automates the encryption of a partition using LUKS (Linux Unified Key Setup) with **LUKS1** encryption. The role prepares the partition, encrypts it, and restores the data with the following key tasks:
1. Backs up any existing data from the partition before encryption.
2. Clears the partition to ensure no residual metadata interferes with encryption.
3. Encrypts the specified partition using LUKS with a secure key file.
4. Restores data to the encrypted partition and mounts it securely.

---

## **Requirements and Dependencies**

### **Supported Systems**
- Linux distributions with support for `cryptsetup` (e.g., Ubuntu, Debian).

### **Required Privileges**
- Root or sudo access is required to encrypt the partition, modify mount points, and manage files.

---

## **Role Variables**

The role uses the following variables to configure the encryption process:

| Variable              | Source       | Description                                                                                       |
|-----------------------|--------------|---------------------------------------------------------------------------------------------------|
| `partition_to_encrypt`| Inventory    | The partition to encrypt (e.g., `/dev/xvda14`).                                                  |
| `key_file`            | Vars         | Path to the LUKS key file (e.g., `/root/luks-keyfile`).                                           |
| `mount_point`         | Vars         | The directory where the encrypted partition will be mounted (e.g., `/mnt/data`).                 |
| `backup_location`     | Vars         | Temporary directory to store backup data during the encryption process (e.g., `/mnt/backup`).     |
| `encrypted_name`      | Defaults     | Name of the encrypted LUKS device mapping (e.g., `encrypted_xvda14`).                            |

---

## **Steps Performed**

### **1. Install Required Package**
- Ensures that the `cryptsetup` package is installed on the target system.

### **2. Backup Existing Data**
- Creates a backup directory and copies data from the partition to the specified backup location.

### **3. Unmount the Partition**
- Unmounts the partition if it is mounted to prepare it for encryption.

### **4. Clear the Partition**
- Clears the first 2 KB of the partition using the `dd` command to remove residual metadata or filesystem signatures.

### **5. Generate and Secure a LUKS Key File**
- Creates a secure key file using `/dev/urandom` and sets restrictive permissions (`0600`).

### **6. Encrypt the Partition**
- Formats the specified partition with LUKS1 encryption using the key file.

### **7. Open the Encrypted Partition**
- Maps the encrypted partition to `/dev/mapper/{{ encrypted_name }}` and ensures the mapping is successful.

### **8. Create a Lightweight Filesystem**
- Formats the encrypted partition with a **vfat** filesystem due to the small size of the partition.

### **9. Mount the Encrypted Partition**
- Creates the specified mount point directory and mounts the encrypted partition.

### **10. Restore Data**
- Restores the backup data to the encrypted partition and cleans up the temporary backup.

---

## **Example Playbook Usage**

Here’s an example playbook that includes this role:

```yaml
- hosts: main
  become: true
  roles:
    - partition_encryption
```

---

## **Running the Playbook**

Run the playbook with the following command:

```bash
ansible-playbook playbook.yml
```

---

## **Notes**

1. **Data Loss Warning**:
   - The role clears and encrypts the specified partition, which causes data loss. Ensure any critical data is backed up before running the playbook.

2. **Key Management**:
   - The key file (`key_file`) is essential for accessing the encrypted partition. Secure the key file and avoid storing it on the same machine as the encrypted partition.

3. **Filesystem Choice**:
   - The role uses **vfat** as the filesystem due to the small size of the partition. For larger partitions, consider using other filesystems like ext4.

4. **Partition Size**:
   - Ensure the partition to be encrypted has sufficient space after accounting for LUKS metadata. Smaller partitions may not accommodate larger filesystems.

---