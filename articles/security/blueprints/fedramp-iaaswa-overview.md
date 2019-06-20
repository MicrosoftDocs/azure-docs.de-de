---
title: Blaupause für Azure Security and Compliance – IaaS-Webanwendung für FedRAMP
description: Blaupause für Azure Security and Compliance – IaaS-Webanwendung für FedRAMP
services: security
documentationcenter: na
author: jomolesk
manager: barbkess
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2018
ms.author: jomolesk
ms.openlocfilehash: 1ba5b813843ce2f5d31f337ab4d3d94e521b0e0c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60586133"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-fedramp"></a>Azure-Blaupause für Sicherheit und Compliance: IaaS-Webanwendung für FedRAMP

## <a name="overview"></a>Übersicht

Das [Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov) ist ein Programm der US-Regierung, mit dem ein standardisierter Ansatz zur Sicherheitsbewertung, Autorisierung und kontinuierlichen Überwachung von Cloudprodukten und -diensten bereitgestellt wird. Diese Automatisierung per Vorlage für Azure Security and Compliance umfasst Anleitungen für den Einsatz einer mit FedRAMP konformen IaaS-Umgebung (Infrastructure-as-a-Service), die für eine einfache, internetfähige Webanwendung geeignet ist. Diese Lösung automatisiert die Bereitstellung und Konfiguration von Azure-Ressourcen für eine allgemeine Referenzarchitektur. Sie zeigt Wege auf, wie Kunden spezifische Sicherheits- und Konformitätsanforderungen erfüllen können und dient als Grundlage für Kunden, um ihre eigenen Lösungen in Azure zu entwickeln und zu konfigurieren. Die Lösung implementiert eine Teilmenge der Regulierungen aus der FedRAMP High-Grundlage (basierend auf NIST SP 800-53). Weitere Informationen zu den Anforderungen an das Federal Risk and Authorization Management Program und zu dieser Lösung finden Sie unter [Dokumentation zur Konformität](#compliance-documentation).
> [!NOTE]
> Diese Lösung wird für Azure Government bereitgestellt.

Diese Azure Security and Compliance Blueprint-Automatisierung stellt automatisch eine Referenzarchitektur für IaaS-Webanwendungen mit vorkonfigurierten Sicherheitsregulierungen bereit, um Kunden bei der Einhaltung der FedRAMP-Anforderungen zu unterstützen. Die Lösung besteht aus Azure Resource Manager-Vorlagen und PowerShell-Skripts, die die Ressourcenbereitstellung und -konfiguration steuern.

Diese Architektur soll Kunden als Grundlage für die Anpassung an ihre spezifischen Anforderungen dienen. Sie sollte nicht unverändert in einer Produktionsumgebung verwendet werden. Es reicht nicht aus, eine Anwendung ohne Änderungen in dieser Umgebung bereitzustellen, um die Anforderungen der FedRAMP High-Grundlage vollständig zu erfüllen. Beachten Sie Folgendes:
- Diese Architektur bietet eine Grundlage, um Kunden bei der Verwendung von Azure in FedRAMP-konformer Weise zu unterstützen.
- Der Kunde ist für die Durchführung einer angemessenen Sicherheits- und Konformitätsbewertung jeder Lösung verantwortlich, die mit dieser Architektur erstellt wurde, da die Anforderungen je nach den Besonderheiten der jeweiligen Implementierung des Kunden unterschiedlich sein können.

Eine schnelle Übersicht über die Funktionsweise dieser Lösung finden Sie in diesem [Video](https://aka.ms/fedrampblueprintvideo), in dem die Bereitstellung erklärt und gezeigt wird.

Klicken Sie [hier](https://aka.ms/fedrampblueprintrepo), um Anleitungen zur Bereitstellung zu erhalten.

## <a name="architecture-diagram-and-components"></a>Architekturdiagramm und Komponenten
Diese Lösung stellt eine Referenzarchitektur für eine IaaS-Webanwendung mit einem SQL Server-Back-End bereit. Die Architektur umfasst eine Webschicht, eine Datenschicht, eine Active Directory-Infrastruktur, ein Application Gateway und ein Lastenausgleichsmodul. Virtuelle Computer, die in den Web- und Datenschichten bereitgestellt werden, werden in einer Verfügbarkeitsgruppe konfiguriert, und SQL Server-Instanzen werden in einer Always On-Verfügbarkeitsgruppe für Hochverfügbarkeit konfiguriert. Virtuelle Computer sind in Domänen eingebunden, und Active Directory-Gruppenrichtlinien werden verwendet, um Sicherheits- und Konformitätskonfigurationen auf Betriebssystemebene durchzusetzen. Ein Bastionhost bietet Administratoren eine sichere Verbindung für den Zugriff auf bereitgestellte Ressourcen. **Azure empfiehlt das Konfigurieren einer VPN- oder Azure ExpressRoute-Verbindung für die Verwaltung und den Datenimport in das Subnetz der Referenzarchitektur.**

![IaaS-Webanwendung für FedRAMP – Diagramm zur Referenzarchitektur](images/fedramp-iaaswa-architecture.png?raw=true "IaaS-Webanwendung für FedRAMP – Diagramm zur Referenzarchitektur")

Diese Lösung verwendet die folgenden Azure-Dienste. Details zur Bereitstellungsarchitektur finden Sie im Abschnitt [Bereitstellungsarchitektur](#deployment-architecture).

- Azure Virtual Machines
    - (1) Bastionhost (Windows Server 2016 Datacenter)
    - (2) Active Directory-Domänencontroller (Windows Server 2016 Datacenter)
    - (2) SQL Server-Clusterknoten (SQL Server 2017 unter Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Datacenter)
- Verfügbarkeitsgruppen
    - (1) Active Directory-Domänencontroller
    - (1) SQL-Clusterknoten
    - (1) Web/IIS
- Virtuelles Azure-Netzwerk
    - (1) /16 virtuelle Netzwerke
    - (5) /24 Subnetze
    - DNS-Einstellungen werden auf beiden Domänencontrollern festgelegt.
- Azure Load Balancer
- Azure Application Gateway
    - (1) WAF Application Gateway (aktiviert)
        - Firewallmodus: Prävention
        - Regelsatz: OWASP 3.0
        - Listener: Port 443
- Azure Storage
    - (7) Georedundante Speicherkonten
- Azure-Cloudzeuge
- Recovery Services-Tresor
- Azure Key Vault
- Azure Active Directory (Azure AD)
- Azure Resource Manager
- Azure Monitor (Protokolle)

## <a name="deployment-architecture"></a>Bereitstellungsarchitektur

Im folgenden Abschnitt werden die Elemente für Entwicklung und Implementierung beschrieben.

**Bastionhost**: Der Bastionhost dient als alleiniger Zugangspunkt, der eine sichere Verbindung bereitstellt, über die Administratoren auf bereitgestellte Ressourcen zugreifen können. Die Netzwerksicherheitsgruppe des Bastionhosts lässt nur über TCP-Port 3389 für RDP Verbindungen zu. Kunden können den Bastionhost weiter konfigurieren, um Anforderungen von Organisationen zu erfüllen, die für die Erhöhung des Schutzes gelten.

### <a name="virtual-network"></a>Virtuelles Netzwerk
Die Architektur definiert ein privates virtuelles Netzwerk mit dem Adressraum 10.200.0.0/16.

**Netzwerksicherheitsgruppen**: Diese Lösung stellt Ressourcen in einer Architektur mit einem separaten Websubnetz, Datenbanksubnetz, Active Directory-Subnetz und Managementsubnetz in einem virtuellen Netzwerk bereit. Subnetze werden logisch durch Regeln für Netzwerksicherheitsgruppen getrennt, die auf die einzelnen Subnetze angewendet werden, um den Datenverkehr zwischen den Subnetzen auf das für die System- und Verwaltungsfunktionen erforderliche Maß einzuschränken.

Details finden Sie in der Konfiguration für [Netzwerksicherheitsgruppen](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json), die mit dieser Lösung bereitgestellt wird. Kunden können Netzwerksicherheitsgruppen konfigurieren, indem sie die Datei oben bearbeiten und dabei [diese Dokumentation](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) als Leitfaden verwenden.

Jedes der Subnetze verfügt über eine dedizierte Netzwerksicherheitsgruppe (NSG):
- 1 NSG für Application Gateway (LBNSG)
- 1 NSG für Bastionhost (MGTNSG)
- 1 NSG für primären und Sicherungsdomänencontroller (ADNSG)
- 1 NSG für SQL Server-Instanzen (SQLNSG)
- 1 NSG für Webschicht (WEBNSG)

**Subnetze**: Jedem Subnetz ist die entsprechende NSG zugeordnet.

### <a name="data-at-rest"></a>Ruhende Daten

Die Architektur schützt ruhende Daten mithilfe mehrerer Verschlüsselungsmaßnahmen.

**Azure Storage**: Um den Verschlüsselungsanforderungen von ruhenden Daten gerecht zu werden, verwenden alle Speicherkonten [Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

**SQL Server:** SQL Server ist für die Verwendung der [TDE-Technologie (Transparent Data Encryption)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) konfiguriert, die Echtzeitverschlüsselung und -entschlüsselung von Daten und Protokolldateien ausführt, um ruhende Informationen zu schützen. TDE gewährleistet, dass gespeicherte Daten keinen unbefugten Zugriffen ausgesetzt sind.

Kunden können auch die folgenden SQL Server-Sicherheitsmaßnahmen konfigurieren:
-   Die [AD-Authentifizierung und -Autorisierung](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) ermöglicht die zentrale Verwaltung der Identitäten von Datenbankbenutzern und anderen Microsoft-Diensten.
-   Die [SQL-Datenbank-Überprüfung](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) verfolgt Datenbankereignisse nach und schreibt diese in ein Überwachungsprotokoll in einem Azure Storage-Konto.
-   [Firewallregeln](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) verhindern sämtlichen Zugriff auf Datenbankserver, bis die richtigen Berechtigungen erteilt werden. Die Firewall gewährt den Datenbankzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung.
-   Die [SQL-Bedrohungserkennung](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) ermöglicht die Erkennung und Reaktion auf potenzielle Bedrohungen, sobald sie auftreten. Dazu werden Sicherheitswarnungen bei verdächtigen Datenbankaktivitäten, potenziellen Sicherheitslücken, Angriffen durch Einschleusung von SQL-Befehlen und anormalen Mustern beim Datenbankzugriff ausgegeben.
-   [Always Encrypted-Spalten](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) stellen sicher, dass vertrauliche Daten im Datenbanksystem niemals im Klartextformat angezeigt werden. Nach dem Aktivieren der Datenverschlüsselung können nur Clientanwendungen oder App-Server, die Zugriff auf die Schlüssel haben, auf Klartextdaten zugreifen.
-   Die [dynamische Datenmaskierung in SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) kann durchgeführt werden, nachdem die Referenzarchitektur bereitgestellt wurde. Kunden müssen die Einstellungen für die dynamische Datenmaskierung an ihr Datenbankschema anpassen.

**Azure Disk Encryption**: Azure Disk Encryption wird zur Verschlüsselung der Datenträger von virtuellen Windows-IaaS-Computern verwendet. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) nutzt die BitLocker-Funktion von Windows, um Volumeverschlüsselung für Betriebssysteme und Datenträger bereitzustellen. Die Lösung ist in Azure Key Vault integriert, um die Steuerung und Verwaltung der Datenträger-Verschlüsselungsschlüssel zu erleichtern.

### <a name="identity-management"></a>Identitätsverwaltung

Die folgenden Technologien bieten Identitätsverwaltungsfunktionen in der Azure-Umgebung:
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) ist der mehrinstanzenfähige cloudbasierte Verzeichnis- und Identitätsverwaltungsdienst von Microsoft.
- Die Authentifizierung mit einer vom Kunden bereitgestellten Webanwendung kann mit Azure AD ausgeführt werden. Weitere Informationen finden Sie unter [Integrieren von Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  
- Die [rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) in Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) ermöglicht eine präzise Zugriffsverwaltung für Azure. Der Abonnementzugriff ist auf den Abonnementadministrator beschränkt, und der Zugriff auf Ressourcen kann je nach Benutzerrolle eingeschränkt werden.
- Eine bereitgestellte IaaS-Active-Directory-Instanz bietet Identitätsverwaltung auf Betriebssystemebene für bereitgestellte virtuelle IaaS-Computer.

### <a name="security"></a>Sicherheit
**Geheimnisverwaltung**: Die Lösung verwendet [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) für die Verwaltung von Schlüsseln und Geheimnissen. Azure Key Vault unterstützt Sie dabei, kryptografische Schlüssel und Geheimnisse zu schützen, die von Cloudanwendungen und -diensten verwendet werden. Azure Key Vault unterstützt die Verwaltung von Datenträger-Verschlüsselungsschlüsseln und -geheimnissen virtueller IaaS-Computer für diese Referenzarchitektur.

**Patchverwaltung**: Virtuelle Windows-Computer, die durch diese Azure Security and Compliance Blueprint-Automatisierung bereitgestellt werden, sind standardmäßig so konfiguriert, dass sie automatische Updates von Windows Update Service erhalten. Diese Lösung stellt auch die Azure Automation-Lösung bereit, mit der Updatebereitstellungen erstellt werden können, um bei Bedarf Patches auf Windows-Servern bereitzustellen.

**Schutz vor Schadsoftware**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) für Virtual Machines bietet Echtzeitschutzfunktionen, mit denen die Identifizierung und Entferung von Viren, Spyware und anderer bösartiger Software ermöglicht wird, mit konfigurierbaren Warnmeldungen, wenn bekannte bösartige oder unerwünschte Software versucht, sich auf geschützten virtuellen Computern zu installieren oder die Ausführung zu starten.

**Application Gateway**: Die Architektur verringert das Risiko von Sicherheitsschwachstellen durch ein Application Gateway mit Web Application Firewall (WAF) und dem aktivierten OWASP-Regelsatz. Weitere Funktionen:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Aktivierte [SSL-Auslagerung](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Deaktivieren von [TLS v1.0 und v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Web Application Firewall](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (WAF-Modus)
- [Präventionsmodus](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) mit OWASP 3.0-Regelsatz

### <a name="business-continuity"></a>Geschäftskontinuität

**Hochverfügbarkeit**: Während einer geplanten oder ungeplanten Wartung ist mindestens ein virtueller Computer verfügbar, um die Azure-SLA von 99,95 % zu erfüllen. Die Lösung stellt alle virtuellen Computer der Web- und Datenschicht in einer [Verfügbarkeitsgruppe](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) bereit. Verfügbarkeitsgruppen stellen sicher, dass die virtuellen Computer über mehrere isolierte Hardwarecluster verteilt sind, um die Verfügbarkeit zu verbessern. Außerdem stellt diese Lösung virtuelle SQL Server-Computer in einer Verfügbarkeitsgruppe als [AlwaysOn-Verfügbarkeitsgruppe](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview) bereit. Das Feature „AlwaysOn-Verfügbarkeitsgruppe“ ermöglicht für Funktionen für hohe Verfügbarkeit und Notfallwiederherstellung.

**Recovery Services-Tresor**: Der [Recovery Services-Tresor](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) speichert Sicherungsdaten und schützt alle Konfigurationen von Azure Virtual Machines in dieser Architektur. Durch einen Recovery Services-Tresor können Kunden Dateien und Ordner von einer IaaS-VM wiederherstellen, ohne die gesamte VM wiederherstellen zu müssen. So werden die Wiederherstellungszeiten verkürzt.

**Cloudzeuge**: Ein [Cloudzeuge](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) ist ein Failovercluster-Quorumzeuge in Windows Server 2016, der Azure als Vermittlungspunkt nutzt. Der Cloudzeuge erhält wie jeder andere Quorumzeuge eine Stimme und kann damit an Quorumberechnungen teilnehmen. Er verwendet aber den öffentlich verfügbaren Azure Blob Storage-Standard. Damit entfällt der zusätzliche Wartungsaufwand für virtuelle Computer, die in einer öffentlichen Cloud gehostet werden.

### <a name="logging-and-auditing"></a>Protokollierung und Überwachung

Azure Monitor-Protokolle ermöglicht eine umfassende Protokollierung von System- und Benutzeraktivitäten sowie der Systemintegrität. [Azure Monitor-Protokolle](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) ist eine Lösung für die Erfassung und Analyse von Daten, die von Ressourcen in Azure-basierten und lokalen Umgebungen generiert werden.

- **Aktivitätsprotokolle**:  [Aktivitätsprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) bieten Einblicke in Vorgänge, die für Ressourcen in einem Abonnement ausgeführt wurden. Aktivitätsprotokolle können dabei helfen, den Initiator eines Vorgangs, die Zeit des Auftretens und den Status zu bestimmen.
- **Diagnoseprotokolle:**  [Diagnoseprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) sind alle Protokolle, die von jeder Ressource ausgegeben werden. Zu diesen Protokollen gehören Windows-Ereignissystemprotokolle, Azure-Speicherprotokolle, Key Vault-Überwachungsprotokolle sowie Application Gateway-Zugriffs- und -Firewallprotokolle.
- **Protokollarchivierung**:  Alle Diagnoseprotokolle werden für die Archivierung in ein zentrales und verschlüsseltes Azure Storage-Konto geschrieben. Die Aufbewahrung kann vom Benutzer konfiguriert werden (bis zu 730 Tage), um den unternehmensspezifischen Aufbewahrungsanforderungen gerecht zu werden. Diese Protokolle werden dann zur Verarbeitung, Speicherung und Dashboardanzeige mit Azure Monitor-Protokolle verbunden.

Darüber hinaus sind in dieser Architektur die folgenden Überwachungslösungen installiert. Beachten Sie, dass der Kunde für die Konfiguration dieser Lösungen verantwortlich ist, um die Anpassung an die FedRAMP-Sicherheitskontrollen vorzunehmen:
-   [Active Directory-Bewertung](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Die Lösung „Active Directory-Integritätsüberprüfung“ bewertet die Risiken und die Integrität von Serverumgebungen in regelmäßigen Abständen und erstellt eine priorisierte Liste mit spezifischen Empfehlungen für die bereitgestellte Serverinfrastruktur.
-   [Antischadsoftwarebewertung](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): Die Antischadsoftwarelösung meldet Schadsoftware, Bedrohungen und den Schutzstatus.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Die Azure Automation-Lösung dient der Speicherung, Ausführung und Verwaltung von Runbooks.
-   [Sicherheit und Überwachung](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): Das Sicherheits- und Überwachungsdashboard ermöglicht einen allgemeinen Einblick in den Sicherheitszustand von Ressourcen. Hierzu werden Metriken zu Sicherheitsdomänen, relevanten Problemen, Erkennungen, Bedrohungsdaten und allgemeinen Sicherheitsabfragen bereitgestellt.
-   [SQL-Bewertung](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Die Lösung „SQL-Integritätsüberprüfung“ bewertet die Risiken und die Integrität von Serverumgebungen in regelmäßigen Abständen und erstellt für die Kunden eine priorisierte Liste mit speziellen Empfehlungen für die bereitgestellte Serverinfrastruktur.
-   [Updateverwaltung](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): Die Updateverwaltungslösung ermöglicht die Verwaltung von Sicherheitsupdates des Betriebssystems durch die Kunden. Dies umfasst auch den Status der verfügbaren Updates und den Fortschritt beim Installieren der erforderlichen Updates.
-   [Agent-Integritätsdiagnose](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Die Lösung „Agent-Integritätsdiagnose“ meldet die Anzahl bereitgestellter Agents sowie deren geografische Verteilung. Außerdem meldet sie, wie viele Agents nicht mehr reagieren und wie viele Agents Betriebsdaten übermitteln.
-   [Azure-Aktivitätsprotokolle](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Die Lösung „Aktivitätsprotokollanalyse“ hilft bei der Analyse der Azure-Aktivitätsprotokolle in allen Azure-Abonnements eines Kunden.
-   [Änderungsnachverfolgung](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Die Lösung „Änderungsnachverfolgung“ ermöglicht es Kunden, problemlos Änderungen in der Umgebung zu erkennen.

**Azure Monitor**
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) unterstützt Benutzer beim Nachverfolgen der Leistung, Aufrechterhalten der Sicherheit und Identifizieren von Trends, indem Organisationen das Überwachen, Erstellen von Warnungen und Archivieren von Daten ermöglicht wird, z.B. die Nachverfolgung von API-Aufrufen in den Azure-Ressourcen von Kunden.

## <a name="threat-model"></a>Bedrohungsmodell
Das Datenflussdiagramm für diese Referenzarchitektur steht zum [Download](https://aka.ms/fedrampWAdfd) bereit und ist unten angegeben. Dieses Modell kann Kunden dabei helfen, bei Änderungen die potenziellen Risikopunkte in der Systeminfrastruktur zu verstehen.

![IaaS-Webanwendung für das FedRAMP-Bedrohungsmodell](images/fedramp-iaaswa-threat-model.png?raw=true "IaaS-Webanwendung für das FedRAMP-Bedrohungsmodell")

## <a name="compliance-documentation"></a>Konformitätsdokumentation

Die [Vorlage für Azure Security and Compliance – FedRAMP High-Kundenzuständigkeitsmatrix](https://aka.ms/blueprinthighcrm) listet alle Sicherheitskontrollen auf, die von der FedRAMP High-Baseline verlangt werden. In der Matrix ist angegeben, ob die Implementierung der einzelnen Steuerelemente Aufgabe von Microsoft, des Kunden oder beider Seiten ist.

In der [Vorlage für Azure Security and Compliance – FedRAMP High für IaaS-Webanwendung – Regulierungsimplementierungsmatrix](https://aka.ms/blueprintwacim) sind alle Sicherheitskontrollen aufgelistet, die von der FedRAMP High-Baseline verlangt werden. Die Matrix enthält Informationen dazu, welche Kontrollen durch die IaaS-Webanwendungsarchitektur abgedeckt sind, z.B. ausführliche Beschreibungen, wie die Implementierung die Anforderungen der einzelnen Kontrollen erfüllt.

## <a name="deploy-the-solution"></a>Bereitstellen der Lösung

Diese Azure Security and Compliance Blueprint-Automatisierung besteht aus JSON-Konfigurationsdateien und PowerShell-Skripts, die vom API-Dienst von Azure Resource Manager verwaltet werden, um Ressourcen innerhalb von Azure bereitzustellen. Ausführlichere Bereitstellungsanweisungen finden Sie [hier](https://aka.ms/fedrampblueprintrepo).
> [!NOTE]
> Diese Lösung wird für Azure Government bereitgestellt.

#### <a name="quickstart"></a>Schnellstart
1. Klonen oder laden Sie [dieses](https://aka.ms/fedrampblueprintrepo) GitHub-Repository auf die lokale Arbeitsstation herunter.

2. Führen Sie das PowerShell-Skript vor der Bereitstellung aus: azure-blueprint/predeploy/Orchestration_InitialSetup.ps1.

3. Klicken Sie auf die Schaltfläche unten, melden Sie sich im Azure-Portal an, geben Sie die erforderlichen ARM-Vorlagenparameter ein, und klicken Sie dann auf **Kaufen**.

    [![In Azure bereitstellen](https://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="guidance-and-recommendations"></a>Anleitungen und Empfehlungen
### <a name="vpn-and-expressroute"></a>VPN und ExpressRoute
Für sichere Verbindungen mit Ressourcen, die als Teil dieser Referenzarchitektur einer IaaS-Webanwendung bereitgestellt werden, muss ein sicherer VPN-Tunnel oder eine [ExpressRoute-Verbindung](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) konfiguriert werden. Durch die ordnungsgemäße Einrichtung von VPN oder ExpressRoute können Kunden eine zusätzliche Sicherheitsebene für Daten während der Übertragung hinzufügen.

Mit der Implementierung eines sicheren VPN-Tunnels mit Azure kann eine virtuelle private Verbindung zwischen einem lokalen Netzwerk und einem virtuellen Azure-Netzwerk hergestellt werden. Diese Verbindung wird über das Internet hergestellt und ermöglicht Kunden das Übertragen von Informationen in einem „Tunnel“ innerhalb eines verschlüsselten Links zwischen dem Kundennetzwerk und Azure. Eine Site-to-Site-VPN-Verbindung ist eine sichere, ausgereifte Technologie, die seit Jahrzehnten von Unternehmen aller Größen eingesetzt wird. Der [IPsec-Tunnelmodus](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) wird bei dieser Option als Verschlüsselungsmechanismus verwendet.

Da Datenverkehr im VPN-Tunnel über ein Site-to-Site-VPN über das Internet übertragen wird, bietet Microsoft eine weitere Verbindungsoption an, die noch sicherer ist. Azure ExpressRoute ist ein dedizierter WAN-Link zwischen Azure und einem lokalen Standort oder einem Exchange-Hostinganbieter. Da ExpressRoute-Verbindungen nicht über das Internet verlaufen, bieten sie eine höhere Sicherheit, größere Zuverlässigkeit und schnellere Geschwindigkeit bei geringerer Latenz als herkömmliche Verbindungen über das Internet. Da es sich außerdem um eine direkte Verbindung vom Telekommunikationsanbieter des Kunden handelt, werden die Daten nicht über das Internet übertragen und dort nicht verfügbar gemacht.

Bewährte Methoden für das Implementieren eines sicheren hybriden Netzwerks, mit dem ein lokales Netzwerk auf Azure erweitert wird, finden Sie [hier](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Haftungsausschluss

- Dieses Dokument dient nur zu Informationszwecken. MICROSOFT ÜBERNIMMT KEINE AUSDRÜCKLICHEN, STILLSCHWEIGENDEN ODER GESETZLICH VORGEGEBENEN GARANTIEN HINSICHTLICH DER INFORMATIONEN IN DIESEM DOKUMENT. Dieses Dokument wird so bereitgestellt, wie es ist. Informationen und Stellungnahmen in diesem Dokument, einschließlich URLs und anderer Verweise auf Internetwebsites, können ohne vorherige Ankündigung geändert werden. Kunden, die dieses Dokument lesen, tragen das Risiko, es zu verwenden.  
- Dieses Dokument gibt Kunden keine Rechte an geistigem Eigentum an Microsoft-Produkten oder -Lösungen.  
- Kunden dürfen dieses Dokument zu internen Referenzzwecken kopieren und verwenden.  
- Bestimmte Empfehlungen in diesem Dokument können zu einer erhöhten Nutzung von Daten, Netzwerken oder Computeressourcen in Azure sowie zu höheren Azure-Lizenz- oder -Abonnementkosten für den Kunden führen.  
- Diese Architektur soll Kunden als Grundlage für die Anpassung an ihre spezifischen Anforderungen dienen. Sie sollte nicht unverändert in einer Produktionsumgebung verwendet werden.
- Dieses Dokument wurde als Referenz entwickelt und sollte nicht dazu verwendet werden, alle Mittel zu definieren, mit denen ein Kunde bestimmte Konformitätsanforderungen und -vorschriften erfüllen kann. Kunden sollten bei genehmigten Kundenimplementierungen rechtliche Unterstützung von ihrer Organisation einholen.
