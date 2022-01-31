.. _prismops_capacity_lab:

#########################
Prism Pro Capacity Runway
#########################

Overview
========

In this lab, you will learn how Prism Pro can help IT Admins monitor, analyze, and automatically generate reports on their remaining cluster's runway.

Runway Monitoring
=================

Capacity Runway is a measure of the remaining capacity left within a given cluster. There is an overall cluster runway and individual runway measurements for CPU, memory, and storage capacity. The Capacity Runway is calculated using X-FIT, Prism Pro's machine intelligence engine. Let's view the Capacity Runway of your cluster.

#. Within Prism Central, click **Prism-Pro-Cluster** within the *Capacity Runway* widget on the dashboard.

   .. figure:: images/ppro_11.png

   Observe the Capacity Runway information for storage, CPU, and memory.

#. Select **Memory** from the left-hand side menu. The red exclamation mark indicates the date on which this cluster is estimated to run out of memory. You can hover over the chart at this point to view additional information.

   .. figure:: images/ppro_12.png

#. Click on the **Optimize Resources** button to see the inefficient VMs in the environment, along with suggestions on how to optimize these VMs to be as efficient as possible.

   .. figure:: images/ppro_14.png 

#. Close the optimize resources popup.

Runway Analysis
===============

Prism Pro's X-FIT engine also provides the capability to plan for future workloads. It will recommend hardware that can be added to the cluster to account for the new proposed workload(s) resource requirements.

#. Click the **Get Started** button within the *Adjust Resources* section on the left-hand side menu. We can now use this to start planning for new workloads and see how the runway will need to be extended in the future.

#. Click **Add/adjust** adjacent to *Workloads*.

   .. figure:: images/ppro_15.png

#. Select **VDI** from the *Workload* drop-down, and enter **1000** into the *Number Of Users* field. By default, it will add the workload 60 days from today. Click **Save**.

   .. figure:: images/ppro_16.png

#. (Optional) Add another workload of your choice.

#. Click **Recommend**.

   .. figure:: images/ppro_18.png

#. Once the recommendation is available, toggle between list and chart view to better understand your scenario.

   .. figure:: images/ppro_19.png

#. Click the **Generate PDF** button in the upper right-hand corner to open a new tab with a PDF report for the scenario/workloads you have created.

   .. figure:: images/ppro_19b.png

#. View your report.

   .. figure:: images/ppro_20.png

#. Do not save your scenario. Instead, exit this screen. We will be configuring automated report generation using Playbooks.

Runway Reporting
================

Now let's look at how we can take automated action to generate this report when the Capacity Runway is low. We will use X-Play, Prism Pro's simple automation engine.

#. Click :fa:`bars` **Operations > Playbook > Get Started**.

#. Click **Create Playbook**.

#. Select **Alerts Matching Criteria** as the trigger. Using this trigger allows us to trigger this Playbook for one or more Alert Policies that match the criteria specified.

   .. figure:: images/cap3.png

#. Select **Specific Alert Policies**, and enter **runway** in the text field directly below. Select the following from the drop-down since these are the three alerts we would want to generate a report for if Storage, Memory, or CPU runway are low:
   
   - **Cluster running out of storage capacity (low runway).**
   - **Cluster running out of Memory Capacity (low runway).** and 
   - **Cluster running out of CPU Capacity (low runway)** 

   .. figure:: images/cap4.png

#. First, we would like to Generate a Forecast report for this alert. Click **Add Action** from the left-hand pane, and then select the **Generate Forecast Report** action.

   .. figure:: images/cap5.png

#. Select **Alerts Matching Criteria: Source Entity** from the *Cluster* drop-down.

   .. figure:: images/cap6.png

#. Next, we would like to notify someone that X-Play created the ticket. Click **Add Action** and select the **Email** action.

   .. figure:: images/cap7.png

#. Fill out the following fields:

   - **Recipient:** Fill in your email address.
   - **Subject :** ``Playbook {{playbook.playbook_name}} was executed.``
   - **Message:** ``As a result of the alert, {{trigger[0].alert_entity_info.name}}, the playbook, {{playbook.playbook_name}}, was executed. The generated report is attached to this email.``
   - **Attachment:** Choose **Generate Forecast Report: Report PDF**

   .. figure:: images/cap8.png

#. Click **Save & Close**. Enter *Initials* - Automatically Generated Forecast Report in the *Name* field, and set the *Playbook Stats* to **Enabled** before clicking **Save**.

   .. figure:: images/cap9.png

#. Now let's trigger the workflow. Switch to the *alerts* browser tab  you opened in the :ref:`prism_ops_labsetup` section (ex. `HTTP://10.38.17.12/alerts`).

#. Select the **Memory Runway is Short** option, and then click **Simulate Alert** to simulate a memory-constrained alert for *Prism-Pro-Cluster*.

   .. figure:: images/cap10.png

#. Switch back to the previous browser tab. Click *Initials* - Automatically Generate Forecast Report Playbook.

   .. figure:: images/cap11.png

#. Switch to the **Plays** tab. Click on the title of the first play to view its details.

   .. figure:: images/cap12.png

#. The sections in this view can be expanded to display additional details for each item. If there were any errors, they would also be shown in this view. Close this view once you are done reviewing it.

   .. figure:: images/cap13.png

#. Check your email. You should also get an email with the report attached, and this can take up to 5 minutes to send.

   .. figure:: images/cap14.png

#. Click on the **List** tab within *Playbooks*. Select your playbook, and then choose **Disable** from the *Actions* drop-down.

   .. figure:: images/cap15.png

Takeaways
=========

- Prism Pro is our solution to make IT OPS smarter and automated. It covers the IT OPS process ranging from intelligent detection to automated remediation.

- X-FIT is our machine learning engine to support smart IT OPS, including capacity forecasting.

- X-Play, the IFTTT for the enterprise, is our engine to automate daily operations tasks, making it so easy that every admin can build automation.
