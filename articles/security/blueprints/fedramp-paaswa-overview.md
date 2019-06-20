---
title: Azure Blueprint Automation – PaaS-Webanwendung für FedRAMP
description: Azure Blueprint Automation – PaaS-Webanwendung für FedRAMP
services: security
author: jomolesk
ms.assetid: 41570fc1-4d74-48ed-afb0-ef1be857029e
ms.service: security
ms.topic: article
ms.date: 06/01/2018
ms.author: jomolesk
ms.openlocfilehash: 46c72191ee17f63311b041d798cccec279e4b000
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60585980"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-fedramp"></a>Azure-Blaupause für Sicherheit und Compliance: PaaS-Webanwendung für FedRAMP

## <a name="overview"></a>Übersicht

Das [Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) ist ein Programm der US-Regierung, das einen standardisierten Ansatz zur Sicherheitsbewertung, Autorisierung und kontinuierlichen Überwachung von Cloudprodukten und -diensten bietet. Diese Blaupause von Azure Blueprint Automation enthält Anleitungen zum Bereitstellen einer „Platform as a Service“-Architektur (PaaS) für Microsoft Azure, über die Sie eine Teilmenge der FedRAMP High-Steuerelemente implementieren können. Diese Lösung bietet Anleitungen für die Bereitstellung und Konfiguration von Azure-Ressourcen für eine allgemeine Referenzarchitektur, wobei Möglichkeiten veranschaulicht werden, in denen Kunden bestimmte Sicherheits- und Konformitätsanforderungen erfüllen können, und dient Kunden als Grundlage für das Erstellen und Konfigurieren ihrer eigenen Lösungen in Azure.

Diese Referenzarchitektur, die zugehörige Anleitung für die Implementierung von Steuerelementen und die Bedrohungsmodelle sollen Kunden als Grundlage für die Anpassung an ihre spezifischen Anforderungen dienen. Sie sollten nicht unverändert in einer Produktionsumgebung verwendet werden. Es reicht nicht aus, eine Anwendung ohne Änderungen in dieser Umgebung bereitzustellen, um die Anforderungen der FedRAMP High-Baseline vollständig zu erfüllen. Beachten Sie Folgendes:
- Diese Architektur bietet eine Baseline, um Kunden bei der Bereitstellung von Workloads in Azure in FedRAMP-konformer Weise zu unterstützen.
- Der Kunde ist für die Durchführung einer angemessenen Sicherheits- und Konformitätsbewertung jeder Lösung verantwortlich, die mit dieser Architektur erstellt wurde, da die Anforderungen je nach den Besonderheiten der jeweiligen Implementierung des Kunden unterschiedlich sein können.

## <a name="architecture-diagram-and-components"></a>Architekturdiagramm und Komponenten
Diese Lösung stellt eine Referenzarchitektur für eine PaaS-Webanwendung mit einem Azure SQL-Datenbank-Back-End bereit. Die Webanwendung wird in einer isolierten Azure App Service-Umgebung gehostet. Hierbei handelt es sich um eine private, dedizierte Umgebung in einem Azure-Datencenter. In der Umgebung wird ein Lastenausgleich für den Datenverkehr der Webanwendung für die VMs durchgeführt, die von Azure verwaltet werden. Diese Architektur umfasst auch Netzwerksicherheitsgruppen, ein Application Gateway, Azure DNS und einen Load Balancer. Darüber hinaus bietet Azure Monitor Analysen der Systemintegrität in Echtzeit. **In Azure werden das Konfigurieren einer VPN- oder ExpressRoute-Verbindung für die Verwaltung und der Datenimport in das Subnetz der Referenzarchitektur empfohlen.**

![PaaS-Webanwendung für FedRAMP – Diagramm zur Referenzarchitektur](images/fedramp-paaswa-architecture.png?raw=true "PaaS-Webanwendung für FedRAMP – Diagramm zur Referenzarchitektur")

Diese Lösung verwendet die folgenden Azure-Dienste. Details zur Bereitstellungsarchitektur finden Sie im Abschnitt [Bereitstellungsarchitektur](#deployment-architecture).

- Azure Active Directory
- Azure Key Vault
- Azure SQL-Datenbank
- Application Gateway
    - (1) Web Application Firewall
        - Firewallmodus: Prävention
        - Regelsatz: OWASP 3.0
        - Listener: Port 443
- Azure Virtual Network
- Netzwerksicherheitsgruppen
- Azure DNS
- Azure Storage
- Azure Monitor
- App Service-Umgebung v2
- Azure Load Balancer
- Azure-Web-App
- Azure Resource Manager

## <a name="deployment-architecture"></a>Bereitstellungsarchitektur
Im folgenden Abschnitt werden die Elemente für Entwicklung und Implementierung beschrieben.

**Azure Resource Manager**: Mit [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) können Kunden mit den Ressourcen der Lösung als Gruppe arbeiten. Kunden können alle Ressourcen für die Lösung in einem einzigen koordinierten Vorgang bereitstellen, aktualisieren oder löschen. Kunden verwenden eine Vorlage für die Bereitstellung, die für unterschiedliche Umgebungen geeignet sein kann, z.B. Testing, Staging und Produktion. Resource Manager umfasst Sicherheits-, Überwachungs- und Kennzeichnungsfunktionen, mit denen Kunden ihre Ressourcen nach der Bereitstellung verwalten können.

**App Service-Umgebung v2**: Die [Azure App Service-Umgebung](https://docs.microsoft.com/azure/app-service/environment/intro) (Azure App Service Environment, ASE) ist ein App Service-Feature, das eine vollständig isolierte und dedizierte Umgebung zur sicheren Ausführung von App Service-Anwendungen in großem Umfang bereitstellt.

Aufgrund der Isolierung werden in ASUs nur Anwendungen eines einzelnen Kunden ausgeführt. Die Umgebungen werden zudem immer in einem virtuellen Netzwerk bereitgestellt. Kunden haben eine genauere Kontrolle über den eingehenden und ausgehenden Anwendungs-Netzwerkdatenverkehr, und Anwendungen können über virtuelle Netzwerke sichere Hochgeschwindigkeitsverbindungen mit lokalen Unternehmensressourcen herstellen.

Die Verwendung von ASUs für diese Architektur ist für die folgenden Steuerelemente/Konfigurationen zulässig:

- Hosten in einem geschützten virtuellen Azure-Netzwerk und in Netzwerksicherheitsregeln
- ASE, konfiguriert mit einem selbstsignierten ILB-Zertifikat für die HTTPS-Kommunikation
- [Interner Lastenausgleichsmodus](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer)
- Deaktivieren von [TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Ändern von [TLS-Verschlüsselung](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Steuern der [N/W-Port für eingehenden Datenverkehr](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Web Application Firewall – Daten einschränken](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Zulassen von [Azure SQL-Datenbank-Datenverkehr](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

Der Abschnitt [Anleitungen und Empfehlungen](#guidance-and-recommendations) enthält zusätzliche Informationen zu ASEs.

**Azure-Web-App**: [Azure App Service](https://docs.microsoft.com/azure/app-service/) ermöglicht Kunden das Erstellen und Hosten von Webanwendungen in der Programmiersprache ihrer Wahl, ohne eine Infrastruktur verwalten zu müssen. Der Dienst bietet automatische Skalierung und Hochverfügbarkeit, unterstützt Windows und Linux und ermöglicht automatisierte Bereitstellungen über GitHub, Azure DevOps oder ein anderes beliebiges Git-Repository.

### <a name="virtual-network"></a>Virtual Network
Die Architektur definiert ein privates virtuelles Netzwerk mit dem Adressraum 10.200.0.0/16.

**Netzwerksicherheitsgruppen**: [Netzwerksicherheitsgruppen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (Network Security Groups, NSGs) enthalten Zugriffssteuerungslisten, die den Datenverkehr in einem virtuellen Netzwerk zulassen oder ablehnen. NSGs können dafür verwendet werden, den Datenverkehr auf der Ebene eines Subnetzes oder einzelner VMs zu schützen. Die folgenden NSGs sind vorhanden:
- 1 NSG für Application Gateway
- 1 NSG für die App Service-Umgebung
- 1 NSG für Azure SQL-Datenbank

Jede der NSGs verfügt über bestimmte offene Ports und Protokolle, damit die Lösung sicher und richtig ausgeführt werden kann. Darüber hinaus werden die folgenden Konfigurationen für jede NSG aktiviert:
  - [Diagnoseprotokolle und -ereignisse](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) sind aktiviert und werden im Speicherkonto gespeichert.
  - Azure Monitor-Protokolle ist mit der [Diagnose der NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json) verbunden.

**Subnetze**: Jedem Subnetz ist die entsprechende NSG zugeordnet.

**Azure DNS**: Das Domain Name System (DNS) ist für die Übersetzung (oder Auflösung) eines Website- oder Dienstnamens in die IP-Adresse verantwortlich. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) ist ein Hostingdienst für DNS-Domänen, der die Namensauflösung mithilfe der Azure-Infrastruktur durchführt. Durch das Hosten von Domänen in Azure können Benutzer DNS-Einträge mithilfe der gleichen Anmeldeinformationen, APIs, Tools und Abrechnungsschritte wie für die anderen Azure-Dienste verwalten. Azure DNS unterstützt auch private DNS-Domänen.

**Azure Load Balancer**: Mit [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) können Kunden ihre Anwendungen skalieren und Hochverfügbarkeit für Dienste erzielen. Load Balancer unterstützt sowohl Szenarien mit eingehenden als auch ausgehenden Verbindungen, bietet niedrige Latenzen und einen hohen Durchsatz und unterstützt Millionen Datenflüsse für alle TCP- und UDP-Anwendungen.

### <a name="data-in-transit"></a>Daten während der Übertragung
Azure verschlüsselt standardmäßig die gesamte Kommunikation zu und von Azure-Rechenzentren. Alle Transaktionen für Azure Storage über das Azure-Portal werden per HTTPS durchgeführt.

### <a name="data-at-rest"></a>Ruhende Daten
Die Architektur schützt ruhende Daten durch die Verwendung von Verschlüsselung, Datenbanküberwachung und anderen Maßnahmen.

**Azure Storage**: Um die Anforderungen für verschlüsselte ruhende Daten zu erfüllen, wird überall in [Azure Storage](https://azure.microsoft.com/services/storage/) die [Speicherdienstverschlüsselung](https://docs.microsoft.com/azure/storage/storage-service-encryption) verwendet.

**Azure Disk Encryption**
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) nutzt das BitLocker-Features von Windows, um Volumeverschlüsselung für Datenträger bereitzustellen. Die Lösung ist in Azure Key Vault integriert, um die Steuerung und Verwaltung der Datenträgerverschlüsselungsschlüssel zu erleichtern.

**Azure SQL-Datenbank**: In der Azure SQL-Datenbankinstanz werden die folgenden Datenbanksicherheitsmaßnahmen verwendet:
-   Die [AD-Authentifizierung und -Autorisierung](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) ermöglicht die zentrale Verwaltung der Identitäten von Datenbankbenutzern und anderen Microsoft-Diensten.
-   Die [SQL-Datenbank-Überprüfung](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) verfolgt Datenbankereignisse nach und schreibt diese in ein Überwachungsprotokoll in einem Azure Storage-Konto.
-   Azure SQL-Datenbank ist so konfiguriert, dass [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) für die Echtzeitverschlüsselung und -entschlüsselung der Datenbank, der zugehörigen Sicherungen und der Transaktionsprotokolldateien verwendet wird, um ruhende Informationen zu schützen.
-   [Firewallregeln](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) verhindern sämtlichen Zugriff auf Datenbankserver, bis die richtigen Berechtigungen erteilt werden. Die Firewall gewährt den Datenbankzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung.
-   Die [SQL-Bedrohungserkennung](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) ermöglicht die Erkennung und Reaktion auf potenzielle Bedrohungen, sobald sie auftreten. Dazu werden Sicherheitswarnungen bei verdächtigen Datenbankaktivitäten, potenziellen Sicherheitslücken, Angriffen durch Einschleusung von SQL-Befehlen und anormalen Mustern beim Datenbankzugriff ausgegeben.
-   [Always Encrypted-Spalten](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) stellen sicher, dass vertrauliche Daten im Datenbanksystem niemals im Klartextformat angezeigt werden. Nach dem Aktivieren der Datenverschlüsselung können nur Clientanwendungen oder Anwendungsserver, die Zugriff auf die Schlüssel haben, auf Klartextdaten zugreifen.
- Die [Sicherheit auf Zeilenebene](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) ermöglicht Benutzern das Festlegen von Richtlinien zum Einschränken des Zugriffs auf Daten, um die Verarbeitung zu beenden.
- Die [dynamische Datenmaskierung in SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) kann durchgeführt werden, nachdem die Referenzarchitektur bereitgestellt wurde. Kunden müssen die Einstellungen für die dynamische Datenmaskierung an ihr Datenbankschema anpassen.

### <a name="identity-management"></a>Identitätsverwaltung
Die folgenden Technologien bieten Identitätsverwaltungsfunktionen in der Azure-Umgebung:
-   [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) ist der mehrinstanzenfähige cloudbasierte Verzeichnis- und Identitätsverwaltungsdienst von Microsoft. Alle Benutzer für diese Lösung werden in AAD erstellt. Dies gilt auch für Benutzer, die auf Azure SQL-Datenbank zugreifen.
-   Die Authentifizierung für die Anwendung erfolgt mithilfe von AAD. Weitere Informationen finden Sie unter [Integrieren von Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Darüber hinaus wird für Verschlüsselung der Datenbankspalten Azure Active Directory verwendet, um die Anwendung in der Azure SQL-Datenbank zu authentifizieren. Weitere Informationen finden Sie unter [Schützen von vertraulichen Daten in SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   Die [rollenbasierte Zugriffssteuerung in Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) ermöglicht eine präzise Zugriffsverwaltung für Azure. Der Abonnementzugriff ist auf den Abonnementadministrator beschränkt, und der Zugriff auf Ressourcen kann je nach Benutzerrolle eingeschränkt werden.
- Mit [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) können Kunden die Anzahl von Benutzern mit Zugriff auf bestimmte Informationen einschränken.  Administratoren können mit AAD Privileged Identity Management privilegierte Identitäten und den damit verbundenen Zugriff auf Ressourcen erkennen, einschränken und überwachen. Diese Funktionalität kann auch verwendet werden, um einen bedarfsgesteuerten Just-in-Time-Administratorzugriff zu erzwingen.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) erkennt potenzielle Sicherheitsrisiken, die sich auf die Identitäten einer Organisation auswirken, konfiguriert automatisierte Antworten auf erkannte verdächtige Aktionen, die im Zusammenhang mit Identitäten der Organisation stehen, untersucht verdächtige Vorfälle und führt die entsprechenden Aktionen zu deren Behebung aus.

### <a name="security"></a>Sicherheit
**Geheimnisverwaltung**: Die Lösung verwendet [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) für die Verwaltung von Schlüsseln und Geheimnissen. Azure Key Vault unterstützt Sie dabei, kryptografische Schlüssel und Geheimnisse zu schützen, die von Cloudanwendungen und -diensten verwendet werden. Die folgenden Funktionen von Azure Key Vault unterstützen Kunden beim Schützen von Daten und des Zugriffs auf diese Daten:
- Erweiterte Zugriffsrichtlinien werden auf Bedarfsbasis konfiguriert.
- Key Vault-Zugriffsrichtlinien werden mit minimalen erforderlichen Berechtigungen für Schlüssel und Geheimnisse definiert.
- Alle Schlüssel und Geheimnisse in Key Vault besitzen ein Ablaufdatum.
- Alle Schlüssel in Key Vault werden durch spezielle Hardwaresicherheitsmodule (HSMs) geschützt. Der Schlüsseltyp ist ein HSM-geschützter 2.048-Bit-RSA-Schlüssel.
- Allen Benutzern und Identitäten werden mithilfe von rollenbasierter Zugriffssteuerung die erforderlichen Mindestberechtigungen erteilt.
- Diagnoseprotokolle für Key Vault werden mit einer Aufbewahrungsdauer von mindestens 365 Tagen aktiviert.
- Zulässige kryptographische Vorgänge für Schlüssel sind auf die erforderlichen Vorgänge beschränkt.

**Application Gateway**: Die Architektur verringert die Gefahr von Sicherheitsrisiken durch ein Application Gateway mit Web Application Firewall und aktiviertem OWASP-Regelsatz. Weitere Funktionen:
- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Aktivierte [SSL-Auslagerung](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Deaktivieren von [TLS v1.0 und v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Web Application Firewall](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)
- [Präventionsmodus](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) mit OWASP 3.0-Regelsatz
- Aktivieren der [Diagnoseprotokollierung](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Benutzerdefinierte Integritätstests](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) und [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) bieten zusätzlichen Schutz und zusätzliche Benachrichtigungen. Azure Security Center stellt außerdem ein Reputationssystem bereit.

### <a name="logging-and-auditing"></a>Protokollierung und Überwachung
Azure Monitor ermöglicht eine umfassende Protokollierung von System- und Benutzeraktivitäten sowie der Systemintegrität. Es handelt sich um eine Lösung für die Erfassung und Analyse von Daten, die von Ressourcen in Azure und lokalen Umgebungen generiert werden.
- **Aktivitätsprotokolle**: [Aktivitätsprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) bieten Einblicke in Vorgänge, die für Ressourcen in einem Abonnement ausgeführt wurden. Aktivitätsprotokolle können dabei helfen, den Initiator eines Vorgangs, die Zeit des Auftretens und den Status zu bestimmen.
- **Diagnoseprotokolle**: [Diagnoseprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) umfassen sämtliche Protokolle, die von jeder Ressource ausgegeben werden. Zu diesen Protokollen gehören Windows-Ereignissystemprotokolle, Azure Storage-Protokolle, Key Vault-Überwachungsprotokolle sowie Application Gateway-Zugriffs- und -Firewallprotokolle.
- **Protokollarchivierung**: Alle Diagnoseprotokolle werden für die Archivierung in ein zentrales und verschlüsseltes Azure Storage-Konto geschrieben. Die Aufbewahrung kann vom Benutzer konfiguriert werden (bis zu 730 Tage), um den unternehmensspezifischen Aufbewahrungsanforderungen gerecht zu werden. Diese Protokolle werden dann zur Verarbeitung, Speicherung und Dashboardanzeige mit Azure Monitor-Protokolle verbunden.

Darüber hinaus sind in dieser Architektur die folgenden Überwachungslösungen enthalten:
-   [Active Directory-Bewertung](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Die Lösung „Active Directory-Integritätsüberprüfung“ bewertet die Risiken und die Integrität von Serverumgebungen in regelmäßigen Abständen und erstellt eine priorisierte Liste mit spezifischen Empfehlungen für die bereitgestellte Serverinfrastruktur.
-   [Antischadsoftwarebewertung](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware): Die Antischadsoftwarelösung meldet Schadsoftware, Bedrohungen und den Schutzstatus.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Die Azure Automation-Lösung dient der Speicherung, Ausführung und Verwaltung von Runbooks. Bei dieser Lösung werden Protokolle über Runbooks für Application Insights und Azure SQL-Datenbank erfasst.
-   [Sicherheit und Überwachung](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started): Das Sicherheits- und Überwachungsdashboard ermöglicht einen allgemeinen Einblick in den Sicherheitszustand von Ressourcen. Hierzu werden Metriken zu Sicherheitsdomänen, relevanten Problemen, Erkennungen, Bedrohungsdaten und allgemeinen Sicherheitsabfragen bereitgestellt.
-   [SQL-Bewertung](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Die Lösung „SQL-Integritätsüberprüfung“ bewertet die Risiken und die Integrität von Serverumgebungen in regelmäßigen Abständen und erstellt für die Kunden eine priorisierte Liste mit speziellen Empfehlungen für die bereitgestellte Serverinfrastruktur.
-   [Updateverwaltung](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management): Die Updateverwaltungslösung ermöglicht die Verwaltung von Sicherheitsupdates des Betriebssystems durch die Kunden. Dies umfasst auch den Status der verfügbaren Updates und den Fortschritt beim Installieren der erforderlichen Updates.
-   [Agent-Integritätsdiagnose](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Die Lösung „Agent-Integritätsdiagnose“ meldet die Anzahl bereitgestellter Agents sowie deren geografische Verteilung. Außerdem meldet sie, wie viele Agents nicht mehr reagieren und wie viele Agents Betriebsdaten übermitteln.
-   [Azure-Aktivitätsprotokolle](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Die Lösung „Aktivitätsprotokollanalyse“ hilft bei der Analyse der Azure-Aktivitätsprotokolle in allen Azure-Abonnements eines Kunden.
-   [Änderungsnachverfolgung](https://docs.microsoft.com/azure/automation/automation-change-tracking): Die Lösung „Änderungsnachverfolgung“ ermöglicht es Kunden, problemlos Änderungen in der Umgebung zu erkennen.

**Azure Monitor**
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) unterstützt Benutzer beim Nachverfolgen der Leistung, Aufrechterhalten der Sicherheit und Identifizieren von Trends, indem Organisationen das Überwachen, Erstellen von Warnungen und Archivieren von Daten ermöglicht wird, z.B. die Nachverfolgung von API-Aufrufen in den Azure-Ressourcen von Kunden.

## <a name="threat-model"></a>Bedrohungsmodell

Das Datenflussdiagramm für diese Referenzarchitektur steht zum [Download](https://aka.ms/fedrampPaaSWebAppDFD) bereit und ist unten angegeben. Dieses Modell kann Kunden dabei helfen, bei Änderungen die potenziellen Risikopunkte in der Systeminfrastruktur zu verstehen.

![PaaS-Webanwendung für das FedRAMP-Bedrohungsmodell](images/fedramp-paaswa-threat-model.png?raw=true "PaaS-Webanwendung für das FedRAMP-Bedrohungsmodell")

## <a name="compliance-documentation"></a>Konformitätsdokumentation
Die [Vorlage für Azure Security and Compliance – FedRAMP High-Kundenzuständigkeitsmatrix](https://aka.ms/blueprinthighcrm) listet alle Sicherheitskontrollen auf, die von der FedRAMP High-Baseline verlangt werden. In der Matrix ist angegeben, ob die Implementierung der einzelnen Steuerelemente Aufgabe von Microsoft, des Kunden oder beider Seiten ist.

In der [Blaupause für Azure Security and Compliance – FedRAMP High für IaaS-Webapp – Regulierungsimplementierungsmatrix](https://aka.ms/fedrampPaaSWebAppCIM) sind alle Sicherheitskontrollen aufgelistet, die von der FedRAMP High-Baseline verlangt werden. Die Matrix enthält Informationen dazu, welche Kontrollen durch die PaaS-Webanwendungsarchitektur abgedeckt sind, z.B. ausführliche Beschreibungen, wie die Implementierung die Anforderungen der einzelnen Kontrollen erfüllt.

## <a name="guidance-and-recommendations"></a>Anleitungen und Empfehlungen
### <a name="vpn-and-expressroute"></a>VPN und ExpressRoute
Zur Erzielung von sicheren Verbindungen mit Ressourcen, die als Teil dieser Referenzarchitektur einer PaaS-Webanwendung bereitgestellt werden, muss ein sicherer VPN-Tunnel oder eine [ExpressRoute-Verbindung](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) konfiguriert werden. Durch die korrekte Einrichtung von VPN oder ExpressRoute können Kunden eine zusätzliche Sicherheitsebene für Daten während der Übertragung hinzufügen.

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
