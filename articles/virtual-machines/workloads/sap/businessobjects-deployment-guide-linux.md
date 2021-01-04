---
title: Bereitstellung der SAP BusinessObjects BI-Plattform in Azure für Linux | Microsoft-Dokumentation
description: Bereitstellen und Konfigurieren der SAP BusinessObjects BI-Plattform in Azure für Linux
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,mysql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: 647009854ef5a0c0811fc303914f724272f1a3f5
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486656"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-linux-on-azure"></a>Bereitstellungsleitfaden für die SAP BusinessObjects BI-Plattform für Linux in Azure

In diesem Artikel wird die Strategie zum Bereitstellen der SAP BOBI-Plattform in Azure für Linux beschrieben. In diesem Beispiel werden zwei virtuelle Computer mit SSD Premium Managed Disks als Installationsverzeichnis konfiguriert. Azure Database for MySQL wird für die CMS-Datenbank verwendet, und Azure NetApp Files für File Repository Server wird für beide Server freigegeben. Die standardmäßige Tomcat Java-Webanwendung und die BI-Plattformanwendung werden zusammen auf beiden virtuellen Computern installiert. Für den Lastenausgleich der Benutzeranforderungen wird ein Anwendungsgateway verwendet, das native TLS/SSL-Auslagerungsfunktionen besitzt.

Diese Art von Architektur eignet sich für kleine Bereitstellungen oder Nicht-Produktionsumgebungen. Für die Produktion oder Bereitstellung in großem Stil können Sie getrennte Hosts für die Webanwendung und auch mehrere BOBI-Anwendungshosts verwenden, sodass der Server mehr Informationen verarbeiten kann.

![SAP BOBI-Bereitstellung in Azure für Linux](media/businessobjects-deployment-guide/businessobjects-deployment-linux.png)

In diesem Beispiel werden eine Version und ein Dateisystemlayout verwendet, die unter der Produktversion angeordnet sind.

- SAP BusinessObjects Platform 4.3
- SUSE Linux Enterprise Server 12 SP5
- Azure Database for MySQL (Version: 8.0.15)
- MySQL C API-Connector – libmysqlclient (Version: 6.1.11)

| Dateisystem        | BESCHREIBUNG                                                                                                               | Größe (GB)             | Besitzer  | Group  | Storage                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|--------|--------|----------------------------|
| /usr/sap           | Das Dateisystem für die Installation der SAP BOBI-Instanz, der standardmäßigen Tomcat-Webanwendung und der Datenbanktreiber (falls erforderlich). | SAP-Größenrichtlinien | bl1adm | sapsys | Managed Disk – SSD Premium |
| /usr/sap/frsinput  | Das Einbindungsverzeichnis ist für die freigegebenen Dateien aller BOBI-Hosts vorgesehen, die als Repositoryverzeichnis der Eingabedatei verwendet werden.  | Geschäftsanforderung         | bl1adm | sapsys | Azure NetApp Files         |
| /usr/sap/frsoutput | Das Einbindungsverzeichnis ist für die freigegebenen Dateien aller BOBI-Hosts vorgesehen, die als Repositoryverzeichnis der Ausgabedatei verwendet werden. | Geschäftsanforderung         | bl1adm | sapsys | Azure NetApp Files         |

## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Bereitstellen von virtuellen Linux-Computern über das Azure-Portal

In diesem Abschnitt erstellen wir zwei virtuelle Computer (VMs) mit Linux-Betriebssystemimage für die SAP BOBI-Plattform. Die allgemeinen Schritte zur Erstellung virtueller Computer sind folgende:

1. Erstellen einer [Ressourcengruppe](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)

2. Erstellen eines [virtuellen Netzwerks](../../../virtual-network/quick-create-portal.md#create-a-virtual-network)

   - Verwenden Sie bei der Bereitstellung der SAP BI-Plattform nicht ein einzelnes Subnetz für alle Azure-Dienste. Basierend auf der Architektur der SAP BI-Plattform müssen Sie mehrere Subnetze erstellen. Bei dieser Bereitstellung werden wir drei Subnetze erstellen: Anwendungssubnetz, File Repository Store-Subnetz und Application Gateway-Subnetz.
   - In Azure müssen sich Application Gateway und Azure NetApp Files immer in einem separaten Subnetz befinden. Weitere Informationen finden Sie im Artikel [Azure Application Gateway](../../../application-gateway/configuration-overview.md) und [Richtlinien für die Azure NetApp Files-Netzwerkplanung](../../../azure-netapp-files/azure-netapp-files-network-topologies.md).

3. Erstellen Sie eine Verfügbarkeitsgruppe.

   - Stellen Sie zur Erzielung von Redundanz für jede Ebene bei einer Bereitstellung mit mehreren Instanzen virtuelle Computer für jede Ebene in einer Verfügbarkeitsgruppe zusammen. Stellen Sie sicher, dass Sie die Verfügbarkeitsgruppen für jede Ebene auf der Grundlage Ihrer Architektur trennen.

4. Erstellen Sie den virtuellen Computer 1 **(azusbosl1)** .

   - Sie können entweder ein benutzerdefiniertes Image verwenden oder ein Image aus Azure Marketplace auswählen. Weitere Informationen finden Sie entsprechend Ihren Anforderungen unter [Bereitstellen eines virtuellen Computers aus dem Azure Marketplace für SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap) oder [Bereitstellen eines virtuellen Computers mit einem benutzerdefinierten Image für SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-2-deploying-a-vm-with-a-custom-image-for-sap).

5. Erstellen Sie den virtuellen Computer 2 **(azusbosl2)** .
6. Fügen Sie einen SSD Premium-Datenträger hinzu. Er wird als SAP BOBI-Installationsverzeichnis verwendet.

## <a name="provision-azure-netapp-files"></a>Bereitstellen von Azure NetApp Files

Bevor Sie mit der Einrichtung von Azure NetApp Files beginnen, sollten Sie sich mit der [Azure NetApp Files-Dokumentation](../../../azure-netapp-files/azure-netapp-files-introduction.md) vertraut machen.

Azure NetApp Files ist in verschiedenen [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) verfügbar. Überprüfen Sie, ob Azure NetApp Files in Ihrer ausgewählten Azure-Region angeboten wird.

Verwenden Sie die Seite [Verfügbarkeit von Azure NetApp Files nach Azure-Region](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all), um die Verfügbarkeit von Azure NetApp Files nach Region zu prüfen.

Fordern Sie Onboarding für Azure NetApp Files an, bevor Sie Azure NetApp Files bereitstellen. Wechseln Sie dazu zu den [Registrierungsanweisungen für Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md).

### <a name="deploy-azure-netapp-files-resources"></a>Bereitstellen von Azure NetApp Files-Ressourcen

In den folgenden Anweisungen wird davon ausgegangen, dass Sie bereits [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) bereitgestellt haben. Die Azure NetApp Files-Ressourcen und die virtuellen Computer, auf denen die Azure NetApp Files-Ressourcen eingebunden werden, müssen im gleichen virtuellen Azure-Netzwerk oder in mittels Peering verknüpften virtuellen Azure-Netzwerken bereitgestellt werden.

1. Wenn Sie die Ressourcen noch nicht bereitgestellt haben, fordern Sie [Onboarding für Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md) an.

2. Erstellen Sie entsprechend den Anweisungen in [Erstellen eines NetApp-Kontos](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md) ein NetApp-Konto in der ausgewählten Azure-Region.

3. Richten Sie entsprechend den Anweisungen in [Einrichten eines Kapazitätspools](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md) einen Azure NetApp Files-Kapazitätspool ein.

   - Die in diesem Artikel vorgestellte Architektur der SAP BI-Plattform verwendet einen einzigen Azure NetApp Files-Kapazitätspool auf der Dienstebene *Premium*. Für SAP BI File Repository Server in Azure empfehlen wir die Verwendung einer Azure NetApp Files-[Dienstebene](../../../azure-netapp-files/azure-netapp-files-service-levels.md) vom Typ *Premium* oder *Ultra*.

4. Delegieren Sie ein Subnetz an Azure NetApp Files, wie in den Anweisungen in [Delegieren eines Subnetzes an Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md) beschrieben.

5. Stellen Sie Azure NetApp Files-Volumes entsprechend den Anweisungen in [Erstellen eines NFS-Volumes für Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md) bereit.

   Das ANF-Volume kann als NFSv3 und NFSv4.1 bereitgestellt werden, da beide Protokolle für die SAP BOBI-Plattform unterstützt werden. Stellen Sie die Volumes im jeweiligen Azure NetApp Files-Subnetz bereit. Die IP-Adressen der Azure NetApp-Volumes werden automatisch zugewiesen.

Denken Sie daran, dass sich die Azure NetApp Files-Ressourcen und die virtuellen Azure-Computer im gleichen virtuellen Azure-Netzwerk oder in mittels Peering verknüpften virtuellen Azure-Netzwerken befinden müssen. Beispielsweise sind „azusbobi-frsinput“, „azusbobi-frsoutput“ die Volumenamen und „nfs://10.31.2.4/azusbobi-frsinput“, „nfs://10.31.2.4/azusbobi-frsoutput“ die Dateipfade für die Azure NetApp Files-Volumes.

- Volume azusbobi-frsinput (nfs://10.31.2.4/azusbobi-frsinput)
- Volume azusbobi-frsoutput (nfs://10.31.2.4/azusbobi-frsoutput)

### <a name="important-considerations"></a>Wichtige Hinweise

Beachten Sie Folgendes beim Erstellen Ihrer Azure NetApp Files-Instanz für den File Repository Server der SAP BOBI-Plattform:

1. Die Mindestgröße eines Kapazitätspools beträgt 4 TiB (Tebibyte).
2. Die Mindestvolumegröße ist 100 GiB (Gibibyte).
3. Azure NetApp Files und alle virtuellen Computer, auf denen die Azure NetApp Files-Volumes eingebunden werden sollen, müssen sich in demselben virtuellen Azure-Netzwerk oder in [über Peering gekoppelten virtuellen Netzwerken](../../../virtual-network/virtual-network-peering-overview.md) in derselben Region befinden. Azure NetApp Files-Zugriff über VNET-Peering in derselben Region wird jetzt unterstützt. Azure NetApp-Zugriff über globales Peering wird noch nicht unterstützt.
4. Das ausgewählte virtuelle Netzwerk muss ein Subnetz besitzen, das an Azure NetApp Files delegiert wurde.
5. Mit den [Exportrichtlinien](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md) von Azure NetApp Files können Sie die zulässigen Clients und den Zugriffstyp (Lese- und Schreibzugriff, schreibgeschützt usw.) steuern.
6. Das Azure NetApp Files-Feature wertet derzeit noch keine Zonen aus. Das Feature wird bisher nicht in allen Verfügbarkeitszonen in einer Azure-Region bereitgestellt. Achten Sie auf mögliche Latenzauswirkungen in einigen Azure-Regionen.
7. Azure NetApp Files-Volumes können als NFSv3- oder NFSv4.1-Volumes bereitgestellt werden. Für die Anwendungen der SAP BI-Plattform werden beide Protokolle unterstützt.

## <a name="configure-file-systems-on-linux-servers"></a>Konfigurieren von Dateisystemen auf Linux-Servern

Für die Schritte in diesem Abschnitt werden die folgenden Präfixe verwendet:

**[A]** : Der Schritt gilt für alle Hosts.

### <a name="format-and-mount-sap-file-system"></a>Formatieren und Einbinden des SAP-Dateisystems

1. **[A]** Alle angefügten Datenträger auflisten

    ```bash
    sudo lsblk
    NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    sda      8:0    0   30G  0 disk
    ├─sda1   8:1    0    2M  0 part
    ├─sda2   8:2    0  512M  0 part /boot/efi
    ├─sda3   8:3    0    1G  0 part /boot
    └─sda4   8:4    0 28.5G  0 part /
    sdb      8:16   0   32G  0 disk
    └─sdb1   8:17   0   32G  0 part /mnt
    sdc      8:32   0  128G  0 disk
    sr0     11:0    1  628K  0 rom  
    # Premium SSD of 128 GB is attached to Virtual Machine, whose device name is sdc
    ```

2. **[A]** Blockgerät für „/usr/sap“ formatieren

    ```bash
    sudo mkfs.xfs /dev/sdc
    ```

3. **[A]** Einbindungsverzeichnis erstellen

    ```bash
    sudo mkdir -p /usr/sap
    ```

4. **[A]** UUID des Blockgeräts abrufen

    ```bash
    sudo blkid

    #It will display information about block device. Copy UUID of the formatted block device

    /dev/sdc: UUID="0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b" TYPE="xfs"
    ```

5. **[A]** Einbindungseintrag des Dateisystems in „/etc/fstab“ verwalten

    ```bash
    sudo echo "UUID=0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b /usr/sap xfs defaults,nofail 0 2" >> /etc/fstab
    ```

6. **[A]** Dateisystem einbinden

    ```bash
    sudo mount -a

    sudo df -h

    Filesystem                     Size  Used Avail Use% Mounted on
    devtmpfs                       7.9G  8.0K  7.9G   1% /dev
    tmpfs                          7.9G   82M  7.8G   2% /run
    tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
    /dev/sda4                       29G  1.8G   27G   6% /
    tmpfs                          1.6G     0  1.6G   0% /run/user/1000
    /dev/sda3                     1014M   87M  928M   9% /boot
    /dev/sda2                      512M  1.1M  511M   1% /boot/efi
    /dev/sdb1                       32G   49M   30G   1% /mnt
    /dev/sdc                       128G   29G  100G  23% /usr/sap
    ```

### <a name="mount-azure-netapp-files-volume"></a>Einbinden des Azure NetApp Files-Volumes

1. **[A]** Einbindungsverzeichnisse erstellen

   ```bash
   sudo mkdir -p /usr/sap/frsinput
   sudo mkdir -p /usr/sap/frsoutput
   ```

2. **[A]** Clientbetriebssystem für die Unterstützung der NFSv4.1-Einbindung konfigurieren **(Nur anwendbar, wenn NFSv4.1 verwendet wird)**

   Wenn Sie Azure NetApp Files-Volumes mit NFSv4.1-Protokoll verwenden, führen Sie die folgende Konfiguration auf allen VMs aus, auf denen NFSv4.1-Volumes von Azure NetApp Files eingebunden werden müssen.

   **Überprüfen der NFS-Domäneneinstellungen**

   Stellen Sie sicher, dass die Domäne als Azure NetApp Files-Standarddomäne (also **defaultv4iddomain.com**) konfiguriert und die Zuordnung auf **nobody** festgelegt ist.

   ```bash
   sudo cat /etc/idmapd.conf
   # Example
   [General]
   Domain = defaultv4iddomain.com
   [Mapping]
   Nobody-User = nobody
   Nobody-Group = nobody
   ```

   > [!Important]
   >
   > Stellen Sie sicher, dass die NFS-Domäne in „/etc/idmapd.conf“ auf der VM so festgelegt ist, dass sie mit der Standarddomänenkonfiguration für Azure NetApp Files übereinstimmt: **defaultv4iddomain.com**. Im Fall eines Konflikts zwischen der Domänenkonfiguration auf dem NFS-Client (also der VM) und dem NFS-Server (also der Azure NetApp-Konfiguration) werden die Berechtigungen für Dateien auf Azure NetApp Files-Volumes, die auf den VMs eingebunden sind, als „nobody“ angezeigt.

   Überprüfen von `nfs4_disable_idmapping` Diese Angabe sollte auf **Y** (Ja) festgelegt sein. Führen Sie den Einbindungsbefehl aus, um bei `nfs4_disable_idmapping` die Verzeichnisstruktur zu erstellen. Sie können das Verzeichnis unter „/sys/modules“ nicht manuell erstellen, da der Zugriff für den Kernel bzw. die Treiber reserviert ist.

   ```bash
   # Check nfs4_disable_idmapping
   cat /sys/module/nfs/parameters/nfs4_disable_idmapping

   # If you need to set nfs4_disable_idmapping to Y
   mkdir /mnt/tmp
   mount -t nfs -o sec=sys,vers=4.1 10.31.2.4:/azusbobi-frsinput /mnt/tmp
   umount /mnt/tmp

   echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping

   # Make the configuration permanent
   echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
   ```

3. **[A]**  Fügen Sie Bereitstellungseinträge hinzu.

   Bei Verwendung von NFSv3

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   ```

   Bei Verwendung von NFSv4.1

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   ```

4. **[A]** NFS-Volumes einbinden

   ```bash
   sudo mount -a

   sudo df -h

   Filesystem                     Size  Used Avail Use% Mounted on
   devtmpfs                       7.9G  8.0K  7.9G   1% /dev
   tmpfs                          7.9G   82M  7.8G   2% /run
   tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
   /dev/sda4                       29G  1.8G   27G   6% /
   tmpfs                          1.6G     0  1.6G   0% /run/user/1000
   /dev/sda3                     1014M   87M  928M   9% /boot
   /dev/sda2                      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1                       32G   49M   30G   1% /mnt
   /dev/sdc                       128G   29G  100G  23% /usr/sap
   10.31.2.4:/azusbobi-frsinput   101T   18G  100T   1% /usr/sap/frsinput
   10.31.2.4:/azusbobi-frsoutput  100T  512K  100T   1% /usr/sap/frsoutput
   ```

## <a name="configure-cms-database---azure-database-for-mysql"></a>Konfigurieren der CMS-Datenbank: Azure Database for MySQL

Dieser Abschnitt enthält ausführliche Informationen zur Bereitstellung von Azure Database for MySQL mithilfe des Azure-Portals. Es enthält auch Anweisungen zum Erstellen von CMS- und Überwachungsdatenbanken für die SAP BOBI-Plattform und ein Benutzerkonto für den Zugriff auf die Datenbank.

Die Anweisungen gelten nur, wenn Sie Azure Database for MySQL verwenden. Informationen zu anderen Datenbanken finden Sie in der SAP- oder datenbankspezifischen Dokumentation.

### <a name="create-an-azure-database-for-mysql"></a>Erstellen einer Azure Database for MySQL-Datenbank

Melden Sie sich im Azure-Portal an, und befolgen Sie die in dieser [Schnellstartanleitung für Azure Database for MySQL](../../../mysql/quickstart-create-mysql-server-database-using-azure-portal.md) aufgeführten Schritte. Beim Bereitstellen von Azure Database for MySQL sind einige Punkte zu beachten:

1. Wählen Sie für Azure Database for MySQL dieselbe Region aus, in der Ihre Anwendungsserver der SAP BI-Plattform ausgeführt werden.

2. Wählen Sie eine unterstützte Datenbankversion basierend auf einer [Produktverfügbarkeitsmatrix (PAM) für SAP BI](https://support.sap.com/pam) aus, die spezifisch für Ihre SAP BOBI-Version ist. Befolgen Sie dieselben Kompatibilitätsrichtlinien, wie sie für MySQL AB in SAP PAM gelten.

3. Wählen Sie unter „Compute und Speicher“ die Option **Server konfigurieren** und anschließend den entsprechenden Tarif auf der Grundlage Ihrer Dimensionierungsausgabe aus.

4. Die **automatische Speichervergrößerung** ist standardmäßig aktiviert. Denken Sie daran, dass der [Speicher](../../../mysql/concepts-pricing-tiers.md#storage) nur hoch- und nicht herunterskaliert werden kann.

5. Standardmäßig beträgt der **Aufbewahrungszeitraum für Sicherungen** sieben Tage, aber Sie können ihn [optional auf bis zu 35 Tage erhöhen](../../../mysql/howto-restore-server-portal.md#set-backup-configuration).

6. Sicherungen von Azure Database for MySQL sind standardmäßig lokal redundant, sodass Sie, wenn Sie Serversicherungen in georedundantem Speicher wünschen, **Geografisch redundant** unter den **Optionen für Sicherungsredundanz** auswählen.

> [!NOTE]
> Das Ändern der [Optionen für Sicherungsredundanz](../../../mysql/concepts-backup.md#backup-redundancy-options) nach der Erstellung des Servers wird nicht unterstützt.

### <a name="configure-connection-security"></a>Konfigurieren der Verbindungssicherheit

Der erstellte Server wird standardmäßig durch eine Firewall geschützt und ist nicht öffentlich zugänglich. Führen Sie die folgenden Schritte aus, um Zugriff auf das virtuelle Netzwerk bereitzustellen, in dem die Anwendungsserver der SAP BI-Plattform ausgeführt werden:  

1. Navigieren Sie im Azure-Portal zu den Serverressourcen, und wählen Sie im Menü auf der linken Seite die Option **Verbindungssicherheit** für Ihre Serverressource aus.
2. Wählen Sie **Ja** aus, um den **Zugriff auf Azure-Dienste zuzulassen**.
3. Wählen Sie unter den VNET-Regeln die Option **Vorhandenes virtuelles Netzwerk wird hinzugefügt** aus. Wählen Sie das virtuelle Netzwerk und Subnetz des Anwendungsservers der SAP BI-Plattform aus. Außerdem müssen Sie Zugriff auf Jumpbox oder andere Server bereitstellen, von denen aus Sie eine Verbindung von [MySQL Workbench](../../../mysql/connect-workbench.md) mit Azure Database for MySQL herstellen können. MySQL Workbench wird verwendet, um die CMS- und Überwachungsdatenbank zu erstellen.
4. Nachdem die virtuellen Netzwerke hinzugefügt wurden, wählen Sie **Speichern** aus.

### <a name="create-cms-and-audit-database"></a>Erstellen der CMS- und Überwachungsdatenbank

1. Sie können MySQL Workbench von der [MySQL-Website](https://dev.mysql.com/downloads/workbench/) herunterladen und installieren. Stellen Sie sicher, dass Sie MySQL Workbench auf dem Server installieren, der auf Azure Database for MySQL zugreifen kann.

2. Stellen Sie mithilfe von MySQL Workbench eine Verbindung mit dem Server her. Befolgen Sie die Anweisungen in diesem [Artikel](../../../mysql/connect-workbench.md#get-connection-information). Wenn der Verbindungstest erfolgreich ausgeführt wurde, erhalten Sie folgende Meldung:

   ![SQL Workbench-Verbindung](media/businessobjects-deployment-guide/businessobjects-sql-workbench.png)

3. Führen Sie auf der Registerkarte „SQL-Abfrage“ folgende Abfrage aus, um ein Schema für die CMS- und Überwachungsdatenbank zu erstellen.

   ```sql
   # Here cmsbl1 is the database name of CMS database. You can provide the name you want for CMS database.
   CREATE SCHEMA `cmsbl1` DEFAULT CHARACTER SET utf8;

   # auditbl1 is the database name of Audit database. You can provide the name you want for CMS database.
   CREATE SCHEMA `auditbl1` DEFAULT CHARACTER SET utf8;

4. Create user account to connect to schema

   ```sql
   # Create a user that can connect from any host, use the '%' wildcard as a host part
   CREATE USER 'cmsadmin'@'%' IDENTIFIED BY 'password';
   CREATE USER 'auditadmin'@'%' IDENTIFIED BY 'password';

   # Grant all privileges to a user account over a specific database:
   GRANT ALL PRIVILEGES ON cmsbl1.* TO 'cmsadmin'@'%' WITH GRANT OPTION;
   GRANT ALL PRIVILEGES ON auditbl1.* TO 'auditadmin'@'%' WITH GRANT OPTION;

   # Following any updates to the user privileges, be sure to save the changes by issuing the FLUSH PRIVILEGES
   FLUSH PRIVILEGES;
   ```

5. So überprüfen Sie die Berechtigungen und Rollen des MySQL-Benutzerkontos

   ```sql
   USE sys;
   SHOW GRANTS for 'cmsadmin'@'%';
   +------------------------------------------------------------------------+
   | Grants for cmsadmin@%                                                  |
   +------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `cmsadmin`@`%`                                   |
   | GRANT ALL PRIVILEGES ON `cmsbl1`.* TO `cmsadmin`@`%` WITH GRANT OPTION |
   +------------------------------------------------------------------------+

   USE sys;
   SHOW GRANTS FOR 'auditadmin'@'%';
   +----------------------------------------------------------------------------+
   | Grants for auditadmin@%                                                    |
   +----------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `auditadmin`@`%`                                     |
   | GRANT ALL PRIVILEGES ON `auditbl1`.* TO `auditadmin`@`%` WITH GRANT OPTION |
   +----------------------------------------------------------------------------+
   ```

### <a name="install-mysql-c-api-connector-libmysqlclient-on-linux-server"></a>Installieren des MySQL C API-Connectors (libmysqlclient) auf dem Linux-Server

Damit der SAP BOBI-Anwendungsserver auf die Datenbank zugreifen kann, benötigt er einen Datenbankclient bzw. Datenbanktreiber. Für den Zugriff auf CMS- und Überwachungsdatenbanken muss der MySQL C API-Connector für Linux verwendet werden. ODBC-Verbindungen mit der CMS-Datenbank werden nicht unterstützt. Dieser Abschnitt enthält Anweisungen zum Einrichten des MySQL C API-Connectors unter Linux.

1. Informationen zu den mit Azure Database for MySQL kompatiblen Treibern und Verwaltungstools finden Sie im Artikel [MySQL-Treiber und -Verwaltungstools, die mit Azure Database for MySQL kompatibel sind](../../../mysql/concepts-compatibility.md). Suchen Sie im Artikel nach dem Treiber **MySQL Connector/C (libmysqlclient)** .

2. Informationen zum Herunterladen von Treibern finden Sie unter diesem [Link](https://downloads.mysql.com/archives/c-c/).

3. Wählen Sie das Betriebssystem aus, und laden Sie das rpm-Paket der freigegebenen Komponente von MySQL Connector herunter. In diesem Beispiel wird die Version „mysql-connector-c-shared-6.1.11 connector“ verwendet.

4. Installieren Sie den Connector in allen SAP BOBI-Anwendungsinstanzen.

   ```bash
   # Install rpm package
   SLES: sudo zypper install <package>.rpm
   RHEL: sudo yum install <package>.rpm
   ```

5. Überprüfen Sie den Pfad von „libmysqlclient.so“.

   ```bash
   # Find the location of libmysqlclient.so file
   whereis libmysqlclient

   # sample output
   libmysqlclient: /usr/lib64/libmysqlclient.so
   ```

6. Legen Sie LD_LIBRARY_PATH so fest, dass es auf das Verzeichnis `/usr/lib64` des Benutzerkontos verweist, das für die Installation verwendet wird.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LD_LIBRARY_PATH=/usr/lib64
   ```

## <a name="server-preparation"></a>Servervorbereitung

Für die Schritte in diesem Abschnitt werden die folgenden Präfixe verwendet:

**[A]** : Der Schritt gilt für alle Hosts.

1. **[A]** Basierend auf der Variante von Linux (SLES oder RHEL) müssen Sie Kernelparameter festlegen und erforderliche Bibliotheken installieren. Weitere Informationen finden Sie im Abschnitt **Systemanforderungen** unter [Business Intelligence-Plattform: Installationsleitfaden für Unix](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US).

2. **[A]** Stellen Sie sicher, dass die Zeitzone auf Ihrem Computer ordnungsgemäß eingestellt ist. Weitere Informationen finden Sie im Abschnitt [Weitere Unix- und Linux-Anforderungen](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US/46b143336e041014910aba7db0e91070.html) im Installationsleitfaden.

3. **[A]** Erstellen Sie ein Benutzerkonto (**bl1** adm) und eine Benutzergruppe (sapsys), unter denen die Hintergrundprozesse der Software ausgeführt werden können. Verwenden Sie dieses Konto, um die Installation und die Software auszuführen. Das Konto erfordert keine Stammberechtigungen.

4. **[A]** Legen Sie die Umgebung des Benutzerkontos (**bl1** adm) so fest, dass ein unterstütztes UTF-8-Gebietsschema verwendet wird, und stellen Sie sicher, dass Ihre Konsolensoftware UTF-8-Zeichensätze unterstützt. Um sicherzustellen, dass Ihr Betriebssystem das richtige Gebietsschema verwendet, legen Sie die Umgebungsvariablen LC_ALL und LANG auf Ihr bevorzugtes Gebietsschema in Ihrer Benutzerumgebung (**bl1** adm) fest.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LANG=en_US.utf8
   export LC_ALL=en_US.utf8
   ```

5. **[A]** Konfigurieren Sie das Benutzerkonto (**bl1** adm).

   ```bash
   # Set ulimit for bl1adm to unlimited
   root@azusbosl1:~> ulimit -f unlimited bl1adm
   root@azusbosl1:~> ulimit -u unlimited bl1adm

   root@azusbosl1:~> su - bl1adm
   bl1adm@azusbosl1:~> ulimit -a

   core file size          (blocks, -c) unlimited
   data seg size           (kbytes, -d) unlimited
   scheduling priority             (-e) 0
   file size               (blocks, -f) unlimited
   pending signals                 (-i) 63936
   max locked memory       (kbytes, -l) 64
   max memory size         (kbytes, -m) unlimited
   open files                      (-n) 1024
   pipe size            (512 bytes, -p) 8
   POSIX message queues     (bytes, -q) 819200
   real-time priority              (-r) 0
   stack size              (kbytes, -s) 8192
   cpu time               (seconds, -t) unlimited
   max user processes              (-u) unlimited
   virtual memory          (kbytes, -v) unlimited
   file locks                      (-x) unlimited
   ```

6. Laden Sie Material für die SAP BusinessObjects BI-Plattform vom SAP Service Marketplace herunter und extrahieren Sie es.

## <a name="installation"></a>Installation

Überprüfen Sie das Gebietsschema für das Benutzerkonto **bl1** adm auf dem Server.

```bash
bl1adm@azusbosl1:~> locale
LANG=en_US.utf8
LC_ALL=en_US.utf8
```

Navigieren Sie zu Materialien der SAP BusinessObjects BI-Plattform, und führen Sie nachfolgenden Befehl mit dem Benutzer **bl1** adm aus:

```bash
./setup.sh -InstallDir /usr/sap/BL1
```

Befolgen Sie die Installationsanleitung für die [SAP BOBI-Plattform](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM) für Unix, die spezifisch für Ihre Version ist. Bei der Installation der SAP BOBI-Plattform sind einige Punkte zu beachten.

- Auf dem Bildschirm zum **Konfigurieren der Produktregistrierung** können Sie entweder einen temporären Lizenzschlüssel für SAP BusinessObjects Solutions aus dem SAP-Hinweis [1288121](https://launchpad.support.sap.com/#/notes/1288121) verwenden oder einen Lizenzschlüssel im SAP Service Marketplace generieren.

- Wählen Sie auf dem Bildschirm zum **Auswählen des Installationstyps** die Option für eine **vollständige** Installation auf dem ersten Server (azusbosl1) aus. Für den anderen Server (azusbosl2) wählen Sie die Option zum **Anpassen/Erweitern** aus, wodurch das bestehende BOBI-Setup erweitert wird.

- Wählen Sie auf dem Bildschirm zum **Auswählen der Standard- oder einer vorhandenen Datenbank** die Option zum **Konfigurieren einer vorhandenen Datenbank** aus, wodurch Sie aufgefordert werden, die CMS- und Überwachungsdatenbank auszuwählen. Wählen Sie **MySQL** für den Typ „CMS-Datenbank“ und den Typ „Überwachungsdatenbank“ aus.

  Sie können auch die Option für „Keine Überwachungsdatenbank“ auswählen, wenn Sie die Überwachung während der Installation nicht konfigurieren möchten.

- Wählen Sie geeignete Optionen auf dem Bildschirm zum **Auswählen des Java-Webanwendungsservers** basierend auf Ihrer SAP BOBI-Architektur aus. In diesem Beispiel haben wir Option 1 ausgewählt, bei der der Tomcat-Server auf derselben SAP BOBI-Plattform installiert wird.

- Geben Sie CMS-Datenbankinformationen in **Configure CMS Repository Database – MySQL** (CMS-Repositorydatenbank konfigurieren – MySQL) ein. Beispieleingabe für CMS-Datenbankinformationen für eine Linux-Installation. Azure Database for MySQL wird am dem Standardport 3306 verwendet.
  
  ![SAP BOBI-Bereitstellung unter Linux: CMS-Datenbank](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cms.png)

- (Optional) Geben Sie Überwachungsdatenbankinformationen in **Configure Audit Repository Database – MySQL** (Überwachungsrepositorydatenbank konfigurieren – MySQL) ein. Beispieleingabe für Überwachungsdatenbankinformationen für eine Linux-Installation.

  ![SAP BOBI-Bereitstellung unter Linux: Überwachungsdatenbank](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-audit.png)

- Befolgen Sie die Anweisungen, und geben Sie die erforderlichen Eingaben ein, um die Installation abzuschließen.

Bei Bereitstellung mehrerer Instanzen führen Sie das Installationssetup auf einem zweiten Host (azusbosl2) aus. Wählen Sie auf dem Bildschirm zum **Auswählen des Installationstyps** die Option zum **Anpassen/Erweitern** aus, wodurch das bestehende BOBI-Setup erweitert wird.

Im Angebot von Azure Database for MySQL wird ein Gateway verwendet, um die Verbindungen zu Serverinstanzen umzuleiten. Sobald die Verbindung hergestellt ist, zeigt der MySQL-Client die im Gateway festgelegte Version von MySQL an, nicht die tatsächliche Version, die auf Ihrer MySQL-Serverinstanz ausgeführt wird. Um die Version Ihrer MySQL-Serverinstanz zu ermitteln, geben Sie den `SELECT VERSION();`-Befehl an der MySQL-Eingabeaufforderung ein. Somit finden Sie in der zentralen Verwaltungskonsole (Central Management Console, CMC) eine andere Datenbankversion, die im Grunde die auf dem Gateway festgelegte Version ist. Weitere Informationen finden Sie unter [Unterstützte Azure Database for MySQL-Serverversionen](../../../mysql/concepts-supported-versions.md).

![SAP BOBI-Bereitstellung unter Linux: CMC-Einstellungen](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cmc.png)

```sql
# Run direct query to the database using MySQL Workbench

select version();

+-----------+
| version() |
+-----------+
| 8.0.15    |
+-----------+
```

## <a name="post-installation"></a>Nach der Installation

### <a name="tomcat-clustering---session-replication"></a>Tomcat-Clustering: Sitzungsreplikation

Tomcat unterstützt das Clustering von mindestens zwei Anwendungsservern für Sitzungsreplikation und Failover. SAP BOBI-Plattformsitzungen sind serialisiert, eine Benutzersitzung kann reibungslos ein Failover auf eine andere Instanz von Tomcat ausführen, selbst wenn ein Anwendungsserver ausfällt.

Wenn ein Benutzer z. B. mit einem Webserver verbunden ist, der ausfällt, während der Benutzer in einer Ordnerhierarchie in einer SAP BI-Anwendung navigiert. Mit einer ordnungsgemäß konfigurierten Clusterkonfiguration kann der Benutzer weiterhin in der Ordnerhierarchie navigieren, ohne zur Anmeldeseite umgeleitet zu werden.

Im SAP-Hinweis [2808640](https://launchpad.support.sap.com/#/notes/2808640) werden Schritte zur Konfiguration des Tomcat-Clusterings mithilfe von Multicast bereitgestellt. In Azure wird Multicast jedoch nicht unterstützt. Damit der Tomcat-Cluster in Azure funktioniert, müssen Sie [StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) verwenden (SAP-Hinweis [2764907](https://launchpad.support.sap.com/#/notes/2764907)). Lesen Sie [Tomcat-Clustering mithilfe der statischen Mitgliedschaft für die SAP BusinessObjects BI-Plattform](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/) im SAP-Blog, um einen Tomcat-Cluster in Azure einzurichten.

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>Lastenausgleich für die Webebene der SAP BI-Plattform

Bei der Bereitstellung von SAP BOBI mit mehreren Instanzen werden Java-Webanwendungsserver (Webebene) auf zwei oder mehr Hosts ausgeführt. Um die Benutzerauslastung gleichmäßig auf die Webserver zu verteilen, können Sie einen Lastenausgleich zwischen Endbenutzern und Webservern verwenden. In Azure können Sie entweder Azure Load Balancer oder Azure Application Gateway verwenden, um den Datenverkehr zu Ihren Webanwendungsservern zu verwalten. Einzelheiten zum jeweiligen Angebot werden im folgenden Abschnitt erläutert.

#### <a name="azure-load-balancer-network-based-load-balancer"></a>Azure Load Balancer (netzwerkbasierter Lastenausgleich)

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) ist ein Lastenausgleich mit hoher Leistung und Layer 4-Latenz (TCP, UDP), mit dem der Datenverkehr auf fehlerfreie virtuelle Computer verteilt wird. Der Integritätstest eines Lastenausgleichs überwacht einen bestimmten Port auf jedem virtuellen Computer und verteilt Datenverkehr nur an einen betriebsbereiten virtuellen Computer. Sie können entweder einen öffentlichen Lastenausgleich oder einen internen Lastenausgleich wählen, je nachdem, ob die SAP BI-Plattform über das Internet zugänglich sein soll. Es ist zonenredundant und gewährleistet Hochverfügbarkeit über mehrere Verfügbarkeitszonen hinweg.

Weitere Informationen finden Sie im Abschnitt zum internen Lastenausgleich in der nachfolgenden Abbildung, in der der Webanwendungsserver an Port 8080 ausgeführt wird, dem Standard-HTTP-Port von Tomcat, der vom Integritätstest überwacht wird. Somit wird jede von Endbenutzern eingehende Anforderung an die Webanwendungsserver (azusbosl1 oder azusbosl2) im Back-End-Pool umgeleitet. Der Lastenausgleich unterstützt keine TLS/SSL-Terminierung (auch bekannt als TLS/SSL-Abladung). Wenn Sie Azure Load Balancer verwenden, um Datenverkehr auf Webserver zu verteilen, empfehlen wir die Verwendung von Load Balancer Standard.

> [!NOTE]
> Wenn virtuelle Computer ohne öffentliche IP-Adressen im Back-End-Pool einer internen Azure Load Balancer Standard-Instanz (ohne öffentliche IP-Adresse) platziert werden, liegt keine ausgehende Internetverbindung vor, sofern nicht in einer zusätzlichen Konfiguration das Routing an öffentliche Endpunkte zugelassen wird. Ausführliche Informationen zum Erreichen ausgehender Konnektivität finden Sie unter [Public endpoint connectivity for Virtual Machines using Azure Standard Load Balancer in SAP high-availability scenarios](high-availability-guide-standard-load-balancer-outbound-connections.md) (Konnektivität mit öffentlichen Endpunkten für virtuelle Computer mithilfe von Azure Load Balancer Standard in SAP-Szenarien mit Hochverfügbarkeit).

![Azure Load Balancer zum Ausgleichen des Datenverkehrs über Webserver](media/businessobjects-deployment-guide/businessobjects-deployment-load-balancer.png)

#### <a name="azure-application-gateway-web-application-load-balancer"></a>Azure Application Gateway (Lastenausgleich für Webanwendungen)

[Azure Application Gateway (AGW)](../../../application-gateway/overview.md) stellt Application Delivery Controller (ADC) als Dienst zur Verfügung, mit dessen Hilfe Anwendungen den Benutzerdatenverkehr zu einem oder mehreren Webanwendungsservern leiten können. Es bietet für Ihre Anwendungen verschiedene Layer 7-Lastenausgleichsfunktionen wie TLS/SSL-Abladung, Web Application Firewall (WAF), cookiebasierte Sitzungsaffinität und andere.

Auf der SAP BI-Plattform leitet das Anwendungsgateway den Webdatenverkehr von Anwendungen an die angegebenen Ressourcen in einem Back-End-Pool (azusbosl1 oder azusbos2) weiter. Sie weisen dem Port einen Listener zu, erstellen Regeln und fügen Ressourcen zu einem Back-End-Pool hinzu. In der folgenden Abbildung fungiert ein Anwendungsgateway mit privater Front-End-IP-Adresse (31.10.3.20) als Einstiegspunkt für die Benutzer, behandelt eingehende TLS/SSL-Verbindungen (HTTPS – TCP/443), entschlüsselt TLS/SSL und leitet die unverschlüsselte Anforderung (HTTP – TCP/8080) an die Server im Back-End-Pool weiter. Mit dem integrierten TLS/SSL-Terminierungsfeature brauchen wir nur ein TLS/SSL-Zertifikat am Anwendungsgateway zu erstellen, was den Betrieb vereinfacht.

![Application Gateway zum Ausgleichen des Datenverkehrs über Webserver](media/businessobjects-deployment-guide/businessobjects-deployment-application-gateway.png)

Informationen zum Konfigurieren von Application Gateway für SAP BOBI-Webserver finden Sie unter [Lastenausgleich für SAP BOBI-Webserver mithilfe von Azure Application Gateway](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/) im SAP-Blog.

> [!NOTE]
> Es wird empfohlen, Azure Application Gateway für den Lastenausgleich des Datenverkehrs zum Webserver zu verwenden, sodass es Features wie SSL-Abladung, Zentralisieren der SSL-Verwaltung zur Verringerung des Aufwands für die Ver- und Entschlüsselung auf dem Server, Roundrobin-Algorithmus zur Verteilung des Datenverkehrs, WAF-Funktionen (Web Application Firewall), Hochverfügbarkeit usw. bietet.

### <a name="sap-businessobjects-bi-platform---back-up-and-restore"></a>SAP BusinessObjects BI-Plattform: Sicherung und Wiederherstellung

„Sicherung und Wiederherstellung“ ist ein Prozess, bei dem regelmäßig Kopien von Daten und Anwendungen an einem separaten Speicherort erstellt werden. Somit kann bei Verlust oder Beschädigung der ursprünglichen Daten oder Anwendungen der vorherige Zustand wiederhergestellt werden. Es ist auch ein wesentlicher Bestandteil jeder Strategie zur Notfallwiederherstellung in Unternehmen.

Identifizieren Sie zur Entwicklung einer umfassenden Sicherungs- und Wiederherstellungsstrategie für die SAP BOBI-Plattform die Komponenten, die zu Systemausfällen oder Störungen der Anwendung führen. Auf der SAP BOBI-Plattform ist die Sicherung der folgenden Komponenten für den Schutz der Anwendung unerlässlich.

- SAP BOBI-Installationsverzeichnis (verwaltete Premium-Datenträger)
- File Repository Server (Azure NetApp Files oder Azure Files Premium)
- CMS-Datenbank (Azure Database for MySQL oder Datenbank auf Azure-VM)

Im folgenden Abschnitt wird beschrieben, wie die Sicherungs- und Wiederherstellungsstrategie für die einzelnen Komponenten auf der SAP BOBI-Plattform implementiert wird.

#### <a name="backup--restore-for-sap-bobi-installation-directory"></a>Sicherung und Wiederherstellung für das SAP BOBI-Installationsverzeichnis

In Azure ist die einfachste Möglichkeit zur Sicherung von Anwendungsservern und allen angeschlossenen Datenträgern die Verwendung des [Azure Backup](../../../backup/backup-overview.md)-Diensts. Er bietet unabhängige und isolierte Sicherungen zum Schutz vor dem versehentlichen Löschen der Daten auf Ihren VMs. Sicherungen werden in einem Recovery Services-Tresor mit integrierter Verwaltung von Wiederherstellungspunkten gespeichert. Konfiguration und Skalierung sind unkompliziert. Die Sicherungen sind optimiert und können bei Bedarf problemlos wiederhergestellt werden.

Im Rahmen des Sicherungsvorgangs wird eine Momentaufnahme erstellt, und die Daten werden ohne Auswirkung auf Produktionsworkloads in den Recovery Services-Tresor übertragen. Die Momentaufnahme bietet einen anderen Grad an Konsistenz, wie im Artikel [Konsistenz von Momentaufnahmen](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency) beschrieben. Sie können sich auch dafür entscheiden, eine Teilmenge der Datenträger einer VM zu sichern, indem Sie die Sicherungs- und Wiederherstellungsfunktionen für selektive Datenträger verwenden. Weitere Informationen finden Sie im Dokument [Azure-VM-Sicherung](../../../backup/backup-azure-vms-introduction.md) und unter [Häufig gestellte Fragen: Sichern von virtuellen Azure-Computern](../../../backup/backup-azure-vm-backup-faq.md).

#### <a name="backup--restore-for-file-repository-server"></a>Sicherung und Wiederherstellung für File Repository Server

Für **Azure NetApp Files** können Sie eine On-Demand-Momentaufnahme erstellen und mithilfe von Momentaufnahmerichtlinien eine automatische Momentaufnahme planen. Momentaufnahmekopien stellen eine Zeitpunktkopie Ihres ANF-Volumes dar. Weitere Informationen finden Sie unter [Verwalten von Momentaufnahmen mithilfe von Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md).

Die **Azure Files**-Sicherung ist in den nativen [Azure Backup](../../../backup/backup-overview.md)-Dienst integriert, der die Sicherungs- und Wiederherstellungsfunktion zusammen mit der Sicherung von VMs zentralisiert und die Abläufe vereinfacht. Weitere Informationen finden Sie unter [Sichern von Azure-Dateifreigaben](../../../backup/azure-file-share-backup-overview.md) und [Häufig gestellte Fragen zu Azure Files](../../../backup/backup-azure-files-faq.md).

#### <a name="backup--restore-for-cms-database"></a>Sicherung und Wiederherstellung für CMS-Datenbank

Azure Database for MySQL bietet DBaaS in Azure, wodurch automatisch Serversicherungen erstellt und in einem vom Benutzer konfigurierten lokal redundanten oder georedundanten Speicher gespeichert werden. Azure Database for MySQL nimmt Sicherungen der Datendateien und der Transaktionsprotokolle vor. Abhängig von der unterstützten maximalen Speichergröße werden entweder vollständige oder differenzielle Sicherungen (Server mit maximal 4 TB Speicher) oder Sicherungsmomentaufnahmen (Server mit bis zu 16 TB Speicher) angelegt. Diese Sicherungen ermöglichen es Ihnen, einen Server zu jedem Zeitpunkt innerhalb des von Ihnen konfigurierten Aufbewahrungszeitraums für Sicherungen wiederherzustellen. Der standardmäßige Aufbewahrungszeitraum für Sicherungen beträgt sieben Tage, den Sie [optional auf drei Tage festlegen können](../../../mysql/howto-restore-server-portal.md#set-backup-configuration). Zur Verschlüsselung aller Sicherungen wird die AES-Verschlüsselung mit 256 Bit verwendet.

Diese Sicherungsdateien sind nicht für den Benutzer verfügbar und können nicht exportiert werden. Sie können nur für Wiederherstellungsvorgänge in Azure Database for MySQL verwendet werden. Zum Kopieren einer Datenbank können Sie [mysqldump](../../../mysql/concepts-migrate-dump-restore.md) verwenden. Weitere Informationen finden Sie unter [Sicherung und Wiederherstellung in Azure Database for MySQL](../../../mysql/concepts-backup.md).

Für Datenbanken, die auf virtuellen Computern installiert sind, können Sie standardmäßige Sicherungstools oder [Azure Backup](../../../backup/sap-hana-db-about.md) für die HANA-Datenbank verwenden. Wenn die Azure-Dienste und -Tools nicht Ihren Anforderungen entsprechen, können Sie zudem andere Sicherungstools oder Skripts verwenden, um Datenträgersicherungen zu erstellen.

## <a name="sap-businessobjects-bi-platform-reliability"></a>Zuverlässigkeit der SAP BusinessObjects BI-Plattform

Die SAP BusinessObjects BI-Plattform umfasst unterschiedliche Ebenen, die für bestimmte Aufgaben und Vorgänge optimiert sind. Wenn eine Komponente einer Ebene nicht mehr verfügbar ist, kann auf die SAP BOBI-Anwendung entweder nicht mehr zugegriffen werden oder bestimmte Funktionen der Anwendung funktionieren nicht mehr. Es muss daher sichergestellt werden, dass jede Ebene so ausgelegt ist, dass sie zuverlässig ist, um die Anwendung ohne Unterbrechung beim Geschäftsbetrieb betriebsbereit zu halten.

In diesem Abschnitt werden die folgenden Optionen für die SAP BOBI-Plattform behandelt:

- **Hochverfügbarkeit:** Eine Plattform mit Hochverfügbarkeit verfügt innerhalb der Azure-Region von jeder Komponente über mindestens zwei Kopien, damit die Anwendung betriebsbereit bleibt, wenn einer der Server nicht mehr verfügbar ist.
- **Notfallwiederherstellung:** Es ist ein Prozess zur Wiederherstellung der Azure-Funktionalität Ihrer Anwendung, wenn es in einer Notfallsituation zu einem Ausfall kommt, z. B. wenn die gesamte Azure-Region aufgrund einer Naturkatastrophe nicht mehr verfügbar ist.

Die Implementierung dieser Lösung variiert je nach Art des Systemsetups in Azure. Daher müssen Kunden ihre Lösung für Hochverfügbarkeit und Notfallwiederherstellung an ihre Geschäftsanforderungen anpassen.

### <a name="high-availability"></a>Hochverfügbarkeit

Hochverfügbarkeit bezieht sich auf eine Reihe von Technologien, die IT-Störungen minimieren kann, indem sie die Geschäftskontinuität von Anwendungen/Diensten durch redundante, fehlertolerante oder durch Failover geschützte Komponenten innerhalb desselben Rechenzentrums bereitstellen. In unserem Fall befindet sich die Rechenzentren in einer Azure-Region. Der Artikel [Architektur und Szenarien für die Hochverfügbarkeit für SAP](sap-high-availability-architecture-scenarios.md) bietet einen ersten Einblick in verschiedene Techniken zur Hochverfügbarkeit und Empfehlungen für Azure für SAP-Anwendungen, die die Anleitungen in diesem Abschnitt ergänzen.

Basierend auf dem Dimensionierungsergebnis der SAP BOBI-Plattform müssen Sie die Umgebung entwerfen und die Verteilung der BI-Komponenten auf Azure Virtual Machines und Subnetze bestimmen. Das Maß der Redundanz in der verteilten Architektur hängt von dem geschäftlich erforderlichen RTO (Recovery Time Objective) und RPO (Recovery Point Objective) ab. Die SAP BOBI-Plattform umfasst verschiedene Ebenen, und die Komponenten auf den einzelnen Ebenen sollten so konzipiert sein, dass Redundanz erreicht wird. Wenn bei einer Komponente ein Fehler auftritt, gibt es nur wenige oder keine Unterbrechungen für Ihre SAP BOBI-Anwendung. Beispiel:

- Redundante Anwendungsserver wie BI-Anwendungsserver und -Webserver
- Eindeutige Komponenten wie CMS-Datenbank, File Repository Server, Load Balancer

Im folgenden Abschnitt wird beschrieben, wie Sie für jede Komponente der SAP BOBI-Plattform Hochverfügbarkeit erreichen können.

#### <a name="high-availability-for-application-servers"></a>Hochverfügbarkeit für Anwendungsserver

Für BI- und Webanwendungsserver (ob getrennt oder zusammen installiert), ist keine bestimmte Lösung für Hochverfügbarkeit erforderlich. Sie können Hochverfügbarkeit durch Redundanz erreichen, d. h. durch die Konfiguration mehrerer Instanzen von BI- und Webservern in verschiedenen Azure Virtual Machines-Instanzen.

Um die Auswirkungen von Downtime aufgrund eines oder mehrerer Ereignisse zu reduzieren, ist es ratsam, die folgende Methode für Hochverfügbarkeit für die Anwendungsserver zu befolgen, die auf mehreren virtuellen Computern ausgeführt werden.

- Verwenden Sie Verfügbarkeitszonen, um vor Ausfällen des Rechenzentrums zu schützen.
- Konfigurieren Sie mehrere virtuelle Computer in einer Verfügbarkeitsgruppe für höhere Redundanz.
- Verwenden Sie verwaltete Datenträger für virtuelle Computer in einer Verfügbarkeitsgruppe.
- Konfigurieren Sie die einzelnen Logikschichten in separate Verfügbarkeitsgruppen.

Weitere Informationen finden Sie unter [Verwalten der Verfügbarkeit virtueller Linux-Computer](../../manage-availability.md).

#### <a name="high-availability-for-cms-database"></a>Hochverfügbarkeit für die CMS-Datenbank

Wenn Sie den Azure-DBaaS-Dienst (Database-as-a-Service) für die CMS-Datenbank verwenden, wird standardmäßig ein Hochverfügbarkeitsframework bereitgestellt. Sie müssen nur die Region und den Dienst auswählen, der Funktionen für Hochverfügbarkeit, Redundanz und Resilienz bietet, ohne dass Sie zusätzliche Komponenten konfigurieren müssen. Weitere Informationen zur Vereinbarung zum Servicelevel des unterstützten DBaaS-Angebots in Azure finden Sie unter [Hochverfügbarkeit in Azure Database for MySQL](../../../mysql/concepts-high-availability.md) und [Hochverfügbarkeit für Azure SQL-Datenbank](../../../azure-sql/database/high-availability-sla.md).

Weitere Informationen zur Bereitstellung von DBMS für CMS-Datenbanken finden Sie in den [DBMS-Bereitstellungshandbüchern für SAP-Workloads](dbms_guide_general.md), die einen Einblick in die verschiedenen DBMS-Bereitstellungen und ihren Ansatz zum Erzielen von Hochverfügbarkeit bietet.

#### <a name="high-availability-for-file-repository-server"></a>Hochverfügbarkeit für File Repository Server

File Repository Server (FRS) bezieht sich auf die Datenträgerverzeichnisse, in denen Inhalte wie Berichte, Universen und Verbindungen gespeichert werden. Es wird von allen Anwendungsservern dieses Systems gemeinsam genutzt. Daher müssen Sie sicherstellen, dass es über Hochverfügbarkeit verfügt.

In Azure können Sie für die Dateifreigabe entweder [Azure Files Premium](../../../storage/files/storage-files-introduction.md) oder [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) auswählen, die für Hochverfügbarkeit und Langlebigkeit konzipiert sind. Weitere Informationen finden Sie im Abschnitt [Redundanz](../../../storage/files/storage-files-planning.md#redundancy) für Azure Files.

> [!NOTE]
> Das SMB-Protokoll für Azure Files ist allgemein verfügbar, aber die Unterstützung des NFS-Protokolls für Azure Files befindet sich derzeit in der Vorschauversion. Weitere Informationen finden Sie unter [Unterstützung von NFS 4.1 für Azure Files befindet sich jetzt in der Vorschau](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/)

Da dieser Dateifreigabedienst nicht in allen Regionen verfügbar ist, stellen Sie sicher, dass Sie auf der Website [Verfügbare Produkte nach Region](https://azure.microsoft.com/en-us/global-infrastructure/services/) nach aktuellen Informationen suchen. Wenn der Dienst in Ihrer Region nicht verfügbar ist, können Sie einen NFS-Server erstellen, über den Sie das Dateisystem für die SAP BOBI-Anwendung freigeben können. Sie müssen aber auch seine Hochverfügbarkeit berücksichtigen.

#### <a name="high-availability-for-load-balancer"></a>Hochverfügbarkeit für den Lastenausgleich

Für die Verteilung des Datenverkehrs über Webserver können Sie entweder Azure Load Balancer oder Azure Application Gateway verwenden. Die Redundanz für einen der beiden Lastenausgleiche kann auf der Grundlage der SKU erreicht werden, die Sie für die Bereitstellung auswählen.

- Für Azure Load Balancer kann Redundanz erreicht werden, indem das Front-End von Load Balancer Standard als zonenredundant konfiguriert wird. Weitere Informationen finden Sie unter [Load Balancer Standard und Verfügbarkeitszonen](../../../load-balancer/load-balancer-standard-availability-zones.md).
- Für Application Gateway kann Hochverfügbarkeit basierend auf der Art der während der Bereitstellung ausgewählten Ebene erreicht werden.
  - Die v1-SKU unterstützt Szenarien mit Hochverfügbarkeit, wenn Sie mindestens zwei Instanzen bereitgestellt haben. Azure verteilt diese Instanzen auf Update- und Fehlerdomänen, um sicherzustellen, dass nicht alle Instanzen gleichzeitig ausfallen. Mit dieser SKU kann die Redundanz in der Zone erreicht werden.
  - Die v2-SKU stellt automatisch sicher, dass neue Instanzen über Fehlerdomänen und Updatedomänen hinweg verteilt werden. Haben Sie Zonenredundanz gewählt, werden die neuesten Instanzen darüber hinaus über Verfügbarkeitszonen hinweg verteilt, um Resilienz gegen Zonenausfälle zu erreichen. Ausführlichere Informationen finden Sie unter [Automatische Skalierung und zonenredundantes Application Gateway v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md).

#### <a name="reference-high-availability-architecture-for-sap-businessobjects-bi-platform"></a>Referenzarchitektur für Hochverfügbarkeit für die SAP BusinessObjects BI-Plattform

Die folgende Referenzarchitektur beschreibt die Einrichtung der SAP BOBI-Plattform mithilfe von Verfügbarkeitsgruppen, die für virtuelle Computer Redundanz und Verfügbarkeit innerhalb der Zone bieten. Die Architektur zeigt die Verwendung verschiedener Azure-Dienste wie Azure Application Gateway, Azure NetApp Files und Azure Database for MySQL für die SAP BOBI-Plattform, die integrierte Redundanz bieten, was die Komplexität der Verwaltung verschiedener Lösungen für Hochverfügbarkeit reduziert.

In der folgenden Abbildung erfolgt der Lastenausgleich für den eingehenden Datenverkehr (HTTPS – TCP/443) mithilfe der Azure Application Gateway v1-SKU, die bei der Bereitstellung auf zwei oder mehr Instanzen eine entsprechende Hochverfügbarkeit aufweist. Mehrere Instanzen von Webservern, Verwaltungsservern und Verarbeitungsservern werden auf separaten virtuellen Computern bereitgestellt, um Redundanz zu erreichen, und jede Ebene wird in separaten Verfügbarkeitsgruppen bereitgestellt. Azure NetApp Files verfügt über integrierte Redundanz innerhalb des Rechenzentrums, sodass Ihre ANF-Volumes für File Repository Server hochverfügbar sind. Die CMS-Datenbank wird für Azure Database for MySQL (DBaaS) bereitgestellt, das über eine inhärente Hochverfügbarkeit verfügt. Weitere Informationen finden Sie im Leitfaden [Hochverfügbarkeit in Azure Database for MySQL](../../../mysql/concepts-high-availability.md).

![Redundanz für die SAP BusinessObjects BI-Plattform mithilfe von Verfügbarkeitsgruppen](media/businessobjects-deployment-guide/businessobjects-deployment-high-availability.png)

Die obige Architektur bietet einen Einblick, wie die SAP BOBI-Bereitstellung in Azure erfolgen kann. Aber sie behandelt nicht alle möglichen Konfigurationsoptionen für die SAP BOBI-Plattform in Azure. Kunden können ihre Bereitstellung auf der Grundlage ihrer Geschäftsanforderungen anpassen, indem sie verschiedene Produkte/Dienste für verschiedene Komponenten wie Load Balancer, File Repository Server und DBMS auswählen.

In mehreren Azure-Regionen werden Verfügbarkeitszonen angeboten, was bedeutet, dass sie über eine unabhängige Versorgung mit Energie, Kühlung und Netzwerken verfügen. Dadurch kann der Kunde die Anwendung in zwei oder drei Verfügbarkeitszonen bereitstellen. Für Kunden, die Hochverfügbarkeit über Verfügbarkeitszonen hinweg erreichen möchten, kann die SAP BOBI-Plattform über Verfügbarkeitszonen hinweg bereitgestellt werden, wobei sichergestellt wird, dass jede Komponente in der Anwendung zonenredundant ist.

### <a name="disaster-recovery"></a>Notfallwiederherstellung

Die Anleitung in diesem Abschnitt erläutert die Strategie zur Bereitstellung von Schutz für die Notfallwiederherstellung für die SAP BOBI-Plattform. Sie ergänzt das Dokument [Notfallwiederherstellung für SAP](../../../site-recovery/site-recovery-sap.md), das die primären Ressourcen für den gesamten SAP-Ansatz zur Notfallwiederherstellung darstellt.

#### <a name="reference-disaster-recovery-architecture-for-sap-businessobjects-bi-platform"></a>Referenzarchitektur für Notfallwiederherstellung für die SAP BusinessObjects BI-Plattform

Diese Referenzarchitektur führt eine Bereitstellung mit mehreren Instanzen der SAP BOBI-Plattform mit redundanten Anwendungsservern aus. Für die Notfallwiederherstellung sollten Sie auf allen Ebenen ein Failover in eine sekundäre Region ausführen. Für jede Ebene wird eine andere Strategie genutzt, um per Notfallwiederherstellung für den erforderlichen Schutz zu sorgen.

![SAP BusinessObjects BI-Plattform: Notfallwiederherstellung](media/businessobjects-deployment-guide/businessobjects-deployment-disaster-recovery.png)

#### <a name="load-balancer"></a>Load Balancer

Load Balancer wird verwendet, um Datenverkehr über Webanwendungsserver der SAP BOBI-Plattform zu verteilen. Um die Notfallwiederherstellung für Azure Application Gateway zu erreichen, implementieren Sie eine parallele Einrichtung von Application Gateway in der sekundären Region.

#### <a name="virtual-machines-running-web-and-bi-application-servers"></a>Virtuelle Computer mit Web- und BI-Anwendungsservern

Der Azure Site Recovery-Dienst kann verwendet werden, um virtuelle Computer, die Web- und BI-Anwendungsserver ausführen, in der sekundären Region zu replizieren. Er repliziert die Server in der sekundären Region, sodass Sie bei Notfällen und Ausfällen leicht ein Failover zu Ihrer replizierten Umgebung ausführen und weiterarbeiten können.

#### <a name="file-repository-servers"></a>File Repository Server

- **Azure NetApp Files** stellt NFS- und SMB-Volumes bereit, sodass jedes dateibasierte Kopiertool zur Replikation von Daten zwischen Azure-Regionen verwendet werden kann. Weitere Informationen zum Kopieren eines ANF-Volumes in eine andere Region finden Sie unter [Häufig gestellte Fragen zu Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region).

  Sie können die regionsübergreifende Replikation von Azure NetApp Files verwenden, die sich derzeit in der [Vorschauversion](https://azure.microsoft.com/en-us/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/) befindet und die NetApp SnapMirror®-Technologie verwendet. Daher werden nur geänderte Blöcke in einem komprimierten, effizienten Format über das Netzwerk gesendet. Diese proprietäre Technologie minimiert die Menge an Daten, die regionsübergreifend repliziert werden muss, wodurch Datenübertragungskosten eingespart werden. Auch die Replikationszeit verkürzt sich dadurch, sodass Sie eine kürzere Recovery Point Objective (RPO) erreichen können. Weitere Informationen finden Sie unter [Regionsübergreifende Replikation: Anforderungen und Überlegungen](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md).

- **Azure Files Premium** unterstützt nur lokal redundanten Speicher (LRS) und zonenredundanten Speicher (ZRS). Für die Azure Files Premium-Strategie zur Notfallwiederherstellung können Sie [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) oder [Azure PowerShell](/powershell/module/az.storage/) verwenden, um die Dateien in ein anderes Speicherkonto in einer anderen Region zu kopieren. Weitere Informationen finden Sie unter [Notfallwiederherstellung und Speicherkontofailover](../../../storage/common/storage-disaster-recovery-guidance.md).

#### <a name="cms-database"></a>CMS-Datenbank

Azure Database for MySQL bietet mehrere Optionen zum Wiederherstellen einer Datenbank im Notfall. Wählen Sie die entsprechende Option aus, die für Ihr Unternehmen geeignet ist.

- Aktivieren Sie regionsübergreifende Lesereplikate, um Ihre BCDR-Planung (Business Continuity & Disaster Recovery) zu verbessern. Sie können vom Quellserver auf bis zu fünf Replikate replizieren. Lesereplikate werden mithilfe der binären Protokollreplikation von MySQL asynchron aktualisiert. Replikate sind neue Server, die Sie ähnlich wie normale Azure Database for MySQL-Server verwalten. Weitere Informationen zu Lesereplikaten, zu verfügbaren Regionen, zu Einschränkungen und zum Failover finden Sie im [Konzeptartikel zu Lesereplikaten](../../../mysql/concepts-read-replicas.md).

- Verwenden Sie das Geowiederherstellungsfeature von Azure Database for MySQL, das den Server mithilfe von georedundanten Sicherungen wiederherstellt. Auf diese Sicherungen kann selbst dann zugegriffen werden, wenn die Region, in der Ihr Server gehostet wird, offline ist. Sie können eine Wiederherstellung aus diesen Sicherungen in eine andere Region ausführen und den Server wieder online schalten.

  > [!NOTE]
  > Die Geowiederherstellung ist nur möglich, wenn Sie den Server mit einem georedundanten Sicherungsspeicher bereitgestellt haben. Das Ändern der **Optionen für Sicherungsredundanz** nach der Erstellung des Servers wird nicht unterstützt. Weitere Informationen finden Sie im Artikel [Sicherungsredundanz](../../../mysql/concepts-backup.md#backup-redundancy-options).

Nachfolgend ist die Empfehlung für eine Notfallwiederherstellung der einzelnen Ebenen angegeben, die in diesem Beispiel verwendet werden.

| Ebenen der SAP BOBI-Plattform   | Empfehlung                                                                                           |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| Azure Application Gateway | Paralleles Einrichten von Application Gateway in der sekundären Region                                                |
| Webanwendungsserver   | Replizieren über Site Recovery                                                                         |
| BI-Anwendungsserver    | Replizieren über Site Recovery                                                                         |
| Azure NetApp Files        | Dateibasiertes Kopiertool zum Replizieren von Daten in die sekundäre Region **oder** für die regionsübergreifende ANF-Replikation (Vorschau) |
| Azure Database for MySQL  | Regionsübergreifende Lesereplikate **oder** Wiederherstellung einer Sicherung aus georedundanten Sicherungen.                             |

## <a name="next-steps"></a>Nächste Schritte

- [Einrichten der Notfallwiederherstellung für die Bereitstellung einer SAP-App mit mehreren Ebenen](../../../site-recovery/site-recovery-sap.md)
- [Azure Virtual Machines – Planung und Implementierung für SAP](planning-guide.md)
- [Azure Virtual Machines – Bereitstellung für SAP](deployment-guide.md)
- [Azure Virtual Machines – DBMS-Bereitstellung für SAP](./dbms_guide_general.md)