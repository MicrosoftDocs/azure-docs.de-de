---
title: Architektur der Azure Migrate-Appliance
description: Bietet einen Überblick über die Azure Migrate-Appliance, die bei der Serverbewertung und -migration verwendet wird.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: 42d4a722be25eec4b3e27012350346018fdba0f3
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754112"
---
# <a name="azure-migrate-appliance-architecture"></a>Architektur der Azure Migrate-Appliance

In diesem Artikel werden die Architektur und Prozesse der Azure Migrate-Appliance beschrieben. Die Azure Migrate-Appliance ist eine einfache Appliance, die lokal bereitgestellt wird, um VMs und physische Server für die Migration zu Azure zu ermitteln. 

## <a name="deployment-scenarios"></a>Bereitstellungsszenarien

Die Azure Migrate-Appliance wird in den folgenden Szenarien verwendet.

**Szenario** | **Tool** | **Verwendung** 
--- | --- | ---
**Bewertung von VMware-VMs** | Azure Migrate-Serverbewertung | Ermitteln von VMware-VMs<br/><br/> Ermitteln der Apps und Abhängigkeiten der Computer<br/><br/> Erfassen von Computer- und Leistungsmetadaten und Senden der Daten an Azure
**VMware-VM-Migration (ohne Agents)** | Azure Migrate-Servermigration | Ermitteln von virtuellen VMware-Computern<br/><br/>  Replizieren von VMware-VMs per [Migration ohne Agent](server-migrate-overview.md)
**Bewertung von Hyper-V-VMs** | Azure Migrate-Serverbewertung | Ermitteln von Hyper-V-VMs<br/><br/> Erfassen von Computer- und Leistungsmetadaten und Senden der Daten an Azure
**Physischer Computer** |  Azure Migrate-Serverbewertung |  Ermitteln physischer Server<br/><br/> Erfassen von Computer- und Leistungsmetadaten und Senden der Daten an Azure

## <a name="appliance-components"></a>Appliancekomponenten

Die Appliance verfügt über eine Reihe von Komponenten.

- **Verwaltungs-App**: Dies ist eine Web-App für Benutzereingaben während der Appliance-Bereitstellung. Wird beim Bewerten von Computern für die Migration zu Azure verwendet.
- **Ermittlungs-Agent**: Der Agent erfasst Computerkonfigurationsdaten. Wird beim Bewerten von Computern für die Migration zu Azure verwendet. 
- **Collector-Agent:** Der Agent sammelt Leistungsdaten. Wird beim Bewerten von Computern für die Migration zu Azure verwendet.
- **DRA-Agent**: Orchestriert die VM-Replikation und koordiniert die Kommunikation zwischen replizierten Computern und Azure. Wird nur bei der Replikation von VMware-VMs in Azure mithilfe der Migration ohne Agent verwendet.
- **Gateway**: Sendet replizierte Daten an Azure. Wird nur bei der Replikation von VMware-VMs in Azure mithilfe der Migration ohne Agent verwendet.
- **Dienst für automatische Aktualisierungen**: Aktualisiert Appliancekomponenten (wird alle 24 Stunden ausgeführt).



## <a name="appliance-deployment"></a>Bereitstellung einer Appliance

- Die Azure Migrate-Appliance kann mithilfe einer Vorlage für [Hyper-V](how-to-set-up-appliance-hyper-v.md) oder [VMware](how-to-set-up-appliance-vmware.md) oder mithilfe eines PowerShell-Skript-Installationsprogramms für [VMware-/Hyper-V](deploy-appliance-script.md) und für [physische Server](how-to-set-up-appliance-physical.md) eingerichtet werden. 
- Die Unterstützungsanforderungen und Bereitstellungsvoraussetzungen der Appliance sind in der [Applianceunterstützungsmatrix](migrate-appliance.md) zusammengefasst.


## <a name="appliance-registration"></a>Applianceregistrierung

Beim Einrichten der Appliance registrieren Sie die Appliance bei Azure Migrate, und die in der Tabelle zusammengefassten Aktionen werden ausgeführt.

**Aktion** | **Details** | **Berechtigungen**
--- | --- | ---
**Registrieren von Ressourcenanbietern** | Diese Ressourcenanbieter werden in dem Abonnement registriert, das Sie während der Applianceeinrichtung auswählen: Microsoft.OffAzure, Microsoft.Migrate und Microsoft.KeyVault.<br/><br/> Durch Registrieren eines Ressourcenanbieters wird Ihr Abonnement für die Verwendung mit dem Ressourcenanbieter konfiguriert. | Sie müssen über die Rolle „Mitwirkender“ oder „Besitzer“ für das Abonnement verfügen, um die Ressourcenanbieter zu registrieren.
**Erstellen einer Azure AD-App für die Kommunikation** | Azure Migrate erstellt eine Azure AD-App (Azure Active Directory) für die Kommunikation (Authentifizierung und Autorisierung) zwischen den auf der Appliance ausgeführten Agents und den entsprechenden Diensten in Azure.<br/><br/> Diese App verfügt weder über Berechtigungen zum Durchführen von Azure Resource Manager-Aufrufen noch über Azure-RBAC-Zugriff auf Ressourcen. | Sie benötigen [diese Berechtigungen](./tutorial-discover-vmware.md#prepare-an-azure-user-account), damit Azure Migrate die App erstellen kann.
**Erstellen einer Azure AD-App für den Schlüsseltresor** | Diese App wird nur für die Migration von VMware-VMs zu Azure ohne Agent erstellt.<br/><br/> Sie wird ausschließlich für den Zugriff auf den im Abonnement des Benutzers erstellten Schlüsseltresor für die Migration ohne Agent verwendet.<br/><br/> Sie verfügt über Azure-RBAC-Zugriff auf den Azure-Schlüsseltresor (im Kundenmandanten erstellt), wenn die Ermittlung von der Appliance initiiert wird. | Sie benötigen [diese Berechtigungen](./tutorial-discover-vmware.md#prepare-an-azure-user-account), damit Azure Migrate die App erstellen kann.



## <a name="collected-data"></a>Gesammelte Daten

Die vom Client für alle Bereitstellungsszenarien gesammelten Daten werden in der [Applianceunterstützungsmatrix](migrate-appliance.md) zusammengefasst.

## <a name="discovery-and-collection-process"></a>Ermittlungs- und Sammlungsprozess

![Aufbau](./media/migrate-appliance-architecture/architecture1.png)

Die Appliance kommuniziert mit vCenter-Servern und Hyper-V-Hosts/Clustern über den folgenden Prozess.

1. **Start der Ermittlung**:
    - Wenn Sie die Ermittlung auf der Hyper-V-Appliance starten, kommuniziert sie mit den Hyper-V-Hosts über den WinRM-Port 5985 (HTTP).
    - Wenn Sie die Ermittlung auf der VMware-Appliance starten, kommuniziert sie standardmäßig mit dem vCenter-Server über TCP-Port 443. Wenn der vCenter-Server an einem anderen Port lauscht, können Sie ihn in der Appliance-Webanwendung konfigurieren.
2. **Sammeln von Metadaten und Leistungsdaten**:
    - Die Appliance verwendet eine Common Information Model (CIM)-Sitzung, um Hyper-V-VM-Daten vom Hyper-V-Host an Port 5985 zu sammeln.
    - Die Appliance kommuniziert standardmäßig über Port 443, um VMware-VM-Daten vom vCenter Server zu sammeln.
3. **Senden von Daten**: Die Appliance sendet die gesammelten Daten über SSL-Port 443 an die Azure Migrate-Serverbewertung und die Azure Migrate-Servermigration. Die Verbindung der Appliance mit Azure kann über das Internet oder über ExpressRoute (Microsoft-Peering erforderlich) erfolgen.
    - Bei Leistungsdaten sammelt die Appliance Nutzungsdaten in Echtzeit.
        - Die Leistungsdaten werden für VMware alle 20 Sekunden und für Hyper-V alle 30 Sekunden für jede Leistungsmetrik erfasst.
        - Die gesammelten Daten werden zu einem einzigen 10-Minuten-Datenpunkt zusammengeführt.
        - Der Spitzenauslastungswert wird aus allen 20/30-Sekunden-Datenpunkten ausgewählt und zur Berechnung der Bewertung an Azure gesendet.
        - Basierend auf dem in den Bewertungseigenschaften angegebenen Perzentilwert (50./90./95./99.) werden die Zehn-Minuten-Punkte in aufsteigender Reihenfolge sortiert, und der entsprechende Perzentilwert wird zur Berechnung der Bewertung verwendet
    - Bei der Servermigration beginnt die Appliance mit der Ermittlung von VM-Daten und repliziert diese nach Azure.
4. **Bewerten und Migrieren**: Sie können nun mit der Azure Migrate-Serverbewertung aus den von der Appliance gesammelten Metadaten Bewertungen erstellen. Darüber hinaus können Sie auch mit der Migration von VMware-VMs mit der Azure Migrate-Servermigration beginnen, um die VM-Replikation ohne Agent zu organisieren.

## <a name="appliance-upgrades"></a>Appliance-Upgrades

Die Appliance wird aktualisiert, wenn die auf der Appliance ausgeführten Azure Migrate-Agents aktualisiert werden. Dies geschieht automatisch, da auf der Appliance standardmäßig die automatische Aktualisierung aktiviert ist. Sie können diese Standardeinstellung ändern, um die Agents manuell zu aktualisieren.

Sie deaktivieren die automatische Aktualisierung in der Registrierung, indem Sie in „HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance“ den Schlüssel „AutoUpdate“ auf 0 (DWORD) festlegen.


## <a name="next-steps"></a>Nächste Schritte

[Überprüfen](migrate-appliance.md) Sie die Applianceunterstützungsmatrix.