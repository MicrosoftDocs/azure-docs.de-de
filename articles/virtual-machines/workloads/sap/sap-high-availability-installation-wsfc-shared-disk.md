---
title: SAP NetWeaver-HA-Installation auf einem Windows-Failovercluster und freigegebenen Datenträger für eine SAP ASCS/SCS-Instanz in Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie SAP NetWeaver-HA auf einem Windows-Failovercluster und freigegebenen Datenträger für eine SAP ASCS/SCS-Instanz installieren.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 6209bcb3-5b20-4845-aa10-1475c576659f
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7ce13bdc14ea2af0a4528606d5b12fabf50da06d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965390"
---
# <a name="install-sap-netweaver-ha-on-a-windows-failover-cluster-and-shared-disk-for-an-sap-ascsscs-instance-in-azure"></a>SAP NetWeaver-HA-Installation auf einem Windows-Failovercluster und freigegebenen Datenträger für eine SAP ASCS/SCS-Instanz in Azure

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
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-ha-guide-8.9]:high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md



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
[sap-ha-guide-figure-3046-ers2]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ers2-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

In diesem Artikel wird beschrieben, wie Sie ein SAP-Hochverfügbarkeitssystem in Azure für das Clustering einer SAP ASCS/SCS-Instanz unter Verwendung eines Windows Server-Failoverclusters und eines freigegebenen Datenträgers für den Cluster installieren und konfigurieren. Wie im [Architekturleitfaden: Gruppieren einer SAP ASCS/SCS-Instanz in einem Windows-Failovercluster mithilfe freigegebener Clusterdatenträger in Azure][sap-high-availability-guide-wsfc-shared-disk] beschrieben, gibt es zwei Alternativen für *freigegebene Clusterdatenträger*:

- [Freigegebene Azure-Datenträger](../../disks-shared.md)
- Verwenden von [SIOS DataKeeper Cluster Edition](https://us.sios.com/products/datakeeper-cluster/) zum Erstellen eines gespiegelten Speichers, der die freigegebenen Clusterdatenträger simuliert 

## <a name="prerequisites"></a>Voraussetzungen

Lesen Sie die folgenden Dokumente, bevor Sie mit der Installation beginnen:

* [Architekturleitfaden: Gruppieren einer SAP ASCS/SCS-Instanz in einem Windows-Failovercluster mithilfe freigegebener Clusterdatenträger in Azure][sap-high-availability-guide-wsfc-shared-disk]

* [Vorbereiten der Azure-Infrastruktur für SAP HA mit einem Windows-Failovercluster und einem freigegebenen Datenträger für eine SAP ASCS/SCS-Instanz][sap-high-availability-infrastructure-wsfc-shared-disk]

Das DBMS-Setup wird in diesem Artikel nicht beschrieben, da die Einrichtung vom verwendeten DBMS abhängt. Es wird davon ausgegangen, dass auf Hochverfügbarkeit bezogene Aspekte des DBMS mit den Funktionen verwaltet werden, die verschiedene DBMS-Hersteller für Azure unterstützen. Beispiele hierfür sind AlwaysOn oder Datenbankspiegelung für SQL Server und Oracle Data Guard für Oracle-Datenbanken. Die Hochverfügbarkeitsszenarien für DBMS werden in diesem Artikel nicht beschrieben.

Es sind keine Besonderheiten zu berücksichtigen, wenn unterschiedliche DBMS-Dienste mit einer SAP ASCS/SCS-Clusterkonfiguration in Azure interagieren.

> [!NOTE]
> Der Installationsvorgang von SAP NetWeaver ABAP-, Java- und ABAP+Java-Systemen ist nahezu identisch. Der wichtigste Unterschied ist, dass ein SAP ABAP-System eine ASCS-Instanz hat. Das SAP Java-System hat eine SCS-Instanz. Das SAP ABAP+Java-System hat eine ASCS- und eine SCS-Instanz, die in der gleichen Microsoft-Failoverclustergruppe ausgeführt werden. Unterschiede bei der Installation der einzelnen SAP NetWeaver-Installationsstapel werden explizit angegeben. Sie können davon ausgehen, dass die verbleibenden Schritte die gleichen sind.  

## <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>Installieren von SAP mit einer ASCS/SCS-Hochverfügbarkeitsinstanz

> [!IMPORTANT]
> Wenn Sie SIOS zum Darstellen der freigegebenen Datenträger verwenden, platzieren Sie die Auslagerungsdatei nicht in den gespiegelten SIOS DataKeeper-Volumes. Belassen Sie die Auslagerungsdatei auf dem temporären Laufwerk D eines virtuellen Azure-Computers (dies ist auch die Standardeinstellung). Wenn sie sich noch nicht dort befindet, verschieben Sie die Windows-Auslagerungsdatei auf Laufwerk D des virtuellen Azure-Computers.  


Das Installieren von SAP mit einer ASCS/SCS-Instanz mit hoher Verfügbarkeit umfasst die folgenden Aufgaben:

* Erstellen eines virtuellen Hostnamens für die SAP ASCS/SCS-Clusterinstanz
* Installieren von SAP auf dem ersten Clusterknoten
* Ändern des SAP-Profils der ASCS/SCS-Instanz
* Hinzufügen eines Testports
* Öffnen des Windows-Firewall-Testports

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>Erstellen eines virtuellen Hostnamens für die SAP ASCS/SCS-Clusterinstanz

1. Erstellen Sie im Windows-DNS-Manager einen DNS-Eintrag für den virtuellen Hostnamen der ASCS/SCS-Instanz.

   > [!IMPORTANT]
   > Bei der IP-Adresse, die Sie dem virtuellen Hostnamen der ASCS/SCS-Instanz zuweisen, muss es sich um die IP-Adresse handeln, die Sie der Azure Load Balancer-Instanz zugewiesen haben.  
   
   
   ![Abbildung 1: Festlegen des DNS-Eintrags für den virtuellen SAP ASCS/SCS-Clusternamen und der TCP/IP-Adresse][sap-ha-guide-figure-3046]

   _Festlegen des DNS-Eintrags für den virtuellen SAP ASCS/SCS-Clusternamen und der TCP/IP-Adresse_

2. Wenn Sie den neuen SAP Enqueue Replication Server 2 verwenden, bei dem es sich ebenfalls um eine Clusterinstanz handelt, müssen Sie in DNS auch einen virtuellen Hostnamen für ERS2 reservieren. 

   > [!IMPORTANT]
   > Bei der IP-Adresse, die Sie dem virtuellen Hostnamen der ERS2-Instanz zuweisen, muss es sich um die zweite IP-Adresse handeln, die Sie der Azure Load Balancer-Instanz zugewiesen haben.    
   
   
   ![Abbildung 1A: Festlegen des DNS-Eintrags für den virtuellen SAP ASCS/SCS-Clusternamen und der TCP/IP-Adresse][sap-ha-guide-figure-3046-ers2]

   _Definieren des DNS-Eintrags für den Namen und die TCP/IP-Adresse des virtuellen SAP ERS2-Clusters_


3. Wählen Sie zum Definieren der IP-Adresse, die dem virtuellen Hostnamen zugewiesen ist, die Option **DNS-Manager** > **Domäne**.

   ![Abbildung 2: Neuer virtueller Name und TCP/IP-Adresse für die SAP ASCS/SCS-Clusterkonfiguration][sap-ha-guide-figure-3047]

   _Neuer virtueller Name und neue TCP/IP-Adresse für die SAP ASCS/SCS-Clusterkonfiguration_

### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Installieren des ersten SAP-Clusterknotens

1. Führen Sie die Option für den ersten Clusterknoten auf dem Clusterknoten A aus. Wählen Sie Folgendes aus:

   * **ABAP-System**: **ASCS**-Instanznummer **00**
   * **Java-System**: **SCS**-Instanznummer **01**
   * **ABAP + Java-System**: **ASCS**-Instanznummer **00** und **SCS**-Instanznummer **01**

   
   > [!IMPORTANT]
   > Denken Sie daran, dass die Konfiguration in den Lastenausgleichsregeln für die interne Azure Load Balancer-Instanz (bei Verwendung der SKU „Basic“) und die ausgewählten SAP-Instanznummern übereinstimmen müssen.

2. Führen Sie das von SAP beschriebene Installationsverfahren aus. Stellen Sie sicher, dass Sie bei der Option zum Starten der Installation für „Erster Clusterknoten“ die Konfigurationsoption „Freigegebener Clusterdatenträger“ auswählen.

> [!TIP]
> Die SAP-Installationsdokumentation beschreibt, wie der erste ASCS/SCS-Clusterknoten installiert wird.



### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Ändern des SAP-Profils der ASCS/SCS-Instanz

Wenn Sie Enqueue Replication Server 1 ausführen, fügen Sie den SAP-Profilparameter `enque/encni/set_so_keepalive` wie nachfolgend beschrieben hinzu. Dieser Profilparameter verhindert das Schließen von Verbindungen zwischen SAP-Workprozessen und dem Enqueue-Server, wenn diese sich zu lange im Leerlauf befinden. Der SAP-Parameter ist für ERS2 nicht erforderlich. 

1. Wenn Sie ERS1 verwenden, fügen Sie diesen Profilparameter dem Profil der SAP ASCS/SCS-Instanz hinzu.

   ```
   enque/encni/set_so_keepalive = true
   ```
   
   Stellen Sie sowohl für ERS1 als auch für ERS2 sicher, dass die `keepalive`-Parameter für das Betriebssystem wie im SAP-Hinweis [1410736](https://launchpad.support.sap.com/#/notes/1410736) beschrieben festgelegt sind.   

2. Starten Sie die SAP ASCS/SCS-Instanz neu, um die Änderungen der SAP-Profilparameter zu übernehmen.

### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Hinzufügen eines Testports

Nutzen Sie die Testfunktionalität des internen Lastenausgleichs, damit die gesamte Clusterkonfiguration mit Azure Load Balancer funktioniert. Der interne Azure Load Balancer verteilt in der Regel die eingehende Workload gleichmäßig auf die beteiligten virtuellen Computer.

Allerdings funktioniert dies bei einigen Clusterkonfigurationen nicht, da nur eine Instanz aktiv ist. Die andere Instanz ist passiv und kann daher keine Workload annehmen. Eine Testfunktion hilft, wenn der interne Azure Load Balancer erkennt, welche Instanz aktiv ist, und nur die aktive Instanz zum Ziel hat.  

> [!IMPORTANT]
> In dieser Beispielkonfiguration ist **ProbePort** auf „620 **Nr**“ festgelegt. Für die SAP ASCS-Instanz mit der Nummer **00** lautet der Port „620 **00**“. Sie müssen die Konfiguration entsprechend Ihren SAP-Instanznummern und Ihrer SAP-SID anpassen.

Zum Hinzufügen eines Testports führen Sie das folgende PowerShell-Modul auf einer der Cluster-VMs aus:

- Für eine SAP ASC/SCS-Instanz: 
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID SID -ProbePort 62000
   ```

- Bei Verwendung von ERS2 (gehört zu einem Cluster); für ERS1 muss kein Testport konfiguriert werden, da dieser Server nicht zu einem Cluster gehört:  
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID SID -ProbePort 62001 -IsSAPERSClusteredInstance $True
   ```

 Der Code für die Funktion `Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource` sieht wie folgt aus:
   ```powershell
    function Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource {
    
    <#
    .SYNOPSIS 
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    
    .DESCRIPTION
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    It will also restart SAP Cluster group (default behavior), to activate the changes. 
    
    You need to run it on one of the SAP ASCS/SCS Windows cluster nodes.
    
    Expectation is that SAP group is installed with official SWPM installation tool, which will set default expected naming convention for:
    - SAP Cluster Group:               'SAP $SAPSID'
    - SAP Cluster IP Address Resource: 'SAP $SAPSID IP' 
    
    .PARAMETER SAPSID 
    SAP SID - 3 characters staring with letter.
    
    .PARAMETER ProbePort 
    Azure Load Balancer Health Check Probe Port.
    
    .PARAMETER RestartSAPClusterGroup 
    Optional parameter. Default value is '$True', so SAP cluster group will be restarted to activate the changes.
    
    .PARAMETER IsSAPERSClusteredInstance 
    Optional parameter.Default value is '$False'.
    If set to $True , then handle clsutered new SAP ERS2 instance.
    
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP', and restart the SAP cluster group 'SAP AB1', to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP'. SAP cluster group 'SAP AB1' IS NOT restarted, therefore changes are NOT active.
    # To activate the changes you need to manualy restart 'SAP AB1' cluster group.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -RestartSAPClusterGroup $False
    
    .EXAMPLE 
    # Set probe port to 62001, on SAP cluster resource 'SAP AB1 ERS IP'. SAP cluster group 'SAP AB1 ERS' IS restarted, to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -IsSAPERSClusteredInstance $True
    
    #> 
    
        [CmdletBinding()]
        param(
            
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]  
            [ValidateLength(3,3)]      
            [string]$SAPSID,
                  
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]        
            [int] $ProbePort,
    
            [Parameter(Mandatory=$False)] 
            [bool] $RestartSAPClusterGroup = $True,
    
            [Parameter(Mandatory=$False)] 
            [bool] $IsSAPERSClusteredInstance = $False
        )
    
        BEGIN{}
        
        PROCESS{
            try{                                      
                
                if($IsSAPERSClusteredInstance){
                    #Handle clustered SAP ERS Instance
                    $SAPClusterRoleName = "SAP $SAPSID ERS"
                    $SAPIPresourceName = "SAP $SAPSID ERS IP"            
                }else{
                    #Handle clustered SAP ASCS/SCS Instance
                    $SAPClusterRoleName = "SAP $SAPSID"
                    $SAPIPresourceName = "SAP $SAPSID IP"
                }
    
                $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
                $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
                $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
                $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
                $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
                $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
                $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value
    
                $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }
                Write-Output "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" 
    
                Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
    
                Write-Output " "
                Write-Output "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." 
                Write-Output " "
                Write-Output "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." 
                Write-Output " "
    
                $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}
    
                Write-Output " "
                    
                if($RestartSAPClusterGroup){
                    Write-Output ""
                    Write-Output "Activating changes..." 
    
                    Write-Output " "
                    Write-Output "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..."
                    Stop-ClusterResource -Name $SAPIPresourceName
                    sleep 5
    
                    Write-Output "Starting SAP cluster role '$SAPClusterRoleName' ..."
                    Start-ClusterGroup -Name $SAPClusterRoleName
    
                    Write-Output "New ProbePort parameter is active." 
                    Write-Output " "
    
                    Write-Output "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" 
                    Write-Output " " 
                    Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
                }else
                {
                    Write-Output "SAP cluster role '$SAPClusterRoleName' is not restarted, therefore changes are not activated."
                }   
            }
            catch{
               Write-Error  $_.Exception.Message
           }
        }
        END {}
    }

   ```

### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Öffnen des Windows-Firewall-Testports

Öffnen Sie einen Windows-Firewall-Testport auf beiden Clusterknoten. Verwenden Sie das folgende Skript, um einen Windows-Firewall-Testport zu öffnen. Aktualisieren Sie die PowerShell-Variablen für Ihre Umgebung.  
Bei Verwendung von ERS2 müssen Sie auch den Firewallport für den ERS2-Testport öffnen.  

  ```powershell
    $ProbePort = 62000   # ProbePort of the Azure internal load balancer
    New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

## <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Installieren der Datenbankinstanz

Führen Sie für die Installation der Datenbankinstanz die in der SAP-Installationsdokumentation beschriebene Vorgehensweise durch.

## <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Installieren des zweiten Clusterknotens

Führen Sie zum Installieren des zweiten Clusters die im SAP-Installationshandbuch beschriebenen Schritte aus.

## <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Installieren des primären SAP-Anwendungsservers

Installieren Sie die PAS-Instanz (primärer Anwendungsserver) „\<SID\>-di-0“ auf dem virtuellen Computer, den Sie als Host für den PAS vorgesehen haben. Es gibt keine Abhängigkeiten in Azure. Bei Verwendung von SIOS gibt es keine DataKeeper-spezifischen Einstellungen.

## <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Installieren des zusätzlichen SAP-Anwendungsservers

Installieren Sie einen zusätzlichen SAP-Anwendungsserver (Additional Application Server, AAS) auf allen virtuellen Computern, die Sie als Hosts von SAP-Anwendungsserverinstanzen festgelegt haben. 

## <a name="test-the-sap-ascsscs-instance-failover"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Testen des SAP ASCS/SCS-Instanzfailovers

Bei den beschriebenen Failovertests wird angenommen, dass SAP ASCS auf Knoten A aktiv ist.  

1. Überprüfen Sie, ob das SAP-System ein Failover von Knoten A zu Knoten B ausführen kann. Initiieren Sie mithilfe einer der folgenden Optionen ein Failover der Clustergruppe „SAP \<SID\>“ vom Clusterknoten A zum Clusterknoten B:
    - Failovercluster-Manager  
    - Failovercluster-PowerShell

    ```powershell
    $SAPSID = "PR1"     # SAP <SID>
 
    $SAPClusterGroup = "SAP $SAPSID"
    Move-ClusterGroup -Name $SAPClusterGroup

    ```

2. Starten Sie Clusterknoten A unter dem Windows-Gastbetriebssystem neu. Dadurch wird ein automatisches Failover der Clustergruppe „SAP \<SID\>“ vom Knoten A auf den Knoten B initiiert.  
3. Starten Sie Clusterknoten A im Azure-Portal neu. Dadurch wird ein automatisches Failover der Clustergruppe „SAP \<SID\>“ vom Knoten A auf den Knoten B initiiert.  
4. Starten Sie Clusterknoten A mit Azure PowerShell neu. Dadurch wird ein automatisches Failover der Clustergruppe „SAP \<SID\>“ vom Knoten A auf den Knoten B initiiert.

5. Überprüfung
   - Überprüfen Sie nach dem Failover, ob die Clustergruppe „SAP \<SID\>“ auf dem Clusterknoten B ausgeführt wird. 

      ![Abbildung 8: Failovercluster-Manager: Die SAP-Clustergruppe \<SID\> wird auf Clusterknoten B ausgeführt.][sap-ha-guide-figure-5002]

      _Im Failovercluster-Manager wird die Clustergruppe „SAP \<SID\>“ auf dem Clusterknoten B ausgeführt._

   - Überprüfen Sie nach dem Failover, ob der freigegebene Datenträger jetzt auf dem Clusterknoten B eingebunden ist. 
   - Überprüfen Sie nach dem Failover mithilfe von SIOS, ob SIOS DataKeeper Daten vom Quellvolumelaufwerk S auf dem Clusterknoten B im Zielvolumelaufwerk S auf dem Clusterknoten A repliziert. 

      ![Abbildung 9: SIOS DataKeeper repliziert das lokale Volume von Clusterknoten B auf Clusterknoten A.][sap-ha-guide-figure-5003]

      _SIOS DataKeeper repliziert das lokale Volume vom Clusterknoten B auf den Clusterknoten A._