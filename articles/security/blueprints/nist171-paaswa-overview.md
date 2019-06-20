---
title: 'Vorlage für Azure Security and Compliance: PaaS-Webanwendungen gemäß NIST SP 800-171'
description: 'Vorlage für Azure Security and Compliance: PaaS-Webanwendungen gemäß NIST SP 800-171'
services: security
author: jomolesk
ms.assetid: eea21a0a-5930-43e8-937f-5419c20744c9
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: f9773c3b372ab22cbcd99828e147d23c185c4eb6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127326"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-nist-special-publication-800-171"></a>Vorlage für Azure Security and Compliance: PaaS-Webanwendungen gemäß NIST Special Publication 800-171

## <a name="overview"></a>Übersicht
[NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) enthält Richtlinien zum Schützen der nicht klassifizierten kontrollierten Informationen (Controlled Unclassified Information, CUI), die sich in nicht behördlichen Informationssystemen und Organisationen befinden. In NIST SP 800-171 sind 14 „Familien“ mit Sicherheitsanforderungen zum Schützen der Vertraulichkeit von nicht klassifizierten kontrollierten Informationen festgelegt.

Diese Vorlage für Azure Security and Compliance enthält eine Anleitung, mit der Kunden eine PaaS-Webanwendung (Platform-as-a-Service) in Azure bereitstellen können, die eine Teilmenge der NIST SP 800-171-Kontrollmechanismen implementiert. Diese Lösung veranschaulicht die Möglichkeiten von Kunden, bestimmte Sicherheits- und Konformitätsanforderungen zu erfüllen. Sie dient auch als Grundlage für Kunden für die Erstellung und Konfiguration ihrer eigenen Webanwendung in Azure.

Diese Referenzarchitektur, die zugehörige Implementierungsanleitung und das Bedrohungsmodell sollen Kunden als Grundlage für die Anpassung an ihre spezifischen Anforderungen dienen. Sie sollten nicht unverändert in einer Produktionsumgebung verwendet werden. Die Bereitstellung dieser Architektur ohne Änderungen ist unzureichend, um die Anforderungen von NIST SP 800-171 vollständig zu erfüllen. Kunden sind dafür verantwortlich, angemessene Sicherheits- und Konformitätsbewertungen für jede Lösung durchzuführen, die mit dieser Architektur erstellt wurde. Die Anforderungen variieren je nach den Gegebenheiten der Implementierung des jeweiligen Kunden.

## <a name="architecture-diagram-and-components"></a>Architekturdiagramm und Komponenten

Diese Blaupause für Azure Security and Compliance stellt eine Referenzarchitektur für eine PaaS-Webanwendung mit einem Azure SQL-Datenbank-Back-End bereit. Die Webanwendung wird in einer isolierten App Service-Umgebung gehostet. Hierbei handelt es sich um eine private, dedizierte Umgebung in einem Azure-Rechenzentrum. In der Umgebung wird ein Lastenausgleich für den Datenverkehr der Webanwendung für die virtuellen Computer (VMs, Virtual Machines) durchgeführt, die von Azure verwaltet werden. Diese Architektur umfasst auch Netzwerksicherheitsgruppen (NSGs), Azure Application Gateway, Azure DNS und Azure Load Balancer.

Für erweiterte Analysen und Berichterstattungen können Azure SQL-Datenbanken mit Columnstore-Indizes konfiguriert werden. Azure SQL-Datenbanken können abhängig von der Nutzung durch den Kunden zentral hoch- bzw. herunterskaliert oder vollständig ausgeschaltet werden. Jeglicher SQL-Datenverkehr wird mit SSL über selbstsignierte Zertifikate verschlüsselt. Azure empfiehlt als bewährte Methode die Verwendung einer vertrauenswürdigen Zertifizierungsstelle für erweiterte Sicherheit.

Die Lösung verwendet Azure Storage-Konten, die Kunden für die Verwendung von Speicherdienstverschlüsselung konfigurieren können, um die Vertraulichkeit ruhender Daten zu wahren. Azure speichert drei Kopien der Daten im ausgewählten Rechenzentrum eines Kunden, um Resilienz zu gewährleisten. Der georedundante Speicher sorgt dafür, dass Daten in einem sekundären Rechenzentrum Hunderte Kilometer entfernt repliziert und in Form von drei Kopien in diesem Rechenzentrum erneut gespeichert werden. Dadurch wird ein unerwünschtes Ereignis im primären Rechenzentrum des Kunden verhindert, das zu einem Verlust von Daten führt.

Für höhere Sicherheit werden alle Ressourcen in dieser Lösung als Ressourcengruppe über Azure Resource Manager verwaltet. Die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure Active Directory (Azure AD) dient zur Steuerung des Zugriffs auf bereitgestellte Ressourcen. Zu diesen Ressourcen zählen Kundenschlüssel in Azure Key Vault. Die Systemintegrität wird durch Azure Monitor überwacht. Kunden konfigurieren diesen Überwachungsdienst zur Erfassung von Protokollen. Die Systemintegrität wird auf einem einzigen, benutzerfreundlichen Dashboard angezeigt.

Die Azure SQL-Datenbank wird im Allgemeinen über SQL Server Management Studio verwaltet. Die Lösung wird über einen lokalen Computer so konfiguriert, dass über eine sichere VPN-Verbindung oder eine Azure ExpressRoute-Verbindung auf die SQL-Datenbank zugegriffen werden kann.

Darüber hinaus ermöglicht Application Insights über Azure Monitor-Protokolle die Verwaltung der Anwendungsleistung in Echtzeit sowie entsprechende Analysen. *Microsoft empfiehlt Ihnen, eine VPN- oder ExpressRoute-Verbindung für die Verwaltung und den Datenimport in das Subnetz der Referenzarchitektur zu konfigurieren.*

![PaaS-Webanwendung gemäß NIST SP 800-171 – Diagramm zur Referenzarchitektur](images/nist171-paaswa-architecture.png "PaaS-Webanwendung gemäß NIST SP 800-171 – Diagramm zur Referenzarchitektur")

Diese Lösung verwendet die folgenden Azure-Dienste. Weitere Informationen finden Sie im Abschnitt [Bereitstellungsarchitektur](#deployment-architecture).

- Azure Virtual Machines
    - (1) Management/Bastion (Windows Server 2016 Datacenter)
- Virtuelles Azure-Netzwerk
    - (1) /16-Netzwerk
    - (4) /24-Netzwerke
    - (4) Netzwerksicherheitsgruppen
- Azure Application Gateway
    - Web Application Firewall
        - Firewallmodus: Prävention
        - Regelsatz: OWASP
        - Listenerport: 443
- Application Insights
- Azure Active Directory
- App Service-Umgebung v2
- Azure-Automatisierung
- Azure DNS
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor (Protokolle)
- Azure Resource Manager
- Azure Security Center
- Azure SQL-Datenbank
- Azure Storage
- Azure-Automatisierung
- Azure-Web-Apps

## <a name="deployment-architecture"></a>Bereitstellungsarchitektur
Im folgenden Abschnitt werden die Elemente für Entwicklung und Implementierung beschrieben.

**Azure Resource Manager**: Mit [Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) können Kunden mit den Ressourcen der Lösung als Gruppe arbeiten. Kunden können alle Ressourcen für die Lösung in einem einzigen koordinierten Vorgang bereitstellen, aktualisieren oder löschen. Kunden verwenden eine Vorlage für die Bereitstellung. Die Vorlage kann für unterschiedliche Umgebungen geeignet sein, z.B. Testing, Staging und Produktion. Resource Manager umfasst Sicherheits-, Überwachungs- und Kennzeichnungsfunktionen, mit denen Kunden ihre Ressourcen nach der Bereitstellung verwalten können.

**Bastionhost**: Der Bastionhost ist der einzelne Zugangspunkt, über den Benutzer Zugriff auf die bereitgestellten Ressourcen in dieser Umgebung erhalten. Der Bastionhost bietet eine sichere Verbindung mit den bereitgestellten Ressourcen, indem nur Remotedatenverkehr von öffentlichen IP-Adressen auf einer Sicherheitsliste zugelassen wird. Damit Remotedesktopverkehr zugelassen wird, muss die Quelle des Datenverkehrs in der NSG definiert sein.

Diese Lösung erstellt eine VM als Bastionhost in der Domäne mit den folgenden Konfigurationen:
-   [Antischadsoftware-Erweiterung](https://docs.microsoft.com/azure/security/azure-security-antimalware).
-   [Azure-Diagnoseerweiterung](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template).
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) mit Azure Key Vault.
-   Eine [Richtlinie zum automatischen Herunterfahren](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/), um die Nutzung der VM-Ressourcen zu verringern, wenn diese nicht benötigt werden.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) ist aktiviert, damit Anmeldeinformationen und andere Geheimnisse in einer geschützten Umgebung, die vom ausgeführten Betriebssystem isoliert ist, ausgeführt werden.

**Web-Apps**: [Web-Apps](https://docs.microsoft.com/azure/app-service/) ist ein Feature von Azure App Service. Kunden können damit Webanwendungen in der Programmiersprache ihrer Wahl erstellen und hosten, ohne eine Infrastruktur verwalten zu müssen. Das Feature ermöglicht eine automatische Skalierung und sorgt für die Hochverfügbarkeit. Es unterstützt Windows und Linux und ermöglicht automatisierte Bereitstellungen über GitHub, Azure DevOps oder ein anderes beliebiges Git-Repository.

**App Service-Umgebung**: [App Service-Umgebung](https://docs.microsoft.com/azure/app-service/environment/intro) ist ein Feature von App Service. Es stellt eine vollständig isolierte und dedizierte Umgebung zum sicheren Ausführen von App Service-Anwendungen in großem Stil bereit.

Die App Service-Umgebung ist zur Ausführung nur einer einzigen Anwendung isoliert. Sie wird immer in einem virtuellen Netzwerk bereitgestellt. Aufgrund des Isolationsfeatures verfügt die Referenzarchitektur über eine vollständige Mandantenisolierung und wird aus der Azure-Umgebung für mehrere Mandanten entfernt. Kunden haben präzise Kontrolle sowohl über den eingehenden als auch über den ausgehenden Netzwerkdatenverkehr der Anwendung. Anwendungen können schnelle, sichere Verbindungen über virtuelle Netzwerke zu lokalen Unternehmensressourcen einrichten. Kunden können mit der App Service-Umgebung auf Basis von Lastmetriken, verfügbarem Budget oder einem definierten Zeitplan "automatisch skalieren".

Durch die Verwendung der App Service-Umgebung für diese Architektur werden die folgenden Steuerelemente und Konfigurationen bereitgestellt:

- Hosten in einem geschützten virtuellen Azure-Netzwerk und in Netzwerksicherheitsregeln
- Selbstsigniertes Zertifikat des internen Lastenausgleichsmoduls für die HTTPS-Kommunikation. Microsoft empfiehlt als bewährte Methode die Verwendung einer vertrauenswürdigen Zertifizierungsstelle für erweiterte Sicherheit.
- [Interner Lastenausgleichsmodus](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer) (Modus 3)
- Deaktivieren von [TLS 1.0](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Ändern von [TLS-Verschlüsselung](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Steuern der [N/W-Port für eingehenden Datenverkehr](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Web Application Firewall – Daten einschränken](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Zulassen von [Azure SQL-Datenbank-Datenverkehr](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

### <a name="virtual-network"></a>Virtuelles Netzwerk
Die Architektur definiert ein privates virtuelles Netzwerk mit dem Adressraum 10.200.0.0/16.

**Netzwerksicherheitsgruppen**: [NSGs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) enthalten Zugriffssteuerungslisten, die Datenverkehr in einem virtuellen Netzwerk zulassen oder ablehnen. NSGs können dafür verwendet werden, den Datenverkehr auf der Ebene eines Subnetzes oder einzelner VMs zu schützen. Die folgenden NSGs sind vorhanden:
- Eine NSG für Application Gateway
- Eine NSG für die App Service-Umgebung
- Eine NSG für die SQL-Datenbank
- Eine NSG für Bastionhost

Jede der NSGs verfügt über bestimmte offene Ports und Protokolle, damit die Lösung sicher und richtig ausgeführt werden kann. Darüber hinaus werden die folgenden Konfigurationen für jede NSG aktiviert:
  - [Diagnoseprotokolle und -ereignisse](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) werden aktiviert und in einem Speicherkonto gespeichert.
  - Azure Monitor-Protokolle ist mit der [Diagnose der NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json) verbunden.

**Subnetze**: Jedem Subnetz ist die entsprechende NSG zugeordnet.

**Azure DNS**: Das DNS (Domain Name System) ist für die Übersetzung (oder Auflösung) eines Website- oder Dienstnamens in die IP-Adresse verantwortlich. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) ist ein Hostingdienst für DNS-Domänen, der die Namensauflösung mithilfe der Azure-Infrastruktur durchführt. Durch das Hosten von Domänen in Azure können Benutzer DNS-Einträge mithilfe der gleichen Anmeldeinformationen, APIs, Tools und Abrechnungsschritte wie für die anderen Azure-Dienste verwalten. Azure DNS unterstützt auch private DNS-Domänen.

**Azure Load Balancer**: Mit [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) können Kunden ihre Anwendungen skalieren und Hochverfügbarkeit für Dienste erzielen. Load Balancer unterstützt Szenarien mit eingehenden und ausgehenden Verbindungen. Der Dienst sorgt für niedrige Latenzen und einen hohen Durchsatz und kann eine Skalierung auf Millionen von Datenflüssen für alle TCP- und UDP-Anwendungen durchführen.

### <a name="data-in-transit"></a>Daten während der Übertragung
Azure verschlüsselt standardmäßig die gesamte Kommunikation zu und von Azure-Rechenzentren. Alle Transaktionen für Azure Storage über das Azure-Portal werden per HTTPS durchgeführt.

### <a name="data-at-rest"></a>Ruhende Daten

Die Architektur schützt ruhende Daten durch die Verwendung von Verschlüsselung, Datenbanküberwachung und anderen Maßnahmen.

**Azure Storage**: Zur Erfüllung der Anforderungen an verschlüsselte ruhende Daten wird für den gesamten [Storage](https://azure.microsoft.com/services/storage/)-Bereich die [Speicherdienstverschlüsselung](https://docs.microsoft.com/azure/storage/storage-service-encryption) verwendet. Dadurch werden Daten geschützt, um die Zusagen und Anforderungen der Organisation im Hinblick auf die Sicherheit und Compliance gemäß NIST SP 800-171 einzuhalten.

**Azure Disk Encryption**: Für [Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) wird das BitLocker-Feature von Windows verwendet, um Volumeverschlüsselung für Datenträger bereitzustellen. Die Lösung ist in Azure Key Vault integriert, um die Steuerung und Verwaltung der Datenträgerverschlüsselungsschlüssel zu erleichtern.

**Azure SQL-Datenbank**: In der SQL-Datenbank-Instanz werden die folgenden Datenbanksicherheitsmaßnahmen verwendet:
-   Die [Active Directory-Authentifizierung und -Autorisierung](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) ermöglicht die zentrale Verwaltung der Identitäten von Datenbankbenutzern und anderen Microsoft-Diensten.
-   Die [SQL-Datenbank-Überprüfung](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) verfolgt Datenbankereignisse nach und schreibt diese in ein Überwachungsprotokoll in einem Azure Storage-Konto.
-   Azure SQL-Datenbank wird für die Verwendung der [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) konfiguriert. Bei diesem Vorgang wird die Echtzeitverschlüsselung und -entschlüsselung der Datenbank, der zugehörigen Sicherungen und der Transaktionsprotokolldateien durchgeführt, um ruhende Informationen zu schützen. Die transparente Datenverschlüsselung gewährleistet, dass gespeicherte Daten keinen unbefugten Zugriffen ausgesetzt sind.
-   [Firewallregeln](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) verhindern sämtlichen Zugriff auf Datenbankserver, bis die richtigen Berechtigungen erteilt werden. Die Firewall gewährt den Datenbankzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung.
-   [SQL-Bedrohungserkennung](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) ermöglicht die Erkennung und Reaktion auf potenzielle Bedrohungen, sobald diese auftreten. Sie stellt Warnungen zu verdächtigen Datenbankaktivitäten, potenziellen Sicherheitsrisiken, Angriffen durch Einschleusung von SQL-Befehlen sowie zu anomalen Datenbank-Zugriffsmustern bereit.
-   [Verschlüsselte Spalten](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) stellen sicher, dass vertrauliche Daten im Datenbanksystem niemals im Klartextformat angezeigt werden. Nach der Aktivierung der Datenverschlüsselung können nur Clientanwendungen oder Anwendungsserver, die Zugriff auf die Schlüssel haben, auf Klartextdaten zugreifen.
- Die [dynamische Datenmaskierung](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) schränkt die Offenlegung vertraulicher Daten ein, indem diese Daten für nicht berechtigte Benutzer oder Anwendungen maskiert werden. Sie kann automatisch potenziell vertrauliche Daten ermitteln und eine geeignete Maskierung vorschlagen. Die dynamische Datenmaskierung unterstützt Sie beim Reduzieren des Zugriffs, damit vertrauliche Daten die Datenbank nicht über nicht autorisierten Zugriff verlassen. *Kunden sind dafür verantwortlich, die Einstellungen an ihr Datenbankschema anzupassen.*

### <a name="identity-management"></a>Identitätsverwaltung
Die folgenden Technologien enthalten Funktionen zum Verwalten des Zugriffs auf Daten in der Azure-Umgebung:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) ist der mehrinstanzenfähige cloudbasierte Verzeichnis- und Identitätsverwaltungsdienst von Microsoft. Alle Benutzer dieser Lösung werden in Azure AD erstellt. Dies gilt auch für die Benutzer, die Zugriff auf die SQL-Datenbank haben.
-   Authentifizierung für die Anwendung erfolgt mithilfe von Azure AD. Weitere Informationen finden Sie unter [Integrieren von Anwendungen in Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Für die Verschlüsselung der Datenbankspalten wird Azure AD auch verwendet, um die Anwendung für Azure SQL-Datenbank zu authentifizieren. Weitere Informationen finden Sie unter [Schützen von vertraulichen Daten in SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) kann von Administratoren zum Definieren detaillierter Zugriffsberechtigungen verwendet werden. Damit können sie nur den Benutzern die Zugriffsrechte gewähren, die diese zum Ausführen ihrer Aufgaben benötigen. Anstatt jedem Benutzer uneingeschränkten Zugriff für Azure-Ressourcen zu gewähren, können Administratoren auch nur bestimmte Aktionen für den Zugriff auf Ressourcen und Daten erlauben. Der Abonnementzugriff ist auf den Abonnementadministrator beschränkt.
- Mit [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) können Kunden die Anzahl von Benutzern mit Zugriff auf bestimmte Informationen einschränken. Administratoren können mit Azure AD Privileged Identity Management privilegierte Identitäten und den damit verbundenen Zugriff auf Ressourcen erkennen, einschränken und überwachen. Diese Funktionalität kann auch verwendet werden, um einen bedarfsgesteuerten Just-in-Time-Administratorzugriff zu erzwingen.
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

**Azure Security Center**: Mit [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) können Kunden Sicherheitsrichtlinien über Workloads hinweg zentral anwenden und verwalten, die Angriffsfläche verringern sowie Angriffe erkennen und darauf reagieren. Azure Security Center greift zudem auf die vorhandenen Konfigurationen der Azure-Dienste zu, um Empfehlungen für die Konfiguration und den Dienst bereitzustellen, die zur Verbesserung des Sicherheitsstatus und zum Schutz von Daten beitragen sollen.

Azure Security Center verwendet verschiedene Erkennungsfunktionen, um Kunden vor potenziellen Angriffen auf deren Umgebung zu warnen. Diese Warnungen enthalten wichtige Informationen zum Auslöser der Warnung, zu den möglicherweise betroffenen Ressourcen und zur Quelle des Angriffs. In Security Center stehen verschiedene [vordefinierte Sicherheitswarnungen](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) zur Verfügung, die ausgelöst werden, wenn eine Bedrohung oder eine verdächtige Aktivität erkannt wird. Mithilfe [benutzerdefinierter Warnungsregeln](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) können Kunden neue Sicherheitswarnungen auf der Grundlage von Daten definieren, die bereits in ihrer Umgebung gesammelt werden.

Security Center bietet priorisierte Sicherheitswarnungen und -vorfälle. Security Center erleichtert Kunden die Erkennung und Behebung potenzieller Sicherheitsprobleme. Für jede erkannte Bedrohung wird ein [Bericht über Bedrohungen](https://docs.microsoft.com/azure/security-center/security-center-threat-report) generiert. Teams zur Reaktion auf Vorfälle können die Berichte verwenden, wenn sie Bedrohungen untersuchen und beheben.

**Azure Application Gateway**: Die Architektur verringert die Gefahr von Sicherheitsrisiken durch ein Anwendungsgateway mit einer konfigurierten Web Application Firewall und aktiviertem OWASP-Regelsatz. Weitere Funktionen:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- Aktivieren der [SSL-Auslagerung](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal).
- Deaktivieren von [TLS v1.0 und v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [Web Application Firewall](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (Schutzmodus).
- [Präventionsmodus](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) mit OWASP 3.0-Regelsatz.
- Aktivieren der [Diagnoseprotokollierung](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
- [Benutzerdefinierte Integritätstests](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal).
- [Azure Security Center](https://azure.microsoft.com/services/security-center) und [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) bieten zusätzlichen Schutz und zusätzliche Benachrichtigungen. Azure Security Center stellt außerdem ein Reputationssystem bereit.

### <a name="logging-and-auditing"></a>Protokollierung und Überwachung

Azure-Dienste protokollieren umfassend die System- und Benutzeraktivitäten sowie die Systemintegrität:
- **Aktivitätsprotokolle**: [Aktivitätsprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) bieten Einblicke in Vorgänge, die für Ressourcen in einem Abonnement ausgeführt wurden. Aktivitätsprotokolle können dabei helfen, den Initiator eines Vorgangs, die Zeit des Auftretens und den Status zu bestimmen.
- **Diagnoseprotokolle**: [Diagnoseprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) umfassen sämtliche Protokolle, die von jeder Ressource ausgegeben werden. Zu diesen Protokollen gehören Windows-Ereignissystemprotokolle, Azure Storage-Protokolle, Key Vault-Überwachungsprotokolle sowie Application Gateway-Zugriffs- und -Firewallprotokolle. Alle Diagnoseprotokolle werden für die Archivierung in ein zentrales und verschlüsseltes Azure Storage-Konto geschrieben. Benutzer können den Aufbewahrungszeitraum, bis zu 730 Tage, so konfigurieren, dass die spezifischen Anforderungen erfüllt werden.

**Azure Monitor-Protokolle:** Protokolle werden zur Verarbeitung, Speicherung und Dashboardanzeige in [Azure Monitor-Protokolle](https://azure.microsoft.com/services/log-analytics/) konsolidiert. Nach der Erfassung der Daten werden diese in separaten Tabellen für die einzelnen Datentypen in den Log Analytics-Arbeitsbereichen organisiert. Auf diese Weise können alle Daten zusammen analysiert werden, unabhängig von der zugehörigen ursprünglichen Quelle. Security Center lässt sich in Azure Monitor-Protokolle integrieren. Kunden können mithilfe von Kusto-Abfragen auf ihre Sicherheitsereignisdaten zugreifen und diese mit Daten von anderen Diensten kombinieren.

In dieser Architektur sind die folgenden Azure-[Überwachungslösungen](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) enthalten:
-   [Active Directory-Bewertung](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): Mit der Lösung für die Active Directory-Integritätsüberprüfung können Sie in regelmäßigen Abständen die Risiken und die Integrität Ihrer Serverumgebungen bewerten. Sie stellt eine priorisierte Liste von Empfehlungen bereit, die spezifisch für Ihre bereitgestellte Serverinfrastruktur gelten.
- [SQL-Bewertung](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): Mit der SQL-Lösung für die Integritätsüberprüfung können Sie in regelmäßigen Abständen die Risiken und die Integrität Ihrer Serverumgebungen bewerten. Die Lösung stellt für Kunden eine priorisierte Liste mit Empfehlungen bereit, die spezifisch für die bereitgestellte Serverinfrastruktur gelten.
- [Agent-Integritätsdiagnose](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): Diese Lösung für die Agent-Integritätsdiagnose meldet die Anzahl von bereitgestellten Agents sowie deren geografische Verteilung. Zudem meldet sie, wie viele Agents nicht reagieren und wie viele Agents operative Daten übermitteln.
-   [Aktivitätsprotokollanalyse](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): Die Lösung „Aktivitätsprotokollanalyse“ hilft bei der Analyse der Azure-Aktivitätsprotokolle in allen Azure-Abonnements eines Kunden.

**Azure Automation**: [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) dient zum Speichern, Ausführen und Verwalten von Runbooks. Bei dieser Lösung werden Protokolle über Runbooks für Azure SQL-Datenbank erfasst. Kunden können mithilfe der [Änderungsnachverfolgung](https://docs.microsoft.com/azure/automation/automation-change-tracking) von Azure Automation auf einfache Weise Änderungen in der Umgebung identifizieren.

**Azure Monitor**: [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) dient zum Nachverfolgen der Leistung, zum Gewährleisten der Sicherheit und zum Identifizieren von Trends. Organisationen können diese Plattform zum Überwachen, zum Erstellen von Warnungen und zum Archivieren von Daten verwenden. Zudem können sie die API-Aufrufe in ihren Azure-Ressourcen nachverfolgen.

**Application Insights**: [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) ist ein erweiterbarer, für Webentwickler konzipierter Dienst zur Verwaltung der Anwendungsleistung auf mehreren Plattformen. Application Insights erkennt Leistungsanomalien. Kunden verwenden die Lösung zur Überwachung von aktiven Webanwendungen. Application Insights verfügt über leistungsstarke Analysetools, mit denen Kunden Probleme diagnostizieren und nachvollziehen können, wie ihre App von den Benutzern verwendet wird. Der Dienst unterstützt Kunden bei der kontinuierlichen Verbesserung der Leistung und Benutzerfreundlichkeit.

## <a name="threat-model"></a>Bedrohungsmodell

Das Datenflussdiagramm für diese Referenzarchitektur steht zum [Download](https://aka.ms/nist171-paaswa-tm) bereit oder kann hier gefunden werden. Dieses Modell kann Kunden dabei helfen, bei der Vornahme von Änderungen die potenziellen Risikopunkte in der Systeminfrastruktur zu verstehen.

![PaaS-Webanwendung gemäß NIST SP 800-171 – Bedrohungsmodell](images/nist171-paaswa-threat-model.png "PaaS-Webanwendung gemäß NIST SP 800-171 – Bedrohungsmodell")

## <a name="compliance-documentation"></a>Konformitätsdokumentation
Die [Vorlage für Azure Security and Compliance: NIST SP 800-171 – Kundenzuständigkeitsmatrix](https://aka.ms/nist171-crm) listet alle Sicherheitskontrollen auf, die für NIST SP 800-171 erforderlich sind. In dieser Matrix ist angegeben, ob die Implementierung der einzelnen Kontrollen Aufgabe von Microsoft, des Kunden oder beider Seiten ist.

Die [Azure-Blaupause für Sicherheit und Compliance: PaaS-Webanwendung gemäß NIST SP 800-171 – Regulierungsimplementierungsmatrix](https://aka.ms/nist171-paaswa-cim) enthält Informationen dazu, auf welche NIST SP 800-171-Kontrollen durch die Architektur der PaaS-Webanwendung verwiesen wird. Sie enthält zudem eine ausführliche Beschreibung, wie die Implementierung die Anforderungen der einzelnen Kontrollen erfüllt.

## <a name="guidance-and-recommendations"></a>Anleitungen und Empfehlungen
### <a name="vpn-and-expressroute"></a>VPN und ExpressRoute
Für sichere Verbindungen mit Ressourcen, die als Teil dieser Referenzarchitektur einer PaaS-Webanwendung bereitgestellt werden, muss ein sicherer VPN-Tunnel oder eine [ExpressRoute-Verbindung](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) konfiguriert werden. Durch die korrekte Einrichtung von VPN oder ExpressRoute können Kunden eine zusätzliche Sicherheitsebene für Daten während der Übertragung hinzufügen.

Mit der Implementierung eines sicheren VPN-Tunnels mit Azure kann eine virtuelle private Verbindung zwischen einem lokalen Netzwerk und einem virtuellen Azure-Netzwerk hergestellt werden. Diese Verbindung wird über das Internet hergestellt und ermöglicht Kunden das Übertragen von Informationen in einem „Tunnel“ innerhalb eines verschlüsselten Links zwischen dem Kundennetzwerk und Azure. Eine Site-to-Site-VPN-Verbindung ist eine sichere, ausgereifte Technologie, die seit Jahrzehnten von Unternehmen aller Größen eingesetzt wird. Der [IPsec-Tunnelmodus](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) wird bei dieser Option als Verschlüsselungsmechanismus verwendet.

Da Datenverkehr im VPN-Tunnel über ein Site-to-Site-VPN über das Internet übertragen wird, bietet Microsoft eine weitere Verbindungsoption an, die noch sicherer ist. ExpressRoute ist ein dedizierter WAN-Link zwischen Azure und einem lokalen Standort oder einem Exchange-Hostinganbieter. ExpressRoute-Verbindungen stellen eine direkte Verbindung mit einem Telekommunikationsanbieter des Kunden her. Daher werden die Daten nicht über das Internet übertragen und im Internet verfügbar gemacht. Diese Verbindungen bieten mehr Zuverlässigkeit, eine höhere Geschwindigkeit, niedrigere Latenzzeiten und mehr Sicherheit als herkömmliche Verbindungen.

Bewährte Methoden für das Implementieren eines sicheren hybriden Netzwerks, mit dem ein lokales Netzwerk auf Azure erweitert wird, finden Sie [hier](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Haftungsausschluss

 - Dieses Dokument dient nur zu Informationszwecken. MICROSOFT ÜBERNIMMT KEINE AUSDRÜCKLICHEN, STILLSCHWEIGENDEN ODER GESETZLICH VORGEGEBENEN GARANTIEN HINSICHTLICH DER INFORMATIONEN IN DIESEM DOKUMENT. Dieses Dokument wird so bereitgestellt, wie es ist. Informationen und Stellungnahmen in diesem Dokument, einschließlich URLs und anderer Verweise auf Internetwebsites, können ohne vorherige Ankündigung geändert werden. Kunden, die dieses Dokument lesen, tragen das Risiko, es zu verwenden.
 - Dieses Dokument gibt Kunden keine Rechte an geistigem Eigentum an Microsoft-Produkten oder -Lösungen.
 - Kunden dürfen dieses Dokument zu internen Referenzzwecken kopieren und verwenden.
 - Bestimmte Empfehlungen in diesem Dokument können zu einer erhöhten Nutzung von Daten, Netzwerken oder Computeressourcen in Azure sowie zu höheren Azure-Lizenz- oder -Abonnementkosten für den Kunden führen.
 - Diese Architektur soll Kunden als Grundlage für die Anpassung an ihre spezifischen Anforderungen dienen. Sie sollte nicht unverändert in einer Produktionsumgebung verwendet werden.
 - Dieses Dokument wurde als Referenz entwickelt und sollte nicht dazu verwendet werden, alle Mittel zu definieren, mit denen ein Kunde bestimmte Konformitätsanforderungen und -vorschriften erfüllen kann. Kunden sollten bei genehmigten Kundenimplementierungen rechtliche Unterstützung von ihrer Organisation einholen.
