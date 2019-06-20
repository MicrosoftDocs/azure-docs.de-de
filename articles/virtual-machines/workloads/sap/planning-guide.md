---
title: Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver | Microsoft-Dokumentation
description: Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: sedusch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2ddcf1f38d3d92f9d9bdd12203ebf99f20600478
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65409782"
---
# <a name="azure-virtual-machines-planning-and-implementation-for-sap-netweaver"></a>Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]: https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]: https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]: https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]: https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]: https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]: https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../../../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../../../azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-Azvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md  
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f
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

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/install-az-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-windows.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-windows-capture-image-resource-manager]:../../windows/capture-image.md
[virtual-machines-windows-capture-image]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-windows-capture-image-prepare-the-vm-for-image-capture]:../../virtual-machines-windows-generalize-vhd.md
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-create-upload-vhd-oracle]:../../linux/oracle-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics-windows]:../../windows/multiple-nics.md
[virtual-networks-multiple-nics-linux]:../../linux/multiple-nics.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-about-vpngateways.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md
[capture-image-linux-step-2-create-vm-image]:../../linux/capture-image.md#step-2-create-vm-image

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Microsoft Azure ermöglicht Unternehmen das Abrufen von Server- und Speicherressourcen in kürzester Zeit ohne langwierige Beschaffungszyklen. Der Dienst Azure Virtual Machines ermöglicht Unternehmen, klassische Anwendungen wie SAP NetWeaver-basierte Anwendungen in Azure bereitzustellen und deren Zuverlässigkeit und Verfügbarkeit zu erweitern, ohne dass hierfür weitere Ressourcen lokal verfügbar sein müssen. Microsoft Azure Virtual Machines-Dienste unterstützen auch standortübergreifende Konnektivität, wodurch Unternehmen virtuelle Azure-Computer aktiv in ihren lokalen Domänen, privaten Clouds und ihrer SAP-Systemlandschaft integrieren können.
Dieses Whitepaper beschreibt die Grundlagen von Microsoft Azure Virtual Machines und bietet eine schrittweise Anleitung für die Planungs- und Implementierungsaspekte für SAP NetWeaver-Installationen in Azure. Daher sollten Sie dieses Dokument lesen, bevor Sie die eigentlichen Bereitstellungen von SAP NetWeaver in Azure beginnen.
Das Dokument ergänzt die SAP-Installationsdokumentation und die SAP-Hinweise, die die primäre Ressource für Installationen und Bereitstellungen von SAP-Software auf verschiedenen Plattformen darstellen.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="summary"></a>Zusammenfassung
Cloud Computing ist ein häufig verwendeter Begriff, der in der IT-Branche mehr und mehr an Bedeutung gewinnt – in kleinen Unternehmen ebenso wie in großen und multinationalen Konzernen.

Microsoft Azure ist die Plattform für Clouddienste von Microsoft, die ein umfangreiches Spektrum an neuen Möglichkeiten bietet. Nun können Kunden Anwendungen schnell als Dienste in der Cloud bereitstellen bzw. die Bereitstellung aufheben und sind nicht mehr durch technische oder budgetbezogene Aspekte eingeschränkt. Anstatt Zeit und Geld in die Hardwareinfrastruktur zu investieren, können sich Unternehmen auf die Anwendung, Geschäftsprozesse und ihre Vorteile für Kunden und Benutzer konzentrieren.

Mit den Microsoft Azure Virtual Machines-Diensten bietet Microsoft eine umfassende IaaS-Plattform (Infrastructure-as-a-Service). Auf SAP NetWeaver basierende Anwendungen werden auf Azure Virtual Machines (IaaS) unterstützt. Dieses Whitepaper beschreibt, wie auf SAP NetWeaver basierende Anwendungen in Microsoft Azure als Plattform der Wahl geplant und implementiert werden.

Das Dokument selbst konzentriert sich auf zwei Hauptaspekte:

* Im erste Teil werden zwei unterstützte Bereitstellungsmuster für SAP NetWeaver-basierte Anwendungen in Azure beschrieben. Es wird auch die allgemeine Handhabung von Azure bei SAP-Bereitstellungen beschrieben.
* Der zweite Teil enthält ausführliche Informationen zum Implementieren der zwei unterschiedlichen Szenarios, die im ersten Teil beschrieben werden.

Zusätzliche Ressourcen finden Sie im Kapitel [Ressourcen][planning-guide-1.2] in diesem Dokument.

### <a name="definitions-upfront"></a>Definitionen
Im gesamten Dokument werden die folgenden Begriffe verwendet:

* IaaS: Infrastructure-as-a-Service
* PaaS: PaaS (Platform-as-a-Service)
* SaaS: Software-as-a-Service
* SAP-Komponente: Eine einzelne SAP-Anwendung wie ECC, BW, Solution Manager oder S/4HANA.  SAP-Komponenten können auf herkömmlichen ABAP- oder Java-Technologien oder auf einer Nicht-NetWeaver-basierten Anwendung wie Business Objects basieren.
* SAP-Umgebung: eine oder mehrere SAP-Komponenten, die logisch gruppiert sind, um eine Geschäftsfunktion wie Entwicklung, QAS, Schulung, DR oder Produktion auszuführen.
* SAP-Landschaft: Alle SAP-Ressourcen in der IT-Landschaft eines Kunden. Die SAP-Landschaft umfasst alle Produktions- und anderen Umgebungen.
* SAP-System: Die Kombination aus DBMS-Schicht und Anwendungsschicht in einem SAP ERP-Entwicklungssystem, einem SAP BW-Testsystem, einem SAP CRM-Produktionssystem usw. In Azure-Bereitstellungen wird die Aufteilung dieser beiden Ebenen zwischen lokalen Systemen und Azure nicht unterstützt. Das bedeutet, dass ein SAP-System entweder lokal oder in Azure bereitgestellt wird. Allerdings können Sie die verschiedenen Systeme einer SAP-Landschaft in Azure oder lokal bereitstellen. Sie konnte z.B. die SAP CRM-Entwicklungs- und Testsysteme in Azure und die SAP CRM-Produktionssysteme lokal bereitstellen.
* Standortübergreifend oder hybrid: Beschreibt ein Szenario, in dem virtuelle Computer mit einem Azure-Abonnement bereitgestellt werden, das Site-to-Site-, Multisite- oder ExpressRoute-Verbindungen zwischen den lokalen Rechenzentren und Azure umfasst. In der allgemeinen Azure-Dokumentation werden diese Arten von Bereitstellungen auch als standortübergreifende oder Hybridszenarien bezeichnet. Durch die Verbindung sollen lokale Domänen, das lokale Active Directory/OpenLDAP und lokales DNS auf Azure erweitert werden. Die lokale Landschaft wird auf die Azure-Ressourcen des Abonnements erweitert. Durch diese Erweiterung können die virtuellen Computer Teil der lokalen Domäne sein. Domänenbenutzer der lokalen Domäne können auf die Server zugreifen und Dienste auf diesen virtuellen Computern ausführen (z.B. DBMS-Dienste). Die Kommunikation und Namensauflösung zwischen lokal bereitgestellten virtuellen Computern und in Azure bereitgestellten virtuellen Computern ist möglich. Dies ist der am häufigsten verwendete und fast ausschließliche Fall, bei dem SAP-Assets in Azure bereitgestellt werden. In [diesem][vpn-gateway-cross-premises-options] und in [diesem Artikel][vpn-gateway-site-to-site-create] finden Sie weitere Informationen.

> [!NOTE]
> Standortübergreifende oder Hybridbereitstellungen von SAP-Systemen, in denen virtuelle Azure-Computer mit SAP-Systemen Mitglieder einer lokalen Domäne sind, werden für SAP-Produktionssysteme unterstützt. Standortübergreifende oder Hybridkonfigurationen werden für die Bereitstellung von Teilen von SAP-Landschaften oder vollständigen SAP-Landschaften in Azure unterstützt. Auch für das Ausführen von vollständigen SAP-Landschaften in Azure müssen diese virtuellen Computer Teil der lokalen Domäne und ADS/OpenLDAP sein. 
>
>



### <a name="e55d1e22-c2c8-460b-9897-64622a34fdff"></a>Ressourcen
Den Einstiegspunkt für die SAP-Workload in der Azure-Dokumentation finden Sie [hier](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started). Beginnend mit diesem Einstiegspunkt finden Sie zahlreiche Artikel, die folgende Themen behandeln:

- SAP NetWeaver und Business One in Azure
- SAP DBMS-Anleitungen für verschiedene DBMS-Systeme in Azure
- Hochverfügbarkeit und Notfallwiederherstellung für die SAP-Workload in Azure
- Spezielle Anleitungen zum Ausführen von SAP HANA in Azure
- Spezifische Anleitungen für Azure HANA (große Instanzen) für SAP HANA-DBMS 


> [!IMPORTANT]
> Nach Möglichkeit wird eine Verknüpfung mit den entsprechenden SAP-Installationshandbüchern oder anderer SAP-Dokumentation verwendet (Reference InstGuide-01, siehe <http://service.sap.com/instguides>). Wenn es um Voraussetzungen, den Installationsvorgang oder Details zu spezifischen SAP-Funktionen geht, sollten Sie die SAP-Dokumentation und -Anleitungen immer sorgfältig lesen, da die Microsoft-Dokumentation nur spezielle Aufgaben für SAP-Software abdeckt, die auf einem virtuellen Microsoft Azure-Computer installiert ist und betrieben wird.
>
>

Die folgenden SAP-Hinweise beziehen sich auf das Thema SAP in Azure:

| Hinweisnummer | Titel |
| --- | --- |
| [1928533] |SAP-Anwendungen auf Azure: Unterstützte Produkte und Größen |
| [2015553] |SAP auf Microsoft Azure: Voraussetzungen für die Unterstützung |
| [1999351] |Problembehandlung für die erweiterte Azure-Überwachung für SAP |
| [2178632] |Wichtige Überwachungsmetriken für SAP in Microsoft Azure |
| [1409604] |Virtualisierung unter Windows: Erweiterte Überwachung |
| [2191498] |SAP unter Linux mit Azure: Erweiterte Überwachung |
| [2243692] |Microsoft Azure (IaaS)-VM: SAP-Lizenzprobleme |
| [1984787] |SUSE LINUX Enterprise Server 12: Installationshinweise |
| [2002167] |Red Hat Enterprise Linux 7.x: Installation und Upgrade |
| [2069760] |Oracle Linux 7.x SAP: Installation und Upgrade |
| [1597355] |Empfehlung zu Auslagerungsbereichen für Linux |

Lesen Sie bitte auch das [SCN-Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes), das alle SAP-Hinweise für Linux enthält.

Allgemeine standardmäßige und maximale Einschränkungen von Azure-Abonnements finden Sie in [diesem Artikel][azure-subscription-service-limits-subscription].

## <a name="possible-scenarios"></a>Mögliche Szenarien
SAP wird in Unternehmen häufig als eine der Anwendungen angesehen, die am wichtigsten für den Erfolg sind. Die Architektur und Abläufe dieser Anwendungen sind in der Regel komplex, und es ist wichtig, dass Sie die Verfügbarkeits- und Leistungsanforderungen erfüllen.

Unternehmen müssen daher sorgfältig abwägen, welchen Cloudanbieter sie auswählen, bei dem diese unternehmenskritischen Geschäftsprozesse ausgeführt werden sollen. Azure ist die ideale öffentliche Cloudplattform für unternehmenskritische SAP-Anwendungen und Geschäftsprozesse. Angesichts der Vielfältigkeit der Azure-Infrastruktur können heute nahezu sämtliche vorhandene SAP NetWeaver -und S/4HANA-Systeme in Azure gehostet werden. Azure bietet VMs mit vielen Terabyte Arbeitsspeicher und mehr als 200 CPUs an. Darüber hinaus bietet Azure [HANA (große Instanzen)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) als Möglichkeit, HANA-Bereitstellungen horizontal auf bis zu 24 TB und ANA-Bereitstellungen horizontal auf bis zu 120 TB zu skalieren. 


Um SAP-Systeme erfolgreich in Azure IaaS oder IaaS bereitzustellen, müssen Sie die wichtigsten Unterschiede zwischen den Angeboten herkömmlicher Outsourcer oder Hoster und IaaS-Angeboten kennen. Während herkömmliche Hoster oder Outsourcer die Infrastruktur (Netzwerk-, Speicher- und Servertyp) an die Workload anpassen, die ein Kunde hosten möchte, liegt es stattdessen in der Verantwortung des Kunden, die richtigen Azure-Komponenten für VMs, Speicher und Netzwerk bei IaaS-Bereitstellungen auszuwählen.

Als ersten Schritt müssen Kunden die folgenden Punkte überprüfen:

* die von SAP unterstützten Typen virtueller Azure-Computer
* die von SAP unterstützten Produkte/Versionen in Azure
* die unterstützten Betriebssystem- und DBMS-Versionen für die entsprechenden SAP-Versionen in Azure
* den von verschiedenen Azure-SKUs bereitgestellten SAPS-Durchsatz

Die Antworten auf diese Fragen finden Sie im SAP-Hinweis [1928533].

Als zweiten Schritt müssen Azure-Ressourcen und Bandbreiteneinschränkungen mit dem tatsächlichen Verbrauch von lokalen Systemen verglichen werden. Daher müssen Kunden mit den verschiedenen Funktionen der Azure-Typen vertraut sein, die mit SAP in folgenden Bereichen unterstützt werden:

* CPU und Speicherressourcen von verschiedenen Typen virtueller Computer und
* IOPS-Bandbreite verschiedener Typen virtueller Computer und
* Netzwerkfunktionen der verschiedenen Typen virtueller Computer.

Die meisten dieser Daten finden Sie [hier (für Linux)][virtual-machines-sizes-linux] und [hier (für Windows)][virtual-machines-sizes-windows].

Denken Sie daran, dass die im oben stehenden Link aufgeführten Grenzwerte die Obergrenzen darstellen. Das bedeutet nicht, dass die Grenzwerte für die Ressourcen, z.B. IOPS, immer bereitgestellt werden können. Ausnahmen sind jedoch die CPU- und Speicherressourcen eines ausgewählten Typs virtueller Computer. Für die Typen virtueller Computer, die von SAP unterstützt werden, sind die CPU- und Speicherressourcen reserviert und stehen somit jederzeit zur Nutzung auf dem virtuellen Computer zur Verfügung.

Die Microsoft Azure-Plattform ist eine Plattform für mehrere Mandanten. Infolgedessen werden Speicher-, Netzwerk- und andere Ressourcen von Mandanten gemeinsam genutzt. Intelligente Drosselungs- und Kontingentlogik wird verwendet, um zu verhindern, dass ein Mandant die Leistung eines anderen Mandanten deutlich beeinträchtigt ("Noisy Neighbor"). Insbesondere bei der Zertifizierung der Azure-Plattform für SAP HANA muss Microsoft die Isolierung von Ressourcen für Fälle nachweisen, in denen mehrere VMs für SAP regelmäßig auf demselben Host ausgeführt werden können. Obwohl durch Logik in Azure versucht wird, Bandbreitenabweichungen klein zu halten, tendieren Plattformen mit umfangreicher gemeinsamer Nutzung zu größeren Abweichungen in der Ressourcen-/Bandbreitenverfügbarkeit, als die Kunden dies von ihren lokalen Bereitstellungen kennen. Die Wahrscheinlichkeit, dass ein SAP-System in Azure stärkere Schwankungen als ein lokales System aufweist, muss berücksichtigt werden.

Der letzte Schritt ist das Auswerten von Verfügbarkeitsanforderungen. Es kann vorkommen, dass die zugrunde liegenden Azure-Infrastruktur aktualisiert werden muss und dass die Hosts, auf denen virtuelle Computer ausgeführt werden, neu gestartet werden müssen. Microsoft dokumentiert die unterschiedlichen Fälle in [Wartung für VMs in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates). Für die seltenen Fälle, in denen virtuelle Computer zu einem Neustart gezwungen sind, jedoch vor allem für die Fälle, in denen Sie Komponenten des Gastbetriebssystems oder DBMS-Komponenten patchen müssen, ist es wichtig, für Ihre SAP-Produktionssysteme gültige Hochverfügbarkeitskonzepte zu entwickeln. Diese Anforderung unterscheidet sich nicht von den lokalen Anforderungen. Microsoft entwickelt die Azure-Plattform ständig weiter, um Ausfallzeiten aufgrund von Plattformänderungen zu verringern. 

Um erfolgreich ein SAP-System auf Azure bereitzustellen, müssen das lokale SAP-System, das Betriebssystem, Datenbank- und SAP-Anwendungen in der SAP-Azure-Support-Matrix angezeigt werden, in die Ressourcen passen, die die Azure-Infrastruktur bereitstellen kann und die mit den Verfügbarkeits-SLAs, die Microsoft Azure bietet, funktionieren können. Wenn diese Systeme identifiziert werden, müssen Sie sich für eines der folgenden zwei Bereitstellungsszenarios entscheiden.





### <a name="f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10"></a>Standortübergreifend: Bereitstellung einzelner oder mehrerer virtueller SAP-Computer in Azure, die vollständig in das lokale Netzwerk integriert sein muss
![VPN mit Site-to-Site-Konnektivität (standortübergreifende)][planning-guide-figure-300]

Dieses Szenario ist ein standortübergreifendes Szenario mit zahlreichen möglichen Bereitstellungsmustern. Es kann als das Ausführen einiger Teile der SAP-Landschaft lokal und anderer Teile der SAP-Landschaft in Azure beschrieben werden. Alle Aspekte der Tatsache, dass ein Teil der SAP-Komponenten in Azure ausgeführt wird, sollten für Endbenutzer ersichtlich sein. Daher funktionieren SAP Transport Correction System (STMS), RFC-Kommunikation, Drucken, Sicherheit (wie SSO) usw. problemlos für SAP-Systeme, die unter Windows Azure ausgeführt werden. Das standortübergreifende Szenario beschreibt aber auch ein Szenario, in dem die vollständige SAP-Landschaft in Azure ausgeführt wird, wobei die Domäne und der DNS-Dienst des Kunden in Azure erweitert werden.

> [!NOTE]
> Dies ist das Bereitstellungsszenario, das für die Ausführung produktiver SAP-Systeme unterstützt wird.
>
>

In [diesem Artikel][vpn-gateway-create-site-to-site-rm-powershell] erhalten Sie weitere Informationen zum Verbinden Ihres lokalen Netzwerks mit Microsoft Azure.

> [!IMPORTANT]
> Wenn wir über standortübergreifende Szenarios zwischen Azure- und lokalen Kundenbereitstellungen sprechen, geht es um die Granularität ganzer SAP-Systeme. Folgende Szenarios werden für standortübergreifende Szenarios *nicht unterstützt* :
>
> * Ausführen von unterschiedlichen SAP-Anwendungsebenen in unterschiedlichen Bereitstellungsmethoden. Beispielsweise die lokale Ausführung der DBMS-Ebene, während die SAP-Anwendungsschicht auf virtuellen Computern in Form von virtuellen Azure-Computern bereitgestellt wird (oder umgekehrt).
> * Einige Komponenten einer SAP-Ebene in Azure und einige lokal. Beispielsweise das Aufteilen der Instanzen der SAP-Anwendungsschicht auf lokale und virtuelle Azure-Computer
> * Die Verteilung der virtuellen Computer, auf denen SAP-Instanzen eines Systems ausgeführt werden, auf mehrere Azure-Regionen wird nicht unterstützt.
>
> Der Grund für diese Einschränkungen ist die Anforderung eines Hochleistungsnetzwerk mit einer niedrigen Latenz innerhalb eines SAP-Systems, insbesondere zwischen den Anwendungsinstanzen und der DBMS-Ebene eines SAP-Systems.
>
> Eine besondere Planung der Systeme und Regionen muss erfolgen, wenn mehrere hochintegrierte SAP-Systeme verwendet werden. Stellen Sie diese Systeme so nah wie möglich beieinander bereit, um Netzwerklatenzen zu minimieren. Einige Beispiele für hochintegrierte SAP-Systeme:
> * SAP BW zum Lesen von Daten aus SAP-OLTP-Systemen wie ERP-, CRM oder SRM
> * SAP SLT zum Replizieren von Daten zwischen mehreren SPA-Systemen oder sogar zwischen SAP- und Nicht-SAP-Systemen
> * SAP S/4 mit Verbindung mit einem SAP ERP-System usw.


### <a name="supported-os-and-database-releases"></a>Unterstützte Betriebssystem- und Datenbankversionen
* Die für Azure Virtual Machine-Dienste unterstützte Microsoft-Serversoftware wird im folgenden Artikel aufgeführt: <https://support.microsoft.com/kb/2721672>.
* Unterstützte Betriebssystemversionen und Datenbanksystemversionen, die von Diensten virtueller Azure-Computer zusammen mit SAP-Software unterstützt werden, sind im SAP-Hinweis [1928533]dokumentiert.
* SAP-Anwendungen und -Versionen, die von Diensten virtueller Azure-Computer unterstützt werden, sind im SAP-Hinweis [1928533]dokumentiert.
* Nur 64-Bit-Images werden für die Ausführung als virtuelle Gastcomputer in Azure für SAP-Szenarios unterstützt. Aufgrund dessen werden nur 64-Bit-SAP-Anwendungen und -Datenbanken unterstützt.

## <a name="microsoft-azure-virtual-machine-services"></a>Microsoft Azure Virtual Machine-Dienste
Die Microsoft Azure-Plattform ist eine internetweite Clouddiensteplattform, die in Microsoft-Rechenzentren gehostet und betrieben wird. Die Plattform umfasst die Microsoft Azure Virtual Machine-Dienste (Infrastructure as a Service oder IaaS) und eine Reihe von umfassenden PaaS-Funktionen (Platform as a Service)-Funktionen.

Die Azure-Plattform verringert die Notwendigkeit für den Vorabkauf von Technologie und Infrastruktur. Sie vereinfacht die Wartung und den Betrieb von Anwendungen durch die Bereitstellung von bedarfsgesteuerten Server- und Speicherressourcen zum Hosten, Skalieren und Verwalten von Webanwendungen und verbundenen Anwendungen. Die Infrastrukturverwaltung wird mit einer Plattform automatisiert, die für Hochverfügbarkeit und dynamische Skalierung konzipiert wurde, um den Nutzungsanforderungen mit der Option eines nutzungsbasierten Preismodells zu entsprechen.

![Positionieren von Microsoft Azure Virtual Machine-Dienste][planning-guide-figure-400]

Mit Azure Virtual Machine-Diensten ermöglicht Ihnen Microsoft das Bereitstellen benutzerdefinierter Images in Azure als IaaS-Instanzen (siehe Abbildung 4). Die virtuellen Computer in Azure basieren auf virtuellen Hyper-V-Festplatten (VHD) und können unterschiedliche Betriebssysteme als Gastbetriebssysteme ausführen.

Aus Betriebsperspektive bietet der Azure Virtual Machine-Dienst ähnliche Funktionen wie lokal bereitgestellte virtuellen Computer. Er hat jedoch den wichtigen Vorteil, dass Sie keine Infrastruktur beschaffen, verwalten und managen müssen. Entwickler und Administratoren haben Vollzugriff auf das Betriebssystemimage auf diesen virtuellen Computern. Administratoren können sich remote bei diesen virtuellen Computern anmelden, um Wartungs- und Problembehandlungsaufgaben sowie Softwarebereitstellungsaufgaben auszuführen. Hinsichtlich der Bereitstellung sind die einzigen Einschränkungen die Größen und Funktionen von virtuellen Azure-Computern. Diese Größen sind möglicherweise nicht so präzise konfigurierbar, wie es lokal möglich wäre. Es gibt eine Auswahl von virtuellen Computertypen, die eine Kombination aus Folgendem darstellen:

* Anzahl der vCPUs
* Arbeitsspeicher
* Anzahl der VHDs, die angefügt werden können
* Netzwerk- und Speicherbandbreiten

Die Größen und die Einschränkungen verschiedener angebotener virtueller Computer finden Sie in einer Tabelle in [diesem Artikel (für Linux)][virtual-machines-sizes-linux] und in [diesem Artikel (für Windows)][virtual-machines-sizes-windows].

Nicht alle VM-Serien werden in jeder der Azure-Regionen (Informationen zu Azure-Regionen finden Sie im nächsten Kapitel) angeboten. Beachten Sie auch, dass nicht alle virtuellen Computer oder VM-Serie für SAP zertifiziert sind.

> [!IMPORTANT]
> Für die Verwendung von SAP NetWeaver-basierten Anwendungen werden nur die Typen virtueller Computer, die im SAP-Hinweis [1928533] aufgeführt werden, unterstützt.
>
>

### <a name="be80d1b9-a463-4845-bd35-f4cebdb5424a"></a>Azure-Regionen
Auf virtuellen Computern werden sogenannte *Azure-Regionen* bereitgestellt. Eine Azure-Region kann ein oder mehrere Rechenzentren umfassen, die sich nahe beieinander befinden. Für die meisten geopolitischen Regionen auf der Welt verfügt Microsoft über mindestens zwei Azure-Regionen. In Europa gibt es z. B. die Azure-Regionen *Europa, Norden* und *Europa, Westen*. Diese zwei Azure-Regionen in einer geopolitischen Region haben genug Abstand zueinander, sodass sich Naturkatastrophen oder technische Notfälle nicht auf beide Azure-Regionen in der gleichen geopolitischen Region auswirken können. Da Microsoft kontinuierlich neue Azure-Regionen in verschiedenen geopolitischen Regionen in aller Welt aufbaut, werden diese Regionen immer zahlreicher. Im Dezember 2015 gab es bereits 20 Azure-Regionen, und zusätzliche Regionen wurden bereits angekündigt. Als Kunde können Sie SAP-Systeme in allen diesen Regionen bereitstellen, auch in den beiden Azure-Regionen in China. Aktuelle Informationen zu Azure-Regionen finden Sie auf der folgenden Website: <https://azure.microsoft.com/regions/>

### <a name="8d8ad4b8-6093-4b91-ac36-ea56d80dbf77"></a>Das Konzept der virtuellen Microsoft Azure-Computer
Microsoft Azure bietet eine IaaS-Lösung (Infrastruktur as a Service) zum Hosten von virtuellen Computern mit ähnlichen Funktionen wie eine lokale Virtualisierungslösung. Sie können virtuelle Computer innerhalb des Azure-Portals, PowerShell und der CLI erstellen, die ebenfalls Bereitstellungs- und Verwaltungsfunktionen bieten.

Mit dem Azure-Ressourcen-Manager können Sie Ihre Anwendungen mit einer deklarativen Vorlage bereitstellen. In einer Vorlage können Sie mehrere Dienste zusammen mit ihren Abhängigkeiten bereitstellen. Sie verwenden die gleiche Vorlage, um Ihre Anwendung in allen Phasen des Anwendungslebenszyklus wiederholt bereitzustellen.

Weitere Informationen zu Resource Manager-Vorlagen finden Sie hier:

* [Bereitstellen und Verwalten von virtuellen Computern mit Azure-Ressourcen-Manager-Vorlagen und der Azure-CLI](../../linux/create-ssh-secured-vm-from-template.md)
* [Verwalten virtueller Computer mit Azure Resource Manager und PowerShell][virtual-machines-deploy-rmtemplates-powershell]
* <https://azure.microsoft.com/documentation/templates/>

Ein weiteres interessantes Feature ist die Möglichkeit zum Erstellen von Images von virtuellen Computern, die es Ihnen ermöglichen, bestimmte Repositorys vorzubereiten, von denen Sie schnell virtuelle Computerinstanzen bereitstellen können, die Ihren Anforderungen entsprechen.

Weitere Informationen zum Erstellen von Images von virtuellen Computern finden Sie in [diesem Artikel (für Linux)][virtual-machines-linux-capture-image-resource-manager] oder [diesem Artikel (für Windows)][virtual-machines-windows-capture-image-resource-manager].

#### <a name="df49dc09-141b-4f34-a4a2-990913b30358"></a>Fehlerdomänen
Fehlerdomänen stellen eine physische Fehlereinheit dar, die eng mit der physischen Infrastruktur in Rechenzentren verwandt ist, und obwohl ein physisches Blade oder Rack als Fehlerdomäne betrachtet werden kann, gibt es keine direkte 1:1-Zuordnung zwischen den beiden.

Wenn Sie mehrere virtuelle Computer als Teil eines SAP-Systems in Microsoft Azure Virtual Machine-Diensten bereitstellen, können Sie die Azure Fabric Controller zum Bereitstellen der Anwendung in verschiedenen Fehlerdomänen einsetzen und so die Anforderungen des Microsoft Azure-SLA erfüllen. Allerdings ist die Verteilung von Fehlerdomänen über eine Azure-Skalierungseinheit (Sammlung von Hunderten von Serverknoten oder Speicherknoten und Netzwerken) oder die Zuweisung von virtuellen Computern zu einer bestimmten Fehlerdomäne etwas, das Sie nicht direkt steuern können. Um Azure Fabric Controller anzuweisen, eine Gruppe aus virtuellen Computern in verschiedenen Fehlerdomänen bereitzustellen, müssen Sie den virtuellen Computern zum Zeitpunkt der Bereitstellung eine Azure-Verfügbarkeitsgruppe zuweisen. Weitere Informationen zu Azure-Verfügbarkeitsgruppen finden Sie im Kapitel [Azure-Verfügbarkeitsgruppen][planning-guide-3.2.3] in diesem Dokument.

#### <a name="fc1ac8b2-e54a-487c-8581-d3cc6625e560"></a>Upgradedomänen
Upgradedomänen stellen eine logische Einheit dar, mit deren Hilfe Sie bestimmen können, wie eine VM innerhalb eines SAP-Systems aktualisiert wird, das aus SAP-Instanzen besteht, die auf mehreren VMs ausgeführt werden. Wenn ein Upgrade durchgeführt wird, durchläuft Microsoft Azure nacheinander den Aktualisierungsprozess für diese Upgradedomänen. Durch das Verteilen von virtuellen Computern zum Zeitpunkt der Bereitstellung über verschiedene Upgradedomänen können Sie Ihr SAP-System teilweise vor potenziellen Ausfallzeiten schützen. Um zu erzwingen, dass Azure die virtuellen Computer eines SAP-Systems bereitstellt, das über verschiedene Upgradedomänen verteilt ist, müssen Sie ein bestimmtes Attribut zum Zeitpunkt der Bereitstellung der einzelnen virtuellen Computer festlegen. Ähnlich wie Fehlerdomänen ist auch eine Azure-Skalierungseinheit in mehrere Upgradedomänen unterteilt. Um Azure Fabric Controller anzuweisen, eine Gruppe aus virtuellen Computern in verschiedenen Upgradedomänen bereitzustellen, müssen Sie den virtuellen Computern zum Zeitpunkt der Bereitstellung eine Azure-Verfügbarkeitsgruppe zuweisen. Weitere Informationen zu Azure-Verfügbarkeitsgruppen finden Sie im Kapitel [Azure-Verfügbarkeitsgruppen][planning-guide-3.2.3] weiter unten.

#### <a name="18810088-f9be-4c97-958a-27996255c665"></a>Azure-Verfügbarkeitsgruppen
Virtuelle Azure-Computer innerhalb einer Azure-Verfügbarkeitsgruppe werden vom Azure Fabric Controller auf verschiedene Fehler- und Upgradedomänen verteilt. Der Zweck der Verteilung auf verschiedene Fehler- und Upgradedomänen besteht darin, zu verhindern, dass alle virtuellen Computer eines SAP-Systems im Falle von Infrastrukturwartungsarbeiten oder eines Fehler innerhalb einer Fehlerdomäne heruntergefahren werden. Standardmäßig sind virtuelle Computer nicht Teil einer Verfügbarkeitsgruppe. Die Teilnahme einer VM in einer Verfügbarkeitsgruppe wird zum Zeitpunkt der Bereitstellung oder später durch eine Neukonfiguration und erneute Bereitstellung einer VM definiert.

Lesen Sie zum besseren Verständnis des Konzepts von Azure-Verfügbarkeitsgruppen und der Beziehung von Verfügbarkeitsgruppen zu Fehler- und Upgradedomänen [diesen Artikel][virtual-machines-manage-availability].

Informationen zum Definieren von Verfügbarkeitsgruppen für den Azure Resource Manager über eine JSON-Vorlage finden Sie in den [REST-API-Spezifikationen](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2015-06-15/swagger/compute.json). Suchen Sie dort nach „Verfügbarkeit“.

### <a name="a72afa26-4bf4-4a25-8cf7-855d6032157f"></a>Speicher: Microsoft Azure Storage und Datenträger
Microsoft Azure Virtual Machines nutzt verschiedene Speichertypen. Bei der Implementierung von SAP auf Azure Virtual Machine-Diensten ist es wichtig, die Unterschiede zwischen diesen zwei Speicherhaupttypen zu verstehen:

* Nicht permanenter, temporärer Speicher.
* Permanenter Speicher.

Azure-VMs bieten Datenträger mit flüchtigem Speicher, nachdem eine VM bereitgestellt wurde. Bei einem Neustart der VM werden alle Inhalte auf diesen Laufwerken gelöscht. Deswegen sollten Datendateien und Protokoll- bzw. Wiederholungsdateien von Datenbanken unter keinen Umständen auf diesen nicht permanenten Laufwerken gespeichert werden. Es gibt möglicherweise Ausnahmen für einige der Datenbanken, in denen diese nicht permanenten Laufwerke für tempdb und temporäre Tabellenbereiche geeignet sind. Verwenden Sie diese Laufwerke jedoch nicht für VMs der A-Serie, da diese nicht permanenten Laufwerke mit diesen VMs nur einen begrenzten Durchsatz ermöglichen. Weitere Informationen finden Sie im Artikel [Understanding the temporary drive on Windows Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/) (Grundlegendes zum temporären Laufwerk auf virtuellen Windows-Computern in Azure).

- - -
> ![Windows][Logo_Windows] Windows
> 
> Laufwerk „D:\“ auf einer Azure-VM ist ein nicht permanentes Laufwerk, das durch einige lokale Datenträger im Azure-Serverknoten gesichert wird. Da es nicht permanent ist, bedeutet dies, dass alle Änderungen am Inhalt auf dem Laufwerk „D:\“ verloren gehen, wenn der virtuelle Computer neu gestartet wird. „Alle Änderungen“ beziehen sich u. a. auf gespeicherte Dateien, erstellte Verzeichnisse und installierte Anwendungen.
> 
> ![Linux][Logo_Linux] Linux
> 
> Azure-VMs unter Linux stellen ein Laufwerk automatisch unter „/mnt/resource“ bereit. Dies ist ein nicht permanentes Laufwerk, das durch lokale Datenträger im Azure-Serverknoten gesichert wird. Da es nicht permanent ist, bedeutet dies, dass alle Änderungen am Inhalt unter „/mnt/resource“ verloren gehen, wenn die VM neu gestartet wird. „Alle Änderungen“ beziehen sich u. a. auf gespeicherte Dateien, erstellte Verzeichnisse und installierte Anwendungen.
> 
> 

- - -

Microsoft Azure Storage stellt permanenten Speicher und die normalen Ebenen für Schutz und Redundanz, die auch SAN-Speicher bietet, bereit. Auf Azure Storage basierende Datenträger sind virtuelle Festplatten (VHD), die sich in den Azure Storage-Diensten befinden. Der lokale Betriebssystem-Datenträger (Windows C:\, Linux /dev/sda1) wird in Azure Storage gespeichert. Gleiches gilt für zusätzliche Volumes/Datenträger, die auf dem virtuellen Computer bereitgestellt werden.

Es ist möglich, eine vorhandene virtuelle lokale Festplatte hochzuladen oder eine leere innerhalb von Azure zu erstellen und diese bereitgestellten virtuellen Computern anzufügen.

Nach dem Erstellen oder Hochladen einer virtuellen Festplatte in Azure Storage ist es möglich, diese in einem vorhandenen virtuellen Computer bereitzustellen und anzufügen und vorhandene (nicht bereitgestellte) VHDs zu kopieren.

Da diese VHDs permanent sind, sind beim Neustart und erneuten Erstellen einer virtuellen Computerinstanz alle Daten und Änderungen sicher. Auch wenn eine Instanz gelöscht wird, sind diese VHDs weiterhin sicher und können erneut bereitgestellt oder im Falle von Datenträgern ohne Betriebssystem anderen virtuellen Computern bereitgestellt werden.

Weitere Informationen zu Azure Storage finden Sie hier:

* <https://azure.microsoft.com/documentation/services/storage/>
* <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>
* <https://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview.aspx>

#### <a name="azure-standard-storage"></a>Azure-Standardspeicher
Der Azure Storage Standard war der verfügbare Speichertyp, als Azure IaaS veröffentlicht wurde. IOPS-Kontingente wurden für jeden einzelnen Datenträger erzwungen. Die Latenz unterschied sich grundlegend von SAN/NAS-Geräten, wie sie in der Regel für lokal gehostete High-End-SAP-Systeme bereitgestellt werden. Dennoch erwies sich der Azure-Standardspeicher für mehrere Hunderte von SAP-Systemen, die in der Zwischenzeit in Azure bereitgestellt wurden, als ausreichend.

Datenträger, die in Azure Storage Standard-Konten gespeichert werden, werden basierend auf den tatsächlich gespeicherten Daten, der Menge der Speichertransaktionen, den ausgehenden Datenübertragungen und der ausgewählten Redundanzoption in Rechnung gestellt. Viele Datenträger können mit der Maximalgröße von 1 TB erstellt werden, aber solange sie leer bleiben, werden sie nicht in Rechnung gestellt. Wenn Sie dann eine virtuelle Festplatte mit 100 GB füllen, wird Ihnen das Speichern von 100 GB in Rechnung gestellt, nicht die nominale Größe, mit der die virtuelle Festplatte erstellt wurde.

#### <a name="ff5ad0f9-f7f4-4022-9102-af07aef3bc92"></a>Azure Storage Premium
Azure Storage Premium wurde mit dem Ziel, Folgendes bereitzustellen, eingeführt:

* Bessere E/A-Latenz.
* Besserer Durchsatz.
* Weniger variierende E/A-Latenz.

Zu diesem Zweck wurden viele Änderungen eingeführt. Die beiden wichtigsten sind folgende:

* Verwendung von SSD-Laufwerken in Azure Storage-Knoten
* Ein neuer Lesecache, der durch die lokale SSD eines Azure-Serverknotens unterstützt wird

Im Gegensatz zum Standardspeicher, bei dem sich Funktionen nicht abhängig von der Größe der Festplatte (oder der VHD) geändert haben, verfügt Storage Premium derzeit über drei unterschiedliche Datenträgerkategorien, die Sie in diesem Artikel: <https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/>

Sie sehen, dass IOPS- und Datenträgerdurchsatz von der Größenkategorie der Datenträger abhängen.

Kostenbasis im Falle von Storage Premium ist nicht der tatsächliche Datenumfang, der auf diesem Datenträger gespeichert wird, sondern die Größenkategorie eines solchen Datenträgers, unabhängig von der Datenmenge, die auf dem Datenträger gespeichert wird.

Sie können auch Datenträger in Storage Premium erstellen, die den unten gezeigten Größenkategorien nicht direkt zugeordnet sind. Insbesondere beim Kopieren von Datenträgern aus dem Standardspeicher zu Storage Premium kann dies der Fall sein. Hier erfolgt eine Zuordnung zur nächstgrößeren Storage Premium-Datenträgeroption.

Die meisten Azure-VM-Familien, die SAP-zertifiziert sind, sind mit Storage Premium kompatibel und/oder stellen eine Kombination aus Azure Storage Standard und Storage Premium dar.

Wenn Sie sich den Teil zu virtuellen Computern der DS-Serie in [diesem Artikel (für Linux)][virtual-machines-sizes-linux] und [diesen Artikel (für Windows)][virtual-machines-sizes-windows] ansehen, erkennen Sie auch, dass es Datenmengeneinschränkungen für Storage Premium-Datenträgern bezüglich der Granularität der VM-Stufe gibt. Unterschiedliche virtuelle Computer der DS-Serie oder der GS-Serie verfügen auch über andere Einschränkungen im Hinblick auf die Anzahl der Datenträger für Daten, die eingebunden werden können. Diese Einschränkungen werden ebenfalls im oben erwähnten Artikel dokumentiert. Aber im Wesentlichen bedeutet das Folgendes: Wenn Sie z.B. 32 P30-Datenträger auf einem einzelnen virtuellen DS14-Computer bereitstellen, erhalten Sie NICHT das 32-fache des maximalen Durchsatzes eines P30-Datenträgers. Stattdessen beschränkt der maximale Durchsatz auf VM-Ebene den Datendurchsatz, wie im Artikel dokumentiert.

Weitere Informationen zu Storage Premium finden Sie hier: <https://azure.microsoft.com/blog/2015/04/16/azure-premium-storage-now-generally-available-2>

#### <a name="azure-storage-accounts"></a>Azure-Speicherkonten

Wenn Sie Dienste oder virtuelle Computer in Azure bereitstellen, können die Bereitstellung von VHDs und VM-Images in sogenannten Azure-Speicherkonten organisiert werden. Wenn Sie eine Azure-Bereitstellung planen, müssen Sie die Einschränkungen von Azure sorgfältig berücksichtigen. Einerseits gibt es eine begrenzte Anzahl von Speicherkonten pro Azure-Abonnement. Obwohl jedes Azure-Speicherkonto eine große Anzahl von VHD-Dateien enthalten kann, besteht eine feste Obergrenze für die Gesamt-IOPS pro Speicherkonto. Bei der Bereitstellung von Hunderten von SAP-VMs mit DBMS-Systemen, die umfassende E/A-Aufrufe erzeugen, wird empfohlen, virtuelle Computer mit hoher IOPS DBMS auf mehrere Azure-Speicherkonten zu verteilen. Achten Sie darauf, das aktuelle Limit für Azure-Speicherkonten pro Abonnement nicht zu überschreiten. Da der Speicher ein wesentlicher Teil der Datenbankbereitstellung für ein SAP-System ist, wird dieses Konzept detaillierter im bereits erwähnten [DBMS-Bereitstellungshandbuch][dbms-guide] erläutert.

Weitere Informationen zu Azure Storage-Konten finden Sie in [diesem Artikel][storage-scalability-targets]. In diesem Artikel werden Sie feststellen, dass es Unterschiede hinsichtlich der Einschränkungen zwischen Azure Storage Standard-Konten und Storage Premium-Konten gibt. Hauptunterschied ist die Datenmenge, die innerhalb eines solchen Speicherkontos gespeichert werden kann. Beim Standardspeicher ist die Menge wesentlich größer als bei Storage Premium. Auf der anderen Seite ist das Storage Standard-Konto hinsichtlich IOPS stark eingeschränkt (siehe Spalte **Total Request Rate**), während das Azure Storage Premium-Konto keine solche Einschränkung aufweist. Bei der Erörterung der Bereitstellung von SAP-Systemen, insbesondere der DBMS-Server, werden Details und Ergebnisse dieser Unterschiede erläutert.

Innerhalb eines Speicherkontos haben Sie die Möglichkeit, unterschiedliche Container für die Organisation und Kategorisierung verschiedener VHDs zu erstellen. Diese Container werden verwendet, um z.B. VHDs unterschiedlicher VMs zu trennen. Es gibt keine Leistungseinbußen bei der Verwendung von nur einem Container oder mehrerer Container unter einem einzelnen Azure-Speicherkonto.

Innerhalb von Azure folgt ein VHD-Name der folgenden Benennungskonvention, die einen eindeutigen Namen für die virtuelle Festplatte in Azure bereitstellen muss:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Die oben genannte Zeichenfolge muss die VHD eindeutig identifizieren, die in Azure Storage gespeichert ist.

#### <a name="c55b2c6e-3ca1-4476-be16-16c81927550f"></a>Verwaltete Datenträger

Bei Managed Disks handelt es sich um einen neuen Ressourcentyp in Azure Resource Manager, der anstelle von in Azure Storage-Konten gespeicherten VHDs verwendet werden kann. Verwaltete Datenträger lassen sich automatisch an der Verfügbarkeitsgruppe des virtuellen Computers ausrichten, mit der sie verbunden sind, und erhöhen daher die Verfügbarkeit Ihres virtuellen Computers sowie der Dienste, die auf diesem ausgeführt werden. Weitere Informationen finden Sie [in diesem allgemeinen Artikel](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview).

Es wird empfohlen, dass die verwaltete Datenträger verwenden, da diese die Bereitstellung und Verwaltung virtueller Computer erleichtern.
SAP unterstützt derzeit nur Managed Disks Premium. Weitere Informationen finden Sie im SAP-Hinweis [1928533].

#### <a name="microsoft-azure-storage-resiliency"></a>Microsoft Azure Storage – Resilienz

Microsoft Azure Storage speichert die Basis-VM (mit Betriebssystem) und angefügte Datenträger oder BLOBs in mindestens drei getrennten Speicherknoten. Das wird als „lokaler redundanter Speicher „ (LRS) bezeichnet. LRS ist die Standardeinstellung für alle Azure-Speichertypen. 

Es gibt mehrere weitere Redundanzmethoden, die im Artikel [Azure Storage-Replikation](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) beschrieben werden.

> [!NOTE]
>Ab Azure Storage Premium, dem empfohlenen Speichertyp für DBMS-VMs und Datenträger, die Datenbank- und Protokoll-/Wiederholungsdateien speichern, ist LRS die einzige verfügbare Methode. Daher müssen Sie Datenbankmethoden wie SQL Server Always On, Oracle Data Guard oder HANA-Systemreplikation konfigurieren, um die Datenbank-Datenreplikation in einer anderen Azure-Region oder -Verfügbarkeitszone zu aktivieren.


> [!NOTE]
> Für DBMS-Bereitstellungen wird die Verwendung von georedundantem Speicher, verfügbar mit Azure-Standardspeicher, nicht empfohlen, da er erhebliche Leistungseinbußen zur Folge hat und die Schreibreihenfolge auf verschiedenen VHDs, die an eine VM angeschlossen sind, nicht berücksichtigt. Das Ignorieren der Schreibreihenfolge über verschiedene VHDs hinweg erhöht das Risiko für inkonsistente Datenbanken auf Seiten des Replikationsziels, wenn Datenbank- und Protokoll-/Wiederholungsdateien über mehrere VHDs (wie üblich) auf der Quell-VM verteilt werden.


### <a name="61678387-8868-435d-9f8c-450b2424f5bd"></a>Microsoft Azure-Netzwerk

Microsoft Azure stellt eine Netzwerkinfrastruktur bereit, die die Zuordnung aller Szenarios ermöglicht, die wir mit SAP-Software umsetzen möchten. Die Funktionen sind:

* Zugriff von außen direkt auf die virtuellen Computer über Windows Terminal Services oder ssh/VNC
* Zugriff auf Dienste und bestimmte Ports, die von Anwendungen auf den virtuellen Computern verwendet werden
* Interne Kommunikation und Namensauflösung zwischen einer Gruppe von virtuellen Computern, die als virtuelle Azure-Computer bereitgestellt werden
* Standortübergreifende Konnektivität zwischen einem lokalen Kundennetzwerk und dem Azure-Netzwerk
* Azure-Region-übergreifende Konnektivität oder Rechenzentrumskonnektivität zwischen Azure-Standorten

Weitere Informationen finden Sie hier: <https://azure.microsoft.com/documentation/services/virtual-network/>

Es gibt viele verschiedene Möglichkeiten zum Konfigurieren der Namens- und IP-Auflösung in Azure. Es gibt auch einen Azure DNS-Dienst, der verwendet werden kann, anstatt einen eigenen DNS-Server einzurichten. Weitere Informationen finden Sie in [diesem Artikel][virtual-networks-manage-dns-in-vnet] und auf [dieser Seite](https://azure.microsoft.com/services/dns/).

Für standortübergreifende oder Hybridszenarios verlassen wir uns auf die Tatsache, dass das lokale AD/OpenLDAP/DNS über VPN- oder private Verbindungen zu Azure erweitert wurde. Für bestimmte hier dokumentierte Szenarios kann es notwendig sein, ein AD/OpenLDAP-Replikat in Azure zu installieren.

Da Netzwerk und Namensauflösung wesentliche Teile der Datenbankbereitstellung für ein SAP-System sind, wird dieses Konzept detaillierter im [DBMS-Bereitstellungshandbuch][dbms-guide] erläutert.

##### <a name="azure-virtual-networks"></a>Virtuelle Azure-Netzwerke

Durch das Einrichten eines virtuellen Azure-Netzwerk können Sie den Adressbereich der privaten IP-Adressen definieren, die durch die Azure-DHCP-Funktionen zugeordnet werden. In standortübergreifenden Szenarios wird der definierte IP-Adressbereich weiterhin mithilfe von DHCP von Azure zugeordnet. Allerdings erfolgt die Auflösung von Domänennamen lokal (vorausgesetzt, dass die virtuellen Computer einer lokalen Domäne angehören). Darum können auch Adressen außerhalb der verschiedenen Azure-Clouddienste aufgelöst werden.

Alle virtuellen Computer in Azure müssen mit einem virtuellen Netzwerk verbunden sein.

Weitere Informationen finden Sie in [diesem Artikel][resource-groups-networking] und auf [dieser Seite](https://azure.microsoft.com/documentation/services/virtual-network/).


> [!NOTE]
> Standardmäßig können Sie nach der Bereitstellung eines virtuellen Computers die Konfiguration des virtuellen Netzwerks nicht mehr ändern. Die TCP/IP-Einstellungen müssen dem Azure-DHCP-Server überlassen werden. Das Standardverhalten ist die dynamische IP-Zuweisung.
>
>

Die MAC-Adresse der virtuellen Netzwerkkarte kann sich ändern, z.B. nach einer Größenanpassung, und das Windows- oder Linux-Gastbetriebssystem erkennt die neue Netzwerkkarte und verwendet automatisch DHCP zum Zuweisen der IP- und DNS-Adressen.

##### <a name="static-ip-assignment"></a>Statische IP-Zuweisung
Es ist möglich, virtuellen Computern in einem virtuellen Azure-Netzwerk feste oder reservierte IP-Adressen zuzuweisen. Das Ausführen der virtuellen Computer in einem virtuellen Azure-Netzwerk eröffnet eine gute Möglichkeit, um diese Funktion für einige Szenarios im Bedarfsfall zu nutzen. Die IP-Zuweisung bleibt während des gesamten Lebenszyklus des virtuellen Computers gültig, unabhängig davon, ob der virtuelle Computer ausgeführt oder heruntergefahren wird. Daher müssen Sie die Gesamtzahl der VMs (ausgeführte und beendete VMs) berücksichtigen, wenn Sie den IP-Adressbereich für das virtuelle Netzwerk definieren. Die IP-Adresse bleibt zugewiesen, bis der virtuelle Computer und seine Netzwerkschnittstelle gelöscht werden oder bis die Zuweisung der IP-Adresse wieder aufgehoben wird. Weitere Informationen finden Sie [in diesem Artikel][virtual-networks-static-private-ip-arm-pportal].

> [!NOTE]
> Sie sollten einzelnen virtuellen Netzwerkkarten statische IP-Adressen über Azure-Tools zuweisen. Virtuellen Netzwerkkarten sollten nicht innerhalb des Gastbetriebssystems statische IP-Adressen zugewiesen werden. Einige Azure-Dienste wie der Azure Backup-Dienst basieren darauf, dass mindestens die primäre virtuelle Netzwerkkarte auf DHCP festgelegt ist und nicht auf statische IP-Adressen. Weitere Informationen finden Sie auch im Dokument zur [Problembehandlung bei der Sicherung virtueller Azure-Computer](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking).
>
>

##### <a name="multiple-nics-per-vm"></a>Mehrere NICs pro virtuellen Computer

Sie können mehrere virtuelle Netzwerkschnittstellenkarten (vNIC) für virtuelle Azure-Computer definieren. Dank der Möglichkeit, mehrere vNICs einzusetzen, können Sie mit der Einrichtung der Netzwerk-Datenverkehrstrennung beginnen. Dabei wird z.B. der Clientdatenverkehr über eine vNIC und der Back-End-Datenverkehr über eine zweite vNIC weitergeleitet. Je nach Typ des virtuellen Computers gibt es unterschiedliche Einschränkungen im Hinblick auf die Anzahl von vNICs. Informationen zu Details, Funktionen und Einschränkungen finden Sie in den folgenden Artikeln:

* [Erstellen eines virtuellen Computers mit mehreren Netzwerkschnittstellenkarten][virtual-networks-multiple-nics-windows]
* [Erstellen eines virtuellen Computers mit mehreren Netzwerkkarten mithilfe einer Vorlage][virtual-networks-multiple-nics-linux]
* [Bereitstellen von Multi-NIC-VMs mithilfe einer Vorlage][virtual-network-deploy-multinic-arm-template]
* [Bereitstellen von Multi-NIC-VMs mit PowerShell][virtual-network-deploy-multinic-arm-ps]
* [Bereitstellen von Multi-NIC-VMs mithilfe der Azure-Befehlszeilenschnittstelle][virtual-network-deploy-multinic-arm-cli]

#### <a name="site-to-site-connectivity"></a>Standort-zu-Standort-Konnektivität

„Standortübergreifend“ bezieht sich darauf, dass virtuelle Azure-Computer und lokale Computer über eine transparente und permanente VPN-Verbindung verknüpft sind. Dieses Konzept wird wahrscheinlich zum häufigsten SAP-Bereitstellungsmuster in Azure werden. Die Annahme ist, dass betriebliche Verfahren und Prozesse mit SAP-Instanzen in Azure transparent arbeiten sollten. Das bedeutet, dass Sie in der Lage sein sollten, aus diesen Systemen heraus zu drucken und das SAP Transport Management System (TMS) zu verwenden, um Änderungen aus einem Entwicklungssystem in Azure in ein lokal bereitgestelltes Testsystem zu übertragen. Weitere Dokumentationen zu „Standort-zu-Standort“ finden Sie in [diesem Artikel][vpn-gateway-create-site-to-site-rm-powershell].

##### <a name="vpn-tunnel-device"></a>VPN-Tunnelgerät

Um eine Standort-zu-Standort-Verbindung (lokales Rechenzentrum zu Azure-Rechenzentrum) zu erstellen, müssen Sie ein VPN-Gerät abrufen und konfigurieren oder RRAS (Routing and Remote Access Service) verwenden, der als Softwarekomponente mit Windows Server 2012 eingeführt wurde.

* [Erstellen eines virtuellen Netzwerks mit einer Site-to-Site-VPN-Verbindung mit PowerShell][vpn-gateway-create-site-to-site-rm-powershell]
* [Informationen zu VPN-Geräten für Site-to-Site-VPN Gateway-Verbindungen][vpn-gateway-about-vpn-devices]
* [Häufig gestellte Fragen zu VPN Gateway][vpn-gateway-vpn-faq]

![Site-to-Site-Verbindung zwischen lokalem System und Azure][planning-guide-figure-600]

Die obige Abbildung zeigt zwei Azure-Abonnements mit IP-Adressunterbereichen, die für die Verwendung in virtuellen Netzwerken in Azure reserviert sind. Die Verbindung vom lokalen Netzwerk zu Azure wird mittels VPN hergestellt.

#### <a name="point-to-site-vpn"></a>Punkt-zu-Standort-VPN

Punkt-zu-Standort-VPN erfordert, dass jeder Clientcomputer eine Verbindung mit dem eigenen VPN in Azure herstellt. Für die SAP-Szenarios, die wir betrachten, ist die Punkt-zu-Standort-Konnektivität nicht geeignet. Aus diesem Grund werden keine weiteren Angaben zur Punkt-zu-Standort-VPN-Konnektivität gemacht.

Weitere Informationen finden Sie hier:
* [Konfigurieren einer Point-to-Site-Verbindung mit einem VNet über das Azure-Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Konfigurieren einer Point-to-Site-Verbindung mit einem VNet mithilfe von PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

#### <a name="multi-site-vpn"></a>VPN mit mehreren Standorten

Azure bietet jetzt auch die Möglichkeit zum Erstellen von VPN-Konnektivität mit mehreren Standorten für ein Azure-Abonnement. Zuvor war ein einzelnes Abonnement auf eine Standort-zu-Standort-VPN-Verbindung beschränkt. Diese Einschränkung wurde mit VPN-Verbindungen mit mehreren Standorten für ein einzelnes Abonnement aufgehoben. Dadurch können mehrere Azure-Regionen für ein bestimmtes Abonnement über standortübergreifende Konfigurationen genutzt werden.

Weitere Informationen finden Sie in [diesem Artikel][vpn-gateway-create-site-to-site-rm-powershell].

#### <a name="vnet-to-vnet-connection"></a>VNet-zu-VNet-Verbindung

Bei Verwendung von VPN mit mehreren Standorten müssen Sie ein separates virtuelles Azure-Netzwerk in jeder einzelnen Region konfigurieren. Jedoch besteht häufig die Anforderung, dass die Komponenten in den verschiedenen Regionen miteinander kommunizieren sollen. Im Idealfall sollte diese Kommunikation nicht von einer Azure-Region zu lokalen Standorten und von dort aus in die andere Azure-Region weitergeleitet werden. Azure bietet zur Vereinfachung die Möglichkeit, eine Verbindung von einem virtuellen Azure-Netzwerk in einer Region mit einem anderen virtuellen Azure-Netzwerk, das in einer anderen Region gehostet wird, zu konfigurieren. Diese Funktionalität wird als VNet-zu-VNet-Verbindung bezeichnet. Weitere Informationen zu dieser Funktionalität finden Sie hier: <https://azure.microsoft.com/documentation/articles/vpn-gateway-vnet-vnet-rm-ps/>.

#### <a name="private-connection-to-azure-expressroute"></a>Private Verbindung mit Azure ExpressRoute

Microsoft Azure ExpressRoute ermöglicht das Erstellen privater Verbindungen zwischen Azure-Rechenzentren und der lokalen Infrastruktur des Kunden oder in einer Umgebung mit mehreren Standorten. ExpressRoute wird von verschiedenen MPLS-VPN-Anbietern (paketvermittelt) oder anderen Netzwerkdienstanbietern angeboten. ExpressRoute-Verbindungen verlaufen nicht über das öffentliche Internet. ExpressRoute-Verbindungen bieten eine größere Sicherheit, höhere Zuverlässigkeit durch mehrere parallele Leitungen, höhere Geschwindigkeit und weniger Latenz als herkömmliche Verbindungen über das Internet.

Weitere Informationen zu Azure ExpressRoute und den Angeboten finden Sie hier:

* <https://azure.microsoft.com/documentation/services/expressroute/>
* <https://azure.microsoft.com/pricing/details/expressroute/>
* <https://azure.microsoft.com/documentation/articles/expressroute-faqs/>

Express Route ermöglicht mehrere Azure-Abonnements über eine ExpressRoute-Verbindung, wie hier beschrieben wird.

* <https://azure.microsoft.com/documentation/articles/expressroute-howto-linkvnet-arm/>
* <https://azure.microsoft.com/documentation/articles/expressroute-howto-circuit-arm/>

#### <a name="forced-tunneling-in-case-of-cross-premises"></a>Tunnelerzwingung in standortübergreifenden Szenarien
Für virtuelle Computer, die lokalen Domänen über Standort-zu-Standort-, Punkt-zu-Standort- oder ExpressRoute-Verbindungen beitreten, müssen Sie sicherstellen, dass auch die Internetproxyeinstellungen für alle Benutzer auf diesen virtuellen Computern bereitgestellt werden. Standardmäßig würde Software, die auf diesen virtuellen Computer ausgeführt wird, und würden Benutzer, die mit einem Browser auf das Internet zugreifen, nicht durch den Unternehmensproxy geleitet, sondern über Azure eine direkte Verbindung mit dem Internet herstellen. Aber auch die Proxyeinstellung ist keine hundertprozentige Lösung, um den Datenverkehr über den Unternehmensproxy zu leiten, da es in der Verantwortung der Software und der Dienste liegt, den Proxy zu suchen. Wenn die auf dem virtuellen Computer ausgeführte Software dies nicht leistet oder wenn ein Administrator die Einstellungen ändert, kann der Datenverkehr ins Internet wieder direkt durch Azure ins Internet umgeleitet werden.

Um eine derartige direkte Internetkonnektivität zu vermeiden, können Sie erzwungenes Tunneling mit Standort-zu-Standort-Konnektivität zwischen lokalen Standorten und Azure konfigurieren. Eine ausführliche Beschreibung zum erzwungenen Tunneling finden Sie hier: <https://azure.microsoft.com/documentation/articles/vpn-gateway-forced-tunneling-rm/>

Erzwungenes Tunneling mit ExpressRoute wird von Kunden aktiviert, die eine Standardroute über die ExpressRoute-BGP-Peeringsitzungen anfordern.

#### <a name="summary-of-azure-networking"></a>Zusammenfassung der Azure-Netzwerke

In diesem Kapitel haben viele wichtige Informationen zu Azure-Netzwerken erhalten. Es folgt eine Zusammenfassung der wichtigsten Punkte:

* Mit Azure Virtual Networks können Sie eine Netzwerkstruktur in Ihrer Azure-Bereitstellung abbilden. VNETs können isoliert voneinander sein oder der Datenverkehr zwischen VNETs kann mithilfe von Netzwerksicherheitsgruppen gesteuert werden.
* Virtuelle Azure-Netzwerke können genutzt werden, um virtuellen Computern IP-Adressbereiche oder feste IP-Adressen zuzuweisen.
* Zum Einrichten einer Standort-zu-Standort- oder Punkt-zu-Standort-Verbindung müssen Sie zunächst ein virtuelles Azure-Netzwerk erstellen.
* Nach der Bereitstellung eines virtuellen Computers ist es nicht mehr möglich, das virtuelle Netzwerk, das dem virtuellen Computer zugewiesen wurde, zu ändern.

### <a name="quotas-in-azure-virtual-machine-services"></a>Kontingente in Azure Virtual Machine-Diensten
Wir müssen klar sagen, dass die Speicher- und Netzwerkinfrastruktur von mehreren virtuellen Computern, auf denen verschiedene Dienste in der Azure-Infrastruktur ausgeführt werden, gemeinsam genutzt wird. Und genau wie in den Rechenzentren des Kunden findet die übermäßige Bereitstellung einiger Infrastrukturressourcen zu einem gewissen Grad statt. Die Microsoft Azure-Plattform verwendet Datenträger-, CPU-, Netzwerk- und andere Kontingente, um den Ressourcenverbrauch zu begrenzen und eine konsistente sowie vorhersagbare Leistung beizubehalten.  Die verschiedenen VM-Typen (A5, A6 usw.) haben unterschiedliche Kontingente für die Anzahl der Datenträger, für die CPU, das RAM und das Netzwerk.

> [!NOTE]
> CPU- und Arbeitsspeicherressourcen der von SAP unterstützten Typen virtueller Computer werden auf den Hostknoten vorab zugeordnet. Dies bedeutet, dass nach der Bereitstellung des virtuellen Computers die Ressourcen auf dem Host gemäß der Definition durch den Typ des virtuellen Computers zur Verfügung stehen.
>
>

Bei der Planung und Größenänderung von SAP-Lösungen in Azure müssen die Kontingente für virtuelle Computer jeder Größe berücksichtigt werden. Die VM-Kontingente werden [hier (für Linux)][virtual-machines-sizes-linux] und [hier (für Windows)][virtual-machines-sizes-windows] beschrieben.

Die beschriebenen Kontingente stellen die theoretischen Höchstwerte dar.  Das IOPS-Limit pro VHD kann mit kleinen IOs (8 KB) erreicht werden, aber möglicherweise nicht mit großen IOs (1 MB).  Das IOPS-Limit wird für die Granularität einzelner Datenträger erzwungen.

Nachfolgende Entscheidungsstruktur hilft Ihnen zu ermitteln, ob sich Ihr vorhandenes SAP-System in Azure Virtual Machines Services und seine Features einpasst oder ob zu dessen Implementierung in Azure Änderungen an seiner Konfiguration erforderlich sind:

![Entscheidungsbaum für die Bereitstellung Von SAP in Azure][planning-guide-figure-700]

**Schritt 1:** Ermitteln Sie zunächst die SAPS-Anforderungen für ein bestimmtes SAP-System. Die SAPS-Anforderungen müssen in eine DBMS-Komponente und eine SAP-Anwendungskomponente unterteilt werden, selbst wenn das SAP-System bereits lokal in einer 2-Tier-Konfiguration bereitgestellt ist. Bei vorhandenen Systemen können die mit der derzeitigen Hardware verwendeten SAPS meist mithilfe vorhandener SAP-Benchmarks bestimmt bzw. geschätzt werden. Anweisungen dazu finden Sie hier: <https://sap.com/about/benchmark.html>.
Für neu bereitgestellte SAP-Systeme sollten Sie Dimensionierungstests durchführen, durch die Sie die SAPS-Anforderungen des Systems bestimmen.
In diesem Blog und im angehängten Dokument finden Sie Informationen zu SAP-Größen in Azure: <https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>

**Schritt 2:** Bei vorhandenen Systemen sollten das E/A-Volumen sowie die E/A-Vorgänge pro Sekunde auf dem DBMS-Server gemessen werden. Bei neu geplanten Systemen sollten die Dimensionierungstests für das neue System auch grob Aufschluss über die E/A-Anforderungen auf DBMS-Seite geben. Falls Sie sich unsicher sind, sollten Sie eventuell eine Machbarkeitsstudie durchführen.

**Schritt 3:** Vergleichen Sie die SAPS-Anforderungen für den DBMS-Server mit den SAPS der verschiedenen von Azure bereitgestellten VM-Typen. Informationen zu den SAPS der verschiedenen Azure-VM-Typen finden Sie in SAP-Hinweis [1928533]. Hauptaugenmerk sollten Sie zunächst auf die DBMS-VM legen, da die Datenbankebene die Ebene in einem SAP-NetWeaver-System ist, die sich in den meisten Bereitstellungen nicht verteilen lässt. Im Gegensatz dazu lässt sich die SAP-Anwendungsebene durchaus verteilen. Wenn keiner der von SAP unterstützten VM-Typen von Azure die erforderlichen SAPS bereitstellt, kann die Workload des geplanten SAP-Systems nicht in Azure ausgeführt werden. Sie müssen das System entweder lokal bereitstellen oder das Volumen der Arbeitslast für das System ändern.

**Schritt 4:** Wie [hier (für Linux)][virtual-machines-sizes-linux] und [hier (für Windows)][virtual-machines-sizes-windows] dokumentiert, erzwingt Azure ein IOPS-Kontingent pro Datenträger, und zwar unabhängig davon, ob Sie Storage Standard oder Storage Premium verwenden. Abhängig vom VM-Typ variiert die Anzahl der Datenträger für Daten, die bereitgestellt werden können. Daher können Sie eine maximale Anzahl von IOPS berechnen, die mit den verschiedenen VM-Typen erreicht werden kann. Abhängig vom Layout der Datenbankdatei können Sie Datenträger so aufteilen, dass sie ein Volumen des Gastbetriebssystems werden. Wenn allerdings das aktuelle IOPS-Volumen eines bereitgestellten SAP-Systems die berechneten Grenzen des größten VM-Typs von Azure überschreitet und keine Möglichkeit besteht, dies mit zusätzlichem Speicher zu kompensieren, kann dies die Arbeitslast des SAP-Systems erheblich beeinträchtigen. In solchen Fällen kann ein Punkt erreicht werden, an dem das System nicht mehr in Azure bereitgestellt werden sollte.

**Schritt 5:** Gerade bei lokal in 2-Ebenen-Konfigurationen bereitgestellten SAP-Systemen ist die Wahrscheinlichkeit groß, dass das System in Azure in einer 3-Ebenen-Konfiguration konfiguriert werden muss. In diesem Schritt prüfen Sie, ob die SAP-Anwendungsschicht Komponenten enthält, die nicht verteilt werden können und sich daher nicht in die von den verschiedenen Azure-VM-Typen bereitgestellten CPU- und Arbeitsspeicherressourcen einpassen. Sollte es tatsächlich eine solche Komponente geben, können das SAP-System und seine Workload nicht in Azure bereitgestellt werden. Lassen sich die SAP-Anwendungskomponenten jedoch auf mehrere Azure-VMs verteilen, so kann das System in Azure bereitgestellt werden.

**Schritt 6:** Wenn die Komponenten der DBMS- und SAP-Anwendungsebene auf Azure-VMs ausgeführt werden können, müssen bei der Konfiguration die folgenden Punkte berücksichtigt werden:

* Anzahl der Azure-VMs
* VM-Typen für die einzelnen Komponenten
* Anzahl der VHDs einer DBMS-VM zur Bereitstellung ausreichender IOPS

## <a name="managing-azure-assets"></a>Verwalten von Azure-Assets

### <a name="azure-portal"></a>Azure-Portal

Das Azure-Portal ist eine von drei Schnittstellen für die Verwaltung von Azure-VM-Bereitstellungen. Die grundlegenden Verwaltungsaufgaben, wie das Bereitstellen von VMs aus Images, können über das Azure-Portal ausgeführt werden. Darüber hinaus können im Azure-Portal auch Speicherkonten, virtuelle Netzwerke und andere Azure-Komponenten erstellt werden. Funktionen wie das Hochladen von VHDs vom lokalen Standort nach Azure oder das Kopieren von VHDs innerhalb von Azure sind allerdings Aufgaben, für die Tools von Drittanbietern bzw. Verwaltungstools wie PowerShell oder die Befehlszeilenschnittstelle erforderlich sind.

![Übersicht der virtuellen Computer im Microsoft Azure-Portal][planning-guide-figure-800]


Die Verwaltung und Konfiguration einer VM-Instanz können im Azure-Portal erfolgen.

Sie können hierbei VMs nicht nur neu starten und herunterfahren, sondern auch Datenträger für Daten für VM-Instanzen anfügen, trennen und erstellen, um die Instanzen zur Imagevorbereitung zu erfassen und die Größe der VM-Instanzen zu konfigurieren.

Das Azure-Portal bietet grundlegende Funktionen für die Bereitstellung und Konfiguration von VMs und zahlreiche weitere Azure-Dienste. Jedoch stellt das Azure-Portal nicht alle verfügbaren Funktionen bereit. So können im Azure-Portal Aufgaben wie die Folgenden nicht ausgeführt werden:

* Hochladen von VHDs in Azure
* Kopieren von VMs


### <a name="management-via-microsoft-azure-powershell-cmdlets"></a>Verwaltung mit Microsoft Azure PowerShell-Cmdlets

Windows PowerShell ist ein leistungsfähiges und erweiterbares Framework, das gerade von Kunden, die eine größere Anzahl von Systemen in Azure bereitstellen, sehr geschätzt wird. Nach der Installation der PowerShell-Cmdlets auf einem Desktop, Laptop oder einer dedizierten Verwaltungsstation können die Cmdlets remote ausgeführt werden.

Der Vorgang zum Aktivieren eines lokalen Desktops/Laptops für die Verwendung der Azure PowerShell-Cmdlets und deren Konfiguration für die Verwendung mit Azure-Abonnements wird in [diesem Artikel][powershell-install-configure] beschrieben.

Ausführlichere Schritte zum Installieren, Aktualisieren und Konfigurieren der Azure PowerShell-Cmdlets finden Sie auch in [diesem Kapitel des Bereitstellungshandbuchs][deployment-guide-4.1].

Bisherige Erfahrungen der Kunden zeigen PowerShell (PS) mit Sicherheit als das leistungsstärkere Tool für die Bereitstellung von VMs und die Erstellung benutzerdefinierter Schritte innerhalb der Bereitstellung von VMs. Alle Kunden, die SAP-Instanzen in Azure ausführen, verwenden PS-Cmdlets zur Ergänzung Ihrer Verwaltungsaufgaben im Azure-Portal bzw. sogar ausschließlich zur Verwaltung ihrer Bereitstellungen in Azure. Da für die Azure-spezifischen Cmdlets dieselben Namenskonventionen gelten wie für die mehr als 2000 Windows-spezifischen Cmdlets, fällt Windows-Administratoren die Übernahme dieser Cmdlets nicht schwer.

Ein Beispiel finden Sie hier: <https://blogs.technet.com/b/keithmayer/archive/2015/07/07/18-steps-for-end-to-end-iaas-provisioning-in-the-cloud-with-azure-resource-manager-arm-powershell-and-desired-state-configuration-dsc.aspx>


Die Bereitstellung der Azure Monitoring Extension for SAP (siehe Kapitel [Azure-Überwachungslösung für SAP][planning-guide-9.1] in diesem Dokument) ist nur über PowerShell oder die Befehlszeilenschnittstelle möglich. Daher muss PowerShell oder die Befehlszeilenschnittstelle bei der Bereitstellung oder Verwaltung eines SAP NetWeaver-Systems in Azure installiert und konfiguriert werden.  

Da Azure eine erweiterte Funktionalität bietet, werden neue PS-Cmdlets hinzugefügt, weshalb ein Update der Cmdlets erforderlich ist. Daher ist es sinnvoll, mindestens einmal im Monat die Azure-Downloadwebsite (<https://azure.microsoft.com/downloads/>) auf neue Cmdlet-Versionen zu prüfen. Die neue Version wird zusätzlich zur älteren Version installiert.

Eine allgemeine Liste von Azure-bezogenen PowerShell-Befehlen finden hier: <https://docs.microsoft.com/powershell/azure/overview>.

### <a name="management-via-microsoft-azure-cli-commands"></a>Verwaltung mit den Befehlen der Microsoft Azure-Befehlszeilenschnittstelle

Für Kunden, die Linux verwenden und Azure-Ressourcen verwalten möchten, steht PowerShell womöglich nicht zur Debatte. Als Alternative bietet Microsoft die Azure-Befehlszeilenschnittstelle.
Die Azure-Befehlszeilenschnittstelle stellt eine Reihe von plattformübergreifenden Open Source-Befehlen für die Arbeit mit der Azure-Plattform bereit. Die Azure-Befehlszeilenschnittstelle bietet im Wesentlichen die gleiche Funktionalität wie das Azure-Portal.

Informationen zur Installation, Konfiguration und Verwendung der Befehle der Befehlszeilenschnittstelle für die Ausführung von Azure-Aufgaben finden Sie unter den folgenden Themen:

* [Installieren der klassischen Azure CLI][xplat-cli]
* [Bereitstellen und Verwalten von virtuellen Computern mit Azure Resource Manager-Vorlagen und der Azure-CLI][../../linux/create-ssh-secured-vm-from-template.md]
* [Verwenden von Azure-Ressourcen und Ressourcengruppen mit der Azure CLI][xplat-cli-azure-resource-manager]

Weitere Informationen zur Verwendung der Azure-Befehlszeilenschnittstelle zum Bereitstellen der Azure Monitoring Extension for SAP finden Sie im [Bereitstellungshandbuch][planning-guide] im Kapitel [Azure-Befehlszeilenschnittstelle für Linux-VMs][deployment-guide-4.5.2].

## <a name="different-ways-to-deploy-vms-for-sap-in-azure"></a>Verschiedene Möglichkeiten der Bereitstellung von VMs für SAP in Azure

In diesem Kapitel lernen Sie verschiedene Methoden der Bereitstellung von VMs in Azure kennen. Zudem werden weitere Vorbereitungsverfahren wie auch die Behandlung von VHDs und VMs in Azure beschrieben.

### <a name="deployment-of-vms-for-sap"></a>Bereitstellung von VMs für SAP

Microsoft Azure bietet verschiedene Methoden der Bereitstellung von VMs und der zugehörigen Datenträger. Daher ist es wichtig, die Unterschiede zu kennen, da die Vorbereitung der VMs je nach Bereitstellungsmethode variiert. Insbesondere werfen wir einen Blick auf die folgenden Szenarios:

#### <a name="4d175f1b-7353-4137-9d2f-817683c26e53"></a>Verschieben eines virtuellen lokalen Computers mit einem nicht generalisierten Datenträger in Azure

Sie planen, ein bestimmtes SAP-System vom lokalen Standort nach Azure zu verschieben. Dies kann erfolgen, indem Sie die VHD mit dem Betriebssystem und den SAP- und DBMS-Binärdateien sowie die VHDs mit den Daten- und Protokolldateien des DBMS in Azure hochladen. Im Gegensatz zu [Szenario 2][planning-guide-5.1.2] unten werden Hostname, SAP-SID und SAP-Benutzerkonten auf der Azure-VM entsprechend der Konfiguration in der lokalen Umgebung beibehalten. Daher ist eine Generalisierung des Images nicht erforderlich. Lokale Vorbereitungsschritte sowie Informationen zum Hochladen nicht generalisierter VMs oder Datenträger in Azure finden Sie in diesem Dokument im Kapitel [Vorbereitung des Verschiebens eines virtuellen lokalen Computers mit einem nicht generalisierten Datenträger in Azure][planning-guide-5.2.1]. Ausführliche Schritte zur Bereitstellung eines solchen Images in Azure finden Sie im Kapitel [Szenario 3: Verschieben eines virtuellen lokalen Computers mit einer nicht generalisierten virtuellen Azure-Festplatte mit SAP][deployment-guide-3.4] des [Bereitstellungshandbuchs][deployment-guide].

#### <a name="e18f7839-c0e2-4385-b1e6-4538453a285c"></a>Bereitstellen eines virtuellen Computers mit einem kundenspezifischen Image

Aufgrund der spezifischen Patch-Anforderungen Ihrer Betriebssystem- bzw. DBMS-Version sind die im Azure Marketplace erhältlichen Images möglicherweise nicht für Ihren Bedarf geeignet. Daher muss möglicherweise anhand eines eigenen, benutzerdefinierten OS/DBMS-VM-Images ein virtueller Computer erstellt werden, der anschließend mehrmals bereitgestellt werden kann. Bei der Vorbereitung eines solchen privaten Images für die Duplizierung müssen folgende Punkte berücksichtigt werden:

- - -
> ![Windows][Logo_Windows] Windows
>
> Ausführlichere Informationen finden Sie hier: <https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed> Die Windows-Einstellungen (wie die Windows-SID und der Hostname) müssen auf der lokalen VM mit dem folgenden sysprep-Befehl abstrahiert/generalisiert werden.
>
>
> ![Linux][Logo_Linux] Linux
>
> Führen Sie die in den folgenden Artikeln beschriebenen Schritte für [SUSE][virtual-machines-linux-create-upload-vhd-suse], [Red Hat][virtual-machines-linux-redhat-create-upload-vhd] oder [Oracle Linux][virtual-machines-linux-create-upload-vhd-oracle] aus, um eine VHD zum Hochladen in Azure vorzubereiten.
>
>

- - -
Wenn Sie auf Ihrer lokalen VM bereits SAP-Inhalte installiert haben (insbesondere bei zweischichtigen Systemen), können Sie die SAP-Systemeinstellungen nach der Bereitstellung der Azure-VM über das vom SAP Software Provisioning Manager unterstützte Verfahren zum Umbenennen von Instanzen anpassen (SAP-Hinweis [1619720]). Die lokalen Vorbereitungsschritte sowie Informationen zum Hochladen einer generalisierten VM in Azure finden Sie in diesem Dokument in den Kapiteln [Vorbereitung der Bereitstellung eines virtuellen Computers mit einem kundenspezifischen Image für SAP][planning-guide-5.2.2] und [Hochladen einer virtuellen Festplatte vom lokalen Standort in Azure][planning-guide-5.3.2]. Ausführliche Schritte zur Bereitstellung eines solchen Images in Azure finden Sie im Kapitel [Szenario 2: Bereitstellen eines virtuellen Computers mit einem benutzerdefinierten Image für SAP][deployment-guide-3.3] des [Bereitstellungshandbuchs][deployment-guide].

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Bereitstellen eines virtuellen Computers aus Azure Marketplace

Sie möchten ein von Microsoft oder einem Drittanbieter bereitgestelltes VM-Image aus dem Azure Marketplace zur Bereitstellung Ihrer VM verwenden. Nachdem Sie Ihre VM in Azure bereitgestellt haben, verwenden Sie dieselben Richtlinien und Tools zum Installieren der SAP-Software und/oder des DBMS auf der VM wie in einer lokalen Umgebung. Eine detailliertere Beschreibung der Bereitstellung finden Sie im Kapitel [Szenario 1: Bereitstellen eines virtuellen Computers aus dem Azure Marketplace für SAP][deployment-guide-3.2] des [Bereitstellungshandbuchs][deployment-guide].

### <a name="6ffb9f41-a292-40bf-9e70-8204448559e7"></a>Vorbereiten virtueller Computer mit SAP für Azure

Vor dem Hochladen von VMs in Azure müssen Sie sicherstellen, dass die VMs und VHDs bestimmte Anforderungen erfüllen. Abhängig von der Bereitstellungsmethode gibt es hierbei geringfügige Unterschiede.

#### <a name="1b287330-944b-495d-9ea7-94b83aff73ef"></a>Vorbereitung des Verschiebens eines virtuellen lokalen Computers mit einem nicht generalisierten Datenträger in Azure

Eine gängige Bereitstellungsmethode ist die Migration einer vorhandenen VM mit einem lokal ausgeführten SAP-System nach Azure. Diese VM und das SAP-System auf der VM sollten in Azure mit dem gleichen Hostnamen und vermutlich auch derselben SAP-SID ausgeführt werden. Das Gastbetriebssystem der VM sollte in diesem Fall nicht für mehrere Bereitstellungen generalisiert werden. Wenn das lokale Netzwerk in Azure erweitert wurde (siehe das Kapitel [Standortübergreifend – Bereitstellung einzelner oder mehrerer virtueller SAP-Computer in Azure mit der Anforderung der vollständigen Integration in das lokale Netzwerk][planning-guide-2.2] in diesem Dokument), können innerhalb der VM sogar die gleichen Domänenkonten verwendet werden wie zuvor lokal vor der Verschiebung.

Für die Vorbereitung Ihres eigenen Azure-VM-Datenträgers gelten folgende Anforderungen:

* Bislang war die VHD mit dem Betriebssystem auf eine Größe von maximal 127 GB beschränkt. Diese Einschränkung gilt seit Ende März 2015 nicht mehr. Die VHD mit dem Betriebssystem kann nun wie jede andere im Azure-Speicher bereitgestellte VHD bis zu 1 TB groß sein kann.
* Das VHD-Format muss jedoch statisch sein. Dynamische VHDs oder VHDs im VHDx-Format werden von Azure noch nicht unterstützt. Dynamische VHDs werden beim Hochladen mit PowerShell-Cmdlets bzw. der Befehlszeilenschnittstelle in statische VHDs konvertiert.
* Auch auf der VM bereitgestellte VHDs, die in Azure weiterhin bereitgestellt werden sollen, müssen ein statisches VHD-Format aufweisen. Lesen Sie [diesen Artikel (für Linux)](../../linux/managed-disks-overview.md) und [diesen Artikel (für Windows)](../../windows/managed-disks-overview.md), um mehr über Größenbegrenzungen von Datenträgern zu erfahren. Dynamische VHDs werden beim Hochladen mit PowerShell-Cmdlets bzw. der Befehlszeilenschnittstelle in statische VHDs konvertiert.
* Solange die VM noch nicht bereitgestellt ist und keine geeigneteren Benutzer zur Verfügung stehen, sollten Sie ein anderes lokales Konto mit Administratorrechten hinzufügen, das vom Microsoft-Support verwendet bzw. als Kontext für die Ausführung von Diensten und Anwendungen zugewiesen werden kann.
* Fügen Sie weitere lokale Konten hinzu, wie sie für die jeweiligen Bereitstellungsszenarien erforderlich sind.

- - -
> ![Windows][Logo_Windows] Windows
>
> In diesem Szenario ist für das Hochladen und die Bereitstellung der VM in Azure keine Generalisierung (sysprep) der VM erforderlich.
> Stellen Sie sicher, dass das Laufwerk D:\ nicht verwendet wird.
> Legen Sie dann die automatische Bereitstellung für angefügte Datenträger fest, wie in diesem Dokument im Kapitel [Festlegen der automatischen Bereitstellung für angefügte Datenträger][planning-guide-5.5.3] beschrieben.
>
> ![Linux][Logo_Linux] Linux
>
> In diesem Szenario ist für das Hochladen und die Bereitstellung der VM in Azure keine Generalisierung (waagent -deprovision) der VM erforderlich.
> Stellen Sie sicher, dass /mnt/resource nicht verwendet wird und alle Datenträger über uuid bereitgestellt wurden. Stellen Sie für den Betriebssystem-Datenträger sicher, dass im Bootloadereintrag auch die Uuid-basierte Bereitstellung enthalten ist.
>
>

- - -
#### <a name="57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3"></a>Vorbereitung der Bereitstellung eines virtuellen Computers mit einem kundenspezifischen Image für SAP

VHD-Dateien, die ein generalisiertes Betriebssystem enthalten, werden in Azure-Speicherkonten in Containern oder als Images für einen verwalteten Datenträger gespeichert. Von einem solchen Image können Sie eine neue VM bereitstellen, indem Sie in Ihren Vorlagendateien für die Bereitstellung als Quelle auf die VHD oder das Image für einen verwalteten Datenträger verweisen. Diese Methode wird im Kapitel [Szenario 2: Bereitstellen eines virtuellen Computers mit einem benutzerdefinierten Image für SAP][deployment-guide-3.3] des [Bereitstellungshandbuchs][deployment-guide] beschrieben.

Für die Vorbereitung Ihres eigenen Azure-VM-Image gelten folgende Anforderungen:

* Bislang war die VHD mit dem Betriebssystem auf eine Größe von maximal 127 GB beschränkt. Diese Einschränkung gilt seit Ende März 2015 nicht mehr. Die VHD mit dem Betriebssystem kann nun wie jede andere im Azure-Speicher bereitgestellte VHD bis zu 1 TB groß sein kann.
* Das VHD-Format muss jedoch statisch sein. Dynamische VHDs oder VHDs im VHDx-Format werden von Azure noch nicht unterstützt. Dynamische VHDs werden beim Hochladen mit PowerShell-Cmdlets bzw. der Befehlszeilenschnittstelle in statische VHDs konvertiert.
* Auch auf der VM bereitgestellte VHDs, die in Azure weiterhin bereitgestellt werden sollen, müssen ein statisches VHD-Format aufweisen. Lesen Sie [diesen Artikel (für Linux)](../../windows/managed-disks-overview.md) und [diesen Artikel (für Windows)](../../linux/managed-disks-overview.md), um mehr über Größenbegrenzungen von Datenträgern zu erfahren. Dynamische VHDs werden beim Hochladen mit PowerShell-Cmdlets bzw. der Befehlszeilenschnittstelle in statische VHDs konvertiert.
* Fügen Sie weitere lokale Konten hinzu, wie sie für die jeweiligen Bereitstellungsszenarien erforderlich sind.
* Wenn das Image eine Installation von SAP NetWeaver enthält und es wahrscheinlich ist, dass der Hostname bei der Bereitstellung in Azure geändert wird, empfiehlt es sich, die neuesten Versionen der SAP Software Provisioning Manager-DVD in die Vorlage zu kopieren. Sie können dann problemlos auf die von SAP bereitgestellte Umbenennungsfunktion zurückgreifen, um den geänderten Hostnamen anzupassen und/oder die SID des SAP-Systems im bereitgestellten VM-Image zu ändern, sobald eine neue Kopie gestartet wird.

- - -
> ![Windows][Logo_Windows] Windows
>
> Stellen Sie sicher, dass Laufwerk D:\ nicht verwendet wird. Legen Sie dann die automatische Bereitstellung für angefügte Datenträger fest, wie in diesem Dokument in Kapitel [Festlegen der automatischen Bereitstellung für angefügte Datenträger][planning-guide-5.5.3] beschrieben.
>
> ![Linux][Logo_Linux] Linux
>
> Stellen Sie sicher, dass /mnt/resource nicht verwendet wird und alle Datenträger über uuid bereitgestellt wurden. Stellen Sie für den Betriebssystem-Datenträger sicher, dass im Bootloadereintrag auch die Uuid-basierte Bereitstellung enthalten ist.
>
>

- - -
* Die SAP-GUI (für Verwaltungs- und Setup-Aufgaben) kann in einer solchen Vorlage vorinstalliert sein.
* Auch andere Programme, die für die erfolgreiche Ausführung der VMs in standortübergreifenden Szenarios erforderlich sind, können installiert werden, solange diese die Umbenennung der VM unterstützen.

Wenn die VM ausreichend vorbereitet ist, um generisch und letztendlich unabhängig von Konten bzw. Benutzern zu sein, die im anvisierten Azure-Bereitstellungsszenario nicht verfügbar sind, kann der letzte Vorbereitungsschritt für die Generalisierung eines solchen Image durchgeführt werden.

##### <a name="generalizing-a-vm"></a>Generalisieren eines virtuellen Computers
- - -
> ![Windows][Logo_Windows] Windows
>
> Der letzte Schritt ist die Anmeldung bei einer VM mit einem Administratorkonto. Öffnen Sie als *Administrator* ein Windows-Befehlsfenster. Wechseln Sie zu %windir%\windows\system32\sysprep and execute sysprep.exe.
> Daraufhin wird ein kleines Fenster geöffnet. Aktivieren Sie das Kontrollkästchen **Verallgemeinern** (das standardmäßig deaktiviert ist), und ändern Sie die Option für das Herunterfahren von der Standardeinstellung „Neu starten“ in „Herunterfahren“. Dieses Verfahren setzt voraus, dass der sysprep-Prozess lokal auf dem Gastbetriebssystem einer VM ausgeführt wird.
> Wenn Sie diesen Vorgang für einen bereits in Azure ausgeführten virtuellen Computer durchführen möchten, folgen Sie den in [diesem Artikel](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) beschriebenen Schritten.
>
> ![Linux][Logo_Linux] Linux
>
> [Erfassen eines virtuellen Linux-Computers zur Verwendung als Resource Manager-Vorlage][capture-image-linux-step-2-create-vm-image]
>
>

- - -
### <a name="transferring-vms-and-vhds-between-on-premises-to-azure"></a>Übertragen von virtuellen Computern und VHDs vom lokalen Standort nach Azure
Zum Hochladen von VM-Images und -Datenträgern in Azure ist das Azure-Portal ungeeignet. Verwenden Sie hierzu stattdessen die PowerShell-Cmdlets bzw. die Befehlszeilenschnittstelle von Azure. Ebenso können Sie das Tool „AzCopy“ verwenden. Das Tool kann VHDs in beiden Richtungen zwischen lokalem Standort und Azure kopieren. Mit ihm können Sie auch VHDs zwischen Azure-Regionen kopieren. Download und Verwendung von AzCopy werden in [dieser Dokumentation][storage-use-azcopy] beschrieben.

Eine weitere Alternative sind die verschiedenen auf dem Markt erhältlichen GUI-ähnlichen Tools von Drittanbietern. Vor der Verwendung eines solchen Tools sollten Sie jedoch sicherstellen, dass das Tool Azure-Seitenblobs unterstützt. Für unsere Zwecke müssen wir den Azure-Seitenblobspeicher verwenden (die Unterschiede werden hier beschrieben: <https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs>). Mit den von Azure bereitgestellten Tools lassen sich die VMs und VHDs vor dem Upload auch effizient komprimieren. Dadurch reduziert sich die zum Hochladen benötigte Zeit (die ohnehin schon je nach Upload-Link zum Internet zwischen dem lokalen Standort und der gewünschten Azure-Bereitstellungsregion variiert). Es ist leicht nachzuvollziehen, dass das Hochladen von VMs oder VHDs von einem europäischen Standort in ein Azure-Rechenzentrum in den USA länger dauert als das Hochladen derselben VMs/VHDs in ein Azure-Rechenzentrum in Europa.

#### <a name="a43e40e6-1acc-4633-9816-8f095d5a7b6a"></a>Hochladen einer virtuellen Festplatte vom lokalen Standort in Azure
Eine vorhandene VM oder VHD, die Sie aus dem lokalen Netzwerk hochladen möchten, muss die Anforderungen erfüllen, die in diesem Dokument im Kapitel [Vorbereitung des Verschiebens eines virtuellen lokalen Computers mit einem nicht generalisierten Datenträger in Azure][planning-guide-5.2.1] beschrieben werden.

Eine solche VM muss nicht generalisiert werden und kann in dem Zustand hochgeladen werden, den sie nach dem Herunterfahren am lokalen Standort hat. Das Gleiche gilt auch für zusätzliche VHDs, die kein Betriebssystem enthalten.

##### <a name="uploading-a-vhd-and-making-it-an-azure-disk"></a>Hochladen einer VHD und Konvertieren in einen Azure-Datenträger
In diesem Fall möchten wir eine VHD mit oder ohne Betriebssystem hochladen und sie als Datenträger für Daten oder das Betriebssystem auf einer VM bereitstellen. Dies ist ein mehrstufiger Prozess.

**PowerShell**

* Melden Sie sich mit *Connect-AzAccount* bei Ihrem Abonnement an.
* Legen Sie das Abonnement Ihres Kontexts mit *Set-AzContext* und dem Parameter SubscriptionId oder SubscriptionName fest – siehe <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>.
* Laden Sie die VHD mit *Add-AzVhd* in ein Azure Storage-Konto hoch – siehe <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>.
* (Optional) Erstellen Sie mit *New-AzDisk* einen verwalteten Datenträger aus der VHD – siehe <https://docs.microsoft.com/powershell/module/az.compute/new-Azdisk>.
* Legen Sie mit *Set-AzVMOSDisk* den Betriebssystem-Datenträger der neuen VM-Konfiguration auf die VHD oder den verwalteten Datenträger fest – siehe <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>.
* Erstellen Sie mit *New-AzVM* einen neuen virtuellen Computer aus der VM-Konfiguration – siehe <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>.
* Fügen Sie einem neuen virtuellen Computer mit *Add-AzVMDataDisk* einen Datenträger hinzu – siehe <https://docs.microsoft.com/powershell/module/az.compute/add-Azvmdatadisk>.

**Azure-Befehlszeilenschnittstelle**

* Melden Sie sich mit *az login* bei Ihrem Abonnement an.
* Wählen Sie mit *azure account set - subscription`<subscription name or id`>* Ihr Abonnement aus.
* Laden Sie die VHD mit *az storage blob upload* hoch (siehe [Verwenden der Azure-Befehlszeilenschnittstelle mit Azure Storage][storage-azure-cli]).
* (Optional) Erstellen Sie mit *az disk create* aus der VHD einen verwalteten Datenträger (siehe https://docs.microsoft.com/cli/azure/disk ).
* Erstellen Sie mit *azure vm create* und dem Parameter *--attach-os-disk* eine neue VM, wobei Sie die hochgeladene VHD oder den verwalteten Datenträger als Betriebssystem-Datenträger angeben.
* Fügen Sie einen Datenträger für Daten mit *vm disk attach-new* und dem Parameter *--new* hinzu.

**Vorlage**

* Hochladen der VHD mit PowerShell oder der Azure-Befehlszeilenschnittstelle
* (Optional) Erstellen Sie einen verwalteten Datenträger aus der VHD mit PowerShell, der Azure CLI oder dem Azure-Portal.
* Stellen Sie die VM mit einer JSON-Vorlage bereit, die auf die VHD verweist, wie in [dieser JSON-Beispielvorlage](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) gezeigt, oder mit verwalteten Datenträgern wie in [dieser JSON-Beispielvorlage](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json) gezeigt.

#### <a name="deployment-of-a-vm-image"></a>Bereitstellen eines VM-Image
Eine vorhandene VM oder VHD, die Sie aus dem lokalen Netzwerk hochladen möchten, um sie als Azure-VM-Image zu verwenden, muss die Anforderungen erfüllen, die in diesem Dokument im Kapitel [Vorbereitung der Bereitstellung einer VM mit einem kundenspezifischen Image für SAP][planning-guide-5.2.2] beschrieben werden.

* Verwenden Sie *sysprep* (unter Windows) bzw. *waagent -deprovision* (unter Linux), um die VM zu generalisieren (siehe [Technische Referenz zu Sysprep](https://technet.microsoft.com/library/cc766049.aspx) für Windows bzw. [Erfassen eines virtuellen Linux-Computers zur Verwendung als Resource Manager-Vorlage][capture-image-linux-step-2-create-vm-image] für Linux).
* Melden Sie sich mit *Connect-AzAccount* bei Ihrem Abonnement an.
* Legen Sie das Abonnement Ihres Kontexts mit *Set-AzContext* und dem Parameter SubscriptionId oder SubscriptionName fest – siehe <https://docs.microsoft.com/powershell/module/az.accounts/set-Azcontext>.
* Laden Sie die VHD mit *Add-AzVhd* in ein Azure Storage-Konto hoch – siehe <https://docs.microsoft.com/powershell/module/az.compute/add-Azvhd>.
* (Optional) Erstellen Sie mit *New-AzImage* einen verwalteten Datenträger aus der VHD – siehe <https://docs.microsoft.com/powershell/module/az.compute/new-Azimage>.
* Legen Sie den Betriebssystem-Datenträger der neuen VM-Konfiguration auf
  * VHD mit *Set-AzVMOSDisk -SourceImageUri -CreateOption fromImage* – siehe <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmosdisk>.
  * Image für einen verwalteten Datenträger *Set-AzVMSourceImage* – siehe <https://docs.microsoft.com/powershell/module/az.compute/set-Azvmsourceimage>.
* Erstellen Sie mit *New-AzVM* einen neuen virtuellen Computer aus der VM-Konfiguration – siehe <https://docs.microsoft.com/powershell/module/az.compute/new-Azvm>.

**Azure-Befehlszeilenschnittstelle**

* Verwenden Sie *sysprep* (unter Windows) bzw. *waagent -deprovision* (unter Linux), um die VM zu generalisieren (siehe [Technische Referenz zu Sysprep](https://technet.microsoft.com/library/cc766049.aspx) für Windows bzw. [Erfassen eines virtuellen Linux-Computers zur Verwendung als Resource Manager-Vorlage][capture-image-linux-step-2-create-vm-image] für Linux).
* Melden Sie sich mit *az login* bei Ihrem Abonnement an.
* Wählen Sie mit *azure account set - subscription`<subscription name or id`>* Ihr Abonnement aus.
* Laden Sie die VHD mit *az storage blob upload* hoch (siehe [Verwenden der Azure-Befehlszeilenschnittstelle mit Azure Storage][storage-azure-cli]).
* (Optional) Erstellen Sie mit *az image create* aus der VHD ein Image für einen verwalteten Datenträger (siehe https://docs.microsoft.com/cli/azure/image ).
* Erstellen Sie mit *azure vm create* und dem Parameter *--attach-os-disk* eine neue VM, wobei Sie die hochgeladene VHD oder das Image des verwalteten Datenträgers als Betriebssystem-Datenträger angeben.

**Vorlage**

* Verwenden Sie *sysprep* (unter Windows) bzw. *waagent -deprovision* (unter Linux), um die VM zu generalisieren (siehe [Technische Referenz zu Sysprep](https://technet.microsoft.com/library/cc766049.aspx) für Windows bzw. [Erfassen eines virtuellen Linux-Computers zur Verwendung als Resource Manager-Vorlage][capture-image-linux-step-2-create-vm-image] für Linux).
* Hochladen der VHD mit PowerShell oder der Azure-Befehlszeilenschnittstelle
* (Optional) Erstellen eines Image eines verwalteten Datenträgers aus der VHD mit PowerShell, der Azure CLI oder dem Azure-Portal
* Stellen Sie die VM mit einer JSON-Vorlage bereit, die auf die Image-VHD verweist, wie in [dieser JSON-Beispielvorlage](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json) gezeigt, oder mit dem Image des verwalteten Datenträgers wie in [dieser JSON-Beispielvorlage](https://github.com/Azure/azure-quickstart-templates/blob/master/sap-2-tier-user-image-md/azuredeploy.json) gezeigt.

#### <a name="downloading-vhds-or-managed-disks-to-on-premises"></a>Lokales Herunterladen von VHDs oder verwalteten Datenträgern
Azure Infrastructure as a Service (IaaS) ist keine Einbahnstraße, auf der Sie VHDs und SAP-Systeme nur in eine Richtung hochladen können. Vielmehr können Sie Ihre SAP-Systeme auch wieder aus Azure zurück an Ihren lokalen Standort übertragen.

Während des Downloads dürfen die VHDs oder Managed Disks allerdings nicht aktiv sein. Selbst wenn Sie Datenträger herunterladen, die auf VMs bereitgestellt werden, müssen Sie diese VMs herunterfahren und ihre Zuordnung aufheben. Wenn Sie nur den Inhalt der Datenbank herunterladen möchten, um daraus lokal ein neues System einzurichten, und es akzeptabel ist, dass das System während des Downloads und der Einrichtung des neuen Systems in Azure betriebsbereit bleibt, können Sie eine lange Ausfallzeit vermeiden, indem Sie eine komprimierte Datenbanksicherung auf einem Datenträger erstellen und anschließend anstatt der VM mit der Betriebssystembasis nur diesen Datenträger herunterladen.

#### <a name="powershell"></a>PowerShell

* Herunterladen eines verwalteten Datenträgers  
  Zunächst brauchen Sie Zugriff auf den zugrundeliegenden Blob des verwalteten Datenträgers. Dann können Sie den zugrundeliegenden Blob in ein neues Speicherkonto kopieren und den Blob aus diesem Speicherkonto herunterladen.

  ```powershell
  $access = Grant-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name> -Access Read -DurationInSecond 3600
  $key = (Get-AzStorageAccountKey -ResourceGroupName <resource group> -Name <storage account name>)[0].Value
  $destContext = (New-AzStorageContext -StorageAccountName <storage account name -StorageAccountKey $key)
  Start-AzStorageBlobCopy -AbsoluteUri $access.AccessSAS -DestContainer <container name> -DestBlob <blob name> -DestContext $destContext
  # Wait for blob copy to finish
  Get-AzStorageBlobCopyState -Container <container name> -Blob <blob name> -Context $destContext
  Save-AzVhd -SourceUri <blob in new storage account> -LocalFilePath <local file path> -StorageKey $key
  # Wait for download to finish
  Revoke-AzDiskAccess -ResourceGroupName <resource group> -DiskName <disk name>
  ```

* Herunterladen einer VHD  
  Wenn das SAP-System beendet und die VM heruntergefahren sind, können Sie die VHD mit dem PowerShell-Cmdlet „Save-AzVhd“ (ausgeführt auf dem lokalen Ziel) zurück in die lokale Umgebung herunterladen. Sie benötigen dazu die URL der VHD, die Sie im Abschnitt „Speicher“ des Azure-Portals finden (navigieren Sie zum Speicherkonto und dann zu dem Speichercontainer, in dem die VHD erstellt wurde). Außerdem müssen Sie angeben, wohin die VHD kopiert werden soll.

  Sie geben dann den Befehl mit den Parametern „SourceUri“ (URL der herunterzuladenden VHD) und „LocalFilePath“ (physischer Speicherort der VHD, einschließlich ihres Namens) ein. Der Befehl kann wie folgt aussehen:

  ```powerhell
  Save-AzVhd -ResourceGroupName <resource group name of storage account> -SourceUri http://<storage account name>.blob.core.windows.net/<container name>/sapidedata.vhd -LocalFilePath E:\Azure_downloads\sapidesdata.vhd
  ```

  Weitere Informationen zum Cmdlet „Save-AzVhd“ finden Sie hier: <https://docs.microsoft.com/powershell/module/az.compute/save-Azvhd>.

#### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle
* Herunterladen eines verwalteten Datenträgers  
  Zunächst brauchen Sie Zugriff auf den zugrundeliegenden Blob des verwalteten Datenträgers. Dann können Sie den zugrundeliegenden Blob in ein neues Speicherkonto kopieren und den Blob aus diesem Speicherkonto herunterladen.
  ```
  az disk grant-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --duration-in-seconds 3600
  az storage blob download --sas-token "<sas token>" --account-name <account name> --container-name <container name> --name <blob name> --file <local file>
  az disk revoke-access --ids "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>"
  ```

* Herunterladen einer VHD   
  Wenn das SAP-System beendet und die VM heruntergefahren sind, können Sie die VHD mit dem Befehl _azure storage blob download_ der Azure-Befehlszeilenschnittstelle (ausgeführt auf dem lokalen Ziel) zurück in die lokale Umgebung herunterladen. Sie benötigen dazu den Namen und den Container der VHD, die Sie im Abschnitt „Speicher“ des Azure-Portals finden (navigieren Sie zum Speicherkonto und dann zu dem Speichercontainer, in dem die VHD erstellt wurde). Außerdem müssen Sie angeben, wohin die VHD kopiert werden soll.

  Sie geben dann den Befehl mit den Parametern „blob“ und „container“ der herunterzuladenden VHD sowie „destination“ (physischer Zielspeicherort der VHD, einschließlich ihres Namens) ein. Der Befehl kann wie folgt aussehen:

  ```
  az storage blob download --name <name of the VHD to download> --container-name <container of the VHD to download> --account-name <storage account name of the VHD to download> --account-key <storage account key> --file <destination of the VHD to download>
  ```

### <a name="transferring-vms-and-disks-within-azure"></a>Übertragen von VMs und Datenträgern innerhalb von Azure

#### <a name="copying-sap-systems-within-azure"></a>Kopieren von SAP-Systemen innerhalb von Azure

Ein SAP-System und sogar ein dedizierter DBMS-Server, der eine SAP-Anwendungsschicht unterstützt, besteht vermutlich aus mehreren Datenträgern, die das Betriebssystem mit den Binärdateien sowie die Daten- und Protokolldateien der SAP-Datenbank enthalten. Weder die Azure-Funktionalität des Kopierens von Datenträgern noch die Azure-Funktionalität des Speicherns von Datenträgern auf einem lokalen Datenträger verfügen jedoch über einen Synchronisierungsmechanismus, der mehrere Datenträger auf konsistente Weise abbilden kann. Daher hätten die kopierten oder gespeicherten Datenträger, selbst wenn diese auf derselben VM bereitgestellt werden, wohl kaum einen identischen Status. Wenn aber die verschiedenen Datenträger unterschiedliche Daten- und Protokolldateien enthielten, wäre die Datenbank letztendlich inkonsistent.

**Zusammenfassung: Zum Kopieren oder Speichern der Datenträger einer SAP-Systemkonfiguration müssen Sie das SAP-System beenden und die für das SAP-System bereitgestellte VM herunterfahren. Erst danach können Sie den Datenträgersatz mit den Systemdateien kopieren oder herunterladen, um eine Kopie des SAP-Systems in Azure oder am lokalen Standort zu erstellen.**

Datenträger mit Daten können als VHD-Dateien in einem Azure-Speicherkonto gespeichert und direkt an eine VM angefügt oder als Image verwendet werden. Die VHD wird in diesem Fall vor dem Anfügen an die VM an einen anderen Speicherort kopiert. Der vollständige Name der VHD-Datei muss innerhalb von Azure eindeutig sein. Wie bereits erwähnt besteht der Name wie folgt aus drei Teilen:

    http(s)://<storage account name>.blob.core.windows.net/<container name>/<vhd name>

Datenträger für Daten können auch verwaltete Datenträger sein. In diesem Fall wird der verwaltete Datenträger verwendet, um einen neuen Datenträger zu erstellen, bevor er an den virtuellen Computer angefügt wird. Der Name des verwalteten Datenträgers muss in der Ressourcengruppe eindeutig sein.

##### <a name="powershell"></a>PowerShell

Wie in [diesem Artikel][storage-powershell-guide-full-copy-vhd] beschrieben, können Sie VHDs mit den Azure PowerShell-Cmdlets kopieren. Um einen neuen verwalteten Datenträger zu erstellen, verwenden Sie „New-AzDiskConfig“ und „New-AzDisk“, so wie in folgendem Beispiel gezeigt.

```powershell
$config = New-AzDiskConfig -CreateOption Copy -SourceUri "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" -Location <location>
New-AzDisk -ResourceGroupName <resource group name> -DiskName <disk name> -Disk $config
```

##### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Wie in [diesem Artikel][storage-azure-cli-copy-blobs] beschrieben, können Sie VHDs mithilfe der Azure-Befehlszeilenschnittstelle kopieren. Um einen neuen verwalteten Datenträger zu erstellen, verwenden Sie *az disk create*, so wie in folgendem Beispiel gezeigt.

```
az disk create --source "/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/disks/<disk name>" --name <disk name> --resource-group <resource group name> --location <location>
```

##### <a name="azure-storage-tools"></a>Azure Storage-Tools

* <https://storageexplorer.com/>

Professionelle Editionen von Azure Storage-Explorern finden Sie hier:

* <https://www.cerebrata.com/>
* <http://clumsyleaf.com/products/cloudxplorer>

Das Kopieren einer VHD innerhalb eines Speicherkontos ist ein kurzer Prozess, der (wie das Erstellen von Momentaufnahmen bei SAN-Hardware mit „lazy copy“ und „copy on write“) nur wenige Sekunden dauert. Sobald Sie eine solche Kopie erstellt haben, können Sie sie direkt an eine VM anfügen bzw. als Image verwenden, um sie in dieser Form als Kopie an VMs anzufügen.

##### <a name="powershell"></a>PowerShell

```powershell
# attach a vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -VhdUri <path to vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name newdatadisk -ManagedDiskId <managed disk id> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption attach
$vm | Update-AzVM

# attach a copy of the vhd to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$vm = Add-AzVMDataDisk -VM $vm -Name <disk name> -VhdUri <new path of vhd> -SourceImageUri <path to image vhd> -Caching <caching option> -DiskSizeInGB $null -Lun <lun, for example 0> -CreateOption fromImage
$vm | Update-AzVM

# attach a copy of the managed disk to a vm
$vm = Get-AzVM -ResourceGroupName <resource group name> -Name <vm name>
$diskConfig = New-AzDiskConfig -Location $vm.Location -CreateOption Copy -SourceUri <source managed disk id>
$disk = New-AzDisk -DiskName <disk name> -Disk $diskConfig -ResourceGroupName <resource group name>
$vm = Add-AzVMDataDisk -VM $vm -Caching <caching option> -Lun <lun, for example 0> -CreateOption attach -ManagedDiskId $disk.Id
$vm | Update-AzVM
```
##### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

```

# attach a vhd to a vm
az vm unmanaged-disk attach --resource-group <resource group name> --vm-name <vm name> --vhd-uri <path to vhd>

# attach a managed disk to a vm
az vm disk attach --resource-group <resource group name> --vm-name <vm name> --disk <managed disk id>

# attach a copy of the vhd to a vm
# this scenario is currently not possible with Azure CLI. A workaround is to manually copy the vhd to the destination.

# attach a copy of a managed disk to a vm
az disk create --name <new disk name> --resource-group <resource group name> --location <location of target virtual machine> --source <source managed disk id>
az vm disk attach --disk <new disk name or managed disk id> --resource-group <resource group name> --vm-name <vm name> --caching <caching option> --lun <lun, for example 0>
```

#### <a name="9789b076-2011-4afa-b2fe-b07a8aba58a1"></a>Kopieren von Datenträgern zwischen Azure-Speicherkonten
Diese Aufgabe kann nicht im Azure-Portal ausgeführt werden. Verwenden Sie stattdessen die Azure PowerShell-Cmdlets, die Azure CLI oder einen Speicherbrowser eines Drittanbieters. Mit den PowerShell-Cmdlets bzw. den Befehlen der Befehlszeilenschnittstelle können Sie Blobs erstellen und verwalten. Dies schließt auch die Möglichkeit des asynchronen Kopierens von Blobs zwischen Speicherkonten und Regionen innerhalb des Azure-Abonnements ein.

##### <a name="powershell"></a>PowerShell
Sie können VHDs auch von einem Abonnement ins andere kopieren. Weitere Informationen finden Sie [in diesem Artikel][storage-powershell-guide-full-copy-vhd].

Die grundlegende Abfolge der Logik des PS-Cmdlets sieht wie folgt aus:

* Erstellen Sie mit *New-AzStorageContext* einen Speicherkontokontext für das **Quellspeicherkonto** – siehe <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>.
* Erstellen Sie mit *New-AzStorageContext* einen Speicherkontokontext für das **Zielspeicherkonto** – siehe <https://docs.microsoft.com/powershell/module/az.storage/new-AzStoragecontext>.
* Starten Sie die Kopie mit:

```powershell
Start-AzStorageBlobCopy -SrcBlob <source blob name> -SrcContainer <source container name> -SrcContext <variable containing context of source storage account> -DestBlob <target blob name> -DestContainer <target container name> -DestContext <variable containing context of target storage account>
```

* Überprüfen Sie den Status der Kopie in einer Schleife mit:

```powershell
Get-AzStorageBlobCopyState -Blob <target blob name> -Container <target container name> -Context <variable containing context of target storage account>
```

* Fügen Sie die neue VHD an eine VM an, wie oben beschrieben.

Beispiele finden Sie in [diesem Artikel][storage-powershell-guide-full-copy-vhd].

##### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle
* Starten Sie die Kopie mit:

```
az storage blob copy start --source-blob <source blob name> --source-container <source container name> --source-account-name <source storage account name> --source-account-key <source storage account key> --destination-container <target container name> --destination-blob <target blob name> --account-name <target storage account name> --account-key <target storage account name>
```

* Wenn sich die Kopie nach wie vor in einer Schleife befindet, überprüfen Sie den Status mit Folgendem:

```
az storage blob show --name <target blob name> --container <target container name> --account-name <target storage account name> --account-key <target storage account name>
```

* Fügen Sie die neue VHD an eine VM an, wie oben beschrieben.

Beispiele finden Sie in [diesem Artikel][storage-azure-cli-copy-blobs].

### <a name="disk-handling"></a>Behandlung von Datenträgern

#### <a name="4efec401-91e0-40c0-8e64-f2dceadff646"></a>VM-/Datenträgerstruktur für SAP-Bereitstellungen

Im Idealfall sollten die Verfahren in Verbindung mit VM-Strukturen und den zugehörigen Datenträgern einfach sein. In lokalen Installationen finden die verschiedensten kundenspezifischen Strukturierungskonzepte für Serverinstallationen Anwendung.

* Ein Basisdatenträger mit dem Betriebssystem und allen Binärdateien des DBMS- bzw. SAP-Systems. Seit März 2015 gilt für dieser Datenträger ein Größenlimit von 1 TB (statt bislang 127 GB).
* Mindestens ein Datenträger mit der DBMS-Protokolldatei der SAP-Datenbank sowie der Protokolldatei des temporären DBMS-Speicherbereichs (sofern vom DBMS unterstützt). Wenn die Anforderungen an die Datenbankprotokoll-IOPS hoch sind, müssen Sie das System auf mehrere Datenträger aufteilen, um das erforderliche IOPS-Volumen zu erreichen.
* Mehrere Datenträger mit jeweils einer oder zwei Datenbankdateien der SAP-Datenbank sowie den temporären DBMS-Datendateien (sofern vom DBMS unterstützt).

![Verweiskonfiguration der Azure-IaaS-VM für SAP][planning-guide-figure-1300]


- - -
> ![Windows][Logo_Windows] Windows
>
> Bei vielen Kunden haben wir Konfigurationen gesehen, in denen beispielsweise die SAP- und DBMS-Binärdateien nicht auf Laufwerk C:\ mit dem Betriebssystem installiert waren. Hierfür wurden die verschiedensten Gründe genannt, gingen wir der Sache jedoch auf den Grund, stellte sich meist heraus, dass die Laufwerke vor 10 oder 15 Jahren zu klein wurden und Betriebssystemupdates einfach mehr Speicherplatz erforderten. Diese Bedingungen treffen heute aber kaum mehr zu. Heute kann Laufwerk C:\ Festplatten oder VMs mit großen Kapazitäten zugeordnet werden. Um die Bereitstellungsstruktur möglichst einfach zu halten, empfehlen wir für SAP NetWeaver-Systeme in Azure das folgende Bereitstellungsmuster:
>
> Die Auslagerungsdatei für das Betriebssystem Windows sollte auf Laufwerk D: (nicht persistenter Datenträger) eingerichtet werden.
>
> ![Linux][Logo_Linux] Linux
>
> Die Auslagerungsdatei für Linux sollte unter „/mnt/mnt/resource“ eingerichtet werden, wie in [diesem Artikel][virtual-machines-linux-agent-user-guide] beschrieben. Die Auslagerungsdatei kann in der Konfigurationsdatei „/etc/waagent.conf“ des Linux-Agenten konfiguriert werden. Nehmen Sie die folgenden Einstellungen vor:
>
>

```
ResourceDisk.EnableSwap=y
ResourceDisk.SwapSizeMB=30720
```

Zur Aktivierung der Änderungen müssen Sie den Linux-Agenten neu starten:

```
sudo service waagent restart
```

Informationen zur empfohlenen Größe der Auslagerungsdatei finden Sie in SAP-Hinweis [1597355] .

- - -
Wie viele Datenträger Sie für Ihre DBMS-Datendateien benötigen und der Typ des Azure-Speichers, auf dem diese Datenträger bereitgestellt werden, richtet sich nach den IOPS-Anforderungen und der benötigten Latenz. Die genauen Kontingente werden in [diesem (für Linux)][virtual-machines-sizes-linux] und in [diesem Artikel (für Windows)][virtual-machines-sizes-windows] beschrieben.

Aus den letzten zwei Jahren der SAP-Bereitstellung ziehen wir einige Rückschlüsse, die wir Ihnen wie folgt zusammenfassen möchten:

* Der IOPS-Datenverkehr mit den verschiedenen Datendateien ist nicht immer identisch, da die Datendateien der bestehenden Kundensysteme je nach SAP-Datenbank(en) völlig unterschiedlich dimensioniert sein können. Für die Platzierung der aus den Datendateien gebildeten LUNs hat sich daher eine RAID-Konfiguration gegenüber mehreren Datenträger als wesentlich sinnvoller erwiesen. Es gab Situationen, insbesondere bei Verwendung von Azure Storage Standard, in denen allein die IOPS-Rate das Kontingent einer einzelnen VHD für das DBMS-Transaktionsprotokoll sprengte. In solchen Szenarios empfiehlt sich die Verwendung von Storage Premium oder auch mehrerer Storage Standard-Datenträger mit einem Softwarestripe.

- - -
> ![Windows][Logo_Windows] Windows
>
> * [Best Practices für die Leistung von SQL Server in Azure Virtual Machines][virtual-machines-sql-server-performance-best-practices]
>
> ![Linux][Logo_Linux] Linux
>
> * [Konfigurieren von Software-RAID unter Linux][virtual-machines-linux-configure-raid]
> * [Konfigurieren von LVM auf einem virtuellen Linux-Computer in Azure][virtual-machines-linux-configure-lvm]
> * [Geheime Schlüssel für Azure Storage und E/A-Optimierungen unter Linux](https://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
>
>

- - -
* Der Premiumspeicher zeigt eine wesentlich bessere Leistung, insbesondere bei kritischen Schreibzugriffen auf Transaktionsprotokolle. In SAP-Szenarios, von denen eine produktionsähnliche Leistung erwartet wird, empfiehlt sich die Verwendung von VM-Series, das Azure-Premiumspeicher nutzen kann.

Beachten Sie auch, dass der Datenträger mit dem Betriebssystem und, wie empfohlen wird, mit den SAP-Binärdateien und möglichst auch der Datenbank (Basis-VM) nicht mehr auf 127 GB begrenzt ist. Diese VHD kann nun bis zu 1 TB groß sein. Dies sollte als Speicher für alle erforderlichen Dateien, einschließlich z.B. der SAP-Batchjobprotokolle, ausreichen.

Weitere Empfehlungen und Details, insbesondere für DBMS-VMs, finden Sie im [DBMS-Bereitstellungsleitfaden][dbms-guide].

#### <a name="disk-handling"></a>Behandlung von Datenträgern

In den meisten Szenarien müssen Sie zusätzliche Datenträger erstellen, um die SAP-Datenbank auf der VM bereitzustellen. Über die Anzahl der Datenträger haben wir bereits im Kapitel [VM-/Datenträgerstruktur für SAP-Bereitstellungen][planning-guide-5.5.1] dieses Dokuments gesprochen. Nach der Bereitstellung der Basis-VM können Sie über das Azure-Portal weitere Datenträger anfügen und trennen. Das Anfügen und Trennen der Datenträger kann unabhängig vom Betriebszustand der VM erfolgen, also auch dann, wenn die VM heruntergefahren ist. Im Azure-Portal haben Sie sowohl die Möglichkeit, einen leeren Datenträger anzufügen, als auch einen bereits bestehenden, der zu diesem Zeitpunkt allerdings an keiner anderen VM angefügt sein darf.

**Hinweis**: Datenträger können jeweils immer nur an eine VM angefügt sein.

![Verbinden/Trennen von Datenträgern und dem Azure-Standardspeicher][planning-guide-figure-1400]

Bei der Bereitstellung einer neuen VM können Sie entweder Managed Disks verwenden oder Ihre Datenträger in Azure Storage-Konten platzieren. Wenn Sie Storage Premium verwenden möchten, wird empfohlen, dass Sie Managed Disks verwenden.

Sie müssen sich als Nächstes entscheiden, ob Sie eine neuen und leeren Datenträger oder einen vorhandenen Datenträger verwenden möchten, der bereits hochgeladen wurde und nun an die VM angefügt werden soll.

**WICHTIG:** Verwenden Sie in Kombination mit Azure Storage Standard **KEINE Hostzwischenspeicherung**. Übernehmen Sie daher unverändert den Standardwert NONE der Host-Cache-Einstellung. Bei Azure Storage Premium sollten Sie das Caching für Lesevorgänge aktivieren, wenn Ihre E/As in erster Linie aus Lesezugriffen bestehen, wie es für den E/A-Datenverkehr bei den Datendateien einer Datenbank üblich ist. Im Falle der Transaktionsprotokolldatei einer Datenbank wird von jeder Art des Caching abgeraten.

- - -
> ![Windows][Logo_Windows] Windows
>
> [Anfügen eines Datenträgers im Azure-Portal][virtual-machines-linux-attach-disk-portal]
>
> Falls der VM bereits Datenträger angefügt sind, müssen Sie sich bei der VM anmelden, um den Windows Disk Manager zu öffnen. Wenn die automatische Bereitstellung für angefügte Datenträger nicht aktiviert ist, wie im Kapitel [Festlegen der automatischen Bereitstellung für angefügte Datenträger][planning-guide-5.5.3] empfohlen, muss das neu angefügte Volume online geschaltet und initialisiert werden.
>
> ![Linux][Logo_Linux] Linux
>
> Falls der VM bereits Datenträger angefügt sind, müssen Sie sich bei der VM anmelden und die Datenträger initialisieren, wie in [diesem Artikel][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux] beschrieben wird.
>
>

- - -
Außerdem müssen Sie den neuen Datenträger, wenn er noch leer ist, formatieren. Für die Formatierung gelten insbesondere für DBMS-Daten- und Protokolldateien dieselben Empfehlungen wie für Bare-Metal-Bereitstellungen des DBMS.

Wie bereits im Kapitel [Das Konzept der virtuellen Microsoft Azure-Computer][planning-guide-3.2] erwähnt, kann ein Azure Storage-Konto nicht unbegrenzt Ressourcen in Bezug auf E/A-Volumen, IOPS und Datenvolumen bereitstellen. DBMS-VMs sind in der Regel am meisten davon betroffen. Falls Sie nur wenige VMs mit großem E/A-Volumen bereitstellen müssen, sollten Sie möglichst für jede VM ein eigenes Speicherkonto einrichten, damit Sie innerhalb des Größenlimits für Azure-Speicherkonten bleiben. Andernfalls müssen Sie versuchen, diese VMs auf die verschiedenen Speicherkonten aufzuteilen, ohne die Grenzen einzelner Speicherkonten zu sprengen. Nähere Einzelheiten hierzu finden Sie im [DBMS-Bereitstellungshandbuch][dbms-guide]. Diese Einschränkungen sollten Sie auch bei reinen SAP-Anwendungsserver-VMs oder anderen VMs bedenken, denen vermutlich irgendwann einmal weitere VHDs hinzugefügt werden müssen. Diese Einschränkungen gelten nicht, wenn Sie Managed Disks verwenden. Wenn Sie Storage Premium verwenden möchten, wird empfohlen, dass Sie Managed Disks verwenden.

Ein weiterer wichtiger Punkt, der bei Speicherkonten bedacht werden sollte, ist, ob die VHDs in einem Speicherkonto geografisch repliziert werden. Die Georeplikation wird auf Ebene des Speicherkontos, nicht auf VM-Ebene aktiviert oder deaktiviert. Wenn die Georeplikation aktiviert ist, werden die VHDs eines Speicherkontos an ein anderes Azure-Rechenzentrum innerhalb derselben Region repliziert. Bei Ihrer Entscheidung sollten Sie die folgende Einschränkung berücksichtigen:

Die Georeplikation von Azure erfolgt lokal und individuell für jede VHD einer VM, d.h., die E/As werden nicht in chronologischer Reihenfolge über mehrere VHDs einer VM repliziert. Das bedeutet, dass die VHD mit der Basis-VM sowie alle weiteren VHDs, die an der VM angefügt sind, unabhängig voneinander repliziert werden. Es findet also keine Synchronisierung zwischen den Änderungen innerhalb der verschiedenen VHDs statt. Da die E/As unabhängig von der Reihenfolge repliziert werden, in der sie geschrieben wurden, ist die Georeplikation für Datenbankserver, deren Datenbanken über mehrere VHDs verteilt sind, völlig ungeeignet. Neben DBMS gibt es auch andere Anwendungen, deren Prozesse Daten auf mehreren VHDs schreiben oder bearbeiten, wobei die Reihenfolge der Änderungen erhalten bleiben muss. Falls dies eine Voraussetzung ist, sollte die Georeplikation in Azure nicht aktiviert werden. Wenn Sie wissen, dass Sie für bestimmte VMs Georeplikation benötigen, für andere hingegen nicht, können Sie diese VMs und deren VHDs bereits unter verschiedenen Speicherkonten einrichten, für die Sie dann nach Bedarf Georeplikation aktivieren oder deaktivieren.

#### <a name="17e0d543-7e8c-4160-a7da-dd7117a1ad9d"></a>Festlegen der automatischen Bereitstellung für angefügte Datenträger
- - -
> ![Windows][Logo_Windows] Windows
>
> Für VMs, die aus eigenen Images oder Datenträgern erstellt werden, sollten Sie den Parameter „automount“ überprüfen und gegebenenfalls aktivieren. Wenn die automatische Bereitstellung mit diesem Parameter aktiviert wurde, kann die VM nach einem Neustart oder einer erneuten Bereitstellung in Azure die angefügten (d.h. bereitgestellten) Laufwerke automatisch wieder bereitstellen.
> Für die von Microsoft bereitgestellten Images wird der Parameter in Azure Marketplace aktiviert.
>
> Wie Sie die automatische Bereitstellung aktivieren, können Sie der Dokumentation zum Befehlszeilentool „diskpart.exe“ entnehmen:
>
> * [Befehlszeilenoptionen für DiskPart](https://technet.microsoft.com/library/bb490893.aspx)
> * [Automatische Bereitstellung](https://technet.microsoft.com/library/cc753703.aspx)
>
> Öffnen Sie das Windows-Befehlszeilenfenster als Administrator.
>
> Falls der VM bereits Datenträger angefügt sind, müssen Sie sich bei der VM anmelden, um den Windows Disk Manager zu öffnen. Wenn die automatische Bereitstellung für angefügte Datenträger nicht aktiviert ist, wie im Kapitel [Festlegen der automatischen Bereitstellung für angefügte Datenträger][planning-guide-5.5.3] empfohlen, muss das neu angefügte Volume online geschaltet und initialisiert werden.
>
> ![Linux][Logo_Linux] Linux
>
> Neu angefügte leere Datenträger müssen initialisiert werden, wie in [diesem Artikel][virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux] beschrieben.
> Außerdem müssen neue Datenträger „/etc/fstab“ hinzugefügt werden.
>
>

- - -
### <a name="final-deployment"></a>Die eigentliche Bereitstellung

Das Verfahren der eigentlichen Bereitstellung, insbesondere die Bereitstellung von SAP Extended Monitoring, wird ausführlich und mit einer detaillierten Beschreibung der einzelnen Schritte im [Bereitstellungsleitfaden][deployment-guide] behandelt.

## <a name="accessing-sap-systems-running-within-azure-vms"></a>Zugriff auf SAP-Systeme auf Azure-VMs

Bei Szenarien, in denen Sie mit diesen SAP-Systemen mithilfe der SAP-GUI über das öffentliche Internet eine Verbindung herstellen möchten, müssen die folgenden Verfahren angewendet werden.

Später gehen wir in diesem Dokument noch auf ein zweites gängiges Szenario ein, nämlich den Zugriff auf SAP-Systeme bei standortübergreifenden Bereitstellungen über eine Standort-zu-Standort-Verbindung (VPN-Tunnel) bzw. eine Azure ExpressRoute-Verbindung zwischen den lokalen und den Azure-Systemen.

### <a name="remote-access-to-sap-systems"></a>Remotezugriff auf SAP-Systeme

Der Azure Resource Manager gibt keine Standardendpunkte mehr vor, wie dies bislang im klassischen Modell der Fall war. Alle Ports einer Azure Resource Manager-VM sind geöffnet, solange Folgendes zutrifft:

1. Für das Subnetz oder die Netzwerkschnittstelle ist keine Netzwerksicherheitsgruppe definiert. Der Netzwerkverkehr zu Azure-VMs kann über Netzwerksicherheitsgruppen geschützt werden. Weitere Informationen finden Sie unter [Sicherheitsgruppen][virtual-networks-nsg].
2. Für die Netzwerkschnittstelle ist kein Azure Load Balancer definiert.   

Die Architekturunterschiede zwischen dem klassischen Modell und ARM werden in [diesem Artikel][virtual-machines-azure-resource-manager-architecture] beschrieben.

#### <a name="configuration-of-the-sap-system-and-sap-gui-connectivity-over-the-internet"></a>Konfiguration des SAP-Systems und der SAP-GUI-Konnektivität über das Internet

In diesem Artikel finden Sie ausführliche Informationen zu diesem Thema: <https://blogs.msdn.com/b/saponsqlserver/archive/2014/06/24/sap-gui-connection-closed-when-connecting-to-sap-system-in-azure.aspx>

#### <a name="changing-firewall-settings-within-vm"></a>Ändern der Firewalleinstellungen in der VM

Möglicherweise müssen Sie die Firewall Ihrer VMs konfigurieren, um auf Ihrem SAP-System eingehenden Datenverkehr zuzulassen.

- - -
> ![Windows][Logo_Windows] Windows
>
> Die Windows-Firewall ist auf einer in Azure bereitgestellten VM standardmäßig aktiviert. Sie müssen die Firewall daher so einstellen, dass der SAP-Port geöffnet ist. Andernfalls kann über die SAP-GUI keine Verbindung hergestellt werden.
> Gehen Sie dazu folgendermaßen vor:
>
> * Öffnen Sie „Systemsteuerung“ > „System und Sicherheit“ > „Windows-Firewall“, und klicken Sie dann auf **Erweiterte Einstellungen**.
> * Klicken Sie mit der rechten Maustaste auf „Eingehende Regeln“, und wählen Sie **Neue Regel** aus.
> * Geben Sie im daraufhin geöffneten Assistenten an, dass Sie eine neue **Port**-Regel erstellen möchten.
> * Übernehmen Sie im nächsten Schritt des Assistenten die Einstellung TCP, und geben Sie die Nummer des Ports ein, den Sie öffnen möchten. Da unsere SAP-Instanznummer 00 ist, haben wir hier 3200 eingegeben. Wenn Ihre Instanz eine andere Nummer hat, sollten Sie hier nun den Port öffnen, den Sie zuvor auf Basis Ihrer Instanznummer festgelegt haben.
> * Lassen Sie im nächsten Fenster des Assistenten die Option **Verbindung zulassen** aktiviert.
> * Legen Sie im nächsten Schritt fest, ob die Regel auf die Domäne, auf das private und/oder auf das öffentliche Netzwerk angewendet werden soll. Stellen Sie diese Optionen nach Bedarf ein. Die Regel muss jedoch für das öffentliche Netzwerk gelten, wenn über die SAP-GUI Verbindungen von außerhalb über das öffentliche Netzwerk möglich sein sollen.
> * Benennen Sie die Regel im letzten Schritt des Assistenten, und drücken Sie auf **Beenden**.
>
> Die Regel wird sofort wirksam.
>
> ![Portregeldefinition][planning-guide-figure-1600]
>
> ![Linux][Logo_Linux] Linux
>
> Durch die Linux-Images aus Azure Marketplace wird die iptables-Firewall standardmäßig nicht aktiviert, weshalb die Verbindung mit Ihrem SAP-System funktionieren sollte. Wenn Sie „iptables“ oder eine andere Firewall aktiviert haben, lesen Sie in der Dokumentation zu „iptables“ oder der betreffenden Firewall, wie eingehender TCP-Datenverkehr an Port 32xx zugelassen wird. („xx“ ist hierbei die Systemnummer Ihres SAP-Systems.)
>
>

- - -
#### <a name="security-recommendations"></a>Sicherheitsempfehlungen

Die SAP-GUI stellt zu keiner der aktiven SAP-Instanzen (Port 32xx) eine direkte Verbindung her, sondern verbindet sich zunächst über den Port, der für den SAP-Nachrichtenserverprozess (Port 36xx) geöffnet ist. Früher wurde derselbe Port vom Nachrichtenserver für die interne Kommunikation mit den Anwendungsinstanzen verwendet. Um zu verhindern, dass lokale Anwendungsserver versehentlich mit einem Azure-Nachrichtenserver kommunizieren, können die internen Kommunikationsports geändert werden. Bei Systemen, die von lokalen Systemen geklont wurden, beispielsweise einem Klon der Entwicklung für einen Projekttest, wird dringend empfohlen, die interne Kommunikation zwischen dem SAP-Nachrichtenserver und seinen Anwendungsinstanzen auf einen anderen Port zu verlegen. Dies kann mit dem Standardprofilparameter erfolgen:

> rdisp/msserv_internal
>
>

wie in [Sicherheitseinstellungen für den SAP Message-Server](https://help.sap.com/saphelp_nwpi71/helpdata/en/47/c56a6938fb2d65e10000000a42189c/content.htm) dokumentiert.


### <a name="3e9c3690-da67-421a-bc3f-12c520d99a30"></a>Einzelner virtueller Computer mit SAP NetWeaver-Demo/Schulungsszenario

![Ausführung von SAP-Demo-Systemen mit einem einzelnen virtuellen Computer mit denselben VM-Namen, isoliert in Azure Cloud Services][planning-guide-figure-1700]

In diesem Szenario implementieren wir ein typisches Schulungs-/Demosystem, wobei sich das Material für das vollständige Schulungs-/Demoszenario auf einer einzigen VM befindet. Wir gehen davon aus, dass die Bereitstellung über VM-Imagevorlagen erfolgt. Außerdem gehen wir davon aus, dass mehrere dieser Demo-/Schulungs-VMs bereitgestellt werden müssen, wobei für alle VMs der gleiche Name verwendet wird. Die gesamten Schulungssysteme besitzen keine Verbindung mit Ihren lokalen Ressourcen und stellen das Gegenteil einer Hybridbereitstellung dar.

Es wird vorausgesetzt, dass Sie ein VM-Image erstellt haben, wie in einigen Abschnitten des Kapitels [Vorbereiten virtueller Computer mit SAP für Azure][planning-guide-5.2] in diesem Dokument beschrieben.

Die Abfolge der Ereignisse zur Implementierung dieses Szenarios sieht wie folgt aus:

##### <a name="powershell"></a>PowerShell

* Erstellen einer neuen Ressourcengruppe für jede Schulungs-/Demoumgebung

```powershell
$rgName = "SAPERPDemo1"
New-AzResourceGroup -Name $rgName -Location "North Europe"
```
* Erstellen Sie ein neues Speicherkonto, wenn Sie Managed Disks nicht verwenden möchten.

```powershell
$suffix = Get-Random -Minimum 100000 -Maximum 999999
$account = New-AzStorageAccount -ResourceGroupName $rgName -Name "saperpdemo$suffix" -SkuName Standard_LRS -Kind "Storage" -Location "North Europe"
```

* Erstellen Sie für jede Schulungs-/Demoumgebung ein neues virtuelles Netzwerk, so dass der gleiche Hostname und die gleichen IP-Adressen verwendet werden können. Das virtuelle Netzwerk wird durch eine Netzwerksicherheitsgruppe geschützt, die nur Datenverkehr an Port 3389 (für Remotedesktopzugriff) und Port 22 (für SSH) zulässt.

```powershell
# Create a new Virtual Network
$rdpRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGRDP -Protocol * -SourcePortRange * -DestinationPortRange 3389 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 100
$sshRule = New-AzNetworkSecurityRuleConfig -Name SAPERPDemoNSGSSH -Protocol * -SourcePortRange * -DestinationPortRange 22 -Access Allow -Direction Inbound -SourceAddressPrefix * -DestinationAddressPrefix * -Priority 101
$nsg = New-AzNetworkSecurityGroup -Name SAPERPDemoNSG -ResourceGroupName $rgName -Location  "North Europe" -SecurityRules $rdpRule,$sshRule

$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name Subnet1 -AddressPrefix  10.0.1.0/24 -NetworkSecurityGroup $nsg
$vnet = New-AzVirtualNetwork -Name SAPERPDemoVNet -ResourceGroupName $rgName -Location "North Europe"  -AddressPrefix 10.0.1.0/24 -Subnet $subnetConfig
```

* Erstellen Sie eine neue öffentliche IP-Adresse, über die Zugriff auf die VM über das Internet möglich ist.

```powershell
# Create a public IP address with a DNS name
$pip = New-AzPublicIpAddress -Name SAPERPDemoPIP -ResourceGroupName $rgName -Location "North Europe" -DomainNameLabel $rgName.ToLower() -AllocationMethod Dynamic
```

* Erstellen Sie eine neue Netzwerkschnittstelle für die VM.

```powershell
# Create a new Network Interface
$nic = New-AzNetworkInterface -Name SAPERPDemoNIC -ResourceGroupName $rgName -Location "North Europe" -Subnet $vnet.Subnets[0] -PublicIpAddress $pip
```

* Erstellen Sie eine VM. Für dieses Szenario erhält jede VM den gleichen Namen. Auch die SAP-SIDs der SAP NetWeaver-Instanzen dieser VMs sind identisch. Innerhalb einer Azure-Ressourcengruppe müssen die Namen von VMs eindeutig sein, aber in verschiedenen Azure-Ressourcengruppen können VMs mit identischen Namen ausgeführt werden. Die Standardkonten „Administrator“ (Windows) und „root“ (Linux) sind nicht gültig. Daher muss ein neuer Benutzername mit Administratorrechten samt Kennwort eingerichtet werden. Auch die Größe der VM muss definiert werden.

```powershell
#####
# Create a new virtual machine with an official image from the Azure Marketplace
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

# select image
$vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "SUSE" -Offer "SLES-SAP" -Skus "12-SP1" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "RedHat" -Offer "RHEL" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMSourceImage -VM $vmconfig -PublisherName "Oracle" -Offer "Oracle-Linux" -Skus "7.2" -Version "latest"
# $vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$diskName="osfromimage"
$osDiskUri=$account.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"

$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Windows
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOSDisk -VM $vmconfig -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri <path to VHD that contains the OS image> -Linux
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

```powershell
#####
# Create a new virtual machine with a Managed Disk Image
#####
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vmconfig = New-AzVMConfig -VMName SAPERPDemo -VMSize Standard_D11

$vmconfig = Add-AzVMNetworkInterface -VM $vmconfig -Id $nic.Id

$vmconfig = Set-AzVMSourceImage -VM $vmconfig -Id <Id of Managed Disk Image>
$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Windows -ComputerName "SAPERPDemo" -Credential $cred
#$vmconfig = Set-AzVMOperatingSystem -VM $vmconfig -Linux -ComputerName "SAPERPDemo" -Credential $cred

$vmconfig = Set-AzVMBootDiagnostics -Disable -VM $vmconfig
$vm = New-AzVM -ResourceGroupName $rgName -Location "North Europe" -VM $vmconfig
```

* Fügen Sie optional weitere Datenträger hinzu, und stellen Sie erforderliche Inhalte wieder her. Innerhalb von Azure müssen alle Blobnamen (URLs zu den Blobs) eindeutig sein.

```powershell
# Optional: Attach additional VHD data disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
$dataDiskUri = $account.PrimaryEndpoints.Blob.ToString() + "vhds/datadisk.vhd"
Add-AzVMDataDisk -VM $vm -Name datadisk -VhdUri $dataDiskUri -DiskSizeInGB 1023 -CreateOption empty | Update-AzVM

# Optional: Attach additional Managed Disks
$vm = Get-AzVM -ResourceGroupName $rgName -Name SAPERPDemo
Add-AzVMDataDisk -VM $vm -Name datadisk -DiskSizeInGB 1023 -CreateOption empty -Lun 0 | Update-AzVM
```

##### <a name="cli"></a>Befehlszeilenschnittstelle (CLI)

Der folgende Beispielcode ist für Linux vorgesehen. Für Windows verwenden Sie entweder PowerShell, wie zuvor beschrieben, oder Sie geben in diesem Beispiel „%rgName%“ statt „$rgName“ ein und legen die Umgebungsvariable mit dem Windows-Befehl *set*fest.

* Erstellen einer neuen Ressourcengruppe für jede Schulungs-/Demoumgebung

```
rgName=SAPERPDemo1
rgNameLower=saperpdemo1
az group create --name $rgName --location "North Europe"
```

* Erstellen eines neuen Speicherkontos

```
az storage account create --resource-group $rgName --location "North Europe" --kind Storage --sku Standard_LRS --name $rgNameLower
```

* Erstellen Sie für jede Schulungs-/Demoumgebung ein neues virtuelles Netzwerk, so dass der gleiche Hostname und die gleichen IP-Adressen verwendet werden können. Das virtuelle Netzwerk wird durch eine Netzwerksicherheitsgruppe geschützt, die nur Datenverkehr an Port 3389 (für Remotedesktopzugriff) und Port 22 (für SSH) zulässt.

```
az network nsg create --resource-group $rgName --location "North Europe" --name SAPERPDemoNSG
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGRDP --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 3389 --access Allow --priority 100 --direction Inbound
az network nsg rule create --resource-group $rgName --nsg-name SAPERPDemoNSG --name SAPERPDemoNSGSSH --protocol \* --source-address-prefix \* --source-port-range \* --destination-address-prefix \* --destination-port-range 22 --access Allow --priority 101 --direction Inbound

az network vnet create --resource-group $rgName --name SAPERPDemoVNet --location "North Europe" --address-prefixes 10.0.1.0/24
az network vnet subnet create --resource-group $rgName --vnet-name SAPERPDemoVNet --name Subnet1 --address-prefix 10.0.1.0/24 --network-security-group SAPERPDemoNSG
```

* Erstellen Sie eine neue öffentliche IP-Adresse, über die Zugriff auf die VM über das Internet möglich ist.

```
az network public-ip create --resource-group $rgName --name SAPERPDemoPIP --location "North Europe" --dns-name $rgNameLower --allocation-method Dynamic
```

* Erstellen Sie eine neue Netzwerkschnittstelle für die VM.

```
az network nic create --resource-group $rgName --location "North Europe" --name SAPERPDemoNIC --public-ip-address SAPERPDemoPIP --subnet Subnet1 --vnet-name SAPERPDemoVNet
```

* Erstellen Sie eine VM. Für dieses Szenario erhält jede VM den gleichen Namen. Auch die SAP-SIDs der SAP NetWeaver-Instanzen dieser VMs sind identisch. Innerhalb einer Azure-Ressourcengruppe müssen die Namen von VMs eindeutig sein, aber in verschiedenen Azure-Ressourcengruppen können VMs mit identischen Namen ausgeführt werden. Die Standardkonten „Administrator“ (Windows) und „root“ (Linux) sind nicht gültig. Daher muss ein neuer Benutzername mit Administratorrechten samt Kennwort eingerichtet werden. Auch die Größe der VM muss definiert werden.

```
#####
# Create virtual machines using storage accounts
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --authentication-type password

#####
# Create virtual machines using Managed Disks
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image SUSE:SLES-SAP:12-SP1:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image RedHat:RHEL:7.2:latest --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --image "Oracle:Oracle-Linux:7.2:latest" --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --authentication-type password
```

```
#####
# Create a new virtual machine with a VHD that contains the private image that you want to use
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Windows --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --os-type Linux --admin-username <username> --admin-password <password> --size Standard_D11 --use-unmanaged-disk --storage-account $rgNameLower --storage-container-name vhds --os-disk-name os --image <path to image vhd> --authentication-type password

#####
# Create a new virtual machine with a Managed Disk Image
#####
az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id>
#az vm create --resource-group $rgName --location "North Europe" --name SAPERPDemo --nics SAPERPDemoNIC --admin-username <username> --admin-password <password> --size Standard_DS11_v2 --os-disk-name os --image <managed disk image id> --authentication-type password
```

* Fügen Sie optional weitere Datenträger hinzu, und stellen Sie erforderliche Inhalte wieder her. Innerhalb von Azure müssen alle Blobnamen (URLs zu den Blobs) eindeutig sein.

```
# Optional: Attach additional VHD data disks
az vm unmanaged-disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --vhd-uri https://$rgNameLower.blob.core.windows.net/vhds/data.vhd  --new

# Optional: Attach additional Managed Disks
az vm disk attach --resource-group $rgName --vm-name SAPERPDemo --size-gb 1023 --disk datadisk --new
```

##### <a name="template"></a>Vorlage

Sie können die Beispielvorlagen aus dem GitHub-Repository „azure-quickstart-templates“ verwenden.

* [Simple Linux VM (Einfache Linux-VM)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [Simple Windows VM (Einfache Windows-VM)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [VM from image (VM aus Image)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

### <a name="implement-a-set-of-vms-that-communicate-within-azure"></a>Implementieren mehrerer VMs, die innerhalb von Azure miteinander kommunizieren

Dieses Nicht-Hybridszenario ist ein typisches Szenario für Schulungs- und Demozwecke, wobei die Software für dieses Szenario auf mehrere VMs verteilt ist. Die auf den verschiedenen VMs installierten Komponenten müssen miteinander kommunizieren können. Auch für dieses Szenario ist weder eine lokale Netzwerkkommunikation noch ein standortübergreifendes Szenario erforderlich.

Dieses Szenario ist eine Erweiterung der Installation, die in diesem Dokument im Kapitel [Schulungs-/Demoszenario mit einem einzelnen virtuellen Computer mit SAP NetWeaver][planning-guide-7.1] beschrieben wird. In diesem Szenario werden einer vorhandenen Ressourcengruppe weitere VMs hinzugefügt. Im folgenden Beispiel besteht die Schulungsumgebung aus einer SAP ASCS/SCS-VM, einer VM mit einem DBMS und einer VM mit einer SAP-Anwendungsserverinstanz.

Vor der Einrichtung dieses Szenarios sollten Sie sich wie im vorherigen Szenario Gedanken über die grundlegenden Einstellungen machen.

#### <a name="resource-group-and-virtual-machine-naming"></a>Benennung von Ressourcengruppen und virtuellen Computern

Alle Namen von Ressourcengruppen müssen eindeutig sein. Entwickeln Sie Ihr eigenes Benennungsschema für Ihre Ressourcen, z.B. `<rg-name`>-Suffix.

Der Name des virtuellen Computers muss innerhalb der Ressourcengruppe eindeutig sein.

#### <a name="set-up-network-for-communication-between-the-different-vms"></a>Einrichten des Netzwerks für die Kommunikation zwischen den verschiedenen VMs

![Gruppe von virtuellen Computern in Azure Virtual Network][planning-guide-figure-1900]

Um Namenskonflikte mit Klonen der gleiche Schulungs- bzw Demo-Landschaften zu verhindern, erstellen Sie für jedes Querformat ein Azure Virtual Network. Die DNS-Namensauflösung erfolgt durch Azure. Sie können außerhalb von Azure aber auch einen eigenen DNS-Server konfigurieren (was hier nicht näher erörtert wird). In diesem Szenario konfigurieren wir keinen eigenen DNS-Server. Für alle virtuellen Computer in einem Azure Virtual Network wird die Kommunikation über Hostnamen ermöglicht.

Schulungs- oder Demo-Landschaften können aus folgenden Gründen durch virtuelle Netzwerke anstatt nur durch Ressourcengruppen getrennt werden:

* Die SAP-Landschaft erfordert in der gegebenen Einrichtung ein eigenes AD/OpenLDAP. Zudem muss jede der Landschaften über einen Domänenserver verfügen.  
* Die SAP-Landschaft verfügt in der gegebenen Einrichtung über Komponenten, die feste IP-Adressen erfordern.

Weitere Informationen zu virtuellen Azure-Netzwerken und deren Definition finden Sie [in diesem Artikel][virtual-networks-create-vnet-arm-pportal].

## <a name="deploying-sap-vms-with-corporate-network-connectivity-cross-premises"></a>Bereitstellen von SAP-VMs mit (standortübergreifender) Verbindung zum Unternehmensnetzwerk

Sie führen eine SAP-Landschaft aus und möchten die Bereitstellung auf Bare-Metal-Umgebungen für High-End-DBMS-Server, lokale virtualisierte Umgebungen für Anwendungsschichten und kleinere SAP-Systeme mit 2-Tier-Konfiguration sowie Azure IaaS aufteilen. Die Grundannahme ist, dass SAP-Systeme innerhalb einer SAP-Landschaft ungeachtet der Bereitstellungsform untereinander und mit zahlreichen anderen Softwarekomponenten innerhalb des Unternehmens kommunizieren müssen. Es sollte auch nicht zu Abweichungen aufgrund der Bereitstellungsform für Endbenutzer kommen, die über SAP GUI oder andere Schnittstellen auf SAP-Systeme zugreifen. Diese Bedingung können nur erfüllt werden, wenn wir das lokale Active Directory/OpenLDAP und DNS-Dienste über Site-to-Site-/Multi-Site- oder private Verbindungen wie Azure ExpressRoute erweitern.



### <a name="scenario-of-an-sap-landscape"></a>Szenario einer SAP-Landschaft

Das standortübergreifende oder Hybridszenario kann in etwa wie in der folgenden Grafik beschrieben werden:

![Site-to-Site-Verbindung zwischen lokale und Azure-Ressourcen][planning-guide-figure-2100]

Die oben dargestellte Grafik beschreibt ein Szenario, in dem das lokale

Als Mindestanforderung ist die Verwendung von sicheren Kommunikationsprotokollen wie SSL/TLS für den Browserzugriff oder von VPN-basierten Verbindungen für den Systemzugriff auf die Azure-Dienste erforderlich. Es wird davon ausgegangen, dass Unternehmen die VPN-Verbindung zwischen ihrem Unternehmensnetzwerk und Azure unterschiedlich verwalten. Einige Unternehmen öffnen möglicherweise einfach alle Ports. Einige andere Unternehmen möchten möglicherweise genauer spezifizieren, welche Ports geöffnet werden müssen usw.

In der nachfolgenden Tabelle sind typische SAP-Kommunikationsports aufgeführt. Im Grunde reicht es aus, den SAP-Gatewayport zu öffnen.

<!-- sapms is prefix of a SAP service name and not a spelling error -->

| Dienst | Portname | Beispiel: `<nn`> = 01 | Standardbereich (min.–max.) | Comment |
| --- | --- | --- | --- | --- |
| Verteiler |sapdp`<nn>` siehe * |3201 |3200 - 3299 |SAP Dispatcher, wird von der SAP-Benutzeroberfläche für Windows und Java verwendet |
| Nachrichtenserver |sapms`<sid`> siehe ** |3600 |frei sapms`<anySID`> |sid = SAP-System-ID |
| Gateway |sapgw`<nn`> siehe * |3301 |frei |SAP-Gateway für die CPIC- und RFC-Kommunikation |
| SAP-Router |sapdp99 |3299 |frei |Nach der Installation können einem beliebigen Wert nur CI-Dienstnamen (Central Instance) in /etc/services erneut zugewiesen werden. |

*) nn = SAP-Instanznummer

**) sid = SAP-System-ID

Ausführlichere Informationen zu den erforderlichen Ports für unterschiedliche SAP-Produkte oder -Dienste finden Sie hier: <https://scn.sap.com/docs/DOC-17124>.
Mit diesem Dokument sollten Sie auf dem VPN-Gerät dedizierte Ports für bestimmte SAP-Produkte und -Szenarien öffnen können.

Als weitere Sicherheitsmaßnahme bei der Bereitstellung von virtuellen Computern in einem solchen Szenario können Sie eine [Netzwerksicherheitsgruppe][virtual-networks-nsg] erstellen, um Zugriffsregeln zu definieren.

### <a name="dealing-with-different-virtual-machine-series"></a>Umgang mit anderen virtuellen Computerserien

Microsoft hat viele weitere VM-Typen hinzugefügt, die sich entweder hinsichtlich der Anzahl der vCPUs, des Speichers oder (noch wichtiger) der Hardware unterscheiden, auf der sie ausgeführt werden. Nicht alle diese virtuellen Computer werden von SAP unterstützt (unterstützte virtuelle Computertypen finden Sie im SAP-Hinweis [1928533]). Einige dieser virtuellen Computer werden auf anderen Host-Hardwaregenerationen ausgeführt. Die Bereitstellung dieser Host-Hardwaregenerationen erfolgt in der Granularität einer Azure-Skalierungseinheit. Es können Fällen auftreten, in denen die verschiedenen von Ihnen ausgewählten VM-Typen nicht mit derselben Skalierungseinheit ausgeführt werden können. Eine Verfügbarkeitsgruppe kann nur Skalierungseinheiten für unterschiedliche Hardware umfassen.  Wenn Sie z.B. die SAP-DBMS-Ebene auf einer E64s_v3-VM ausführen, die sich in einer Verfügbarkeitsgruppe zusammen mit der VM befindet, auf der die sekundäre DBMS-Instanz in einer Hochverfügbarkeitskonfiguration ausgeführt wird, können Sie die sekundäre VM nicht einfach anhalten und als VM der M-Serie neu starten, weil Sie etwa ein Upgrade für die VM durchführen möchten. Grund hierfür ist, dass VMs der M-Serie und VMs der Ev3-Serie auf unterschiedlicher Hardware und somit in verschiedenen Skalierungseinheiten ausgeführt werden. Sie müssten eine neue Verfügbarkeitsgruppe erstellen, die sekundäre VM der Ev3-Serie löschen, ohne den Speicher zu löschen, und die VM erneut als VM der M-Serie in der neuen Verfügbarkeitsgruppe bereitstellen.

#### <a name="printing-on-a-local-network-printer-from-sap-instance-in-azure"></a>Drucken auf einem lokalen Netzwerkdrucker von einer SAP-Instanz in Azure

##### <a name="printing-over-tcpip-in-cross-premises-scenario"></a>Drucken über TCP/IP in standortübergreifendem Szenario

Das Einrichten Ihre lokalen TCP/IP-basierten Netzwerkdrucker auf einem virtuellen Azure-Computer erfolgt generell auf die gleiche Weise wie in Ihrem Unternehmensnetzwerk. Es wird vorausgesetzt, dass Sie über einen Site-to-Site-VPN-Tunnel oder eine ExpressRoute-Verbindung verfügen.

- - -
> ![Windows][Logo_Windows] Windows
>
> Gehen Sie dazu folgendermaßen vor:
>
> * Einige Netzwerkdrucker verfügen über einen Konfigurations-Assistenten, mit dem Sie den Drucker auf einem virtuellen Azure-Computer einrichten können. Wenn der Drucker keine Assistenten-Software besitzt, richten Sie ihn manuell ein, indem Sie einen neuen TCP/IP-Druckeranschluss erstellen.
> * Öffnen Sie "Systemsteuerung -> Geräte und Drucker -> Drucker hinzufügen".
> * Wählen Sie "Drucker unter Verwendung einer TCP/IP-Adresse oder eines Hostnamens hinzufügen" aus.
> * Geben Sie die IP-Adresse des Druckers ein.
> * Standard-Druckeranschluss 9100
> * Installieren Sie bei Bedarf den entsprechenden Druckertreiber manuell.
>
> ![Linux][Logo_Linux] Linux
>
> * Befolgen Sie wie für Windows einfach das Standardverfahren zum Installieren eines Netzwerkdruckers.
> * Halten Sie sich beim Hinzufügen eines Druckers an die Anweisungen aus den öffentlich verfügbaren Linux-Handbüchern für [SUSE](https://www.suse.com/documentation/sles-12/book_sle_deployment/data/sec_y2_hw_print.html) oder [Red Hat und Oracle Linux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Deployment_Guide/sec-Printer_Configuration.html).
>
>

- - -
![Drucken im Netzwerk][planning-guide-figure-2200]

##### <a name="host-based-printer-over-smb-shared-printer-in-cross-premises-scenario"></a>Hostbasierter Drucker über SMB (freigegebener Drucker) in standortübergreifendem Szenario

Hostbasierte Drucker sind nicht von Haus aus mit dem Netzwerk kompatibel. Sie können einen hostbasierten Drucker jedoch auf Computern in einem Netzwerk freigegeben, vorausgesetzt, er ist an einen eingeschalteten Computer angeschlossen. Stellen Sie eine Site-to-Site- oder ExpressRoute-Verbindung zu Ihrem Unternehmensnetzwerk her, und geben Sie den lokalen Drucker frei. Das SMB-Protokoll verwendet als Namensdienst NetBIOS anstelle von DNS. Der NetBIOS-Hostname kann vom DNS-Hostnamen abweichen. Normalerweise sind der NetBIOS-Hostname und der DNS-Hostname identisch. Die DNS-Domäne wird im NetBIOS-Namespace nicht unterstützt. Folglich kann der vollständig qualifizierte DNS-Hostname bestehend aus dem DNS-Hostnamen und der DNS-Domäne nicht im NetBIOS-Namespace verwendet werden.

Die Druckerfreigabe wird durch einen eindeutigen Namen im Netzwerk identifiziert:

* Hostname des SMB-Hosts (immer erforderlich)
* Name der Freigabe (immer erforderlich)
* Name der Domäne, wenn sich die Druckerfreigabe nicht in derselben Domäne wie das SAP-System befindet
* Darüber hinaus sind für den Zugriff auf die Druckerfreigabe möglicherweise ein Benutzernamen und ein Kennwort erforderlich.

Gewusst wie:

- - -
> ![Windows][Logo_Windows] Windows
>
> Geben Sie Ihre lokalen Drucker frei.
> Öffnen Sie auf dem virtuellen Azure-Computer Windows Explorer, und geben Sie den Freigabenamen des Druckers ein.
> Ein Druckerinstallations-Assistent führt Sie durch den Installationsprozess.
>
> ![Linux][Logo_Linux] Linux
>
> Es folgen einige Beispiele der Dokumentation zur Konfiguration von Netzwerkdruckern unter Linux einschließlich eines Kapitels zum Drucken unter Linux. Dies funktioniert auf einem virtuellen Azure Linux-Computer auf gleiche Weise, sofern der virtuelle Computer Teil eines VPN ist:
>
> * SLES <https://en.opensuse.org/SDB:Printing_via_SMB_(Samba)_Share_or_Windows_Share>
> * RHEL oder Oracle Linux <https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sec-Printer_Configuration.html#s1-printing-smb-printer>
>
>

- - -
##### <a name="usb-printer-printer-forwarding"></a>USB-Drucker (Druckerweiterleitung)

In Azure kann Benutzern in einer Remotesitzung mithilfe der Remotedesktopdienste kein Zugriff auf ihre lokalen Druckergeräte gewährt werden.

- - -
> ![Windows][Logo_Windows] Windows
>
> Weitere Informationen zum Drucken mit Windows finden Sie hier: <https://technet.microsoft.com/library/jj590748.aspx>.
>
>

- - -
#### <a name="integration-of-sap-azure-systems-into-correction-and-transport-system-tms-in-cross-premises"></a>Integration von SAP Azure-Systemen in das Change and Transport System (CTS) in standortübergreifenden Umgebungen

Das SAP Change and Transport System (CTS) muss für den Export und Import von Transportanforderungen innerhalb Ihrer Landschaft systemübergreifend konfiguriert werden. Es wird davon ausgegangen, dass sich die Entwicklungsinstanzen eines SAP-Systems (DEV) in Azure befinden, während die Qualitätssicherung (QA) sowie die produktiven Systeme (PRD) lokal vorhanden sind. Darüber hinaus wird erwartet, dass ein zentrales Transportverzeichnis vorhanden ist.

##### <a name="configuring-the-transport-domain"></a>Konfigurieren der Transportdomäne

Informationen zum Konfigurieren der Transportdomäne auf dem System, das Sie als Transport Domain Controller festgelegt haben, finden Sie unter [Configuring the Transport Domain Controller](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm)(Konfigurieren des Transport Domain Controllers). Der Systembenutzer TMSADM wird erstellt und das erforderliche RFC-Ziel wird generiert. Sie können diese RFC-Verbindungen mithilfe der Transaktion SM59 überprüfen. Die Hostnamensauflösung muss innerhalb Ihrer gesamten Transportdomäne aktiviert sein.

Gewusst wie:

* In diesem Szenario haben wir das lokale QAS-System als CTS-Domänencontroller festgelegt. Rufen Sie das Transaktions-STMS auf. Das Dialogfeld für TMS wird angezeigt. Das Dialogfeld "Transportdomäne konfigurieren" wird angezeigt. (Dieses Dialogfeld wird nur angezeigt, wenn Sie noch keine Transportdomäne konfiguriert haben.)
* Vergewissern Sie sich unter „SM59 > ABAP-Verbindung > TMSADM@E61.DOMAIN_E61 > Details > Dienstprogramme (M) > Autorisierungstest“, dass der automatisch erstellte Benutzer „TMSADM“ autorisiert ist. Auf dem Startbildschirm des Transaktions-STMS sollte angezeigt werden, dass dieses SAP-System jetzt wie hier dargestellt als Controller der Transportdomäne fungiert:

![Startbildschirm für Transaktions-STMS auf dem Domänencontroller][planning-guide-figure-2300]

#### <a name="including-sap-systems-in-the-transport-domain"></a>Einbeziehen von SAP-Systemen in die Transportdomäne

Schließen Sie mit den folgenden Schritten ein SAP-System in eine Transportdomäne ein:

* Rufen Sie auf dem DEV-System in Azure das Transportsystem (Client 000) und anschließend das Transaktion-STMS auf. Wählen Sie im Dialogfeld die Option "Andere Konfiguration" aus, und fahren Sie mit "Domäne in System einbeziehen" fort. Geben Sie den Domänencontroller als Zielhost an ([Einbeziehen von SAP-Systemen in die Transportdomäne](https://help.sap.com/erp2005_ehp_04/helpdata/en/44/b4a0c17acc11d1899e0000e829fbbd/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm)). Das System wartet jetzt auf die Einbeziehung in die Transportdomäne.
* Aus Sicherheitsgründen müssen Sie anschließend zum Domänencontroller zurückkehren, um Ihre Anforderung zu bestätigen. Wählen Sie auf dem wartenden System "Systemübersicht" und "Genehmigen" aus. Bestätigen Sie die Eingabeaufforderung, woraufhin die Konfiguration verteilt wird.

Dieses SAP-System enthält jetzt die erforderlichen Informationen über alle anderen SAP-Systeme in der Transportdomäne. Gleichzeitig werden die Adressdaten des neuen SAP-Systems an alle anderen SAP-Systeme gesendet, und das SAP-System wird in das Transportprofil des Transportverwaltungsprogramms eingegeben. Überprüfen Sie, ob RFCs und der Zugriff auf das Transportverzeichnis der Domäne funktionieren.

Fahren Sie wie gewohnt mit der Konfiguration Ihres Transportsystems fort. Eine Beschreibung finden Sie in der Dokumentation zum [Change and Transport System](https://help.sap.com/saphelp_nw70ehp3/helpdata/en/48/c4300fca5d581ce10000000a42189c/content.htm?frameset=/en/44/b4a0b47acc11d1899e0000e829fbbd/frameset.htm).

Gewusst wie:

* Stellen Sie sicher, dass das lokale STMS ordnungsgemäß konfiguriert ist.
* Stellen Sie sicher, dass der Hostname des Transport Domain Controllers vom virtuellen Computer in Azure aufgelöst werden kann und umgekehrt.
* Wählen Sie "Transaktions-STMS -> Andere Konfiguration -> System in Domäne einbeziehen".
* Bestätigen Sie die Verbindung im lokale TMS-System.
* Konfigurieren Sie Transportrouten, Gruppen und Ebenen wie gewohnt.

In standortübergreifenden Szenarios mit Standort-zu-Standort-Verbindung kann zwischen dem lokalen System und Azure eine beträchtlich Latenz vorliegen. Wenn wir die Schritte des Objekttransports von der Entwicklung über Testsysteme bis hin zur Produktion verfolgen oder die Anwendung von Transportfunktionen oder Supportpaketen auf die unterschiedlichen Systeme erwägen, fällt auf, dass je nach Speicherort im zentralen Transportverzeichnis bei manchen Systemen eine hohe Latenz beim Lesen und Schreiben von Daten im zentralen Transportverzeichnis auftritt. Die Situation ähnelt der Konfiguration von SAP-Landschaften, in denen die verschiedenen Systeme auf unterschiedliche Rechenzentren verteilt sind, die in beträchtlichem Abstand zueinander liegen.

Um solche Latenzen zu umgehen und schnelle Lese- oder Schreibvorgänge in oder aus dem Transportverzeichnis zu ermöglichen, können Sie zwei STMS-Transportdomänen einrichten (eine für das lokale System und eine für die Systeme in Azure), und diese anschließend verbinden. In diesem Artikel werden die grundlegenden Prinzipien für dieses Konzept im SAP TMS erläutert: <https://help.sap.com/saphelp_me60/helpdata/en/c4/6045377b52253de10000009b38f889/content.htm?frameset=/en/57/38dd924eb711d182bf0000e829fbfe/frameset.htm>.

Gewusst wie:

* Richten Sie mit der Transaktion STMS für jeden Standort (lokal und Azure) eine Transportdomäne ein: <https://help.sap.com/saphelp_nw70ehp3/helpdata/en/44/b4a0b47acc11d1899e0000e829fbbd/content.htm>
* Verknüpfen Sie die Domänen mit einem Domänenlink, und bestätigen Sie die Verknüpfung zwischen den beiden Domänen:
  <https://help.sap.com/saphelp_nw73ehp1/helpdata/en/a3/139838280c4f18e10000009b38f8cf/content.htm>
* Verteilen Sie die Konfiguration auf das verknüpfte System.

#### <a name="rfc-traffic-between-sap-instances-located-in-azure-and-on-premises-cross-premises"></a>RFC-Datenverkehr zwischen SAP-Instanzen in Azure und auf dem lokalen System (standortübergreifend)

Die RFC-Datenübertragung zwischen lokalen und Azure-Systemen muss funktionieren. Rufen Sie zum Einrichten einer Verbindung die Transaktion „SM59“ in einem Quellsystem auf, in dem Sie eine RFC-Verbindung mit dem Zielsystem definieren müssen. Die Konfiguration ähnelt der Standardeinrichtung einer RFC-Verbindung.

Es wird davon ausgegangen, dass sich die VMs im standortübergreifenden Szenario, die SAP-Systeme ausführen, die miteinander kommunizieren müssen, in derselben Domäne befinden. Daher unterscheidet sich die Einrichtung einer RFC-Verbindung zwischen SAP-Systeme nicht von den Einrichtungsschritten und Eingaben in lokalen Szenarios.

#### <a name="accessing-local-fileshares-from-sap-instances-located-in-azure-or-vice-versa"></a>Zugriff auf lokale Dateifreigaben von SAP-Instanzen in Azure oder umgekehrt

SAP-Instanzen in Azure müssen auf Dateifreigaben innerhalb der lokalen Systeme des Unternehmens zugreifen. Darüber hinaus benötigen lokale SAP-Instanzen Zugriff auf Dateifreigaben in Azure. Um die Dateifreigaben zu aktivieren, konfigurieren Sie die Berechtigungen und Freigabeoptionen auf dem lokalen System. Stellen Sie sicher, dass die Ports für die VPN- oder ExpressRoute-Verbindung zwischen Azure und Ihrem Rechenzentrum geöffnet sind.

## <a name="supportability"></a>Unterstützungsmöglichkeiten

### <a name="6f0a47f3-a289-4090-a053-2521618a28c3"></a>Azure-Überwachungslösung für SAP

Um die Überwachung unternehmenskritischer SAP-Systeme in Azure zu aktivieren, ruft das SAP-Überwachungstool SAPOSCOL bzw. der SAP-Host-Agent über eine Azure-Überwachungserweiterung für SAP Daten vom Host des Azure Virtual Machine-Diensts ab. Da bei SAP spezifische Anforderungen hinsichtlich SAP-Anwendungen gelten, hat sich Microsoft entschieden, die erforderliche Funktionalität nicht generisch in Azure zu implementieren. Die Kunden sollten die erforderlichen Überwachungskomponenten und Konfigurationen selbst auf ihren virtuellen Computer in Azure bereitstellen. Die Bereitstellung und das Lebenszyklusmanagement der Überwachungskomponenten wird jedoch zum Großteil von Azure automatisiert.

#### <a name="solution-design"></a>Lösungsentwurf

Die Architektur des Azure-VM-Agenten und des Erweiterungs-Frameworks bildet die Grundlage für die zur SAP-Überwachung entwickelte Lösung. Der Azure-VM-Agent und das Erweiterungs-Framework sollen die Installation der im Azure-VM-Erweiterungskatalog auf einem virtuellen Computer enthaltenen Software-Anwendungen zulassen. Die grundlegende Idee hinter diesem Konzept besteht darin, die Bereitstellung spezieller Funktionen auf einem virtuellen Computer (in Fällen wie der Azure-Überwachungserweiterung für SAP) und die Konfiguration dieser Software zum Zeitpunkt der Bereitstellung zuzulassen.

Der „Azure-VM-Agent“, der die Verarbeitung bestimmter Azure-VM-Erweiterungen auf dem virtuellen Computer ermöglicht, wird bei der Erstellung von virtuellen Computern im Azure-Portal standardmäßig in virtuelle Windows-Computer integriert. Bei SUSE, Red Hat oder Oracle Linux ist der Agent des virtuellen Computers bereits Teil des Azure Marketplace-Images. Wenn ein virtueller Linux-Computer aus dem lokalen System in Azure hochgeladen wird, muss der Agent des virtuellen Computers manuell installiert werden.

Dies sind die grundlegenden Bausteine der Überwachungslösung in Azure für SAP:

![Microsoft Azure-Erweiterungskomponenten][planning-guide-figure-2400]

Wie in dem oben abgebildeten Blockdiagramm dargestellt, wird ein Teil der Überwachungslösung für SAP im Azure-VM-Image und im Azure-Erweiterungskatalog gehostet. Hierbei handelt es sich um ein weltweit repliziertes Repository, das von Azure-Vorgängen verwaltet wird. Es ist die Aufgabe des gemeinsamen SAP/MS-Teams, das für die Zusammenarbeit der Azure-Implementierung von SAP mit Azure-Vorgängen zuständig ist, neue Versionen der Azure-Überwachungserweiterung für SAP zu veröffentlichen.

Wenn Sie einen neuen virtuellen Windows-Computer bereitstellen, wird der Azure-VM-Agent dem virtuellen Computer automatisch hinzugefügt. Dieser Agent hat die Aufgabe, das Laden und die Konfiguration der Azure-Erweiterungen für die Überwachung von SAP NetWeaver-Systemen zu koordinieren. Bei Linux-VMs ist der Azure-VM-Agent bereits Teil des Azure Marketplace-Betriebssystemimages.

Ein Schritt muss jedoch noch vom Kunden ausgeführt werden. Dies ist die Aktivierung und Konfiguration der Leistungserfassung. Der mit der Konfiguration verbundene Vorgang wird durch ein PowerShell-Skript oder einen CLI-Befehl automatisiert. Das PowerShell-Skript kann wie im [Bereitstellungshandbuch][deployment-guide] beschrieben in das Microsoft Azure Script Center heruntergeladen werden.

Die Gesamtarchitektur der Azure-Überwachungslösung für SAP sieht wie folgt aus:

![Azure-Überwachungslösung für SAP NetWeaver][planning-guide-figure-2500]

**Anweisungen zur exakten Vorgehensweise sowie detaillierte Schritte zur Verwendung dieser PowerShell-Cmdlets oder CLI-Befehle während der Bereitstellung finden Sie im [Bereitstellungshandbuch][deployment-guide].**

### <a name="integration-of-azure-located-sap-instance-into-saprouter"></a>Integration von in Azure befindlichen SAP-Instanzen in SAProuter

Die in Azure ausgeführten SAP-Instanzen müssen auch von SAProuter aus zugänglich sein.

![Netzwerkverbindung zu SAP-Router][planning-guide-figure-2600]

Ein SAProuter ermöglicht die TCP/IP-Kommunikation zwischen teilnehmenden Systemen, wenn keine direkte IP-Verbindung vorhanden ist. Dies bietet den Vorteil, dass auf Netzwerkebene keine End-to-End-Verbindung zwischen den Kommunikationspartnern erforderlich ist. Der SAProuter hört standardmäßig Port 3299 ab.
Um SAP-Instanzen über einen SAProuter miteinander zu verbinden, müssen Sie bei jedem Verbindungsversuch die SAProuter-Zeichenfolge und Hostnamen eingeben.

## <a name="sap-netweaver-as-java"></a>SAP NetWeaver AS Java

Bisher wurden in diesem Dokument schwerpunktmäßig SAP NetWeaver im allgemeinen bzw. der SAP NetWeaver ABAP-Stapel behandelt. In diesem kurzen Abschnitt sind spezielle Aspekte des SAP Java-Stapels aufgeführt. Eines der wichtigsten ausschließlich auf SAP NetWeaver Java basierten Anwendungen ist das SAP Enterprise Portal. Andere SAP NetWeaver-basierte Anwendungen wie SAP PI und SAP Solution Manager verwenden sowohl die SAP NetWeaver ABAP- als auch die Java-Stapel. Daher sind sicherlich bestimmten Aspekte im Zusammenhang mit dem SAP NetWeaver Java-Stapel zu berücksichtigen.

### <a name="sap-enterprise-portal"></a>SAP Enterprise Portal

Bei standortübergreifenden Szenarios ähnelt das Einrichten eines SAP-Portals auf einem virtuellen Azure-Computer einer lokalen Installation. Da DNS lokal ausgeführt wird, können die Porteinstellungen der einzelnen Instanzen lokal wie bei einer lokalen Konfiguration festgelegt werden. Die bisher in diesem Dokument beschriebenen Empfehlungen und Einschränkungen gelten im Allgemeinen für eine Anwendung wie das SAP Enterprise Portal oder den SAP NetWeaver Java-Stapel im Allgemeinen.

![Bereitgestelltes SAP-Portal][planning-guide-figure-2700]

In einem speziellen Bereitstellungsszenario einiger Kunden wird das SAP Enterprise Portal direkt über das Internet verfügbar gemacht, während der Host des virtuellen Computers über einen Site-to-Site-VPN-Tunnel oder ExpressRoute mit dem Unternehmensnetzwerk verbunden ist. Bei einem solchen Szenario ist sicherzustellen, dass bestimmte Ports geöffnet und nicht durch eine Firewall oder eine Netzwerksicherheitsgruppe blockiert sind. 

Der anfängliche Portal-URI ist „http(s):`<Portalserver`>:5XX00/irj“. Der Port wird dabei wie von SAP in <https://help.sap.com/saphelp_nw70ehp1/helpdata/de/a2/f9d7fed2adc340ab462ae159d19509/frameset.htm> dokumentiert gebildet.

![Endpunktkonfiguration][planning-guide-figure-2800]

Informationen zum Anpassen der URL und/oder Ports des SAP Enterprise Portal finden Sie in dieser Dokumentation:

* [Change Portal URL (Ändern der Portal-URL)](https://wiki.scn.sap.com/wiki/display/EP/Change+Portal+URL)
* [Change Default port numbers, Portal port numbers (Ändern der Standard- und Portal-Portnummern)](https://wiki.scn.sap.com/wiki/display/NWTech/Change+Default++port+numbers%2C+Portal+port+numbers)

## <a name="high-availability-ha-and-disaster-recovery-dr-for-sap-netweaver-running-on-azure-virtual-machines"></a>Hochverfügbarkeit (High Availability, HA) und Notfallwiederherstellung (Disaster Recovery, DR) für SAP NetWeaver auf virtuellen Azure-Computern

### <a name="definition-of-terminologies"></a>Definition der Terminologie

Der Begriff **Hochverfügbarkeit (High Availability, HA)** bezieht sich generell auf eine Reihe von Technologien, die IT-Störungen minimieren, indem sie die Geschäftskontinuität von IT-Diensten durch redundante, fehlertolerante oder Failover-geschützte Komponenten innerhalb des **gleichen** Rechenzentrums bereitstellen. In unserem Fall erfolgt diese innerhalb einer einzelnen Azure-Region.

Die **Notfallwiederherstellung** (Disaster Recovery, DR) zielt ebenfalls auf die Minimierung von IT-Dienstunterbrechungen und deren Behebung ab, dabei werden jedoch **unterschiedliche** Rechenzentren verwendet, die in der Regel Hunderte von Kilometer voneinander entfernt sind. In unserem Fall erfolgt dies in der Regel zwischen verschiedenen Azure-Regionen innerhalb der geopolitischen Region oder entsprechend Ihrer Konfiguration als Kunde.

### <a name="overview-of-high-availability"></a>Übersicht über Hochverfügbarkeit

Die Erörterung der SAP-Hochverfügbarkeit in Azure kann in zwei Bereiche untergliedert werden:

* **Hochverfügbarkeit der Azure-Infrastruktur** – beispielsweise Hochverfügbarkeit von Compute (VMs), Netzwerk, Speicher usw. und die damit verbundenen Vorteile zur Verbesserung der SAP-Anwendungsverfügbarkeit.
* **Hochverfügbarkeit von SAP-Anwendungen** – beispielsweise die Hochverfügbarkeit von SAP-Software-Komponenten:
  * SAP-Anwendungsserver
  * SAP ASCS/SCS-Instanz
  * DB-Server

Hinzu kommt die Kombinationsmöglichkeit mit der hohen Verfügbarkeit der Azure-Infrastruktur.

Die hohe Verfügbarkeit von SAP in Azure unterscheidet sich in einigen Punkten von der hohen Verfügbarkeit von SAP in einer lokalen physischen oder virtuellen Umgebung. Das folgende Dokument von SAP beschreibt Standardkonfigurationen für Hochverfügbarkeit von SAP in virtualisierten Umgebungen unter Windows: <https://scn.sap.com/docs/DOC-44415>. Es gibt keine in SAPinst integrierte SAP HA-Konfiguration für Linux, wie dies für Windows der Fall ist. Weitere Informationen zur SAP HA für Linux auf lokalen Systemen erhalten Sie hier: <https://scn.sap.com/docs/DOC-8541>.

### <a name="azure-infrastructure-high-availability"></a>Hohe Verfügbarkeit der Azure-Infrastruktur

Aktuell gibt es eine SLA für eine einzelne VM mit 99,9 %. Um einen Eindruck von der Hochverfügbarkeit einer einzelnen VM zu erhalten, können Sie das Produkt aus den verschiedenen verfügbaren Azure-SLAs erstellen: <https://azure.microsoft.com/support/legal/sla/>.

Als Grundlage für die Berechnung werden 30 Tage pro Monat bzw. 43200 Minuten verwendet. Eine Ausfallzeit von 0,05 % entspricht somit 21,6 Minuten. Wie üblich multipliziert sich die Verfügbarkeit der verschiedenen Diensten auf folgende Weise:

(Verfügbarkeitsdienst 1/100) * (Verfügbarkeitsdienst 2/100) * (Verfügbarkeitsdienst 3/100) 

Beispiel:

(99,95/100) * (99,9/100) * (99,9/100) = 0,9975 bzw. eine allgemeine Verfügbarkeit von 99,75 Prozent.

#### <a name="virtual-machine-vm-high-availability"></a>Hochverfügbarkeit von virtuellen Computern (VM)

Es gibt zwei Arten von Azure-Plattformereignissen, die sich auf die Verfügbarkeit von virtuellen Computern auswirken können: geplante und ungeplante Wartung.

* Geplante Wartungsereignisse sind regelmäßige Updates, die von Microsoft für die zugrunde liegende Azure-Plattform ausgeführt werden können, um die gesamte Verfügbarkeit, Leistung und Sicherheit der Plattforminfrastruktur zu verbessern, unter der die virtuellen Computer ausgeführt werden.
* Ungeplante Wartungsereignisse treten auf, wenn die Hardware oder physische Infrastruktur, die dem virtuellen Computer zugrunde liegt, einen Ausfall verursacht. Diese können Ausfälle des lokalen Netzwerks, Datenträgers oder andere Fehler auf Rackebene umfassen. Wenn ein solcher Fehler festgestellt wird, migriert die Azure-Plattform Ihren virtuellen Computer automatisch vom fehlerhaften physischen Server, der den virtuellen Computer hostet, zu einem fehlerfreien physischen Server. Diese Ereignisse treten selten auf, verursachen jedoch u. U. den Neustart des virtuellen Computers.

Weitere Informationen finden Sie in dieser Dokumentation: <https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="azure-storage-redundancy"></a>Azure-Speicher-Redundanz

Die Daten in Ihrem Microsoft Azure Storage-Konto werden stets repliziert, um Beständigkeit und hohe Verfügbarkeit sicherzustellen sowie die Azure Storage-SLA auch bei vorübergehend auftretenden Hardwareausfällen zu erfüllen.

Da Azure Storage standardmäßig drei Images der Daten aufbewahrt, ist auf mehreren Azure-Datenträgern weder RAID5 noch RAID1 erforderlich.

Weitere Informationen finden Sie in diesem Artikel: <https://azure.microsoft.com/documentation/articles/storage-redundancy/>

#### <a name="utilizing-azure-infrastructure-vm-restart-to-achieve-higher-availability-of-sap-applications"></a>Neustart des virtuellen Computers der Azure-Infrastruktur für eine höhere Verfügbarkeit von SAP-Anwendung

Wenn Sie Funktionen wie das Windows Server-Failoverclustering (WSFC) oder oder Pacemaker unter Linux (wird aktuell nur für SLES 12 und höher unterstützt), werden SAP-Systeme mithilfe der Neustartfunktion für virtuelle Azure-Computer vor geplanten und ungeplante Ausfallzeiten der physischen Serverinfrastruktur von Azure und der gesamten zugrundeliegenden Azure-Plattform geschützt.

> [!NOTE]
> Wichtig ist dabei, dass die Neustartfunktion für virtuelle Azure-Computer in erster Linie die virtuellen Computer und NICHT die Anwendungen schützt. Die Neustartfunktion für virtuelle Computer bietet keine Hochverfügbarkeit für SAP-Anwendungen. Aufgrund des gewissen Maßes an Infrastrukturverfügbarkeit, das dadurch bereitgestellt wird, bietet sie jedoch indirekt eine höhere Verfügbarkeit von SAP-Systemen. Es gibt auch keinen SLA für die Zeit, die es dauert, einen virtuellen Computer nach einem geplanten oder ungeplanten Hostausfall neu starten. Aus diesem Grund eignet sich diese Methode der Hochverfügbarkeit nicht für wichtige Komponenten eines SAP-Systems wie (A)SCS oder DBMS.
>
>

Eine weiteres wichtiges Infrastrukturelement für Hochverfügbarkeit ist der Speicher. Die Azure Storage-SLA bietet z.B. 99,9 % Verfügbarkeit. Wenn alle virtuellen Computer samt Datenträgern in einem einzelnen Azure Storage-Konto bereitgestellt werden, würde ein Ausfall von Azure Storage zu einem Ausfall aller in diesem Azure Storage-Konto vorhandenen virtuellen Computer sowie aller SAP-Komponenten innerhalb dieser virtuellen Computer führen.  

Anstatt alle virtuellen Computer in einem einzelnen Azure Storage-Konto bereitzustellen, können Sie auch für jeden virtuellen Speicher dedizierten Speicherkonten verwenden. Sie erhöhen durch die Verwendung mehrerer unabhängiger Azure Storage-Konten die allgemeine Verfügbarkeit der virtuellen Computer und der SAP-Anwendung.

Von Azure verwaltete Datenträger werden automatisch in die Fehlerdomäne der VM platziert, an die sie angehängt sind. Wenn Sie zwei virtuelle Computer in einen Verfügbarkeitssatz platzieren und Managed Disks verwenden, kümmert sich die Plattform um die Verteilung der verwalteten Datenträger in verschiedenen Fehlerdomänen. Wenn Sie Storage Premium verwenden möchten, wird dringend empfohlen, dass Sie Managed Disks verwenden.

Hier ein Beispiel der Architektur eines SAP NetWeaver-Systems, die eine hohe Verfügbarkeit der Azure-Infrastruktur und Speicherkonten nutzt:

![Nutzung der hochverfügbaren Azure-Infrastruktur für eine höhere Verfügbarkeit von SAP-Anwendungen][planning-guide-figure-2900]

Hier ein Beispiel der Architektur eines SAP NetWeaver-Systems, die eine hohe Verfügbarkeit der Azure-Infrastruktur und Managed Disks nutzt:

![Nutzung der hochverfügbaren Azure-Infrastruktur für eine höhere Verfügbarkeit von SAP-Anwendungen][planning-guide-figure-2901]

Für wichtige SAP-Komponenten haben wir bisher Folgendes erreicht:

* Hochverfügbarkeit für SAP-Anwendungsserver (AS)

  SAP-Anwendungsserverinstanzen sind redundante Komponenten. Jede SAP-AS-Instanz wird auf ihrem eigenen virtuellen Computer bereitgestellt, der in einer anderen Fehler- und Upgradedomäne von Azure ausgeführt wird (siehe Kapitel [Fehlerdomänen][planning-guide-3.2.1] und [Upgradedomänen][planning-guide-3.2.2]). Dies wird durch die Verwendung von Azure-Verfügbarkeitsgruppen sichergestellt (siehe Kapitel [Azure-Verfügbarkeitsgruppen][planning-guide-3.2.3]). Infolge von potenziellen geplanten oder ungeplanten Ausfällen einer Fehler- oder Upgradedomäne fallen eine begrenzte Anzahl von virtuellen Computern mit ihren SAP-Instanzen aus.

  Jede SAP-AS-Instanz befindet sich in ihrem eigenen Azure Storage-Konto. Der Ausfall eines Azure Storage-Kontos würde nur zum Ausfall eines virtuellen Speichers in dessen SAP-AS-Instanz führen. Zu berücksichtigen ist jedoch, dass mit einem Azure-Abonnement nur eine begrenzte Anzahl von Azure Storage-Konten verfügbar ist. Um nach dem Neustart des virtuellen Computers den automatischen Start der (A)SCS-Instanz zu gewährleisten, legen Sie den im Kapitel [Verwenden von Autostart für SAP-Instanzen][planning-guide-11.5] beschriebenen Autostart-Parameter im Startprofil der (A)SCS-Instanz fest.
  Weitere Details finden Sie auch im Kapitel [Hochverfügbarkeit für SAP-Anwendungsserver][planning-guide-11.4.1].

  Auch wenn Sie Managed Disks verwenden, werden diese Datenträger auch in Azure Storage-Konten gespeichert und stehen möglicherweise nicht zur Verfügung, wenn der Speicher ausfällt.

* *Höhere* Verfügbarkeit der SAP-(A)SCS-Instanz

  Hier verwenden wir die Neustartfunktion für virtuelle Azure-Computer, um den virtuellen Computer mit der installierten SAP-(A)SCS-Instanz zu schützen. Bei geplanten oder ungeplanten Ausfallzeiten von Azure-Servern werden die virtuellen Computer auf einem anderen verfügbaren Server neu gestartet. Wie bereits erwähnt, schützt die Neustartfunktion für virtuelle Azure-Computer in erster Linie virtuelle Computer und NICHT Anwendungen, in diesem Fall die (A)SCS-Instanz. Mit der Neustartfunktion für VMs erreichen wir indirekt eine höhere Verfügbarkeit der SAP-(A)SCS-Instanz. Um nach dem Neustart des virtuellen Computers den automatischen Start der (A)SCS-Instanz zu gewährleisten, legen Sie den im Kapitel [Verwenden von Autostart für SAP-Instanzen beschriebenen Autostart-Parameter im Startprofil der (A)SCS-Instanz fest][planning-guide-11.5]. Dies bedeutet, dass die (A)SCS-Instanz als einzelne Fehlerquelle (Single Point of Failure, SPOF), die auf einem einzelnen virtuellen Computer ausgeführt wird, der ausschlaggebende Faktor für die Verfügbarkeit der gesamten SAP-Landschaft ist.

* *Höhere* Verfügbarkeit des DBMS-Servers

  Hier verwenden wir ähnlich wie beim Anwendungsfall der SAP-(A)SCS-Instanz die Neustartfunktion für virtuelle Azure-Computer, um den virtuellen Computer mit der installierten DBMS-Software zu schützen. Zudem erreichen wir durch den Neustart des virtuellen Computers eine höhere Verfügbarkeit der DBMS-Software.
  Ein auf einem einzelnen virtuellen Computer ausgeführtes DBMS ist ebenfalls ein SPOF und damit der ausschlaggebende Faktor für die Verfügbarkeit der gesamten SAP-Landschaft.

### <a name="sap-application-high-availability-on-azure-iaas"></a>Hochverfügbarkeit von SAP-Anwendungen in Azure IaaS

Um die Hochverfügbarkeit des gesamten SAP-Systems zu erreichen, müssen alle kritische SAP-Systemkomponenten wie redundante SAP-Anwendungsserver und einzigartige Komponenten (z.B. eine einzelne Fehlerquelle) wie eine SAP-(A)SCS-Instanz und DBMS schützen.

#### <a name="5d9d36f9-9058-435d-8367-5ad05f00de77"></a>Hohe Verfügbarkeit für SAP-Anwendungsserver

Für die SAP-Anwendungsserver/Dialoginstanzen ist keine spezielle Hochverfügbarkeitslösung erforderlich. Die Hochverfügbarkeit wird durch Redundanz erzielt, die durch die verschiedenen virtuellen Computer mehr als genügend gegeben ist. Die virtuellen Computer sollten alle in der gleichen Azure-Verfügbarkeitsgruppe platziert werden, um zu vermeiden, dass sie während geplanter Wartungsausfallzeiten alle zur gleichen Zeit aktualisiert werden. Die grundlegende Funktion, die auf unterschiedlichen Upgrade- und Fehlerdomänen innerhalb einer Azure-Skalierungseinheit basiert, wurde bereits im Kapitel [Upgradedomänen][planning-guide-3.2.2] vorgestellt. Azure-Verfügbarkeitsgruppen wurden im Kapitel [Azure-Verfügbarkeitsgruppen][planning-guide-3.2.3] dieses Dokuments präsentiert.

Die Anzahl der von einer Azure-Verfügbarkeitsgruppe innerhalb einer Azure-Skalierungseinheit verwendbaren Fehler- und Upgradedomänen ist begrenzt. Wenn Sie einer Verfügbarkeitsgruppe eine Reihe von virtuellen Computern hinzufügen, werden früher oder später mehrere virtuelle Computer innerhalb derselben Fehler- oder Upgradedomäne enthalten sein.

Durch die Bereitstellung mehrerer SAP-Anwendungsserverinstanzen auf ihren dedizierten virtuellen Computern entsteht bei fünf Upgradedomänen am Ende das folgende Bild. Die aktuelle maximale Anzahl von Fehler- und Updatedomänen innerhalb einer Verfügbarkeitsgruppe kann sich im Lauf der Zeit ändern:

![Hohe Verfügbarkeit der SAP-Anwendungsserver in Azure][planning-guide-figure-3000]

Weitere Informationen finden Sie in dieser Dokumentation: <https://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability>

#### <a name="high-availability-for-sap-central-services-on-azure"></a>Hochverfügbarkeit für SAP Central Services in Azure

Lesen Sie zum Einstieg die Informationen zur Hochverfügbarkeitsarchitektur von SAP Central Services in Azure im Artikel [Hochverfügbarkeitsarchitektur und -szenarien für SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios). Der Artikel verweist auf ausführlichere Beschreibungen für bestimmte Betriebssysteme.

#### <a name="high-availability-for-the-sap-database-instance"></a>Hochverfügbarkeit für die SAP-Datenbankinstanz

Normalerweise basiert die SAP HA-Einrichtung für DBMS auf zwei virtuellen DBMS-Computern, wobei mit den Hochverfügbarkeitsfunktionen des DBMS Daten aus der aktiven DBMS-Instanz auf dem zweiten virtuellen Computer in eine passive DBMS-Instanz repliziert werden.

Eine Erläuterung der Hochverfügbarkeit sowie der Funktionen zur Notfallwiederherstellung für ein allgemeines oder spezielles DBMS finden Sie im [DBMS-Bereitstellungshandbuch][dbms-guide].

#### <a name="end-to-end-high-availability-for-the-complete-sap-system"></a>End-to-End-Hochverfügbarkeit für das vollständige SAP-System

Hier sind zwei Beispiele für eine vollständige SAP NetWeaver HA-Architektur in Azure – einmal für Windows und einmal für Linux.

Nur nicht verwaltete Datenträger: Bei den nachfolgend erläuterten Konzepten müssen Sie möglicherweise gewisse Kompromisse eingehen, wenn Sie viele SAP-Systeme bereitstellen und die Anzahl der bereitgestellten virtuellen Computer die maximale Anzahl von Speicherkonten pro Abonnement überschreitet. In diesen Fällen müssen die VHDs virtueller Computer in einem Speicherkonto kombiniert werden. Normalerweise würden Sie zu diesem Zweck VHDs der SAP-Anwendungsebene mit virtuellen Computern verschiedener SAP-Systeme kombinieren.  Wir haben zudem in einem Azure Storage-Konto verschiedene VHDs von unterschiedlichen virtuellen DBMS-Computern der verschiedenen SAP-Systemen kombiniert. Beachten Sie dabei die IOPS-Grenzwerte von Azure-Speicherkonten (<https://azure.microsoft.com/documentation/articles/storage-scalability-targets>).


##### <a name="windowslogowindows-ha-on-windows"></a>![Windows][Logo_Windows] Hohe Verfügbarkeit unter Windows

![Hochverfügbare Anwendungsarchitektur für SAP NetWeaver mit SQL Server in Azure IaaS][planning-guide-figure-3200]

Die folgenden Azure-Konstrukte werden für das SAP NetWeaver-System verwendet, um die Auswirkungen von Infrastrukturproblemen und Host-Patches zu minimieren:

* Das vollständige System wird in Azure bereitgestellt (erforderlich – die DBMS-Ebene, die (A)SCS-Instanz und die vollständige Anwendungsebene müssen am selben Ort ausgeführt werden).
* Das vollständige System wird im Rahmen eines Azure-Abonnements (erforderlich) ausgeführt.
* Das vollständige System wird auf einem einzelnen Azure Virtual Network (erforderlich) ausgeführt.
* Die virtuellen Computer eines SAP-Systems können auch dann in drei Verfügbarkeitsgruppen aufgeteilt werden, wenn alle VMs demselben virtuellen Netzwerk angehören.
* Jede Ebene (z.B. DBMS, ASCS, Anwendungsserver) muss eine dedizierte Verfügbarkeitsgruppe verwenden.
* Alle virtuellen Computer, die DBMS-Instanzen eines bestimmten SAP-Systems ausführen, sind in einer Verfügbarkeitsgruppe enthalten. Es wird davon ausgegangen, dass pro System mehrere virtuelle Computer vorhanden sind, auf denen DBMS-Instanzen ausgeführt werden, da systemeigene Hochverfügbarkeitsfunktionen für das DBMS, wie SQL Server AlwaysOn oder Oracle Data Guard, verwendet werden.
* Alle virtuellen Computer, auf denen DBMS-Instanzen ausgeführt werden, verwenden ihr eigenes Speicherkonto. Daten- und Protokolldateien des DBMS werden mit den Hochverfügbarkeitsfunktionen des DBMS, die zur Datensynchronisierung dienen, von einem Speicherkonto zum nächsten repliziert. Bei Nichtverfügbarkeit eines Speicherkontos fällt ein SQL-Windows-Clusterknoten aber nicht der gesamte SQL Server-Dienst aus.
* Alle virtuellen Computer, die (A)SCS-Instanzen eines bestimmten SAP-Systems ausführen, sind in einer Verfügbarkeitsgruppe enthalten. In diesen virtuellen Computern ist zum Schutz der (A)SCS-Instanz das Windows Server-Failovercluster (WSFC) konfiguriert.
* Alle virtuellen Computer, auf denen (A)SCS-Instanzen ausgeführt werden, verwenden ihr eigenes Speicherkonto. (A)SCS-Instanzdateien und globale SAP-Ordner werden mithilfe der SIOS DataKeeper-Replikation von einem Speicherkonto zum nächsten repliziert. Bei Nichtverfügbarkeit eines Speicherkontos fällt ein (A)SCS-Windows-Clusterknoten aber nicht der gesamte (A)SCS-Dienst aus.
* ALLE virtuellen Computer, welche die SAP-Anwendungsserverebene repräsentieren, sind in einer dritten Verfügbarkeitsgruppe enthalten.
* ALLE virtuellen Computer, auf denen SAP-Anwendungsserver ausgeführt werden, verwenden ihr eigenes Speicherkonto. Bei Nichtverfügbarkeit eines Speicherkontos fällt ein SAP-Anwendungsserver aus, während die anderen SAP-Anwendungsserver weiter ausgeführt werden.

Die folgende Abbildung veranschaulicht die gleiche Landschaft mit Managed Disks

![Hochverfügbare Anwendungsarchitektur für SAP NetWeaver mit SQL Server in Azure IaaS][planning-guide-figure-3201]

##### <a name="linuxlogolinux-ha-on-linux"></a>![Linux][Logo_Linux] Hohe Verfügbarkeit unter Linux

Die Architektur für SAP HA unter Linux in Azure ist im Grunde mit der oben für Windows beschriebenen Architektur identisch. Eine Liste unterstützter Hochverfügbarkeitslösungen finden Sie im SAP-Hinweis [1928533].

### <a name="4e165b58-74ca-474f-a7f4-5e695a93204f"></a>Verwenden von Autostart für SAP-Instanzen

SAP bot diese Funktion zum Starten von SAP-Instanzen unmittelbar nach dem Start des Betriebssystems auf dem virtuellen Computer an. Die genauen Schritte sind im SAP Knowledge Base-Artikel [1909114] dokumentiert. SAP rät jetzt jedoch von der Verwendung dieser Einstellung ab, da die Reihenfolge der Instanzneustarts nicht gesteuert wird, in der Annahme, dass mehrere virtuelle Computer betroffen sind oder mehrere Instanzen pro virtuellem Computer ausgeführt wurden. Bei einem typischen Azure-Szenario mit einer SAP-Anwendungsserverinstanz auf einer VM, bei der eine einzelne VM neu gestartet wird, ist der Autostart-Parameter nicht wichtig und kann durch Hinzufügen des folgenden Parameters aktiviert werden:

    Autostart = 1

In das Startprofil der SAP ABAP- und/oder Java-Instanz.

> [!NOTE]
> Der Autostart-Parameter kann auch Nachteile haben. Der Parameter löst den Start einer SAP ABAP- oder Java-Instanz aus, wenn der mit der Instanz verbundene Windows-/Linux-Dienst gestartet wird. Das erfolgt in jedem Fall beim Hochfahren des Betriebssystems. Neustarts von SAP-Diensten sind jedoch auch für die Lebenszyklusmanagementfunktionen der SAP-Software wie SUM oder andere Updates bzw. Upgrades gängig. Bei diesen Funktionen wird kein automatischer Neustart einer Instanz erwartet. Aus diesem Grund sollte der Autostart-Parameter vor dem Ausführen dieser Aufgaben deaktiviert werden. Die Autostart-Parameter sollte auch nicht für geclusterte SAP-Instanzen wie ASCS/SCS/CI verwendet werden.
>
>

Weitere Informationen zur Autostartfunktion für SAP-Instanzen finden Sie hier:

* [Start/Stop SAP along with your Unix Server Start/Stop (Starten/Beenden von SAP zusammen mit Ihrem Unix-Server)](https://scn.sap.com/community/unix/blog/2012/08/07/startstop-sap-along-with-your-unix-server-startstop)
* [Starting and Stopping SAP NetWeaver Management Agents (Starten und Beenden von SAP NetWeaver-Verwaltungsagents)](https://help.sap.com/saphelp_nwpi711/helpdata/en/49/9a15525b20423ee10000000a421938/content.htm)
* [How to enable auto Start of HANA Database (Aktivieren des automatischen Starts der HANA-Datenbank)](http://www.freehanatutorials.com/2012/10/how-to-enable-auto-start-of-hana.html)

### <a name="larger-3-tier-sap-systems"></a>Größere SAP-Systeme mit drei Ebenen
Die Aspekte der hohen Verfügbarkeit von SAP-Konfigurationen mit 3 Ebenen wurden bereits in früheren Abschnitten erläutert. Was ist jedoch mit Systemen, auf denen die Anforderungen der DBMS-Server zu groß sind, um die in Azure zu platzieren, während die SAP-Anwendungsebene in Azure bereitgestellt werden könnte?

#### <a name="location-of-3-tier-sap-configurations"></a>Speicherort von SAP-Konfigurationen mit drei Ebenen
Weder die Anwendungsebene an sich noch die Anwendung und die DBMS-Ebene können auf das lokale System und Azure aufgeteilt werden. Ein SAP-System wird entweder vollständig lokal oder in Azure bereitgestellt. Zudem ist es nicht möglich, einige der Anwendungsserver lokal und andere in Azure auszuführen. Das ist der Ausgangspunkt der Erörterung. Des Weiteren können die DBMS-Komponenten eines SAP-Systems und die SAP-Server-Anwendungsserverebene nicht in zwei verschiedenen Azure-Regionen bereitgestellt werden. Beispielsweise DBMS in „USA, Westen“ und die SAP-Anwendungsschicht in „USA, Mitte“. Diese Konfigurationen werden aufgrund der Empfindlichkeit der SAP NetWeaver-Architektur gegenüber Latenzen nicht unterstützt.

Rechenzentrumspartner haben jedoch im vergangenen Jahr weitere Standorte (Kolokationen) für Azure-Regionen entwickelt. Diese Kolokationen befinden sich häufig in der Nähe der physischen Azure-Rechenzentren innerhalb einer Azure-Region. Durch die kurze Entfernung und die Verbindung von Ressourcen in der Kolokation über ExpressRoute mit Azure kann die Latenz auf unter 2 ms sinken. In diesen Fällen kann die DBMS-Ebene (einschließlich SAN/NAS) an einer solchen Kolokation und die SAP-Anwendungsebene in Azure platziert werden. Weitere Informationen finden Sie unter [Was ist SAP HANA in Azure (große Instanzen)?](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). 

### <a name="offline-backup-of-sap-systems"></a>Offline-Sicherung von SAP-Systemen
Je nach SAP-Konfiguration (2 oder 3 Ebenen) ist möglicherweise eine Sicherung erforderlich. Dabei sollte der Inhalt des virtuellen Computers an sich sowie die Datenbank gesichert werden. Die DBMS-bezogene Sicherungen sollten mit Datenbankmethoden durchgeführt werden. Eine ausführliche Beschreibung der unterschiedlichen Datenbanken finden Sie im [DBMS-Handbuch][dbms-guide]. Andererseits können SAP-Daten (einschließlich des Datenbankinhalts) auch wie in diesem Abschnitt beschrieben offline gesichert werden. Die Online-Sicherung wird im nächsten Abschnitt erläutert.

Für die Offline-Sicherung muss der virtuelle Computer im Grunde über das Azure-Portal heruntergefahren werden. Anschließend sollte ein Kopie des VM-Basisdatenträgers einschließlich aller mit dem virtuellen Computer verbundenen Datenträger erstellt werden. Dadurch wird ein Image des virtuellen Computers und des damit verbundenen Datenträgers zu einem bestimmten Zeitpunkt erstellt. Es wird empfohlen, die Sicherungen in ein anderes Azure Storage-Konto zu kopieren. Gehen Sie hierfür wie im Kapitel [Kopieren von Datenträgern zwischen Azure Storage-Konten][planning-guide-5.4.2] dieses Dokuments vor.
Das Herunterfahren kann nicht nur über das Azure-Portal, sondern auch über Powershell oder die Befehlszeilenschnittstelle erfolgen. Weitere Informationen finden Sie hier: <https://azure.microsoft.com/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/>

Bei einer Wiederherstellung dieses Zustands werden sowohl der virtuelle Basiscomputer als auch dessen Originaldatenträger sowie die hinzugefügten Datenträger gelöscht. Die gespeicherten Datenträger werden in das ursprüngliche Storage-Konto oder in die Ressourcengruppe für verwaltete Datenträger zurück kopiert. Anschließend wird das System erneut bereitgestellt.
Im folgenden Artikel wird anhand eines Beispiels veranschaulicht, wie dieser Prozess in PowerShell geschrieben wird: <http://www.westerndevs.com/azure-snapshots/>

Wichtig ist, dass Sie eine neue SAP-Lizenz installieren, da beim Wiederherstellen einer VM-Sicherung wie oben beschrieben ein neuer Hardwareschlüssel erstellt wird.

### <a name="online-backup-of-an-sap-system"></a>Online-Sicherung von SAP-Systemen

Das DBMS wird mit speziellen DBMS-Methoden gesichert. Eine Beschreibung finden Sie im [DBMS-Handbuch][dbms-guide].

Andere virtuelle Computer im SAP-System können mit der Sicherungsfunktion für virtuelle Azure-Computer gesichert werden. Die Sicherung virtueller Azure-Computer ist mittlerweile eine Standardmethode zum Sichern einer vollständigen VM in Azure. Azure Backup speichert die Sicherungen in Azure und ermöglicht eine erneute Wiederherstellung von einem virtuellen Computer.

> [!NOTE]
> Seit Dezember 2015 wird bei der Sicherung virtueller Computer NICHT mehr die eindeutige VM-ID beibehalten, die für die SAP-Lizenzierung erforderlich ist. Dies bedeutet, dass bei der Wiederherstellung von einer VM-Sicherung ein neuer SAP-Lizenzschlüssel installiert werden muss. Der wiederhergestellte virtuelle Computer gilt als neue VM und nicht als Ersatz der zuvor gespeicherten VM.
>
> ![Windows][Logo_Windows] Windows
>
> Theoretisch können virtuelle Computer, auf denen Datenbanken ausgeführt werden, konsistent gesichert werden, wenn die DBMS-Systeme den Volumeschattenkopie-Dienst (Volume Shadow Copy Service <https://msdn.microsoft.com/library/windows/desktop/bb968832(v=vs.85).aspx>) von Windows unterstützen, wie es etwa bei SQL Server der Fall ist.
> Bedenken Sie jedoch, dass auf Basis der Sicherungen virtueller Azure-Computer keine Zeitpunktwiederherstellung von Datenbanken möglich ist. Daher wird empfohlen, die Sicherung von Datenbanken mit DBMS-Funktionen durchzuführen und nicht die Sicherung virtueller Azure-Computer zu verwenden.
>
> Hier können Sie sich mit der Sicherung virtueller Azure-Computer vertraut machen: <https://docs.microsoft.com/azure/backup/backup-azure-vms>.
>
> Zudem können Sie Datenbanken mit einer Kombination aus Microsoft Data Protection Manager, der auf einem virtuellen Azure-Computer installiert ist, und der Azure-Sicherung sichern/wiederherstellen. Weitere Informationen finden Sie hier: <https://docs.microsoft.com/azure/backup/backup-azure-dpm-introduction>.  
>
> ![Linux][Logo_Linux] Linux
>
> Es gibt unter Linux keine Entsprechung zu Windows VSS. Daher sind nur dateikonsistente Sicherungen aber keine anwendungskonsistenten Sicherungen möglich. Die SAP-DBMS-Sicherung sollte mit DBMS-Funktionen erfolgen. Das Dateisystem mit den SAP-bezogenen Daten kann beispielsweise mit „tar“ gespeichert werden. Informationen dazu finden Sie hier <https://help.sap.com/saphelp_nw70ehp2/helpdata/en/d3/c0da3ccbb04d35b186041ba6ac301f/content.htm>
>
>

### <a name="azure-as-dr-site-for-production-sap-landscapes"></a>Azure als DR-Standort für die SAP-Produktionslandschaften

Seit Mitte 2014 ermöglichen Erweiterungen verschiedener Komponenten zu Hyper-V, System Center und Azure die Verwendung von Azure als DR-Standort für lokal auf der Basis von Hyper-V ausgeführte VMs.

Einen Blog mit Informationen zum Bereitstellen dieser Lösung finden Sie hier: <https://blogs.msdn.com/b/saponsqlserver/archive/2014/11/19/protecting-sap-solutions-with-azure-site-recovery.aspx>.

## <a name="summary"></a>Zusammenfassung

Hier die wichtigsten Punkte der Hochverfügbarkeit für SAP-Systeme in Azure:

* Derzeit können die einzelnen SAP-Fehlerquelle nicht exakt auf die gleiche Weise wie in lokalen Bereitstellungen geschützt werden. Der Grund dafür ist, dass Cluster mit freigegebenen Datenträgern noch nicht ohne die Verwendung von Drittanbietersoftware in Azur erstellt werden können.
* Für die DBMS-Ebene müssen DBMS-Funktionen verwendet werden, die von der Clustertechnologie für freigegebene Datenträger unabhängig sind. Details finden Sie im [DBMS-Handbuch][dbms-guide].
* Um die Auswirkungen von Problemen in Fehlerdomänen innerhalb der Azure-Infrastruktur oder bei der Hostwartung zu minimieren, sollten Sie Azure-Verfügbarkeitsgruppen verwenden:
  * Es wird empfohlen, eine Verfügbarkeitsgruppe für die SAP-Anwendungsebene zu verwenden.
  * Es wird empfohlen, eine separate Verfügbarkeitsgruppe für die SAP-DBMS-Ebene zu verwenden.
  * Es wird NICHT empfohlen, dieselbe Verfügbarkeitsgruppe auf virtuelle Computer unterschiedlicher SAP-Systeme anzuwenden.
  * Es wird empfohlen, dass Sie Premium Managed Disks verwenden.
* Informationen zum Sichern der SAP-DBMS-Ebene finden Sie im [DBMS-Leitfaden][dbms-guide].
* Das Sichern von SAP-Dialoginstanzen ist nicht sinnvoll, da es bei einfachen Dialoginstanzen in der Regel schneller geht, sie erneut bereitzustellen.
* Es ist empfehlenswert, den virtuellen Computer, der das globale Verzeichnis des SAP-Systems enthält, einschließlich aller Profile der unterschiedlichen Instanzen zu sichern. Dies sollte beispielsweise mit der Windows-Sicherung oder unter Linux mit „tar“ durchgeführt werden. Aufgrund von Unterschieden zwischen Windows Server 2008 (R2) und Windows Server 2012 (R2) ist die Sicherung mit den neueren Windows Server-Releases einfacher. Es wird daher empfohlen, Windows Server 2012 (R2) als Windows-Gastbetriebssystem auszuführen.

## <a name="next-steps"></a>Nächste Schritte
Lesen Sie die folgenden Artikel:

- [Azure Virtual Machines – Bereitstellung für SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Überlegungen zur Azure Virtual Machines-DBMS-Bereitstellung für SAP-Workloads](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SAP HANA-Infrastrukturkonfigurationen und -Vorgänge in Azure](https://docs.microsoft.com/
- azure/virtual-machines/workloads/sap/hana-vm-operations)
