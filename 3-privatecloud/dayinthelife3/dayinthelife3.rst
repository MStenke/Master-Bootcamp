.. _dayinthelife3:

###############################
Blueprints & Developer Workflow
###############################

Staging Blueprints
==================

A Blueprint is a framework for every application that you model by using Nutanix Calm. Blueprints are templates that describe all the steps required to provision, configure, and execute tasks on the services and applications created. A Blueprint also defines the lifecycle of an application, and its underlying infrastructure, starting from the creation of the application to the actions carried out on an application (updating software, scaling out, etc.), all the way through the termination of the application.

You can use Blueprints to model applications of various complexities, from simply provisioning a single VM to provisioning and managing a multi-node, multi-tier, multi-cloud application.

While developers will create and publish their Blueprints, Kara wants to provide a standard Fiesta Blueprint available for the team.

#. Download the Fiesta-Multi Blueprint to your desktop by right-clicking `here <HTTPS://raw.githubusercontent.com/nutanixworkshops/EnterprisePrivateCloud_Bootcamp/master/dayinthelife1/Fiesta-Multi.json>`_ and choosing **Save Link As...**.

#. Within Prism Central, select :fa:`bars` **Services > Calm**, and then select **Blueprints** from the left-hand menu.

#. Click **Upload Blueprint**.

   .. figure:: images/25.png

#. Select **Fiesta-Multi.json**.

#. Update the *Blueprint Name* to include your initials. Even across different projects, Calm Blueprint names must be unique.

#. Select *Initials*\ **-FiestaProject**, and then click **Upload**.

   .. figure:: images/26.png

#. Select *Initials*\ **-Environment** from the *Environment* drop-down within the right-hand pane.

#. Expand the **db_password** selection by clicking the **>** symbol. Enter **nutanix/4u** within the *Value* text field.

#. Click **Credentials** from the top pane.

#. Expand the **CENTOS** selection by clicking the **>** symbol.

#. Enter **nutanix/4u** within the *Password* text field.

#. Click **Save** and then click **Back**.

#. Select the **NodeReact** service within the main window.

#. Within the pane on the right-hand side, scroll down to the **NETWORK ADAPTERS (NICS) (1)** section, and select **Secondary** from the **NIC 1** drop-down.

#. Note that in the *Categories* section directly above, the *Initials*\ **-Team: Fiesta** and **Environment: Dev** categories for the **NodeReact** Service have already been selected.

   .. figure:: images/27.png

#. Repeat the **NIC 1** assignment for the **MySQL** service.

#. Click **Save**.

   Within minutes, Kara has laid the groundwork to provide virtual infrastructure and application self-service directly to her end users.

Developer Workflow
==================

Meet Dan. Dan is a member of the Fiesta Engineering team. He's behind on testing a new feature, as his request to IT to deploy the virtual infrastructure he requires to perform the testing are several days overdue.

Dan has resorted to deploying VMs on a public cloud service outside of the heavily-secured corporate network and its security oversight. Dan is inexperienced with the public cloud, so he's putting the company at risk of having their intellectual property stolen because he's desperate to finish this project on time.

Kara is made aware of this and encourages Dan to follow the exercise below, which will allow him to deploy the resources he securely needs for his project quickly.

#. Within the *admin* drop-down at the top right-hand corner of the screen, choose **Sign out**

#. Log in to *Prism Central* with Dan's credentials:

   - **User Name** - ``devuser01@ntnxlab.local``
   - **Password** - ``nutanix/4u``

#. Click :fa:`bars` and observe that you now have restricted access to the environment.

#. On the *VMs* page, your *Initials*\ **-WinToolsVM** is available to be managed by Dan. Click on the VM, and within the lower pane, observe that Dan can view basic metrics associated with this VM. He can also control the VM configuration, power operations, and even delete the VM.

   .. figure:: images/29.png

   Two workflows could be followed for self-service creation of VMs: Traditional VM creation wizard and Calm. Dan's requirement is a Linux virtual machine that runs multiple tools required for his development workflow.

#. Close this screen by clicking on the :fa:`times` at the top right-hand corner.

#. Click **Create VM**, and then fill out the following fields to provision a virtual machine.

   This is similar to the manual VM deployment process Kara followed earlier in the lab.

   - **Create VM from** - Disk Images (default)
   - Click **Next**
   - **Select Disk Image(s)** - Linux_ToolsVM.qcow2
   - Click **Next**
   - **Name** - *Initials*\ **-LinuxToolsVM**
   - Click :fa:`plus` **Connect To Subnet**
   - **Subnet** - Secondary
   - Click **Save**
   - **Categories** - Environment: Dev
   - Select **Manually configure the CPU and Memory for this VM.**
   - **CPU** - 2
   - **Cores Per CPU** - 1
   - **Memory** - 4 GiB

#. Click **Save**, and note the VM is immediately powered on following its creation.

   In addition to the tools VM, Dan also desires to deploy infrastructure that can be used to test new builds of the Fiesta application. Having end-users deploy multi-tier applications through single-VM provisioning and manual integration is slow, inconsistent, and doesn't result in high user satisfaction. Luckily, we can leverage the pre-created Blueprint for Fiesta, which was previously staged to our project by Kara.

#. Select :fa:`bars` **> Services > Calm**.

#. Select **Blueprints** from the left-hand menu and click on the *Initials*\ -Fiesta-Multi Blueprint.

   .. figure:: images/30.png

   .. note::

      If you're unfamiliar with Calm Blueprints, take a moment to explore the following critical components of the *Fiesta-Multi* Blueprint:

      - Select either the **NodeReact** or **MySQL** service and review the **VM** configuration in the configuration pane on the right hand of the screen.

         .. figure:: images/31.png

      - Select the **Package** tab, and then click **Configure Install** to view the installation tasks for the selected service. These are the scripts and actions associated with the configuration of each Service or VM.

         .. figure:: images/32.png

      - Under *Application Profile*, select **AHV** and view the variables defined for the Blueprint. Variables allow for runtime customization and can also be used on a per-application profile basis to build a single application Blueprint, enabling you to provision an application to multiple environments, including AHV, ESXi, AWS, GCP, and Azure.

         .. figure:: images/33.png

      - Select the **Create** action under *Application Profile* to visualize dependencies between services. Dependencies can be defined explicitly, but Calm will also identify implicit dependencies depending on the assignment of variables. In this Blueprint, you see the web tier installation process will not begin until the MySQL database is running.

         .. figure:: images/34.png

      - Click **Credentials** in the toolbar at the top of the Blueprint editor, and then expand the existing **CENTOS** credential. Blueprints can contain multiple credentials, authenticating to VMs to execute scripts or securely passing credentials directly into scripts.

         .. figure:: images/35.png

#. Click **Launch** to provision an instance of the Blueprint.

#. Fill out the following fields, and then click **Create**.

   - **Name of the Application** - *Initials*\ **-FiestaMySQL**
   - Select *Initials*\ **-Environment** from the Environment (default)
   - **Initials** - <YOUR-INITIALS>
   - Click **Deploy**.

   .. figure:: images/37.png

#. Select the **Audit** tab to monitor the deployment of the Fiesta development environment. The provisioning of the app should take approximately 5 minutes.

   .. figure:: images/38.png

#. While the application is provisioning, open :fa:`bars` **> Administration > Projects** and select your project.

#. Review the *Summary*, *Usage*, *VMs*, and *Users* tabs to see what type of data is available. These breakouts make understanding the resources consumed on a per-project, VM, or user level.

   .. figure:: images/39.png

#. Return to **Calm > Applications >** *Initials*\ **-FiestaMySQL** and wait for the application to move from *Provisioning* to *Running*.

#. Select the **Services** tab, and then select the **NodeReact** service to obtain the IP of the web tier.

   .. figure:: images/40.png

#. Open ``<NODEREACT-IP>`` in a new browser tab to validate that the app is running.

   .. figure:: images/41.png

Instead of filing tickets and waiting days, Dan was able to get his test environment up and running himself - even before he could even finish his breakfast burrito!