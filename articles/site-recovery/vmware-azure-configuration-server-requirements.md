---
title: Anforderungen des Konfigurationsservers für die VMware-Notfallwiederherstellung in Azure mit Azure Site Recovery | Microsoft-Dokumentation
description: In diesem Artikel werden die Unterstützung und die Anforderungen für das Bereitstellen des Konfigurationsservers für die VMware-Notfallwiederherstellung in Azure mit Azure Site Recovery beschrieben.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 94f410b7bd3b7c2eb3d7d6a9316323092010338e
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418333"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Anforderungen des Konfigurationsservers für die VMware-Notfallwiederherstellung in Azure

Sie stellen einen lokalen Konfigurationsserver bereit, wenn Sie [Azure Site Recovery](site-recovery-overview.md) für die Notfallwiederherstellung von VMware-VMs und physischen Servern in Azure verwenden.

- Der Konfigurationsserver koordiniert die Kommunikation zwischen der lokalen VMware-Umgebung und Azure. Darüber hinaus verwaltet er die Datenreplikation.
- [Erfahren Sie mehr](vmware-azure-architecture.md) zu den Komponenten und Prozessen von Konfigurationsservern.

## <a name="configuration-server-deployment"></a>Bereitstellung des Konfigurationsservers

Für die Notfallwiederherstellung von VMware-VMs in Azure stellen Sie den Konfigurationsserver als VMware-VM bereit.

- Site Recovery stellt eine OVA-Vorlage bereit, die Sie aus dem Azure-Portal herunterladen und in vCenter Server importieren, um die Konfigurationsserver-VM einzurichten.
- Wenn Sie den Konfigurationsserver mit der OVA-Vorlage bereitstellen, erfüllt die VM automatisch die Anforderungen, die in diesem Artikel aufgeführt sind.
- Es wird dringend empfohlen, den Konfigurationsserver mit der OVA-Vorlage einzurichten. Aber wenn Sie die Notfallwiederherstellung für VMware-VMs einrichten und die OVA-Vorlage nicht verwenden können, können Sie den Konfigurationsserver anhand [dieser Anleitung](physical-azure-set-up-source.md) bereitstellen.
- Falls Sie den Konfigurationsserver für die Notfallwiederherstellung von lokalen physischen Computern in Azure bereitstellen, hilft Ihnen die Anleitung in [diesem Artikel](physical-azure-set-up-source.md) weiter. 


## <a name="hardware-requirements"></a>Hardwareanforderungen

**Komponente** | **Anforderung** 
--- | ---
CPU-Kerne | 8 
RAM | 16 GB
Anzahl der Datenträger | 3, einschließlich Betriebssystem-Datenträger, Prozessservercache-Datenträger und Aufbewahrungslaufwerk für Failback 
Freier Speicherplatz (Prozessservercache) | 600 GB
Freier Speicherplatz (Aufbewahrungslaufwerk) | 600 GB

## <a name="software-requirements"></a>Softwareanforderungen

**Komponente** | **Anforderung** 
--- | ---
Betriebssystem | Windows Server 2012 R2 <br> Windows Server 2016
Gebietsschema des Betriebssystems | Englisch (en-us)
Windows Server-Rollen | Aktivieren Sie die folgenden Rollen nicht: <br> - Active Directory Domain Services <br>- Internetinformationsdienste <br> - Hyper-V 
Gruppenrichtlinien | Aktivieren Sie die folgenden Gruppenrichtlinien nicht: <br> - Zugriff auf Eingabeaufforderung verhindern <br> - Zugriff auf Programme zum Bearbeiten der Registrierung verhindern <br> - Vertrauenslogik für Dateianlagen <br> - Skriptausführung aktivieren <br> [Weitere Informationen](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Keine bereits vorhandene Standardwebsite <br> - Keine bereits vorhandene Website/Anwendung sollte an Port 443 lauschen <br>- Die [anonyme Authentifizierung](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) ist aktiviert. <br> - Aktivieren der Einstellung [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 

## <a name="network-requirements"></a>Netzwerkanforderungen

**Komponente** | **Anforderung** 
--- | --- 
Art der IP-Adresse | statischen 
Zugriff auf das Internet | Der Server benötigt Zugriff auf diese URLs (direkt oder über einen Proxy): <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com  <br> - https:\//management.azure.com <br> - *.services.visualstudio.com <br> - time.nist.gov <br> - time.windows.com <br> OVF benötigt auch Zugriff auf die folgenden URLs: <br> - https:\//login.microsoftonline.com <br> - https:\//secure.aadcdn.microsoftonline-p.com <br> - https:\//login.live.com  <br> - https:\//auth.gfx.ms <br> - https:\//graph.windows.net <br> - https:\//login.windows.net <br> - https:\//www.live.com <br> - https:\//www.microsoft.com <br> - https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi 
Ports | 443 (Steuerkanalorchestrierung)<br>9443 (Datentransport) 
NIC-Typ | VMXNET3 (wenn der Konfigurationsserver eine VMware-VM ist)

## <a name="required-software"></a>Erforderliche Software

**Komponente** | **Anforderung** 
--- | ---
VMware vSphere PowerCLI | [PowerCLI Version 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) sollte installiert werden, wenn der Konfigurationsserver auf einer VMware-VM ausgeführt wird.
MYSQL | MySQL sollte installiert sein. Sie können es manuell installieren oder durch Site Recovery installieren lassen.

## <a name="sizing-and-capacity-requirements"></a>Anforderungen an Größenanpassung und Kapazität

Die folgende Tabelle enthält die Kapazitätsanforderungen für den Konfigurationsserver. Wenn Sie mehrere VMware-VMs replizieren, sollten Sie sich den Artikel [Planen der Kapazität und Skalierung der VMware-Notfallwiederherstellung für Azure](site-recovery-plan-capacity-vmware.md) durchlesen und das Tool [Azure Site Recovery-Bereitstellungsplaner](site-recovery-deployment-planner.md) für die VMWare-Replikation ausführen. 

**Komponente** | **Anforderung** 
--- | ---

| **CPU** | **Memory** | **Cachedatenträger** | **Datenänderungsrate** | **Replizierte Computer** |
| --- | --- | --- | --- | --- |
| 8 vCPUs<br/><br/> 2 Sockets * 4 Kerne \@ 2,5GHz | 16 GB | 300 GB | 500 GB oder weniger | Weniger als 100 Computer |
| 12 vCPUs<br/><br/> 2 Sockets * 6 Kerne \@ 2,5GHz | 18 GB | 600 GB | 500 GB bis 1 TB | 100 bis 150 Computer |
| 16 vCPUs<br/><br/> 2 Sockets * 8 Kerne \@ 2,5GHz | 32 GB | 1 TB | 1–2 TB | 150 - 200 Computer | 



## <a name="next-steps"></a>Nächste Schritte
[Einrichten der Notfallwiederherstellung in Azure für lokale VMware-VMs](vmware-azure-tutorial.md)
