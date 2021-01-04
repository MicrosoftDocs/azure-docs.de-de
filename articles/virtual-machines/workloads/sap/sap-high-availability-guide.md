---
title: Hochverfügbarkeit von Azure Virtual Machines für SAP NetWeaver
description: In diesem Artikel wird Hochverfügbarkeit bei Azure Virtual Machines für SAP NetWeaver beschrieben.
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
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8e0baeb7eddb1d74a8d7708b04391134d2e188b2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005672"
---
# <a name="high-availability-azure-virtual-machines-for-sap-netweaver"></a>Azure Virtual Machines mit Hochverfügbarkeit für SAP NetWeaver

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

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



Azure Virtual Machines eignet sich als Lösung für Organisationen, die Compute-, Speicher- und Netzwerkressourcen in kürzester Zeit und ohne langwierige Beschaffungszyklen benötigen. Sie können Azure Virtual Machines verwenden, um klassische Anwendungen wie SAP NetWeaver-basiertes ABAP und Java sowie einen ABAP+Java-Stapel bereitzustellen. Erweitern Sie die Zuverlässigkeit und Verfügbarkeit ohne zusätzliche lokale Ressourcen. Azure Virtual Machines unterstützt die standortübergreifende Konnektivität, sodass Sie Azure Virtual Machines in die lokalen Domänen, privaten Clouds und die SAP-Systemumgebung Ihrer Organisation integrieren können.

In diesem Artikel werden die Schritte beschrieben, mit denen Sie hoch verfügbare SAP-Systeme in Azure mit dem Azure Resource Manager-Bereitstellungsmodell bereitstellen. Dabei werden die folgenden Hauptaufgaben behandelt:

* Die entsprechenden SAP-Installationshandbücher und -hinweise finden Sie im Abschnitt [Ressourcen][sap-ha-guide-2]. Dieser Artikel ergänzt die SAP-Dokumentation und -Hinweise zur Installation. Diese stellen die primären Ressourcen für das Installieren und Bereitstellen von SAP-Software auf bestimmten Plattformen dar.
* Informieren Sie sich über die Unterschiede zwischen dem Azure Resource Manager-Bereitstellungsmodell und dem klassischen Azure-Bereitstellungsmodell.
* Informieren Sie sich über die Quorummodi für das Windows Server-Failoverclustering, damit Sie das für Ihre Azure-Bereitstellung geeignete Modell auswählen können.
* Informieren Sie sich über freigegebenen Speicher für das Windows Server-Failoverclustering in Azure-Diensten.
* Informieren Sie sich darüber, wie Sie Single Point of Failure-Komponenten wie Advanced Business Application Programming (ABAP) SAP Central Services (ASCS)/SAP Central Services (SCS) und Datenbank-Managementsysteme (DBMS) sowie redundante Komponenten wie SAP-Anwendungsserver in Azure schützen.
* Arbeiten Sie ein Schritt-für-Schritt-Beispiel einer Installation und Konfiguration eines SAP-Systems mit hoher Verfügbarkeit in einem Windows Server-Failoverclustering-Cluster in Azure mit dem Azure Resource Manager durch.
* Informieren Sie sie über die weiteren erforderlichen Schritte zum Verwenden von Windows Server-Failoverclustering in Azure, die bei einer lokalen Bereitstellung nicht erforderlich sind.

Zur Vereinfachung der Bereitstellung und Konfiguration verwenden wir in diesem Artikel die Resource Manager-Vorlagen für hohe Verfügbarkeit bei SAP mit drei Ebenen. Die Vorlagen automatisieren die Bereitstellung der gesamten Infrastruktur, die Sie für ein SAP-System mit hoher Verfügbarkeit benötigen. Außerdem unterstützt die Infrastruktur die SAPS-Größenfestlegung (SAP Application Performance Standard) Ihres SAP-Systems.

## <a name="prerequisites"></a><a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> Voraussetzungen
Achten Sie vor Beginn darauf, dass die in den folgenden Abschnitten beschriebenen Voraussetzungen erfüllt sind. Überprüfen Sie darüber hinaus alle Ressourcen im Abschnitt [Ressourcen][sap-ha-guide-2].

In diesem Artikel verwenden wir Azure Resource Manager-Vorlagen für [SAP NetWeaver mit drei Ebenen und Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md/). Eine hilfreiche Übersicht über die Vorlagen finden Sie unter [Azure Resource Manager-Vorlagen für SAP](/archive/blogs/saponsqlserver/azure-quickstart-templates-for-sap).

## <a name="resources"></a><a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> Ressourcen
In diesen Artikeln werden SAP-Bereitstellungen in Azure behandelt:

* [Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver][planning-guide]
* [Azure Virtual Machines – Bereitstellung für SAP NetWeaver][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP NetWeaver][dbms-guide]
* [Hochverfügbarkeit von Azure Virtual Machines für SAP NetWeaver (dieses Handbuch)][sap-ha-guide]

> [!NOTE]
> Sofern möglich, wird ein Link zum entsprechenden SAP-Installationshandbuch bereitgestellt (siehe [SAP-Installationshandbücher][sap-installation-guides]). Wenn Sie Informationen zu den Voraussetzungen und zum Installationsvorgang benötigen, sollten Sie die SAP NetWeaver-Installationshandbücher sorgfältig lesen. Dieser Artikel behandelt nur die spezifischen Aufgaben für SAP NetWeaver-basierte Systeme, die Sie mit Azure Virtual Machines verwenden können.
>
>

Diese SAP-Hinweise beziehen sich auf das Thema SAP in Azure:

| Hinweisnummer | Titel |
| --- | --- |
| [1928533] |SAP-Anwendungen auf Azure: Unterstützte Produkte und Größen |
| [2015553] |SAP auf Microsoft Azure: Voraussetzungen für die Unterstützung |
| [1999351] |Enhanced Azure Monitoring for SAP (Erweiterte Azure-Überwachung für SAP) |
| [2178632] |Wichtige Überwachungsmetriken für SAP in Microsoft Azure |
| [1999351] |Virtualisierung unter Windows: Erweiterte Überwachung |
| [2243692] |Verwendung von Azure Storage Premium (SSD) für SAP-DBMS-Instanz |

Informieren Sie sich auch über die [Einschränkungen von Azure-Abonnements][azure-resource-manager/management/azure-subscription-service-limits-subscription], einschließlich der allgemeinen Standard- und Maximaleinschränkungen.

## <a name="high-availability-sap-with-azure-resource-manager-vs-the-azure-classic-deployment-model"></a><a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>Vergleich zwischen dem Azure Resource Manager- und dem klassischen Azure-Bereitstellungsmodell für SAP mit hoher Verfügbarkeit
Das Azure Resource Manager-Bereitstellungsmodell und das klassische Azure-Bereitstellungsmodell unterscheiden sich in den folgenden Bereichen:

- Ressourcengruppen
- Abhängigkeit des internen Azure Load Balancer von der Azure-Ressourcengruppe
- Unterstützung für SAP-Multi-SID-Szenarien

### <a name="resource-groups"></a><a name="f76af273-1993-4d83-b12d-65deeae23686"></a> Ressourcengruppen
In Azure Resource Manager können Sie mit Ressourcengruppen alle Anwendungsressourcen im Azure-Abonnement verwalten. Bei einem integrierten Ansatz in einer Ressourcengruppe haben alle Ressourcen denselben Lebenszyklus. So werden z.B. alle Ressourcen zur gleichen Zeit erstellt und gelöscht. Weitere Informationen zu [Ressourcengruppen](../../../azure-resource-manager/management/overview.md#resource-groups).

### <a name="azure-internal-load-balancer-dependency-on-the-azure-resource-group"></a><a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Abhängigkeit des internen Azure Load Balancer von der Azure-Ressourcengruppe

Beim klassischen Azure-Bereitstellungsmodell besteht eine Abhängigkeit zwischen dem internen Lastenausgleich (Azure Load Balancer-Dienst) und dem Clouddienst. Für jeden internen Lastenausgleich wird ein Clouddienst benötigt.

In Azure Resource Manager muss jede Azure-Ressource in eine Azure-Ressourcengruppe aufgenommen werden, und dies gilt auch für den Azure-Lastenausgleich. Allerdings besteht keine Notwendigkeit, eine Azure-Ressourcengruppe pro Azure-Lastenausgleich zu verwenden. Eine Azure-Ressourcengruppe kann also mehrere Azure-Lastenausgleichsmodule enthalten. Die Umgebung ist einfacher und flexibler. 

### <a name="support-for-sap-multi-sid-scenarios"></a>Unterstützung für SAP-Multi-SID-Szenarien

In Azure Resource Manager können Sie mehrere SAP-SID-ASCS/SCS-Instanzen in einem Cluster installieren. Instanzen mit mehreren SIDs sind möglich, weil für jeden internen Azure Load Balancer mehrere IP-Adressen unterstützt werden.

Wenn Sie das klassische Azure-Bereitstellungsmodell verwenden möchten, befolgen Sie die Schritte in [SAP NetWeaver in Azure: Clustering SAP ASCS/SCS instances by using Windows Server Failover Clustering in Azure with SIOS DataKeeper](https://go.microsoft.com/fwlink/?LinkId=613056) (SAP NetWeaver in Azure: Clustering von SAP ASCS/SCS-Instanzen mit dem Windows Server-Failoverclustering in Azure mit SIOS DataKeeper).

> [!IMPORTANT]
> Es wird dringend empfohlen, das Azure Resource Manager-Bereitstellungsmodell für Ihre SAP-Installationen zu verwenden. Es bietet viele Vorteile, die beim klassischen Bereitstellungsmodell nicht zur Verfügung stehen. Erfahren Sie mehr über Azure-[Bereitstellungsmodelle][virtual-machines-azure-resource-manager-architecture-benefits-arm].   
>
>

## <a name="windows-server-failover-clustering"></a><a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Windows Server-Failoverclustering
Windows Server-Failoverclustering ist die Grundlage für eine SAP ASCS/SCS-Installation und ein DBMS in Windows mit hoher Verfügbarkeit.

Bei einem Failovercluster handelt es sich um eine Gruppe von 1+n unabhängigen Servern, die zur Steigerung der Verfügbarkeit von Anwendungen und Diensten zusammenarbeiten. Wenn ein Knotenfehler auftritt, berechnet das Windows Server-Failoverclustering die Anzahl von Fehlern, die auftreten können, während trotzdem ein fehlerfreier Cluster für die Bereitstellung der Anwendungen und Dienste aufrechterhalten wird. Sie können zwischen verschiedenen Quorummodi wählen, um das Failoverclustering zu erzielen.

### <a name="quorum-modes"></a><a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> Quorummodi
Sie können zwischen vier Quorummodi auswählen, wenn Sie das Windows Server-Failoverclustering verwenden:

* **Knotenmehrheit:** Jeder Knoten des Clusters kann abstimmen. Der Cluster funktioniert nur mit der Mehrheit der Stimmen, d.h. mit mehr als der Hälfte der Stimmen. Diese Option wird für Cluster mit einer ungeraden Anzahl von Knoten empfohlen. So können z.B. drei Knoten in einem Cluster mit sieben Knoten einen Ausfall verursachen. Der Cluster erreicht immer noch eine Mehrheit und wird weiterhin ausgeführt.  
* **Knoten- und Datenträgermehrheit:** Alle Knoten plus ein festgelegter Datenträger im Clusterspeicher (der Datenträgerzeuge) können abstimmen, solange sie verfügbar sind und kommunizieren. Der Cluster funktioniert nur mit der Mehrheit der Stimmen, d.h. mit mehr als der Hälfte der Stimmen. Dieser Modus ist in einer Clusterumgebung mit einer geraden Anzahl von Knoten sinnvoll. Solange die Hälfte der Knoten sowie der Datenträgerzeuge online sind, verbleibt der Cluster in einem ordnungsgemäßen Zustand.
* **Knoten- und Dateifreigabemehrheit:** Alle Knoten sowie eine vom Administrator erstellte zugewiesene Dateifreigabe (der Dateifreigabenzeuge) können abstimmen – unabhängig davon, ob die Knoten und Dateifreigaben verfügbar sind und kommunizieren. Der Cluster funktioniert nur mit der Mehrheit der Stimmen, d.h. mit mehr als der Hälfte der Stimmen. Dieser Modus ist in einer Clusterumgebung mit einer geraden Anzahl von Knoten sinnvoll. Er ähnelt dem Modus mit Knoten- und Datenträgermehrheit, verwendet jedoch einen Dateifreigabenzeugen anstelle eines Datenträgerzeugen. Die Implementierung dieses Modus ist einfach, doch wenn die Dateifreigabe selbst nicht hoch verfügbar ist, kann diese Lösung ein Single Point of Failure (SPOF) sein.
* **Keine Mehrheit: nur Datenträger**. Der Cluster hat ein Quorum, wenn ein Knoten verfügbar ist und mit einem bestimmten Datenträger im Clusterspeicher kommuniziert. Nur die Knoten, die auch mit diesem Datenträger kommunizieren, können dem Cluster beitreten. Die Verwendung dieses Modus wird nicht empfohlen.


## <a name="windows-server-failover-clustering-on-premises"></a><a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Lokales Windows Server-Failoverclustering
Abbildung 1 zeigt einen Cluster mit zwei Knoten. Wenn die Netzwerkverbindung zwischen den Knoten unterbrochen wird, aber beide Knoten weiter ausgeführt werden, bestimmt ein Quorumdatenträger oder eine Quorumdateifreigabe, welcher Knoten die Clusteranwendungen und -dienste weiter bereitstellt. Der Knoten, der Zugriff auf den Quorumdatenträger oder die Quorumdateifreigabe hat, ist derjenige, der die weitere Ausführung der Dienste gewährleistet.

Da in diesem Beispiel ein Cluster mit zwei Knoten verwendet wird, verwenden wir den Quorummodus „Knoten- und Dateifreigabemehrheit“. Der Modus „Knoten- und Datenträgermehrheit“ ist auch eine mögliche Option. In einer Produktionsumgebung wird die Verwendung eines Quorumdatenträgers empfohlen. Sie können die Technologien für die Netzwerk- und Speichersysteme verwenden, um diese hoch verfügbar zu machen.

![Abbildung 1: Beispiel für eine Konfiguration des Windows Server-Failoverclusterings für SAP ASCS/SCS in Azure][sap-ha-guide-figure-1000]

_**Abbildung 1:** Beispiel für eine Konfiguration des Windows Server-Failoverclusterings für SAP ASCS/SCS in Azure_

### <a name="shared-storage"></a><a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> Freigegebener Speicher
Abbildung 1 zeigt auch einen Cluster mit zwei Knoten und freigegebenem Speicher. In einem lokalen Cluster mit freigegebenem Speicher erkennen alle Knoten im Cluster den freigegebenen Speicher. Mithilfe eines Sperrmechanismus werden die Daten vor Beschädigung geschützt. Alle Knoten können erkennen, wenn ein anderer Knoten ausfällt. Wenn ein Knoten ausfällt, übernehmen die restlichen den Besitz der Speicherressourcen und stellen die Verfügbarkeit der Dienste sicher.

> [!NOTE]
> Sie benötigen bei einigen DBMS-Anwendungen wie SQL Server für Hochverfügbarkeit keine freigegebenen Datenträger. SQL Server Always On führt die Replikation von DBMS-Daten- und -Protokolldateien vom lokalen Datenträger eines Clusterknotens auf den lokalen Datenträger eines anderen Clusterknotens durch. In diesem Fall ist bei der Windows-Clusterkonfiguration kein freigegebener Datenträger erforderlich.
>
>

### <a name="networking-and-name-resolution"></a><a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> Netzwerk und Namensauflösung
Clientcomputer erreichen den Cluster über eine virtuelle IP-Adresse und den virtuellen Hostnamen, der vom DNS-Server bereitgestellt wird. Die lokalen Knoten und der DNS-Server können mehrere IP-Adressen verwenden.

Bei einer normalen Konfiguration können Sie zwei oder mehr Netzwerkverbindungen verwenden:

* Eine dedizierte Verbindung mit dem Speicher
* Eine clusterinterne Netzwerkverbindung für den Takt
* Ein von den Clients verwendetes öffentliches Netzwerk zum Herstellen der Verbindung mit dem Cluster

## <a name="windows-server-failover-clustering-in-azure"></a><a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Windows Server-Failoverclustering in Azure
Im Vergleich mit Bare-Metal- oder privaten Cloudbereitstellungen sind für virtuelle Azure-Computer zusätzliche Schritte erforderlich, um Windows Server-Failoverclustering zu konfigurieren. Wenn Sie einen freigegebenen Clusterdatenträger erstellen, müssen Sie mehrere IP-Adressen und virtuelle Hostnamen für die SAP ASCS/SCS-Instanz festlegen.

In diesem Artikel werden die wichtigsten Konzepte und die zusätzlichen Schritte besprochen, die erforderlich sind, um einen SAP Central Services-Cluster mit hoher Verfügbarkeit in Azure zu erstellen. Wir zeigen Ihnen, wie Sie das Drittanbietertool SIOS DataKeeper einrichten und den internen Load Balancer von Azure konfigurieren. Mit diesen Tools können Sie einen Windows-Failovercluster mit einem Dateifreigabenzeugen in Azure erstellen.

![Abbildung 2: Konfiguration des Windows Server-Failoverclusterings in Azure ohne freigegebenen Datenträger][sap-ha-guide-figure-1001]

_**Abbildung 2:** Konfiguration des Windows Server-Failoverclusterings in Azure ohne freigegebenen Datenträger_

### <a name="shared-disk-in-azure-with-sios-datakeeper"></a><a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> Freigegebener Datenträger in Azure mit SIOS DataKeeper
Sie benötigen freigegebenen Clusterspeicher für eine hoch verfügbare SAP ASCS/SCS-Instanz. Ab September 2016 bietet Azure keinen freigegebenen Speicher zum Erstellen eines Clusters mit freigegebenem Speicher mehr an. Die Drittanbietersoftware SIOS DataKeeper Cluster Edition ermöglicht es Ihnen, einen gespiegelten Speicher zu erstellen, der freigegebenen Clusterspeicher simuliert. Die SIOS-Lösung bietet eine synchrone Datenreplikation in Echtzeit. Eine freigegebene Datenträgerressource für einen Cluster wird folgendermaßen erstellt:

1. Fügen Sie einen zusätzlichen Datenträger an sämtliche virtuelle Computer (VMs) in einer Windows-Clusterkonfiguration an.
2. Führen Sie SIOS DataKeeper Cluster Edition auf beiden virtuellen Computerknoten aus.
3. Konfigurieren Sie SIOS DataKeeper Cluster Edition so, dass synchron der Inhalt des zusätzlich angefügten Datenträgervolumes vom virtuellen Quellcomputer im zusätzlich angefügten Datenträgervolume des virtuellen Zielcomputers gespiegelt wird. SIOS DataKeeper abstrahiert die lokalen Quell- und Zielvolumes und präsentiert diese beim Windows Server-Failoverclustering als einen freigegebenen Datenträger.

Weitere Informationen zu [SIOS DataKeeper](https://us.sios.com/products/datakeeper-cluster/).

![Abbildung 3: Konfiguration des Windows Server-Failoverclusterings in Azure mit SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Abbildung 3:** Konfiguration des Windows Server-Failoverclusterings in Azure mit SIOS DataKeeper_

> [!NOTE]
> Sie benötigen bei einigen DBMS-Produkten wie SQL Server für Hochverfügbarkeit keine freigegebenen Datenträger. SQL Server Always On führt die Replikation von DBMS-Daten- und -Protokolldateien vom lokalen Datenträger eines Clusterknotens auf den lokalen Datenträger eines anderen Clusterknotens durch. In diesem Fall ist bei der Windows-Clusterkonfiguration kein freigegebener Datenträger erforderlich.
>
>

### <a name="name-resolution-in-azure"></a><a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Namensauflösung in Azure
Die Azure-Cloudplattform bietet keine Möglichkeit, virtuelle IP-Adressen, z.B. Floating IP-Adressen, zu konfigurieren. Sie benötigen eine alternative Lösung für die Einrichtung einer virtuellen IP-Adresse, um die Clusterressource in der Cloud zu erreichen.
Azure stellt über den Azure Load Balancer-Dienst einen internen Lastenausgleich zur Verfügung. Mit dem internen Load Balancer erreichen Clients den Cluster über die virtuelle IP-Adresse des Clusters.
Sie müssen den internen Load Balancer in der Ressourcengruppe bereitstellen, die die Clusterknoten enthält. Anschließend konfigurieren Sie alle erforderlichen Portweiterleitungsregeln mithilfe der Testports des internen Load Balancers.
Die Clients können über den virtuellen Hostnamen eine Verbindung herstellen. Der DNS-Server löst die IP-Adresse des Clusters auf. Der interne Load Balancer übernimmt die Weiterleitung an den aktiven Knoten des Clusters.

## <a name="sap-netweaver-high-availability-in-azure-infrastructure-as-a-service-iaas"></a><a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> Hohe Verfügbarkeit von SAP NetWeaver in Azure Infrastructure-as-a-Service (IaaS)
Um Hochverfügbarkeit für SAP-Anwendungen zu erreichen, z.B. für SAP-Softwarekomponenten, müssen Sie die folgenden Komponenten schützen:

* SAP-Anwendungsserverinstanz
* SAP ASCS/SCS-Instanz
* DBMS-Server

Weitere Informationen zum Schützen von SAP-Komponenten in Szenarien mit hoher Verfügbarkeit finden Sie unter [Azure Virtual Machines planning and implementation for SAP NetWeaver][planning-guide-11] (Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver).

### <a name="high-availability-sap-application-server"></a><a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> Hohe Verfügbarkeit für SAP-Anwendungsserver
Für die SAP-Anwendungsserver und -Dialoginstanzen ist meist keine spezielle hoch verfügbare Lösung erforderlich. Sie erreichen Hochverfügbarkeit durch Redundanz und durch Konfiguration mehrerer Dialoginstanzen in verschiedenen Instanzen von virtuellen Azure-Computern. Es müssen mindestens zwei SAP-Anwendungsinstanzen auf zwei Instanzen von virtuellen Azure-Computern installiert sein.

![Abbildung 4: Hochverfügbarkeit für SAP-Anwendungsserver][sap-ha-guide-figure-2000]

_**Abbildung 4:** Hochverfügbarkeit für SAP-Anwendungsserver_

Alle virtuellen Computer, auf denen SAP-Anwendungsserverinstanzen gehostet werden, müssen in derselben Azure-Verfügbarkeitsgruppe platziert werden. Eine Azure-Verfügbarkeitsgruppe stellt Folgendes sicher:

* Alle virtuellen Computer sind Teil derselben Upgradedomäne. Eine Upgradedomäne stellt beispielsweise sicher, dass die virtuellen Computer nicht gleichzeitig mit geplanten Wartungsausfallzeiten aktualisiert werden.
* Alle virtuellen Computer sind Teil derselben Fehlerdomäne. Eine Fehlerdomäne stellt beispielsweise bei der Bereitstellung virtueller Computer sicher, dass kein Single Point of Failure Auswirkungen auf die Verfügbarkeit aller virtuellen Computer hat.

Erfahren Sie mehr über das [Verwalten der Verfügbarkeit virtueller Computer][../manage-availability.md].

Nur nicht verwalteter Datenträger: Da das Azure-Speicherkonto ein potenzieller Single Point of Failure sein kann, benötigen Sie unbedingt mindestens zwei Azure-Speicherkonten, auf die mindestens zwei virtuelle Computer verteilt sind. In einer idealen Konfiguration würden die Datenträger jedes virtuellen Computers, auf dem eine SAP-Dialoginstanz ausgeführt wird, in einem anderen Speicherkonto bereitgestellt werden.

### <a name="high-availability-sap-ascsscs-instance"></a><a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> Hohe Verfügbarkeit für SAP ASCS/SCS-Instanzen
Abbildung 5 ist ein Beispiel für eine SAP ASCS/SCS-Instanz mit hoher Verfügbarkeit.

![Abbildung 5: Hohe Verfügbarkeit für SAP ASCS/SCS-Instanzen][sap-ha-guide-figure-2001]

_**Abbildung 5:** Hochverfügbarkeit für SAP ASCS/SCS-Instanzen_

#### <a name="sap-ascsscs-instance-high-availability-with-windows-server-failover-clustering-in-azure"></a><a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> Hohe Verfügbarkeit für SAP ASCS/SCS-Instanzen mit Windows Server-Failoverclustering in Azure
Im Vergleich mit Bare-Metal- oder privaten Cloudbereitstellungen sind für virtuelle Azure-Computer zusätzliche Schritte erforderlich, um Windows Server-Failoverclustering zu konfigurieren. Zum Erstellen eines Windows-Failoverclusters sind für das Clustern einer SAP ASCS/SCS-Instanz ein freigegebener Clusterdatenträger, mehrere IP-Adressen und virtuelle Hostnamen und ein interner Azure Load Balancer erforderlich. Dies wird später in diesem Artikel ausführlicher erläutert.

![Abbildung 6: Konfiguration des Windows Server-Failoverclusterings für eine SAP ASCS/SCS-Instanz in Azure mit SIOS DataKeeper][sap-ha-guide-figure-1002]

_**Abbildung 6:** Konfiguration des Windows Server-Failoverclusterings für eine SAP ASCS/SCS-Instanz in Azure mit SIOS DataKeeper_

### <a name="high-availability-dbms-instance"></a><a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>Hohe Verfügbarkeit für DBMS-Instanzen
Das DBMS ist auch eine zentrale Kontaktstelle in einem SAP-System. Sie sollten es mit einer Lösung mit hoher Verfügbarkeit schützen. Abbildung 7 zeigt eine SQL Server Always On-Lösung mit hoher Verfügbarkeit in Azure unter Verwendung von Windows Server-Failoverclustering und des internen Azure Load Balancers. SQL Server Always On repliziert DBMS-Daten- und -Protokolldateien mithilfe seiner eigenen DBMS-Replikation. In diesem Fall benötigen Sie keine freigegebenen Clusterdatenträger, sodass die gesamte Einrichtung vereinfacht wird.

![Abbildung 7: Beispiel für ein hoch verfügbares SAP-DBMS mit SQL Server Always On][sap-ha-guide-figure-2003]

_**Abbildung 7:** Beispiel für ein hoch verfügbares SAP-DBMS mit SQL Server Always On_

Weitere Informationen zum Clustering von SQL Server in Azure mithilfe des Azure Resource Manager-Bereitstellungsmodells finden Sie in den folgenden Artikeln:

* [Manuelles Konfigurieren von Always On-Verfügbarkeitsgruppen in Azure Virtual Machines mit dem Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [Konfigurieren eines internen Azure Load Balancers für eine Always On-Verfügbarkeitsgruppe in Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

## <a name="end-to-end-high-availability-deployment-scenarios"></a><a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> End-to-End-Bereitstellungsszenarios mit hoher Verfügbarkeit

### <a name="deployment-scenario-using-architectural-template-1"></a>Bereitstellungsszenario mit Architekturvorlage 1

Abbildung 8 zeigt ein Beispiel für eine SAP NetWeaver-Architektur mit hoher Verfügbarkeit in Azure für **ein** SAP-System. Dieses Szenario wird wie folgt eingerichtet:

- Ein dedizierter Cluster wird für die SAP ASCS/SCS-Instanz verwendet.
- Ein dedizierter Cluster wird für die DBMS-Instanz verwendet.
- SAP-Anwendungsserverinstanzen werden auf eigenen dedizierten VMs bereitgestellt.

![Abbildung 8: SAP-Architekturvorlage 1 für Hochverfügbarkeit mit dediziertem Cluster für ASCS/SCS und DBMS][sap-ha-guide-figure-2004]

_**Abbildung 8:** SAP-Architekturvorlage 1 für Hochverfügbarkeit mit dediziertem Cluster für ASCS/SCS und DBMS_

### <a name="deployment-scenario-using-architectural-template-2"></a>Bereitstellungsszenario mit Architekturvorlage 2

Abbildung 9 zeigt ein Beispiel für eine SAP NetWeaver-Architektur mit hoher Verfügbarkeit in Azure für **ein** SAP-System. Dieses Szenario wird wie folgt eingerichtet:

- Ein dedizierter Cluster wird für die SAP ASCS/SCS-Instanz **und** für die DBMS verwendet.
- SAP-Anwendungsserverinstanzen werden auf eigenen dedizierten VMs bereitgestellt.

![Abbildung 9: SAP-Architekturvorlage 2 für Hochverfügbarkeit mit dediziertem Cluster für ASCS/SCS und dediziertem Cluster für DBMS][sap-ha-guide-figure-2005]

_**Abbildung 9:** SAP-Architekturvorlage 2 für Hochverfügbarkeit mit dediziertem Cluster für ASCS/SCS und dediziertem Cluster für DBMS_

### <a name="deployment-scenario-using-architectural-template-3"></a>Bereitstellungsszenario mit Architekturvorlage 3

Abbildung 10 zeigt ein Beispiel für eine SAP NetWeaver-Architektur mit hoher Verfügbarkeit in Azure für **zwei** SAP-Systeme mit &lt;SID1&gt; und &lt;SID2&gt;. Dieses Szenario wird wie folgt eingerichtet:

- Ein dedizierter Cluster wird **sowohl** für die SAP ASCS/SCS SID1-Instanz *als auch* für die SAP ASCS/SCS SID2-Instanz (ein Cluster) verwendet.
- Ein dedizierter Cluster wird für DBMS SID1 verwendet, und ein anderer dedizierter Cluster wird für DBMS SID2 verwendet (zwei Cluster).
- SAP-Anwendungsserverinstanzen für das SAP-System SID1 verfügen über eigene dedizierte VMs.
- SAP-Anwendungsserverinstanzen für das SAP-System SID2 verfügen über eigene dedizierte VMs.

![Abbildung 10: SAP-Architekturvorlage 3 für Hochverfügbarkeit mit dediziertem Cluster für verschiedene ASCS/SCS-Instanzen][sap-ha-guide-figure-6003]

_**Abbildung 10:** SAP-Architekturvorlage 3 für Hochverfügbarkeit mit dediziertem Cluster für verschiedene ASCS/SCS-Instanzen_

## <a name="prepare-the-infrastructure"></a><a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> Vorbereiten der Infrastruktur

### <a name="prepare-the-infrastructure-for-architectural-template-1"></a>Vorbereiten der Infrastruktur für Architekturvorlage 1
Azure Resource Manager-Vorlagen für SAP vereinfachen die Bereitstellung der erforderlichen Ressourcen.

Die Vorlagen mit drei Ebenen in Azure Resource Manager unterstützen auch Szenarien mit hoher Verfügbarkeit, z.B. in Architekturvorlage 1, die über zwei Cluster verfügt. Jeder Cluster ist ein SAP-Single Point of Failure für SAP ASCS/SCS und das DBMS.

Hier können Sie die Azure Resource Manager-Vorlagen für das in diesem Artikel beschriebene Beispielszenario abrufen:

* [Azure Marketplace-Image](https://github.com/Azure/azure-quickstart-templates/)  
* [Azure Marketplace-Image mit Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-md)  
* [Benutzerdefiniertes Image](https://github.com/Azure/azure-quickstart-templates/)
* [Benutzerdefiniertes Image mit Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-md)

So bereiten Sie die Infrastruktur für Architekturvorlage 1 vor

- Wählen Sie im Azure-Portal auf dem Blatt **Parameter** im Feld **SYSTEMAVAILABILITY** die Option **HA**.

  ![Abbildung 11: Festlegen von Azure Resource Manager-Parametern für die Hochverfügbarkeit von SAP][sap-ha-guide-figure-3000]

_**Abbildung 11:** Festlegen von Azure Resource Manager-Parametern für die Hochverfügbarkeit von SAP_


  Mit den Vorlagen wird Folgendes erstellt:

  * **Virtuelle Computer:**
    * Virtuelle Computer für SAP-Anwendungsserver: <*SAP-System-SID*>-di-<*Anzahl*>
    * Virtuelle Computer für den ASCS/SCS-Cluster: <*SAP-System-SID*>-ascs-<*Anzahl*>
    * DBMS-Cluster: <*SAP-System-SID*>-db-<*Anzahl*>

  * **Netzwerkkarten für alle virtuellen Computer mit zugeordneten IP-Adressen:**
    * <*SAP-System-SID*&gt;-nic-di-&lt;*Anzahl*>
    * <*SAP-System-SID*&gt;-nic-ascs-&lt;*Anzahl*>
    * <*SAP-System-SID*&gt;-nic-db-&lt;*Anzahl*>

  * **Azure-Speicherkonten (nur nicht verwalteter Datenträger)**

  * **Verfügbarkeitsgruppen** für:
    * Virtuelle Computer für SAP-Anwendungsserver: <*SAP-System-SID*>-avset-di
    * Virtuelle Computer für den SAP ASCS/SCS-Cluster: <*SAP-System-SID*>-avset-ascs
    * Virtuelle Computer für das DBMS: <*SAP-System-SID*>-avset-db

  * **Interner Azure Load Balancer:**
    * Mit allen Ports für die ASCS/SCS-Instanz und IP-Adresse <*SAP-System-SID*>-lb-ascs
    * Mit allen Ports für das SQL Server-DBMS und IP-Adresse <*SAP-System-SID*>-lb-db

  * **Netzwerksicherheitsgruppe:** &lt;*SAP-System-SID*&gt;-nsg-ascs-0  
    * Mit einem offenen externen RDP-Port (Remote Desktop Protocol) zum virtuellen Computer <*SAP-System-SID*>-ascs-0

> [!NOTE]
> Sämtliche IP-Adressen für die Netzwerkkarten und den internen Azure Load Balancer sind standardmäßig **dynamisch**. Ändern Sie diese in **statische** IP-Adressen. Die Vorgehensweise wird weiter unten in diesem Artikel beschrieben.
>
>

### <a name="deploy-virtual-machines-with-corporate-network-connectivity-cross-premises-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> Bereitstellen virtueller Computer mit Verbindungen mit dem Unternehmensnetzwerk (standortübergreifend) für die Verwendung in der Produktion
Stellen Sie für SAP-Produktionssysteme virtuelle Azure-Computer mit einer Verbindung mit dem Unternehmensnetzwerk bereit. Verwenden Sie dazu ein Azure-Site-to-Site-VPN oder Azure ExpressRoute.

> [!NOTE]
> Sie können Ihre Azure Virtual Network-Instanz verwenden. Das virtuelle Netzwerk und das Subnetz wurden bereits erstellt und vorbereitet.
>
>

1. Wählen Sie im Azure-Portal auf dem Blatt **Parameter** im Feld **NEWOREXISTINGSUBNET** die Option **existing**.
2. Fügen Sie im Feld **SUBNETID** die vollständige Zeichenfolge Ihrer vorbereiteten Subnetz-ID für das Azure-Netzwerk ein, in dem Sie Ihre virtuellen Azure-Computer bereitstellen möchten.
3. Führen Sie diesen PowerShell-Befehl aus, um eine Liste mit allen Azure-Netzwerksubnetzen abzurufen:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
   ```

   Das Feld **ID** zeigt die **SUBNETID** an.
4. Führen Sie diesen PowerShell-Befehl aus, um eine Liste mit allen **SUBNETID**-Werten abzurufen:

   ```powershell
   (Get-AzVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
   ```

   Die **SUBNETID** sieht in etwa wie folgt aus:

   ```
   /subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
   ```

### <a name="deploy-cloud-only-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> Bereitstellen von auf die Cloud beschränkten SAP-Instanzen für Tests und Demos
Sie können Ihr SAP-System mit hoher Verfügbarkeit in einem reinen Cloudbereitstellungsmodell bereitstellen. Diese Art der Bereitstellung eignet sich hauptsächlich für Demonstrations- und Testzwecke. Es eignet sich nicht für den Einsatz in Produktionsumgebungen.

- Wählen Sie im Azure-Portal auf dem Blatt **Parameter** im Feld **NEWOREXISTINGSUBNET** die Option **new**. Lassen Sie das Feld **SUBNETID** leer.

  Das virtuelle Azure-Netzwerk und das Subnetz werden von der Azure Resource Manager-Vorlage für SAP automatisch erstellt.

> [!NOTE]
> Außerdem müssen Sie mindestens einen dedizierten virtuellen Computer für Active Directory und DNS in der gleichen Instanz von Azure Virtual Network bereitstellen. Die Vorlage erstellt diese virtuellen Computer nicht.
>
>


### <a name="prepare-the-infrastructure-for-architectural-template-2"></a>Vorbereiten der Infrastruktur für Architekturvorlage 2

Sie können diese Azure Resource Manager-Vorlage für SAP zur Vereinfachung der Bereitstellung der erforderlichen Infrastrukturressourcen für SAP-Architekturvorlage 2 verwenden.

Azure Resource Manager-Vorlagen für dieses Bereitstellungsszenario sind hier verfügbar:

* [Azure Marketplace-Image](https://github.com/Azure/azure-quickstart-templates/)  
* [Azure Marketplace-Image mit Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image-converged-md)  
* [Benutzerdefiniertes Image](https://github.com/Azure/azure-quickstart-templates/)
* [Benutzerdefiniertes Image mit Managed Disks](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image-converged-md)


### <a name="prepare-the-infrastructure-for-architectural-template-3"></a>Vorbereiten der Infrastruktur für Architekturvorlage 3

Sie können die Infrastruktur vorbereiten und SAP für **Multi-SID** konfigurieren. Beispielsweise können Sie eine weitere SAP ASCS/SCS-Instanz einer *vorhandenen* Clusterkonfiguration hinzufügen. Weitere Informationen finden Sie unter [Konfigurieren einer zusätzlichen SAP ASCS/SCS-Instanz in einer vorhandenen Clusterkonfiguration zum Erstellen einer Multi-SID-SAP-Konfiguration in Azure Resource Manager][sap-ha-multi-sid-guide].

Wenn Sie einen neuen Multi-SID-Cluster erstellen möchten, können Sie die Multi-SID-[Schnellstartvorlagen auf GitHub](https://github.com/Azure/azure-quickstart-templates) verwenden.
Sie müssen die folgenden drei Vorlagen bereitstellen, um einen neuen Multi-SID-Cluster zu erstellen:

* [ASCS/SCS-Vorlage](#ASCS-SCS-template)
* [Datenbankvorlage](#database-template)
* [Anwendungsservervorlage](#application-servers-template)

Die folgenden Abschnitte enthalten weitere Details zu den Vorlagen und Parametern, die Sie in den Vorlagen angeben müssen.

#### <a name="ascsscs-template"></a><a name="ASCS-SCS-template"></a> ASCS/SCS-Vorlage

Mit der ASCS/SCS-Vorlage werden zwei virtuelle Computer bereitgestellt, die Sie verwenden können, um einen Windows-Failovercluster zum Hosten von mehreren ASCS/SCS-Instanzen zu erstellen.

Geben Sie in der [ASCS/SCS-Multi-SID-Vorlage][sap-templates-3-tier-multisid-xscs-marketplace-image] oder der [ASCS/SCS-Multi-SID-Vorlage mit Managed Disks][sap-templates-3-tier-multisid-xscs-marketplace-image-md] Werte für die folgenden Parameter ein, um die ASCS/SCS-Multi-SID-Vorlage einzurichten:

  - **Ressourcenpräfix**.  Legen Sie das Ressourcenpräfix fest, um alle Ressourcen, die während der Bereitstellung erstellt werden, mit einem Präfix zu versehen. Da die Ressourcen nicht nur zu einem einzigen SAP-System gehören, ist das Präfix der Ressource nicht die SID eines einzigen SAP-Systems.  Das Präfix muss zwischen **drei und sechs Zeichen** lang sein.
  - **Stapeltyp**. Wählen Sie den Stapeltyp des SAP-Systems aus. Abhängig vom Stapeltyp hat der Azure Load Balancer eine (nur ABAP oder Java) oder zwei (ABAP+Java) private IP-Adressen pro SAP-System.
  -  **Betriebssystemtyp**. Wählen Sie das Betriebssystem des virtuellen Computers aus.
  -  **Anzahl von SAP-Systemen**. Wählen Sie die Anzahl von SAP-Systemen aus, die Sie in diesem Cluster installieren möchten.
  -  **Systemverfügbarkeit**. Wählen Sie **HA** (Hohe Verfügbarkeit).
  -  **Administratorbenutzername und Administratorkennwort**. Erstellen Sie einen neuen Benutzer, der verwendet werden kann, um sich am Computer anzumelden.
  -  **Neues oder vorhandenes Subnetz**. Legen Sie fest, ob ein neues virtuelles Netzwerk und Subnetz erstellt oder ein vorhandenes Subnetz verwendet werden soll. Wenn Sie bereits über ein virtuelles Netzwerk verfügen, das mit dem lokalen Netzwerk verbunden ist, wählen Sie hier **vorhanden** aus.
  -  **Subnetz-ID**. Wenn Sie die VM in einem vorhandenen VNET bereitstellen möchten, in dem Sie ein Subnetz definiert haben, dem die VM zugewiesen werden soll, geben Sie die ID dieses spezifischen Subnetzes an. Die ID hat normalerweise das folgende Format: /subscriptions/<*Abonnement-ID*>/resourceGroups/<*Name der Ressourcengruppe*>/providers/Microsoft.Network/virtualNetworks/<*Name des virtuellen Netzwerks*>/subnets/<*Name des Subnetzes*>

Die Vorlage stellt eine Azure Load Balancer-Instanz bereit, die mehrere SAP-Systeme unterstützt.

- Die ASCS-Instanzen werden für die Instanznummer 00, 10, 20... konfiguriert.
- Die SCS-Instanzen werden für die Instanznummer 01, 11, 21... konfiguriert.
- Die Instanzen von ASCS Enqueue Replication Server (ERS) (nur Linux) werden für die Instanznummern 02, 12, 22 usw. konfiguriert.
- Die SCS ERS-Instanzen (nur Linux) werden für die Instanznummer 03, 13, 23... konfiguriert.

Der Load Balancer enthält 1 VIP (2 für Linux), 1 VIP für ASCS/SCS und 1 VIP für ERS (nur Linux).

Die folgende Liste enthält alle Lastenausgleichsregeln (wobei x für die Nummer des SAP-Systems steht, z.B. 1, 2, 3...):
- Windows-spezifische Ports für jedes SAP-System: 445, 5985
- ASCS-Ports (Instanznummer x0): 32x0, 36x0, 39x0, 81x0, 5x013, 5x014, 5x016
- SCS-Ports (Instanznummer x1): 32x1, 33x1, 39x1, 81x1, 5x113, 5x114, 5x116
- ASCS ERS-Ports unter Linux (Instanznummer x2): 33x2, 5x213, 5x214, 5x216
- SCS ERS-Ports unter Linux (Instanznummer x3): 33x3, 5x313, 5x314, 5x316

Der Lastenausgleich wird so konfiguriert, dass die folgenden Testports verwendet werden (wobei x die Nummer des SAP-Systems ist, z.B. 1, 2, 3...).
- ASCS/SCS – Testport des internen Lastenausgleichs: 620x0
- ERS – Testport des internen Lastenausgleichs (nur Linux): 621x2

#### <a name="database-template"></a><a name="database-template"></a> Datenbankvorlage

Mit der Datenbankvorlage werden ein oder zwei virtuelle Computer bereitgestellt, die Sie zum Installieren des relationalen Datenbankverwaltungssystems (RDBMS) für ein SAP-System verwenden können. Wenn Sie z.B. eine ASCS/SCS-Vorlage für fünf SAP-Systeme bereitstellen, müssen Sie diese Vorlage fünfmal bereitstellen.

Geben Sie in der [Datenbank-Multi-SID-Vorlage][sap-templates-3-tier-multisid-db-marketplace-image] oder der [Datenbank-Multi-SID-Vorlage mit Managed Disks][sap-templates-3-tier-multisid-db-marketplace-image-md] Werte für die folgenden Parameter ein, um die Datenbank-Multi-SID-Vorlage einzurichten:

- **SAP-System-ID**. Geben Sie die SAP-System-ID des SAP-Systems ein, das Sie installieren möchten. Die ID wird als Präfix für die Ressourcen verwendet, die bereitgestellt werden.
- **Betriebssystemtyp**. Wählen Sie das Betriebssystem des virtuellen Computers aus.
- **Dbtype**. Wählen Sie den Typ der Datenbank, die Sie auf dem Cluster installieren möchten. Wählen Sie **SQL**, wenn Sie Microsoft SQL Server installieren möchten. Wählen Sie **HANA**, wenn Sie SAP HANA auf den virtuellen Computern installieren möchten. Stellen Sie sicher, dass Sie den richtigen Betriebssystemtyp auswählen: Wählen Sie **Windows** für SQL und eine Linux-Distribution für HANA aus. Der Azure Load Balancer, der mit den virtuellen Computern verbunden ist, wird so konfiguriert, dass er den ausgewählten Datenbanktyp unterstützt:
  * **SQL**. Der Lastenausgleich ist auf Port 1433 wirksam. Stellen Sie sicher, dass dieser Port für Ihr SQL Server AlwaysOn-Setup verwendet wird.
  * **HANA**. Der Lastenausgleich ist auf den Ports 35015 und 35017 wirksam. Stellen Sie sicher, dass Sie SAP HANA mit Instanznummer **50** installieren.
  Der Lastenausgleich verwendet den Testport 62550.
- **SAP-Systemgröße**. Legen Sie die Anzahl von SAPS fest, die vom neuen System bereitgestellt werden. Wenn Sie nicht sicher sind, wie viele SAPS das System benötigt, wenden Sie sich an den SAP-Technologiepartner oder Systemintegrator.
- **Systemverfügbarkeit**. Wählen Sie **HA** (Hohe Verfügbarkeit).
- **Administratorbenutzername und Administratorkennwort**. Erstellen Sie einen neuen Benutzer, der verwendet werden kann, um sich am Computer anzumelden.
- **Subnetz-ID**. Geben Sie die ID des Subnetzes ein, das Sie während der Bereitstellung der ASCS/SCS-Vorlage verwendet haben, oder die ID des Subnetzes, das als Teil der ASCS/SCS-Vorlagenbereitstellung erstellt wurde.

#### <a name="application-servers-template"></a><a name="application-servers-template"></a> Anwendungsservervorlage

Die Anwendungsservervorlage stellt zwei oder mehr virtuelle Computer bereit, die als SAP-Anwendungsserverinstanzen für ein SAP-System verwendet werden können. Wenn Sie z.B. eine ASCS/SCS-Vorlage für fünf SAP-Systeme bereitstellen, müssen Sie diese Vorlage fünfmal bereitstellen.

Geben Sie in der [Anwendungsserver-Multi-SID-Vorlage][sap-templates-3-tier-multisid-apps-marketplace-image] oder der [Anwendungsserver-Multi-SID-Vorlage mit Managed Disks][sap-templates-3-tier-multisid-apps-marketplace-image-md] Werte für die folgenden Parameter ein, um die Anwendungsserver-Multi-SID-Vorlage einzurichten:

  -  **SAP-System-ID**. Geben Sie die SAP-System-ID des SAP-Systems ein, das Sie installieren möchten. Die ID wird als Präfix für die Ressourcen verwendet, die bereitgestellt werden.
  -  **Betriebssystemtyp**. Wählen Sie das Betriebssystem des virtuellen Computers aus.
  -  **SAP-Systemgröße**. Die Anzahl von SAPS, die vom neuen System bereitgestellt werden. Wenn Sie nicht sicher sind, wie viele SAPS das System benötigt, wenden Sie sich an den SAP-Technologiepartner oder Systemintegrator.
  -  **Systemverfügbarkeit**. Wählen Sie **HA** (Hohe Verfügbarkeit).
  -  **Administratorbenutzername und Administratorkennwort**. Erstellen Sie einen neuen Benutzer, der verwendet werden kann, um sich am Computer anzumelden.
  -  **Subnetz-ID**. Geben Sie die ID des Subnetzes ein, das Sie während der Bereitstellung der ASCS/SCS-Vorlage verwendet haben, oder die ID des Subnetzes, das als Teil der ASCS/SCS-Vorlagenbereitstellung erstellt wurde.


### <a name="azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Azure Virtual Network
Bei unserem Beispiel ist der Adressbereich des virtuellen Azure-Netzwerks 10.0.0.0/16. Es gibt ein Subnetz mit dem Namen **Subnet** und dem Adressbereich 10.0.0.0/24. Alle virtuellen Computer und internen Load Balancer werden in diesem virtuellen Netzwerk bereitgestellt.

> [!IMPORTANT]
> Nehmen Sie keine Änderungen an den Netzwerkeinstellungen innerhalb des Gastbetriebssystems vor. Dies schließt IP-Adressen, DNS-Server und Subnetz ein. Konfigurieren Sie alle Einstellungen für Ihr Netzwerk in Azure. Der DHCP-Dienst (Dynamic Host Configuration Protocol) übermittelt diese Einstellungen.
>
>

### <a name="dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> DNS-IP-Adressen

Führen Sie die folgenden Schritte aus, um die erforderlichen DNS-IP-Adressen festzulegen.

1. Stellen Sie im Azure-Portal auf dem Blatt **DNS-Server** sicher, dass die Option **DNS-Server** für Ihr virtuelles Netzwerk auf **Custom DNS** (Benutzerdefiniertes DNS) festgelegt ist.
2. Wählen Sie die Einstellungen basierend auf dem Typ des Netzwerks aus. Weitere Informationen finden Sie in den folgenden Ressourcen:
   * Fügen Sie die IP-Adressen der lokalen DNS-Server hinzu.  
   Sie können lokale DNS-Server auf die virtuellen Computer erweitern, die in Azure ausgeführt werden. In diesem Szenario können Sie die IP-Adressen der virtuellen Azure-Computer hinzufügen, auf denen den DNS-Dienst ausgeführt wird.
   * Für in Azure isolierte VM-Bereitstellungen: Stellen Sie eine zusätzliche VM in der gleichen Virtual Network-Instanz bereit, die auch als DNS-Server fungiert. Fügen Sie die IP-Adressen der virtuellen Azure-Computer hinzu, die Sie zum Ausführen des DNS-Diensts eingerichtet haben.

   ![Abbildung 12: Konfigurieren von DNS-Servern für Azure Virtual Network][sap-ha-guide-figure-3001]

   _**Abbildung 12:** Konfigurieren von DNS-Servern für Azure Virtual Network_

   > [!NOTE]
   > Wenn Sie die IP-Adressen der DNS-Server ändern, müssen Sie die virtuellen Azure-Computer neu starten, damit die Änderung übernommen und an die neuen DNS-Server übertragen wird.
   >
   >

In unserem Beispiel ist der DNS-Dienst auf den folgenden virtuellen Windows-Computern installiert und konfiguriert:

| Rolle für virtuellen Computer | Hostname für virtuellen Computer | Name der Netzwerkkarte | Statische IP-Adresse |
| --- | --- | --- | --- |
| Erster DNS-Server |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| Zweiter DNS-Server |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

### <a name="host-names-and-static-ip-addresses-for-the-sap-ascsscs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> Hostnamen und statische IP-Adressen für die SAP ASCS/SCS-Clusterinstanz und -DBMS-Clusterinstanz

Für lokale Bereitstellungen benötigen Sie diese reservierten Hostnamen und IP-Adressen:

| Rolle des virtuellen Hosts | Name des virtuellen Hosts | Virtuelle statische IP-Adresse |
| --- | --- | --- |
| SAP ASCS/SCS – erster virtueller Host im Cluster (für die Clusterverwaltung) |pr1-ascs-vir |10.0.0.42 |
| SAP ASCS/SCS-Instanz – virtueller Hostname |pr1-ascs-sap |10.0.0.43 |
| SAP-DBMS – zweiter virtueller Host im Cluster (Clusterverwaltung) |pr1-dbms-vir |10.0.0.32 |

Bei der Erstellung des Clusters erstellen Sie die virtuellen Hostnamen **pr1-ascs-vir** und **pr1-dbms-vir** und die zugehörigen IP-Adressen, die den Cluster selbst verwalten. Informationen zur Vorgehensweise finden Sie unter [Collect cluster nodes in a cluster configuration][sap-ha-guide-8.12.1] (Erfassen von Clusterknoten in einer Clusterkonfiguration).

Sie können die anderen beiden virtuellen Hostnamen **pr1-ascs-sap** und **pr1-dbms-sap** und die zugeordneten IP-Adressen manuell auf dem DNS-Server erstellen. Die SAP ASCS/SCS-Clusterinstanz und die DBMS-Clusterinstanz verwenden diese Ressourcen. Informationen zur Vorgehensweise finden Sie unter [Erstellen eines virtuellen Hostnamens für die SAP ASCS/SCS-Clusterinstanz][sap-ha-guide-9.1.1].

### <a name="set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> Festlegen der statischen IP-Adressen für die beiden virtuellen SAP-Computer
Nach der Bereitstellung der virtuellen Computer für Ihren Cluster müssen Sie statische IP-Adressen für alle virtuellen Computer festlegen. Sie führen diesen Schritt in der Azure Virtual Network-Konfiguration und nicht im Gastbetriebssystem aus.

1. Wählen Sie im Azure-Portal **Ressourcengruppe** > **Netzwerkkarte** > **Einstellungen** > **IP-Adresse**.
2. Wählen Sie auf dem Blatt **IP-Adressen** unter **Zuweisung** die Option **Statisch**. Geben Sie im Feld **IP-Adresse** die IP-Adresse ein, die Sie verwenden möchten.

   > [!NOTE]
   > Wenn Sie die IP-Adresse der Netzwerkkarte ändern, müssen Sie die virtuellen Azure-Computer neu starten, um die Änderung zu übernehmen.  
   >
   >

   ![Abbildung 13: Festlegen der statischen IP-Adressen für die Netzwerkkarten der einzelnen VMs][sap-ha-guide-figure-3002]

   _**Abbildung 13:** Festlegen der statischen IP-Adressen für die Netzwerkkarten der einzelnen VMs_

   Wiederholen Sie diesen Schritt für alle Netzwerkschnittstellen, d.h. für alle virtuellen Computer, einschließlich der virtuellen Computer, die Sie für den Active Directory-/DNS-Dienst verwenden möchten.

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

### <a name="set-a-static-ip-address-for-the-azure-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> Festlegen der statischen IP-Adresse für den internen Azure Load Balancer

Die Azure Resource Manager-Vorlage für SAP erstellt einen internen Azure Load Balancer, der für den Cluster der SAP ASCS/SCS-Instanz und den DBMS-Cluster verwendet wird.

> [!IMPORTANT]
> Die IP-Adresse des virtuellen Hostnamens von SAP ASCS/SCS ist mit der IP-Adresse des internen Load Balancers **pr1-lb-ascs** für die SAP ASCS/SCS-Instanz identisch.
> Die IP-Adresse des virtuellen Namens des DBMS ist mit der IP-Adresse des internen Load Balancers **pr1-lb-dbms** für das DBMS identisch.
>
>

So legen Sie eine statische IP-Adresse für den internen Azure Load Balancer fest

1. Bei der ersten Bereitstellung wird die IP-Adresse des internen Load Balancers auf **Dynamisch** festgelegt. Wählen Sie im Azure-Portal auf dem Blatt **IP-Adressen** unter **Zuweisung** die Option **Statisch**.
2. Legen Sie die IP-Adresse des internen Load Balancers **pr1-lb-ascs** auf die IP-Adresse des virtuellen Hostnamens der SAP ASCS/SCS-Instanz fest.
3. Legen Sie die IP-Adresse des internen Load Balancers **pr1-lb-dbms** auf die IP-Adresse des virtuellen Hostnamens der DBMS-Instanz fest.

   ![Abbildung 14: Festlegen der statischen IP-Adressen für den internen Lastenausgleich der SAP ASCS/SCS-Instanz][sap-ha-guide-figure-3003]

   _**Abbildung 14:** Festlegen der statischen IP-Adressen für den internen Lastenausgleich der SAP ASCS/SCS-Instanz_

In unserem Beispiel verwenden wir zwei interne Azure Load Balancer mit diesen statischen IP-Adressen:

| Rolle des internen Azure Load Balancers | Name des internen Azure Load Balancers | Statische IP-Adresse |
| --- | --- | --- |
| Interner Load Balancer für die SAP ASCS/SCS-Instanz |pr1-lb-ascs |10.0.0.43 |
| Interner Load Balancer für das SAP-DBMS |pr1-lb-dbms |10.0.0.33 |


### <a name="default-ascsscs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Standardregeln für den ASCS/SCS-Lastenausgleich für den internen Azure Load Balancer

Die Azure Resource Manager-Vorlage für SAP erstellt alle erforderlichen Ports:
* Eine ABAP ASCS-Instanz mit der Standardinstanznummer **00**
* Eine Java SCS-Instanz mit der Standardinstanznummer **01**

Wenn Sie Ihre SAP ASCS/SCS-Instanz installieren, müssen Sie die Standardinstanznummer **00** für Ihre ABAP ASCS-Instanz und die Standardinstanznummer **01** für Ihre Java SCS-Instanz verwenden.

Als Nächstes erstellen Sie erforderliche Endpunkte für den internen Lastenausgleich für die SAP NetWeaver-Ports.

Für die Erstellung von erforderlichen Endpunkten für den internen Lastenausgleich erstellen Sie zuerst diese Endpunkte für den internen Lastenausgleich für die SAP NetWeaver ABAP ASCS-Ports:

| Dienst/Name der Lastenausgleichsregel | Standardportnummern | Konkrete Ports für (ASCS-Instanz mit Instanznummer 00) (ERS mit 10) |
| --- | --- | --- |
| Enqueue-Server/ *lbrule3200* |32<*Instanznummer*> |3200 |
| ABAP-Message-Server/ *lbrule3600* |36<*Instanznummer*> |3600 |
| Interne ABAP-Nachricht/ *lbrule3900* |39<*Instanznummer*> |3900 |
| Message-Server HTTP/ *Lbrule8100* |81<*Instanznummer*> |8100 |
| SAP Start Service ASCS HTTP/ *Lbrule50013* |5<*Instanznummer*>13 |50013 |
| SAP Start Service ASCS HTTPS/ *Lbrule50014* |5<*Instanznummer*>14 |50014 |
| Enqueue-Replikation/ *Lbrule50016* |5<*Instanznummer*>16 |50016 |
| SAP Start Service ERS HTTP *Lbrule51013* |5<*Instanznummer*>13 |51013 |
| SAP Start Service ERS HTTP *Lbrule51014* |5<*Instanznummer*>14 |51014 |
| Win RM *Lbrule5985* | |5985 |
| Dateifreigabe *Lbrule445* | |445 |

_**Tabelle 1:** Portnummern der SAP NetWeaver ABAP ASCS-Instanzen_

Erstellen Sie anschließend diese Endpunkte für den Lastenausgleich für die SAP NetWeaver Java SCS-Ports:

| Dienst/Name der Lastenausgleichsregel | Standardportnummern | Konkrete Ports für (SCS-Instanz mit Instanznummer 01) (ERS mit 11) |
| --- | --- | --- |
| Enqueue-Server/ *lbrule3201* |32<*Instanznummer*> |3201 |
| Gateway-Server/ *lbrule3301* |33<*Instanznummer*> |3301 |
| Java-Message-Server/ *lbrule3900* |39<*Instanznummer*> |3901 |
| Message-Server-HTTP/ *Lbrule8101* |81<*Instanznummer*> |8101 |
| SAP Start Service SCS HTTP/ *Lbrule50113* |5<*Instanznummer*>13 |50113 |
| SAP Start Service SCS HTTPS/ *Lbrule50114* |5<*Instanznummer*>14 |50114 |
| Enqueue-Replikation/ *Lbrule50116* |5<*Instanznummer*>16 |50116 |
| SAP Start Service ERS HTTP *Lbrule51113* |5<*Instanznummer*>13 |51113 |
| SAP Start Service ERS HTTP *Lbrule51114* |5<*Instanznummer*>14 |51114 |
| Win RM *Lbrule5985* | |5985 |
| Dateifreigabe *Lbrule445* | |445 |

_**Tabelle 2:** Portnummern der SAP NetWeaver Java SCS-Instanzen_

![Abbildung 15: Standardregeln für den ASCS/SCS-Lastenausgleich für den internen Azure Load Balancer][sap-ha-guide-figure-3004]

_**Abbildung 15:** Standardregeln für den ASCS/SCS-Lastenausgleich für den internen Azure Load Balancer_

Legen Sie die IP-Adresse des Lastenausgleichs **pr1-lb-dbms** auf die IP-Adresse des virtuellen Hostnamens der DBMS-Instanz fest.

### <a name="change-the-ascsscs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> Ändern der Standardregeln für den ASCS/SCS-Lastenausgleich für den internen Azure Load Balancer

Wenn Sie andere Nummern für die SAP ASCS- oder SCS-Instanzen verwenden möchten, müssen Sie die Namen und Werte der entsprechenden Ports in der Standardeinstellung ändern.

1. Wählen Sie im Azure-Portal **<*SID*>-lb-ascs load balancer** > **Lastenausgleichsregeln**.
2. Ändern Sie für alle zur SAP ASCS- oder SCS-Instanz gehörenden Lastenausgleichsregeln die folgenden Werte:

   * Name
   * Port
   * Back-End-Port

   Wenn Sie z.B. die Standardnummer der ASCS-Instanz von 00 in 31 ändern möchten, müssen Sie die Änderungen für alle in Tabelle 1 aufgeführten Ports durchführen.

   Dies ist ein Beispiel einer Änderung für den Port *lbrule3200*.

   ![Abbildung 16: Ändern der Standardregeln für den ASCS/SCS-Lastenausgleich für den internen Azure Load Balancer][sap-ha-guide-figure-3005]

   _**Abbildung 16:** Ändern der Standardregeln für den ASCS/SCS-Lastenausgleich für den internen Azure Load Balancer_

### <a name="add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> Hinzufügen virtueller Windows-Computer zur Domäne

Nachdem Sie den virtuellen Computern eine statische IP-Adresse zugewiesen haben, fügen Sie die virtuellen Computer der Domäne hinzu.

![Abbildung 17: Hinzufügen einer VM zu einer Domäne][sap-ha-guide-figure-3006]

_**Abbildung 17:** Hinzufügen einer VM zu einer Domäne_

### <a name="add-registry-entries-on-both-cluster-nodes-of-the-sap-ascsscs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> Hinzufügen von Registrierungseinträgen auf beiden Clusterknoten für eine SAP ASCS/SCS-Instanz

Der Azure Load Balancer verfügt über einen internen Load Balancer, der Verbindungen schließt, wenn sich diese für einen festgelegten Zeitraum im Leerlauf befinden (Leerlaufzeitlimit). SAP-Workprozesse öffnen in Dialoginstanzen Verbindungen mit dem SAP-Enqueue-Prozess, sobald die erste Enqueue-/Dequeue-Anforderung gesendet werden muss. Diese Verbindungen bleiben in der Regel bestehen, bis der Workprozess oder Enqueue-Prozess neu gestartet wird. Wenn sich die Verbindung für einen festgelegten Zeitraum im Leerlauf befindet, schließt der interne Azure Load Balancer aber die Verbindungen. Dies stellt kein Problem dar, da der SAP-Arbeitsprozesses die Verbindung mit dem Enqueue-Prozess erneut herstellt, wenn sie nicht mehr vorhanden sein sollte. Diese Aktivitäten werden in den Entwickler-Traces von SAP-Prozessen dokumentiert, sie erstellen jedoch eine große Menge an zusätzlichen Inhalten in diesen Traces. Daher wird empfohlen, die TCP/IP-Einstellungen `KeepAliveTime` und `KeepAliveInterval` auf beiden Clusterknoten zu ändern. Kombinieren Sie diese Änderungen an den TCP/IP-Parametern mit SAP-Profilparametern, die weiter unten in diesem Artikel beschrieben werden.

Fügen Sie zuerst diese Windows-Registrierungseinträge auf beiden Windows-Clusterknoten für SAP ASCS/SCS hinzu, um die Registrierungseinträge auf beiden Clusterknoten der SAP ASCS/SCS-Instanz hinzuzufügen:

| `Path` | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Variablenname |`KeepAliveTime` |
| Variablentyp |REG_DWORD-Wert (dezimal) |
| Wert |120000 |
| Link zur Dokumentation |[https://technet.microsoft.com/library/cc957549.aspx](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)) |

_**Tabelle 3:** Ändern des ersten TCP/IP-Parameters_

Fügen Sie diese Windows-Registrierungseinträge dann auf beiden Windows-Clusterknoten für SAP ASCS/SCS hinzu:

| `Path` | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| Variablenname |`KeepAliveInterval` |
| Variablentyp |REG_DWORD-Wert (dezimal) |
| Wert |120000 |
| Link zur Dokumentation |[https://technet.microsoft.com/library/cc957548.aspx](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) |

_**Tabelle 4:** Ändern des zweiten TCP/IP-Parameters_

**Starten Sie beide Clusterknoten neu, um die Änderungen zu übernehmen.**

### <a name="set-up-a-windows-server-failover-clustering-cluster-for-an-sap-ascsscs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> Einrichten eines Windows Server-Failoverclustering-Clusters für eine SAP ASCS/SCS-Instanz

Das Einrichten eines Windows Server-Failoverclustering-Clusters für eine SAP ASCS/SCS-Instanz umfasst diese Aufgaben:

- Erfassen der Clusterknoten in einer Clusterkonfiguration
- Konfigurieren eines Cluster-Dateifreigabenzeugen

#### <a name="collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> Erfassen der Clusterknoten in einer Clusterkonfiguration

1. Fügen Sie im Assistenten zum Hinzufügen von Rollen und Features beiden Clusterknoten das Failoverclustering hinzu.
2. Richten Sie den Failovercluster mit dem Failovercluster-Manager ein. Wählen Sie im Failovercluster-Manager die Option **Cluster erstellen**, und fügen Sie dann nur den Namen des ersten Clusters (Knoten A) hinzu. Fügen Sie den zweiten Knoten noch nicht hinzu. Dies führen Sie in einem späteren Schritt durch.

   ![Abbildung 18: Hinzufügen eines Namens für den Server oder die VM des ersten Clusterknotens][sap-ha-guide-figure-3007]

   _**Abbildung 18:** Hinzufügen eines Namens für den Server oder die VM des ersten Clusterknotens_

3. Geben Sie den Netzwerknamen (Name des virtuellen Hosts) des Clusters ein.

   ![Abbildung 19: Eingeben des Clusternamens][sap-ha-guide-figure-3008]

   _**Abbildung 19:** Eingeben des Clusternamens_

4. Führen Sie nach dem Erstellen des Clusters einen Test zur Überprüfung des Clusters durch.

   ![Abbildung 20: Ausführen der Clusterüberprüfung][sap-ha-guide-figure-3009]

   _**Abbildung 20:** Ausführen der Clusterüberprüfung_

   Sie können an diesem Punkt im Prozess sämtliche Warnungen zu Datenträgern ignorieren. Sie fügen später einen Dateifreigabenzeugen und die freigegebenen SIOS-Datenträger hinzu. In dieser Phase müssen Sie noch kein Quorum berücksichtigen.

   ![Abbildung 21: Kein Quorumdatenträger gefunden][sap-ha-guide-figure-3010]

   _**Abbildung 21:** Kein Quorumdatenträger gefunden_

   ![Abbildung 22: Die Hauptclusterressource benötigt eine neue IP-Adresse.][sap-ha-guide-figure-3011]

   _**Abbildung 22:** Die Hauptclusterressource benötigt eine neue IP-Adresse._

5. Ändern Sie die IP-Adresse des Hauptclusterdiensts. Der Cluster kann erst gestartet werden, nachdem Sie die IP-Adresse des Hauptclusterdiensts geändert haben, da die IP-Adresse der Serverpunkte auf einen der Knoten des virtuellen Computers zeigt. Dies wird auf der Seite **Eigenschaften** der IP-Ressource des Hauptclusterdiensts durchgeführt.

   Wir müssen z.B. dem virtuellen Hostnamen des Clusters **pr1-ascs-vir** eine IP-Adresse (im Beispiel **10.0.0.42**) zuweisen.

   ![Abbildung 23: Ändern der IP-Adresse im Dialogfeld „Eigenschaften“][sap-ha-guide-figure-3012]

   _**Abbildung 23:** Ändern der IP-Adresse im Dialogfeld **Eigenschaften**_

   ![Abbildung 24: Zuweisen der für den Cluster reservierten IP-Adresse][sap-ha-guide-figure-3013]

   _**Abbildung 24:** Zuweisen der für den Cluster reservierten IP-Adresse_

6. Versetzen Sie den Namen des virtuellen Hosts für den Cluster in den Onlinezustand.

   ![Abbildung 25: Hauptclusterdienst im laufenden Betrieb mit korrekter IP-Adresse][sap-ha-guide-figure-3014]

   _**Abbildung 25:** Hauptclusterdienst im laufenden Betrieb mit korrekter IP-Adresse_

7. Fügen Sie den zweiten Clusterknoten hinzu.

   Nachdem der Hauptclusterdienst in Betrieb ist, können Sie den zweiten Clusterknoten hinzufügen.

   ![Abbildung 26: Hinzufügen des zweiten Clusterknotens][sap-ha-guide-figure-3015]

   _**Abbildung 26:** Hinzufügen des zweiten Clusterknotens_

8. Geben Sie einen Namen für zweiten Clusterknotenhost ein.

   ![Abbildung 27: Eingeben des Namens für den zweiten Clusterknotenhost][sap-ha-guide-figure-3016]

   _**Abbildung 27:** Eingeben des Namens für den zweiten Clusterknotenhost_

   > [!IMPORTANT]
   > Stellen Sie sicher, dass das Kontrollkästchen **Der gesamte geeignete Speicher soll dem Cluster hinzugefügt werden** **NICHT** aktiviert wird.  
   >
   >

   ![Abbildung 28: Kontrollkästchen nicht aktivieren][sap-ha-guide-figure-3017]

   _**Abbildung 28:** Kontrollkästchen **nicht** aktivieren_

   Sie können Warnungen zu Quorum und Datenträgern ignorieren. Sie konfigurieren das Quorum und die Freigabe der Datenträger später in [Installieren von SIOS DataKeeper Cluster Edition für SAP ASCS/SCS-Clusterfreigabe-Datenträger][sap-ha-guide-8.12.3].

   ![Abbildung 29: Ignorieren der Warnungen zum Datenträgerquorum][sap-ha-guide-figure-3018]

   _**Abbildung 29:** Ignorieren der Warnungen zum Datenträgerquorum_


#### <a name="configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> Konfigurieren eines Cluster-Dateifreigabenzeugen

Das Konfigurieren eines Cluster-Dateifreigabenzeugen umfasst die folgenden Aufgaben:

- Erstellen einer Dateifreigabe
- Festlegen des Dateifreigabenzeugen-Quorums im Failovercluster-Manager

##### <a name="create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> Erstellen einer Dateifreigabe

1. Wählen Sie anstelle eines Quorumdatenträgers einen Dateifreigabenzeugen aus. Diese Option wird von SIOS DataKeeper unterstützt.

   In den Beispielen in diesem Artikel befindet sich der Dateifreigabenzeuge auf dem Active Directory-/DNS-Server, der in Azure ausgeführt wird. Der Dateifreigabenzeuge heißt **domcontr 0**. Da Sie eine VPN-Verbindung mit Azure (Site-to-Site-VPN oder Azure ExpressRoute) konfiguriert hätten, ist Ihr Active Directory-/DNS-Dienst lokal und nicht für die Ausführung eines Dateifreigabenzeugen geeignet.

   > [!NOTE]
   > Wenn Ihr Active Directory-/DNS-Dienst nur lokal ausgeführt wird, konfigurieren Sie keinen Dateifreigabenzeugen auf dem lokalen Windows-Betriebssystem mit Active Directory/DNS. Die Netzwerklatenz zwischen Clusterknoten in Azure und lokalen Active Directory-/DNS-Diensten wäre zu hoch und könnte Verbindungsprobleme verursachen. Konfigurieren Sie den Dateifreigabenzeugen unbedingt auf einem virtuellen Azure-Computer, der in der Nähe des Clusterknotens betrieben wird.  
   >
   >

   Das Quorumlaufwerk benötigt mindestens 1.024 MB freien Speicherplatz. Für das Quorumlaufwerk empfehlen wir die Verwendung von 2.048 MB freiem Speicherplatz.

2. Fügen Sie das Clusternamenobjekt hinzu.

   ![Abbildung 30: Zuweisen der Berechtigungen für die Freigabe für das Clusternamenobjekt][sap-ha-guide-figure-3019]

   _**Abbildung 30:** Zuweisen der Berechtigungen für die Freigabe für das Clusternamenobjekt_

   Vergewissern Sie sich, dass die Berechtigungen das Ändern von Daten auf der Freigabe für das Clusternamenobjekt zulassen (im Beispiel **pr1-ascs-vir$** ).

3. Wählen Sie **Hinzufügen** aus, um das Clusternamenobjekt der Liste hinzuzufügen. Ändern Sie den Filter, um zusätzlich zu den in Abbildung 31 dargestellten Objekten auch Computerobjekte einzuschließen.

   ![Abbildung 31: Ändern des Objekttyps, um Computer einzuschließen][sap-ha-guide-figure-3020]

   _**Abbildung 31:** Ändern des Objekttyps, um Computer einzuschließen_

   ![Abbildung 32: Aktivieren des Kontrollkästchens „Computer“][sap-ha-guide-figure-3021]

   _**Abbildung 32:** Aktivieren des Kontrollkästchens **Computer**_

4. Geben Sie das Clusternamenobjekt ein, wie in Abbildung 31 dargestellt. Da der Datensatz bereits erstellt wurde, können Sie die Berechtigungen ändern (siehe Abbildung 30).

5. Wählen Sie die Registerkarte **Sicherheit** für die Freigabe aus, und legen Sie anschließend ausführlichere Berechtigungen für das Clusternamenobjekt fest.

   ![Abbildung 33: Festlegen der Sicherheitsattribute für das Clusternamenobjekt im Dateifreigabequorum][sap-ha-guide-figure-3022]

   _**Abbildung 33:** Festlegen der Sicherheitsattribute für das Clusternamenobjekt im Dateifreigabequorum_

##### <a name="set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> Festlegen des Dateifreigabenzeugen-Quorums im Failovercluster-Manager

1. Öffnen Sie den Assistenten zum Konfigurieren des Clusterquorums.

   ![Abbildung 34: Starten des Assistenten zum Konfigurieren des Clusterquorums][sap-ha-guide-figure-3023]

   _**Abbildung 34:** Starten des Assistenten zum Konfigurieren des Clusterquorums_

2. Wählen Sie auf der Seite **Quorumkonfiguration auswählen** die Option **Quorumzeugen auswählen**.

   ![Abbildung 35: Verfügbare Quorumkonfigurationen][sap-ha-guide-figure-3024]

   _**Abbildung 35:** Verfügbare Quorumkonfigurationen_

3. Wählen Sie auf der Seite **Quorumzeuge auswählen** die Option **Dateifreigabenzeugen konfigurieren**.

   ![Abbildung 36: Auswählen des Dateifreigabenzeugen][sap-ha-guide-figure-3025]

   _**Abbildung 36:** Auswählen des Dateifreigabenzeugen_

4. Geben Sie den UNC-Pfad zur Dateifreigabe ein (im Beispiel \\domcontr-0\FSW). Wählen Sie **Weiter** aus, um eine Liste der Änderungen anzuzeigen, die Sie vornehmen können.

   ![Abbildung 37: Festlegen des Dateifreigabespeicherorts für die Zeugenfreigabe][sap-ha-guide-figure-3026]

   _**Abbildung 37:** Festlegen des Dateifreigabespeicherorts für die Zeugenfreigabe_

5. Wählen Sie die gewünschten Änderungen und dann **Weiter** aus. Sie müssen den Cluster erfolgreich neu konfigurieren, wie in Abbildung 38 dargestellt.  

   ![Abbildung 38: Bestätigung der Neukonfiguration des Clusters][sap-ha-guide-figure-3027]

   _**Abbildung 38:** Bestätigung der Neukonfiguration des Clusters_

Nach der erfolgreichen Installation des Windows-Failoverclusters müssen an einigen Schwellenwerten Änderungen vorgenommen werden, um die Failovererkennung den Bedingungen in Azure anzupassen. Die zu ändernden Parameter sind in diesem Blog dokumentiert: https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/ . Vorausgesetzt, dass Ihre beiden virtuellen Computer, die die Windows-Clusterkonfiguration für ASCS/SCS erstellen, sich im gleichen Subnetz befinden, müssen die folgenden Parameter in diese Werte geändert werden:
- SameSubNetDelay = 2
- SameSubNetThreshold = 15

Diese Einstellungen wurden von Kunden getestet und boten einerseits einen guten Kompromiss, um flexibel genug zu sein. Andererseits ermöglichten diese Einstellungen unter realen Fehlerbedingungen der SAP-Software bzw. bei Knoten-/VM-Fehlern ein genügend schnelles Failover. 

### <a name="install-sios-datakeeper-cluster-edition-for-the-sap-ascsscs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> Installieren von SIOS DataKeeper Cluster Edition für den SAP ASCS/SCS-Clusterfreigabe-Datenträger

Sie verfügen nun über eine funktionierende Windows Server-Failoverclusteringkonfiguration in Azure. Um eine SAP ASCS/SCS-Instanz installieren zu können, benötigen Sie jedoch eine freigegebene Datenträgerressource. Sie können die erforderlichen freigegebenen Datenträgerressourcen nicht in Azure erstellen. SIOS DataKeeper Cluster Edition ist eine Drittanbieterlösung, die Sie zum Erstellen von freigegebenen Datenträgerressourcen verwenden können.

Das Installieren von SIOS DataKeeper Cluster Edition für SAP ASCS/SCS-Clusterfreigabe-Datenträger umfasst die folgenden Aufgaben:

- Hinzufügen von .NET Framework 3.5
- Installieren von SIOS DataKeeper
- Einrichten von SIOS DataKeeper

#### <a name="add-the-net-framework-35"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> Hinzufügen von .NET Framework 3.5
Microsoft .NET Framework 3.5 wird unter Windows Server 2012 R2 nicht automatisch aktiviert oder installiert. Da .NET Framework für SIOS DataKeeper auf allen Knoten erforderlich ist, auf denen Sie DataKeeper installieren, müssen Sie .NET Framework 3.5 unter dem Gastbetriebssystem aller virtuellen Computer im Cluster installieren.

Es gibt zwei Möglichkeiten, .NET Framework 3.5 hinzuzufügen:

- Verwenden Sie den Assistenten zum Hinzufügen von Rollen und Features in Windows, der in Abbildung 39 dargestellt ist.

  ![Abbildung 39: Installieren von .NET Framework 3.5 mithilfe des Assistenten zum Hinzufügen von Rollen und Features][sap-ha-guide-figure-3028]

  _**Abbildung 39:** Installieren von .NET Framework 3.5 mithilfe des Assistenten zum Hinzufügen von Rollen und Features_

  ![Abbildung 40: Statusanzeige für die Installation von .NET Framework 3.5 mit dem Assistenten zum Hinzufügen von Rollen und Features][sap-ha-guide-figure-3029]

  _**Abbildung 40:** Statusanzeige für die Installation von .NET Framework 3.5 mit dem Assistenten zum Hinzufügen von Rollen und Features_

- Verwenden Sie das Befehlszeilentool „dism.exe“. Für diese Art von Installation benötigen Sie Zugriff auf das Verzeichnis „SxS“ auf dem Windows-Installationsmedium. Geben Sie an einer Eingabeaufforderung mit erhöhten Rechten Folgendes ein:

  ```
  Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
  ```

#### <a name="install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a> Installieren von SIOS DataKeeper

Installieren Sie SIOS DataKeeper Cluster Edition auf jedem Knoten im Cluster. Erstellen Sie eine synchronisierte Spiegelung, und simulieren Sie dann freigegebenen Clusterspeicher, um mit SIOS DataKeeper virtuellen freigegebenen Speicher zu erstellen.

Erstellen Sie vor der Installation der SIOS-Software den Domänenbenutzer **DataKeeperSvc**.

> [!NOTE]
> Fügen Sie den Benutzer **DataKeeperSvc** der Gruppe **Lokale Administratoren** auf beiden Clusterknoten hinzu.
>
>

So installieren Sie SIOS DataKeeper

1. Installieren Sie die SIOS-Software auf beiden Clusterknoten.

   ![SIOS-Installationsprogramm][sap-ha-guide-figure-3030]

   ![Abbildung 41: Erste Seite der SIOS DataKeeper-Installation][sap-ha-guide-figure-3031]

   _**Abbildung 41:** Erste Seite der SIOS DataKeeper-Installation_

2. Wählen Sie in dem in Abbildung 42 dargestellten Dialogfeld **Ja** aus.

   ![Abbildung 42: Benachrichtigung über die Deaktivierung eines Diensts in DataKeeper][sap-ha-guide-figure-3032]

   _**Abbildung 42:** Benachrichtigung über die Deaktivierung eines Diensts in DataKeeper_

3. Wir raten Ihnen, im Dialogfeld, das in Abbildung 43 dargestellt ist, die Option **Domänen- oder Serverkonto** zu wählen.

   ![Abbildung 43: Benutzerauswahl für SIOS DataKeeper][sap-ha-guide-figure-3033]

   _**Abbildung 43:** Benutzerauswahl für SIOS DataKeeper_

4. Geben Sie den Benutzernamen für das Domänenkonto und die Kennwörter ein, die Sie für SIOS DataKeeper erstellt haben.

   ![Abbildung 44: Eingeben des Domänenbenutzernamens und des Kennworts für die SIOS DataKeeper-Installation][sap-ha-guide-figure-3034]

   _**Abbildung 44:** Eingeben des Domänenbenutzernamens und des Kennworts für die SIOS DataKeeper-Installation_

5. Installieren Sie den Lizenzschlüssel für Ihre SIOS DataKeeper-Instanz, wie in Abbildung 45 gezeigt.

   ![Abbildung 45: Eingeben Ihres SIOS DataKeeper-Lizenzschlüssels][sap-ha-guide-figure-3035]

   _**Abbildung 45:** Eingeben Ihres SIOS DataKeeper-Lizenzschlüssels_

6. Starten Sie den virtuellen Computer neu, wenn Sie dazu aufgefordert werden.

#### <a name="set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> Einrichten von SIOS DataKeeper

Nach der Installation von SIOS DataKeeper auf beiden Knoten müssen Sie mit der Konfiguration beginnen. Ziel der Konfiguration ist eine synchrone Datenreplikation zwischen den zusätzlichen Datenträgern, die Sie an jeden virtuellen Computer angefügt haben.

1. Starten Sie das DataKeeper-Tool für die Verwaltung und Konfiguration, und wählen Sie dann **Verbindung mit Server herstellen** aus. (In Abbildung 46 ist diese Option rot markiert.)

   ![Abbildung 46: SIOS DataKeeper-Tool für die Verwaltung und Konfiguration][sap-ha-guide-figure-3036]

   _**Abbildung 46:** SIOS DataKeeper-Tool für die Verwaltung und Konfiguration_

2. Geben Sie den Namen oder die TCP/IP-Adresse des ersten Knotens, mit dem das Tool für die Verwaltung und Konfiguration eine Verbindung herstellen soll, und im zweiten Schritt den zweiten Knoten ein.

   ![Abbildung 47: Eingeben des Namens oder der TCP/IP-Adresse des ersten Knotens, mit dem das Tool für die Verwaltung und Konfiguration eine Verbindung herstellen soll, und im zweiten Schritt Eingeben des zweiten Knotens][sap-ha-guide-figure-3037]

   _**Abbildung 47:** Eingeben des Namens oder der TCP/IP-Adresse des ersten Knotens, mit dem das Tool für die Verwaltung und Konfiguration eine Verbindung herstellen soll, und im zweiten Schritt Eingeben des zweiten Knotens_

3. Erstellen Sie den Replikationsauftrag zwischen den beiden Knoten.

   ![Abbildung 48: Erstellen eines Replikationsauftrags][sap-ha-guide-figure-3038]

   _**Abbildung 48:** Erstellen eines Replikationsauftrags_

   Ein Assistent führt Sie durch die Erstellung eines Replikationsauftrags.
4. Definieren Sie Name, TCP/IP-Adresse und Datenträgervolume des Quellknotens.

   ![Abbildung 49: Festlegen des Namens für den Replikationsauftrag][sap-ha-guide-figure-3039]

   _**Abbildung 49:** Festlegen des Namens für den Replikationsauftrag_

   ![Abbildung 50: Definieren der Basisdaten für den Knoten, der der aktuelle Quellknoten sein soll][sap-ha-guide-figure-3040]

   _**Abbildung 50:** Definieren der Basisdaten für den Knoten, der der aktuelle Quellknoten sein soll_

5. Definieren Sie Name, TCP/IP-Adresse und Datenträgervolume des Zielknotens.

   ![Abbildung 51: Definieren der Basisdaten für den Knoten, der der aktuelle Zielknoten sein soll][sap-ha-guide-figure-3041]

   _**Abbildung 51:** Definieren der Basisdaten für den Knoten, der der aktuelle Zielknoten sein soll_

6. Definieren Sie die Komprimierungsalgorithmen. Für unser Beispiel wird das Komprimieren des Replikationsdatenstroms empfohlen. Insbesondere in Situationen mit erneuter Synchronisierung wird die dafür erforderliche Dauer durch die Komprimierung des Replikationsdatenstroms erheblich verkürzt. Beachten Sie, dass die Komprimierung CPU- und Arbeitsspeicherressourcen eines virtuellen Computers verbraucht. Wenn die Komprimierungsrate zunimmt, steigt auch die Menge der erforderlichen CPU-Ressourcen. Sie können diese Einstellung auch später anpassen.

7. Eine weitere Einstellung, die Sie überprüfen müssen, ist, ob die Replikation asynchron oder synchron erfolgt. *Wenn Sie SAP ASCS/SCS-Konfigurationen schützen, müssen Sie die synchrone Replikation verwenden*.  

   ![Abbildung 52: Festlegen der Replikationsdetails][sap-ha-guide-figure-3042]

   _**Abbildung 52:** Festlegen der Replikationsdetails_

8. Definieren Sie, ob das Volume, das vom Replikationsauftrag repliziert wird, gegenüber einer Windows Server-Failoverclustering-Clusterkonfiguration als freigegebener Datenträger dargestellt werden sollte. Für die SAP ASCS/SCS-Konfiguration wählen Sie **Ja** aus, damit der Windows-Cluster das replizierte Volume als freigegebenen Datenträger erkennt, das als Clustervolume verwendet werden kann.

   ![Abbildung 53: Klicken auf „Ja“, um das replizierte Volume als Clustervolume festzulegen][sap-ha-guide-figure-3043]

   _**Abbildung 53:** Klicken auf **Ja**, um das replizierte Volume als Clustervolume festzulegen_

   Nachdem das Volume erstellt wurde, zeigt das DataKeeper-Tool für die Verwaltung und Konfiguration an, dass der Replikationsauftrag aktiv ist.

   ![Abbildung 54: Die synchrone Spiegelung von DataKeeper für den SAP ASCS/SCS-Freigabedatenträger ist aktiv.][sap-ha-guide-figure-3044]

   _**Abbildung 54:** Die synchrone Spiegelung von DataKeeper für den SAP ASCS/SCS-Freigabedatenträger ist aktiv._

   Der Failovercluster-Manager zeigt nun den Datenträger als DataKeeper-Datenträger an, wie in Abbildung 55 dargestellt.

   ![Abbildung 55: Anzeige des von DataKeeper replizierten Datenträgers im Failovercluster-Manager][sap-ha-guide-figure-3045]

   _**Abbildung 55:** Anzeige des von DataKeeper replizierten Datenträgers im Failovercluster-Manager_

## <a name="install-the-sap-netweaver-system"></a><a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> Installieren des SAP NetWeaver-Systems

Das DBMS-Setup wird hier nicht beschrieben, da die Einrichtung vom verwendeten DBMS-System abhängt. Jedoch wird davon ausgegangen, dass auf hohe Verfügbarkeit bezogene Aspekte des DBMS mit den Funktionen verwaltet werden, die verschiedene DBMS-Hersteller für Azure unterstützen. Beispiele hierfür sind Always On oder Datenbankspiegelung für SQL Server und Oracle Data Guard für Oracle-Datenbanken. In dem Szenario in diesem Artikel haben wir keinen zusätzlichen Schutz für das DBMS hinzugefügt.

Es sind keine Besonderheiten zu berücksichtigen, wenn unterschiedliche DBMS-Dienste mit dieser Art von SAP ASCS/SCS-Clusterkonfiguration in Azure interagieren.

> [!NOTE]
> Der Installationsvorgang von SAP NetWeaver ABAP-, Java- und ABAP+Java-Systemen ist nahezu identisch. Der wichtigste Unterschied ist, dass ein SAP ABAP-System eine ASCS-Instanz hat. Das SAP Java-System hat eine SCS-Instanz. Das SAP ABAP+Java-System hat eine ASCS- und eine SCS-Instanz, die in der gleichen Microsoft-Failoverclustergruppe ausgeführt werden. Unterschiede bei der Installation der einzelnen SAP NetWeaver-Installationsstapel werden explizit angegeben. Sie können davon ausgehen, dass alle anderen Teile gleich sind.  
>
>

### <a name="install-sap-with-a-high-availability-ascsscs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> Installieren von SAP mit einer hoch verfügbaren ASCS/SCS-Instanz

> [!IMPORTANT]
> Achten Sie darauf, nicht die Auslagerungsdatei auf von DataKeeper gespiegelten Volumes zu platzieren. DataKeeper unterstützt keine gespiegelten Volumes. Belassen Sie die Auslagerungsdatei auf dem temporären Laufwerk D eines virtuellen Azure-Computers (dies ist auch die Standardeinstellung). Wenn sie sich noch nicht dort befindet, verschieben Sie die Windows-Auslagerungsdatei auf Laufwerk D: des virtuellen Azure-Computers.
>
>

Das Installieren von SAP mit einer ASCS/SCS-Instanz mit hoher Verfügbarkeit umfasst die folgenden Aufgaben:

- Erstellen eines virtuellen Hostnamens für die SAP ASCS/SCS-Clusterinstanz
- Installieren des ersten SAP-Clusterknotens
- Ändern des SAP-Profils der ASCS/SCS-Instanz
- Hinzufügen eines Testports
- Öffnen des Windows-Firewall-Testports

#### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> Erstellen eines virtuellen Hostnamens für die SAP ASCS/SCS-Clusterinstanz

1. Erstellen Sie im Windows-DNS-Manager einen DNS-Eintrag für den virtuellen Hostnamen der ASCS/SCS-Instanz.

   > [!IMPORTANT]
   > Die IP-Adresse, die Sie dem virtuellen Hostnamen der ASCS/SCS-Instanz zuweisen, muss mit der IP-Adresse identisch sein, die Sie dem Azure Load Balancer zugeordnet haben ( **<*SID*>-lb-ascs**).  
   >
   >

   Die IP-Adresse des virtuellen Hostnamens der SAP ASCS/SCS-Instanz (**pr1-ascs-sap**) ist mit der IP-Adresse des Azure Load Balancers (**pr1-lb-ascs**) identisch.

   ![Abbildung 56: Festlegen des DNS-Eintrags für den virtuellen SAP ASCS/SCS-Clusternamen und der TCP/IP-Adresse][sap-ha-guide-figure-3046]

   _**Abbildung 56:** Festlegen des DNS-Eintrags für den virtuellen SAP ASCS/SCS-Clusternamen und der TCP/IP-Adresse_

2. Wählen Sie zum Definieren der IP-Adresse, die dem virtuellen Hostnamen zugewiesen ist, die Option **DNS-Manager** > **Domäne**.

   ![Abbildung 57: Neuer virtueller Name und TCP/IP-Adresse für die SAP ASCS/SCS-Clusterkonfiguration][sap-ha-guide-figure-3047]

   _**Abbildung 57:** Neuer virtueller Name und TCP/IP-Adresse für die SAP ASCS/SCS-Clusterkonfiguration_

#### <a name="install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> Installieren des ersten SAP-Clusterknotens

1. Führen Sie die Option für den ersten Clusterknoten auf Clusterknoten A aus, z.B. auf dem Host **pr1-ascs-0**.
2. Wählen Sie Folgendes aus, um für den internen Azure Load Balancer die Standardports beizubehalten:

   * **ABAP-System**: **ASCS**-Instanznummer **00**
   * **Java-System**: **SCS**-Instanznummer **01**
   * **ABAP + Java-System**: **ASCS**-Instanznummer **00** und **SCS**-Instanznummer **01**

   Um andere Instanznummern als 00 für eine ABAP ASCS-Instanz und 01 für eine Java SCS-Instanz zu verwenden, müssen Sie zunächst die Standardregeln für den Lastenausgleich durch den internen Azure-Lastenausgleich ändern. Dieser Vorgang wird in [Ändern der Standardregeln für den ASCS/SCS-Lastenausgleich durch den internen Azure-Lastenausgleich][sap-ha-guide-8.9] beschrieben.

Die nächsten Aufgaben sind in der SAP-Dokumentation für die Standardinstallation nicht beschrieben.

> [!NOTE]
> Die SAP-Installationsdokumentation beschreibt, wie der erste ASCS/SCS-Clusterknoten installiert wird.
>
>

#### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> Ändern des SAP-Profils der ASCS/SCS-Instanz

Sie müssen einen neuen Profilparameter hinzuzufügen. Dieser Profilparameter verhindert das Schließen von Verbindungen zwischen SAP-Workprozessen und dem Enqueue-Server, wenn diese sich zu lange im Leerlauf befinden. Dieses Problem wurde bereits unter [Hinzufügen von Registrierungseinträgen auf beiden Clusterknoten für eine SAP ASCS/SCS-Instanz][sap-ha-guide-8.11] erwähnt. In diesem Abschnitt haben wir auch zwei Änderungen an grundlegenden TCP/IP-Verbindungsparametern vorgenommen. In einem zweiten Schritt müssen Sie den Enqueue-Server für das Senden eines `keep_alive`-Signals konfigurieren, damit die Verbindungen nicht den Schwellenwert für den Leerlauf des internen Azure Load Balancers erreichen.

So ändern Sie das SAP-Profil der ASCS/SCS-Instanz

1. Fügen Sie diesen Profilparameter dem Profil der SAP ASCS/SCS-Instanz hinzu:

   ```
   enque/encni/set_so_keepalive = true
   ```
   Bei unserem Beispiel lautet der Pfad:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

   Beispiel für das Profil einer SAP SCS-Instanz und den zugehörigen Pfad:

   `<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

2. Starten Sie die SAP ASCS/SCS-Instanz neu, um die Änderungen zu übernehmen.

#### <a name="add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> Hinzufügen eines Testports

Nutzen Sie die Testfunktionalität des internen Lastenausgleichs, damit die gesamte Clusterkonfiguration mit Azure Load Balancer funktioniert. Der interne Azure Load Balancer verteilt in der Regel die eingehende Workload gleichmäßig auf die beteiligten virtuellen Computer. Allerdings funktioniert dies bei einigen Clusterkonfigurationen nicht, da nur eine Instanz aktiv ist. Die andere Instanz ist passiv und kann daher keine Workload annehmen. Eine Testfunktion ist hilfreich, wenn der interne Azure Load Balancer die Workload nur einer aktiven Instanz zuweist. Mit der Testfunktion kann der internen Load Balancer erkennen, welche Instanzen aktiv sind, und dann nur dieser Instanz Workload zuweisen.

So fügen Sie einen Testport hinzu

1. Überprüfen Sie die aktuelle **ProbePort**-Einstellung, indem Sie den folgenden PowerShell-Befehl ausführen. Führen Sie ihn auf einem virtuellen Computer in der Clusterkonfiguration aus.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter
   ```

2. Definieren Sie einen Testport. Die Standardnummer für den Testport ist **0**. In unserem Beispiel verwenden wir als Testport **62000**.

   ![Abbildung 58: Der Testport der Clusterkonfiguration ist standardmäßig 0.][sap-ha-guide-figure-3048]

   _**Abbildung 58:** Der Standardtestport der Clusterkonfiguration ist 0._

   Die Portnummer ist in Azure Resource Manager-Vorlagen für SAP definiert. Sie können die Portnummer in PowerShell zuweisen.

   Führen Sie das folgende PowerShell-Skript aus, um einen neuen ProbePort-Wert für die Clusterressource **SAP <*SID*> IP** festzulegen. Aktualisieren Sie die PowerShell-Variablen für Ihre Umgebung. Nach dem Ausführen des Skripts werden Sie aufgefordert, die SAP-Clustergruppe neu zu starten, um die Änderungen zu aktivieren.

   ```powershell
   $SAPSID = "PR1"      # SAP <SID>
   $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

   Clear-Host
   $SAPClusterRoleName = "SAP $SAPSID"
   $SAPIPresourceName = "SAP $SAPSID IP"
   $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
   $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
   $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
   $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
   $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
   $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
   $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value

   $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }

   Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter

   Write-Host
   Write-Host "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." -ForegroundColor Cyan
   Write-Host
   Write-Host "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." -ForegroundColor Cyan
   Write-Host

   $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}

   Write-Host

   $ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"

   if($ActivateChanges -eq "yes"){
   Write-Host
   Write-Host "Activating changes..." -ForegroundColor Cyan

   Write-Host
   write-host "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..." -ForegroundColor Cyan
   Stop-ClusterResource -Name $SAPIPresourceName
   sleep 5

   Write-Host "Starting SAP cluster role '$SAPClusterRoleName' ..." -ForegroundColor Cyan
   Start-ClusterGroup -Name $SAPClusterRoleName

   Write-Host "New ProbePort parameter is active." -ForegroundColor Green
   Write-Host

   Write-Host "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" -ForegroundColor Cyan
   Write-Host
   Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
   }else
   {
   Write-Host "Changes are not activated."
   }
   ```

   Überprüfen Sie, nachdem Sie die Clusterrolle **SAP <*SID*>** online geschaltet haben, ob **ProbePort** auf den neuen Wert festgelegt wurde.

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPNetworkIPClusterName = "SAP $SAPSID IP"
   Get-ClusterResource $SAPNetworkIPClusterName | Get-ClusterParameter

   ```

   ![Abbildung 59: Testen des Clusterports nach dem Festlegen des neuen Werts][sap-ha-guide-figure-3049]

   _**Abbildung 59:** Testen des Clusterports nach dem Festlegen des neuen Werts_

#### <a name="open-the-windows-firewall-probe-port"></a><a name="4498c707-86c0-4cde-9c69-058a7ab8c3ac"></a> Öffnen des Windows-Firewall-Testports

Sie müssen einen Windows-Firewall-Testport auf beiden Clusterknoten öffnen. Verwenden Sie das folgende Skript, um einen Windows-Firewall-Testport zu öffnen. Aktualisieren Sie die PowerShell-Variablen für Ihre Umgebung.

  ```powershell
  $ProbePort = 62000   # ProbePort of the Azure Internal Load Balancer

  New-NetFirewallRule -Name AzureProbePort -DisplayName "Rule for Azure Probe Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort $ProbePort
  ```

Die Einstellung **ProbePort** wurde auf **62000** festgelegt. Sie können nun auf die Dateifreigabe **\\\ascsha-clsap\sapmnt** von anderen Hosts zugreifen, z.B. **ascsha-dbas**.

### <a name="install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> Installieren der Datenbankinstanz

Führen Sie für die Installation der Datenbankinstanz die in der SAP-Installationsdokumentation beschriebene Vorgehensweise durch.

### <a name="install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> Installieren des zweiten Clusterknotens

Führen Sie zum Installieren des zweiten Clusterknotens die Schritte im SAP-Installationshandbuch aus.

### <a name="change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> Ändern des Starttyps der Windows-Dienstinstanz für SAP ERS

Ändern Sie den Starttyp der Windows-Dienste für SAP ERS auf beiden Clusterknoten in **Automatisch (Verzögerter Start)** .

![Abbildung 60: Ändern des Starttyps der SAP ERS-Instanz in „Automatisch (Verzögerter Start)“][sap-ha-guide-figure-3050]

_**Abbildung 60:** Ändern des Starttyps der SAP ERS-Instanz in „Automatisch (Verzögerter Start)“_

### <a name="install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> Installieren des primären SAP-Anwendungsservers

Installieren Sie die PAS-Instanz (primärer Anwendungsserver) <*SID*>-di-0 auf dem virtuellen Computer, den Sie als Host für den PAS vorgesehen haben. Es bestehen keine Abhängigkeiten mit spezifischen Einstellungen für Azure oder DataKeeper.

### <a name="install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> Installieren des zusätzlichen SAP-Anwendungsservers

Installieren Sie einen zusätzlichen SAP-Anwendungsserver (Additional Application Server, AAS) auf allen virtuellen Computern, die Sie als Hosts von SAP-Anwendungsserverinstanzen festgelegt haben. Beispielsweise auf <*SID*>-di-1 bis <*SID*>-di-&lt;n&gt;.

> [!NOTE]
> Die Installation eines SAP NetWeaver-Systems mit hoher Verfügbarkeit ist hiermit abgeschlossen. Als Nächstes stehen die Failovertests an.
>


## <a name="test-the-sap-ascsscs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> Testen des Failovers der SAP ASCS/SCS-Instanz und der SIOS-Replikation
Mit dem Failovercluster-Manager und dem SIOS DataKeeper-Tool für die Verwaltung und Konfiguration können Sie das Failover für eine SAP ASCS/SCS-Instanz und die SIOS-Datenträgerreplikation ganz einfach testen und überwachen.

### <a name="sap-ascsscs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> Die SAP ASCS/SCS-Instanz wird auf Clusterknoten A ausgeführt

Die Clustergruppe **SAP PR1** wird auf dem Clusterknoten A ausgeführt, z.B. auf **pr1-ascs-0**. Weisen Sie den freigegebenen Datenträger S, der Teil der Clustergruppe **SAP PR1** ist und von der ASCS/SCS-Instanz verwendet wird, dem Clusterknoten A zu.

![Abbildung 61: Failovercluster-Manager: Die SAP-Clustergruppe <SID> wird auf Clusterknoten A ausgeführt.][sap-ha-guide-figure-5000]

_**Abbildung 61:** Failovercluster-Manager: Die SAP-Clustergruppe <*SID*> wird auf Clusterknoten A ausgeführt._

Im SIOS DataKeeper-Tool für die Verwaltung und Konfiguration erkennen Sie, dass die Daten auf dem freigegebenen Datenträger synchron vom Quellvolume S auf Clusterknoten A auf das Zielvolume S auf Clusterknoten B repliziert werden, also z.B. von **pr1-ascs-0 [10.0.0.40]** nach **pr1-ascs-1 [10.0.0.41]** .

![Abbildung 62: Replizieren des lokalen Volumes von Clusterknoten A auf Clusterknoten B in SIOS DataKeeper][sap-ha-guide-figure-5001]

_**Abbildung 62:** Replizieren des lokalen Volumes von Clusterknoten A auf Clusterknoten B in SIOS DataKeeper_

### <a name="failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> Failover von Knoten A auf Knoten B

1. Wählen Sie eine dieser Optionen, um ein Failover der SAP-Clustergruppe <*SID*> von Clusterknoten A auf Clusterknoten B auszulösen:
   - Mithilfe des Failovercluster-Managers  
   - Mithilfe der Failovercluster-PowerShell

   ```powershell
   $SAPSID = "PR1"     # SAP <SID>

   $SAPClusterGroup = "SAP $SAPSID"
   Move-ClusterGroup -Name $SAPClusterGroup

   ```
2. Durch einen Neustart von Clusterknoten A im Windows-Gastbetriebssystem (dadurch wird ein automatisches Failover der SAP-Clustergruppe <*SID*> von Knoten A auf Knoten B ausgelöst).  
3. Durch einen Neustart von Clusterknoten A im Azure-Portal (dadurch wird ein automatisches Failover der SAP-Clustergruppe <*SID*> von Knoten A auf Knoten B ausgelöst).  
4. Durch einen Neustart von Clusterknoten A mit Azure PowerShell (dadurch wird ein automatisches Failover der SAP-Clustergruppe <*SID*> von Knoten A auf Knoten B ausgelöst).

   Nach dem Failover wird die SAP-Clustergruppe <*SID*> auf dem Clusterknoten B ausgeführt, z.B. auf **pr1-ascs-1**.

   ![Abbildung 63: Failovercluster-Manager: Die SAP-Clustergruppe <SID> wird auf Clusterknoten B ausgeführt.][sap-ha-guide-figure-5002]

   _**Abbildung 63**: Failovercluster-Manager: Die SAP-Clustergruppe <*SID*> wird auf Clusterknoten B ausgeführt._

   Der freigegebene Datenträger wird jetzt auf Clusterknoten B bereitgestellt. SIOS DataKeeper repliziert Daten vom Quellvolume S auf Clusterknoten B auf das Zielvolume S auf Clusterknoten A, also z.B. von **pr1-ascs-1 [10.0.0.41]** nach **pr1-ascs-0 [10.0.0.40]** .

   ![Abbildung 64: SIOS DataKeeper repliziert das lokale Volume von Clusterknoten B auf Clusterknoten A.][sap-ha-guide-figure-5003]

   _**Abbildung 64:** SIOS DataKeeper repliziert das lokale Volume von Clusterknoten B auf Clusterknoten A._
