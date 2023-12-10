# gen-debvm - Quickly create virtual machine images from scratch for Debian and Ubuntu

Creates Debian and Ubuntu virtual machine images with the specified username, password, root password, and list of preinstalled packages. Uses debootstrap. Designed to run on Ubuntu.

## Usage

    gen-debvm (debian|ubuntu) releaseCodename userName userPassword rootPassword [package1 [package2...]] vmName vmSize

## Example

To make an 16-GiB sized Ubuntu virtual machine with IceWM, X.Org, and startx:

    sudo gen-debvm ubuntu jammy user z x icewm xserver-xorg xinit ubuntu.qcow2 16G

To make a 16-GiB sized Debian virtual machine with LXQt and networking via NetworkManager:

    sudo gen-debvm debian bookworm user z x task-lxqt-desktop network-manager debian.qcow2 16G

## Details

gen-debvm creates BIOS-bootable VM images (NOT UEFI-bootable!). It creates an MBR disk image containing a single ext4-formatted partition, installs a base Debian or Ubuntu system into the image, then chroots in and installs a kernel, a bootloader, and the user-specified list of packages. The bootloader is grub-pc, while the kernel is either linux-image-amd64 on Debian, or linux-generic on Ubuntu. It finishes up with a few configuration steps (writing a basic fstab, creating a user, setting the user and root passwords, installing and configuring the bootloader, and (on Ubuntu only) ensuring that NetworkManager works properly). All Debconf prompts are prevented from showing during software installation

If you need UEFI boot capabilities, a more advanced partition layout, GPT partitioning, debconf prompts, or any other advanced features like that, you can manually create a VM image using qemu-nbd, debootstrap, and apt. See https://arraybolt3.substack.com/p/making-hyper-minimal-ubuntu-virtual for more info.

## Warnings

This script assumes that /dev/nbd0 is unused. It attempts to attach a disk image to this device with qemu-nbd, then partitions and formats it. If you have a disk or disk image attached to /dev/nbd0, **data loss may be possible.** Please be careful.

## Dependencies

You will need the following commands to be available for gen-debvm to work:

* bash
* qemu-img
* qemu-nbd
* mkfs.ext4
* fdisk
* debootstrap

Additionally a working GNU userland is necessary. (BSD userland *might* work but is entirely untested and unsupported. Other userlands are similarly untested and unsupported.)

## License:

GPL-v2+.

This program is free software. You may use it under the terms of the GNU General Public License version 2, or (at your option) any later version. This license applies to all files in this repo.
