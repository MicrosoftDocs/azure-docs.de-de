---
title: Azure-Sicherheitsbaseline für Batch
description: Die Batch-Sicherheitsbaseline enthält Schrittanleitungen und Ressourcen für die Implementierung der Sicherheitsempfehlungen, die im Vergleichstest für die Azure-Sicherheit angegeben sind.
author: msmbaldwin
ms.service: batch
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e7be42b2a6e9f2cdc1aa0258f218fea9fd963093
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532029"
---
# <a name="azure-security-baseline-for-batch"></a>Azure-Sicherheitsbaseline für Batch

Diese Sicherheitsbaseline wendet Empfehlungen des [Azure-Sicherheitsvergleichstests Version 1.0](../security/benchmarks/overview-v1.md) auf Batch an. Der Azure-Sicherheitsvergleichstest enthält Empfehlungen zum Schutz Ihrer Cloudlösungen in Azure.
Der Inhalt wird anhand der **Sicherheitskontrollen** gruppiert, die durch den Vergleichstest für die Azure-Sicherheit und die entsprechenden für Batch geltenden Empfehlungen definiert werden. Nicht auf Batch anwendbare **Kontrollen** wurden ausgeschlossen.

 
Die vollständige Zuordnung von Batch zum Vergleichstest für die Azure-Sicherheit finden Sie in der [vollständigen Zuordnungsdatei der Batch-Sicherheitsbaseline](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Schützen von Azure-Ressourcen in virtuellen Netzwerken

**Leitfaden**: Bereitstellen von Azure Batch-Pools im virtuellen Netzwerk. Um die sichere Kommunikation zwischen Computeknoten und VMs oder lokalen Netzwerken zu ermöglichen, können Sie den Pool in einem Subnetz eines virtuellen Azure-Netzwerks bereitstellen. Durch die Bereitstellung Ihres Pools innerhalb eines virtuellen Netzwerks erhalten Sie außerdem die Kontrolle über die Netzwerksicherheitsgruppe (NSG), die zur Sicherung der Netzwerkschnittstellen (NIC) der einzelnen Knoten sowie des Subnetzes verwendet wird. Konfigurieren Sie die NSG so, dass Datenverkehr von nur vertrauenswürdigen IP-Adressen/Speicherorten im Internet zugelassen wird.

Deaktivieren Sie ggf. den Zugriff auf das öffentliche Netzwerk mithilfe von Azure Private Link, um über einen privaten Endpunkt eine Verbindung mit dem Azure Batch-Konto herzustellen. Der Azure Private Link-Dienst ist geschützt und akzeptiert nur Verbindungen von authentifizierten und autorisierten privaten Endpunkten. Sie können die Konnektivität und Auffindbarkeit zusätzlich einschränken, indem Sie öffentlich verfügbar gemachte RDP-/SSH-Endpunkte für Computeknoten in einem Batch-Pool deaktivieren.

- [Erstellen eines Azure Batch-Pools in einem virtuellen Netzwerk](batch-virtual-network.md)

- [Erstellen eines Azure Batch-Kontos mit deaktiviertem Netzwerkzugriff](private-connectivity.md)

- [Erstellen eines privaten Endpunkts](../private-link/create-private-endpoint-portal.md)

- [Verweigern des Zugriffs auf RDP-/SSH-Datenverkehr](pool-endpoint-configuration.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von virtuellen Netzwerken, Subnetzen und Netzwerkschnittstellen

**Leitfaden**: Verwenden Sie Azure Security Center, und setzen Sie die Netzwerkschutzempfehlungen im Zusammenhang mit dem virtuellen Netzwerk bzw. der Netzwerksicherheitsgruppe (NSG) um, das oder die Ihrem Batch-Pool zugeordnet ist. Aktivieren Sie die Flussprotokolle für die NSG, die zum Schutz Ihres Batch-Pools verwendet wird, und senden Sie Protokolle an ein Azure Storage-Konto für die Überwachung des Datenverkehrs. Sie können auch NSG-Flussprotokolle an einen Azure Log Analytics-Arbeitsbereich senden und Azure Traffic Analytics verwenden, um Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud bereitzustellen. Einige Vorteile von Azure Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu erkennen, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

- [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Aktivieren und Verwenden von Traffic Analytics](../network-watcher/traffic-analytics.md)

- [Grundlegendes zu der von Azure Security Center bereitgestellten Netzwerksicherheit](../security-center/security-center-network-recommendations.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten schädlichen IP-Adressen

**Leitfaden**: Aktivieren Sie den Azure DDoS-Standardschutz (Distributed Denial-of-Service) im virtuellen Netzwerk, der Ihren Azure Batch-Pool vor DDoS-Angriffen schützt. Verwenden Sie die in Azure Security Center integrierte Threat Intelligence, um die Kommunikation mit bekannten schädlichen oder nicht genutzten IP-Adressen zu verweigern.

- [Konfigurieren von DDoS-Schutz](/azure/virtual-network/manage-ddos-protection)

- [Grundlegendes zur integrierten Threat Intelligence in Azure Security Center](/azure/security-center/security-center-alerts-service-layer)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets"></a>1.5: Aufzeichnen von Netzwerkpaketen

**Leitfaden**: Aktivieren Sie die Flussprotokolle für die Netzwerksicherheitsgruppe (NSG), die zum Schutz Ihres Azure Batch-Pools verwendet wird, und senden Sie Protokolle an ein Azure Storage-Konto für die Überwachung des Datenverkehrs.

- [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (Intrusion Detection/Intrusion Prevention Systems, IDS/IPS)

**Leitfaden**: Wenn dies für Kompatibilitätszwecke erforderlich ist, wählen Sie ein virtuelles Netzwerkgerät aus Azure Marketplace aus, das Angriffserkennungssysteme (IDS) und IPS-Funktionen (Eindringschutzsysteme) mit Nutzlastinspektionsfunktionen unterstützt.

Wenn Angriffserkennung und/oder -verhinderung auf der Grundlage der Nutzlastüberprüfung keine Anforderung ist, kann Azure Firewall mit Threat Intelligence verwendet werden. Das Filtern auf Basis von Threat Intelligence mit Azure Firewall kann vor Datenverkehr von und zu bekannten schädlichen IP-Adressen oder Domänen warnen und diesen verweigern. Die IP-Adressen und Domänen stammen aus dem Microsoft Threat Intelligence-Feed.

Stellen Sie Azure Firewall mit einer öffentlichen IP-Adresse im gleichen virtuellen Netzwerk wie Ihre Azure Batch Pool-Knoten bereit. Konfigurieren Sie NAT-Regeln (Network Address Translation, Netzwerkadressübersetzung) zwischen vertrauenswürdigen Speicherorten im Internet und den privaten IP-Adressen der einzelnen Poolknoten. Konfigurieren Sie in Azure Firewall unter Threat Intelligence „Warnen und verweigern“, um zu warnen und den Datenverkehr an bzw. von bekannten schädlichen IP-Adressen und Domänen zu blockieren. Die IP-Adressen und Domänen stammen aus dem Microsoft Threat Intelligence-Feed. Es sind nur Datensätze mit der höchsten Genauigkeit enthalten. 

- [Erstellen eines Azure Batch-Pools in einem virtuellen Netzwerk](batch-virtual-network.md)

- [Bereitstellen von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie Virtual Network-Diensttags, um Netzwerkzugriffssteuerungen in Netzwerksicherheitsgruppen oder Azure Firewalls zu definieren, die Ihren Azure Batch-Pools zugeordnet sind. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Indem Sie den Diensttagnamen (z. B. „ApiManagement“) im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

- [Grundlegendes zu Diensttags und Verwenden von Diensttags](../virtual-network/service-tags-overview.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie mit Azure Policy Standardsicherheitskonfigurationen für Netzwerkressourcen, die Ihren Azure Batch-Pools zugeordnet sind. Verwenden Sie Azure Policy-Aliase in den Namespaces „Microsoft.Batch“ und „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer Azure Batch-Pools zu erstellen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Verwenden Sie Tags für Netzwerkdienstgruppen (NSGs) und andere Ressourcen im Zusammenhang mit Netzwerksicherheit und Datenverkehrsfluss, die mit ihren Azure Batch-Pools verknüpft sind. Verwenden Sie für einzelne NSG-Regeln das Feld „Beschreibung“, um geschäftliche Anforderungen und/oder deren Dauer (usw.) für alle Regeln festzulegen, die Datenverkehr in ein bzw. aus einem Netzwerk zulassen.

Verwenden Sie eine der integrierten Azure Policy-Definitionen zum Tagging, z. B. „Tag und zugehörigen Wert erzwingen“, um sicherzustellen, dass alle Ressourcen mit Tags erstellt werden und Sie über vorhandene nicht markierte Ressourcen benachrichtigt werden.

Sie können Azure PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden, um Ressourcen basierend auf ihren Tags zu suchen oder Aktionen auszuführen.

- [Erstellen und Verwenden von Tags](/azure/azure-resource-manager/resource-group-using-tags)

- [Erstellen eines virtuellen Netzwerks](../virtual-network/quick-create-portal.md)

- [Erstellen einer Netzwerksicherheitsgruppe (NSG)](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen der Konfigurationen von Netzwerkressourcen und zum Erkennen von Änderungen bei Netzwerkressourcen, die sich auf Ihre Azure Batch-Pools beziehen. Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkressourcen ausgelöst werden.

- [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](/azure/azure-monitor/platform/activity-log-view) 

- [Erstellen von Warnungen in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Integrieren Sie das Azure Batch-Konto in Azure Monitor, um die von den Clustergeräten generierten Sicherheitsdaten zu aggregieren. Nutzen Sie benutzerdefinierte Abfragen, um Bedrohungen in der Umgebung zu erkennen und darauf zu reagieren.  Verwenden Sie für Azure Batch-Überwachung auf Ressourcenebene die Batch-APIs, um den Status von Ressourcen wie Aufträgen, Aufgaben, Knoten und Pools zu überwachen oder abzufragen.

- [Integrieren eines Azure Batch-Kontos in Azure Monitor](batch-diagnostics.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Überwachen Sie für Azure Batch-Überwachung auf Kontoebene jedes Batch-Konto mithilfe der Features von Azure Monitor. Azure Monitor erfasst Metriken und optional Diagnoseprotokolle für Ressourcen auf der Ebene eines Batch-Kontos. Hierzu zählen beispielsweise Pools, Aufträge und Aufgaben. Erfassen und nutzen Sie diese Daten manuell oder programmgesteuert, um Aktivitäten in Ihrem Batch-Konto zu überwachen und Probleme zu diagnostizieren.

Verwenden Sie für Azure Batch-Überwachung auf Ressourcenebene die Azure Batch-APIs, um den Status von Ressourcen wie Aufträgen, Aufgaben, Knoten und Pools zu überwachen oder abzufragen.

- [Konfigurieren von Azure Batch-Überwachung und -Protokollierung auf Kontoebene](monitoring-overview.md)

- [Grundlegendes zur Batch-Überwachung auf Ressourcenebene](monitoring-overview.md#batch-resource-monitoring)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

#### <a name="azure-policy-built-in-definitions"></a>Integrierte Azure Policy-Definitionen

[!INCLUDE [microsoft.batch-2-3](../../includes/policy/standards/asb/rp-controls/microsoft.batch-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Leitfaden**: Azure Monitor erfasst Metriken und Diagnoseprotokolle für Ressourcen in Ihrem Azure Batch-Konto. Sie können mithilfe verschiedener Methoden diese Daten sammeln und nutzen, um Ihr Azure Batch-Konto zu überwachen und Probleme zu diagnostizieren. Sie können auch Metrikwarnungen konfigurieren, um Benachrichtigungen zu erhalten, wenn eine Metrik einen angegebenen Wert erreicht.

Falls erforderlich, stellen Sie möglicherweise eine Verbindung mit Ihren einzelnen Poolknoten über Secure Shell (SSH) oder Remotedesktopprotokoll (RDP) her, um auf lokale Betriebssystemprotokolle zuzugreifen.

- [Erfassen von Diagnoseprotokollen aus Ihrem Azure Batch-Konto](batch-diagnostics.md#batch-diagnostics)

- [Herstellen einer Remoteverbindung mit Ihren Azure Batch-Poolknoten](/azure/batch/batch-api-basics#error-handling)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Integrieren eines Azure Batch-Kontos in Azure Monitor. Stellen Sie sicher, dass für den verwendeten Azure Log Analytics-Arbeitsbereich der Protokollaufbewahrungszeitraum gemäß den Compliancebestimmungen Ihrer Organisation festgelegt ist.

- [Konfigurieren von Azure Batch-Überwachung und -Protokollierung](monitoring-overview.md)

- [Konfigurieren des Aufbewahrungszeitraums des Azure Log Analytics-Arbeitsbereichs](../azure-monitor/platform/manage-cost-storage.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Erstellen Sie Azure Batch-Metrikwarnungen, die ausgelöst werden, wenn der Wert für eine bestimmte Metrik einen angegebenen Schwellenwert überschreitet.

- [Konfigurieren von Azure Batch-Metrikwarnungen](batch-diagnostics.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden**: Erstellen Sie Azure Batch-Metrikwarnungen, die ausgelöst werden, wenn der Wert für eine bestimmte Metrik einen angegebenen Schwellenwert überschreitet.

- [Konfigurieren von Azure Batch-Metrikwarnungen](batch-diagnostics.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Verwenden Sie Windows Defender für Ihre einzelnen Batch-Knoten im Fall von Windows-Betriebssystemen, oder stellen Sie eine eigene Antischadsoftwarelösung bereit, wenn Sie Linux verwenden.

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Implementieren Sie eine Drittanbieterlösung für die DNS-Protokollierung.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Leitfaden**: Konfigurieren Sie die Konsolenprotokollierung und PowerShell-Transkription manuell pro Knoten.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Verwalten Sie den Datensatz für das lokale Administratorkonto, das während der Bereitstellung des Azure Batch-Pools erstellt wird, sowie für alle anderen Konten, die Sie erstellen. Wenn außerdem Azure Active Directory-Integration verwendet wird, verfügt Azure AD über integrierte Rollen, die explizit zugewiesen werden müssen und daher abgefragt werden können. Verwenden Sie das Azure AD PowerShell-Modul, um Ad-hoc-Abfragen zum Ermitteln von Konten durchzuführen, die Mitglieder von Verwaltungsgruppen sind.

Außerdem können Sie Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center anwenden.

- [Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [Überwachen der Identität und des Zugriffs](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Wenn Sie einen Azure Batch-Pool bereitstellen, haben Sie die Möglichkeit, lokale Computerkonten zu erstellen. Es gibt keine Standardkennwörter, die geändert werden müssen. Sie können jedoch unterschiedliche Kennwörter für den SSH-Zugriff (Secure Shell) und RDP-Zugriff (Remotedesktopprotokoll) angeben. Nachdem Azure Batch Pool konfiguriert wurde, können Sie einen zufälligen Benutzer für einzelne Knoten innerhalb des Azure-Portals oder über die Azure Resource Manager-API generieren.

- [Hinzufügen eines Benutzers zu einem bestimmten Computeknoten](/rest/api/batchservice/computenode/adduser)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Integrieren Sie Authentifizierung für Azure Batch-Anwendungen mit Azure Active Directory. Erstellen Sie Richtlinien und Vorgänge für die Verwendung dedizierter Administratorkonten.

Außerdem können Sie Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center anwenden.

- [Authentifizieren von Batch-Anwendungen mit Azure Active Directory](batch-aad-auth.md)

- [Überwachen der Identität und des Zugriffs](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Integrieren Sie Authentifizierung für Azure Batch-Anwendungen mit Azure Active Directory. Aktivieren Sie mehrstufige Azure AD-Authentifizierung, und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center.

 

- [Planen einer Bereitstellung von Azure AD Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md)

- [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: Verwenden von sicheren, von Azure verwalteten Arbeitsstationen für Verwaltungsaufgaben

**Leitfaden**: Verwenden Sie PAWs (Privileged Access Workstations, Arbeitsstationen mit privilegiertem Zugriff) mit mehrstufiger Authentifizierung, die für die Anmeldung und Konfiguration von Azure Batch-Ressourcen konfiguriert sind.

- [Informationen zu Arbeitsstationen mit privilegiertem Zugriff](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Planen einer Bereitstellung von Azure AD Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Wenn Sie über integrierte Authentifizierung für Azure Batch-Anwendungen mit Azure Active Directory verfügen, verwenden Sie Azure Active Directory-Sicherheitsberichte für die Generierung von Protokollen und Warnungen, wenn verdächtige oder unsichere Aktivitäten in der Umgebung auftreten. Verwenden Sie Azure Security Center zum Überwachen von identitäts- und zugriffsbezogenen Aktivitäten.

- [Identifizieren von Azure AD-Benutzern, die aufgrund riskanter Aktivitäten gekennzeichnet wurden](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Überwachen der identitäts- und zugriffsbezogenen Aktivitäten von Benutzern in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Wenn Sie über integrierte Authentifizierung für Azure Batch-Anwendungen mit Azure Active Directory verfügen, können Sie bedingten Zugriff mit benannten Speicherorten verwenden, um den Zugriff nur für bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen.

- [Konfigurieren benannter Standorte in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory als zentrales Authentifizierungs- und Autorisierungssystem, und integrieren Sie Authentifizierung für Azure Batch-Anwendungen mit Azure AD. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs verwendet, und Anmeldeinformationen werden sicher gespeichert.

- [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory-domain-services/tutorial-create-instance.md)

- [Authentifizieren von Batch-Anwendungen mit Azure AD](batch-aad-auth.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure Active Directory enthält Protokolle zum Ermitteln von veralteten Konten. Außerdem können Sie zusätzlich Zugriffsüberprüfungen für Azure-Identitäten verwenden, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin Zugriff besitzen.

- [Verwenden von Zugriffsüberprüfungen für Azure-Identitäten](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Anmeldeinformationen

**Leitfaden**: Erstellen Sie Diagnoseeinstellungen für Azure Active Directory-Benutzerkonten, und senden Sie die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich. Konfigurieren Sie die gewünschten Warnungen im Log Analytics-Arbeitsbereich.

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Mit den Funktionen zur Risikoerkennung und zum Identitätsschutz von Azure Active Directory können Sie automatische Antworten auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten konfigurieren. Außerdem können Sie Daten zur weiteren Untersuchung in Azure Sentinel erfassen.

- [Anzeigen riskanter Azure AD-Anmeldungen](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien  

**Leitfaden**: Nicht verfügbar. Kunden-Lockbox wird für Azure Batch nicht unterstützt.
 
- [Unterstützte Dienste und Szenarios bei allgemeiner Verfügbarkeit](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-protection"></a>Datenschutz

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Schutz von Daten](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für die Nachverfolgung von Azure-Ressourcen, die vertrauliche Informationen speichern oder verarbeiten.

- [Erstellen und Verwenden von Tags](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie separate Abonnements und/oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Azure Batch-Pools sollten durch ein virtuelles Netzwerk/Subnetz getrennt, entsprechend gekennzeichnet und durch eine Netzwerksicherheitsgruppe (NSG) gesichert werden. Azure Batch-Daten sollten in einem gesicherten Azure Storage-Konto enthalten sein.

- [Erstellen eines Azure Batch-Pools in einem virtuellen Netzwerk](batch-virtual-network.md)

- [Schützen von Azure Storage-Konten](/azure/storage/common/storage-security-guide)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Bei Azure Storage-Konten, die Ihren Azure Batch-Pools zugeordnet sind, die vertrauliche Informationen enthalten, markieren Sie diese mithilfe von Tags als vertraulich, und schützen Sie sie mit bewährten Azure-Methoden.

Funktionen für Datenidentifizierung, -klassifizierung und -verlust sind für Azure Storage- oder Computeressourcen noch nicht verfügbar. Implementieren Sie eine Drittanbieterlösung, wenn dies für Compliancezwecke erforderlich ist.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

- [Schützen von Azure Storage-Konten](/azure/storage/common/storage-security-guide)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Verschlüsseln Sie alle vertraulichen Informationen während der Übertragung. Microsoft Azure Ressourcen handeln standardmäßig TLS 1.2 aus. Stellen Sie sicher, dass alle Clients, die sich mit Ihren Azure Batch-Pools oder Datenspeichern (Azure Storage-Konten) verbinden, TLS 1.2 oder höher aushandeln können.

Stellen Sie sicher, dass für den Zugriff auf das Speicherkonto mit den Azure Batch-Daten HTTPS erforderlich ist.

- [Grundlegendes zur Azure Storage-Kontoverschlüsselung während der Übertragung](../storage/blobs/security-recommendations.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Bei Azure Storage-Konten, die Ihren Azure Batch-Pools zugeordnet sind, die vertrauliche Informationen enthalten, markieren Sie diese mithilfe von Tags als vertraulich, und schützen Sie sie mit bewährten Azure-Methoden.

Funktionen für Datenidentifizierung, -klassifizierung und -verlust sind für Azure Storage- oder Computeressourcen noch nicht verfügbar. Implementieren Sie eine Drittanbieterlösung, wenn dies für Compliancezwecke erforderlich ist.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

- [Schützen von Azure Storage-Konten](/azure/storage/common/storage-security-guide)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: Verwenden der rollenbasierten Zugriffssteuerung zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Verwenden Sie rollenbasierte Zugriffssteuerung von Azure (Azure RBAC), um den Zugriff auf die Verwaltungsebene von Azure-Ressourcen (einschließlich Batch-Konto, Batch-Pools und Speicherkonten) zu steuern.

- [Grundlegendes zu Azure RBAC](../role-based-access-control/overview.md)

- [Konfigurieren von Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Funktionen für Datenidentifizierung, -klassifizierung und -verlust sind für Azure Storage- oder Computeressourcen noch nicht verfügbar. Implementieren Sie eine Drittanbieterlösung, wenn dies für Compliancezwecke erforderlich ist.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Für Speicherkonten, die Ihrem Azure Batch-Konto zugeordnet sind, wird empfohlen, Microsoft die Verwaltung von Verschlüsselungsschlüsseln zu gestatten. Sie haben jedoch die Möglichkeit, bei Bedarf Ihre eigenen Schlüssel zu verwalten.

Die Azure-Datenträgerverschlüsselung unterstützt Sie beim Schutz Ihrer Daten gemäß den Sicherheits- und Complianceanforderungen einer Organisation. Alle verwalteten Datenträger, Momentaufnahmen, Images und Daten, die auf vorhandene Datenträger geschrieben wurden, werden im Ruhezustand automatisch mit plattformseitig verwalteten Schlüsseln verschlüsselt.

- [Verwalten von Verschlüsselungsschlüsseln für Azure Storage-Konten](/azure/storage/common/storage-encryption-keys-portal)

- [Konfigurieren von kundenseitig verwalteten Schlüsseln mit Azure Key Vault über das Azure-Portal](/azure/storage/common/storage-encryption-keys-portal)

- [Erstellen eines Pools mit aktivierter Datenträgerverschlüsselung](disk-encryption.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen zu erstellen, die bei Änderungen an wichtigen Azure-Ressourcen ausgegeben werden, die Ihren Azure Batch-Konten/-Pools zugeordnet sind.

Konfigurieren Sie Diagnoseeinstellungen für Speicherkonten, die dem Azure Batch-Pool zugeordnet sind, um alle CRUD-Vorgänge für Pooldaten zu überwachen und zu protokollieren.

- [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](../azure-monitor/platform/alerts-activity-log.md)

- [Aktivieren zusätzlicher Protokollierung/Überwachung für ein Azure Storage-Konto](../storage/common/storage-monitor-storage-account.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Für Azure Batch-Poolknoten sind Sie für die Verwaltung der Lösung für die Sicherheitsrisikoverwaltung verantwortlich.

Wenn Sie über ein Rapid7-, Qualys- oder anderes Sicherheitsrisiko-Verwaltungsplattformabonnement verfügen, können Sie optional Sicherheitsanfälligkeits-Agents für Batch-Poolknoten manuell installieren und Knoten über das entsprechende Portal verwalten.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Leitfaden**: Microsoft zum Verwalten und Aktualisieren von Basisknotenimages für Azure Batch-Pools. Stellen Sie sicher, dass das Betriebssystem der Azure Batch-Poolknoten für die Clusterlebensdauer gepatcht bleibt, was die Aktivierung automatischer Updates, die Überwachung der Knoten oder die Durchführung regelmäßiger Neustarts erfordern kann.

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Bereitstellen einer automatisierten Patchverwaltungslösung für Softwaretitel von Drittanbietern

**Leitfaden**: Stellen Sie sicher, dass die Drittanbieteranwendungen der Azure Batch-Poolknoten für die Clusterlebensdauer gepatcht bleibt, was die Aktivierung automatischer Updates, die Überwachung der Knoten oder die Durchführung regelmäßiger Neustarts erfordern kann.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden**: Wenn Sie über ein Rapid7-, Qualys- oder anderes Sicherheitsrisiko-Verwaltungsplattformabonnement verfügen, können Sie das Portal dieses Anbieters verwenden, um kaskadierende Sicherheitsrisikoprüfungen anzuzeigen und zu vergleichen.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Verwenden Sie ein gängiges Risikobewertungsprogramm (z. B. Common Vulnerability Scoring System) oder die von Ihrem Scantool eines Drittanbieters bereitgestellten Standardrisikobewertungen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen (z. B. Computeressourcen, Speicher, Netzwerke usw.) in Ihren Abonnements abzufragen bzw. zu ermitteln. Stellen Sie entsprechende Berechtigungen (Leseberechtigungen) in Ihrem Mandanten sicher, und vergewissern Sie sich, dass Sie alle Azure-Abonnements sowie Ressourcen in Ihren Abonnements aufzählen können.

Obwohl klassische Azure-Ressourcen über Azure Resource Graph Explorer ermittelt werden können, wird dringend empfohlen, Azure Resource Manager-Ressourcen zu erstellen und zu verwenden.

- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../governance/resource-graph/first-query-portal.md)

- [Anzeigen Ihrer Azure-Abonnements](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Grundlegendes zu Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Ihre Azure-Ressourcen an, die Metadaten erzeugen, um sie logisch in einer Taxonomie zu organisieren.

- [Erstellen und Verwenden von Tags](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie nach Bedarf Tagging, Verwaltungsgruppen und separate Abonnements zum Organisieren und Nachverfolgen von Ressourcen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

- [Erstellen zusätzlicher Azure-Abonnements](/azure/billing/billing-create-subscription)

- [Erstellen von Verwaltungsgruppen](/azure/governance/management-groups/create)

- [Erstellen und Verwenden von Tags](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten des Bestands an genehmigten Azure-Ressourcen

**Leitfaden**: Definieren Sie eine Liste der genehmigte Azure-Ressourcen und der genehmigten Software für Computeressourcen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

Verwenden Sie Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Überwachen auf nicht genehmigte Softwareanwendungen innerhalb von Computeressourcen

**Leitfaden**: Implementieren Sie für Azure Batch-Poolknoten eine Drittanbieterlösung, um Clusterknoten für nicht genehmigte Softwareanwendungen zu überwachen.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Implementieren Sie für Azure Batch-Poolknoten eine Drittanbieterlösung, um Clusterknoten für nicht genehmigte Softwareanwendungen zu überwachen.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="68-use-only-approved-applications"></a>6.8: Ausschließliche Verwendung genehmigter Anwendungen

**Leitfaden**: Implementieren Sie für Azure Batch-Poolknoten eine Drittanbieterlösung, um zu verhindern, dass nicht autorisierte Software ausgeführt wird.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen: 
- Not allowed resource types (Unzulässige Ressourcentypen) 
- Zulässige Ressourcentypen 

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/built-in-policies.md#general)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Verwalten eines Bestands an genehmigten Softwaretiteln

**Leitfaden**: Implementieren Sie für Azure Batch-Poolknoten eine Drittanbieterlösung, um zu verhindern, dass nicht autorisierte Dateitypen ausgeführt werden.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Verwenden Sie den bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.

- [Verwalten des Zugriffs auf die Azure-Verwaltung mit bedingtem Zugriff](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.Batch“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration Ihrer Azure Batch-Konten und -Pools zu erstellen.

- [Anzeigen verfügbarer Azure Policy-Aliase](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Einrichten sicherer Konfigurationen für das Betriebssystem Ihrer Batch-Poolknoten.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure Policy-Richtlinien [deny] (Verweigern) und [deploy if not exist] (Bereitstellen, falls nicht vorhanden), um sichere Einstellungen für die Azure-Ressourcen zu erzwingen, die mit Ihrem Batch-Konto und Ihren Pools verknüpft sind (z. B. virtuelle Netzwerke, Subnetze, Azure Firewall-Instanzen, Azure Storage-Konten usw.). Sie können Azure Policy-Aliase aus den folgenden Namespaces verwenden, um benutzerdefinierte Richtlinien zu erstellen:

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Azure Batch-Poolbetriebssystemimages werden von Microsoft verwaltet und gewartet. Sie sind verantwortlich für die Implementierung der Zustandskonfiguration auf Betriebssystemebene.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Wenn Sie benutzerdefinierte Azure Policy-Definitionen für Azure Batch-Konten, -Pools oder verwandte Ressourcen verwenden, nutzen Sie Azure Repos, um Code sicher zu speichern und zu verwalten.

- [Speichern von Code in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Dokumentation zu Azure Repos](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Leitfaden**: Wenn Sie benutzerdefinierte Images für Ihre Azure Batch-Pools verwenden, verwenden Sie rollenbasierte Zugriffssteuerung (RBAC), um sicherzustellen, dass nur autorisierte Benutzer auf die Images zugreifen können.

- [Grundlegendes zu RBAC in Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Konfigurieren von RBAC in Azure](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Verwenden Sie integrierte Azure Policy-Definitionen, um Azure Batch-bezogene Ressourcenkonfigurationen mit Warnungen zu versehen, zu überwachen und zu erzwingen.  Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.Batch“, um benutzerdefinierte Richtlinien für Ihre Azure Batch-Konten und -Pools zu erstellen. Entwickeln Sie außerdem einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Konfigurationsverwaltungstools für Betriebssysteme

**Leitfaden**: Implementieren Sie eine Drittanbieterlösung, um den gewünschten Zustand für die Betriebssysteme Ihrer Azure Batch-Poolknoten zu verwalten.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.Batch“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration Ihrer Azure Batch-Instanz zu erstellen. Sie können auch die integrierten Richtlinien verwenden, die speziell für Azure Batch oder die von Azure Batch verwendeten Ressourcen erstellt wurden, beispielsweise:
- Subnetze sollten einer Netzwerksicherheitsgruppe zugeordnet sein, und Speicherkonten sollten einen Dienstendpunkt eines virtuellen Netzwerks verwenden.
- Diagnoseprotokolle in Batch-Konten sollten aktiviert sein.

- [Anzeigen verfügbarer Azure Policy-Aliase](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Leitfaden**: Implementieren Sie eine Drittanbieterlösung, um den Zustand für die Betriebssysteme Ihrer Azure Batch-Poolknoten zu überwachen.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden**: Azure Key Vault kann mit Azure Batch-Bereitstellungen verwendet werden, um Schlüssel für den Poolspeicher innerhalb von Azure Storage-Konten zu verwalten.

- [Integrieren mit verwalteten Azure-Identitäten](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Erstellen einer Azure Key Vault-Instanz](../key-vault/general/quick-create-portal.md)

- [Authentifizieren bei Key Vault](../key-vault/general/authentication.md)
- [Zuweisen einer Key Vault-Zugriffsrichtlinie](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen. 

- [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Verwenden einer zentral verwalteten Antischadsoftware

**Leitfaden**: Verwenden Sie Windows Defender für Ihre einzelnen Azure Batch-Poolknoten im Fall von Windows-Betriebssystemen, oder stellen Sie eine eigene Antischadsoftwarelösung bereit, wenn Sie Linux verwenden.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure Batch) aktiviert, wird jedoch nicht für Kundeninhalte ausgeführt.

Führen Sie eine Vorabprüfung aller Dateien durch, die in computefremde Azure-Ressourcen hochgeladen werden sollen, z. B. in App Service, Data Lake Storage, Blob Storage usw. Microsoft kann in diesen Instanzen nicht auf Kundendaten zugreifen.

- [Grundlegendes zu Microsoft Antimalware für Azure Cloud Services und Virtual Machines](../security/fundamentals/antimalware.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Verwenden Sie Windows Defender für Ihre einzelnen Azure Batch-Poolknoten im Fall von Windows-Betriebssystemen, und stellen Sie sicher, dass automatische Updates aktiviert sind. Stellen Sie Ihre eigene Antischadsoftwarelösung bereit, wenn Sie Linux verwenden.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Wenn Sie ein Azure Storage-Konto für den Azure Batch-Pooldatenspeicher verwenden, wählen Sie die entsprechende Redundanzoption (LRS, ZRS, GRS, RA-GRS) aus. 

- [Konfigurieren der Speicherredundanz für Azure Storage-Konten](../storage/common/storage-redundancy.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller kundenseitig verwalteten Schlüssel

**Leitfaden**: Wenn Sie ein Azure Storage-Konto für den Azure Batch-Pooldatenspeicher verwenden, wählen Sie die entsprechende Redundanzoption (LRS, ZRS, GRS, RA-GRS) aus. Wenn Sie Azure Key Vault für einen Teil Ihrer Azure Batch-Bereitstellung verwenden, stellen Sie sicher, dass Ihre Schlüssel gesichert werden.

- [Konfigurieren der Speicherredundanz für Azure Storage-Konten](../storage/common/storage-redundancy.md)

- [Sichern von Schlüsseltresorschlüsseln in Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich kundenseitig verwalteter Schlüssel

**Leitfaden**: Wenn Sie Ihre eigenen Schlüssel für Azure Storage-Konten oder eine andere Ressource verwalten, die sich auf Ihre Azure Batch-Implementierung bezieht, testen Sie regelmäßig die Wiederherstellung gesicherter Schlüssel.

- [Sichern von Schlüsseltresorschlüsseln in Azure](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

- [Wiederherstellen eines kundenseitig verwalteten Schlüssels mit PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Wenn Azure Key Vault zum Speichern von Schlüsseln verwendet wird, die sich auf Azure Batch-Poolspeicherkonten beziehen, aktivieren Sie vorläufiges Löschen in Azure Key Vault, um Schlüssel vor versehentlichem oder böswilligem Löschen zu schützen.

- [Aktivieren des vorläufigen Löschens in Azure Key Vault](/azure/key-vault/key-vault-soft-delete-powershell)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Leitfaden**: Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen die Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen definiert sind.

- [Leitfaden zu Planung und Betrieb](../security-center/security-center-planning-and-operations-guide.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Leitfaden**: Security Center weist Warnungen einen Schweregrad zu, um Ihnen zu helfen, die Reihenfolge zu priorisieren, in der Sie sich um Warnungen kümmern. So können Sie, sobald eine Ressource gefährdet ist, sofort zu ihr gelangen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Tests zur Reaktionsfähigkeit Ihrer Systeme auf Vorfälle durch. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf.

- [„Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities“ des National Institute of Standards and Technology (NIST)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf Ihre Daten zugegriffen haben.

- [Festlegen der Kontaktinformationen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen.

- [Konfigurieren des fortlaufenden Exports](../security-center/continuous-export.md)

- [Streamen von Warnungen in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um über „Logic Apps“ automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen.

- [Konfigurieren von Workflowautomatisierung und Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Durchführen regelmäßiger Penetrationstests Ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnissen

**Leitfaden**: [Befolgen Sie die Einsatzregeln von Microsoft (Microsoft Rules of Engagement), um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.).

Weitere Informationen zur Microsoft-Strategie im Zusammenhang mit Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastrukturen, Dienste und Anwendungen sowie zu deren Durchführung finden Sie unter diesem Link: 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die [Übersicht über Version 2 des Azure-Sicherheitsvergleichstests](/azure/security/benchmarks/overview) an.
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](/azure/security/benchmarks/security-baselines-overview).
