# Ansible Role: Network Setup

## **Role Purpose**

This Ansible role renames the active network interface to a desired name using Netplan. It ensures network interface consistency and is particularly useful in environments where standard naming conventions are critical. The role also provides information about the active network interface before and after the renaming process.

---

## **Requirements and Dependencies**

### **Supported Systems**
- Linux distributions using Netplan for network management.

### **Required Privileges**
- Root or sudo access is required to modify Netplan configurations.
---

## **Role Variables**

The role uses the following variables for configuration:

| Variable                  | Source    | Description                                                      |
|---------------------------|-----------|------------------------------------------------------------------|
| `new_network_interface_name` | Defaults  | The desired name for the active network interface (e.g., `net0`). 

---

## **Example Playbook Usage**

Create a playbook `network_setup.yml` that includes this role:

```yaml
- hosts: main
  become: true
  roles:
    - { role: network_setup, new_network_interface_name: "net0" }
```

### **Running the Playbook**

Run the playbook with the following command:

```bash
ansible-playbook playbook.yml
```
---

## **Steps Performed**

### **1. Gather System Facts**
- Collects network-related system facts using the `setup` module.

### **2. Identify Active Network Interface**
- Detects the currently active network interface using the `ip` command.
- Displays the active interface before any changes.

### **3. Rename the Active Network Interface**
- Creates a backup of the existing Netplan configuration file (`/etc/netplan/50-cloud-init.yaml`).
- Replaces the name of the active network interface with `new_network_interface_name` in the Netplan configuration file.
- Applies the updated Netplan configuration if changes are detected.

### **4. Display Updated Interface Details**
- Retrieves and displays detailed information about the renamed network interface.

---

## **Notes**
- **Netplan Configuration:** Ensure `/etc/netplan/50-cloud-init.yaml` exists and is correctly formatted before running the playbook.
- **Backup Management:** The role creates a backup of the original Netplan configuration file to `/etc/netplan/50-cloud-init.yaml.bak` for recovery.
- **Variable Management:** Customize the `new_network_interface_name` variable if a different name is desired.