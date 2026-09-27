# Keyboard-Like Virtual Character Device Driver in Linux

A simple Linux **character device driver** that simulates a keyboard-like text input device. The project demonstrates how user-space applications communicate with kernel-space code through a Loadable Kernel Module (LKM).

## Project Overview

This project implements a virtual character device called `mydevice`. Users can write text to the device and read the stored text back using standard Linux commands such as `echo` and `cat`.

The project is designed for learning basic **Operating System and Linux Kernel concepts** without requiring any physical hardware.

## Objectives

* Understand Linux Kernel Module programming
* Implement a character device driver using C
* Understand kernel-space and user-space communication
* Implement `open()`, `read()`, `write()`, and `release()` operations
* Understand major and minor device numbers
* Learn basic kernel logging and debugging

## Technologies Used

* **Language:** C
* **Operating System:** Ubuntu Linux
* **Compiler:** GCC
* **Kernel:** Linux Kernel
* **Tools:** `insmod`, `rmmod`, `lsmod`, `dmesg`, `mknod`
* **Editors:** Nano / VS Code

## Project Structure

```text
.
├── mydevice.c
├── Makefile
└── README.md
```

## How It Works

The driver creates a virtual character device named `mydevice`.

The basic data flow is:

```text
User Space
    │
    │ write()
    ▼
/dev/mydevice
    │
    ▼
Kernel Space
    │
    │ Kernel Buffer
    ▼
/dev/mydevice
    │
    │ read()
    ▼
User Space
```

Data written by the user is copied from **user space to kernel space** using `copy_from_user()` and stored in a kernel buffer.

When the user reads from the device, the data is copied back from **kernel space to user space** using `copy_to_user()`.

## Compilation

Make sure the Linux kernel headers and GCC are installed.

```bash
sudo apt update
sudo apt install build-essential linux-headers-$(uname -r)
```

Compile the kernel module:

```bash
make
```

This generates the kernel module:

```text
mydevice.ko
```

## Loading the Driver

Load the kernel module:

```bash
sudo insmod mydevice.ko
```

Check the kernel log to find the assigned major number:

```bash
dmesg | tail
```

You should see something similar to:

```text
MyDevice loaded with Major Number 240
Create device using: mknod /dev/mydevice c 240 0
```

The major number may be different on your system.

## Creating the Device File

Create the device file using the major number shown by `dmesg`:

```bash
sudo mknod /dev/mydevice c <MAJOR_NUMBER> 0
```

For example:

```bash
sudo mknod /dev/mydevice c 240 0
```

Give the device appropriate permissions:

```bash
sudo chmod 666 /dev/mydevice
```

## Testing the Driver

Write text to the virtual device:

```bash
echo "Hello from User Space" > /dev/mydevice
```

Read the stored text:

```bash
cat /dev/mydevice
```

Expected output:

```text
Hello from User Space
```

You can also monitor kernel messages:

```bash
dmesg | tail
```

## Removing the Driver

Remove the device file:

```bash
sudo rm /dev/mydevice
```

Unload the kernel module:

```bash
sudo rmmod mydevice
```

Verify that the module has been removed:

```bash
lsmod | grep mydevice
```

## Important Concepts Demonstrated

### Character Device

The driver behaves like a file and supports standard file operations such as reading and writing.

### Major and Minor Numbers

The **major number** identifies the driver, while the **minor number** identifies a particular device handled by that driver.

### Kernel Buffer

A fixed-size buffer is used to temporarily store data written to the device.

### User-Kernel Communication

The driver uses:

```c
copy_from_user()
```

to transfer data from user space to kernel space, and:

```c
copy_to_user()
```

to transfer data from kernel space to user space.

### Loadable Kernel Module

The driver can be dynamically loaded and removed using:

```bash
insmod
```

and:

```bash
rmmod
```

## Limitations

* This is a simulated device and does not communicate with real keyboard hardware.
* It does not use hardware interrupts.
* Only a single kernel buffer is used.
* Advanced blocking and non-blocking I/O are not implemented.
* The driver is intended primarily for educational purposes.

## Future Improvements

* Character-by-character input handling
* Blocking and non-blocking I/O
* Multiple device instances
* Improved buffer management
* Integration with actual Linux input-device interfaces

## Authors

**Muhammad Awais** — 23-CS-055
**Muhammad Huzaifa** — 23-CS-007

**Section:** 5C
**Department of Computer Science**
**HITEC University, Taxila**

## License

This project is created for educational purposes as part of an Operating Systems course.
