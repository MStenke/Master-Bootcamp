.. _linux_tools_vm:

---------------
Linux Tools VM
---------------

Übersicht
+++++++++

Dieses CentOS VM-Image wird mit Paketen bereitgestellt, die zur Unterstützung mehrerer Lab-Übungen verwendet werden. Stellen Sie diese VM auf Ihrem zugewiesenen Cluster bereit, wenn Sie im Rahmen von **Lab Setup** dazu aufgefordert werden. **Stellen Sie die VM nur einmal bereit, sie wird u.U. in mehreren Labs verwendet.**

Bereitstellen von CentOS
++++++++++++++++++++++++

In **Prism Central** > auswählen :fa:`bars` **> Virtual Infrastructure > VMs** und klicken Sie **Create VM**.

Füllen Sie die folgenden Felder aus:

- **Name** - *Initialen*-Linux-ToolsVM
- **Description** - (Optional) Beschreibung für Ihre VM.
- **vCPU(s)** - 1
- **Number of Cores per vCPU** - 2
- **Memory** - 2 GiB

- Wählen Sie **+ Add New Disk**
    - **Type** - DISK
    - **Operation** - Clone from Image Service
    - **Image** - CentOS7.qcow2
    - Wählen Sie **Add**

- **Boot Configuration**
    - Leave the default selected **Legacy Boot**

- Wählen Sie **Add New NIC**
    - **VLAN Name** - Secondary
    - Wählen Sie **Add**

Klicken Sie auf **Save**, um die VM zu erstellen.

Schalten Sie die VM ein.

Tools Installation
++++++++++++++++++

Melden Sie sich über die SSH- oder Konsolensitzung mit den folgenden Anmeldeinformationen bei der VM an:

- **Benutzername** - root
- **Passwort** - nutanix/4u

Installieren Sie die benötigte Software, indem Sie die folgenden Befehle ausführen:

.. code-block:: bash

  yum update -y
  yum install -y ntp ntpdate unzip stress nodejs python-pip s3cmd awscli
  npm install -g request
  npm install -g express


NTP konfigurieren 
.................

Aktivieren und konfigurieren Sie NTP, indem Sie die folgenden Befehle ausführen:

.. code-block:: bash

  systemctl start ntpd
  systemctl enable ntpd
  ntpdate -u -s 0.pool.ntp.org 1.pool.ntp.org 2.pool.ntp.org 3.pool.ntp.org
  systemctl restart ntpd

Deaktivieren von Firewall und SELinux 
.....................................

Deaktivieren Sie die Firewall und SELinux, indem Sie die folgenden Befehle ausführen:

.. code-block:: bash

  systemctl disable firewalld
  systemctl stop firewalld
  setenforce 0
  sed -i 's/enforcing/disabled/g' /etc/selinux/config /etc/selinux/config

Installation von Python
.......................

Installieren Sie Python, indem Sie die folgenden Befehle ausführen:

.. code-block:: bash

  yum -y install python36
  python3.6 -m ensurepip
  yum -y install python36-setuptools
  pip install -U pip
  pip install boto3