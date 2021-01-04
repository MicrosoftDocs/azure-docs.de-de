---
title: Azure Virtual Machines – SQL Server-DBMS-Bereitstellung für SAP-Workload | Microsoft-Dokumentation
description: Azure Virtual Machines – SQL Server-DBMS-Bereitstellung für SAP-Workload
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: Azure, SQL Server, SAP, Always On
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ef00f33040d30795ae5374b74d0d7a6e9b6e2156
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957672"
---
# <a name="sql-server-azure-virtual-machines-dbms-deployment-for-sap-netweaver"></a>Azure Virtual Machines – SQL Server-DBMS-Bereitstellung für SAP NetWeaver

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
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
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide_general.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms_guide_sqlserver.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c 
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

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

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

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
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd 
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
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
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/eresources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md




Dieses Dokument behandelt verschiedene wichtige Themen für die Bereitstellung von SQL Server für SAP-Workload in Azure IaaS. Es ist sinnvoll, im Vorfeld das Dokument [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](./dbms_guide_general.md) und andere Artikel der [Azure-Dokumentation für SAP-Workload](./get-started.md) zu lesen. 



> [!IMPORTANT]
> Dieser Artikel berücksichtigt die Windows-Version auf SQL Server. SAP unterstützt die Linux-Version von SQL Server mit keiner SAP-Software. Der Artikel geht nicht auf die Microsoft Azure SQL-Datenbank ein. Dabei handelt es sich um ein PaaS-Angebot (Platform as a Service) von Microsoft Azure Platform. In diesem Dokument geht es um das Ausführen des bekannten SQL Server-Produkts für lokale Bereitstellungen auf virtuellen Azure-Computern, wobei die IaaS-Funktion (Infrastructure as a Service) von Azure genutzt wird. Die Datenbankfunktionen und -features für diese beiden Angebote unterscheiden sich und dürfen nicht verwechselt werden. Weitere Informationen: <https://azure.microsoft.com/services/sql-database/>
> 
>

Generell wird empfohlen, die neuesten SQL Server-Releases zu verwenden, um SAP-Workload in Azure IaaS auszuführen. Die neuesten SQL Server-Releases bieten eine bessere Integration in einige Azure-Dienste und -Funktionen. Außerdem umfassen sie Änderungen, die Abläufe in einer Azure IaaS-Infrastruktur optimieren.

Es wird empfohlen, den Artikel [Was ist SQL Server auf Azure Virtual Machines (Windows)?][https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview ] zu lesen, bevor Sie fortfahren.

In den folgenden Abschnitten werden Inhalte aus dieser Dokumentation unter dem oben genannten Link zusammengefasst und erwähnt. Außerdem werden Besonderheiten in Bezug auf SAP erwähnt, und einige Konzepte werden ausführlicher beschrieben. Es wird jedoch dringend empfohlen, zuerst die oben genannte Dokumentation durchzuarbeiten, bevor Sie die SQL Server-spezifische Dokumentation lesen.

Einige spezifische Informationen für SQL Server in IaaS sollten Sie vor dem Fortfahren kennen:

* **SQL-Versionsunterstützung**: Für SAP-Kunden wird SQL Server 2008 R2 und höher auf virtuellen Microsoft Azure-Computern unterstützt. Frühere Versionen werden nicht unterstützt. Ausführliche Informationen finden Sie in der allgemeinen [Supporterklärung](https://support.microsoft.com/kb/956893). Generell wird SQL Server 2008 auch von Microsoft unterstützt. Aufgrund spezieller Funktionen für SAP, die in SQL Server 2008 R2 eingeführt wurden, ist die Mindestversion für SAP jedoch SQL Server 2008 R2. Generell wird empfohlen, die neuesten SQL Server-Releases zu verwenden, um SAP-Workload in Azure IaaS auszuführen. Die neuesten SQL Server-Releases bieten eine bessere Integration in einige Azure-Dienste und -Funktionen. Außerdem umfassen sie Änderungen, die Abläufe in einer Azure IaaS-Infrastruktur optimieren. Deswegen bezieht sich dieses Dokument nur auf SQL Server 2016 und SQL Server 2017.
* **SQL-Leistung**: Unter Microsoft Azure gehostete virtuelle Computer erbringen im Vergleich zu anderen Virtualisierungsangeboten in der öffentlichen Cloud eine gute Leistung. Die einzelnen Ergebnisse können allerdings variieren. Weitere Informationen finden Sie im Artikel [Bewährte Methoden zur Leistung für SQL Server auf virtuellen Azure-Computern](../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md).
* **Verwenden von Images aus Azure Marketplace**: Die schnellste Möglichkeit zum Bereitstellen eines neuen virtuellen Microsoft Azure-Computers besteht darin, ein Image aus dem Azure Marketplace zu verwenden. Einige Images in Azure Marketplace enthalten die neuesten SQL Server-Releases. Die Images, bei denen SQL Server bereits installiert ist, können nicht sofort für SAP NetWeaver-Anwendungen verwendet werden. Dies liegt daran, dass innerhalb dieser Images die standardmäßige SQL Server-Sortierung und nicht die für SAP NetWeaver-Systeme erforderliche Sortierung installiert ist. Um solche Images zu verwenden, lesen Sie sich die Schritte im Kapitel [Verwenden eines SQL Server-Images aus dem Microsoft Azure Marketplace][dbms-guide-5.6] durch. 


## <a name="recommendations-on-vmvhd-structure-for-sap-related-sql-server-deployments"></a>Empfehlungen für die VM-/VHD-Struktur für SAP-bezogene SQL Server-Bereitstellungen
In Übereinstimmung mit der allgemeinen Beschreibung, sollten Betriebssystem, die ausführbaren SQL Server-Dateien und bei zweischichtigen SAP-Systemen die ausführbaren SAP-Dateien auf getrennten Azure-Datenträgern gespeichert oder installiert werden. Üblicherweise werden die meisten SQL Server-Systemdatenbanken durch die SAP NetWeaver-Workload nicht stark genutzt. Dennoch sollten sich die Systemdatenbanken von SQL Server (master, msdb und model) zusammen mit den anderen SQL Server-Verzeichnissen auf einem separaten Azure-Datenträger befinden. Die tempdb von SQL Server sollte sich entweder auf dem nicht persistenten Laufwerk „D:\“ oder auf einem separaten Datenträger befinden.


* Für alle SAP-zertifizierten VM-Typen (siehe SAP-Hinweis [1928533]), außer für VMs der A-Serie, können tempdb-Daten- und Protokolldateien auf dem nicht permanenten Laufwerk D:\ platziert werden. 
* Bei älteren SQL Server-Releases, bei denen SQL Server die tempdb standardmäßig mit einer Datendatei installiert, sollten Sie mehrere tempdb-Datendateien verwenden. Beachten Sie, dass das Laufwerkvolumen bei „D:\“ je nach VM-Typ variieren kann. Weitere Informationen zur genauen Größe des Laufwerks „D:\“ verschiedener virtueller Computer finden Sie im Artikel [Größen für virtuelle Windows-Computer in Azure](../../sizes.md).

Mithilfe dieser Konfigurationen kann die tempdb mehr Speicherplatz und – noch wichtiger – mehr IOPS und Speicherbandbreite nutzen, als auf dem Systemlaufwerk verfügbar ist. Außerdem bietet das nicht persistente Laufwerk „D:\“ eine bessere E/A-Latenz und einen höheren E/A-Durchsatz (mit Ausnahme von VMs der A-Serie). Um die richtige tempdb-Größe zu bestimmen, können Sie die tempdb-Größen auf vorhandenen Systemen überprüfen. 

>[!NOTE]
> Falls Sie tempdb-Datendateien und Protokolldateien in einem Ordner auf dem Laufwerk „D:\“ ablegen, müssen Sie sicherstellen, dass der Ordner nach einem Neustart der VM vorhanden ist. Da das Laufwerk „D:\“ nach dem Neustart einer VM neu initialisiert wird, werden alle Datei- und Verzeichnisstrukturen gelöscht. Eine Möglichkeit, eventuelle Verzeichnisstrukturen auf Laufwerk „D:\“ vor dem Start des SQL Server-Diensts erneut zu erstellen, finden Sie in [diesem Artikel](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/).

Eine VM-Konfiguration, die SQL Server mit einer SAP-Datenbank ausführt und bei der die tempdb-Daten und die tempdb-Protokolldatei auf dem Laufwerk „D:\“ platziert sind, würde wie folgt aussehen:

![Diagramm einer einfachen VM-Datenträgerkonfiguration für SQL Server](./media/dbms_sqlserver_deployment_guide/Simple_disk_structure.PNG)

Das obige Diagramm zeigt ein einfaches Szenario. Wie in Artikel [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md) erläutert, sind der Azure Storage-Typ sowie die Anzahl und Größe von Datenträgern von verschiedenen Faktoren abhängig. Generell wird Folgendes empfohlen:

- Verwenden Sie für die SQL Server-Datendateien ein großes Volume. Diese Konfiguration ist darauf zurückzuführen, dass es in der Praxis zahlreiche SAP-Datenbanken mit unterschiedlich großen Datenbankdateien mit unterschiedlichen E/A-Workloads gibt.
- Verwenden Sie das Laufwerk „D:\“ für tempdb, solange die Leistung gut genug ist. Wenn die Leistung des Gesamtworkloads durch die tempdb auf Laufwerk „D:\“ eingeschränkt wird, verschieben Sie die tempdb auf einen separaten Azure Storage Premium- oder Ultra-Datenträger, wie in [diesem Artikel](../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) empfohlen.


### <a name="special-for-m-series-vms"></a>Besonderheiten von VMs der M-Serie
Bei Azure-VMs der M-Serie kann die Latenz beim Schreiben in das Transaktionsprotokoll im Vergleich zu Azure Storage Premium um Faktoren reduziert werden, wenn Azure-Schreibbeschleunigung verwendet wird. Daher sollten Sie Azure-Schreibbeschleunigung für die VHD(s) bereitstellen, die das Volume für das SQL Server-Transaktionsprotokoll bilden. Details finden Sie im Dokument [Schreibbeschleunigung](../../how-to-enable-write-accelerator.md).
  

### <a name="formatting-the-disks"></a>Formatieren der Datenträger
Für SQL Server sollte die NTFS-Blockgröße für Datenträger, die SQL Server-Daten und -Protokolldateien enthalten, 64 KB betragen. Es ist nicht erforderlich, Laufwerk „D:\“ zu formatieren. Dieses Laufwerk ist bereits vorformatiert.

Um sicherzustellen, dass die Wiederherstellung oder Erstellung von Datenbanken die Datendateien nicht initialisiert, indem der Inhalt der Dateien gelöscht wird, sollten Sie überprüfen, ob der Benutzerkontext, in dem der SQL Server-Dienst ausgeführt wird, eine bestimmte Berechtigung hat. Normalerweise verfügen die Benutzer in der Windows-Administratorengruppe über diese Berechtigungen. Wenn der SQL Server-Dienst im Benutzerkontext eines Benutzers ausgeführt wird, der kein Windows-Administrator ist, müssen Sie diesem Benutzer das Benutzerrecht **Volumenwartungsaufgaben durchführen** zuweisen.  Ausführliche Informationen finden Sie im Microsoft Knowledge Base-Artikel: <https://support.microsoft.com/kb/2574695>

### <a name="impact-of-database-compression"></a>Auswirkungen der Datenbankkomprimierung
Bei Konfigurationen, bei denen die E/A-Bandbreite zum begrenzenden Faktor werden könnte, sollte jede Möglichkeit ergriffen werden, die IOPS zu senken. Dadurch kann die ausführbare Workload in einem IaaS-Szenario wie Azure besser verteilt werden. Falls noch nicht erfolgt, wird sowohl von SAP als auch von Microsoft empfohlen, vor dem Hochladen vorhandener SAP-Datenbanken in Azure die SQL Server-Seitenkomprimierung anzuwenden.

Der Empfehlung zur Durchführung der Datenbankkomprimierung vor dem Hochladen in Azure liegen zwei Gründe zugrunde:

* Die hochzuladende Datenmenge ist geringer.
* Die Ausführungsdauer der Komprimierung ist geringer (unter der Annahme, dass lokal leistungsfähigere Hardware mit mehr CPUs, einer höheren E/A-Bandbreite oder einer geringeren E/A-Latenz zur Verfügung steht).
* Durch kleinere Datenbanken werden u. U. die Kosten für die Datenträgerzuordnung gesenkt.

Die Datenbankkomprimierung funktioniert auf virtuellen Azure-Computern genauso wie in der lokalen Umgebung. Weitere Informationen zum Komprimieren vorhandener SAP NetWeaver-Datenbanken für SQL Server finden Sie im Artikel [Verbessertes SAP-Komprimierungstool MSSCOMPRESS](/archive/blogs/saponsqlserver/improved-sap-compression-tool-msscompress) (in englischer Sprache). 

## <a name="sql-server-2014-and-more-recent---storing-database-files-directly-on-azure-blob-storage"></a>SQL Server 2014 und höher: Direktes Speichern von Datenbankdateien in Azure Blob Storage
SQL Server 2014 und höher bietet die Möglichkeit, Datenbankdateien direkt in Azure Blob Storage ohne den umschließenden Wrapper einer VHD zu speichern. Insbesondere bei der Verwendung von Azure-Standardspeicher oder kleineren VM-Typen werden durch diese Bereitstellungsart Szenarien ermöglicht, in denen Sie die IOPS-Einschränkungen überwinden können, die durch eine begrenzte Anzahl von Datenträgern erzwungen werden, die möglicherweise auf einigen kleineren VM-Typen bereitgestellt werden. Diese Bereitstellung funktioniert für Benutzerdatenbanken, jedoch nicht für SQL Server-Systemdatenbanken. Es funktioniert auch für Daten- und Protokolldateien von SQL Server. Wenn Sie eine SAP-SQL Server-Datenbank auf diese Weise bereitstellen möchten, statt sie in VHD „einzuschließen“, bedenken Sie Folgendes:

* Das verwendete Speicherkonto muss sich in derselben Azure-Region befinden wie das Speicherkonto, das zur Bereitstellung des virtuellen Computers verwendet wird, auf dem SQL Server ausgeführt wird.
* Die bereits genannten Überlegungen im Hinblick auf die Verteilung von VHDs auf verschiedene Azure Storage-Konten gelten auch für diese Bereitstellungsmethode. Dies bedeutet, dass die E/A-Vorgänge für die Grenzwerte des Azure-Speicherkontos eingerechnet werden.
* Anstatt mit dem E/A-Speicherkontingent der VM verrechnet zu werden, wird der Datenverkehr mit Speicherblobs verrechnet, die die SQL Server-Daten und Protokolldateien darstellen, und zählt so zur Netzwerkbandbreite der VM des jeweiligen VM-Typs. Weitere Informationen zur Netzwerk- und Speicherbandbreite eines bestimmten VM-Typs finden Sie im Artikel [Größen für virtuelle Windows-Computer in Azure](../../sizes.md).
* Indem Sie den E/A-Dateidurchsatz über das Netzwerkkontingent leiten, vergeuden Sie das Speicherkontingent zu weiten Teilen und nutzen die gesamte Bandbreite der VM daher nur teilweise.
* Die Leistungsziele des IOPS- und E/A-Durchsatzes, die Azure Storage Premium für die verschiedenen Datenträgergrößen hat, gelten nicht mehr. Dies gilt auch für von Ihnen erstellte und in Azure Storage Premium gespeicherte Blobs. Die Ziele finden Sie unter [Storage Premium-Hochleistungsspeicher und verwaltete Datenträger für VMs](../../disks-types.md#premium-ssd). Da SQL Server-Datendateien und -Protokolldateien direkt auf Blobs platziert werden, die in Azure Storage Premium gespeichert sind, können die Leistungsmerkmale im Vergleich zu VHDs in Azure Storage Premium unterschiedlich sein.
* Hostbasiertes Caching, wie es für Azure Storage Premium-Datenträger verfügbar ist, steht nicht zur Verfügung, wenn Sie SQL Server-Datendateien direkt in Azure-Blobs platzieren.
* Auf VMs der M-Serie kann Azure-Schreibbeschleunigung nicht verwendet werden, um Schreibvorgänge im Bereich unter Millisekunden für SQL Server-Transaktionsprotokolldateien zu unterstützen. 

Weitere Informationen zu dieser Funktionalität finden Sie im Artikel [SQL Server-Datendateien in Microsoft Azure](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure).

Für Produktionssysteme wird empfohlen, diese Konfiguration zu vermeiden und SQL Server-Daten und -Protokolldateien direkt in Azure Storage Premium-VHDs statt direkt in Azure-Blobs zu speichern.


## <a name="sql-server-2014-buffer-pool-extension"></a>SQL Server 2014-Pufferpoolerweiterung
Mit SQL Server 2014 wurde eine neue Funktion eingeführt, die sogenannte [Pufferpoolerweiterung](/sql/database-engine/configure-windows/buffer-pool-extension). Diese Funktion erweitert den Pufferpool von SQL Server, der im Arbeitsspeicher mit einem Cache zweiter Ebene gespeichert wird, der von lokalen SSDs eines Servers oder der VM unterstützt wird. Die Pufferpoolerweiterung ermöglicht einen umfangreicheren Arbeitssatz von Daten „im Arbeitsspeicher“. Im Vergleich zum Zugriff auf Azure-Standardspeicher ist der Zugriff auf die Erweiterung des Pufferpools, der auf lokalen SSDs einer Azure-VM gespeichert wird, um viele Faktoren schneller. Vergleicht man die Pufferpoolerweiterung mit dem Azure Storage Premium-Lesecache – für SQL Server-Datendateien empfohlen – bietet die Pufferpoolerweiterung keine erheblichen Vorteile. Der Grund dafür ist, dass beide Caches (SQL Server-Pufferpoolerweiterung und Storage Premium-Lesecache) die lokalen Datenträger des Azure-Serverknotens verwenden.

Die bislang gesammelten Erfahrungen mit der SQL Server-Pufferpoolerweiterung mit SAP-Workload sind unterschiedlich und lassen noch keine eindeutigen Rückschlüsse darauf zu, ob sie in allen Fällen eingesetzt werden soll. Im Idealfall passt der von der SAP-Anwendung benötigte Arbeitssatz in den Hauptspeicher. Da Azure mittlerweile VMs mit bis zu 4 TB Arbeitsspeicher bereitstellt, sollte der Arbeitssatz im Arbeitsspeicher bleiben können. Deswegen ist die Verwendung der Pufferpoolerweiterung auf einige seltene Fälle beschränkt und sollte nicht die Regel sein.  

## <a name="backuprecovery-considerations-for-sql-server"></a>Überlegungen zur Sicherung und Wiederherstellung für SQL Server
Bei der Bereitstellung von SQL Server in Azure müssen Sie die Sicherungsmethode überprüfen. Auch wenn es sich um kein Produktionssystem handelt, muss die durch SQL Server gehostete SAP-Datenbank regelmäßig gesichert werden. Da Azure Storage drei Images vorhält, ist die Sicherung, um einen etwaigen Speicherabsturz kompensieren zu können, weniger dringlich. Der wichtigste Grund, einen ordnungsgemäßen Sicherungs- und Wiederherstellungsplan zu verfolgen, ist vielmehr, dass Sie mithilfe der Point-in-Time-Wiederherstellung logische und manuelle Fehler beheben können. Das Ziel ist also entweder, Sicherungen für die Wiederherstellung des Zustands der Datenbank zu einem bestimmten Zeitpunkt zu verwenden, oder, mithilfe einer Sicherung in Azure durch Seeding ein anderes System aufzusetzen, indem die bestehende Datenbank kopiert wird. 

Die verschiedenen SQL Server-Sicherungsmöglichkeiten in Azure finden Sie im Artikel [Sicherung und Wiederherstellung für SQL Server auf virtuellen Azure-Computern](../../../azure-sql/virtual-machines/windows/backup-restore.md). Der Artikel behandelt mehrere Möglichkeiten.

### <a name="manual-backups"></a>Manuelle Sicherungen
Sie haben mehrere Möglichkeiten, „manuelle“ Sicherungen vorzunehmen:

1. Herkömmliche SQL Server-Sicherungen auf direkt angefügten Azure-Datenträgern. Diese Methode hat den Vorteil, dass Ihnen die Sicherungen als Kopien bestehender SAP-Systeme für Systemaktualisierungen und zum Erstellen neuer Systeme schnell zur Verfügung stehen.
2.  SQL Server 2012 CU4 und höher kann Datenbanken unter einer Azure-Speicher-URL sichern.
3.  Dateimomentaufnahme-Sicherungen für Datenbankdateien in Azure Blob Storage. Diese Methode ist nur anwendbar, wenn sich die SQL Server-Daten und -Protokolldateien im Azure Blob Storage befinden.

Die erste Methode ist weitverbreitet und wird auch oft lokal angewendet. Dennoch müssen Sie den Sicherungsspeicherort längerfristig bestimmen. Da Sie Ihre Sicherungen nicht länger als 30 Tage im lokal installierten Azure Storage aufbewahren wollen, benötigen Sie Azure Backup-Dienste oder ein anderes Sicherungs-/Wiederherstellungstool von einem Drittanbieter, um den Zugriff und die Aufbewahrung für Ihre Sicherungen zu verwalten. Alternativ können Sie einen großen Dateiserver in Azure mithilfe von Windows-Speicherplätzen erstellen.

Die zweite Methode wird im Artikel [SQL Server-Sicherung über URLs](../../../azure-sql/virtual-machines/windows/backup-restore.md) behandelt. Diese Funktionalität unterscheidet sich bei den verschiedenen SQL Server-Versionen. Sehen Sie sich deswegen die Dokumentation des jeweiligen SQL Server-Releases an. Beachten Sie, dass in diesem Artikel viele Einschränkungen aufgeführt werden. Sie haben folgende Sicherungsmöglichkeiten:

- Ein einzelnes Azure-Seitenblob, das die Größe der Sicherungen auf 1.000 GB beschränkt. Diese Einschränkung begrenzt auch den möglichen Durchsatz.
- Mehrere (bis zu 64) Azure-Blockblobs, die theoretisch eine Sicherungsgröße von 12 TB bieten. Tests mit Kundendatenbanken ergaben jedoch, dass die maximale Sicherungsgröße kleiner als der theoretische Grenzwert sein kann. In diesem Fall müssen Sie die Aufbewahrung von und den Zugriff auf Sicherungen verwalten.


### <a name="automated-backup-for-sql-server"></a>Automatisierte Sicherung für SQL Server
Die automatisierte Sicherung ist ein automatischer Sicherungsdienst für die SQL Server-Editionen Standard und Enterprise, die auf einem virtuellen Windows-Computer in Azure ausgeführt werden. Dieser Dienst wird von der [SQL Server-IaaS-Agent-Erweiterung](../../../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md) bereitgestellt, die auf SQL Server Windows-VM-Images im Azure-Portal automatisch installiert wird. Wenn Sie Ihre eigenen Betriebssystemimages mit SQL Server bereitstellen, müssen Sie die VM-Erweiterungen separat installieren. Die Anleitung finden Sie in [diesem Artikel](../../../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md).

Weitere Informationen zu den Funktionen dieser Methode finden Sie in den folgenden Artikeln:

- SQL Server 2014:[ Automatisierte Sicherung für SQL Server 2014-VMs (Ressourcen-Manager)](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md)
- SQL Server 2016/2017:[ Automatisierte Sicherung v2 für virtuelle Azure-Computer (Ressourcen-Manager)](../../../azure-sql/virtual-machines/windows/automated-backup.md)

Die Dokumentation zeigt, dass die Funktionalität mit den neueren SQL Server-Releases verbessert wurde. Weitere Informationen zu automatisierten Sicherungen für SQL Server finden Sie im Artikel [SQL Server Managed Backup für Microsoft Azure](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure). Die theoretische Sicherungsgröße beträgt 12 TB.  Die automatisierten Sicherungen können eine gute Methode für die Sicherung von bis zu 12 TB sein. Da mehrere Blobs parallel beschrieben werden, ist ein Durchsatz von über 100 MB/Sekunde zu erwarten. 
 

### <a name="azure-backup-for-sql-server-vms"></a>Automatisierte Sicherung für SQL Server-VMS
Diese neue Methode für SQL Server-Sicherungen wird ab Juni 2018 von den Azure Backup-Diensten als Public Preview angeboten. Die Methode zum Sichern von SQL Server ist die gleiche wie bei anderen Drittanbietertools: mithilfe der VSS/VDI-Schnittstelle von SQL Server werden Sicherungen an einen Zielspeicherort gestreamt. In diesem Fall ist der Zielspeicherort ein Azure Recovery Services-Tresor.

Ausführlichere Informationen zu dieser Sicherungsmethode, die zahlreiche Vorteile wie zentrale Sicherungskonfigurationen, Überwachung und Verwaltung bietet, [finden Sie hier](../../../backup/backup-azure-sql-database.md). 


### <a name="third-party-backup-solutions"></a>Sicherungslösungen von Drittanbietern
Einige SAP-Kunden hatten nicht die Möglichkeit, für die SAP-Dienste, die in Azure ausgeführt werden, komplett neue Sicherungslösungen einzuführen. Daher mussten die vorhandenen Sicherheitslösungen in Azure verwendet und erweitert werden. Das Erweitern bestehender Sicherungslösungen auf Azure funktionierte für die meisten wichtigen Anbieter in diesem Bereich. 


## <a name="using-a-sql-server-image-out-of-the-microsoft-azure-marketplace"></a><a name="1b353e38-21b3-4310-aeb6-a77e7c8e81c8"></a>Verwenden eines SQL Server-Images aus dem Microsoft Azure Marketplace
Microsoft bietet im Azure Marketplace VMs an, die bereits Versionen von SQL Server enthalten. Für SAP-Kunden, die Lizenzen für SQL Server und Windows benötigen, bieten diese Images die Möglichkeit, diese Lizenzen durch Einrichten von VMs abzudecken, auf denen SQL Server bereits installiert ist. Um solche Images für SAP zu verwenden, müssen die folgenden Aspekte berücksichtigt werden:

* Die Nicht-Evaluierungsversionen von SQL Server verursachen höhere Kosten als ein virtueller Computer mit einer „Nur-Windows-Bereitstellung“ aus Azure Marketplace. Lesen Sie diese Artikel, um die Preise zu vergleichen: <https://azure.microsoft.com/pricing/details/virtual-machines/windows/> und <https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/>. 
* Sie können nur SQL Server-Releases verwenden, die von SAP unterstützt werden.
* Die Sortierung der SQL Server-Instanz, die auf den im Azure Marketplace verfügbaren VMs installiert ist, ist nicht die Sortierung, die SAP NetWeaver zum Ausführen der SQL Server-Instanz benötigt. Sie können die Sortierung aber mithilfe der Anleitung im folgenden Abschnitt ändern.

### <a name="changing-the-sql-server-collation-of-a-microsoft-windowssql-server-vm"></a>Ändern der SQL Server-Sortierung eines virtuellen Microsoft Windows-/SQL Server-Computers
Da die SQL Server-Images im Azure Marketplace nicht die Sortierung verwenden, die für SAP NetWeaver-Anwendungen erforderlich ist, muss diese sofort nach der Bereitstellung geändert werden. Für SQL Server können Sie die Sortierung mithilfe der folgenden Anleitung ändern, sobald die VM bereitgestellt wurde und ein Administrator sich an der bereitgestellten VM anmelden kann:

* Öffnen Sie als Administrator ein Windows-Befehlsfenster.
* Ändern Sie das Verzeichnis in „C:\Program Files\Microsoft SQL Server\110\Setup Bootstrap\SQLServer2012“.
* Führen Sie den folgenden Befehl aus: Setup.exe /QUIET /ACTION=REBUILDDATABASE /INSTANCENAME=MSSQLSERVER /SQLSYSADMINACCOUNTS=`<local_admin_account_name`> /SQLCOLLATION=SQL_Latin1_General_Cp850_BIN2   
  * `<local_admin_account_name`&gt; ist das Konto, das als Administratorkonto definiert wurde, als die VM zum ersten Mal über den Katalog bereitgestellt wurde.

Dieser Vorgang sollte nur wenige Minuten dauern. Um sicherzustellen, dass dieser Schritt zum richtigen Ergebnis geführt hat, führen Sie die folgenden Schritte durch:

* Öffnen Sie SQL Server Management Studio.
* Öffnen Sie ein Abfragefenster.
* Führen Sie den Befehl „sp_helpsort“ in der SQL Server-Masterdatenbank aus.

Das Ergebnis sollte wie folgt aussehen:

```output
Latin1-General, binary code point comparison sort for Unicode Data, SQL Server Sort Order 40 on Code Page 850 for non-Unicode Data
```

Wenn das nicht der Fall ist, BEENDEN Sie die SAP-Bereitstellung, und untersuchen Sie, warum der setup-Befehl nicht wie erwartet funktioniert hat. Die Bereitstellung von SAP NetWeaver-Anwendungen auf SQL Server-Instanzen mit anderen SQL Server-Codeseiten als den oben genannten wird **NICHT** unterstützt.

## <a name="sql-server-high-availability-for-sap-in-azure"></a>SQL Server-Hochverfügbarkeit für SAP in Azure
Mit SQL Server in Azure IaaS-Bereitstellungen für SAP haben Sie verschiedene Möglichkeiten, die DBMS-Schicht hochverfügbar zu implementieren. Wie im Artikel [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md) erläutert, bietet Azure verschiedene SLAs zur Betriebszeit einer einzelnen VM und von zwei VMs, die in einer Azure-Verfügbarkeitsgruppe bereitgestellt werden. Es wird davon ausgegangen, dass Sie eine Betriebszeit-SLA für Ihre Produktionsbereitstellungen wünschen, die die Bereitstellung in Azure-Verfügbarkeitsgruppen erfordert. In diesem Fall müssen Sie mindestens zwei VMs in einer solchen Verfügbarkeitsgruppe bereitstellen. Eine VM wird die aktive SQL Server-Instanz ausführen. Die andere VM wird die passive Instanz ausführen.

### <a name="sql-server-clustering-using-windows-scale-out-file-server-or-azure-shared-disk"></a>SQL Server-Clustering mit dem Windows-Dateiserver mit horizontaler Skalierung oder freigegebenen Azure-Datenträgern
Mit Windows Server 2016 führte Microsoft [direkte Speicherplätze](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) ein. Basierend auf der Bereitstellung direkter Speicherplätze wird das SQL Server-FCI-Clustering allgemeinen unterstützt. Azure bietet auch [freigegebene Azure-Datenträger](../../disks-shared-enable.md?tabs=azure-cli), die für das Windows-Clustering verwendet werden können. Für SAP-Workloads werden diese Hochverfügbarkeitsoptionen nicht unterstützt. 

### <a name="sql-server-log-shipping"></a>SQL Server-Protokollversand
Eine der Methoden für Hochverfügbarkeit (High Availability, HA) ist der SQL Server-Protokollversand. Wenn die an der Hochverfügbarkeitskonfiguration beteiligten VMs über eine funktionierende Namensauflösung verfügen, besteht kein Problem, und die Einrichtung in Azure unterscheidet sich nicht von anderen, lokal ausgeführten Einrichtungen. Dies gilt für die Einrichtung des Protokollversands und den zugehörigen Prinzipien. Weitere Informationen zum SQL Server-Protokollversand finden Sie im Artikel [Informationen zum Protokollversand (SQL Server)](/sql/database-engine/log-shipping/about-log-shipping-sql-server).

Die Funktion „SQL Server-Protokollversand“ wurde in Azure kaum verwendet, um Hochverfügbarkeit in einer Azure-Region zu erzielen. Allerdings setzen SAP-Kunden den Protokollversand in den folgenden Fällen erfolgreich mit Azure ein:

- Notfallwiederherstellung zwischen verschiedenen Azure-Regionen.
- Konfiguration der Notfallwiederherstellung aus einem lokalen System in eine Azure-Region
- Cut-Over-Szenarien aus lokalen Systemen in Azure. In diesen Fällen synchronisiert der Protokollversand die neue DBMS-Bereitstellung in Azure mit dem laufenden Produktionssystem vor Ort. Zum Zeitpunkt des Cut-Over wird die Produktion gestoppt und sichergestellt, dass die letzten und aktuellsten Transaktionsprotokollsicherungen in die Azure-DBMS-Bereitstellung übertragen wurden. Anschließend wird die Azure-DBMS-Bereitstellung für die Produktion eröffnet.  



### <a name="database-mirroring"></a>Datenbankspiegelung
Die von SAP unterstützte Datenbankspiegelung (siehe SAP-Hinweis [965908]) basiert auf der Definition eines Failoverpartners in der SAP-Verbindungszeichenfolge. Bei standortübergreifenden Fällen wird davon ausgegangen, dass sich die beiden VMs in derselben Domäne befinden und dass es sich bei dem Benutzerkontext, unter dem die beiden SQL Server-Instanzen ausgeführt werden, ebenfalls um Domänenbenutzer handelt, die über ausreichende Berechtigungen für die zwei beteiligten SQL Server-Instanzen verfügen. Daher unterscheidet sich die Einrichtung der Datenbankspiegelung in Azure nicht von einer typischen lokalen Einrichtung/Konfiguration.

Für Nur-Cloud-Bereitstellungen besteht die einfachste Methode darin, eine weitere Domäne in Azure einzurichten, damit sich die DBMS-VMs (und im Idealfall die dedizierten SAP-VMs) innerhalb einer Domäne befinden.

Wenn keine weitere Domäne möglich ist, können Sie auch Zertifikate für die Endpunkte der Datenbankspiegelung verwenden, wie hier beschrieben: <https://docs.microsoft.com/sql/database-engine/database-mirroring/use-certificates-for-a-database-mirroring-endpoint-transact-sql>

Ein Tutorial zum Einrichten der Datenbankspiegelung in Azure finden Sie hier: <https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server> 

### <a name="sql-server-always-on"></a>SQL Server Always On
Da Always On für lokale SAP-Bereitstellungen unterstützt wird (siehe SAP-Hinweis [1772688]), wird Always On auch in Kombination mit SAP in Azure unterstützt. Bei der Bereitstellung des SQL Server-Verfügbarkeitsgruppenlisteners (nicht zu verwechseln mit der Azure-Verfügbarkeitsgruppe) sind einige Besonderheiten zu beachten, da Azure zurzeit die Erstellung eines AD-/DNS-Objekts nicht zulässt, wie es lokal möglich ist. Daher sind einige abweichende Installationsschritte notwendig, um das spezifische Verhalten von Azure zu umgehen.

Im Folgenden finden Sie einige Überlegungen zur Verwendung eines Verfügbarkeitsgruppenlisteners:

* Die Verwendung eines Verfügbarkeitsgruppenlisteners ist nur mit Windows Server 2012 oder höheren Version als Gastbetriebssystem der VM möglich. Bei Windows Server 2012 müssen Sie sicherstellen, dass der folgende Patch angewendet wurde: <https://support.microsoft.com/kb/2854082> 
* Dieser Patch ist für Windows Server 2008 R2 nicht vorhanden, und Always On müsste auf die gleiche Weise wie die Datenbankspiegelung verwendet werden, indem ein Failoverpartner in der Verbindungszeichenfolge angegeben wird (über den SAP-default.pfl-Parameter „dbs/mss/server“ – siehe SAP-Hinweis [965908]).
* Bei der Verwendung eines Verfügbarkeitsgruppenlisteners müssen die Datenbank-VMs mit einem dedizierten Lastenausgleich verbunden sein. Um zu vermeiden, dass Azure neue IP-Adressen in Fällen zuweist, in denen durch Zufall beide VMs heruntergefahren werden, sollten Sie den Netzwerkschnittstellen dieser VMs in der Always On-Konfiguration statische IP-Adressen zuweisen. (Das Definieren einer statischen IP-Adresse wird in [diesem][virtual-networks-reserved-private-ip] Artikel beschrieben.)
* Beim Erstellen der WSFC-Clusterkonfiguration, in der dem Cluster eine spezielle IP-Adresse zugewiesen muss, sind spezielle Schritte notwendig, da Azure dem Clusternamen derzeit dieselbe IP-Adresse zuweisen würde wie dem Knoten, auf dem der Cluster erstellt wird. Das bedeutet, dass ein manueller Schritt ausgeführt werden muss, um dem Cluster eine andere IP-Adresse zuzuweisen.
* Der Verfügbarkeitsgruppenlistener wird in Azure mit TCP/IP-Endpunkten erstellt, die den VMs zugewiesen werden, auf denen die primären und sekundären Replikate der Verfügbarkeitsgruppe ausgeführt werden.
* Möglicherweise müssen diese Endpunkte mit Zugriffssteuerungslisten (ACLs) gesichert werden.

Eine ausführliche Dokumentation der Bereitstellung von Always On mit SQL Server auf virtuellen Azure-Computern finden Sie in den folgenden Artikeln:

- [Einführung in SQL Server Always On-Verfügbarkeitsgruppen auf virtuellen Azure-Computern](../../../azure-sql/virtual-machines/windows/availability-group-overview.md)
- [Konfigurieren einer Always On-Verfügbarkeitsgruppe auf virtuellen Azure-Computern in verschiedenen Regionen](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-multiple-regions.md)
- [Konfigurieren einer Load Balancer-Instanz für eine Always On-Verfügbarkeitsgruppe in Azure](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md)

>[!NOTE]
> Wenn Sie die Azure Load Balancer-Instanz für die virtuelle IP-Adresse vom Verfügbarkeitsgruppenlistener konfigurieren, überprüfen Sie, ob DirectServerReturn konfiguriert ist. Die Konfiguration dieser Option wird die Roundtrip-Netzwerklatenz zwischen der SAP-Anwendungsschicht und der DBMS-Schicht reduzieren. 

SQL Server Always On ist die am häufigsten in Azure verwendete Funktionalität für Hochverfügbarkeit und Notfallwiederherstellung für SAP-Workloadbereitstellungen. Die meisten Kunden nutzen Always On für Hochverfügbarkeit innerhalb einer einzelnen Azure-Region. Wenn die Bereitstellung auf nur zwei Knoten beschränkt ist, haben Sie zwei Verbindungsmöglichkeiten:

- Verwenden des Verfügbarkeitsgruppenlisteners. Wenn Sie den Verfügbarkeitsgruppenlistener verwenden, müssen Sie eine Azure Load Balancer-Instanz bereitstellen. Dies ist die Standardbereitstellungsmethode. SAP-Anwendungen sind so konfiguriert, dass sie sich mit dem Verfügbarkeitsgruppenlistener und nicht mit einem einzelnen Knoten verbinden.
- Verwenden des Konnektivitätsparameters der SQL Server-Datenbankspiegelung. In diesem Fall müssen Sie die Konnektivität der SAP-Anwendungen so konfigurieren, dass beide Knotennamen benannt sind. Ausführliche Informationen zu dieser SAP-Konfiguration finden Sie im SAP-Hinweis [965908](https://launchpad.support.sap.com/#/notes/965908). Indem Sie diese Option verwenden, müssen Sie den Verfügbarkeitsgruppenlistener nicht konfigurieren. Und daher ist kein Azure-Lastenausgleich erforderlich, um die Hochverfügbarkeit von SQL Server sicherzustellen. Dadurch ist die Netzwerklatenz zwischen der SAP-Anwendungsschicht und der DBMS-Schicht geringer. Denn der auf der SQL Server-Instanz eingehende Datenverkehr wird über keine Azure Load Balancer-Instanz weitergeleitet. Beachten Sie jedoch, dass diese Option nur funktioniert, wenn Sie Ihre Verfügbarkeitsgruppe auf zwei Instanzen beschränken. 

Einige Kunden verwenden SQL Server Always On für zusätzliche Notfallwiederherstellungsfunktionen zwischen unterschiedlichen Azure-Regionen. Einige Kunden nutzen auch die Möglichkeit, Sicherungen von einem sekundären Replikat durchzuführen. 

## <a name="sql-server-transparent-data-encryption"></a>SQL Server – Transparent Data Encryption
Viele Kunden verwenden SQL Server [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) beim Bereitstellen ihrer SAP-SQL Server-Datenbanken in Azure. Die TDE-Funktion von SQL Server wird vollständig von SAP unterstützt (siehe SAP-Hinweis [1380493](https://launchpad.support.sap.com/#/notes/1380493)). 

### <a name="applying-sql-server-tde"></a>Anwenden von TDE (SQL Server)
Wenn Sie eine heterogene Migration aus einem anderen lokalen DBMS nach Windows/SQL Server in Azure ausführen, erstellen Sie vorab eine leere Zieldatenbank in SQL Server. Im nächsten Schritt wenden Sie die TDE-Funktion von SQL Server an. Währenddessen wird Ihr Produktionssystem lokal ausgeführt. Sie sollten in dieser Reihenfolge vorgehen, weil die Verschlüsselung der leeren Datenbank einige Zeit dauern kann. Die SAP-Importvorgänge importieren die Daten dann während der Downtime in die verschlüsselte Datenbank. Der Import in eine verschlüsselte Datenbank dauert wesentlich kürzer als das Verschlüsseln der Datenbank nach dem Export während der Downtime. Beim Versuch, TDE auf SAP-Workloads anzuwenden, die auf der Datenbank ausgeführt werden, sind Probleme aufgetreten. Daher wird empfohlen, die TDE-Bereitstellung als eine Aktivität zu behandeln, die ohne SAP-Workload auf der jeweiligen Datenbank ausgeführt werden muss.

In Fällen, in denen Sie SAP-SQL Server-Datenbanken aus einem lokalen System nach Azure verschieben, wird empfohlen zu testen, auf welcher Infrastruktur Sie die Verschlüsselung am schnellsten anwenden können. Beachten Sie Folgendes:

- Sie können nicht festlegen, wie viele Threads verwendet werden, um Datenverschlüsselung auf die Datenbank anzuwenden. Die Anzahl von Threads hängt vor allem von der Anzahl von Datenträgervolumes ab, über die die Daten und Protokolldateien von SQL Server verteilt sind. Das heißt, je mehr unterschiedliche Volumes (Laufwerkbuchstaben), desto mehr Threads werden parallel geschaltet, um die Verschlüsselung durchzuführen. Eine solche Konfiguration widerspricht früheren Datenträgerkonfigurationsvorschlägen, maximal einen Speicherplatz für die SQL Server-Datenbankdateien auf Azure-VMs zu erstellen. Eine Konfiguration mit wenigen Volumes hätte eine geringe Anzahl von Threads, die die Verschlüsselung ausführen, zur Folge. Eine einzelne Threadverschlüsselung liest 64-KB-Erweiterungen, verschlüsselt sie und schreibt dann einen Datensatz in die Transaktionsprotokolldatei, der angibt, dass die Erweiterung verschlüsselt wurde. Deswegen ist die Last des Transaktionsprotokolls mittelmäßig.
- In älteren SQL Server-Releases war die Sicherungskomprimierung nicht mehr effizient, wenn die SQL Server-Datenbank verschlüsselt wurde. Dieses Verhalten war ggf. problematisch, wenn Sie Ihre SQL Server-Datenbank lokal verschlüsseln und dann eine Sicherung in Azure kopieren wollten, um die Datenbank in Azure wiederherzustellen. Die SQL Server-Sicherungskomprimierung erzielt in der Regel ein Komprimierungsverhältnis von Faktor 4.
- Mit SQL Server 2016 hat SQL Server eine neue Funktionalität eingeführt, die es ermöglicht, auch verschlüsselte Datenbanken effizient zu komprimieren. In [diesem Blog](/archive/blogs/sqlcat/sqlsweet16-episode-1-backup-compression-for-tde-enabled-databases) finden Sie weitere Informationen.
 
Wenn Sie die Anwendung der TDE-Verschlüsselung ohne oder mit kleinen SAP-Workloads nutzen, testen Sie in Ihrer spezifischen Konfiguration, ob es besser ist, TDE lokal oder in Azure auf Ihre SAP-Datenbank anzuwenden. Azure bietet mehr Flexibilität in Bezug auf die Überbereitstellung und Verkleinerung der Infrastruktur, nachdem TDE angewendet wurde.

### <a name="using-azure-key-vault"></a>Verwenden von Azure Key Vault
Azure bietet den Dienst [Key Vault](https://azure.microsoft.com/services/key-vault/) zum Speichern von Verschlüsselungsschlüsseln an. SQL Server auf der anderen Seite bietet einen Connector, um Azure Key Vault als Speicher für die TDE-Zertifikate zu nutzen.

Weitere Informationen zum Verwenden von Azure Key Vault für SQL Server-TDE finden Sie in den folgenden Artikeln:

- [Erweiterbare Schlüsselverwaltung mit Azure Key Vault (SQL Server)](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)
- [Erweiterbare Schlüsselverwaltung mit Azure Key Vault (SQL Server-TDE): Setupschritte](/sql/relational-databases/security/encryption/setup-steps-for-extensible-key-management-using-the-azure-key-vault)
- [SQL Server-Connector – Wartung & Problembehandlung](/sql/relational-databases/security/encryption/sql-server-connector-maintenance-troubleshooting)
- [Weitere Fragen von Kunden zu SQL Server Transparent Data Encryption – TDE & Azure Key Vault](/archive/blogs/saponsqlserver/more-questions-from-customers-about-sql-server-transparent-data-encryption-tde-azure-key-vault) (in englischer Sprache)


>[!IMPORTANT]
>Mit TDE für SQL Server, insbesondere mit Azure Key Vault, wird empfohlen, die neuesten Patches von SQL Server 2014, SQL Server 2016 und SQL Server 2017 zu verwenden. Grund dafür ist, dass der Code auf Basis von Kundenfeedback optimiert und Fehler behoben wurden. Hier finden Sie ein Beispiel: [4058175](https://support.microsoft.com/help/4058175/tde-enabled-backup-and-restore-slow-if-encryption-key-is-stored-in-ekm).
>  

## <a name="general-sql-server-for-sap-on-azure-summary"></a><a name="9053f720-6f3b-4483-904d-15dc54141e30"></a>SQL Server für SAP in Azure – Allgemeine Zusammenfassung
In diesem Handbuch finden Sie eine Reihe von Empfehlungen. Es ist ratsam, das Handbuch mehrmals zu lesen, bevor Sie Ihre Azure-Bereitstellung planen. Im Allgemeinen jedoch sollten Sie den wichtigsten Empfehlungen für DBMS in Azure folgen:

1. Verwenden Sie die neueste DBMS-Version wie SQL Server 2017, die die meisten Vorteile in Azure bietet. 
2. Planen Sie Ihre SAP-Systemlandschaft in Azure sorgfältig, um das Layout von Datendateien und die Einschränkungen von Azure gegeneinander abzuwägen:
   * Verwenden Sie nicht zu viele Datenträger, aber ausreichend viele, um sicherzustellen, dass die erforderlichen IOPS erzielt werden können.
   * Wenn Sie nicht Managed Disks verwenden, denken Sie daran, dass IOPS auch pro Azure Storage-Konto beschränkt sind und dass Speicherkonten innerhalb jedes Azure-Abonnements beschränkt sind ([weitere Details][azure-resource-manager/management/azure-subscription-service-limits]). 
   * Erstellen Sie nur Stripesets für Datenträger, wenn Sie einen höheren Durchsatz erzielen müssen.
3. Auf dem Laufwerk „D:\“ sollten Sie nie Software installieren oder Dateien speichern, die Persistenz erfordern, da dieses Laufwerk nicht permanent ist und somit sämtliche Inhalte bei einem Windows-Neustart verloren gehen.
4. Verwenden Sie keine Datenträgerzwischenspeicherung für Azure-Standardspeicher.
5. Verwenden Sie keine georeplizierten Azure-Standardspeicherkonten.  Verwenden Sie die Option „Lokal redundant“ für DBMS-Workloads.
6. Verwenden Sie die Hochverfügbarkeits- und Notfallwiederherstellungslösung Ihres DBMS-Anbieters, um Datenbankdaten zu replizieren.
7. Verwenden Sie immer die Namensauflösung, und verlassen Sie sich nicht auf IP-Adressen.
8. Wenden Sie die neuesten SQL Server-Patches an, wenn Sie TDE von SQL Server verwenden.
9. Verwenden Sie die höchstmögliche Datenbankkomprimierung. Hierbei handelt es sich um Seitenkomprimierung für SQL Server.
10. Verwenden Sie SQL Server-Images aus dem Azure Marketplace mit großer Sorgfalt. Wenn Sie SQL Server verwenden, müssen Sie die Instanzensortierung vor der Installation eines SAP NetWeaver-Systems ändern.
11. Installieren und konfigurieren Sie die SAP-Hostüberwachung für Azure wie im [Bereitstellungshandbuch][deployment-guide] beschrieben.


## <a name="next-steps"></a>Nächste Schritte
Artikel lesen 

- [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md)