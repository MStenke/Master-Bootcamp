.. _windows_tools_vm:

----------------
Windows Tools VM
----------------

Übersicht
+++++++++

Stellen Sie diese VM auf Ihrem zugewiesenen Cluster bereit, wenn Sie im Rahmen von **Lab Setup** dazu aufgefordert werden. **Stellen Sie die VM nur einmal bereit, sie wird u.U. in mehreren Labs verwendet.**

Dieses Windows Server 2012 R2-Image ist mit einer Reihe von Tools vorinstalliert, darunter:

- Microsoft Remote Server Administration Tools (RSAT)
- PuTTY, CyberDuck, WinSCP
- Sublime Text 3, Visual Studio Code
- OpenOffice
- Python
- pgAdmin
- Chocolatey Package Manager

Bereitstellen der Tools VM 
++++++++++++++++++++++++++

In **Prism Central** > auswählen :fa:`bars` **> Virtual Infrastructure > VMs**, und klicken Sie auf **Create VM**.

Füllen Sie die folgenden Felder aus:

- **Name** - *Initials*-Windows-ToolsVM
- **Description** - (Optional) Beschreibung für Ihre VM.
- **vCPU(s)** - 1
- **Number of Cores per vCPU** - 2
- **Memory** - 4 GiB

- Wählen Sie **+ Add New Disk**
    - **Type** - DISK
    - **Operation** - Clone from Image Service
    - **Image** - ToolsVM.qcow2
    - Wählen Sie **Add**

 - **Boot Configuration**
 ..  - Leave the default selected **Legacy Boot**

- Wählen Sie **Add New NIC**
    - **VLAN Name** - Secondary
    - Wählen Sie **Add**

Klicken Sie auf **Save**, um die VM zu erstellen.

Schalten Sie die VM ein.

Melden Sie sich über eine RDP- oder Konsolensitzung mit den folgenden Anmeldeinformationen bei der VM an:

- **Benutzername** - NTNXLAB\\Administrator
- **Passwort** - nutanix/4u