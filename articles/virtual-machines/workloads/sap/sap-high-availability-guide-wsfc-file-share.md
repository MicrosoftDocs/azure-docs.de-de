---
title: Gruppieren einer SAP ASCS/SCS-Instanz in WSFC per Dateifreigabe in Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine SAP ASCS/SCS-Instanz in einem Windows-Failovercluster per Dateifreigabe in Azure gruppieren.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/24/2019
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 469f6a1021fde661c4eae7951b86c9bb500c7050
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012492"
---
# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-file-share-in-azure"></a>Gruppieren einer SAP ASCS/SCS-Instanz in einem Windows-Failovercluster per Dateifreigabe in Azure

> ![Windows-Logo.][Logo_Windows] Windows
>

Windows Server-Failoverclustering ist die Grundlage für eine SAP ASCS/SCS-Installation und ein DBMS in Windows mit Hochverfügbarkeit.

Bei einem Failovercluster handelt es sich um eine Gruppe von 1+n unabhängigen Servern, die zur Steigerung der Verfügbarkeit von Anwendungen und Diensten zusammenarbeiten. Wenn ein Knotenfehler auftritt, berechnet das Windows Server-Failoverclustering die Anzahl von Fehlern, die auftreten können, und erhält weiterhin einen fehlerfreien Cluster für die Bereitstellung der Anwendungen und Dienste aufrecht. Sie können zwischen verschiedenen Quorummodi wählen, um das Failoverclustering zu erzielen.

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie mit den in diesem Artikel beschriebenen Aufgaben beginnen, lesen Sie diesen Artikel:

* [Azure Virtual Machines – Architektur und Szenarien für die Hochverfügbarkeit von SAP NetWeaver][sap-high-availability-architecture-scenarios]

> [!IMPORTANT]
> Das Gruppieren von SAP ASCS/SCS-Instanzen über eine Dateifreigabe wird für SAP NetWeaver 7.40-Produkte (und höher) mit SAP Kernel 7.49 (und höher) unterstützt.
>


## <a name="windows-server-failover-clustering-in-azure"></a>Windows Server-Failoverclustering in Azure

Im Vergleich zu Bare-Metal- oder privaten Cloudbereitstellungen sind für virtuelle Azure-Computer zusätzliche Schritte erforderlich, um Windows Server-Failoverclustering zu konfigurieren. Wenn Sie einen Cluster erstellen, müssen Sie mehrere IP-Adressen und virtuelle Hostnamen für die SAP ASCS/SCS-Instanz festlegen.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Namensauflösung in Azure und Name des virtuellen Hosts für den Cluster

Die Azure-Cloudplattform bietet keine Möglichkeit, virtuelle IP-Adressen, z.B. Floating IP-Adressen, zu konfigurieren. Sie benötigen eine alternative Lösung für die Einrichtung einer virtuellen IP-Adresse, um die Clusterressource in der Cloud zu erreichen. 

Der Azure Load Balancer-Dienst stellt einen *internen Lastenausgleich* für Azure zur Verfügung. Mit dem internen Load Balancer erreichen Clients den Cluster über die virtuelle IP-Adresse des Clusters. 

Stellen Sie den internen Lastenausgleich in der Ressourcengruppe mit den Clusterknoten bereit. Konfigurieren Sie dann alle erforderlichen Portweiterleitungsregeln mithilfe der Testports des internen Lastenausgleichs. Die Clients können über den virtuellen Hostnamen eine Verbindung herstellen. Der DNS-Server löst die IP-Adresse des Clusters auf. Der interne Lastenausgleich übernimmt die Weiterleitung an den aktiven Knoten des Clusters.

![Abbildung 1: Konfiguration des Windows Server-Failoverclusterings in Azure ohne freigegebenen Datenträger][sap-ha-guide-figure-1001]

_**Abbildung 1:** Konfiguration des Windows Server-Failoverclusterings in Azure ohne freigegebenen Datenträger_

## <a name="sap-ascsscs-ha-with-file-share"></a>SAP ASCS/SCS-HA mit Dateifreigabe

SAP hat neue Ansätze und eine Alternative zum Gruppieren von freigegebenen Datenträgern entwickelt, die eine Gruppierung von SAP ASCS/SCS-Instanzen in einem Windows-Failovercluster ermöglichen. Anstatt freigegebene Datenträger in einem Cluster zu verwenden, können Sie mit einer SMB-Dateifreigabe globale SAP-Hostdateien bereitstellen.

> [!NOTE]
> Eine SMB-Dateifreigabe ist eine Alternative zur Verwendung von freigegebenen Datenträgern in einem Cluster, um die Gruppierung von SAP ASCS/SCS-Instanzen zu ermöglichen.  
>

Diese Architektur weist in folgenden Bereichen Besonderheiten auf:

* SAP Central Services (mit eigener Dateistruktur und Prozessen für Nachrichten und das Einreihen in die Warteschlange) sind von globalen SAP-Hostdateien getrennt.
* SAP Central Services werden unter einer SAP ASCS/SCS-Instanz ausgeführt.
* Die SAP ASCS/SCS-Instanz ist in einem Cluster enthalten und über den virtuellen Hostnamen des \<ASCS/SCS virtual host name\> zugänglich.
* Globale SAP-Dateien befinden sich in der SMB-Dateifreigabe, und es wird über den Hostnamen des \<SAP global host\> darauf zugegriffen: \\\\&lt;globaler SAP-Host&gt;\sapmnt\\&lt;SID&gt;\SYS\..
* Die SAP ASCS/SCS-Instanz wird auf einem lokalen Datenträger auf beiden Clusterknoten installiert.
* Der \<ASCS/SCS virtual host name\>-Netzwerkname unterscheidet sich vom &lt;globalen SAP-Host&gt;.

![Abbildung 2: SAP ASCS/SCS-Hochverfügbarkeitsarchitektur mit SMB-Dateifreigabe][sap-ha-guide-figure-8004]

_**Abbildung 2:** Neue SAP ASCS/SCS-Hochverfügbarkeitsarchitektur mit einer SMB-Dateifreigabe_

Voraussetzungen für SMB-Dateifreigaben:

* SMB 3.0-Protokoll (oder höher).
* Möglichkeit zum Festlegen von Active Directory-Zugriffssteuerungslisten (ACLs) für Active Directory-Benutzergruppen und das Computerobjekt `computer$`.
* Für die Dateifreigabe muss Hochverfügbarkeit aktiviert sein:
    * Die zum Speichern von Dateien verwendeten Datenträger dürfen kein Single Point of Failure sein.
    * Ausfallzeiten des Servers oder des virtuellen Computers verursachen keine Ausfallzeiten für die Dateifreigabe.

Die SAP \<SID\>-Clusterrolle enthält jetzt keine freigegebenen Clusterdatenträger und keine Clusterressource von allgemeinen Dateifreigaben.


![Abbildung 3: SAP \<SID\>-Clusterrollenressourcen zur Verwendung einer Dateifreigabe][sap-ha-guide-figure-8005]

_**Abbildung 3:** SAP &lt;SID&gt;-Clusterrollenressourcen zur Verwendung einer Dateifreigabe_


## <a name="scale-out-file-shares-with-storage-spaces-direct-in-azure-as-an-sapmnt-file-share"></a>Dateifreigaben mit horizontaler Skalierung mit direkten Speicherplätzen in Azure als SAPMNT-Dateifreigabe

Dateifreigaben mit horizontaler Skalierung können zum Hosten und Schützen von globalen SAP-Hostdateien verwendet werden. Eine Dateifreigabe mit horizontaler Skalierung bietet außerdem einen hochverfügbaren SAPMNT-Dateifreigabedienst.

![Abbildung 4: Dateifreigabe mit horizontaler Skalierung zum Schützen von globalen SAP-Hostdateien][sap-ha-guide-figure-8006]

_**Abbildung 4:** Eine Dateifreigabe mit horizontaler Skalierung zum Schützen von globalen SAP-Hostdateien_

> [!IMPORTANT]
> Dateifreigaben mit horizontaler Skalierung werden in der Microsoft Azure-Cloud und in lokalen Umgebungen vollständig unterstützt.
>

Durch Dateifreigaben mit horizontaler Skalierung wird eine hochverfügbare und horizontal skalierbare SAPMNT-Dateifreigabe ermöglicht.

Direkte Speicherplätze werden als freigegebener Datenträger für eine Dateifreigabe mit horizontaler Skalierung verwendet. Mit direkten Speicherplätzen können Sie hochverfügbaren und skalierbaren Speicher erstellen, in dem Server mit lokalem Speicher verwendet werden. Freigegebener Speicher für eine Dateifreigabe mit horizontaler Skalierung ist, wie bei globalen SAP-Hostdateien, kein Single Point of Failure.

Beachten Sie bei der Auswahl von „Direkte Speicherplätze“ die folgenden Anwendungsfälle:

- Die zum Erstellen des Clusters mit „Direkte Speicherplätze“ verwendeten virtuellen Computer müssen in einer Azure-Verfügbarkeitsgruppe bereitgestellt werden.
- Für die Notfallwiederherstellung eines Clusters mit „Direkte Speicherplätze“ können Sie [Azure Site Recovery Services](../../../site-recovery/azure-to-azure-support-matrix.md#replicated-machines---storage) verwenden.
- Ein Ausdehnen des Clusters mit „Direkte Speicherplätze“ über verschiedene Azure-Verfügbarkeitszonen wird nicht unterstützt.

### <a name="sap-prerequisites-for-scale-out-file-shares-in-azure"></a>SAP-Voraussetzungen für Dateifreigaben mit horizontaler Skalierung in Azure

Um eine Dateifreigabe mit horizontaler Skalierung verwenden zu können, muss Ihr System folgende Anforderungen erfüllen:

* Mindestens zwei Clusterknoten für eine Dateifreigabe mit horizontaler Skalierung.
* Jeder Knoten muss mindestens zwei lokale Datenträger aufweisen.
* Aus Leistungsgründen müssen Sie die *Spiegelungsresilienz* nutzen:
    * Zwei-Wege-Spiegelung für eine Dateifreigabe mit horizontaler Skalierung mit zwei Clusterknoten.
    * Drei-Wege-Spiegelung für eine Dateifreigabe mit horizontaler Skalierung mit drei oder mehr Clusterknoten.
* Es wird empfohlen, mindestens drei Clusterknoten mit Drei-Wege-Spiegelung für eine Dateifreigabe mit horizontaler Skalierung zu verwenden.
    Diese Einrichtung bietet eine bessere Skalierbarkeit und Speicherresilienz als ein Setup mit einer Dateifreigabe mit horizontaler Skalierung mit zwei Clusterknoten und Zwei-Wege-Spiegelung.
* Sie müssen Azure Premium-Datenträger verwenden.
* Wir empfehlen die Verwendung von Azure Managed Disks.
* Es wird empfohlen, Volumes mit dem robusten Dateisystem (ReFS) zu formatieren.
    * Weitere Informationen finden Sie im [SAP-Hinweis 1869038 – SAP-Unterstützung für das ReFS-Dateisystem][1869038] und im Kapitel [Auswählen des Dateisystems][planning-volumes-s2d-choosing-filesystem] des Artikels „Planen von Volumes in Direkte Speicherplätze“.
    * Installieren Sie unbedingt das [kumulative Update für Microsoft KB4025334][kb4025334].
* Sie können als Azure-VM-Größen die DS-Serie oder die DSv2-Serie verwenden.
* Um eine gute Netzwerkleistung zwischen VMs zu erzielen, die für die Datenträgersynchronisierung mit direkten Speicherplätzen erforderlich ist, sollten Sie einen VM-Typ verwenden, der mindestens über eine „hohe“ Netzwerkbandbreite verfügt.
    Weitere Informationen finden Sie in den Spezifikationen zur [DSv2-Serie][dv2-series] und [DS-Serie][ds-series].
* Es wird empfohlen, eine gewisse nicht zugeordnete Kapazität im Speicherpool zu reservieren. Indem Sie nicht zugeordnete Kapazität im Speicherpool übrig lassen, erhalten Volumes Speicherplatz für „direkte“ Korrekturen, wenn ein Laufwerk ausfällt. Dies verbessert die Datensicherheit und die Leistung.  Weitere Informationen finden Sie unter [Auswählen der Volumegröße][choosing-the-size-of-volumes-s2d].
* Sie müssen den internen Lastenausgleich von Azure nicht für den Netzwerknamen der Dateifreigabe mit horizontaler Skalierung konfigurieren wie bei \<SAP global host\>. Dies geschieht für den \<ASCS/SCS virtual host name\> der SAP ASCS/SCS-Instanz oder für das DBMS. Eine Dateifreigabe mit horizontaler Skalierung skaliert die Last horizontal auf alle Clusterknoten. \<SAP global host\> verwendet die lokale IP-Adresse für alle Clusterknoten.


> [!IMPORTANT]
> Die SAPMNT-Dateifreigabe, die auf den \<SAP global host\> verweist, kann nicht umbenannt werden. SAP unterstützt ausschließlich den Freigabenamen „sapmnt“.
>
> Weitere Informationen finden Sie unter [SAP-Hinweis 2492395 – Kann der Freigabename „sapmnt“ geändert werden?][2492395]

### <a name="configure-sap-ascsscs-instances-and-a-scale-out-file-share-in-two-clusters"></a>Konfigurieren von SAP ASCS/SCS-Instanzen und einer Dateifreigabe mit horizontaler Skalierung in zwei Clustern

Sie können SAP ASCS/SCS-Instanzen auch in einem Cluster mit eigener SAP-\<SID\>-Clusterrolle bereitstellen. In diesem Fall konfigurieren Sie die Dateifreigabe mit horizontaler Skalierung in einem anderen Cluster mit einer anderen Clusterrolle.

> [!IMPORTANT]
>In diesem Szenario wird die SAP ASCS/SCS-Instanz so konfiguriert, dass sie über den UNC-Pfad \\\\&lt;globaler SAP-Host&gt;\sapmnt\\&lt;SID&gt;\SYS\. auf den globalen SAP-Host zugreifen kann.
>

![Abbildung 5: In zwei Clustern bereitgestellte SAP ASCS/SCS-Instanz und eine Dateifreigabe mit horizontaler Skalierung][sap-ha-guide-figure-8007]

_**Abbildung 5:** In zwei Clustern bereitgestellte SAP ASCS/SCS-Instanz und eine Dateifreigabe mit horizontaler Skalierung_

> [!IMPORTANT]
> In der Azure-Cloud muss jeder Cluster, der für SAP und Dateifreigaben mit horizontaler Skalierung verwendet wird, in einer eigenen Azure-Verfügbarkeitsgruppe oder über Azure-Verfügbarkeitszonen bereitgestellt werden. Dadurch wird eine verteilte Platzierung der Cluster-VMs in der zugrunde liegenden Azure-Infrastruktur sichergestellt. Bei dieser Technologie werden Bereitstellungen über Verfügbarkeitszonen unterstützt.
>

## <a name="generic-file-share-with-sios-datakeeper-as-cluster-shared-disks"></a>Allgemeine Dateifreigabe mit SIOS DataKeeper als freigegebenen Clusterdatenträgern


Eine allgemeine Dateifreigabe ist eine weitere Option zum Erzielen einer hochverfügbaren Dateifreigabe.

In diesem Fall können Sie eine SIOS-Lösung eines Drittanbieters als freigegebenen Clusterdatenträger nutzen.

## <a name="next-steps"></a>Nächste Schritte

* [Vorbereiten der Azure-Infrastruktur für SAP-Hochverfügbarkeit mit einem Windows-Failovercluster und einer Dateifreigabe für SAP ASCS-/SCS-Instanzen][sap-high-availability-infrastructure-wsfc-file-share]
* [SAP NetWeaver-HA-Installation auf einem Windows-Failovercluster und freigegebenen Datenträger für eine SAP ASCS/SCS-Instanz in Azure][sap-high-availability-installation-wsfc-shared-disk]
* [Bereitstellen eines Scale-Out-Dateiservers mit direkten Speicherplätzen und zwei Knoten für die Speicherung von Benutzerprofil-Datenträgern][deploy-sofs-s2d-in-azure]
* [Direkte Speicherplätze in Windows Server 2016][s2d-in-win-2016]
* [Ausführliche Betrachtung: Volumes in direkten Speicherplätzen][deep-dive-volumes-in-s2d]

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[kb4025334]:https://support.microsoft.com/help/4025334/windows-10-update-kb4025334

[dv2-series]:../../dv2-dsv2-series.md
[ds-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-volumes-s2d-choosing-filesystem]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-filesystem
[choosing-the-size-of-volumes-s2d]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-size-of-volumes
[deploy-sofs-s2d-in-azure]:https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment
[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[deep-dive-volumes-in-s2d]:https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/

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

[1869038]:https://launchpad.support.sap.com/#/notes/1869038 
