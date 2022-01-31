.. _dayinthelife1:

##############################################
Configuring Storage & Networking, Creating VMs
##############################################

In this lab, you will follow a day in the life of Kara O’Kay, a ten-year veteran of administrating virtual environments on 3-tier architecture, who has recently deployed her first Nutanix cluster. The Nutanix cluster is being used for a mix of production IT workloads, including its primary application, an inventory management solution called Fiesta, which is vital to their retail storefronts.

.. note::

   If multiple people utilize the same Nutanix cluster to complete this lab, specific steps may have already been completed. If this occurs, skip that particular step, and continue with the lab after you’ve verified the action was completed correctly.

Configuring Storage
===================

In this exercise, you will experience how to provision and monitor primary storage for virtualized environments through Prism - in just a few clicks. In stark contrast to what is typically required regarding time investment and specialized skills to planning and managing traditional SAN storage.

#. In a browser, type in the IP address for *Prism Element*, and log in.

   - **Username** - admin
   - **Password** - <HPOC-PASSWORD>

   .. figure:: images/1.png

#. Select **Storage** from the drop-down menu.

   .. figure:: images/2.png

#. Mouse over the *Storage Summary* and *Capacity Optimization* widgets to see an explanation of what each is displaying.

   It’s important to understand that Nutanix considers the *Data Reduction* ratio as savings only from compression, deduplication, and erasure coding. The *Overall Efficiency*, comparable to what other vendors believe “Data Reduction” is, incorporates the data efficiency and avoidance features like thin provisioning, intelligent cloning, and zero suppression.

   .. figure:: images/3.png

   .. note::

      Within the *Storage Summary* widget, you will notice a gear icon and a drop-down that displays *Logical*. The gear icon allows you to set a custom warning threshold for resilient capacity, and above which, the widget will turn amber. The drop-down will enable you to display either the *Logical* (replication factor included) or *Physical* (raw disk capacity).

#. Click :fa:`plus` **Storage Container**.

   Storage Containers represent logical policies for storage, allowing you to create reservations, enable/disable data efficiency features like compression, deduplication, and erasure coding, and configure the Redundancy Factor (RF). Every Storage Container on a Nutanix cluster still leverages all physical disks, referred to as the Storage Pool. A typical Nutanix cluster will have a small number of Storage Containers, typically corresponding to workloads that benefit from different data efficiency technologies.

   Simply put, think of Containers as how we assign settings to VMs.

   .. figure:: images/4.png

#. Provide *Initials*\-Storage-Container as the name for the Storage Container, and then click :fa:`gear` **Advanced Settings** to explore additional configuration options.

   .. figure:: images/5.png

   Nutanix provides different ways to optimize storage capacity that are intelligent and adaptive to workload's characteristics. Nutanix uses native data avoidance (thin provisioning, intelligent cloning, and zero suppression) and data reduction (compression, deduplication, and erasure coding). All data reduction optimizations are performed at the container level, so different containers can use different settings.

   **Compression**

      Nutanix provides two choices - inline or post-process data compression. Irrespective of inline or post-process compression, write data coming into OpLog that is >4k and shows good compression will be written compressed in OpLog. For inline compression (Delay=0), sequential streams of data or large size I/Os (>64K) will be compressed when writing to the Extent Store. For post-process (Delay > 0), data is compressed after being drained from OpLog to the Extent Store after compression delay is met.

      Compression provides on-disk space savings for databases applications, resulting in fewer writes being written to storage. Post-process compression is turned on by default on all containers. Starting with AOS 5.18, inline compression will be turned on by default on all containers. We recommend turning on inline compression for almost all use cases. Workloads not ideal for compression are encrypted datasets or already compressed datasets.

   **Erasure Coding**

      To provide a balance between availability and the amount of storage required, the Nutanix Distributed Storage Fabric (DSF) provides the ability to encode data using erasure codes (EC). Similar to RAID (levels 4, 5, 6, etc.), where parity is calculated, EC encodes a strip of data blocks across different nodes and calculates parity. The parity data is used to calculate any missing data blocks (decoding) in a host or disk failure. In the case of DSF, the data block must be on a different node and belong to a different vDisk. EC is always a post-process operation, only performed on write-cold data (i.e., data that hasn't been overwritten in more than seven days). The system automatically chooses the number of data and parity blocks in a strip based on the number of nodes and configured failures to tolerate.

      Turn on EC for non-mission-critical workloads and workloads with a significant amount of write-cold data since erasure coding works on write-cold data to provide more usable storage. For more information, refer to `application-specific best practice guides <HTTPS://portal.nutanix.com/page/documents/solutions/list/>`_.

   **Deduplication**

      Capacity-tier and performance-tier deduplication are both options. Data is fingerprinted on ingesting using an SHA-1 hash, which is then stored as metadata. A background process removes the duplicates when duplicate information is detected (multiple copies with the same fingerprint). When deduplicated data is read, it is placed in a unified cache, and any subsequent requests for data with the same fingerprint are satisfied directly from the cache.

      Deduplication is recommended for full virtual desktop clones, physical to virtual (P2V) migrations, and persistent virtual desktops.

   **Redundancy Factor**

      Redundancy Factor controls the number of data copies. Observe that the Redundancy Factor cannot be configured for this particular cluster due to this cluster having four nodes, and the minimum number of nodes required to support RF3 is 5.

   .. note::

      For more information on how Nutanix protects your data or implements data reduction, click the diagram below to review the relevant section of the Nutanix Bible.

      .. figure:: HTTPS://nutanixbible.com/imagesv2/data_protection.png
         :target: HTTPS://nutanixbible.com/#anchor-book-of-acropolis-data-protection
         :alt: Nutanix Bible - Data Protection

#. Click **Save** to create the container, which will also mount it to all available hosts within the cluster.

   In a vSphere or Hyper-V environment, creating the Storage Container will also automate mounting the storage to the hypervisor.

#. Select your *Initials*\ **-Storage-Container**. Review the individual savings from different data reduction/avoidance features and the Effective Capacity, a projection of available storage based on the overall efficiency. These values are found in the Storage Container Details table.

   Unfortunately, it is not possible to quickly test the data resiliency capabilities of the cluster in a shared environment. Still, the short video below will walk you through the experience from Prism when a node in the cluster is unexpectedly unavailable.

   .. raw:: html

     <center><iframe width="640" height="360" src="HTTPS://www.youtube.com/embed/hA4l1UHZO2w?rel=0&amp;showinfo=0" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></center>

Provisioning A New Network
==========================

In this exercise, Kara will use Prism to configure a new VM Network for the cluster.

AHV leverages Open vSwitch (OVS) for all VM networking. OVS is an open-source software switch implemented in the Linux kernel and designed to work in a multiserver virtualization environment. Each AHV server maintains an OVS instance, and all OVS instances combine to form a single logical switch. Each node is typically uplinked to a physical switch port trunked/tagged to multiple VLANs, exposed as virtual networks.

#. Select **VM** from the drop-down menu, and then click on **Network Config**.

   .. figure:: images/9.png

#. Click :fa:`plus` **Create Network**, and then fill out the following fields, using the details from your cluster assignment:

   - **Name** - *Initials*\ **-Network_IPAM**
   - **VLAN ID** - A value (< 4096) other than your *Primary* or *Secondary* network VLANs
   - Select **Enable IP Address Management**
   - **Network IP Address / Prefix Length** - 10.0.0.0/24
   - **Gateway** - 10.0.0.1
   - Deselect **Configure Domain Settings**
   - Select :fa:`plus` **Create Pool**
      - **Start Address** - 10.0.0.100
      - **End Address** - 10.0.0.150
      - Click **Submit**

   .. figure:: images/9a.png

   .. figure:: images/9b.png

   Note that AHV can provide integrated DHCP services (i.e., IP Address Management - IPAM), allowing virtualization administrators to allocate IPs to VMs from a configured pool or easily specifying IPs as DHCP reservations when adding virtual NICs to VMs.

#. Click **Save**.

   The configured virtual network will now be available across all nodes within the cluster. Virtual networks in AHV behave like Distributed Virtual Switches in ESXi, meaning you do not need to configure the same settings on each host within the cluster. Instead, you create this network, and it exists on every AHV host.

#. Close the *Network Configuration* window.

Responding to VM Creation Requests
==================================

Virtualization administrators are commonly tasked with the deployment of new VMs. In this exercise, Kara walks through the deployment of two VMs in Prism as a Nutanix administrator.

#. Click :fa:`plus` **Create VM**.

   .. figure:: images/10.png

#. Fill out the following fields to complete the user VM request:

   - **Name** - *Initials*\ **-WinToolsVM**
   - **Description** - Manually deployed Windows Tools VM
   - **vCPU(s)** - 2
   - **Number of Cores per vCPU** - 1
   - **Memory** - 4 GiB

   - Select :fa:`plus` **Add New Disk**
      - **Type** - DISK
      - **Operation** - Clone from Image Service
      - **Image** - WinToolsVM-Q1CY21.qcow2
      - Select **Add**

   - Select :fa:`plus` **Add New NIC**
      - **VLAN Name** - Secondary
      - Select **Add**

   Nutanix AHV provides an *Image Service* feature that allows you to build a store of imported files that you can use to mount a CD-ROM device from an ISO image or an operating system disk from a disk image when creating a VM. The Image Service supports raw, vhd, vhdx, vmdk, vdi, iso, and qcow2 disk formats.

   The VM creation wizard also provides the ability to specify an *Unattend.xml* file for Windows Sysprep automation or a *cloud-init* file for Linux OS configuration.

#. Click **Save** to create the Windows Tools VM.

#. Click :fa:`plus` **Create VM**.

#. Fill out the following fields to complete the user VM request:

   - **Name** - *Initials*\ **-LinuxToolsVM**
   - **Description** - Manually deployed Linux Tools VM
   - **vCPU(s)** - 2
   - **Number of Cores per vCPU** - 1
   - **Memory** - 4 GiB

   - Select :fa:`plus` **Add New Disk**
      - **Type** - DISK
      - **Operation** - Clone from Image Service
      - **Image** - CentOS7.qcow2
      - Select **Add**

   - Select **Add New NIC**
      - **VLAN Name** - Secondary
      - Select **Add**

#. Click **Save** to create the Linux Tools VM.

   .. note::

      Many VM operations, including VM creation, can be scripted using the AHV CLI, ``acli``. The ACLI Reference Guide can be found `here <HTTPS://portal.nutanix.com/page/documents/details?targetId=Command-Ref-AOS-v5_20:Command-Ref-AOS-v5_20>`_.

#. Click **Table** to change views. Then, using the search field at the top of the table, enter *Initials*\ -WinToolsVM. Right-click the VM, and then select **Power On** from the pop-up menu. Perform the same steps for the *Initials*\ -LinuxToolsVM.

#. Once the VM has completed booting, note its IP address.

   .. figure:: images/11.png

   In the past, Kara has experienced issues with newly created VM networks not working as expected and has had to engage in lengthy troubleshooting sessions with her network administrator counterpart to identify the source of the issue. This situation required additional unplanned time, taking them both away from other projects and adding a level of frustration and uncertainty. With AHV, Kara can easily visualize the complete network path of the virtual machine that she's provisioned and can complete many tasks without attention from the network administrator.

#. Select **Network** from the drop-down menu, and then **Group by VM Type**. This diagram illustrates the path to and from the VM, including the host ports traversing and the network switch.

   .. figure:: images/13.png