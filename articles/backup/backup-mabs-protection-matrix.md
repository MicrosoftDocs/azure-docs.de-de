---
title: MABS (Azure Backup Server) V3 UR1-Schutzmatrix
description: Dieser Artikel bietet eine Unterstützungsmatrix aller Workloads, Datentypen und Installationen, die von Azure Backup Server geschützt werden.
ms.date: 03/19/2020
ms.topic: conceptual
ms.openlocfilehash: d37d51ee781dfbc5bcd56fa3158a622202a979c6
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754180"
---
# <a name="mabs-azure-backup-server-v3-ur1-protection-matrix"></a>MABS (Azure Backup Server) V3 UR1-Schutzmatrix

In diesem Artikel sind verschiedene Server und Workloads aufgeführt, die Sie mit Azure Backup Server schützen können. In der folgenden Matrix sind die Elemente aufgelistet, die mit Azure Backup Server geschützt werden können.

Verwenden Sie die folgende Matrix für MABS v3 UR1:

* Workloads – Der Workloadtyp der Technologie.

* Version – Unterstützte MABS-Version für die Workloads.

* MABS-Installation – Der Computer/Standort, auf dem bzw. an dem Sie MABS installieren möchten.

* Schutz und Wiederherstellung – Listen Sie die detaillierten Informationen zu den Workloads auf, wie z. B. unterstützter Speichercontainer oder unterstützte Bereitstellung.

>[!NOTE]
>Die Unterstützung für den 32-Bit-Schutz-Agent ist mit MABS v3 UR1 veraltet. Informationen dazu finden Sie unter [Eingestellte Unterstützung für den 32-Bit-Schutz-Agent](backup-mabs-whats-new-mabs.md#32-bit-protection-agent-deprecation).

## <a name="protection-support-matrix"></a>Schutzunterstützungsmatrix

In den folgenden Abschnitten wird die Schutzunterstützungsmatrix für MABS detailliert beschrieben:

* [Anwendungssicherung](#applications-backup)
* [VM-Sicherung](#vm-backup)
* [Linux](#linux)

## <a name="applications-backup"></a>Anwendungssicherung

| **Workload**               | **Version**                                                  | **Azure Backup Server V2-Installation**                       | **Unterstützte Azure Backup Server-Instanz** | **Schutz und Wiederherstellung**                                  |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------- | ------------------------------------------------------------ |
| Clientcomputer (64-Bit) | Windows 10                                                  | Physischer Server  <br><br>    Virtueller Hyper-V-Computer   <br><br>   Virtueller VMware-Computer | V3 UR1                            | Volume, Freigabe, Ordner, Dateien, deduplizierte Volumes   <br><br>   Geschützte Volumes müssen mit NTFS formatiert sein. FAT und FAT32 werden nicht unterstützt.  <br><br>    Volumes müssen mindestens 1 GB groß sein. Azure Backup Server verwendet den Volumeschattenkopie-Dienst (Volume Shadow Copy Service, VSS) für die Daten der Momentaufnahme, und die Momentaufnahme funktioniert nur, wenn das Volume mindestens 1GB groß ist. |
| Server (64 Bit)          | Windows Server 2019, 2016, 2012 R2, 2012                    | Virtueller Azure-Computer (wenn die Workload als virtueller Azure-Computer ausgeführt wird)  <br><br>    Physischer Server  <br><br>    Virtueller Hyper-V-Computer <br><br>     Virtueller VMware-Computer  <br><br>    Azure Stack | V3 UR1                            | Volume, Freigabe, Ordner, Datei <br><br>    deduplizierte Volumes (nur NTFS)  <br><br>   Systemstatus und Bare-Metal (wird nicht unterstützt, wenn die Workload als virtueller Azure-Computer ausgeführt wird) |
| Server (64 Bit)          | Windows Server 2008 R2 SP1, Windows Server 2008 SP2 (Sie müssen [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616) installieren.) | Physischer Server  <br><br>    Virtueller Hyper-V-Computer  <br><br>      Virtueller VMware-Computer  <br><br>   Azure Stack | V3 UR1                            | Volume, Freigabe, Ordner, Datei, Systemstatus/Bare-Metal        |
| SQL Server                | SQL Server 2019, 2017, 2016 und [unterstützte SPs](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202016), 2014 und unterstützte [SPs](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202014) | Physischer Server  <br><br>     Virtueller Hyper-V-Computer   <br><br>     Virtueller VMware-Computer  <br><br>   Virtueller Azure-Computer (wenn die Workload als virtueller Azure-Computer ausgeführt wird)  <br><br>     Azure Stack | V3 UR1                            | Alle Bereitstellungsszenarien: Datenbank       <br><br>  MABS v3 UR1 unterstützt die Sicherung von SQL-Datenbanken über ReFS-Volumes.                  |
| Exchange                   | Exchange 2019, 2016                                         | Physischer Server   <br><br>   Virtueller Hyper-V-Computer  <br><br>      Virtueller VMware-Computer  <br><br>   Azure Stack  <br><br>    Virtueller Azure-Computer (wenn die Workload als virtueller Azure-Computer ausgeführt wird) | V3 UR1                            | Schutz (alle Bereitstellungsszenarien): Eigenständiger Exchange-Server, Datenbank unter einer Database Availability Group (DAG)  <br><br>    Wiederherstellen (alle Bereitstellungsszenarien): Postfach, Postfachdatenbanken unter einer DAG    <br><br>  Die Sicherung von Exchange über ReFS wird mit MABS v3 UR1 unterstützt. |
| SharePoint                 | SharePoint 2019, 2016 mit neuesten SPs                       | Physischer Server  <br><br>    Virtueller Hyper-V-Computer <br><br>    Virtueller VMware-Computer  <br><br>   Virtueller Azure-Computer (wenn die Workload als virtueller Azure-Computer ausgeführt wird)   <br><br>   Azure Stack | V3 UR1                            | Schutz (alle Bereitstellungsszenarien): Farm, Inhalt von Front-End-Webserver  <br><br>    Wiederherstellen (alle Bereitstellungsszenarien): Farm, Datenbank, Webanwendung, Datei oder Listenelement, SharePoint-Suche, Front-End-Webserver  <br><br>    Der Schutz von SharePoint-Farmen, die für die Inhaltsdatenbanken das Always On-Feature von SQL Server 2012 verwenden, wird nicht unterstützt. |

## <a name="vm-backup"></a>VM-Sicherung

| **Workload**                                                 | **Version**                                             | **Azure Backup Server V2-Installation**                      | **Unterstützte Azure Backup Server-Instanz** | **Schutz und Wiederherstellung**                                 |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Hyper-V-Host – MABS-Schutz-Agent auf Hyper-V-Hostserver, Cluster oder VM | Windows Server 2019, 2016, 2012 R2, 2012               | Physischer Server  <br><br>    Virtueller Hyper-V-Computer <br><br>    Virtueller VMware-Computer | V3 UR1                             | Schützen:  Hyper-V-Computer, freigegebene Clustervolumes (CSVs)  <br><br>    Wiederherstellen: Virtueller Computer, Wiederherstellung auf Elementebene von Dateien und Ordnern (nur für Windows verfügbar), Volumes, virtuelle Festplatten |
| Virtuelle VMware-Computer                                                  | VMware-Server 5.5, 6.0 oder 6.5, 6.7 (lizenzierte Version) | Virtueller Hyper-V-Computer  <br><br>   Virtueller VMware-Computer         | V3 UR1                             | Schützen:  VMware-Ms in freigegebenen Clustervolumen (CSVs), NFS und im SAN-Speicher   <br><br>     Wiederherstellen:  Virtueller Computer, Wiederherstellung auf Elementebene von Dateien und Ordnern (nur für Windows verfügbar), Volumes, virtuelle Festplatten <br><br>    VMware-vApps werden nicht unterstützt. |

## <a name="linux"></a>Linux

| **Workload** | **Version**                               | **Azure Backup Server V2-Installation**                      | **Unterstützte Azure Backup Server-Instanz** | **Schutz und Wiederherstellung**                                 |
| ------------ | ----------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Linux        | Linux, ausgeführt als [Hyper-V](back-up-hyper-v-virtual-machines-mabs.md)- oder [VMware](backup-azure-backup-server-vmware.md)-Gast | Physischer Server, lokaler virtueller Hyper-V-Computer, virtueller Windows-Computer in VMWare | V3 UR1                             | Hyper-V muss unter Windows Server 2012 R2, Windows Server 2016 oder Windows Server 2019 ausgeführt werden. Schützen:  Gesamter virtueller Computer   <br><br>   Wiederherstellen: Gesamter virtueller Computer   <br><br>    Es werden nur dateikonsistente Momentaufnahmen unterstützt.    <br><br>   Eine vollständige Liste der unterstützten Linux-Verteilungen und -Versionen finden Sie im Artikel [Linux auf von Azure unterstützten Verteilungen](../virtual-machines/linux/endorsed-distros.md). |

## <a name="azure-expressroute-support"></a>Azure ExpressRoute-Unterstützung

Sie können Ihre Daten über Azure ExpressRoute mit öffentlichem Peering (verfügbar für alte Verbindungen) und Microsoft-Peering sichern. Die Sicherung über privates Peering wird nicht unterstützt.

Bei öffentlichem Peering: Stellen Sie den Zugriff auf die folgenden Domänen/Adressen sicher:

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

Mit Microsoft-Peering: Wählen Sie die folgenden Dienste, Regionen und relevanten Communitywerte aus:

* Azure Active Directory (12076:5060)
* Microsoft Azure-Region (entsprechend dem Standort Ihres Recovery Services-Tresors)
* Azure Storage (entsprechend dem Standort Ihres Recovery Services-Tresors)

Weitere Informationen finden Sie unter [ExpressRoute-Routinganforderungen](../expressroute/expressroute-routing.md).

>[!NOTE]
>Öffentliches Peering gilt für neue Leitungen als veraltet.

## <a name="cluster-support"></a>Unterstützung für Cluster

Azure Backup Server kann Daten in den folgenden gruppierten Anwendungen schützen:

* Dateiserver

* SQL Server

* Hyper-V: Wenn Sie einen Hyper-V-Cluster mithilfe von horizontal skaliertem MABS-Schutz-Agent schützen, können Sie keinen sekundären Schutz für die geschützten Hyper-V-Workloads hinzufügen.

* Exchange Server – Azure Backup Server kann nicht freigegebene Datenträgercluster für unterstützte Exchange Server-Versionen schützen (fortlaufende Clusterreplikation). Auch der Schutz von Exchange Server in einer Konfiguration für fortlaufende lokale Replikation ist mit Azure Backup Server möglich.

* SQL Server – Azure Backup Server unterstützt nicht das Sichern von SQL Server-Datenbanken, die auf freigegebenen Clustervolumes (CSVs) gehostet werden.

Azure Backup Server kann Workloads schützen, die sich in derselben Domäne wie der MABS-Server, in einer untergeordneten oder in einer vertrauenswürdigen Domäne befinden. Wenn Sie Datenquellen in nicht vertrauenswürdigen Domänen oder Arbeitsgruppen schützen möchten, verwenden Sie NTLM, Zertifikatauthentifizierung für einen einzelnen Server oder Zertifikatauthentifizierung nur für einen Cluster.
