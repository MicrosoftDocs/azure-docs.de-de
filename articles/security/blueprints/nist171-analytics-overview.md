---
title: 'Vorlage für Azure Security and Compliance: Datenanalyse gemäß NIST SP 800-171'
description: 'Vorlage für Azure Security and Compliance: Datenanalyse gemäß NIST SP 800-171'
services: security
author: jomolesk
ms.assetid: ca75d2a8-4e20-489b-9a9f-3a61e74b032d
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: f79ba9ae60454d4e73c914fc1c8af675a6d07d5d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60608837"
---
# <a name="azure-security-and-compliance-blueprint---data-analytics-for-nist-sp-800-171"></a>Vorlage für Azure Security and Compliance: Datenanalyse gemäß NIST SP 800-171

## <a name="overview"></a>Übersicht
[NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) enthält Richtlinien zum Schützen der nicht klassifizierten kontrollierten Informationen (Controlled Unclassified Information, CUI), die sich in nicht behördlichen Informationssystemen und Organisationen befinden. In NIST SP 800-171 sind 14 „Familien“ mit Sicherheitsanforderungen zum Schützen der Vertraulichkeit von nicht klassifizierten kontrollierten Informationen festgelegt.

Diese Vorlage für Azure Security and Compliance enthält eine Anleitung, mit der Kunden eine Architektur für die Datenanalyse in Azure bereitstellen können, die eine Teilmenge der NIST SP 800-171-Kontrollmechanismen implementiert. Diese Lösung veranschaulicht die Möglichkeiten von Kunden, bestimmte Sicherheits- und Konformitätsanforderungen zu erfüllen. Sie dient auch als Grundlage für Kunden für die Erstellung und Konfiguration ihrer eigenen Lösungen für die Datenanalyse in Azure.

Diese Referenzarchitektur, die zugehörige Implementierungsanleitung und das Bedrohungsmodell sollen Kunden als Grundlage für die Anpassung an ihre spezifischen Anforderungen dienen. Sie sollten nicht unverändert in einer Produktionsumgebung verwendet werden. Die Bereitstellung dieser Architektur ohne Änderungen ist unzureichend, um die Anforderungen von NIST SP 800-171 vollständig zu erfüllen. Kunden sind dafür verantwortlich, angemessene Sicherheits- und Konformitätsbewertungen für jede Lösung durchzuführen, die mit dieser Architektur erstellt wurde. Die Anforderungen variieren je nach den Gegebenheiten der Implementierung des jeweiligen Kunden.

## <a name="architecture-diagram-and-components"></a>Architekturdiagramm und Komponenten
Diese Lösung umfasst eine Analyseplattform, auf der Kunden ihre eigenen Analysetools aufbauen können. Die Referenzarchitektur stellt einen generischen Anwendungsfall dar. Anhand dieser Referenzarchitektur können Kunden Daten über Massendatenimporte des SQL- bzw. Datenadministrators eingeben. Sie können anhand dieser Referenzarchitektur auch Daten über Aktualisierungen operativer Daten über einen betrieblichen Benutzer eingeben. Bei beiden Möglichkeiten wird Azure Functions für den Import von Daten in Azure SQL-Datenbank integriert. Azure Functions muss vom Kunden über das Azure-Portal konfiguriert werden, damit die Importaufgaben entsprechend den jeweiligen Analyseanforderungen des Kunden verarbeitet werden können.

Azure bietet eine Vielzahl von Berichts- und Analysediensten für Kunden. Diese Lösung verwendet Azure Machine Learning-Dienste und Azure SQL-Datenbank, damit Daten schnell durchsucht werden und durch eine bessere Modellierung von Daten schneller Ergebnisse geliefert werden können. Azure Machine Learning dient zur Erhöhung der Abfragegeschwindigkeit, indem neue Beziehungen zwischen Datasets ermittelt werden. Zunächst werden Daten über mehrere statistische Funktionen trainiert. Anschließend können bis zu sieben zusätzliche Abfragepools mit den gleichen tabellarischen Modellen synchronisiert werden, um die Abfrageworkload zu verteilen und Reaktionszeiten zu reduzieren. Durch den Kundenserver steigt die Gesamtzahl der Abfragepools auf acht.

Für erweiterte Analysen und Berichterstattungen kann Azure SQL-Datenbank mit Spaltenspeicherindizes konfiguriert werden. Azure Machine Learning und Azure SQL-Datenbank können abhängig von der Nutzung durch den Kunden zentral hoch- bzw. herunterskaliert oder vollständig ausgeschaltet werden. Jeglicher SQL-Datenverkehr wird mit SSL über selbstsignierte Zertifikate verschlüsselt. Als bewährte Methode wird die Verwendung einer vertrauenswürdigen Zertifizierungsstelle für erweiterte Sicherheit empfohlen.

Nachdem Daten in Azure SQL-Datenbank hochgeladen und von Azure Machine Learning trainiert wurden, werden sie vom betrieblichen Benutzer und vom SQL- bzw. Datenadministrator über Power BI verarbeitet. Power BI zeigt Daten intuitiv an und erfasst Informationen für mehrere Datasets, um zusätzliche Erkenntnisse zu erhalten. Power BI verfügt über ein hohes Maß an Anpassungsfähigkeit und einfache Integration in Azure SQL-Datenbank. Kunden können die Lösung für die Verarbeitung einer Vielzahl von Szenarios konfigurieren, die für ihre geschäftlichen Anforderungen erforderlich sind.

Die gesamte Lösung baut auf Azure Storage-Dienst auf, den Kunden über das Azure-Portal konfigurieren können. Azure Storage verschlüsselt alle Daten über die Speicherdienstverschlüsselung, um die Vertraulichkeit von ruhenden Daten zu bewahren. Durch georedundanten Speicher wird sichergestellt, dass ein Zwischenfall im primären Rechenzentrum des Kunden keinen Datenverlust verursacht. An einem separaten Standort Hunderte von Kilometern entfernt wird eine zweite Kopie gespeichert.

Für höhere Sicherheit werden alle Ressourcen in dieser Lösung als Ressourcengruppe über Azure Resource Manager verwaltet. Die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure Active Directory (Azure AD) dient zur Steuerung des Zugriffs auf bereitgestellte Ressourcen. Zu diesen Ressourcen zählen Kundenschlüssel in Azure Key Vault. Die Systemintegrität wird durch Azure Security Center und Azure Monitor überwacht. Kunden konfigurieren beide Überwachungsdienste für die Erfassung von Protokollen. Die Systemintegrität wird auf einem einzigen, benutzerfreundlichen Dashboard angezeigt.

Die Azure SQL-Datenbank wird im Allgemeinen über SQL Server Management Studio verwaltet. Es wird über einen lokalen Computer so konfiguriert, dass über eine sichere VPN-Verbindung oder eine Azure ExpressRoute-Verbindung auf die SQL-Datenbank zugegriffen werden kann. *Es wird empfohlen, eine VPN- oder ExpressRoute-Verbindung für die Verwaltung und den Datenimport in die Ressourcengruppe zu konfigurieren*.

![Datenanalyse gemäß NIST SP 800-171 – Diagramm zur Referenzarchitektur](images/nist171-analytics-architecture.png "Datenanalyse gemäß NIST SP 800-171 – Diagramm zur Referenzarchitektur")

Diese Lösung verwendet die folgenden Azure-Dienste. Weitere Informationen finden Sie im Abschnitt [Bereitstellungsarchitektur](#deployment-architecture).

- Application Insights
- Azure Active Directory
- Azure Data Catalog
- Azure Disk Encryption
- Azure Event Grid
- Azure-Funktionen
- Azure Key Vault
- Azure Machine Learning
- Azure Monitor (Protokolle)
- Azure Security Center
- Azure SQL-Datenbank
- Azure Storage
- Virtuelles Azure-Netzwerk
    - (1) /16 Netzwerk
    - (2) /24 Netzwerke
    - (2) Netzwerksicherheitsgruppen
- Power BI-Dashboard

## <a name="deployment-architecture"></a>Bereitstellungsarchitektur
Im folgenden Abschnitt werden die Elemente für Entwicklung und Implementierung beschrieben.

**Azure Event Grid**: Mit [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) können Kunden mühelos Anwendungen mit ereignisbasierten Architekturen erstellen. Benutzer wählen die Azure-Ressource aus, die sie abonnieren möchten. Anschließend teilen sie dem Ereignishandler oder dem Webhook einen Endpunkt zu, an den das Ereignis gesendet werden soll. Kunden können ihren Webhook-Endpunkt sichern, indem sie der Webhook-URL beim Erstellen eines Ereignisabonnements Abfrageparameter hinzufügen. Event Grid unterstützt nur HTTPS-Webhook-Endpunkte. Mit Event Grid können Kunden die Zugriffsebene steuern, die unterschiedlichen Benutzern zum Ausführen verschiedener Verwaltungsvorgänge zugeordnet wird. Benutzer können Ereignisabonnements auflisten, neue Abonnements erstellen und Schlüssel generieren. Event Grid verwendet Azure RBAC.

**Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) ist ein serverloser Computedienst, der die bedarfsgesteuerte Codeausführung ermöglicht. Sie müssen eine Infrastruktur nicht explizit bereitstellen oder verwalten. Verwenden Sie Azure Functions, um ein Skript oder einen Codeabschnitt als Reaktion auf verschiedene Ereignisse auszuführen.

**Azure Machine Learning Service**: [Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/) ist ein Data Science-Verfahren, mit dem Computer aus vorhandenen Daten lernen können, um zukünftiges Verhalten, Ergebnisse und Trends vorherzusagen.

**Azure Data Catalog**: [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) sorgt dafür, dass Datenquellen von den Benutzern, die die Daten verwalten, leicht ermittelt und verstanden werden können. Häufig verwendete Datenquellen können registriert und markiert und nach personenbezogenen Daten durchsucht werden. Die Daten verbleiben an ihrem vorhandenen Speicherort, aber eine Kopie der Metadaten wird zu Azure Data Catalog hinzugefügt. Darin inbegriffen ist ein Verweis auf den Speicherort der Datenquelle. Die Metadaten werden indiziert, damit die einzelnen Datenquellen über die Suchfunktion mühelos ermittelt werden können. Durch die Indizierung wird dies auch für die ermittelnden Benutzer nachvollziehbar.

### <a name="virtual-network"></a>Virtuelles Netzwerk
Diese Referenzarchitektur definiert ein privates virtuelles Netzwerk mit dem Adressraum 10.0.0.0/16.

**Netzwerksicherheitsgruppen**: [Netzwerksicherheitsgruppen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSGs) enthalten Zugriffssteuerungslisten, die den Datenverkehr in einem virtuellen Netzwerk zulassen oder ablehnen. NSGs können verwendet werden, um Datenverkehr auf der Ebene eines Subnetzes oder eines einzelnen virtuellen Computers zu schützen. Die folgenden NSGs sind vorhanden:
  - eine NSG für Active Directory
  - eine NSG für die Workload

Jede der NSGs verfügt über bestimmte offene Ports und Protokolle, damit die Lösung sicher und richtig ausgeführt werden kann. Darüber hinaus werden die folgenden Konfigurationen für jede NSG aktiviert:
  - [Diagnoseprotokolle und -ereignisse](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) sind aktiviert und werden im Speicherkonto gespeichert.
  - Azure Monitor-Protokolle ist mit der [Diagnose der NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json) verbunden.

**Subnetze**: Jedem Subnetz ist die entsprechende NSG zugeordnet.

### <a name="data-in-transit"></a>Daten während der Übertragung
Azure verschlüsselt standardmäßig die gesamte Kommunikation zu und von Azure-Rechenzentren. Alle Transaktionen für Azure Storage über das Azure-Portal werden per HTTPS durchgeführt.

### <a name="data-at-rest"></a>Ruhende Daten

Die Architektur schützt ruhende Daten durch die Verwendung von Verschlüsselung, Datenbanküberwachung und anderen Maßnahmen.

**Azure Storage**: Zur Erfüllung der Anforderungen an verschlüsselte ruhende Daten wird für den gesamten [Storage](https://azure.microsoft.com/services/storage/)-Bereich die [Speicherdienstverschlüsselung](https://docs.microsoft.com/azure/storage/storage-service-encryption) verwendet. Dadurch werden Daten geschützt, um die Zusagen und Anforderungen der Organisation im Hinblick auf die Sicherheit und Compliance gemäß NIST SP 800-171 einzuhalten.

**Azure Disk Encryption**: Für [Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) wird das BitLocker-Feature von Windows verwendet, um Volumeverschlüsselung für Datenträger bereitzustellen. Die Lösung ist in Azure Key Vault integriert, um die Steuerung und Verwaltung der Datenträgerverschlüsselungsschlüssel zu erleichtern.

**Azure SQL-Datenbank**: In der SQL-Datenbank-Instanz werden die folgenden Datenbanksicherheitsmaßnahmen verwendet:
-   Die [Active Directory-Authentifizierung und -Autorisierung](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) ermöglicht die zentrale Verwaltung der Identitäten von Datenbankbenutzern und anderen Microsoft-Diensten.
-   Die [SQL-Datenbank-Überprüfung](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) verfolgt Datenbankereignisse nach und schreibt diese in ein Überwachungsprotokoll in einem Azure Storage-Konto.
-   Azure SQL-Datenbank wird für die Verwendung der [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) konfiguriert. Bei diesem Vorgang wird die Echtzeitverschlüsselung und -entschlüsselung der Datenbank, der zugehörigen Sicherungen und der Transaktionsprotokolldateien durchgeführt, um ruhende Informationen zu schützen. Die transparente Datenverschlüsselung gewährleistet, dass gespeicherte Daten keinen unbefugten Zugriffen ausgesetzt sind.
-   [Firewallregeln](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) verhindern sämtlichen Zugriff auf Datenbankserver, bis die richtigen Berechtigungen erteilt werden. Die Firewall gewährt den Datenbankzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung.
-   [SQL-Bedrohungserkennung](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) ermöglicht die Erkennung und Reaktion auf potenzielle Bedrohungen, sobald diese auftreten. Sie stellt Warnungen zu verdächtigen Datenbankaktivitäten, potenziellen Sicherheitsrisiken, Angriffen durch Einschleusung von SQL-Befehlen sowie zu anomalen Datenbank-Zugriffsmustern bereit.
-   [Verschlüsselte Spalten](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) stellen sicher, dass vertrauliche Daten im Datenbanksystem niemals im Klartextformat angezeigt werden. Nach der Aktivierung der Datenverschlüsselung können nur Clientanwendungen oder Anwendungsserver, die Zugriff auf die Schlüssel haben, auf Klartextdaten zugreifen.
- Die [dynamische Datenmaskierung in SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) schränkt die Offenlegung vertraulicher Daten ein, indem diese Daten für nicht berechtigte Benutzer maskiert werden. Sie kann automatisch potenziell vertrauliche Daten ermitteln und eine geeignete Maskierung vorschlagen. Die dynamische Datenmaskierung unterstützt Sie beim Reduzieren des Zugriffs, damit vertrauliche Daten die Datenbank nicht über nicht autorisierten Zugriff verlassen. *Kunden sind dafür verantwortlich, die Einstellungen an ihr Datenbankschema anzupassen.*

### <a name="identity-management"></a>Identitätsverwaltung
Die folgenden Technologien enthalten Funktionen zum Verwalten des Zugriffs auf Daten in der Azure-Umgebung:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) ist der mehrinstanzenfähige cloudbasierte Verzeichnis- und Identitätsverwaltungsdienst von Microsoft. Alle Benutzer dieser Lösung werden in Azure AD erstellt. Dies gilt auch für die Benutzer, die Zugriff auf SQL-Datenbank haben.
-   Authentifizierung für die Anwendung erfolgt mithilfe von Azure AD. Weitere Informationen finden Sie unter [Integrieren von Anwendungen in Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Für die Verschlüsselung der Datenbankspalten wird Azure AD auch verwendet, um die Anwendung für Azure SQL-Datenbank zu authentifizieren. Weitere Informationen finden Sie unter [Schützen von vertraulichen Daten in SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) kann von Administratoren zum Definieren detaillierter Zugriffsberechtigungen verwendet werden. Damit können sie nur den Benutzern die Zugriffsrechte gewähren, die diese zum Ausführen ihrer Aufgaben benötigen. Anstatt jedem Benutzer uneingeschränkte Benutzerberechtigungen für Azure-Ressourcen zu gewähren, können Administratoren auch nur bestimmte Aktionen für den Zugriff auf Daten erlauben. Der Abonnementzugriff ist auf den Abonnementadministrator beschränkt.
- Mit [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) können Kunden die Anzahl von Benutzern mit Zugriff auf bestimmte Informationen, z.B. Daten, einschränken. Administratoren können mit Azure AD Privileged Identity Management privilegierte Identitäten und den damit verbundenen Zugriff auf Ressourcen erkennen, einschränken und überwachen. Diese Funktionalität kann auch verwendet werden, um einen bedarfsgesteuerten Just-in-Time-Administratorzugriff zu erzwingen.
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) erkennt potenzielle Sicherheitsrisiken, die Identitäten einer Organisation betreffen. Der Dienst konfiguriert automatische Reaktionen auf erkannte verdächtige Aktionen im Zusammenhang mit den Identitäten einer Organisation. Zudem untersucht er verdächtige Vorfälle, um entsprechende Maßnahmen zu deren Behebung zu ergreifen.

### <a name="security"></a>Sicherheit
**Geheimnisverwaltung**: Die Lösung verwendet [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) für die Verwaltung von Schlüsseln und Geheimnissen. Azure Key Vault unterstützt Sie dabei, kryptografische Schlüssel und geheime Schlüssel zu schützen, die von Cloudanwendungen und -diensten verwendet werden. Die folgenden Funktionen von Azure Key Vault unterstützen Kunden beim Schützen von Daten:
- Erweiterte Zugriffsrichtlinien werden auf Bedarfsbasis konfiguriert.
- Key Vault-Zugriffsrichtlinien werden mit minimalen erforderlichen Berechtigungen für Schlüssel und Geheimnisse definiert.
- Alle Schlüssel und Geheimnisse in Key Vault besitzen ein Ablaufdatum.
- Alle Schlüssel in Key Vault werden durch spezielle Hardwaresicherheitsmodule geschützt. Der Schlüsseltyp ist ein Hardwaresicherheitsmodul, das mit einem 2048-Bit-RSA-Schlüssel geschützt wird.
- Allen Benutzern und Identitäten werden mit RBAC die mindestens erforderlichen Berechtigungen erteilt.
- Diagnoseprotokolle für Key Vault werden mit einer Aufbewahrungsdauer von mindestens 365 Tagen aktiviert.
- Zulässige kryptographische Vorgänge für Schlüssel sind auf die erforderlichen Vorgänge beschränkt.

**Azure Security Center**: Mit [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) können Kunden Sicherheitsrichtlinien über Workloads hinweg zentral anwenden und verwalten, die Angriffsfläche verringern sowie Angriffe erkennen und darauf reagieren. Azure Security Center greift zudem auf die vorhandenen Konfigurationen der Azure-Dienste zu, um Empfehlungen für die Konfiguration und den Dienst bereitzustellen, die zur Verbesserung des Sicherheitsstatus und zum Schutz von Daten beitragen sollen.

 Azure Security Center verwendet verschiedene Erkennungsfunktionen, um Kunden vor potenziellen Angriffen auf deren Umgebung zu warnen. Diese Warnungen enthalten wichtige Informationen zum Auslöser der Warnung, zu den möglicherweise betroffenen Ressourcen und zur Quelle des Angriffs. In Security Center stehen verschiedene [vordefinierte Sicherheitswarnungen](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) zur Verfügung, die ausgelöst werden, wenn eine Bedrohung oder eine verdächtige Aktivität erkannt wird. Mithilfe [benutzerdefinierter Warnungsregeln](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) können Kunden neue Sicherheitswarnungen auf der Grundlage von Daten definieren, die bereits in ihrer Umgebung gesammelt werden.

 Security Center bietet priorisierte Sicherheitswarnungen und -vorfälle. Security Center erleichtert Kunden die Erkennung und Behebung potenzieller Sicherheitsprobleme. Für jede erkannte Bedrohung wird ein [Bericht über Bedrohungen](https://docs.microsoft.com/azure/security-center/security-center-threat-report) generiert. Teams zur Reaktion auf Vorfälle können die Berichte verwenden, wenn sie Bedrohungen untersuchen und beheben.

### <a name="logging-and-auditing"></a>Protokollierung und Überwachung

Azure-Dienste protokollieren umfassend die System- und Benutzeraktivitäten sowie die Systemintegrität:
- **Aktivitätsprotokolle**: [Aktivitätsprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) bieten Einblicke in Vorgänge, die für Ressourcen in einem Abonnement ausgeführt wurden. Aktivitätsprotokolle können dabei helfen, den Initiator eines Vorgangs, die Zeit des Auftretens und den Status zu bestimmen.
- **Diagnoseprotokolle**: [Diagnoseprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) umfassen sämtliche Protokolle, die von jeder Ressource ausgegeben werden. Zu diesen Protokollen gehören Windows-Ereignissystemprotokolle, Azure Storage-Protokolle, Key Vault-Überwachungsprotokolle sowie Azure Application Gateway-Zugriffs- und -Firewallprotokolle. Alle Diagnoseprotokolle werden für die Archivierung in ein zentrales und verschlüsseltes Azure Storage-Konto geschrieben. Benutzer können den Aufbewahrungszeitraum, bis zu 730 Tage, so konfigurieren, dass die spezifischen Anforderungen erfüllt werden.

**Azure Monitor-Protokolle:** Protokolle werden zur Verarbeitung, Speicherung und Dashboardanzeige in [Azure Monitor-Protokolle](https://azure.microsoft.com/services/log-analytics/) konsolidiert. Nach der Erfassung von Daten werden diese in separaten Tabellen für die einzelnen Datentypen in den Log Analytics-Arbeitsbereichen organisiert. Auf diese Weise können alle Daten zusammen analysiert werden, unabhängig von der zugehörigen ursprünglichen Quelle. Security Center lässt sich in Azure Monitor-Protokolle integrieren. Kunden können mithilfe von Kusto-Abfragen auf ihre Sicherheitsereignisdaten zugreifen und diese mit Daten von anderen Diensten kombinieren.

In dieser Architektur sind die folgenden Azure-[Überwachungslösungen](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) enthalten:
-   [Active Directory-Bewertung](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Mit der Lösung für die Active Directory-Integritätsüberprüfung können Sie in regelmäßigen Abständen die Risiken und die Integrität Ihrer Serverumgebungen bewerten. Sie stellt eine priorisierte Liste von Empfehlungen bereit, die spezifisch für Ihre bereitgestellte Serverinfrastruktur gelten.
- [SQL-Bewertung](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Mit der SQL-Lösung für die Integritätsüberprüfung können Sie in regelmäßigen Abständen die Risiken und die Integrität Ihrer Serverumgebungen bewerten. Die Lösung stellt für Kunden eine priorisierte Liste mit Empfehlungen bereit, die spezifisch für die bereitgestellte Serverinfrastruktur gelten.
- [Agent-Integritätsdiagnose](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Diese Lösung für die Agent-Integritätsdiagnose meldet die Anzahl von bereitgestellten Agents sowie deren geografische Verteilung. Zudem meldet sie, wie viele Agents nicht reagieren und wie viele Agents operative Daten übermitteln.
-   [Aktivitätsprotokollanalyse](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Die Lösung „Aktivitätsprotokollanalyse“ hilft bei der Analyse der Azure-Aktivitätsprotokolle in allen Azure-Abonnements eines Kunden.

**Azure Automation**: [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) dient zum Speichern, Ausführen und Verwalten von Runbooks. Bei dieser Lösung werden Protokolle über Runbooks für Azure SQL-Datenbank erfasst. Kunden können mithilfe der [Änderungsnachverfolgung](https://docs.microsoft.com/azure/automation/automation-change-tracking) von Azure Automation auf einfache Weise Änderungen in der Umgebung identifizieren.

**Azure Monitor**: [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) dient zum Nachverfolgen der Leistung, zum Gewährleisten der Sicherheit und zum Identifizieren von Trends. Organisationen können diese Plattform zum Überwachen, zum Erstellen von Warnungen und zum Archivieren von Daten verwenden. Zudem können sie die API-Aufrufe in ihren Azure-Ressourcen nachverfolgen.

**Application Insights**: [Application Insights](https://docs.microsoft.com/azure/application-insights/) ist ein erweiterbarer, für Webentwickler konzipierter Dienst zur Verwaltung der Anwendungsleistung auf mehreren Plattformen. Der Dienst erkennt Leistungsanomalien und enthält leistungsstarke Analysetools. Mithilfe der Tools können Probleme diagnostiziert und Kunden dabei unterstützt werden, den Verwendungszweck der App für Benutzer zu verstehen. Er unterstützt Benutzer bei der kontinuierlichen Verbesserung der Leistung und Benutzerfreundlichkeit der App.

## <a name="threat-model"></a>Bedrohungsmodell

Das Datenflussdiagramm für diese Referenzarchitektur steht zum [Download](https://aka.ms/nist171-analytics-tm) bereit oder kann hier gefunden werden. Dieses Modell kann Kunden dabei helfen, bei der Vornahme von Änderungen die potenziellen Risikopunkte in der Systeminfrastruktur zu verstehen.

![Datenanalyse gemäß NIST SP 800-171 – Bedrohungsmodell](images/nist171-analytics-threat-model.png "Datenanalyse gemäß NIST SP 800-171 – Bedrohungsmodell")

## <a name="compliance-documentation"></a>Konformitätsdokumentation
Die [Vorlage für Azure Security and Compliance: NIST SP 800-171 – Kundenzuständigkeitsmatrix](https://aka.ms/nist171-crm) listet alle Sicherheitskontrollen auf, die für NIST SP 800-171 erforderlich sind. In dieser Matrix ist angegeben, ob die Implementierung der einzelnen Kontrollen Aufgabe von Microsoft, des Kunden oder beider Seiten ist.

Die [Azure-Blaupause für Sicherheit und Compliance: Datenanalyse gemäß NIST SP 800-171 – Regulierungsimplementierungsmatrix](https://aka.ms/nist171-analytics-cim) enthält Informationen dazu, auf welche NIST SP 800-171-Kontrollen durch die Architektur der Datenanalyse verwiesen wird. Sie enthält zudem eine ausführliche Beschreibung, wie die Implementierung die Anforderungen der einzelnen Kontrollen erfüllt.


## <a name="guidance-and-recommendations"></a>Anleitungen und Empfehlungen
### <a name="vpn-and-expressroute"></a>VPN und ExpressRoute
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) oder ein sicherer VPN-Tunnel müssen für eine sichere Verbindung mit Ressourcen, die als Teil dieser Referenzarchitektur für die Datenanalyse bereitgestellt werden, konfiguriert werden. Durch die korrekte Einrichtung von VPN oder ExpressRoute können Kunden eine zusätzliche Sicherheitsebene für Daten während der Übertragung hinzufügen.

Mit der Implementierung eines sicheren VPN-Tunnels mit Azure kann eine virtuelle private Verbindung zwischen einem lokalen Netzwerk und einem virtuellen Azure-Netzwerk hergestellt werden. Diese Verbindung wird über das Internet hergestellt. Sie ermöglicht Kunden das sichere Übertragen von Informationen in einem „Tunnel“ innerhalb eines verschlüsselten Links zwischen ihrem Netzwerk und Azure. Eine Site-to-Site-VPN-Verbindung ist eine sichere, ausgereifte Technologie, die seit Jahrzehnten von Unternehmen aller Größen eingesetzt wird. Der [IPsec-Tunnelmodus](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) wird bei dieser Option als Verschlüsselungsmechanismus verwendet.

Da Datenverkehr im VPN-Tunnel über ein Site-to-Site-VPN über das Internet übertragen wird, bietet Microsoft eine weitere Verbindungsoption an, die noch sicherer ist. ExpressRoute ist ein dedizierter WAN-Link zwischen Azure und einem lokalen Standort oder einem Exchange-Hostinganbieter. ExpressRoute-Verbindungen stellen eine direkte Verbindung mit einem Telekommunikationsanbieter des Kunden her. Daher werden die Daten nicht über das Internet übertragen und im Internet verfügbar gemacht. Diese Verbindungen bieten mehr Zuverlässigkeit, eine höhere Geschwindigkeit, niedrigere Latenzzeiten und mehr Sicherheit als herkömmliche Verbindungen. 

Bewährte Methoden für das Implementieren eines sicheren hybriden Netzwerks, mit dem ein lokales Netzwerk auf Azure erweitert wird, finden Sie [hier](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>ETL-Prozess (Extrahieren, Transformieren, Laden)
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) kann Daten in Azure SQL-Datenbank laden, ohne ein separates Tool für den ETL-Prozess (Extrahieren, Transformieren, Laden) oder den Import zu verwenden. PolyBase ermöglicht den Zugriff auf Daten über T-SQL-Abfragen. Microsoft Business Intelligence und der Analysestapel sowie mit SQL Server kompatible Drittanbietertools können mit PolyBase verwendet werden.

### <a name="azure-ad-setup"></a>Azure AD-Setup
[Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) ist wichtig für die Verwaltung der Bereitstellung von Zugriff für Mitarbeiter, die mit der Umgebung interagieren. Das lokale Active Directory kann über [vier Klicks](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express) in Azure AD integriert werden. Kunden können die bereitgestellte Active Directory-Infrastruktur (Domänencontroller) auch mit Azure AD verknüpfen. Machen Sie die bereitgestellte Active Directory-Infrastruktur zu diesem Zweck zu einer Unterdomäne einer Azure AD-Gesamtstruktur.

## <a name="disclaimer"></a>Haftungsausschluss

 - Dieses Dokument dient nur zu Informationszwecken. MICROSOFT ÜBERNIMMT KEINE AUSDRÜCKLICHEN, STILLSCHWEIGENDEN ODER GESETZLICH VORGEGEBENEN GARANTIEN HINSICHTLICH DER INFORMATIONEN IN DIESEM DOKUMENT. Dieses Dokument wird so bereitgestellt, wie es ist. Informationen und Stellungnahmen in diesem Dokument, einschließlich URLs und anderer Verweise auf Internetwebsites, können ohne vorherige Ankündigung geändert werden. Kunden, die dieses Dokument lesen, tragen das Risiko, es zu verwenden.
 - Dieses Dokument gibt Kunden keine Rechte an geistigem Eigentum an Microsoft-Produkten oder -Lösungen.
 - Kunden dürfen dieses Dokument zu internen Referenzzwecken kopieren und verwenden.
 - Bestimmte Empfehlungen in diesem Dokument können zu einer erhöhten Nutzung von Daten, Netzwerken oder Computeressourcen in Azure sowie zu höheren Azure-Lizenz- oder -Abonnementkosten für den Kunden führen.
 - Diese Architektur soll Kunden als Grundlage für die Anpassung an ihre spezifischen Anforderungen dienen. Sie sollte nicht unverändert in einer Produktionsumgebung verwendet werden.
 - Dieses Dokument wurde als Referenz entwickelt und sollte nicht dazu verwendet werden, alle Mittel zu definieren, mit denen ein Kunde bestimmte Konformitätsanforderungen und -vorschriften erfüllen kann. Kunden sollten bei genehmigten Kundenimplementierungen rechtliche Unterstützung von ihrer Organisation einholen.
