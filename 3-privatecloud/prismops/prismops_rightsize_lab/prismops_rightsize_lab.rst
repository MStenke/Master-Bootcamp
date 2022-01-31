.. _prismops_rightsize_lab:

###############################
Right-sizing VMs with Prism Pro
###############################

Overview
========

In this lab, you will learn how Prism Pro can help IT Admins monitor, analyze and automatically act when a VM's memory resource is constrained.

Detecting Inefficiencies with Prism Pro X-FIT
=============================================

Prism Pro uses X-FIT machine learning to detect and monitor the behaviors of VMs running within the managed clusters. Prism Pro then analyzes the data and applies a classification to VMs that are discovered to be inefficient. The following are short descriptions of the different classifications:

   - Overprovisioned: VMs identified as using minimal amounts of assigned resources.

   - Inactive: VMs that have been powered off for some time or running VMs that do not consume any CPU, memory, or I/O resources.

   - Constrained: VMs that could see improved performance with additional resources.

   - Bully: VMs identified as using an abundance of resources and affecting other VMs.

#. Open a new browser window, and navigate to the IP address of the *PrismProLabUtilityServer* VM, as described in the :ref:`prism_ops_labsetup` section.

   Observe the *VM Efficiency* widget, which summarizes inefficient VMs that Prism Pro's X-FIT machine learning has detected in your environment.

#. Click on **View All Inefficient VMs**.

   .. figure:: images/ppro_58.png

#. You are now viewing the *Efficiency* focus in the VMs list view, which details why Prism Pro flagged each of these VMs.

   .. figure:: images/ppro_59.png

#. With this information in hand, an admin can decide against which VMs to take action. VMs that have too many or too few resources will require those VMs to be modified. This can be done in several ways, with a few examples listed below:

   - Manually: An admin edits the VM(s) configuration via Prism or vCenter (for ESXi VMs).

   - X-Play: Use X-Plays automated Playbooks to automatically resize VM(s) via a trigger or an admin's direction. There will be an example of this later in this lab.

   - Automation: Use some other automation method such as Powershell or REST APIs to resize the VM(s).

Increase Constrained VM Memory with X-Play based on Conditional Execution
=========================================================================

Let's look at how we can take automated action to resolve some of these inefficiencies. 

For this lab, we will assume that *Initials*\ -LinuxTools VM is constrained for memory, which we will be automatically remediating by right-sizing this VM.

We will also use a custom ticket system to explain how this typical workflow could integrate with ticketing systems such as ServiceNow, and use string parsing and conditional execution, two of our latest capabilities added into X-Play.

Playbook 1
-----------

#. Click on :fa:`bars` **Compute & Storage > VMs**.

#. Click on your *Initials*\ -LinuxTools VM, and note the current memory capacity. We will later increase it with X-Play.

   .. figure:: images/linuxvm.png

#. Click on :fa:`bars` **Operations** > **Playbooks**.

#. Click **Create Playbook**. We will first be creating the Playbook that will be increasing the memory of the VM. The Playbook will accept a string from the ticket system (approved or denied, in this case) and have conditional branching and execution of the next steps.

#. Select **Webhook** as the trigger. Using this trigger exposes a public API that allows scripts and third-party tools such as ServiceNow to use this Webhook to call back into Prism Central and trigger this Playbook. In our case, this Playbook will be called by the ticket system to initiate conditional execution.

   .. figure:: images/rs16.png

#. Click the :fa:`plus` **Add Action** item on the left side.

   .. figure:: images/rs17.png

#. The first action we will add is the **String Parser** action. This action allows the user to parse data from a string that can subsequently be used in actions.

   .. note::
   
      The input from the webhook will be in the format ``{"message":"The request was approved.","status":"approved"}`` or ``{"message":"The request was denied.","status":"denied"}``. This example is included for information only as to what is happening in the background. You do not need to input these values. 
      
      We will choose the status field as **string5** to check if the request was approved or denied. Fill in the other fields as instructed below.

#. Fill the following fields:

   - **String to Parse** - Click the **Parameters** link, and then select **String5**

   - **Format** - JSON

   - **JSON Path** - ``$.status``

   .. figure:: images/editparse.png

#. Click **Add Action**.

#. Select the **Branch** action, which gives the ability to execute different action sequences based on the conditions and criteria matched.

#. Fill the following fields:

   - **Condition**   - If 
   - **Operand**     - Click the **Parameters** link, and then select **Parsed String**
   - **Operator**    - ``=``
   - **Value**       - ``approved``

   .. figure:: images/editbranch.png

#. Click **Add Action** within the **Branch** section.

#. The first action we want to take is to add memory to the VM. Select the **VM Add Memory** action. Fill the following fields:
   
   - **Target VM**         - Click the **Parameters** link, and then select **Webhook: entity1**
   - **Memory to Add**     - 1  (GiB)
   - **Maximum Limit**     - 20 (GiB)
   
   .. figure:: images/addmemory.png

#. Click **Add Action** within the **Branch** section.

#. Select the **Resolve Alert** action. Fill the following fields:

   - **Parameters**  - Click the **Parameters** link, and then select **entity2**

   .. figure:: images/resolvealert.png

#. Click **Add Action** within the **Branch** section.

#. Select **Email**. Fill the following fields:

   - **Recipient:** - Your email address
   - **Subject:** - ``Playbook {{playbook.playbook_name}} was executed.``
   - **Message:** - ``{{playbook.playbook_name}} has run and has added 1GiB of Memory to the VM {{trigger[0].entity1.name}}.``

   .. figure:: images/approvedemail.png

   We will now report back to the ticket service to resolve this ticket.
   
#. Click **Add Action** within the **Branch** section.

#. Select **REST API**. Fill the following fields:

   Thus concludes our first conditional branch for an approved request.

   - **Method:**           - PUT
   - **URL:**              - ``HTTP://<PRISMOPSLABUTILITYSERVER-IP-ADDRESS>/resolve_ticket/`` (ex. HTTP://10.42.13.108/resolve_ticket/)
   - **Request Body:**     - ``{"incident_id":"{{trigger[0].entity2.uuid}}"}``
   - **Request Header:**   - ``Content-Type:application/json;charset=utf-8``

   .. figure:: images/resolveticket.png

   Now we'll add the 2nd condition, used when the request is denied. 

#. Click on **Add Condition**, select **Branch**, and then select the **Else** condition. 

   .. note:: 
      
      We could also add *Else If* we wanted to check more than just the approved and denied condition, and for now, we'll use just *Else*. We can also add a description for this action as "denied," following the same steps for the "approved" branch description above.

   .. figure:: images/elsebranch.png

   This condition will send an email notifying the user that the request has been denied and that the memory was not added.

#. Click **Add Action**.

#. Select **Email**. Fill the following fields:

   - **Description** - Click on the :fa:`pencil-alt` icon next to *Email - Copy 1* and enter **Denied**
   - **Recipient:**  - Your email address
   - **Subject:**    - ``Memory Increase Request Denied``
   - **Message:**    - ``The request to increase the memory of your VM {{trigger[0].entity1.name}} by 1 GB was denied. If you'd like to review the ticket, please navigate to HTTP://<PRISMOPSLABUTILITYSERVER-IP-ADDRESS>/ticketsystem``

   .. figure:: images/deniedemail.png

#. Click **Save & Close** button. Fill the following fields to save:

   - **Name**              - *Initials* - Resolve Service Ticket” 
   - **Description**       - Leave blank
   - **Playbook Status**   - Enabled (toggle to Enabled)

Playbook 2
-----------

For the next part of this lab, We will create a custom action to be used in our 2nd Playbook.

.. note::

   If you understand how to set up Playbooks already and wish to do so, you have the option to skip the setup of the next Playbook. 

   We recommend reading through the steps to create the Playbook to understand better what it is doing.
 
   Instead, follow the steps under the Importing/Exporting Playbooks :ref:`import-export-section` below. 

#. In the Prism Ops Lab Utility Server Console ``HTTP://<PRISMOPSLABUTILITYSERVER-IP-ADDRESS>/`` 

#. Go to click on :fa:`bars` > **Operations > Playbooks > Action Gallery** from the left-hand side menu. 

   .. figure:: images/rs3c.png

#. Select the **REST API** action and choose the **Clone** operation from the actions menu. 

   .. figure:: images/rs4.png

#. Fill in the following values, and then click **Copy**.

   - **Name:** *Initials* - Generate Service Ticket
   - **Method:** POST
   - **Username** - leave blank
   - **Password** - leave blank
   - **URL:** - ``HTTP://<PRISMOPSLABUTILITYSERVER-IP-ADDRESS>/generate_ticket/``
   - **Request Body:** ``{"vm_name":"{{trigger[0].source_entity_info.name}}","vm_id":"{{trigger[0].source_entity_info.uuid}}","alert_name":"{{trigger[0].alert_entity_info.name}}","alert_id":"{{trigger[0].alert_entity_info.uuid}}", "webhook_id":"<ENTER_ID_HERE>","string1":"Request 1GiB memory increase."}``
   - **Request Header:** - ``Content-Type:application/json;charset=utf-8``

   .. figure:: images/rs5.png

#. Now switch to the Playbooks list by clicking the **List** item in the top menu. 

   .. figure:: images/rs6.png

#. We will need to copy the Webhook ID from the first Playbook we created to be passed in the generate ticket step. Open up your Resolve Service Ticket Playbook and copy the Webhook ID to your clipboard. 

   .. figure:: images/webhookid.png

#. Now we will create a Playbook to automate the generation of a service ticket. Close your Playbook and then click **Create Playbook** at the top of the table view. 

   .. figure:: images/rs7.png

#. Select **Alert** as a trigger. 

   .. figure:: images/rs8.png

#. Search and select **VM {vm_name} Memory Constrained** as the alert policy. Since this is the issue, we are looking to take automated steps to remediate it.

   .. figure:: images/rs9.png

#. Select the *Specify VMs* radio button and choose the VM (ex. *Initials*\ **-LinuxToolsVM**) you created for the lab, so only alerts raised on your VM will trigger this Playbook. 

   .. figure:: images/selectvm.png

#. First, we would like to generate a ticket for this alert. 

#. Click **Add Action** on the left side and select the **Generate Service Ticket** action you created. Notice the details from the **Generate Service Ticket** Action you created are automatically filled in for you. Go ahead and replace the **<ENTER_ID_HERE>** text with the Webhook ID you copied to your clipboard. 

   .. figure:: images/serviceticket.png

#. Next, we would like to notify someone that X-Play created the ticket.

#. Click **Add Action** and select the Email action. Fill in the field in the email action. Here are the examples. Be sure to replace <PRISMOPSLABUTILITYSERVER-IP-ADDRESS> in the message with its IP Address. 

   - **Recipient:** - Fill in your email address.
   - **Subject :** - ``Service Ticket Pending Approval: {{trigger[0].alert_entity_info.name}}``
   - **Message:** - ``The alert {{trigger[0].alert_entity_info.name}} triggered Playbook {{playbook.playbook_name}} and has generated a Service ticket for the VM: {{trigger[0].source_entity_info.name}} which is now pending your approval. A ticket has been generated for you to take action on at HTTP://<PRISMOPSLABUTILITYSERVER-IP-ADDRESS>/ticketsystem``

   .. figure:: images/rs13.png

#. Click **Save & Close** button and save it with the following details: 

   - **Name**              - *Initials* - Generate Service Ticket for Constrained VM” 
   - **Description**       - Leave blank
   - **Playbook Status**   - Enabled (toggle to Enabled)

   .. figure:: images/rs14.png

#. Now let's trigger the workflow. Navigate to the tab you opened in the setup with the **/alerts** URL [example 10.38.17.12/alerts]. Select the Radio for **VM Memory Constrained** and input your VM. Click the **Simulate Alert** button to simulate a memory-constrained alert on your VM.

   .. figure:: images/alertsimulate.png

#. You should receive an email to the email address you put down in the first Playbook. It may take up to 5 minutes.

   .. figure:: images/ticketemail.png

#. Inside the email, click the link to visit the ticket system. Alternatively, you can directly access the ticket system by navigating to ``HTTP://<PRISMOPSLABUTILITYSERVER-IP-ADDRESS>/ticketsystem`` from a new tab in your browser.

   .. figure:: images/ticketsystem.png

#. Identify the ticket created for your VM, and click the vertical dots icon to show the Action menu. Click the **Deny** option to call the Webhook that was passed in the REST API to generate the service ticket, which will trigger the Resolve Service Ticket Playbook. It will pass on the condition for branching action and execute the **Denied** workflow. You should receive an email within a few minutes with the message input for this condition.

   .. figure:: images/ticketoption.png

#. While waiting for the email, switch back to the previous tab with the ``HTTP://<PRISMOPSLABUTILITYSERVER-IP-ADDRESS>/``. Open up the details for the **`Initials` - Resolve Service Ticket** Playbook 

#. Click the **Plays** tab towards the top of the view to look at the Plays executed for this Playbook. The sections in this view can be expanded by clicking to show more details for each item. If there were any errors, they would also be surfaced in this view. 

#. Click on the **String Parser** action to confirm that the correct condition was passed in from the webhook.

   .. figure:: images/deniedplay.png

#. Now navigate back to the ticket system either using the link in the denied email or going directly to ``HTTP://<PRISMOPSLABUTILITYSERVER-IP-ADDRESS>/ticketsystem``

#. Identify the ticket created for your VM, and click the vertical dots icon to show the Action menu. 

#. Click the **Approve** option to call the Webhook passed in the REST API to generate the service ticket, which will trigger the Resolve Service Ticket Playbook. It will pass on the condition for branching action and execute the *Approved* workflow. It will also pass on the information for the VM and Alert that triggered the workflow, so the following steps to add memory and resolve alerts are also executed.

   .. figure:: images/ticketoption.png

#. Switch back to the previous tab with the Prism Central console open. Open up the details for the **`Initials` - Resolve Service Ticket** Playbook

#. Click the **Plays** tab towards the top of the view to look at the Plays executed for this Playbook. The sections in this view can be expanded to show more details for each item. If there were any errors, they would also be surfaced in this view. 

#. Click on the **String Parser** action to confirm that the correct condition was passed in from the webhook.

   .. figure:: images/approvedbranch.png

#. Navigate back to your VM and verify that the RAM increased by 1GB.

   .. figure:: images/finalmemory.png

#. You should also get an email indicating the successful Playbook run.

   .. figure:: images/successemail.png

.. _import-export-section:

Importing/Exporting Playbooks
++++++++++++++++++++++++++++++

X-Play now can import and export playbooks across Prism Central instances. In the example below, we will show how to import the Playbook created in the preceding steps. The user will still need to make the alert policies and go through the workflow to trigger the alert as listed in the steps in the previous section. We recommend reading through the steps to create the Playbook and understanding them correctly.

#. Download the file, which is an export of the Playbook `here. <HTTPS://drive.google.com/file/d/1f5utfXCp1MJZc-KIxGQwkigkxVnd4OVp/view?usp=sharing>`_ . The extension of the downloaded file should be **.pbk**. If not, rename the downloaded file extension to **.pbk**. 

   .. note::

      Importing/Exporting Playbooks should be done in Prism Central URL
      
      **Do not do this on the Lab Utility Server**

#. Go to Prism Central > :fa:`bars` > Operations > Playbooks page and click on **Import**. 

   .. figure:: images/import0.png

#. You will need to choose the binary file that you downloaded as the Playbook to import.

   .. figure:: images/import1.png

#. You may see some validation errors since specific fields such as credentials and URLs will differ for your environment. Click on **Import**. We will resolve these errors in the next step.

   .. figure:: images/import2.png

#. Click on the Playbook that has just been imported for you - there will be a timestamp in the Playbook name. Once open the you will see that the actions that have validation errors have been highlighted. Confirm that passwords, URLs, and IP addresses are updated according to your environment.

#. Click on **Update** to change fields in the Playbook. Refer to the Playbook creation steps above to confirm these fields.

#. First, you will need to specify your VM for the alert. Click on the trigger, make sure it is the right Alert Policy and choose your VM from the dropdown.

   .. figure:: images/rsimport2.png

#. Then you will need the change the **URL** in the **Generate Service Ticket** action. Change the IP Address to your ``HTTP://<PRISMOPSLABUTILITYSERVER-IP-ADDRESS>/`` in the URL.

   .. figure:: images/rsimport3.png

#. Last, make sure the email address in the **Email** action is updated to your email address.

   .. figure:: images/rsimport4.png

#. Once you have changed these fields, click on **Save & Close**. Pop-ups will indicate validation errors that are still present. 

#. Click **Enable** and add your Initials to the Playbook name before clicking **Save**. 

   .. note::
   
     **Do remember to remove any special characters from the Playbook name to avoid validation errors.**

   .. figure:: images/rsimport1.png

Takeaways
++++++++++

- Prism Pro is our solution to make IT OPS more intelligent and automated. It covers the IT OPS process ranging from intelligent detection to automated remediation.

- X-FIT is our machine learning engine to support smart IT OPS, including anomaly detection and inefficiency detection.

- X-Play enables admins to automate their daily tasks within minutes confidently.

- X-Play is extensive and can use customers' existing APIs and scripts as part of its Playbooks and integrate nicely with customers' existing ticketing workflows.

- X-Play can enable automation of daily operations tasks with a complete IFTTT workflow thanks to conditional execution.
