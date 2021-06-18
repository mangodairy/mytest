---
title: "Linux Installation"
header:
  overlay_color: "#80aaff              "
  teaser: /assets/images/kuberneties/IntroductiontoKubernetes.png
  excerpt: "Linux Installation?"
  show_date: true
sidebar:
  - title: "Linux Installation"
    image: /assets/images/kuberneties/KubernetesSidebar.png
    image_alt: "logo"
    text: "Written by"
  - title: "Rajith P"
    text: "Linux Installation"

tags:
  - table of contents
toc: true
toc_label: " "
toc_sticky: true
---

## Installation
If you are clear with the structure of the computer ( Both OS and hardware ) then the installation is a simple process;  even if you are using a new version of Linux. Else each distribution and version to version will look different to you.
{: style="text-align: justify;"}

## Different types of Linux installation

* Installation through bootable media
* Kickstart, unattended installation.
* Linux asks method.

Each installation methods have a graphical and text mode.

All these methods and options make us confused. Before we go depth into installation clearly understand the concept behind it.

Most of the new commerce prefer to install it from OS CD. So will go through the steps involved in it. Nowadays instead of using CD we make bootable Pendrive or if it a virtual machine we will make use of the ISO image.Still the concept remains the same.
{: .notice}
{: style="text-align: justify;"}


### Installing the OS with bootable media ( CD or DVD).

**Note:**  Considering you are using a brand new machine or there is no data in your existing system. If this is not the case take the backup of the data before starting the installation. There are options to install Linux by keeping the data untouched however, it is difficult to understand a person who is very new to Linux.
{: .notice--warning}
{: style="text-align: justify;"}

Hardware setup, Make sure your BIOS selection is made to boot the system from CD/DVD. Depends on the BIOS manufacturer the option to enter into BIOS may change, the most common method are pressing “DEL” or F2 key, during the time of system boot. It displays the key to being pressed to enter into the BIOS screen.
{: style="text-align: justify;"}
 ----------------BIOS screenshots-------------------
 
* Insert the bootable installation media into the drive and start the system. 
* Once the POST(Hardware verification processes )is completed, its search for the bootable media and boot from the media. 
* Till this almost all X86 based OS to boot the same order, now start the Linux part of it.
* Linux installation, setting up the OS.
* Irrespective of the distribution and installation methods, our part is,
   * Deciding the hard disk partition,
   * selecting the filesystem and its size.
   * Keyboard/ Terminal selection.
   * Setting the Date and time, enabling or disabling Daylight saving.
   * Time zone selection.
   * Providing the administrative user( root ) password and optionally an additional user.
   * Providing grub password or skipping it.
   * Package or package group selection.
{: style="text-align: justify;"}

<p>
These are the most common installation task for an admin.

Linux OS installation is clicking next → Next → again next ......... and finish :); as simple as that of a windows desktop installation. 

All the latest Linux distributions provide these options. Anyone can do this; no administrative knowledge is needed. 
</p>
{: .notice--success}

**But are we here to do that?**

## Enterprise-level Server Installation and Configuration.

Instead of  placing some screenshots of installation I prefer discussing,
 
   * What is important in each step, and why it is required? 
   * What is the significance of it during the production run?
    
This part may be boring for you but try to understand it. This helps you in each stage of your admin career from L0, L1, L2, L3, SME or Architect or even if your path is to become a manager, you will not become a dump manager just shaking your head for all your admin's comments or you can avoid foolish questions by understanding these concepts.
{: style="text-align: justify;"} 

## Hard-disk partitioning and filesystem selection

### Plan for "/boot" partition.

> Under /boot we have only **grub, kernel and "initramfs"** so we don't need much space over here. Till RHEL 5 it is recommended to have 100Mb size, for RHEL 6 200Mb or maximum 500Mb, even if you have 1TB hard disk there is no need of giving huge  “/boot” partition.

<p>
Under “/boot” we are not going to keep any data or software packages, the only changes happening here is at the time of kernel install or update. By default, if you are using yum utility it keeps only 3 recent kernels and the corresponding  "initramfs". each may come up to 20 to 25Mb so you need a maximum size of 80Mb.


The old kernel will be used only if there is an issue in the application or if the OS becomes unstable after the patching. In that case, we will be reverting the kernel to the previous version. In these cases, we need only the previous kernel. Therefore no need of increasing the size of this partition beyond a limit.
</p>
{: .notice--info}
{: style="text-align: justify;"}

<div markdown="0"><a href="#" class="btn btn--success">Go back to the Top of the page </a></div>



