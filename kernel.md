# Custom Linux Kernel Build (v6.14.8-nithin)

This repository documents my journey and steps taken to build a **custom Linux kernel (v6.14.8)** for experimentation and learning purposes. The goal was to build a **minimal, fast, and secure kernel** without bloated features, optimized for a VirtualBox VM.

---

## 🧠 System Overview

* **Host OS**: Linux Mint (based on Debian/Ubuntu)
* **Test Environment**: VirtualBox VM
* **CPU**: Intel (Generic x86 family)
* **Kernel Source**: [kernel.org](https://www.kernel.org/) (v6.14.8)

---

## ⚙️ Build Steps

### 1. Download Kernel Source

```bash
wget https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.14.8.tar.xz
tar -xvf linux-6.14.8.tar.xz
cd linux-6.14.8
```

### 2. Configure the Kernel

```bash
make menuconfig
```

* Used `menuconfig` to interactively configure features.
* Optimization goals:

  * Lightweight and fast boot.
  * Only Intel CPU support.
  * Disabled unused drivers (GPU switching, hybrid graphics, exotic filesystems).
  * Enabled ZSTD compression for faster load time.
  * Enabled full tickless kernel.
  * POSIX message queues: enabled.
  * Disabled initial RAM disk compression support.
  * Removed unused GPIO and virtualization drivers.
  * Processor type set to **Generic x86** and Intel only.
  * Disabled NUMA, maximum SMP CPUs, and preempt RT.
  * Kernel log buffer size set to 20.

### 3. Compilation

```bash
make -j4
sudo make modules_install
sudo make install
```

> Note: Fixed errors during compilation related to `gelf.h` and `openssl/opensslv.h` by installing missing dependencies:

```bash
sudo apt install libelf-dev libssl-dev
```

Also edited `.config` to:

* Disable module signing: `CONFIG_MODULE_SIG=n`
* Clear keys: `CONFIG_MODULE_SIG_KEY=""`

### 4. Install Kernel Files

```bash
sudo cp arch/x86/boot/bzImage /boot/vmlinuz-6.14.8-nithin
sudo cp System.map /boot/System.map-6.14.8-nithin
sudo cp .config /boot/config-6.14.8-nithin
```

### 5. Update GRUB

```bash
sudo update-grub
```

### 6. Reboot & Select Kernel

* From GRUB menu, select: `Advanced Options > Linux 6.14.8-nithin`

### 7. Confirm Kernel is Active

```bash
uname -r
# Output: 6.14.8-nithin
```

---

## 📊 Memory Usage Results

| Kernel                     | Memory Used (free -h) |
| -------------------------- | --------------------- |
| Default (6.8.0-58-generic) | \~1.7 GiB             |
| Custom (6.14.8-nithin)     | \~1.4 GiB             |

---

## ✅ Goals Achieved

* ✅ Successful build of a custom kernel
* ✅ Booted into the kernel without issues
* ✅ Reduced memory usage by \~300 MB
* ✅ Clean config for minimal experimentation

---

## 📌 Future Work

* Test kernel with custom drivers or patches
* Explore initramfs and kernel module optimization
* Build a fully minimal Linux OS

---

## 🧑‍💻 Author

**Nithin**

* Focus: OS experimentation, performance tuning
* Built on: VirtualBox with Linux Mint
