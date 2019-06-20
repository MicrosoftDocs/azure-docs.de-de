---
title: 'Vorlage für Azure Security and Compliance: Data Warehouse für die Automatisierung gemäß FedRAMP'
description: 'Vorlage für Azure Security and Compliance: Data Warehouse für die Automatisierung gemäß FedRAMP'
services: security
author: jomolesk
ms.assetid: 834d1ff6-8369-455f-b052-1ef301e3d7e6
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: 3c78aed2f30ea85f5bc16a8c0fb270bb1c761be8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60586031"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-fedramp-automation"></a>Azure-Blaupause für Sicherheit und Compliance: Data Warehouse für FedRAMP-Automatisierung

## <a name="overview"></a>Übersicht

Das [Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) ist ein Programm der US-Regierung, das einen standardisierten Ansatz zur Sicherheitsbewertung, Autorisierung und kontinuierlichen Überwachung von Cloudprodukten und -diensten bietet. Diese Vorlage für Azure Security and Compliance enthält Anweisungen zum Erstellen einer Data Warehouse-Architektur für Microsoft Azure, über die Sie FedRAMP High-Steuerelemente implementieren können. Diese Lösung umfasst Anweisungen für die Bereitstellung und Konfiguration von Azure-Ressourcen für eine allgemeine Referenzarchitektur. Sie zeigt Wege auf, wie Kunden spezifische Sicherheits- und Konformitätsanforderungen erfüllen können und dient als Grundlage für Kunden, damit diese ihre eigenen Data Warehouse-Lösungen in Azure entwickeln und konfigurieren können.

Diese Referenzarchitektur, die zugehörige Anleitung für die Implementierung von Steuerelementen und die Bedrohungsmodelle sollen Kunden als Grundlage für die Anpassung an ihre spezifischen Anforderungen dienen. Sie sollten nicht unverändert in einer Produktionsumgebung verwendet werden. Es reicht nicht aus, eine Anwendung ohne Änderungen in dieser Umgebung bereitzustellen, um die Anforderungen der FedRAMP High-Baseline vollständig zu erfüllen. Beachten Sie Folgendes:
- Diese Architektur bietet eine Baseline, um Kunden bei der Bereitstellung von Workloads in Azure in FedRAMP-konformer Weise zu unterstützen.
- Der Kunde ist für die Durchführung einer angemessenen Sicherheits- und Konformitätsbewertung jeder Lösung verantwortlich, die mit dieser Architektur erstellt wurde, da die Anforderungen je nach den Besonderheiten der jeweiligen Implementierung des Kunden unterschiedlich sein können.

## <a name="architecture-diagram-and-components"></a>Architekturdiagramm und Komponenten

Diese Lösung bietet eine Data Warehouse-Referenzarchitektur, die ein leistungsstarkes und sicheres Cloud-Data Warehouse implementiert. Es gibt zwei separate Datenschichten in dieser Architektur: eine, in die die Daten importiert und gespeichert werden und die in einer SQL-Clusterumgebung gestaffelt ist, und eine Schicht für Azure SQL Data Warehouse, in der die Daten für die Verarbeitung mit einem ETL-Tool geladen werden (z.B. [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)-T-SQL-Abfragen). Für die in Azure SQL Data Warehouse gespeicherten Daten können umfangreiche Analysen durchgeführt werden.

Microsoft Azure bietet eine Vielzahl von Berichts- und Analysediensten für Kunden. Diese Lösung umfasst SQL Server Reporting Services (SSRS) für die schnelle Erstellung von Berichten aus Azure SQL Data Warehouse. Jeglicher SQL-Datenverkehr wird mit SSL über selbstsignierte Zertifikate verschlüsselt. Azure empfiehlt als bewährte Methode die Verwendung einer vertrauenswürdigen Zertifizierungsstelle für erweiterte Sicherheit.

Daten in Azure SQL Data Warehouse werden in relationalen Tabellen spaltenweise gespeichert. Dieses Format reduziert die Kosten für die Datenspeicherung erheblich und verbessert die Abfrageleistung.  Je nach Nutzungsanforderungen kann Azure SQL Data Warehouse Ressourcen zentral hoch- oder herunterskalieren oder vollständig herunterfahren, wenn keine aktiven Prozesse Computerressourcen erfordern.

Ein SQL-Lastenausgleich verwaltet den SQL-Datenverkehr und stellt eine hohe Leistung sicher. Alle virtuellen Computer in dieser Referenzarchitektur werden in einer Verfügbarkeitsgruppe mit SQL Server-Instanzen bereitgestellt, die in einer Always On-Verfügbarkeitsgruppe für Hochverfügbarkeit und die Notfallwiederherstellung konfiguriert wurden.

Diese Data Warehouse-Referenzarchitektur enthält auch eine Active Directory-Ebene (AD) für die Verwaltung von Ressourcen innerhalb der Architektur. Das Active Directory-Subnetz ermöglicht die einfache Übernahme in einer größeren AD-Gesamtstruktur und erlaubt damit den fortlaufenden Betrieb der Umgebung, selbst wenn der Zugriff auf die größere Gesamtstruktur nicht möglich ist. Alle virtuellen Computer sind in Domänen der Active Directory-Ebene eingebunden und verwenden Active Directory-Gruppenrichtlinien, um Sicherheits- und Konformitätskonfigurationen auf Betriebssystemebene durchzusetzen.

Ein virtueller Computer fungiert als geschützter Host (Bastionhost) für die Verwaltung und bietet Administratoren eine sichere Verbindung für den Zugriff auf bereitgestellte Ressourcen. Die Daten werden über diesen verwaltenden Bastionhost in den Staging-Bereich geladen. **Azure empfiehlt das Konfigurieren einer VPN- oder Azure ExpressRoute-Verbindung für die Verwaltung und den Datenimport in das Subnetz der Referenzarchitektur.**

![Abbildung der Referenzarchitektur von Data Warehouse für FedRAMP](images/fedramp-datawarehouse-architecture.png?raw=true "Abbildung der Referenzarchitektur von Data Warehouse für FedRAMP")

Diese Lösung verwendet die folgenden Azure-Dienste. Details zur Bereitstellungsarchitektur finden Sie im Abschnitt [Bereitstellungsarchitektur](#deployment-architecture).

Azure Virtual Machines
-   (1) Bastionhost
-   (2) Active Directory-Domänencontroller
-   (2) SQL Server-Clusterknoten
-   (1) SQL Server-Zeuge

Verfügbarkeitsgruppen
-   (1) Active Directory-Domänencontroller
-   (1) SQL-Clusterknoten und -Zeuge

Virtual Network
-   (4) Subnetze
-   (4) Netzwerksicherheitsgruppen

SQL Data Warehouse

SQL Server Reporting Services

Azure SQL-Lastenausgleich

Azure Active Directory

Recovery Services-Tresor

Azure Key Vault

Azure Monitor-Protokolle

## <a name="deployment-architecture"></a>Bereitstellungsarchitektur

Im folgenden Abschnitt werden die Elemente für Entwicklung und Implementierung beschrieben.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) ist ein Enterprise Data Warehouse (EDW) mit Massively Parallel Processing (MPP) zur schnellen Ausführung komplexer Abfragen für mehrere Petabytes an Daten. Importieren Sie große Datenmengen mit einfachen PolyBase-T-SQL-Abfragen in SQL Data Warehouse, und nutzen Sie die Vorteile von MPP für aufwendige Analysen.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) erlaubt die schnelle Erstellung von Berichten mit Tabellen, Diagrammen, Karten, Maßstäben, Matrizen u.v.m. für Azure SQL Data Warehouse.

**Bastionhost**: Der Bastionhost ist der einzelne Zugangspunkt, über den Benutzer Zugriff auf die bereitgestellten Ressourcen in dieser Umgebung erhalten. Der Bastionhost bietet eine sichere Verbindung mit den bereitgestellten Ressourcen, indem nur Remotedatenverkehr von öffentlichen IP-Adressen auf einer Sicherheitsliste zugelassen wird. Damit RDP-Datenverkehr (Remotedesktopprotokoll) zugelassen wird, muss die Quelle des Datenverkehrs in der NSG (Netzwerksicherheitsgruppe) definiert sein.

Ein virtueller Computer wurde als ein in eine Domäne eingebundener Bastionhost mit den folgenden Konfigurationen erstellt:
-   [Antimalware-Erweiterung](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Azure Monitor-Protokolle-Erweiterung](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure-Diagnoseerweiterung](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) mit Azure Key Vault (berücksichtigt Azure Government, PCI-DSS, HIPAA und weitere Anforderungen)
-   Eine [Richtlinie zum automatischen Herunterfahren](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/), um die Nutzung der Ressourcen virtueller Computer zu verringern, wenn sie nicht benötigt werden
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) ist aktiviert, damit Anmeldeinformationen und andere Geheimnisse in einer geschützten Umgebung, die vom ausgeführten Betriebssystem isoliert ist, ausgeführt werden.

### <a name="virtual-network"></a>Virtuelles Netzwerk
Diese Referenzarchitektur definiert ein privates virtuelles Netzwerk mit dem Adressraum 10.0.0.0/16.

**Netzwerksicherheitsgruppen**: [NSGs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) enthalten Zugriffssteuerungslisten, die Datenverkehr in einem VNet zulassen oder ablehnen. NSGs können dafür verwendet werden, den Datenverkehr auf der Ebene eines Subnetzes oder einzelner VMs zu schützen. Die folgenden NSGs sind vorhanden:
  - eine NSG für die Datenschicht (SQL Server-Cluster, SQL Server-Zeuge und SQL-Lastenausgleich)
  - eine NSG für den verwaltenden Bastionhost
  - eine NSG für Active Directory
  - eine NSG für SQL Server Reporting Services

Jede der NSGs verfügt über bestimmte offene Ports und Protokolle, damit die Lösung sicher und richtig ausgeführt werden kann. Darüber hinaus werden die folgenden Konfigurationen für jede NSG aktiviert:
  - [Diagnoseprotokolle und -ereignisse](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) sind aktiviert und werden im Speicherkonto gespeichert.
  - Azure Monitor-Protokolle ist mit der [Diagnose der NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json) verbunden.

**Subnetze**: Jedem Subnetz ist die entsprechende NSG zugeordnet.

### <a name="data-at-rest"></a>Ruhende Daten
Die Architektur schützt ruhende Daten durch die Verwendung von Verschlüsselung, Datenbanküberwachung und anderen Maßnahmen.

**Azure Storage:** Um die Anforderungen für verschlüsselte ruhende Daten zu erfüllen, wird für den gesamten [Azure Storage](https://azure.microsoft.com/services/storage/) die [Speicherdienstverschlüsselung](https://docs.microsoft.com/azure/storage/storage-service-encryption) verwendet.

**Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) nutzt das BitLocker-Feature von Windows, um Volumeverschlüsselung für Betriebssysteme und Datenträger bereitzustellen. Die Lösung ist in Azure Key Vault integriert, um die Steuerung und Verwaltung der Datenträgerverschlüsselungsschlüssel zu erleichtern.

**Azure SQL-Datenbank:** In der Azure SQL-Datenbankinstanz werden die folgenden Datenbanksicherheitsmaßnahmen verwendet:
-   Die [AD-Authentifizierung und -Autorisierung](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) ermöglicht die zentrale Verwaltung der Identitäten von Datenbankbenutzern und anderen Microsoft-Diensten.
-   Die [SQL-Datenbank-Überprüfung](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) verfolgt Datenbankereignisse nach und schreibt diese in ein Überwachungsprotokoll in einem Azure Storage-Konto.
-   Die SQL-Datenbank ist so konfiguriert, dass sie [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) verwendet, die Echtzeitverschlüsselung und -entschlüsselung von Daten und Protokolldateien ausführt, um ruhende Informationen zu schützen. TDE gewährleistet, dass gespeicherte Daten keinen unbefugten Zugriffen ausgesetzt sind.
-   [Firewallregeln](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) verhindern sämtlichen Zugriff auf Datenbankserver, bis die richtigen Berechtigungen erteilt werden. Die Firewall gewährt den Datenbankzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung.
-   Die [SQL-Bedrohungserkennung](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) ermöglicht die Erkennung und Reaktion auf potenzielle Bedrohungen, sobald sie auftreten. Dazu werden Sicherheitswarnungen bei verdächtigen Datenbankaktivitäten, potenziellen Sicherheitslücken, Angriffen durch Einschleusung von SQL-Befehlen und anormalen Mustern beim Datenbankzugriff ausgegeben.
-   [Always Encrypted-Spalten](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) stellen sicher, dass vertrauliche Daten im Datenbanksystem niemals im Klartextformat angezeigt werden. Nach dem Aktivieren der Datenverschlüsselung können nur Clientanwendungen oder App-Server, die Zugriff auf die Schlüssel haben, auf Klartextdaten zugreifen.
-   Die [dynamische Datenmaskierung in SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) kann durchgeführt werden, nachdem die Referenzarchitektur bereitgestellt wurde. Kunden müssen die Einstellungen für die dynamische Datenmaskierung an ihr Datenbankschema anpassen.

### <a name="business-continuity"></a>Geschäftskontinuität
**Hochverfügbarkeit**: Serverworkloads werden in einer [Verfügbarkeitsgruppe](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) zusammengefasst, um Hochverfügbarkeit für virtuelle Computer in Azure besser sicherzustellen. Während einer geplanten oder ungeplanten Wartung ist mindestens ein virtueller Computer verfügbar, um die Azure-SLA von 99,95 % zu erfüllen.

**Recovery Services-Tresor**: Der [Recovery Services-Tresor](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) speichert Sicherungsdaten und schützt alle Konfigurationen von Azure Virtual Machines in dieser Architektur. Durch einen Recovery Services-Tresor können Kunden Dateien und Ordner von einer IaaS-VM wiederherstellen, ohne die gesamte VM wiederherstellen zu müssen. So werden die Wiederherstellungszeiten verkürzt.

### <a name="logging-and-audit"></a>Protokollierung und Überwachung
[Azure Monitor-Protokolle](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) ermöglicht eine umfassende Protokollierung von System- und Benutzeraktivitäten sowie der Systemintegrität. [Azure Monitor-Protokolle](https://azure.microsoft.com/services/log-analytics/) ist eine Lösung für die Erfassung und Analyse von Daten, die von Ressourcen in Azure-basierten und lokalen Umgebungen generiert werden.
- **Aktivitätsprotokolle:** [Aktivitätsprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) bieten Einblicke in Vorgänge, die für Ressourcen in einem Abonnement ausgeführt wurden.
- **Diagnoseprotokolle:** [Diagnoseprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) umfassen sämtliche Protokolle, die von jeder Ressource ausgegeben werden. Diese Protokolle umfassen Windows-Ereignissystemprotokolle, Azure Blob Storage-, Tabellen- und Warteschlangenprotokolle.
- **Firewallprotokolle:** Application Gateway stellt umfassende Diagnose- und Zugriffsprotokolle bereit. Firewallprotokolle stehen für Application Gateway-Ressourcen zur Verfügung, für die WAF aktiviert ist.
- **Protokollarchivierung:** Alle Diagnoseprotokolle schreiben in ein zentrales und verschlüsseltes Azure-Speicherkonto für die Archivierung mit einer definierten Beibehaltungsdauer von 2 Tagen. Diese Protokolle werden dann zur Verarbeitung, Speicherung und Dashboardanzeige mit Azure Monitor-Protokolle verbunden.

Darüber hinaus sind in dieser Architektur die folgenden Überwachungslösungen enthalten:
-   [Active Directory-Bewertung](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Die Lösung „Active Directory-Integritätsüberprüfung“ bewertet die Risiken und die Integrität von Serverumgebungen in regelmäßigen Abständen und erstellt eine priorisierte Liste mit spezifischen Empfehlungen für die bereitgestellte Serverinfrastruktur.
-   [Antischadsoftwarebewertung](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): Die Antischadsoftwarelösung meldet Schadsoftware, Bedrohungen und den Schutzstatus.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Die Azure Automation-Lösung dient der Speicherung, Ausführung und Verwaltung von Runbooks.
-   [Sicherheit und Überwachung](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): Das Sicherheits- und Überwachungsdashboard ermöglicht einen allgemeinen Einblick in den Sicherheitszustand von Ressourcen. Hierzu werden Metriken zu Sicherheitsdomänen, relevanten Problemen, Erkennungen, Bedrohungsdaten und allgemeinen Sicherheitsabfragen bereitgestellt.
-   [SQL-Bewertung](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Die Lösung „SQL-Integritätsüberprüfung“ bewertet die Risiken und die Integrität von Serverumgebungen in regelmäßigen Abständen und erstellt für die Kunden eine priorisierte Liste mit speziellen Empfehlungen für die bereitgestellte Serverinfrastruktur.
-   [Updateverwaltung](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): Die Updateverwaltungslösung ermöglicht die Verwaltung von Sicherheitsupdates des Betriebssystems durch die Kunden. Dies umfasst auch den Status der verfügbaren Updates und den Fortschritt beim Installieren der erforderlichen Updates.
-   [Agent-Integritätsdiagnose](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Die Lösung „Agent-Integritätsdiagnose“ meldet die Anzahl bereitgestellter Agents sowie deren geografische Verteilung. Außerdem meldet sie, wie viele Agents nicht mehr reagieren und wie viele Agents Betriebsdaten übermitteln.
-   [Azure-Aktivitätsprotokolle](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Die Lösung „Aktivitätsprotokollanalyse“ hilft bei der Analyse der Azure-Aktivitätsprotokolle in allen Azure-Abonnements eines Kunden.
-   [Änderungsnachverfolgung](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Die Lösung „Änderungsnachverfolgung“ ermöglicht es Kunden, problemlos Änderungen in der Umgebung zu erkennen.

### <a name="identity-management"></a>Identitätsverwaltung
Die folgenden Technologien bieten Identitätsverwaltungsfunktionen in der Azure-Umgebung:
-   [Active Directory (AD)](https://azure.microsoft.com/services/active-directory/) ist der mehrinstanzenfähige cloudbasierte Verzeichnis- und Identitätsverwaltungsdienst von Microsoft. Alle Benutzer für die Lösung wurden in Azure Active Directory erstellt, so auch die Benutzer, die auf die SQL-Datenbank zugreifen.
-   Authentifizierung für die Anwendung erfolgt mithilfe von Azure AD. Weitere Informationen finden Sie unter [Integrieren von Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Darüber hinaus wird für die Verschlüsselung der Datenbankspalten Azure AD verwendet, um die Anwendung für Azure SQL-Datenbank zu authentifizieren. Weitere Informationen finden Sie unter [Schützen von vertraulichen Daten in SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) erkennt potenzielle Sicherheitsrisiken, die sich auf die Identitäten einer Organisation auswirken, konfiguriert automatisierte Antworten auf erkannte verdächtige Aktionen, die im Zusammenhang mit Identitäten der Organisation stehen, untersucht verdächtige Vorfälle und führt die entsprechenden Aktionen zu deren Behebung aus.
-   Die [rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) in Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) ermöglicht eine Zugriffsverwaltung für Azure. Der Abonnementzugriff ist auf den Abonnementadministrator beschränkt.

Weitere Informationen zum Verwenden der Sicherheitsfunktionen von Azure SQL-Datenbank finden Sie im Beispiel [Contoso Clinic Demo Application](https://github.com/Microsoft/azure-sql-security-sample).

### <a name="security"></a>Sicherheit
**Geheimnisverwaltung**: Die Lösung verwendet [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) für die Verwaltung von Schlüsseln und Geheimnissen. Azure Key Vault unterstützt Sie dabei, kryptografische Schlüssel und Geheimnisse zu schützen, die von Cloudanwendungen und -diensten verwendet werden.

**Schutz vor Schadsoftware**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) für Virtual Machines bietet Echtzeitschutzfunktionen, mit denen die Identifizierung und Entferung von Viren, Spyware und anderer bösartiger Software ermöglicht wird, mit konfigurierbaren Warnmeldungen, wenn bekannte bösartige oder unerwünschte Software versucht, sich auf geschützten virtuellen Computern zu installieren oder die Ausführung zu starten.

**Patchverwaltung**: Virtuelle Windows-Computer, die mit dieser Referenzarchitektur bereitgestellt werden, sind standardmäßig so konfiguriert, dass sie automatische Updates vom Windows Update-Dienst erhalten. Diese Lösung umfasst auch den Dienst [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro), über den aktualisierte Bereitstellungen erstellt werden können, um virtuelle Computer bei Bedarf zu patchen.


## <a name="guidance-and-recommendations"></a>Anleitungen und Empfehlungen
### <a name="expressroute-and-vpn"></a>ExpressRoute und VPN
Für sichere Verbindungen mit Ressourcen, die als Teil dieser Data Warehouse-Referenzarchitektur bereitgestellt werden, muss ein sicherer VPN-Tunnel oder [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) konfiguriert werden. Da ExpressRoute-Verbindungen nicht über das Internet verlaufen, bieten sie eine höhere Sicherheit, größere Zuverlässigkeit und schnellere Geschwindigkeit bei geringerer Latenz als herkömmliche Verbindungen über das Internet. Durch die ordnungsgemäße Einrichtung von VPN oder ExpressRoute können Kunden eine zusätzliche Sicherheitsebene für Daten während der Übertragung hinzufügen.

### <a name="extract-transform-load-etl-process"></a>ETL-Prozess (Extrahieren, Transformieren, Laden)
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) kann Daten in Azure SQL Data Warehouse laden, ohne ein separates ETL- oder Importtool zu verwenden. PolyBase ermöglicht den Zugriff auf Daten über T-SQL-Abfragen. Microsoft Business Intelligence und der Analysestapel sowie mit SQL Server kompatible Drittanbietertools können mit PolyBase verwendet werden.

### <a name="azure-active-directory-setup"></a>Azure Active Directory-Einrichtungen
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) ist wichtig für die Verwaltung der Bereitstellung von Zugriff für Mitarbeiter, die mit der Umgebung interagieren. Ein bereits vorhandenes Windows Server Active Directory-Verzeichnis kann über [vier Klicks](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express) in Azure Active Directory integriert werden. Die Kunden können außerdem die bereitgestellte Active Directory-Infrastruktur (Domänencontroller) an ein bereits vorhandenes Azure Active Directory-Verzeichnis binden, indem sie die bereitgestellte Active Directory-Infrastruktur als untergeordnete Domäne für eine AAD-Struktur festlegen.

### <a name="additional-services"></a>Zusätzliche Dienste
Obwohl diese Data Warehouse-Architektur nicht für die Bereitstellung in der [Azure Commercial](https://azure.microsoft.com/overview/what-is-azure/)-Umgebung vorgesehen ist, können ähnliche Ergebnisse über die in dieser Referenzarchitektur beschriebenen Dienste sowie über zusätzliche Dienste erzielt werden, die nur in der Azure Commercial-Umgebung verfügbar sind. Beachten Sie, dass Azure Commercial FedRAMP JAB P-ATO für die Moderate Impact-Ebene verwaltet. Dadurch ist es Behörden und Partnern möglich, vertrauliche Informationen in der Cloud bereitzustellen, die die Azure Commercial-Umgebung nutzt.

Azure Commercial bietet eine Vielzahl von Diensten, die formatierten und nicht formatierten Datenspeicher verarbeiten und Staging für Data Warehousing ausführen können, darunter:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) ist ein spezieller verwalteter Clouddienst für komplexe Hybridprojekte mit ETL (Extrahieren, Transformieren und Laden), ELT (Extrahieren, Laden und Transformieren) und Datenintegration. Mit Azure Data Factory können Kunden datengesteuerte Workflows (sogenannte Pipelines) erstellen und planen, die Daten aus unterschiedlichen Datenspeichern erfassen. Kunden können die Daten dann verarbeiten und für die Ausgabe in Datenspeicher wie z.B. Azure SQL Data Warehouse transformieren.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) bietet die Möglichkeit, Daten von beliebiger Größe, Art und Erfassungsgeschwindigkeit zur Durchführung operativer und explorativer Analysen an einem einzigen Ort zu erfassen. Azure Data Lake Store ist mit den meisten Open-Source-Komponenten des Hadoop-Ökosystems kompatibel und kann sehr einfach in andere Azure-Dienste wie Azure SQL Data Warehouse integriert werden.

## <a name="threat-model"></a>Bedrohungsmodell

Das Datenflussdiagramm (DFD) für diese Referenzarchitektur steht zum [Download](https://aka.ms/blueprintdwthreatmodel) bereit und ist im Folgenden angegeben:

![Bedrohungsmodell für Data Warehouse gemäß FedRAMP](images/fedramp-datawarehouse-threat-model.png?raw=true "Bedrohungsmodell für Data Warehouse gemäß FedRAMP")

## <a name="compliance-documentation"></a>Konformitätsdokumentation
Die [Vorlage für Azure Security and Compliance: FedRAMP High-Kundenzuständigkeitsmatrix](https://aka.ms/blueprinthighcrm) listet alle Sicherheitskontrollen auf, die von der FedRAMP High-Baseline verlangt werden. Die [Vorlage für Azure Security and Compliance: FedRAMP Moderate-Kundenzuständigkeitsmatrix](https://aka.ms/blueprintcrmmod) listet auf ähnliche Weise alle Sicherheitskontrollen auf, die von der FedRAMP Moderate-Baseline verlangt werden. In beiden Dokumenten wird erläutert, ob die Implementierung der einzelnen Steuerelemente Aufgabe von Microsoft, dem Kunden oder von beiden ist.

Die [Vorlage für Azure Security and Compliance: FedRAMP High-Regulierungsimplementierungsmatrix](https://aka.ms/blueprintdwcimhigh) und die [Vorlage für Azure Security and Compliance: FedRAMP Moderate-Regulierungsimplementierungsmatrix](https://aka.ms/blueprintdwcimmod) beinhalten Informationen dazu, welche Steuerelemente von der Data Warehouse-Architektur für die einzelnen FedRAMP-Baselines abgedeckt werden, einschließlich detaillierter Beschreibungen, inwiefern die Implementierung die Anforderungen der jeweiligen abgedeckten Steuerelemente erfüllt werden.

## <a name="disclaimer"></a>Haftungsausschluss

 - Dieses Dokument dient nur zu Informationszwecken. MICROSOFT ÜBERNIMMT KEINE AUSDRÜCKLICHEN, STILLSCHWEIGENDEN ODER GESETZLICH VORGEGEBENEN GARANTIEN HINSICHTLICH DER INFORMATIONEN IN DIESEM DOKUMENT. Dieses Dokument wird so bereitgestellt, wie es ist. Informationen und Stellungnahmen in diesem Dokument, einschließlich URLs und anderer Verweise auf Internetwebsites, können ohne vorherige Ankündigung geändert werden. Kunden, die dieses Dokument lesen, tragen das Risiko, es zu verwenden.
 - Dieses Dokument gibt Kunden keine Rechte an geistigem Eigentum an Microsoft-Produkten oder -Lösungen.
 - Kunden dürfen dieses Dokument zu internen Referenzzwecken kopieren und verwenden.
 - Bestimmte Empfehlungen in diesem Dokument können zu einer erhöhten Nutzung von Daten, Netzwerken oder Computeressourcen in Azure sowie zu höheren Azure-Lizenz- oder -Abonnementkosten für den Kunden führen.
 - Diese Architektur soll Kunden als Grundlage für die Anpassung an ihre spezifischen Anforderungen dienen. Sie sollte nicht unverändert in einer Produktionsumgebung verwendet werden.
 - Dieses Dokument wurde als Referenz entwickelt und sollte nicht dazu verwendet werden, alle Mittel zu definieren, mit denen ein Kunde bestimmte Konformitätsanforderungen und -vorschriften erfüllen kann. Kunden sollten bei genehmigten Kundenimplementierungen rechtliche Unterstützung von ihrer Organisation einholen.
