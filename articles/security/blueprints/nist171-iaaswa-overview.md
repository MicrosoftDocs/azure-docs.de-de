---
title: 'Vorlage für Azure Security and Compliance: IaaS-Webanwendung gemäß NIST SP 800-171'
description: 'Vorlage für Azure Security and Compliance: IaaS-Webanwendung gemäß NIST SP 800-171'
services: security
author: jomolesk
ms.assetid: 1f1ad27f-32c3-4e76-abb9-ea768d01747f
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: b30094e264086f018acbf84144300df46c60ac4e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60610276"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-nist-sp-800-171"></a>Vorlage für Azure Security and Compliance: IaaS-Webanwendung gemäß NIST SP 800-171

## <a name="overview"></a>Übersicht
[NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) enthält Richtlinien zum Schützen der nicht klassifizierten kontrollierten Informationen (Controlled Unclassified Information, CUI), die sich in nicht behördlichen Informationssystemen und Organisationen befinden. In NIST SP 800-171 sind 14 „Familien“ mit Sicherheitsanforderungen zum Schützen der Vertraulichkeit von nicht klassifizierten kontrollierten Informationen festgelegt.

Diese Vorlage für Azure Security and Compliance enthält eine Anleitung, mit der Kunden eine Architektur für eine Webanwendung in Azure bereitstellen können, die eine Teilmenge der NIST SP 800-171-Kontrollmechanismen implementiert. Diese Lösung veranschaulicht die Möglichkeiten von Kunden, bestimmte Sicherheits- und Konformitätsanforderungen zu erfüllen. Sie dient auch als Grundlage für Kunden für die Erstellung und Konfiguration ihrer eigenen Web-Apps in Azure.

Diese Referenzarchitektur, die zugehörige Implementierungsanleitung und das Bedrohungsmodell sollen Kunden als Grundlage für die Anpassung an ihre spezifischen Anforderungen dienen. Sie sollten nicht unverändert in einer Produktionsumgebung verwendet werden. Die Bereitstellung dieser Architektur ohne Änderungen ist unzureichend, um die Anforderungen von NIST SP 800-171 vollständig zu erfüllen. Kunden sind dafür verantwortlich, angemessene Sicherheits- und Konformitätsbewertungen für jede Lösung durchzuführen, die mit dieser Architektur erstellt wurde. Die Anforderungen variieren je nach den Gegebenheiten der Implementierung des jeweiligen Kunden.

## <a name="architecture-diagram-and-components"></a>Architekturdiagramm und Komponenten
Diese Azure-Blaupause für Sicherheit und Compliance stellt eine Referenzarchitektur für eine IaaS-Webanwendung mit einem SQL Server-Back-End bereit. Die Architektur umfasst eine Webschicht, eine Datenschicht, eine Active Directory-Infrastruktur, ein Azure Application Gateway und einen Azure Load Balancer. Für die Web- und Datenschichten bereitgestellte virtuelle Computer (Virtual Machines, VMs) werden in einer Verfügbarkeitsgruppe konfiguriert. SQL Server-Instanzen werden in einer Always On-Verfügbarkeitsgruppe für Hochverfügbarkeit konfiguriert. Virtuelle Computer sind in Domänen eingebunden. Active Directory-Gruppenrichtlinien setzen Sicherheits- und Konformitätskonfigurationen auf Betriebssystemebene durch.

Die gesamte Lösung baut auf Azure Storage-Dienst auf, den Kunden über das Azure-Portal konfigurieren können. Azure Storage verschlüsselt alle Daten über die Speicherdienstverschlüsselung, um die Vertraulichkeit von ruhenden Daten zu bewahren. Durch georedundanten Speicher wird sichergestellt, dass ein Zwischenfall im primären Rechenzentrum des Kunden keinen Datenverlust verursacht. An einem separaten Standort Hunderte von Kilometern entfernt wird eine zweite Kopie gespeichert.

Für höhere Sicherheit werden alle Ressourcen in dieser Lösung als Ressourcengruppe über Azure Resource Manager verwaltet. Die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure Active Directory (Azure AD) dient zur Steuerung des Zugriffs auf die bereitgestellten Ressourcen und Schlüssel in Azure Key Vault. Die Systemintegrität wird durch Azure Monitor überwacht. Kunden konfigurieren beide Überwachungsdienste für die Erfassung von Protokollen. Die Systemintegrität wird auf einem einzigen, benutzerfreundlichen Dashboard angezeigt.

Ein Bastionhost für die Verwaltung bietet Administratoren eine sichere Verbindung für den Zugriff auf bereitgestellte Ressourcen. *Microsoft empfiehlt Ihnen, eine VPN- oder Azure ExpressRoute-Verbindung für die Verwaltung und den Datenimport in das Subnetz der Referenzarchitektur zu konfigurieren.*


![IaaS-Webanwendung gemäß NIST SP 800-171 – Diagramm zur Referenzarchitektur](images/nist171-iaaswa-architecture.png "IaaS-Webanwendung gemäß NIST SP 800-171 – Diagramm zur Referenzarchitektur")

Diese Lösung verwendet die folgenden Azure-Dienste. Weitere Informationen finden Sie im Abschnitt [Bereitstellungsarchitektur](#deployment-architecture).

- Azure Virtual Machines
    - (1) Management/Bastion (Windows Server 2016 Datacenter)
    - (2) Active Directory-Domänencontroller (Windows Server 2016 Datacenter)
    - (2) SQL Server-Clusterknoten (SQL Server 2017 unter Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Virtuelles Azure-Netzwerk
    - (1) /16-Netzwerk
    - (5) /24-Netzwerke
    - (5) Netzwerksicherheitsgruppen
- Verfügbarkeitsgruppen
    - (1) Active Directory-Domänencontroller
    - (1) SQL-Clusterknoten
    - (1) Web/IIS
- Azure Application Gateway
    - (1) Web Application Firewall
        - Firewallmodus: Prävention
        - Regelsatz: OWASP 3.0
        - Listenerport: 443
- Azure Active Directory
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor (Protokolle)
- Azure Resource Manager
- Azure Security Center
- Azure Storage
- Azure-Automatisierung
- Cloudzeuge
- Recovery Services-Tresor

## <a name="deployment-architecture"></a>Bereitstellungsarchitektur
Im folgenden Abschnitt werden die Elemente für Entwicklung und Implementierung beschrieben.

**Bastionhost**: Der Bastionhost ist der einzelne Zugangspunkt, über den Benutzer Zugriff auf die bereitgestellten Ressourcen in dieser Umgebung erhalten. Der Bastionhost bietet eine sichere Verbindung mit den bereitgestellten Ressourcen, indem nur Remotedatenverkehr von öffentlichen IP-Adressen auf einer Sicherheitsliste zugelassen wird. Damit Remotedesktopprotokoll-Datenverkehr zugelassen wird, muss die Quelle des Datenverkehrs in der Netzwerksicherheitsgruppe (NSG) definiert sein.

Diese Lösung erstellt eine VM als Bastionhost in der Domäne mit den folgenden Konfigurationen:
-   [Antischadsoftware-Erweiterung](https://docs.microsoft.com/azure/security/azure-security-antimalware).
-   [Azure-Diagnoseerweiterung](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) mit Azure Key Vault.
-   Eine [Richtlinie zum automatischen Herunterfahren](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/), um die Nutzung der VM-Ressourcen zu verringern, wenn diese nicht benötigt werden.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) ist aktiviert, damit Anmeldeinformationen und andere geheimen Schlüssel in einer geschützten Umgebung, die vom ausgeführten Betriebssystem isoliert ist, ausgeführt werden.

### <a name="virtual-network"></a>Virtuelles Netzwerk
Die Architektur definiert ein privates virtuelles Netzwerk mit dem Adressraum 10.200.0.0/16.

**Netzwerksicherheitsgruppen**: Diese Lösung stellt Ressourcen in einer Architektur mit separatem Subnetzen für das Web, Datenbanken, Active Directory und die Verwaltung innerhalb eines virtuellen Netzwerks bereit. Subnetze werden logisch durch NSG-Regeln getrennt, die auf die einzelnen Subnetze angewendet werden. Die Regeln schränken den Datenverkehr zwischen Subnetzen auf den Datenverkehr ein, der für die System- und Managementfunktionen erforderlich ist.

Weitere Informationen finden Sie in der Konfiguration für [NSGs](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json), die mit dieser Lösung bereitgestellt wird. Organisationen können NSGs konfigurieren, indem sie die vorherige Datei bearbeiten und dabei [diese Dokumentation](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) als Leitfaden verwenden.

Jedes der Subnetze verfügt über eine dedizierte NSG:
- Eine NSG für Application Gateway (LBNSG)
- Eine NSG für Bastionhost (MGTNSG)
- Eine NSG für primären und Sicherungsdomänencontroller (ADNSG)
- Eine NSG für Computer mit SQL Server und Cloudzeuge (SQLNSG)
- Eine NSG für Webschicht (WEBNSG)

### <a name="data-in-transit"></a>Daten während der Übertragung
Azure verschlüsselt standardmäßig die gesamte Kommunikation zu und von Azure-Rechenzentren. Darüber hinaus werden alle Transaktionen mit Azure Storage über das Azure-Portal über HTTPS durchgeführt.

### <a name="data-at-rest"></a>Ruhende Daten
Die Architektur schützt ruhende Daten über mehrere Measures. Diese Measures umfassen die Überprüfung von Verschlüsselungen und Datenbanken.

**Azure Storage**: Zur Erfüllung der Anforderungen an verschlüsselte ruhende Daten wird für den gesamten [Storage](https://azure.microsoft.com/services/storage/)-Bereich die [Speicherdienstverschlüsselung](https://docs.microsoft.com/azure/storage/storage-service-encryption) verwendet. Mit dieser Funktion werden Daten geschützt, um die Zusagen und Anforderungen der Organisation im Hinblick auf Sicherheit und Konformität gemäß NIST SP 800-171 einzuhalten.

**Azure Disk Encryption**: Azure Disk Encryption wird zur Verschlüsselung der IaaS-VM-Datenträger unter Windows verwendet. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) verwendet die BitLocker-Funktion von Windows, um Volumeverschlüsselung für Betriebssystemdatenträger und reguläre Datenträger bereitzustellen. Die Lösung ist in Azure Key Vault integriert, um die Steuerung und Verwaltung der Datenträger-Verschlüsselungsschlüssel zu erleichtern.

**SQL Server:** In der SQL Server-Instanz werden die folgenden Datenbanksicherheitsmaßnahmen verwendet:
-   Bei der [SQL Server-Überwachung](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) werden Datenbankereignisse nachverfolgt und in Überwachungsprotokolle geschrieben.
-   Beim [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017)-Vorgang wird die Echtzeitverschlüsselung und -entschlüsselung der Datenbank, der zugehörigen Sicherungen und der Transaktionsprotokolldateien durchgeführt, um ruhende Informationen zu schützen. Die transparente Datenverschlüsselung gewährleistet, dass gespeicherte Daten keinen unbefugten Zugriffen ausgesetzt sind.
-   [Firewallregeln](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) verhindern sämtlichen Zugriff auf Datenbankserver, bis die richtigen Berechtigungen erteilt werden. Die Firewall gewährt den Datenbankzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung.
-   [Verschlüsselte Spalten](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) stellen sicher, dass vertrauliche Daten im Datenbanksystem niemals im Klartextformat angezeigt werden. Nach der Aktivierung der Datenverschlüsselung können nur Clientanwendungen oder Anwendungsserver, die Zugriff auf die Schlüssel haben, auf Klartextdaten zugreifen.
- Die [dynamische Datenmaskierung](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) schränkt die Offenlegung vertraulicher Daten ein, indem diese Daten für nicht berechtigte Benutzer oder Anwendungen maskiert werden. Sie kann automatisch potenziell vertrauliche Daten ermitteln und eine geeignete Maskierung vorschlagen. Die dynamische Datenmaskierung unterstützt Sie beim Reduzieren des Zugriffs, damit vertrauliche Daten die Datenbank nicht über nicht autorisierten Zugriff verlassen. *Kunden sind dafür verantwortlich, die Einstellungen an ihr Datenbankschema anzupassen.*

### <a name="identity-management"></a>Identitätsverwaltung
Die folgenden Technologien enthalten Funktionen zum Verwalten des Zugriffs auf Daten in der Azure-Umgebung:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) ist der mehrinstanzenfähige cloudbasierte Verzeichnis- und Identitätsverwaltungsdienst von Microsoft. Alle Benutzer dieser Lösung werden in Azure AD erstellt. Dies gilt auch für die Benutzer, die Zugriff auf die SQL Server-Instanz haben.
-   Authentifizierung für die Anwendung erfolgt mithilfe von Azure AD. Weitere Informationen finden Sie unter [Integrieren von Anwendungen in Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
-   [Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) ermöglicht Administratoren das Definieren präziser Zugriffsberechtigungen, um jeweils nur den Zugriff zu gewähren, den Benutzer zum Erledigen ihrer Arbeit benötigen. Anstatt jedem Benutzer uneingeschränkte Benutzerberechtigungen für Azure-Ressourcen zu gewähren, können Administratoren auch nur bestimmte Aktionen für den Zugriff auf Daten erlauben. Der Abonnementzugriff ist auf den Abonnementadministrator beschränkt.
- Mit [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) können Kunden die Anzahl von Benutzern mit Zugriff auf bestimmte Ressourcen einschränken. Administratoren können mit Azure AD Privileged Identity Management privilegierte Identitäten und den damit verbundenen Zugriff auf Ressourcen erkennen, einschränken und überwachen. Diese Funktionalität kann auch verwendet werden, um einen bedarfsgesteuerten Just-in-Time-Administratorzugriff zu erzwingen.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) erkennt potenzielle Sicherheitsrisiken, die Identitäten einer Organisation betreffen. Der Dienst konfiguriert automatische Reaktionen auf erkannte verdächtige Aktionen im Zusammenhang mit den Identitäten einer Organisation. Zudem untersucht er verdächtige Vorfälle, um entsprechende Maßnahmen zu deren Behebung zu ergreifen.

### <a name="security"></a>Sicherheit
**Geheimnisverwaltung**: Die Lösung verwendet [Key Vault](https://azure.microsoft.com/services/key-vault/) für die Verwaltung von Schlüsseln und Geheimnissen. Azure Key Vault unterstützt Sie dabei, kryptografische Schlüssel und geheime Schlüssel zu schützen, die von Cloudanwendungen und -diensten verwendet werden. Die folgenden Funktionen von Azure Key Vault unterstützen Kunden beim Schützen von Daten:
- Erweiterte Zugriffsrichtlinien werden auf Bedarfsbasis konfiguriert.
- Key Vault-Zugriffsrichtlinien werden mit minimalen erforderlichen Berechtigungen für Schlüssel und Geheimnisse definiert.
- Alle Schlüssel und Geheimnisse in Key Vault besitzen ein Ablaufdatum.
- Alle Schlüssel in Key Vault werden durch spezielle Hardwaresicherheitsmodule geschützt. Der Schlüsseltyp ist ein Hardwaresicherheitsmodul, das mit einem 2048-Bit-RSA-Schlüssel geschützt wird.
- Allen Benutzern und Identitäten werden mit RBAC die mindestens erforderlichen Berechtigungen erteilt.
- Diagnoseprotokolle für Key Vault werden mit einer Aufbewahrungsdauer von mindestens 365 Tagen aktiviert.
- Zulässige kryptographische Vorgänge für Schlüssel sind auf die erforderlichen Vorgänge beschränkt.
- Die Lösung ist in Azure Key Vault integriert, um die Verwaltung der Verschlüsselungsschlüssel für IaaS-VM-Datenträger zu erleichtern.

**Patchverwaltung**: Windows-VMs, die mit dieser Referenzarchitektur bereitgestellt werden, sind standardmäßig so konfiguriert, dass sie automatische Updates vom Windows Update-Dienst erhalten. Diese Lösung umfasst auch den Dienst [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro), über den aktualisierte Bereitstellungen erstellt werden können, um VMs bei Bedarf zu patchen.

**Schutz vor Schadsoftware**: [Microsoft-Antischadsoftware](https://docs.microsoft.com/azure/security/azure-security-antimalware) für VMs bietet eine Echtzeit-Schutzfunktion zum Bestimmen und Entfernen von Viren, Spyware und anderer Schadsoftware. Kunden können Warnungen konfigurieren, die generiert werden, wenn versucht wird, bekannte Schadsoftware oder unerwünschte Software auf geschützten VMs zu installieren oder auszuführen.

**Azure Security Center**: Mit [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) können Kunden Sicherheitsrichtlinien über Workloads hinweg zentral anwenden und verwalten, die Angriffsfläche verringern sowie Angriffe erkennen und darauf reagieren. Azure Security Center greift zudem auf die vorhandenen Konfigurationen der Azure-Dienste zu, um Empfehlungen für die Konfiguration und den Dienst bereitzustellen, die zur Verbesserung des Sicherheitsstatus und zum Schutz von Daten beitragen sollen.

Azure Security Center verwendet verschiedene Erkennungsfunktionen, um Kunden vor potenziellen Angriffen auf deren Umgebung zu warnen. Diese Warnungen enthalten wichtige Informationen zum Auslöser der Warnung, zu den möglicherweise betroffenen Ressourcen und zur Quelle des Angriffs. In Security Center steht eine Reihe vordefinierter [Sicherheitswarnungen](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) zur Verfügung, die ausgelöst werden, wenn eine Bedrohung oder eine verdächtige Aktivität erkannt wird. Mithilfe [benutzerdefinierter Warnungsregeln](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) können Kunden neue Sicherheitswarnungen auf der Grundlage von Daten definieren, die bereits in ihrer Umgebung gesammelt werden.

Security Center bietet priorisierte Sicherheitswarnungen und -vorfälle. Security Center erleichtert Kunden die Erkennung und Behebung potenzieller Sicherheitsprobleme. Für jede erkannte Bedrohung wird ein [Bericht über Bedrohungen](https://docs.microsoft.com/azure/security-center/security-center-threat-report) generiert. Teams zur Reaktion auf Vorfälle können die Berichte verwenden, wenn sie Bedrohungen untersuchen und beheben.

Diese Referenzarchitektur verwendet die Funktion [Sicherheitsrisikobewertung](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) in Azure Security Center. Nach der Konfiguration meldet ein Partner-Agent (z.B. Qualys) Daten zu Sicherheitsrisiken an die Verwaltungsplattform des Partners. Im Gegenzug liefert die Verwaltungsplattform des Partners Überwachungsdaten zu Sicherheitsrisiken und zur Integrität an Security Center. Kunden können anhand dieser Informationen schnell anfällige VMs identifizieren.

**Azure Application Gateway**: Die Architektur verringert die Gefahr von Sicherheitsrisiken durch ein Anwendungsgateway mit einer konfigurierten Web Application Firewall und aktiviertem OWASP-Regelsatz. Weitere Funktionen:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Aktivieren der [SSL-Auslagerung](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal).
- Deaktivieren von [TLS v1.0 und v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Web Application Firewall](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (Schutzmodus).
- [Präventionsmodus](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) mit OWASP 3.0-Regelsatz.
- Aktivieren der [Diagnoseprotokollierung](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
- [Benutzerdefinierte Integritätstests](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal).
- [Azure Security Center](https://azure.microsoft.com/services/security-center) und [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) bieten zusätzlichen Schutz und zusätzliche Benachrichtigungen. Azure Security Center stellt außerdem ein Reputationssystem bereit.

### <a name="business-continuity"></a>Geschäftskontinuität

**Hochverfügbarkeit**: Die Lösung stellt alle VMs in einer [Verfügbarkeitsgruppe](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) bereit. Verfügbarkeitsgruppen stellen sicher, dass die VMs über mehrere isolierte Hardwarecluster verteilt sind, um die Verfügbarkeit zu verbessern. Während einer geplanten oder ungeplanten Wartung ist mindestens eine VM verfügbar, wodurch 99,95 % des SLA von Azure erfüllt werden.

**Recovery Services-Tresor**: Der [Recovery Services-Tresor](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) speichert Sicherungsdaten und schützt alle Konfigurationen von Azure Virtual Machines in dieser Architektur. Durch einen Recovery Services-Tresor können Kunden Dateien und Ordner von einer IaaS-VM wiederherstellen, ohne die gesamte VM wiederherstellen zu müssen. So werden die Wiederherstellungszeiten verkürzt.

**Cloudzeuge**: Ein [Cloudzeuge](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) ist ein Failovercluster-Quorumzeuge in Windows Server 2016, der Azure als Vermittlungspunkt nutzt. Der Cloudzeuge erhält wie jeder andere Quorumzeuge eine Stimme und kann damit an Quorumberechnungen teilnehmen. Er verwendet aber den öffentlich verfügbaren Azure Blob Storage-Standard. Durch diese Anordnung entfällt der zusätzliche Wartungsaufwand für VMs, die in einer öffentlichen Cloud gehostet werden.

### <a name="logging-and-auditing"></a>Protokollierung und Überwachung

Azure-Dienste protokollieren umfassend die System- und Benutzeraktivitäten sowie die Systemintegrität:
- **Aktivitätsprotokolle**: [Aktivitätsprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) bieten Einblicke in Vorgänge, die für Ressourcen in einem Abonnement ausgeführt wurden. Aktivitätsprotokolle können dabei helfen, den Initiator eines Vorgangs, die Zeit des Auftretens und den Status zu bestimmen.
- **Diagnoseprotokolle**: [Diagnoseprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) umfassen sämtliche Protokolle, die von jeder Ressource ausgegeben werden. Zu diesen Protokollen gehören Windows-Ereignissystemprotokolle, Azure Storage-Protokolle, Key Vault-Überwachungsprotokolle sowie Application Gateway-Zugriffs- und -Firewallprotokolle. Alle Diagnoseprotokolle werden für die Archivierung in ein zentrales und verschlüsseltes Azure Storage-Konto geschrieben. Benutzer können den Aufbewahrungszeitraum, bis zu 730 Tage, so konfigurieren, dass die spezifischen Anforderungen erfüllt werden.

**Azure Monitor-Protokolle:** Diese Protokolle werden dann zur Verarbeitung, Speicherung und Dashboardanzeige in [Azure Monitor-Protokolle](https://azure.microsoft.com/services/log-analytics/) konsolidiert. Nach der Erfassung von Daten werden diese in separaten Tabellen für die einzelnen Datentypen in den Log Analytics-Arbeitsbereichen organisiert. Auf diese Weise können alle Daten zusammen analysiert werden, unabhängig von der zugehörigen ursprünglichen Quelle. Security Center lässt sich in Azure Monitor-Protokolle integrieren. Kunden können mithilfe von Kusto-Abfragen auf ihre Sicherheitsereignisdaten zugreifen und diese mit Daten von anderen Diensten kombinieren.

In dieser Architektur sind die folgenden Azure-[Überwachungslösungen](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) enthalten:
-   [Active Directory-Bewertung](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Mit der Lösung für die Active Directory-Integritätsüberprüfung können Sie in regelmäßigen Abständen die Risiken und die Integrität Ihrer Serverumgebungen bewerten. Sie stellt eine priorisierte Liste von Empfehlungen bereit, die spezifisch für Ihre bereitgestellte Serverinfrastruktur gelten.
- [SQL-Bewertung](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Mit der SQL-Lösung für die Integritätsüberprüfung können Sie in regelmäßigen Abständen die Risiken und die Integrität Ihrer Serverumgebungen bewerten. Die Lösung stellt für Kunden eine priorisierte Liste mit Empfehlungen bereit, die spezifisch für die bereitgestellte Serverinfrastruktur gelten.
- [Agent-Integritätsdiagnose](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Diese Lösung für die Agent-Integritätsdiagnose meldet die Anzahl von bereitgestellten Agents sowie deren geografische Verteilung. Zudem meldet sie, wie viele Agents nicht reagieren und wie viele Agents operative Daten übermitteln.
-   [Aktivitätsprotokollanalyse](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Die Lösung „Aktivitätsprotokollanalyse“ hilft bei der Analyse der Azure-Aktivitätsprotokolle in allen Azure-Abonnements eines Kunden.

**Azure Automation**: [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) dient zum Speichern, Ausführen und Verwalten von Runbooks. Bei dieser Lösung werden Protokolle über Runbooks für SQL Server erfasst. Kunden können mithilfe der [Änderungsnachverfolgung](https://docs.microsoft.com/azure/automation/automation-change-tracking) von Azure Automation auf einfache Weise Änderungen in der Umgebung identifizieren.

**Azure Monitor**: [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) dient zum Nachverfolgen der Leistung, zum Gewährleisten der Sicherheit und zum Identifizieren von Trends. Organisationen können diese Plattform zum Überwachen, zum Erstellen von Warnungen und zum Archivieren von Daten verwenden. Zudem können sie die API-Aufrufe in ihren Azure-Ressourcen nachverfolgen.

## <a name="threat-model"></a>Bedrohungsmodell

Das Datenflussdiagramm für diese Referenzarchitektur steht zum [Download](https://aka.ms/nist171-iaaswa-tm) bereit oder kann hier gefunden werden. Dieses Modell kann Kunden dabei helfen, bei der Vornahme von Änderungen die potenziellen Risikopunkte in der Systeminfrastruktur zu verstehen.

![IaaS-Webanwendung gemäß NIST SP 800-171 – Bedrohungsmodell](images/nist171-iaaswa-threat-model.png "IaaS-Webanwendung gemäß NIST SP 800-171 – Bedrohungsmodell")

## <a name="compliance-documentation"></a>Konformitätsdokumentation

Die [Vorlage für Azure Security and Compliance: NIST SP 800-171 – Kundenzuständigkeitsmatrix](https://aka.ms/nist171-crm) listet alle Sicherheitskontrollen auf, die für NIST SP 800-171 erforderlich sind. In dieser Matrix ist angegeben, ob die Implementierung der einzelnen Kontrollen Aufgabe von Microsoft, des Kunden oder beider Seiten ist.

Die [Azure-Blaupause für Sicherheit und Compliance: IaaS-Webanwendung gemäß NIST SP 800-171 – Regulierungsimplementierungsmatrix](https://aka.ms/nist171-iaaswa-cim) enthält Informationen dazu, auf welche NIST SP 800-171-Kontrollen durch die Architektur der IaaS-Webanwendung verwiesen wird. Sie enthält zudem eine ausführliche Beschreibung, wie die Implementierung die Anforderungen der einzelnen Kontrollen erfüllt.

## <a name="guidance-and-recommendations"></a>Anleitungen und Empfehlungen
### <a name="vpn-and-expressroute"></a>VPN und ExpressRoute
Für sichere Verbindungen mit Ressourcen, die als Teil dieser Referenzarchitektur einer IaaS-Webanwendung bereitgestellt werden, muss ein sicherer VPN-Tunnel oder eine [ExpressRoute-Verbindung](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) konfiguriert werden. Durch die korrekte Einrichtung von VPN oder ExpressRoute können Kunden eine zusätzliche Sicherheitsebene für Daten während der Übertragung hinzufügen.

Mit der Implementierung eines sicheren VPN-Tunnels mit Azure kann eine virtuelle private Verbindung zwischen einem lokalen Netzwerk und einem virtuellen Azure-Netzwerk hergestellt werden. Diese Verbindung wird über das Internet hergestellt. Sie ermöglicht Kunden das sichere Übertragen von Informationen in einem „Tunnel“ innerhalb eines verschlüsselten Links zwischen dem Kundennetzwerk und Azure. Eine Site-to-Site-VPN-Verbindung ist eine sichere, ausgereifte Technologie, die seit Jahrzehnten von Unternehmen aller Größen eingesetzt wird. Der [IPsec-Tunnelmodus](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) wird bei dieser Option als Verschlüsselungsmechanismus verwendet.

Da Datenverkehr im VPN-Tunnel über ein Site-to-Site-VPN über das Internet übertragen wird, bietet Microsoft eine weitere Verbindungsoption an, die noch sicherer ist. ExpressRoute ist ein dedizierter WAN-Link zwischen Azure und einem lokalen Standort oder einem Exchange-Hostinganbieter. ExpressRoute-Verbindungen stellen eine direkte Verbindung mit dem Telekommunikationsanbieter des Kunden her. Daher werden die Daten nicht über das Internet übertragen und im Internet verfügbar gemacht. Diese Verbindungen bieten mehr Zuverlässigkeit, eine höhere Geschwindigkeit, niedrigere Latenzzeiten und mehr Sicherheit als herkömmliche Verbindungen. 

Bewährte Methoden für das Implementieren eines sicheren hybriden Netzwerks, mit dem ein lokales Netzwerk auf Azure erweitert wird, finden Sie [hier](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Haftungsausschluss

- Dieses Dokument dient nur zu Informationszwecken. MICROSOFT ÜBERNIMMT KEINE AUSDRÜCKLICHEN, STILLSCHWEIGENDEN ODER GESETZLICH VORGEGEBENEN GARANTIEN HINSICHTLICH DER INFORMATIONEN IN DIESEM DOKUMENT. Dieses Dokument wird so bereitgestellt, wie es ist. Informationen und Stellungnahmen in diesem Dokument, einschließlich URLs und anderer Verweise auf Internetwebsites, können ohne vorherige Ankündigung geändert werden. Kunden, die dieses Dokument lesen, tragen das Risiko, es zu verwenden. 
- Dieses Dokument gibt Kunden keine Rechte an geistigem Eigentum an Microsoft-Produkten oder -Lösungen. 
- Kunden dürfen dieses Dokument zu internen Referenzzwecken kopieren und verwenden. 
- Bestimmte Empfehlungen in diesem Dokument können zu einer erhöhten Nutzung von Daten, Netzwerken oder Computeressourcen in Azure sowie zu höheren Azure-Lizenz- oder -Abonnementkosten für den Kunden führen. 
- Diese Architektur soll Kunden als Grundlage für die Anpassung an ihre spezifischen Anforderungen dienen. Sie sollte nicht unverändert in einer Produktionsumgebung verwendet werden.
- Dieses Dokument wurde als Referenz entwickelt und sollte nicht dazu verwendet werden, alle Mittel zu definieren, mit denen ein Kunde bestimmte Konformitätsanforderungen und -vorschriften erfüllen kann. Kunden sollten bei genehmigten Kundenimplementierungen rechtliche Unterstützung von ihrer Organisation einholen.
