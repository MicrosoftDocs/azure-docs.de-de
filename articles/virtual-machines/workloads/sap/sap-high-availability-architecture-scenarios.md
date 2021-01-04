---
title: Architektur und Szenarien mit Azure-VMs für die Hochverfügbarkeit von SAP NetWeaver | Microsoft-Dokumentation
description: Architektur und Szenarien für die Hochverfügbarkeit von SAP NetWeaver auf Azure Virtual Machines
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/26/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b409254abbde1c1f6156052c49a07e6cc09a4dfd
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94958777"
---
# <a name="high-availability-architecture-and-scenarios-for-sap-netweaver"></a>Architektur und Szenarien für die Hochverfügbarkeit von SAP NetWeaver

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


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
[sap-ascs-ha-multi-sid-wsfc-file-share]:sap-ascs-ha-multi-sid-wsfc-file-share.md
[sap-ascs-ha-multi-sid-wsfc-shared-disk]:sap-ascs-ha-multi-sid-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-suse-ascs-ha-anf]:high-availability-guide-suse-netapp-files.md
[sap-higher-availability]:sap-higher-availability-architecture-scenarios.md

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92

[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:../../windows/sql/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md

[sap-ha-bc-virtual-env-hyperv-vmware-white-paper]:https://scn.sap.com/docs/DOC-44415
[sap-ha-partner-information]:https://scn.sap.com/docs/DOC-8541
[azure-sla]:https://azure.microsoft.com/support/legal/sla/
[azure-virtual-machines-manage-availability]:../../windows/manage-availability.md
[azure-storage-redundancy]:https://azure.microsoft.com/documentation/articles/storage-redundancy/
[azure-storage-managed-disks-overview]:https://docs.microsoft.com/azure/storage/storage-managed-disks-overview

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/planning-monitoring-overview-2502.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-2901]:media/virtual-machines-shared-sap-planning-guide/2901-azure-ha-sap-ha-md.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-3201]:media/virtual-machines-shared-sap-planning-guide/3201-sap-ha-with-sql-md.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

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

[virtual-machines-manage-availability]:../../manage-availability.md


## <a name="terminology-definitions"></a>Terminologiedefinitionen

**Hochverfügbarkeit**: Bezieht sich auf eine Reihe von Technologien, die IT-Störungen minimieren, indem sie die Geschäftskontinuität von IT-Diensten durch redundante, fehlertolerante oder durch Failover geschützte Komponenten innerhalb *desselben* Rechenzentrums bereitstellen. In unserem Fall befindet sich das Rechenzentrum in einer Azure-Region.

**Notfallwiederherstellung**: Bezieht sich ebenfalls auf die Minimierung der Unterbrechung und Wiederherstellung von IT-Diensten, jedoch über *verschiedene* Rechenzentren, die möglicherweise Hunderte Kilometer voneinander entfernt liegen. In unserem Fall können die Rechenzentren in verschiedenen Azure-Regionen innerhalb derselben geopolitischen Region oder an Standorten liegen, die von Ihnen als Kunde eingerichtet wurden.


## <a name="overview-of-high-availability"></a>Übersicht über Hochverfügbarkeit
Hochverfügbarkeit von SAP in Azure kann in drei Typen unterteilt werden:

* **Hochverfügbarkeit der Azure-Infrastruktur:** 

    Hochverfügbarkeit kann beispielsweise Compute (VMs), Netzwerk und Speicher und die damit verbundenen Vorteile zur Verbesserung der SAP-Anwendungsverfügbarkeit umfassen.

* **Verwenden der Neustartfunktion des virtuellen Computers der Azure-Infrastruktur für eine *höhere Verfügbarkeit* von SAP-Anwendungen:** 

    Wenn Sie keine Funktionen wie Windows Server-Failoverclustering (WSFC) oder Pacemaker unter Linux verwenden möchten, wird die Azure-VM neu gestartet. Auf diese Weise werden SAP-Systeme vor geplanten und ungeplanten Ausfallzeiten der physischen Azure-Serverinfrastruktur und der insgesamt zugrunde liegenden Azure-Plattform geschützt.

* **Hochverfügbarkeit von SAP-Anwendungen:** 

    Für eine vollständige Hochverfügbarkeit von SAP-Systemen müssen Sie alle kritischen SAP-Systemkomponenten schützen. Beispiel:
    * Redundante SAP-Anwendungsserver
    * Einmalig auftretende Komponenten. Beispiel: eine Single Point of Failure-Komponente (SPOF) wie z.B. eine SAP ASCS/SCS-Instanz oder ein Datenbank-Managementsystem (DBMS).

Die Hochverfügbarkeit von SAP in Azure unterscheidet sich von der Hochverfügbarkeit von SAP in einer lokalen physischen oder virtuellen Umgebung. Das Dokument [Hochverfügbarkeit und Geschäftskontinuität von SAP NetWeaver in virtuellen Umgebungen mit VMware und Hyper-V unter Microsoft Windows][sap-ha-bc-virtual-env-hyperv-vmware-white-paper] beschreibt die Standardkonfigurationen für die Hochverfügbarkeit von SAP in virtualisierten Umgebungen unter Windows.

Es gibt keine in SAPinst integrierte Hochverfügbarkeitskonfiguration von SAP für Linux, wie dies für Windows der Fall ist. Weitere Informationen zur lokalen Hochverfügbarkeit von SAP unter Linux finden Sie unter [Partnerinformationen zur Hochverfügbarkeit][sap-ha-partner-information].

## <a name="azure-infrastructure-high-availability"></a>Hochverfügbarkeit der Azure-Infrastruktur

### <a name="sla-for-single-instance-virtual-machines"></a>SLA für virtuelle Einzelinstanzcomputer

Aktuell gibt es eine SLA mit 99,9% für einen einzelnen virtuellen Computer mit Premium-Speicher. Sie können das Produkt aus den verschiedenen verfügbaren [Azure-Vereinbarungen zum Servicelevel][azure-sla] erstellen, um einen Eindruck von der Hochverfügbarkeit eines einzelnen virtuellen Computers zu erhalten.

Als Grundlage für die Berechnung werden 30 Tage pro Monat bzw. 43.200 Minuten verwendet. Eine Ausfallzeit von 0,05 % entspricht beispielsweise 21,6 Minuten. Wie üblich wird die Verfügbarkeit der verschiedenen Dienste auf folgende Weise berechnet:

(Verfügbarkeitsdienst Nr. 1/100) × (Verfügbarkeitsdienst Nr. 2/100) × (Verfügbarkeitsdienst Nr. 3/100) \*…

Beispiel:

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 bzw. eine allgemeine Verfügbarkeit von 99,75 Prozent.

### <a name="multiple-instances-of-virtual-machines-in-the-same-availability-set"></a>Mehrere Instanzen von virtuellen Computern in derselben Verfügbarkeitsgruppe
Für alle virtuellen Computer, die über mindestens zwei bereitgestellte Instanzen in derselben *Verfügbarkeitsgruppe* verfügen, garantieren wir die Verfügbarkeit der VM-Verbindung mit mindestens einer Instanz zu mindestens 99,95 % der Zeit.

Wenn mehrere virtuelle Computer Teil derselben Verfügbarkeitsgruppe sind, wird jeder virtuelle Computer in einer Verfügbarkeitsgruppe durch die zugrunde liegende Azure-Plattform einer *Updatedomäne* und einer *Fehlerdomäne* zugewiesen.

* Durch **Updatedomänen** wird sichergestellt, dass nicht mehrere virtuelle Computer gleichzeitig während einer geplanten Wartung der Azure-Infrastruktur neu gestartet werden. Es wird immer nur jeweils ein virtueller Computer neu gestartet.

* Durch **Fehlerdomänen** wird sichergestellt, dass virtuelle Computer auf Hardwarekomponenten bereitgestellt werden, die keine gemeinsame Stromquelle und keinen gemeinsamen Netzwerkswitch verwenden. Bei ungeplanten Ausfallzeiten des Servers, des Netzwerkswitches oder der Stromquelle ist nur ein virtueller Computer betroffen.

Weitere Informationen finden Sie unter [Verwalten der Verfügbarkeit virtueller Windows-Computer in Azure][azure-virtual-machines-manage-availability].

Verfügbarkeitsgruppen werden zum Erreichen der Hochverfügbarkeit von Folgendem verwendet:

* Redundante SAP-Anwendungsserver  
* Cluster mit mehreren Knoten (z.B. virtuelle Computer), die SPOFs wie eine SAP ASCS/SCS-Instanz oder ein DBMS schützen


### <a name="azure-availability-zones"></a>Azure-Verfügbarkeitszonen
Bei Azure wird derzeit das Konzept von [Azure-Verfügbarkeitszonen](../../../availability-zones/az-overview.md) in verschiedenen [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/regions/) umgesetzt. In Azure-Regionen, in denen Verfügbarkeitszonen angeboten werden, umfassen die Azure-Regionen mehrere Rechenzentren, die von der Stromversorgung, der Kühlung und dem Netzwerk unabhängig sind. Es werden unterschiedliche Zonen in einer einzelnen Azure-Region angeboten, um Ihnen die Bereitstellung von Anwendungen über zwei oder drei Verfügbarkeitszonen hinweg zu ermöglichen. Unter der Annahme, dass sich Probleme an Stromversorgungsquellen und/oder Netzwerken nur auf die Infrastruktur einer Verfügbarkeitszone auswirkt, ist Ihre Anwendungsbereitstellung innerhalb einer Azure-Region weiterhin voll funktionsfähig. Dies kann schließlich zu einer reduzierten Kapazität führen, da einige VMs in einer Zone verloren gehen könnten. VMs in den anderen beiden Zonen werden jedoch weiterhin ausgeführt. Die Azure-Regionen, die Zonen anbieten, sind unter [Azure-Verfügbarkeitszonen](../../../availability-zones/az-overview.md) aufgelistet.

Für Verfügbarkeitszonen müssen einige Punkte beachtet werden. Darunter befinden sich Überlegungen wie die folgenden:

- Sie können Azure-Verfügbarkeitsgruppen nicht in einer Verfügbarkeitszone bereitstellen. Sie müssen sich für eine Verfügbarkeitszone oder eine Verfügbarkeitsgruppe als Bereitstellungsrahmen für einen virtuellen Computer entscheiden.
- Sie können den [Basic-Load Balancer](../../../load-balancer/load-balancer-overview.md) nicht zum Erstellen von Failoverclusterlösungen auf der Grundlage von Windows-Failoverclusterdiensten oder Linux Pacemaker verwenden. Stattdessen müssen Sie die [Standard-SKU von Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md) verwenden.
- Azure-Verfügbarkeitszonen bieten keine Garantie für eine bestimmte Entfernung zwischen den verschiedenen Zonen innerhalb einer Region.
- Die Netzwerklatenz zwischen unterschiedlichen Azure-Verfügbarkeitszonen in den verschiedenen Azure-Regionen kann sich zwischen Azure-Regionen unterscheiden. In einigen Fällen können Sie als Kunde die in verschiedenen Zonen bereitgestellte SAP-Anwendungsebene sinnvoll ausführen, da die Netzwerklatenz von einer Zone zur aktiven DBMS-VM bezüglich der Beeinträchtigung von Geschäftsprozessen akzeptabel ist. Es gibt jedoch auch Kundenszenarien, in denen die Latenz zwischen der aktiven DBMS-VM in einer Zone und einer SAP-Anwendungsinstanz auf einer VM in einer anderen Zone zu starke Beeinträchtigungen verursacht und für die SAP-Geschäftsprozesse nicht akzeptabel ist. Daher müssen sich die Bereitstellungsarchitekturen für eine Aktiv/Aktiv-Architektur für die Anwendung und eine Aktiv/Passiv-Architektur unterscheiden, wenn die Leselatenz zu hoch ist.
- Die Verwendung von [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) ist für die Bereitstellung in Azure-Verfügbarkeitszonen zwingend erforderlich. 


### <a name="planned-and-unplanned-maintenance-of-virtual-machines"></a>Geplante und ungeplante Wartung von virtuellen Computern

Zwei Typen von Azure-Plattformereignissen können die Verfügbarkeit Ihrer virtuellen Computer beeinflussen:

* **Geplante Wartungsereignisse** sind regelmäßige Updates, die Microsoft an der zugrunde liegende Azure-Plattform vornimmt. Die Updates verbessern die allgemeine Zuverlässigkeit, Leistung und Sicherheit der Plattforminfrastruktur, in der Ihre virtuellen Computer ausgeführt werden.

* **Ungeplante Wartungsereignisse** treten auf, wenn die Hardware oder physische Infrastruktur, die dem virtuellen Computer zugrunde liegt, einen Ausfall verursacht. Dies kann Ausfälle des lokalen Netzwerks oder des Datenträgers oder andere Fehler auf Rackebene umfassen. Wenn ein solcher Fehler festgestellt wird, migriert die Azure-Plattform Ihren virtuellen Computer automatisch vom fehlerhaften physischen Server, der den virtuellen Computer hostet, zu einem fehlerfreien physischen Server. Ereignisse dieser Art treten selten auf, verursachen jedoch eventuell eines Neustart des virtuellen Computers.

Weitere Informationen finden Sie unter [Verwalten der Verfügbarkeit virtueller Windows-Computer in Azure][azure-virtual-machines-manage-availability].

### <a name="azure-storage-redundancy"></a>Azure Storage-Redundanz
Die Daten in Ihrem Speicherkonto werden stets repliziert, um Dauerhaftigkeit und Hochverfügbarkeit sicherzustellen sowie um die Azure Storage-SLA auch bei vorübergehend auftretenden Hardwareausfällen zu erfüllen.

Da Azure Storage standardmäßig drei Images der Daten beibehält, ist die Verwendung von RAID 5 oder RAID 1 für mehrere Azure-Datenträger nicht erforderlich.

Weitere Informationen finden Sie unter [Azure Storage-Replikation][azure-storage-redundancy].

### <a name="azure-managed-disks"></a>Azure Managed Disks
Bei Managed Disks handelt es sich um einen Ressourcentyp in Azure Resource Manager, dessen Verwendung anstelle von in Azure Storage-Konten gespeicherten virtuellen Festplatten (VHDs) empfohlen wird. Verwaltete Datenträger richten sich automatisch an der Azure-Verfügbarkeitsgruppe des virtuellen Computers aus, mit dem sie verbunden sind. Sie erhöhen die Verfügbarkeit Ihres virtuellen Computers und der Dienste, die auf ihm ausgeführt werden.

Weitere Informationen finden Sie unter [Azure Managed Disks – Übersicht][azure-storage-managed-disks-overview].

Es wird empfohlen, verwaltete Datenträger zu verwenden, da diese die Bereitstellung und Verwaltung virtueller Computer vereinfachen.



## <a name="utilizing-azure-infrastructure-high-availability-to-achieve-higher-availability-of-sap-applications"></a>Verwenden der Hochverfügbarkeit des virtuellen Computers der Azure-Infrastruktur für eine *höhere Verfügbarkeit* von SAP-Anwendungen

Wenn Sie keine Funktionen wie WSFC oder Pacemaker unter Linux (derzeit nur für SUSE Linux Enterprise Server [SLES] 12 und höher unterstützt) verwenden möchten, wird ein Neustart der Azure-VM angewendet. Auf diese Weise werden SAP-Systeme vor geplanten und ungeplanten Ausfallzeiten der physischen Azure-Serverinfrastruktur und der insgesamt zugrunde liegenden Azure-Plattform geschützt.

Weitere Informationen zu diesem Ansatz finden Sie unter [Verwenden der Neustartfunktion von virtuellen Computern der Azure-Infrastruktur für eine höhere Verfügbarkeit des SAP-Systems][sap-higher-availability].

## <a name="high-availability-of-sap-applications-on-azure-iaas"></a><a name="baed0eb3-c662-4405-b114-24c10a62954e"></a> Hochverfügbarkeit von SAP-Anwendungen in Azure IaaS

Für eine vollständige Hochverfügbarkeit von SAP-Systemen müssen Sie alle kritischen SAP-Systemkomponenten schützen. Beispiel:
  * Redundante SAP-Anwendungsserver
  * Einmalig auftretende Komponenten. Beispiel: eine Single Point of Failure-Komponente (SPOF) wie z.B. eine SAP ASCS/SCS-Instanz oder ein Datenbank-Managementsystem (DBMS).

In den nächsten Abschnitten wird das Erreichen von Hochverfügbarkeit für alle drei kritischen SAP-Systemkomponenten erläutert.

### <a name="high-availability-architecture-for-sap-application-servers"></a>Hochverfügbarkeitsarchitektur für SAP-Anwendungsserver

> Dieser Abschnitt gilt für:
>
> ![Windows-Logo][Logo_Windows] Windows und ![Linux-Logo][Logo_Linux] Linux
>

Für die SAP-Anwendungsserver und -Dialoginstanzen ist meist keine spezielle Lösung für Hochverfügbarkeit erforderlich. Sie erreichen Hochverfügbarkeit durch Redundanz und durch das Konfigurieren von mehreren Dialoginstanzen in verschiedenen Instanzen von virtuellen Azure-Computern. Es müssen mindestens zwei SAP-Anwendungsinstanzen auf zwei Instanzen von virtuellen Azure-Computern installiert sein.

![Abbildung 1: Hochverfügbarkeit für SAP-Anwendungsserver][sap-ha-guide-figure-2000]

_**Abbildung 1:** Hochverfügbarkeit für SAP-Anwendungsserver_

Alle virtuellen Computer, auf denen SAP-Anwendungsserverinstanzen gehostet werden, müssen in derselben Azure-Verfügbarkeitsgruppe platziert werden. Eine Azure-Verfügbarkeitsgruppe stellt Folgendes sicher:

* Alle virtuellen Computer sind nicht Teil derselben Updatedomäne.  
    Eine Updatedomäne stellt sicher, dass die virtuellen Computer nicht gleichzeitig mit geplanten Wartungsausfallzeiten aktualisiert werden.

    Die grundlegende Funktionalität, die auf unterschiedlichen Update- und Fehlerdomänen innerhalb einer Azure-Skalierungseinheit basiert, wurde bereits im Kapitel [Updatedomänen][planning-guide-3.2.2] vorgestellt.

* Alle virtuellen Computer sind nicht Teil derselben Fehlerdomäne.  
    Eine Fehlerdomäne stellt bei der Bereitstellung virtueller Computer sicher, dass kein Single Point of Failure Auswirkungen auf die Verfügbarkeit aller virtuellen Computer hat.

Die Anzahl der von einer Azure-Verfügbarkeitsgruppe innerhalb einer Azure-Skalierungseinheit verwendbaren Update- und Fehlerdomänen ist begrenzt. Wenn Sie einer einzelnen Verfügbarkeitsgruppe immer weiter virtuelle Computer hinzufügen, werden schließlich mindestens zwei virtuelle Computer in dieselbe Fehler- oder Updatedomäne aufgenommen.

Wenn Sie mehrere SAP-Anwendungsserverinstanzen auf eigenen dedizierten virtuellen Computern bereitstellen, entsteht bei fünf Updatedomänen das folgende Bild. Die aktuelle maximale Anzahl von Update- und Fehlerdomänen innerhalb einer Verfügbarkeitsgruppe kann sich zukünftig ändern:

![Abbildung 2: Hochverfügbarkeit von SAP-Anwendungsservern in einer Azure-Verfügbarkeitsgruppe][planning-guide-figure-3000]
 _**Abbildung 2:** Hochverfügbarkeit von SAP-Anwendungsservern in einer Azure-Verfügbarkeitsgruppe_

Weitere Informationen finden Sie unter [Verwalten der Verfügbarkeit virtueller Windows-Computer in Azure][azure-virtual-machines-manage-availability].

Weitere Informationen finden Sie im Abschnitt [Azure-Verfügbarkeitsgruppen][planning-guide-3.2.3] des Dokuments zur Planung und Implementierung von virtuellen Azure-Computern für SAP NetWeaver.

**Nur nicht verwaltete Datenträger:** Da das Azure-Speicherkonto ein potenzieller Single Point of Failure sein kann, benötigen Sie unbedingt mindestens zwei Azure-Speicherkonten, auf die mindestens zwei virtuelle Computer verteilt sind. In einer idealen Konfiguration würden die Datenträger jedes virtuellen Computers, auf dem eine SAP-Dialoginstanz ausgeführt wird, in einem anderen Speicherkonto bereitgestellt werden.

> [!IMPORTANT]
> Es wird dringend empfohlen, Azure Managed Disks für Ihre SAP-Hochverfügbarkeitsinstallationen zu verwenden. Da sich verwaltete Datenträger automatisch an der Verfügbarkeitsgruppe des virtuellen Computers ausrichten, mit der sie verbunden sind, erhöhen sie die Verfügbarkeit Ihres virtuellen Computers und der auf dem virtuellen Computer ausgeführten Dienste.  
>

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-windows"></a>Hochverfügbarkeitsarchitektur für eine SAP ASCS/SCS-Instanz unter Windows

> ![Windows-Logo][Logo_Windows] Windows
>

Sie können eine WSFC-Lösung zum Schützen der SAP ASCS/SCS-Instanz verwenden. Die Lösung weist zwei Varianten auf:

* **Gruppieren der SAP ASCS/SCS-Instanz mithilfe freigegebener Datenträger für Cluster:** Weitere Informationen zu dieser Architektur finden Sie unter [Gruppieren einer SAP ASCS/SCS-Instanz in einem Windows-Failovercluster mithilfe freigegebener Clusterdatenträger in Azure][sap-high-availability-guide-wsfc-shared-disk].   

* **Gruppieren der SAP ASCS/SCS-Instanz mithilfe einer Dateifreigabe:** Weitere Informationen zu dieser Architektur finden Sie unter [Gruppieren einer SAP ASCS/SCS-Instanz in einem Windows-Failovercluster per Dateifreigabe in Azure][sap-high-availability-guide-wsfc-file-share].

* **Gruppieren der SAP ASCS/SCS-Instanz mithilfe einer ANF-SMB-Freigabe:** Weitere Informationen zu dieser Architektur finden Sie unter [Gruppieren einer SAP ASCS/SCS-Instanz in einem Windows-Failovercluster über eine ANF-SMB-Freigabe](./high-availability-guide-windows-netapp-files-smb.md).

### <a name="high-availability-architecture-for-an-sap-ascsscs-instance-on-linux"></a>Hochverfügbarkeitsarchitektur für eine SAP ASCS/SCS-Instanz unter Linux

> ![Linux-Logo][Logo_Linux] Linux
> 
> Weitere Informationen zum Clustering der SAP ASCS/SCS-Instanz mithilfe des SLES-Clusterframeworks finden Sie unter [Hochverfügbarkeit für SAP NetWeaver auf Azure-VMs auf dem SUSE Linux Enterprise Server for SAP Applications][sap-suse-ascs-ha]. Informationen zu einer alternativen Hochverfügbarkeitsarchitektur unter SLES, für die kein hochverfügbares NFS erforderlich ist, finden Sie unter [Hochverfügbarkeit für SAP NetWeaver auf Azure-VMs unter SUSE Linux Enterprise Server mit Azure NetApp Files für SAP-Anwendungen][sap-suse-ascs-ha-anf].

Weitere Informationen zum Clustering der SAP ASCS/SCS-Instanz mithilfe des Red Hat-Clusterframeworks finden Sie unter [Hochverfügbarkeit von Azure Virtual Machines für SAP NetWeaver unter Red Hat Enterprise Linux](./high-availability-guide-rhel.md).


### <a name="sap-netweaver-multi-sid-configuration-for-a-clustered-sap-ascsscs-instance"></a>SAP NetWeaver-Multi-SID-Konfiguration für SAP ASCS/SCS-Clusterinstanzen

> ![Windows-Logo][Logo_Windows] Windows
> 
> Multi-SID wird mit WSFC über Dateifreigaben und freigegebene Datenträger unterstützt.
> 
> Weitere Informationen zur Multi-SID-Hochverfügbarkeitsarchitektur unter Windows finden Sie unter:

* [Hochverfügbarkeit von SAP ASCS/SCS-Instanzen mit Multi-SID-Konfiguration mithilfe von Windows Server-Failoverclustering und Dateifreigaben in Azure][sap-ascs-ha-multi-sid-wsfc-file-share]

* [Multi-SID-Hochverfügbarkeit für SAP ASCS/SCS-Instanzen unter Verwendung von Windows Server-Failoverclustering und freigegebene Datenträger in Azure][sap-ascs-ha-multi-sid-wsfc-shared-disk]

> ![Linux-Logo][Logo_Linux] Linux
> 
> Multi-SID-Clustering wird auf Linux-Pacemaker-Clustern für SAP ASCS/ERS unterstützt und ist auf **fünf** SAP-SIDs im selben Cluster beschränkt.
> Weitere Informationen zur Multi-SID-Hochverfügbarkeitsarchitektur unter Linux finden Sie unter:

* [Leitfaden zur Multi-SID-Hochverfügbarkeit für SAP NetWeaver auf virtuellen Azure-Computern unter SUSE Linux Enterprise Server für SAP-Anwendungen](./high-availability-guide-suse-multi-sid.md)
* [Hochverfügbarkeit für SAP NetWeaver auf virtuellen Azure-Computern unter Red Hat Enterprise Linux für SAP-Anwendungen: Multi-SID-Leitfaden](./high-availability-guide-rhel-multi-sid.md)

### <a name="high-availability-dbms-instance"></a>Hochverfügbarkeit für DBMS-Instanzen

Das DBMS ist auch eine zentrale Kontaktstelle in einem SAP-System. Sie sollten es mit einer Lösung mit hoher Verfügbarkeit schützen. Die folgende Abbildung zeigt eine SQL Server Always On-Lösung mit Hochverfügbarkeit in Azure unter Verwendung von Windows Server-Failoverclustering und des internen Azure-Lastenausgleichs. SQL Server Always On repliziert DBMS-Daten- und -Protokolldateien mithilfe einer eigenen DBMS-Replikation. In diesem Fall benötigen Sie keinen freigegebenen Clusterdatenträger, sodass die gesamte Einrichtung vereinfacht wird.

![Abbildung 3: Beispiel für ein hoch verfügbares SAP-DBMS mit SQL Server Always On][sap-ha-guide-figure-2003]

_**Abbildung 3:** Beispiel für ein hoch verfügbares SAP-DBMS mit SQL Server Always On_

Weitere Informationen zum Clustering des SQL Server-DBMS in Azure mithilfe des Azure Resource Manager-Bereitstellungsmodells finden Sie in den folgenden Artikeln:

* [Manuelles Konfigurieren einer AlwaysOn-Verfügbarkeitsgruppe in Azure Virtual Machines mit Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]

* [Konfigurieren eines internen Azure-Lastenausgleichs für eine AlwaysOn-Verfügbarkeitsgruppe in Azure][virtual-machines-windows-portal-sql-alwayson-int-listener]

Weitere Informationen zum Clustering des SAP HANA-DBMS in Azure mithilfe des Azure Resource Manager-Bereitstellungsmodells finden Sie unter [Hochverfügbarkeit von SAP HANA auf virtuellen Azure-Computern (VMs)][sap-hana-ha].
