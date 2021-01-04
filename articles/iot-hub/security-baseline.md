---
title: Azure-Sicherheitsbaseline für Azure IoT Hub
description: Die Azure IoT Hub-Sicherheitsbaseline enthält Schrittanleitungen und Ressourcen für die Implementierung der Sicherheitsempfehlungen, die im Vergleichstest für die Azure-Sicherheit angegeben sind.
author: msmbaldwin
ms.service: iot-hub
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 891366060cddaf2a046de36fa9300432977ec903
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96457734"
---
# <a name="azure-security-baseline-for-azure-iot-hub"></a>Azure-Sicherheitsbaseline für Azure IoT Hub

Die Azure-Sicherheitsbaseline für Microsoft Azure IoT Hub enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können. Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können. Weitere Informationen finden Sie unter [Übersicht über Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Schützen von Azure-Ressourcen in virtuellen Netzwerken

**Leitfaden**: Standardmäßig werden IoT Hub-Hostnamen einem öffentlichen Endpunkt mit einer öffentlich routingfähigen IP-Adresse über das Internet zugeordnet. Dieser öffentliche IoT Hub-Endpunkt wird von mehreren Kunden gemeinsam genutzt, und sämtliche IoT-Geräte in WANs und lokalen Netzwerken können darauf zugreifen.

IoT Hub-Features wie das Nachrichtenrouting, der Dateiupload und das Importieren und Exportieren von Geräten per Massenvorgang erfordern ebenfalls Konnektivität zwischen IoT Hub und einer kundeneigenen Azure-Ressource über den öffentlichen Endpunkt. Diese Konnektivitätspfade bilden gemeinsam den ausgehenden Datenverkehr von IoT Hub zu Kundenressourcen.

Es empfiehlt sich, die Konnektivität mit Ihren Azure-Ressourcen (einschließlich Azure IoT Hub) über ein virtuelles Netzwerk, das Sie besitzen und betreiben, einzuschränken. So können Sie die Konnektivität in einem isolierten Netzwerk reduzieren und lokale Netzwerkkonnektivität direkt mit dem Azure-Backbone-Netzwerk ermöglichen. Verwenden Sie, wenn möglich, Azure Private Link und den privaten Endpunkt in Azure, um privaten Zugriff auf Ihre Dienste aus anderen virtuellen Netzwerken zu ermöglichen.

Beschränken Sie geöffnete Hardwareanschlüsse in Ihren Geräten auf ein absolutes Minimum, um unerwünschten Zugriff zu vermeiden. Entwickeln Sie darüber hinaus Mechanismen, um physische Manipulationen am Gerät zu verhindern oder zu erkennen.

- [Unterstützung für virtuelle IoT-Netzwerke](virtual-network-support.md)
- [Bewährte Methoden für IoT-Netzwerke](../iot-fundamentals/security-recommendations.md?context=azure%2fiot-hub%2frc%2frc#networking)
- [Übersicht über Azure Private Link](../private-link/private-link-overview.md)
- [Azure-Netzwerksicherheitsgruppe](../virtual-network/network-security-groups-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von virtuellen Netzwerken, Subnetzen und Netzwerkkarten (NICs)

**Leitfaden**: Verwenden Sie Azure Security Center, und befolgen Sie die Empfehlungen für den Netzwerkschutz, um Ihre Azure-Netzwerkressourcen abzusichern. Aktivieren Sie Netzwerksicherheitsgruppen-Datenflussprotokolle, und senden Sie Protokolle zur Überwachung an ein Azure Storage-Konto. Sie können die Datenflussprotokolle auch an einen Log Analytics-Arbeitsbereich senden und dann mithilfe von Traffic Analytics Einblicke in die Datenverkehrsmuster in Ihrer Azure-Cloud ermöglichen. Einige Vorteile von Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren, Hotspots und Sicherheitsbedrohungen zu erkennen, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.
 
- [Aktivieren von Datenflussprotokollen für Netzwerksicherheitsgruppen](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Aktivieren und Verwenden von Traffic Analytics](../network-watcher/traffic-analytics.md)
 
- [Grundlegendes zu der von Azure Security Center bereitgestellten Netzwerksicherheit](../security-center/security-center-network-recommendations.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten bösartigen IP-Adressen

**Leitfaden**: Blockieren Sie bekannte bösartige IP-Adressen mit IoT Hub-Filterregeln für IP-Adressen. Bösartige Versuche werden auch über Azure Security Center für IoT aufgezeichnet, und es wird davor gewarnt.

Azure DDoS Protection Basic ist bereits aktiviert und steht im Rahmen von IoT Hub ohne zusätzliche Kosten zur Verfügung. Die stets verfügbare Überwachung des Datenverkehrs und die Abwehr von häufig vorkommenden Angriffen auf Netzwerkebene in Echtzeit bieten die gleichen Schutzmaßnahmen wie die Onlinedienste von Microsoft.  Das gesamte weltweite Netzwerk von Azure steht für die Verteilung und Abwehr des regionsübergreifenden Angriffsdatenverkehrs zur Verfügung.

- [IoT Hub-Filter für IP-Adressen](iot-hub-ip-filtering.md)

- [Azure Security Center für verdächtige IoT-IP-Adressenkommunikation](../defender-for-iot/concept-security-alerts.md)

- [Verwalten von Azure DDoS Protection Basic](../ddos-protection/ddos-protection-overview.md)

- [Bedrohungsschutz in Azure Security Center](../security-center/azure-defender.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets"></a>1.5: Aufzeichnen von Netzwerkpaketen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Angebote zur Erzeugung von Netzwerkpaketen vorgesehen, die von Kunden aufgezeichnet und angezeigt werden können. IoT Hub erzeugt keine Netzwerkpakete für Kunden und ist nicht für die direkte Bereitstellung in virtuellen Azure-Netzwerken konzipiert.

**Azure Security Center-Überwachung**: Nein

**Verantwortlichkeit**: Nicht zutreffend

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (Intrusion Detection/Intrusion Prevention Systems, IDS/IPS)

**Leitfaden**: Wählen Sie ein Angebot aus Azure Marketplace aus, das IDS-/IPS-Funktionen mit Funktionen zur Nutzlastuntersuchung unterstützt.  Wenn Nutzlastuntersuchung keine Anforderung ist, kann Azure Firewall Threat Intelligence verwendet werden. Die auf Azure Firewall Threat Intelligence basierende Filterung wird verwendet, um bei bekannten böswilligen IP-Adressen und Domänen zu warnen und/oder Datenverkehr zu und von diesen Adressen oder Domänen zu blockieren. Die IP-Adressen und Domänen stammen aus dem Microsoft Threat Intelligence-Feed.

Stellen Sie die Firewalllösung Ihrer Wahl an allen Netzwerkgrenzen Ihrer Organisation bereit, um schädlichen Datenverkehr zu erkennen und/oder zu blockieren. 

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Bereitstellen von Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [Konfigurieren von Warnungen mit Azure Firewall](../firewall/threat-intel.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie für Ressourcen, die auf Ihren Azure IoT Hub zugreifen müssen, Diensttags in virtuellen Netzwerken, um Netzwerkzugriffssteuerungen für Netzwerksicherheitsgruppen oder Azure Firewall zu definieren. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Wenn Sie den Diensttagnamen (z. B. „AzureIoTHub“) im jeweiligen Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

- [Verwenden von Diensttags für Azure IoT](iot-hub-understand-ip-address.md)
- [Weitere Informationen zur Verwendung von Diensttags](../virtual-network/service-tags-overview.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie mit Azure Policy Standardsicherheitskonfigurationen für Netzwerkressourcen, die Ihren Azure IoT Hub-Namespaces zugeordnet sind. Verwenden Sie Azure Policy-Aliase in den Namespaces „Microsoft.Devices“ und „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihrer Machine Learning-Namespaces zu erstellen. 

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Verwenden Sie Tags für Netzwerkressourcen, die Ihrer Azure IoT Hub-Bereitstellung zugeordnet sind, um sie in einer Taxonomie logisch zu organisieren.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen der Konfigurationen von Netzwerkressourcen und zum Erkennen von Änderungen bei Netzwerkressourcen, die sich auf Azure IoT Hub beziehen. Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkressourcen ausgelöst werden.

- [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Erstellen von Warnungen in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Microsoft verwaltet die für Azure-Ressourcen wie Azure IoT Hub verwendete Zeitquelle für Zeitstempel in den Protokollen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Erfassen Sie Protokolle über Azure Monitor zum Aggregieren der von Azure IoT Hub generierten Sicherheitsdaten. Verwenden Sie in Azure Monitor Log Analytics-Arbeitsbereiche zum Abfragen und Ausführen von Analysen, und verwenden Sie Azure Storage-Konten für langfristige Speicherung/Archivierung. Alternativ dazu können Sie auch Daten in Azure Sentinel oder einer Security Incident & Event Management-Lösung (SIEM) eines Drittanbieters aktivieren und integrieren.

- [Einrichten von Azure IoT-Protokollen](monitor-iot-hub-reference.md#resource-logs)
- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Aktivieren Sie die Azure IoT-Diagnoseeinstellungen für Azure-Ressourcen für den Zugriff auf Überwachungs-, Sicherheits- und Ressourcenprotokolle. Aktivitätsprotokolle, die automatisch verfügbar sind, enthalten Ereignisquelle, Datum, Benutzer, Zeitstempel, Quelladressen, Zieladressen und andere nützliche Elemente.

- [Einrichten von Azure IoT Hub-Protokollen](monitor-iot-hub-reference.md#resource-logs)

- [Sammeln von Plattformprotokollen und -metriken mit Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [Grundlegendes zur Protokollierung und zu verschiedenen Protokolltypen in Azure](../azure-monitor/platform/platform-logs-overview.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Legen Sie in Azure Monitor die Protokollaufbewahrungsdauer für Log Analytics-Arbeitsbereiche, die Ihren Azure IoT Hub-Instanzen zugeordnet sind, entsprechend den Compliancevorschriften Ihrer Organisation fest.

- [Ändern des Datenaufbewahrungszeitraums](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Analysieren und überwachen Sie Protokolle auf anomales Verhalten, und überprüfen Sie regelmäßig die Ergebnisse aus Ihrem Azure IoT Hub. Verwenden Sie Azure Monitor und einen Log Analytics-Arbeitsbereich, um Protokolle zu überprüfen und Abfragen für die Protokolldaten auszuführen.

Alternativ können Sie auch Daten in Azure Sentinel oder einer SIEM-Drittanbieterlösung aktivieren und integrieren. 

- [Überwachen der Azure IoT-Integrität](monitor-iot-hub.md)
- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)
  
- [Erste Schritte mit Log Analytics-Abfragen](../azure-monitor/log-query/log-analytics-tutorial.md)
   
- [Ausführen benutzerdefinierter Abfragen in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Aktivieren von Warnungen bei anomalen Aktivitäten

**Leitfaden**: Verwenden Sie Azure Security Center für IoT bei einem Log Analytics-Arbeitsbereich für die Überwachung und Warnung bei anomalen Aktivitäten, die in Sicherheitsprotokollen und Ereignissen gefunden werden. Alternativ dazu können Sie auch Daten in Azure Sentinel aktivieren und integrieren. Sie können auch Betriebswarnungen mit Azure Monitor definieren, die möglicherweise Auswirkungen auf die Sicherheit haben, z. B. bei einem unerwarteten Ausfall des Datenverkehrs.

- [Überwachen der Azure IoT Hub-Integrität](monitor-iot-hub.md)
- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)
- [Azure Security Center für IoT-Warnungen](../defender-for-iot/concept-security-alerts.md)

- [Warnungen bei Log Analytics-Protokolldaten](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Nicht zutreffend. In Azure IoT Hub werden keine Protokolle zu Antischadsoftware verarbeitet oder erstellt.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Nicht zutreffend. In Azure IoT Hub werden keine DNS-bezogenen Protokolle verarbeitet oder erstellt.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Mit der rollenbasierten Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) kann der Zugriff auf Azure IoT Hub über Rollenzuweisungen verwaltet werden. Diese Rollen können Benutzern, Gruppen, Dienstprinzipalen und verwalteten Identitäten zugewiesen werden. Für bestimmte Ressourcen gibt es vordefinierte integrierte Rollen. Diese Rollen können über Tools wie die Azure CLI, Azure PowerShell oder das Azure-Portal inventarisiert oder abgefragt werden. 

- [Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Die Zugriffsverwaltung für Azure IoT Hub-Ressourcen wird über Azure Active Directory (Azure AD) gesteuert. Azure AD verfügt nicht über das Konzept von Standardkennwörtern.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Erstellen Sie Standardbetriebsvorgänge für die Verwendung dedizierter Administratorkonten.

Sie können auch den Just-In-Time-Zugriff auf Verwaltungskonten mithilfe von Azure AD Privileged Identity Management und Azure Resource Manager aktivieren.

- [Weitere Informationen zu Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden**: Verwenden Sie für Benutzer, die auf IoT Hub zugreifen, Azure Active Directory SSO. Wenden Sie die Empfehlungen für die Identität und den Zugriff in Azure Security Center an. 

- [Grundlegendes zu SSO mit Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie Azure AD MFA zum Schutz Ihres gesamten Azure-Mandanten, und profitieren Sie dabei von allen Diensten. Beim IoT Hub-Dienst gibt es keinen MFA-Support.  

- [Aktivieren von MFA in Azure](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Überwachen von Identität und Zugriff in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie eine sichere, von Azure verwaltete Arbeitsstation (auch als Arbeitsstation mit privilegiertem Zugriff, Privileged Access Workstation (PAW) bezeichnet) für Verwaltungsaufgaben, für die erhöhte Rechte benötigt werden.

- [Informationen zu sicheren, von Azure verwalteten Arbeitsstationen](../active-directory/devices/concept-azure-managed-workstation.md)

- [Planen einer Bereitstellung von Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Protokollieren von und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Verwenden Sie Azure Active Directory-Sicherheitsberichte und -Überwachungsfunktionen, um verdächtige oder nicht sichere Aktivität in der Umgebung zu ermitteln. Verwenden Sie Azure Security Center zum Überwachen von identitäts- und zugriffsbezogenen Aktivitäten.

- [Identifizieren von Azure AD-Benutzern, die aufgrund riskanter Aktivitäten gekennzeichnet wurden](../active-directory/identity-protection/overview-identity-protection.md)
- [Überwachen der identitäts- und zugriffsbezogenen Aktivitäten von Benutzern in Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Bei Benutzern, die auf IoT Hub zugreifen, wird bedingter Zugriff nicht unterstützt. Verwenden Sie zur Entschärfung dieses Problems benannte Azure AD-Standorte, um den Zugriff nur aus bestimmten logischen Gruppierungen von IP-Adressbereichen oder Ländern/Regionen für Ihren gesamten Azure-Mandanten zuzulassen, und profitieren Sie dabei von allen Diensten, einschließlich IoT Hub. 

- [Schnellstart: Konfigurieren benannter Orte in Azure Active Directory](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (Azure AD) als zentrales Authentifizierungs- und Autorisierungssystem für den Benutzerzugriff auf IoT Hub. Azure AD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in Azure AD Salts und Hashs für Anmeldeinformationen verwendet und diese sicher gespeichert.

IoT Hub verwendet für den Geräte- und Dienstzugriff Sicherheitstoken und Shared Access Signature (SAS)-Token zum Authentifizieren von Geräten und Diensten, um das Senden von Schlüsseln über das Netzwerk zu vermeiden. 

- [Erstellen und Konfigurieren einer Azure AD-Instanz](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)
- [IoT Hub-Sicherheitstoken](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Azure AD enthält Protokolle zum Ermitteln von veralteten Konten. Verwenden Sie zusätzlich Azure AD-Identitäts und -Zugriffsüberprüfungen, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen sowie Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin über Zugriff verfügen. 

Verwenden Sie Azure AD Privileged Identity Management (PIM) für die Generierung von Protokollen und Warnungen bei verdächtigen oder unsicheren Aktivitäten in der Umgebung.

- [Grundlegendes zur Azure AD-Berichterstellung](../active-directory/reports-monitoring/index.yml)
- [Was sind Azure AD-Zugriffsüberprüfungen?](../active-directory/governance/access-reviews-overview.md)
- [Bereitstellen von Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Anmeldeinformationen

**Leitfaden**: Sie haben Zugriff auf Azure AD-Anmeldeaktivitäten und Überwachungs- und Risikoereignisse, sodass die Integration in jedes SIEM- und Überwachungstool möglich ist. 

Sie können diesen Prozess optimieren, indem Sie Diagnoseeinstellungen für Azure AD-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Log Analytics-Arbeitsbereich senden. Sie können gewünschte Warnungen im Log Analytics-Arbeitsbereich konfigurieren. 

Verwenden Sie Azure Monitor-Ressourcenprotokolle zur Überwachung nicht autorisierter Verbindungsversuche in der Kategorie „Verbindungen“.

- [Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Konfigurieren von Ressourcenprotokollen für IoT-Hub](monitor-iot-hub.md#collection-and-routing)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Mit den Azure AD Identity Protection-Funktionen können Sie automatische Antworten auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten konfigurieren. Außerdem können Sie Daten zur weiteren Untersuchung in Azure Sentinel erfassen.
  
- [Anzeigen riskanter Azure AD-Anmeldungen](../active-directory/identity-protection/overview-identity-protection.md)
  
- [Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)
  
- [Durchführen des Onboardings für Azure Sentinel](../sentinel/quickstart-onboard.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: In Supportszenarien, in denen Microsoft auf Kundendaten zugreifen muss, werden diese direkt vom Kunden angefordert. 

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

## <a name="data-protection"></a>Schutz von Daten

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenschutz](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für die Nachverfolgung von Azure-Ressourcen, die vertrauliche Informationen speichern oder verarbeiten.
 
- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie eine Isolation mit separaten Abonnements und Verwaltungsgruppen für einzelne Sicherheitsdomänen, z. B. Umgebungstyp und Datenvertraulichkeitsstufe. Sie können die Zugriffsebene auf diejenigen Ihrer Azure-Ressourcen beschränken, die von Ihren Anwendungen und Unternehmensumgebungen gefordert werden. Sie können den Zugriff auf Azure-Ressourcen über die rollenbasierte Zugriffssteuerung in Azure steuern.
  
- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)
- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)
- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Verwenden Sie eine Drittanbieterlösung aus Azure Marketplace an Netzwerkumkreisen, die die nicht autorisierte Übertragung von vertraulichen Informationen überwacht, derartige Übertragungen blockiert und gleichzeitig die Informationssicherheitsexperten benachrichtigt.

Bei der von Microsoft verwalteten zugrunde liegenden Plattform behandelt Microsoft alle Kundeninhalte als vertraulich und schützt Kundendaten vor Verlust und Offenlegung. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: IoT Hub verwendet Transport Layer Security (TLS) zum Sichern von Verbindungen mit IoT-Geräten und -Diensten. Derzeit werden drei Versionen des TLS-Protokolls unterstützt, nämlich die Versionen 1.0, 1.1 und 1.2. Es wird dringend empfohlen, beim Herstellen einer Verbindung mit IoT Hub TLS 1.2 als bevorzugte TLS-Version zu verwenden.

Befolgen Sie ggf. die Empfehlungen von Azure Security Center zur Verschlüsselung ruhender Daten und zur Verschlüsselung während der Übertragung.

- [TLS-Unterstützung in IoT Hub](iot-hub-tls-support.md)
- [Grundlegendes zur Verschlüsselung während der Übertragung mit Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Features für Datenidentifizierung und -klassifizierung sowie die Vermeidung von Datenverlusten sind für Azure IoT Hub noch nicht verfügbar. Implementieren Sie eine Drittanbieterlösung, wenn dies für Compliancezwecke erforderlich ist.

Bei der von Microsoft verwalteten zugrunde liegenden Azure-Plattform behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Offenlegung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Verwalten des Ressourcenzugriffs

**Leitfaden**: Verwenden Sie für den Benutzerzugriff auf Steuerungsebene auf IoT Hub Azure RBAC zum Steuern des Zugriffs. Verwenden Sie für den Zugriff auf Datenebene auf IoT Hub freigegebene Zugriffsrichtlinien für IoT Hub.

- [Konfigurieren von Azure RBAC](../role-based-access-control/role-assignments-portal.md)

- [Verwalten des Zugriffs auf IoT Hub](iot-hub-devguide-security.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

Microsoft verwaltet die zugrunde liegende Infrastruktur für Azure IoT Hub und hat strenge Kontrollen implementiert, um den Verlust oder die Offenlegung von Kundendaten zu verhindern.

- [Grundlegendes zum Schutz von Kundendaten in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: IoT Hub unterstützt die Verschlüsselung ruhender Daten mit kundenseitig verwalteten Schlüsseln (Customer-Managed Keys, CMK), auch bezeichnet als „Bring Your Own Key“ (BYOK).

Azure IoT Hub bietet Verschlüsselung von ruhenden Daten und Daten während der Übertragung, wenn sie in unseren Rechenzentren geschrieben werden, und entschlüsselt sie, wenn Sie darauf zugreifen. Standardmäßig verwendet IoT Hub von Microsoft verwaltete Schlüssel, um die ruhenden Daten zu verschlüsseln.

- [Verschlüsselung ruhender Daten mit vom Kunden verwalteten Schlüsseln für IoT Hub](iot-hub-customer-managed-keys.md)

- [Grundlegendes zur Verschlüsselung ruhender Daten in Azure](../security/fundamentals/encryption-atrest.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Monitor mit dem Azure-Aktivitätsprotokoll zum Erstellen von Warnungen für den Fall, dass Änderungen an Produktionsinstanzen von Azure IoT Hub und anderen kritischen oder verwandten Ressourcen vorgenommen werden.

- [Erstellen von Warnungen für Ereignisse des Azure-Aktivitätsprotokolls](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Nicht zutreffend. Microsoft führt die Verwaltung von Sicherheitsrisiken auf den zugrunde liegenden Systemen aus, die Azure IoT Hub unterstützen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Leitfaden**: Nicht zutreffend. Microsoft führt die Patchverwaltung auf den zugrunde liegenden Systemen aus, die Azure IoT Hub unterstützen. 

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Microsoft

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Bereitstellen einer automatisierten Patchverwaltungslösung für Softwaretitel von Drittanbietern

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Verwenden der automatisierten Asset Discovery-Lösung

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Azure-Ressourcen an (nicht alle Ressourcen – aber die meisten – unterstützen Tags), um sie in einer Taxonomie logisch zu organisieren.

- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie nach Bedarf Tagging, Verwaltungsgruppen und separate Abonnements zum Organisieren und Nachverfolgen von Ressourcen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.
  
- [Erstellen zusätzlicher Azure-Abonnements](../cost-management-billing/manage/create-subscription.md)
  
- [Erstellen von Verwaltungsgruppen](../governance/management-groups/create-management-group-portal.md)
  
- [Erstellen und Verwenden von Tags](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definieren und Verwalten eines Bestands an genehmigten Azure-Ressourcen

**Leitfaden**: Erstellen Sie einen Bestand an genehmigten Azure-Ressourcen und genehmigter Software für Computeressourcen gemäß den Anforderungen Ihrer Organisation.

Bei jedem IoT Hub gibt es eine Identitätsregistrierung, die zum Erstellen von gerätebasierten Ressourcen im Dienst verwendet werden kann. Geräteidentitäten können einzeln oder in Gruppen einer Zulassungsliste oder Blockierungsliste hinzugefügt werden, um die vollständige Kontrolle über den Gerätezugriff zu haben.

- [IoT Hub-Identitätsregistrierung](iot-hub-devguide-identity-registry.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen festzulegen, die in Ihren Abonnements erstellt werden können. 

Verwenden Sie Azure Resource Graph, um Ressourcen in ihren Abonnements abzufragen und zu ermitteln.  Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind. 

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe des Azure Resource Graph-Explorers](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Überwachen auf nicht genehmigte Softwareanwendungen innerhalb von Computeressourcen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="68-use-only-approved-applications"></a>6.8: Ausschließliche Verwendung genehmigter Anwendungen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie dazu die folgenden integrierten Richtliniendefinitionen:

* Not allowed resource types (Unzulässige Ressourcentypen)
* Zulässige Ressourcentypen

Verwenden Sie darüber hinaus Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Erstellen von Abfragen mit Azure Graph](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Verwalten eines Bestands an genehmigten Softwaretiteln

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Einschränken der Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager

**Leitfaden**: Mithilfe des bedingten Azure AD-Zugriffs können Sie die Möglichkeiten von Benutzern zur Interaktion mit Azure Resource Manager einschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.
  
- [Konfigurieren des bedingten Zugriffs, um den Zugriff auf Azure Resource Manager zu blockieren](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Einschränken der Skriptausführung durch Benutzer in Computeressourcen

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Anleitung:** Nicht zutreffend. Diese Empfehlung ist für Webanwendungen gedacht, die in Azure App Service oder auf Computeressourcen ausgeführt werden.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für Ihren Azure IoT Hub-Dienst mit Azure Policy. Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.Devices“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration Ihrer Azure IoT Hub-Dienste zu erstellen.

Mit Azure Resource Manager ist es möglich, die Vorlage in JSON-Code (JavaScript Object Notation) zu exportieren. Dieser sollte überprüft werden, um sicherzustellen, dass die Konfigurationen die Sicherheitsanforderungen für Ihre Organisation erfüllen.

Sie können auch die Empfehlungen von Azure Security Center als sichere Konfigurationsbaseline für Ihre Azure-Ressourcen verwenden.

- [Anzeigen verfügbarer Azure Policy-Aliase](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)

- [Exportieren von einzelnen oder mehreren Ressourcen in eine Vorlage im Azure-Portal](../azure-resource-manager/templates/export-template-portal.md)

- [Sicherheitsempfehlungen: Referenzhandbuch](../security-center/recommendations-reference.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure Policy-Auswirkungen [Deny] und [DeployIfNotExists], um sichere Einstellungen für alle Ihre Azure-Ressourcen zu erzwingen. Darüber hinaus können Sie Azure Resource Manager-Vorlagen verwenden, um die für Ihre Organisation erforderliche Sicherheitskonfiguration der Azure-Ressourcen zu verwalten.  
 
- [Grundlegendes zu Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)
- [Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)
- [Übersicht über Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/overview.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Wenn Sie benutzerdefinierte Azure Policy-Definitionen für Ihre Azure IoT Hub-Ressourcen oder verwandte Ressourcen verwenden, nutzen Sie Azure Repos zum sicheren Speichern und Verwalten Ihres Codes.

- [Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops)
- [Dokumentation zu Azure Repos](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Bereitstellen von Konfigurationsverwaltungstools für Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.Devices“, um benutzerdefinierte Richtlinien zum Überwachen und Erzwingen von Systemkonfigurationen zu erstellen sowie Warnungen dazu zu erhalten. Entwickeln Sie außerdem einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

- [Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)
- [Verwenden von Aliasen](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Konfigurationsverwaltungstools für Betriebssysteme

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Security Center, um Baselineüberprüfungen der Azure-Ressourcen durchzuführen. Nutzen Sie Azure Policy auch für Warnungen und Überprüfungen in Bezug auf Azure-Ressourcenkonfigurationen. 
 
- [Umsetzen von Empfehlungen in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Leitfaden**: Nicht zutreffend. Diese Leitlinie ist für Computeressourcen vorgesehen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Nicht zutreffend

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden**: IoT Hub verwendet Sicherheitstoken und Shared Access Signature (SAS)-Token zum Authentifizieren von Geräten und Diensten, um das Senden von Schlüsseln über das Netzwerk zu vermeiden. 

Verwenden Sie verwaltete Identitäten in Verbindung mit Azure Key Vault, um die Verwaltung von Geheimnissen für Cloudanwendungen zu vereinfachen.

- [IoT Hub-Sicherheitstoken](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)
- [Verwenden von verwalteten Identitäten für IoT Hub](virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

- [Erstellen eines Schlüsseltresors](../key-vault/general/quick-create-portal.md)
- [Bereitstellen der Key Vault-Authentifizierung mit einer verwalteten Identität](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: IoT Hub verwendet Sicherheitstoken und Shared Access Signature (SAS)-Token zum Authentifizieren von Geräten und Diensten, um das Senden von Schlüsseln über das Netzwerk zu vermeiden. 

Verwenden Sie verwaltete Identitäten, um Azure-Dienste mit einer automatisch verwalteten Identität in Azure AD bereitzustellen. Mithilfe verwalteter Identitäten können Sie die Authentifizierung bei jedem Dienst durchführen, der die Azure AD-Authentifizierung unterstützt, einschließlich Key Vault. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein.

- [IoT Hub-Sicherheitstoken](../iot-fundamentals/iot-security-deployment.md#iot-hub-security-tokens)
- [Konfigurieren von verwalteten Identitäten für IoT Hub](virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Anleitung:** Implementieren Sie Credential Scanner, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen. 
 
- [Einrichten von Credential Scanner](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: Verwenden zentral verwalteter Antischadsoftware

**Leitfaden**: Nicht zutreffend. Diese Empfehlung ist für Computeressourcen vorgesehen.

Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure App Service) aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste (z. B. Azure IoT Hub) aktiviert, wird aber für Kundeninhalte nicht ausgeführt.

Es liegt in Ihrer Verantwortung, alle Inhalte vorab zu überprüfen, die auf computefremde Azure-Ressourcen hochgeladen werden. Microsoft kann nicht auf Kundendaten zugreifen und kann daher keine Antischadsoftwarescans von Kundeninhalten in Ihrem Namen durchführen.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Nicht zutreffend. Diese Benchmark ist für Computeressourcen vorgesehen. Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Microsoft

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Der Azure IoT Hub-Dienst stellt Methoden und ein Framework bereit, um Ihre IoT Hub-Dienste hoch verfügbar und bei Notfällen aufgrund von bestimmten Geschäftszielen wiederherstellbar zu machen. 

- [Hochverfügbarkeit und Notfallwiederherstellung von IoT Hub](iot-hub-ha-dr.md)

- [Klonen von IoT Hub](iot-hub-how-to-clone.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller kundenseitig verwalteten Schlüssel

**Leitfaden**: Azure IoT Hub empfiehlt, dass der sekundäre IoT Hub alle Geräteidentitäten enthalten muss, die eine Verbindung mit der Lösung herstellen können. Für die Lösung sollten georeplizierte Backups von Geräteidentitäten vorgehalten und auf die sekundäre IoT Hub-Einheit hochgeladen werden, bevor der aktive Endpunkt für die Geräte gewechselt wird. Die Funktionen zum Exportieren der Geräteidentität von IoT Hub sind in diesem Zusammenhang praktisch.

- [Hochverfügbarkeit und Notfallwiederherstellung von IoT Hub](iot-hub-ha-dr.md#achieve-cross-region-ha)

- [Exportieren der Geräteidentität von IoT Hub](iot-hub-bulk-identity-mgmt.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich kundenseitig verwalteter Schlüssel

**Leitfaden**: Azure IoT Hub empfiehlt, dass der sekundäre IoT Hub alle Geräteidentitäten enthalten muss, die eine Verbindung mit der Lösung herstellen können. Für die Lösung sollten georeplizierte Backups von Geräteidentitäten vorgehalten und auf die sekundäre IoT Hub-Einheit hochgeladen werden, bevor der aktive Endpunkt für die Geräte gewechselt wird. Die Funktionen zum Exportieren der Geräteidentität von IoT Hub sind in diesem Zusammenhang praktisch.

Führen Sie die Datenwiederherstellung von Inhalten im Backup regelmäßig aus. Stellen Sie sicher, dass gesicherte kundenseitig verwaltete Schlüssel wiederhergestellt werden können.

- [Hochverfügbarkeit und Notfallwiederherstellung von IoT Hub](iot-hub-ha-dr.md#achieve-cross-region-ha)

- [Exportieren der Geräteidentität von IoT Hub](iot-hub-bulk-identity-mgmt.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Aktivieren Sie vorläufiges Löschen und den Löschschutz in Key Vault, um Schlüssel vor versehentlichem oder böswilligem Löschen zu schützen. Wenn Azure Storage zum Speichern von Sicherungen verwendet wird, aktivieren Sie vorläufiges Löschen, um Ihre Daten zu speichern und wiederherzustellen, wenn Blobs oder Blobmomentaufnahmen gelöscht werden.
 
 
- [Grundlegendes zu Azure RBAC](../role-based-access-control/overview.md)
- [Vorläufiges Löschen für Azure Blob Storage](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Leitfaden**: Erarbeiten Sie einen Leitfaden für die Reaktion auf Vorfälle in Ihrer Organisation. Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen alle Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen von der Ermittlung bis zur abschließenden Überprüfung definiert sind. 
  
- [Anleitung zum Entwickeln Ihres eigenen Prozesses für die Reaktion auf Sicherheitsvorfälle](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
  
- [Struktur eines Vorfalls laut Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
 
- [Nutzen des Computer Security Incident Handling Guide des US-amerikanischen National Institute of Standards and Technology (NIST) als Hilfe bei der Entwicklung Ihres eigenen Plans zur Reaktion auf Vorfälle](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Leitfaden**: Azure Security Center weist jeder Warnung einen Schweregrad zu, damit Sie priorisieren können, welche Warnungen zuerst untersucht werden sollen. Der Schweregrad basiert darauf, wie sicher Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

  
 Markieren Sie Abonnements außerdem mithilfe von Tags, und erstellen Sie ein Benennungssystem, um Azure-Ressourcen eindeutig zu identifizieren und zu kategorisieren, insbesondere solche, die vertrauliche Daten verarbeiten. Die Priorisierung der Behebung von Warnungen basierend auf der Wichtigkeit der Azure-Ressourcen und der Umgebung, in der der Vorfall aufgetreten ist, liegt in Ihrer Verantwortung.
  
- [Sicherheitswarnungen in Azure Security Center](../security-center/security-center-alerts-overview.md)
  
- [Verwenden von Tags zum Organisieren Ihrer Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Übungen durch, um die Reaktionsfähigkeit Ihrer Systeme zu testen und so Ihre Azure-Ressourcen zu schützen. Ermitteln Sie Schwachpunkte und Lücken, und passen Sie Ihren Reaktionsplan dann entsprechend an.
  
- [NIST-Veröffentlichung: Leitfaden zum Testen, Trainieren und Ausführen von Programmen für IT-Pläne und -Funktionen](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft kontaktiert Sie unter den für Sicherheitsvorfälle angegebenen Kontaktdaten, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf Ihre Daten zugegriffen haben. Überprüfen Sie die Vorfälle anschließend, um sicherzustellen, dass die Probleme behoben wurden.
  
- [Festlegen der Kontaktinformationen in Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie Ihre Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“, um Risiken für Azure-Ressourcen zu ermitteln. Über die Funktion „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen.
  
- [Konfigurieren des fortlaufenden Exports](../security-center/continuous-export.md)
 
- [Streamen von Warnungen in Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen und Ihre Azure-Ressourcen noch besser zu schützen.
  
- [Konfigurieren der Workflowautomatisierung in Security Center](../security-center/workflow-automation.md)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Azure-Sicherheitsvergleichstest: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Durchführen regelmäßiger Penetrationstests Ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnissen

**Leitfaden**: Befolgen Sie in dem Fall die Einsatzregeln für Penetrationstests von Microsoft Cloud, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen. Nutzen Sie die Microsoft-Strategie und Durchführung von Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastruktur, Dienste und Anwendungen.

- [Penetrationstests – Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center-Überwachung**: Nicht zutreffend

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).