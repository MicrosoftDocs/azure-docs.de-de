---
title: Azure-Sicherheitsbaseline für HDInsight
description: Azure-Sicherheitsbaseline für HDInsight
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 5174c3bf2150c4671f1034eba6e661a10c222aec
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807681"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Azure-Sicherheitsbaseline für HDInsight

Die Azure-Sicherheitsbaseline für HDInsight enthält Empfehlungen, mit deren Hilfe Sie den Sicherheitsstatus Ihrer Bereitstellung verbessern können.

Die Baseline für diesen Dienst wird von [Azure Security Benchmark-Version 1.0](../security/benchmarks/overview.md) abgeleitet, die Empfehlungen dazu enthält, wie Sie Ihre Cloudlösungen in Azure mithilfe unserer bewährten Methoden schützen können.

Weitere Informationen finden Sie unter [Übersicht über Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).

## <a name="network-security"></a>Netzwerksicherheit

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Netzwerksicherheit](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Schützen von Ressourcen mithilfe von Netzwerksicherheitsgruppen oder Azure Firewall in Virtual Network

**Leitfaden**: Die Umgebungssicherheit in Azure HDInsight wird mithilfe von virtuellen Netzwerken erreicht. Ein Unternehmensadministrator kann einen Cluster in einem virtuellen Netzwerk erstellen und den Zugriff auf das virtuelle Netzwerk mithilfe einer Netzwerksicherheitsgruppe (NSG) beschränken. Nur die zulässigen IP-Adressen in den eingehenden Netzwerksicherheitsgruppen-Regeln können mit dem Azure HDInsight-Cluster kommunizieren. Diese Konfiguration bietet Umgebungssicherheit. Alle in einem virtuellen Netzwerk bereitgestellten Cluster verfügen auch über einen privaten Endpunkt, der in eine private IP-Adresse innerhalb des virtuellen Netzwerks aufgelöst wird, um privaten HTTP-Zugriff auf die Clustergateways zu ermöglichen.

Beschränken Sie Netzwerkdatenverkehr in ausgehender Richtung für Azure HDInsight-Cluster per Azure Firewall, um das Risiko für Datenverlust aufgrund einer Exfiltration zu verringern.

Bereitstellen von Azure HDInsight in einem virtuellen Netzwerk und Schützen mit einer Netzwerksicherheitsgruppe: https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Einschränken des ausgehenden Datenverkehrs für Azure HDInsight-Cluster per Azure Firewall: https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Überwachen und Protokollieren der Konfiguration und des Datenverkehrs von VNETs, Subnetzen und Netzwerkkarten (NICs)

**Leitfaden**: Verwenden Sie Azure Security Center, und setzen Sie die Empfehlungen zum Netzwerkschutz für das virtuelle Netzwerk, das Subnetz und die Netzwerksicherheitsgruppe um, die für den Schutz Ihres Azure HDInsight-Clusters genutzt werden. Aktivieren Sie NSG-Flussprotokolle (Netzwerksicherheitsgruppe), und senden Sie Protokolle an ein Azure Storage-Konto, um den Datenverkehr zu überwachen. Sie können NSG-Flussprotokolle auch an einen Azure Log Analytics-Arbeitsbereich senden und Azure Traffic Analytics verwenden, um Einblicke in den Datenverkehrsfluss in Ihrer Azure-Cloud zu ermöglichen. Einige Vorteile von Azure Traffic Analytics sind die Möglichkeit, die Netzwerkaktivität zu visualisieren und Hotspots zu erkennen, Sicherheitsbedrohungen zu identifizieren, Datenverkehrsflussmuster zu verstehen und Netzwerkfehlkonfigurationen zu ermitteln.

Aktivieren der NSG-Flussprotokolle:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Aktivieren und Verwenden von Azure Traffic Analytics:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Grundlegendes zu der von Azure Security Center bereitgestellten Netzwerksicherheit:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="13-protect-critical-web-applications"></a>1.3: Schützen kritischer Webanwendungen

**Leitfaden**: Nicht anwendbar. Die Benchmark ist für Azure App Service- oder Computeressourcen vorgesehen, auf denen Webanwendungen gehostet werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Ablehnen der Kommunikation mit bekannten bösartigen IP-Adressen

**Leitfaden**: Aktivieren Sie als Schutz vor DDoS-Angriffen den Azure-DDoS-Standardschutz für das virtuelle Netzwerk, in dem Ihre Azure HDInsight-Instanz bereitgestellt wurde. Verwenden Sie die in Azure Security Center integrierte Threat Intelligence, um die Kommunikation mit bekannten schädlichen oder nicht genutzten IP-Adressen zu verweigern.

Konfigurieren von DDoS-Schutz:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Grundlegendes zu integrierten Informationen zu Bedrohungen in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Aufzeichnen von Netzwerkpaketen und Flussprotokollen

**Leitfaden**: Aktivieren Sie NSG-Flussprotokolle (Netzwerksicherheitsgruppe) für die NSG, die an das Subnetz angefügt wurde, mit dem Ihr Azure HDInsight-Cluster geschützt wird. Erfassen Sie die NSG-Flussprotokolle in einem Azure Storage-Konto, um Flussdatensätze zu generieren. Aktivieren Sie die Azure Network Watcher-Paketerfassung, falls dies für die Untersuchung anomaler Aktivitäten erforderlich ist.

Aktivieren der NSG-Flussprotokolle:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Aktivieren von Network Watcher:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Bereitstellen von netzwerkbasierten Angriffserkennungs-/Eindringschutzsystemen (IDS/IPS)

**Leitfaden**: Anforderung kann erfüllt werden: Azure-Sicherheitskontrolle, ID 1.1. Stellen Sie den Azure HDInsight-Cluster in einem virtuellen Netzwerk bereit, und schützen Sie ihn durch eine Netzwerksicherheitsgruppe (NSG).

Es gibt mehrere Abhängigkeiten für Azure HDInsight, für die eingehender Datenverkehr erforderlich ist. Der eingehende Verwaltungsdatenverkehr kann nicht über ein Firewallgerät gesendet werden. Die Quelladressen für den erforderlichen Verwaltungsdatenverkehr sind bekannt und werden veröffentlicht. Erstellen Sie mit diesen Informationen Netzwerksicherheitsgruppen-Regeln, um nur Datenverkehr von vertrauenswürdigen Orten zuzulassen und so den eingehenden Datenverkehr für Cluster zu schützen.

Beschränken Sie Netzwerkdatenverkehr in ausgehender Richtung für Azure HDInsight-Cluster per Azure Firewall, um das Risiko für Datenverlust aufgrund einer Exfiltration zu verringern.

Bereitstellen von HDInsight in einem virtuellen Netzwerk und Schützen mit einer Netzwerksicherheitsgruppe: https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Grundlegendes zu HDInsight-Abhängigkeiten und zur Firewallverwendung: https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

HDInsight-Verwaltungs-IP-Adressen: https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verwalten von Datenverkehr für Webanwendungen

**Leitfaden**: Nicht anwendbar. Die Benchmark ist für Azure App Service- oder Computeressourcen vorgesehen, auf denen Webanwendungen gehostet werden.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimieren der Komplexität und des Verwaltungsaufwands von Netzwerksicherheitsregeln

**Leitfaden**: Verwenden Sie Diensttags in virtuellen Netzwerken, um Netzwerkzugriffssteuerungen in Netzwerksicherheitsgruppen (NSGs) zu definieren, die an das Subnetz angefügt sind, in dem Ihr Azure HDInsight-Cluster bereitgestellt wurde. Sie können Diensttags anstelle von spezifischen IP-Adressen nutzen, wenn Sie Sicherheitsregeln erstellen. Indem Sie den Diensttagnamen (z. B. „ApiManagement“) im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

Grundlegendes zu Diensttags und Verwenden von Diensttags für Azure HDInsight:

https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Beibehalten von Standardsicherheitskonfigurationen für Netzwerkgeräte

**Leitfaden**: Definieren und implementieren Sie Standardsicherheitskonfigurationen für die Netzwerkressourcen Ihres Azure HDInsight-Clusters. Verwenden Sie Azure Policy-Aliase in den Namespaces „Microsoft.HDInsight“ und „Microsoft.Network“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihres Azure HDInsight-Clusters zu erstellen.

Sie können umfangreiche Azure-Bereitstellungen auch mithilfe von Azure Blueprints vereinfachen, indem Sie wichtige Umgebungsartefakte wie Azure Resource Manager-Vorlagen, Azure RBAC-Kontrollen und Richtlinien in einer einzigen Blaupausendefinition packen. Sie können die Blaupause leicht auf neue Abonnements und Umgebungen anwenden und die Steuerung und Verwaltung durch die Versionsverwaltung optimieren.

Anzeigen verfügbarer Azure Policy-Aliase:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias Konfigurieren und Verwalten von Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Erstellen einer Azure-Blaupause:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentieren von Datenverkehrskonfigurationsregeln

**Leitfaden**: Verwenden Sie Tags für Netzwerksicherheitsgruppen (NSGs) und andere Ressourcen im Zusammenhang mit Netzwerksicherheit und Datenverkehrsfluss, die Ihrem Azure HDInsight-Cluster zugeordnet sind. Verwenden Sie für einzelne NSG-Regeln das Feld „Beschreibung“, um geschäftliche Anforderungen und/oder deren Dauer (usw.) für alle Regeln festzulegen, die Datenverkehr in ein bzw. aus einem Netzwerk zulassen.

Verwenden Sie eine der integrierten Azure Policy-Definitionen zum Tagging, z. B. „Tag und zugehörigen Wert erzwingen“, um sicherzustellen, dass alle Ressourcen mit Tags erstellt werden und Sie über vorhandene nicht markierte Ressourcen benachrichtigt werden.

Sie können Azure PowerShell oder die Azure-Befehlszeilenschnittstelle (CLI) verwenden, um Ressourcen anhand ihrer Tags zu suchen oder Aktionen auszuführen.

Erstellen und Verwenden von Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Erstellen eines virtuellen Netzwerks:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Erstellen einer NSG mit einer Sicherheitskonfiguration:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Verwenden automatisierter Tools zum Überwachen von Netzwerkressourcenkonfigurationen und Erkennen von Änderungen

**Leitfaden**: Verwenden Sie das Azure-Aktivitätsprotokoll zum Überwachen der Konfigurationen von Netzwerkressourcen und zum Erkennen von Änderungen bei Netzwerkressourcen, die sich auf Ihre Azure HDInsight-Bereitstellungen beziehen. Erstellen Sie Warnungen in Azure Monitor, die bei Änderungen an wichtigen Netzwerkressourcen ausgelöst werden.

Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Erstellen von Warnungen in Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="logging-and-monitoring"></a>Protokollierung und Überwachung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Verwenden von genehmigten Zeitsynchronisierungsquellen

**Leitfaden**: Microsoft verwaltet Zeitquellen für Azure HDInsight-Clusterkomponenten. Sie können die Zeitsynchronisierung für Ihre Computebereitstellungen aktualisieren.

Konfigurieren der Zeitsynchronisierung für Azure-Computeressourcen:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Konfigurieren der zentralen Sicherheitsprotokollverwaltung

**Leitfaden**: Sie können für Ihren Azure HDInsight-Cluster das Onboarding für Azure Monitor durchführen, um die vom Cluster generierten Sicherheitsdaten zu aggregieren. Nutzen Sie benutzerdefinierte Abfragen, um Bedrohungen in der Umgebung zu erkennen und darauf zu reagieren. 

Durchführen des Onboardings für einen Azure HDInsight-Cluster für Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Erstellen von benutzerdefinierten Abfragen für einen Azure HDInsight-Cluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Aktivieren der Überwachungsprotokollierung für Azure-Ressourcen

**Leitfaden**: Aktivieren Sie Azure Monitor für den HDInsight-Cluster, und verweisen Sie dafür auf einen Log Analytics-Arbeitsbereich. Hierdurch werden dann alle relevanten Clusterinformationen und Betriebssystemmetriken für alle Azure HDInsight-Clusterknoten protokolliert.

Aktivieren der Protokollierung für den HDInsight-Cluster:

 https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Abfragen von HDInsight-Protokollen:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Erfassen von Sicherheitsprotokollen von Betriebssystemen

**Leitfaden**: Führen Sie das Onboarding für einen Azure HDInsight-Cluster für Azure Monitor durch. Stellen Sie sicher, dass für den verwendeten Log Analytics-Arbeitsbereich der Protokollaufbewahrungszeitraum gemäß den Konformitätsbestimmungen Ihrer Organisation festgelegt ist.

Durchführen des Onboardings für einen Azure HDInsight-Cluster für Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Konfigurieren des Aufbewahrungszeitraums des Log Analytics-Arbeitsbereichs:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="25-configure-security-log-storage-retention"></a>2.5: Konfigurieren der Sicherheitsprotokoll-Aufbewahrungsdauer im Speicher

**Leitfaden**: Führen Sie das Onboarding für einen Azure HDInsight-Cluster für Azure Monitor durch. Stellen Sie sicher, dass für den verwendeten Azure Log Analytics-Arbeitsbereich der Protokollaufbewahrungszeitraum gemäß den Konformitätsbestimmungen Ihrer Organisation festgelegt ist.

Durchführen des Onboardings für einen Azure HDInsight-Cluster für Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Konfigurieren des Aufbewahrungszeitraums des Log Analytics-Arbeitsbereichs:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="26-monitor-and-review-logs"></a>2.6: Überwachen und Überprüfen von Protokollen

**Leitfaden**: Verwenden Sie Abfragen für den Azure Log Analytics-Arbeitsbereich, um Azure HDInsight-Protokolle abzufragen:

Erstellen von benutzerdefinierten Abfragen für Azure HDInsight-Cluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Aktivieren von Warnungen für anormale Aktivitäten

**Leitfaden**: Verwenden Sie den Azure Log Analytics-Arbeitsbereich zum Überwachen von und Warnen vor anomalen Aktivitäten in Sicherheitsprotokollen und -ereignissen Ihres Azure HDInsight-Clusters.

Verwalten von Warnungen in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Warnungen bei Log Analytics-Protokolldaten:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="28-centralize-anti-malware-logging"></a>2.8: Zentralisieren der Antischadsoftwareprotokollierung

**Leitfaden**: Bei Azure HDInsight ist Clamscan vorinstalliert und für die Clusterknotenimages aktiviert, aber Sie müssen die Software verwalten und alle von Clamscan erzeugten Protokolle manuell aggregieren bzw. überwachen.

Grundlegendes zu Clamscan:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="29-enable-dns-query-logging"></a>2.9: Aktivieren der DNS-Abfrageprotokollierung

**Leitfaden**: Implementieren Sie eine Drittanbieterlösung für die DNS-Protokollierung.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="210-enable-command-line-audit-logging"></a>2.10: Aktivieren der Befehlszeilen-Überwachungsprotokollierung

**Leitfaden**: Führen Sie die manuelle Konfiguration der Konsolenprotokollierung jeweils für die einzelnen Knoten durch.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="identity-and-access-control"></a>Identität und Zugriffssteuerung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Identität und Zugriffssteuerung](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Verwalten eines Bestands von Administratorkonten

**Leitfaden**: Verwalten Sie den Datensatz für das lokale Administratorkonto, das während der Clusterbereitstellung des Azure HDInsight-Clusters erstellt wird, sowie für alle anderen von Ihnen erstellten Konten. Wenn außerdem die Azure AD-Integration verwendet wird, verfügt Azure AD über integrierte Rollen, die explizit zugewiesen werden müssen und daher abgefragt werden können. Verwenden Sie das Azure AD PowerShell-Modul, um Ad-hoc-Abfragen zum Ermitteln von Konten durchzuführen, die Mitglieder von Verwaltungsgruppen sind.

Außerdem können Sie Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center anwenden.

Abrufen einer Verzeichnisrolle in Azure AD mit PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole

Abrufen von Mitgliedern einer Verzeichnisrolle in Azure AD mit PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember

Überwachen von Identität und Zugriff mit Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändern von Standardkennwörtern bei Bedarf

**Leitfaden**: Beim Bereitstellen eines Clusters müssen Sie in Azure neue Kennwörter für das Webportal und den SSH-Zugriff (Secure Shell) erstellen. Es müssen keine Standardkennwörter geändert werden, aber Sie können unterschiedliche Kennwörter für den SSH- und Webportalzugriff angeben.

Festlegen von Kennwörtern beim Bereitstellen eines Azure HDInsight-Clusters:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Verwenden dedizierter Administratorkonten

**Leitfaden**: Integrieren Sie die Authentifizierung für Azure HDInsight-Cluster mit Azure Active Directory. Erstellen Sie Richtlinien und Vorgänge für die Verwendung dedizierter Administratorkonten.

Außerdem können Sie Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center anwenden.

Integrieren der Azure HDInsight-Authentifizierung mit Azure Active Directory:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Überwachen von Identität und Zugriff mit Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Verwenden des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory

**Leitfaden**: Verwenden Sie Azure HDInsight-Identitätsbroker, um sich an ESP-Clustern (Enterprise-Sicherheitspaket) per Multi-Factor Authentication anzumelden, ohne Kennwörter anzugeben. Wenn Sie bereits bei anderen Azure-Diensten angemeldet sind, z. B. beim Azure-Portal, können Sie sich mittels einmaligem Anmelden (SSO) bei Ihrem Azure HDInsight-Cluster anmelden.

Aktivieren von Azure HDInsight-Identitätsbroker:

https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Verwenden der mehrstufigen Authentifizierung für den gesamten Azure Active Directory-basierten Zugriff

**Leitfaden**: Aktivieren Sie Azure AD MFA, und befolgen Sie die Empfehlungen für die Identitäts- und Zugriffsverwaltung in Azure Security Center. Azure HDInsight-Cluster mit konfiguriertem Enterprise-Sicherheitspaket sind mit einer Domäne verbunden, sodass Domänenbenutzer ihre Domänenanmeldeinformationen zum Authentifizieren beim Cluster verwenden und Big Data-Aufträge ausführen können. Bei der Authentifizierung mit aktivierter Multi-Factor Authentication (MFA) werden die Benutzer aufgefordert, eine zweite Authentifizierungsstufe zu nutzen.

Aktivieren von MFA in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Überwachen von Identität und Zugriff in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Verwenden dedizierter Computer (Arbeitsstationen mit privilegiertem Zugriff) für alle administrativen Aufgaben

**Leitfaden**: Verwenden Sie PAWs (Privileged Access Workstation, Arbeitsstation mit privilegiertem Zugriff) mit MFA (mehrstufige Authentifizierung), die für die Anmeldung und Konfiguration Ihrer Azure HDInsight-Cluster und der zugehörigen Ressourcen konfiguriert sind.

Informationen zu Arbeitsstationen mit privilegiertem Zugriff:

https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/

Aktivieren von MFA in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Protokollieren und Warnen bei verdächtigen Aktivitäten in Administratorkonten

**Leitfaden**: Azure HDInsight-Cluster mit konfiguriertem Enterprise-Sicherheitspaket sind mit einer Domäne verbunden, damit Domänenbenutzer ihre Domänenanmeldeinformationen zum Authentifizieren verwenden können. Sie können AAD-Sicherheitsberichte (Azure Active Directory) für die Generierung von Protokollen und Warnungen bei verdächtigen oder sicherheitsrelevanten Aktivitäten in der AAD-Umgebung verwenden. Verwenden Sie Azure Security Center zum Überwachen von identitäts- und zugriffsbezogenen Aktivitäten.

Identifizieren von AAD-Benutzern, die aufgrund riskanter Aktivitäten gekennzeichnet wurden:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Überwachen der identitäts- und zugriffsbezogenen Aktivitäten von Benutzern in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Verwalten von Azure-Ressourcen nur über genehmigte Standorte

**Leitfaden**: Azure HDInsight-Cluster mit konfiguriertem Enterprise-Sicherheitspaket sind mit einer Domäne verbunden, damit Domänenbenutzer ihre Domänenanmeldeinformationen zum Authentifizieren verwenden können. Verwenden Sie benannte Standorte mit bedingtem Zugriff, um den Zugriff nur über bestimmte logische Gruppierungen von IP-Adressbereichen oder Ländern/Regionen zuzulassen.

Konfigurieren benannter Standorte in Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="39-use-azure-active-directory"></a>3.9: Verwenden von Azure Active Directory

**Leitfaden**: Verwenden Sie Azure Active Directory (AAD) als zentrales Authentifizierungs- und Autorisierungssystem. AAD schützt Daten durch eine starke Verschlüsselung für ruhende und übertragene Daten. Außerdem werden in AAD Salts und Hashs für Anmeldeinformationen verwendet und diese sicher gespeichert.

Azure HDInsight-Cluster mit konfiguriertem Enterprise-Sicherheitspaket (ESP) sind mit einer Domäne verbunden, damit Domänenbenutzer ihre Domänenanmeldeinformationen zum Authentifizieren für die Cluster verwenden können.

Erstellen und Konfigurieren einer AAD-Instanz:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Konfigurieren des Enterprise-Sicherheitspakets mit Azure Active Directory Domain Services in Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmäßiges Überprüfen und Abstimmen des Benutzerzugriffs

**Leitfaden**: Verwenden Sie die AAD-Authentifizierung (Azure Active Directory) mit Ihrem Azure HDInsight-Cluster. AAD enthält Protokolle zum Ermitteln von veralteten Konten. Verwenden Sie zusätzlich Zugriffsüberprüfungen für Azure-Identitäten, um Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient zu verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Benutzer weiterhin Zugriff besitzen. 

Verwenden von Zugriffsüberprüfungen für Azure-Identitäten:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Überwachen von Zugriffsversuchen auf deaktivierte Konten

**Leitfaden**: Verwenden Sie AAD-Anmelde- und Überwachungsprotokolle (Azure Active Directory), um zu überwachen, ob versucht wird, auf deaktivierte Konten zuzugreifen. Diese Protokolle können in alle SIEM-/Überwachungstools von Drittanbietern integriert werden.

Sie können diesen Prozess optimieren, indem Sie Diagnoseeinstellungen für AAD-Benutzerkonten erstellen und die Überwachungs- und Anmeldeprotokolle an einen Azure Log Analytics-Arbeitsbereich senden. Konfigurieren Sie die gewünschten Warnungen im Log Analytics-Arbeitsbereich.

Integrieren von Azure-Aktivitätsprotokollen in Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Warnung bei abweichendem Verhalten bei der Kontoanmeldung

**Leitfaden**: Azure HDInsight-Cluster mit konfiguriertem Enterprise-Sicherheitspaket (ESP) sind mit einer Domäne verbunden, damit Domänenbenutzer ihre Domänenanmeldeinformationen zum Authentifizieren für die Cluster verwenden können.  Mit den Funktionen zur Risikoerkennung und zum Identitätsschutz von Azure Active Directory (AAD) können Sie automatische Antworten auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten konfigurieren. Außerdem können Sie Daten zur weiteren Untersuchung in Azure Sentinel erfassen.

Anzeigen von AAD-Risikoanmeldungen:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Konfigurieren und Aktivieren von Risikorichtlinien für den Identitätsschutz:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ermöglichen des Zugriffs auf relevante Kundendaten für Microsoft in Supportszenarien

**Leitfaden**: Nicht verfügbar. Kunden-Lockbox wird für Azure HDInsight noch nicht unterstützt.

Liste der durch Kunden-Lockbox unterstützten Dienste: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-protection"></a>Datenschutz

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz von Daten](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Verwalten eines Bestands an vertraulichen Informationen

**Leitfaden**: Verwenden Sie Tags für Ressourcen Ihrer Azure HDInsight-Bereitstellungen als Hilfe beim Nachverfolgen von Azure-Ressourcen, die vertrauliche Informationen speichern oder verarbeiten.

Erstellen und Verwenden von Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolieren von Systemen, die vertrauliche Informationen speichern oder verarbeiten

**Leitfaden**: Implementieren Sie separate Abonnements und/oder Verwaltungsgruppen für Entwicklungs-, Test- und Produktionsumgebungen. Azure HDInsight-Cluster und alle zugeordneten Speicherkonten sollten durch ein virtuelles Netzwerk bzw. ein Subnetz getrennt, entsprechend mit Tags versehen und in einer Netzwerksicherheitsgruppe (NSG) oder per Azure Firewall geschützt sein. Clusterdaten sollten in einem geschützten Azure Storage-Konto oder in Azure Data Lake Storage (Gen1 oder Gen2) enthalten sein.

Wählen Sie Speicheroptionen für Ihren Azure HDInsight-Cluster aus:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Schützen von Azure Data Lake Storage:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Schützen von Azure Storage-Konten:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Überwachen und Blockieren einer nicht autorisierten Übertragung vertraulicher Informationen

**Leitfaden**: Kennzeichnen Sie den Cluster und die zugehörigen Ressourcen mit Tags als vertraulich, wenn mit Azure HDInsight-Clustern vertrauliche Informationen gespeichert oder verarbeitet werden. Beschränken Sie Netzwerkdatenverkehr in ausgehender Richtung für Azure HDInsight-Cluster per Azure Firewall, um das Risiko für Datenverlust aufgrund einer Exfiltration zu verringern.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

Einschränken des ausgehenden Datenverkehrs für Azure HDInsight-Cluster per Azure Firewall:

https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

Grundlegendes zum Schutz von Kundendaten in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Verschlüsseln aller vertraulichen Informationen während der Übertragung

**Leitfaden**: Verschlüsseln Sie alle vertraulichen Informationen während der Übertragung. Stellen Sie sicher, dass alle Clients, die eine Verbindung mit Ihrem Azure HDInsight-Cluster oder mit Clusterdatenspeichern (Azure Storage-Konten oder Azure Data Lake Storage Gen1/Gen2) herstellen, die Aushandlung mit TLS 1.2 oder höher durchführen können. Für Microsoft Azure-Ressourcen wird standardmäßig die TLS 1.2-Aushandlung durchgeführt. 

Grundlegendes zur Azure Data Lake Storage-Verschlüsselung während der Übertragung:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Grundlegendes zur Azure Storage-Kontoverschlüsselung während der Übertragung:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Verwenden eines aktiven Ermittlungstools zur Bestimmung vertraulicher Daten

**Leitfaden**: Funktionen für Datenidentifizierung, -klassifizierung und -verlust sind für Azure Storage- oder Computeressourcen noch nicht verfügbar. Implementieren Sie eine Drittanbieterlösung, wenn dies für Compliancezwecke erforderlich ist.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

Grundlegendes zum Schutz von Kundendaten in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Verwenden von Azure RBAC zum Steuern des Zugriffs auf Ressourcen

**Leitfaden**: Mit dem Enterprise-Sicherheitspaket (ESP) von Azure HDInsight können Sie Apache Ranger verwenden, um Richtlinien für eine präzise Zugriffssteuerung und Obfuskation Ihrer Daten, die in Dateien, Ordnern, Datenbanken, Tabellen und Zeilen bzw. Spalten gespeichert sind, zu erstellen und zu verwalten. Der Hadoop-Administrator kann die rollenbasierte Zugriffssteuerung (RBAC) konfigurieren, um Apache Hive, HBase, Kafka und Spark mit diesen Plug-Ins in Apache Ranger zu schützen.

Durch die Konfiguration von RBAC-Richtlinien mit Apache Ranger können Sie Berechtigungen einer Rolle in der Organisation zuordnen. Diese Abstraktionsebene erleichtert die Sicherstellung, dass Personen nur über die Berechtigungen verfügen, die sie zur Erfüllung ihrer beruflichen Aufgaben benötigen.

Konfigurationen des Enterprise-Sicherheitspakets mit Azure Active Directory Domain Services in Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Übersicht über die Unternehmenssicherheit in Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview



**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Verwenden der hostbasierten Verhinderung von Datenverlusten zum Erzwingen der Zugriffssteuerung

**Leitfaden**: Kennzeichnen Sie den Cluster und die zugehörigen Ressourcen mit Tags als vertraulich, wenn mit Azure HDInsight-Clustern vertrauliche Informationen gespeichert oder verarbeitet werden. Funktionen für Datenidentifizierung, -klassifizierung und -verlust sind für Azure Storage- oder Computeressourcen noch nicht verfügbar. Implementieren Sie eine Drittanbieterlösung, wenn dies für Compliancezwecke erforderlich ist.

Für die zugrundeliegende Plattform, die von Microsoft verwaltet wird, behandelt Microsoft alle Kundeninhalte als vertraulich und unternimmt große Anstrengungen, um Kundendaten vor Verlust und Gefährdung zu schützen. Um die Sicherheit von Kundendaten innerhalb von Azure zu gewährleisten, hat Microsoft eine Reihe von robusten Datenschutzkontrollen und -funktionen implementiert und kümmert sich um deren Verwaltung.

Grundlegendes zum Schutz von Kundendaten in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Verschlüsseln vertraulicher, ruhender Informationen

**Leitfaden**: Stellen Sie bei der Verwendung von Azure SQL-Datenbank zum Speichern von Apache Hive- und Apache Oozie-Metadaten sicher, dass die SQL-Daten jederzeit verschlüsselt sind. Für Azure Storage-Konten und Data Lake Storage (Gen1 oder Gen2) empfehlen wir Ihnen, Microsoft die Verwaltung Ihrer Verschlüsselungsschlüssel zu überlassen. Sie haben aber auch die Möglichkeit, Ihre Schlüssel selbst zu verwalten.

Verwalten von Verschlüsselungsschlüsseln für Azure Storage-Konten:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

Erstellen von Azure Data Lake Storage mit vom Kunden verwalteten Verschlüsselungsschlüsseln:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal

Grundlegendes zur Verschlüsselung für Azure SQL-Datenbank:

https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview#data-encryption

Konfigurieren von Transparent Data Encryption für SQL-Datenbank mit vom Kunden verwalteten Schlüsseln:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Shared

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Protokollieren und Warnen bei Änderungen an wichtigen Azure-Ressourcen

**Leitfaden**: Konfigurieren Sie Diagnoseeinstellungen für Azure Storage-Konten, die Azure HDInsight-Clustern zugeordnet sind, um alle CRUD-Vorgänge für Clusterdaten zu überwachen und zu protokollieren. Aktivieren Sie die Überwachung für alle Storage-Konten oder Data Lake Stores, die dem Azure HDInsight-Cluster zugeordnet sind.

Aktivieren zusätzlicher Protokollierung/Überwachung für ein Azure Storage-Konto:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

Aktivieren zusätzlicher Protokollierung/Überwachung für Azure Data Lake Storage:

https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Verwaltung von Sicherheitsrisiken](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Ausführen automatisierter Scan-Tools für Sicherheitsrisiken

**Leitfaden**: Implementieren Sie eine Drittanbieterlösung zur Verwaltung von Sicherheitsrisiken.

Wenn Sie über ein Rapid7-, Qualys- oder anderes Abonnement einer Plattform für die Sicherheitsrisikoverwaltung verfügen, können Sie Skriptaktionen verwenden, um Agents für die Sicherheitsrisikobewertung auf Ihren Azure HDInsight-Clusterknoten zu installieren, und die Knoten über das entsprechende Portal verwalten.

Manuelles Installieren des Rapid7-Agents:

https://insightvm.help.rapid7.com/docs/install

Manuelles Installieren des Qualys-Agents:

https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf

Verwenden von Skriptaktionen:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Betriebssystemen

**Leitfaden**: Für Clusterknotenimages wurden automatische Systemupdates aktiviert. Sie müssen die Clusterknoten aber regelmäßig neu starten, um sicherzustellen, dass die Updates angewendet werden.

Microsoft verwaltet und aktualisiert die Basisknotenimages für Azure HDInsight.

Konfigurieren des Zeitplans für das Patchen des Betriebssystems für HDInsight-Cluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Bereitstellen der automatisierten Lösung für die Patchverwaltung von Drittanbietersoftware

**Leitfaden**: Verwenden Sie Skriptaktionen oder andere Mechanismen, um Ihre Azure HDInsight-Cluster zu patchen. Neu erstellte Cluster verfügen immer über die aktuellen verfügbaren Updates, einschließlich der neuesten Sicherheitspatches.

Konfigurieren des Zeitplans für das Patchen des Betriebssystems für Linux-basierte Azure HDInsight-Cluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

Verwenden von Skriptaktionen:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4 Vergleichen von kaskadierenden Sicherheitsrisikoscans

**Leitfaden**: Implementieren Sie eine Drittanbieterlösung für die Verwaltung von Sicherheitsrisiken, mit der Überprüfungen auf Sicherheitsrisiken zeitabhängig verglichen werden können. Falls Sie über ein Rapid7- oder Qualys-Abonnement verfügen, können Sie das Portal des Anbieters nutzen, um aufeinanderfolgende Überprüfungen auf Sicherheitsrisiken anzuzeigen und zu vergleichen.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Verwenden eines Risikobewertungsprozesses, um die Behebung von erkannten Sicherheitsrisiken zu priorisieren

**Leitfaden**: Verwenden Sie ein gängiges Risikobewertungsprogramm (z. B. Common Vulnerability Scoring System) oder die von Ihrem Scantool eines Drittanbieters bereitgestellten Standardrisikobewertungen.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="inventory-and-asset-management"></a>Bestands- und Ressourcenverwaltung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Bestands- und Ressourcenverwaltung](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Verwenden von Azure Asset Discovery

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen (z. B. Computeressourcen, Speicher, Netzwerke, Ports und Protokolle usw.), einschließlich HDInsight-Cluster, in Ihren Abonnements abzufragen bzw. zu ermitteln.  Stellen Sie sicher, dass Sie über entsprechende (Lese-)Berechtigungen in Ihrem Mandanten verfügen und alle Azure-Abonnements und Ressourcen in Ihren Abonnements auflisten können.

Obwohl klassische Azure-Ressourcen über das Resource Graph ermittelt werden können, wird dringend empfohlen, Azure Resource Manager-Ressourcen zu erstellen und zu verwenden.

Erstellen von Abfragen mit Azure Resource Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Anzeigen Ihrer Azure-Abonnements:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription

Grundlegendes zu Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="62-maintain-asset-metadata"></a>6.2: Verwalten von Ressourcenmetadaten

**Leitfaden**: Wenden Sie Tags auf Ihre Azure-Ressourcen an, die Metadaten erzeugen, um sie logisch in einer Taxonomie zu organisieren.

Erstellen und Verwenden von Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Löschen nicht autorisierter Azure-Ressourcen

**Leitfaden**: Verwenden Sie nach Bedarf Tagging, Verwaltungsgruppen und separate Abonnements zum Organisieren und Nachverfolgen von Ressourcen. Stimmen Sie den Bestand regelmäßig ab, und stellen Sie sicher, dass nicht autorisierte Ressourcen rechtzeitig aus dem Abonnement gelöscht werden.

Erstellen zusätzlicher Azure-Abonnements:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Erstellen von Verwaltungsgruppen:

https://docs.microsoft.com/azure/governance/management-groups/create

Erstellen und Verwenden von Tags:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Verwalten eines Bestands genehmigter Azure-Ressourcen und Softwaretitel

**Leitfaden**: Definieren Sie eine Liste mit den genehmigten Azure-Ressourcen und der genehmigten Software für Computeressourcen.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Überwachung auf nicht genehmigte Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)

- Zulässige Ressourcentypen

Verwenden Sie Azure Resource Graph, um Ressourcen in Ihren Abonnements abzufragen und zu ermitteln. Stellen Sie sicher, dass alle in der Umgebung vorhandenen Azure-Ressourcen genehmigt sind.

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Erstellen von Abfragen mit Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Überwachen auf nicht genehmigte Softwareanwendungen innerhalb von Computeressourcen

**Leitfaden**: Implementieren Sie eine Drittanbieterlösung, um Clusterknoten auf nicht genehmigte Softwareanwendungen zu überwachen.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Entfernen nicht genehmigter Azure-Ressourcen und Softwareanwendungen

**Leitfaden**: Verwenden Sie Azure Resource Graph, um alle Ressourcen (z. B. Computeressourcen, Speicher, Netzwerke, Ports und Protokolle usw.), einschließlich HDInsight-Cluster, in Ihren Abonnements abzufragen bzw. zu ermitteln.  Entfernen Sie alle nicht genehmigten Azure-Ressourcen, die Sie finden. Implementieren Sie für Azure HDInsight-Cluster eine Drittanbieterlösung, um nicht genehmigte Software zu entfernen bzw. Warnungen dazu anzuzeigen.

Erstellen von Abfragen mit Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="68-use-only-approved-applications"></a>6.8: Ausschließliche Verwendung genehmigter Anwendungen

**Leitfaden**: Implementieren Sie für HDInsight-Clusterknoten eine Drittanbieterlösung, um zu verhindern, dass nicht autorisierte Software ausgeführt wird.


**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="69-use-only-approved-azure-services"></a>6.9: Ausschließliche Verwendung genehmigter Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy, um Einschränkungen für den Typ der Ressourcen anzugeben, die in Kundenabonnements erstellt werden können. Nutzen Sie hierzu die folgenden integrierten Richtliniendefinitionen:

- Not allowed resource types (Unzulässige Ressourcentypen)

- Zulässige Ressourcentypen

Konfigurieren und Verwalten von Azure Policy: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Ablehnen eines bestimmten Ressourcentyps mit Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="610-implement-approved-application-list"></a>6.10: Implementieren einer Liste genehmigter Anwendungen

**Leitfaden**: Implementieren Sie für HDInsight-Clusterknoten eine Drittanbieterlösung, um zu verhindern, dass Dateien eines nicht autorisierten Typs ausgeführt werden.


**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Einschränken der Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager über Skripts

**Leitfaden**: Verwenden Sie den bedingten Azure-Zugriff, um die Möglichkeiten der Benutzer zur Interaktion mit Azure Resource Manager einzuschränken, indem Sie „Zugriff blockieren“ für die App zur „Verwaltung von Microsoft Azure“ konfigurieren.

Konfigurieren des bedingten Zugriffs, um den Zugriff auf Azure Resource Manager zu blockieren: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Einschränken der Möglichkeiten der Benutzer, Skripte innerhalb von Computeressourcen auszuführen

**Leitfaden**: Nicht anwendbar. Dies gilt nicht für Azure HDInsight, da Clusterbenutzer (die keine Administratoren sind) keinen Zugriff auf die einzelnen Knoten benötigen, um Aufträge auszuführen. Der Clusteradministrator besitzt root-Zugriff auf alle Clusterknoten.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Physische oder logische Trennung von Anwendungen mit hohem Risiko

**Leitfaden**: Nicht anwendbar. Die Benchmark ist für Azure App Service- oder Computeressourcen vorgesehen, auf denen Webanwendungen gehostet werden.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

## <a name="secure-configuration"></a>Sichere Konfiguration

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Sichere Konfiguration](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Einrichten sicherer Konfigurationen für alle Azure-Ressourcen

**Leitfaden**: Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.HDInsight“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Netzwerkkonfiguration Ihres HDInsight-Clusters zu erstellen.

Anzeigen verfügbarer Azure Policy-Aliase:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias

Konfigurieren und Verwalten von Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Einrichten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Azure HDInsight-Betriebssystemimages werden von Microsoft verwaltet und gewartet. Der Kunde ist verantwortlich für die Implementierung sicherer Konfigurationen für das Betriebssystem Ihrer Clusterknoten. 


**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Verwalten von sicheren Konfigurationen für Azure-Ressourcen

**Leitfaden**: Verwenden Sie die Azure Policy-Richtlinien „[deny]“ (Verweigern) und „[deploy if not exist]“ (Bereitstellen, falls nicht vorhanden), um sichere Einstellungen für Ihre Azure HDInsight-Cluster und die zugehörigen Ressourcen zu erzwingen.

Konfigurieren und Verwalten von Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Grundlegendes zu Azure Policy-Auswirkungen:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Verwalten sicherer Betriebssystemkonfigurationen

**Leitfaden**: Azure HDInsight-Betriebssystemimages werden von Microsoft verwaltet und gewartet. Der Kunde ist verantwortlich für die Implementierung der Zustandskonfiguration auf Betriebssystemebene.


**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Sicheres Speichern der Konfiguration von Azure-Ressourcen

**Leitfaden**: Wenn Sie benutzerdefinierte Azure Policy-Definitionen verwenden, nutzen Sie Azure DevOps oder Azure Repos, um Ihren Code sicher zu speichern und zu verwalten.

[Speichern von Code in Azure DevOps](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

[Dokumentation zu Azure Repos](/azure/devops/repos/index?view=azure-devops&preserve-view=true)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Sicheres Speichern von benutzerdefinierten Betriebssystemimages

**Leitfaden**: Nicht anwendbar. Benutzerdefinierte Images werden für Azure HDInsight nicht verwendet.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Nicht verfügbar

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Bereitstellen von Verwaltungstools zur Systemkonfiguration

**Leitfaden**: Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.HDInsight“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen von Systemkonfigurationen zu erstellen und Warnungen dazu zu erhalten. Entwickeln Sie außerdem einen Prozess und eine Pipeline zum Verwalten von Richtlinienausnahmen.

Konfigurieren und Verwalten von Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Bereitstellen von Verwaltungstools für Systemkonfigurationen für Betriebssysteme

**Leitfaden**: Implementieren Sie eine Drittanbieterlösung, um den gewünschten Zustand für die Betriebssysteme Ihrer Clusterknoten zu verwalten.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Implementieren der automatisierten Konfigurationsüberwachung für Azure-Dienste

**Leitfaden**: Verwenden Sie Azure Policy-Aliase im Namespace „Microsoft.HDInsight“, um benutzerdefinierte Richtlinien zum Überwachen oder Erzwingen der Konfiguration Ihres HDInsight-Clusters zu erstellen.

[Anzeigen verfügbarer Azure Policy-Aliase](/powershell/module/az.resources/get-azpolicyalias)

[Konfigurieren und Verwalten von Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center-Überwachung:** Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementieren der automatisierten Konfigurationsüberwachung für Betriebssysteme

**Leitfaden**: Implementieren Sie eine Drittanbieterlösung, um den Zustand der Betriebssysteme Ihrer Clusterknoten zu überwachen.

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="711-manage-azure-secrets-securely"></a>7.11: Sicheres Verwalten von Azure-Geheimnissen

**Leitfaden**: Azure HDInsight beinhaltet BYOK-Unterstützung (Bring Your Own Key) für Apache Kafka. Dank dieser Funktion können Sie die Verantwortung und die Verwaltung für die Schlüssel übernehmen, die zum Verschlüsseln ruhender Daten verwendet werden.

Alle verwalteten Datenträger in Azure HDInsight werden mit der Speicherdienstverschlüsselung (Storage Service Encryption, SSE) von Azure geschützt. Die Daten auf diesen Datenträgern werden standardmäßig mit von Microsoft verwalteten Schlüsseln verschlüsselt. Bei der Aktivierung von BYOK geben Sie den Verschlüsselungsschlüssel für Azure HDInsight für die Verwendung und Verwaltung mit Azure Key Vault an.

Key Vault kann auch mit Azure HDInsight-Bereitstellungen verwendet werden, um Schlüssel für Clusterspeicher (Azure Storage-Konten und Azure Data Lake Storage) zu verwalten.

Verwenden von „Bring Your Own Key“ für Apache Kafka in Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Verwalten von Verschlüsselungsschlüsseln für Azure Storage-Konten:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Sicheres und automatisches Verwalten von Identitäten

**Leitfaden**: Verwaltete Identitäten können in Azure HDInsight verwendet werden, um Ihren Clustern den Zugriff auf Azure Active Directory Domain Services, Azure Key Vault oder Dateien in Azure Data Lake Storage Gen2 zu ermöglichen.

Grundlegendes zu verwalteten Identitäten mit Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Beheben der unbeabsichtigten Offenlegung von Anmeldeinformationen

**Leitfaden**: Bei Verwendung von Code zu Ihrer Azure HDInsight-Bereitstellung können Sie Credential Scanner implementieren, um Anmeldeinformationen im Code zu identifizieren. In Credential Scanner wird auch das Verschieben von ermittelten Anmeldeinformationen an sicherere Speicherorte (z. B. Azure Key Vault) empfohlen. 

Einrichten von Credential Scanner:

https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="malware-defense"></a>Schutz vor Schadsoftware

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Schutz vor Schadsoftware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Verwenden einer zentral verwalteten Antischadsoftware

**Leitfaden**: Bei Azure HDInsight ist Clamscan vorinstalliert und für die Clusterknotenimages aktiviert, aber Sie müssen die Software verwalten und alle von Clamscan erzeugten Protokolle manuell aggregieren bzw. überwachen.

Grundlegendes zu Clamscan für Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vorabprüfen von in computefremde Azure-Ressourcen hochzuladenden Dateien

**Leitfaden**: Microsoft Antimalware ist auf dem zugrunde liegenden Host für die Azure-Dienste aktiviert, wird aber nicht für Kundeninhalte ausgeführt.

Führen Sie für alle Dateien, die auf Azure-Ressourcen Ihrer Azure HDInsight-Clusterbereitstellung hochgeladen werden, z. B. Data Lake Storage, Blob Storage usw., eine Vorabprüfung durch. Microsoft kann auf diesen Instanzen nicht auf Kundendaten zugreifen.

Grundlegendes zu Microsoft Antimalware für Azure Cloud Services und Virtual Machines:

 https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Shared

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>Schritt 8.3: Sicherstellen der Aktualisierung von Antischadsoftware und Signaturen

**Leitfaden**: Bei Azure HDInsight ist Clamscan vorinstalliert und für die Clusterknotenimages aktiviert. Clamscan führt automatische Updates für die Engine und die Definitionen durch, aber die Aggregation und Verwaltung der Protokolle muss manuell erfolgen.

Grundlegendes zu Clamscan für Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="data-recovery"></a>Datenwiederherstellung

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Datenwiederherstellung](../security/benchmarks/security-control-data-recovery.md).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Sicherstellen regelmäßiger automatisierter Sicherungen

**Leitfaden**: Wählen Sie die entsprechende Redundanzoption (LRS, ZRS, GRS, RA-GRS) aus, wenn Sie ein Azure Storage-Konto für den HDInsight-Clusterdatenspeicher verwenden.  Konfigurieren Sie bei Verwendung einer Azure SQL-Datenbankinstanz für den Azure HDInsight-Clusterdatenspeicher die aktive Georeplikation.

Konfigurieren der Speicherredundanz für Azure Storage-Konten:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Konfigurieren von Redundanz für Azure SQL-Datenbank:

https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Durchführen vollständiger Systemsicherungen und Sichern aller von Kunden verwalteten Schlüssel

**Leitfaden**: Wählen Sie die entsprechende Redundanzoption (LRS, ZRS, GRS, RA-GRS) aus, wenn Sie ein Azure Storage-Konto für den Azure HDInsight-Clusterdatenspeicher verwenden. Wenn Sie Azure Key Vault für einen Teil Ihrer Azure HDInsight-Bereitstellung verwenden, sollten Sie sicherstellen, dass Ihre Schlüssel gesichert werden.

Wählen Sie Speicheroptionen für Ihren Azure HDInsight-Cluster aus:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Konfigurieren der Speicherredundanz für Azure Storage-Konten:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Sichern von Key Vault-Schlüsseln in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Überprüfen aller Sicherungen einschließlich der von Kunden verwalteten Schlüssel

**Leitfaden**: Wenn Azure Key Vault für Ihre Azure HDInsight-Bereitstellung verwendet wird, sollten Sie die Wiederherstellung der gesicherten vom Kunden verwalteten Schlüssel testen.

Verwenden von „Bring Your Own Key“ für Apache Kafka in Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Wiederherstellen von Key Vault-Schlüsseln in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Sicherstellen des Schutzes von Sicherungen und von kundenseitig verwalteten Schlüsseln

**Leitfaden**: Wenn Azure Key Vault für Ihre Azure HDInsight-Bereitstellung verwendet wird, sollten Sie in Key Vault das vorläufige Löschen aktivieren, um Schlüssel vor dem versehentlichen oder böswilligen Löschen zu schützen.

Aktivieren des vorläufigen Löschens in Azure Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="incident-response"></a>Reaktion auf Vorfälle

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Reaktion auf Vorfälle](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Erstellen eines Leitfadens für die Reaktion auf Vorfälle

**Leitfaden**: Stellen Sie sicher, dass es schriftliche Pläne für die Reaktion auf Vorfälle gibt, in denen die Rollen der Mitarbeiter sowie die Phasen der Bearbeitung und Verwaltung von Vorfällen definiert sind.

Konfigurieren von Workflowautomatisierungen in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Erstellen eines Verfahrens zur Bewertung und Priorisierung von Vorfällen

**Leitfaden**: Security Center weist Warnungen einen Schweregrad zu, um Ihnen zu helfen, die Reihenfolge zu priorisieren, in der Sie sich um Warnungen kümmern. So können Sie, sobald eine Ressource gefährdet ist, sofort zu ihr gelangen. Der Schweregrad basiert darauf, wie zuversichtlich Security Center in Bezug auf den Befund oder die Analyse ist, die zum Auslösen der Warnung verwendet wird, sowie auf dem Zuverlässigkeitsgrad, dass hinter der Aktivität, die zu der Warnung führte, eine böswillige Absicht stand.

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="103-test-security-response-procedures"></a>10.3: Verfahren zum Testen der Reaktion auf Sicherheitsvorfälle

**Leitfaden**: Führen Sie in regelmäßigen Abständen Tests zur Reaktionsfähigkeit Ihrer Systeme auf Vorfälle durch. Identifizieren Sie Schwachstellen und Lücken, und überarbeiten Sie den Plan bei Bedarf. Informationen hierzu finden Sie in der folgenden Veröffentlichung des NIST: Leitfaden zum Testen, Trainieren und Ausführen von Programmen für IT-Pläne und -Funktionen: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Angeben von Kontaktdaten für Sicherheitsvorfälle und Konfigurieren von Warnungsbenachrichtigungen für Sicherheitsvorfälle

**Leitfaden**: Microsoft wendet sich unter den für Sicherheitsvorfälle angegebenen Kontaktdaten an Sie, wenn das Microsoft Security Response Center (MSRC) feststellt, dass Personen unrechtmäßig oder unbefugt auf Ihre Daten zugegriffen haben.

Festlegen der Kontaktinformationen in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center-Überwachung**: Ja

**Verantwortlichkeit**: Kunde

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Integrieren von Sicherheitswarnungen in das System zur Reaktion auf Vorfälle

**Leitfaden**: Exportieren Sie die Azure Security Center-Warnungen und -Empfehlungen über die Funktion „Fortlaufender Export“. Über „Fortlaufender Export“ können Sie Warnungen und Empfehlungen entweder manuell oder kontinuierlich exportieren. Sie können den Azure Security Center-Datenconnector verwenden, um die Warnungen an Azure Sentinel zu streamen.

Konfigurieren des fortlaufenden Exports:

https://docs.microsoft.com/azure/security-center/continuous-export

Streamen von Warnungen in Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatisieren der Reaktion auf Sicherheitswarnungen

**Leitfaden**: Verwenden Sie die Funktion „Workflowautomatisierung“ in Azure Security Center, um über „Logic Apps“ automatisch Reaktionen auf Sicherheitswarnungen und -empfehlungen auszulösen.

Konfigurieren von „Workflowautomatisierung“ und Logic Apps:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center-Überwachung**: Zurzeit nicht verfügbar

**Verantwortlichkeit**: Kunde

## <a name="penetration-tests-and-red-team-exercises"></a>Penetrationstests und Red Team-Übungen

*Weitere Informationen finden Sie unter [Sicherheitskontrolle: Penetrationstests und Red Team-Übungen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Durchführen regelmäßiger Penetrationstests ihrer Azure-Ressourcen und Sicherstellen der Behebung aller kritischen Sicherheitsergebnisse innerhalb von 60 Tagen

**Leitfaden**: Befolgen Sie die Microsoft Rules of Engagement, um sicherzustellen, dass die Penetrationstests nicht gegen Microsoft-Richtlinien verstoßen:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Weitere Informationen zur Microsoft-Strategie und Durchführung von Red Team- und Livewebsite-Penetrationstests für von Microsoft verwaltete Cloudinfrastruktur, Dienste und Anwendungen finden Sie hier: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e.

**Azure Security Center-Überwachung**: Nicht verfügbar

**Verantwortlichkeit**: Shared

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie den [Vergleichstest für die Azure-Sicherheit](../security/benchmarks/overview.md).
- Erfahren Sie mehr über [Azure-Sicherheitsbaselines](../security/benchmarks/security-baselines-overview.md).