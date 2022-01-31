.. _managing_workloads:

##################
Managing Workloads
##################

Overview
========

Now that you have a couple of VMs deployed let’s have some fun and explore some of the VM management tasks with AHV - power actions, searching, cloning, and migrating.

Workload Management
===================

Power Actions and Console Access
--------------------------------

Explore VM power actions and console access.

#. Choose **VM > Table** from the main drop-down and use the search bar to locate the Linux VM you created in the previous exercise (e.g., *Initials*-Linux).

   Note that the Power State column for that VM shows a red dot, indicating that the VM is powered off.

#. Right-click on the VM and choose **Power On**. Wait a few moments. then right-click on the VM once more and select **Launch Console**.

   The console window provides 4 actions: Mount ISO, Send CTRLALTDEL, Take screenshot, and Power.

   .. figure:: images/manage_workloads_01.png

Cloning VMs
-----------

#. Right-click on your *Initials*-Linux VM, and choose **Clone**.

#. Fill out the following fields, and then click **Save**:

   Leave other settings at their default values.

   - **Number of Clones** - 2
   - **Prefix Name**  - *Initials*-Linux-Clone
   - **Starting Index Number** - 1

   .. figure:: images/manage_workloads_02.png

   Both Nutanix snapshots and clones use a `redirect-on-write <https://nutanixbible.com/#anchor-book-of-acropolis-snapshots-and-clones>`_ algorithm to quickly and efficiently create copies of VMs as a metadata operation.

Migrating a VM Between Hosts
----------------------------

VM live migration is a critical feature for any virtualized environment, allowing VMs to move seamlessly across hosts within a cluster to enable infrastructure maintenance or performance balancing.

#. Right-click on your *Initials*-Linux_VM, and choose **Migrate**.

   You can either choose one of the other hosts in the cluster as a migration target for the VM or accept the default and let AHV automatically select a location.

#. Click **Migrate** to finalize the action.

   When the task completes, verify that your VM host location has changed from the host recorded above to the new location you selected.

   .. figure:: images/manage_workloads_03.png

Configuring Affinity Policies
-----------------------------

VM-to-Host affinity rules are commonly used to configure VMs to only run on specific hosts. Affinity is typically done either for performance or licensing reasons. AHV can also create VM-to-VM anti-affinity rules, commonly used for highly available applications. You need to ensure multiple instances of an application do not run on the same node.

#. Select one of your *Initials*-Linux clones from the previous exercises, right-click and choose **Update** :fa:`plus` **Set Affinity**.

#. Select any two hosts to which the VM can have an affinity, and click **Save > Save**.

   .. figure:: images/manage_workloads_04.png

   .. note:: We select more than one host, so the VM has a place to migrate to in the event of a node failure.

#. Power on the VM and verify it is running on one of the specified affinity hosts you selected within the affinity policy.

#. Right-click on the VM, and then click **Migrate**.

   .. figure:: images/manage_workloads_05.png

#. Click **Migrate**.

   You should see that the VM has moved to the other host you selected within the affinity policy.

   .. figure:: images/manage_workloads_06.png

High Availability & Dynamic Scheduling
--------------------------------------

High availability is enabled by default for AHV and will restart VMs in a best-effort manner in the event of a host failure. Additional configuration can set resource reservations to ensure there is available capacity during an HA event.

With the *Acropolis Dynamic Scheduler* service, AHV performs both intelligent initial placement of VMs, and also dynamically migrates VMs to other hosts within the cluster to optimize workload performance. ADS runs by default, and without additional configuration.

A benefit of a Nutanix AHV solution is making VM placement decisions not based solely on CPU/memory congestion avoidance but also on storage performance.

See `here <https://nutanixbible.com/#anchor-book-of-acropolis-dynamic-scheduler>`_ for additional details about the *Acropolis Dynamic Scheduler*.

Takeaways
=========

In this lab, you experienced how AHV provides a complete set of tools and actions that can be performed to manage VMs in the cluster.