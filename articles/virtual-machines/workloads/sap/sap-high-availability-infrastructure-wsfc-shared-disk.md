---
title: Azure-Infrastruktur für SAP ASCS/SCS mit WSFC und freigegebenem Datenträger | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die Azure-Infrastruktur für SAP HA mit einem Windows-Failovercluster und einem freigegebenen Datenträger für eine SAP ASCS/SCS-Instanz vorbereiten.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 00d0aa10f2beda3d7b8508e5ca823e020dc79d95
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96489138"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Vorbereiten der Azure-Infrastruktur für SAP HA mit einem Windows-Failovercluster und einem freigegebenen Datenträger für SAP ASCS/SCS

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

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
[sap-ha-guide-9.1.1]:high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f


[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-shared-disk-vpn]:sap-high-availability-infrastructure-wsfc-shared-disk.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-high-availability-infrastructure-wsfc-shared-disk-change-def-ilb]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-wsfc]:sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-high-availability-infrastructure-wsfc-shared-disk-add-dot-net]:sap-high-availability-infrastructure-wsfc-shared-disk.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios-both-nodes]:sap-high-availability-infrastructure-wsfc-shared-disk.md#dd41d5a2-8083-415b-9878-839652812102
[sap-high-availability-infrastructure-wsfc-shared-disk-setup-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006

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

[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows-Betriebssystem][Logo_Windows] Windows


In diesem Artikel werden die Schritte zum Vorbereiten der Azure-Infrastruktur auf die Installation und Konfiguration einer hochverfügbaren SAP ASCS/SCS-Instanz in einem Windows-Failovercluster mithilfe eines *freigegebener Clusterdatenträgers* als Option für das Clustering einer SAP ASCS-Instanz beschrieben.
In der Dokumentation sind zwei Alternativen für *freigegebene Clusterdatenträger* aufgeführt:

- [Freigegebene Azure-Datenträger](../../disks-shared.md)
- Verwenden von [SIOS DataKeeper Cluster Edition](https://us.sios.com/products/datakeeper-cluster/) zum Erstellen eines gespiegelten Speichers, der freigegebenen Clusterdatenträger simuliert 

Die vorgestellte Konfiguration basiert auf [Azure-Näherungsplatzierungsgruppen (Proximity Placement Group, PPG)](./sap-proximity-placement-scenarios.md), um eine optimale Netzwerklatenz für SAP-Workloads zu erzielen. Die Dokumentation behandelt nicht die Datenbankschicht.  

> [!NOTE]
> Azure-Näherungsplatzierungsgruppen sind eine Voraussetzung für den Einsatz freigegebener Azure-Datenträger.
 

## <a name="prerequisites"></a>Voraussetzungen

Lesen Sie sich diesen Artikel durch, bevor Sie mit der Installation beginnen:

* [Architekturleitfaden: Gruppieren einer SAP ASCS/SCS-Instanz in einem Windows-Failovercluster mithilfe freigegebener Clusterdatenträger in Azure][sap-high-availability-guide-wsfc-shared-disk]

## <a name="create-the-ascs-vms"></a>Erstellen der ASCS-VMs

Stellen Sie für den SAP ASCS/SCS-Cluster zwei VMs in der Azure-Verfügbarkeitsgruppe bereit. Stellen Sie die VMs in derselben Näherungsplatzierungsgruppe bereit. Nach der Bereitstellung der VMs:  
- Erstellen Sie einen internen Azure Load Balancer für die SAP ASCS/SCS-Instanz. 
- Fügen Sie der AD-Domäne Windows-VMs hinzu.

Die Hostnamen und die IP-Adressen für das vorgestellte Szenario lauten wie folgt:

| Hostnamenrolle | Hostname | Statische IP-Adresse | Verfügbarkeitsgruppe | Näherungsplatzierungsgruppe |
| --- | --- | --- |---| ---|
| Erster ASCS-/SCS-Cluster des Clusterknotens |pr1-ascs-10 |10.0.0.4 |pr1-ascs-avset |PR1PPG |
| Zweiter ASCS-/SCS-Cluster des Clusterknotens |pr1-ascs-11 |10.0.0.5 |pr1-ascs-avset |PR1PPG |
| Name des Clusternetzwerks | pr1clust |10.0.0.42 (**nur** für Win 2016-Cluster) | – | – |
| Name des ASCS-Clusternetzwerks | pr1-ascscl |10.0.0.43 | – | – |
| Name des ERS-Clusternetzwerks (**nur** für ERS2) | pr1-erscl |10.0.0.44 | – | – |


## <a name="create-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Erstellen einer internen Azure Load Balancer-Instanz

SAP ASCS, SAP SCS und das neue SAP-ERS2 verwenden einen virtuellen Hostnamen und virtuelle IP-Adressen. Für die Verwendung einer virtuellen IP-Adresse ist in Azure ein [Lastenausgleich](../../../load-balancer/load-balancer-overview.md) erforderlich. Es wird dringend empfohlen, [Load Balancer Standard](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md) zu verwenden. 

> [!IMPORTANT]
> Floating IP-Adressen werden für sekundäre NIC-IP-Konfigurationen in Szenarien mit Lastenausgleich nicht unterstützt. Weitere Informationen finden Sie unter [Azure Load Balancer – Einschränkungen](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Wenn Sie zusätzliche IP-Adressen für die VM benötigen, stellen Sie eine zweite NIC bereit.    


Die folgende Liste zeigt die Konfiguration des (A)SCS-/ERS-Lastenausgleichs. Die Konfiguration für SAP ASCS und ERS2 erfolgt in derselben Azure Load Balancer-Instanz.  

**(A)SCS**
- Frontendkonfiguration
    - Statische ASCS-/SCS-IP-Adresse **10.0.0.43**
- Backendkonfiguration  
    Fügen Sie alle virtuellen Computer hinzu, die Teil des (A)SCS-/ERS-Clusters sein sollen. In diesem Beispiel sind dies die VMs **pr1-ascs-10** und **pr1-ascs-11**.
- Testport
    - Port 620 **nr**: Lassen Sie die Standardoptionen für das Protokoll (TCP), das Intervall (5) und den Fehlerschwellenwert (2) unverändert.
- Lastenausgleichsregeln
    - Wenn Sie Load Balancer Standard verwenden, wählen Sie HA-Ports aus.
    - Wenn Sie Load Balancer Basic verwenden, erstellen Sie Lastenausgleichsregeln für die folgenden Ports:
        - 32 **Nr.** TCP
        - 36 **Nr.** TCP
        - 39 **Nr.** TCP
        - 81 **Nr.** TCP
        - 5 **Nr.** 13 TCP
        - 5 **Nr.** 14 TCP
        - 5 **Nr.** 16 TCP

    - Stellen Sie sicher, dass „Leerlauftimeout (Minuten)“ auf den Höchstwert 30 festgelegt ist und dass „Floating IP (Direct Server Return)“ aktiviert ist.

**ERS2**

Da Enqueue Replication Server 2 (ERS2) ebenfalls in Cluster unterteilt ist, muss die virtuelle IP-Adresse von ERS2 zusätzlich zur SAP ASCS-/SCS-IP-Adresse ebenfalls in Azure ILB konfiguriert werden. Dieser Abschnitt gilt nur, wenn Sie die Enqueue Replication Server 2-Architektur verwenden.  
- Konfiguration des zweiten Front-Ends
    - Statische SAP ERS2-IP-Adresse **10.0.0.44**

- Backendkonfiguration  
  Die VMs wurden dem ILB-Back-End-Pool bereits hinzugefügt.  

- Zweiter Testport
    - Port 621 **nr**  
    Lassen Sie die Standardoptionen für das Protokoll (TCP), das Intervall (5) und den Fehlerschwellenwert (2) unverändert.

- Regeln für den zweiten Lastenausgleich
    - Wenn Sie Load Balancer Standard verwenden, wählen Sie HA-Ports aus.
    - Wenn Sie Load Balancer Basic verwenden, erstellen Sie Lastenausgleichsregeln für die folgenden Ports:
        - 32 **Nr.** TCP
        - 33 **Nr.** TCP
        - 5 **Nr.** 13 TCP
        - 5 **Nr.** 14 TCP
        - 5 **Nr.** 16 TCP

    - Stellen Sie sicher, dass „Leerlauftimeout (Minuten)“ auf den Höchstwert 30 festgelegt ist und dass „Floating IP (Direct Server Return)“ aktiviert ist.


> [!TIP]
> Mit der [Azure Resource Manager-Vorlage für WSFC für die SAP ASCS-/SCS-Instanz mit freigegebenem Azure-Datenträger](https://github.com/robotechredmond/301-shared-disk-sap) können Sie die Vorbereitung der Infrastruktur mit einem freigegebenen Azure-Datenträger für eine SAP-SID mit ERS1 automatisieren.  
> Die ARM-Vorlage erstellt zwei VMs mit Windows 2019 oder 2016 sowie einen freigegebenen Azure-Datenträger und fügt diesen an die VMs an. Außerdem wird eine interne Azure Load Balancer-Instanz erstellt und konfiguriert. Weitere Informationen finden Sie in der ARM-Vorlage. 

## <a name="add-registry-entries-on-both-cluster-nodes-of-the-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Hinzufügen von Registrierungseinträgen auf beiden Clusterknoten der ASCS-/SCS-Instanz

Azure Load Balancer kann Verbindungen schließen, wenn sich diese Verbindungen längere Zeit im Leerlauf befinden und das Leerlauftimeout überschreiten. SAP-Arbeitsprozesse öffnen Verbindungen mit dem SAP-Enqueue-Prozess, sobald die erste Anforderung zum Einstellen in eine Warteschlange bzw. zum Entfernen aus dieser gesendet werden muss. Um Unterbrechungen dieser Verbindungen zu vermeiden, ändern Sie die TCP/IP-Werte für KeepAliveTime und KeepAliveInterval auf beiden Clusterknoten. Wenn Sie ERS1 verwenden, müssen Sie auch SAP-Profilparameter hinzufügen, wie weiter unten in diesem Artikel beschrieben.
Die folgenden Registrierungseinträge müssen auf beiden Clusterknoten geändert werden:

- KeepAliveTime
- KeepAliveInterval

| `Path`| Variablenname | Variablentyp  | value | Dokumentation |
| --- | --- | --- |---| ---|
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |KeepAliveTime |REG_DWORD-Wert (dezimal) |120000 |[KeepAliveTime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)) |
| HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |KeepAliveInterval |REG_DWORD-Wert (dezimal) |120000 |[KeepAliveInterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) |


Starten Sie beide Clusterknoten neu, um die Änderungen zu übernehmen.
  
## <a name="add-the-windows-vms-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Hinzufügen der Windows-VMs zur Domäne
Nachdem Sie den virtuellen Computern statische IP-Adressen zugewiesen haben, fügen Sie die virtuellen Computer der Domäne hinzu. 

## <a name="install-and-configure--windows-failover-cluster"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Installieren und Konfigurieren eines Windows-Failoverclusters 

### <a name="install-the-windows-failover-cluster-feature"></a>Installieren des Windows-Failoverclusterfeatures

Führen Sie den Befehl auf einem der Clusterknoten aus:

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # Install Windows features.
    # After the feature installs, manually reboot both nodes
    Invoke-Command $ClusterNodes {Install-WindowsFeature Failover-Clustering, FS-FileServer -IncludeAllSubFeature -IncludeManagementTools }
   ```

Nachdem die Installation des Features abgeschlossen ist, starten Sie beide Clusterknoten neu.  

### <a name="test-and-configure-windows-failover-cluster"></a>Testen und Konfigurieren des Windows-Failoverclusters 

Unter Windows 2019 erkennt der Cluster automatisch, dass er in Azure ausgeführt wird. Als Standardoption für die Clusterverwaltungs-IP-Adresse wird der Name des verteilten Netzwerks verwendet. Daher wird eine beliebige lokale IP-Adresse der Clusterknoten verwendet. Aus diesem Grund ist kein Name eines dedizierten (virtuellen) Netzwerks für den Cluster erforderlich, und diese IP-Adresse muss nicht in der internen Instanz von Azure Load Balancer konfiguriert werden.

Weitere Informationen finden Sie unter [Neue Features für Windows Server 2019-Failovercluster](https://techcommunity.microsoft.com/t5/failover-clustering/windows-server-2019-failover-clustering-new-features/ba-p/544029). Führen Sie diesen Befehl auf einem der Clusterknoten aus:

   ```powershell
    # Hostnames of the Win cluster for SAP ASCS/SCS
    $SAPSID = "PR1"
    $ClusterNodes = ("pr1-ascs-10","pr1-ascs-11")
    $ClusterName = $SAPSID.ToLower() + "clust"
    
    # IP adress for cluster network name is needed ONLY on Windows Server 2016 cluster
    $ClusterStaticIPAddress = "10.0.0.42"
        
    # Test cluster
    Test-Cluster –Node $ClusterNodes -Verbose
    
    $ComputerInfo = Get-ComputerInfo
    
    $WindowsVersion = $ComputerInfo.WindowsProductName
    
    if($WindowsVersion -eq "Windows Server 2019 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2019 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes -Verbose
    }elseif($WindowsVersion -eq "Windows Server 2016 Datacenter"){
        write-host "Configuring Windows Failover Cluster on Windows Server 2016 Datacenter..."
        New-Cluster –Name $ClusterName –Node  $ClusterNodes –StaticAddress $ClusterStaticIPAddress -Verbose 
    }else{
        Write-Error "Not supported Windows version!"
    }
   ```

### <a name="configure-cluster-cloud-quorum"></a>Konfigurieren des Clustercloudquorums
Wenn Sie Windows Server 2016 oder 2019 verwenden, empfiehlt es sich, einen [Azure-Cloudzeugen](/windows-server/failover-clustering/deploy-cloud-witness) als Clusterquorum zu konfigurieren.

Führen Sie den Befehl auf einem der Clusterknoten aus:

   ```powershell
    $AzureStorageAccountName = "cloudquorumwitness"
    Set-ClusterQuorum –CloudWitness –AccountName $AzureStorageAccountName -AccessKey <YourAzureStorageAccessKey> -Verbose
   ```

### <a name="tuning-the-windows-failover-cluster-thresholds"></a>Abstimmen der Schwellenwerte des Windows-Failoverclusters
 
Nachdem Sie den Windows-Failovercluster installiert haben, müssen Sie einige Schwellenwerte anpassen, damit der Cluster in Azure bereitgestellt werden kann. Die zu ändernden Parameter sind unter [Optimieren der Netzwerkschwellenwerte für Failovercluster](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) dokumentiert. Wenn sich Ihre beiden virtuellen Computer, aus denen die Windows-Clusterkonfiguration für ASCS/SCS besteht, in demselben Subnetz befinden, müssen Sie die folgenden Parameter in diese Werte ändern:
- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

Diese Einstellungen wurden bei Kunden getestet und stellen einen guten Kompromiss dar. Sie sind hinreichend stabil, ermöglichen aber gleichzeitig ein Failover, das unter echten Fehlerbedingungen in SAP-Workloads oder bei einem VM-Ausfall schnell genug durchgeführt werden kann.  

## <a name="configure-azure-shared-disk"></a>Konfigurieren des freigegebenen Azure-Datenträgers
Dieser Abschnitt gilt nur, wenn Sie einen freigegebenen Azure-Datenträger verwenden. 

### <a name="create-and-attach-azure-shared-disk-with-powershell"></a>Erstellen und Anfügen eines freigegebenen Azure-Datenträgers mit PowerShell
Führen Sie diesen Befehl auf einem der Clusterknoten aus. Sie müssen die Werte für Ihre Ressourcengruppe, die Azure-Region, die SAP-SID usw. anpassen.  

   ```powershell
    #############################
    # Create Azure Shared Disk
    #############################
    
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyAzureRegion"
    $SAPSID = "PR1"
    
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    
    # With parameter '-MaxSharesCount', we define the maximum number of cluster nodes to attach the shared disk
    $NumberOfWindowsClusterNodes = 2
            
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "$SAPSID-ascs-10"
    
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "$SAPSID-ascs-11"
    
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```

### <a name="format-the-shared-disk-with-powershell"></a>Formatieren des freigegebenen Datenträgers mit PowerShell
1. Rufen Sie die Datenträgernummer ab. Führen Sie diese PowerShell-Befehle auf einem der Clusterknoten aus:

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 2      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. Formatieren Sie den Datenträger. In diesem Beispiel handelt es sich um Datenträger Nr. 2. 

   ```powershell
    # Format SAP ASCS Disk number '2', with drive letter 'S'
    $SAPSID = "PR1"
    $DiskNumber = 2
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR1SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. Vergewissern Sie sich, dass der Datenträger jetzt als Clusterdatenträger sichtbar ist.  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : 88ff1d94-0cf1-4c70-89ae-cbbb2826a484
    # Name       : Cluster Disk 1
    # Number     : 2
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk2\Partition2\}
   ```
4. Registrieren Sie den Datenträger im Cluster.  
   ```powershell
    # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output     
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 1 Online Available Storage Physical Disk
   ```

## <a name="sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Installieren von SIOS DataKeeper Cluster Edition für den SAP ASCS-/SCS-Clusterfreigabe-Datenträger
Dieser Abschnitt gilt nur, wenn Sie die Drittanbietersoftware SIOS DataKeeper Cluster Edition zum Erstellen eines gespiegelten Speichers verwenden, der einen freigegebenen Clusterdatenträger simuliert.  

Sie verfügen nun über eine funktionierende Windows Server-Failoverclusteringkonfiguration in Azure. Um eine SAP ASCS/SCS-Instanz installieren zu können, benötigen Sie eine freigegebene Datenträgerressource. Eine der Optionen ist SIOS DataKeeper Cluster Edition, eine Drittanbieterlösung, die Sie zum Erstellen von freigegebenen Datenträgerressourcen verwenden können.  

Das Installieren von SIOS DataKeeper Cluster Edition für SAP ASCS/SCS-Clusterfreigabe-Datenträger umfasst die folgenden Aufgaben:
- Fügen Sie bei Bedarf Microsoft .NET Framework hinzu. Aktuelle .NET Framework-Anforderungen finden Sie in der [SIOS-Dokumentation](https://us.sios.com/products/datakeeper-cluster/). 
-  Installieren von SIOS DataKeeper
- Konfigurieren von SIOS DataKeeper

### <a name="install-sios-datakeeper"></a> Installieren von SIOS DataKeeper
Installieren Sie SIOS DataKeeper Cluster Edition auf jedem Knoten im Cluster. Erstellen Sie eine synchronisierte Spiegelung, und simulieren Sie dann freigegebenen Clusterspeicher, um mit SIOS DataKeeper virtuellen freigegebenen Speicher zu erstellen.

Erstellen Sie vor der Installation der SIOS-Software den Domänenbenutzer DataKeeperSvc.

> [!NOTE]
> Fügen Sie den Domänenbenutzer DataKeeperSvc der Gruppe „Lokale Administratoren“ auf beiden Clusterknoten hinzu.
>

1. Installieren Sie die SIOS-Software auf beiden Clusterknoten.

   ![SIOS-Installationsprogramm][sap-ha-guide-figure-3030]

   ![Abbildung 31: Erste Seite der SIOS DataKeeper-Installation][sap-ha-guide-figure-3031]

   _Erste Seite der SIOS DataKeeper-Installation_

2. Klicken Sie im Dialogfeld auf **Ja**.

   ![Abbildung 32: Benachrichtigung über die Deaktivierung eines Diensts in DataKeeper][sap-ha-guide-figure-3032]

   _Benachrichtigung über die Deaktivierung eines Diensts in DataKeeper_

3. Es wird empfohlen, im Dialogfeld die Option **Domain or Server account** (Domänen- oder Serverkonto) auszuwählen.

   ![Abbildung 33: Benutzerauswahl für SIOS DataKeeper][sap-ha-guide-figure-3033]

   _Benutzerauswahl für SIOS DataKeeper_

4. Geben Sie den Benutzernamen für das Domänenkonto, den Sie für SIOS DataKeeper erstellt haben, mit dem dazugehörigen Kennwort ein.

   ![Abbildung 34: Eingeben des Domänenbenutzernamens und des Kennworts für die SIOS DataKeeper-Installation][sap-ha-guide-figure-3034]

   _Eingeben des Domänenbenutzernamens und des Kennworts für die SIOS DataKeeper-Installation_

5. Installieren Sie den Lizenzschlüssel für Ihre SIOS DataKeeper-Instanz, wie in Abbildung 35 gezeigt.

   ![Abbildung 35: Eingeben Ihres SIOS DataKeeper-Lizenzschlüssels][sap-ha-guide-figure-3035]

   _Eingeben Ihres SIOS DataKeeper-Lizenzschlüssels_

6. Starten Sie den virtuellen Computer neu, wenn Sie dazu aufgefordert werden.

### <a name="configure-sios-datakeeper"></a>Konfigurieren von SIOS DataKeeper
Beginnen Sie nach der Installation von SIOS DataKeeper auf beiden Knoten mit der Konfiguration. Ziel der Konfiguration ist eine synchrone Datenreplikation zwischen den zusätzlichen Datenträgern, die Sie an jeden virtuellen Computer angefügt haben.

1. Starten Sie das DataKeeper-Tool für die Verwaltung und Konfiguration, und wählen Sie dann **Verbindung mit Server herstellen** aus.

   ![Abbildung 36: SIOS DataKeeper-Tool für die Verwaltung und Konfiguration][sap-ha-guide-figure-3036]

   _SIOS DataKeeper-Tool für die Verwaltung und Konfiguration_

2. Geben Sie den Namen oder die TCP/IP-Adresse des ersten Knotens, mit dem das Tool für die Verwaltung und Konfiguration eine Verbindung herstellen soll, und im zweiten Schritt den zweiten Knoten ein.

   ![Abbildung 37: Eingeben des Namens oder der TCP/IP-Adresse des ersten Knotens, mit dem das Tool für die Verwaltung und Konfiguration eine Verbindung herstellen soll, und im zweiten Schritt Eingeben des zweiten Knotens][sap-ha-guide-figure-3037]

   _Eingeben des Namens oder der TCP/IP-Adresse des ersten Knotens, mit dem das Tool für die Verwaltung und Konfiguration eine Verbindung herstellen soll, und im zweiten Schritt Eingeben des zweiten Knotens_

3. Erstellen Sie den Replikationsauftrag zwischen den beiden Knoten.

   ![Abbildung 38: Erstellen eines Replikationsauftrags][sap-ha-guide-figure-3038]

   _Erstellen eines Replikationsauftrags_

   Ein Assistent führt Sie durch die Erstellung eines Replikationsauftrags.

4. Definieren Sie den Namen des Replikationsauftrags.

   ![Abbildung 39: Festlegen des Namens für den Replikationsauftrag][sap-ha-guide-figure-3039]

   _Festlegen des Namens für den Replikationsauftrag_

   ![Abbildung 40: Definieren der Basisdaten für den Knoten, der der aktuelle Quellknoten sein soll][sap-ha-guide-figure-3040]

   _Definieren der Basisdaten für den Knoten, der der aktuelle Quellknoten sein soll_

5. Definieren Sie Name, TCP/IP-Adresse und Datenträgervolume des Zielknotens.

   ![Abbildung 41: Definieren von Name, TCP/IP-Adresse und Datenträgervolume des aktuellen Zielknotens][sap-ha-guide-figure-3041]

   _Definieren von Name, TCP/IP-Adresse und Datenträgervolume des aktuellen Zielknotens_

6. Definieren Sie die Komprimierungsalgorithmen. Für unser Beispiel wird das Komprimieren des Replikationsdatenstroms empfohlen. Insbesondere in Situationen mit erneuter Synchronisierung wird die dafür erforderliche Dauer durch die Komprimierung des Replikationsdatenstroms erheblich verkürzt. Die Komprimierung verbraucht CPU- und Arbeitsspeicherressourcen eines virtuellen Computers. Wenn die Komprimierungsrate zunimmt, steigt auch die Menge der verwendeten CPU-Ressourcen. Sie können diese Einstellung später anpassen.

7. Eine weitere Einstellung, die Sie überprüfen müssen, ist, ob die Replikation asynchron oder synchron erfolgt. Wenn Sie SAP ASCS/SCS-Konfigurationen schützen, müssen Sie die synchrone Replikation verwenden.  

   ![Abbildung 42: Festlegen der Replikationsdetails][sap-ha-guide-figure-3042]

   _Festlegen der Replikationsdetails_

8. Definieren Sie, ob das Volume, das vom Replikationsauftrag repliziert wird, gegenüber einer Windows Server-Failoverclusterkonfiguration als freigegebener Datenträger dargestellt werden sollte. Für die SAP ASCS/SCS-Konfiguration wählen Sie **Ja** aus, damit der Windows-Cluster das replizierte Volume als freigegebenen Datenträger erkennt, das als Clustervolume verwendet werden kann.

   ![Abbildung 43: Klicken auf „Ja“, um das replizierte Volume als Clustervolume festzulegen][sap-ha-guide-figure-3043]

   _Auswählen von **Ja**, um das replizierte Volume als Clustervolume festzulegen_

   Nachdem das Volume erstellt wurde, zeigt das DataKeeper-Tool für die Verwaltung und Konfiguration an, dass der Replikationsauftrag aktiv ist.

   ![Abbildung 44: Die synchrone Spiegelung von DataKeeper für den SAP ASCS/SCS-Freigabedatenträger ist aktiv.][sap-ha-guide-figure-3044]

   _Die synchrone Spiegelung von DataKeeper für den SAP ASCS/SCS-Freigabedatenträger ist aktiv._

   Der Failovercluster-Manager zeigt nun den Datenträger als DataKeeper-Datenträger an, wie in Abbildung 45 dargestellt:

   ![Abbildung 45: Anzeige des von DataKeeper replizierten Datenträgers im Failovercluster-Manager][sap-ha-guide-figure-3045]

   _Anzeige des von DataKeeper replizierten Datenträgers im Failovercluster-Manager_


## <a name="next-steps"></a>Nächste Schritte

* [SAP NetWeaver-HA-Installation auf einem Windows-Failovercluster und freigegebenen Datenträger für eine SAP ASCS/SCS-Instanz in Azure][sap-high-availability-installation-wsfc-shared-disk]