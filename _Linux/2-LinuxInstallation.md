---
title: "2 Linux Installation"
excerpt: "Points to be taken care of during installation."
header:
  overlay_color: "#80aaff              "
  excerpt: "Linux Installation"
  show_overlay_excerpt: false
  show_date: true
sidebar:
  - title: "Linux Installation"
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

### Plan for "/home" and "/var".
We will go through few other filesystems like “/home”, “/var”.
> When “/home” partition is into consideration we should understand the purpose of the server.If it is a file server or any other application in which the user is going to place lots of files under the user home directory. Then you need to think of a bigger “/home” and you can choose 100 or 500Gb or more.
{: style="text-align: justify;"}

> If a large-sized filesystem is expected or the filesystem is expected to have high reads/write then it is recommended to keeps the filesystem in SAN or NAS with an appropriate RAID level. The SAN and NAS devices are designed to handle high I/O also filesystem expansion is easy. This helps in faster backup and restoration.
{: style="text-align: justify;"}

> “/var” is a variable filesystem. In any system, there will be lots of log records, therefore, it is good to keep a separate “/var/log” and also keep periodic log rotation. Same way if it is a web server keep  "/var/www/html" ( or its Document root path ) as a separate partition.
{: style="text-align: justify;"}


## Discussion on the need for a separate filesystem.

I know most of you are going to ask if it is not configured like this will it not work?
                    Or
I have a setup that was not configured in this manner still is working perfectly for the last XX years.

I am not saying this will not work or another configuration is wrong, however, this would be one of the recommended ways. Because it will be easy to manage and easy in case of disaster recovery also the chance of system breakdown will be less compared to the other configuration.

<p>
Consider you configured “/var” as a directory under “/ “ filesystem and it has become 100% full because of exponential growth in the system or application logs. In this situation, even the root user itself find it difficult to get into the system to compress the logs. Or you may end up taking the system to single-user mode to recover the system back to normal condition. If it was configured as a separate filesystem, it will not occupy the space under the root filesystem, it would be within "/var/log filesystem", still the root user can easily log in to the system and do the log rotation or copy the log file to some other partition and nullifying the logs.
</p>
{: .notice--warning}
{: style="text-align: justify;"}

**Another important scenario:** The chance of filesystem corruption is high for the filesystems with high I/O. If you keep this partition within the "/" filesystem along with the affected directory,  the root "/" filesystem also will get corrupted. If the root filesystem is corrupted the only option to bring back the system is a fresh installation. If it was a separate filesystem fix the filesystem issue or recreate the filesystem and restore the data from backup which will be much easier than making the server from the scratch.
{: .notice--warning}
{: style="text-align: justify;"}

The same is applicable for all the filesystem. Here I am not going through each filesystem.
{: style="text-align: justify;"}

## Keyboard and terminal selection

Most of the section will not give any issues and the default will be okay.

## Time, time zone daylight saving.

* Time zone, if you are building a server for the Australian region and you selected the US time zone you are almost be going a day behind.
* Daylight saving, in most of the western countries the time depends on the sunlight availability they adjust the clock one hour ahead or behind for few months. So if you are selecting the correct time zone/country etc then the system will automatically adjust the time during the time of daylight saving.

## Package Selection

Some Linux distribution shows “Web server”, “mail-server “, “Samba “ etc at the time of installation or you will get an option to select the packages. 
What is wrong with installing everything?
{: style="text-align: justify;"}

We will list out some of the most common issues.
{: style="text-align: justify;"}

> If you install extra packages it needs more space, there is no use in keeping an NFS server package on a web server if it is not acting as an NFS server other than eating up space from the system hard disk.
{: style="text-align: justify;"}

> If the service is configured in autostart, it takes some memory and CPU, there is no use in running an FTP or NIS service in a web server and consuming its resources. This memory and processing can be utilized for fastening Web service request.
{: style="text-align: justify;"}

> If the server is configured as a web server we will be doing the security and fine-tuning required for HTTP and port level security blocking etc for a web server. If FTP service is unnecessarily running on a web server and if it has any vulnerabilities then the attack will come through FTP, why unnecessarily exposing our servers to attack by keeping unwanted services?
{: style="text-align: justify;"}

> Some of the services will create some service account which an attacker may be well aware of; they will try to penetrate the server through that service account, even if there is no successful login the server needs to respond to each request.
{: style="text-align: justify;"}


**Once all the above steps are completed, it will ask for the root password and grub password at the time of installation. Then it will take some time to copy the package and configure the system and it will go for a reboot.**

If you clearly understand the above steps you can go for any Linux installation irrespective of the distribution and version. Even the different types of installation like Kickstart and ask method needs a clear understanding of the above concept.
{: .notice--success}
{: style="text-align: justify;"}

If you still require a screenshot of the installation I will provide few links.

* [To see the ubuntu desktop installation step, click here](https://ubuntu.com/tutorials/install-ubuntu-desktop#1-overview)

* [To see the step to Create a bootable USB stick on Ubuntu](https://ubuntu.com/tutorials/create-a-usb-stick-on-ubuntu#1-overview)

* [To See the step to Create a Create a bootable USB stick on Windows](https://ubuntu.com/tutorials/create-a-usb-stick-on-windows#1-overview)

* [Installation Guide CentOS](https://docs.centos.org/en-US/centos/install-guide/Simple_Installation/)

* [RHEL 8 Bare Metal Quick Install](https://developers.redhat.com/rhel8/install-rhel8#installation)
{: style="text-align: justify;"}
<p>
But I strongly recommend you to be clear in the above concept which will make a difference in your administration or at least you will know why a server is configured like......... in your environment? Or you can suggest a better configuration and layout.
</p>
{: .notice--info}
{: style="text-align: justify;"}

So we will wind up here and will cover more on the Security and hardening series.

<div markdown="0"><a href="#" class="btn btn--success">Go back to the Top of the page </a></div>



