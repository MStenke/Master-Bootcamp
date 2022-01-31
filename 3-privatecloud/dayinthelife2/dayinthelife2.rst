.. _dayinthelife2:

##########################################
Self-Service, Categories, Roles & Projects
##########################################

Enabling User Self Service
==========================

While Prism and ``acli`` provide simple workflows for creating VMs, Kara is regularly inundated with these requests. She would love to focus more of her time on modernizing other parts of her organization's aging infrastructure and maybe even attending her son's soccer games for a change.

In the following exercises, Kara will level up her Private Cloud game by providing Infrastructure as a Service (IaaS) self-service capabilities to her users by leveraging the native capabilities in *Prism Central*.

#. Select **Home** from the drop-down menu.

#. Access *Prism Central* by clicking the **Launch** button and logging in with the following credentials:

   - **User Name** - admin
   - **Password** - <HPOC-PASSWORD>

   .. figure:: images/15a.png

Exploring Categories
====================

A *Category* is a key-value pair. Categories are assigned to entities (ex. VMs, networks, or images) based on some criteria (ex. location, production level, app name). Policies can then be mapped to those entities that are assigned a specific category value.

For example, you might have a department category that includes engineering, Finance, and HR values. In this case, you could create one backup policy that applies to Engineering and HR and a separate (more stringent) backup policy that applies to just Finance. Categories allow you to implement various policies across entity groups, and Prism Central will enable you to view any established relationships quickly.

In this exercise, you'll create a custom category for Kara to help align access to the proper resources for the Fiesta app team.

#. Within **Prism Central**, select :fa:`bars` **> Administration > Categories**.

#. Click **New Category**, fill out the following fields, and then click **Save**:

   - **Name** - *Initials*\ -Team
   - **Purpose** - Allows resource access based on application team
   - **Values** - Fiesta

   .. figure:: images/15.png

#. Click on the **Environment** category, and note the available values. *Environment* is a system category, and while you can add additional values, you cannot modify or delete the category or any out-of-the-box values.

   .. figure:: images/16.png

#. Close the *Environment* window.

#. Select :fa:`bars` **> Compute & Storage > VMs**.

#. Select both the **AutoAD** and **NTNX-BootcampFS-1** VMs and then click **Actions > Manage Categories**.

   .. figure:: images/17.png

   .. note::

      Depending on the number of participants, some of the VMs you need to select could be on another page. You may either search for your VM(s), click to view additional pages to select the VM, or choose to show other rows. Any of these techniques can be accomplished at the upper right-hand portion of the interface.

#. In the *Search for a category* field, begin typing ``Environment``. Select the **Environment: Production** value, and then click :fa:`plus-circle`.

   .. figure:: images/18.png

   .. note::

      For categories tied to Security, Protection, or Recovery policies, related policies will appear in this window to show the impact of applying a category to an entity.

#. Click **Save**.

#. Using the checkboxes, de-select both the **AutoAD** and **NTNX-BootcampFS-1** VMs.

#. Using the checkbox, select the *Initials*\ **-WinToolsVM**, and then click **Actions > Manage Categories**.

#. Assign the *Initials*\ **-Team: Fiesta** category, click :fa:`plus-circle` and then click **Save**.

   .. figure:: images/18a.png

Exploring Roles
===============

   By default, Prism Central ships with several roles that map to typical user personas. Roles define what actions a user can perform and are mapped to categories or other entities.

   Kara needs to support two types of users working on the Fiesta team:

   - Developers who need to provision VMs for test environments
   - Operators who monitor multiple environments within the organization have minimal capabilities to modify each environment.

#. Within **Prism Central**, select :fa:`bars` **> Administration > Roles**.

   The built-in Developer role allows users to create and modify VMs, create, provision, and manage Calm Blueprints, and more.

#. Click on the built-in **Developer** role, review the approved actions for the role, and then click **Manage Assignment**.

   .. figure:: images/19.png

#. Click on **Add New**. Within the *Select Users or Groups* section, specify the **SSP Developers** user group.

#. Within the *Select Entities* section, use the drop-down menu to specify the following resources:

   - **Cluster > Individual entity** - *<YOUR-CLUSTER>*
   - **Subnets: VLAN > Individual entity** - Secondary
   - **AHV VM > In Category** - Environment:Testing, Environment:Staging, Environment:Dev, *Initials*\ -Team:Fiesta

   .. figure:: images/20.png

#. Click **Save**, and then close this screen by clicking on the :fa:`times` at the top right-hand corner.

   The default *Operator* role includes deleting VMs and applications deployed from Blueprints, which isn't desired in our environment. Rather than building a new role from scratch, we can clone an existing role and modify it to suit our needs. The desired operator role should only have the rights to view VM metrics, perform power operations, and update VM configurations such as vCPU or memory to address application performance issues.

#. Click the built-in *Operator* role, and then click **Duplicate**.

   .. figure:: images/20a.png

#. Fill out the following fields, and then click **Save** to create your custom role.

   - **Role Name** - *Initials*\ **-SmoothOperator**
   - **Description** - Limited operator account
   - **App** - No Access
   - **VM** - Edit Access

   .. note::

      The *App* access will grey out and change to *Set custom permissions* once you select *Edit* access with the *VM* section.

   .. figure:: images/21.png

#. Close this screen by clicking on the :fa:`times` at the top right-hand corner.

#. Select your *Initials*\ **-SmoothOperator** role, and click **Manage Assignment**.

#. Click on **Add New**.

#. Create the following assignment:

   - **Select Users or Groups** - ``operator01@ntnxlab.local``
   - **Select Entities > AHV VM > In Category** - Environment:Production, Environment:Testing, Environment:Staging, Environment:Dev

   Operator01 has access to VMs within any *Environment* category but lacks generic access to specific clusters.

#. Click on **Add New** to add an additional assignment to the same role:

   - **Select Users or Groups** - ``operator02@ntnxlab.local``
   - **Select Entities > AHV VM > In Category** - Environment:Dev, *Initials*\ -Team:Fiesta

   Operator02 will have access to VMs within the *Environment: Dev* or *Fiesta* category.

   .. figure:: images/22.png

#. Click **Save**, and then close this screen by clicking on the :fa:`times` at the top right-hand corner.

#. For infrastructure administrators such as Kara, you can map AD users to the *Prism Admin* or *Super Admin* roles through selecting :fa:`bars` **> Prism Central Settings > Role Mapping** and adding a new *Cluster Admin* or *User Admin* mapping to AD accounts.

   .. figure:: images/28.png

Exploring Projects
==================

The previous exercises are sufficient to provide essential VM creation self-service to Kara's users, but much of their work involves applications that consist of multiple VMs. Manual deployment of numerous VMs for a single development, testing, or staging environment is slow and subject to inconsistency and user error. To provide a better experience for her users, Kara will introduce Nutanix Calm into the environment.

Nutanix Calm allows you to build, provision, and manage your applications across private (AHV, ESXi) and public cloud (AWS, Azure, GCP) infrastructure.

For non-infrastructure administrators to access Calm, allowing them to create or manage applications, users or groups must first be assigned to a *Project, which acts as a logical container to define user roles, infrastructure resources, and resource quotas. Projects represent users with a standard set of requirements or a typical structure and function, such as engineers collaborating on the Fiesta project.

#. Within **Prism Central**, select :fa:`bars` **> Services > Calm**.

#. Select **Projects** from the left-hand menu, and then click :fa:`plus` **Create Project**.

   .. figure:: images/23.png

#. Within the *Project Name* field, enter *Initials*\ **-FiestaProject**, and then click **Create**.

   .. figure:: images/23a.png

#. Click on **Add Accounts**, fill out the following fields:

   - Click on the **Select Account** drop-down, and choose **NTNX_LOCAL_AZ**.
   - Click the **Add/Edit Clusters and Subnets** button, and select your cluster from the drop-down.
   - Check both the **Primary** and **Secondary** boxes, and then click **Confirm**.
   - Within the *Quotas (optional)* section, specify the following:
      - **vCPUs** - 100
      - **Memory** - 100
   - Click **Save Accounts and Project**.
   
#. Click **Create Environment**, and fill out the following fields:

   - Enter *Initials*\ -Environment, and then click **Next**.
   - Click on the **Select Account** drop-down, and choose **NTNX_LOCAL_AZ**.
   - Click anywhere in the *VM Configuration* box to expand it.

      Perform the following for the *Windows* section.

      - Within the *VM Configuration* section, specify the following:

         - **vCPUs** - 2
         - **Cores per vCPU** - 1
         - **Memory (GiB)** - 4

      - Scroll down to the *Disks* section, and choose **Windows2016.qcow2** from the *Image* drop-down.
      - Scroll down to the *Network Adapters (NICs)* section, and click the :fa:`plus`.
      - Choose **Secondary** from the *NIC 1* drop-down.

      Perform the following within the *Linux* section.

      - Within the *VM Configuration* section, specify the following:

         - **vCPUs** - 2
         - **Cores per vCPU** - 1
         - **Memory (GiB)** - 4

      - Scroll down to the *Disks* section, and choose **CentOS7.qcow2** from the *Image* drop-down.
      - Scroll down to the *Network Adapters (NICs)* section, and click the :fa:`plus`.
      - Choose **Secondary** from the *NIC 1* drop-down.
      - Click **Next**.  
      - Click :fa:`plus` **Add Credentials**, and specify the following:

         - **Credential Name** - CENTOS
         - **Username** - root
         - **Secret Type** - Password
         - **Password** - ``nutanix/4u``
      - Click **Save Environment & Project**.

#. Within the *Users, Groups, and Roles* section, click the **Add Users** button, fill out the following fields, and click **Save**.
   
   Click :fa:`plus` **Add User** before entering each entry.

   - **Name** - Administrators (group)
   - **Role** - Project Admin

   - **Name** - Operator02 (person)
   - **Role** - *Initials*\ **-SmoothOperator**

   - **Name** - SSP Developers (group)
   - **Role** - Developer

      .. figure:: images/24b.png

   Note that we've only granted *Operator02* to the *Calm* Project, rather than all *Operator* accounts.