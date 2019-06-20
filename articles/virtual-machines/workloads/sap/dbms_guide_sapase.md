---
title: Azure Virtual Machines – SAP ASE-DBMS-Bereitstellung für SAP-Workload| Microsoft-Dokumentation
description: Azure Virtual Machines – SAP ASE-DBMS-Bereitstellung für SAP-Workload
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/1/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3f50f013020c704ddc294a59f8c6c5dac24bbd5a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60835276"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Azure Virtual Machines – SAP ASE-DBMS-Bereitstellung für SAP-Workload

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]: https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]: https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]: https://launchpad.support.sap.com/#/notes/1558958
[1585981]: https://launchpad.support.sap.com/#/notes/1585981
[1588316]: https://launchpad.support.sap.com/#/notes/1588316
[1590719]: https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]: https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]: https://launchpad.support.sap.com/#/notes/1619967
[1750510]: https://launchpad.support.sap.com/#/notes/1750510
[1752266]: https://launchpad.support.sap.com/#/notes/1752266
[1757924]: https://launchpad.support.sap.com/#/notes/1757924
[1757928]: https://launchpad.support.sap.com/#/notes/1757928
[1758182]: https://launchpad.support.sap.com/#/notes/1758182
[1758496]: https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]: https://launchpad.support.sap.com/#/notes/1814258
[1882376]: https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]: https://launchpad.support.sap.com/#/notes/1922555
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[1941500]: https://launchpad.support.sap.com/#/notes/1941500
[1956005]: https://launchpad.support.sap.com/#/notes/1956005
[1973241]: https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]: https://launchpad.support.sap.com/#/notes/2121797
[2134316]: https://launchpad.support.sap.com/#/notes/2134316
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

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
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../windows/disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/storage-scalability-targets.md
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
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./../../windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./../../windows/sql/virtual-machines-windows-sql-performance.md
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



Dieses Dokument behandelt verschiedene wichtige Themen für die Bereitstellung von SAP ASE in Azure IaaS. Es ist sinnvoll, im Vorfeld das Dokument [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md) und andere Artikel der [Azure-Dokumentation für SAP-Workload](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) zu lesen. 

## <a name="specifics-to-sap-ase-on-windows"></a>Besonderheiten bei SAP ASE unter Windows
Ab Microsoft Azure lassen sich bestehende SAP ASE-Anwendungen zu Azure-VMs migrieren. Wenn Sie SAP ASE auf einer Azure-VM betreiben, können Sie die Gesamtkosten für Bereitstellung, Verwaltung und Wartung von unternehmensweiten Anwendungen senken, indem Sie diese Anwendungen einfach zu Microsoft Azure migrieren. Bei SAP ASE auf einem virtuellen Azure-Computer stehen Administratoren und Entwicklern die vertrauten Entwicklungs- und Verwaltungstools aus der lokalen Umgebung zur Verfügung.

Die SLAs für Azure-VMs finden Sie hier: <https://azure.microsoft.com/support/legal/sla/virtual-machines>.

Microsoft Azure stellt zahlreiche verschiedene VM-Typen zur Verfügung, mit denen Sie SAP-Systeme und -Landschaften jeder Größe ausführen können: von den kleinsten bis hin zu den großen mit Tausenden von Benutzern. Informationen zur SAP-Größe für SAPS-Zahlen der verschiedenen SAP-zertifizierten VM-SKUs finden Sie im SAP-Hinweis [1928533].

Aussagen und Empfehlungen zur Verwendung von Azure Storage, Bereitstellung von SAP-VMs oder SAP-Überwachung (siehe [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md)) gelten ebenfalls für Bereitstellungen von SAP ASE.

### <a name="sap-ase-version-support"></a>Versionsunterstützung für SAP ASE
Zur Verwendung mit Produkten der SAP Business Suite wird in SAP momentan SAP ASE Version 16.0 unterstützt. Jegliche Updates für SAP ASE-Server sowie JDBC- und ODBC-Treiber für die Verwendung mit Produkten der SAP Business Suite werden ausschließlich über den SAP Service Marketplace bereitgestellt: <https://support.sap.com/swdc>.

Laden Sie Updates für SAP ASE-Server und für JDBC- sowie ODBC-Treiber nicht direkt von den Sybase-Websites herunter. Ausführliche Informationen zu Patches, die für die lokale Verwendung mit SAP-Produkten und die Verwendung auf Azure-VMs unterstützt werden, finden Sie in den folgenden SAP-Hinweisen:

* [1590719]
* [1973241]

Allgemeine Informationen zum Ausführen der SAP Business Suite in SAP ASE finden Sie im [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>SAP ASE-Konfigurationsrichtlinien für SAP-bezogene SAP ASE-Installationen auf Azure-VMs
#### <a name="structure-of-the-sap-ase-deployment"></a>Struktur der SAP ASE-Bereitstellung
Ausführbare SAP ASE-Dateien müssen sich im Systemlaufwerk des Betriebssystemdatenträgers (Laufwerk „C:\“\) der VM befinden bzw. dort installiert werden. Für die meisten System- und Tooldatenbanken von SAP ASE liegen keine anspruchsvollen Workloads vor. Daher können die System- und Tooldatenbanken („master“, „model“, „saptools“, „sybmgmtdb“, „sybsystemdb“) auf dem Laufwerk „C:\“ verbleiben. 

Als Ausnahme kommt die temporäre Datenbank in Betracht, die im Falle einiger SAP ERP- sowie aller BW-Workloads höhere Volumes für Daten oder für E/A-Vorgänge erfordert. Volumes oder IOPS können nicht vom VM-Betriebssystemdatenträger (Laufwerk „C:\“\) zur Verfügung gestellt werden.

Abhängig von der zur Installation verwendeten Version von SAPInst/SWPM kann die Konfiguration der SAP ASE-Instanz folgendermaßen aussehen:

* Eine einzelne SAP ASE-tempdb, die bei der Installation von SAP ASE erstellt wird
* Eine bei der Installation von SAP ASE erstellte SAP ASE-tempdb sowie eine zusätzliche saptempdb, die durch die SAP-Installationsroutine erstellt wird
* Eine SAP ASE-tempdb, die bei der Installation von SAP ASE erstellt wird, sowie eine zusätzliche, (z.B. durch Befolgen der Schritte in SAP-Hinweis [1752266]) manuell erstellte tempdb, um bestimmte ERP/BW-spezifische Anforderungen an tempdb zu erfüllen

Bei bestimmten ERP- oder allen BW-Workloads ist es hinsichtlich der Leistung sinnvoll, die tempdb-Geräte der zusätzlich erstellten tempdb auf einem anderen Laufwerk als „C:\“ zu speichern. Wenn keine zusätzliche tempdb vorhanden ist, sollten Sie diese erstellen (siehe SAP-Hinweis [1752266]).

Trifft dies auf Ihr System zu, sollten Sie für die zusätzlich erstellte tempdb die folgenden Schritte ausführen:

* Verschieben Sie das erste tempdb-Gerät auf das erste Gerät der SAP-Datenbank.
* Fügen Sie tempdb-Geräte zu jeder der VHDs hinzu, die ein Gerät der SAP-Datenbank enthält.

Mit dieser Konfiguration kann tempdb mehr Speicherplatz nutzen, als auf dem Systemlaufwerk vorhanden ist. Als Vergleichsmaßstab können Sie die tempdb-Gerätegrößen bestehender lokaler Systeme heranziehen. Eine solche Konfiguration ermöglicht außerdem IOPS-Größenordnungen für tempdb, die das Systemlaufwerk nicht erzielen kann. Hier können lokal ausgeführte Systeme zur Überwachung des E/A-Workloads für tempdb verwendet werden.

Platzieren Sie niemals SAP ASE-Geräte auf dem Laufwerk „D:\“ des virtuellen Computers. Im Falle von SAP ASE gilt dies auch für die tempdb, auch wenn die Objekte in der tempdb nur temporär sind.

Aussagen und Empfehlungen zur Bereitstellung von Daten- und Transaktionsprotokolldateien finden Sie unter [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md). Für Windows-basierte Bereitstellungen wird die Verwendung von Windows-Speicherplätzen empfohlen, um Stripesets mit ausreichend IOPS, Durchsatz und Volume zu erstellen.  

#### <a name="impact-of-database-compression"></a>Auswirkungen der Datenbankkomprimierung
Bei Konfigurationen, bei denen die E/A-Bandbreite zum begrenzenden Faktor werden könnte, sollte jede Möglichkeit ergriffen werden, die IOPS zu senken. Dadurch kann die ausführbare Workload in einem IaaS-Szenario wie Azure besser verteilt werden. Es wird daher empfohlen, die SAP ASE-Komprimierung zu verwenden, bevor Sie eine vorhandene SAP-Datenbank in Azure hochladen.

Es ist aus mehreren Gründen ratsam, die Komprimierung vor dem Hochladen in Azure durchzuführen:

* Die in Azure hochzuladende Datenmenge ist geringer.
* Die Ausführungsdauer der Komprimierung ist geringer (unter der Annahme, dass lokal leistungsfähigere Hardware mit mehr CPUs, einer höheren E/A-Bandbreite oder einer geringeren E/A-Latenz zur Verfügung steht).
* Durch kleinere Datenbanken werden u. U. die Kosten für die Datenträgerzuordnung gesenkt.

Die Daten- und LOB-Komprimierung funktioniert auf einer VM, die auf einem virtuellen Azure-Computer gehostet wird, genauso wie in der lokalen Umgebung. Weitere Informationen zur Überprüfung, ob die Komprimierung bei einer bestehenden SAP ASE-Datenbank bereits in Verwendung ist, erhalten Sie in SAP-Hinweis [1750510].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Verwenden von DBACockpit für die Überwachung von Datenbankinstanzen
Bei SAP-Systemen, bei denen SAP ASE als Datenbankplattform verwendet wird, erfolgt der Zugriff auf das DBACockpit über eingebettete Browserfenster im Transaktions-DBACockpit oder über Webdynpro. Die vollständige Funktionalität für die Überwachung und Verwaltung der Datenbank steht allerdings nur über die Webdynpro-Implementierung von DBACockpit zur Verfügung.

Wie bei lokalen Systemen sind einige Schritte erforderlich, um die gesamte SAP NetWeaver-Funktionalität zu aktivieren, die durch die Webdynpro-Implementierung von DBACockpit genutzt wird. Befolgen Sie die Schritte in SAP-Hinweis [1245200], um die Verwendung von Webdynpros zu aktivieren und die erforderlichen Webdynpros zu generieren. Beim Befolgen der Anweisungen in den obigen Hinweisen werden Sie auch den Internet Communication Manager (ICM) zusammen mit den Ports konfigurieren, die für HTTP- und HTTPS-Verbindungen verwendet werden. Die Standardeinstellung für HTTP sieht wie folgt aus:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

Die im Transaktions-DBACockpit generierten Links ähneln den folgenden Links:

> https:\//\<vollqualifizierterhostname>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<vollqualifizierterhostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Der virtuelle Azure-Computer, auf dem das SAP-System gehostet wird, kann auf verschiedene Arten mit AD und dem DNS verbunden sein. In Abhängigkeit davon muss ICM einen vollqualifizierten Hostnamen verwenden, der von dem Computer aufgelöst werden kann, auf dem DBACockpit geöffnet wird. In SAP-Hinweis [773830] erfahren Sie, wie ICM den vollqualifizierten Hostnamen anhand von Profilparametern ermittelt. Legen Sie explizit den Parameter „icm/host_name_full“ fest, falls erforderlich.

Wird die VM in einem Nur-Cloud-Szenario ohne standortübergreifende Konnektivität zwischen der lokalen Umgebung und Azure bereitgestellt, müssen eine öffentliche IP-Adresse und eine Domänenbezeichnung festgelegt werden. Das Format des öffentlichen DNS-Namens der VM sieht folgendermaßen aus:

> `<custom domainlabel`&gt;.`<azure region`&gt;.cloudapp.azure.com
> 
> 

Einzelheiten zum DNS-Namen finden Sie [hier][virtual-machines-azurerm-versus-azuresm].

Wenn Sie den SAP-Profilparameter „icm/host_name_full“ auf den DNS-Namen der Azure-VM einstellen, erhalten Sie einen Link, der ähnlich wie dieser aussieht:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

Stellen Sie in diesem Fall Folgendes sicher:

* Fügen Sie der Netzwerksicherheitsgruppe im Azure-Portal für die TCP/IP-Ports, die für die Kommunikation mit ICM verwendet werden, eingehende Regeln hinzu.
* Fügen Sie der Windows-Firewall-Konfiguration für die TCP/IP-Ports, die für die Kommunikation mit ICM verwendet werden, eingehende Regeln hinzu.

Für den automatischen Import aller zur Verfügung stehenden Korrekturen wird empfohlen, regelmäßig den Ihre Version betreffenden SAP-Hinweis zur Korrektursammlung zu konsultieren:

* [1558958]
* [1619967]
* [1882376]

Weitere Informationen zu DBA Cockpit für SAP ASE erhalten Sie in den folgenden SAP-Hinweisen:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Überlegungen zur Sicherung und Wiederherstellung für SAP ASE
Bei der Bereitstellung von SAP ASE in Azure ist es erforderlich, die Sicherungsmethode zu überprüfen. Auch wenn es sich um kein Produktionssystem handelt, müssen SAP-Datenbanken regelmäßig gesichert werden. Da Azure Storage drei Images vorhält, ist die Sicherung, um einen etwaigen Speicherabsturz kompensieren zu können, weniger dringlich. Der wichtigste Grund, einen ordnungsgemäßen Sicherungs- und Wiederherstellungsplan zu verfolgen, ist vielmehr, dass Sie mithilfe der Point-in-Time-Wiederherstellung logische und manuelle Fehler beheben können. Das Ziel ist also entweder, Sicherungen für die Wiederherstellung des Zustands der Datenbank zu einem bestimmten Zeitpunkt zu verwenden, oder, mithilfe einer Sicherung in Azure durch Seeding ein anderes System aufzusetzen, indem die bestehende Datenbank kopiert wird. 

Sicherung und Wiederherstellung einer Datenbank in Azure funktionieren wie bei lokalen Umgebungen. Weitere Informationen finden Sie in diesen SAP-Hinweisen:

* [1588316]
* [1585981]

Darin wird erläutert, wie Abbildkonfigurationen erstellt und Sicherungen geplant werden. Je nach Strategie und Anforderungen können Sie Datenbank- und Protokollsicherungen für Datenträger konfigurieren und auf einem der vorhandenen Datenträgern speichern. Außerdem lässt sich ein zusätzlicher Datenträger zur Sicherung hinzufügen. Um die Gefahr von Datenverlusten bei einem Fehler zu reduzieren, wird empfohlen, einen Datenträger zu verwenden, auf dem sich keine Datenbankdateien befinden.

SAP ASE bietet neben der Daten- und LOB-Komprimierung auch die Sicherungskomprimierung. Um mit Datenbank- und Protokollsicherungen weniger Speicherplatz zu verbrauchen, wird empfohlen, die Sicherungskomprimierung zu verwenden. Weitere Informationen finden Sie in SAP-Hinweis [1588316]. Für den Fall, dass Sie zukünftig Sicherungen oder VHDs mit Sicherungsabbildungen vom virtuellen Azure-Computer auf lokale Rechner herunterladen möchten, wird durch Komprimieren der Sicherung auch die zu übertragende Datenmenge gesenkt.

Verwenden Sie das Laufwerk „D:\“ nicht als Speicherziel für Datenbank- oder Protokolldateien.

#### <a name="performance-considerations-for-backupsrestores"></a>Einfluss der Sicherung und Wiederherstellung auf die Leistung
Wie bei der Bare-Metal-Bereitstellung hängt die Leistung bei der Sicherung und Wiederherstellung davon ab, wie viele Volumes parallel ausgelesen werden können und wie hoch deren Durchsatz ist. Bedenken Sie, dass die Sicherungskomprimierung CPU-Ressourcen benötigt. Bei virtuellen Computern mit einer geringen Anzahl von CPU-Threads kann der CPU-Verbrauch durch die Sicherungskomprimierung eine bedeutende Rolle spielen. Es gilt also Folgendes:

* Je geringer die Anzahl an Datenträgern, auf denen sich Datenbankgeräte befinden, desto geringer der Durchsatz beim Auslesen insgesamt.
* Je weniger CPU-Threads in der VM, desto schwerwiegender der Einfluss der Sicherungskomprimierung.
* Je weniger Ziele (Stripesetverzeichnisse, Datenträger), in bzw. auf die Sicherungen geschrieben werden, desto geringer der Durchsatz.

Wenn Sie die Anzahl der Ziele erhöhen möchten, stehen Ihnen je nach Anforderungen zwei Optionen zur Verfügung, die Sie verwenden bzw. kombinieren können:

* Striping des Zielvolumes der Sicherung auf mehrere bereitgestellte Datenträger, wodurch der IOPS-Durchsatz auf den betreffenden Datenträgervolumes verbessert wird
* Erstellen einer Speicherabbildkonfiguration auf SAP ASE-Ebene, bei der mehr als ein Zielverzeichnis für das Speichern des Abbilds angegeben wird

Weitere Informationen zum Striping von Datenträgervolumes über mehrere bereitgestellte Datenträger finden Sie im Artikel [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md). Einzelheiten zur Verwendung mehrerer Verzeichnisse in der SAP ASE-Speicherabbildkonfiguration finden Sie in der Dokumentation zur gespeicherten Prozedur „sp_config_dump“. Diese Funktion wird verwendet, um die Speicherabbildkonfiguration im [Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp) zu erstellen.

### <a name="disaster-recovery-with-azure-vms"></a>Notfallwiederherstellung mit Azure-VMs
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Datenreplikation mit SAP Sybase Replication Server
Mit dem SAP Sybase Replication Server (SRS) bietet SAP ASE eine Lösung für den betriebsbereiten Standbymodus, mit der Datenbanktransaktionen asynchron an einen anderen Standort übertragen werden können. 

Installation und Betrieb von SRS funktionieren in einer VM, die in Azure Virtual Machine Services gehostet wird, genauso gut wie bei einer lokalen Implementierung.

SAP ASE HADR benötigt keine interne Azure Load Balancer-Instanz, und weist keine Abhängigkeiten vom Clustering auf Betriebssystemebene auf. Außerdem ist es für Windows Azure- und Linux-VMs verfügbar. Weitere Informationen zu SAP ASE HADR finden Sie im [SAP ASE HADR-Benutzerhandbuch](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.3/en-US/a6645e28bc2b1014b54b8815a64b87ba.html).

## <a name="specifics-to-sap-ase-on-linux"></a>Besonderheiten bei SAP ASE unter Linux
Ab Microsoft Azure lassen sich bestehende SAP ASE-Anwendungen einfach zu virtuellen Azure-Computern migrieren. Wenn Sie SAP ASE auf einem virtuellen Computer betreiben, können Sie die Gesamtbetriebskosten der Bereitstellung, Verwaltung und Wartung von unternehmensweiten Anwendungen senken, indem Sie diese Anwendungen einfach zu Microsoft Azure migrieren. Bei SAP ASE auf einem virtuellen Azure-Computer stehen Administratoren und Entwicklern die vertrauten Entwicklungs- und Verwaltungstools aus der lokalen Umgebung zur Verfügung.

Wichtig bei der Bereitstellung von Azure-VMs ist die Kenntnis der offiziellen SLAs. Diese finden Sie hier: <https://azure.microsoft.com/support/legal/sla>

Informationen zur jeweils empfohlenen SAP-Größe sowie eine Liste mit SAP-zertifizierten VM-SKUs erhalten Sie in SAP-Hinweis [1928533]. Weitere Dokumente zur SAP-Größe für Azure Virtual Machines finden Sie hier <https://blogs.msdn.com/b/saponsqlserver/archive/2015/06/19/how-to-size-sap-systems-running-on-azure-vms.aspx> und hier <https://blogs.msdn.com/b/saponsqlserver/archive/2015/12/01/new-white-paper-on-sizing-sap-solutions-on-azure-public-cloud.aspx>.

Aussagen und Empfehlungen zur Verwendung von Azure Storage, Bereitstellung von SAP-VMs oder SAP-Überwachung gelten auch für Bereitstellungen von SAP ASE in Verbindung mit SAP-Anwendungen (siehe die ersten vier Kapitel dieses Dokuments).

Die beiden folgenden SAP-Hinweise beinhalten allgemeine Informationen zu ASE unter Linux und ASE in der Cloud:

* [2134316]
* [1941500]

### <a name="sap-ase-version-support"></a>Versionsunterstützung für SAP ASE
Zur Verwendung mit Produkten der SAP Business Suite wird in SAP momentan SAP ASE Version 16.0 unterstützt. Jegliche Updates für SAP ASE-Server sowie JDBC- und ODBC-Treiber für die Verwendung mit Produkten der SAP Business Suite werden ausschließlich über den SAP Service Marketplace bereitgestellt: <https://support.sap.com/swdc>.

Wie für alle lokalen Installationen gilt: Laden Sie Updates für SAP ASE-Server und für JDBC- sowie ODBC-Treiber nicht direkt von den Sybase-Websites herunter. Detaillierte Informationen zu Patches, die für die lokale Verwendung mit Produkten der SAP Business Suite und die Verwendung mit virtuellen Azure-Computern unterstützt werden, erhalten Sie in den folgenden SAP-Hinweisen:

* [1590719]
* [1973241]

Allgemeine Informationen zum Ausführen der SAP Business Suite in SAP ASE finden Sie im [SCN](https://www.sap.com/community/topic/ase.html)

### <a name="sap-ase-configuration-guidelines-for-sap-related-sap-ase-installations-in-azure-vms"></a>SAP ASE-Konfigurationsrichtlinien für SAP-bezogene SAP ASE-Installationen auf Azure-VMs
#### <a name="structure-of-the-sap-ase-deployment"></a>Struktur der SAP ASE-Bereitstellung
Ausführbare SAP ASE-Dateien müssen sich im Stammdateisystem der VM (/sybase) befinden bzw. dort installiert werden. Für die meisten System- und Tooldatenbanken von SAP ASE liegen keine anspruchsvollen Workloads vor. Daher können die System- und Tooldatenbanken („master“, „model“, „saptools“, „sybmgmtdb“, „sybsystemdb“) im Stammdateisystem gespeichert bleiben. 

Als Ausnahme kommt die temporäre Datenbank in Betracht, die im Falle einiger SAP ERP- sowie aller BW-Workloads höhere Volumes für Daten oder für E/A-Vorgänge erfordert. Volumes oder IOPS können nicht vom VM-Betriebssystemdatenträger zur Verfügung gestellt werden. 

Abhängig von der Version von SAPInst/SWPM, die für die Installation des Systems verwendet wurde, kann die Datenbank Folgendes enthalten:

* Eine einzelne SAP ASE-tempdb, die bei der Installation von SAP ASE erstellt wird
* Eine bei der Installation von SAP ASE erstellte SAP ASE-tempdb sowie eine zusätzliche saptempdb, die durch die SAP-Installationsroutine erstellt wird
* Eine SAP ASE-tempdb, die bei der Installation von SAP ASE erstellt wird, sowie eine zusätzliche, (z.B. durch Befolgen der Schritte in SAP-Hinweis [1752266]) manuell erstellte tempdb, um bestimmte ERP/BW-spezifische Anforderungen an tempdb zu erfüllen

Aus Leistungsgründen kann es für bestimmte ERP- bzw. alle BW-Workloads sinnvoll sein, die tempdb-Geräte der (durch SWPM oder manuell) zusätzlich erstellten tempdb auf einem gesonderten Dateisystem zu speichern. Dieses kann durch einen einzelnen Azure-Datenträger oder durch Linux-RAID mit mehreren Azure-Datenträgern dargestellt werden. Wenn keine zusätzliche tempdb vorhanden ist, sollten Sie diese erstellen (siehe SAP-Hinweis [1752266]).

Trifft dies auf Ihr System zu, sollten Sie für die zusätzlich erstellte tempdb die folgenden Schritte ausführen:

* Verschieben Sie das Verzeichnis der ersten tempdb in das erste Dateisystem der SAP-Datenbank.
* Fügen Sie tempdb-Verzeichnisse zu jedem Datenträger hinzu, der ein Dateisystem der SAP-Datenbank enthält.

Mit dieser Konfiguration kann tempdb mehr Speicherplatz nutzen, als auf dem Systemlaufwerk vorhanden ist. Als Vergleichsmaßstab können Sie die tempdb-Gerätegrößen bestehender lokaler Systeme heranziehen. Eine solche Konfiguration ermöglicht außerdem IOPS-Größenordnungen für tempdb, die das Systemlaufwerk nicht erzielen kann. Hier können lokal ausgeführte Systeme zur Überwachung des E/A-Workloads für tempdb verwendet werden.

SAP ASE-Verzeichnisse dürfen sich unter keinen Umständen auf „/mnt“ oder „/mnt/resource“ der VM befinden. Im Falle von SAP ASE gilt dies auch für die tempdb, auch wenn die Objekte in der tempdb nur temporär sind. Denn „/mnt“ oder „/mnt/resource“ ist ein standardmäßiger temporärer Speicherplatz von Azure-VMs, der nicht permanent ist. Weitere Informationen zu temporären Speicherplätzen von virtuellen Azure-Computern erhalten Sie in [diesem Artikel][virtual-machines-linux-how-to-attach-disk].

Aussagen und Empfehlungen zur Bereitstellung von Daten- und Transaktionsprotokolldateien finden Sie unter [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md). Für Linux-basierte Bereitstellungen wird die Verwendung von LVM oder MDADM empfohlen, um Stripesets mit ausreichend IOPS, Durchsatz und Volume zu erstellen. 

#### <a name="impact-of-database-compression"></a>Auswirkungen der Datenbankkomprimierung
Bei Konfigurationen, bei denen die E/A-Bandbreite zum begrenzenden Faktor werden könnte, sollte jede Möglichkeit ergriffen werden, die IOPS zu senken. Dadurch kann die ausführbare Workload in einem IaaS-Szenario wie Azure besser verteilt werden. Es wird daher empfohlen, die SAP ASE-Komprimierung zu verwenden, bevor Sie eine vorhandene SAP-Datenbank in Azure hochladen.

Es ist aus mehreren Gründen ratsam, die Komprimierung vor dem Hochladen in Azure durchzuführen:

* Die in Azure hochzuladende Datenmenge ist geringer.
* Die Ausführungsdauer der Komprimierung ist geringer (unter der Annahme, dass lokal leistungsfähigere Hardware mit mehr CPUs, einer höheren E/A-Bandbreite oder einer geringeren E/A-Latenz zur Verfügung steht).
* Durch kleinere Datenbanken werden u. U. die Kosten für die Datenträgerzuordnung gesenkt.

Die Daten- und LOB-Komprimierung funktioniert auf einer VM, die auf einem virtuellen Azure-Computer gehostet wird, genauso wie in der lokalen Umgebung. Weitere Informationen zur Überprüfung, ob die Komprimierung bei einer bestehenden SAP ASE-Datenbank bereits in Verwendung ist, erhalten Sie in SAP-Hinweis [1750510]. Weitere Informationen zur Datenbankkomprimierung finden Sie im SAP-Hinweis [2121797].

#### <a name="using-dbacockpit-to-monitor-database-instances"></a>Verwenden von DBACockpit für die Überwachung von Datenbankinstanzen
Bei SAP-Systemen, bei denen SAP ASE als Datenbankplattform verwendet wird, erfolgt der Zugriff auf das DBACockpit über eingebettete Browserfenster im Transaktions-DBACockpit oder über Webdynpro. Die vollständige Funktionalität für die Überwachung und Verwaltung der Datenbank steht allerdings nur über die Webdynpro-Implementierung von DBACockpit zur Verfügung.

Wie bei lokalen Systemen sind einige Schritte erforderlich, um die gesamte SAP NetWeaver-Funktionalität zu aktivieren, die durch die Webdynpro-Implementierung von DBACockpit genutzt wird. Befolgen Sie die Schritte in SAP-Hinweis [1245200], um die Verwendung von Webdynpros zu aktivieren und die erforderlichen Webdynpros zu generieren. Beim Befolgen der Anweisungen in den obigen Hinweisen werden Sie auch den Internet Communication Manager (ICM) zusammen mit den Ports konfigurieren, die für HTTP- und HTTPS-Verbindungen verwendet werden. Die Standardeinstellung für HTTP sieht wie folgt aus:

> icm/server_port_0 = PROT=HTTP,PORT=8000,PROCTIMEOUT=600,TIMEOUT=600
> 
> icm/server_port_1 = PROT=HTTPS,PORT=443$$,PROCTIMEOUT=600,TIMEOUT=600
> 
> 

Die im Transaktions-DBACockpit generierten Links sehen so aus:

> https:\//\<vollqualifizierterhostname>:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//\<vollqualifizierterhostname>:8000/sap/bc/webdynpro/sap/dba_cockpit
> 
> 

Der virtuelle Azure-Computer, auf dem das SAP-System gehostet wird, kann auf verschiedene Arten mit AD und dem DNS verbunden sein. In Abhängigkeit davon muss ICM einen vollqualifizierten Hostnamen verwenden, der von dem Computer aufgelöst werden kann, auf dem DBACockpit geöffnet wird. In SAP-Hinweis [773830] erfahren Sie, wie ICM den vollqualifizierten Hostnamen anhand von Profilparametern ermittelt. Legen Sie explizit den Parameter „icm/host_name_full“ fest, falls erforderlich.

Wird die VM in einem Nur-Cloud-Szenario ohne standortübergreifende Konnektivität zwischen der lokalen Umgebung und Azure bereitgestellt, müssen eine öffentliche IP-Adresse und eine Domänenbezeichnung festgelegt werden. Das Format des öffentlichen DNS-Namens der VM sieht folgendermaßen aus:

> `<custom domainlabel`&gt;.`<azure region`&gt;.cloudapp.azure.com
> 
> 

Einzelheiten zum DNS-Namen finden Sie [hier][virtual-machines-azurerm-versus-azuresm].

Wenn Sie den SAP-Profilparameter „icm/host_name_full“ auf den DNS-Namen der Azure-VM einstellen, erhalten Sie einen Link, der ähnlich wie dieser aussieht:

> https:\//mydomainlabel.westeurope.cloudapp.net:44300/sap/bc/webdynpro/sap/dba_cockpit
> 
> http:\//mydomainlabel.westeurope.cloudapp.net:8000/sap/bc/webdynpro/sap/dba_cockpit

Stellen Sie in diesem Fall Folgendes sicher:

* Fügen Sie der Netzwerksicherheitsgruppe im Azure-Portal für die TCP/IP-Ports, die für die Kommunikation mit ICM verwendet werden, eingehende Regeln hinzu.
* Fügen Sie der Windows-Firewall-Konfiguration für die TCP/IP-Ports, die für die Kommunikation mit ICM verwendet werden, eingehende Regeln hinzu.

Für den automatischen Import aller zur Verfügung stehenden Korrekturen wird empfohlen, regelmäßig den Ihre Version betreffenden SAP-Hinweis zur Korrektursammlung zu konsultieren:

* [1558958]
* [1619967]
* [1882376]

Weitere Informationen zu DBA Cockpit für SAP ASE erhalten Sie in den folgenden SAP-Hinweisen:

* [1605680]
* [1757924]
* [1757928]
* [1758182]
* [1758496]    
* [1814258]
* [1922555]
* [1956005]

#### <a name="backuprecovery-considerations-for-sap-ase"></a>Überlegungen zur Sicherung und Wiederherstellung für SAP ASE
Bei der Bereitstellung von SAP ASE in Azure ist es erforderlich, die Sicherungsmethode zu überprüfen. Auch wenn es sich um kein Produktionssystem handelt, müssen SAP-Datenbanken regelmäßig gesichert werden. Da Azure Storage drei Images vorhält, ist die Sicherung, um einen etwaigen Speicherabsturz kompensieren zu können, weniger dringlich. Der wichtigste Grund, einen ordnungsgemäßen Sicherungs- und Wiederherstellungsplan zu verfolgen, ist vielmehr, dass Sie mithilfe der Point-in-Time-Wiederherstellung logische und manuelle Fehler beheben können. Das Ziel ist also entweder, Sicherungen für die Wiederherstellung des Zustands der Datenbank zu einem bestimmten Zeitpunkt zu verwenden, oder, mithilfe einer Sicherung in Azure durch Seeding ein anderes System aufzusetzen, indem die bestehende Datenbank kopiert wird. 

Sicherung und Wiederherstellung einer Datenbank in Azure funktionieren wie bei lokalen Umgebungen. Weitere Informationen finden Sie in diesen SAP-Hinweisen:

* [1588316]
* [1585981]

Darin wird erläutert, wie Abbildkonfigurationen erstellt und Sicherungen geplant werden. Je nach Strategie und Anforderungen können Sie Datenbank- und Protokollsicherungen für Datenträger konfigurieren und auf einem der vorhandenen Datenträgern speichern. Außerdem lässt sich ein zusätzlicher Datenträger zur Sicherung hinzufügen. Um die Gefahr von Datenverlusten bei einem Fehler zu verringern, wird empfohlen, einen Datenträger zu verwenden, auf dem sich kein Datenbankverzeichnis bzw. keine Datenbankdatei befindet.

SAP ASE bietet neben der Daten- und LOB-Komprimierung auch die Sicherungskomprimierung. Um mit Datenbank- und Protokollsicherungen weniger Speicherplatz zu verbrauchen, wird empfohlen, die Sicherungskomprimierung zu verwenden. Weitere Informationen finden Sie in SAP-Hinweis [1588316]. Für den Fall, dass Sie zukünftig Sicherungen oder VHDs mit Sicherungsabbildungen vom virtuellen Azure-Computer auf lokale Rechner herunterladen möchten, wird durch Komprimieren der Sicherung auch die zu übertragende Datenmenge gesenkt.

Verwenden Sie nicht die von der Azure-VM verwendeten temporären Speicherorte „/mnt“ oder „/mnt/resource“ als Ziel für Datenbank- oder Protokollabbilder.

#### <a name="performance-considerations-for-backupsrestores"></a>Einfluss der Sicherung und Wiederherstellung auf die Leistung
Wie bei der Bare-Metal-Bereitstellung hängt die Leistung bei der Sicherung und Wiederherstellung davon ab, wie viele Volumes parallel ausgelesen werden können und wie hoch deren Durchsatz ist. Bedenken Sie, dass die Sicherungskomprimierung CPU-Ressourcen benötigt. Bei virtuellen Computern mit einer geringen Anzahl von CPU-Threads kann der CPU-Verbrauch durch die Sicherungskomprimierung eine bedeutende Rolle spielen.  Es gilt also Folgendes:

* Je geringer die Anzahl an Datenträgern, auf denen sich Datenbankgeräte befinden, desto geringer der Durchsatz beim Auslesen insgesamt.
* Je weniger CPU-Threads in der VM, desto schwerwiegender der Einfluss der Sicherungskomprimierung.
* Je weniger Sicherungsziele (Linux-Software-RAID, Datenträger), desto geringer der Durchsatz.

Wenn Sie die Anzahl der Ziele erhöhen möchten, stehen Ihnen je nach Anforderungen zwei Optionen zur Verfügung, die Sie verwenden bzw. kombinieren können:

* Striping des Zielvolumes der Sicherung auf mehrere bereitgestellte Datenträger, wodurch der IOPS-Durchsatz auf den betreffenden Datenträgervolumes verbessert wird
* Erstellen einer Speicherabbildkonfiguration auf SAP ASE-Ebene, bei der mehr als ein Zielverzeichnis für das Speichern des Abbilds angegeben wird

Weitere Informationen zum Striping von Datenträgervolumes über mehrere bereitgestellte Datenträger finden Sie im Artikel [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md). Einzelheiten zur Verwendung mehrerer Verzeichnisse in der SAP ASE-Speicherabbildkonfiguration finden Sie in der Dokumentation zur gespeicherten Prozedur „sp_config_dump“. Diese Funktion wird verwendet, um die Speicherabbildkonfiguration im [Sybase Infocenter](http://infocenter.sybase.com/help/index.jsp) zu erstellen.

### <a name="disaster-recovery-with-azure-vms"></a>Notfallwiederherstellung mit Azure-VMs
#### <a name="data-replication-with-sap-sybase-replication-server"></a>Datenreplikation mit SAP Sybase Replication Server
Mit dem SAP Sybase Replication Server (SRS) bietet SAP ASE eine Lösung für den betriebsbereiten Standbymodus, mit der Datenbanktransaktionen asynchron an einen anderen Standort übertragen werden können. 

Installation und Betrieb von SRS funktionieren in einer VM, die in Azure Virtual Machine Services gehostet wird, genauso gut wie bei einer lokalen Implementierung.

ASE HADR über SAP Replication Server wird unterstützt. Es wird dringend empfohlen, SAP ASE 16.03 für eine solche Konfiguration zu verwenden. Genauere Anweisungen zur Installation solcher Konfigurationen finden Sie [in diesem Blog](https://blogs.msdn.microsoft.com/saponsqlserver/2018/06/18/installation-procedure-for-sybase-16-3-patch-level-3-always-on-dr-on-suse-12-3-recent-customer-proof-of-concept/).
