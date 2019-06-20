---
title: include file
description: include file
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 06/10/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 371cbcc50b574f95e8d9ba4efe79058b2b25a8ba
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177798"
---
**Anforderungen an den Konfigurations-/Prozessserver**

**Komponente** | **Anforderung** 
--- | ---
**HARDWAREEINSTELLUNGEN** | 
CPU-Kerne | 8 
RAM | 16 GB
Anzahl der Datenträger | 3, einschließlich Betriebssystem-Datenträger, Prozessservercache-Datenträger und Aufbewahrungslaufwerk für Failback 
Freier Speicherplatz (Prozessservercache) | 600 GB
Freier Speicherplatz (Aufbewahrungslaufwerk) | 600 GB
 | 
**SOFTWAREEINSTELLUNGEN** | 
Betriebssystem | Windows Server 2012 R2 <br> Windows Server 2016
Gebietsschema des Betriebssystems | Englisch (en-us)
Windows Server-Rollen | Aktivieren Sie die folgenden Rollen nicht: <br> - Active Directory Domain Services <br>- Internetinformationsdienste <br> - Hyper-V 
Gruppenrichtlinien | Aktivieren Sie die folgenden Gruppenrichtlinien nicht: <br> - Zugriff auf Eingabeaufforderung verhindern <br> - Zugriff auf Programme zum Bearbeiten der Registrierung verhindern <br> - Vertrauenslogik für Dateianlagen <br> - Skriptausführung aktivieren <br> [Weitere Informationen](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Keine bereits vorhandene Standardwebsite <br> - Keine bereits vorhandene Website/Anwendung sollte an Port 443 lauschen <br>- Die [anonyme Authentifizierung](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) ist aktiviert. <br> - Aktivieren der Einstellung [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 
| 
**NETZWERKEINSTELLUNGEN** | 
Art der IP-Adresse | statischen 
Ports | 443 (Steuerkanalorchestrierung)<br>9443 (Datentransport) 
NIC-Typ | VMXNET3 (wenn der Konfigurationsserver eine VMware-VM ist)
 |
**Internetzugriff** (Der Server benötigt Zugriff auf diese URLs – direkt oder über einen Proxy):|
\*.backup.windowsazure.com | Wird für die Übertragung und Koordinierung replizierter Daten verwendet
\*.store.core.windows.net | Wird für die Übertragung und Koordinierung replizierter Daten verwendet
\*.blob.core.windows.net | Wird für den Zugriff auf das Speicherkonto verwendet, in dem replizierte Daten gespeichert werden
\*.hypervrecoverymanager.windowsazure.com | Für Vorgänge und Koordinierung der Replikationsverwaltung verwendet
https:\//management.azure.com | Für Vorgänge und Koordinierung der Replikationsverwaltung verwendet 
*.services.visualstudio.com | Wird für Telemetriezwecke (optional) verwendet
time.nist.gov | Wird zum Überprüfen der Zeitsynchronisierung zwischen Systemzeit und der globalen Zeit verwendet.
time.windows.com | Wird zum Überprüfen der Zeitsynchronisierung zwischen Systemzeit und der globalen Zeit verwendet.
| <ul> <li> https:\//login.microsoftonline.com </li><li> https:\//secure.aadcdn.microsoftonline-p.com </li><li> https:\//login.live.com </li><li> https:\//graph.windows.net </li><li> https:\//login.windows.net </li><li> https:\//www.live.com </li><li> https:\//www.microsoft.com </li></ul> | Beim OVF-Setup wird Zugriff auf diese URLs benötigt. Die URLs werden für die Zugriffssteuerung und die Identitätsverwaltung durch Azure Active Directory verwendet.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | So schließen Sie den MySQL-Download ab
|
**ZU INSTALLIERENDE SOFTWARE** | 
VMware vSphere PowerCLI | [PowerCLI Version 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) sollte installiert werden, wenn der Konfigurationsserver auf einer VMware-VM ausgeführt wird.
MYSQL | MySQL sollte installiert sein. Sie können es manuell installieren oder durch Site Recovery installieren lassen. (Weitere Informationen finden Sie unter [Konfigurieren von Einstellungen](../articles/site-recovery/vmware-azure-deploy-configuration-server.md#configure-settings)).

**Größenanforderungen an den Konfigurations-/Prozessserver**

**CPU** | **Memory** | **Cachedatenträger** | **Datenänderungsrate** | **Replizierte Computer**
--- | --- | --- | --- | ---
8 vCPUs<br/><br/> 2 Sockets * 4 Kerne \@ 2,5GHz | 16 GB | 300 GB | 500 GB oder weniger | < 100 Computer
12 vCPUs<br/><br/> 2 Sockets * 6 Kerne \@ 2,5GHz | 18 GB | 600 GB | 500 GB bis 1 TB | 100 bis 150 Computer
16 vCPUs<br/><br/> 2 Sockets * 8 Kerne \@ 2,5GHz | 32 GB | 1 TB | 1–2 TB | 150–200 Computer

