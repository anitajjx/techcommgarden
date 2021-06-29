---
title: "VirtualBox: Solve the issue of \"low disk space on Filesystem root\""
categories: VirtualBox
permalink: virtualbox-resize-root-disk.html
tags: [VirtualBox]
---

"Low Disk Space on Filesystem root" - this message suddenly popped out when logged on my virtual machine (VM) after used it without incident for some days. The VM is RedHat running in VirtualBox 6.1 on Windows 10 host, with 12g maximum disk space assigned. Apparently, 12g is too small that triggered the message when space is going to run out. I need to enlarge disk space and resize the root disk. After hours of research and trial with different solutions, finally solved it. This post records the steps how to fix the issue.

## Problem

{% include image.html file="blog_imgs/msg_low_disks_pace.png" caption="Low Disk Space message" %}

## Cause

The VM was allocated with a fixed disk size which is going to run out.

## Solution

The basic idea is to clone the existing VM with a new virtual disk, then recreate the LVM partition and resize the physical volume (PV) and root volume. Please see the step-by-step guide as follows.

### Tasks on host

It is always wise to back up the VM in case something goes wrong, so I created a new virtual disk with 20g size, then clone the old VM to the new disk. Please see the commands below:

1. Create a new virtual disk

   ```batch
   c:\Program Files\Oracle\VirtualBox>VBoxManage.exe createmedium disk --filename "C:\Users\axiong\VirtualBox VMs\tw_workshop20g" --size 20480
   ```

2. Clone the old VM

   ```batch
   c:\Program Files\Oracle\VirtualBox>vboxmanage clonemedium disk "C:\Users\axiong\VirtualBox VMs\tw_workshop\tw_workshop-disk002.vdi" "C:\Users\axiong\VirtualBox VMs\tw_workshop20g.vdi" --existing
   ```

3. Import the new VDI into VirtualBox

    3a. Start VirtualBox and create a new virtual machine

    {% include image.html file="blog_imgs/vm_create_new_vm.png" caption="Create new vm" %}

    3b. When asked for a hard disk image, select **Use an existing virtual hard disk file** and click on the small icon on the right:

    {% include image.html file="blog_imgs/vm_select_vdi_file.png" caption="Select vdi file" %}

    3c. On the opened window, click on **Add** and select the VDI file from step 1

    3d. Click **Next** and finish the creation process

4. Back to VM manager, and start the new virtual machine

### Tasks on VM

1. Check the disk size

   {% include image.html file="blog_imgs/vm_fdisk_before.png" caption="Check disk space before partition" %}

   The disk size has been changed from 12g to 20g, but the root directory (/dev/mapper/ol-root) size remains the same, this means we need to create new partion, create physical volumn, and extend logical volumn etc for the new virtaul disk.

2. Delete and recreate the LVM partition (the data will be remain unchanged)

   ```shell
      # fdisk /dev/sda
      Command (m for help): m
      Command (m for help): p    #print the partition table

      Command (m for help): d    #delete a partition
      Partition number (1,2, default 2): 2

      Command (m for help): n    #add a new partition
      Partition type:
         p   primary (1 primary, 0 extended, 3 free)
         e   extended
      Select (default p): p
      Partition number (2-4, default 2): 2

      Command (m for help): p
      Command (m for help): t    #change a partition's system id
      Partition number (1,2, default 2): 2
      Hex code (type L to list all codes): L
      Hex code (type L to list all codes): 8e
      Changed type of partition 'Linux' to 'Linux LVM'

      Command (m for help): w
      The partition table has been altered!
   ```

3. Reboot the VM

4. Resize the physical volume

   ```shell
      # pvdisplay
      # pvresize /dev/sda2
      # pvdisplay
   ```

5. Resize the root volume

   ```shell
      # lvdisplay
      # lvextend -l +100%FREE  /dev/ol/root
      # lvdisplay
   ```

6. Resize the file system

    6a. As Oracle Linux 8 is using **xfs** as default file system, so we need to install xfsprogs to resize the file system by running `yum install xfsprogs`

    6b. Then run `xfs_growfs /dev/mapper/ol-root`
      {% include image.html file="blog_imgs/vm_resize_fs.png" caption="Resize file system" %}

{% include links.html %}
