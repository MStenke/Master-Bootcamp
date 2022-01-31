.. _storage_configuration:

#####################
Storage Configuration
#####################

Overview
========

In this section, we will use Prism to perform a basic container setup.

Distributed Storage Fabric (DSF)
================================

The Nutanix Distributed Storage Fabric (DSF) presents storage to the hypervisor in a way that looks identical as if it were presented from any centralized storage array. However, Nutanix uses Controller VMs (CVMs) combined with the local storage within each node to provide shared storage for the cluster that is both redundant and resilient. The combination of computing and distributed local storage resources is now commonly referred to as Hyper-converged Infrastructure (HCI).

   .. figure:: images/dsf_overview.png

As a pioneer in the HCI space, Nutanix DSF is a mature solution capable of delivering the performance and resiliency needed to support `many different workloads <https://www.nutanix.com/solutions/>`_, including enterprise databases, virtual desktops, Remote Office - Branch Office (ROBO), Big Data, and more.

The two main storage constructs within the DSF are the *Storage Pool* and *Storage Containers*.

The *Storage Pool* is the aggregation of all of the physical disks within a given Nutanix cluster. The cluster manages data distribution, so other storage pools (like LUNs in a traditional storage environment) are not required. As new nodes are added to a cluster, disks are automatically added to the pool, and the cluster will begin `re-distributing data to the new disks <https://nutanixbible.com/#anchor-book-of-acropolis-disk-balancing>`_ as a background task.

The *Storage Containers* are software-defined, logical constructs that allow you to configure storage policy for groups of VMs or vDisks. In the next exercise, you will walk through the process for creating and configuring Nutanix storage within Prism.

.. note::

   To learn more about additional DSF constructs such as vDisks, extents, and extent groups, refer to `this section <https://nutanixbible.com/#anchor-book-of-acropolis-distributed-storage-fabric>`_ of the Nutanix Bible.

In this lab, we will use Prism Elements to perform a basic storage container setup.

Prism Element Storage Configuration Items
=========================================

Configure Storage Containers
----------------------------

Let's use PE to perform a basic container setup.

#. From the main drop-down, choose **Storage**. Select **Table** :fa:`plus` **Storage Container**.

#. Use the following specifications, and then click **Save**.

   - **Name** - *Initials*-Container
   - Select **Advanced Settings**
   - **Advertised Capacity** - 500 GiB
   - Select **Compression**
   - **Delay (In Minutes)** - 0

   .. figure:: images/storage_config_01.png

   The storage container is now instantly available across all nodes within the cluster.

   You can create multiple containers with different policies, all sharing capacity from the **Storage Pool**. For example, you may want to enable `deduplication <https://nutanixbible.com/#anchor-book-of-acropolis-elastic-dedupe-engine>`_ for a storage container used for full clone persistent virtual desktops. However, deduplication wouldn't make sense for workloads such as databases. Similarly, you may want to create a storage container with `erasure coding <https://nutanixbible.com/#anchor-book-of-acropolis-erasure-coding>`_ enabled for archival data, such as backups or security footage.

#. Explore the configuration basics further by updating your Container configuration. How would you ensure capacity availability for critical VMs on a cluster running mixed workloads?

#. Try selecting different storage containers on the cluster and reviewing the *Storage Container Details* below.

   .. figure:: images/storage_config_02.png

   This view provides a breakdown of the savings from each available data reduction option and the *Effective Usable Capacity* of the container. Hover your mouse over any link for further details. The *Data Reduction Ratio* is the data efficiency when accounting for compression, deduplication, and erasure coding. The *Overall Efficiency* number tracks data reduction and native data avoidance, specific savings from thin provisioning and cloning.

Redundancy Factor (RF)
----------------------

The Nutanix platform currently uses a redundancy factor, also known as a replication factor (RF), and checksum to ensure data redundancy and availability in the case of a node or disk failure or corruption. RF sets the number of data copies to maintain (2 or 3). A redundancy factor of 3 adds an extra layer of data protection at the cost of storage an additional data copy.

Interested in learning about how RF writes and reads work? Check out the video below.

   .. raw:: html

      <iframe width="640" height="360" src="https://www.youtube.com/embed/OWhdo81yTpk" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

RF policies are applied on a per-container basis.

Nutanix clusters can also enforce `availability domain policies <https://nutanixbible.com/#anchor-book-of-acropolis-availability-domains>`_ at the Block or Rack level.

Block Awareness ensures that secondary copies of data are not written to a node within the same physical enclosure as the primary copy. Block Awareness allows for the loss of a multi-node block without experiencing data unavailability. The same concept can be applied using a Nutanix cluster spanning multiple racks.

The basic requirement for rack/block fault tolerance is to have a minimum of 3 blocks in the cluster (for RF2) as we need to store three copies of metadata. Starting in AOS 5.8, rack and block awareness can be supported with erasure coding enabled.

#. Within the *Home* screen, click **OK** within the *Data Resiliency Status* box.

   .. figure:: images/storage_config_03.png

   Data Resiliency Status indicates how many failures can be tolerated without impacting the cluster. Each service listed has a specific function in the cluster. For example, Zookeeper nodes maintain configuration data (service states, IPs, host information, etc.) for the cluster.

#. The RF of a cluster in PE can be configured by clicking **Redundancy State** in the :fa:`cog` menu. An RF2 cluster can be upgraded to support RF3 (which requires a minimum of 5 nodes).

Takeaways
=========

- The Distributed Storage Fabric provides RF2 or RF3 shared storage to the cluster.

- Storage Containers allow you to define storage policy for VMs, including RF level, compression, deduplication, and erasure coding.