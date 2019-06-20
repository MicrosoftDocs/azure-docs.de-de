---
title: Azure-Blaupause für Sicherheit und Compliance – PaaS-Webanwendung für GESCHÜTZTE Daten (Australien, AU-PROTECTED)
description: Azure-Blaupause für Sicherheit und Compliance – PaaS-Webanwendung für GESCHÜTZTE Daten (Australien, AU-PROTECTED)
services: security
author: meladie
ms.assetid: 708aa129-b226-4e02-85c6-1f86e54564e4
ms.service: security
ms.topic: article
ms.date: 08/23/2018
ms.author: meladie
ms.openlocfilehash: c17f16ce796c9f296facd69c18de4effc7ff5258
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60610160"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-australia-protected"></a>Azure-Blaupause für Sicherheit und Compliance – PaaS-Webanwendung für GESCHÜTZTE Daten (Australien, AU-PROTECTED)

## <a name="overview"></a>Übersicht

Diese Azure-Blaupause für Sicherheit und Compliance bietet Orientierungshilfe für die Bereitstellung einer Platform-as-a-Service-Umgebung (PaaS-Umgebung), die für das Erfassen, Speichern und Abrufen von AU-PROTECTED-Regierungsdaten geeignet ist und den Zielen des vom Australischen Direktorium für Signale (Australian Signals Directorate, ASD) erstellten Australian Government Information Security Manual (ISM) entspricht. Diese Blaupause stellt eine allgemeine Referenzarchitektur dar und veranschaulicht die ordnungsgemäße Verarbeitung vertraulicher Regierungsdaten in einer sicheren, konformen Umgebung mit mehreren Ebenen.

Diese Referenzarchitektur, ein Implementierungsleitfaden und ein Bedrohungsmodell bieten Kunden eine Grundlage für die Ausführung ihrer eigenen Planungs- und Systemakkreditierungsprozesse und unterstützen Kunden dabei, Workloads ASD-konform für Azure bereitzustellen. Kunden können auch eine Azure VPN Gateway-Instanz oder ExpressRoute implementieren, um Verbunddienste zu verwenden und lokale Ressourcen in Azure-Ressourcen zu integrieren. Kunden müssen die Auswirkungen der Verwendung lokaler Ressourcen auf die Sicherheit berücksichtigen. Eine zusätzliche Konfiguration ist erforderlich, um alle Anforderungen zu erfüllen, da diese je nach den Besonderheiten der jeweiligen Kundenimplementierung variieren können.

Um ASD-Konformität zu erreichen, muss das System von einem Information Security Registered Assessor überwacht werden. Der Kunde ist für die Durchführung einer angemessenen Sicherheits- und Konformitätsbewertung jeder Lösung verantwortlich, die mit dieser Architektur erstellt wurde, da die Anforderungen je nach den Besonderheiten der jeweiligen Implementierung des Kunden unterschiedlich sein können.

## <a name="architecture-diagram-and-components"></a>Architekturdiagramm und Komponenten
Diese Lösung stellt eine Referenzarchitektur für eine PaaS-Webanwendung mit einem Azure SQL-Datenbank-Back-End bereit. Die Webanwendung wird in einer isolierten Azure App Service-Umgebung gehostet. Hierbei handelt es sich um eine private, dedizierte Umgebung in einem Azure-Rechenzentrum. In der Umgebung wird ein Lastenausgleich für den Datenverkehr der Webanwendung für die virtuellen Computer durchgeführt, die von Azure verwaltet werden. Alle Webanwendungsverbindungen erfordern TLS mit mindestens Version 1.2. Diese Architektur umfasst auch Netzwerksicherheitsgruppen, ein Application Gateway, Azure DNS und einen Load Balancer.

Die Architektur bietet eine sichere Hybridumgebung, die ein lokales Netzwerk auf Azure erweitert und den sicheren Zugriff auf webbasierte Workloads durch Unternehmensbenutzer eines privaten lokalen Netzwerks einer Organisation oder aus dem Internet ermöglicht. Bei lokalen Lösungen ist der Kunde für alle Aspekte der Sicherheit, Vorgänge und Compliance haftbar und verantwortlich.

Die in dieser Lösung enthaltenen Azure-Ressourcen können über ein IPSec-VPN mit einem VPN-Gateway und über ExpressRoute eine Verbindung mit einem lokalen Netzwerk oder einer Rechenzentrums-Housingumgebung (z. B. CDC in Canberra) herstellen. Die Entscheidung zur Nutzung eines VPNs sollte unter Berücksichtigung der Klassifizierung der übertragenen Daten und des Netzwerkpfads getroffen werden. Kunden, die umfangreiche, unternehmenswichtige Workloads mit Big Data-Anforderungen ausführen, sollten eine Hybridnetzwerkarchitektur unter Verwendung von ExpressRoute für private Netzwerkkonnektivität mit Azure-Diensten in Erwägung ziehen. Weitere Informationen zu Verbindungsmechanismen für Azure finden Sie im Abschnitt [Anleitungen und Empfehlungen](#guidance-and-recommendations).

Der Verbund mit Azure Active Directory sollte verwendet werden, um Benutzern die Authentifizierung mit lokalen Anmeldeinformationen und den Zugriff auf alle Ressourcen in der Cloud über eine lokale Infrastruktur der Active Directory-Verbunddienste zu ermöglichen. Active Directory-Verbunddienste können einen vereinfachten und sicheren Identitätsverbund sowie Funktionen der einmaligen Webanmeldung für diese Hybridumgebung bereitstellen. Weitere Informationen zur Einrichtung von Azure Active Directory finden Sie im Abschnitt [Anleitungen und Empfehlungen](#guidance-and-recommendations).

Die Lösung verwendet Azure Storage-Konten, die Kunden für die Verwendung von Speicherdienstverschlüsselung konfigurieren können, um die Vertraulichkeit ruhender Daten zu wahren. Azure speichert drei Kopien der Daten in der ausgewählten Region eines Kunden, um Resilienz zu gewährleisten. Azure-Regionen werden in robusten Regionspaaren bereitgestellt, und durch georedundanten Speicher wird sichergestellt, dass die Daten ebenfalls mit drei Kopien in die zweite Region repliziert werden. Dadurch wird ein unerwünschtes Ereignis am primären Datenspeicherort des Kunden verhindert, das zu einem Verlust von Daten führt.

Zwecks höherer Sicherheit werden alle Azure-Ressourcen in dieser Lösung als Ressourcengruppe über Azure Resource Manager verwaltet. Die rollenbasierte Zugriffssteuerung von Azure Active Directory dient zur Steuerung des Zugriffs auf die bereitgestellten Ressourcen und Schlüssel in Azure Key Vault. Die Systemintegrität wird durch Azure Security Center und Azure Monitor überwacht. Der Kunde konfiguriert das Überwachen von Diensten zum Speichern von Protokollen und Anzeigen der Systemintegration in nur einem Dashboard, durch das man leicht navigieren kann. Azure Application Gateway wird als Firewall im Schutzmodus konfiguriert und lässt nur Datenverkehr zu, für den TLS v1.2 oder höher verwendet wird. Die Lösung nutzt die Azure Application Service-Umgebung v2 zum Isolieren der Webebene in einer Umgebung, die nicht mehrere Mandanten aufweist.

![PaaS-Webanwendung für GESCHÜTZTE Daten (AU-PROTECTED) – Referenzarchitektur](images/au-protected-paaswa-architecture.png?raw=true "PaaS-Webanwendung für GESCHÜTZTE Daten (AU-PROTECTED) – Diagramm zur Referenzarchitektur")

Diese Lösung verwendet die folgenden Azure-Dienste. Weitere Informationen finden Sie im Abschnitt [Bereitstellungsarchitektur](#deployment-architecture).

- Application Gateway
    - Web Application Firewall
        - Firewallmodus: Prävention
        - Regelsatz: OWASP
        - Listenerport: 443
- Application Insights
- Azure Active Directory
- Azure Application Service-Umgebung v2
- Azure-Automatisierung
- Azure DNS
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure SQL-Datenbank
- Azure Storage
- Azure Monitor-Protokolle
- Virtuelles Azure-Netzwerk
    - (1) /16 Netzwerk
    - (4) /24 Netzwerke
    - Netzwerksicherheitsgruppen
- Netzwerksicherheitsgruppen
- Recovery Services-Tresor
- Azure-Web-App

Diese Blaupause enthält Azure-Dienste, die nicht für die Verwendung unter der Klassifizierung „Protected“ (Geschützt) vom Australian Cyber Security Centre (ACSC) zertifiziert wurden. Microsoft empfiehlt, dass Kunden die veröffentlichten Sicherheits- und Überwachungsberichte überprüfen, die sich auf diese Azure-Dienste beziehen, und anhand des jeweiligen Risikomanagementframeworks ermitteln, ob der Azure-Dienst für ihre interne Akkreditierung und Nutzung unter der Klassifizierung „Protected“ (Geschützt) geeignet ist.

## <a name="deployment-architecture"></a>Bereitstellungsarchitektur
Im folgenden Abschnitt werden die Elemente für Entwicklung und Implementierung beschrieben.

**Azure Resource Manager**: Mit [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) können Kunden mit den Ressourcen der Lösung als Gruppe arbeiten. Kunden können alle Ressourcen für die Lösung in einem einzigen koordinierten Vorgang bereitstellen, aktualisieren oder löschen. Kunden verwenden eine Vorlage für die Bereitstellung, die für unterschiedliche Umgebungen geeignet sein kann, z.B. Testing, Staging und Produktion. Resource Manager umfasst Sicherheits-, Überwachungs- und Kennzeichnungsfunktionen, mit denen Kunden ihre Ressourcen nach der Bereitstellung verwalten können.

**Bastionhost**: Der Bastionhost ist der einzelne Zugangspunkt, über den Benutzer Zugriff auf die bereitgestellten Ressourcen in dieser Umgebung erhalten. Der Bastionhost bietet eine sichere Verbindung mit den bereitgestellten Ressourcen, indem nur Remotedatenverkehr von öffentlichen IP-Adressen auf einer Sicherheitsliste zugelassen wird. Damit RDP-Datenverkehr (Remotedesktopprotokoll) zugelassen wird, muss die Quelle des Datenverkehrs in der Netzwerksicherheitsgruppe definiert sein.

Diese Lösung erstellt einen virtuellen Computer als Bastionhost in der Domäne mit den folgenden Konfigurationen:
-   [Antimalware-Erweiterung](https://docs.microsoft.com/azure/security/azure-security-antimalware)
-   [Azure-Diagnoseerweiterung](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) mit Azure Key Vault
-   Eine [Richtlinie zum automatischen Herunterfahren](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/), um die Nutzung der Ressourcen virtueller Computer zu verringern, wenn sie nicht benötigt werden

**App Service-Umgebung v2**: Die [Azure App Service-Umgebung](https://docs.microsoft.com/azure/app-service/environment/intro) ist ein App Service-Feature, das eine vollständig isolierte und dedizierte Umgebung zur sicheren Ausführung von App Service-Anwendungen in großem Umfang bereitstellt.

Aufgrund der Isolierung werden in App Service-Umgebungen nur Anwendungen eines einzelnen Kunden ausgeführt. Die Umgebungen werden zudem immer in einem virtuellen Netzwerk bereitgestellt. Kunden haben eine genauere Kontrolle über den eingehenden und ausgehenden Anwendungs-Netzwerkdatenverkehr, und Anwendungen können über virtuelle Netzwerke sichere Hochgeschwindigkeitsverbindungen mit lokalen Unternehmensressourcen herstellen.

Die Verwendung von App Service-Umgebungen für diese Architektur ermöglicht die folgenden Kontrollmechanismen/Konfigurationen:

- App Service-Umgebungen müssen für die Verwendung des Serviceplans „Isolated“ konfiguriert werden.
    - Konfigurieren verschiedener App Service-Umgebungen für Anwendungen unter verschiedenen Klassifizierungen
- Hosten in einem geschützten virtuellen Azure-Netzwerk und in Netzwerksicherheitsregeln
- Mit einem selbstsignierten Zertifikat des internen Lastenausgleichsmoduls für die HTTPS-Kommunikation konfigurierte App Service-Umgebungen. Microsoft empfiehlt als bewährte Methode die Verwendung einer vertrauenswürdigen Zertifizierungsstelle für erweiterte Sicherheit.
- [Interner Lastenausgleichsmodus](https://docs.microsoft.com/azure/app-service-web/app-service-environment-with-internal-load-balancer) (Modus 3)
- Deaktivieren von [TLS v1.0 und v1.1](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Ändern von [TLS-Verschlüsselung](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Steuern der [N/W-Port für eingehenden Datenverkehr](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic)
- [Web Application Firewall – Daten einschränken](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Zulassen von [Azure SQL-Datenbank-Datenverkehr](https://docs.microsoft.com/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)

**Azure-Web-App**: [Azure App Service](https://docs.microsoft.com/azure/app-service/) ermöglicht Kunden das Erstellen und Hosten von Webanwendungen in der Programmiersprache ihrer Wahl, ohne eine Infrastruktur verwalten zu müssen. Der Dienst bietet automatische Skalierung und Hochverfügbarkeit, unterstützt Windows und Linux und ermöglicht automatisierte Bereitstellungen über GitHub, Azure DevOps Services oder ein anderes beliebiges Git-Repository.

### <a name="virtual-network"></a>Virtual Network
Die Architektur definiert ein privates virtuelles Netzwerk mit dem Adressraum 10.200.0.0/16.

**Netzwerksicherheitsgruppen**: [Netzwerksicherheitsgruppen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) enthalten Zugriffssteuerungslisten, die den Datenverkehr in einem virtuellen Netzwerk zulassen oder ablehnen. Netzwerksicherheitsgruppen können verwendet werden, um Datenverkehr auf der Ebene eines Subnetzes oder eines einzelnen Computers zu schützen. Es gibt die folgenden Netzwerksicherheitsgruppen:
- 1 Netzwerksicherheitsgruppe für Application Gateway
- 1 Netzwerksicherheitsgruppe für die App Service-Umgebung
- 1 Netzwerksicherheitsgruppe für die Azure SQL-Datenbank
- 1 Netzwerksicherheitsgruppe für den Bastionhost

Jede der Netzwerksicherheitsgruppen verfügt über bestimmte offene Ports und Protokolle, damit die Lösung sicher und richtig ausgeführt werden kann. Darüber hinaus werden die folgenden Konfigurationen für jede Netzwerksicherheitsgruppe aktiviert:

  - [Diagnoseprotokolle und -ereignisse](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) sind aktiviert und werden im Speicherkonto gespeichert.
  - Azure Monitor-Protokolle ist mit der [Diagnose der Netzwerksicherheitsgruppe](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json) verbunden.

**Subnetze**: Jedes Subnetz ist seiner entsprechenden Netzwerksicherheitsgruppe zugeordnet.

**Azure DNS**: Das Domain Name System (DNS) ist für die Übersetzung (oder Auflösung) eines Website- oder Dienstnamens in die IP-Adresse verantwortlich. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) ist ein Hostingdienst für DNS-Domänen, der die Namensauflösung mithilfe der Azure-Infrastruktur durchführt. Durch das Hosten von Domänen in Azure können Benutzer DNS-Einträge mithilfe der gleichen Anmeldeinformationen, APIs, Tools und Abrechnungsschritte wie für die anderen Azure-Dienste verwalten. Azure DNS unterstützt auch private DNS-Domänen.

**Azure Load Balancer**: Mit [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) können Kunden ihre Anwendungen skalieren und Hochverfügbarkeit für Dienste erzielen. Load Balancer unterstützt sowohl Szenarien mit eingehenden als auch ausgehenden Verbindungen, bietet niedrige Latenzen und einen hohen Durchsatz und unterstützt Millionen Datenflüsse für alle TCP- und UDP-Anwendungen.

### <a name="data-in-transit"></a>Daten während der Übertragung
Azure verschlüsselt standardmäßig die gesamte Kommunikation zu und von Azure-Rechenzentren. 

Für geschützte Daten während der Übertragung aus kundeneigenen Netzwerken verwendet die Architektur das Internet oder Azure ExpressRoute mit einem mit IPSEC konfigurierten VPN-Gateway.

Darüber hinaus erfolgen alle Transaktionen mit Azure über das Azure-Verwaltungsportal über HTTPS mit TLS v1.2.

### <a name="data-at-rest"></a>Ruhende Daten
Die Architektur schützt ruhende Daten durch die Verwendung von Verschlüsselung, Datenbanküberwachung und anderen Maßnahmen.

**Azure Storage**: Um die Anforderungen für verschlüsselte ruhende Daten zu erfüllen, wird überall in [Azure Storage](https://azure.microsoft.com/services/storage/) die [Speicherdienstverschlüsselung](https://docs.microsoft.com/azure/storage/storage-service-encryption) verwendet. Dadurch werden Daten geschützt, um die Zusagen und Anforderungen der Organisation im Hinblick auf die Sicherheit und Konformität gemäß dem Australian Government ISM einzuhalten.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) nutzt das BitLocker-Feature von Windows, um eine Volumeverschlüsselung für Datenträger bereitzustellen. Die Lösung ist in Azure Key Vault integriert, um die Steuerung und Verwaltung der Datenträgerverschlüsselungsschlüssel zu erleichtern.

**Azure SQL-Datenbank**: In der Azure SQL-Datenbankinstanz werden die folgenden Datenbanksicherheitsmaßnahmen verwendet:
-   Die [Active Directory-Authentifizierung und -Autorisierung](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) ermöglicht die zentrale Verwaltung der Identitäten von Datenbankbenutzern und anderen Microsoft-Diensten.
-   Die [SQL-Datenbank-Überprüfung](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) verfolgt Datenbankereignisse nach und schreibt diese in ein Überwachungsprotokoll in einem Azure Storage-Konto.
-   Azure SQL-Datenbank ist so konfiguriert, dass [transparente Datenverschlüsselung](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) (Transparent Data Encryption) für die Echtzeitverschlüsselung und -entschlüsselung der Datenbank, der zugehörigen Sicherungen und der Transaktionsprotokolldateien verwendet wird, um ruhende Informationen zu schützen. Die transparente Datenverschlüsselung gewährleistet, dass gespeicherte Daten keinen unbefugten Zugriffen ausgesetzt sind.
-   [Firewallregeln](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) verhindern sämtlichen Zugriff auf Datenbankserver, bis die richtigen Berechtigungen erteilt werden. Die Firewall gewährt den Datenbankzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung.
-   Die [SQL-Bedrohungserkennung](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) ermöglicht die Erkennung und Reaktion auf potenzielle Bedrohungen, sobald sie auftreten. Dazu werden Sicherheitswarnungen bei verdächtigen Datenbankaktivitäten, potenziellen Sicherheitslücken, Angriffen durch Einschleusung von SQL-Befehlen und anormalen Mustern beim Datenbankzugriff ausgegeben. Die SQL-Bedrohungserkennung integriert Warnungen in [Azure Security Center](https://azure.microsoft.com/services/security-center/). Dabei werden Detailinformationen zu verdächtigen Aktivitäten und empfohlene Maßnahmen zur Untersuchung und Abwendung der Bedrohung bereitgestellt.
-   [Always Encrypted-Spalten](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) stellen sicher, dass vertrauliche Daten im Datenbanksystem niemals im Klartextformat angezeigt werden. Nach dem Aktivieren der Datenverschlüsselung können nur Clientanwendungen oder Anwendungsserver, die Zugriff auf die Schlüssel haben, auf Klartextdaten zugreifen.
- Die [dynamische Datenmaskierung in SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) schränkt die Offenlegung vertraulicher Daten ein, indem diese Daten für nicht berechtigte Benutzer maskiert werden. Die dynamische Datenmaskierung kann automatisch potenziell vertrauliche Daten ermitteln und eine geeignete Maskierung vorschlagen. Dies trägt zur Beschränkung des Zugriffs bei, damit sensible Daten die Datenbank nicht aufgrund von unberechtigtem Zugriff verlassen. Kunden müssen die Einstellungen für die dynamische Datenmaskierung an ihr Datenbankschema anpassen.

### <a name="identity-management"></a>Identitätsverwaltung
Kunden können lokale Active Directory-Verbunddienste für einen Verbund mit [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) verwenden, dem mehrinstanzenfähigen cloudbasierten Verzeichnis- und Identitätsverwaltungsdienst von Microsoft. [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) integriert lokale Verzeichnisse in Azure Active Directory. Alle Benutzer dieser Lösung benötigen Azure Active Directory-Konten, einschließlich der Benutzer, die auf die Azure SQL-Datenbank zugreifen. Durch die Verbundanmeldung können sich Benutzer mit ihren lokalen Anmeldeinformationen bei Azure Active Directory anmelden und bei Azure-Ressourcen authentifizieren.

Darüber hinaus unterstützen Sie die folgenden Azure Active Directory-Funktionen beim Verwalten des Zugriffs auf Daten in der Azure-Umgebung:
- Authentifizierung für die Anwendung erfolgt mithilfe von Azure Active Directory. Weitere Informationen finden Sie unter [Integrieren von Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Darüber hinaus wird für Verschlüsselung der Datenbankspalten Azure Active Directory verwendet, um die Anwendung in der Azure SQL-Datenbank zu authentifizieren. Weitere Informationen finden Sie unter [Schützen von vertraulichen Daten in SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Die [rollenbasierte Zugriffssteuerung in Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) (Role-Based Access Control) ermöglicht Administratoren das Definieren präziser Zugriffsberechtigungen, um jeweils nur den Zugriff zu gewähren, den Benutzer zum Erledigen ihrer Arbeit benötigen. Anstatt jedem Benutzer uneingeschränkte Benutzerberechtigungen für Azure-Ressourcen zu gewähren, können Administratoren auch nur bestimmte Aktionen für den Zugriff auf Daten erlauben. Der Abonnementzugriff ist auf den Abonnementadministrator beschränkt.
- Mit [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) können Kunden die Anzahl von Benutzern mit Zugriff auf bestimmte Informationen einschränken. Administratoren können mit Azure Active Directory Privileged Identity Management privilegierte Identitäten und den damit verbundenen Zugriff auf Ressourcen erkennen, einschränken und überwachen. Diese Funktionalität kann auch verwendet werden, um einen bedarfsgesteuerten Just-in-Time-Administratorzugriff zu erzwingen.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) erkennt potenzielle Sicherheitsrisiken, die sich auf die Identitäten einer Organisation auswirken, konfiguriert automatisierte Antworten auf erkannte verdächtige Aktionen, die im Zusammenhang mit Identitäten der Organisation stehen, und untersucht verdächtige Vorfälle, um die entsprechenden Aktionen zur Problembehebung ausführen zu können.

**Azure Multi-Factor Authentication**: Um Identitäten zu schützen, sollte die mehrstufige Authentifizierung implementiert werden. [Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) ist eine benutzerfreundliche, skalierbare und zuverlässige Lösung, die eine zweite Authentifizierungsmethode zum Schutz der Benutzer bietet. Azure Multi-Factor Authentication nutzt die Vorteile der Cloud und kann nahtlos in das lokale Active Directory und in benutzerdefinierte Anwendungen integriert werden. Dieser Schutz wird auf umfangreiche unternehmenskritische Szenarien erweitert.

### <a name="security"></a>Sicherheit
**Geheimnisverwaltung**: Die Lösung verwendet [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) für die Verwaltung von Schlüsseln und Geheimnissen. Azure Key Vault unterstützt Sie dabei, kryptografische Schlüssel und Geheimnisse zu schützen, die von Cloudanwendungen und -diensten verwendet werden. Die folgenden Funktionen von Azure Key Vault unterstützen Kunden beim Schützen von Daten:
- Erweiterte Zugriffsrichtlinien werden auf Bedarfsbasis konfiguriert.
- Key Vault-Zugriffsrichtlinien werden mit minimalen erforderlichen Berechtigungen für Schlüssel und Geheimnisse definiert.
- Alle Schlüssel und Geheimnisse in Key Vault besitzen ein Ablaufdatum.
- Alle Schlüssel in Key Vault werden durch spezielle Hardwaresicherheitsmodule geschützt. Der Schlüsseltyp ist ein Hardwaresicherheitsmodul, das mit einem 2.048-Bit-RSA-Schlüssel geschützt wird.
- Allen Benutzern und Identitäten werden mithilfe von rollenbasierter Zugriffssteuerung die erforderlichen Mindestberechtigungen erteilt.
- Diagnoseprotokolle für Key Vault werden mit einer Aufbewahrungsdauer von mindestens 365 Tagen aktiviert.
- Zulässige kryptographische Vorgänge für Schlüssel sind auf die erforderlichen Vorgänge beschränkt.

**Azure Security Center**: Mit [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) können Kunden Sicherheitsrichtlinien über Workloads hinweg zentral anwenden und verwalten, die Angriffsfläche verringern sowie Angriffe erkennen und darauf reagieren. Darüber hinaus greift Azure Security Center auf die vorhandenen Konfigurationen der Azure-Dienste zu, um Empfehlungen für die Konfiguration und Verwaltung zu geben und zur Verbesserung der Sicherheit und zum Schutz Daten beizutragen.

Azure Security Center verwendet eine Vielzahl von Erkennungsfunktionen, um Kunden vor potenziellen Angriffen auf ihre Umgebungen zu warnen. Diese Warnungen enthalten wichtige Informationen zum Auslöser der Warnung, zu den möglicherweise betroffenen Ressourcen und zur Quelle des Angriffs. In Azure Security Center steht eine Reihe vordefinierter [Sicherheitswarnungen](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) zur Verfügung, die ausgelöst werden, wenn eine Bedrohung oder eine verdächtige Aktivität erkannt wird. Mithilfe [benutzerdefinierter Warnungsregeln](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) in Azure Security Center können Kunden neue Sicherheitswarnungen auf der Grundlage von Daten definieren, die bereits in ihrer Umgebung gesammelt werden.

Azure Security Center bietet priorisierte Sicherheitswarnungen und Incidents, sodass es für Kunden einfacher wird, potenzielle Sicherheitsprobleme zu erkennen und zu beheben. Ein [Bericht über Bedrohungen](https://docs.microsoft.com/azure/security-center/security-center-threat-report) wird zu jeder erkannten Bedrohung generiert, der den Teams, die für die Bearbeitung von Incidents zuständig sind, beim Untersuchen und Beheben von Bedrohungen als Hilfe dient.

**Application Gateway**: Die Architektur verringert das Risiko von Sicherheitsschwachstellen durch ein Application Gateway mit Web Application Firewall und dem aktivierten OWASP-Regelsatz. Weitere Funktionen:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Aktivierte [SSL-Auslagerung](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Deaktivieren von [TLS v1.0 und v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Web Application Firewall](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (Schutzmodus)
- [Präventionsmodus](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) mit OWASP 3.0-Regelsatz
- Aktivieren der [Diagnoseprotokollierung](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Benutzerdefinierte Integritätstests](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-gateway-portal)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) und [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) bieten zusätzlichen Schutz und zusätzliche Benachrichtigungen. Azure Security Center stellt außerdem ein Reputationssystem bereit.

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

Azure Network Watcher: [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) bietet Tools für die Überwachung, Diagnose, Metrikanzeige und Aktivierung/Deaktivierung von Protokollen für Ressourcen in einem virtuellen Azure-Netzwerk.  Commonwealth-Entitäten sollten Network Watcher-Datenflussprotokolle für Netzwerksicherheitsgruppen und virtuelle Computer implementieren. Diese Protokolle sollten in einem dedizierten Speicherkonto gespeichert werden, in dem nur Sicherheitsprotokolle gespeichert werden, und der Zugriff auf das Speicherkonto sollte durch rollenbasierte Zugriffssteuerungen geschützt werden.

## <a name="threat-model"></a>Bedrohungsmodell

Das Datenflussdiagramm für diese Referenzarchitektur steht zum [Download](https://aka.ms/au-protected-paaswa-tm) bereit und ist unten angegeben. Dieses Modell kann Kunden dabei helfen, bei Änderungen die potenziellen Risikopunkte in der Systeminfrastruktur zu verstehen.

![PaaS-Webanwendung für das AU-PROTECTED-Bedrohungsmodell](images/au-protected-paaswa-threat-model.png?raw=true "Diagramm zur PaaS-Webanwendung für das AU-PROTECTED-Bedrohungsmodell")

## <a name="compliance-documentation"></a>Konformitätsdokumentation
Diese Konformitätsdokumentation wird von Microsoft basierend auf den verfügbaren Plattformen und Diensten von Microsoft erstellt. Aufgrund der vielen unterschiedlichen Kundenbereitstellungen bietet diese Dokumentation einen allgemeinen Ansatz für eine Lösung, die nur in der Azure-Umgebung gehostet wird. Kunden können andere Produkte und Dienste basierend auf ihren eigenen Betriebssystemumgebungen und Geschäftsergebnissen identifizieren und verwenden. Kunden, die sich für die Verwendung lokaler Ressourcen entscheiden, müssen die Sicherheit und die Vorgänge für diese lokalen Ressourcen berücksichtigen. Die dokumentierte Lösung kann von den Kunden angepasst werden, um die spezifischen lokalen und sicherheitsrelevanten Anforderungen zu erfüllen.

In der [Azure-Blaupause für Sicherheit und Compliance – AU-PROTECTED-Kundenzuständigkeitsmatrix](https://aka.ms/au-protected-crm) sind alle für AU-PROTECTED erforderlichen Sicherheitskontrollen aufgeführt. In dieser Matrix ist angegeben, ob die Implementierung der einzelnen Kontrollen Aufgabe von Microsoft, des Kunden oder beider Seiten ist.

Die [Azure-Blaupause für Sicherheit und Compliance – Implementierungsmatrix für die PaaS-Webanwendung für GESCHÜTZTE Daten (AU-PROTECTED)](https://aka.ms/au-protected-paaswa-cim) enthält Informationen zu den AU-PROTECTED-Kontrollen, die in der Architektur der PaaS-Webanwendung berücksichtigt werden, sowie eine ausführliche Beschreibung, wie die Implementierung die Anforderungen der einzelnen Kontrollen erfüllt.

## <a name="guidance-and-recommendations"></a>Anleitungen und Empfehlungen
### <a name="vpn-and-expressroute"></a>VPN und ExpressRoute

Für klassifizierte Daten muss ein sicherer IPSec-VPN-Tunnel konfiguriert werden, um eine sichere Verbindung mit den Ressourcen herzustellen, die als Teil dieser Referenzarchitektur einer IaaS-Webanwendung bereitgestellt werden. Durch die ordnungsgemäße Einrichtung eines IPSec-VPNs können Kunden eine zusätzliche Schutzebene für Daten während der Übertragung hinzufügen.

Durch die Implementierung eines sicheren IPSec-VPN-Tunnels mit Azure kann eine virtuelle private Verbindung zwischen einem lokalen Netzwerk und einem virtuellen Azure-Netzwerk hergestellt werden. Diese Verbindung kann über das Internet erfolgen und ermöglicht den Kunden das Übertragen von Informationen in einem sicheren „Tunnel“ innerhalb einer verschlüsselten Verbindung zwischen dem Kundennetzwerk und Azure. Eine Site-to-Site-VPN-Verbindung ist eine sichere, ausgereifte Technologie, die seit Jahrzehnten von Unternehmen aller Größen eingesetzt wird. 

Da der Datenverkehr im VPN-Tunnel über ein Site-to-Site-VPN über das Internet übertragen wird, bietet Microsoft eine Option für private Verbindungen an. Azure ExpressRoute ist eine dedizierte Verbindung zwischen Azure und einem lokalen Standort oder einem Exchange-Hostinganbiete und gilt als privates Netzwerk. Da ExpressRoute-Verbindungen nicht über das Internet erfolgen, bieten diese Verbindungen mehr Zuverlässigkeit, eine höhere Geschwindigkeit und eine geringere Latenz als herkömmliche Verbindungen über das Internet. Da es sich außerdem um eine direkte Verbindung vom Telekommunikationsanbieter des Kunden handelt, werden die Daten nicht über das Internet übertragen und dort nicht verfügbar gemacht.

Bewährte Methoden für das Implementieren eines sicheren hybriden Netzwerks, mit dem ein lokales Netzwerk auf Azure erweitert wird, finden Sie [hier](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid). 

Unabhängig davon, ob die Übertragung über das Internet oder Azure ExpressRoute erfolgt, müssen Kunden zum Schützen klassifizierter Daten das IPSec-VPN mit den folgenden Einstellungen konfigurieren:

• Der Kunden-VPN-Initiator muss für eine SA-Gültigkeitsdauer von höchstens 14400 Sekunden konfiguriert werden.
• Der Kunden-VPN-Initiator muss den aggressiven IKEv1-Modus deaktivieren.
• Der Kunden-VPN-Initiator muss Perfect Forward Secrecy (PFS) konfigurieren.
• Der Kunden-VPN-Initiator muss die Verwendung von HMAC-SHA256 oder höher konfigurieren.

Informationen zu den Konfigurationsoptionen für VPN-Geräte und IPSec-/IKE-Parameter [finden Sie hier](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="azure-active-directory-setup"></a>Azure Active Directory-Einrichtungen
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) ist wichtig für die Verwaltung der Bereitstellung von Zugriff für Mitarbeiter, die mit der Umgebung interagieren. Ein bereits vorhandenes Windows Server Active Directory-Verzeichnis kann über [vier Klicks](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express) in Azure Active Directory integriert werden.

Darüber hinaus können Kunden mit [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect) einen Verbund mit lokalen [Active Directory-Verbunddiensten]( https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-azure-adfs) und Azure Active Directory konfigurieren. Durch die Verbundanmeldung können Kunden es ihren Benutzern ermöglichen, sich mit ihren lokalen Kennwörtern bei Azure Active Directory-basierten Diensten anzumelden, ohne ihre Kennwörter erneut eingeben zu müssen, während sie im Unternehmensnetzwerk angemeldet sind. Wenn Sie die Verbundoption mit Active Directory-Verbunddiensten (AD FS) verwenden, können Sie eine neue Installation von AD FS bereitstellen oder eine vorhandene Installation in einer Windows Server 2012 R2-Farm angeben.

Um zu verhindern, dass klassifizierte Daten mit Azure Active Directory synchronisiert werden, können Kunden die in Azure Active Directory replizierten Attribute einschränken, indem in Azure Active Directory Connect die folgenden Einstellungen angewendet werden:

- [Aktivieren der Filterung](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-configure-filtering)
- [Deaktivieren der Kennworthashsynchronisierung](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)
-   [Deaktivieren der Funktion zum Kennwortrückschreiben](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
-   [Deaktivieren der Funktion zum Geräterückschreiben](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-device-writeback)
-   Übernehmen der Standardeinstellungen für die Funktion zum [Verhindern von versehentlichen Löschungen](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-feature-prevent-accidental-deletes) und für [Automatisches Upgrade](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-feature-automatic-upgrade)


## <a name="disclaimer"></a>Haftungsausschluss

 - Dieses Dokument dient nur zu Informationszwecken. MICROSOFT ÜBERNIMMT KEINE AUSDRÜCKLICHEN, STILLSCHWEIGENDEN ODER GESETZLICH VORGEGEBENEN GARANTIEN HINSICHTLICH DER INFORMATIONEN IN DIESEM DOKUMENT. Dieses Dokument wird so bereitgestellt, wie es ist. Informationen und Stellungnahmen in diesem Dokument, einschließlich URLs und anderer Verweise auf Internetwebsites, können ohne vorherige Ankündigung geändert werden. Kunden, die dieses Dokument lesen, tragen das Risiko, es zu verwenden.
 - Dieses Dokument gibt Kunden keine Rechte an geistigem Eigentum an Microsoft-Produkten oder -Lösungen.
 - Kunden dürfen dieses Dokument zu internen Referenzzwecken kopieren und verwenden.
 - Bestimmte Empfehlungen in diesem Dokument können zu einer erhöhten Nutzung von Daten, Netzwerken oder Computeressourcen in Azure sowie zu höheren Azure-Lizenz- oder -Abonnementkosten für den Kunden führen.
 - Diese Architektur soll Kunden als Grundlage für die Anpassung an ihre spezifischen Anforderungen dienen. Sie sollte nicht unverändert in einer Produktionsumgebung verwendet werden.
 - Dieses Dokument wurde als Referenz entwickelt und sollte nicht dazu verwendet werden, alle Mittel zu definieren, mit denen ein Kunde bestimmte Konformitätsanforderungen und -vorschriften erfüllen kann. Kunden sollten bei genehmigten Kundenimplementierungen rechtliche Unterstützung von ihrer Organisation einholen.
