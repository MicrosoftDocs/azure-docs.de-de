---
title: Azure Virtual Machines – Bereitstellung für SAP NetWeaver | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie SAP-Software auf virtuellen Linux-Computern in Azure bereitstellen.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/16/2020
ms.author: sedusch
ms.openlocfilehash: ed30c271e4c2458a33784cbcfc682001b542f2b6
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964948"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>Azure Virtual Machines – Bereitstellung für SAP NetWeaver

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
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
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:../../../cli-install-nodejs.md
[azure-cli-2]:https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (Azure Virtual Machines – DBMS-Bereitstellung für SAP)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (Caching für VMs und VHDs)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Software RAID)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure Storage)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (Struktur einer RDBMS-Bereitstellung)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Hohe Verfügbarkeit und Notfallwiederherstellung mit virtuellen Azure-Computern)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 und höher)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 und frühere Versionen)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Verwenden eines SQL Server-Images aus dem Azure Marketplace)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (SQL Server für SAP in Azure – Allgemeine Zusammenfassung)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (Besonderheiten bei SQL Server-RDBMS)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (Speicherkonfiguration)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (Sicherung und Wiederherstellung)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (Leistungsüberlegungen hinsichtlich Sicherung und Wiederherstellung)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (Sonstiges)
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

[deployment-guide]:deployment-guide.md (Bereitstellung von Azure Virtual Machines für SAP)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP-Ressourcen)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (Bereitstellen eines virtuellen Computers mithilfe eines benutzerdefinierten Images)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (Szenario 1: Bereitstellen eines virtuellen Computers aus dem Azure Marketplace für SAP)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (Szenario 2: Bereitstellen eines virtuellen Computers mit einem benutzerdefinierten Image für SAP)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (Szenario 3: Verschieben eines lokalen virtuellen Computers mit einer nicht generalisierten virtuellen Azure-Festplatte mit SAP)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Bereitstellungsszenarien für virtuelle Computer für SAP in Microsoft Azure)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Bereitstellen von Azure PowerShell-Cmdlets)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (Herunterladen und Importieren relevanter PowerShell-Cmdlets für SAP)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (Einbinden eines virtuellen Computers in eine lokale Domäne [nur Windows])
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Herunterladen, Installieren und Aktivieren des Azure-VM-Agents)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Konfigurieren der Azure-Erweiterung für SAP)
[deployment-guide-configure-new-extension-ps]:deployment-guide.md#2ad55a0d-9937-4943-9dd2-69bc2b5d3de0 (Konfigurieren der neuen Azure-Erweiterung für SAP mit Azure PowerShell)
[deployment-guide-configure-new-extension-cli]:deployment-guide.md#c8749c24-fada-42ad-b114-f9aae2dc37da (Konfigurieren der neuen Azure-Erweiterung für SAP mit Azure CLI)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Bereitschaftsprüfung für die Azure-Erweiterung für SAP)
[deployment-guide-5.1-new]:deployment-guide.md#7bf24f59-7347-4c7a-b094-4693e4687ee5 (Bereitschaftsprüfung für die neue Azure-Erweiterung für SAP)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Integritätsprüfung für die Azure-Erweiterung für die SAP-Konfiguration)
[deployment-guide-5.2-new]:deployment-guide.md#464ac96d-7d3c-435d-a5ae-3faf3bfef4b3 (Integritätsprüfung für die neue Azure-Erweiterung für die SAP-Konfiguration)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Problembehandlung für die Azure-Erweiterung für SAP)
[deployment-guide-5.3-new]:deployment-guide.md#b7afb8ef-a64c-495d-bb37-2af96688c530 (Problembehandlung für die neue Azure-Erweiterung für SAP)
[deployment-guide-contact-support]:deployment-guide.md#3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2 (Problembehandlung für die Azure-Erweiterung für SAP – Support kontaktieren)
[deployment-guide-run-the-script]:deployment-guide.md#0d2847ad-865d-4a4c-a405-f9b7baaa00c7 (Problembehandlung für die Azure-Erweiterung für SAP – Ausführen des Setups)
[deployment-guide-redeploy-after-sysprep]:deployment-guide.md#2cd61f22-187d-42ed-bb8c-def0c983d756 (Problembehandlung für die Azure-Erweiterung für SAP – erneute Bereitstellung nach Sysprep)
[deployment-guide-fix-internet-connection]:deployment-guide.md#e92bc57d-80d9-4a2b-a2f4-16713a22ad89 ( Problembehandlung für die Azure-Erweiterung für SAP – Korrektur der Internetverbindung)


[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Konfigurieren der VM-Erweiterung)
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Konfigurieren des Proxys)
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
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Fehler und Problembehandlung)

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

[msdn-set-Azvmaemextension]:https://docs.microsoft.com/powershell/module/az.compute/set-azvmaemextension

[planning-guide]:planning-guide.md (Azure Virtual Machines – Planung und Implementierung für SAP)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (Ressourcen)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Hochverfügbarkeit und Notfallwiederherstellung für SAP NetWeaver auf Azure Virtual Machines)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (Hochverfügbarkeit für SAP-Anwendungsserver)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (Verwenden des automatischen Starts für SAP-Instanzen)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (Nur Cloud: Bereitstellungen virtueller Computer in Azure ohne Abhängigkeiten vom lokalen Kundennetzwerk)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (Standortübergreifend: Bereitstellung einzelner oder mehrerer virtueller SAP-Computer in Azure, die vollständig in das lokale Netzwerk integriert sind)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure-Regionen)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (Fehlerdomänen)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (Upgradedomänen)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Azure-Verfügbarkeitsgruppen)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Konzept der virtuellen Microsoft Azure-Computer)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (Verschieben eines virtuellen lokalen Computers mit einem nicht generalisierten Datenträger zu Azure)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (Bereitstellen eines virtuellen Computers mit einem kundenspezifischen Image)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (Vorbereitung der Verschiebung eines virtuellen lokalen Computers mit einem nicht generalisierten Datenträger in Azure)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (Vorbereitung der Bereitstellung eines virtuellen Computers mit einem kundenspezifischen Image für SAP)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Vorbereiten virtueller Computer mit SAP für Azure)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Unterschied zwischen einem Azure-Datenträger und einem Azure-Image)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (Hochladen einer virtuellen Festplatte vom lokalen Standort zu Azure)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Kopieren von Datenträgern zwischen Azure Storage-Konten)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (VM-/VHD-Struktur für SAP-Bereitstellungen)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (Festlegen der automatischen Bereitstellung für angefügte Datenträger)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure-Überwachungslösung für SAP)
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
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure-Netzwerk)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Speicher: Microsoft Azure Storage und Datenträger)

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]:https://support.sap.com/pam (SAP-Produktverfügbarkeitsmatrix [Product Availability Matrix, PAM])
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-os-disk-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk-md%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-2-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image-md%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image-md%2Fazuredeploy.json
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
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Bereitstellen und Verwalten von virtuellen Computern mit Azure Resource Manager-Vorlagen und der Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Verwalten virtueller Computer mit Azure Resource Manager und PowerShell)
[virtual-machines-windows-agent-user-guide]:../../extensions/agent-windows.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-linux.md#command-line-options
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
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../windows/quick-create-powershell.md
[virtual-machines-sizes]:../../linux/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
[virtual-machines-upload-image-windows-resource-manager]:../../windows/upload-image.md
[virtual-machines-windows-tutorial]:../../windows/quick-create-portal.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
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
[qs-configure-powershell-windows-vm]:../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md
[qs-configure-cli-windows-vm]:../../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md
[howto-assign-access-powershell]:../../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md
[howto-assign-access-cli]:../../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Azure Virtual Machines eignet sich als Lösung für Organisationen, die Compute- und Speicherressourcen in kürzester Zeit und ohne langwierige Beschaffungszyklen benötigen. Sie können Azure Virtual Machines verwenden, um klassische Anwendungen, z.B. SAP NetWeaver-basierte Anwendungen, in Azure bereitzustellen. Erhöhen Sie die Zuverlässigkeit und Verfügbarkeit einer Anwendung ohne zusätzliche lokale Ressourcen. Azure Virtual Machines unterstützt die standortübergreifende Konnektivität, sodass Sie Azure Virtual Machines in die lokalen Domänen, privaten Clouds und die SAP-Systemumgebung Ihrer Organisation integrieren können.

Dieser Artikel beschreibt die Schritte zum Bereitstellen von SAP-Anwendungen auf virtuellen Computern (VMs) in Azure sowie alternative Bereitstellungsoptionen und Problembehandlungsoptionen. Dieser Artikel baut auf den Informationen unter [Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver][planning-guide] auf. Außerdem ist der Artikel eine Ergänzung der SAP-Dokumentation zur Installation und der SAP-Hinweise, also der Hauptressourcen für die Installation und Bereitstellung von SAP-Software.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Zum Einrichten eines virtuellen Azure-Computers für die Bereitstellung der SAP-Software sind mehrere Schritte und Ressourcen erforderlich. Stellen Sie zu Beginn sicher, dass die Voraussetzungen für die Installation der SAP-Software auf virtuellen Computern in Azure erfüllt sind.

### <a name="local-computer"></a>Lokalem Computer

Zum Verwalten von Windows- oder Linux-VMs können Sie ein PowerShell-Skript und das Azure-Portal verwenden. Für beide Tools benötigen Sie einen lokalen Computer mit Windows 7 oder einer höheren Version von Windows. Falls Sie nur Linux-VMs verwalten und einen Linux-Computer für diese Aufgabe einsetzen möchten, können Sie die Azure-Befehlszeilenschnittstelle verwenden.

### <a name="internet-connection"></a>Internetverbindung

Zum Herunterladen und Ausführen der Tools und Skripts, die für die Bereitstellung von SAP-Software erforderlich sind, muss eine Internetverbindung bestehen. Für die Azure-VM, auf der die Azure-Erweiterung für SAP ausgeführt wird, wird ebenfalls Zugriff auf das Internet benötigt. Falls die Azure-VM Teil eines virtuellen Azure-Netzwerks oder einer lokalen Domäne ist, sollten Sie auf eine korrekte Einrichtung der entsprechenden Proxyeinstellungen achten. Diese werden unter [Konfigurieren des Proxys][deployment-guide-configure-proxy] beschrieben.

### <a name="microsoft-azure-subscription"></a>Microsoft Azure-Abonnement

Sie benötigen ein aktives Azure-Konto.

### <a name="topology-and-networking"></a>Topologie und Netzwerk

Sie müssen die Topologie und Architektur der SAP-Bereitstellung in Azure definieren:

* Zu verwendende Azure-Speicherkonten
* Virtuelles Netzwerk, in dem Sie das SAP-System bereitstellen möchten
* Ressourcengruppe, in der Sie das SAP-System bereitstellen möchten
* Azure-Region, in der Sie das SAP-System bereitstellen möchten
* SAP-Konfiguration (zwei oder drei Ebenen)
* VM-Größen und die Anzahl von zusätzlichen Datenträgern für die Bereitstellung auf den VMs
* Konfiguration des SAP Correction and Transport System (CTS)

Erstellen und konfigurieren Sie Azure-Speicherkonten (sofern erforderlich) oder virtuelle Azure-Netzwerke, bevor Sie mit dem Prozess zur Bereitstellung von SAP-Software beginnen. Informationen dazu, wie Sie diese Ressourcen erstellen und konfigurieren, finden Sie unter [Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver][planning-guide].

### <a name="sap-sizing"></a>SAP-Größenanpassung

Für die SAP-Größenanpassung sind die folgenden Informationen wichtig:

* Ermittlung der projizierten SAP-Workload, z.B. mit dem SAP Quick Sizer-Tool, und der SAPS-Zahl (SAP Application Performance Standard)
* Für das SAP-System erforderliche CPU-Ressourcen und der Arbeitsspeicherverbrauch
* Erforderliche Eingabe-/Ausgabevorgänge (E/A) pro Sekunde
* Erforderliche Netzwerkbandbreite für die spätere Kommunikation zwischen VMs in Azure
* Erforderliche Netzwerkbandbreite zwischen lokalen Assets und dem über Azure bereitgestellten SAP-System

### <a name="resource-groups"></a>Ressourcengruppen

In Azure Resource Manager können Sie mit Ressourcengruppen alle Anwendungsressourcen im Azure-Abonnement verwalten. Weitere Informationen finden Sie unter [Übersicht über den Azure Resource Manager][resource-group-overview].

## <a name="resources"></a>Ressourcen

### <a name="sap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>SAP-Ressourcen

Beim Einrichten der Bereitstellung Ihrer SAP-Software benötigen Sie die folgenden SAP-Ressourcen:

* SAP-Hinweis [1928533] mit folgenden Informationen:
  * Liste der Azure-VM-Größen, die für die Bereitstellung von SAP-Software unterstützt werden
  * Wichtige Kapazitätsinformationen für Größen von Azure-VMs
  * Unterstützte SAP-Software und Kombinationen aus Betriebssystem (OS) und Datenbank
  * Erforderliche SAP-Kernelversion für Windows und Linux in Microsoft Azure

* In SAP-Hinweis [2015553] sind die Voraussetzungen für Bereitstellungen von SAP-Software in Azure aufgeführt, die von SAP unterstützt werden.
* SAP-Hinweis [2178632] enthält ausführliche Informationen zu allen Überwachungsmetriken, die für SAP in Azure gemeldet werden.
* SAP-Hinweis [1409604] enthält die erforderliche SAP Host Agent-Version für Windows in Azure.
* SAP-Hinweis [2191498] enthält die erforderliche SAP Host Agent-Version für Linux in Azure.
* SAP-Hinweis [2243692] enthält Informationen zur SAP-Lizenzierung unter Linux in Azure.
* SAP-Hinweis [1984787] enthält allgemeine Informationen zu SUSE Linux Enterprise Server 12.
* SAP-Hinweis [2002167] enthält allgemeine Informationen zu Red Hat Enterprise Linux 7.x.
* SAP-Hinweis [2069760] enthält allgemeine Informationen zu Oracle Linux 7.x.
* SAP-Hinweis [1999351] enthält Informationen zur Problembehandlung für die Azure-Erweiterung für SAP.
* SAP-Hinweis [1597355] enthält allgemeine Informationen zum Auslagerungsbereich für Linux.
* Die [SCN-Seite zu SAP in Azure](https://wiki.scn.sap.com/wiki/x/Pia7Gg) bietet Neuigkeiten und eine Sammlung nützlicher Ressourcen.
* Das [WIKI der SAP-Community](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) enthält alle erforderlichen SAP-Hinweise für Linux.
* SAP-spezifische PowerShell-Cmdlets, die Teil von [Azure PowerShell][azure-ps] sind.
* SAP-spezifische Befehle der Azure-Befehlszeilenschnittstelle, die Teil der [Azure-Befehlszeilenschnittstelle][azure-cli] sind.

### <a name="windows-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Windows-Ressourcen

In diesen Microsoft-Artikeln werden SAP-Bereitstellungen in Azure behandelt:

* [Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver][planning-guide]
* [Azure Virtual Machines – Bereitstellung für SAP NetWeaver (dieser Artikel)][deployment-guide]
* [Azure Virtual Machines – DBMS-Bereitstellung für SAP NetWeaver][dbms-guide]

## <a name="deployment-scenarios-for-sap-software-on-azure-vms"></a><a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Bereitstellungsszenarien für SAP-Software auf Azure-VMs

Sie haben mehrere Optionen, um VMs und die dazugehörigen Datenträger in Azure bereitzustellen. Es ist wichtig, die Unterschiede zwischen den Bereitstellungsoptionen zu verstehen. Der Grund ist, dass Sie unter Umständen je nach gewähltem Bereitstellungstyp andere Schritte ausführen, um Ihre VMs für die Bereitstellung vorzubereiten.

### <a name="scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap"></a><a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Szenario 1: Bereitstellen eines virtuellen Computers aus dem Azure Marketplace für SAP

Sie können ein Image verwenden, das von Microsoft oder einem Drittanbieter im Azure Marketplace angeboten wird, um Ihre VM bereitzustellen. Auf dem Marketplace sind einige OS-Standardimages von Windows Server und verschiedenen Linux-Distributionen verfügbar. Sie können auch ein Image bereitstellen, das DBMS-SKUs (Database Management System) enthält, z.B. Microsoft SQL Server. Weitere Informationen zur Verwendung von Images mit DBMS-SKUs finden Sie unter [Azure Virtual Machines – DBMS-Bereitstellung für SAP NetWeaver][dbms-guide].

Im folgenden Flussdiagramm ist die SAP-spezifische Schrittfolge zum Bereitstellen einer VM über den Azure Marketplace dargestellt:

![Flussdiagramm der VM-Bereitstellung für SAP-Systeme mithilfe eines VM-Image aus dem Azure Marketplace][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Erstellen eines virtuellen Computers mit dem Azure-Portal

Die einfachste Möglichkeit zum Erstellen eines neuen virtuellen Computers mit einem Image aus dem Azure Marketplace ist die Verwendung des Azure-Portals.

1.  Gehe zu <https://portal.azure.com/#create/hub>.  Oder wählen Sie im Azure-Portal die Option **+ Neu**.
1.  Wählen Sie **Compute** und anschließend den Betriebssystemtyp aus, den Sie bereitstellen möchten. Beispiel: Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12), Red Hat Enterprise Linux 7.2 (RHEL 7.2) oder Oracle Linux 7.2. In der Standardlistenansicht werden nicht alle unterstützten Betriebssysteme angezeigt. Wählen Sie die Option **Alle anzeigen**, um eine vollständige Liste anzuzeigen. Weitere Informationen zu unterstützten Betriebssystemen für die Bereitstellung von SAP-Software finden Sie im SAP-Hinweis [1928533].
1.  Lesen Sie sich auf der nächsten Seite die Geschäftsbedingungen durch.
1.  Wählen Sie unter **Bereitstellungsmodell auswählen** die Option **Resource Manager**.
1.  Klicken Sie auf **Erstellen**.

Im Assistenten werden Sie durch die Schritte zum Festlegen der erforderlichen Parameter für die Erstellung des virtuellen Computers mit allen erforderlichen Ressourcen wie Netzwerkschnittstellen oder Speicherkonten geführt. Diese Parameter sind beispielsweise:

1. **Grundlagen**:
   * **Name**: Dies ist der Name der Ressource (Name des virtuellen Computers).
   * **VM-Datenträgertyp**: Wählen Sie den Datenträgertyp des Betriebssystem-Datenträgers aus. Wenn Sie Storage Premium für Ihre Datenträger für Daten verwenden möchten, empfiehlt es sich, auch für den Betriebssystem-Datenträger Storage Premium zu nutzen.
   * **Benutzername und Kennwort** oder **öffentlicher SSH-Schlüssel**: Geben Sie Benutzernamen und Kennwort des Benutzers ein, der während der Bereitstellung erstellt wird. Für einen virtuellen Linux-Computer können Sie den öffentlichen SSH-Schlüssel (Secure Shell) eingeben, den Sie zum Anmelden am Computer verwenden.
   * **Abonnement**: Wählen Sie das Abonnement aus, das Sie verwenden möchten, um den neuen virtuellen Computer bereitzustellen.
   * **Ressourcengruppe**: Name der Ressourcengruppe für die VM. Sie können entweder den Namen einer neuen oder einer bereits vorhanden Ressourcengruppe eingeben.
   * **Standort**: Gibt an, wo der neue virtuelle Computer bereitgestellt wird. Wenn Sie den virtuellen Computer mit dem lokalen Netzwerk verbinden möchten, sollten Sie darauf achten, den Speicherort des virtuellen Netzwerks auszuwählen, das Azure mit dem lokalen Netzwerk verbindet. Weitere Informationen finden Sie im Abschnitt [Microsoft Azure-Netzwerk][planning-guide-microsoft-azure-networking] unter [Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver][planning-guide].
1. **Size**:

     Eine Liste mit den unterstützten VM-Typen finden Sie im SAP-Hinweis [1928533]. Achten Sie darauf, dass Sie den richtigen VM-Typ wählen, wenn Sie Azure Storage Premium nutzen möchten. Nicht alle Typen von virtuellen Computern unterstützten Storage Premium. Weitere Informationen finden Sie unter [Speicher: Microsoft Azure Storage und Datenträger][planning-guide-storage-microsoft-azure-storage-and-data-disks] und [Azure-Speichertypen für SAP-Workloads](./planning-guide-storage.md) unter [Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver][planning-guide].

1. **Einstellungen**:
   * **Storage**
     * **Datenträgertyp**: Wählen Sie den Datenträgertyp des Betriebssystem-Datenträgers aus. Wenn Sie Storage Premium für Ihre Datenträger für Daten verwenden möchten, empfiehlt es sich, auch für den Betriebssystem-Datenträger Storage Premium zu nutzen.
     * **Verwaltete Datenträger verwenden**: Wenn Sie verwaltete Datenträger verwenden möchten, klicken Sie auf „Ja“. Weitere Informationen zu Managed Disks finden Sie im Kapitel [Managed Disks](./planning-guide-storage.md#microsoft-azure-storage-resiliency) der Planhinweisliste.
     * **Speicherkonto**: Wählen Sie ein vorhandenes Speicherkonto aus, oder erstellen Sie ein neues. Nicht alle Speichertypen funktionieren in Bezug auf die Ausführung von SAP-Anwendungen. Weitere Informationen zu Speichertypen finden Sie unter [Speicherstruktur eines virtuellen Computers für RDBMS-Bereitstellungen](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64).
   * **Network**
     * **Virtuelles Netzwerk** und **Subnetz**: Wählen Sie das virtuelle Netzwerk aus, das mit dem lokalen Netzwerk verbunden ist, um den virtuellen Computer in das Intranet zu integrieren.
     * **Öffentliche IP-Adresse:** Wählen Sie die öffentliche IP-Adresse aus, die Sie verwenden möchten, oder geben Sie Parameter ein, um eine neue öffentliche IP-Adresse zu erstellen. Mithilfe der öffentlichen IP-Adresse können Sie über das Internet auf den virtuellen Computer zugreifen. Achten Sie auch darauf, eine Netzwerksicherheitsgruppe zu erstellen, um den Zugriff auf den virtuellen Computer zu schützen.
     * **Netzwerksicherheitsgruppe**: Weitere Informationen finden Sie unter [Steuern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen][virtual-networks-nsg].
   * **Erweiterungen**: Sie können Erweiterungen für virtuelle Computer installieren, indem Sie sie zur Bereitstellung hinzufügen. In diesem Schritt müssen Sie keine Erweiterungen hinzufügen. Die für die SAP-Unterstützung benötigten Erweiterungen werden später installiert. Weitere Informationen finden Sie im Kapitel [Konfigurieren der Azure-Erweiterung für SAP][deployment-guide-4.5] in diesem Handbuch.
   * **Hochverfügbarkeit**: Wählen Sie eine vorhandene Verfügbarkeitsgruppe aus, oder geben Sie die Parameter zum Erstellen einer neuen ein. Weitere Informationen finden Sie unter [Azure-Verfügbarkeitsgruppen][planning-guide-3.2.3].
   * **Überwachung**
     * **Startdiagnose**: Sie können für die Startdiagnose die Option **Deaktivieren** auswählen.
     * **Diagnose des Gastbetriebssystems**: Sie können für die Überwachungsdiagnose die Option **Deaktivieren** wählen.

1. **Zusammenfassung**:

   Überprüfen Sie Ihre Auswahl, und wählen Sie dann **OK**.

Ihr virtueller Computer wird in der ausgewählten Ressourcengruppe bereitgestellt.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Erstellen eines virtuellen Computers mit einer Vorlage

Sie können einen virtuellen Computer auch mit einer im [GitHub-Repository azure-quickstart-templates][azure-quickstart-templates-github] veröffentlichten SAP-Vorlage erstellen. Außerdem können Sie einen virtuellen Computer über das [Azure-Portal][virtual-machines-windows-tutorial], [PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms] oder die [Azure-Befehlszeilenschnittstelle][virtual-machines-linux-tutorial] erstellen.

* [**Vorlage für die Konfiguration mit zwei Ebenen (nur ein virtueller Computer)** (sap-2-tier-marketplace-image)][sap-templates-2-tier-marketplace-image]

  Verwenden Sie diese Vorlage, um ein System mit zwei Ebenen mit nur einem virtuellen Computer zu erstellen.
* [**Vorlage für die Konfiguration mit zwei Ebenen (nur ein virtueller Computer) – Managed Disks** (sap-2-tier-marketplace-image-md)][sap-templates-2-tier-marketplace-image-md]

  Verwenden Sie diese Vorlage, um ein System mit zwei Ebenen mit nur einem virtuellen Computer und verwalteten Datenträgern zu erstellen.
* [**Vorlage für die Konfiguration mit drei Ebenen (mehrere virtuelle Computer)** (sap-3-tier-marketplace-image)][sap-templates-3-tier-marketplace-image]

  Verwenden Sie diese Vorlage, um ein System mit drei Ebenen mit mehreren virtuellen Computern zu erstellen.
* [**Vorlage für die Konfiguration mit drei Ebenen (mehrere virtuelle Computer) – Managed Disks** (sap-3-tier-marketplace-image-md)][sap-templates-3-tier-marketplace-image-md]

  Verwenden Sie diese Vorlage, um ein System mit drei Ebenen mit mehreren virtuellen Computern und verwalteten Datenträgern zu erstellen.

Geben Sie im Azure-Portal die folgenden Parameter für die Vorlage ein:

1. **Grundlagen**:
   * **Abonnement**: Das Abonnement, das zum Bereitstellen der Vorlage verwendet wird.
   * **Ressourcengruppe**: Die Ressourcengruppe, die zum Bereitstellen der Vorlage verwendet wird. Sie können eine neue Ressourcengruppe erstellen oder eine vorhandene Ressourcengruppe des Abonnements auswählen.
   * **Standort**: Hier stellen Sie die Vorlage bereit. Wenn Sie eine vorhandene Ressourcengruppe ausgewählt haben, wird der Standort dieser Ressourcengruppe verwendet.

1. **Einstellungen**:
   * **SAP-System-ID**: Die SAP-System-ID (SID).
   * **Betriebssystemtyp**: Das Betriebssystem, das Sie bereitstellen möchten, z.B. Windows Server 2012 R2, SUSE Linux Enterprise Server 12 (SLES 12) oder Red Hat Enterprise Linux 7.2 (RHEL 7.2) oder Oracle Linux 7.2.

     In der Listenansicht werden nicht alle unterstützten Betriebssysteme angezeigt. Weitere Informationen zu unterstützten Betriebssystemen für die Bereitstellung von SAP-Software finden Sie im SAP-Hinweis [1928533].
   * **SAP-Systemgröße**: Die Größe des SAP-Systems.

     Die SAPS-Zahl des neuen Systems. Wenn Sie nicht sicher sind, welche SAPS-Zahl für das System benötigt wird, können Sie sich an den SAP-Technologiepartner oder -Systemintegrator wenden.
   * **Systemverfügbarkeit** (nur Vorlage für drei Ebenen): Gibt die Systemverfügbarkeit an.

     Wählen Sie **HA** (hohe Verfügbarkeit) aus, um eine Konfiguration für eine Installation mit hoher Verfügbarkeit zu erhalten. Es werden zwei Datenbankserver und zwei Server für ABAP SAP Central Services (ASCS) erstellt.
   * **Speichertyp** (nur Vorlage für zwei Ebenen): Der zu verwendende Speicherkontotyp.

     Für größere Systeme empfehlen wir dringend die Verwendung von Azure Storage Premium. Weitere Informationen zu Speichertypen finden Sie unter den folgenden Ressourcen:
      * [Verwenden von Azure Storage SSD Premium für SAP-DBMS-Instanzen][2367194]
      * [Speicherstruktur einer VM für RDBMS-Bereitstellungen](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Storage Premium: Hochleistungsspeicher für Azure Virtual Machines-Workloads][storage-premium-storage-preview-portal]
      * [Einführung in Microsoft Azure Storage][storage-introduction]
   * **Administratorbenutzername** und **Administratorkennwort**: Ein Benutzername und Kennwort.
     Für die Anmeldung am virtuellen Computer wird ein neuer Benutzer erstellt.
   * **Neues oder vorhandenes Subnetz**: Legt fest, ob ein neues virtuelles Netzwerk und Subnetz erstellt oder ein bestehendes Subnetz verwendet werden soll. Wenn Sie bereits über ein virtuelles Netzwerk verfügen, das mit dem lokalen Netzwerk verbunden ist, wählen Sie hier **Vorhanden** aus.
   * **Subnetz-ID**: Wenn Sie die VM in einem vorhandenen VNET bereitstellen möchten, in dem Sie ein Subnetz definiert haben, dem die VM zugewiesen werden soll, geben Sie die ID dieses spezifischen Subnetzes an. Die ID hat normalerweise das folgende Format: /subscriptions/&lt;Abonnement-ID>/resourceGroups/&lt;Name der Ressourcengruppe>/providers/Microsoft.Network/virtualNetworks/&lt;Name des virtuellen Netzwerks>/subnets/&lt;Name des Subnetzes>

1. **Geschäftsbedingungen**:  
    Lesen Sie sich die Bedingungen durch, und bestätigen Sie diese.

1. Wählen Sie die Option **Kaufen**.

Der Azure-VM-Agent wird bei Verwendung eines Images vom Azure Marketplace standardmäßig bereitgestellt.

#### <a name="configure-proxy-settings"></a>Konfigurieren von Proxyeinstellungen

Je nach Konfiguration Ihres lokalen Netzwerks müssen Sie unter Umständen den Proxy für Ihre VM einrichten. Wenn Ihre VM über VPN oder ExpressRoute mit Ihrem lokalen Netzwerk verbunden ist, kann die VM unter Umständen nicht auf das Internet zugreifen und kann weder die erforderlichen Erweiterungen herunterladen noch die SAP-Erweiterung für Azure verwenden, um Azure-Infrastrukturinformationen für den SAP-Host-Agent zu erfassen. Weitere Informationen finden Sie unter [Konfigurieren von Proxys][deployment-guide-configure-proxy].

#### <a name="join-a-domain-windows-only"></a>Beitreten zu einer Domäne (nur Windows)

Wenn für Ihre Azure-Bereitstellung eine Verbindung mit einer lokalen Active Directory- oder DNS-Instanz per Azure-Site-to-Site-VPN- oder ExpressRoute-Verbindung besteht (unter [Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver][planning-guide] als *standortübergreifend* bezeichnet), wird erwartet, dass die VM einer lokalen Domäne beitritt. Weitere Informationen zu den Aspekten dieser Aufgabe finden Sie unter [Einbinden virtueller Computer in die lokale Domäne (nur Windows)][deployment-guide-4.3].

#### <a name="configure-vm-extension"></a><a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Konfigurieren der VM-Erweiterung

Um sicherzustellen, dass SAP Ihre Umgebung unterstützt, richten Sie die Azure-Erweiterung für SAP so ein, wie dies unter [Konfigurieren der Azure-Erweiterung für SAP][deployment-guide-4.5] beschrieben ist. Überprüfen Sie die Voraussetzungen für die SAP und die erforderlichen Mindestversionen des SAP-Kernels und des SAP-Host-Agents in den Ressourcen, die unter [SAP-Ressourcen][deployment-guide-2.2] aufgeführt sind.

#### <a name="vm-extension-for-sap-check"></a>VM-Erweiterung für SAP-Überprüfung

Überprüfen Sie, ob die VM-Erweiterung für SAP wie in [Überprüfungen und Problembehandlung für die End-to-End-Datensammlung für den SAP-Host-Agent][deployment-guide-troubleshooting-chapter] beschrieben funktioniert.

#### <a name="post-deployment-steps"></a>Schritte nach der Bereitstellung

Nach der Erstellung und Bereitstellung der VM müssen Sie die erforderlichen Softwarekomponenten auf der VM installieren. Aufgrund der Sequenz der Bereitstellung bzw. Softwareinstallation bei dieser Art von VM-Bereitstellung muss die zu installierende Software bereits verfügbar sein, und zwar entweder in Azure, auf einer anderen VM oder auf einem angefügten Datenträger. Sie können auch ein standortübergreifendes Szenario verwenden, bei dem die Konnektivität mit den lokalen Assets (Installationsfreigaben) gegeben ist.

Verwenden Sie nach dem Bereitstellen Ihrer VM in Azure dieselben Richtlinien und Tools zum Installieren der SAP-Software auf der VM wie in einer lokalen Umgebung. Für die Installation von SAP-Software auf einer Azure-VM wird sowohl von SAP als auch von Microsoft empfohlen, dass Sie die SAP-Installationsmedien auf Azure-VHDs oder verwaltete Datenträger hochladen und speichern oder dass Sie eine Azure-VM erstellen, die als Dateiserver mit allen erforderlichen SAP-Installationsmedien dient.

### <a name="scenario-2-deploying-a-vm-with-a-custom-image-for-sap"></a><a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Szenario 2: Bereitstellen eines virtuellen Computers mit einem benutzerdefinierten Image für SAP

Da für unterschiedliche Versionen eines Betriebssystems oder eines DBMS auch unterschiedliche Patchanforderungen gelten, erfüllen die Images auf dem Azure Marketplace unter Umständen nicht Ihre Anforderungen. Stattdessen kann es ratsam sein, eine VM mit einem eigenen OS/DBMS-VM-Image zu erstellen, das Sie später erneut bereitstellen können.
Bei der Erstellung eines privaten Images für Linux müssen andere Schritte ausgeführt werden als bei der Erstellung eines privaten Images für Windows.

---
> ![Windows-Logo][Logo_Windows] Windows
>
> Um ein Windows-Image vorzubereiten, das Sie zum Bereitstellen mehrerer virtueller Computer verwenden können, müssen die Windows-Einstellungen (z.B. Windows-SID und Hostname) auf dem lokalen virtuellen Computer abstrahiert bzw. generalisiert werden. Hierfür können Sie [sysprep](/previous-versions/windows/it-pro/windows-8.1-and-8/hh825084(v=win.10)) verwenden.
>
> ![Linux-Logo][Logo_Linux] Linux
>
> Um ein Linux-Image vorzubereiten, das Sie zum Bereitstellen mehrerer virtueller Computer verwenden können, müssen einige Linux-Einstellungen auf dem lokalen virtuellen Computer abstrahiert bzw. generalisiert werden. Hierfür können Sie `waagent -deprovision` verwenden. Weitere Informationen finden Sie unter [Erfassen eines virtuellen Linux-Computers, der in Azure ausgeführt wird][virtual-machines-linux-capture-image] und im [Benutzerleitfaden zum Azure Linux-Agent][virtual-machines-linux-agent-user-guide-command-line-options].
>
>

---
Sie können ein benutzerdefiniertes Image vorbereiten und erstellen und dann zum Erstellen mehrerer neuer VMs verwenden. Dies wird unter [Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver][planning-guide] beschrieben. Richten Sie den Datenbankinhalt ein, indem Sie entweder den SAP Software Provisioning Manager verwenden, um ein neues SAP-System zu installieren (eine Datenbanksicherung wird von einem Datenträger wiederhergestellt, der an den virtuellen Computer angeschlossen ist), oder indem Sie eine Datenbanksicherung direkt aus Azure Storage wiederherstellen, falls Ihr DBMS dies unterstützt. Weitere Informationen finden Sie unter [Azure Virtual Machines – DBMS-Bereitstellung für SAP NetWeaver][dbms-guide]. Wenn Sie bereits ein SAP-System auf dem lokalen virtuellen Computer installiert haben (insbesondere bei Systemen mit zwei Ebenen), können Sie die SAP-Systemeinstellungen nach der Bereitstellung des virtuellen Azure-Computers über das vom SAP Software Provisioning Manager unterstützte Verfahren zum Umbenennen von Systemen anpassen (SAP-Hinweis [1619720]). Andernfalls können Sie die SAP-Software nach dem Bereitstellen der Azure-VM installieren.

Im folgenden Flussdiagramm ist die SAP-spezifische Schrittfolge zum Bereitstellen einer VM über ein benutzerdefiniertes Image dargestellt:

![Flussdiagramm der VM-Bereitstellung für SAP-Systeme mithilfe eines VM-Images aus dem privaten Marketplace][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Erstellen eines virtuellen Computers mit dem Azure-Portal

Die einfachste Möglichkeit zum Erstellen eines neuen virtuellen Computers mit einem Image auf einem verwalteten Datenträger ist die Verwendung des Azure-Portals. Weitere Informationen zum Erstellen eines Images auf einem verwalteten Datenträger finden Sie unter [Erfassen eines verwalteten Images eines generalisierten virtuellen Computers in Azure](../../windows/capture-image-resource.md).

1.  Gehe zu <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages>. Oder wählen Sie im Azure-Portal die Option **Images**.
1.  Wählen Sie das Image auf einem verwalteten Datenträger, das Sie bereitstellen möchten, und klicken Sie auf **VM erstellen**

Im Assistenten werden Sie durch die Schritte zum Festlegen der erforderlichen Parameter für die Erstellung des virtuellen Computers mit allen erforderlichen Ressourcen wie Netzwerkschnittstellen oder Speicherkonten geführt. Diese Parameter sind beispielsweise:

1. **Grundlagen**:
   * **Name**: Dies ist der Name der Ressource (Name des virtuellen Computers).
   * **VM-Datenträgertyp**: Wählen Sie den Datenträgertyp des Betriebssystem-Datenträgers aus. Wenn Sie Storage Premium für Ihre Datenträger für Daten verwenden möchten, empfiehlt es sich, auch für den Betriebssystem-Datenträger Storage Premium zu nutzen.
   * **Benutzername und Kennwort** oder **öffentlicher SSH-Schlüssel**: Geben Sie Benutzernamen und Kennwort des Benutzers ein, der während der Bereitstellung erstellt wird. Für einen virtuellen Linux-Computer können Sie den öffentlichen SSH-Schlüssel (Secure Shell) eingeben, den Sie zum Anmelden am Computer verwenden.
   * **Abonnement**: Wählen Sie das Abonnement aus, das Sie verwenden möchten, um den neuen virtuellen Computer bereitzustellen.
   * **Ressourcengruppe**: Name der Ressourcengruppe für die VM. Sie können entweder den Namen einer neuen oder einer bereits vorhanden Ressourcengruppe eingeben.
   * **Standort**: Gibt an, wo der neue virtuelle Computer bereitgestellt wird. Wenn Sie den virtuellen Computer mit dem lokalen Netzwerk verbinden möchten, sollten Sie darauf achten, den Speicherort des virtuellen Netzwerks auszuwählen, das Azure mit dem lokalen Netzwerk verbindet. Weitere Informationen finden Sie im Abschnitt [Microsoft Azure-Netzwerk][planning-guide-microsoft-azure-networking] unter [Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver][planning-guide].
1. **Size**:

     Eine Liste mit den unterstützten VM-Typen finden Sie im SAP-Hinweis [1928533]. Achten Sie darauf, dass Sie den richtigen VM-Typ wählen, wenn Sie Azure Storage Premium nutzen möchten. Nicht alle Typen von virtuellen Computern unterstützten Storage Premium. Weitere Informationen finden Sie unter [Speicher: Microsoft Azure Storage und Datenträger][planning-guide-storage-microsoft-azure-storage-and-data-disks] und [Azure-Speichertypen für SAP-Workloads](./planning-guide-storage.md) unter [Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver][planning-guide].

1. **Einstellungen**:
   * **Storage**
     * **Datenträgertyp**: Wählen Sie den Datenträgertyp des Betriebssystem-Datenträgers aus. Wenn Sie Storage Premium für Ihre Datenträger für Daten verwenden möchten, empfiehlt es sich, auch für den Betriebssystem-Datenträger Storage Premium zu nutzen.
     * **Verwaltete Datenträger verwenden**: Wenn Sie verwaltete Datenträger verwenden möchten, klicken Sie auf „Ja“. Weitere Informationen zu Managed Disks finden Sie im Kapitel [Managed Disks](./planning-guide-storage.md#microsoft-azure-storage-resiliency) der Planhinweisliste.
   * **Network**
     * **Virtuelles Netzwerk** und **Subnetz**: Wählen Sie das virtuelle Netzwerk aus, das mit dem lokalen Netzwerk verbunden ist, um den virtuellen Computer in das Intranet zu integrieren.
     * **Öffentliche IP-Adresse:** Wählen Sie die öffentliche IP-Adresse aus, die Sie verwenden möchten, oder geben Sie Parameter ein, um eine neue öffentliche IP-Adresse zu erstellen. Mithilfe der öffentlichen IP-Adresse können Sie über das Internet auf den virtuellen Computer zugreifen. Achten Sie auch darauf, eine Netzwerksicherheitsgruppe zu erstellen, um den Zugriff auf den virtuellen Computer zu schützen.
     * **Netzwerksicherheitsgruppe**: Weitere Informationen finden Sie unter [Steuern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen][virtual-networks-nsg].
   * **Erweiterungen**: Sie können Erweiterungen für virtuelle Computer installieren, indem Sie sie zur Bereitstellung hinzufügen. In diesem Schritt müssen Sie keine Erweiterung hinzufügen. Die für die SAP-Unterstützung benötigten Erweiterungen werden später installiert. Weitere Informationen finden Sie im Kapitel [Konfigurieren der Azure-Erweiterung für SAP][deployment-guide-4.5] in diesem Handbuch.
   * **Hochverfügbarkeit**: Wählen Sie eine vorhandene Verfügbarkeitsgruppe aus, oder geben Sie die Parameter zum Erstellen einer neuen ein. Weitere Informationen finden Sie unter [Azure-Verfügbarkeitsgruppen][planning-guide-3.2.3].
   * **Überwachung**
     * **Startdiagnose**: Sie können für die Startdiagnose die Option **Deaktivieren** auswählen.
     * **Diagnose des Gastbetriebssystems**: Sie können für die Überwachungsdiagnose die Option **Deaktivieren** wählen.

1. **Zusammenfassung**:

   Überprüfen Sie Ihre Auswahl, und wählen Sie dann **OK**.

Ihr virtueller Computer wird in der ausgewählten Ressourcengruppe bereitgestellt.

#### <a name="create-a-virtual-machine-by-using-a-template"></a>Erstellen eines virtuellen Computers mit einer Vorlage

Verwenden Sie zum Erstellen einer Bereitstellung über ein privates Betriebssystemimage im Azure-Portal eine der unten angegebenen SAP-Vorlagen. Diese Vorlagen werden im [GitHub-Repository azure-quickstart-templates][azure-quickstart-templates-github] veröffentlicht. Sie können auch manuell mit [PowerShell][virtual-machines-upload-image-windows-resource-manager] einen virtuellen Computer erstellen.

* [**Vorlage für die Konfiguration mit zwei Ebenen (nur ein virtueller Computer)** (sap-2-tier-user-image)][sap-templates-2-tier-user-image]

  Verwenden Sie diese Vorlage, um ein System mit zwei Ebenen mit nur einem virtuellen Computer zu erstellen.
* [**Vorlage für die Konfiguration mit zwei Ebenen (nur ein virtueller Computer) – Managed Disks-Image** (sap-2-tier-user-image-md)][sap-templates-2-tier-user-image-md]

  Verwenden Sie diese Vorlage, um ein System mit zwei Ebenen mit nur einem virtuellen Computer und einem Image auf einem verwalteten Datenträger zu erstellen.
* [**Vorlage für die Konfiguration mit drei Ebenen (mehrere virtuelle Computer)** (sap-3-tier-user-image)][sap-templates-3-tier-user-image]

  Verwenden Sie diese Vorlage, um ein System mit drei Ebenen mit mehreren virtuellen Computern oder einem eigenen Betriebssystemimage zu erstellen.
* [**Vorlage für die Konfiguration mit drei Ebenen (mehrere virtuelle Computer) – Managed Disks-Image** (sap-3-tier-user-image-md)][sap-templates-3-tier-user-image-md]

  Verwenden Sie diese Vorlage, um ein System mit drei Ebenen mit mehreren virtuellen Computern oder einem eigenen Betriebssystemimage und einem Image auf einem verwalteten Datenträger zu erstellen.

Geben Sie im Azure-Portal die folgenden Parameter für die Vorlage ein:

1. **Grundlagen**:
   * **Abonnement**: Das Abonnement, das zum Bereitstellen der Vorlage verwendet wird.
   * **Ressourcengruppe**: Die Ressourcengruppe, die zum Bereitstellen der Vorlage verwendet wird. Sie können eine neue Ressourcengruppe erstellen oder eine vorhandene Ressourcengruppe im Abonnement auswählen.
   * **Standort**: Hier stellen Sie die Vorlage bereit. Wenn Sie eine vorhandene Ressourcengruppe ausgewählt haben, wird der Standort dieser Ressourcengruppe verwendet.
1. **Einstellungen**:
   * **SAP-System-ID**: Die SAP-System-ID.
   * **Betriebssystemtyp**: Der Betriebssystemtyp für die Bereitstellung (Windows oder Linux).
   * **SAP-Systemgröße**: Die Größe des SAP-Systems.

     Die SAPS-Zahl des neuen Systems. Wenn Sie nicht sicher sind, welche SAPS-Zahl für das System benötigt wird, können Sie sich an den SAP-Technologiepartner oder -Systemintegrator wenden.
   * **Systemverfügbarkeit** (nur Vorlage für drei Ebenen): Gibt die Systemverfügbarkeit an.

     Wählen Sie **HA** (hohe Verfügbarkeit) aus, um eine Konfiguration für eine Installation mit hoher Verfügbarkeit zu erhalten. Es werden zwei Datenbankserver und zwei Server für ASCS erstellt.
   * **Speichertyp** (nur Vorlage für zwei Ebenen): Der zu verwendende Speicherkontotyp.

     Für größere Systeme empfehlen wir dringend die Verwendung von Azure Storage Premium. Weitere Informationen zu Speichertypen finden Sie in den folgenden Ressourcen:
      * [Verwenden von Azure Storage SSD Premium für SAP-DBMS-Instanzen][2367194]
      * [Speicherstruktur einer VM für RDBMS-Bereitstellungen](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Storage Premium: Hochleistungsspeicher für Azure Virtual Machine-Workloads][storage-premium-storage-preview-portal]
      * [Einführung in Microsoft Azure Storage][storage-introduction]
   * **VHD-URI für das Benutzerimage** (Nur Vorlage für Images für nicht verwaltete Datenträger): Der URI der VHD mit dem privaten Betriebssystemimage, z.B. „https://&lt;Kontoname>.blob.core.windows.net/vhds/userimage.vhd“.
   * **Speicherkonto für das Benutzerimage** (Nur Vorlage für Images für nicht verwaltete Datenträger): Der Name des Speicherkontos, unter dem das private Betriebssystemimage gespeichert ist, z.B. &lt;Kontoname&gt; in „https://&lt;Kontoname>.blob.core.windows.net/vhds/userimage.vhd“.
   * **userImageId** (Nur Vorlage für Images für nicht verwaltete Datenträger): ID des Images des verwalteten Datenträgers, das Sie verwenden möchten.
   * **Administratorbenutzername** und **Administratorkennwort**: Der Benutzername und das Kennwort.

     Für die Anmeldung am virtuellen Computer wird ein neuer Benutzer erstellt.
   * **Neues oder vorhandenes Subnetz**: Legt fest, ob ein neues virtuelles Netzwerk und Subnetz erstellt oder ein bestehendes Subnetz verwendet werden soll. Wenn Sie bereits über ein virtuelles Netzwerk verfügen, das mit dem lokalen Netzwerk verbunden ist, wählen Sie hier **Vorhanden** aus.
   * **Subnetz-ID**: Wenn Sie die VM in einem vorhandenen VNET bereitstellen möchten, in dem Sie ein Subnetz definiert haben, dem die VM zugewiesen werden soll, geben Sie die ID dieses spezifischen Subnetzes an. Die ID hat normalerweise das folgende Format: /subscriptions/&lt;Abonnement-ID>/resourceGroups/&lt;Name der Ressourcengruppe>/providers/Microsoft.Network/virtualNetworks/&lt;Name des virtuellen Netzwerks>/subnets/&lt;Name des Subnetzes>

1. **Geschäftsbedingungen**:  
    Lesen Sie sich die Bedingungen durch, und bestätigen Sie diese.

1. Wählen Sie die Option **Kaufen**.

#### <a name="install-the-vm-agent-linux-only"></a>Installieren des VM-Agent (nur Linux)

Zum Verwenden der im vorherigen Abschnitt beschriebenen Vorlagen muss der Linux-Agent bereits im Benutzerimage installiert sein, da die Bereitstellung ansonsten nicht erfolgreich ist. Laden Sie den VM-Agent aus dem Benutzerimage herunter, und installieren Sie ihn. Eine Beschreibung finden Sie unter [Herunterladen, Installieren und Aktivieren des Azure-VM-Agent][deployment-guide-4.4]. Wenn Sie die Vorlagen nicht verwenden, können Sie den VM-Agent auch später installieren.

#### <a name="join-a-domain-windows-only"></a>Beitreten zu einer Domäne (nur Windows)

Wenn für Ihre Azure-Bereitstellung eine Verbindung mit einer lokalen Active Directory- oder DNS-Instanz per Azure-Site-to-Site-VPN- oder Azure ExpressRoute-Verbindung besteht (unter [Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver][planning-guide] als *standortübergreifend* bezeichnet), wird erwartet, dass die VM einer lokalen Domäne beitritt. Weitere Informationen zu den Aspekten dieses Schritts finden Sie unter [Einbinden virtueller Computer in die lokale Domäne (nur Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Konfigurieren von Proxyeinstellungen

Je nach Konfiguration Ihres lokalen Netzwerks müssen Sie unter Umständen den Proxy für Ihre VM einrichten. Wenn Ihre VM über VPN oder ExpressRoute mit Ihrem lokalen Netzwerk verbunden ist, kann die VM unter Umständen nicht auf das Internet zugreifen und kann weder die erforderlichen Erweiterungen herunterladen noch die SAP-Erweiterung für Azure verwenden, um Azure-Infrastrukturinformationen für den SAP-Host-Agent zu erfassen. Weitere Informationen hierzu finden Sie unter [Konfigurieren des Proxys][deployment-guide-configure-proxy].

#### <a name="configure-azure-vm-extension-for-sap"></a>Konfigurieren der Azure-VM-Erweiterung für SAP

Um sicherzustellen, dass SAP Ihre Umgebung unterstützt, richten Sie die Azure-Erweiterung für SAP so ein, wie dies unter [Konfigurieren der Azure-Erweiterung für SAP][deployment-guide-4.5] beschrieben ist. Überprüfen Sie die Voraussetzungen für die SAP und die erforderlichen Mindestversionen des SAP-Kernels und des SAP-Host-Agents in den Ressourcen, die unter [SAP-Ressourcen][deployment-guide-2.2] aufgeführt sind.

#### <a name="sap-vm-extension-check"></a>Überprüfung der SAP-VM-Erweiterung

Überprüfen Sie, ob die VM-Erweiterung für SAP wie in [Überprüfungen und Problembehandlung für die End-to-End-Datensammlung für den SAP-Host-Agent][deployment-guide-troubleshooting-chapter] beschrieben funktioniert.


### <a name="scenario-3-moving-an-on-premises-vm-by-using-a-non-generalized-azure-vhd-with-sap"></a><a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Szenario 3: Verschieben einer lokalen VM mit einer nicht generalisierten virtuellen Azure-Festplatte mit SAP

In diesem Szenario planen Sie die Verschiebung eines bestimmten SAP-Systems aus einer lokalen Umgebung in Azure. Hierfür können Sie die VHD mit dem Betriebssystem und den SAP- und DBMS-Binärdateien sowie die VHDs mit den Daten- und Protokolldateien des DBMS in Azure hochladen. Im Gegensatz zum Szenario unter [Szenario 2: Bereitstellen eines virtuellen Computers mit einem benutzerdefinierten Image für SAP][deployment-guide-3.3] behalten Sie hier den Hostnamen, die SAP-SID und die SAP-Benutzerkonten auf der Azure-VM bei, weil sie in der lokalen Umgebung konfiguriert wurden. Es ist nicht erforderlich, das Betriebssystem zu generalisieren. Dieser Fall gilt am häufigsten für standortübergreifende Szenarien, bei denen ein Teil der SAP-Landschaft lokal und ein anderer Teil in Azure ausgeführt wird.

In diesem Szenario wird der VM-Agent während der Bereitstellung **nicht** automatisch installiert. Da der VM-Agent und die Azure-Erweiterung für SAP Voraussetzungen für die Ausführung von SAP NetWeaver in Azure sind, müssen Sie beide Komponenten nach dem Erstellen des virtuellen Computers manuell herunterladen, installieren und aktivieren.

Weitere Informationen zum Azure-VM-Agent finden Sie in den folgenden Ressourcen.

---
> ![Windows-Logo][Logo_Windows] Windows
>
> [Übersicht über den Agent für virtuelle Azure-Computer][virtual-machines-windows-agent-user-guide]
>
> ![Linux-Logo][Logo_Linux] Linux
>
> [Benutzerhandbuch für Azure Linux-Agent][virtual-machines-linux-agent-user-guide]
>
>

---

Im folgenden Flussdiagramm ist die Folge der Schritte zum Verschieben einer lokalen VM mit einer nicht generalisierten Azure-VHD dargestellt:

![Flussdiagramm der VM-Bereitstellung für SAP-Systeme mithilfe eines VM-Datenträgers][deployment-guide-figure-400]

Wenn der Datenträger bereits hochgeladen und in Azure definiert wurde (siehe [Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver][planning-guide]), können Sie die Aufgaben in den nächsten Abschnitten durchführen.

#### <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Verwenden Sie zum Erstellen einer Bereitstellung über einen privaten Betriebssystemdatenträger im Azure-Portal die im GitHub-Repository [azure-quickstart-templates][azure-quickstart-templates-github] veröffentlichte SAP-Vorlage. Sie können auch manuell einen virtuellen Computer erstellen, indem Sie PowerShell verwenden.

* [**Vorlage für die Konfiguration mit zwei Ebenen (nur ein virtueller Computer)** (sap-2-tier-user-disk)][sap-templates-2-tier-os-disk]

  Verwenden Sie diese Vorlage, um ein System mit zwei Ebenen mit nur einem virtuellen Computer zu erstellen.
* [**Vorlage für die Konfiguration mit zwei Ebenen (nur ein virtueller Computer) – Managed Disks** (sap-2-tier-user-disk-md)][sap-templates-2-tier-os-disk-md]

  Verwenden Sie diese Vorlage, um ein System mit zwei Ebenen mit nur einem virtuellen Computer und einem verwalteten Datenträger zu erstellen.

Geben Sie im Azure-Portal die folgenden Parameter für die Vorlage ein:

1. **Grundlagen**:
   * **Abonnement**: Das Abonnement, das zum Bereitstellen der Vorlage verwendet wird.
   * **Ressourcengruppe**: Die Ressourcengruppe, die zum Bereitstellen der Vorlage verwendet wird. Sie können eine neue Ressourcengruppe erstellen oder eine vorhandene Ressourcengruppe im Abonnement auswählen.
   * **Standort**: Hier stellen Sie die Vorlage bereit. Wenn Sie eine vorhandene Ressourcengruppe ausgewählt haben, wird der Standort dieser Ressourcengruppe verwendet.
1. **Einstellungen**:
   * **SAP-System-ID**: Die SAP-System-ID.
   * **Betriebssystemtyp**: Der Betriebssystemtyp für die Bereitstellung (Windows oder Linux).
   * **SAP-Systemgröße**: Die Größe des SAP-Systems.

     Die SAPS-Zahl des neuen Systems. Wenn Sie nicht sicher sind, welche SAPS-Zahl für das System benötigt wird, können Sie sich an den SAP-Technologiepartner oder -Systemintegrator wenden.
   * **Speichertyp** (nur Vorlage für zwei Ebenen): Der zu verwendende Speicherkontotyp.

     Für größere Systeme empfehlen wir dringend die Verwendung von Azure Storage Premium. Weitere Informationen zu Speichertypen finden Sie in den folgenden Ressourcen:
      * [Verwenden von Azure Storage SSD Premium für SAP-DBMS-Instanzen][2367194]
      * [Speicherstruktur einer VM für RDBMS-Bereitstellungen](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Storage Premium: Hochleistungsspeicher für Azure Virtual Machines-Workloads][storage-premium-storage-preview-portal]
      * [Einführung in Microsoft Azure Storage][storage-introduction]
   * **VHD-URI des Betriebssystemdatenträgers** (nur Vorlage für nicht verwaltete Datenträger): Der URI des Datenträgers mit dem privaten Betriebssystem, z.B. „https://&lt;Kontoname>.blob.core.windows.net/vhds/osdisk.vhd“.
   * **ID des verwalteten Betriebssystemdatenträgers** (nur Vorlage für verwaltete Datenträger): Die ID des verwalteten Datenträgers mit dem Betriebssystem, „/subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/group/providers/Microsoft.Compute/disks/WIN“
   * **Neues oder vorhandenes Subnetz**: Legt fest, ob ein neues virtuelles Netzwerk und Subnetz erstellt oder ein bestehendes Subnetz verwendet werden soll. Wenn Sie bereits über ein virtuelles Netzwerk verfügen, das mit dem lokalen Netzwerk verbunden ist, wählen Sie hier **Vorhanden** aus.
   * **Subnetz-ID**: Wenn Sie die VM in einem vorhandenen VNET bereitstellen möchten, in dem Sie ein Subnetz definiert haben, dem die VM zugewiesen werden soll, geben Sie die ID dieses spezifischen Subnetzes an. Die ID hat normalerweise das folgende Format: /subscriptions/&lt;Abonnement-ID>/resourceGroups/&lt;Name der Ressourcengruppe>/providers/Microsoft.Network/virtualNetworks/&lt;Name des virtuellen Netzwerks>/subnets/&lt;Name des Subnetzes>

1. **Geschäftsbedingungen**:  
    Lesen Sie sich die Bedingungen durch, und bestätigen Sie diese.

1. Wählen Sie die Option **Kaufen**.

#### <a name="install-the-vm-agent"></a>Installieren des VM-Agents

Zum Verwenden der Vorlagen, die im vorherigen Abschnitt beschrieben sind, muss der VM-Agent auf dem Betriebssystemdatenträger installiert sein, da die Bereitstellung ansonsten nicht erfolgreich ist. Laden Sie den VM-Agent herunter, und installieren Sie ihn auf der VM. Eine Beschreibung finden Sie unter [Herunterladen, Installieren und Aktivieren des Azure-VM-Agent][deployment-guide-4.4].

Falls Sie die im vorherigen Abschnitt beschriebenen Vorlagen nicht verwenden, können Sie den VM-Agent auch später installieren.

#### <a name="join-a-domain-windows-only"></a>Beitreten zu einer Domäne (nur Windows)

Wenn für Ihre Azure-Bereitstellung eine Verbindung mit einer lokalen Active Directory- oder DNS-Instanz per Azure-Site-to-Site-VPN- oder ExpressRoute-Verbindung besteht (unter [Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver][planning-guide] als *standortübergreifend* bezeichnet), wird erwartet, dass die VM einer lokalen Domäne beitritt. Weitere Informationen zu den Aspekten dieser Aufgabe finden Sie unter [Einbinden virtueller Computer in die lokale Domäne (nur Windows)][deployment-guide-4.3].

#### <a name="configure-proxy-settings"></a>Konfigurieren von Proxyeinstellungen

Je nach Konfiguration Ihres lokalen Netzwerks müssen Sie unter Umständen den Proxy für Ihre VM einrichten. Wenn Ihre VM über VPN oder ExpressRoute mit Ihrem lokalen Netzwerk verbunden ist, kann die VM unter Umständen nicht auf das Internet zugreifen und kann weder die erforderlichen Erweiterungen herunterladen noch die SAP-Erweiterung für Azure verwenden, um Azure-Infrastrukturinformationen für den SAP-Host-Agent zu erfassen. Weitere Informationen hierzu finden Sie unter [Konfigurieren des Proxys][deployment-guide-configure-proxy].

#### <a name="configure-azure-vm-extension-for-sap"></a>Konfigurieren der Azure-VM-Erweiterung für SAP

Um sicherzustellen, dass SAP Ihre Umgebung unterstützt, richten Sie die Azure-Erweiterung für SAP so ein, wie dies unter [Konfigurieren der Azure-Erweiterung für SAP][deployment-guide-4.5] beschrieben ist. Überprüfen Sie die Voraussetzungen für die SAP und die erforderlichen Mindestversionen des SAP-Kernels und des SAP-Host-Agents in den Ressourcen, die unter [SAP-Ressourcen][deployment-guide-2.2] aufgeführt sind.

#### <a name="sap-vm-check"></a>SAP-VM-Überprüfung

Überprüfen Sie, ob die VM-Erweiterung für SAP wie in [Überprüfungen und Problembehandlung für die End-to-End-Datensammlung für den SAP-Host-Agent][deployment-guide-troubleshooting-chapter] beschrieben funktioniert.

## <a name="update-the-configuration-of-azure-extension-for-sap"></a>Aktualisieren der Konfiguration der Azure-Erweiterung für SAP

Aktualisieren Sie die Konfiguration der Azure-Erweiterung für SAP, wenn eines der folgenden Szenarien zutrifft:
* Das gemeinsame Team von Microsoft und SAP hat die Funktionalität der VM-Erweiterung ausgeweitet, wodurch mehr oder weniger Leistungsindikatoren erforderlich sind.
* Microsoft führt eine neue Version der zugrunde liegenden Azure-Infrastruktur ein, die die Daten bereitstellt, und die Azure-Erweiterung für SAP muss an diese Änderungen angepasst werden.
* Sie binden zusätzliche Datenträger für Daten in den virtuellen Azure-Computer oder entfernen einen Datenträger für Daten. In diesem Fall muss die Sammlung mit den speicherbezogenen Daten aktualisiert werden. Wenn die Konfiguration durch Hinzufügen oder Löschen von Endpunkten oder Zuweisen von IP-Adressen zu einem virtuellen Computer geändert wird, wirkt sich dies nicht auf die Konfiguration der Erweiterung aus.
* Beispielsweise ändern Sie die Größe Ihrer Azure-VM von A5 in eine andere VM-Größe.
* Dem virtuellen Azure-Computer werden neue Netzwerkschnittstellen hinzugefügt.

Um die Einstellungen zu aktualisieren, aktualisieren Sie die Konfiguration der Azure-Erweiterung für SAP, indem Sie die Schritte ausführen, die unter [Konfigurieren der Azure-Erweiterung für SAP][deployment-guide-4.5] aufgeführt sind.

## <a name="detailed-tasks-for-sap-software-deployment"></a>Ausführliche Aufgaben der Bereitstellung von SAP-Software

Dieser Abschnitt enthält ausführliche Schritte zum Ausführen von bestimmten Aufgaben des Konfigurations- und Bereitstellungsprozesses.

### <a name="deploy-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Bereitstellen von Azure PowerShell-Cmdlets

Führen Sie die Schritte aus, die im Artikel [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) beschrieben werden.

Prüfen Sie regelmäßig, ob Updates für die PowerShell-Cmdlets vorhanden sind. Normalerweise wird einmal im Monat eine Aktualisierung durchgeführt. Führen Sie die in [diesem](/powershell/azure/install-az-ps#update-the-azure-powershell-module) Artikel beschriebenen Schritte aus. Sofern in SAP-Hinweis [1928533] oder SAP-Hinweis [2015553] nicht anders angegeben, empfehlen wir Ihnen die Verwendung der jeweils aktuellen Version der Azure PowerShell-Cmdlets.

Führen Sie diesen PowerShell-Befehl aus, um die Version der Azure PowerShell-Cmdlets zu überprüfen, die auf Ihrem Computer installiert ist:

```powershell
(Get-Module Az.Compute).Version
```

### <a name="deploy-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Bereitstellen der Azure-Befehlszeilenschnittstelle

Führen Sie die Schritte aus, die im Artikel [Installieren der Azure CLI](/cli/azure/install-azure-cli) beschrieben werden.

Prüfen Sie regelmäßig, ob Updates für die Azure-Befehlszeilenschnittstelle vorhanden sind. Normalerweise wird einmal im Monat eine Aktualisierung durchgeführt.

Führen Sie diesen Befehl aus, um zu prüfen, welche Version der Azure-Befehlszeilenschnittstelle auf Ihrem Computer installiert ist:

```console
az --version
```

### <a name="join-a-vm-to-an-on-premises-domain-windows-only"></a><a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Einbinden virtueller Computer in die lokale Domäne (nur Windows)

Wenn Sie SAP-VMs in einem standortübergreifenden Szenario bereitstellen, bei dem lokale Active Directory- und DNS-Instanzen in Azure erweitert werden, sollten die VMs in eine lokale Domäne eingebunden werden. Die einzelnen Schritte zur Einbindung einer VM in eine lokale Domäne sowie die hierzu erforderliche zusätzliche Software sind kundenspezifisch. Normalerweise müssen Sie zum Einbinden einer VM in eine lokale Domäne zusätzliche Software installieren, z.B. Antischadsoftware und Sicherungs- oder Überwachungssoftware.

Falls die Internetproxyeinstellungen beim Beitritt einer VM zu einer Domäne in Ihrer Umgebung erzwungen sind, müssen Sie bei diesem Szenario sicherstellen, dass auch das lokale Windows-Systemkonto (S-1-5-18) auf der Gast-VM die gleichen Proxyeinstellungen aufweist. Die einfachste Möglichkeit besteht darin, den Proxy mit einer Gruppenrichtlinie für die Domäne zu erzwingen, die für die Systeme in der Domäne gilt.

### <a name="download-install-and-enable-the-azure-vm-agent"></a><a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Herunterladen, Installieren und Aktivieren des Azure-VM-Agent

Für virtuelle Computer, die über ein nicht generalisiertes Betriebssystemimage bereitgestellt werden (z.B. ein Image, das nicht vom Windows-Systemvorbereitungstool Sysprep stammt), müssen Sie den Azure-VM-Agent manuell herunterladen, installieren und aktivieren.

Dieser Schritt ist nicht erforderlich, wenn Sie eine VM über den Azure Marketplace bereitstellen. Images vom Azure Marketplace verfügen bereits über den Azure-VM-Agent.

#### <a name="windows"></a><a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

1. Laden Sie den Azure-VM-Agent herunter:
   1.  Laden Sie das [Installationspaket für den Azure-VM-Agent](https://go.microsoft.com/fwlink/?LinkId=394789) herunter.
   1.  Speichern Sie das MSI-Paket für den VM-Agent lokal auf einem PC oder Server.
1. Installieren Sie den Azure-VM-Agent:
   1.  Stellen Sie per Remotedesktopprotokoll (RDP) eine Verbindung mit der bereitgestellten Azure-VM her.
   1.  Öffnen Sie ein Windows-Explorer-Fenster auf dem virtuellen Computer, und wählen Sie das Zielverzeichnis für die MSI-Datei des VM-Agent aus.
   1.  Ziehen Sie die MSI-Datei für die Installation des Azure-VM-Agent vom lokalen Computer/Server in das Zielverzeichnis des VM-Agent auf dem virtuellen Computer.
   1.  Doppelklicken Sie auf dem virtuellen Computer auf die MSI-Datei.
1. Beachten Sie bei virtuellen Computern, die in eine lokale Domäne eingebunden sind, dass die Internetproxyeinstellungen auch für das lokale Windows-Systemkonto (S-1-5-18) des virtuellen Computers gelten. Dies wird unter [Konfigurieren von Proxys][deployment-guide-configure-proxy] beschrieben. Der VM-Agent wird in diesem Kontext ausgeführt und muss eine Verbindung mit Azure herstellen können.

Für die Aktualisierung des Azure-VM-Agent ist keine Benutzerinteraktion erforderlich. Der VM-Agent wird automatisch aktualisiert, und es ist kein Neustart der VM erforderlich.

#### <a name="linux"></a><a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux

Verwenden Sie die folgenden Befehle, um den VM-Agent für Linux zu installieren:

* **SUSE Linux Enterprise Server (SLES)**

  ```console
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) oder Oracle Linux**

  ```console
  sudo yum install WALinuxAgent
  ```

Wenn der Agent bereits installiert ist, müssen Sie zum Aktualisieren des Azure Linux-Agent die Schritte unter [Aktualisieren des Azure Linux-Agent auf einem virtuellen Computer auf die neueste Version von GitHub][virtual-machines-linux-update-agent] ausführen.

### <a name="configure-the-proxy"></a><a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Konfigurieren von Proxys

Die Schritte, die Sie zum Konfigurieren des Proxys in Windows ausführen, unterscheiden sich von der Konfiguration des Proxys unter Linux.

#### <a name="windows"></a>Windows

Die Proxyeinstellungen müssen richtig eingerichtet werden, damit mit dem lokalen Systemkonto auf das Internet zugegriffen werden kann. Wenn die Proxyeinstellungen nicht durch eine Gruppenrichtlinie festgelegt werden, können Sie die Einstellungen für das lokale Systemkonto konfigurieren.

1. Navigieren Sie zu **Start**, geben Sie **gpedit.msc** ein, und drücken Sie die **EINGABETASTE**.
1. Wählen Sie **Computerkonfiguration** > **Administrative Vorlagen** > **Windows-Komponenten** > **Internet Explorer**. Stellen Sie sicher, dass die Einstellung **Proxyeinstellungen pro Computer vornehmen (anstelle von pro Benutzer)** deaktiviert bzw. nicht konfiguriert ist.
1. Navigieren Sie in der **Systemsteuerung** zu **Netzwerk- und Freigabecenter** > **Internetoptionen**.
1. Wählen Sie auf der Registerkarte die Option **Verbindungen**, und klicken Sie dann auf die Schaltfläche **LAN-Einstellungen**.
1. Deaktivieren Sie das Kontrollkästchen **Automatische Suche der Einstellungen**.
1. Aktivieren Sie das Kontrollkästchen **Proxyserver für das LAN verwenden**, und geben Sie dann die Proxyadresse und den Port ein.
1. Wählen Sie die Schaltfläche **Advanced** (Erweitert).
1. Geben Sie im Feld **Ausnahmen** die IP-Adresse **168.63.129.16** ein. Klicken Sie auf **OK**.

#### <a name="linux"></a>Linux

Konfigurieren Sie zunächst den richtigen Proxy in der Konfigurationsdatei des Microsoft Azure Gast-Agents „\\etc\\waagent.conf“.

Legen Sie die folgenden Parameter fest:

1. **HTTP-Proxyhost**: Geben Sie beispielsweise **proxy.corp.local** an.

   ```console
   HttpProxy.Host=<proxy host>

   ```

1. **HTTP-Proxyport**: Geben Sie beispielsweise **80** an.

   ```console
   HttpProxy.Port=<port of the proxy host>

   ```

1. Starten Sie den Agent neu.

   ```console
   sudo service waagent restart
   ```

Die Proxyeinstellungen in „\\etc\\waagent.conf“ gelten auch für die erforderlichen VM-Erweiterungen. Falls Sie die Azure-Repositorys verwenden möchten, sollten Sie darauf achten, dass der Datenverkehr mit diesen Repositorys nicht über das lokale Intranet geführt wird. Falls benutzerdefinierte Routen zum Aktivieren erzwungener Tunnel erstellt wurden, muss eine Route hinzugefügt werden, die den Datenverkehr an die Repositorys direkt in das Internet umleitet, und nicht über die Site-to-Site-VPN-Verbindung.

* **SLES**

  Zudem müssen Routen für die IP-Adressen in „\\etc\\regionserverclnt.cfg“ hinzugefügt werden. Die folgende Abbildung zeigt ein Beispiel:

  ![Tunnelerzwingung][deployment-guide-figure-50]


* **RHEL**

  Zudem müssen Routen für die IP-Adressen der in „\\etc\\yum.repos.d\\rhui-load-balancers“ aufgelisteten Hosts hinzugefügt werden. Die obige Abbildung enthält ein Beispiel hierzu.

* **Oracle Linux**

  Azure bietet keine Repositorys für Oracle Linux. Sie müssen selbst Repositorys für Oracle Linux konfigurieren oder die öffentlichen Repositorys verwenden.

Weitere Informationen zu benutzerdefinierten Routen finden Sie unter [Benutzerdefinierte Routen und IP-Weiterleitung][virtual-networks-udr-overview].

### <a name="configure-the-azure-extension-for-sap"></a><a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Konfigurieren der Azure-Erweiterung für SAP

> [!NOTE]
> Allgemeine Supportanweisung: Öffnen Sie einen Incident immer mit SAP auf der Komponente BC-OP-NT-AZR für Windows oder BC-OP-LNX-AZR, wenn Sie Unterstützung für die Azure-Erweiterung für SAP benötigen.
> Im SAP-Supportsystem arbeiten dedizierte Microsoft-Supportmitarbeiter, um unseren gemeinsamen Kunden zu helfen.

Wenn Sie die VM wie unter [Bereitstellungsszenarien für virtuelle Computer für SAP in Azure][deployment-guide-3] beschrieben vorbereitet haben, ist der Azure-VM-Agent auf dem virtuellen Computer installiert. Der nächste Schritt ist das Bereitstellen der Azure-Erweiterung für SAP, die im Repository für Azure-Erweiterungen in den globalen Azure-Datencentern verfügbar ist. Weitere Informationen finden Sie unter [Azure Virtual Machines – Planung und Implementierung für SAP NetWeaver][planning-guide-9.1].

Wir sind dabei, eine neue Version der Azure-Erweiterung für SAP zu veröffentlichen. Die neue Erweiterung verwendet die vom System zugewiesene Identität der VM, um Informationen zu den angefügten Datenträgern, den Netzwerkschnittstellen und der VM selbst zu erhalten. Um auf diese Ressourcen zugreifen zu können, benötigt die Systemidentität des virtuellen Computers die Leseberechtigung für den virtuellen Computer, den Betriebssystemdatenträger, die übrigen Datenträger und die Netzwerkschnittstellen. Zurzeit sollten Sie die neue Erweiterung nur in den folgenden Szenarien installieren:

1. Sie möchten die Erweiterung mit Terraform, Azure Resource Manager-Vorlagen oder mit anderen Mittel als Azure CLI oder Azure PowerShell installieren.
1. Sie möchten die Erweiterung auf SuSE SLES 15 oder höher installieren.
1. Der Microsoft- oder SAP-Support fordert Sie auf, die neue Erweiterung zu installieren.
1. Sie möchten Azure Ultra Disk oder Standard Managed Disks verwenden.

Führen Sie für diese Szenarien die Schritte im Kapitel [Konfigurieren der neuen Azure-Erweiterung für SAP mit Azure PowerShell][deployment-guide-configure-new-extension-ps] für Azure PowerShell oder [Konfigurieren der neuen Azure-Erweiterung für SAP mit Azure CLI][deployment-guide-configure-new-extension-cli] für Azure CLI aus.

Verwenden Sie [Azure PowerShell][deployment-guide-4.5.1] oder [Azure CLI][deployment-guide-4.5.2] zum Installieren und Konfigurieren der Standardversion der Azure-Erweiterung für SAP.

#### <a name="azure-powershell-for-linux-and-windows-vms"></a><a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Azure PowerShell für virtuelle Linux- und Windows-Computer

Gehen Sie wie folgt vor, um die Azure-Erweiterung für SAP mit PowerShell zu installieren:

1. Achten Sie darauf, dass Sie die aktuelle Version der Azure PowerShell-Cmdlets verwenden. Weitere Informationen finden Sie unter [Bereitstellen von Azure PowerShell-Cmdlets][deployment-guide-4.1].  
1. Führen Sie das folgende PowerShell-Cmdlet aus.
    Führen Sie das Cmdlet `Get-AzEnvironment` aus, um eine Liste der verfügbaren Umgebungen zu erhalten. Falls Sie öffentliches Azure verwenden möchten, lautet die zugehörige Umgebung **AzureCloud**. Wählen Sie für Azure China 21Vianet die Option **AzureChinaCloud**.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

Nachdem Sie Ihre Kontodaten angegeben haben, stellt das Skript die erforderlichen Erweiterungen bereit und aktiviert die benötigten Features. Dies kann einige Minuten dauern.
Weitere Informationen zu `Set-AzVMAEMExtension` finden Sie unter [Set-AzVMAEMExtension][msdn-set-Azvmaemextension].

![Erfolgreiche Ausführung des SAP-spezifischen Azure-Cmdlets „Set-AzVMAEMExtension“][deployment-guide-figure-900]

Bei der `Set-AzVMAEMExtension`-Konfiguration werden alle Schritte zum Konfigurieren der Hostdatensammlung für SAP ausgeführt.

Die Skriptausgabe enthält die folgenden Informationen:

* Bestätigung, dass die Datensammlung für den Betriebssystemdatenträger und alle weiteren Datenträger für Daten konfiguriert wurde.
* Mit den nächsten beiden Meldungen wird die Konfiguration der Speichermetriken für ein bestimmtes Speicherkonto bestätigt.
* Eine Zeile der Ausgabe gibt den Zustand der laufenden Aktualisierung der Konfiguration der VM-Erweiterung für SAP an.
* In einer anderen Zeile der Ausgabe wird bestätigt, dass die Konfiguration bereitgestellt oder aktualisiert wurde.
* Die letzte Zeile der Ausgabe dient nur zu Informationszwecken. Sie enthält Ihre Optionen für ein Testen der Konfiguration der VM-Erweiterung für SAP.
* Um zu überprüfen, ob alle Schritte der Konfiguration der VM-Erweiterung für SAP erfolgreich ausgeführt wurden und die Azure-Infrastruktur die notwendigen Daten bereitstellt, fahren Sie mit der Bereitschaftsprüfung für die Azure-Erweiterung für SAP fort. Dies ist unter [Bereitschaftsprüfung für die Azure-Erweiterung für SAP][deployment-guide-5.1] beschrieben.
* Warten Sie 15 bis 30 Minuten, bis die relevanten Daten mit der Azure-Diagnose erfasst wurden.

#### <a name="azure-cli-for-linux-vms"></a><a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Azure-CLI für virtuelle Linux-Computer

Gehen Sie wie folgt vor, um die Azure-Erweiterung für SAP über die Azure-Befehlszeilenschnittstelle zu installieren:

   1. Installieren Sie die klassische Azure-Befehlszeilenschnittstelle (Azure CLI), wie unter [Installieren der klassischen Azure CLI][azure-cli] beschrieben.
   1. Melden Sie sich mit Ihrem Azure-Konto an:

      ```console
      azure login
      ```

   1. Wechseln Sie in den Azure Resource Manager-Modus:

      ```console
      azure config mode arm
      ```

   1. Aktivieren Sie die Azure-Erweiterung für SAP:

      ```console
      azure vm enable-aem <resource-group-name> <vm-name>
      ```

1. So führen Sie eine Installation mit der Azure CLI 2.0 aus

   1. Installieren Sie die Azure CLI 2.0 wie unter [Installieren der Azure CLI 2.0][azure-cli-2] beschrieben.
   1. Melden Sie sich mit Ihrem Azure-Konto an:

      ```azurecli
      az login
      ```

   1. Installieren Sie Azure CLI-AEM-Erweiterung.
  
      ```azurecli
      az extension add --name aem
      ```
  
   1. Installieren Sie die Erweiterung mit diesem Befehl:
  
      ```azurecli
      az vm aem set -g <resource-group-name> -n <vm name>
      ```

1. Stellen Sie sicher, dass die Azure-Erweiterung für SAP auf dem virtuellen Azure Linux-Computer aktiv ist. Vergewissern Sie sich, dass die Datei „\\var\\lib\\AzureEnhancedMonitor\\PerfCounters“ vorhanden ist. Ist sie vorhanden, führen Sie an einer Befehlseingabeaufforderung den folgenden Befehl aus, um die mit der Azure-Erweiterung für SAP gesammelten Informationen anzuzeigen:

   ```console
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   Die Ausgabe sieht wie folgt aus:

   ```output
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

#### <a name="configure-the-new-azure-extension-for-sap-with-azure-powershell"></a><a name="2ad55a0d-9937-4943-9dd2-69bc2b5d3de0"></a>Konfigurieren der neuen Azure-Erweiterung für SAP mit Azure PowerShell

Die neue VM-Erweiterung für SAP verwendet eine verwaltete Identität, die dem virtuellen Computer zugewiesen ist, um auf Überwachungs- und Konfigurationsdaten der VM zuzugreifen. Um die neue Azure-Erweiterung für SAP mithilfe von PowerShell zu installieren, müssen Sie der VM zunächst eine solche Identität zuweisen und dieser Identität Zugriff auf alle Ressourcen gewähren, die von dieser VM verwendet werden, z. B. Datenträger und Netzwerkschnittstellen.

> [!NOTE]
> Die folgenden Schritte erfordern Besitzerberechtigungen für die Ressourcengruppe oder einzelne Ressourcen (virtueller Computer, Datenträger usw.).

1. Stellen Sie sicher, dass Sie SAP Host Agent 7.21 PL 47 oder höher verwenden.
1. Stellen Sie sicher, dass Sie die aktuelle Version der VM-Erweiterung für SAP deinstallieren. Die Installation beider Versionen der VM-Erweiterung für SAP auf demselben virtuellen Computer wird nicht unterstützt.
1. Achten Sie darauf, die aktuelle Version der Azure PowerShell-Cmdlets (mindestens 4.3.0) zu verwenden. Weitere Informationen finden Sie unter [Bereitstellen von Azure PowerShell-Cmdlets][deployment-guide-4.1].
1. Führen Sie das folgende PowerShell-Cmdlet aus.
    Führen Sie das Cmdlet `Get-AzEnvironment` aus, um eine Liste der verfügbaren Umgebungen zu erhalten. Falls Sie öffentliches Azure verwenden möchten, lautet die zugehörige Umgebung **AzureCloud**. Wählen Sie für Azure China 21Vianet die Option **AzureChinaCloud**.

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name> -InstallNewExtension
    ```

#### <a name="configure-the-new-azure-extension-for-sap-with-azure-cli"></a><a name="c8749c24-fada-42ad-b114-f9aae2dc37da"></a>Konfigurieren der neuen Azure-Erweiterung für SAP mit Azure CLI

Die neue VM-Erweiterung für SAP verwendet eine verwaltete Identität, die dem virtuellen Computer zugewiesen ist, um auf Überwachungs- und Konfigurationsdaten der VM zuzugreifen. Um die neue Azure-Erweiterung für SAP mithilfe der Azure CLI zu installieren, müssen Sie der VM zunächst eine solche Identität zuweisen und dieser Identität Zugriff auf alle Ressourcen gewähren, die von dieser VM verwendet werden, z. B. Datenträger und Netzwerkschnittstellen.

> [!NOTE]
> Die folgenden Schritte erfordern Besitzerberechtigungen für die Ressourcengruppe oder einzelne Ressourcen (virtueller Computer, Datenträger usw.).

1. Stellen Sie sicher, dass Sie SAP Host Agent 7.21 PL 47 oder höher verwenden.
1. Stellen Sie sicher, dass Sie die aktuelle Version der VM-Erweiterung für SAP deinstallieren. Die Installation beider Versionen der VM-Erweiterung für SAP auf demselben virtuellen Computer wird nicht unterstützt.
1. Installieren Sie die Azure CLI 2.0 wie unter [Installieren der Azure CLI 2.0][azure-cli-2] beschrieben.

1. Melden Sie sich mit Ihrem Azure-Konto an:

   ```azurecli
   az login
   ```

1. Befolgen Sie die Schritte im Artikel [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe von Azure CLI][qs-configure-cli-windows-vm], um eine vom System zugewiesene verwaltete Identität auf der VM zu aktivieren. Vom Benutzer zugewiesene verwaltete Identitäten werden von der VM-Erweiterung für SAP nicht unterstützt. Sie können jedoch sowohl eine vom System zugewiesene als auch eine vom Benutzer zugewiesene Identität aktivieren.

   Beispiel:
   ```azurecli
   az vm identity assign -g <resource-group-name> -n <vm name>
   ```

1. Weisen Sie der Ressourcengruppe des virtuellen Computers oder allen Netzwerkschnittstellen, verwalteten Datenträgern und der VM selbst wie unter [Zuweisen des Zugriffs auf eine Ressource für eine verwaltete Identität mithilfe der Azure CLI][howto-assign-access-cli] beschrieben den Zugriff auf die verwaltete Identität zu.

    Beispiel:

    ```azurecli
    spID=$(az resource show -g <resource-group-name> -n <vm name> --query identity.principalId --out tsv --resource-type Microsoft.Compute/virtualMachines)
    rgId=$(az group show -g <resource-group-name> --query id --out tsv)
    az role assignment create --assignee $spID --role 'Reader' --scope $rgId
    ```

1. Führen Sie den folgenden Azure CLI-Befehl aus, um die Azure-Erweiterung für SAP zu installieren.
    Die Erweiterung wird zurzeit nur in Azure Cloud unterstützt. Azure China 21ViaNet, Azure Government oder andere spezielle Umgebungen werden noch nicht unterstützt.

    ```azurecli
    # For Linux machines
    az vm extension set --publisher Microsoft.AzureCAT.AzureEnhancedMonitoring --name MonitorX64Linux --version 1.0 -g <resource-group-name> --vm-name <vm name> --settings '{"system":"SAP"}'

    #For Windows machines
    az vm extension set --publisher Microsoft.AzureCAT.AzureEnhancedMonitoring --name MonitorX64Windows --version 1.0 -g <resource-group-name> --vm-name <vm name> --settings '{"system":"SAP"}'
    ```

## <a name="checks-and-troubleshooting"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Fehler und Problembehandlung

Nachdem Sie den virtuellen Azure-Computer bereitgestellt und die entsprechende Azure-Erweiterung für SAP eingerichtet haben, sollten Sie überprüfen, ob alle Komponenten der Erweiterung ordnungsgemäß funktionieren.

Führen Sie die Bereitschaftsprüfung für die Azure-Erweiterung für SAP so aus, wie dies unter [Bereitschaftsprüfung für die Azure-Erweiterung für SAP][deployment-guide-5.1] beschrieben ist. Wenn alle Ergebnisse der Bereitschaftsprüfung positiv ausfallen und alle relevanten Leistungsindikatoren in Ordnung sind, wurde die Azure-Erweiterung für SAP erfolgreich eingerichtet. Sie können mit der Installation des SAP Host Agents fortfahren, wie in den SAP-Hinweisen unter [SAP-Ressourcen][deployment-guide-2.2] beschrieben. Wenn für die Bereitschaftsprüfung das Fehlen von Leistungsindikatoren angezeigt wird, führen Sie den Integritätstest für die Azure-Erweiterung für SAP so aus, wie dies unter [Integritätsprüfung für die Konfiguration der Azure-Erweiterung für SAP][deployment-guide-5.2] beschrieben ist. Weitere Optionen zum Troubleshooting finden Sie unter [Problembehandlung für die Azure-Erweiterung für SAP][deployment-guide-5.3].

### <a name="readiness-check-for-the-azure-extension-for-sap"></a><a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Bereitschaftsprüfung für die Azure-Erweiterung für SAP

> [!NOTE]
> Es gibt zwei Versionen der VM-Erweiterung. In diesem Kapitel wird die Standard-VM-Erweiterung behandelt. Wenn Sie die neue VM-Erweiterung installiert haben, finden Sie im Kapitel [Bereitschaftsüberprüfung für die neue Azure-Erweiterung für SAP][deployment-guide-5.1-new] weitere Informationen.

Mit dieser Prüfung wird sichergestellt, dass alle Leistungsmetriken, die in Ihrer SAP-Anwendung angezeigt werden, über die zugrunde liegende Azure-Erweiterung für SAP bereitgestellt werden.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Ausführen der Bereitschaftsprüfung auf einem virtuellen Windows-Computer

1. Melden Sie sich am virtuellen Azure-Computer an (die Verwendung eines Administratorkontos ist nicht erforderlich).
1. Öffnen Sie ein Eingabeaufforderungsfenster.
1. Ändern Sie an der Eingabeaufforderung das Verzeichnis für den Installationsordner der Azure-Erweiterung für SAP: C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;version>\\drop

   Die *Version* im Pfad zur Erweiterung kann variieren. Sollten Ordner für mehrere Versionen der Erweiterung im Installationsordner vorhanden sein, sollten Sie die Konfiguration des Windows-Diensts „AzureEnhancedMonitoring“ überprüfen und dann in den Ordner wechseln, der als *Pfad zur ausführbaren Datei* angegeben ist.

   ![Eigenschaften des Diensts, der die Azure-Erweiterung für SAP ausführt][deployment-guide-figure-1000]

1. Führen Sie an der Befehlseingabeaufforderung die Datei **azperflib.exe** ohne Parameter aus.

   > [!NOTE]
   > Das Programm „azperflib.exe“ wird in einer Schleife ausgeführt und aktualisiert die erfassten Leistungsindikatoren alle 60 Sekunden. Schließen Sie das Eingabeaufforderungsfenster, um die Schleife zu beenden.
   >
   >

Wenn die Azure-Erweiterung für SAP nicht installiert ist oder der Dienst „AzureEnhancedMonitoring“ nicht ausgeführt wird, wurde die Erweiterung nicht richtig konfiguriert. Ausführliche Informationen zur Bereitstellung der Erweiterung finden Sie unter [Problembehandlung für die Azure-Erweiterung für SAP][deployment-guide-5.3].

> [!NOTE]
> „Azperflib.exe“ kann nicht für eigene Zwecke verwendet werden. Sie ist eine Komponente, die Azure-Infrastrukturdaten in Bezug auf die VM exklusiv für den SAP-Host-Agent bereitstellt.
> 

##### <a name="check-the-output-of-azperflibexe"></a>Überprüfen der Ausgabe von „azperflib.exe“

Die Ausgabe von „azperflib.exe“ enthält alle ausgefüllten Azure-Leistungsindikatoren für SAP. Am Ende der Liste mit den gesammelten Indikatoren finden Sie eine Übersicht und einen Integritätsindikator, der den Zustand der Azure-Erweiterung für SAP anzeigt.

![Ausgabe der Integritätsprüfung durch Ausführen von „azperflib.exe“, wenn keine Probleme bestehen][deployment-guide-figure-1100]
<a name="figure-11"></a>

Prüfen Sie das Ergebnis unter **Counters total** (Indikatoren gesamt), das als „Leer“ gemeldet wird, und für den **Integritätsstatus**, wie in der obigen Abbildung gezeigt.

Interpretieren Sie die Ergebniswerte wie folgt:

| Ergebniswerte von „azperflib.exe“ | Integritätsstatus der Azure-Erweiterung für SAP |
| --- | --- |
| **API-Aufrufe – nicht verfügbar** | Leistungsindikatoren, die nicht verfügbar sind, gelten entweder nicht für die Konfiguration des virtuellen Computers oder sind fehlerhaft. Weitere Informationen finden Sie unter **Integritätsstatus**. |
| **Indikatoren gesamt – leer** |Die folgenden beiden Azure Storage-Indikatoren dürfen leer sein: <ul><li>Storage Read Op Latency Server msec</li><li>Storage Read Op Latency E2E msec</li></ul>Alle anderen Leistungsindikatoren müssen Werte enthalten. |
| **Integritätsstatus** |Nur in Ordnung bei Rückgabestatus **OK**. |
| **Diagnose** |Liefert ausführliche Informationen zum Integritätsstatus. |

Wenn für den Wert **Integritätsstatus** nicht **OK** angezeigt wird, hilft Ihnen die Anleitung unter [Integritätsprüfung für die Konfiguration der Azure-Erweiterung für SAP][deployment-guide-5.2] weiter.

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Ausführen der Bereitschaftsprüfung auf einem virtuellen Linux-Computer

1. Stellen Sie per SSH eine Verbindung mit der Azure-VM her.

1. Überprüfen Sie die Ausgabe der Azure-Erweiterung für SAP.

   a.  Ausführen von `more /var/lib/AzureEnhancedMonitor/PerfCounters`

   **Erwartetes Ergebnis**: Gibt die Liste der Leistungsindikatoren zurück. Die Datei sollte nicht leer sein.

   b. Ausführen von `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error`

   **Erwartetes Ergebnis**: Gibt eine Zeile zurück, für die der Fehler **none** lautet, z.B. **3;config;Error;;0;0;none;0;1456416792;tst-servercs;** .

   c. Ausführen von `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord`

   **Erwartetes Ergebnis**: Wird als „Leer“ zurückgegeben oder ist nicht vorhanden.

Führen Sie diese zusätzliche Überprüfungen durch, wenn die vorherige Überprüfung nicht erfolgreich war:

1. Achten Sie darauf, dass waagent installiert und aktiviert wurde.

   a.  Ausführen von `sudo ls -al /var/lib/waagent/`

     **Erwartetes Ergebnis**: Listet den Inhalt des Verzeichnisses „waagent“ auf.

   b.  Ausführen von `ps -ax | grep waagent`

   **Erwartetes Ergebnis**: Zeigt einen Eintrag an, der dem folgenden Text ähnelt: `python /usr/sbin/waagent -daemon`

1. Vergewissern Sie sich, dass die Azure-Erweiterung für SAP installiert ist und ausgeführt wird.

   a.  Ausführen von `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'`

   **Erwartetes Ergebnis**: Listet den Inhalt des Verzeichnisses für die Azure-Erweiterung für SAP auf.

   b. Ausführen von `ps -ax | grep AzureEnhanced`

   **Erwartetes Ergebnis**: Zeigt einen Eintrag an, der dem folgenden Text ähnelt: `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon`

1. Installieren Sie den SAP Host Agent gemäß SAP-Hinweis [1031096], und überprüfen Sie die Ausgabe von `saposcol`.

   a.  Ausführen von `/usr/sap/hostctrl/exe/saposcol -d`

   b.  Ausführen von `dump ccm`

   c.  Überprüfen Sie, ob für die Metrik **Virtualization_Configuration\Enhanced Monitoring Access** der Wert **true** angegeben ist.

Wenn bereits ein SAP NetWeaver ABAP-Anwendungsserver installiert ist, öffnen Sie die Transaktion ST06, und überprüfen Sie, ob die Überwachung aktiviert ist.

Falls eine dieser Überprüfungen einen Fehler verursacht und Sie ausführliche Informationen zur Bereitstellung der Erweiterung benötigen, lesen Sie [Problembehandlung für die Azure-Erweiterung für SAP][deployment-guide-5.3].

### <a name="readiness-check-for-the-new-azure-extension-for-sap"></a><a name="7bf24f59-7347-4c7a-b094-4693e4687ee5"></a>Bereitschaftsüberprüfung für die neue Azure-Erweiterung für SAP

> [!NOTE]
> Es gibt zwei Versionen der VM-Erweiterung. In diesem Kapitel wird die neue VM-Erweiterung behandelt. Wenn Sie die Standard-VM-Erweiterung installiert haben, finden Sie im Kapitel [Bereitschaftsprüfung für die Azure-Erweiterung für SAP][deployment-guide-5.1] weitere Informationen.

Mit dieser Prüfung wird sichergestellt, dass alle Leistungsmetriken, die in Ihrer SAP-Anwendung angezeigt werden, über die zugrunde liegende Azure-Erweiterung für SAP bereitgestellt werden.

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Ausführen der Bereitschaftsprüfung auf einem virtuellen Windows-Computer

1. Melden Sie sich am virtuellen Azure-Computer an (die Verwendung eines Administratorkontos ist nicht erforderlich).
1. Öffnen Sie einen Webbrowser, und navigieren Sie zu http://127.0.0.1:11812/azure4sap/metrics.
1. Der Browser sollte eine XML-Datei anzeigen oder herunterladen, die die Überwachungsdaten des virtuellen Computers enthält. Falls nicht, vergewissern Sie sich, dass die Azure-Erweiterung für SAP installiert ist.

##### <a name="check-the-content-of-the-xml-file"></a>Überprüfen des Inhalts der XML-Datei

Die XML-Datei, auf die Sie unter http://127.0.0.1:11812/azure4sap/metrics zugreifen können, enthält alle gefüllten Azure-Leistungsindikatoren für SAP. Außerdem enthält sie eine Zusammenfassung und einen Integritätsindikator für den Status der Azure-Erweiterung für SAP.

Überprüfen Sie den Wert des Elements **Provider Health Description**. Wenn der Wert nicht **OK** ist, hilft Ihnen die Anleitung unter [Integritätsprüfung für die Konfiguration der neuen Azure-Erweiterung für SAP][deployment-guide-5.2-new] weiter.

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Ausführen der Bereitschaftsprüfung auf einem virtuellen Linux-Computer

1. Stellen Sie per SSH eine Verbindung mit der Azure-VM her.

1. Überprüfen Sie die Ausgabe des folgenden Befehls.

    ```console
    curl http://127.0.0.1:11812/azure4sap/metrics
    ```
    
   **Erwartetes Ergebnis**: Rückgabe eines XML-Dokuments, das die Überwachungsinformationen für den virtuellen Computer, seine Datenträger und die Netzwerkschnittstellen enthält.

Führen Sie diese zusätzliche Überprüfungen durch, wenn die vorherige Überprüfung nicht erfolgreich war:

1. Achten Sie darauf, dass waagent installiert und aktiviert wurde.

   a.  Ausführen von `sudo ls -al /var/lib/waagent/`

     **Erwartetes Ergebnis**: Listet den Inhalt des Verzeichnisses „waagent“ auf.

   b.  Ausführen von `ps -ax | grep waagent`

   **Erwartetes Ergebnis**: Zeigt einen Eintrag an, der dem folgenden Text ähnelt: `python /usr/sbin/waagent -daemon`

1. Vergewissern Sie sich, dass die Azure-Erweiterung für SAP installiert ist und ausgeführt wird.

   a.  Ausführen von `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-*/'`

   **Erwartetes Ergebnis**: Listet den Inhalt des Verzeichnisses für die Azure-Erweiterung für SAP auf.

   b. Ausführen von `ps -ax | grep AzureEnhanced`

   **Erwartetes Ergebnis**: Zeigt einen Eintrag an, der dem folgenden Text ähnelt: `/var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-1.0.0.82/AzureEnhancedMonitoring -monitor`

1. Installieren Sie den SAP Host Agent gemäß SAP-Hinweis [1031096], und überprüfen Sie die Ausgabe von `saposcol`.

   a.  Ausführen von `/usr/sap/hostctrl/exe/saposcol -d`

   b.  Ausführen von `dump ccm`

   c.  Überprüfen Sie, ob für die Metrik **Virtualization_Configuration\Enhanced Monitoring Access** der Wert **true** angegeben ist.

Wenn bereits ein SAP NetWeaver ABAP-Anwendungsserver installiert ist, öffnen Sie die Transaktion ST06, und überprüfen Sie, ob die Überwachung aktiviert ist.

Falls eine dieser Überprüfungen einen Fehler verursacht und Sie ausführliche Informationen zur Bereitstellung der Erweiterung benötigen, lesen Sie [Problembehandlung für die neue Azure-Erweiterung für SAP][deployment-guide-5.3-new].

### <a name="health-check-for-the-azure-extension-for-sap-configuration"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Integritätsprüfung für die Azure-Erweiterung für die SAP-Konfiguration

> [!NOTE]
> Es gibt zwei Versionen der VM-Erweiterung. In diesem Kapitel wird die Standard-VM-Erweiterung behandelt. Wenn Sie die neue VM-Erweiterung installiert haben, finden Sie im Kapitel [Integritätsprüfung für die neue Azure-Erweiterung für die SAP-Konfiguration][deployment-guide-5.2-new] weitere Informationen.

Wenn einige Infrastrukturdaten gemäß der Beschreibung des Tests unter [Bereitschaftsprüfung für die Azure-Erweiterung für SAP][deployment-guide-5.1] nicht richtig bereitgestellt werden, führen Sie das Cmdlet `Test-AzVMAEMExtension` aus. Mit diesem Cmdlet können Sie überprüfen, ob die Azure-Infrastruktur und die Azure-Erweiterung für SAP richtig konfiguriert sind.

1. Achten Sie darauf, dass die neueste Version des Azure PowerShell-Cmdlets gemäß [Bereitstellen von Azure PowerShell-Cmdlets][deployment-guide-4.1] installiert ist.
1. Führen Sie das folgende PowerShell-Cmdlet aus. Führen Sie zum Anzeigen der Liste mit den verfügbaren Umgebungen das `Get-AzEnvironment`-Cmdlet aus. Wählen Sie die Umgebung **AzureCloud** aus, um globales Azure zu verwenden. Wählen Sie für Azure China 21Vianet die Option **AzureChinaCloud**.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. Mit dem Skript wird die Konfiguration des von Ihnen ausgewählten virtuellen Computers getestet.

   ![Ausgabe eines erfolgreichen Tests der Azure-Erweiterung für SAP][deployment-guide-figure-1300]

Stellen Sie sicher, dass das Ergebnis für jede Integritätsprüfung **OK** lautet. Wenn für einige Prüfungen nicht **OK** angezeigt wird, führen Sie das Aktualisierungs-Cmdlet wie unter [Konfigurieren der Azure-Erweiterung für SAP][deployment-guide-4.5] beschrieben aus. Warten Sie 15 Minuten, und wiederholen Sie dann die Tests, die unter [Bereitschaftsprüfung für die Azure-Erweiterung für SAP][deployment-guide-5.1] und [Integritätsprüfung für die Konfiguration der Azure-Erweiterung für SAP][deployment-guide-5.2] beschrieben sind. Falls die Tests weiterhin für einige oder alle Indikatoren Probleme melden, lesen Sie [Problembehandlung für die Azure-Erweiterung für SAP][deployment-guide-5.3].

> [!Note]
> Möglicherweise treten einige Warnungen auf, wenn Sie verwaltete Azure-Standarddatenträger verwenden. Die Tests geben nicht „OK“ zurück, stattdessen werden Warnungen angezeigt. Dies ist normal und im Fall dieses Datenträgertyps beabsichtigt. Weitere Informationen finden Sie unter [Problembehandlung für die Azure-Erweiterung für SAP][deployment-guide-5.3].
> 

### <a name="health-check-for-the-new-azure-extension-for-sap-configuration"></a><a name="464ac96d-7d3c-435d-a5ae-3faf3bfef4b3"></a>Integritätsprüfung für die neue Azure-Erweiterung für die SAP-Konfiguration

> [!NOTE]
> Es gibt zwei Versionen der VM-Erweiterung. In diesem Kapitel wird die neue VM-Erweiterung behandelt. Wenn Sie die Standard-VM-Erweiterung installiert haben, finden Sie im Kapitel [Integritätsprüfung für die Azure-Erweiterung für die SAP-Konfiguration][deployment-guide-5.2] weitere Informationen.

Wenn einige Infrastrukturdaten gemäß der Beschreibung des Tests unter [Bereitschaftsprüfung für die Azure-Erweiterung für SAP][deployment-guide-5.1-new] nicht richtig bereitgestellt werden, führen Sie das Cmdlet `Get-AzVMExtension` aus, um zu überprüfen, ob die Azure-Erweiterung für SAP installiert ist. Die `Test-AzVMAEMExtension` unterstützt die neue Erweiterung noch nicht. Wenn das Cmdlet die neue Erweiterung unterstützt, wird dieser Artikel aktualisiert.

1. Achten Sie darauf, dass die neueste Version des Azure PowerShell-Cmdlets gemäß [Bereitstellen von Azure PowerShell-Cmdlets][deployment-guide-4.1] installiert ist.
1. Führen Sie das folgende PowerShell-Cmdlet aus. Führen Sie zum Anzeigen der Liste mit den verfügbaren Umgebungen das `Get-AzEnvironment`-Cmdlet aus. Wählen Sie die Umgebung **AzureCloud** aus, um globales Azure zu verwenden. Wählen Sie für Azure China 21Vianet die Option **AzureChinaCloud**.

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. Das Cmdlet testet die Konfiguration der VM-Erweiterung für SAP auf dem ausgewählten virtuellen Computer.

### <a name="troubleshooting-azure-extension-for-sap"></a><a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Problembehandlung für die Azure-Erweiterung für SAP

> [!NOTE]
> Es gibt zwei Versionen der VM-Erweiterung. In diesem Kapitel wird die Standard-VM-Erweiterung behandelt. Wenn Sie die neue VM-Erweiterung installiert haben, finden Sie weitere Informationen im Kapitel [Problembehandlung für die neue Azure-Erweiterung für SAP][deployment-guide-5.3-new].

#### <a name="windows-logologo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows-Logo][Logo_Windows] Es werden keinerlei Azure-Leistungsindikatoren angezeigt.

Mit dem Windows-Dienst „AzureEnhancedMonitoring“ werden in Azure Leistungsmetriken gesammelt. Wenn der Dienst nicht richtig installiert wurde oder wenn er auf der VM nicht ausgeführt wird, können keine Leistungsmetriken erfasst werden.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Das Installationsverzeichnis der Azure-Erweiterung für SAP ist leer.

###### <a name="issue"></a>Problem

Das Installationsverzeichnis „C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;Version>\\drop“ ist leer.

###### <a name="solution"></a>Lösung

Die Erweiterung ist nicht installiert. Ermitteln Sie, ob es sich um ein Proxyproblem handelt (wie weiter oben beschrieben). Unter Umständen müssen Sie den Computer neu starten oder das Konfigurationsskript `Set-AzVMAEMExtension` erneut ausführen.

##### <a name="service-for-azure-extension-for-sap-does-not-exist"></a>Der Dienst für die Azure-Erweiterung für SAP ist nicht vorhanden.

###### <a name="issue"></a>Problem

Der Windows-Dienst „AzureEnhancedMonitoring“ ist nicht vorhanden.

Mit der Ausgabe von „azperflib.exe“ wird ein Fehler ausgelöst:

![Ausführung von „azperflib.exe“ mit der Angabe, dass der Dienst der Azure-Erweiterung für SAP nicht ausgeführt wird][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Lösung

Wenn der Dienst nicht vorhanden ist, wurde die Azure-Erweiterung für SAP nicht richtig installiert. Stellen Sie die Erweiterung erneut bereit, indem Sie die Schritte für Ihr Bereitstellungsszenario unter [Bereitstellungsszenarien für virtuelle Computer für SAP in Microsoft Azure][deployment-guide-3] ausführen.

Überprüfen Sie nach dem Bereitstellen der Erweiterung nach Ablauf einer Stunde erneut, ob die Azure-Leistungsindikatoren in der Azure-VM angegeben werden.

##### <a name="service-for-azure-extension-for-sap-exists-but-fails-to-start"></a>Der Dienst für die Azure-Erweiterung für SAP ist vorhanden, startet jedoch nicht.

###### <a name="issue"></a>Problem

Der Windows-Dienst „AzureEnhancedMonitoring“ ist vorhanden und aktiviert, kann aber nicht gestartet werden. Weitere Informationen finden Sie im Anwendungsereignisprotokoll.

###### <a name="solution"></a>Lösung

Die Konfiguration ist fehlerhaft. Starten Sie die Azure-Erweiterung für SAP auf der VM neu, wie dies unter [Konfigurieren der Azure-Erweiterung für SAP][deployment-guide-4.5] beschrieben ist.

#### <a name="windows-logologo_windows-some-azure-performance-counters-are-missing"></a>![Windows-Logo][Logo_Windows] Einige Azure-Leistungsindikatoren fehlen.

Mit dem Windows-Dienst „AzureEnhancedMonitoring“ werden in Azure Leistungsmetriken gesammelt. Mit dem Dienst werden Daten aus mehreren Quellen abgerufen. Einige Konfigurationsdaten werden lokal gesammelt, und einige Leistungsmetriken werden aus der Azure-Diagnose ausgelesen. Die Speicherindikatoren werden aus Ihrer Protokollierung auf Speicherabonnementebene verwendet.

Führen Sie das Konfigurationsskript `Set-AzVMAEMExtension` erneut aus, falls die Problembehandlung mithilfe von SAP-Hinweis [1999351] nicht zur Lösung des Problems führt. Unter Umständen ist eine Wartezeit von einer Stunde erforderlich, da Speicheranalyse- und Diagnoseindikatoren ggf. nicht sofort nach der Aktivierung erstellt werden. Falls das Problem weiterhin besteht, öffnen Sie eine SAP-Kundendienstmeldung für die Komponente BC-OP-NT-AZR für virtuelle Windows-Computer oder BC-OP-LNX-AZR für virtuelle Linux-Computer.

#### <a name="linux-logologo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux-Logo][Logo_Linux] Es werden keinerlei Azure-Leistungsindikatoren angezeigt.

Leistungsmetriken werden in Azure über einen Daemon erfasst. Wenn der Daemon nicht ausgeführt wird, können keine Leistungsmetriken erfasst werden.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Das Installationsverzeichnis der Azure-Erweiterung für SAP ist leer.

###### <a name="issue"></a>Problem

Das Verzeichnis „\\var\\lib\\waagent\\“ enthält kein Unterverzeichnis für die Azure-Erweiterung für SAP.

###### <a name="solution"></a>Lösung

Die Erweiterung ist nicht installiert. Ermitteln Sie, ob es sich um ein Proxyproblem handelt (wie weiter oben beschrieben). Unter Umständen müssen Sie den Computer neu starten bzw. das Konfigurationsskript `Set-AzVMAEMExtension` erneut ausführen.

##### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>Bei der Ausführung von „Set-AzVMAEMExtension“ und „Test-AzVMAEMExtension“ weisen Warnmeldungen darauf hin, dass verwaltete Standarddatenträger nicht unterstützt werden.

###### <a name="issue"></a>Problem

Bei der Ausführung von „Set-AzVMAEMExtension“ oder „Test-AzVMAEMExtension“ werden Meldungen wie die folgenden angezeigt:

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

Beim Ausführen von „azperfli.exe“ wie oben beschrieben können Sie ein Ergebnis erzielen, das auf einen fehlerhaften Zustand hinweist. 

###### <a name="solution"></a>Lösung

Diese Meldungen werden dadurch verursacht, dass verwaltete Standarddatenträger nicht die APIs bereitstellen, die von der Azure-Erweiterung für SAP verwendet werden, um die Statistiken der Azure Storage-Standardkonten zu überprüfen. Dies ist kein Grund zur Besorgnis. Der Grund für die Einführung des Sammelns von Daten für Disk Storage-Standardkonten war die häufig auftretende Drosselung von Ein- und Ausgaben. Verwaltete Datenträger verhindern eine solche Drosselung, indem die Anzahl von Datenträgern in einem Storage-Konto eingeschränkt wird. Daher ist es kein Problem, wenn dieser Typ von Daten nicht verfügbar ist.


#### <a name="linux-logologo_linux-some-azure-performance-counters-are-missing"></a>![Linux-Logo][Logo_Linux] Einige Azure-Leistungsindikatoren fehlen.

Leistungsmetriken werden in Azure über einen Daemon erfasst, der Daten aus mehreren Quellen abruft. Einige Konfigurationsdaten werden lokal gesammelt, und einige Leistungsmetriken werden aus der Azure-Diagnose ausgelesen. Speicherindikatoren stammen aus den Protokollen in Ihrem Speicherabonnement.

Eine vollständige und aktuelle Liste mit bekannten Problemen finden Sie im SAP-Hinweis [1999351], der zusätzliche Informationen zur Problembehandlung für die Azure-Erweiterung für SAP enthält.

Führen Sie das Konfigurationsskript `Set-AzVMAEMExtension` wie unter [Konfigurieren der Azure-Erweiterung für SAP][deployment-guide-4.5] beschrieben erneut aus, falls die Problembehandlung mithilfe von SAP-Hinweis [1999351] nicht zur Lösung des Problems führt. Unter Umständen ist eine Wartezeit von einer Stunde erforderlich, da Speicheranalyse- und Diagnoseindikatoren ggf. nicht sofort nach der Aktivierung erstellt werden. Falls das Problem weiterhin besteht, öffnen Sie eine SAP-Kundendienstmeldung für die Komponente BC-OP-NT-AZR für virtuelle Windows-Computer oder BC-OP-LNX-AZR für virtuelle Linux-Computer.

### <a name="troubleshooting-the-new-azure-extension-for-sap"></a><a name="b7afb8ef-a64c-495d-bb37-2af96688c530"></a>Problembehandlung für die neue Azure-Erweiterung für SAP

> [!NOTE]
> Es gibt zwei Versionen der VM-Erweiterung. In diesem Kapitel wird die neue VM-Erweiterung behandelt. Wenn Sie die Standard-VM-Erweiterung installiert haben, finden Sie im Kapitel [Problembehandlung für die Azure-Erweiterung für SAP][deployment-guide-5.3] weitere Informationen.

#### <a name="windows-logologo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows-Logo][Logo_Windows] Es werden keinerlei Azure-Leistungsindikatoren angezeigt.

Mit dem Prozess AzureEnhancedMonitoring werden in Azure Leistungsmetriken gesammelt. Wenn der Prozess in Ihrer VM nicht ausgeführt wird, können keine Leistungsmetriken erfasst werden.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Das Installationsverzeichnis der Azure-Erweiterung für SAP ist leer.

###### <a name="issue"></a>Problem

Das Installationsverzeichnis „C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Windows\\&lt;Version>“ ist leer.

###### <a name="solution"></a>Lösung

Die Erweiterung ist nicht installiert. Ermitteln Sie, ob es sich um ein Proxyproblem handelt (wie weiter oben beschrieben). Möglicherweise müssen Sie den Computer neu starten oder die VM-Erweiterung erneut installieren.

#### <a name="windows-logologo_windows-some-azure-performance-counters-are-missing"></a>![Windows-Logo][Logo_Windows] Einige Azure-Leistungsindikatoren fehlen.

Mit dem Windows-Prozess AzureEnhancedMonitoring werden in Azure Leistungsmetriken gesammelt. Mit dem Prozess werden Daten aus mehreren Quellen abgerufen. Einige Konfigurationsdaten werden lokal gesammelt, und einige Leistungsmetriken werden aus Azure Monitor ausgelesen.

Falls die Problembehandlung mit SAP-Hinweis [1999351] das Problem nicht löst, öffnen Sie eine SAP-Kundendienstmeldung für die Komponente BC-OP-NT-AZR für virtuelle Windows-Computer oder BC-OP-LNX-AZR für virtuelle Linux-Computer. Fügen Sie die Protokolldatei „C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Windows\\&lt;Version>\\logapp.txt“ dem Incident an.

#### <a name="linux-logologo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux-Logo][Logo_Linux] Es werden keinerlei Azure-Leistungsindikatoren angezeigt.

Leistungsmetriken werden in Azure über einen Daemon erfasst. Wenn der Daemon nicht ausgeführt wird, können keine Leistungsmetriken erfasst werden.

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Das Installationsverzeichnis der Azure-Erweiterung für SAP ist leer.

###### <a name="issue"></a>Problem

Das Verzeichnis „\\var\\lib\\waagent\\“ enthält kein Unterverzeichnis für die Azure-Erweiterung für SAP.

###### <a name="solution"></a>Lösung

Die Erweiterung ist nicht installiert. Ermitteln Sie, ob es sich um ein Proxyproblem handelt (wie weiter oben beschrieben). Möglicherweise müssen Sie den Computer neu starten und/oder die VM-Erweiterung erneut installieren.

#### <a name="linux-logologo_linux-some-azure-performance-counters-are-missing"></a>![Linux-Logo][Logo_Linux] Einige Azure-Leistungsindikatoren fehlen.

Leistungsmetriken werden in Azure über einen Daemon erfasst, der Daten aus mehreren Quellen abruft. Einige Konfigurationsdaten werden lokal gesammelt, und einige Leistungsmetriken werden aus Azure Monitor ausgelesen.

Eine vollständige und aktuelle Liste mit bekannten Problemen finden Sie im SAP-Hinweis [1999351], der zusätzliche Informationen zur Problembehandlung für die Azure-Erweiterung für SAP enthält.

Falls die Problembehandlung mit SAP-Hinweis [1999351] das Problem nicht löst, installieren Sie die Erweiterung wie unter [Konfigurieren der Azure-Erweiterung für SAP][deployment-guide-4.5] beschrieben erneut. Falls das Problem weiterhin besteht, öffnen Sie eine SAP-Kundendienstmeldung für die Komponente BC-OP-NT-AZR für virtuelle Windows-Computer oder BC-OP-LNX-AZR für virtuelle Linux-Computer. Fügen Sie die Protokolldatei „/var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-&lt;Version>logapp.txt“ dem Incident an.

## <a name="azure-extension-error-codes"></a>Fehlercodes für die Azure-Erweiterung

| Fehler-ID | Fehlerbeschreibung | Lösung |
|---|---|---|
| <a name="cfg_018"></a>cfg/018 | App-Konfiguration fehlt. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="cfg_019"></a>cfg/019 | Keine Bereitstellungs-ID in der App-Konfiguration. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_020"></a>cfg/020 | Keine RoleInstanceId in der App-Konfiguration. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_022"></a>cfg/022 | Keine RoleInstanceId in der App-Konfiguration. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_031"></a>cfg/031 | Azure-Konfiguration kann nicht gelesen werden. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_021"></a>cfg/021 | App-Konfigurationsdatei fehlt. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="cfg_015"></a>cfg/015 | Keine VM-Größe in der App-Konfiguration. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="cfg_016"></a>cfg/016 | Fehler bei GlobalMemoryStatusEx-Indikator. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_023"></a>cfg/023 | Fehler bei MaxHwFrequency-Indikator. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_024"></a>cfg/024 | Fehler bei NIC-Indikatoren. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_025"></a>cfg/025 | Fehler bei Datenträgerzuordnungs-Indikator. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_026"></a>cfg/026 | Fehler bei Prozessornamen-Indikator. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_027"></a>cfg/027 | Fehler bei Datenträgerzuordnungs-Indikator. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_038"></a>cfg/038 | Die Metrik „Datenträgertyp“ fehlt in der Erweiterungskonfigurationsdatei „config.xml“. „Datenträgertyp“ wurde zusammen mit einigen anderen Indikatoren in v2.2.0.68 12/16/2015 eingeführt. Wenn Sie die Erweiterung vor dem 16.12.2015 bereitgestellt haben, wird die alte Konfigurationsdatei verwendet. Das Azure-Erweiterungsframework führt automatisch ein Upgrade der Erweiterung auf eine neuere Version durch, aber die Datei „config.xml“ bleibt unverändert. Laden Sie zum Aktualisieren der Konfiguration das neueste PowerShell-Setupskript herunter, und führen Sie es aus. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="cfg_039"></a>cfg/039 | Kein Zwischenspeichern auf Datenträger. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="cfg_036"></a>cfg/036 | Kein Datenträger-SLA-Durchsatz. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="cfg_037"></a>cfg/037 | Keine Datenträger-SLA-IOPS. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="cfg_028"></a>cfg/028 | Fehler bei Datenträgerzuordnungs-Indikator. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_029"></a>cfg/029 | Fehler bei Indikator für letzte Hardwareänderung. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_030"></a>cfg/030 | Fehler bei NIC-Indikatoren. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_017"></a>cfg/017 | Wegen Sysprep der VM hat sich Ihre Windows-SID geändert. | [erneute Bereitstellung nach Sysprep][deployment-guide-redeploy-after-sysprep] |
| <a name="str_007"></a>str/007 | Fehler bei Zugriff auf die Speicheranalyse. <br /><br />Da das Auffüllen mit Speicheranalysedaten auf einer neu erstellten VM bis zu einer halben Stunde dauern kann, wird die Fehlermeldung möglicherweise nach einiger Zeit ausgeblendet. Wenn der Fehler weiterhin auftritt, führen Sie das Setupskript erneut aus. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="str_010"></a>str/010 | Keine Speicheranalyse-Indikatoren. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="str_009"></a>str/009 | Fehler bei Speicheranalyse. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="wad_004"></a>wad/004 | Fehlerhafte WAD-Konfiguration. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="wad_002"></a>wad/002 | Unerwartetes WAD-Format. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="wad_001"></a>wad/001 | Keine WAD-Indikatoren gefunden. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="wad_040"></a>wad/040 | Abgelaufene WAD-Indikatoren gefunden. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="wad_003"></a>wad/003 | WAD-Tabelle kann nicht gelesen werden. Es besteht keine Verbindung mit der WAD-Tabelle. Dies kann mehrere Gründe haben:<br /><br /> 1) veraltete Konfiguration <br />2) keine Netzwerkverbindung mit Azure <br />3) Probleme mit dem WAD-Setup | [Setupskript ausführen][deployment-guide-run-the-script]<br />[Internetverbindung korrigieren][deployment-guide-fix-internet-connection]<br />[Support kontaktieren][deployment-guide-contact-support] |
| <a name="prf_011"></a>prf/011 | Fehler bei Systemmonitor-NIC-Metriken. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="prf_012"></a>prf/012 | Fehler bei Systemmonitor-Datenträger-Metriken. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="prf_013"></a>prf/013 | Fehler bei einigen Systemmonitor-Metriken. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="prf_014"></a>prf/014 | Fehler beim Erstellen eines Indikators durch den Systemmonitor. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="cfg_035"></a>cfg/035 | Keine Anbieter für Metriken konfiguriert. | [Support kontaktieren][deployment-guide-contact-support] |
| <a name="str_006"></a>str/006 | Fehlerhafte Speicheranalysekonfiguration. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="str_032"></a>str/032 | Fehler bei Speicheranalyse-Metriken. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="cfg_033"></a>cfg/033 | Fehler bei einem Anbieter für Metriken. | [Setupskript ausführen][deployment-guide-run-the-script] |
| <a name="str_034"></a>str/034 | Fehler bei Anbieterthread. | [Support kontaktieren][deployment-guide-contact-support] |

### <a name="detailed-guidelines-on-solutions-provided"></a>Ausführliche Richtlinien zu den bereitgestellten Lösungen

#### <a name="run-the-setup-script"></a><a name="0d2847ad-865d-4a4c-a405-f9b7baaa00c7"></a>Ausführen des Setupskripts

Führen Sie die Schritte im Kapitel [Konfigurieren der Azure-Erweiterung für SAP][deployment-guide-4.5] in diesem Handbuch aus, um die Erweiterung erneut zu installieren. Beachten Sie, dass einige Leistungsindikatoren für die Bereitstellung möglicherweise bis zu 30 Minuten benötigen.

Wenn die Fehler weiterhin auftreten, [wenden Sie sich an den Support][deployment-guide-contact-support].

#### <a name="contact-support"></a><a name="3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2"></a>Kontaktieren des Supports

Unerwarteter Fehler, oder es ist keine Lösung bekannt. Wenden Sie sich unter Verwendung der Protokolldatei „AzureEnhancedMonitoring_service.log“ im Ordner „C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\\<version\>\drop“ (Windows) oder „/var/log/azure/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux“ an den SAP-Support, um weitere Unterstützung zu erhalten.

#### <a name="redeploy-after-sysprep"></a><a name="2cd61f22-187d-42ed-bb8c-def0c983d756"></a>Erneute Bereitstellung nach Sysprep

Wenn Sie ein verallgemeinertes, mit Sysprep bearbeitetes Betriebssystemimage (das SAP-Software einschließen kann) erstellen möchten, sollte dieses Image die Azure-Erweiterung für SAP nicht enthalten. Sie sollten die Azure-Erweiterung für SAP nach dem Bereitstellen der neuen Instanz des verallgemeinerten Betriebssystemimages installieren.

Wenn Ihr verallgemeinertes, mit Sysprep bearbeitetes Betriebssystemimage jedoch bereits die Azure-Erweiterung für SAP enthält, können Sie die Erweiterung mit folgender Problemumgehung auf der neu bereitgestellten VM-Instanz neu konfigurieren:

* Löschen Sie auf der neu bereitgestellten VM-Instanz den Inhalt der folgenden Ordner:  
  C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\\<version\>\RuntimeSettings C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\\<version\>\Status

* Führen Sie die Schritte im Kapitel [Konfigurieren der Azure-Erweiterung für SAP][deployment-guide-4.5] in diesem Handbuch aus, um die Erweiterung erneut zu installieren.

#### <a name="fix-internet-connection"></a><a name="e92bc57d-80d9-4a2b-a2f4-16713a22ad89"></a>Korrigieren der Internetverbindung

Der virtuelle Microsoft Azure-Computer, der die Azure-Erweiterung für SAP ausführt, benötigt Zugang zum Internet. Falls die Azure-VM Teil eines virtuellen Azure-Netzwerks oder einer lokalen Domäne ist, stellen Sie sicher, dass die entsprechenden Proxyeinstellungen festgelegt sind. Diese Einstellung müssen auch für den Internetzugang des lokalen Systemkontos gültig sein. Befolgen Sie das Kapitel [Konfigurieren des Proxy][deployment-guide-configure-proxy] in diesem Handbuch.

Wenn Sie außerdem eine statische IP-Adresse für Ihren virtuellen Azure-Computer festlegen müssen, legen Sie sie nicht manuell innerhalb der Azure-VM fest, sondern mit [Azure PowerShell](../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md), [Azure CLI](../../../virtual-network/virtual-networks-static-private-ip-arm-cli.md) oder [Azure-Portal](../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md). Die statische IP-Adresse wird über den Azure-DHCP-Dienst weitergegeben.

Manuelles Festlegen einer statischen IP-Adresse auf dem virtuellen Azure-Computer wird nicht unterstützt und kann zu Problemen mit der Azure-Erweiterung für SAP führen.
