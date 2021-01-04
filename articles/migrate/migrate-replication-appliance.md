---
title: Azure Migrate-Replikationsappliance
description: Hier erhalten Sie Informationen zur Azure Migrate-Replikationsappliance für die Agent-basierte VMware-Migration.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: ec277bcc3e361561f54e72c54526d65487c113b4
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754095"
---
# <a name="replication-appliance"></a>Replikationsappliance

In diesem Artikel wird die Replikationsappliance beschrieben, die vom Tool [Azure Migrate- Servermigration](migrate-services-overview.md#azure-migrate-server-migration-tool) bei der Migration von virtuellen VMware-Computern, physischen Computern und privaten/öffentlichen virtuellen Cloudcomputern zu Azure mithilfe der Agent-basierten Migration verwendet wird. 


## <a name="overview"></a>Übersicht

Die Replikationsappliance wird bereitgestellt, wenn Sie die Agent-basierte Migration von virtuellen VMware-Computern oder physischen Servern einrichten. Sie wird als einzelner lokaler Computer bereitgestellt, entweder als virtueller VMware-Computer oder als physischer Server. Sie führt folgende Komponenten aus:

- **Replikationsappliance**: Die Replikationsappliance koordiniert die Kommunikation und verwaltet die Datenreplikation für lokale VMware-VMs und physische Server, die nach Azure repliziert werden.
- **Prozessserver** Der Prozessserver, der standardmäßig auf der Replikationsappliance installiert ist, und folgende Schritte ausführt:
    - **Replikationsgateway**: Fungiert als Replikationsgateway. Empfängt Replikationsdaten von für die Replikation aktivierten Computern. Optimiert Replikationsdaten durch Zwischenspeicherung, Komprimierung und Verschlüsselung und sendet sie an Azure.
    - **Agent-Installer**: Führt die Pushinstallation des Mobility Service aus. Dieser Dienst muss auf jedem lokalen Computer, den Sie für die Migration replizieren möchten, installiert sein und ausgeführt werden.

## <a name="appliance-deployment"></a>Bereitstellung einer Appliance

**Verwendung** | **Details**
--- |  ---
**Agent-basierte Migration virtueller VMware-Computer** | Sie laden die OVA-Vorlage aus dem Azure Migrate-Hub herunter und importieren sie in vCenter Server, um die Appliance-VM zu erstellen.
**Agent-basierte Migration physischer Computer** | Falls Sie keine VMware-Infrastruktur besitzen oder keinen virtuellen VMware-Computer mithilfe einer OVA-Vorlage erstellen können, laden Sie ein Softwareinstallationsprogramm vom Azure Migrate-Hub herunter und führen es zum Einrichten des Appliancecomputers aus.

> [!NOTE]
> Verwenden Sie bei der Bereitstellung in Azure Government die Installationsdatei, um die Replikationsappliance bereitzustellen.

## <a name="appliance-requirements"></a>Appliance-Anforderungen

Wenn Sie die Replikationsappliance mit der OVA-Vorlage im Azure Migrate-Hub einrichten, wird die Appliance unter Windows Server 2016 ausgeführt und erfüllt die Supportanforderungen. Stellen Sie bei der manuellen Einrichtung der Replikationsappliance auf einem physischen Server sicher, dass sie die Anforderungen erfüllt.

**Komponente** | **Anforderung**
--- | ---
 | **VMware-VM-Appliance**
PowerCLI | [PowerCLI Version 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) sollte installiert werden, wenn die Replikationsappliance auf einer VMware-VM ausgeführt wird.
NIC-Typ | VMXNET3 (wenn es sich bei der Appliance um eine VMware-VM handelt)
 | **Hardwareeinstellungen**
CPU-Kerne | 8
RAM | 16 GB
Anzahl der Datenträger | Drei: Betriebssystem-Datenträger, Prozessservercache-Datenträger, Aufbewahrungslaufwerk
Freier Speicherplatz (Cache) | 600 GB
Freier Speicherplatz (Aufbewahrungslaufwerk) | 600 GB
**Softwareeinstellungen** |
Betriebssystem | Windows Server 2016 oder Windows Server 2012 R2
Lizenz | Die Appliance ist mit einer Windows Server 2016-Evaluierungslizenz ausgestattet, die 180 Tage lang gültig ist.<br/><br/> Wenn der Evaluierungszeitraum fast abgelaufen ist, empfiehlt es sich, eine neue Appliance herunterzuladen und bereitzustellen oder die Betriebssystemlizenz der Appliance-VM zu aktivieren.
Gebietsschema des Betriebssystems | Englisch (en-us)
TLS | TLS 1.2 muss aktiviert sein.
.NET Framework | NET Framework 4.6 oder höher sollte auf dem Computer installiert sein (mit aktivierter starker Kryptografie).
MySQL | MySQL sollte auf der Appliance installiert sein.<br/> MySQL sollte installiert sein. Sie können MySQL manuell installieren oder bei der Bereitstellung der Appliance durch Site Recovery installieren lassen.
Andere Apps | Führen Sie keine anderen Apps auf der Replikationsappliance aus.
Windows Server-Rollen | Aktivieren Sie die folgenden Rollen nicht: <br> - Active Directory Domain Services <br>- Internetinformationsdienste <br> - Hyper-V
Gruppenrichtlinien | Aktivieren Sie die folgenden Gruppenrichtlinien nicht: <br> - Zugriff auf Eingabeaufforderung verhindern <br> - Zugriff auf Programme zum Bearbeiten der Registrierung verhindern <br> - Vertrauenslogik für Dateianlagen <br> - Skriptausführung aktivieren <br> [Weitere Informationen](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | - Keine bereits vorhandene Standardwebsite <br> - Keine bereits vorhandene Website/Anwendung sollte an Port 443 lauschen <br>- Die [anonyme Authentifizierung](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) ist aktiviert. <br> - Aktivieren der Einstellung [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10))
**Netzwerkeinstellungen** |
Art der IP-Adresse | statischen
Ports | 443 (Steuerkanalorchestrierung)<br>9443 (Datentransport)
NIC-Typ | VMXNET3

## <a name="mysql-installation"></a>MySQL-Installation 

MySQL muss auf dem Replikationsappliancecomputer installiert sein. Die Komponente kann mit einer der folgenden Methoden installiert werden:

**Methode** | **Details**
--- | ---
Manuell herunterladen und installieren | Laden Sie die Anwendung MySQL manuell herunter, legen Sie sie im Ordner „C:\Temp\ASRSetup“ ab, und installieren Sie sie manuell.<br/> Bei der Einrichtung der Appliance wird MySQL als bereits installiert angezeigt.
Ohne Onlinedownload | Legen Sie das MySQL-Installationsprogramm im Ordner „C:\Temp\ASRSetup“ ab. Wenn Sie die Appliance installieren und zum Herunterladen und Installieren von MySQL klicken, verwendet das Setup das von Ihnen hinzugefügte Installationsprogramm.
Herunterladen und Installieren in Azure Migrate | Wenn Sie die Appliance installieren und nach MySQL gefragt werden, wählen Sie **Herunterladen und installieren** aus.

## <a name="url-access"></a>URL-Zugriff

Die Replikationsappliance benötigt Zugriff auf die folgenden URLs in der öffentlichen Azure-Cloud.

**URL** | **Details**
--- | ---
\*.backup.windowsazure.com | Wird für die Übertragung und Koordinierung replizierter Daten verwendet
\*.store.core.windows.net | Wird für die Übertragung und Koordinierung replizierter Daten verwendet
\*.blob.core.windows.net | Wird für den Zugriff auf das Speicherkonto verwendet, in dem replizierte Daten gespeichert werden
\*.hypervrecoverymanager.windowsazure.com | Für Vorgänge und Koordinierung der Replikationsverwaltung verwendet
https:\//management.azure.com | Für Vorgänge und Koordinierung der Replikationsverwaltung verwendet
*.services.visualstudio.com | Wird für Telemetriezwecke (optional) verwendet
time.windows.com | Wird zum Überprüfen der Zeitsynchronisierung zwischen Systemzeit und der globalen Zeit verwendet.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | Beim Einrichten der Appliance wird Zugriff auf diese URLs benötigt. Die URLs werden für die Zugriffssteuerung und die Identitätsverwaltung durch Azure Active Directory verwendet.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | So schließen Sie den MySQL-Download ab In einigen Regionen wird der Download möglicherweise an die CDN-URL umgeleitet. Stellen Sie in dem Fall sicher, dass die CDN-URL ebenfalls zulässig ist.


## <a name="azure-government-url-access"></a>Zugriff auf Azure Government-URLs

Die Replikationsappliance benötigt Zugriff auf die folgenden URLs in Azure Government.

**URL** | **Details**
--- | ---
\*.backup.windowsazure.us | Wird für die Übertragung und Koordinierung replizierter Daten verwendet
\*.store.core.windows.net | Wird für die Übertragung und Koordinierung replizierter Daten verwendet
\*.blob.core.windows.net | Wird für den Zugriff auf das Speicherkonto verwendet, in dem replizierte Daten gespeichert werden
\*.hypervrecoverymanager.windowsazure.us | Für Vorgänge und Koordinierung der Replikationsverwaltung verwendet
https:\//management.usgovcloudapi.net | Für Vorgänge und Koordinierung der Replikationsverwaltung verwendet
*.services.visualstudio.com | Wird für Telemetriezwecke (optional) verwendet
time.nist.gov | Wird zum Überprüfen der Zeitsynchronisierung zwischen Systemzeit und der globalen Zeit verwendet.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | Beim Einrichten der Appliance mit OVA wird Zugriff auf diese URLs benötigt. Sie werden für die Zugriffssteuerung und Identitätsverwaltung durch Azure Active Directory verwendet.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | So schließen Sie den MySQL-Download ab In einigen Regionen wird der Download möglicherweise an die CDN-URL umgeleitet. Stellen Sie in dem Fall sicher, dass die CDN-URL ebenfalls zulässig ist.

## <a name="port-access"></a>Portzugriff

**Device** | **Connection**
--- | ---
VMs | Der Mobilitätsdienst auf virtuellen Computern kommuniziert mit der lokalen Replikationsappliance (Konfigurationsserver) über den HTTPS-Port 443 für eingehenden Datenverkehr, um die Replikationsverwaltung auszuführen.<br/><br/> Virtuelle Computer senden Replikationsdaten an den Prozessserver (der auf dem Konfigurationsserver ausgeführt wird) über Port HTTPS 9443 für eingehenden Datenverkehr. Dieser Port kann geändert werden.
Replikationsappliance | Die Replikationsappliance orchestriert die Replikation mit Azure über Port HTTPS 443 für ausgehenden Datenverkehr.
Prozessserver | Der Prozessserver empfängt Replikationsdaten, optimiert und verschlüsselt sie und sendet sie über den ausgehenden Port 443 an Azure Storage.<br/> Der Prozessserver wird standardmäßig auf der Replikationsappliance ausgeführt.


## <a name="replication-process"></a>Replikationsprozess

1. Wenn Sie die Replikation für einen virtuellen Computer aktivieren, beginnt die erste Replikation in Azure Storage mithilfe der angegebenen Replikationsrichtlinie. 
2. Der Datenverkehr wird über das Internet auf öffentliche Endpunkte von Azure Storage repliziert. Das Replizieren von Datenverkehr über ein virtuelles privates Site-to-Site-Netzwerk (VPN) von einem lokalen Standort nach Azure wird nicht unterstützt.
3. Nach Abschluss der ersten Replikation beginnt die Deltareplikation. Nachverfolgte Änderungen für einen Computer werden protokolliert.
4. Die Kommunikation erfolgt folgendermaßen:
    - VMs kommunizieren mit dem Replikationsserver über den Port HTTPS 443 für eingehenden Datenverkehr, um die Replikationsverwaltung durchzuführen.
    - Die Replikationsappliance orchestriert die Replikation mit Azure über Port HTTPS 443 für ausgehenden Datenverkehr.
    - Virtuelle Computer senden Replikationsdaten an den Prozessserver (der auf der Replikationsappliance ausgeführt wird) über Port HTTPS 9443 für eingehenden Datenverkehr. Dieser Port kann geändert werden.
    - Der Prozessserver empfängt Replikationsdaten, optimiert und verschlüsselt sie und sendet sie über den ausgehenden Port 443 an Azure Storage.
5. Die Replikationsdatenprotokolle werden zunächst in einem Cachespeicherkonto in Azure gespeichert. Diese Protokolle werden dann verarbeitet, und die Daten werden auf einem verwalteten Azure-Datenträger gespeichert.

![Das Diagramm zeigt die Architektur des Replikationsprozesses.](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Appliance-Upgrades

Das Upgrade der Appliance erfolgt manuell vom Azure Migrate-Hub. Sie sollten immer die neueste Version ausführen.

1. Klicken Sie in „Azure Migrate“ > „Server“ > „Azure Migrate: Server Bewertung“, „Infrastrukturserver“ auf **Konfigurationsserver**.
2. Wenn eine neue Version der Replikationsappliance verfügbar ist, wird in **Konfigurationsserver** unter **Agentversion** ein Link angezeigt. 
3. Laden Sie das Installationsprogramm auf den Replikationsappliancecomputer herunter, und installieren Sie das Upgrade. Das Installationsprogramm erkennt die aktuell auf dem Computer ausgeführte Version.
 
## <a name="next-steps"></a>Nächste Schritte

- [Hier](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance) erfahren Sie, wie Sie die Replikationsappliance für die Agent-basierte Migration virtueller VMware-Computer einrichten.
- [Hier](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) erfahren Sie, wie Sie die Replikationsappliance für physische Server einrichten.
