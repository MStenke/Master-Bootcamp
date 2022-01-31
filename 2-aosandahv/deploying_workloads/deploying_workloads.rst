.. _deploying_workloads:

###################
Deploying Workloads
###################

Overview
========

In addition to storage, VM creation, management, and monitoring can all be performed for Nutanix AHV directly through Prism.

.. note::

   Prism also offers native support for VM CRUD (create, read, update, delete) operations for Nutanix clusters running ESXi.

In the following exercise, we'll create VMs from source media and existing disk images.

Creating a Windows VM
=====================

You will now create a Windows Server VM from a Windows installation ISO image.

AHV provides an Image Service feature to build a store of imported ISO or disk image files. The Image Service supports raw, vhd, vhdx, vmdk, vdi, iso, and qcow2 disk formats.

.. note::

   You can explore the available images and upload additional images under :fa:`cog` **> Image Configuration** in Prism Element.

To provide high-performance IO to VMs, AHV requires installing paravirtualized drivers into the guest (similar to VMware Tools). For Windows guests specifically, these drivers must be loaded during installation for the VM's disk to be accessible by the Windows installer.

Nutanix validates and distributes these drivers via `http://portal.nutanix.com`. The ISO image containing the drivers has already been uploaded to the Image Service.

#. From the main drop-down, choose **VM**. Select **Table >** :fa:`plus` **Create VM**.

#. Fill out the following fields and click **Save**:

    Leave other settings at their default values.

   - **Name** - *Initials*-Windows
   - **vCPU(s)** - 2
   - **Number of Cores per vCPU** - 1
   - **Memory** - 4 GiB
   - Within the *Disks* section, select :fa:`pencil` next to CD-ROM
       - **Operation** - Clone from Image Service
       - **Image** - Windows2019.ISO
       - Select **Update**

       *This will mount the Windows Server ISO from the Image Service for boot/installation*

   - Select :fa:`plus` **Add New Disk**
       - **Type** - DISK
       - **Operation** - Allocate on Storage Container
       - **Storage Container** - *Initials*-Container
       - **Size (GiB)** - 30 GiB
       - Select **Add**

       *This will create a 30GiB vDisk on the selected Storage Container*

   - Select :fa:`plus` **Add New Disk**
       - **Type** - CD-ROM
       - **Operation** - Clone from Image Service
       - **Image** - Nutanix-VirtIO-1.1.6.iso
       - Select **Add**

   - Select :fa:`plus` **Add New NIC**
       - **VLAN Name** - *Initials*-Network
       - Select **Add**

       *This will add a single virtual NIC to the VM on the selected Virtual Network*

#. Click **Save** to create the VM.

#. Right-click on your VM and then select **Power On**.

   .. figure:: images/vm_power_on.png

#. Right-click on your VM and select **Launch Console** from the list of actions to access the HTML5 console, allowing you to interact with the VM.

#. Progress through the standard install questions until you reach the Windows install location.

   .. note::

      Choose **I don't have a license key**, **Windows Server 2019 Datacenter (Desktop Experience)**, and **Custom** installation when presented with the choice.

#. Click **Load Driver**, and navigate to the CD where the Nutanix VirtIO ISO is mounted.

#. Select the directory that corresponds to the Windows 2019.

   .. figure:: images/deploy_workloads_05.png

#. Select the three Nutanix drivers displayed. Press and hold the *Ctrl* key to select all three drivers: Balloon, Ethernet adapter, and SCSI passthrough controller.

   .. figure:: images/deploy_workloads_06.png

#. Click **Next**.

   After the drivers are loaded, the disk created in step 2 appears as an installation target.

#. Select that disk, and continue with the normal install process.

#. Following Windows installation, install the Nutanix Guest Tools (NGT) by right-clicking and choosing **Manage Guest Tools > Enable Nutanix Guest Tools > Mount Nutanix Guest Tools > Submit**. Select the link to unmount one of the CD-ROM drives when prompted.

   This will use the virtual CD-ROM device to mount the NGT installation ISO to the VM. NGT includes the previously installed VirtIO drivers and services to support *Self-Service File Restore (SSR)* and *Application Consistent (VSS) snapshots*.

    .. figure:: images/deploy_workloads_nutanix_guest_tools.png

#. (Optional) Complete the installation within the VM console.

Creating a Linux VM
===================

You will now create a CentOS VM from an existing, pre-installed disk image in the Image Service. It is common in many environments to have template-style images of pre-installed operating systems. Similar to the previous exercise, the disk image has already been uploaded to the Image Service.

#. From the main drop-down, choose **VM > Table**, and then click :fa:`plus` **Create VM**.

#. Fill out the following fields and click **Save**:

   - **Name** - *Initials*-Linux
   - **vCPU(s)** - 1
   - **Number of Cores per vCPU** - 1
   - **Memory** - 2 GiB

   - Select :fa:`plus` **Add New Disk**
      - **Type** - DISK
      - **Operation** - Clone from Image Service
      - **Image** - CentOS7.ISO
      - Select **Add**

    *This will create a thin clone of the existing CentOS disk image*

   - Select **Add New NIC**
      - **VLAN Name** - *Initials*-Network
      - Select **Add**

#. Click **Save** to create the VM.

#. (Optional) Complete the installation within the VM console.

Takeaways
=========

- In this lab, you saw how simple it is to deploy both a Windows and Linux VM.
- The Image Configuration tool allows you to catalog available images used in VM deployments as needed and cover broad format support that includes qcow, qcow2, vmdk, VHD, VHDx, RAW, and ISO.