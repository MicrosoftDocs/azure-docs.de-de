---
title: 'Azure VMware Solution by CloudSimple: Sichern virtueller Workloadcomputer in der privaten Cloud mit Veeam'
description: Beschreibt, wie Sie Ihre virtuellen Computer mit Veeam B&R 9.5 sichern können, die in einer Azure-basierten privaten CloudSimple-Cloud ausgeführt werden.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cb883b70d1ae9f2894c7679633a586586922aa18
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92419946"
---
# <a name="back-up-workload-vms-on-cloudsimple-private-cloud-using-veeam-br"></a>Sichern von Workload-VMs in einer privaten CloudSimple-Cloud mithilfe von Veeam B&R

In diesem Leitfaden wird beschrieben, wie Sie Ihre virtuellen Computer mit Veeam B&R 9.5 sichern können, die in einer Azure-basierten privaten CloudSimple-Cloud ausgeführt werden.

## <a name="about-the-veeam-back-up-and-recovery-solution"></a>Informationen zur Veeam-Sicherungs- und -Wiederherstellungslösung

Die Veeam-Lösung umfasst die folgenden Komponenten.

**Sicherungsserver**

Der Sicherungsserver ist ein Windows-Server (VM), der als Steuerungszentrum für Veeam fungiert und die folgenden Funktionen ausführt: 

* Koordinieren von Sicherung, Replikation, Wiederherstellungsüberprüfung und Wiederherstellungsaufgaben.
* Steuern der Auftragsplanung und der Ressourcenzuordnung.
* Ermöglicht das Einrichten und Verwalten von Komponenten der Sicherungsinfrastruktur und das Angeben globaler Einstellungen für die Sicherungsinfrastruktur.

**Proxyserver**

Proxy Server werden zwischen dem Sicherungsserver und anderen Komponenten der Sicherungsinfrastruktur installiert. Sie verwalten die folgenden Funktionen:

* Abrufen von VM-Daten aus dem Produktionsspeicher.
* Komprimierung
* Deduplizierung
* Verschlüsselung
* Übertragung von Daten in das Sicherungsrepository.

**Sicherungsrepository**

Das Sicherungsrepository ist der Speicherort, an dem Veeam Sicherungsdateien, VM-Kopien und Metadaten für replizierte VMS speichert.  Das Repository kann ein Windows- oder Linux-Server mit lokalen Datenträgern (oder bereitgestelltem NFS/SMB) oder ein Hardware-Speicherdeduplizierungsgerät sein.

### <a name="veeam-deployment-scenarios"></a>Veeam-Bereitstellungsszenarien
Sie können Azure nutzen, um ein Sicherungsrepository und ein Speicherziel für die langfristige Sicherung und Archivierung bereitzustellen. Der gesamte Netzwerkdatenverkehr für Sicherungen zwischen VMs in der privaten Cloud und dem Sicherungsrepository in Azure erfolgt über eine Verbindung mit hoher Bandbreite und geringer Latenz. Der Replikationsdatenverkehr zwischen Regionen verläuft über das interne Azure-Backplanenetzwerk und verringert so Bandbreitenkosten für Benutzer.

**Grundlegende Bereitstellung**

Für Umgebungen, in denen weniger als 30 TB gesichert werden müssen, empfiehlt CloudSimple die folgende Konfiguration:

* Veeam-Serversserver und Proxyserver sind auf derselben VM in der privaten Cloud installiert.
* Ein Linux-basiertes primäres Sicherungsrepository in Azure, das als Ziel für Sicherungsaufträge konfiguriert ist.
* `azcopy` wird verwendet, um die Daten aus dem primären Sicherungsrepository in einen Azure-Blobcontainer zu kopieren, der in eine andere Region repliziert wird.

![Diagramm grundlegender Bereitstellungsszenarien für Veeam.](media/veeam-basicdeployment.png)

**Erweiterte Bereitstellung**

Für Umgebungen, in denen mehr als 30 TB gesichert werden müssen, empfiehlt CloudSimple die folgende Konfiguration:

* Ein Proxyserver pro Knoten im vSAN-Cluster, wie von Veeam empfohlen.
* Windows-basiertes primäres Sicherungsrepository in der privaten Cloud zum Zwischenspeichern von Daten aus fünf Tagen für schnelle Wiederherstellungen.
* Ein Linux-Sicherungsrepository in Azure als Ziel für Sicherungskopieaufträge für eine längere Beibehaltungsdauer. Dieses Repository sollte als Sicherungsrepository für horizontale Skalierung konfiguriert werden.
* `azcopy` wird verwendet, um die Daten aus dem primären Sicherungsrepository in einen Azure-Blobcontainer zu kopieren, der in eine andere Region repliziert wird.

![Grundlegende Bereitstellungsszenarien](media/veeam-advanceddeployment.png)

Beachten Sie in der Abbildung oben, dass der Sicherungsproxy ein virtueller Computer mit Hot-Add-Zugriff auf Datenträger von Workload-VMs im vSAN-Datenspeicher ist. Veeam verwendet den Sicherungsproxy-Transportmodus „Virtual Appliance“ für vSAN.

## <a name="requirements-for-veeam-solution-on-cloudsimple"></a>Anforderungen für die Veeam-Lösung für CloudSimple

Die Veeam-Lösung erfordert, dass Sie wie folgt vorgehen:

* Bereitstellen Ihrer eigenen Veeam-Lizenzen.
* Bereitstellen und Verwalten von Veeam zum Sichern der Workloads, die in der privaten CloudSimple-Cloud ausgeführt werden.

Diese Lösung bietet Ihnen die vollständige Kontrolle über das Veeam-Sicherungstool und die Möglichkeit, die native Veeam-Schnittstelle oder das Veeam vCenter-Plug-In zum Verwalten von VM-Sicherungsaufträgen zu verwenden.

Wenn Sie bereits Benutzer von Veeam sind, können Sie den Abschnitt zu den Veeam-Lösungskomponenten überspringen und direkt mit [Veeam-Bereitstellungsszenarien](#veeam-deployment-scenarios) fortfahren.

## <a name="install-and-configure-veeam-backups-in-your-cloudsimple-private-cloud"></a>Installieren und Konfigurieren von Veeam-Sicherungen in Ihrer privaten CloudSimple-Cloud

In den folgenden Abschnitten wird beschrieben, wie Sie eine Veeam-Sicherungslösung für Ihre private CloudSimple-Cloud installieren und konfigurieren.

Der Bereitstellungsvorgang besteht aus den folgenden Schritten:

1. [vCenter-Benutzeroberfläche: Einrichten von Infrastrukturdiensten in Ihrer privaten Cloud](#vcenter-ui-set-up-infrastructure-services-in-your-private-cloud)
2. [CloudSimple-Portal: Einrichten privater Cloudnetzwerke für Veeam](#cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam)
3. [CloudSimple-Portal: Eskalieren von Berechtigungen](#cloudsimple-private-cloud-escalate-privileges-for-cloudowner)
4. [Azure-Portal: Herstellen einer Verbindung Ihres virtuellen Netzwerks mit der privaten Cloud](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
5. [Azure-Portal: Erstellen eines Sicherungsrepositorys in Azure](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
6. [Azure-Portal: Konfigurieren von Azure-Blobspeicher für die langfristige Datenaufbewahrung](#configure-azure-blob-storage-for-long-term-data-retention)
7. [vCenter-Benutzeroberfläche der privaten Cloud: Installieren von Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br)
8. [Veeam-Konsole: Konfigurieren der Veeam-Sicherungs- und -Wiederherstellungssoftware](#veeam-console-install-veeam-backup-and-recovery-software)
9. [CloudSimple-Portal: Einrichten von Veeam-Zugriff und Einschränken von Berechtigungen](#cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges)

### <a name="before-you-begin"></a>Voraussetzungen

Folgendes ist erforderlich, bevor Sie mit der Veeam-Bereitstellung beginnen können:

* Ein Azure-Abonnement, dessen Besitzer Sie sind.
* Eine vorab Erstellte Azure-Ressourcengruppe.
* Ein virtuelles Azure-Netzwerk in Ihrem Abonnement.
* Ein Azure-Speicherkonto
* Eine [private Cloud](create-private-cloud.md), die mithilfe des CloudSimple-Portals erstellt wurde.  

Die folgenden Elemente werden während der Implementierungsphase benötigt:

* VMware-Vorlagen für Windows zum Installieren von Veeam (z.B. Windows Server 2012 R2, 64-Bit-Image)
* Ein verfügbares VLAN für das Sicherungsnetzwerk
* CIDR des Subnetzes, das dem Sicherungsnetzwerk zugewiesen werden soll
* Installierbare Veeam 9.5 u3-Medien (ISO), die in den vSAN-Datenspeicher der privaten Cloud hochgeladen wurden

### <a name="vcenter-ui-set-up-infrastructure-services-in-your-private-cloud"></a>vCenter-Benutzeroberfläche: Einrichten von Infrastrukturdiensten in Ihrer privaten Cloud

Konfigurieren Sie Infrastrukturdienste in der privaten Cloud, um die Verwaltung von Workloads und Tools zu vereinfachen.

* Sie können einen externen Identitätsanbieter wie unter [Einrichten von vCenter-Identitätsquellen für die Verwendung von Active Directory](set-vcenter-identity.md) beschrieben hinzufügen, wenn eine der folgenden Bedingungen zutrifft:

  * Sie möchten Benutzer aus Ihrem lokalen Active Directory (AD) in Ihrer privaten Cloud identifizieren.
  * Sie möchten ein AD für alle Benutzer in Ihrer privaten Cloud einrichten.
  * Sie möchten Azure AD verwenden.
* Um das Nachschlagen von IP-Adressen, IP-Adressverwaltung und Namensauflösungsdienste für Ihre Workloads in der privaten Cloud bereitzustellen, richten Sie einen DHCP- und DNS-Server ein, wie unter [Einrichten von DNS- und DHCP-Anwendungen und -Workloads in Ihrer privaten CloudSimple-Cloud](dns-dhcp-setup.md) beschrieben.

### <a name="cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam"></a>Private CloudSimple-Cloud: Einrichten privater Cloudnetzwerke für Veeam

Greifen Sie auf das CloudSimple-Portal zu, um das private Cloudnetzwerk für die Veeam-Lösung einzurichten.

Erstellen Sie ein VLAN für das Sicherungsnetzwerk, und weisen Sie ihm einen Subnetz-CIDR-Wert zu. Anweisungen dazu finden Sie unter [Erstellen und Verwalten von VLANs/Subnetzen](create-vlan-subnet.md).

Erstellen Sie Firewallregeln zwischen dem Verwaltungssubnetz und dem Sicherungsnetzwerk, um Netzwerkdatenverkehr an von Veeam verwendeten Ports zuzulassen. Weitere Informationen finden Sie im Veeam-Thema zu [verwendeten Ports](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95). Anweisungen zum Erstellen von Firewallregeln finden Sie unter [Einrichten von Firewalltabellen und -regeln](firewall.md).

Die folgende Tabelle enthält eine Portliste.

| Symbol | BESCHREIBUNG | Symbol | BESCHREIBUNG |
| ------------ | ------------- | ------------ | ------------- |
| Sicherungsserver  | vCenter  | HTTPS/TCP  | 443 |
| Sicherungsserver <br> *Erforderlich zum Bereitstellen von Veeam-Sicherungs- und -Replikationskomponenten* | Sicherungsproxy  | TCP/UDP  | 135, 137 bis 139 und 445 |
    | Sicherungsserver   | DNS  | UDP  | 53  | 
    | Sicherungsserver   | Veeam-Aktualisierungsbenachrichtigungsserver  | TCP  | 80  | 
    | Sicherungsserver   | Veeam-Lizenzaktualisierungsserver  | TCP  | 443  | 
    | Sicherungsproxy   | vCenter |   |   | 
    | Sicherungsproxy  | Linux-Sicherungsrepository   | TCP  | 22  | 
    | Sicherungsproxy  | Windows-Sicherungsrepository  | TCP  | 49152 bis 65535   | 
    | Sicherungsrepository  | Sicherungsproxy  | TCP  | 2500 bis 5000  | 
    | Quellsicherungsrepository<br> *Wird für Sicherungskopieaufträge verwendet*  | Zielsicherungsrepository  | TCP  | 2500 bis 5000  | 

Erstellen Sie Firewallregeln zwischen dem Workloadsubnetz und dem Sicherungsnetzwerk, wie unter [Einrichten von Firewalltabellen und -regeln](firewall.md) beschrieben.  Für anwendungsfähige Sicherung und Wiederherstellung müssen [zusätzliche Ports](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95) auf den Workload-VMS geöffnet werden, die bestimmte Anwendungen hosten.

CloudSimple bietet standardmäßig eine ExpressRoute-Verbindung mit 1 GBit/s. Für größere Umgebungen ist möglicherweise eine Verbindung mit höherer Bandbreite erforderlich. Wenden Sie sich an den Azure-Support, um weitere Informationen zu Verbindungen mit einer höheren Bandbreite zu erhalten.

Um das Setup fortzusetzen, benötigen Sie den Autorisierungsschlüssel und den Peer-Verbindungs-URI sowie Zugriff auf Ihr Azure-Abonnement.  Diese Informationen sind im CloudSimple-Portal auf der Seite „Virtual Network Connection“ (Virtuelle Netzwerkverbindung) verfügbar. Anleitungen finden Sie unter [Abrufen von Peeringinformationen für eine Verbindung zwischen Azure Virtual Network und CloudSimple](virtual-network-connection.md). Wenn Sie Probleme haben, diese Informationen abzurufen, [wenden Sie sich an den Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="cloudsimple-private-cloud-escalate-privileges-for-cloudowner"></a>Private CloudSimple-Cloud: Eskalieren von Berechtigungen für „cloudowner“

Der Standardbenutzer „cloudowner“ verfügt nicht über ausreichende Berechtigungen im vCenter der privaten Cloud für die Installation von Veeam, sodass die vCenter-Berechtigungen des Benutzers eskaliert werden müssen. Weitere Informationen finden Sie unter [Eskalieren von Berechtigungen](escalate-private-cloud-privileges.md).

### <a name="azure-portal-connect-your-virtual-network-to-the-private-cloud"></a>Azure-Portal: Herstellen einer Verbindung Ihres virtuellen Netzwerks mit der privaten Cloud

Stellen Sie eine Verbindung zwischen Ihrem virtuellen Netzwerk und der privaten Cloud her, indem Sie die Anweisungen unter [Azure Virtual Network-Verbindung mithilfe von ExpressRoute](azure-expressroute-connection.md) befolgen.

### <a name="azure-portal-create-a-backup-repository-vm"></a>Azure-Portal: Erstellen einer Sicherungsrepository-VM

1. Erstellen Sie eine D2 v3-Standard-VM (mit 2 vCPUs und 8 GB Arbeitsspeicher).
2. Wählen Sie das auf CentOS 7.4 basierende Image aus.
3. Konfigurieren Sie eine Netzwerksicherheitsgruppe (NSG) für den virtuellen Computer. Vergewissern Sie sich, dass der virtuelle Computer nicht über eine öffentliche IP-Adresse verfügt und über das öffentliche Internet nicht erreichbar ist.
4. Erstellen Sie einen Benutzernamen und ein kennwortbasiertes Benutzerkonto für die neue VM. Anleitungen dazu finden Sie unter [Erstellen eines virtuellen Linux-Computers im Azure-Portal](../virtual-machines/linux/quick-create-portal.md).
5. Erstellen Sie eines HDD Standard-Datenträgers mit 1x512 GiB, und fügen Sie ihn an die Repository-VM an.  Anleitungen dazu finden Sie unter [Anfügen eines verwalteten Datenträgers an eine Windows-VM im Azure-Portal](../virtual-machines/windows/attach-managed-disk-portal.md).
6. [Erstellen Sie ein XFS-Volume auf dem verwalteten Datenträger](https://www.digitalocean.com/docs/volumes/how-to/). Melden Sie sich mit den zuvor erwähnten Anmeldeinformationen bei der VM an. Führen Sie das folgende Skript aus, um ein logisches Volume zu erstellen, fügen Sie den Datenträger hinzu, erstellen Sie eine [XFS-Dateisystempartition](https://www.digitalocean.com/docs/volumes/how-to/partition/), und [binden](https://www.digitalocean.com/docs/volumes/how-to/mount/) Sie die Partition unter dem Pfad „/backup1“ ein.

    Beispielskript:

    ```
    sudo pvcreate /dev/sdc
    sudo vgcreate backup1 /dev/sdc
    sudo lvcreate -n backup1 -l 100%FREE backup1
    sudo mkdir -p /backup1
    sudo chown veeamadmin /backup1
    sudo chmod 775 /backup1
    sudo mkfs.xfs -d su=64k -d sw=1 -f /dev/mapper/backup1-backup1
    sudo mount -t xfs /dev/mapper/backup1-backup1 /backup1
    ```

7. Stellen Sie „/backup1“ als NFS-Bereitstellungspunkt für den Veeam-Sicherungsserver bereit, der in der privaten Cloud ausgeführt wird. Anweisungen hierzu finden Sie im Digital Ocean-Artikel [How To Set Up an NFS Mount on CentOS 6](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6) (Einrichten eines NFS-Bereitstellungspunkts unter CentOS 6). Verwenden Sie diesen NFS-Freigabenamen, wenn Sie das Sicherungsrepository auf dem Veeam-Sicherungsserver konfigurieren.

8. Konfigurieren Sie Filterregeln in der NSG für die Sicherungsrepository-VM, um den gesamten Netzwerkdatenverkehr von der und zu der VM explizit zuzulassen.

> [!NOTE]
> Veeam Backup & Replication verwendet das SSH-Protokoll für die Kommunikation mit Linux-Sicherungstrepositorys und erfordert das SCP-Hilfsprogramm für Linux-Repositorys. Vergewissern Sie sich, dass der SSH-Daemon ordnungsgemäß konfiguriert ist und dass SCP auf dem Linux-Host verfügbar ist.

### <a name="configure-azure-blob-storage-for-long-term-data-retention"></a>Konfigurieren von Azure-Blobspeicher für die langfristige Datenaufbewahrung

1. Erstellen Sie ein universelles Speicherkonto (GPv2) des Standardtyps und einen Blobcontainer, wie im Microsoft-Video [Getting Started with Azure Storage](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage) (Erste Schritte mit Azure Storage) beschrieben.
2. Erstellen Sie einen Azure Storage-Container, wie in der Referenz [Erstellen eines Containers](/rest/api/storageservices/create-container) beschrieben.
2. Laden Sie das `azcopy`-Befehlszeilenhilfsprogramm für Linux von Microsoft herunter. Sie können die folgenden Befehle in der Bash-Shell in CentOS 7.5 verwenden.

    ```
    wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
    tar -xf azcopy.tar.gz
    sudo ./install.sh
    sudo yum -y install libunwind.x86_64
    sudo yum -y install icu
    ```

3. Verwenden Sie den Befehl `azcopy`, um Sicherungsdateien in den und aus dem Blobcontainer zu kopieren.  Ausführliche Informationen zu den Befehlen finden Sie unter [Übertragen von Daten mit AzCopy unter Linux](../storage/common/storage-use-azcopy-v10.md).

### <a name="vcenter-console-of-private-cloud-install-veeam-br"></a>vCenter-Konsole der privaten Cloud: Installieren von Veeam B&R

Greifen Sie über Ihre private Cloud auf vCenter zu, um ein Veeam-Dienstkonto zu erstellen, installieren Sie Veeam B&R 9.5, und konfigurieren Sie Veeam mithilfe des Dienstkontos.

1. Erstellen Sie eine neue Rolle mit dem Namen „Veeam Backup Role“ (Veeam-Sicherungsrolle), und weisen Sie ihr die erforderlichen Berechtigungen zu (wie von Veeam empfohlen). Weitere Informationen finden Sie im Veeam-Thema [Required Permissions](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95) (Erforderliche Berechtigungen).
2. Erstellen Sie eine neue Gruppe „Veeam User Group“ (Veeam-Benutzergruppe) in vCenter, und weisen Sie ihr die „Veeam Backup Role“ zu.
3. Erstellen Sie eines neues „Veeam Service Account“ (Veeam-Dienstkonto), und fügen Sie es der „Veeam User Group“ hinzu.

    ![Erstellen eines Veeam-Dienstkontos](media/veeam-vcenter01.png)

4. Erstellen Sie eine verteilte Portgruppe in vCenter mithilfe des Sicherungsnetzwerk-VLAN. Ausführliche Informationen finden Sie im VMware-Video [Creating a Distributed Port Group in the vSphere Web Client](https://www.youtube.com/watch?v=wpCd5ZbPOpA) (Erstellen einer verteilten Portgruppe im vSphere-Webclient).
5. Erstellen Sie die VMs für die Veeam-Sicherungs- und -Proxyserver in vCenter gemäß den [Systemanforderungen für Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95). Sie können Windows 2012 R2 oder Linux verwenden. Weitere Informationen finden Sie unter [Anforderungen für die Verwendung von Linux-Sicherungsrepositorys](https://www.veeam.com/kb2216).
6. Stellen Sie die installierbare Veeam-ISO-Datei als CDROM-Gerät in der Veeam-Sicherungsserver-VM bereit.
7. Wenn Sie eine RDP-Sitzung mit dem Windows 2012 R2-Computer (Ziel für die Veeam-Installation) verwenden, [installieren Sie Veeam B&R 9.5u3](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95) auf einer Windows 2012 R2-VM.
8. Suchen Sie die interne IP-Adresse der Veeam-Sicherungsserver-VM, und konfigurieren Sie die IP-Adresse auf dem DHCP-Server als statisch. Die genauen Schritte, die hierfür erforderlich sind, hängen vom DHCP-Server ab. Der Netgate-Artikel <a href="https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html" target="_blank">Static DHCP Mappings</a> (Statische DHCP-Zuordnungen) erläutert beispielsweise, wie ein DHCP-Server mit einem pfSense-Router konfiguriert wird.

### <a name="veeam-console-install-veeam-backup-and-recovery-software"></a>Veeam-Konsole: Installieren der Veeam-Sicherungs- und -Wiederherstellungssoftware

Konfigurieren Sie mithilfe der Veeam-Konsole die Veeam-Sicherungs- und -Wiederherstellungssoftware. Weitere Informationen finden Sie unter [Veeam Backup & Replication v9: Installation und Bereitstellung](https://www.youtube.com/watch?v=b4BqC_WXARk).

1. Fügen Sie VMware vSphere als verwaltete Serverumgebung hinzu. Wenn Sie dazu aufgefordert werden, geben Sie die Anmeldeinformationen des Veeam-Dienstkontos an, das Sie am Anfang mit [vCenter-Konsole der privaten Cloud: Installieren von Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br) erstellt haben.

    * Verwenden Sie die Standardeinstellungen für die Laststeuerung und die erweiterten Standardeinstellungen.
    * Legen Sie den Speicherort des Bereitstellungservers auf den Sicherungsserver fest.
    * Ändern Sie den Speicherort der Konfigurationssicherung für den Veeam-Server in das Remoterepository.

2. Fügen Sie den Linux-Server in Azure als Sicherungsrepository hinzu.

    * Verwenden Sie die Standardeinstellungen für die Laststeuerung und für die erweiterten Einstellungen. 
    * Legen Sie den Speicherort des Bereitstellungservers auf den Sicherungsserver fest.
    * Ändern Sie den Speicherort der Konfigurationssicherung für den Veeam-Server in das Remoterepository.

3. Aktivieren Sie die Verschlüsselung der Konfigurationssicherung mithilfe der Einstellungen **Home > Konfigurationssicherung**.

4. Fügen Sie eine Windows Server-VM als Proxyserver für die VMware-Umgebung hinzu. Verwenden Sie „Datenverkehrsregeln“ für einen Proxy, um Sicherungsdaten über das Netzwerk zu verschlüsseln.

5. Konfigurieren Sie Sicherungsaufträge.
    * Befolgen Sie die Anweisungen unter [Erstellen eines Sicherungsauftrags](https://www.youtube.com/watch?v=YHxcUFEss4M), um Sicherungsaufträge zu konfigurieren.
    * Aktivieren Sie die Verschlüsselung der Sicherungsdateien unter **Erweiterte Einstellungen > Speicher**.

6. Konfigurieren Sie Sicherungskopieaufträge.

    * Befolgen Sie die Anweisungen im Video [Creating a Backup Copy Job](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s) (Erstellen eines Sicherungskopieauftrags), um Sicherungskopieaufträge zu konfigurieren.
    * Aktivieren Sie die Verschlüsselung der Sicherungsdateien unter **Erweiterte Einstellungen > Speicher**.

### <a name="cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges"></a>CloudSimple-Portal: Einrichten von Veeam-Zugriff und Einschränken von Berechtigungen
Erstellen Sie eine öffentliche IP-Adresse für den Veeam-Sicherungs- und -Wiederherstellungsserver. Anweisungen hierzu finden Sie unter [Zuweisen von öffentlichen IP-Adressen](public-ips.md).

Erstellen Sie eine Firewallregel, um dem Veeam-Sicherungsserver zu erlauben, eine ausgehende Verbindung mit der Veeam-Website zum Herunterladen von Updates/Patches über TCP-Port 80 herzustellen. Anweisungen dazu finden Sie unter [Einrichten von Firewalltabellen und -regeln](firewall.md).

Informationen zum Einschränken von Berechtigungen finden Sie unter [Einschränken von Berechtigungen](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="references"></a>References

### <a name="cloudsimple-references"></a>CloudSimple-Referenzen

* [Erstellen einer privaten Cloud](create-private-cloud.md)
* [Erstellen und Verwalten von VLANs/Subnetzen](create-vlan-subnet.md)
* [vCenter-Identitätsquellen](set-vcenter-identity.md)
* [Workload-DNS und DHCP-Einrichtung](dns-dhcp-setup.md)
* [Eskalieren von Berechtigungen](escalate-privileges.md)
* [Einrichten von Firewalltabellen und -regeln](firewall.md)
* [Berechtigungen für private Cloud](learn-private-cloud-permissions.md)
* [Zuweisen öffentlicher IP-Adressen](public-ips.md)

### <a name="veeam-references"></a>Veeam-Referenzen

* [Verwendete Ports](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)
* [Erforderliche Berechtigungen](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)
* [Systemanforderungen](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)
* [Installieren von Veeam Backup & Replication](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
* [Erforderliche Module und Berechtigungen für die Unterstützung von Multi-OS FLR und Repositorys für Linux](https://www.veeam.com/kb2216)
* [Veeam Backup & Replication v9: Installation und Bereitstellung (Video)](https://www.youtube.com/watch?v=b4BqC_WXARk)
* [Veeam v9: Erstellen eines Sicherungsauftrags (Video)](https://www.youtube.com/watch?v=YHxcUFEss4M)
* [Veeam v9: Erstellen eines Sicherungskopieauftrags (Video)](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)

### <a name="azure-references"></a>Azure-Referenzen

* [Konfigurieren eines virtuellen Netzwerkgateways für ExpressRoute mit dem Azure-Portal](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
* [Verknüpfen eines VNET mit einer Verbindung – anderes Abonnement](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)
* [Erstellen eines virtuellen Linux-Computers im Azure-Portal](../virtual-machines/linux/quick-create-portal.md)
* [Anfügen eines verwalteten Datenträgers an eine Windows-VM im Azure-Portal](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Erste Schritte mit Azure Storage (Video)](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
* [Erstellen eines Containers](/rest/api/storageservices/create-container)
* [Übertragen von Daten mit AzCopy unter Linux](../storage/common/storage-use-azcopy-v10.md)

### <a name="vmware-references"></a>VMware-Referenzen

* [Erstellen einer verteilten Portgruppe im vSphere-Webclient](https://www.youtube.com/watch?v=wpCd5ZbPOpA) (Video)

### <a name="other-references"></a>Andere Referenzen

* [Erstellen eines XFS-Volume auf dem verwalteten Datenträger (RedHat)](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-xfs)
* [Einrichten eines NFS-Bereitstellungspunkts unter CentOS 7 (HowToForge)](https://www.howtoforge.com/nfs-server-and-client-on-centos-7)
* [Konfigurieren des DHCP-Servers: Netgate](https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html)
