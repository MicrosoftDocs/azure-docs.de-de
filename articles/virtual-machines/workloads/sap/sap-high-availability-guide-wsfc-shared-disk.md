---
title: Clustering von SAP ASCS/SCS-Instanzen auf WSFC mithilfe freigegebener Datenträger in Azure | Microsoft-Dokumentation
description: Erhalten Sie Informationen zum Clustering von SAP ASCS/SCS-Instanzen auf Windows-Failoverclustern mithilfe freigegebener Clusterdatenträger.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: f6fb85f8-c77a-4af1-bde8-1de7e4425d2e
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e46aa79bc84f3eba218932c1e8a463584de3f1bb
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488952"
---
# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-cluster-shared-disk-in-azure"></a>Gruppieren einer SAP ASCS/SCS-Instanz in einem Windows-Failovercluster mithilfe freigegebener Clusterdatenträger in Azure

> ![Windows-Betriebssystem][Logo_Windows] Windows
>

Windows Server-Failoverclustering ist die Grundlage für eine SAP ASCS/SCS-Installation und ein DBMS in Windows mit Hochverfügbarkeit.

Bei einem Failovercluster handelt es sich um eine Gruppe von 1+n unabhängigen Servern, die zur Steigerung der Verfügbarkeit von Anwendungen und Diensten zusammenarbeiten. Wenn ein Knotenfehler auftritt, berechnet das Windows Server-Failoverclustering die Anzahl von Fehlern, die auftreten können, und erhält weiterhin einen fehlerfreien Cluster für die Bereitstellung der Anwendungen und Dienste aufrecht. Sie können zwischen verschiedenen Quorummodi wählen, um das Failoverclustering zu erzielen.

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie mit den Aufgaben in diesem Artikel beginnen, lesen Sie den folgenden Artikel:

* [Azure Virtual Machines – Architektur und Szenarien für die Hochverfügbarkeit von SAP NetWeaver][sap-high-availability-architecture-scenarios]


## <a name="windows-server-failover-clustering-in-azure"></a>Windows Server-Failoverclustering in Azure

Für das Windows Server-Failoverclustering mit Azure Virtual Machines sind zusätzliche Konfigurationsschritte erforderlich. Wenn Sie einen Cluster erstellen, müssen Sie mehrere IP-Adressen und virtuelle Hostnamen für die SAP ASCS/SCS-Instanz festlegen.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Namensauflösung in Azure und Name des virtuellen Hosts für den Cluster

Die Azure-Cloudplattform bietet keine Möglichkeit, virtuelle IP-Adressen, z.B. Floating IP-Adressen, zu konfigurieren. Sie benötigen eine alternative Lösung für die Einrichtung einer virtuellen IP-Adresse, um die Clusterressource in der Cloud zu erreichen. 

Der Azure Load Balancer-Dienst stellt einen *internen Lastenausgleich* für Azure zur Verfügung. Mit dem internen Load Balancer erreichen Clients den Cluster über die virtuelle IP-Adresse des Clusters. 

Stellen Sie den internen Lastenausgleich in der Ressourcengruppe mit den Clusterknoten bereit. Konfigurieren Sie dann alle erforderlichen Portweiterleitungsregeln mithilfe der Testports des internen Lastenausgleichs. Clients können über den virtuellen Hostnamen eine Verbindung herstellen. Der DNS-Server löst die IP-Adresse des Clusters auf. Der interne Load Balancer übernimmt die Weiterleitung an den aktiven Knoten des Clusters.

> [!IMPORTANT]
> Floating IP-Adressen werden für sekundäre NIC-IP-Konfigurationen in Szenarien mit Lastenausgleich nicht unterstützt. Ausführliche Informationen finden Sie unter [Azure Load Balancer – Einschränkungen](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Wenn Sie zusätzliche IP-Adressen für die VM benötigen, stellen Sie eine zweite NIC bereit.  

![Abbildung 1: Konfiguration des Windows-Failoverclusterings in Azure ohne freigegebenen Datenträger][sap-ha-guide-figure-1001]

_Konfiguration des Windows Server-Failoverclusterings in Azure ohne freigegebenen Datenträger_

### <a name="sap-ascsscs-ha-with-cluster-shared-disks"></a>SAP ASCS/SCS HA mit freigegebenen Clusterdatenträgern
In Windows enthält eine SAP ASCS/SCS-Instanz SAP Central Services, SAP Message Server, Serverprozesse zum Einreihen in die Warteschlange und globale SAP-Hostdateien. Globale SAP-Hostdateien speichern zentrale Dateien für das gesamte SAP-System.

Eine SAP ASCS/SCS-Instanz verfügt über die folgenden Komponenten:

* SAP Central Services:
    * Zwei Prozesse, Nachrichtenserver und Server zum Einreihen in die Warteschlange sowie \<ASCS/SCS virtual host name> für den Zugriff auf diese beiden Prozesse.
    * Dateistruktur: S:\usr\sap\\&lt;SID&gt;\ASCS/SCS\<instance number\>


* Globaler SAP-Hostname:
  * Dateistruktur: S:\usr\sap\\&lt;SID&gt;\SYS\..
  * Die sapmnt-Dateifreigabe, die den Zugriff auf dieses globalen „S:\usr\sap\\&lt;SID&gt;\SYS\...“-Dateien mithilfe des folgenden UNC-Pfads ermöglicht:

    \\\\<Name des virtuellen ASCS/SCS-Hosts\>\sapmnt\\&lt;SID&gt;\SYS\..


![Abbildung 2: Prozesse, Dateistruktur und sapmnt-Dateifreigabe des globalen Hosts einer SAP ASCS/SCS-Instanz][sap-ha-guide-figure-8001]

_Prozesse, Dateistruktur und sapmnt-Dateifreigabe des globalen Hosts einer SAP ASCS/SCS-Instanz_

Bei der Einstellung für die Hochverfügbarkeit erfolgt ein Clustering von SAP ASCS/SCS-Instanzen. Wir verwenden *freigegebene Clusterdatenträger* (in unserem Beispiel ist dies Laufwerk S:\), um Dateien von SAP ASCS/SCS und Dateien des globalen SAP-Hosts zu positionieren.

![Abbildung 3: SAP ASCS/SCS-HA-Architektur mit freigegebenem Datenträger][sap-ha-guide-figure-8002]

_SAP ASCS/SCS-HA-Architektur mit freigegebenem Datenträger_


Enqueue-Server-Replikation 1-Architektur:
* Es wird derselbe \<ASCS/SCS virtual host name> für den Zugriff auf Prozesse des SAP Message Servers und des Servers zum Einreihen in die Warteschlange sowie auf Dateien des globalen SAP-Hosts über die sapmnt-Dateifreigabe verwendet.
* Es wird derselbe freigegebene Clusterdatenträger (Laufwerk S:) gemeinsam genutzt.  

Enqueue-Server-Replikation 2-Architektur: 
* Es wird derselbe \<ASCS/SCS virtual host name> für den Zugriff auf den SAP-Nachrichtenserverprozess sowie auf Dateien des globalen SAP-Hosts über die sapmnt-Dateifreigabe verwendet.
* Es wird derselbe freigegebene Clusterdatenträger (Laufwerk S:) gemeinsam genutzt.
* Es gibt einen separaten \<ERS virtual host name> für den Zugriff auf den Prozess des Servers zum Einreihen in die Warteschlange.  

![Abbildung 4: SAP ASCS/SCS-HA-Architektur mit freigegebenem Datenträger][sap-ha-guide-figure-8003]

_SAP ASCS/SCS-HA-Architektur mit freigegebenem Datenträger_

#### <a name="shared-disk-and-enqueue-replication-server"></a>Freigegebener Datenträger und Enqueue Replication Server 

1. Freigegebene Datenträger werden bei der Enqueue-Server-Replikation 1-Architektur unterstützt, wobei für die ERS-Instanz (Enqueue Replication Server) Folgendes gilt:   

   - nicht gruppiert
   - verwendet `localhost`-Namen
   - wird auf lokalen Datenträgern auf den einzelnen Clusterknoten bereitgestellt

2. Freigegebene Datenträger werden auch bei der Enqueue-Server-Replikation 2-Architektur unterstützt, wobei für die ERS2-Instanz (Enqueue Replication Server 2) Folgendes gilt:  

   - ist gruppiert
   - verwendet dedizierten virtuellen/Netzwerkhostnamen
   - erfordert, dass zusätzlich zur (A)SCS-IP-Adresse die IP-Adresse des virtuellen ERS-Hostnamens auf dem internen Azure Load Balancer konfiguriert wird
   - wird auf **lokalen Datenträgern** auf den einzelnen Clusterknoten bereitgestellt, sodass kein freigegebener Datenträger erforderlich ist

   > [!TIP]
   > Weitere Informationen zu Enqueue Replication Server 1 und 2 (ERS1 und ERS2) finden Sie unter folgenden Themen:  
   > [Enqueue Replication Server in einem Microsoft-Failovercluster](https://help.sap.com/viewer/3741bfe0345f4892ae190ee7cfc53d4c/CURRENT_VERSION_SWPM20/en-US/8abd4b52902d4b17a105c2fabdf5c0cf.html)  
   > [Neuer Enqueue Replicator in Failoverclusterumgebungen](https://blogs.sap.com/2019/03/19/new-enqueue-replicator-in-failover-cluster-environments/)  

#### <a name="options-for-shared-disk-in-azure-for-sap-workloads"></a>Optionen für freigegebene Datenträger in Azure für SAP-Workloads

Es gibt zwei Optionen für freigegebene Datenträger in einem Windows-Failovercluster in Azure:

- [Freigegebene Azure-Datenträger](../../disks-shared.md): Dieses Feature ermöglicht das gleichzeitige Anfügen verwalteter Azure-Datenträger an mehrere VMs. 
- Mithilfe der Drittanbietersoftware [SIOS DataKeeper Cluster Edition](https://us.sios.com/products/datakeeper-cluster) können Sie einen gespiegelten Speicher erstellen, der freigegebenen Clusterspeicher simuliert. 

Beachten Sie beim Auswählen der Technologie für freigegebene Datenträger die folgenden Punkte:

**Freigegebener Azure-Datenträger für SAP-Workloads**
- Ermöglicht das gleichzeitige Anfügen verwalteter Azure-Datenträger an mehrere VMs, ohne dass zusätzliche Software gewartet und ausgeführt werden muss. 
- Sie arbeiten mit einem einzelnen freigegebenen Azure-Datenträger in einem Speichercluster. Dies wirkt sich auf die Zuverlässigkeit Ihrer SAP-Lösung aus.
- Derzeit wird nur die Bereitstellung mit freigegebenem Azure-Premium-Datenträger in der Verfügbarkeitsgruppe unterstützt. Ein freigegebener Azure-Datenträger in einer Zonenbereitstellung wird nicht unterstützt.     
- Stellen Sie sicher, dass der Azure-Premium-Datenträger mit einer minimalen Datenträgergröße entsprechend den Angaben unter [SSD Premium-Bereiche](../../disks-shared.md#disk-sizes) bereitgestellt wird, um die erforderliche Anzahl von VMs gleichzeitig anfügen zu können (normalerweise zwei für den SAP ASCS-Windows-Failovercluster). 
- Freigegebene Azure Ultra-Datenträger werden für SAP-Workloads nicht unterstützt, da sie keine Bereitstellung in einer Verfügbarkeitsgruppe oder Zonenbereitstellung unterstützen.  
 
**SIOS**
- Die SIOS-Lösung bietet synchrone Datenreplikation zwischen zwei Datenträgern in Echtzeit.
- Bei der SIOS-Lösung arbeiten Sie mit zwei verwalteten Datenträgern. Wenn Sie Verfügbarkeitsgruppen oder Verfügbarkeitszonen verwenden, werden die verwalteten Datenträger auf unterschiedliche Speichercluster verteilt. 
- Die Bereitstellung in Verfügbarkeitszonen wird unterstützt.
- Erfordert das Installieren und Ausführen von Drittanbietersoftware, die Sie zusätzlich erwerben müssen.

### <a name="shared-disk-using-azure-shared-disk"></a>Freigegebene Datenträger mithilfe freigegebener Azure-Datenträger

Microsoft bietet [freigegebene Azure-Datenträger](../../disks-shared.md) an, mit denen SAP ASCS/SCS-Hochverfügbarkeit mit einem freigegebenen Datenträger implementiert werden kann.

#### <a name="prerequisites-and-limitations"></a>Voraussetzungen und Einschränkungen

Derzeit können Sie Azure SSD Premium-Datenträger als freigegebenen Azure-Datenträger für die SAP ASCS/SCS-Instanz verwenden. Derzeit gelten die folgenden Einschränkungen:

-  Ein [Azure Ultra-Datenträger](../../disks-types.md#ultra-disk) wird nicht als freigegebener Azure-Datenträger für SAP-Workloads unterstützt. Derzeit ist es nicht möglich, Azure-VMs mithilfe eines Azure Ultra-Datenträgers in einer Verfügbarkeitsgruppe zu platzieren.
-  Ein [freigegebener Azure-Datenträger](../../disks-shared.md) mit SSD Premium-Datenträgern wird nur für VMs in einer Verfügbarkeitsgruppe unterstützt. Bei der Bereitstellung in Verfügbarkeitszonen wird dies nicht unterstützt. 
-  Der Wert [maxShares](../../disks-shared-enable.md?tabs=azure-cli#disk-sizes) des freigegebenen Azure-Datenträgers bestimmt, wie viele Clusterknoten den freigegebenen Datenträger verwenden können. In der Regel werden für SAP ASCS/SCS-Instanzen zwei Knoten im Windows-Failovercluster konfiguriert. Daher muss der Wert für `maxShares` auf zwei festgelegt werden.
-  Alle VMs im SAP ASCS/SCS-Cluster müssen in derselben [Azure-Näherungsplatzierungsgruppe](../../windows/proximity-placement-groups.md) (Proximity Placement Group, PPG) bereitgestellt werden.   
   Zwar können Sie VMs im Windows-Cluster in einer Verfügbarkeitsgruppe mit freigegebenem Azure-Datenträger ohne PPG bereitstellen, doch gewährleistet PPG die physische Nähe von freigegebenen Azure-Datenträgern und Cluster-VMs und erzielt somit eine geringere Latenz zwischen den VMs und der Speicherebene.    

Weitere Informationen zu Einschränkungen für freigegebene Azure-Datenträger finden Sie im Abschnitt [Einschränkungen](../../disks-shared.md#limitations) der entsprechenden Dokumentation.

> [!IMPORTANT]
> Beachten Sie beim Bereitstellen des SAP ASCS/SCS-Windows-Failoverclusters mit freigegebenem Azure-Datenträger, dass die Bereitstellung mit einem einzelnen freigegebenen Datenträger in nur einem Speichercluster erfolgt. Probleme mit dem Speichercluster, in dem der freigegebene Azure-Datenträger bereitgestellt wird, beeinträchtigen Ihre SAP ASCS/SCS-Instanz.    

> [!TIP]
> Wichtige Überlegungen zur Planung der SAP-Bereitstellung finden Sie im [Azure-Planungshandbuch für SAP NetWeaver](./planning-guide.md) und im [Azure Storage-Handbuch für SAP-Workloads](./planning-guide-storage.md).

### <a name="supported-os-versions"></a>Unterstützte Betriebssystemversionen

Sowohl Windows Server 2016 als auch 2019 werden unterstützt (verwenden Sie die neuesten Datacenter-Images).

Aus folgenden Gründen wird dringend empfohlen, **Windows Server 2019 Datacenter** zu verwenden:
- Der Windows 2019-Failoverclusterdienst ist Azure-fähig.
- Durch die Überwachung auf geplante Azure-Ereignisse werden Integration und Kenntnisse über die Azure-Hostwartung sowie verbesserte Benutzerfreundlichkeit erzielt.
- Es kann der Name des verteilten Netzwerks (Standardoption) verwendet werden. Dadurch ist keine dedizierte IP-Adresse für den Namen des Clusternetzwerks erforderlich. Außerdem muss diese IP-Adresse nicht auf dem internen Azure Load Balancer konfiguriert werden. 

### <a name="shared-disks-in-azure-with-sios-datakeeper"></a>Freigegebener Datenträger in Azure mit SIOS DataKeeper

Eine weitere Option für freigegebene Datenträger ist die Verwendung der Drittanbietersoftware SIOS DataKeeper Cluster Edition, die es Ihnen ermöglicht, einen gespiegelten Speicher zu erstellen, der freigegebenen Clusterspeicher simuliert. Die SIOS-Lösung bietet eine synchrone Datenreplikation in Echtzeit.

So erstellen Sie eine freigegebene Datenträgerressource für einen Cluster

1. Fügen Sie einen zusätzlichen Datenträger an sämtliche virtuelle Computer in einer Windows-Clusterkonfiguration an.
2. Führen Sie SIOS DataKeeper Cluster Edition auf beiden virtuellen Computerknoten aus.
3. Konfigurieren Sie SIOS DataKeeper Cluster Edition so, dass synchron der Inhalt des zusätzlich angefügten Datenträgervolumes vom virtuellen Quellcomputer im zusätzlich angefügten Datenträgervolume des virtuellen Zielcomputers gespiegelt wird. SIOS DataKeeper abstrahiert die lokalen Quell- und Zielvolumes und präsentiert diese beim Windows Server-Failoverclustering als einen freigegebenen Datenträger.

Weitere Informationen zu [SIOS DataKeeper](https://us.sios.com/products/datakeeper-cluster/).

![Abbildung 5: Konfiguration des Windows Server-Failoverclusterings in Azure mit SIOS DataKeeper][sap-ha-guide-figure-1002]

_Konfiguration des Windows-Failoverclusterings in Azure mit SIOS DataKeeper_

> [!NOTE]
> Sie benötigen bei einigen DBMS-Produkten wie SQL Server für Hochverfügbarkeit keine freigegebenen Datenträger. SQL Server AlwaysOn führt die Replikation von DBMS-Daten- und -Protokolldateien vom lokalen Datenträger eines Clusterknotens auf den lokalen Datenträger eines anderen Clusterknotens durch. In diesem Fall ist bei der Windows-Clusterkonfiguration kein freigegebener Datenträger erforderlich.
>

## <a name="next-steps"></a>Nächste Schritte

* [Vorbereiten der Azure-Infrastruktur für SAP HA mit einem Windows-Failovercluster und einem freigegebenen Datenträger für eine SAP ASCS/SCS-Instanz][sap-high-availability-infrastructure-wsfc-shared-disk]

* [SAP NetWeaver HA-Installation auf einem Windows-Failovercluster und freigegebenen Datenträger für eine SAP ASCS/SCS-Instanz][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP-Multi-SID-Konfiguration mit Hochverfügbarkeit)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md