.. _technology_overview:

###################
Technology Overview
###################

Overview
========

In this section, we will introduce the Prism Element (PE) user interface (UI), familiarize you with its layout, and navigation.

Prism Element
=============

The Prism service provides the web UI for managing Nutanix clusters, and runs on every Controller VM (CVM), ensuring there isn't a single point of failure. This local Prism service, referred to Prism Element, can be accessed via the IP of any individual CVM, or via the virtual IP for the cluster, which will redirect to the current Prism leader.

From the Firefox web browser (preferred), log into the Nutanix Prism GUI using the Cluster IP.

#. Open `https://<NUTANIX-CLUSTER-IP>:9440` in a new browser tab.

#. Log in using the following credentials:

   - **Username** - admin
   - **Password** - `<HPOC-PASSWORD>`

   .. figure:: images/nutanix_tech_overview_01.png

#. After you log in to PE, familiarize yourself with the UI. Explore the information on the Home screen, as well as the other screens.

#. Review the Home screen, and identify the following items:

   - Hypervisor
   - Version
   - Hardware Model
   - Health
   - VM Summary
   - Warning Alerts
   - Data Resiliency Status

   .. figure:: images/nutanix_tech_overview_02.png

#. Review the UI navigation options under the main drop-down menu. The label of this menu changes based on the dashboard that is currently being displayed. For example, from the Home dashboard it will be labeled *Home*, from the Health dashboard it will be labeled *Health*, etc.

   .. figure:: images/nutanix_tech_overview_03.png

#. Examine the cluster hardware. From the main drop-down, click **Hardware**, and then **Diagram**.

#. Review the hardware summary information:

   - Blocks
   - Hosts
   - Memory
   - CPU
   - Disks

   .. figure:: images/nutanix_tech_overview_04.png

#. Review the other selections from the main drop-down, and do a quick walk through:

   - VM
   - Health
   - Network
   - Data Protection
   - Storage
   - Alerts
   - Etc.

#. Review other selections on the top navigation bar:

   - Health :fa:`heartbeat`
   - Alarms :fa:`bell`
   - Tasks :fa:`circle-o`
   - Search :fa:`search`
   - Help :fa:`question`
   - Configuration :fa:`cog`
   - User - Displays currently logged in user (ex. admin)

   .. figure:: images/nutanix_tech_overview_05.png

Prism Element UI Review
-----------------------

Q: Where would you locate the version of AOS you are running?

   .. figure:: images/nutanix_tech_overview_06.png

A: You can do this by clicking on the *User* drop-down, and then clicking **About Nutanix**.

Q: How would you view a summary of the number of hosts (nodes), the resource capacity, and current utilization?

   .. figure:: images/nutanix_tech_overview_07.png

A: From the main drop-down, click **Hardware > Table > Host**.

Q: How would you see the health of your cluster?

   .. figure:: images/nutanix_tech_overview_08.png

A: From the main drop-down, click **Health**, which will default to *Summary* on the right-hand pane.

Q: What page would show you the latest activity, including being able to monitor the progress of any task, and keep track of what has been done in the past using time stamps? Can you figure out two different ways to get there?

   .. figure:: images/nutanix_tech_overview_09.png

A: From the main drop-down, click **Tasks**. Alternatively, click the circle :fa:`circle-o` icon in the toolbar, just to the right of the bell icon :fa:`bell`.

Takeaways
=========

   - Prism Element is thoughtfully laid-out UI.
   - Critical information is displayed front and center.
