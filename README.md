# Building the Linux Kernel from the Linus Tree (HCBS-patch)

This guide provides the full procedure for preparing a custom Linux kernel required by MC-Kube.
It explains how to clone the HCBS-patch repository, check out the appropriate base tag, build the kernel with the necessary configuration, install it on the system, and finally boot into the newly built kernel environment used by MC-Kube.

---

## 1. Clone the repository

```bash
git clone https://github.com/hufs-ese-lab/HCBS-patch.git
cd HCBS-patch
```

---

## 2. Check out the base tag (detached HEAD)

```bash
git checkout for-mc-kube
```

---

## 3. Build the kernel

```bash
make -j"$(nproc)" LOCALVERSION=-mckube
```

---

## 4. Install kernel modules

```bash
sudo make modules_install
```

---

## 5. Install the kernel

```bash
sudo make install
```

---

## 6. Update GRUB configuration

Edit the GRUB config to set the newly built kernel as the default:

```bash
sudo nano /etc/default/grub

#Update the `GRUB_DEFAULT` entry to match the kernel entry you just installed.
#GRUB_DEFAULT="Advanced options for Ubuntu>Ubuntu, with Linux 6.16.0-rc4-mckube”

#Then apply the changes
sudo update-grub
```

---

## 7. Reboot into the new kernel

```bash
sudo reboot
```

---

## 8. Verify the kernel version

After reboot, run:

```bash
uname -r
```

You should see the version string of the kernel you just built (with the `-mckube` suffix)

---

## 9. Setting Cgroup

```bash
echo 900000 | sudo tee /sys/fs/cgroup/cpu.rt_runtime_us
echo 1000000 | sudo tee /sys/fs/cgroup/kubepods.slice/cpu.rt_period_us
echo 900000 | sudo tee /sys/fs/cgroup/kubepods.slice/cpu.rt_runtime_us

echo 1000000 | sudo tee /sys/fs/cgroup/kubepods.slice/kubepods-besteffort.slice/cpu.rt_period_us
echo 900000 | sudo tee /sys/fs/cgroup/kubepods.slice/kubepods-besteffort.slice/cpu.rt_runtime_us
```

Linux kernel
============

There are several guides for kernel developers and users. These guides can
be rendered in a number of formats, like HTML and PDF. Please read
Documentation/admin-guide/README.rst first.

In order to build the documentation, use ``make htmldocs`` or
``make pdfdocs``.  The formatted documentation can also be read online at:

    https://www.kernel.org/doc/html/latest/

There are various text files in the Documentation/ subdirectory,
several of them using the reStructuredText markup notation.

Please read the Documentation/process/changes.rst file, as it contains the
requirements for building and running the kernel, and information about
the problems which may result by upgrading your kernel.
