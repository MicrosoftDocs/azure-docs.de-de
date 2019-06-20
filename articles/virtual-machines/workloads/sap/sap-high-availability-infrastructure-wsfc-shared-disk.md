---
title: Vorbereiten der Azure-Infrastruktur für SAP HA mit einem Windows-Failovercluster und einem freigegebenen Datenträger für SAP ASCS/SCS | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die Azure-Infrastruktur für SAP HA mit einem Windows-Failovercluster und einem freigegebenen Datenträger für eine SAP ASCS/SCS-Instanz vorbereiten.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ec976257-396b-42a0-8ea1-01c97f820fa6
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ae3d1b36b89bb1bce1ff384bfa12a1bf643614fd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65408773"
---
# <a name="prepare-the-azure-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster-and-shared-disk-for-sap-ascsscs"></a>Vorbereiten der Azure-Infrastruktur für SAP HA mit einem Windows-Failovercluster und einem freigegebenen Datenträger für SAP ASCS/SCS

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides
[tuning-failover-cluster-network-thresholds]:https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834

[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md

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

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/resource-group-overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md


> ![Windows][Logo_Windows] Windows
>

In diesem Artikel werden die Schritte zum Vorbereiten der Azure-Infrastruktur auf die Installation und Konfiguration eines hochverfügbaren SAP-Systems in einem Windows-Failovercluster beschrieben, indem ein *freigegebener Clusterdatenträger* als Option für das Clustering einer SAP ASCS-Instanz verwendet wird.

## <a name="prerequisites"></a>Voraussetzungen

Lesen Sie sich diesen Artikel durch, bevor Sie mit der Installation beginnen:

* [Architekturleitfaden: Gruppieren einer SAP ASCS/SCS-Instanz in einem Windows-Failovercluster mithilfe freigegebener Clusterdatenträger][sap-high-availability-guide-wsfc-shared-disk]

## <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Vorbereiten der Infrastruktur für Architekturvorlage 1
Azure Resource Manager-Vorlagen für SAP vereinfachen die Bereitstellung der erforderlichen Ressourcen.

Die Vorlagen mit drei Ebenen in Azure Resource Manager unterstützen auch Hochverfügbarkeitsszenarien. Die Architekturvorlage 1 hat zwei Cluster. Jeder Cluster ist ein SAP-Single Point of Failure für SAP ASCS/SCS und das DBMS.

Hier können Sie die Azure Resource Manager-Vorlagen für das in diesem Artikel beschriebene Beispielszenario abrufen:

* [Azure Marketplace-Image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [Azure Marketplace image by using Azure Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md) (Azure Marketplace-Image mit Azure Managed Disks)  
* [Benutzerdefiniertes Image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)
* [Benutzerdefiniertes Image mit Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

So bereiten Sie die Infrastruktur für Architekturvorlage 1 vor

- Wählen Sie im Azure-Portal im Bereich **Parameter** im Feld **SYSTEMAVAILABILITY** die Option **HA**.

  ![Abbildung 1: Festlegen von Azure Resource Manager-Parametern für die Hochverfügbarkeit von SAP][sap-ha-guide-figure-3000]

_**Abbildung 1:** Festlegen von Azure Resource Manager-Parametern für die Hochverfügbarkeit von SAP_


  Mit den Vorlagen wird Folgendes erstellt:

  * **Virtuelle Computer:**
    * Virtuelle Computer für SAP-Anwendungsserver: \<SAPSystemSID\>-di-\<Anzahl\>
    * Virtuelle ASCS/SCS-Clustercomputer: \<SAPSystemSID\>-ascs-\<Anzahl\>
    * DBMS-Cluster: \<SAPSystemSID\>-db-\<Anzahl\>

  * **Netzwerkkarten für alle virtuellen Computer mit zugeordneten IP-Adressen:**
    * \<SAPSystemSID\>-nic-di-\<Anzahl\>
    * \<SAPSystemSID\>-nic-ascs-\<Anzahl\>
    * \<SAPSystemSID\>-nic-db-\<Anzahl\>

  * **Azure-Speicherkonten (nur nicht verwaltete Datenträger)** :

  * **Verfügbarkeitsgruppen** für:
    * Virtuelle Computer für SAP-Anwendungsserver: \<SAPSystemSID\>-avset-di
    * Virtuelle SAP ASCS/SCS-Clustercomputer: \<SAPSystemSID\>-avset-ascs
    * Virtuelle DBMS-Clustercomputer: \<SAPSystemSID\>-avset-db

  * **Interner Azure Load Balancer:**
    * Mit allen Ports für die ASCS/SCS-Instanz und IP-Adresse \<SAPSystemSID\>-lb-ascs
    * Mit allen Ports für das SQL Server-DBMS und IP-Adresse \<SAPSystemSID\>-lb-db

  * **Netzwerksicherheitsgruppe**: \<SAPSystemSID\>-nsg-ascs-0  
    * Mit einem offenen externen RDP-Port (Remotedesktopprotokoll) zum virtuellen Computer \<SAPSystemSID\>-ascs-0

> [!NOTE]
> Sämtliche IP-Adressen für die Netzwerkkarten und den internen Azure Load Balancer sind standardmäßig dynamisch. Ändern Sie diese in statische IP-Adressen. Die Vorgehensweise wird weiter unten in diesem Artikel beschrieben.
>
>

## <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Bereitstellen virtueller Computer mit Verbindungen mit dem Unternehmensnetzwerk (standortübergreifend) für die Verwendung in der Produktion
Stellen Sie für SAP-Produktionssysteme virtuelle Azure-Computer mit einer [(standortübergreifenden) Verbindung mit dem Unternehmensnetzwerk][planning-guide-2.2] bereit. Verwenden Sie hierzu Azure VPN Gateway oder Azure ExpressRoute.

> [!NOTE]
> Sie können Ihre Azure Virtual Network-Instanz verwenden. Das virtuelle Netzwerk und das Subnetz wurden bereits erstellt und vorbereitet.
>
>

1. Wählen Sie im Azure-Portal unter **Parameter** im Feld **NEWOREXISTINGSUBNET** die Option **existing**.
2. Fügen Sie im Feld **SUBNETID** die vollständige Zeichenfolge Ihrer vorbereiteten Subnetz-ID für das Azure-Netzwerk ein, in dem Sie Ihre virtuellen Azure-Computer bereitstellen möchten.
3. Führen Sie diesen PowerShell-Befehl aus, um eine Liste mit allen Azure-Netzwerksubnetzen abzurufen:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   Im Feld **ID** wird der Wert für die Subnetz-ID angezeigt.
4. Führen Sie diesen PowerShell-Befehl aus, um eine Liste mit allen Subnetz-ID-Werten abzurufen:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   Die Subnetz-ID sieht in etwa wie folgt aus:

   ```
   /subscriptions/<subscription ID>/resourceGroups/<VPN name>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<subnet name>
   ```

## <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Bereitstellen von auf die Cloud beschränkten SAP-Instanzen für Tests und Demos
Sie können Ihr SAP-System mit hoher Verfügbarkeit in einem reinen Cloudbereitstellungsmodell bereitstellen. Diese Art der Bereitstellung eignet sich hauptsächlich für Demonstrations- und Testzwecke. Es eignet sich nicht für den Einsatz in Produktionsumgebungen.

- Wählen Sie im Azure-Portal unter **Parameter** im Feld **NEWOREXISTINGSUBNET** die Option **new**. Lassen Sie das Feld **SUBNETID** leer.

  Das virtuelle Azure-Netzwerk und das Subnetz werden von der Azure Resource Manager-Vorlage für SAP automatisch erstellt.

> [!NOTE]
> Außerdem müssen Sie mindestens einen dedizierten virtuellen Computer für Active Directory und den DNS-Dienst in der gleichen Instanz von Azure Virtual Network bereitstellen. Die Vorlage erstellt diese virtuellen Computer nicht.
>
>


## <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Vorbereiten der Infrastruktur für Architekturvorlage 2

Sie können diese Azure Resource Manager-Vorlage für SAP zur Vereinfachung der Bereitstellung der erforderlichen Infrastrukturressourcen für SAP-Architekturvorlage 2 verwenden.

Azure Resource Manager-Vorlagen für dieses Bereitstellungsszenario sind hier verfügbar:

* [Azure Marketplace-Image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged)  
* [Azure Marketplace-Image mit Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Benutzerdefiniertes Image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged)
* [Benutzerdefiniertes Image mit Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


## <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Vorbereiten der Infrastruktur für Architekturvorlage 3

Sie können die Infrastruktur vorbereiten und SAP für Multi-SID konfigurieren. Beispielsweise können Sie eine weitere SAP ASCS/SCS-Instanz einer *vorhandenen* Clusterkonfiguration hinzufügen. Weitere Informationen finden Sie unter [Konfigurieren einer zusätzlichen SAP ASCS/SCS-Instanz für eine vorhandene Clusterkonfiguration zum Erstellen einer Multi-SID-SAP-Konfiguration in Azure Resource Manager][sap-ha-multi-sid-guide].

Wenn Sie einen neuen Multi-SID-Cluster erstellen möchten, können Sie die Multi-SID-[Schnellstartvorlagen auf GitHub](https://github.com/Azure/azure-quickstart-templates) verwenden.

Sie müssen die folgenden drei Vorlagen bereitstellen, um einen neuen Multi-SID-Cluster zu erstellen:

* [ASCS/SCS-Vorlage](#ASCS-SCS-template)
* [Datenbankvorlage](#database-template)
* [Anwendungsservervorlage](#application-servers-template)

Die folgenden Abschnitte enthalten weitere Details zu den Vorlagen und Parametern, die Sie in den Vorlagen angeben müssen.

### <a name="ASCS-SCS-template"></a> ASCS/SCS-Vorlage

Mit der ASCS/SCS-Vorlage werden zwei virtuelle Computer bereitgestellt, die Sie verwenden können, um einen Windows-Failovercluster zum Hosten von mehreren ASCS/SCS-Instanzen zu erstellen.

Geben Sie in der [ASCS/SCS-Multi-SID-Vorlage][sap-templates-3-tier-multisid-xscs-marketplace-image] oder der [ASCS/SCS-Multi-SID-Vorlage mit Managed Disks][sap-templates-3-tier-multisid-xscs-marketplace-image-md] Werte für die folgenden Parameter ein, um die ASCS/SCS-Multi-SID-Vorlage einzurichten:

- **Ressourcenpräfix**:  Legen Sie das Ressourcenpräfix fest, um alle Ressourcen, die während der Bereitstellung erstellt werden, mit einem Präfix zu versehen. Da die Ressourcen nicht nur zu einem einzigen SAP-System gehören, ist das Präfix der Ressource nicht die SID eines einzigen SAP-Systems.  Das Präfix muss zwischen drei und sechs Zeichen lang sein.
- **Stapeltyp:** Wählen Sie den Stapeltyp des SAP-Systems aus. Abhängig vom Stapeltyp hat der Azure Load Balancer eine (nur ABAP oder Java) oder zwei (ABAP+Java) private IP-Adressen pro SAP-System.
- **OS Type** (Betriebssystemtyp): Wählen Sie das Betriebssystem des virtuellen Computers aus.
- **Anzahl von SAP-Systemen**: Wählen Sie die Anzahl von SAP-Systemen aus, die Sie in diesem Cluster installieren möchten.
- **Systemverfügbarkeit**: Wählen Sie **HA** (Hohe Verfügbarkeit).
- **Administratorbenutzername und Administratorkennwort:** Erstellen Sie einen neuen Benutzer, der verwendet werden kann, um sich am Computer anzumelden.
- **Neues oder vorhandenes Subnetz:** Legen Sie fest, ob ein neues virtuelles Netzwerk und Subnetz erstellt oder ein vorhandenes Subnetz verwendet werden soll. Wenn Sie bereits über ein virtuelles Netzwerk verfügen, das mit dem lokalen Netzwerk verbunden ist, wählen Sie hier **vorhanden** aus.
- **Subnetz-ID**: Wenn Sie die VM in einem vorhandenen VNET bereitstellen möchten, in dem Sie ein Subnetz definiert haben, dem die VM zugewiesen werden soll, geben Sie die ID dieses spezifischen Subnetzes an. Die ID sieht normalerweise wie folgt aus:

  /subscriptions/\<Abonnement-ID\>/resourceGroups/\<Ressourcengruppenname\>/providers/Microsoft.Network/virtualNetworks/\<Name des virtuellen Netzwerks\>/subnets/\<Subnetzname\>

Die Vorlage stellt eine Azure Load Balancer-Instanz bereit, die mehrere SAP-Systeme unterstützt:

- Die ASCS-Instanzen werden für die Instanznummer 00, 10, 20... konfiguriert.
- Die SCS-Instanzen werden für die Instanznummer 01, 11, 21... konfiguriert.
- Die Instanzen von ASCS Enqueue Replication Server (ERS) (nur Linux) werden für die Instanznummern 02, 12, 22 usw. konfiguriert.
- Die SCS ERS-Instanzen (nur Linux) werden für die Instanznummer 03, 13, 23... konfiguriert.

Der Load Balancer enthält 1 VIP (2 für Linux), 1 VIP für ASCS/SCS und 1 VIP für ERS (nur Linux).

#### <a name="0f3ee255-b31e-4b8a-a95a-d9ed6200468b"></a> SAP ASCS/SCS-Ports
Die folgende Liste enthält alle Lastenausgleichsregeln (wobei x für die Nummer des SAP-Systems steht, z.B. 1, 2, 3...):
- Windows-spezifische Ports für jedes SAP-System: 445, 5985
- ASCS-Ports (Instanznummer x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- SCS-Ports (Instanznummer x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- ASCS ERS-Ports unter Linux (Instanznummer x2): 33x2, 5x213, 5x214, 5x216
- SCS ERS-Ports unter Linux (Instanznummer x3): 33x3, 5x313, 5x314, 5x316

Der Lastenausgleich wird so konfiguriert, dass die folgenden Testports verwendet werden (wobei x die Nummer des SAP-Systems ist, z.B. 1, 2, 3...).
- ASCS/SCS – Testport des internen Lastenausgleichs: 620x0
- ERS – Testport des internen Lastenausgleichs (nur Linux): 621x2

### <a name="database-template"></a> Datenbankvorlage

Mit der Datenbankvorlage werden ein oder zwei virtuelle Computer bereitgestellt, die Sie zum Installieren des relationalen Datenbankverwaltungssystems (RDBMS) für ein SAP-System verwenden können. Wenn Sie z.B. eine ASCS/SCS-Vorlage für fünf SAP-Systeme bereitstellen, müssen Sie diese Vorlage fünfmal bereitstellen.

Geben Sie in der [Datenbank-Multi-SID-Vorlage][sap-templates-3-tier-multisid-db-marketplace-image] oder der [Datenbank-Multi-SID-Vorlage mit Managed Disks][sap-templates-3-tier-multisid-db-marketplace-image-md] Werte für die folgenden Parameter ein, um die Datenbank-Multi-SID-Vorlage einzurichten:

- **SAP-System-ID**: Geben Sie die SAP-System-ID des SAP-Systems ein, das Sie installieren möchten. Die ID wird als Präfix für die Ressourcen verwendet, die bereitgestellt werden.
- **Betriebssystemtyp**: Wählen Sie das Betriebssystem des virtuellen Computers aus.
- **Dbtype**: Wählen Sie den Typ der Datenbank aus, die Sie im Cluster installieren möchten. Wählen Sie **SQL**, wenn Sie Microsoft SQL Server installieren möchten. Wählen Sie **HANA**, wenn Sie SAP HANA auf den virtuellen Computern installieren möchten. Stellen Sie sicher, dass Sie den richtigen Betriebssystemtyp auswählen. Wählen Sie **Windows** für SQL und eine Linux-Distribution für HANA aus. Der Azure Load Balancer, der mit den virtuellen Computern verbunden ist, wird so konfiguriert, dass er den ausgewählten Datenbanktyp unterstützt:
  * **SQL**: Der Lastenausgleich ist auf Port 1433 wirksam. Stellen Sie sicher, dass dieser Port für Ihr SQL Server AlwaysOn-Setup verwendet wird.
  * **HANA**: Der Lastenausgleich ist auf den Ports 35015 und 35017 wirksam. Stellen Sie sicher, dass Sie SAP HANA mit Instanznummer **50** installieren.
  Der Lastenausgleich verwendet den Testport 62550.
- **SAP-Systemgröße**: Legen Sie die Anzahl von SAPS fest, die vom neuen System bereitgestellt werden. Wenn Sie nicht sicher sind, welche SAPS-Zahl für das System benötigt wird, können Sie sich an den SAP-Technologiepartner oder -Systemintegrator wenden.
- **Systemverfügbarkeit**: Wählen Sie **HA** (Hohe Verfügbarkeit).
- **Administratorbenutzername und Administratorkennwort:** Erstellen Sie einen neuen Benutzer, der verwendet werden kann, um sich am Computer anzumelden.
- **Subnetz-ID**: Geben Sie die ID des Subnetzes ein, das Sie während der Bereitstellung der ASCS/SCS-Vorlage verwendet haben, oder die ID des Subnetzes, das als Teil der ASCS/SCS-Vorlagenbereitstellung erstellt wurde.

### <a name="application-servers-template"></a> Anwendungsservervorlage

Die Anwendungsservervorlage stellt zwei oder mehr virtuelle Computer bereit, die als SAP-Anwendungsserverinstanzen für ein SAP-System verwendet werden können. Wenn Sie z.B. eine ASCS/SCS-Vorlage für fünf SAP-Systeme bereitstellen, müssen Sie diese Vorlage fünfmal bereitstellen.

Geben Sie in der [Anwendungsserver-Multi-SID-Vorlage][sap-templates-3-tier-multisid-apps-marketplace-image] oder der [Anwendungsserver-Multi-SID-Vorlage mit Managed Disks][sap-templates-3-tier-multisid-apps-marketplace-image-md] Werte für die folgenden Parameter ein, um die Anwendungsserver-Multi-SID-Vorlage einzurichten:

  -  **SAP-System-ID**: Geben Sie die SAP-System-ID des SAP-Systems ein, das Sie installieren möchten. Die ID wird als Präfix für die Ressourcen verwendet, die bereitgestellt werden.
  -  **Betriebssystemtyp**: Wählen Sie das Betriebssystem des virtuellen Computers aus.
  -  **SAP-Systemgröße**: Die SAPS-Zahl des neuen Systems. Wenn Sie nicht sicher sind, welche SAPS-Zahl für das System benötigt wird, können Sie sich an den SAP-Technologiepartner oder -Systemintegrator wenden.
  -  **Systemverfügbarkeit**: Wählen Sie **HA** (Hohe Verfügbarkeit).
  -  **Administratorbenutzername und Administratorkennwort:** Erstellen Sie einen neuen Benutzer, der verwendet werden kann, um sich am Computer anzumelden.
  -  **Subnetz-ID**: Geben Sie die ID des Subnetzes ein, das Sie während der Bereitstellung der ASCS/SCS-Vorlage verwendet haben, oder die ID des Subnetzes, das als Teil der ASCS/SCS-Vorlagenbereitstellung erstellt wurde.


## <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Virtuelles Azure-Netzwerk (Azure VNET)
In unserem Beispiel lautet der Adressbereich der Azure Virtual Network-Instanz 10.0.0.0/16. Es ist ein Subnetz mit dem Namen „Subnet“ und dem Adressbereich 10.0.0.0/24 vorhanden. Alle virtuellen Computer und internen Load Balancer werden in diesem virtuellen Netzwerk bereitgestellt.

> [!IMPORTANT]
> Nehmen Sie keine Änderungen an den Netzwerkeinstellungen innerhalb des Gastbetriebssystems vor. Dies schließt IP-Adressen, DNS-Server und Subnetz ein. Konfigurieren Sie alle Einstellungen für Ihr Netzwerk in Azure. Der DHCP-Dienst (Dynamic Host Configuration Protocol) übermittelt diese Einstellungen.
>
>

## <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> DNS-IP-Adressen

Führen Sie die folgenden Schritte aus, um die erforderlichen DNS-IP-Adressen festzulegen:

1. Stellen Sie im Azure-Portal im Bereich **DNS-Server** sicher, dass die Option **DNS-Server** für Ihr virtuelles Netzwerk auf **Custom DNS** (Benutzerdefiniertes DNS) festgelegt ist.
2. Wählen Sie die Einstellungen basierend auf dem Typ des Netzwerks aus. Weitere Informationen finden Sie in den folgenden Ressourcen:
   * [Konnektivität des Unternehmensnetzwerks (standortübergreifend)][planning-guide-2.2]: Fügen Sie die IP-Adressen der lokalen DNS-Server hinzu.  
   Sie können lokale DNS-Server auf die virtuellen Computer erweitern, die in Azure ausgeführt werden. In diesem Szenario können Sie die IP-Adressen der virtuellen Azure-Computer hinzufügen, auf denen den DNS-Dienst ausgeführt wird.
   * Für in Azure isolierte VM-Bereitstellungen: Stellen Sie eine zusätzliche VM in der gleichen Virtual Network-Instanz bereit, die auch als DNS-Server fungiert. Fügen Sie die IP-Adressen der virtuellen Azure-Computer hinzu, die Sie zum Ausführen des DNS-Diensts eingerichtet haben.

   ![Abbildung 2: Konfigurieren von DNS-Servern für Azure Virtual Network][sap-ha-guide-figure-3001]

   _**Abbildung 2:** Konfigurieren von DNS-Servern für Azure Virtual Network_

   > [!NOTE]
   > Wenn Sie die IP-Adressen der DNS-Server ändern, müssen Sie die virtuellen Azure-Computer neu starten, damit die Änderung übernommen und an die neuen DNS-Server übertragen wird.
   >
   >

In unserem Beispiel ist der DNS-Dienst auf den folgenden virtuellen Windows-Computern installiert und konfiguriert:

| Rolle für virtuellen Computer | Hostname für virtuellen Computer | Name der Netzwerkkarte | Statische IP-Adresse |
| --- | --- | --- | --- |
| Erster DNS-Server |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Zweiter DNS-Server |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

## <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Hostnamen und statische IP-Adressen für die SAP ASCS/SCS-Clusterinstanz und -DBMS-Clusterinstanz

Für lokale Bereitstellungen benötigen Sie diese reservierten Hostnamen und IP-Adressen:

| Rolle des virtuellen Hosts | Name des virtuellen Hosts | Virtuelle statische IP-Adresse |
| --- | --- | --- |
| SAP ASCS/SCS – erster virtueller Host im Cluster (für die Clusterverwaltung) |pr1-ascs-vir |10.0.0.42 |
| SAP ASCS/SCS-Instanz – virtueller Hostname |pr1-ascs-sap |10.0.0.43 |
| SAP-DBMS – zweiter virtueller Host im Cluster (Clusterverwaltung) |pr1-dbms-vir |10.0.0.32 |

Bei der Erstellung des Clusters erstellen Sie die virtuellen Hostnamen „pr1-ascs-vir“ und „pr1-dbms-vir“ und die zugehörigen IP-Adressen, mit denen der Cluster selbst verwaltet wird. Informationen zur Vorgehensweise finden Sie unter [Collect cluster nodes in a cluster configuration][sap-high-availability-infrastructure-wsfc-shared-disk-collect-cluster-config] (Erfassen von Clusterknoten in einer Clusterkonfiguration).

Sie können die anderen beiden virtuellen Hostnamen „pr1-ascs-sap“ und „pr1-dbms-sap“ und die zugeordneten IP-Adressen manuell auf dem DNS-Server erstellen. Die SAP ASCS/SCS-Clusterinstanz und die DBMS-Clusterinstanz verwenden diese Ressourcen. Informationen zur Vorgehensweise finden Sie unter [Create a virtual host name for a clustered SAP ASCS/SCS instance][sap-ha-guide-9.1.1] (Erstellen eines virtuellen Hostnamens für die SAP ASCS/SCS-Clusterinstanz).

## <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Festlegen der statischen IP-Adressen für die beiden virtuellen SAP-Computer
Nach der Bereitstellung der virtuellen Computer für Ihren Cluster müssen Sie statische IP-Adressen für alle virtuellen Computer festlegen. Sie führen diesen Schritt in der Azure Virtual Network-Konfiguration und nicht im Gastbetriebssystem aus.

1. Wählen Sie im Azure-Portal **Ressourcengruppe** > **Netzwerkkarte** > **Einstellungen** > **IP-Adresse**.
2. Wählen Sie im Bereich **IP-Adressen** unter **Zuweisung** die Option **Statisch**. Geben Sie im Feld **IP-Adresse** die IP-Adresse ein, die Sie verwenden möchten.

   > [!NOTE]
   > Wenn Sie die IP-Adresse der Netzwerkkarte ändern, müssen Sie die virtuellen Azure-Computer neu starten, um die Änderung zu übernehmen.  
   >
   >

   ![Abbildung 3: Festlegen der statischen IP-Adressen für die Netzwerkkarten der einzelnen VMs][sap-ha-guide-figure-3002]

   _**Abbildung 3:** Festlegen der statischen IP-Adressen für die Netzwerkkarten der einzelnen VMs_

   Wiederholen Sie diesen Schritt für alle Netzwerkschnittstellen, d.h. für alle virtuellen Computer, einschließlich der virtuellen Computer, die Sie für Active Directory oder den DNS-Dienst verwenden möchten.

In unserem Beispiel verwenden wir die folgenden virtuellen Computer und statischen IP-Adressen:

| Rolle für virtuellen Computer | Hostname für virtuellen Computer | Name der Netzwerkkarte | Statische IP-Adresse |
| --- | --- | --- | --- |
| Erste SAP-Anwendungsserverinstanz |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| Zweite SAP-Anwendungsserverinstanz |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| Letzte SAP-Anwendungsserverinstanz |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| Erster Clusterknoten für die ASCS/SCS-Instanz |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| Zweiter Clusterknoten für die ASCS/SCS-Instanz |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| Erster Clusterknoten für die DBMS-Instanz |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| Zweiter Clusterknoten für die DBMS-Instanz |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

## <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Festlegen der statischen IP-Adresse für den internen Azure Load Balancer

Die Azure Resource Manager-Vorlage für SAP erstellt einen internen Azure Load Balancer, der für den Cluster der SAP ASCS/SCS-Instanz und den DBMS-Cluster verwendet wird.

> [!IMPORTANT]
> Die IP-Adresse des virtuellen Hostnamens von SAP ASCS/SCS ist mit der IP-Adresse des internen Load Balancers „pr1-lb-ascs“ für die SAP ASCS/SCS-Instanz identisch.
> Die IP-Adresse des virtuellen Namens des DBMS ist mit der IP-Adresse des internen Load Balancers „pr1-lb-dbms“ für das DBMS identisch.
>
>

So legen Sie eine statische IP-Adresse für den internen Azure Load Balancer fest

1. Bei der ersten Bereitstellung wird die IP-Adresse des internen Load Balancers auf **Dynamisch** festgelegt. Wählen Sie im Azure-Portal im Bereich **IP-Adressen** unter **Zuweisung** die Option **Statisch**.
2. Legen Sie die IP-Adresse des internen Load Balancers **pr1-lb-ascs** auf die IP-Adresse des virtuellen Hostnamens der SAP ASCS/SCS-Instanz fest.
3. Legen Sie die IP-Adresse des internen Load Balancers **pr1-lb-dbms** auf die IP-Adresse des virtuellen Hostnamens der DBMS-Instanz fest.

   ![Abbildung 4: Festlegen der statischen IP-Adressen für den internen Lastenausgleich der SAP ASCS/SCS-Instanz][sap-ha-guide-figure-3003]

   _**Abbildung 4:** Festlegen der statischen IP-Adressen für den internen Lastenausgleich der SAP ASCS/SCS-Instanz_

In unserem Beispiel verwenden wir zwei interne Azure Load Balancer mit diesen statischen IP-Adressen:

| Rolle des internen Azure Load Balancers | Name des internen Azure Load Balancers | Statische IP-Adresse |
| --- | --- | --- |
| Interner Load Balancer für die SAP ASCS/SCS-Instanz |pr1-lb-ascs |10.0.0.43 |
| Interner Load Balancer für das SAP-DBMS |pr1-lb-dbms |10.0.0.33 |


## <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Standardregeln für den ASCS/SCS-Lastenausgleich für den internen Azure Load Balancer

Die Azure Resource Manager-Vorlage für SAP erstellt alle erforderlichen Ports:
* Eine ABAP ASCS-Instanz mit der Standardinstanznummer 00
* Eine Java SCS-Instanz mit der Standardinstanznummer 01

Wenn Sie Ihre SAP ASCS/SCS-Instanz installieren, müssen Sie die Standardinstanznummer 00 für Ihre ABAP ASCS-Instanz und die Standardinstanznummer 01 für Ihre Java SCS-Instanz verwenden.

Als Nächstes erstellen Sie die erforderlichen Endpunkte für den internen Lastenausgleich für die SAP NetWeaver-Ports.

Für die Erstellung von erforderlichen Endpunkten für den internen Lastenausgleich erstellen Sie zuerst diese Endpunkte für den internen Lastenausgleich für die SAP NetWeaver ABAP ASCS-Ports:

| Dienst/Name der Lastenausgleichsregel | Standardportnummern | Konkrete Ports für (ASCS-Instanz mit Instanznummer 00) (ERS mit 10) |
| --- | --- | --- |
| Enqueue-Server/*lbrule3200* |32\<InstanceNumber\> |3200 |
| ABAP-Message-Server/*lbrule3600* |36\<InstanceNumber\> |3600 |
| Interne ABAP-Nachricht/*lbrule3900* |39\<InstanceNumber\> |3900 |
| Message-Server HTTP/*Lbrule8100* |81\<InstanceNumber\> |8100 |
| SAP Start Service ASCS HTTP/*Lbrule50013* |5\<InstanceNumber\>13 |50013 |
| SAP Start Service ASCS HTTPS/*Lbrule50014* |5\<InstanceNumber\>14 |50014 |
| Enqueue-Replikation/*Lbrule50016* |5\<InstanceNumber\>16 |50016 |
| SAP Start Service ERS HTTP *Lbrule51013* |5\<InstanceNumber\>13 |51013 |
| SAP Start Service ERS HTTP *Lbrule51014* |5\<InstanceNumber\>14 |51014 |
| Windows-Remoteverwaltung (WinRM) *Lbrule5985* | |5985 |
| Dateifreigabe *Lbrule445* | |445 |

**Tabelle 1:** Portnummern der SAP NetWeaver ABAP ASCS-Instanzen

Erstellen Sie anschließend diese Endpunkte für den Lastenausgleich für die SAP NetWeaver Java SCS-Ports:

| Dienst/Name der Lastenausgleichsregel | Standardportnummern | Konkrete Ports für (SCS-Instanz mit Instanznummer 01) (ERS mit 11) |
| --- | --- | --- |
| Enqueue-Server/*lbrule3201* |32\<InstanceNumber\> |3201 |
| Gateway-Server/*lbrule3301* |33\<InstanceNumber\> |3301 |
| Java-Message-Server/*lbrule3900* |39\<InstanceNumber\> |3901 |
| Message-Server-HTTP/*Lbrule8101* |81\<InstanceNumber\> |8101 |
| SAP Start Service SCS HTTP/*Lbrule50113* |5\<InstanceNumber\>13 |50113 |
| SAP Start Service SCS HTTPS/*Lbrule50114* |5\<InstanceNumber\>14 |50114 |
| Enqueue-Replikation/*Lbrule50116* |5\<InstanceNumber\>16 |50116 |
| SAP Start Service ERS HTTP *Lbrule51113* |5\<InstanceNumber\>13 |51113 |
| SAP Start Service ERS HTTP *Lbrule51114* |5\<InstanceNumber\>14 |51114 |
| WinRM *Lbrule5985* | |5985 |
| Dateifreigabe *Lbrule445* | |445 |

**Tabelle 2:** Portnummern der SAP NetWeaver Java SCS-Instanzen

![Abbildung 5: Standardregeln für den ASCS/SCS-Lastenausgleich für den internen Azure Load Balancer][sap-ha-guide-figure-3004]

_**Abbildung 5:** Standardregeln für den ASCS/SCS-Lastenausgleich für den internen Azure Load Balancer_

Legen Sie die IP-Adresse des Lastenausgleichs „pr1-lb-dbms“ auf die IP-Adresse des virtuellen Hostnamens der DBMS-Instanz fest.

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Ändern der Standardregeln für den ASCS/SCS-Lastenausgleich für den internen Azure Load Balancer

Wenn Sie andere Nummern für die SAP ASCS- oder SCS-Instanzen verwenden möchten, müssen Sie die Namen und Werte der entsprechenden Ports in der Standardeinstellung ändern.

1. Wählen Sie im Azure-Portal **\<SID\>-lb-ascs load balancer** > **Lastenausgleichsregeln**.
2. Ändern Sie für alle zur SAP ASCS- oder SCS-Instanz gehörenden Lastenausgleichsregeln die folgenden Werte:

   * NAME
   * Port
   * Back-End-Port

   Wenn Sie z.B. die Standardnummer der ASCS-Instanz von 00 in 31 ändern möchten, müssen Sie die Änderungen für alle in Tabelle 1 aufgeführten Ports durchführen.

   Dies ist ein Beispiel einer Änderung für den Port *lbrule3200*.

   ![Abbildung 6: Ändern der Standardregeln für den ASCS/SCS-Lastenausgleich für den internen Azure Load Balancer][sap-ha-guide-figure-3005]

   _**Abbildung 6:** Ändern der Standardregeln für den ASCS/SCS-Lastenausgleich für den internen Azure Load Balancer_

## <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Hinzufügen virtueller Windows-Computer zur Domäne

Nachdem Sie den virtuellen Computern eine statische IP-Adresse zugewiesen haben, fügen Sie die virtuellen Computer der Domäne hinzu.

![Abbildung 7: Hinzufügen einer VM zu einer Domäne][sap-ha-guide-figure-3006]

_**Abbildung 7:** Hinzufügen einer VM zu einer Domäne_

## <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Hinzufügen von Registrierungseinträgen auf beiden Clusterknoten für eine SAP ASCS/SCS-Instanz

Der Azure Load Balancer verfügt über einen internen Load Balancer, der Verbindungen schließt, wenn sich diese für einen festgelegten Zeitraum im Leerlauf befinden (Leerlaufzeitlimit). SAP-Workprozesse öffnen in Dialoginstanzen Verbindungen mit dem SAP-Enqueue-Prozess, sobald die erste Enqueue-/Dequeue-Anforderung gesendet werden muss. Diese Verbindungen bleiben in der Regel bestehen, bis der Workprozess oder Enqueue-Prozess neu gestartet wird. Wenn sich die Verbindung für einen festgelegten Zeitraum im Leerlauf befindet, schließt der interne Azure Load Balancer aber die Verbindungen. Dies stellt kein Problem dar, da der SAP-Arbeitsprozesses die Verbindung mit dem Enqueue-Prozess erneut herstellt, wenn sie nicht mehr vorhanden sein sollte. Diese Aktivitäten werden in den Entwickler-Traces von SAP-Prozessen dokumentiert, sie erstellen jedoch eine große Menge an zusätzlichen Inhalten in diesen Traces. Daher wird empfohlen, die TCP/IP-Einstellungen `KeepAliveTime` und `KeepAliveInterval` auf beiden Clusterknoten zu ändern. Kombinieren Sie diese Änderungen an den TCP/IP-Parametern mit SAP-Profilparametern, die weiter unten in diesem Artikel beschrieben werden.

Fügen Sie zuerst diese Windows-Registrierungseinträge auf beiden Windows-Clusterknoten für SAP ASCS/SCS hinzu, um die Registrierungseinträge auf beiden Clusterknoten der SAP ASCS/SCS-Instanz hinzuzufügen:

| `Path` | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Variablenname |`KeepAliveTime` |
| Variablentyp |REG_DWORD-Wert (dezimal) |
| Wert |120000 |
| Link zur Dokumentation |[https://technet.microsoft.com/library/cc957549.aspx](https://technet.microsoft.com/library/cc957549.aspx) |

**Tabelle 3:** Ändern des ersten TCP/IP-Parameters

Fügen Sie diesen Windows-Registrierungseintrag dann auf beiden Windows-Clusterknoten für SAP ASCS/SCS hinzu:

| `Path` | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Variablenname |`KeepAliveInterval` |
| Variablentyp |REG_DWORD-Wert (dezimal) |
| Wert |120000 |
| Link zur Dokumentation |[https://technet.microsoft.com/library/cc957548.aspx](https://technet.microsoft.com/library/cc957548.aspx) |

**Tabelle 4:** Ändern des zweiten TCP/IP-Parameters

Starten Sie beide Clusterknoten neu, um die Änderungen zu übernehmen.

## <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Einrichten eines Windows Server-Failoverclusters für eine SAP ASCS/SCS-Instanz

Das Einrichten eines Windows Server-Failoverclusters für eine SAP ASCS/SCS-Instanz umfasst diese Aufgaben:

- Erfassen der Clusterknoten in einer Clusterkonfiguration
- Konfigurieren eines Cluster-Dateifreigabenzeugen

### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Erfassen der Clusterknoten in einer Clusterkonfiguration

1. Fügen Sie im Assistenten zum Hinzufügen von Rollen und Features beiden Clusterknoten das Failoverclustering hinzu.
2. Richten Sie den Failovercluster mit dem Failovercluster-Manager ein. Wählen Sie im Failovercluster-Manager die Option **Cluster erstellen**, und fügen Sie nur den Namen des ersten Clusters (Knoten A) hinzu. Fügen Sie den zweiten Knoten noch nicht hinzu. Sie führen dies in einem späteren Schritt durch.

   ![Abbildung 8: Hinzufügen eines Namens für den Server oder die VM des ersten Clusterknotens][sap-ha-guide-figure-3007]

   _**Abbildung 8:** Hinzufügen eines Namens für den Server oder die VM des ersten Clusterknotens_

3. Geben Sie den Netzwerknamen (Name des virtuellen Hosts) des Clusters ein.

   ![Abbildung 9: Eingeben des Clusternamens][sap-ha-guide-figure-3008]

   _**Abbildung 9:** Eingeben des Clusternamens_

4. Führen Sie nach dem Erstellen des Clusters einen Test zur Überprüfung des Clusters durch.

   ![Abbildung 10: Ausführen der Clusterüberprüfung][sap-ha-guide-figure-3009]

   _**Abbildung 10:** Ausführen der Clusterüberprüfung_

   Sie können an diesem Punkt im Prozess sämtliche Warnungen zu Datenträgern ignorieren. Sie fügen später einen Dateifreigabenzeugen und die freigegebenen SIOS-Datenträger hinzu. In dieser Phase müssen Sie noch kein Quorum berücksichtigen.

   ![Abbildung 11: Kein Quorumdatenträger gefunden][sap-ha-guide-figure-3010]

   _**Abbildung 11:** Kein Quorumdatenträger gefunden_

   ![Abbildung 12: Eine Hauptclusterressource benötigt eine neue IP-Adresse][sap-ha-guide-figure-3011]

   _**Abbildung 12:** Eine Hauptclusterressource benötigt eine neue IP-Adresse_

5. Ändern Sie die IP-Adresse des Hauptclusterdiensts. Der Cluster kann erst gestartet werden, nachdem Sie die IP-Adresse des Hauptclusterdiensts geändert haben, da die IP-Adresse der Serverpunkte auf einen der Knoten des virtuellen Computers zeigt. Dies wird auf der Seite **Eigenschaften** der IP-Ressource des Hauptclusterdiensts durchgeführt.

   Wir müssen dem virtuellen Hostnamen des Clusters „pr1-ascs-vir“ beispielsweise eine IP-Adresse zuweisen (im Beispiel 10.0.0.42).

   ![Abbildung 13: Ändern der IP-Adresse im Dialogfeld „Eigenschaften“][sap-ha-guide-figure-3012]

   _**Abbildung 13:** Ändern der IP-Adresse im Dialogfeld **Eigenschaften**_

   ![Abbildung 14: Zuweisen der für den Cluster reservierten IP-Adresse][sap-ha-guide-figure-3013]

   _**Abbildung 14:** Zuweisen der für den Cluster reservierten IP-Adresse_

6. Versetzen Sie den Namen des virtuellen Hosts für den Cluster in den Onlinezustand.

   ![Abbildung 15: Hauptclusterdienst im laufenden Betrieb mit der richtigen IP-Adresse][sap-ha-guide-figure-3014]

   _**Abbildung 15:** Hauptclusterdienst im laufenden Betrieb mit der richtigen IP-Adresse_

7. Fügen Sie den zweiten Clusterknoten hinzu.

   Nachdem der Hauptclusterdienst in Betrieb ist, können Sie den zweiten Clusterknoten hinzufügen.

   ![Abbildung 16: Hinzufügen des zweiten Clusterknotens][sap-ha-guide-figure-3015]

   _**Abbildung 16:** Hinzufügen des zweiten Clusterknotens_

8. Geben Sie einen Namen für zweiten Clusterknotenhost ein.

   ![Abbildung 17: Eingeben des Namens für den zweiten Clusterknotenhost][sap-ha-guide-figure-3016]

   _**Abbildung 17:** Eingeben des Namens für den zweiten Clusterknotenhost_

   > [!IMPORTANT]
   > Stellen Sie sicher, dass das Kontrollkästchen **Der gesamte geeignete Speicher soll dem Cluster hinzugefügt werden** *nicht* aktiviert wird.  
   >
   >

   ![Abbildung 18: Kontrollkästchen nicht aktivieren][sap-ha-guide-figure-3017]

   _**Abbildung 18:** Kontrollkästchen *nicht* aktivieren_

   Sie können Warnungen zu Quorum und Datenträgern ignorieren. Sie konfigurieren das Quorum und die Freigabe der Datenträger später, wie unter [Installieren von SIOS DataKeeper Cluster Edition für einen SAP ASCS/SCS-Clusterfreigabe-Datenträger][sap-high-availability-infrastructure-wsfc-shared-disk-install-sios] beschrieben.

   ![Abbildung 19: Ignorieren der Warnungen zum Datenträgerquorum][sap-ha-guide-figure-3018]

   _**Abbildung 19:** Ignorieren der Warnungen zum Datenträgerquorum_


#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Konfigurieren eines Cluster-Dateifreigabenzeugen

Das Konfigurieren eines Cluster-Dateifreigabenzeugen umfasst die folgenden Aufgaben:

- Erstellen einer Dateifreigabe
- Festlegen des Dateifreigabenzeugen-Quorums im Failovercluster-Manager

#### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Erstellen einer Dateifreigabe

1. Wählen Sie anstelle eines Quorumdatenträgers einen Dateifreigabenzeugen aus. Diese Option wird von SIOS DataKeeper unterstützt.

   In den Beispielen in diesem Artikel befindet sich der Dateifreigabenzeuge auf dem Active Directory- oder DNS-Server, der in Azure ausgeführt wird. Der Dateifreigabenzeuge hat den Namen „domcontr-0“. Da Sie eine VPN-Verbindung mit Azure (VPN Gateway oder Azure ExpressRoute) konfiguriert hätten, ist Ihr Active Directory- bzw. DNS-Dienst lokal angeordnet und nicht für die Ausführung eines Dateifreigabenzeugen geeignet.

   > [!NOTE]
   > Wenn Ihr Active Directory- bzw. DNS-Dienst nur lokal ausgeführt wird, konfigurieren Sie keinen Dateifreigabenzeugen auf dem lokalen Windows-Betriebssystem mit Active Directory bzw. DNS. Die Netzwerklatenz zwischen Clusterknoten in Azure und lokalen Active Directory- bzw. DNS-Diensten wäre zu hoch und könnte Verbindungsprobleme verursachen. Konfigurieren Sie den Dateifreigabenzeugen unbedingt auf einem virtuellen Azure-Computer, der in der Nähe des Clusterknotens betrieben wird.  
   >
   >

   Das Quorumlaufwerk benötigt mindestens 1.024 MB freien Speicherplatz. Für das Quorumlaufwerk empfehlen wir die Verwendung von 2.048 MB freiem Speicherplatz.

2. Fügen Sie das Clusternamenobjekt hinzu.

   ![Abbildung 20: Zuweisen der Berechtigungen für die Freigabe für das Clusternamenobjekt][sap-ha-guide-figure-3019]

   _**Abbildung 20:** Zuweisen der Berechtigungen für die Freigabe für das Clusternamenobjekt_

   Vergewissern Sie sich, dass die Berechtigungen das Ändern von Daten auf der Freigabe für das Clusternamenobjekt zulassen (im Beispiel „pr1-ascs-vir$“).

3. Wählen Sie **Hinzufügen** aus, um das Clusternamenobjekt der Liste hinzuzufügen. Ändern Sie den Filter, um zusätzlich zu den in Abbildung 22 dargestellten Objekten auch Computerobjekte einzuschließen.

   ![Abbildung 21: Ändern des Objekttyps, um Computer einzuschließen][sap-ha-guide-figure-3020]

   _**Abbildung 21:** Ändern des **Objekttyps**, um Computer einzuschließen_

   ![Abbildung 22: Aktivieren des Kontrollkästchens „Computer“][sap-ha-guide-figure-3021]

   _**Abbildung 22:** Aktivieren des Kontrollkästchens **Computer**_

4. Geben Sie das Clusternamenobjekt ein, wie in Abbildung 21 dargestellt. Da der Datensatz bereits erstellt wurde, können Sie die Berechtigungen ändern (siehe Abbildung 20).

5. Wählen Sie die Registerkarte **Sicherheit** für die Freigabe aus, und legen Sie anschließend ausführlichere Berechtigungen für das Clusternamenobjekt fest.

   ![Abbildung 23: Festlegen der Sicherheitsattribute für das Clusternamenobjekt im Dateifreigabequorum][sap-ha-guide-figure-3022]

   _**Abbildung 23:** Festlegen der Sicherheitsattribute für das Clusternamenobjekt im Dateifreigabequorum_

#### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Festlegen des Dateifreigabenzeugen-Quorums im Failovercluster-Manager

1. Öffnen Sie den Assistenten zum Konfigurieren des Clusterquorums.

   ![Abbildung 24: Starten des Assistenten zum Konfigurieren des Clusterquorums][sap-ha-guide-figure-3023]

   _**Abbildung 24:** Starten des Assistenten zum Konfigurieren des Clusterquorums_

2. Wählen Sie auf der Seite **Quorumkonfigurationsoption auswählen** die Option **Quorumzeugen auswählen**.

   ![Abbildung 25: Verfügbare Quorumkonfigurationen][sap-ha-guide-figure-3024]

   _**Abbildung 25:** Verfügbare Quorumkonfigurationen_

3. Wählen Sie auf der Seite **Quorumzeuge auswählen** die Option **Dateifreigabenzeugen konfigurieren**.

   ![Abbildung 26: Auswählen des Dateifreigabenzeugen][sap-ha-guide-figure-3025]

   _**Abbildung 26:** Auswählen des Dateifreigabenzeugen_

4. Geben Sie den UNC-Pfad zur Dateifreigabe ein (im Beispiel \\domcontr-0\FSW). Wählen Sie **Weiter** aus, um eine Liste der Änderungen anzuzeigen, die Sie vornehmen können.

   ![Abbildung 27: Festlegen des Dateifreigabespeicherorts für die Zeugenfreigabe][sap-ha-guide-figure-3026]

   _**Abbildung 27:** Festlegen des Dateifreigabespeicherorts für die Zeugenfreigabe_

5. Wählen Sie die gewünschten Änderungen und dann **Weiter** aus. Sie müssen den Cluster erfolgreich neu konfigurieren, wie in Abbildung 28 dargestellt:  

   ![Abbildung 28: Bestätigung der Neukonfiguration des Clusters][sap-ha-guide-figure-3027]

   _**Abbildung 28:** Bestätigung der Neukonfiguration des Clusters_

Nachdem Sie die Installation des Windows-Failoverclusters erfolgreich abgeschlossen haben, müssen Sie einige Schwellenwerte ändern, um die Failovererkennung an die Bedingungen in Azure anzupassen. Die zu ändernden Parameter sind unter [Tuning Failover Cluster Network Thresholds][tuning-failover-cluster-network-thresholds] (Optimieren der Netzwerkschwellenwerte für Failovercluster) dokumentiert. Wenn sich Ihre beiden virtuellen Computer, aus denen die Windows-Clusterkonfiguration für ASCS/SCS besteht, in demselben Subnetz befinden, müssen Sie die folgenden Parameter in diese Werte ändern:

- SameSubNetDelay = 2000
- SameSubNetThreshold = 15
- RoutingHistoryLength = 30

Diese Einstellungen wurden bei Kunden getestet und stellen einen guten Kompromiss dar. Sie sind robust genug, ermöglichen aber gleichzeitig ein Failover, das unter echten Fehlerbedingungen für eine SAP-Software oder bei einem Knoten- oder VM-Ausfall schnell genug durchgeführt werden kann.

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Installieren von SIOS DataKeeper Cluster Edition für den SAP ASCS/SCS-Clusterfreigabe-Datenträger

Sie verfügen nun über eine funktionierende Windows Server-Failoverclusteringkonfiguration in Azure. Um eine SAP ASCS/SCS-Instanz installieren zu können, benötigen Sie eine freigegebene Datenträgerressource. Sie können die erforderlichen freigegebenen Datenträgerressourcen nicht in Azure erstellen. SIOS DataKeeper Cluster Edition ist eine Drittanbieterlösung, die Sie zum Erstellen von freigegebenen Datenträgerressourcen verwenden können.

Das Installieren von SIOS DataKeeper Cluster Edition für SAP ASCS/SCS-Clusterfreigabe-Datenträger umfasst die folgenden Aufgaben:

- Hinzufügen von Microsoft .NET Framework 3.5
- Installieren von SIOS DataKeeper
- Einrichten von SIOS DataKeeper

### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Hinzufügen von .NET Framework 3.5
.NET Framework 3.5 wird unter Windows Server 2012 R2 nicht automatisch aktiviert oder installiert. Da .NET für SIOS DataKeeper auf allen Knoten erforderlich ist, auf denen Sie DataKeeper installieren, müssen Sie .NET Framework 3.5 unter dem Gastbetriebssystem aller virtuellen Computer im Cluster installieren.

Es gibt zwei Möglichkeiten zum Hinzufügen von .NET Framework 3.5:

- Verwenden Sie den Assistenten zum Hinzufügen von Rollen und Features in Windows, der in Abbildung 29 dargestellt ist:

  ![Abbildung 29: Installieren von .NET Framework 3.5 mithilfe des Assistenten zum Hinzufügen von Rollen und Features][sap-ha-guide-figure-3028]

  _**Abbildung 29:** Installieren von .NET Framework 3.5 mithilfe des Assistenten zum Hinzufügen von Rollen und Features_

  ![Abbildung 30: Statusanzeige für die Installation von .NET Framework 3.5 mit dem Assistenten zum Hinzufügen von Rollen und Features][sap-ha-guide-figure-3029]

  _**Abbildung 30:** Statusanzeige für die Installation von .NET Framework 3.5 mit dem Assistenten zum Hinzufügen von Rollen und Features_

- Verwenden Sie das Befehlszeilentool „dism.exe“. Für diese Art von Installation benötigen Sie Zugriff auf das Verzeichnis „SxS“ auf dem Windows-Installationsmedium. Geben Sie diesen Befehl in einem Eingabeaufforderungsfenster mit erhöhten Rechten ein:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> Installieren von SIOS DataKeeper

Installieren Sie SIOS DataKeeper Cluster Edition auf jedem Knoten im Cluster. Erstellen Sie eine synchronisierte Spiegelung, und simulieren Sie dann freigegebenen Clusterspeicher, um mit SIOS DataKeeper virtuellen freigegebenen Speicher zu erstellen.

Erstellen Sie vor der Installation der SIOS-Software den Domänenbenutzer DataKeeperSvc.

> [!NOTE]
> Fügen Sie den Domänenbenutzer DataKeeperSvc der Gruppe „Lokale Administratoren“ auf beiden Clusterknoten hinzu.
>
>

So installieren Sie SIOS DataKeeper

1. Installieren Sie die SIOS-Software auf beiden Clusterknoten.

   ![SIOS-Installationsprogramm][sap-ha-guide-figure-3030]

   ![Abbildung 31: Erste Seite der SIOS DataKeeper-Installation][sap-ha-guide-figure-3031]

   _**Abbildung 31:** Erste Seite der SIOS DataKeeper-Installation_

2. Klicken Sie im Dialogfeld auf **Ja**.

   ![Abbildung 32: Benachrichtigung über die Deaktivierung eines Diensts in DataKeeper][sap-ha-guide-figure-3032]

   _**Abbildung 32:** Benachrichtigung über die Deaktivierung eines Diensts in DataKeeper_

3. Es wird empfohlen, im Dialogfeld die Option **Domain or Server account** (Domänen- oder Serverkonto) auszuwählen.

   ![Abbildung 33: Benutzerauswahl für SIOS DataKeeper][sap-ha-guide-figure-3033]

   _**Abbildung 33:** Benutzerauswahl für SIOS DataKeeper_

4. Geben Sie den Benutzernamen für das Domänenkonto, den Sie für SIOS DataKeeper erstellt haben, mit dem dazugehörigen Kennwort ein.

   ![Abbildung 34: Eingeben des Domänenbenutzernamens und des Kennworts für die SIOS DataKeeper-Installation][sap-ha-guide-figure-3034]

   _**Abbildung 34:** Eingeben des Domänenbenutzernamens und des Kennworts für die SIOS DataKeeper-Installation_

5. Installieren Sie den Lizenzschlüssel für Ihre SIOS DataKeeper-Instanz, wie in Abbildung 35 gezeigt.

   ![Abbildung 35: Eingeben Ihres SIOS DataKeeper-Lizenzschlüssels][sap-ha-guide-figure-3035]

   _**Abbildung 35:** Eingeben Ihres SIOS DataKeeper-Lizenzschlüssels_

6. Starten Sie den virtuellen Computer neu, wenn Sie dazu aufgefordert werden.

### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Einrichten von SIOS DataKeeper

Beginnen Sie nach der Installation von SIOS DataKeeper auf beiden Knoten mit der Konfiguration. Ziel der Konfiguration ist eine synchrone Datenreplikation zwischen den zusätzlichen Datenträgern, die Sie an jeden virtuellen Computer angefügt haben.

1. Starten Sie das DataKeeper-Tool für die Verwaltung und Konfiguration, und wählen Sie dann **Verbindung mit Server herstellen** aus.

   ![Abbildung 36: SIOS DataKeeper-Tool für die Verwaltung und Konfiguration][sap-ha-guide-figure-3036]

   _**Abbildung 36:** SIOS DataKeeper-Tool für die Verwaltung und Konfiguration_

2. Geben Sie den Namen oder die TCP/IP-Adresse des ersten Knotens, mit dem das Tool für die Verwaltung und Konfiguration eine Verbindung herstellen soll, und im zweiten Schritt den zweiten Knoten ein.

   ![Abbildung 37: Eingeben des Namens oder der TCP/IP-Adresse des ersten Knotens, mit dem das Tool für die Verwaltung und Konfiguration eine Verbindung herstellen soll, und im zweiten Schritt Eingeben des zweiten Knotens][sap-ha-guide-figure-3037]

   _**Abbildung 37:** Eingeben des Namens oder der TCP/IP-Adresse des ersten Knotens, mit dem das Tool für die Verwaltung und Konfiguration eine Verbindung herstellen soll, und im zweiten Schritt Eingeben des zweiten Knotens_

3. Erstellen Sie den Replikationsauftrag zwischen den beiden Knoten.

   ![Abbildung 38: Erstellen eines Replikationsauftrags][sap-ha-guide-figure-3038]

   _**Abbildung 38:** Erstellen eines Replikationsauftrags_

   Ein Assistent führt Sie durch die Erstellung eines Replikationsauftrags.

4. Definieren Sie den Namen des Replikationsauftrags.

   ![Abbildung 39: Festlegen des Namens für den Replikationsauftrag][sap-ha-guide-figure-3039]

   _**Abbildung 39:** Festlegen des Namens für den Replikationsauftrag_

   ![Abbildung 40: Definieren der Basisdaten für den Knoten, der der aktuelle Quellknoten sein soll][sap-ha-guide-figure-3040]

   _**Abbildung 40:** Definieren der Basisdaten für den Knoten, der der aktuelle Quellknoten sein soll_

5. Definieren Sie Name, TCP/IP-Adresse und Datenträgervolume des Zielknotens.

   ![Abbildung 41: Definieren von Name, TCP/IP-Adresse und Datenträgervolume des aktuellen Zielknotens][sap-ha-guide-figure-3041]

   _**Abbildung 41:** Definieren von Name, TCP/IP-Adresse und Datenträgervolume des aktuellen Zielknotens_

6. Definieren Sie die Komprimierungsalgorithmen. Für unser Beispiel wird das Komprimieren des Replikationsdatenstroms empfohlen. Insbesondere in Situationen mit erneuter Synchronisierung wird die dafür erforderliche Dauer durch die Komprimierung des Replikationsdatenstroms erheblich verkürzt. Die Komprimierung verbraucht CPU- und Arbeitsspeicherressourcen eines virtuellen Computers. Wenn die Komprimierungsrate zunimmt, steigt auch die Menge der verwendeten CPU-Ressourcen. Sie können diese Einstellung später anpassen.

7. Eine weitere Einstellung, die Sie überprüfen müssen, ist, ob die Replikation asynchron oder synchron erfolgt. Wenn Sie SAP ASCS/SCS-Konfigurationen schützen, müssen Sie die synchrone Replikation verwenden.  

   ![Abbildung 42: Festlegen der Replikationsdetails][sap-ha-guide-figure-3042]

   _**Abbildung 42:** Festlegen der Replikationsdetails_

8. Definieren Sie, ob das Volume, das vom Replikationsauftrag repliziert wird, gegenüber einer Windows Server-Failoverclusterkonfiguration als freigegebener Datenträger dargestellt werden sollte. Für die SAP ASCS/SCS-Konfiguration wählen Sie **Ja** aus, damit der Windows-Cluster das replizierte Volume als freigegebenen Datenträger erkennt, das als Clustervolume verwendet werden kann.

   ![Abbildung 43: Klicken auf „Ja“, um das replizierte Volume als Clustervolume festzulegen][sap-ha-guide-figure-3043]

   _**Abbildung 43:** Klicken auf **Ja**, um das replizierte Volume als Clustervolume festzulegen_

   Nachdem das Volume erstellt wurde, zeigt das DataKeeper-Tool für die Verwaltung und Konfiguration an, dass der Replikationsauftrag aktiv ist.

   ![Abbildung 44: Die synchrone Spiegelung von DataKeeper für den SAP ASCS/SCS-Freigabedatenträger ist aktiv.][sap-ha-guide-figure-3044]

   _**Abbildung 44:** Die synchrone Spiegelung von DataKeeper für den SAP ASCS/SCS-Freigabedatenträger ist aktiv._

   Der Failovercluster-Manager zeigt nun den Datenträger als DataKeeper-Datenträger an, wie in Abbildung 45 dargestellt:

   ![Abbildung 45: Anzeige des von DataKeeper replizierten Datenträgers im Failovercluster-Manager][sap-ha-guide-figure-3045]

   _**Abbildung 45:** Anzeige des von DataKeeper replizierten Datenträgers im Failovercluster-Manager_

## <a name="next-steps"></a>Nächste Schritte

* [SAP NetWeaver HA-Installation auf dem Windows-Failovercluster und dem freigegebenen Datenträger für die SAP (A)SCS-Instanz unter Azure][sap-high-availability-installation-wsfc-shared-disk]
