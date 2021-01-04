---
title: Azure-Sicherheitsbaseline für Azure Cognitive Search
description: Die Azure Cognitive Search-Sicherheitsbaseline enthält Schrittanleitungen und Ressourcen für die Implementierung der Sicherheitsempfehlungen aus dem Azure-Sicherheitsvergleichstest.
author: msmbaldwin
ms.service: search
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 07ef3da9f67de92c3526b83222c1c8088660d7c7
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499865"
---
# <a name="azure-security-baseline-for-azure-cognitive-search"></a>Azure-Sicherheitsbaseline für Azure Cognitive Search

Diese Sicherheitsbaseline wendet Empfehlungen der [Version 1.0 des Vergleichstests für die Azure-Sicherheit](../security/benchmarks/overview.md) auf Azure Cognitive Search an. Der Azure-Sicherheitsvergleichstest enthält Empfehlungen zum Schutz Ihrer Cloudlösungen in Azure. Der Inhalt ist nach den **Sicherheitskontrollen** gruppiert, die durch den Vergleichstest für die Azure-Sicherheit und die entsprechenden, für Azure Cognitive Search geltenden Empfehlungen definiert werden. **Steuerelemente**, die nicht auf Azure Cognitive Search anwendbar sind, oder der Kunde wurden ausgeschlossen.

Die gesamte Zuordnung zwischen Azure Cognitive Search und dem Azure-Sicherheitsvergleichstest finden Sie in der [vollständigen Zuordnungsdatei der Azure Cognitive Search-Sicherheitsbaseline](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Schützen von Azure-Ressourcen in virtuellen Netzwerken

**Leitfaden**: Stellen Sie sicher, dass auf alle Microsoft Azure Virtual Network-Subnetzbereitstellungen eine Netzwerksicherheitsgruppe mit Regeln zum Implementieren eines Zugriffsschemas vom Typ „geringste Berechtigungen“ angewendet wurde. Erlauben Sie nur den Zugriff auf die vertrauenswürdigen Ports und IP-Adressbereiche Ihrer Anwendung. Stellen Sie, wenn möglich, Azure Cognitive Search mit einem privaten Endpunkt in Azure bereit, um privaten Zugriff auf Ihre Dienste aus dem virtuellen Netzwerk zu ermöglichen.

Cognitive Search unterstützt auch zusätzliche Netzwerksicherheitsfunktionen für die Verwaltung von Netzwerk-Zugriffssteuerungslisten. Konfigurieren Sie Ihren Suchdienst so, dass nur die Kommunikation mit vertrauenswürdigen Quellen zulässig ist. Dazu schränken Sie den Zugriff aus bestimmten öffentlichen IP-Adressbereichen mithilfe der Firewallfunktion ein.

- [Konfigurieren von privaten Endpunkten für Azure Cognitive Search](./service-create-private-endpoint.md)

- [Konfigurieren der Azure Cognitive Search-Firewall](./service-configure-firewall.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von virtuellen Netzwerken, Subnetzen und Netzwerkkarten (NICs)

**Leitfaden**: Cognitive Search kann nicht direkt in einem virtuellen Netzwerk bereitgestellt werden. Wenn sich die Clientanwendung oder die Datenquellen jedoch in einem virtuellen Netzwerk befinden, können Sie den Datenverkehr für diese netzwerkinternen Komponenten überwachen und protokollieren, einschließlich Anforderungen, die an einen Suchdienst in der Cloud gesendet werden. Standardempfehlungen sind das Aktivieren eines Datenflussprotokolls für Netzwerksicherheitsgruppen und das Senden von Protokollen an Azure Storage oder einen Log Analytics-Arbeitsbereich. Optional können Sie Traffic Analytics verwenden, um Einblicke in Datenverkehrsmuster zu erhalten.

- [Aktivieren von Datenflussprotokollen für Netzwerksicherheitsgruppen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Aktivieren und Verwenden von Traffic Analytics](../network-watcher/traffic-analytics.md)

- [Grundlegendes zu der von Azure Security Center bereitgestellten Netzwerksicherheit](../security-center/security-center-network-recommendations.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Leitfaden**: Trifft für Cognitive Search nicht zu. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten bösartigen IP-Adressen

**Leitfaden**: Cognitive Search bietet keine spezifische Funktion zur Abwehr eines verteilten Denial-of-Service-Angriffs, aber Sie können DDoS Protection Standard in den virtuellen Netzwerken aktivieren, die Ihrem Cognitive Search-Dienst für den allgemeinen Schutz zugeordnet sind.

- [Konfigurieren von DDoS-Schutz](../ddos-protection/manage-ddos-protection.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets"></a>1.5: Aufzeichnen von Netzwerkpaketen

**Leitfaden**: Aktivieren Sie Datenflussprotokolle für die Netzwerksicherheitsgruppen, die virtuelle Azure-Computer (VM) schützen, die eine Verbindung mit Ihrem Cognitive Search-Dienst herstellen. Senden Sie Protokolle zwecks Datenverkehrsüberwachung an ein Azure Storage-Konto. 

Aktivieren Sie die Network Watcher-Paketerfassung, falls dies für die Untersuchung anomaler Aktivitäten erforderlich ist.

- [Aktivieren der NSG-Flussprotokolle](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Aktivieren von Network Watcher](../network-watcher/network-watcher-create.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (Intrusion Detection/Intrusion Prevention Systems, IDS/IPS)

**Leitfaden**: Cognitive Search bietet keine Unterstützung für die Erkennung von Netzwerkangriffen, aber als Angriffsabwehr können Sie Firewallregeln konfigurieren, um die vom Cognitive Search-Dienst akzeptierten IP-Adressen anzugeben. Konfigurieren Sie einen privaten Endpunkt, um den Suchdatenverkehr vom öffentlichen Internet fernzuhalten.

- [Konfigurieren von kundenseitig verwalteten Schlüsseln für die Datenverschlüsselung](./search-security-manage-encryption-keys.md)

- [Abrufen von Informationen zu kundenseitig verwalteten Schlüsseln aus Indizes und Synonymzuordnungen](./search-security-get-encryption-keys.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Leitfaden**: Trifft für Cognitive Search nicht zu. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie Diensttags, wenn Sie Indexer und Qualifikationsgruppen in Cognitive Search nutzen, um einen Bereich von IP-Adressen darzustellen, die über Berechtigungen zum Herstellen einer Verbindung mit externen Ressourcen verfügen. 

Erlauben oder verweigern Sie den Datenverkehr zu Ressourcen, indem Sie den Diensttagnamen (z. B. AzureCognitiveSearch) im entsprechenden Quell- oder Zielfeld einer Regel angeben. 

- [Diensttags für virtuelle Netzwerke](../virtual-network/service-tags-overview.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Cognitive Search weist entwurfsbedingt keine Netzwerkressourcen auf und ist auch nicht davon abhängig. Client-Apps und Datenquellen im Zusammenhang mit Ihrer Suchanwendung können sich in einem virtuellen Netzwerk befinden, aber der Suchdienst selbst wird nicht im Netzwerk bereitgestellt. 

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Sie können Cognitive Search mit einem privaten Endpunkt in Azure konfigurieren, um Ihren Suchdienst in ein virtuelles Netzwerk zu integrieren.  Verwenden Sie Ressourcentags für Netzwerksicherheitsgruppen und andere Ressourcen im Zusammenhang mit Netzwerksicherheit und Datenverkehrsfluss. Für einzelne Netzwerksicherheitsgruppen-Regeln verwenden Sie das Feld „Beschreibung“, um die Regeln zu dokumentieren, die Datenverkehr aus/zu einem Netzwerk zulassen. 

Verwenden Sie eine der integrierten Azure Policy-Definitionen für das Tagging, z. B. Auswirkungen vom Typ „Tag und zugehörigen Wert erzwingen“, um sicherzustellen, dass alle Ressourcen mit Tags erstellt werden und Sie über vorhandene nicht markierte Ressourcen benachrichtigt werden. 

Sie können Azure PowerShell oder die Azure CLI verwenden, um Ressourcen basierend auf ihren Tags zu suchen oder Aktionen für diese Ressourcen auszuführen. 

- [Erstellen eines privaten Endpunkts für Cognitive Search](./service-create-private-endpoint.md)

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

- [Erstellen eines virtuellen Azure-Netzwerks](../virtual-network/quick-create-portal.md)

- [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen-Regeln](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Da Cognitive Search weder Netzwerkkomponenten aufweist noch davon abhängig ist, können die Konfigurationen dieser Ressourcen nicht überwacht werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Cognitive Search bietet keine Unterstützung für das Konfigurieren eigener Zeitsynchronisierungsquellen. Der Suchdienst ist von Microsoft-Zeitsynchronisierungsquellen abhängig und für Kunden nicht konfigurierbar.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Erfassen Sie Protokolle im Zusammenhang mit Cognitive Search über Azure Monitor, um Sicherheitsdaten zu aggregieren, die von Endpunktgeräten, Netzwerkressourcen und anderen Sicherheitssystemen generiert werden. Verwenden Sie in Azure Monitor Log Analytics-Arbeitsbereiche, um Analysen abzufragen und auszuführen, und verwenden Sie Azure Storage-Konten für langfristige Speicherung und Archivierung.
Alternativ können Sie diese Daten in Azure Sentinel oder einer SIEM-Drittanbieterlösung aktivieren und integrieren.

- [Erste Schritte mit Azure Monitor und der Integration einer SIEM-Drittanbieterlösung](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Diagnose- und Betriebsprotokolle bieten Einblicke in die detaillierten Vorgänge von Cognitive Search und sind nützlich für die Überwachung des Diensts und für Workloads, die auf den Dienst zugreifen.  Zum Erfassen von Diagnosedaten aktivieren Sie die Protokollierung, indem Sie angeben, wo Protokollierungsinformationen gespeichert werden.

- [Sammeln und Analysieren von Protokolldaten für Azure Cognitive Search](./search-monitor-logs.md)

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Leitfaden**: Trifft für Cognitive Search nicht zu. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Verlaufsdaten, die in Diagnosemetriken einfließen, werden von Cognitive Search standardmäßig 30 Tage lang aufbewahrt. Für eine längere Aufbewahrung stellen Sie sicher, dass die Einstellung aktiviert ist, die eine Speicheroption zum Beibehalten protokollierter Ereignisse und Metriken angibt.

Legen Sie in Azure Monitor den Aufbewahrungszeitraum Ihres Log Analytics-Arbeitsbereichs gemäß den Compliancevorschriften Ihrer Organisation fest. Verwenden Sie Azure Storage-Konten für die langfristige Speicherung und Archivierung. 

- [Ändern des Datenaufbewahrungszeitraums in Protokollanalyse](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Konfigurieren der Aufbewahrungsrichtlinie für Azure Storage-Kontoprotokolle](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Analysieren und überwachen Sie Protokolle Ihres Cognitive Search-Diensts auf anormales Verhalten. Verwenden Sie Log Analytics von Azure Monitor, um Protokolle zu überprüfen und Abfragen für Protokolldaten auszuführen. Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Drittanbieter-SIEM-Lösung aktivieren und integrieren.

- [Sammeln und Analysieren von Protokolldaten für Cognitive Search](./search-monitor-logs.md)

- [Visualisieren von Suchprotokolldaten in Power BI](./search-monitor-logs-powerbi.md)

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Weitere Informationen zu Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md)

- [Ausführen benutzerdefinierter Abfragen in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden**: Verwenden Sie Security Center mit einem Log Analytics-Arbeitsbereich für die Überwachung und Warnung bei anomalen Aktivitäten, die in Sicherheitsprotokollen und Ereignissen gefunden werden. Alternativ dazu können Sie auch Daten in Azure Sentinel aktivieren und integrieren.

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

- [Verwalten von Warnungen in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [Warnungen bei Log Analytics-Protokolldaten](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Trifft für Cognitive Search nicht zu. Microsoft verwaltet die Antischadsoftwarelösung für die zugrunde liegende Plattform.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Trifft für Cognitive Search nicht zu. DNS-Protokolle werden nicht erzeugt oder genutzt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Leitfaden**: Trifft für Cognitive Search nicht zu. Die Befehlszeilenüberwachung ist für Cognitive Search nicht verfügbar.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Mit der rollenbasierten Zugriffssteuerung in Azure (Azure RBAC) kann der Zugriff auf Azure-Ressourcen über Rollenzuweisungen verwaltet werden. Diese Rollen können Benutzern, Gruppen, Dienstprinzipalen und verwalteten Identitäten zugewiesen werden. Für bestimmte Ressourcen sind vordefinierte integrierte Rollen verfügbar. Diese Rollen können über Tools wie die Azure CLI, Azure PowerShell oder das Azure-Portal inventarisiert oder abgefragt werden.

Cognitive Search-Rollen sind Berechtigungen zugeordnet, die Verwaltungsaufgaben auf Dienstebene unterstützen.  Diese Rollen gewähren keinen Zugriff auf den Dienstendpunkt. Für den Zugriff auf Vorgänge für den Endpunkt (z. B. Indexverwaltung, Indexauffüllung und Abfragen von Suchdaten) verwenden Sie API-Schlüssel zum Authentifizieren der Anforderung.

- [Festlegen von Rollen für den Administratorzugriff auf Azure Cognitive Search](./search-security-rbac.md)

- [Erstellen und Verwalten von API-Schlüsseln für einen Dienst für die kognitive Azure-Suche](./search-security-api-keys.md)

- [Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)
- [Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Trifft für Cognitive Search nicht zu. Es ist kein Konzept von Standardkennwörtern vorhanden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Cognitive Search verfügt nicht über das Konzept von Administratorkonten auf lokaler Ebene oder in Azure Active Directory (Azure AD), die zum Verwalten von Indizes und Vorgängen verwendet werden können. 

Verwenden Sie für Verwaltungsvorgänge die integrierten Azure AD-Rollen, die explizit zugewiesen werden müssen. Rufen Sie das Azure AD PowerShell-Modul auf, um Ad-hoc-Abfragen zum Ermitteln von Konten auszuführen, die Mitglieder von administrativen Gruppen sind.

- [Verwenden von Rollen für den Administratorzugriff in Cognitive Search](./search-security-rbac.md)

- [Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrole)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden**: Verwenden Sie die SSO-Authentifizierung mit Azure Active Directory (Azure AD), um auf Suchdienstinformationen für Verwaltungsvorgänge zuzugreifen, die über Azure Resource Manager unterstützt werden. 

Richten Sie einen Prozess zum Reduzieren der Anzahl von Identitäten und Anmeldeinformationen ein, indem Sie SSO für den Dienst mit den bereits vorhandenen Identitäten Ihrer Organisation aktivieren.

- [Grundlegendes zu SSO mit Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie das Feature für die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) von Azure Active Directory (Azure AD), und folgen Sie den Empfehlungen für Identität und Zugriff in Security Center.

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md) 

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie eine Arbeitsstation mit privilegiertem Zugriff (Privileged Access Workstation, PAW) mit Multi-Factor Authentication (MFA), die für die Anmeldung an und den Zugriff auf Azure-Ressourcen konfiguriert ist.

- [Informationen zu sicheren, von Azure verwalteten Arbeitsstationen](../active-directory/devices/concept-azure-managed-workstation.md)
 

- [Planen einer Bereitstellung von Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md)
 

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Sicherheitsberichte und Überwachungsfunktionen von Azure Active Directory (Azure AD), um verdächtige oder nicht sichere Aktivitäten in der Umgebung zu ermitteln. Verwenden Sie Security Center zum Überwachen von identitäts- und zugriffsbezogenen Aktivitäten.

- [Identifizieren von Azure AD-Benutzern, die aufgrund riskanter Aktivitäten gekennzeichnet wurden](../active-directory/identity-protection/overview-identity-protection.md)

- [Überwachen der identitäts- und zugriffsbezogenen Aktivitäten von Benutzern in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Trifft für Cognitive Search nicht zu. Die Verwendung von genehmigten Standorten als Bedingung für den Zugriff wird nicht unterstützt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (Azure AD) als zentrales Authentifizierungs- und Autorisierungssystem für Verwaltungsaufgaben auf Dienstebene in Azure Cognitive Search. Azure AD-Identitäten gewähren keinen Zugriff auf den Suchdienstendpunkt.  Der Zugriff auf Vorgänge wie Indexverwaltung, Indexauffüllung und Abfragen von Suchdaten ist über API-Schlüssel möglich.

- [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Erstellen und Verwalten von API-Schlüsseln für einen Dienst für die kognitive Azure-Suche](./search-security-api-keys.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure Active Directory (Azure AD) enthält Protokolle zum Ermitteln von veralteten Konten. Verwenden Sie Identitäts- und Zugriffsüberprüfungen von Azure AD, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen sowie Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin über Zugriff verfügen. 

Überprüfen Sie die Diagnoseprotokolle von Cognitive Search auf Aktivitäten im Suchdienstendpunkt, z. B. Indexverwaltung, Indexauffüllung und Abfragen.

- [Grundlegendes zur Azure AD-Berichterstellung](../active-directory/reports-monitoring/index.yml)

- [Was sind Azure AD-Zugriffsüberprüfungen?](../active-directory/governance/access-reviews-overview.md)

- [Überwachen von Vorgängen und Aktivitäten von Azure Cognitive Search](./search-monitor-usage.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Anmeldeinformationen

**Leitfaden**: Der Zugriff auf Protokollquellen von Azure Active Directory-Anmeldeaktivitäten (Azure AD) sowie von Überwachungs- und Risikoereignissen ermöglichen die Integration in jedes SIEM- oder Überwachungstool.

Optimieren Sie diesen Prozess, indem Sie Diagnoseeinstellungen für Azure AD-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. Konfigurieren Sie die gewünschten Warnungen im Log Analytics-Arbeitsbereich.

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Mit Azure AD-Funktionen (Azure Active Directory) zum Identitätsschutz können Sie automatische Reaktionen auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten konfigurieren. Erfassen Sie Daten nach Bedarf zur weiteren Untersuchung in Azure Sentinel.

- [Anzeigen riskanter Azure AD-Anmeldungen](../active-directory/identity-protection/overview-identity-protection.md) 

- [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md) 

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: Trifft für Cognitive Search nicht zu. Cognitive Search wird von Kunden-Lockbox nicht unterstützt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="data-protection"></a>Schutz von Daten

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenschutz](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für die Nachverfolgung von Azure-Ressourcen, die vertrauliche Informationen speichern oder verarbeiten.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie separate Abonnements und/oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Ressourcen sollten durch ein virtuelles Netzwerk oder Subnetz getrennt, entsprechend gekennzeichnet und in einer Netzwerksicherheitsgruppe oder Azure Firewall gesichert werden. Ressourcen, die vertrauliche Daten speichern oder verarbeiten, müssen isoliert werden. Verwenden Sie Private Link, um einen privaten Endpunkt für Cognitive Search zu konfigurieren.

- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md) 

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

- [Erstellen eines privaten Endpunkts für Cognitive Search](./service-create-private-endpoint.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Verwenden Sie eine Drittanbieterlösung aus Azure Marketplace an Netzwerkumkreisen, die die nicht autorisierte Übertragung von vertraulichen Informationen überwacht, derartige Übertragungen blockiert und gleichzeitig die Informationssicherheitsexperten benachrichtigt.

Microsoft verwaltetet die zugrunde liegende Plattform, behandelt alle Kundeninhalte als vertraulich und schützt Kundendaten vor Verlust und Offenlegung. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md) 

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Cognitive Search verschlüsselt Daten während der Übertragung mit Transport Layer Security 1.2 und erzwingt die Verschlüsselung (SSL/TLS) jederzeit für alle Verbindungen. Dadurch wird sichergestellt, dass alle Daten während der Übertragung zwischen dem Client und dem Dienst verschlüsselt werden.

- [Grundlegendes zur Verschlüsselung während der Übertragung mit Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Funktionen für Datenidentifizierung, -klassifizierung und -verlust sind für Cognitive Search noch nicht verfügbar. Implementieren Sie eine Drittanbieterlösung, wenn dies für Compliancezwecke erforderlich ist. 

Microsoft verwaltetet die zugrunde liegende Plattform, behandelt alle Kundeninhalte als vertraulich und schützt Kundendaten vor Verlust und Offenlegung. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Verwalten des Zugriffs auf Ressourcen

**Leitfaden**: Verwenden Sie für die Dienstverwaltung die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC), um den Zugriff auf Schlüssel und Konfiguration zu verwalten. Bei Inhaltsvorgängen wie Indizierung und Abfragen verwendet Cognitive Search Schlüssel anstelle eines identitätsbasierten Zugriffssteuerungsmodells. Verwenden Sie Azure RBAC, um den Zugriff auf Schlüssel zu steuern.
- [Konfigurieren von RBAC in Azure](../role-based-access-control/role-assignments-portal.md) 

 
- [Verwenden von Rollen für den Administratorzugriff auf Cognitive Search](./search-security-rbac.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Trifft für Cognitive Search nicht zu. Dieser Leitfaden ist für Computeressourcen vorgesehen. 

Microsoft verwaltet die zugrunde liegende Infrastruktur für Cognitive Search und hat strenge Kontrollen implementiert, um Verluste oder Offenlegungen von Kundendaten zu verhindern.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Cognitive Search verschlüsselt ruhende indizierte Inhalte automatisch mit von Microsoft verwalteten Schlüsseln. Wenn ein weiterer Schutz erforderlich ist, können Sie die Standardverschlüsselung durch eine zweite Verschlüsselungsebene ergänzen, indem Sie Schlüssel verwenden, die Sie in Azure Key Vault erstellen und verwalten.

- [Konfigurieren von kundenseitig verwalteten Schlüsseln für die Datenverschlüsselung in Azure Cognitive Search](./search-security-manage-encryption-keys.md)

- [Grundlegendes zur Verschlüsselung ruhender Daten in Azure](../security/fundamentals/encryption-atrest.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll, um Warnungen für den Fall zu erstellen, dass Änderungen an Produktionsinstanzen von Cognitive Search und anderen kritischen bzw. verbundenen Ressourcen vorgenommen werden.

- [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](../azure-monitor/platform/alerts-activity-log.md)

- [Erstellen von Warnungen für Cognitive Search-Aktivitäten](./search-monitor-logs.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Derzeit nicht für Cognitive Search verfügbar.  Bei Clustern, in denen Suchdienstinhalte gespeichert werden, ist Microsoft für die Verwaltung von Sicherheitsrisiken dieser Cluster verantwortlich.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Leitfaden**: Trifft für Cognitive Search nicht zu. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Bereitstellen einer automatisierten Patchverwaltungslösung für Softwaretitel von Drittanbietern

**Leitfaden**: Trifft für Cognitive Search nicht zu. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden**: Trifft für Cognitive Search nicht zu. Microsoft führt die Verwaltung der Sicherheitsrisiken auf den zugrunde liegenden Systemen aus, die Cognitive Search-Dienste unterstützen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Trifft für Cognitive Search nicht zu. Es ist kein standardmäßiges Risikobewertungs- oder Einstufungssystem für Ergebnisse der Überprüfung auf Sicherheitsrisiken vorhanden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen (z. B. Computeressourcen, Speicher, Netzwerke, Ports, Protokolle usw.) in Ihren Abonnements abzufragen und zu ermitteln.  

Sorgen Sie für entsprechende (Lese-) Berechtigungen auf Ihrem Mandanten, und zählen Sie alle Azure-Abonnements sowie Ressourcen in Ihren Abonnements auf.  

- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../governance/resource-graph/first-query-portal.md) 

- [Anzeigen Ihrer Azure-Abonnements](/powershell/module/az.accounts/get-azsubscription) 

- [Grundlegendes zu Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Azure-Ressourcen mit Metadaten an, um sie logisch in einer Taxonomie zu organisieren.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie nach Bedarf Tagging, Verwaltungsgruppen und separate Abonnements zum Organisieren und Nachverfolgen von Ressourcen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.
- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md) 

- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md) 

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md) 

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten eines Bestands an genehmigten Azure-Ressourcen

**Leitfaden**: Definieren Sie eine Liste genehmigter Azure-Ressourcen im Zusammenhang mit der Indizierung und Verarbeitung von Qualifikationsgruppen in Cognitive Search.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Es wird empfohlen, einen Bestand an Azure-Ressourcen zu definieren, die zuvor gemäß den Richtlinien und Standards Ihrer Organisation zur Verwendung genehmigt wurden, und dann mit Azure Policy oder Azure Resource Graph auf nicht genehmigte Azure-Ressourcen zu überwachen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md) 

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Überwachen auf nicht genehmigte Softwareanwendungen innerhalb von Computeressourcen

**Leitfaden**: Trifft für Cognitive Search nicht zu. Dieser Leitfaden ist für Computeressourcen vorgesehen.

Es wird empfohlen, einen Bestand an Softwareanwendungen festzulegen, die gemäß den Richtlinien und Sicherheitsstandards Ihrer Organisation als genehmigt eingestuft wurden, und auf nicht genehmigte Softwaretitel zu überwachen, die auf Ihren Azure-Computeressourcen installiert sind.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Trifft für Cognitive Search nicht zu. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="68-use-only-approved-applications"></a>6.8: Ausschließliche Verwendung genehmigter Anwendungen

**Leitfaden**: Trifft für Cognitive Search nicht zu. Es werden weder Computeressourcen verfügbar gemacht noch dürfen Softwareanwendungen auf Ressourcen installiert werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen festzulegen, die in Kundenabonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)
- Zulässige Ressourcentypen

Verwenden Sie Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen oder zu ermitteln. Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Ablehnen eines bestimmten Ressourcentyps mit Azure Policy](../governance/policy/samples/index.md) 

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Verwalten eines Bestands an genehmigten Softwaretiteln

**Leitfaden**: Trifft für Cognitive Search nicht zu. Diese Empfehlung ist für Anwendungen vorgesehen, die auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Verwenden Sie für die Dienstverwaltung den bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App „Microsoft Azure Management“ konfigurieren. 

Steuern Sie den Zugriff auf die Schlüssel, die zum Authentifizieren von Anforderungen für alle anderen Vorgänge verwendet werden, insbesondere solche, die mit Inhalten in Cognitive Search im Zusammenhang stehen.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Einschränken der Skriptausführung durch Benutzer in Computeressourcen

**Leitfaden**: Trifft für Cognitive Search nicht zu. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Leitfaden**: Trifft für Cognitive Search nicht zu. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.Search“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration Ihrer Azure Cognitive Search-Ressourcen zu erstellen. Sie können auch integrierte Azure Policy-Definitionen für Cognitive Search-Dienste verwenden, z. B.:

- Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

Mit Azure Resource Manager ist es möglich, die Vorlage in JSON-Code (JavaScript Object Notation) zu exportieren. Dieser sollte überprüft werden, um sicherzustellen, dass die Konfigurationen die Sicherheitsanforderungen für Ihre Organisation erfüllen. 

Sie können auch die Empfehlungen von Azure Security Center als sichere Konfigurationsbaseline für Ihre Azure-Ressourcen verwenden. 

- [Kontrollen zur Einhaltung gesetzlicher Bestimmungen in Azure Policy für Azure Cognitive Search](./security-controls-policy.md)

- [Anzeigen verfügbarer Azure Policy-Aliase](/powershell/module/az.resources/get-azpolicyalias)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Trifft für Cognitive Search nicht zu. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure Policy-Einstellungen [deny] (Verweigern) und [deploy if not exist] (Bereitstellen, falls nicht vorhanden), um durchgängig sichere Einstellungen für Ihre Cognitive Search-Dienstressourcen zu erzwingen. 

Azure Resource Manager-Vorlagen können verwendet werden, um die Sicherheitskonfiguration der für Ihre Organisation erforderlichen Azure-Ressourcen zu verwalten. 

- [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)

- [Kontrollen zur Einhaltung gesetzlicher Bestimmungen in Azure Policy für Azure Cognitive Search](./security-controls-policy.md)

- [Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

- [Übersicht über Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/overview.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Trifft für Cognitive Search nicht zu. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Wenn Sie benutzerdefinierte Azure Policy-Definitionen verwenden, nutzen Sie Azure DevOps oder Azure Repos, um Ihren Code sicher zu speichern und zu verwalten.

- [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow)

- [Dokumentation zu Azure Repos](/azure/devops/repos/index)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Leitfaden**: Trifft für Cognitive Search nicht zu. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für Ihre Cognitive Search-Dienstressourcen mit Azure Policy. 

Verwenden Sie Aliase, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfigurationen zu erstellen. Sie können auch auf integrierte Richtliniendefinitionen zurückgreifen, die sich auf Ihre spezifischen Ressourcen beziehen. 

Zusätzlich können Sie Azure Automation verwenden, um Konfigurationsänderungen bereitzustellen und Richtlinienausnahmen zu verwalten. 

- [Kontrollen zur Einhaltung gesetzlicher Bestimmungen in Azure Policy für Azure Cognitive Search](./security-controls-policy.md)

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Konfigurationsverwaltungstools für Betriebssysteme

**Leitfaden**: Trifft für Cognitive Search nicht zu. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie Security Center, um Baselineüberprüfungen Ihrer Cognitive Search-Dienstressourcen auszuführen.  Nutzen Sie auch Azure Policy zur Warnung und Überwachung Ihrer Ressourcenkonfigurationen. 

- [Umsetzen von Empfehlungen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

- [Kontrollen zur Einhaltung gesetzlicher Bestimmungen in Azure Policy für Azure Cognitive Search](./security-controls-policy.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Leitfaden**: Trifft für Cognitive Search nicht zu. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden**: Verwenden Sie verwaltete Azure-Identitäten in Verbindung mit Azure Key Vault, um die Verwaltung von Geheimnissen für Cloudanwendungen zu vereinfachen.
- [Verwenden von verwalteten Identitäten für Azure-Ressourcen](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 
- [Erstellen einer Key Vault-Instanz](../key-vault/general/quick-create-portal.md) 

- [Bereitstellen der Key Vault-Authentifizierung mit einer verwalteten Identität](../key-vault/general/assign-access-policy-portal.md) 

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Verwenden Sie eine verwaltete Azure-Identität, um Cognitive Search Zugriff auf andere Azure-Dienste wie Key Vault und Indexerdatenquellen zu gewähren, indem Sie eine automatisch verwaltete Identität in Azure Active Directory (Azure AD) verwenden. Mit verwalteten Identitäten können Sie die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt, einschließlich Azure Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. 

- [Einrichten einer Indexerverbindung mit einer Datenquelle mithilfe einer verwalteten Identität](./search-howto-managed-identities-data-sources.md)

- [Konfigurieren von kundenseitig verwalteten Schlüsseln für die Datenverschlüsselung mithilfe einer verwalteten Identität](./search-security-manage-encryption-keys.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Leitfaden**: Trifft für Cognitive Search nicht zu. Es wird kein Code gehostet, und es sind keine Anmeldeinformationen zur Identifizierung vorhanden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: Verwenden zentral verwalteter Antischadsoftware

**Leitfaden**: Trifft für Cognitive Search nicht zu. Diese Empfehlung ist für Computeressourcen vorgesehen.

Microsoft Antimalware ist auf dem zugrunde liegenden Host für Azure-Dienste (z. B. Azure Cognitive Search) aktiviert, wird jedoch nicht für Kundeninhalte ausgeführt.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Führen Sie eine Vorabprüfung aller Inhalte durch, die in computefremde Azure-Ressourcen hochgeladen werden sollen, z. B. Cognitive Search, Blob Storage, Azure SQL-Datenbank usw. 

Es liegt in Ihrer Verantwortung, alle Inhalte vorab zu überprüfen, die auf computefremde Azure-Ressourcen hochgeladen werden. Microsoft kann nicht auf Kundendaten zugreifen und kann daher keine Antischadsoftwarescans von Kundeninhalten in Ihrem Namen durchführen.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Trifft für Cognitive Search nicht zu. Es können keine Antischadsoftwarelösungen auf den Ressourcen installiert werden. Für die zugrunde liegende Plattform übernimmt Microsoft das Aktualisieren von Antischadsoftwaresoftware und Signaturen. 

Befolgen Sie für alle Computeressourcen, die sich im Besitz Ihrer Organisation befinden und in Ihrer Suchlösung verwendet werden, Empfehlungen in Compute &amp; Apps von Security Center, um sicherzustellen, dass alle Endpunkte mit den neuesten Signaturen auf dem neuesten Stand sind. Verwenden Sie für Linux eine Antischadsoftware eines Drittanbieters.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Inhalte, die in einem Suchdienst gespeichert sind, können nicht über Azure Backup oder einen anderen integrierten Mechanismus gesichert werden. Sie können jedoch einen Index aus dem Quellcode der Anwendung und den primären Datenquellen neu erstellen oder ein benutzerdefiniertes Tool erstellen, um indizierte Inhalte abzurufen und zu speichern.

- [Beispiel für das Sichern und Wiederherstellen eines Index in GitHub](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/index-backup-restore)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller kundenseitig verwalteten Schlüssel

**Leitfaden**: Cognitive Search unterstützt derzeit keine automatisierte Sicherung von Daten in einem Suchdienst und muss über einen manuellen Prozess gesichert werden.  Sie können auch kundenseitig verwaltete Schlüssel in Azure Key Vault sichern. 

- [Sichern und Wiederherstellen eines Azure Cognitive Search-Index](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Sichern von Schlüsseltresorschlüsseln in Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich kundenseitig verwalteter Schlüssel

**Leitfaden**: Cognitive Search unterstützt derzeit keine automatisierte Sicherung von Daten in einem Suchdienst und muss über einen manuellen Prozess gesichert und wiederhergestellt werden.  Führen Sie regelmäßig eine Datenwiederherstellung von Inhalten aus, die Sie manuell gesichert haben, um die End-to-End-Integrität Ihres Sicherungsprozesses zu gewährleisten.

- [Sichern und Wiederherstellen eines Azure Cognitive Search-Index](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Wiederherstellen von Key Vault-Schlüsseln in Azure](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Cognitive Search unterstützt derzeit keine automatisierte Sicherung von Daten in einem Suchdienst und muss über einen manuellen Prozess gesichert werden.  Sie können auch kundenseitig verwaltete Schlüssel in Azure Key Vault sichern. 

Aktivieren Sie vorläufiges Löschen und den Löschschutz in Key Vault, um Schlüssel vor versehentlichem oder böswilligem Löschen zu schützen. Wenn Azure Storage zum Speichern manueller Sicherungen verwendet wird, aktivieren Sie vorläufiges Löschen, um Ihre Daten zu speichern und wiederherzustellen, wenn Blobs oder Blobmomentaufnahmen gelöscht werden. 

- [Sichern und Wiederherstellen eines Azure Cognitive Search-Index](/samples/azure-samples/azure-search-dotnet-samples/azure-search-backup-restore-index/)

- [Aktivieren des vorläufigen Löschens und des Löschschutzes in Key Vault](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

- [Vorläufiges Löschen für Azure Blob Storage](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Leitfaden**: Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle in Ihrer Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind.

- [Anleitung zum Entwickeln eines Prozesses für die Reaktion auf Sicherheitsvorfälle](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Struktur eines Vorfalls laut Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Kunden können außerdem den Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) nutzen, um einen Plan zur Reaktion auf Incidents auszuarbeiten.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Anleitung:** Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie sicher Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

Markieren Sie Abonnements außerdem mithilfe von Tags, und erstellen Sie ein Benennungssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren, insbesondere solche, die vertrauliche Daten verarbeiten. Die Priorisierung der Behebung von Warnungen basierend auf der Wichtigkeit der Azure-Ressourcen und der Umgebung, in der der Vorfall aufgetreten ist, liegt in Ihrer Verantwortung.

- [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md)

- [Sicherheitswarnungen in Azure Security Center](../security-center/security-center-alerts-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Tests zur Reaktionsfähigkeit Ihrer Systeme auf Vorfälle durch. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf.

- [Siehe NIST-Veröffentlichung: „Leitfaden zum Testen, Trainieren und Ausführen von Programmen für IT-Pläne und -Funktionen“](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf Ihre Daten zugegriffen haben. Überprüfen Sie die Vorfälle anschließend, um sicherzustellen, dass die Probleme behoben wurden.

- [Festlegen der Kontaktinformationen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie Ihre Security Center-Warnungen und -Empfehlungen mithilfe des Features „Fortlaufender Export“. Über die Funktion „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Security Center-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen.

- [Konfigurieren des fortlaufenden Exports](../security-center/continuous-export.md)

- [Streamen von Warnungen in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um über „Logic Apps“ automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen.

- [Konfigurieren von Workflowautomatisierung und Logic Apps](../security-center/workflow-automation.md)

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Durchführen regelmäßiger Penetrationstests Ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnissen

**Leitfaden**: Befolgen Sie in dem Fall die Einsatzregeln für Penetrationstests von Microsoft Cloud, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen. Nutzen Sie die Microsoft-Strategie und Durchführung von Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastruktur, Dienste und Anwendungen.
- [Penetrationstests – Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)
- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).