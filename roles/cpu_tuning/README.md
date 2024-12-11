# Ansible Role: CPU Performance Optimization

This Ansible role disables C-states for all available CPUs and switches the CPU operation mode from power-saving to performance-oriented using the `tuned-adm` profile `latency-performance`. Additionally, it ensures the changes are persistent across reboots.

## Features

- **Disable C-states:** Prevents CPUs from entering power-saving states for improved performance.
- **Performance Mode:** Activates the `latency-performance` profile using `tuned-adm` for low-latency and high-performance operation.
- **Persistence:** Ensures the C-state disabling configuration is persistent by adding kernel parameters to the GRUB configuration.
- **Reboot Management:** Optionally reboots the system to apply persistent changes.

---

## Requirements

- **Operating System:** Compatible with Linux distributions that support `tuned` and GRUB (tested on Ubuntu).
- **Privileges:** Root or sudo access is required for tasks such as updating GRUB and rebooting the system.

---

## Role Variables

**Kernel Parameters for GRUB**
This role does not require custom variables but uses the following hardcoded configurations o disable C-states and optimize CPU performance:
| Kernel Parameter          | Description                                                                                  |
|---------------------------|----------------------------------------------------------------------------------------------|
| `intel_idle.max_cstate=0` | Prevents the CPU from entering any idle states, ensuring maximum performance at higher power usage. |
| `processor.max_cstate=1`  | Restricts the CPU to the shallow C1 state, enabling fast recovery from idle.                 |
| `nohz=off`                | Keeps periodic timer ticks during idle states, improving task scheduling and predictability. |
| `highres=off`             | Disables high-resolution timers to reduce scheduling overhead.                              |
| `tsc=reliable`            | Forces the system to rely on the Time Stamp Counter (TSC) for faster and more efficient timekeeping. |


---

## Steps Performed

### **1. Disable C-States**
- Uses the `find` command to locate CPU C-state control files.
- Writes `1` to each `disable` file to turn off the corresponding C-state.

### **2. Verify C-State Status**
- Reads back the value of each `disable` file to confirm C-states are disabled.
- Outputs the verification results.

### **3. Install and Configure `tuned`**
- Ensures `tuned` is installed.
- Activates the `latency-performance` profile for low-latency, high-performance operation.
- Verifies the active tuned profile.

### **4. Make Changes Persistent**
- Adds kernel parameters to the GRUB configuration to persistently disable C-states.
- Updates the GRUB configuration and reboots the system to apply changes.

---

## Usage

### **Step 1: Include the Role in Your Playbook**
Create a playbook that calls this role:

```yaml
- hosts: main
  become: true
  roles:
    - cpu-tuning
```

### **Step 3: Run the Playbook**
Execute the playbook using the `ansible-playbook` command:

```bash
ansible-playbook playbook.yml
```

### **Optional Tags**
- Use the `--skip-tags persistent` tag to skip tasks that make the configuration persistent and skip rebooting of the system:
  ```bash
  ansible-playbook playbook.yml --skip-tags persistent
  ```
---