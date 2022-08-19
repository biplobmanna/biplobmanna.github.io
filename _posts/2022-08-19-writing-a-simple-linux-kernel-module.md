---
layout: post
title:  "Writing a simple linux kernel module"
date: 2022-08-19 08:18:15 +0530
categories: programming
---

My interest into Low Level programming continues to grow, and fiddling around with a simple Linux Kernel module was just one step on that path.

The aim was simple, setup a development environment, write the module, and see it working. Simply said, but not so simple to implement.

## 1. Setup a Virtualization software

The logic was simple. I am currenly rocking **_Kubuntu-22.04_** and I don't want to mess around with the _kernel_ of my main system. Therefore, I needed a virtualization software to run some Linux OS, and use that for the fiddling around with the custom kernel module.

I've used virtualization softwares like VirtualBox, VMware before, but this time I wanted to try out _QEMU_

### 1.1. Installing QEMU/KVM on Kubuntu 22.04

```bash
# install packages required for qemu & kvm
sudo apt install qemu qemu-kvm libvirt-clients libvirt-daemon-system bridge-utils virtinst libvirt-daemon
# install the gui
sudo apt install virt-manager
```

### 1.2. Setup the _libvert_ daemon

By default, the current user does not have permission to run the virtualization, so we need to add the current user to the `libvirt` group:

```bash
# adding the user
sudo usermod -aG libvirt $(whoami)
# check if the user is added
sudo getent group | grep libvirt
# or, using the below
sudo groups $(whoami)
```

> NOTE: <br> The above steps I performed after reading up online on how to setup the `kvm` but later while modifying the _config_ file, I found out that this feature is enabled by default, and the above step was not needed. <br> However, it is not universal, so the permissions part needs to be tested before performing the rest of the steps. <br> Reboot your system, and then start `libvirtd` <br> That should do the trick.

On top of that, we also need to uncomment out some configuration lines in `/etc/libvirt/libvirtd.conf`

```bash
sudo vim /etc/libvirt/libvirtd.conf
```

Uncomment the following lines:

```bash
unix_sock_group = "libvirt"

unix_sock_ro_perms = "0777"

unix_sock_rw_perms = "0770"
```

> NOTE: <br> There might not be a need to do the above step of altering the default config of libvirtd. <br> It's better to test whether the system works before doing this step: <br> `virsh -c qemu:///system` <br> and if that gives an error, do a system reboot and try again. <br> Only after these steps the error still persists, then alter the config file.

Then, _restart_ + _enable_ the _libvirt_ daemon:

```bash
# check the status of libvirtd
sudo systemctl status libvirtd
# if not running, start the daemon
sudo systemctl start libvirtd
# if already running, restart the daemon
sudo systemctl restart libvirtd
# enable libvirtd to start on boot
sudo systemctl enable libvirtd
```

> NOTE: <br> This much should be enough to run qemu/kvm properly, but if problems do persists, a system restart can resolve most issues.

### 1.3. Setting up an OS inside QEMU/KVM

Since I'm more familiar with Ubuntu & it's variants, and also since I need a lightweight OS to be run on the VM, **_Lubuntu-22.04_** was the apt choice.

While installation using _virt-manager_ there were some configuration needed:

* OS Pool - the _lubuntu_ ISO is inside the `OS/` dir inside the project dir.
* Storage Pool - 20GB storage allocated, and stored inside `/storage`
* Networking - A bridged network is needed. Default bridge network `virbr0` used here.

Also, I don't the VM to keep running in GUI mode and suck up system resources, so a headless run was necessary. To achieve that, _SSH_ is necessary. <br>
To achieve that, I installed the `openssh-server`

```bash
# update & upgrade
sudo apt update && sudo apt upgrade -y
# install openssh-server
sudo apt install openssh-server -y
# restart the firewall
sudo systemctl restart ufw
# check the status of ufw
sudo systemctl status ufw
```

Then, just needed to find the IP of the guest system, ssh into it, and being working. 

```bash
# find the IP of the host
hostname -I
```

## 2. Setup Development environment

### 2.1. Setup git

Setting up git & SSH connection to my Github account. <br>

1. [Generating a new SSH key and adding it to the ssh-agent](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) <br>
2. [Adding a new SSH key to your GitHub account](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)

### 2.2. Stup VIM using my desktop configuration

In my host machine I use Neovim, but here Vim will work. <br>
Setting up Vim the same as my host machine.

### 2.3. Install the development environment

Install the essential development tools & kernel header necessary for this to work.

```bash
sudo apt install build-essential linux-headers-`uname -r`
```

## 3. Playing around with a simple kernel module

All source files are present in the [Github Repository.](https://github.com/biplobmanna/playground-simple-kernel)
### 3.1. First kernel module

Just a small _hello-world_ kernel module. Nothing really fancy, but so much learning here:

* build is done with the kernel build files
* so many different intermediate files are created `.o`, `.ko`, `.mod.o`, etc.. among others
* `Makefile` is a necessity for build
* `obj-m +=` part determines which files get build into `.ko` files
* `test1` directive contains the _bash_ script code to insert, remove to/from the kernel, & view the logs
* By default `printk` does not flush the contents of the buffer into log; used `\n` to flush.
* `dmesg` flushes it's full content into stdout, so it's better to pipe it through `tail`
* insert any module taints the kernel

Small demonstration of `lkm_01` module: <br>
![Linux Kernel Module 01 demonstration]({{ site.url }}/assets/gif/simple-linux-kernel-lkm_01.gif)

### 3.2. Second kernel module

This one goes a little bit deeper. The aim here is to create a device file, and read/write to it. Other applications in userspace can then read from the device file.

Functions used:

```c
// Prototypes for device functions
static int device_open(struct inode *, struct file *);
static int device_release(struct inode *, struct file *);
static ssize_t device_read(struct file *, char *, size_t, loff_t *);
static ssize_t device_write(struct file *, const char *, size_t, loff_t *);
```

* `device_write` second parameter is `const char *`
* device is registered with a `major_num`
* reding from the device will read the message in a loop
* writing to device is not permitted
* `test2` directive contains the _bash_ script code to insert, remove to/from the kernel, & view the logs
* running `make test2` will give the **MAJOR** number, which is required to create the device
* create character device using `sudo mknod /dev/lkm_02 c MAJOR 0`
* grab the content: `dd if=/dev/lkm_02 of=test bs=14 count=10` or using `cat` or any other reader
* remove all traces once done: `make remove`

Small demonstration of `lkm_02` module: <br>
![Linux Kernel Module 02 demonstration]({{ site.url }}/assets/gif/simple-linux-kernel-lkm_02.gif)

## 4. Conclusion

It was a good experience, and although I got stuck multiple times in the middle, even in this small project, I ended up learning way too much. Next time, I will tackle a bigger project.
