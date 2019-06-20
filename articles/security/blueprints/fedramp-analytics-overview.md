---
title: 'Vorlage für Azure Security and Compliance: Analyse für FedRAMP'
description: 'Vorlage für Azure Security and Compliance: Analyse für FedRAMP'
services: security
author: jomolesk
ms.assetid: b4675715-668e-4557-9c1c-698aabf62ceb
ms.service: security
ms.topic: article
ms.date: 05/02/2018
ms.author: jomolesk
ms.openlocfilehash: fa10ff14bf893c268d6b6b1a0d181d11a3f27dc4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60586286"
---
# <a name="azure-security-and-compliance-blueprint-analytics-for-fedramp"></a>Azure-Blaupause für Sicherheit und Compliance: Analyse für FedRAMP

## <a name="overview"></a>Übersicht

Das [Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) ist ein Programm der US-Regierung, das einen standardisierten Ansatz zur Sicherheitsbewertung, Autorisierung und kontinuierlichen Überwachung von Cloudprodukten und -diensten bietet. Diese Vorlage für Azure Security and Compliance enthält Anweisungen zum Erstellen einer Analysearchitektur für Microsoft Azure, über die Sie FedRAMP High-Steuerelemente implementieren können. Diese Lösung umfasst Anweisungen für die Bereitstellung und Konfiguration von Azure-Ressourcen für eine allgemeine Referenzarchitektur. Sie zeigt Wege auf, wie Kunden spezifische Sicherheits- und Konformitätsanforderungen erfüllen können und dient als Grundlage für Kunden, damit diese ihre eigenen Analyselösungen in Azure entwickeln und konfigurieren können.

Diese Referenzarchitektur, die zugehörige Anleitung für die Implementierung von Steuerelementen und die Bedrohungsmodelle sollen Kunden als Grundlage für die Anpassung an ihre spezifischen Anforderungen dienen. Sie sollten nicht unverändert in einer Produktionsumgebung verwendet werden. Es reicht nicht aus, eine Anwendung ohne Änderungen in dieser Umgebung bereitzustellen, um die Anforderungen der FedRAMP High-Baseline vollständig zu erfüllen. Beachten Sie Folgendes:
- Diese Architektur bietet eine Baseline, um Kunden bei der Bereitstellung von Workloads in Azure in FedRAMP-konformer Weise zu unterstützen.
- Der Kunde ist für die Durchführung einer angemessenen Sicherheits- und Konformitätsbewertung jeder Lösung verantwortlich, die mit dieser Architektur erstellt wurde, da die Anforderungen je nach den Besonderheiten der jeweiligen Implementierung des Kunden unterschiedlich sein können.

## <a name="architecture-diagram-and-components"></a>Architekturdiagramm und Komponenten

Diese Lösung umfasst eine Analyseplattform, auf der Kunden ihre eigenen Analysetools aufbauen können. Die Referenzarchitektur stellt einen generischen Anwendungsfall dar. Dabei geben Kunden Daten entweder durch einen Massenimport durch den SQL- bzw. den Datenadministrator oder durch betriebliche Datenupdates über einen betrieblichen Benutzer ein. Bei beiden Möglichkeiten wird [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) für den Import von Daten in die SQL-Datenbank integriert. Azure Functions muss vom Kunden über das Azure-Portal konfiguriert werden, damit die Importaufgaben entsprechend der jeweiligen Analyseanforderungen des Kunden verarbeitet werden können.

Microsoft Azure bietet den Kunden verschiedene Dienste zur Berichterstellung und Analyse. Diese Lösung integriert Azure Analysis Services in Azure SQL-Datenbank, damit Daten schnell durchsucht werden und durch eine bessere Modellierung von Kundendaten schneller Ergebnisse geliefert werden können. Azure Analysis Services ist eine Form von maschinellem Lernen und ist darauf ausgerichtet, die Abfragegeschwindigkeit zu erhöhen, indem neue Beziehungen zwischen Datasets ermittelt werden. Wenn die Daten über verschiedene statistische Funktionen trainiert wurden, können bis zu sieben zusätzliche Abfragepools (mit dem Kundenserver acht) mit denselben tabellarischen Modellen synchronisiert werden, um die Abfrageworkload auszuweiten und die Antwortzeiten zu reduzieren.

Für erweiterte Analysen und Berichterstattungen können SQL-Datenbanken mit Columnstore-Indizes konfiguriert werden. Azure Analysis Services und SQL-Datenbanken können abhängig von der Nutzung durch den Kunden zentral hoch- bzw. herunterskaliert oder vollständig ausgeschaltet werden. Jeglicher SQL-Datenverkehr wird mit SSL über selbstsignierte Zertifikate verschlüsselt. Azure empfiehlt als bewährte Methode die Verwendung einer vertrauenswürdigen Zertifizierungsstelle für erweiterte Sicherheit.

Sobald Daten in Azure SQL-Datenbank hochgeladen und von Azure Analysis Services trainiert wurden, werden Sie sowohl von dem betrieblichen Benutzer als auch vom SQL- bzw. Datenadministrator über Power BI verarbeitet. Power BI zeigt Daten intuitiv an und erfasst Informationen für mehrere Datasets, um zusätzliche Erkenntnisse zu erhalten. Power BI weist ein hohes Maß an Flexibilität auf und kann in Azure SQL-Datenbank integriert werden. Dadurch wird sichergestellt, dass Kunden eine Konfiguration vornehmen können, damit dieser Dienst entsprechend der jeweiligen Geschäftsanforderungen die verschiedensten Szenarios verarbeiten kann.

Die gesamte Lösung baut auf einem Azure Storage auf, den Kunden mit einem Konto über das Azure-Portal konfigurieren. Azure Storage verschlüsselt alle Daten über die Speicherdienstverschlüsselung, um die Vertraulichkeit von ruhenden Daten zu bewahren.  Über einen sogenannten georedundanten Speicher (GRS) wird sichergestellt, dass es bei einem Zwischenfall im primären Rechenzentrum des Kunden nicht zu einem Datenverlust kommt, da eine zweite Kopie an einem weiteren Standort mehrere Hundert Kilometer entfernt gespeichert wird.

Aus Sicherheitsgründen verwaltet diese Architektur Ressourcen über Azure Active Directory und Azure Key Vault. Die Systemintegrität wird durch Azure Monitor überwacht. Der Kunde konfiguriert das Überwachen von Diensten zum Speichern von Protokollen und Anzeigen der Systemintegration in nur einem Dashboard, durch das man leicht navigieren kann.

Azure SQL-Datenbank wird in der Regel über SQL Server Management Studio (SSMS) verwaltet. SSMS wird über einen lokalen Computer ausgeführt, der für den Zugriff auf Azure SQL-Datenbank über eine sichere VPN- oder ExpressRoute-Verbindung konfiguriert ist. **Azure empfiehlt das Konfigurieren einer VPN- oder Azure ExpressRoute-Verbindung für die Verwaltung und den Datenimport in die Ressourcengruppe der Referenzarchitektur.**

![Analyse für ein FedRAMP-Referenzarchitekturdiagramm](images/fedramp-analytics-reference-architecture.png?raw=true "Analytics for FedRAMP reference architecture diagram")

### <a name="roles"></a>Rollen
Die Analytics-Vorlage umfasst ein Szenario mit drei allgemeinen Benutzertypen: der betriebliche Benutzer, der SQL- bzw. Datenadministrator und der Systemtechniker. Mithilfe der rollenbasierten Zugriffssteuerung von Azure kann ein genaues Zugriffsmanagement über integrierte benutzerdefinierte Rollen implementiert werden. Ressourcen sind zum Konfigurieren der [rollenbasierten Zugriffssteuerung](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) und zum Gliedern und Implementieren von [vordefinierten Rollen](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) verfügbar.

#### <a name="systems-engineer"></a>Systemtechniker
Der Systemtechniker ist der Besitzer des Kundenabonnements für Azure. Er konfiguriert die Bereitstellung der Lösung über das Azure-Portal.

#### <a name="sqldata-administrator"></a>SQL- bzw. Datenadministrator
Der SQL- bzw. Datenadministrator richtet die Funktionen für den Massenimport von Daten und für betriebliche Datenupdates für einen Upload auf die Azure SQL-Datenbank ein. Der SQL- bzw. Datenadministrator ist zwar nicht für betriebliche Datenupdates in der Datenbank zuständig, er kann jedoch die Daten über Power BI abrufen.

#### <a name="operational-user"></a>Betrieblicher Benutzer
Der betriebliche Benutzer aktualisiert die Daten regelmäßig und ist der Besitzer der täglich durchgeführten Datengenerierung. Der betriebliche Benutzer interpretiert außerdem Ergebnisse über Power BI.

### <a name="azure-services"></a>Azure-Dienste

Diese Lösung verwendet die folgenden Azure-Dienste. Details zur Bereitstellungsarchitektur finden Sie im Abschnitt [Bereitstellungsarchitektur](#deployment-architecture).
- Azure-Funktionen
- Azure SQL-Datenbank
- Azure Analysis Services
- Azure Active Directory
- Azure Key Vault
- Azure Monitor (Protokolle)
- Azure Storage
- ExpressRoute/VPN Gateway
- Power BI-Dashboard

## <a name="deployment-architecture"></a>Bereitstellungsarchitektur
Im folgenden Abschnitt werden die Elemente für Entwicklung und Implementierung beschrieben.

![alt text](images/fedramp-analytics-components.png?raw=true "Analyse für ein FedRAMP-Komponentendiagramm")

**Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) stellt eine Lösung zum Ausführen kleinerer Codeelemente in der Cloud über einen Großteil der Programmiersprachen dar. Die Funktionen dieser Lösung können in Azure Storage integriert werden, sodass Kundendaten automatisch in die Cloud übertragen werden. Dadurch wird die Integration in andere Azure-Dienste erleichtert. Die Funktionen können einfach skaliert werden und verursachen nur Kosten, wenn sie ausgeführt werden.

**Azure Analysis Services**: [Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) umfasst Funktionen zum Modellieren von Unternehmensdaten und zur Integration in Datenplattformdienste für Azure. Azure Analysis Services beschleunigt das Durchsuchen von sehr großen Datenmengen, indem Daten aus mehreren Quellen in ein einzelnes Datenmodell zusammengefasst werden.

**Power BI**: [Power BI](https://docs.microsoft.com/power-bi/service-azure-and-power-bi) umfasst Funktionen zur Analyse und Berichterstellung für Kunden, die mehr Informationen über die Datenverarbeitung erhalten möchten.

### <a name="networking"></a>Netzwerk
**Netzwerksicherheitsgruppen**: [NSGs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) sind dafür eingerichtet, Datenverkehr zu verwalten, der auf bereitgestellte Ressourcen und Dienste ausgerichtet ist. NSGs sind mit einem Schema konfiguriert, das den Zugriff standardmäßig verweigert und nur Datenverkehr zulässt, der in der vorkonfigurierten Zugriffssteuerungsliste enthalten ist.

Jede der NSGs verfügt über bestimmte offene Ports und Protokolle, damit die Lösung sicher und richtig ausgeführt werden kann. Darüber hinaus werden die folgenden Konfigurationen für jede NSG aktiviert:
  - [Diagnoseprotokolle und -ereignisse](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) sind aktiviert und werden im Speicherkonto gespeichert.
  - [Azure Monitor-Protokolle](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics) ist mit den Diagnoseprotokollen der NSG verbunden.

### <a name="data-at-rest"></a>Ruhende Daten
Die Architektur schützt ruhende Daten durch die Verwendung von Verschlüsselung, Datenbanküberwachung und anderen Maßnahmen.

**Datenreplikation:** Azure Government umfasst zwei Optionen für die [Datenreplikation](https://docs.microsoft.com/azure/storage/common/storage-redundancy):
 - Die Standardeinstellung für die Datenreplikation lautet **georedundanter Speicher**. Durch diese Einstellung werden Kundendaten auf asynchrone Weise in einem separaten Datenzentrum außerhalb der primären Region gespeichert. Dadurch können Daten wiederhergestellt werden, die bei einem möglichen Gesamtausfall des primären Datenzentrums verloren gehen könnten.
 - Stattdessen kann auch der **lokal redundante Speicher (LRS)** über das Azure Storage-Konto konfiguriert werden. Der LRS repliziert Daten in einer Speicherskalierungseinheit, die in der Region gehostet wird, in der der Kunde ein Konto erstellt. Alle Daten werden gleichzeitig repliziert, um sicherzustellen, dass keine Sicherungsdaten im Falle eines Ausfalls der primären Speicherskalierungseinheit verloren gehen können.

**Azure Storage** Damit alle Anforderungen für verschlüsselte ruhende Daten erfüllt werden können, nutzen alle Dienste, die in dieser Referenzarchitektur bereitgestellt werden, [Azure Storage](https://azure.microsoft.com/services/storage/). Azure Storage speichert Daten wiederum über die [Speicherdienstverschlüsselung](https://docs.microsoft.com/azure/storage/storage-service-encryption).

**Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) nutzt das BitLocker-Feature von Windows, um Volumeverschlüsselung für Betriebssysteme und Datenträger bereitzustellen. Die Lösung ist in Azure Key Vault integriert, um die Steuerung und Verwaltung der Datenträgerverschlüsselungsschlüssel zu erleichtern.

**Azure SQL-Datenbank** In der Azure SQL-Datenbankinstanz werden die folgenden Datenbanksicherheitsmaßnahmen verwendet:
-   Die [AD-Authentifizierung und -Autorisierung](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) ermöglicht die zentrale Verwaltung der Identitäten von Datenbankbenutzern und anderen Microsoft-Diensten.
-   Die [SQL-Datenbank-Überprüfung](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) verfolgt Datenbankereignisse nach und schreibt diese in ein Überwachungsprotokoll in einem Azure Storage-Konto.
-   Die SQL-Datenbank ist so konfiguriert, dass sie [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) verwendet, die Echtzeitverschlüsselung und -entschlüsselung von Daten und Protokolldateien ausführt, um ruhende Informationen zu schützen. TDE gewährleistet, dass gespeicherte Daten keinen unbefugten Zugriffen ausgesetzt sind.
-   [Firewallregeln](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) verhindern sämtlichen Zugriff auf Datenbankserver, bis die richtigen Berechtigungen erteilt werden. Die Firewall gewährt den Datenbankzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung.
-   Die [SQL-Bedrohungserkennung](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) ermöglicht die Erkennung und Reaktion auf potenzielle Bedrohungen, sobald sie auftreten. Dazu werden Sicherheitswarnungen bei verdächtigen Datenbankaktivitäten, potenziellen Sicherheitslücken, Angriffen durch Einschleusung von SQL-Befehlen und anormalen Mustern beim Datenbankzugriff ausgegeben.
-   [Always Encrypted-Spalten](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) stellen sicher, dass vertrauliche Daten im Datenbanksystem niemals im Klartextformat angezeigt werden. Nach dem Aktivieren der Datenverschlüsselung können nur Clientanwendungen oder App-Server, die Zugriff auf die Schlüssel haben, auf Klartextdaten zugreifen.
-   Die [dynamische Datenmaskierung in SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) kann durchgeführt werden, nachdem die Referenzarchitektur bereitgestellt wurde. Kunden müssen die Einstellungen für die dynamische Datenmaskierung an ihr Datenbankschema anpassen.

### <a name="logging-and-audit"></a>Protokollierung und Überwachung
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) generiert eine umfassende Anzeige von Überwachungsdaten, einschließlich Aktivitätsprotokolle, Metriken und Diagnosedaten, über die sich Kunden ein umfassendes Bild über die Systemintegration machen können.  
[Azure Monitor-Protokolle](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) ermöglicht eine umfassende Protokollierung von System- und Benutzeraktivitäten sowie der Systemintegrität. Es handelt sich um eine Lösung für die Erfassung und Analyse von Daten, die von Ressourcen in Azure und lokalen Umgebungen generiert werden.
- **Aktivitätsprotokolle:** [Aktivitätsprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) bieten Einblicke in Vorgänge, die für Ressourcen in einem Abonnement ausgeführt wurden.
- **Diagnoseprotokolle:** [Diagnoseprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) umfassen sämtliche Protokolle, die von jeder Ressource ausgegeben werden. Diese Protokolle umfassen Windows-Ereignissystemprotokolle, Azure Blob Storage-, Tabellen- und Warteschlangenprotokolle.
- **Firewallprotokolle:** Application Gateway stellt umfassende Diagnose- und Zugriffsprotokolle bereit. Firewallprotokolle stehen für Application Gateway-Ressourcen zur Verfügung, für die WAF aktiviert ist.
- **Protokollarchivierung:** Alle Diagnoseprotokolle schreiben in ein zentrales und verschlüsseltes Azure-Speicherkonto für die Archivierung mit einer definierten Beibehaltungsdauer von 2 Tagen. Diese Protokolle werden dann zur Verarbeitung, Speicherung und Dashboardanzeige mit Azure Monitor-Protokolle verbunden.

Darüber hinaus sind in dieser Architektur die folgenden Überwachungslösungen enthalten:
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Die Azure Automation-Lösung dient der Speicherung, Ausführung und Verwaltung von Runbooks.
-   [Sicherheit und Überwachung](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): Das Sicherheits- und Überwachungsdashboard ermöglicht einen allgemeinen Einblick in den Sicherheitszustand von Ressourcen. Hierzu werden Metriken zu Sicherheitsdomänen, relevanten Problemen, Erkennungen, Bedrohungsdaten und allgemeinen Sicherheitsabfragen bereitgestellt.
-   [SQL-Bewertung](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Die Lösung „SQL-Integritätsüberprüfung“ bewertet die Risiken und die Integrität von Serverumgebungen in regelmäßigen Abständen und erstellt für die Kunden eine priorisierte Liste mit spezifischen Empfehlungen für die bereitgestellte Serverinfrastruktur.
-   [Azure-Aktivitätsprotokolle](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Die Lösung „Aktivitätsprotokollanalyse“ hilft bei der Analyse der Azure-Aktivitätsprotokolle in allen Azure-Abonnements eines Kunden.

### <a name="identity-management"></a>Identitätsverwaltung
-   Authentifizierung für die Anwendung erfolgt mithilfe von Azure AD. Weitere Informationen finden Sie unter [Integrieren von Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Darüber hinaus wird für die Verschlüsselung der Datenbankspalten Azure AD verwendet, um die Anwendung für Azure SQL-Datenbank zu authentifizieren. Weitere Informationen finden Sie unter [Schützen von vertraulichen Daten in SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) erkennt potenzielle Sicherheitsrisiken, die sich auf die Identitäten einer Organisation auswirken, konfiguriert automatisierte Antworten auf erkannte verdächtige Aktionen, die im Zusammenhang mit Identitäten der Organisation stehen, untersucht verdächtige Vorfälle und führt die entsprechenden Aktionen zu deren Behebung aus.
-   Die [rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) in Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) ermöglicht eine Zugriffsverwaltung für Azure. Der Abonnementzugriff ist auf den Abonnementadministrator beschränkt.

Weitere Informationen zum Verwenden der Sicherheitsfunktionen von Azure SQL-Datenbank finden Sie im Beispiel [Contoso Clinic Demo Application](https://github.com/Microsoft/azure-sql-security-sample).

### <a name="security"></a>Sicherheit
**Geheimnisverwaltung**: Die Lösung verwendet [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) für die Verwaltung von Schlüsseln und Geheimnissen. Azure Key Vault unterstützt Sie dabei, kryptografische Schlüssel und Geheimnisse zu schützen, die von Cloudanwendungen und -diensten verwendet werden.

## <a name="guidance-and-recommendations"></a>Anleitungen und Empfehlungen

### <a name="expressroute-and-vpn"></a>ExpressRoute und VPN
[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) oder ein sicherer VPN-Tunnel müssen für eine sichere Verbindung mit Ressourcen, die als Teil dieser Referenzarchitektur für die Datenanalyse bereitgestellt werden, konfiguriert werden. ExpressRoute-Verbindungen erfolgen nicht über das öffentliche Internet und bieten eine höhere Sicherheit, größere Zuverlässigkeit und schnellere Geschwindigkeit bei geringerer Latenz als herkömmliche Verbindungen über das Internet. Durch die ordnungsgemäße Einrichtung von VPN oder ExpressRoute können Kunden eine zusätzliche Sicherheitsebene für Daten während der Übertragung hinzufügen.

### <a name="azure-active-directory-setup"></a>Azure Active Directory-Einrichtungen
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) ist wichtig für die Verwaltung der Bereitstellung von Zugriff für Mitarbeiter, die mit der Umgebung interagieren. Ein bereits vorhandenes Windows Server Active Directory-Verzeichnis kann über [vier Klicks](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express) in Azure Active Directory integriert werden. Die Kunden können außerdem die bereitgestellte Active Directory-Infrastruktur (Domänencontroller) an ein bereits vorhandenes Azure Active Directory-Verzeichnis binden, indem sie die bereitgestellte Active Directory-Infrastruktur als untergeordnete Domäne für eine AAD-Struktur festlegen.

### <a name="additional-services"></a>Zusätzliche Dienste
#### <a name="iaas---vm-considerations"></a>IaaS: Überlegungen zu virtuellen Computern
Diese PaaS-Lösung umfasst keine Azure-IaaS-VMs. Ein Kunde kann z.B.eine Azure-VM erstellen, um einige dieser PaaS-Dienste auszuführen. In diesem Fall können bestimmte Features und Dienste für Geschäftskontinuität und Azure Monitor-Protokolle verwendet werden:

##### <a name="business-continuity"></a>Geschäftskontinuität
- **Hochverfügbarkeit**: Serverworkloads werden in einer [Verfügbarkeitsgruppe](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) zusammengefasst, um Hochverfügbarkeit für virtuelle Computer in Azure besser sicherzustellen. Während einer geplanten oder ungeplanten Wartung ist mindestens ein virtueller Computer verfügbar, um die Azure-SLA von 99,95 % zu erfüllen.

- **Recovery Services-Tresor**: Der [Recovery Services-Tresor](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) speichert Sicherungsdaten und schützt alle Konfigurationen von Azure Virtual Machines in dieser Architektur. Durch einen Recovery Services-Tresor können Kunden Dateien und Ordner von einer IaaS-VM wiederherstellen, ohne die gesamte VM wiederherstellen zu müssen. So werden die Wiederherstellungszeiten verkürzt.

##### <a name="monitoring-solutions"></a>Überwachungslösungen
-   [Active Directory-Bewertung](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Die Lösung „Active Directory-Integritätsüberprüfung“ bewertet die Risiken und die Integrität von Serverumgebungen in regelmäßigen Abständen und erstellt eine priorisierte Liste mit spezifischen Empfehlungen für die bereitgestellte Serverinfrastruktur.
-   [Antischadsoftwarebewertung](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): Die Antischadsoftwarelösung meldet Schadsoftware, Bedrohungen und den Schutzstatus.
-   [Updateverwaltung](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): Die Updateverwaltungslösung ermöglicht die Verwaltung von Sicherheitsupdates des Betriebssystems durch die Kunden. Dies umfasst auch den Status der verfügbaren Updates und den Fortschritt beim Installieren der erforderlichen Updates.
-   [Agent-Integritätsdiagnose](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Die Lösung „Agent-Integritätsdiagnose“ meldet die Anzahl bereitgestellter Agents sowie deren geografische Verteilung. Außerdem meldet sie, wie viele Agents nicht mehr reagieren und wie viele Agents Betriebsdaten übermitteln.
-   [Änderungsnachverfolgung](https://docs.microsoft.com/azure/automation/automation-change-tracking): Die Lösung „Änderungsnachverfolgung“ ermöglicht es Kunden, problemlos Änderungen in der Umgebung zu erkennen.

##### <a name="security"></a>Sicherheit
- **Schutz vor Schadsoftware**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) für Virtual Machines bietet Echtzeitschutzfunktionen, mit denen die Identifizierung und Entferung von Viren, Spyware und anderer bösartiger Software ermöglicht wird, mit konfigurierbaren Warnmeldungen, wenn bekannte bösartige oder unerwünschte Software versucht, sich auf geschützten virtuellen Computern zu installieren oder die Ausführung zu starten.
- **Patchverwaltung**: Virtuelle Windows-Computer, die mit dieser Referenzarchitektur bereitgestellt werden, sind standardmäßig so konfiguriert, dass sie automatische Updates vom Windows Update-Dienst erhalten. Diese Lösung umfasst auch den Dienst [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro), über den aktualisierte Bereitstellungen erstellt werden können, um virtuelle Computer bei Bedarf zu patchen.

#### <a name="azure-commercial"></a>Azure Commercial
Obwohl diese Architektur zur Datenanalyse nicht für die Bereitstellung in der [Azure Commercial](https://azure.microsoft.com/overview/what-is-azure/)-Umgebung vorgesehen ist, können ähnliche Ergebnisse über die in dieser Referenzarchitektur beschriebenen Dienste sowie über zusätzliche Dienste erzielt werden, die nur in der Azure Commercial-Umgebung verfügbar sind. Beachten Sie, dass Azure Commercial FedRAMP JAB P-ATO für die Moderate Impact-Ebene verwaltet. Dadurch ist es Behörden und Partnern möglich, vertrauliche Informationen in der Cloud bereitzustellen, die die Azure Commercial-Umgebung nutzt.

Azure Commercial bietet verschiedene Analysedienste, um großen Datenmengen Informationen zu entnehmen:
-   [Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/what-is-ml-studio) ist eine Komponente der [Cortana Intelligence Suite](https://azure.microsoft.com/overview/ai-platform/) und entwickelt ein Analysemodell für Vorhersagen aus mindestens einer Datenquelle. Statistische Funktionen werden für verschiedene Iterationen verwendet, um ein effektives Modell zu erstellen, das Anwendungen wie Power BI verarbeiten kann.
-   [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) bietet Ihnen die Möglichkeit, Daten von beliebiger Größe, Art und Erfassungsgeschwindigkeit zur Durchführung operativer und explorativer Analysen an einem einzigen Ort zu erfassen. Azure Data Lake Store ist mit den meisten Open Source-Komponenten des Hadoop-Ökosystems und anderen Azure-Diensten kompatibel.

## <a name="threat-model"></a>Bedrohungsmodell

Das Datenflussdiagramm (DFD) für diese Referenzarchitektur steht zum [Download](https://aka.ms/blueprintanalyticsthreatmodel) bereit und ist im Folgenden angegeben:

## <a name="compliance-documentation"></a>Konformitätsdokumentation
Die [Vorlage für Azure Security and Compliance: FedRAMP High-Kundenzuständigkeitsmatrix](https://aka.ms/blueprinthighcrm) listet alle Sicherheitskontrollen auf, die von der FedRAMP High-Baseline verlangt werden. Die [Vorlage für Azure Security and Compliance: FedRAMP Moderate-Kundenzuständigkeitsmatrix](https://aka.ms/blueprintanalyticscimmod) listet auf ähnliche Weise alle Sicherheitskontrollen auf, die von der FedRAMP Moderate-Baseline verlangt werden. In beiden Dokumenten wird erläutert, ob die Implementierung der einzelnen Steuerelemente Aufgabe von Microsoft, dem Kunden oder von beiden ist.

Die [Vorlage für Azure Security and Compliance: FedRAMP High-Regulierungsimplementierungsmatrix](https://aka.ms/blueprintanalyticscimhigh) und die [Vorlage für Azure Security and Compliance: FedRAMP Moderate-Regulierungsimplementierungsmatrix](https://aka.ms/blueprintanalyticscimmod) beinhalten Informationen dazu, welche Steuerelemente von der Analysearchitektur für die einzelnen FedRAMP-Baselines abgedeckt werden, einschließlich detaillierter Beschreibungen, inwiefern die Implementierung die Anforderungen der jeweiligen abgedeckten Steuerelemente erfüllt werden.

## <a name="disclaimer"></a>Haftungsausschluss

 - Dieses Dokument dient nur zu Informationszwecken. MICROSOFT ÜBERNIMMT KEINE AUSDRÜCKLICHEN, STILLSCHWEIGENDEN ODER GESETZLICH VORGEGEBENEN GARANTIEN HINSICHTLICH DER INFORMATIONEN IN DIESEM DOKUMENT. Dieses Dokument wird so bereitgestellt, wie es ist. Informationen und Stellungnahmen in diesem Dokument, einschließlich URLs und anderer Verweise auf Internetwebsites, können ohne vorherige Ankündigung geändert werden. Kunden, die dieses Dokument lesen, tragen das Risiko, es zu verwenden.
 - Dieses Dokument gibt Kunden keine Rechte an geistigem Eigentum an Microsoft-Produkten oder -Lösungen.
 - Kunden dürfen dieses Dokument zu internen Referenzzwecken kopieren und verwenden.
 - Bestimmte Empfehlungen in diesem Dokument können zu einer erhöhten Nutzung von Daten, Netzwerken oder Computeressourcen in Azure sowie zu höheren Azure-Lizenz- oder -Abonnementkosten für den Kunden führen.
 - Diese Architektur soll Kunden als Grundlage für die Anpassung an ihre spezifischen Anforderungen dienen. Sie sollte nicht unverändert in einer Produktionsumgebung verwendet werden.
 - Dieses Dokument wurde als Referenz entwickelt und sollte nicht dazu verwendet werden, alle Mittel zu definieren, mit denen ein Kunde bestimmte Konformitätsanforderungen und -vorschriften erfüllen kann. Kunden sollten bei genehmigten Kundenimplementierungen rechtliche Unterstützung von ihrer Organisation einholen.
