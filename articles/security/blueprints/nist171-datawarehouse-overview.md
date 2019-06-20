---
title: 'Vorlage für Azure Security and Compliance: Data Warehouse gemäß NIST SP 800-171'
description: 'Vorlage für Azure Security and Compliance: Data Warehouse gemäß NIST SP 800-171'
services: security
author: jomolesk
ms.assetid: dbc9cafe-115d-4965-b0d4-fcf235a064c8
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: a1850ecfbb21eb9495bb0e6de362dc8dee3026a2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60609585"
---
# <a name="azure-security-and-compliance-blueprint---data-warehouse-for-nist-sp-800-171"></a>Vorlage für Azure Security and Compliance: Data Warehouse gemäß NIST SP 800-171

## <a name="overview"></a>Übersicht
[NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) enthält Richtlinien zum Schützen der nicht klassifizierten kontrollierten Informationen (Controlled Unclassified Information, CUI), die sich in nicht behördlichen Informationssystemen und Organisationen befinden. In NIST SP 800-171 sind 14 „Familien“ mit Sicherheitsanforderungen zum Schützen der Vertraulichkeit von nicht klassifizierten kontrollierten Informationen festgelegt.

Diese Vorlage für Azure Security and Compliance enthält eine Anleitung, mit der Kunden eine Architektur für ein Data Warehouse in Azure bereitstellen können, die eine Teilmenge der NIST SP 800-171-Kontrollmechanismen implementiert. Diese Lösung veranschaulicht die Möglichkeiten von Kunden, bestimmte Sicherheits- und Konformitätsanforderungen zu erfüllen. Sie dient auch als Grundlage für Kunden für die Erstellung und Konfiguration ihrer eigenen Data Warehouse-Lösungen in Azure.

Diese Referenzarchitektur, die zugehörige Implementierungsanleitung und das Bedrohungsmodell sollen Kunden als Grundlage für die Anpassung an ihre spezifischen Anforderungen dienen. Sie sollten nicht unverändert in einer Produktionsumgebung verwendet werden. Kunden sind dafür verantwortlich, angemessene Sicherheits- und Konformitätsbewertungen für jede Lösung durchzuführen, die mit dieser Architektur erstellt wurde. Die Anforderungen variieren je nach den Gegebenheiten der Implementierung des jeweiligen Kunden.

## <a name="architecture-diagram-and-components"></a>Architekturdiagramm und Komponenten
Bei dieser Lösung wird eine Referenzarchitektur bereitgestellt, die ein leistungsstarkes und sicheres Cloud-Data Warehouse implementiert. In dieser Architektur gibt es zwei separate Datenschichten. In einer Schicht werden Daten importiert, gespeichert und in einer gruppierten SQL-Umgebung gestaffelt. Die andere Schicht ist für SQL Data Warehouse vorgesehen. In dieser Schicht werden die Daten mit einem ETL-Tool (ETL = Extrahieren, Transformieren und Laden) für die Verarbeitung geladen (z.B. [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)-T-SQL-Abfragen ). Für die in SQL Data Warehouse gespeicherten Daten können umfangreiche Analysen durchgeführt werden.

Azure bietet eine Vielzahl von Berichts- und Analysediensten für Kunden. Diese Lösung umfasst SQL Server Reporting Services für die schnelle Erstellung von Berichten aus SQL Data Warehouse. Jeglicher SQL-Datenverkehr wird mit SSL über selbstsignierte Zertifikate verschlüsselt. Als bewährte Methode wird die Verwendung einer vertrauenswürdigen Zertifizierungsstelle für erweiterte Sicherheit empfohlen.

Azure SQL Data Warehouse speichert Daten in relationalen Tabellen mit spaltenbasiertem Speicher. Dieses Format zeichnet sich durch erheblich geringere Datenspeicherkosten und eine bessere Abfrageleistung aus. Je nach Nutzungsanforderungen kann SQL Data Warehouse Ressourcen zentral hoch- oder herunterskalieren oder vollständig herunterfahren, wenn keine aktiven Prozesse Computerressourcen erfordern.

Ein SQL Server-Lastenausgleich verwaltet den SQL-Datenverkehr und stellt eine hohe Leistung sicher. Alle virtuellen Computer in dieser Referenzarchitektur werden in einer Verfügbarkeitsgruppe mit SQL Server-Instanzen bereitgestellt, die in einer Always On-Verfügbarkeitsgruppe konfiguriert wurden. Diese Konfiguration bietet Hochverfügbarkeits- und Notfallwiederherstellungsfunktionen.

Diese Data Warehouse-Referenzarchitektur enthält auch eine Active Directory-Ebene für die Verwaltung von Ressourcen innerhalb der Architektur. Das Active Directory-Subnetz ermöglicht eine einfache Übernahme unter einer größeren Active Directory-Gesamtstruktur. Auf diese Weise kann die Umgebung kontinuierlich ausgeführt werden, auch wenn der Zugriff auf die größere Gesamtstruktur nicht verfügbar ist. Alle virtuellen Computer sind mit einer Domäne auf der Ebene des Active Directory verknüpft. Sie verwenden Active Directory-Gruppenrichtlinien, um Sicherheits- und Konformitätskonfigurationen auf Betriebssystemebene durchzusetzen.

Die Lösung verwendet Azure Storage-Konten, die Kunden für die Verwendung von Speicherdienstverschlüsselung konfigurieren können, um die Vertraulichkeit ruhender Daten zu wahren. Azure speichert drei Kopien der Daten im ausgewählten Rechenzentrum eines Kunden, um Resilienz zu gewährleisten. Der georedundante Speicher sorgt dafür, dass Daten in einem sekundären Rechenzentrum Hunderte Kilometer entfernt repliziert und in Form von drei Kopien in diesem Rechenzentrum erneut gespeichert werden. Dadurch wird ein unerwünschtes Ereignis im primären Rechenzentrum des Kunden verhindert, das zu einem Verlust von Daten führt.

Für höhere Sicherheit werden alle Ressourcen in dieser Lösung als Ressourcengruppe über Azure Resource Manager verwaltet. Die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure Active Directory (Azure AD) dient zur Steuerung des Zugriffs auf bereitgestellte Ressourcen. Zu diesen Ressourcen zählen Kundenschlüssel in Azure Key Vault. Die Systemintegrität wird durch Azure Security Center und Azure Monitor überwacht. Kunden konfigurieren beide Überwachungsdienste für die Erfassung von Protokollen. Die Systemintegrität wird auf einem einzigen, benutzerfreundlichen Dashboard angezeigt.

Ein virtueller Computer dient als Bastionhost für die Verwaltung. Er bietet Administratoren eine sichere Verbindung für den Zugriff auf bereitgestellte Ressourcen. Die Daten werden über diesen verwaltenden Bastionhost in den Staging-Bereich geladen. *Es wird empfohlen, eine VPN- oder Azure ExpressRoute-Verbindung für die Verwaltung und den Datenimport in das Subnetz der Referenzarchitektur zu konfigurieren.*

![Data Warehouse gemäß NIST SP 800-171 – Diagramm zur Referenzarchitektur](images/nist171-dw-architecture.png "Data Warehouse gemäß NIST SP 800-171 – Diagramm zur Referenzarchitektur")

Diese Lösung verwendet die folgenden Azure-Dienste. Weitere Informationen finden Sie im Abschnitt [Bereitstellungsarchitektur](#deployment-architecture).

- Verfügbarkeitsgruppen
    - Active Directory-Domänencontroller
    - SQL Server-Clusterknoten und -Zeuge
- Azure Active Directory
- Azure Data Catalog
- Azure Key Vault
- Azure Monitor (Protokolle)
- Azure Security Center
- Azure Load Balancer
- Azure Storage
- Azure Virtual Machines
    - (1) Bastionhost
    - (2) Active Directory-Domänencontroller
    - (2) SQL Server-Clusterknoten
    - (1) SQL Server-Zeuge
- Virtuelles Azure-Netzwerk
    - (1) /16-Netzwerk
    - (4) /24-Netzwerke
    - (4) Netzwerksicherheitsgruppen
- Recovery Services-Tresor
- SQL Data Warehouse
- SQL Server Reporting Services

## <a name="deployment-architecture"></a>Bereitstellungsarchitektur
Im folgenden Abschnitt werden die Elemente für Entwicklung und Implementierung beschrieben.

**Azure SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) ist ein Enterprise Data Warehouse mit Massively Parallel Processing zur schnellen Ausführung komplexer Abfragen für mehrere Petabyte an Daten. Benutzer können große Datenmengen mit einfachen PolyBase-T-SQL-Abfragen in SQL Data Warehouse importieren und anschließend die Vorteile von Massively Parallel Processing für aufwendige Analysen nutzen.

**SQL Server Reporting Services**: [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) erlaubt die schnelle Erstellung von Berichten mit Tabellen, Diagrammen, Karten, Maßstäben, Matrizen u.v.m. für SQL Data Warehouse.

**Azure Data Catalog**: [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) sorgt dafür, dass Datenquellen von den Benutzern, die die Daten verwalten, leicht ermittelt und verstanden werden können. Häufig verwendete Datenquellen können registriert und markiert und nach personenbezogenen Daten durchsucht werden. Die Daten verbleiben an ihrem vorhandenen Speicherort, aber eine Kopie der Metadaten wird zu Azure Data Catalog hinzugefügt. Darin inbegriffen ist ein Verweis auf den Speicherort der Datenquelle. Die Metadaten werden indiziert, damit die einzelnen Datenquellen über die Suchfunktion mühelos ermittelt werden können. Durch die Indizierung wird dies auch für die ermittelnden Benutzer nachvollziehbar.

**Bastionhost**: Der Bastionhost ist der einzelne Zugangspunkt, über den Benutzer Zugriff auf die bereitgestellten Ressourcen in dieser Umgebung erhalten. Der Bastionhost bietet eine sichere Verbindung mit den bereitgestellten Ressourcen, indem nur Remotedatenverkehr von öffentlichen IP-Adressen auf einer Sicherheitsliste zugelassen wird. Damit Remotedesktopprotokoll-Datenverkehr zugelassen wird, muss die Quelle des Datenverkehrs in der Netzwerksicherheitsgruppe definiert sein.

Diese Lösung erstellt eine VM als Bastionhost in der Domäne mit den folgenden Konfigurationen:
-   [Antischadsoftware-Erweiterung](https://docs.microsoft.com/azure/security/azure-security-antimalware).
-   [Azure-Diagnoseerweiterung](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) mit Azure Key Vault.
-   Eine [Richtlinie zum automatischen Herunterfahren](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/), um die Nutzung der VM-Ressourcen zu verringern, wenn diese nicht benötigt werden.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) ist aktiviert, damit Anmeldeinformationen und andere geheimen Schlüssel in einer geschützten Umgebung, die vom ausgeführten Betriebssystem isoliert ist, ausgeführt werden.

### <a name="virtual-network"></a>Virtuelles Netzwerk
Diese Referenzarchitektur definiert ein privates virtuelles Netzwerk mit dem Adressraum 10.0.0.0/16.

**Netzwerksicherheitsgruppen**: [Netzwerksicherheitsgruppen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSGs) enthalten Zugriffssteuerungslisten, die den Datenverkehr in einem virtuellen Netzwerk zulassen oder ablehnen. NSGs können dafür verwendet werden, den Datenverkehr auf der Ebene eines Subnetzes oder einzelner VMs zu schützen. Die folgenden NSGs sind vorhanden:
  - eine NSG für die Datenschicht (SQL Server-Cluster, SQL Server-Zeuge und SQL-Lastenausgleich)
  - eine NSG für den verwaltenden Bastionhost
  - eine NSG für Active Directory
  - eine NSG für SQL Server Reporting Services

Jede der NSGs verfügt über bestimmte offene Ports und Protokolle, damit die Lösung sicher und richtig ausgeführt werden kann. Darüber hinaus werden die folgenden Konfigurationen für jede NSG aktiviert:
  - [Diagnoseprotokolle und -ereignisse](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) werden aktiviert und in einem Speicherkonto gespeichert.
  - Azure Monitor-Protokolle ist mit der [Diagnose der NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json) verbunden.

**Subnetze**: Jedem Subnetz ist die entsprechende NSG zugeordnet.

### <a name="data-at-rest"></a>Ruhende Daten
Die Architektur schützt ruhende Daten über mehrere Measures. Diese Measures umfassen die Überprüfung von Verschlüsselungen und Datenbanken.

**Azure Storage**: Zur Erfüllung der Anforderungen an verschlüsselte ruhende Daten wird für den gesamten [Storage](https://azure.microsoft.com/services/storage/)-Bereich die [Speicherdienstverschlüsselung](https://docs.microsoft.com/azure/storage/storage-service-encryption) verwendet. Mit diesem Feature werden Daten geschützt, um die Zusagen und Anforderungen der Organisation im Hinblick auf Sicherheit und Konformität einzuhalten.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) verwendet die BitLocker-Funktion von Windows, um Volumeverschlüsselung für Betriebssystemdatenträger und reguläre Datenträger bereitzustellen. Die Lösung ist in Azure Key Vault integriert, um die Steuerung und Verwaltung der Datenträgerverschlüsselungsschlüssel zu erleichtern.

**Azure SQL-Datenbank**: In der SQL-Datenbank-Instanz werden die folgenden Datenbanksicherheitsmaßnahmen verwendet:
-   Die [Active Directory-Authentifizierung und -Autorisierung](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) ermöglicht die zentrale Verwaltung der Identitäten von Datenbankbenutzern und anderen Microsoft-Diensten.
-   Die [SQL-Datenbank-Überprüfung](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) verfolgt Datenbankereignisse nach und schreibt diese in ein Überwachungsprotokoll in einem Azure Storage-Konto.
-   Azure SQL-Datenbank wird für die Verwendung der [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) konfiguriert. Bei diesem Vorgang wird die Echtzeitverschlüsselung und -entschlüsselung der Datenbank, der zugehörigen Sicherungen und der Transaktionsprotokolldateien durchgeführt, um ruhende Informationen zu schützen. Die transparente Datenverschlüsselung gewährleistet, dass gespeicherte Daten keinen unbefugten Zugriffen ausgesetzt sind.
-   [Firewallregeln](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) verhindern sämtlichen Zugriff auf Datenbankserver, bis die richtigen Berechtigungen erteilt werden. Die Firewall gewährt den Datenbankzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung.
-   [SQL-Bedrohungserkennung](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) ermöglicht die Erkennung und Reaktion auf potenzielle Bedrohungen, sobald diese auftreten. Sie stellt Warnungen zu verdächtigen Datenbankaktivitäten, potenziellen Sicherheitsrisiken, Angriffen durch Einschleusung von SQL-Befehlen sowie zu anomalen Datenbank-Zugriffsmustern bereit.
-   [Verschlüsselte Spalten](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) stellen sicher, dass vertrauliche Daten im Datenbanksystem niemals im Klartextformat angezeigt werden. Nach dem Aktivieren der Datenverschlüsselung können nur Clientanwendungen oder App-Server auf Klartextdaten zugreifen, die Zugriff auf die Schlüssel haben.
- [Erweiterte Eigenschaften](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) können verwendet werden, um die Verarbeitung von Datensubjekten zu beenden. Benutzer können benutzerdefinierte Eigenschaften zu Datenbankobjekten hinzufügen. Sie können Daten auch mit „Nicht mehr unterstützt“ markieren, um die Anwendungslogik dabei zu unterstützen, die Verarbeitung der zugehörigen Finanzdaten zu verhindern.
- Die [Sicherheit auf Zeilenebene](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) ermöglicht Benutzern das Festlegen von Richtlinien zum Einschränken des Zugriffs auf Daten, um die Verarbeitung zu beenden.
- Die [dynamische Datenmaskierung in SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) schränkt die Offenlegung vertraulicher Daten ein, indem diese Daten für nicht berechtigte Benutzer maskiert werden. Sie kann automatisch potenziell vertrauliche Daten ermitteln und eine geeignete Maskierung vorschlagen. Die dynamische Datenmaskierung unterstützt Sie beim Reduzieren des Zugriffs, damit vertrauliche Daten die Datenbank nicht über nicht autorisierten Zugriff verlassen. *Kunden sind dafür verantwortlich, die Einstellungen an ihr Datenbankschema anzupassen.*

### <a name="identity-management"></a>Identitätsverwaltung
Die folgenden Technologien enthalten Funktionen zum Verwalten des Zugriffs auf Daten in der Azure-Umgebung:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) ist der mehrinstanzenfähige cloudbasierte Verzeichnis- und Identitätsverwaltungsdienst von Microsoft. Alle Benutzer dieser Lösung werden in Azure AD erstellt. Dies gilt auch für die Benutzer, die Zugriff auf die SQL-Datenbank haben.
-   Authentifizierung für die Anwendung erfolgt mithilfe von Azure AD. Weitere Informationen finden Sie unter [Integrieren von Anwendungen in Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Für die Verschlüsselung der Datenbankspalten wird Azure AD auch verwendet, um die Anwendung für Azure SQL-Datenbank zu authentifizieren. Weitere Informationen finden Sie unter [Schützen von vertraulichen Daten in SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) kann von Administratoren zum Definieren detaillierter Zugriffsberechtigungen verwendet werden. Damit können sie nur den Benutzern die Zugriffsrechte gewähren, die diese zum Ausführen ihrer Aufgaben benötigen. Anstatt jedem Benutzer uneingeschränkten Zugriff für Azure-Ressourcen zu gewähren, können Administratoren auch nur bestimmte Aktionen für den Zugriff auf Ressourcen und Daten erlauben. Der Abonnementzugriff ist auf den Abonnementadministrator beschränkt.
- Mit [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) können Kunden die Anzahl von Benutzern mit Zugriff auf bestimmte Informationen, z.B. Daten, einschränken. Administratoren können mit Azure AD Privileged Identity Management privilegierte Identitäten und den damit verbundenen Zugriff auf Ressourcen erkennen, einschränken und überwachen. Diese Funktionalität kann auch verwendet werden, um einen bedarfsgesteuerten Just-in-Time-Administratorzugriff zu erzwingen.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) erkennt potenzielle Sicherheitsrisiken, die Identitäten einer Organisation betreffen. Der Dienst konfiguriert automatische Reaktionen auf erkannte verdächtige Aktionen im Zusammenhang mit den Identitäten einer Organisation. Zudem untersucht er verdächtige Vorfälle, um entsprechende Maßnahmen zu deren Behebung zu ergreifen.

### <a name="security"></a>Sicherheit
**Geheimnisverwaltung**: Die Lösung verwendet [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) für die Verwaltung von Schlüsseln und Geheimnissen. Azure Key Vault unterstützt Sie dabei, kryptografische Schlüssel und geheime Schlüssel zu schützen, die von Cloudanwendungen und -diensten verwendet werden. Die folgenden Funktionen von Azure Key Vault unterstützen Kunden beim Schützen von Daten:
- Erweiterte Zugriffsrichtlinien werden auf Bedarfsbasis konfiguriert.
- Key Vault-Zugriffsrichtlinien werden mit minimalen erforderlichen Berechtigungen für Schlüssel und Geheimnisse definiert.
- Alle Schlüssel und Geheimnisse in Key Vault besitzen ein Ablaufdatum.
- Alle Schlüssel in Key Vault werden durch spezielle Hardwaresicherheitsmodule geschützt. Der Schlüsseltyp ist ein Hardwaresicherheitsmodul, das mit einem 2048-Bit-RSA-Schlüssel geschützt wird.
- Allen Benutzern und Identitäten werden mit RBAC die mindestens erforderlichen Berechtigungen erteilt.
- Diagnoseprotokolle für Key Vault werden mit einer Aufbewahrungsdauer von mindestens 365 Tagen aktiviert.
- Zulässige kryptographische Vorgänge für Schlüssel sind auf die erforderlichen Vorgänge beschränkt.

**Patchverwaltung**: Windows-VMs, die mit dieser Referenzarchitektur bereitgestellt werden, sind standardmäßig so konfiguriert, dass sie automatische Updates vom Windows Update-Dienst erhalten. Diese Lösung umfasst auch den Dienst [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro), über den aktualisierte Bereitstellungen erstellt werden können, um VMs bei Bedarf zu patchen.

**Schutz vor Schadsoftware**: [Microsoft-Antischadsoftware](https://docs.microsoft.com/azure/security/azure-security-antimalware) für VMs bietet eine Echtzeit-Schutzfunktion zum Bestimmen und Entfernen von Viren, Spyware und anderer Schadsoftware. Kunden können Warnungen konfigurieren, die generiert werden, wenn versucht wird, bekannte Schadsoftware oder unerwünschte Software auf geschützten VMs zu installieren oder auszuführen.

**Azure Security Center**: Mit [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) können Kunden Sicherheitsrichtlinien über Workloads hinweg zentral anwenden und verwalten, die Angriffsfläche verringern sowie Angriffe erkennen und darauf reagieren. Azure Security Center greift zudem auf die vorhandenen Konfigurationen der Azure-Dienste zu, um Empfehlungen für die Konfiguration und den Dienst bereitzustellen, die zur Verbesserung des Sicherheitsstatus und zum Schutz von Daten beitragen sollen.

Azure Security Center verwendet verschiedene Erkennungsfunktionen, um Kunden vor potenziellen Angriffen auf deren Umgebung zu warnen. Diese Warnungen enthalten wichtige Informationen zum Auslöser der Warnung, zu den möglicherweise betroffenen Ressourcen und zur Quelle des Angriffs. In Security Center stehen verschiedene [vordefinierte Sicherheitswarnungen](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) zur Verfügung, die ausgelöst werden, wenn eine Bedrohung oder eine verdächtige Aktivität erkannt wird. Mithilfe [benutzerdefinierter Warnungsregeln](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) können Kunden neue Sicherheitswarnungen auf der Grundlage von Daten definieren, die bereits in ihrer Umgebung gesammelt werden.

Security Center bietet priorisierte Sicherheitswarnungen und -vorfälle. Security Center erleichtert Kunden die Erkennung und Behebung potenzieller Sicherheitsprobleme. Für jede erkannte Bedrohung wird ein [Bericht über Bedrohungen](https://docs.microsoft.com/azure/security-center/security-center-threat-report) generiert. Teams zur Reaktion auf Vorfälle können die Berichte verwenden, wenn sie Bedrohungen untersuchen und beheben.

Diese Referenzarchitektur verwendet auch die Funktion [Sicherheitsrisikobewertung](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) in Security Center. Nach der Konfiguration meldet ein Partner-Agent (z.B. Qualys) Daten zu Sicherheitsrisiken an die Verwaltungsplattform des Partners. Im Gegenzug liefert die Verwaltungsplattform des Partners Überwachungsdaten zu Sicherheitsrisiken und zur Integrität an Security Center. Kunden können anhand dieser Informationen schnell anfällige VMs identifizieren.

### <a name="business-continuity"></a>Geschäftskontinuität
**Hochverfügbarkeit**: Serverworkloads werden in einer [Verfügbarkeitsgruppe](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) zusammengefasst, um Hochverfügbarkeit für VMs in Azure sicherzustellen. Während einer geplanten oder ungeplanten Wartung ist mindestens eine VM verfügbar, wodurch 99,95 % des SLA von Azure erfüllt werden.

**Recovery Services-Tresor**: Der [Recovery Services-Tresor](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) speichert Sicherungsdaten und schützt alle Konfigurationen von VMs in dieser Architektur. Durch einen Recovery Services-Tresor können Kunden Dateien und Ordner von einer IaaS-VM wiederherstellen, ohne die gesamte VM wiederherstellen zu müssen. So werden die Wiederherstellungszeiten verkürzt.

### <a name="logging-and-auditing"></a>Protokollierung und Überwachung

Azure-Dienste protokollieren umfassend die System- und Benutzeraktivitäten sowie die Systemintegrität:
- **Aktivitätsprotokolle**: [Aktivitätsprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) bieten Einblicke in Vorgänge, die für Ressourcen in einem Abonnement ausgeführt wurden. Aktivitätsprotokolle können dabei helfen, den Initiator eines Vorgangs, die Zeit des Auftretens und den Status zu bestimmen.
- **Diagnoseprotokolle**: [Diagnoseprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) umfassen sämtliche Protokolle, die von jeder Ressource ausgegeben werden. Zu diesen Protokollen gehören Windows-Ereignissystemprotokolle, Azure Storage-Protokolle, Key Vault-Überwachungsprotokolle sowie Azure Application Gateway-Zugriffs- und -Firewallprotokolle. Alle Diagnoseprotokolle werden für die Archivierung in ein zentrales und verschlüsseltes Azure Storage-Konto geschrieben. Benutzer können den Aufbewahrungszeitraum, bis zu 730 Tage, so konfigurieren, dass die spezifischen Anforderungen erfüllt werden.

**Azure Monitor-Protokolle:** Diese Protokolle werden dann zur Verarbeitung, Speicherung und Dashboardanzeige in [Azure Monitor-Protokolle](https://azure.microsoft.com/services/log-analytics/) konsolidiert. Nach der Erfassung von Daten werden diese in separaten Tabellen für die einzelnen Datentypen in den Log Analytics-Arbeitsbereichen organisiert. Auf diese Weise können alle Daten zusammen analysiert werden, unabhängig von der zugehörigen ursprünglichen Quelle. Security Center lässt sich in Azure Monitor-Protokolle integrieren. Kunden können mithilfe von Kusto-Abfragen auf ihre Sicherheitsereignisdaten zugreifen und diese mit Daten von anderen Diensten kombinieren.

In dieser Architektur sind die folgenden Azure-[Überwachungslösungen](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) enthalten:
-   [Active Directory-Bewertung](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Mit der Lösung für die Active Directory-Integritätsüberprüfung können Sie in regelmäßigen Abständen die Risiken und die Integrität Ihrer Serverumgebungen bewerten. Sie stellt eine priorisierte Liste von Empfehlungen bereit, die spezifisch für Ihre bereitgestellte Serverinfrastruktur gelten.
- [SQL-Bewertung](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Mit der SQL-Lösung für die Integritätsüberprüfung können Sie in regelmäßigen Abständen die Risiken und die Integrität Ihrer Serverumgebungen bewerten. Die Lösung stellt für Kunden eine priorisierte Liste mit Empfehlungen bereit, die spezifisch für die bereitgestellte Serverinfrastruktur gelten.
- [Agent-Integritätsdiagnose](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Diese Lösung für die Agent-Integritätsdiagnose meldet die Anzahl von bereitgestellten Agents sowie deren geografische Verteilung. Zudem meldet sie, wie viele Agents nicht reagieren und wie viele Agents operative Daten übermitteln.
-   [Aktivitätsprotokollanalyse](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Die Lösung „Aktivitätsprotokollanalyse“ hilft bei der Analyse der Azure-Aktivitätsprotokolle in allen Azure-Abonnements eines Kunden.

**Azure Automation**: [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) dient zum Speichern, Ausführen und Verwalten von Runbooks. Bei dieser Lösung werden Protokolle über Runbooks für Azure SQL-Datenbank erfasst. Kunden können mithilfe der [Änderungsnachverfolgung](https://docs.microsoft.com/azure/automation/automation-change-tracking) von Azure Automation auf einfache Weise Änderungen in der Umgebung identifizieren.

**Azure Monitor**: [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) dient zum Nachverfolgen der Leistung, zum Gewährleisten der Sicherheit und zum Identifizieren von Trends. Organisationen können diese Plattform zum Überwachen, zum Erstellen von Warnungen und zum Archivieren von Daten verwenden. Zudem können sie die API-Aufrufe in ihren Azure-Ressourcen nachverfolgen.

## <a name="threat-model"></a>Bedrohungsmodell

Das Datenflussdiagramm für diese Referenzarchitektur steht zum [Download](https://aka.ms/nist171-dw-tm) bereit oder kann hier gefunden werden. Dieses Modell kann Kunden dabei helfen, bei der Vornahme von Änderungen die potenziellen Risikopunkte in der Systeminfrastruktur zu verstehen.

![Data Warehouse gemäß NIST SP 800-171 – Bedrohungsmodell](images/nist171-dw-threat-model.png "Data Warehouse gemäß NIST SP 800-171 – Bedrohungsmodell")

## <a name="compliance-documentation"></a>Konformitätsdokumentation
Die [Vorlage für Azure Security and Compliance: NIST SP 800-171-Kundenzuständigkeitsmatrix](https://aka.ms/nist171-crm) listet alle Sicherheitskontrollen auf, die von NIST SP 800-171 verlangt werden. In dieser Matrix ist angegeben, ob die Implementierung der einzelnen Kontrollen Aufgabe von Microsoft, des Kunden oder beider Seiten ist.

Die [Azure-Blaupause für Sicherheit und Compliance: Data Warehouse-Datenbank gemäß NIST SP 800-171 – Regulierungsimplementierungsmatrix](https://aka.ms/nist171-dw-cim) enthält Informationen dazu, auf welche NIST SP 800-171-Kontrollen durch die Architektur der Data Warehouse-Datenbank verwiesen wird. Sie enthält zudem eine ausführliche Beschreibung, wie die Implementierung die Anforderungen der einzelnen Kontrollen erfüllt.

## <a name="guidance-and-recommendations"></a>Anleitungen und Empfehlungen

### <a name="vpn-and-expressroute"></a>VPN und ExpressRoute
Für sichere Verbindungen mit Ressourcen, die als Teil dieser Data Warehouse-Referenzarchitektur bereitgestellt werden, muss ein sicherer VPN-Tunnel oder [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) konfiguriert werden. Durch die korrekte Einrichtung von VPN oder ExpressRoute können Kunden eine zusätzliche Sicherheitsebene für Daten während der Übertragung hinzufügen.

Mit der Implementierung eines sicheren VPN-Tunnels mit Azure kann eine virtuelle private Verbindung zwischen einem lokalen Netzwerk und einem virtuellen Azure-Netzwerk hergestellt werden. Diese Verbindung wird über das Internet hergestellt. Sie ermöglicht Kunden das sichere Übertragen von Informationen in einem „Tunnel“ innerhalb eines verschlüsselten Links zwischen dem Kundennetzwerk und Azure. Eine Site-to-Site-VPN-Verbindung ist eine sichere, ausgereifte Technologie, die seit Jahrzehnten von Unternehmen aller Größen eingesetzt wird. Der [IPsec-Tunnelmodus](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) wird bei dieser Option als Verschlüsselungsmechanismus verwendet.

Da Datenverkehr im VPN-Tunnel über ein Site-to-Site-VPN über das Internet übertragen wird, bietet Microsoft eine weitere Verbindungsoption an, die noch sicherer ist. ExpressRoute ist ein dedizierter WAN-Link zwischen Azure und einem lokalen Standort oder einem Exchange-Hostinganbieter. ExpressRoute-Verbindungen stellen eine direkte Verbindung mit dem Telekommunikationsanbieter des Kunden her. Daher werden die Daten nicht über das Internet übertragen und im Internet verfügbar gemacht. Diese Verbindungen bieten mehr Zuverlässigkeit, eine höhere Geschwindigkeit, niedrigere Latenzzeiten und mehr Sicherheit als herkömmliche Verbindungen.

Bewährte Methoden für das Implementieren eines sicheren hybriden Netzwerks, mit dem ein lokales Netzwerk auf Azure erweitert wird, finden Sie [hier](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>ETL-Prozess (Extrahieren, Transformieren, Laden)
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) kann Daten in SQL Data Warehouse laden, ohne ein separates ETL- oder Importtool zu verwenden. PolyBase ermöglicht den Zugriff auf Daten über T-SQL-Abfragen. Microsoft Business Intelligence und der Analysestapel sowie mit SQL Server kompatible Drittanbietertools können mit PolyBase verwendet werden.

### <a name="azure-ad-setup"></a>Azure AD-Setup
[Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) ist wichtig für die Verwaltung der Bereitstellung von Zugriff für Mitarbeiter, die mit der Umgebung interagieren. Das lokale Active Directory kann über [vier Klicks](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express) in Azure AD integriert werden. Kunden können die bereitgestellte Active Directory-Infrastruktur (Domänencontroller) auch mit Azure AD verknüpfen. Machen Sie die bereitgestellte Active Directory-Infrastruktur zu diesem Zweck zu einer Unterdomäne einer Azure AD-Gesamtstruktur.

### <a name="optional-services"></a>Optionale Dienste
Azure bietet eine Vielzahl von Diensten zur Unterstützung bei Speicherung und Staging formatierter und unformatierter Daten. Die folgenden Dienste können je nach Kundenanforderungen zu dieser Referenzarchitektur hinzugefügt werden:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) ist ein spezieller verwalteter Clouddienst für komplexe Hybridprojekte mit ETL (Extrahieren, Transformieren, Laden) und Datenintegration. Data Factory bietet Funktionen zur Unterstützung der Ablaufverfolgung und Lokalisierung von Daten. Mit Visualisierungs- und Überwachungstools kann ermittelt werden, wenn Daten empfangen wurden und woher die Daten stammen. Kunden können datengesteuerte Workflows, sogenannte Pipelines, erstellen und planen, die Daten aus unterschiedlichen Datenspeichern erfassen. Sie können mithilfe dieser Pipelines Daten aus internen und externen Quellen erfassen. Kunden können die Daten anschließend verarbeiten und für die Ausgabe in Datenspeicher, wie z.B. SQL Data Warehouse, transformieren.
- Kunden können ein Staging für unstrukturierte Daten in [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) durchführen. Dies bietet ihnen die Möglichkeit, Daten von beliebiger Größe, Art und Erfassungsgeschwindigkeit zur Durchführung operativer und explorativer Analysen an einem einzigen Ort zu erfassen. Azure Data Lake verfügt über Funktionen für die Extraktion und Konvertierung von Daten. Data Lake Storage ist mit den meisten Open Source-Komponenten des Hadoop-Ökosystems kompatibel. Darüber hinaus ist eine problemlose Integration in andere Azure-Dienste, wie z.B. SQL Data Warehouse, möglich.

## <a name="disclaimer"></a>Haftungsausschluss

 - Dieses Dokument dient nur zu Informationszwecken. MICROSOFT ÜBERNIMMT KEINE AUSDRÜCKLICHEN, STILLSCHWEIGENDEN ODER GESETZLICH VORGEGEBENEN GARANTIEN HINSICHTLICH DER INFORMATIONEN IN DIESEM DOKUMENT. Dieses Dokument wird so bereitgestellt, wie es ist. Informationen und Stellungnahmen in diesem Dokument, einschließlich URLs und anderer Verweise auf Internetwebsites, können ohne vorherige Ankündigung geändert werden. Kunden, die dieses Dokument lesen, tragen das Risiko, es zu verwenden.
 - Dieses Dokument gibt Kunden keine Rechte an geistigem Eigentum an Microsoft-Produkten oder -Lösungen.
 - Kunden dürfen dieses Dokument zu internen Referenzzwecken kopieren und verwenden.
 - Bestimmte Empfehlungen in diesem Dokument können zu einer erhöhten Nutzung von Daten, Netzwerken oder Computeressourcen in Azure sowie zu höheren Azure-Lizenz- oder -Abonnementkosten für den Kunden führen.
 - Diese Architektur soll Kunden als Grundlage für die Anpassung an ihre spezifischen Anforderungen dienen. Sie sollte nicht unverändert in einer Produktionsumgebung verwendet werden.
 - Dieses Dokument wurde als Referenz entwickelt und sollte nicht dazu verwendet werden, alle Mittel zu definieren, mit denen ein Kunde bestimmte Konformitätsanforderungen und -vorschriften erfüllen kann. Kunden sollten bei genehmigten Kundenimplementierungen rechtliche Unterstützung von ihrer Organisation einholen.
