---
title: Azure Security and Compliance Blueprint – Data Warehouse gemäß NHS (Vereinigtes Königreich)
description: Azure Security and Compliance Blueprint – Data Warehouse gemäß NHS (Vereinigtes Königreich)
services: security
author: jomolesk
ms.assetid: f4e4b939-88db-4d78-8fa9-c2a12b2c025b
ms.service: security
ms.topic: article
ms.date: 06/15/2018
ms.author: jomolesk
ms.openlocfilehash: 12042b853682efcff2de285a97508b8a81b1d647
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60610091"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-uk-nhs"></a>Azure-Blaupause für Sicherheit und Compliance: Data Warehouse für NHS (Vereinigtes Königreich)

## <a name="overview"></a>Übersicht

Diese Azure Security and Compliance Blueprint bietet eine Referenzarchitektur und Anleitungen für eine geeignete Data Warehouse-Lösung für das sichere Erfassen, Staging und Speichern von vertraulichen Daten des Gesundheitswesens sowie das Interagieren mit diesen. Diese Lösung veranschaulicht Möglichkeiten, wie Kunden die Hinweise im von [NHS Digital](https://digital.nhs.uk/) als Partner für das Department of Health and Social Care (DHSC) des Vereinigten Königreichs veröffentlichten [Cloud Security Good Practice Guide](https://digital.nhs.uk/data-and-information/looking-after-information/data-security-and-information-governance/nhs-and-social-care-data-off-shoring-and-the-use-of-public-cloud-services/health-and-social-care-cloud-security-good-practice-guide) (Leitfaden für bewährte Methoden in der Cloudsicherheit) einhalten können. Der Leitfaden für bewährte Methoden in der Cloudsicherheit basiert auf den 14 [Cloudsicherheitsprinzipien](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles), die vom britischen National Cyber Security Center (NCSC) veröffentlicht wurden.

Diese Referenzarchitektur, die Implementierungsanleitung und das Bedrohungsmodell sollen Kunden als Grundlage für die Anpassung an ihre spezifischen Anforderungen dienen. Sie sollten nicht ohne weitere Konfiguration in einer Produktionsumgebung eingesetzt werden. Der Kunde ist für die Durchführung einer angemessenen Sicherheits- und Konformitätsbewertung jeder Lösung verantwortlich, die mit dieser Architektur erstellt wurde, da die Anforderungen je nach den Besonderheiten der jeweiligen Implementierung des Kunden unterschiedlich sein können.

## <a name="architecture-diagram-and-components"></a>Architekturdiagramm und Komponenten

Diese Lösung bietet eine Referenzarchitektur, die ein leistungsstarkes und sicheres Cloud-Data Warehouse implementiert. Es gibt zwei separate Datenschichten in dieser Architektur: eine, in die die Daten importiert und gespeichert werden und die in einer SQL-Clusterumgebung gestaffelt ist, und eine Schicht für Azure SQL Data Warehouse, in der die Daten für die Verarbeitung mit einem ETL-Tool (extrahieren, transformieren, laden) geladen werden (z. B. [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)-T-SQL-Abfragen). Für die in Azure SQL Data Warehouse gespeicherten Daten können umfangreiche Analysen durchgeführt werden.

Azure bietet eine Vielzahl von Berichts- und Analysediensten für Kunden. Diese Lösung umfasst SQL Server Reporting Services für die schnelle Erstellung von Berichten aus Azure SQL Data Warehouse. Jeglicher SQL-Datenverkehr wird mit SSL über selbstsignierte Zertifikate verschlüsselt. Azure empfiehlt als bewährte Methode die Verwendung einer vertrauenswürdigen Zertifizierungsstelle für erweiterte Sicherheit.

Daten in Azure SQL Data Warehouse werden in relationalen Tabellen spaltenweise gespeichert. Dieses Format reduziert die Kosten für die Datenspeicherung erheblich und verbessert die Abfrageleistung.  Je nach Nutzungsanforderungen kann Azure SQL Data Warehouse Ressourcen zentral hoch- oder herunterskalieren oder vollständig herunterfahren, wenn keine aktiven Prozesse Computerressourcen erfordern.

Ein SQL-Lastenausgleich verwaltet den SQL-Datenverkehr und stellt eine hohe Leistung sicher. Alle virtuellen Computer in dieser Referenzarchitektur werden in einer Verfügbarkeitsgruppe mit SQL Server-Instanzen bereitgestellt, die in einer Always On-Verfügbarkeitsgruppe für Hochverfügbarkeit und die Notfallwiederherstellung konfiguriert wurden.

Für höhere Sicherheit werden alle Ressourcen in dieser Lösung als Ressourcengruppe über Azure Resource Manager verwaltet. Die rollenbasierte Zugriffssteuerung von Azure Active Directory dient zur Steuerung des Zugriffs auf die bereitgestellten Ressourcen und Schlüssel in Azure Key Vault. Die Systemintegrität wird durch Azure Security Center und Azure Monitor überwacht. Der Kunde konfiguriert das Überwachen von Diensten zum Speichern von Protokollen und Anzeigen der Systemintegration in nur einem Dashboard, durch das man leicht navigieren kann.


Diese Data Warehouse-Referenzarchitektur enthält auch eine Active Directory-Ebene für die Verwaltung von Ressourcen innerhalb der Architektur. Das Active Directory-Subnetz ermöglicht die einfache Übernahme in einer größeren Active Directory-Gesamtstruktur und erlaubt damit den fortlaufenden Betrieb der Umgebung, selbst wenn der Zugriff auf die größere Gesamtstruktur nicht möglich ist. Alle virtuellen Computer sind in Domänen der Active Directory-Ebene eingebunden und verwenden Active Directory-Gruppenrichtlinien, um Sicherheits- und Konformitätskonfigurationen auf Betriebssystemebene durchzusetzen.

Ein virtueller Computer fungiert als geschützter Host (Bastionhost) für die Verwaltung und bietet Administratoren eine sichere Verbindung für den Zugriff auf bereitgestellte Ressourcen. Die Daten werden über diesen verwaltenden Bastionhost in den Staging-Bereich geladen. **Microsoft empfiehlt das Konfigurieren einer VPN- oder Azure ExpressRoute-Verbindung für die Verwaltung und den Datenimport in das Subnetz der Referenzarchitektur.**

![Abbildung der Referenzarchitektur für Data Warehouse gemäß NHS (Vereinigtes Königreich)](images/uknhs-datawarehouse-architecture.png?raw=true "Abbildung der Referenzarchitektur für Data Warehouse gemäß NHS (Vereinigtes Königreich)")

Diese Lösung verwendet die folgenden Azure-Dienste. Details zur Bereitstellungsarchitektur finden Sie im Abschnitt [Bereitstellungsarchitektur](#deployment-architecture).

- Verfügbarkeitsgruppen
    - Active Directory-Domänencontroller
    - SQL-Clusterknoten und -Zeuge
- Azure Active Directory
- Azure Data Catalog
- Azure Key Vault
- Azure Monitor
- Azure Security Center
- Azure Load Balancer
- Azure Storage
- Azure-Automatisierung
- Azure Virtual Machines
    - (1) Bastionhost
    - (2) Active Directory-Domänencontroller
    - (2) SQL Server-Clusterknoten
    - (1) SQL Server-Zeuge
- Virtuelles Azure-Netzwerk
    - (1) /16 Netzwerk
    - (4) /24 Netzwerke
    - (4) Netzwerksicherheitsgruppen
- Recovery Services-Tresor
- SQL Data Warehouse
- SQL Server Reporting Services

## <a name="deployment-architecture"></a>Bereitstellungsarchitektur

Im folgenden Abschnitt werden die Elemente für Entwicklung und Implementierung beschrieben.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) ist ein Enterprise Data Warehouse mit Massively Parallel Processing zur schnellen Ausführung komplexer Abfragen für Daten im Petabyte-Bereich, sodass Benutzer Daten des Gesundheitswesens effizient ermitteln können. Benutzer können große Datenmengen mit einfachen PolyBase-T-SQL-Abfragen in SQL Data Warehouse importieren und dann die Vorteile von Massively Parallel Processing für aufwendige Analysen nutzen.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) erlaubt die schnelle Erstellung von Berichten mit Tabellen, Diagrammen, Karten, Maßstäben, Matrizen u.v.m. für Azure SQL Data Warehouse.

**Data Catalog**: [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) macht Datenquellen für Benutzer, die die Daten verwalten, einfach ermittelbar und verständlich. Häufig verwendete Datenquellen können registriert und markiert und nach Gesundheitsdaten durchsucht werden. Die Daten verbleiben an ihrem vorhandenen Speicherort, aber eine Kopie der Metadaten wird Azure Data Catalog hinzugefügt, zusammen mit einem Verweis auf den Speicherort der Datenquelle. Die Metadaten werden außerdem indiziert, damit jede Datenquelle per Suchfunktion einfach ermittelt werden kann und für die Benutzer, die sie ermitteln, verständlich ist.

**Bastionhost**: Der Bastionhost ist der einzelne Zugangspunkt, über den Benutzer Zugriff auf die bereitgestellten Ressourcen in dieser Umgebung erhalten. Der Bastionhost bietet eine sichere Verbindung mit den bereitgestellten Ressourcen, indem nur Remotedatenverkehr von öffentlichen IP-Adressen auf einer Sicherheitsliste zugelassen wird. Damit RDP-Datenverkehr (Remotedesktopprotokoll) zugelassen wird, muss die Quelle des Datenverkehrs in der Netzwerksicherheitsgruppe definiert sein.

Diese Lösung erstellt einen virtuellen Computer als Bastionhost in der Domäne mit den folgenden Konfigurationen:
-   [Antimalware-Erweiterung](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Azure-Diagnoseerweiterung](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) mit Azure Key Vault
-   Eine [Richtlinie zum automatischen Herunterfahren](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/), um die Nutzung der Ressourcen virtueller Computer zu verringern, wenn sie nicht benötigt werden
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) ist aktiviert, damit Anmeldeinformationen und andere Geheimnisse in einer geschützten Umgebung, die vom ausgeführten Betriebssystem isoliert ist, ausgeführt werden.

### <a name="virtual-network"></a>Virtuelles Netzwerk

Diese Referenzarchitektur definiert ein privates virtuelles Netzwerk mit dem Adressraum 10.0.0.0/16.

**Netzwerksicherheitsgruppen**: [Netzwerksicherheitsgruppen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) enthalten Zugriffssteuerungslisten, die den Datenverkehr in einem virtuellen Netzwerk zulassen oder ablehnen. Netzwerksicherheitsgruppen können verwendet werden, um Datenverkehr auf der Ebene eines Subnetzes oder eines einzelnen Computers zu schützen. Es gibt die folgenden Netzwerksicherheitsgruppen:

  - Eine Netzwerksicherheitsgruppe für die Datenschicht (SQL Server-Cluster, SQL Server-Zeuge und SQL-Lastenausgleich)
  - Eine Netzwerksicherheitsgruppe für den Verwaltungsbastionhost
  - Eine Netzwerksicherheitsgruppe für Active Directory
  - Eine Netzwerksicherheitsgruppe für SQL Server Reporting Services

Jede der Netzwerksicherheitsgruppen verfügt über bestimmte offene Ports und Protokolle, damit die Lösung sicher und richtig ausgeführt werden kann. Darüber hinaus werden die folgenden Konfigurationen für jede Netzwerksicherheitsgruppe aktiviert:

- [Diagnoseprotokolle und -ereignisse](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) sind aktiviert und werden im Speicherkonto gespeichert.
- Azure Monitor-Protokolle ist mit den [Diagnoseprotokollen der Netzwerksicherheitsgruppe](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json) verbunden.

**Subnetze**: Jedes Subnetz ist seiner entsprechenden Netzwerksicherheitsgruppe zugeordnet.

### <a name="data-at-rest"></a>Ruhende Daten

Die Architektur schützt ruhende Daten durch verschiedene Maßnahmen wie Verschlüsselung und Datenbanküberwachung.

Die Architektur schützt ruhende Daten durch die Verwendung von Verschlüsselung, Datenbanküberwachung und anderen Maßnahmen.

**Azure Storage**: Um die Anforderungen für verschlüsselte ruhende Daten zu erfüllen, wird überall in [Azure Storage](https://azure.microsoft.com/services/storage/) die [Speicherdienstverschlüsselung](https://docs.microsoft.com/azure/storage/storage-service-encryption) verwendet. Dadurch werden Daten geschützt, um die organisatorischen Sicherheitszusagen und Konformitätsanforderungen gemäß der Definition von NHS Digital einzuhalten.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) nutzt das BitLocker-Feature von Windows, um eine Volumeverschlüsselung für Datenträger bereitzustellen. Die Lösung ist in Azure Key Vault integriert, um die Steuerung und Verwaltung der Datenträgerverschlüsselungsschlüssel zu erleichtern.

**Azure SQL-Datenbank**: In der Azure SQL-Datenbankinstanz werden die folgenden Datenbanksicherheitsmaßnahmen verwendet:

- Die [Active Directory-Authentifizierung und -Autorisierung](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) ermöglicht die zentrale Verwaltung der Identitäten von Datenbankbenutzern und anderen Microsoft-Diensten.
- Die [SQL-Datenbank-Überprüfung](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) verfolgt Datenbankereignisse nach und schreibt diese in ein Überwachungsprotokoll in einem Azure Storage-Konto.
- Azure SQL-Datenbank ist so konfiguriert, dass [transparente Datenverschlüsselung](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) (Transparent Data Encryption) für die Echtzeitverschlüsselung und -entschlüsselung der Datenbank, der zugehörigen Sicherungen und der Transaktionsprotokolldateien verwendet wird, um ruhende Informationen zu schützen. Die transparente Datenverschlüsselung gewährleistet, dass gespeicherte Daten keinen unbefugten Zugriffen ausgesetzt sind.
- [Firewallregeln](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) verhindern sämtlichen Zugriff auf Datenbankserver, bis die richtigen Berechtigungen erteilt werden. Die Firewall gewährt den Datenbankzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung.
- Die [SQL-Bedrohungserkennung](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) ermöglicht die Erkennung und Reaktion auf potenzielle Bedrohungen, sobald sie auftreten. Dazu werden Sicherheitswarnungen bei verdächtigen Datenbankaktivitäten, potenziellen Sicherheitslücken, Angriffen durch Einschleusung von SQL-Befehlen und anormalen Mustern beim Datenbankzugriff ausgegeben.
- [Encrypted-Spalten](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) stellen sicher, dass vertrauliche Daten im Datenbanksystem niemals im Klartextformat angezeigt werden. Nach dem Aktivieren der Datenverschlüsselung können nur Clientanwendungen oder Anwendungsserver, die Zugriff auf die Schlüssel haben, auf Klartextdaten zugreifen.
- Die [dynamische Datenmaskierung in SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) schränkt die Offenlegung vertraulicher Daten ein, indem diese Daten für nicht berechtigte Benutzer maskiert werden. Die dynamische Datenmaskierung kann automatisch potenziell vertrauliche Daten ermitteln und eine geeignete Maskierung vorschlagen. Dies hilft, den Zugriff auf die Daten zu identifizieren und so zu reduzieren, dass sie die Datenbank nicht durch unberechtigten Zugriff verlassen. Kunden sind dafür verantwortlich, die Einstellungen für die dynamische Datenmaskierung an ihr Datenbankschema anzupassen.

### <a name="identity-management"></a>Identitätsverwaltung

Die folgenden Technologien enthalten Funktionen zum Verwalten des Zugriffs auf Daten in der Azure-Umgebung:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) ist der mehrinstanzenfähige cloudbasierte Verzeichnis- und Identitätsverwaltungsdienst von Microsoft. Alle Benutzer für diese Lösung werden in Azure Active Directory erstellt, so auch die Benutzer, die auf die Azure SQL-Datenbank zugreifen.
- Authentifizierung für die Anwendung erfolgt mithilfe von Azure Active Directory. Weitere Informationen finden Sie unter [Integrieren von Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Darüber hinaus wird für Verschlüsselung der Datenbankspalten Azure Active Directory verwendet, um die Anwendung in der Azure SQL-Datenbank zu authentifizieren. Weitere Informationen finden Sie unter [Schützen von vertraulichen Daten in SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Die [rollenbasierte Zugriffssteuerung in Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) (Role-Based Access Control) ermöglicht Administratoren das Definieren präziser Zugriffsberechtigungen, um jeweils nur den Zugriff zu gewähren, den Benutzer zum Erledigen ihrer Arbeit benötigen. Anstatt jedem Benutzer uneingeschränkte Benutzerberechtigungen für Azure-Ressourcen zu gewähren, können Administratoren auch nur bestimmte Aktionen für den Zugriff auf Daten erlauben. Der Abonnementzugriff ist auf den Abonnementadministrator beschränkt.
- Mit [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) können Kunden die Anzahl von Benutzern mit Zugriff auf bestimmte Informationen einschränken. Administratoren können mit Azure Active Directory Privileged Identity Management privilegierte Identitäten und den damit verbundenen Zugriff auf Ressourcen erkennen, einschränken und überwachen. Diese Funktionalität kann auch verwendet werden, um einen bedarfsgesteuerten Just-in-Time-Administratorzugriff zu erzwingen.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) erkennt potenzielle Sicherheitsrisiken, die sich auf die Identitäten einer Organisation auswirken, konfiguriert automatisierte Antworten auf erkannte verdächtige Aktionen, die im Zusammenhang mit Identitäten der Organisation stehen, untersucht verdächtige Vorfälle und führt die entsprechenden Aktionen zu deren Behebung aus.

### <a name="security"></a>Sicherheit

**Geheimnisverwaltung**: Die Lösung verwendet [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) für die Verwaltung von Schlüsseln und Geheimnissen. Azure Key Vault unterstützt Sie dabei, kryptografische Schlüssel und Geheimnisse zu schützen, die von Cloudanwendungen und -diensten verwendet werden. Die folgenden Funktionen von Azure Key Vault unterstützen Kunden beim Schützen von und Zugreifen auf diese Daten:

- Erweiterte Zugriffsrichtlinien werden auf Bedarfsbasis konfiguriert.
- Key Vault-Zugriffsrichtlinien werden mit minimalen erforderlichen Berechtigungen für Schlüssel und Geheimnisse definiert.
- Alle Schlüssel und Geheimnisse in Key Vault besitzen ein Ablaufdatum.
- Alle Schlüssel in Key Vault werden durch spezielle Hardwaresicherheitsmodule geschützt. Der Schlüsseltyp ist ein Hardwaresicherheitsmodul, das von einem 2.048-Bit-RSA-Schlüssel geschützt wird.
- Allen Benutzern und Identitäten werden mithilfe von rollenbasierter Zugriffssteuerung die erforderlichen Mindestberechtigungen erteilt.
- Diagnoseprotokolle für Key Vault werden mit einer Aufbewahrungsdauer von mindestens 365 Tagen aktiviert.
- Zulässige kryptographische Vorgänge für Schlüssel sind auf die erforderlichen Vorgänge beschränkt.

**Patchverwaltung**: Virtuelle Windows-Computer, die mit dieser Referenzarchitektur bereitgestellt werden, sind standardmäßig so konfiguriert, dass sie automatische Updates vom Windows Update-Dienst erhalten. Diese Lösung umfasst auch den Dienst [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro), über den aktualisierte Bereitstellungen erstellt werden können, um virtuelle Computer bei Bedarf zu patchen.

**Schutz vor Schadsoftware**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) für virtuelle Computer bietet Echtzeitschutzfunktionen, mit denen die Identifizierung und Entfernung von Viren, Spyware und anderer bösartiger Software ermöglicht wird, mit konfigurierbaren Warnmeldungen, wenn bekannte bösartige oder unerwünschte Software versucht, sich auf geschützten virtuellen Computern zu installieren oder die Ausführung zu starten.

**Azure Security Center**: Mit [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) können Kunden Sicherheitsrichtlinien über Workloads hinweg zentral anwenden und verwalten, die Angriffsfläche verringern sowie Angriffe erkennen und darauf reagieren. Darüber hinaus greift Azure Security Center auf die vorhandenen Konfigurationen der Azure-Dienste zu, um Empfehlungen für die Konfiguration und Verwaltung zu geben und zur Verbesserung der Sicherheit und zum Schutz Daten beizutragen.

Azure Security Center verwendet eine Vielzahl von Erkennungsfunktionen, um Kunden vor potenziellen Angriffen auf ihre Umgebungen zu warnen. Diese Warnungen enthalten wichtige Informationen zum Auslöser der Warnung, zu den möglicherweise betroffenen Ressourcen und zur Quelle des Angriffs. In Azure Security Center steht eine Reihe vordefinierter [Sicherheitswarnungen](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) zur Verfügung, die ausgelöst werden, wenn eine Bedrohung oder eine verdächtige Aktivität erkannt wird. Mithilfe [benutzerdefinierter Warnungsregeln](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) in Azure Security Center können Kunden neue Sicherheitswarnungen auf der Grundlage von Daten definieren, die bereits in ihrer Umgebung gesammelt werden.

Azure Security Center bietet priorisierte Sicherheitswarnungen und Incidents, sodass es für Kunden einfacher wird, potenzielle Sicherheitsprobleme zu erkennen und zu beheben. Ein [Bericht über Bedrohungen](https://docs.microsoft.com/azure/security-center/security-center-threat-report) wird zu jeder erkannten Bedrohung generiert, der den Teams, die für die Bearbeitung von Incidents zuständig sind, beim Untersuchen und Beheben von Bedrohungen als Hilfe dient.

Darüber hinaus nutzt diese Referenzarchitektur die [Sicherheitsrisikobewertung](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) in Azure Security Center. Nach der Konfiguration meldet ein Partner-Agent (z.B. Qualys) Daten zu Sicherheitsrisiken an die Verwaltungsplattform des Partners. Im Gegenzug liefert die Verwaltungsplattform des Partners Überwachungsdaten zu Sicherheitsrisiken und zur Integrität an Azure Security Center, sodass Kunden anfällige virtuelle Computer schnell identifizieren können.

### <a name="business-continuity"></a>Geschäftskontinuität

**Hochverfügbarkeit**: Serverworkloads werden in einer [Verfügbarkeitsgruppe](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) zusammengefasst, um Hochverfügbarkeit für virtuelle Computer in Azure besser sicherzustellen. Während einer geplanten oder ungeplanten Wartung ist mindestens ein virtueller Computer verfügbar, um die Azure-SLA von 99,95 % zu erfüllen.

**Recovery Services-Tresor**: Der [Recovery Services-Tresor](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) speichert Sicherungsdaten und schützt alle Konfigurationen von virtuellen Azure-Computern in dieser Architektur. Durch einen Recovery Services-Tresor können Kunden Dateien und Ordner von einem virtuellen IaaS-Computer wiederherstellen, ohne den gesamten virtuelle Computer wiederherstellen zu müssen. So werden die Wiederherstellungszeiten verkürzt.

### <a name="logging-and-auditing"></a>Protokollierung und Überwachung

Azure-Dienste protokollieren umfassend die System- und Benutzeraktivitäten sowie die Systemintegrität:
- **Aktivitätsprotokolle**: [Aktivitätsprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) bieten Einblicke in Vorgänge, die für Ressourcen in einem Abonnement ausgeführt wurden. Aktivitätsprotokolle können dabei helfen, den Initiator eines Vorgangs, die Zeit des Auftretens und den Status zu bestimmen.
- **Diagnoseprotokolle**: [Diagnoseprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) umfassen sämtliche Protokolle, die von jeder Ressource ausgegeben werden. Zu diesen Protokollen gehören Windows-Ereignissystemprotokolle, Azure Storage-Protokolle, Key Vault-Überwachungsprotokolle sowie Application Gateway-Zugriffs- und -Firewallprotokolle. Alle Diagnoseprotokolle werden für die Archivierung in ein zentrales und verschlüsseltes Azure Storage-Konto geschrieben. Die Aufbewahrung kann vom Benutzer konfiguriert werden (bis zu 730 Tage), um den unternehmensspezifischen Aufbewahrungsanforderungen gerecht zu werden.

**Azure Monitor-Protokolle:** Diese Protokolle werden dann zur Verarbeitung, Speicherung und Dashboardanzeige in [Azure Monitor-Protokolle](https://azure.microsoft.com/services/log-analytics/) konsolidiert. Nachdem die Daten gesammelt wurden, werden sie in separaten Tabellen für die einzelnen Datentypen angeordnet, damit alle Daten unabhängig von der ursprünglichen Quelle zusammen analysiert werden können. Darüber hinaus ist das Azure Security Center in Azure Monitor-Protokolle integriert, sodass Kunden mit Kusto-Abfragen auf ihre Sicherheitsereignisdaten zugreifen und diese mit Daten aus anderen Diensten kombinieren können.

In dieser Architektur sind die folgenden Azure-[Überwachungslösungen](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) enthalten:
-   [Active Directory-Bewertung](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Die Lösung „Active Directory-Integritätsüberprüfung“ bewertet die Risiken und die Integrität von Serverumgebungen in regelmäßigen Abständen und erstellt eine priorisierte Liste mit spezifischen Empfehlungen für die bereitgestellte Serverinfrastruktur.
- [SQL-Bewertung](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Die Lösung „SQL-Integritätsüberprüfung“ bewertet die Risiken und die Integrität von Serverumgebungen in regelmäßigen Abständen und erstellt für die Kunden eine priorisierte Liste mit spezifischen Empfehlungen für die bereitgestellte Serverinfrastruktur.
- [Agent-Integritätsdiagnose](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Die Lösung „Agent-Integritätsdiagnose“ meldet die Anzahl bereitgestellter Agents sowie deren geografische Verteilung. Außerdem meldet sie, wie viele Agents nicht mehr reagieren und wie viele Agents Betriebsdaten übermitteln.
-   [Aktivitätsprotokollanalyse](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Die Lösung „Aktivitätsprotokollanalyse“ hilft bei der Analyse der Azure-Aktivitätsprotokolle in allen Azure-Abonnements eines Kunden.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) dient zum Speichern, Ausführen und Verwalten von Runbooks. Bei dieser Lösung werden Protokolle über Runbooks für Azure SQL-Datenbank erfasst. Die [Änderungsnachverfolgung](https://docs.microsoft.com/azure/automation/automation-change-tracking) von Automation ermöglicht Kunden das Identifizieren von Änderungen in der Umgebung auf einfache Weise.

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) bietet Überwachungsfunktionen für Organisationen und ermöglicht die Erstellung von Warnungen sowie die Archivierung von Daten (einschließlich Nachverfolgung von API-Aufrufen in ihren Azure-Ressourcen), um Benutzer beim Nachverfolgen der Leistung, beim Aufrechterhalten der Sicherheit und beim Identifizieren von Trends zu unterstützen.

## <a name="threat-model"></a>Bedrohungsmodell

Das Datenflussdiagramm für diese Referenzarchitektur steht zum [Download](https://aka.ms/uknhs-dw-tm) bereit und ist unten angegeben. Dieses Modell kann Kunden dabei helfen, bei Änderungen die potenziellen Risikopunkte in der Systeminfrastruktur zu verstehen.

![Bedrohungsmodell für Data Warehouse gemäß NHS (Vereinigtes Königreich)](images/uknhs-datawarehouse-threat-model.png?raw=true "Bedrohungsmodell für Data Warehouse gemäß NHS (Vereinigtes Königreich)")

## <a name="compliance-documentation"></a>Konformitätsdokumentation

In [Azure Security and Compliance Blueprint – NHS-Zuständigkeitsmatrix (Vereinigtes Königreich) für Kunden](https://aka.ms/uknhs-crm) sind alle von NHS (Vereinigtes Königreich) geforderten Ziele aufgeführt. In dieser Matrix ist angegeben, ob die Implementierung der einzelnen Prinzipien Aufgabe von Microsoft, des Kunden oder beider Seiten ist.

Die [Azure Security and Compliance Blueprint – Implementierungsmatrix für Data Warehouse gemäß NHS (Vereinigtes Königreich)](https://aka.ms/uknhs-dw-cim) enthält Informationen darüber, auf welche NHS-Anforderungen durch die Data Warehouse-Architektur verwiesen wird, sowie eine ausführliche Beschreibung, wie die Implementierung die Anforderungen der einzelnen Prinzipien erfüllt.

## <a name="guidance-and-recommendations"></a>Anleitungen und Empfehlungen

### <a name="vpn-and-expressroute"></a>VPN und ExpressRoute

Zur Erzielung von sicheren Verbindungen mit Ressourcen, die als Teil dieser Referenzarchitektur einer PaaS-Webanwendung bereitgestellt werden, muss ein sicherer VPN-Tunnel oder eine [ExpressRoute-Verbindung](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) konfiguriert werden. Durch die korrekte Einrichtung von VPN oder ExpressRoute können Kunden eine zusätzliche Sicherheitsebene für Daten während der Übertragung hinzufügen.

Mit der Implementierung eines sicheren VPN-Tunnels mit Azure kann eine virtuelle private Verbindung zwischen einem lokalen Netzwerk und einem virtuellen Azure-Netzwerk hergestellt werden. Diese Verbindung wird über das Internet hergestellt und ermöglicht Kunden das Übertragen von Informationen in einem &quot;Tunnel&quot; innerhalb eines verschlüsselten Links zwischen dem Kundennetzwerk und Azure. Eine Site-to-Site-VPN-Verbindung ist eine sichere, ausgereifte Technologie, die seit Jahrzehnten von Unternehmen aller Größen eingesetzt wird. Der [IPsec-Tunnelmodus](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) wird bei dieser Option als Verschlüsselungsmechanismus verwendet.

Da Datenverkehr im VPN-Tunnel über ein Site-to-Site-VPN über das Internet übertragen wird, bietet Microsoft eine weitere Verbindungsoption an, die noch sicherer ist. Azure ExpressRoute ist ein dedizierter WAN-Link zwischen Azure und einem lokalen Standort oder einem Exchange-Hostinganbieter. Da ExpressRoute-Verbindungen nicht über das Internet verlaufen, bieten sie eine höhere Sicherheit, größere Zuverlässigkeit und schnellere Geschwindigkeit bei geringerer Latenz als herkömmliche Verbindungen über das Internet. Da es sich außerdem um eine direkte Verbindung vom Telekommunikationsanbieter des Kunden handelt, werden die Daten nicht über das Internet übertragen und dort nicht verfügbar gemacht.

Bewährte Methoden für das Implementieren eines sicheren hybriden Netzwerks, mit dem ein lokales Netzwerk auf Azure erweitert wird, finden Sie [hier](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>ETL-Prozess (Extrahieren, Transformieren, Laden)

[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) kann Daten in Azure SQL Data Warehouse laden, ohne ein separates Tool für Extrahieren, Transformieren, Laden oder für den Import zu verwenden. PolyBase ermöglicht den Zugriff auf Daten über T-SQL-Abfragen. Microsoft Business Intelligence und der Analysestapel sowie mit SQL Server kompatible Drittanbietertools können mit PolyBase verwendet werden.

### <a name="azure-active-directory-setup"></a>Azure Active Directory-Einrichtungen

[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) ist wichtig für die Verwaltung der Bereitstellung von Zugriff für Mitarbeiter, die mit der Umgebung interagieren. Ein bereits vorhandenes Windows Server Active Directory-Verzeichnis kann über [vier Klicks](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express) in Azure Active Directory integriert werden. Die Kunden können außerdem die bereitgestellte Active Directory-Infrastruktur (Domänencontroller) an ein bereits vorhandenes Azure Active Directory-Verzeichnis binden, indem sie die bereitgestellte Active Directory-Infrastruktur als untergeordnete Domäne für eine Azure Active Directory-Struktur festlegen.

### <a name="optional-services"></a>Optionale Dienste

Azure bietet eine Vielzahl von Diensten zur Unterstützung bei Speicherung und Staging formatierter und unformatierter Daten. Die folgenden Dienste können dieser Referenzarchitektur je nach den Kundenanforderungen hinzugefügt werden:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) ist ein spezieller verwalteter Clouddienst für komplexe Hybridprojekte mit ETL (Extrahieren, Transformieren, Laden) und Datenintegration. Azure Data Factory bietet Funktionen für die Verfolgung und Suche von Gesundheitsdaten, einschließlich Visualisierungs- und Überwachungstools, mit denen ermittelt werden kann, wann Daten empfangen wurden und woher sie stammen. Mit Azure Data Factory können Kunden datengesteuerte Workflows (sogenannte Pipelines) erstellen und planen, die Daten aus unterschiedlichen Datenspeichern erfassen. Diese Pipelines ermöglichen Kunden Daten das Erfassen von Daten aus internen und externen Quellen. Kunden können die Daten dann verarbeiten und für die Ausgabe in Datenspeicher wie z.B. Azure SQL Data Warehouse transformieren.
- Kunden können ein Staging für unstrukturierte Daten in [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) durchführen. Dies bietet ihnen die Möglichkeit, Daten von beliebiger Größe, Art und Erfassungsgeschwindigkeit zur Durchführung operativer und explorativer Analysen an einem einzigen Ort zu erfassen.  Azure Data Lake verfügt über Funktionen für die Extraktion und Konvertierung von Daten. Azure Data Lake Store ist mit den meisten Open-Source-Komponenten des Hadoop-Ökosystems kompatibel und kann sehr einfach in andere Azure-Dienste wie Azure SQL Data Warehouse integriert werden.

## <a name="disclaimer"></a>Haftungsausschluss

 - Dieses Dokument dient nur zu Informationszwecken. MICROSOFT ÜBERNIMMT KEINE AUSDRÜCKLICHEN, STILLSCHWEIGENDEN ODER GESETZLICH VORGEGEBENEN GARANTIEN HINSICHTLICH DER INFORMATIONEN IN DIESEM DOKUMENT. Dieses Dokument wird so bereitgestellt, wie es ist. Informationen und Stellungnahmen in diesem Dokument, einschließlich URLs und anderer Verweise auf Internetwebsites, können ohne vorherige Ankündigung geändert werden. Kunden, die dieses Dokument lesen, tragen das Risiko, es zu verwenden.
 - Dieses Dokument gibt Kunden keine Rechte an geistigem Eigentum an Microsoft-Produkten oder -Lösungen.
 - Kunden dürfen dieses Dokument zu internen Referenzzwecken kopieren und verwenden.
 - Bestimmte Empfehlungen in diesem Dokument können zu einer erhöhten Nutzung von Daten, Netzwerken oder Computeressourcen in Azure sowie zu höheren Azure-Lizenz- oder -Abonnementkosten für den Kunden führen.
 - Diese Architektur soll Kunden als Grundlage für die Anpassung an ihre spezifischen Anforderungen dienen. Sie sollte nicht unverändert in einer Produktionsumgebung verwendet werden.
 - Dieses Dokument wurde als Referenz entwickelt und sollte nicht dazu verwendet werden, alle Mittel zu definieren, mit denen ein Kunde bestimmte Konformitätsanforderungen und -vorschriften erfüllen kann. Kunden sollten bei genehmigten Kundenimplementierungen rechtliche Unterstützung von ihrer Organisation einholen.
