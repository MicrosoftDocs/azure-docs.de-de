---
title: include file
description: include file
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: include
ms.date: 09/03/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 3a8a7be6f437687a4de31ce8e0ac62588f64e2eb
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96016901"
---
**Konfigurations-/Prozessserveranforderungen für die Replikation physischer Server**

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
Gruppenrichtlinien | Aktivieren Sie die folgenden Gruppenrichtlinien nicht: <br> - Zugriff auf Eingabeaufforderung verhindern <br> - Zugriff auf Programme zum Bearbeiten der Registrierung verhindern <br> - Vertrauenslogik für Dateianlagen <br> - Skriptausführung aktivieren <br> [Weitere Informationen](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | - Keine bereits vorhandene Standardwebsite <br> - Keine bereits vorhandene Website/Anwendung sollte an Port 443 lauschen <br>- Die [anonyme Authentifizierung](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) ist aktiviert. <br> - Einstellung [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) aktiviert
Art der IP-Adresse | statischen 
| 
**ZUGRIFFSEINSTELLUNGEN** | 
MYSQL | MySQL sollte auf dem Konfigurationsserver installiert sein. Sie können es manuell installieren oder bei der Bereitstellung durch Site Recovery installieren lassen. Prüfen Sie, ob der Computer http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi erreichen kann, damit Site Recovery MySQL installieren kann.
URLs | Der Konfigurationsserver benötigt Zugriff auf die folgenden URLs (direkt oder über einen Proxy):<br/><br/> Azure AD: `login.microsoftonline.com`, `login.microsoftonline.us`, `*.accesscontrol.windows.net`<br/><br/> Übertragung der Replikationsdaten: `*.backup.windowsazure.com`, `*.backup.windowsazure.us`<br/><br/> Replikationsverwaltung: `*.hypervrecoverymanager.windowsazure.com`, `*.hypervrecoverymanager.windowsazure.us`, `https://management.azure.com`, `*.services.visualstudio.com`<br/><br/> Speicherzugriff: `*.blob.core.windows.net`, `*.blob.core.usgovcloudapi.net`<br/><br/> Zeitsynchronisierung: `time.nist.gov`; `time.windows.com`<br/><br/> Telemetrie (optional): `dc.services.visualstudio.com`
Firewall | Die auf IP-Adressen basierenden Firewallregeln sollten die Kommunikation mit den Azure-URLs ermöglichen. Um die IP-Bereiche zu vereinfachen und einzuschränken, wird der Einsatz der URL-Filterung empfohlen.<br/><br/>**Für kommerzielle IP-Adressen:**<br/><br/>– Lassen Sie die [Azure Datacenter-IP-Bereiche](https://www.microsoft.com/download/confirmation.aspx?id=41653) und den HTTPS-Port (443) zu.<br/><br/> – Lassen Sie die IP-Adressbereiche für die Region „USA, Westen“ zu (wird für Zugriffsteuerung und Identitätsverwaltung verwendet).<br/><br/> – Lassen Sie die IP-Adressbereiche für die Azure-Region Ihres Abonnements zu, um die URLs für Azure Active Directory, Sicherung, Replikation und Speicher zu unterstützen.<br/><br/> **Für IP-Adressen von Behörden:**<br/><br/> – Lassen Sie die Azure Government Datacenter-IP-Bereiche und den HTTPS-Port (443) zu.<br/><br/> – Lassen Sie die IP-Adressbereiche für alle US Government-Bereiche (Virginia, Texas, Arizona und Iowa) zu, um die URLs für Azure Active Directory, Sicherung, Replikation und Speicher zu unterstützen.
Ports | Lassen Sie Port 443 (Steuerkanalorchestrierung) zu.<br/><br/> Lassen Sie Port 9443 (Datentransport) zu. 


**Größenanforderungen an den Konfigurations-/Prozessserver**

**CPU** | **Memory** | **Cachedatenträger** | **Datenänderungsrate** | **Replizierte Computer**
--- | --- | --- | --- | ---
8 vCPUs<br/><br/> 2 Sockets * 4 Kerne \@ 2,5GHz | 16 GB | 300 GB | 500 GB oder weniger | < 100 Computer
12 vCPUs<br/><br/> 2 Sockets * 6 Kerne \@ 2,5GHz | 18 GB | 600 GB | 500 GB bis 1 TB | 100 bis 150 Computer
16 vCPUs<br/><br/> 2 Sockets * 8 Kerne \@ 2,5GHz | 32 GB | 1 TB | 1–2 TB | 150–200 Computer