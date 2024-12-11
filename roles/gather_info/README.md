# Ansible Role: Gather System Information

## **Role Purpose**

The `gather_info` role collects and displays key system information, including:
1. The total number of CPUs.
2. Details about Intel Hyper-Threading or AMD Simultaneous Multithreading (SMT), such as threads per core, CPU model, and vendor.

This role is useful for auditing and understanding the hardware capabilities of managed systems.

---

## **Requirements and Dependencies**

### **Supported Systems**
- Linux systems with `lscpu` available (most modern distributions include this by default).
---

## **Example Playbook Usage**

Create a playbook that includes this role:

```yaml
- hosts: main
  become: false
  roles:
    - gather_info
```

### **Running the Playbook**

Run the playbook with the following command:

```bash
ansible-playbook playbook.yml
```
---

## **Steps Performed**

### **1. List All CPUs**
- Counts the total number of CPUs on the system by parsing `/proc/cpuinfo`.
- Displays the result in a human-readable format.

### **2. Check for Hyper-Threading or SMT**
- Uses `lscpu` to extract detailed information about threading, CPU model, and vendor.
- Displays the results in a structured and human-readable format.

---

## **Notes**
- **Dynamic Role:** This role does not require any prior configuration and works dynamically to gather real-time system information.
- **Portable:** The role uses standard Linux commands, ensuring compatibility with most distributions.