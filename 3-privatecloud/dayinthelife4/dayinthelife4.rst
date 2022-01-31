.. _dayinthelife4:

######################################
Operator Workflow, Entity Browser, LCM
######################################

Operator Workflows
==================

Meet Ronald and Elise. Ronald works as a Level 3 engineering with the corporate IT helpdesk, and Elise works as a QA intern on the Fiesta team. In the brief exercise below, you will explore and contrast their access levels based on the roles defined and categories assigned by Kara.

#. Log out of the *devuser01* account, and log back into *Prism Central* with Ronald's credentials:

   - **User Name** - ``operator01@ntnxlab.local``
   - **Password** - ``nutanix/4u``

#. As expected, all VMs with Environment category values assigned are available. Note that you cannot create or delete VMs, but the ability to manage power and change VM configurations is still present.

   What else can be accessed by this user? Is Calm available?

   .. figure:: images/42.png

#. Log out of the *operator01* account, and log back into *Prism Central* with Elise's credentials:

   - **User Name** - ``operator02@ntnxlab.local``
   - **Password** - ``nutanix/4u``

#. Note that only resources tagged with the *Initials*\ **-Team: Fiesta** category are available to be managed.

   .. figure:: images/43.png

Using Entity Browser, Search, and Analysis
==========================================

Now that Kara has freed up time to focus on replacing additional legacy infrastructure, she must understand how a large, diverse environment can all be managed and monitored within Prism Central. In the exercise below, you will explore common workflows for working with entities across multiple clusters in a Nutanix environment. 

#. Log out of the *operator02* account, and then log back into *Prism Central* with Kara's credentials:

   - **User Name** - ``adminuser01@ntnxlab.local``
   - **Password** - ``nutanix/4u``

#. Within Prism Central, select :fa:`bars` **> Compute & Storage > VMs**. Prism Central's *Entity Browser* provides a robust UI for sorting, searching, and viewing entities such as VMs, Images, Clusters, Hosts, Alerts, and more.

#. Select **Filters**, and explore the available options. Specify the following example filters, and verify the corresponding box is checked:

   - **Name** - Contains *Initials*
   - **Categories** - *Initials*\ **-Team: Fiesta**
   - **Hypervisor** - AHV
   - **Power State** - On

   Take notice of other helpful filters available such as VM efficiency, memory usage, and storage latency.

#. Select the filtered VMs, and click the **Label** icon to apply a custom label to your group of filtered VMs (ex. *Initials* AHV Fiesta VMs). Click **Create new label** once you have typed the label name.

   .. figure:: images/44.png

#. Clear all filters, and then select your new label to return to your previously identified VMs quickly. Labels provide an additional taxonomy for entities without tying them to specific policies as is with categories.

   .. figure:: images/45.png

#. Select the **View by** dropdown to access the different out-of-box views. Which view should be used to understand if your VMs are included as part of a DR plan?

#. Click **View by >** :fa:`plus` **Add Custom** to create a VM view (ex. *XYZ-VM-View*) that displays **CPU Usage**, **CPU Ready Time**, **IO Latency**, **Working Set Size Read**, and **Working Set Size Write**.

#. Click **Save**. This view could be used to helping to spot VM performance problems.

   .. figure:: images/46.png

#. To fully appreciate the power of Prism Central for searching, sorting, and analyzing entities, view the following brief video:

   .. raw:: html

     <center><iframe width="560" height="315" src="HTTPS://www.youtube.com/embed/HXWCExTlXm4" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></center>

Life Cycle Manager
==================

While not a daily activity, Kara previously dedicated 40% of her time planning and executing software and firmware updates to legacy infrastructure, leaving little time for innovation. In her Nutanix environments, Kara is leveraging the rules engine and rich automation in Life Cycle Manager (LCM) to take the hassle out of planning and applying her infrastructure software updates.

   .. note:: Unfortunately, in a shared cluster environment, you're not able to test LCM directly without potentially causing issues for other users. To become more familiar with LCM's capabilities and ease of use, click through the video below.

   .. raw:: html

      <center><iframe width="560" height="315" src="HTTPS://www.youtube.com/embed/8HOpsNdTyUg" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></center> 

Wrap-up & Next Steps
====================

You've now seen for yourself how:

- Prism delivers a frictionless experience for virtual infrastructure administrators, including deploying storage, networks, workloads, monitoring, and updating this environment.

- Prism Central capabilities, combined with Active Directory, can control access and enable self-service for non-administrator personas. Additionally, you helped rich application automation capabilities for your Private Cloud through Nutanix Calm.

- Private Clouds aren't built on IaaS, self-service, and application automation alone, however. In the following labs, you can see how Nutanix has built on its foundation to provide advanced monitoring and operations capabilities through its additional *Prism Ultimate* features.