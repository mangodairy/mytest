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
toc_icon: "Linux Installation"
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

<div markdown="0"><a href="#" class="btn btn--success">Go back to the Top of the page </a></div>



