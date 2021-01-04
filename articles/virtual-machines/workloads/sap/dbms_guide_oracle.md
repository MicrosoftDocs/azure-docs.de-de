---
title: Azure Virtual Machines – Oracle-DBMS-Bereitstellung für SAP-Workload | Microsoft-Dokumentation
description: Azure Virtual Machines – Oracle-DBMS-Bereitstellung für SAP-Workload
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure, Oracle, Data Guard
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3e99b3a8960eb49856e9a016eb054eed41eccde9
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965254"
---
# <a name="azure-virtual-machines-oracle-dbms-deployment-for-sap-workload"></a>Oracle-DBMS-Bereitstellung für SAP-Workload auf Azure Virtual Machines

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
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
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


Dieses Dokument behandelt verschiedene wichtige Themen, die Sie bei der Bereitstellung von Oracle Database für SAP-Workload in Azure IaaS berücksichtigen sollten. Bevor Sie dieses Dokument lesen, empfehlen wir, dass Sie [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md) lesen. Zudem empfehlen wir, dass Sie weitere Anleitungen in der [Dokumentation zur SAP-Workload in Azure](./get-started.md) lesen. 

Informationen zu den verschiedenen Oracle-Versionen und den entsprechenden Betriebssystemversionen, die für den Betrieb von SAP unter Oracle in Azure unterstützt werden, finden Sie in SAP-Hinweis [2039619].

Allgemeine Informationen zum Ausführen der SAP Business Suite in Oracle finden Sie unter [SAP unter Oracle](https://www.sap.com/community/topic/oracle.html).
Die Oracle-Software wird von Oracle für die Ausführung in Microsoft Azure unterstützt. Weitere Informationen zur allgemeinen Unterstützung für Windows Hyper-V und Azure finden Sie in [Oracle und Microsoft Azure – häufig gestellte Fragen](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html). 

## <a name="sap-notes-relevant-for-oracle-sap-and-azure"></a>Für Oracle, SAP und Azure relevante SAP-Hinweise 

Die folgenden SAP-Hinweise beziehen sich auf SAP in Azure.

| Hinweisnummer | Titel |
| --- | --- |
| [1928533] |SAP-Anwendungen auf Azure: Unterstützte Produkte und Azure-VM-Typen |
| [2015553] |SAP auf Microsoft Azure: Supportvoraussetzungen |
| [1999351] |Problembehandlung für die erweiterte Azure-Überwachung für SAP |
| [2178632] |Wichtige Überwachungsmetriken für SAP in Microsoft Azure |
| [2191498] |SAP unter Linux mit Azure: Erweiterte Überwachung |
| [2039619] |SAP-Anwendungen auf Microsoft Azure mit der Oracle-Datenbank: Unterstützte Produkte und Versionen |
| [2243692] |Microsoft Azure (IaaS)-VM: SAP-Lizenzprobleme |
| [2069760] |Oracle Linux 7.x SAP: Installation und Upgrade |
| [1597355] |Empfehlung zu Auslagerungsbereichen für Linux |
| [2171857] |Oracle Database 12c – Dateisystemunterstützung unter Linux |
| [1114181] |Oracle Database 11g – Dateisystemunterstützung unter Linux |

Die genauen Konfigurationen und Funktionen, die von Oracle und SAP in Azure unterstützt werden, finden Sie im SAP-Hinweis [2039619](https://launchpad.support.sap.com/#/notes/2039619).

Windows und Oracle Linux sind die einzigen Betriebssysteme, die von Oracle und SAP in Azure unterstützt werden. Die weit verbreiteten Linux-Distributionen SLES und RHEL werden nicht unterstützt, um Oracle-Komponenten in Azure bereitzustellen. Zu den Oracle-Komponenten gehört der Oracle Database-Client, den SAP-Anwendungen zum Verbinden mit dem Oracle-DBMS verwenden. 

Ausnahmen gemäß dem SAP-Hinweis [#2039619](https://launchpad.support.sap.com/#/notes/2039619) sind SAP-Komponenten, bei denen nicht der Oracle Database-Client verwendet wird. Dazu zählen der eigenständige Enqueue-Server, der Nachrichtenserver, der Enqueue-Replikationsserver von SAP, WebDispatcher und das SAP-Gateway.  

Selbst wenn Sie Ihre Oracle-DBMS- und SAP-Anwendungsinstanzen unter Oracle Linux ausführen, können Sie Ihre SAP Central Services auf SLES oder RHEL ausführen und mit einem Pacemaker-basierten Cluster schützen. Pacemaker wird als Hochverfügbarkeitsframework unter Oracle Linux nicht unterstützt.

## <a name="specifics-for-oracle-database-on-windows"></a>Besonderheiten bei Oracle Database unter Windows

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-windows"></a>Oracle-Konfigurationsrichtlinien für SAP-Installationen auf Azure-VMs unter Windows

In Übereinstimmung mit dem SAP-Installationshandbuch sollten Oracle-bezogene Dateien nicht auf dem Betriebssystemdatenträger einer VM (Laufwerk „C:“) installiert oder darauf abgelegt werden. Virtuelle Computer unterschiedlicher Größen können eine variierende Anzahl angefügter Datenträger unterstützen. Kleinere VM-Typen können eine kleinere Anzahl von angefügten Datenträgern unterstützen. 

Wenn Sie mit kleineren VMs arbeiten und Gefahr laufen, den Grenzwert für die Anzahl der Datenträger zu erreichen, die Sie an die VM anfügen können, können Sie Oracle Home, Stage, `saptrace`, `saparch`, `sapbackup`, `sapcheck` oder `sapreorg` auf dem Betriebssystemdatenträger installieren bzw. platzieren. Diese Teile der Oracle DBMS-Komponenten sind nicht sehr E/A- und E/A-durchsatzintensiv. Dies bedeutet, dass der Betriebssystemdatenträger die E/A-Anforderungen verarbeiten kann. Die Standardgröße des Betriebssystemdatenträgers sollte 127 GB betragen. 

Oracle Database- und Wiederholungsprotokolldateien müssen auf separaten Datenträgern gespeichert werden. Für den temporären Oracle-Tabellenbereich gilt eine Ausnahme. `Tempfiles` kann auf D:/ (nicht permanentes Laufwerk) erstellt werden. Außerdem bietet das nicht permanente Laufwerk „D:\“ eine bessere E/A-Latenz und einen höheren E/A-Durchsatz (mit Ausnahme von VMs der A-Serie). 

Um die richtige Größe des Speicherplatzes für die `tempfiles` zu bestimmen, können Sie die Größen der `tempfiles` auf vorhandenen Systemen überprüfen.

### <a name="storage-configuration"></a>Speicherkonfiguration
Unterstützt wird ausschließlich eine einzige Instanz von Oracle unter Verwendung von NTFS-formatierten Datenträgern. Sämtliche Datenbankdateien müssen im NTFS-Dateisystem in Managed Disks (empfohlen) oder auf VHDs gespeichert werden. Diese Datenträger werden in die Azure-VM eingebunden und basieren auf [Azure-Seitenblobspeicher](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) oder [Azure Managed Disks](../../managed-disks-overview.md). 

Lesen Sie den Artikel [Azure Storage-Typen für die SAP-Workload](./planning-guide-storage.md), um weitere Informationen zu den spezifischen Azure-Blockspeichertypen zu erhalten, die für DBMS-Workloads geeignet sind.

Wir empfehlen dringend die Verwendung von [Azure Managed Disks](../../managed-disks-overview.md). Außerdem wird dringend empfohlen, [Azure Storage Premium oder Azure Disk Ultra](../../disks-types.md) für Ihre Oracle Database-Bereitstellungen zu verwenden.

Netzlaufwerke und Remotefreigaben wie Azure-Dateidienste werden für Oracle Database-Dateien nicht unterstützt. Weitere Informationen finden Sie unter

- [Einführung in den Microsoft Azure-Dateidienst](/archive/blogs/windowsazurestorage/introducing-microsoft-azure-file-service)

- [Beibehalten von Verbindungen zu Microsoft Azure-Dateien](/archive/blogs/windowsazurestorage/persisting-connections-to-microsoft-azure-files)


Die in [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md) getroffenen Aussagen gelten auch für Bereitstellungen mit Oracle Database, wenn Sie Datenträger auf Basis von Azure-Seitenblobspeicher oder Managed Disks verwenden.

Es gibt Kontingente für den IOPS-Durchsatz für Azure-Datenträger. Dieses Konzept wird in [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md) erläutert. Die jeweilige exakte Größe der Kontingente hängt vom Typ der verwendeten VM ab. Eine Liste mit VM-Typen und den entsprechenden Kontingenten finden Sie unter [Größen für virtuelle Windows-Computer in Azure][virtual-machines-sizes-windows].

Die unterstützten Typen der Azure-VMs werden in SAP-Hinweis [1928533] aufgeführt.

Die Mindestkonfiguration ist wie folgt: 

| Komponente | Datenträger | Caching | Speicherpool |
| --- | ---| --- | --- |
| „\oracle\<SID>\origlogaA“ und „mirrlogB“ | Premium- oder Ultra-Datenträger | Keine | Nicht erforderlich |
| „\oracle\<SID>\origlogaB“ und „mirrlogA“ | Premium- oder Ultra-Datenträger | Keine | Nicht erforderlich |
| „\oracle\<SID>\sapdata1...n“ | Premium- oder Ultra-Datenträger | Schreibgeschützt | Kann für Premium verwendet werden |
| „\oracle\<SID>\oraarch“ | Standard | Keine | Nicht erforderlich |
| Oracle Home, `saptrace`, ... | Betriebssystemdatenträger (Premium) | | Nicht erforderlich |


Die Datenträgerauswahl für das Hosten von Onlinewiederholungsprotokollen sollte durch die IOPS-Anforderungen gesteuert werden. Alle sapdata1...n (Tabellenbereiche) können auf einem einzelnen eingebundenen Datenträger gespeichert werden, solange Größe, IOPS und Durchsatz die Anforderungen erfüllen. 

Die Leistungskonfiguration ist wie folgt:

| Komponente | Datenträger | Caching | Speicherpool |
| --- | ---| --- | --- |
| „\oracle\<SID>\origlogaA“ | Premium- oder Ultra-Datenträger | Keine | Kann für Premium verwendet werden  |
| „\oracle\<SID>\origlogaB“ | Premium- oder Ultra-Datenträger | Keine | Kann für Premium verwendet werden |
| „\oracle\<SID>\mirrlogAB“ | Premium- oder Ultra-Datenträger | Keine | Kann für Premium verwendet werden |
| „\oracle\<SID>\mirrlogBA“ | Premium- oder Ultra-Datenträger | Keine | Kann für Premium verwendet werden |
| „\oracle\<SID>\sapdata1...n“ | Premium- oder Ultra-Datenträger | Schreibgeschützt | Für Premium empfohlen  |
| \oracle\SID\sapdata(n+1)* | Premium- oder Ultra-Datenträger | Keine | Kann für Premium verwendet werden |
| „\oracle\<SID>\oraarch*“ | Premium- oder Ultra-Datenträger | Keine | Nicht erforderlich |
| Oracle Home, `saptrace`, ... | Betriebssystemdatenträger (Premium) | Nicht erforderlich |

*(n+1): Hosting von SYSTEM-, TEMP- und UNDO-Tabellenbereichen. Die E/A-Muster der System- und Undo-Tabellenbereiche unterscheiden sich von anderen Tabellenbereichen, die Anwendungsdaten hosten. Um die Leistung der System- und Undo-Tabellenbereiche zu optimieren, ist das Auslassen der Zwischenspeicherung die beste Option.

*oraarch: Speicherpool ist unter dem Aspekt der Leistung nicht notwendig. Er kann verwendet werden, um mehr Speicherplatz zu erhalten.

Wenn im Fall von Azure Storage Premium mehr IOPS erforderlich sind, empfehlen wir, Windows-Speicherpools (nur verfügbar unter Microsoft Windows Server 2012 und höher) zu verwenden, um ein einziges großes, logisches Gerät aus mehreren bereitgestellten Datenträgern zu erstellen. Durch diese Herangehensweise wird der Aufwand verringert, der zur Verwaltung des Speicherplatzes notwendig ist. Außerdem müssen Dateien nicht mehr manuell auf mehrere bereitgestellte Datenträger verteilt werden.


#### <a name="write-accelerator"></a>Schreibbeschleunigung
Bei Azure-VMs der M-Serie kann die Latenz beim Schreiben in Onlinewiederholungsprotokolle im Vergleich zu Azure Storage Premium um Faktoren reduziert werden. Aktivieren Sie die Azure-Schreibbeschleunigung für Datenträger (VHDs) basierend auf Azure Storage Premium, die für Dateien von Onlinewiederholungsprotokolle verwendet werden. Weitere Informationen finden Sie unter [Schreibbeschleunigung](../../how-to-enable-write-accelerator.md). Verwenden Sie alternativ Azure Ultra-Datenträger für das Volume mit dem Onlinewiederholungsprotokoll.


### <a name="backuprestore"></a>Sichern/Wiederherstellen
Die Funktionen zum Sichern und Wiederherstellen werden für die SAP BR*Tools für Oracle genauso unterstützt wie auf standardmäßigen Windows Server-Betriebssystemen. Auch Oracle Recovery Manager (RMAN) wird für Sicherungen auf einen Datenträger und Wiederherstellungen von einem Datenträger unterstützt.

Sie können auch mit Azure Backup eine anwendungskonsistente VM-Sicherung ausführen. Der Artikel [Planen der Sicherungsinfrastruktur für VMs in Azure](../../../backup/backup-azure-vms-introduction.md) erläutert, wie Azure Backup die Windows VSS-Funktionalität zur Ausführung einer anwendungskonsistenten Sicherung verwendet. Die Oracle-DBMS-Releases, die in Azure von SAP unterstützt werden, können die VSS-Funktionalität zum Sichern nutzen. Weitere Informationen finden Sie in der Oracle-Dokumentation [Grundkonzepte der Datenbanksicherung und -wiederherstellung mit VSS](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/ntqrf/basic-concepts-of-database-backup-and-recovery-with-vss.html#GUID-C085101B-237F-4773-A2BF-1C8FD040C701).


### <a name="high-availability"></a>Hochverfügbarkeit
Oracle Data Guard wird aus Gründen der Hochverfügbarkeit und der Notfallwiederherstellung unterstützt. Um ein automatisches Failover in Data Guard zu erreichen, muss Fast Start-Failover (FSFA) verwendet werden. Der Beobachter (FSFA) löst das Failover aus. Wenn Sie FSFA nicht verwenden, können Sie nur eine Konfiguration für manuelles Failover verwenden.

Weitere Informationen zur Notfallwiederherstellung für Oracle-Datenbanken in Azure finden Sie unter [Notfallwiederherstellungsszenario für eine Oracle Database 12c-Datenbank in einer Azure-Umgebung](../oracle/oracle-disaster-recovery.md).

### <a name="accelerated-networking"></a>Beschleunigte Netzwerke
Für Oracle-Bereitstellungen unter Windows wird dringend empfohlen, den beschleunigten Netzwerkbetrieb zu nutzen, wie in [Beschleunigter Netzwerkbetrieb in Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) beschrieben. Nützlich sind auch die Empfehlungen in [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md). 
### <a name="other"></a>Andere
Andere wichtige Konzepte im Zusammenhang mit Bereitstellungen von VMs mit Oracle Database, einschließlich Azure-Verfügbarkeitsgruppen und SAP-Überwachung werden in [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md) beschrieben.

## <a name="specifics-for-oracle-database-on-oracle-linux"></a>Besonderheiten bei Oracle Database unter Oracle Linux
Die Oracle-Software wird von Oracle für die Ausführung in Microsoft Azure mit Oracle Linux als Gastbetriebssystem unterstützt. Weitere Informationen zur allgemeinen Unterstützung für Windows Hyper-V und Azure finden Sie in [Azure und Oracle – häufig gestellte Fragen](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html). 

Das spezielle Szenario der gemeinsamen Verwendung von SAP-Anwendungen und Oracle Database wird ebenfalls unterstützt. Details finden Sie im nächsten Teil des Dokuments.

### <a name="oracle-version-support"></a>Versionsunterstützung für Oracle
Informationen dazu, welche Oracle-Versionen und entsprechenden Betriebssystemversionen für die Ausführung von SAP unter Oracle auf einem virtuellen Azure-Computer unterstützt werden, finden Sie in SAP-Hinweis [2039619].

Allgemeine Informationen zum Ausführen der SAP Business Suite in Oracle finden Sie auf der Communityseite zu [SAP unter Oracle](https://www.sap.com/community/topic/oracle.html).

### <a name="oracle-configuration-guidelines-for-sap-installations-in-azure-vms-on-linux"></a>Oracle-Konfigurationsrichtlinien für SAP-Installationen auf Azure-VMs unter Linux

In Übereinstimmung mit den SAP-Installationshandbüchern sollten keine Oracle-bezogenen Dateien im Systemtreiber für den Startdatenträger von VMs installiert oder darin abgelegt werden. Variierende Größen virtuelle Computer unterstützen eine schwankende Anzahl angefügter Datenträger. Kleinere VM-Typen können eine kleinere Anzahl von angefügten Datenträgern unterstützen. 

In diesem Fall empfehlen wir, Oracle Home, Stage, `saptrace`, `saparch`, `sapbackup`, `sapcheck` oder `sapreorg` auf dem Startdatenträger zu installieren bzw. zu platzieren. Diese Teile der Oracle-DBMS-Komponenten weisen keine umfassenden E/As und E/A-Durchsatz auf. Dies bedeutet, dass der Betriebssystemdatenträger die E/A-Anforderungen verarbeiten kann. Die Standardgröße des Betriebssystemdatenträgers beträgt 30 GB. Sie können den Startdatenträger mit dem Azure-Portal, mit PowerShell oder der CLI erweitern. Nachdem der Startdatenträger erweitert wurde, können Sie eine zusätzliche Partition für Oracle-Binärdateien hinzufügen.


### <a name="storage-configuration"></a>Speicherkonfiguration

Die Dateisysteme ext4, xfs oder Oracle ASM werden für Oracle Database-Dateien in Azure unterstützt. Sämtliche Datenbankdateien müssen in diesen Dateisystemen auf VHDs oder in Managed Disks gespeichert werden. Diese Datenträger werden in die Azure-VM eingebunden und basieren auf [Azure-Seitenblobspeicher](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) oder [Azure Managed Disks](../../managed-disks-overview.md).

Für Oracle Linux UEK-Kernels ist mindestens die UEK-Version 4 erforderlich, um [Azure Premium SSD](../../premium-storage-performance.md#disk-caching) unterstützen zu können.

Lesen Sie den Artikel [Azure Storage-Typen für die SAP-Workload](./planning-guide-storage.md), um weitere Informationen zu den spezifischen Azure-Blockspeichertypen zu erhalten, die für DBMS-Workloads geeignet sind.

Es wird ausdrücklich empfohlen, [Azure Managed Disks](../../managed-disks-overview.md) zu nutzen. Außerdem wird dringend empfohlen, [Azure Premium SSD](../../disks-types.md) für Ihre Oracle Database-Bereitstellungen zu verwenden.

Netzlaufwerke und Remotefreigaben wie Azure-Dateidienste werden für Oracle Database-Dateien nicht unterstützt. Weitere Informationen finden Sie unter 

- [Einführung in den Microsoft Azure-Dateidienst](/archive/blogs/windowsazurestorage/introducing-microsoft-azure-file-service)

- [Beibehalten von Verbindungen zu Microsoft Azure-Dateien](/archive/blogs/windowsazurestorage/persisting-connections-to-microsoft-azure-files)

Die im Artikel [Überlegungen zur DBMS-Bereitstellung von Azure Virtual Machines für die SAP-Workload](dbms_guide_general.md) getroffenen Aussagen gelten auch für Bereitstellungen mit Oracle Database, wenn Sie Datenträger auf Basis von Azure-Seitenblobspeicher oder Managed Disks verwenden.

Es gibt Kontingente für den IOPS-Durchsatz für Azure-Datenträger. Dieses Konzept wird in [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md) erläutert. Die jeweilige exakte Größe der Kontingente hängt vom Typ der verwendeten VM ab. Eine Liste der VM-Typen mit den entsprechenden Kontingenten finden Sie unter [Größen für virtuelle Linux-Computer in Azure][virtual-machines-sizes-linux].

Die unterstützten Typen der Azure-VMs werden in SAP-Hinweis [1928533] aufgeführt.

Mindestkonfiguration:

| Komponente | Datenträger | Caching | Striping* |
| --- | ---| --- | --- |
| /oracle/\<SID>/origlogaA & mirrlogB | Premium- oder Ultra-Datenträger | Keine | Nicht erforderlich |
| /oracle/\<SID>/origlogaB & mirrlogA | Premium- oder Ultra-Datenträger | Keine | Nicht erforderlich |
| /oracle/\<SID>/sapdata1...n | Premium- oder Ultra-Datenträger | Schreibgeschützt | Kann für Premium verwendet werden |
| /oracle/\<SID>/oraarch | Standard | Keine | Nicht erforderlich |
| Oracle Home, `saptrace`, ... | Betriebssystemdatenträger (Premium) | | Nicht erforderlich |

*Striping: LVM-Stripe oder MDADM mit RAID 0

Die Datenträgerauswahl für das Hosten von Onlinewiederholungsprotokollen von Oracle sollte durch IOPs-Anforderungen gesteuert werden. Alle sapdata1...n (Tabellenbereiche) können auf einem einzelnen eingebundenen Datenträger gespeichert werden, solange Volumen, IOPS und Durchsatz die Anforderungen erfüllen. 

Leistungskonfiguration:

| Komponente | Datenträger | Caching | Striping* |
| --- | ---| --- | --- |
| /oracle/\<SID>/origlogaA | Premium- oder Ultra-Datenträger | Keine | Kann für Premium verwendet werden  |
| /oracle/\<SID>/origlogaB | Premium- oder Ultra-Datenträger | Keine | Kann für Premium verwendet werden |
| /oracle/\<SID>/mirrlogAB | Premium- oder Ultra-Datenträger | Keine | Kann für Premium verwendet werden |
| /oracle/\<SID>/mirrlogBA | Premium- oder Ultra-Datenträger | Keine | Kann für Premium verwendet werden |
| /oracle/\<SID>/sapdata1...n | Premium- oder Ultra-Datenträger | Schreibgeschützt | Für Premium empfohlen  |
| „/oracle/\<SID>/sapdata(n+1)*“ | Premium- oder Ultra-Datenträger | Keine | Kann für Premium verwendet werden |
| /oracle/\<SID>/oraarch* | Premium- oder Ultra-Datenträger | Keine | Nicht erforderlich |
| Oracle Home, `saptrace`, ... | Betriebssystemdatenträger (Premium) | Nicht erforderlich |

*Striping: LVM-Stripe oder MDADM mit RAID 0

*(n+1): Hosting von SYSTEM-, TEMP- und UNDO-Tabellenbereichen: Die E/A-Muster der System- und Undo-Tabellenbereiche unterscheiden sich von anderen Tabellenbereichen, die Anwendungsdaten hosten. Um die Leistung der System- und Undo-Tabellenbereiche zu optimieren, ist das Auslassen der Zwischenspeicherung die beste Option.

*oraarch: Speicherpool ist unter dem Aspekt der Leistung nicht notwendig.


Wenn bei der Verwendung von Azure Storage Premium mehr IOPS erforderlich sind, wird empfohlen, LVM (Logical Volume Manager) oder MDADM zu verwenden, um ein großes logisches Volume über mehrere bereitgestellte Datenträger hinweg zu erstellen. Unter [Überlegungen zur DBMS-Bereitstellung von Azure Virtual Machines für die SAP-Workload](dbms_guide_general.md) finden Sie weitere Richtlinien und Hinweise zur Verwendung von LVM oder MDADM. Durch diese Herangehensweise wird der Aufwand verringert, der zur Verwaltung des Speicherplatzes notwendig ist. Außerdem müssen Dateien nicht mehr manuell auf mehrere bereitgestellte Datenträger verteilt werden.


#### <a name="write-accelerator"></a>Schreibbeschleunigung
Bei Azure-VMs der M-Serie kann die Latenz beim Schreiben in Onlinewiederholungsprotokolle um Faktoren reduziert werden, wenn Azure-Schreibbeschleunigung und Azure Storage Premium verwendet werden. Aktivieren Sie die Azure-Schreibbeschleunigung für Datenträger (VHDs) basierend auf Azure Storage Premium, die für Dateien von Onlinewiederholungsprotokolle verwendet werden. Weitere Informationen finden Sie unter [Schreibbeschleunigung](../../how-to-enable-write-accelerator.md). Verwenden Sie alternativ Azure Ultra-Datenträger für das Volume mit dem Onlinewiederholungsprotokoll.


### <a name="backuprestore"></a>Sichern/Wiederherstellen
Die Funktionen zum Sichern und Wiederherstellen werden für die SAP BR*Tools für Oracle genauso unterstützt wie auf Bare-Metal-Systemen und Hyper-V. Auch Oracle Recovery Manager (RMAN) wird für Sicherungen auf einen Datenträger und Wiederherstellungen von einem Datenträger unterstützt.

Weitere Informationen zur Verwendung von Azure Backup und Azure Recovery Services zum Sichern und Wiederherstellen von Oracle-Datenbanken finden Sie unter [Sichern und Wiederherstellen einer Oracle Database 12c-Datenbank auf einem virtuellen Azure Linux-Computer](../oracle/oracle-overview.md).

### <a name="high-availability"></a>Hochverfügbarkeit
Oracle Data Guard wird aus Gründen der Hochverfügbarkeit und der Notfallwiederherstellung unterstützt. Um ein automatisches Failover in Data Guard zu erreichen, muss Fast Start-Failover (FSFA) verwendet werden. Die Beobachterfunktion (FSFA) löst das Failover aus. Wenn Sie FSFA nicht verwenden, können Sie nur eine Konfiguration für manuelles Failover verwenden. Weitere Informationen finden Sie unter [Implementieren von Oracle Data Guard auf einer Linux-VM in Azure](../oracle/configure-oracle-dataguard.md).


Weitere Informationen zur Notfallwiederherstellung für Oracle-Datenbanken in Azure finden Sie im Artikel [Notfallwiederherstellungsszenario für eine Oracle Database 12c-Datenbank in einer Azure-Umgebung](../oracle/oracle-disaster-recovery.md).

### <a name="accelerated-networking"></a>Beschleunigte Netzwerke
Unterstützung für den beschleunigten Azure-Netzwerkbetrieb unter Oracle Linux stellt Oracle Linux 7 Update 5 (Oracle Linux 7.5) bereit. Wenn Sie nicht auf die neueste Version von Oracle Linux 7.5 aktualisieren können, können Sie das Problem umgehen, indem Sie den Red Hat Compatible Kernel (RHCK) anstelle des Oracle UEK-Kernels verwenden. 

Die Verwendung des RHEL-Kernels in Oracle Linux wird gemäß SAP-Hinweis [1565179](https://launchpad.support.sap.com/#/notes/1565179) unterstützt. Beim beschleunigten Azure-Netzwerkbetrieb muss die Mindestversion des RHCKL-Kernels 3.10.0-862.13.1.el7 sein. Wenn Sie den UEK-Kernel unter Oracle Linux in Verbindung mit [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) verwenden, müssen Sie Version 5 des Oracle UEK-Kernels verwenden.

Wenn Sie VMs aus einem Image bereitstellen, das nicht auf Azure Marketplace basiert, müssen Sie zusätzliche Konfigurationsdateien in die VM kopieren, indem Sie folgenden Code ausführen: 
<pre><code># Copy settings from GitHub to the correct place in the VM
sudo curl -so /etc/udev/rules.d/68-azure-sriov-nm-unmanaged.rules https://raw.githubusercontent.com/LIS/lis-next/master/hv-rhel7.x/hv/tools/68-azure-sriov-nm-unmanaged.rules 
</code></pre>


## <a name="next-steps"></a>Nächste Schritte
Artikel lesen 

- [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md)
