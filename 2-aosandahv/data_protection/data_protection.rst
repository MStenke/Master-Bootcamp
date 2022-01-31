.. _data_protection:

###############
Data Protection
###############

Overview
========

Nutanix provides the ability to perform VM/vDisk-level storage snapshots. Protection Domains (PDs) are the construct for grouping VMs and applying snapshot and replication policies.

In this section, you will use Prism to create and restore from VM snapshots and create a Protection Domain for your VMs.

Data Protection
===============

VM Snapshots
------------

#. Right-click on *Initials*-Linux-Clone1, and choose **Take Snapshot**.

#. Within the *Name* field, enter *Initials*-Snapshot, and then click **Submit**.

#. Select the **VM Snapshots** tab below the table to view the available snapshots for the selected VM.

   .. figure:: images/data_protection_01.png

#. Under the *Actions* column, click **Details** to see all of the VM’s properties at the time of the snapshot.

   You can see the snapshot contains the VM's state, in addition to just its data.

   *Now it's time to break your VM!*

#. Right-click your VM, and choose **Update**. Remove the disk by clicking the **X** icon, and then click **Save**.

#. Attempt to power on the VM, and launch its console window.

   The VM no longer has any disks from which to boot, so the 2048 game is displayed.

#. Right-click your VM, and choose **Power Off Actions > Power off > Submit**.

#. Select your snapshot under **VM Snapshots** and click **Restore > Yes** to revert the VM to a functioning state.

   Alternatively, you can click **Clone** instead of **Restore** to create a new VM from the snapshot.

#. Verify that the VM boots successfully.

As previously mentioned, Nutanix snapshots use a `redirect-on-write <https://nutanixbible.com/#anchor-book-of-acropolis-snapshots-and-clones>`_ approach that does not suffer from the performance degradation of chained snapshots found in other hypervisors.

Protection Domains
------------------

#. From the main drop-down, choose **Data Protection > Table**, and then click :fa:`plus` **Protection Domain > Async DR**.

#. Within the *Name* field, enter *Initials*-PD, and then click **Create**.

#. Filter or scroll to select the VMs you've created during this bootcamp.

#. Click **Protect Selected Entities**, and verify the VMs appear under *Protected Entities* (right column).

   .. figure:: images/data_protection_02.png

   Consistency groups allow you to group multiple VMs to have their snapshots simultaneously (ex. multiple VMs belonging to the same application).

   .. note::
      
      Nutanix snapshots can perform application consistent snapshots for supported operating systems with NGT installed. Each VM using application consistent snapshots will be part of its consistency group.

#. Click **Next**.

#. Click **New Schedule** to define Recovery Point Objective (RPO) and retention.

#. Select **Repeat every 1 hour(s)**.

   .. note::

      AHV supports NearSync snapshots, with RPOs as low as 1 minute.

      Multiple schedules can be applied to the same PD, allowing you to take and retain X number of hourly, daily, monthly snapshots.

#. Within the *Retention policy* section, enter 5 (i.e., keep the last five snapshots).

   .. note::

      For environments with remote cluster(s) configured, setting up replication is as easy as defining how many snapshots to keep at each remote site.

   .. figure:: images/data_protection_03.png

#. Set the *Start on* time to **11:00 PM**.

#. Click **Create Schedule**.

#. Click **Close** to exit.

Additional information can be found `here <https://nutanixbible.com/#anchor-book-of-acropolis-backup-and-disaster-recovery>`_.

That's it! You've successfully configured native data protection in Prism.

Takeaways
=========

- Nutanix offers data protection solutions for virtual datacenters via different strategies, including one-to-one or one-to-many replication.
- Nutanix provides data protection functions at the VM, file, and volume group level, so VMs and data remain safe in a crash-consistent environment.
- VM-level snapshot and replication policies can be managed through Prism for any supported hypervisor.