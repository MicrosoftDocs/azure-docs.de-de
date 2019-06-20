---
title: Vorlage für Azure Security and Compliance – Hosten von PaaS-Webanwendungen für UK OFFICIAL-Workloads
description: Vorlage für Azure Security and Compliance – Hosten von PaaS-Webanwendungen für UK OFFICIAL-Workloads
services: security
author: jomolesk
ms.assetid: 446105ad-a863-44f5-a964-6ead1dac4787
ms.service: security
ms.topic: article
ms.date: 07/13/2018
ms.author: jomolesk
ms.openlocfilehash: e3ee5a0aa22d1231dca7d02a77d39e0a2b569314
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66753861"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-hosting-for-uk-official-workloads"></a>Azure-Blaupause für Sicherheit und Compliance: Hosten von PaaS-Webanwendungen für UK OFFICIAL-Workloads

## <a name="azure-security-and-compliance-blueprints"></a>Azure-Blaupausen zu Sicherheit und Compliance

Azure-Vorlagen (Blueprints) umfassen Leitfäden und Automatisierungsvorlagen, mit denen cloudbasierte Architekturen bereitgestellt werden, um Lösungen für Szenarien zu ermöglichen, für die Akkreditierungs- oder Konformitätsanforderungen gelten. Bei Azure-Vorlagen handelt es sich um Sammlungen mit Leitfäden und Automatisierungsvorlagen, mit denen Microsoft Azure-Kunden die Erreichung ihrer geschäftlichen Ziele beschleunigen können. Hierzu wird eine Architektur als Fundament bereitgestellt, die erweitert werden kann, um weitere Anforderungen zu erfüllen.

## <a name="overview"></a>Übersicht

Diese Vorlage für Azure Security and Compliance enthält Anleitungen und Automatisierungsskripts, um eine gehostete Microsoft Azure-PaaS-Webanwendungsarchitektur ([Platform-as-a-Service](https://azure.microsoft.com/overview/what-is-paas/)) bereitzustellen, die zur Verarbeitung von Workloads mit der Klassifizierung [UK OFFICIAL](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/715778/May-2018_Government-Security-Classifications-2.pdf) geeignet ist. Diese Sicherheitsklassifizierung gilt für die meisten Informationen, die von Einrichtungen des öffentlichen Sektors erstellt oder verarbeitet werden. Hierzu gehören auch routinemäßige Geschäftsvorgänge und Dienstleistungen, von denen einige schädliche Konsequenzen nach sich ziehen können, wenn sie verloren gehen, gestohlen oder in den Medien veröffentlicht werden. Das typische Bedrohungsprofil für die Klassifizierung „OFFICIAL“ ähnelt hierbei dem Fall eines privaten Unternehmens, das wertvolle Informationen und Dienstleistungen bereitstellt. Bei UK OFFICIAL wird davon ausgegangen, dass Daten oder Dienste der Regierung des Vereinigten Königreichs gegen Bedrohungen oder Kompromittierungen durch Angreifer mit begrenzten Fähigkeiten verteidigt werden müssen, z.B. Hactivists, Interessenparteien, investigative Journalisten, kompetente einzelne Hacker und die Mehrheit von kriminellen Einzelpersonen und Gruppen (keine erschöpfende Liste).

Diese Vorlage wurde vom UK National Cyber Security Centre (NCSC) überprüft und erfüllt die NCSC 14-Cloudsicherheitsprinzipien.

Für die Architektur werden Azure-[Platform-as-a-Service](https://azure.microsoft.com/overview/what-is-paas/)-Komponenten verwendet, um eine Umgebung bereitzustellen, mit der Kunden den Kostenaufwand und die Komplexität vermeiden können, die mit dem Kaufen von Softwarelizenzen und dem Verwalten der zugrunde liegenden Anwendungsinfrastruktur und Middleware oder Entwicklungstools und anderer Ressourcen verbunden ist. Kunden verwalten die Anwendungen und Dienste, die sie entwickeln, und konzentrieren sich auf die Bereitstellung des geschäftlichen Nutzens, während Microsoft Azure die anderen Azure-Ressourcen verwaltet, z.B. virtuelle Computer, Speicher und Netzwerk. So wird ein größerer Anteil der [geteilten Zuständigkeit](https://docs.microsoft.com/azure/security/security-paas-deployments#division-of-responsibility) für die Verwaltung der Infrastruktur auf die Azure Platform verlagert. [Azure App Services](https://azure.microsoft.com/services/app-service/) bietet automatische Skalierung und Hochverfügbarkeit, unterstützt Windows und Linux und ermöglicht automatisierte Bereitstellungen über GitHub, Azure DevOps oder ein anderes Git-Repository als Standarddienste. Durch die Verwendung von App Services können sich Entwickler auf die Erzielung des geschäftlichen Nutzens konzentrieren, ohne dass der Aufwand für die Verwaltung der Infrastruktur anfällt. Es ist möglich, völlig neue Java-, PHP-, Node.js-, Python-, HTML- oder C#-Webanwendungen zu erstellen oder auch vorhandene Cloud- oder lokale Webanwendungen zu Azure App Service zu migrieren (hierfür ist aber die Durchführung von sorgfältigen Prüfungen und Tests zur Sicherstellung der Leistung erforderlich).

In dieser Vorlage liegt der Schwerpunkt auf der Bereitstellung einer webbasierten [Platform-as-a-Service](https://azure.microsoft.com/overview/what-is-paas/)-Schnittstelle als sicheres Fundament für öffentliche Benutzer und Backoffice-Benutzer. Beim Entwurfsszenario dieser Vorlage wird die Nutzung von in Azure gehosteten webbasierten Diensten berücksichtigt, bei der ein öffentlicher Benutzer sensible Daten auf sichere Weise übermitteln, anzeigen und verwalten kann. Außerdem kann ein Backoffice-Bediener oder ein Mitarbeiter einer Behörde die sensiblen Daten sicher verarbeiten, die vom öffentlichen Benutzer übermittelt wurden. Anwendungsfälle für dieses Szenario können Folgendes umfassen:

- Ein Benutzer übermittelt eine Steuererklärung, und ein Mitarbeiter einer Behörde verarbeitet die Übermittlung.
- Ein Benutzer fordert eine Dienstleistung über eine webbasierte Anwendung an, und ein Backoffice-Benutzer überprüft dies und stellt die Dienstleistung bereit.
- Ein Benutzer möchte auf öffentliche zugängliche hilfreiche Informationen zur Dienstleistung einer Behörde zugreifen und diese anzeigen.

Bei Verwendung von [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-Vorlagen und von Skripts der Azure-Befehlszeilenschnittstelle stellt die Vorlage eine Umgebung bereit, die an den 14 [Cloudsicherheitsprinzipien](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) des UK National Cyber Security Centre (NCSC) und den [Critical Security Controls](https://www.cisecurity.org/critical-controls.cfm) (Kritische Sicherheitsregulierungen) des Center for Internet Security (CIS) ausgerichtet ist. Die Empfehlung des NCSC lautet, dass seine Cloudsicherheitsprinzipien von Kunden für die Beurteilung der Sicherheitseigenschaften des Diensts und zum Verstehen der Aufteilung der Zuständigkeit zwischen Kunde und Lieferant verwendet werden sollten. Microsoft hat zu jedem dieser Prinzipien Informationen zur Verfügung gestellt, die Ihnen das Verständnis der Aufteilung von Zuständigkeiten erleichtern sollen. Diese Architektur und die entsprechende Azure Resource Manager-Vorlagen werden vom Microsoft-Whitepaper [14 Cloud Security Controls for UK cloud Using Microsoft Azure](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1) (14 Sicherheitskontrollen für die UK-Cloud mithilfe von Microsoft Azure) unterstützt. Diese Architektur wurde vom NCSC überprüft und erfüllt die UK NCSC 14-Cloudsicherheitsprinzipien. Hierdurch wird es Organisationen des öffentlichen Sektors ermöglicht, Verpflichtungen in Bezug auf die Konformität schneller zu erfüllen, indem im Vereinigten Königreich und weltweit cloudbasierte Dienste über die Microsoft Azure-Cloud genutzt werden. Mit dieser Vorlage wird die Infrastruktur für den Workload bereitgestellt. Der Anwendungscode und unterstützende Software der Geschäftsschicht und der Datenschicht muss von Kunden installiert und konfiguriert werden. Ausführlichere Bereitstellungsanweisungen finden Sie [hier](https://aka.ms/ukofficial-paaswa-repo/).

Diese Vorlage dient als grundlegende Architektur. Unsere Kunden können diese Vorlage als Grundlage für ihre webbasierten Workloads mit OFFICIAL-Klassifizierung verwenden und die Vorlagen und Ressourcen um ihre eigenen Anforderungen erweitern. Diese Vorlage baut auf den Prinzipien auf, die unter [Azure Security and Compliance Blueprint: Dreischichtige IaaS-Webanwendungen für UK OFFICIAL](https://aka.ms/ukofficial-iaaswa) beschrieben sind. So können wir unseren Kunden die Wahl zwischen [IaaS- (Infrastructure-as-a-Service)](https://azure.microsoft.com/overview/what-is-iaas/) und PaaS-Implementierungen zum Hosten von webbasierten Workloads bieten.

Zum Bereitstellen dieser Vorlage benötigen Sie ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich schnell und einfach kostenlos registrieren: Erste Schritte mit Azure. Klicken Sie [hier](https://aka.ms/ukofficial-paaswa-repo/), um Anleitungen zur Bereitstellung zu erhalten.

## <a name="architecture-and-components"></a>Architektur und Komponenten

Mit dieser Vorlage wird eine Lösung zum Hosten von Webanwendungen in einer Azure-Cloudumgebung bereitgestellt, die UK OFFICIAL-Workloads unterstützt. Die Architektur stellt eine sichere Umgebung bereit, bei der Azure-Platform-as-a-Service-Funktionen verwendet werden. In der Umgebung sind zwei App Service-Web-Apps vorhanden (eine für öffentliche Benutzer und eine für Backoffice-Benutzer), und über eine API-App-Ebene werden die Unternehmensdienste für das Web-Front-End bereitgestellt. Eine Azure SQL-Datenbank wird als verwalteter relationaler Datenspeicher für die Anwendung bereitgestellt. Die Konnektivität für diese Komponenten von außerhalb der Plattform und zwischen all diesen Komponenten ist per TLS 1.2 verschlüsselt, um sicherzustellen, dass der Datenschutz während der Übertragung gewährleistet ist. Der Zugriff wird über Azure Active Directory authentifiziert.

![Hosten der PaaS-Webanwendung für UK OFFICIAL-Workloads – Diagramm zur Referenzarchitektur](images/ukofficial-paaswa-architecture.png?raw=true "Hosten der PaaS-Webanwendung für UK OFFICIAL-Workloads – Diagramm zur Referenzarchitektur")

Im Rahmen der Bereitstellungsarchitektur sind außerdem die sichere Speicherbereitstellung, Überwachung und Protokollierung, einheitliche Sicherheitsverwaltung und Advanced Threat Protection sowie Verwaltungsfunktionen vorhanden. So soll sichergestellt werden, dass Kunden über alle Tools verfügen, die sie zum Schützen und Überwachen ihrer Umgebung für diese Lösung benötigen.

Diese Lösung verwendet die folgenden Azure-Dienste. Details zur Bereitstellungsarchitektur finden Sie im Abschnitt [Bereitstellungsarchitektur](#deployment-architecture).

- Azure Active Directory
- App Service
- Web App
- API-App
- Azure DNS
- Key Vault
- Azure Monitor (Protokolle)
- Application Insights
- Azure Resource Manager
- Azure Security Center
- Azure SQL-Datenbank
- Azure Storage

## <a name="deployment-architecture"></a>Bereitstellungsarchitektur

Im folgenden Abschnitt werden die Elemente für Entwicklung und Implementierung beschrieben.

### <a name="security"></a>Sicherheit

#### <a name="identity-and-authentication"></a>Identität und Authentifizierung

Mit dieser Vorlage wird sichergestellt, dass der Zugriff auf die Ressourcen mit Diensten für die Verzeichnis- und Identitätsverwaltung geschützt ist. Bei dieser Architektur wird die [Identität vollständig als Sicherheitsbereich genutzt](https://docs.microsoft.com/azure/security/security-paas-deployments). 

Die folgenden Technologien bieten Identitätsverwaltungsfunktionen in der Azure-Umgebung:

- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) ist der mehrinstanzenfähige cloudbasierte Verzeichnis- und Identitätsverwaltungsdienst von Microsoft. Alle Benutzer für die Lösung wurden in Azure Active Directory erstellt, so auch die Benutzer, die auf die SQL-Datenbank zugreifen.
- Die Authentifizierung gegenüber der Webanwendung für den Bediener und der Zugriff zur Verwaltung der Azure-Ressourcen wird über Azure AD durchgeführt. Weitere Informationen finden Sie unter [Integrieren von Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).
- Für die Verschlüsselung der Datenbankspalten wird Azure AD verwendet, um die Anwendung für Azure SQL-Datenbank zu authentifizieren. Weitere Informationen finden Sie unter [Always Encrypted: Schützen von vertraulichen Daten in einer SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Die für Bürger bestimmte Webanwendung wird für den öffentlichen Zugriff konfiguriert. Um die Kontoerstellung und Authentifizierung per Active Directory oder einen Anbieter von Social Media-Identitäten zuzulassen, kann bei Bedarf [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) integriert werden.
- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) erkennt potenzielle Sicherheitsrisiken und risikobehaftete Konten, stellt Empfehlungen zur Verbesserung des Sicherheitsstatus der Identitäten Ihrer Organisation bereit, konfiguriert automatisierte Antworten auf erkannte verdächtige Aktionen, die im Zusammenhang mit Identitäten Ihrer Organisation stehen, untersucht verdächtige Vorfälle und führt die entsprechenden Aktionen zu deren Behebung aus.
- Die [rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) in Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) ermöglicht eine präzise Zugriffsverwaltung für Azure. Der Zugriff auf das Abonnement ist auf den Abonnementadministrator beschränkt, und der Zugriff auf Azure Key Vault ist nur auf Benutzer beschränkt, die aufgrund der Schlüsselverwaltung Zugriff benötigen.
- Durch die Nutzung des [bedingten Zugriffs mit Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) können Kunden zusätzliche Sicherheitskontrollen für den Zugriff auf Apps oder Benutzer in ihrer Umgebung basierend auf bestimmten Bedingungen erzwingen, z.B. Standort, Gerät, Status und Anmelderisiko.
- [Azure DDoS Protection](https://docs.microsoft.com/azure/security/security-paas-deployments#security-advantages-of-a-paas-cloud-service-model) in Kombination mit den bewährten Methoden für den Anwendungsentwurf ermöglicht die Verteidigung gegen DDoS-Angriffe und verfügt über Always On-Datenverkehrsüberwachung und Echtzeitabwehr von häufigen Angriffen auf Netzwerkebene. Mit einer PaaS-Architektur ist der DDoS-Schutz auf Plattformebene für den Kunden transparent und in die Plattform integriert. Es ist aber wichtig zu beachten, dass der Kunde für das Entwerfen der Anwendungssicherheit verantwortlich ist.

#### <a name="data-in-transit"></a>Daten während der Übertragung

Daten, die von außerhalb und zwischen Azure-Komponenten übertragen werden, sind per [Transport Layer Security/Secure Sockets Layer (TLS/SSL)](https://www.microsoft.com/TrustCenter/Security/Encryption) geschützt. Hierfür wird symmetrische Kryptografie basierend auf einem gemeinsamen Geheimnis genutzt, um die Kommunikation im Netzwerk zu verschlüsseln. Standardmäßig wird der Netzwerkdatenverkehr per TLS 1.2 geschützt.

#### <a name="security-and-malware-protection"></a>Sicherheit und Schutz vor Schadsoftware

[Azure Security Center](https://azure.microsoft.com/services/security-center/) bietet eine zentrale Ansicht der Sicherheitsstatus sämtlicher Azure-Ressourcen. Sie können auf einen Blick überprüfen, ob die erforderlichen Sicherheitskontrollfunktionen implementiert und ordnungsgemäß konfiguriert sind, und schnell Ressourcen ermitteln, die Ihre Aufmerksamkeit erfordern.

Bei [Azure-Ratgeber](https://docs.microsoft.com/azure/advisor/advisor-overview) handelt es sich um einen personalisierten Cloudberater, der Sie mit bewährten Methoden zum Optimieren von Azure-Bereitstellungen unterstützt. Das Tool analysiert die Konfiguration Ihrer Ressourcen und Telemetriedaten zur Nutzung und macht anschließend Vorschläge, wie Sie die Wirtschaftlichkeit, Leistung, Hochverfügbarkeit und Sicherheit Ihrer Azure-Ressourcen steigern können.

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) ist eine Echtzeit-Schutzfunktion zum Bestimmen und Entfernen von Viren, Spyware und anderer Schadsoftware. Sie wird standardmäßig in der zugrunde liegenden PaaS-VM-Infrastruktur installiert und über die Azure-Fabric auf transparente Weise für den Kunden verwaltet.

### <a name="paas-services-in-this-blueprint"></a>PaaS-Dienste in dieser Vorlage

#### <a name="azure-app-service"></a>Azure App Service

Azure App Service stellt eine vollständig verwaltete Webhostingumgebung für Webanwendungen bereit, die in Java, PHP, Node.js, Python, HTML und C# entwickelt werden, ohne dass die Infrastruktur verwaltet werden muss. Der Dienst bietet automatische Skalierung und Hochverfügbarkeit, unterstützt Windows und Linux und ermöglicht automatisierte Bereitstellungen über [Azure DevOps](https://azure.microsoft.com/services/visual-studio-team-services/) oder ein anderes Git-Repository.

App Service ist [mit ISO, SOC und PCI konform](https://www.microsoft.com/TrustCenter/) und kann Benutzer per [Azure Active Directory](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad) oder Anmeldung für soziale Netzwerke ([Google](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-google), [Facebook](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-facebook), [Twitter](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-twitter) und [Microsoft-Authentifizierung](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-microsoft)) authentifiziert werden.

Die Pläne „Basic“, „Standard“ und „Premium“ eignen sich für Produktionsworkloads und werden auf dedizierten virtuellen Computerinstanzen ausgeführt. Jede Instanz kann mehrere Anwendungen und Domänen unterstützen. Außerdem unterstützt App Service [Einschränkungen für IP-Adressen](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions), um den Datenverkehr für vertrauenswürdige IP-Adressen zu schützen, falls dies erforderlich ist. Darüber hinaus werden die [verwalteten Identitäten für Azure-Ressourcen](https://docs.microsoft.com/azure/app-service/overview-managed-identity) unterstützt, um für eine sichere Verbindung mit anderen PaaS-Diensten zu sorgen, z. B. [Key Vault](https://azure.microsoft.com/services/key-vault/) und [Azure SQL-Datenbank](https://azure.microsoft.com/services/sql-database/). Wenn zusätzliche Sicherheit benötigt wird, werden Ihre Apps unter dem Isolated-Plan in einer privaten, dedizierten Azure-Umgebung gehostet. Er eignet sich ideal für Apps, die eine sichere Verbindung mit dem lokalen Netzwerk oder zusätzliche Leistung und Skalierung erfordern.

Mit dieser Vorlage werden die folgenden App Service-Features bereitgestellt:

- App Service-Plantarif [Standard](https://docs.microsoft.com/azure/app-service/overview-hosting-plans)
- Mehrere App Service-[Bereitstellungsslots](https://docs.microsoft.com/azure/app-service/deploy-staging-slots): Dev, Vorschauversion, QA, UAT und natürlich Produktion (Standardslot).
- [Verwaltete Identitäten für Azure-Ressourcen](https://docs.microsoft.com/azure/app-service/overview-managed-identity) für die Verbindungsherstellung mit [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) (kann auch verwendet werden, um Zugriff auf [Azure SQL-Datenbank](https://azure.microsoft.com/services/sql-database/) zu ermöglichen) 
- Integration mit [Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-azure-web-apps) zur Überwachung der Leistung
- [Diagnoseprotokolle](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) 
- [Metrikwarnungen](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) 
- [Azure API-Apps](https://azure.microsoft.com/services/app-service/api/) 

#### <a name="azure-sql-database"></a>Azure SQL-Datenbank

SQL-Datenbank ist ein relationaler verwalteter Datenbankdienst in Microsoft Azure für allgemeine Zwecke, der Strukturen wie relationale Daten, JSON, räumliche Daten und XML unterstützt. SQL-Datenbank verfügt über verwaltete SQL-Einzeldatenbanken, verwaltete SQL-Datenbanken in einem [Pool für elastische Datenbanken](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool) sowie [verwaltete SQL-Instanzen](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) (Public Preview). Sie bietet eine [dynamisch skalierbare Leistung](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers) und stellt Optionen wie z.B. [Columnstore-Indizes](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) für extrem umfangreiche Analysen und Berichte und [In-Memory-OLTP](https://docs.microsoft.com/azure/sql-database/sql-database-in-memory) für aufwendigste Transaktionsverarbeitungen bereit. Microsoft kümmert sich um sämtliche Patches und Updates der SQL-Codebasis und übernimmt damit die Verwaltung der gesamten zugrunde liegenden Infrastruktur.

Azure SQL-Datenbank in dieser Vorlage

In der Azure SQL-Datenbankinstanz werden die folgenden Datenbanksicherheitsmaßnahmen verwendet:

- [Firewallregeln auf Server- oder Datenbankebene](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) oder über [VNET-Dienstendpunkte](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) mit [VNET-Regeln](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview).
- [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) trägt zum Schutz von Azure SQL-Datenbank und Azure Data Warehouse vor der Bedrohung durch schädliche Aktivitäten bei. TDE ver- und entschlüsselt die Datenbank, die zugehörigen Sicherungen und die Transaktionsprotokolldateien im Ruhezustand in Echtzeit, ohne dass Änderungen an der Anwendung erforderlich sind.
- Mithilfe der [Azure AD-Authentifizierung](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) können Sie die Identitäten von Datenbankbenutzern und anderen Microsoft-Diensten an einem zentralen Ort verwalten. Die zentrale ID-Verwaltung ermöglicht eine einheitliche Verwaltung von Datenbankbenutzern und vereinfacht die Berechtigungsverwaltung.
- Verwenden von Azure Active Directory für die Datenbankverwaltung
- [Überwachungsprotokolle](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) für Speicherkonten
- [Metrikwarnungen](https://docs.microsoft.com/azure/application-insights/app-insights-alerts) für fehlgeschlagene DB-Verbindungen
- [SQL-Bedrohungserkennung](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)
- [Always Encrypted-Spalten](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

### <a name="azure-storage"></a>Azure Storage

Microsoft [Azure Storage](https://azure.microsoft.com/services/storage/) ist ein von Microsoft verwalteter Clouddienst, über den hoch verfügbarer, sicherer, stabiler, skalierbarer und redundanter Speicher bereitgestellt wird. Azure Storage besteht aus Blob Storage, File Storage und Queue Storage.

#### <a name="azure-storage-in-this-blueprint"></a>Azure Storage in dieser Vorlage

In dieser Vorlage werden die folgenden Azure Storage-Komponenten genutzt:

- [Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 
- Nur HTTPS-Verbindungen zulassen

#### <a name="data-at-rest"></a>Ruhende Daten

Bei der [Speicherdienstverschlüsselung](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) werden sämtliche in Azure Storage geschriebenen Daten per 256-Bit-AES-Verschlüsselung verschlüsselt. Dies ist eines der sichersten verfügbaren Blockverschlüsselungsverfahren. Mit SSE können von Microsoft verwaltete oder [eigene Verschlüsselungsschlüssel](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys) verwendet werden.

Speicherkonten können mit [VNET-Dienstendpunkten](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) geschützt werden, indem [VNET-Regeln](https://docs.microsoft.com/azure/storage/common/storage-network-security) verwendet werden.

Ausführliche Informationen zum Schützen von Azure Storage finden Sie im [Sicherheitsleitfaden](https://docs.microsoft.com/azure/storage/common/storage-security-guide).


### <a name="secrets-management"></a>Verwaltung geheimer Schlüssel

#### <a name="azure-key-vault"></a>Azure Key Vault

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) wird zum Schützen von Anwendungsschlüsseln und Geheimnissen verwendet, um sicherzustellen, dass Dritte nicht darauf zugreifen können. Key Vault ist jedoch nicht als Speicher für Benutzerkennwörter vorgesehen. Sie können mehrere sichere Container (so genannte Tresore) erstellen. Diese Tresore basieren auf Hardwaresicherheitsmodulen (HSMs). Tresore zentralisieren die Speicherung von Anwendungsgeheimnissen und verringern so die Gefahr, dass Sicherheitsinformationen abhandenkommen. Darüber hinaus steuern und protokollieren Key Vault-Instanzen auch den Zugriff auf alle darin gespeicherten Daten. Azure Key Vault kann Anforderungen und Verlängerungen von TLS-Zertifikaten (Transport Layer Security) abwickeln und stellt Features bereit, die für eine zuverlässige Zertifikatlebenszyklus-Verwaltungslösung benötigt werden.

#### <a name="azure-key-vault-in-this-blueprint"></a>Azure Key Vault in dieser Vorlage

- Enthält den Speicherzugriffsschlüssel, und die [verwaltete Identität](https://docs.microsoft.com/azure/app-service/overview-managed-identity) der für den Kunden bestimmten Web-App verfügt über Lesezugriff
- Enthält das SQL Server-DBA-Kennwort (in einem separaten Tresor)
- Diagnoseprotokollierung

### <a name="monitoring-logging-and-audit"></a>Überwachung, Protokollierung und Auditing

#### <a name="azure-monitor-logs"></a>Azure Monitor-Protokolle

[Azure Monitor-Protokolle](https://azure.microsoft.com/services/log-analytics/) ist ein Dienst in Azure, mit dem Sie Daten sammeln und analysieren können, die von Ressourcen in Ihren Cloud- und lokalen Umgebungen generiert werden.

#### <a name="azure-monitor-logs-in-this-blueprint"></a>Azure Monitor-Protokolle in dieser Vorlage

- SQL Assessment
- Key Vault-Diagnose
- Application Insights-Verbindung
- Azure-Aktivitätsprotokoll

#### <a name="application-insights"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) ist ein erweiterbarer, für Webentwickler konzipierter Dienst zur Verwaltung der Anwendungsleistung (Application Performance Management, APM) auf mehreren Plattformen. Wird zum Überwachen von Live-Webanwendungen verwendet und erkennt automatisch Leistungsanomalien, analysiert die Leistung, diagnostiziert Probleme und liefert Informationen dazu, wie Benutzer mit der App interagieren. Application Insights kann auf Plattformen bereitgestellt werden (z.B. .NET, Node.js und Java EE), die lokal oder in der Cloud gehostet werden. Der Dienst lässt sich in Ihren DevOps-Prozess integrieren und verfügt über Verbindungspunkte mit einer Vielzahl von Entwicklungstools.

#### <a name="application-insights-in-this-blueprint"></a>Application Insights in dieser Vorlage

In dieser Vorlage werden die folgenden Application Insights-Komponenten verwendet:

- Application Insights-Dashboard pro Standort (Bediener, Kunde und API)

#### <a name="azure-activity-logs"></a>Azure-Aktivitätsprotokolle

Mit [Azure-Aktivitätsprotokollen](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview) werden Ereignisse auf Steuerungsebene für Ihre Abonnements überwacht. Mit dem Aktivitätsprotokoll können Sie die Antworten auf die Fragen „Was“, „Wer“ und „Wann“ für alle Schreibvorgänge (PUT, POST, DELETE) ermitteln, die für die Ressourcen Ihres Abonnements durchgeführt wurden. Sie können auch den Status des Vorgangs und andere relevante Eigenschaften verstehen.

#### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) ermöglicht Azure-Diensten die Kernüberwachung durch die Sammlung von Metriken, Aktivitätsprotokollen und Diagnoseprotokollen. Azure Monitor stellt grundlegende Infrastrukturmetriken und Protokolle für die meisten Dienste in Microsoft Azure bereit.

## <a name="threat-model"></a>Bedrohungsmodell

Das Datenflussdiagramm für diese Referenzarchitektur steht zum [Download](https://aka.ms/ukofficial-paaswa-tm) bereit und ist unten angegeben. Dieses Modell kann Kunden dabei helfen, bei Änderungen die potenziellen Risikopunkte in der Systeminfrastruktur zu verstehen.

![Hosten von PaaS-Webanwendungen für UK OFFICIAL-Workloads – Bedrohungsmodell](images/ukofficial-paaswa-threat-model.png?raw=true "Hosten von PaaS-Webanwendungen für UK OFFICIAL-Workloads – Bedrohungsmodell")

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>NCSC-Cloudsicherheitsprinzipien – Dokumentation der Compliance

Der Crown Commercial Service (eine Agentur, die an der Verbesserung der Handels- und Beschaffungsaktivitäten der Regierung arbeitet) hat die Klassifizierung der in den Bereich fallenden Unternehmensclouddienste von Microsoft neu als G-Cloud v6 festgelegt und dabei alle Angebote von Microsoft auf der Stufe OFFICIAL berücksichtigt. Details zu Azure und G-Cloud finden Sie im [Azure UK G-Cloud security assessment summary](https://www.microsoft.com/trustcenter/compliance/uk-g-cloud) (Zusammenfassung der Sicherheitsbewertung von Azure UK G-Cloud).

Diese Vorlage ist an den 14 Prinzipien zur Cloudsicherheit ausgerichtet, die im Abschnitt mit den [Cloudsicherheitsprinzipien](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) der NCSC dokumentiert sind, um eine Umgebung bereitzustellen, die als UK OFFICIAL klassifizierte Workloads unterstützt.

Die [Vorlage für Azure Security and Compliance – UK OFFICIAL – Kundenzuständigkeitsmatrix](https://aka.ms/ukofficial-crm) (Excel-Arbeitsmappe) listet alle 14 Prinzipien der Cloudsicherheit auf und gibt für jedes Prinzip (oder jeden Teilbereich eines Prinzips) an, ob die Implementierung des Prinzips in die Zuständigkeit von Microsoft oder in die des Kunden fällt oder ob sie von beiden geteilt wird.

Die [Vorlage für Azure Security and Compliance – PaaS-Webanwendung für UK OFFICIAL – Prinzipimplementierungsmatrix](https://aka.ms/ukofficial-paaswa-pim) (Excel-Arbeitsmappe) listet alle 14 Prinzipien der Cloudsicherheit auf und gibt für jedes Prinzip (oder jeden Teilbereich eines Prinzips), für das in der Kundenzuständigkeitsmatrix der Kunde als verantwortliche Stelle vermerkt ist, an, ob das Prinzip durch die Vorlage implementiert wird. Außerdem ist eine Beschreibung dazu enthalten, wie die Implementierung die Anforderung(en) des Prinzips abdeckt.  

Darüber hinaus hat die CSA (Cloud Security Alliance) die Cloud Control Matrix veröffentlicht, um Kunden bei der Bewertung von Cloudanbietern zu unterstützen und ihnen die Ermittlung der Fragen zu ermöglichen, die vor der Umstellung auf Clouddienste beantwortet werden sollten. Als Antwort darauf hat Microsoft Azure die Fragen des CSA Consensus Assessment Initiative Questionnaire ([CSA CAIQ](https://www.microsoft.com/TrustCenter/Compliance/CSA)) beantwortet und beschrieben, wie Microsoft den vorgeschlagenen Prinzipien Rechnung trägt.

## <a name="third-party-assessment"></a>Drittanbieterbewertung

Diese Vorlage wurde vom UK National Cyber Security Centre (NCSC) überprüft und erfüllt die NCSC 14-Cloudsicherheitsprinzipien.

Die Automatisierungsvorlagen wurden vom UK Customer Success Unit Azure Cloud Solution Architect-Team und von unserem Microsoft-Partner [Ampliphae](https://www.ampliphae.com/) getestet.


## <a name="deploy-the-solution"></a>Bereitstellen der Lösung

Diese Azure Security and Compliance Blueprint-Automatisierung besteht aus JSON-Konfigurationsdateien und PowerShell-Skripts, die vom API-Dienst von Azure Resource Manager verwaltet werden, um Ressourcen innerhalb von Azure bereitzustellen. Ausführlichere Bereitstellungsanweisungen finden Sie [hier](https://aka.ms/ukofficial-paaswa-repo).

Es gibt drei Ansätze für die Bereitstellung: Einen einfachen Expressansatz per [Azure CLI 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), der für die schnelle Erstellung einer Testumgebung geeignet ist, einen Ansatz per [Azure CLI 2](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) mit Parametrisierung, der eine bessere Konfiguration für Workloadumgebungen ermöglicht, und eine auf dem Azure-Portal basierende Bereitstellung, bei der der Bediener die Bereitstellungsparameter über das Azure-Portal angeben kann. 

1.  Klonen oder laden Sie [dieses](https://aka.ms/ukofficial-paaswa-repo) GitHub-Repository auf die lokale Arbeitsstation herunter.
2.  Lesen Sie [Method 1: Azure CLI 2 (Express version)](https://aka.ms/ukofficial-paaswa-repo/#method-1-azure-cli-2-express-version), und führen Sie die bereitgestellten Befehle aus.
3.  Lesen Sie [Method 1a: Azure CLI 2 (Configuring the deployment via script arguments)](https://aka.ms/ukofficial-paaswa-repo/#method-1a-azure-cli-2-configuring-the-deployment-via-script-arguments), und führen Sie die bereitgestellten Befehle aus.
4.  Lesen Sie [Method 2: Azure Portal Deployment Process](https://aka.ms/ukofficial-paaswa-repo/#method-2-azure-portal-deployment-process), und führen Sie die aufgeführten Befehle aus.

## <a name="guidance-and-recommendations"></a>Anleitungen und Empfehlungen

### <a name="api-management"></a>API Management

[Azure API Management](https://azure.microsoft.com/services/api-management/) kann vor dem API App Service verwendet werden, um zusätzliche Sicherheitsebenen, Drosselung und Kontrollen zum Verfügbarmachen, Vorschalten eines Proxys und Schützen von APIs bereitzustellen.

### <a name="azure-b2c"></a>Azure B2C

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) kann als Kontrollmechanismus implementiert werden, damit sich Benutzer registrieren, eine Identität erstellen und die Autorisierung und Zugriffssteuerung für die öffentliche Webanwendung verwenden können.

## <a name="disclaimer"></a>Haftungsausschluss

- Dieses Dokument dient nur zu Informationszwecken. MICROSOFT ÜBERNIMMT KEINE AUSDRÜCKLICHEN, STILLSCHWEIGENDEN ODER GESETZLICH VORGEGEBENEN GARANTIEN HINSICHTLICH DER INFORMATIONEN IN DIESEM DOKUMENT. Dieses Dokument wird so bereitgestellt, wie es ist. Informationen und Stellungnahmen in diesem Dokument, einschließlich URLs und anderer Verweise auf Internetwebsites, können ohne vorherige Ankündigung geändert werden. Kunden, die dieses Dokument lesen, tragen das Risiko, es zu verwenden.
- Dieses Dokument gibt Kunden keine Rechte an geistigem Eigentum an Microsoft-Produkten oder -Lösungen.
- Kunden dürfen dieses Dokument zu internen Referenzzwecken kopieren und verwenden.
- Bestimmte Empfehlungen in diesem Dokument können zu einer erhöhten Nutzung von Daten, Netzwerken oder Computeressourcen in Azure sowie zu höheren Azure-Lizenz- oder -Abonnementkosten für den Kunden führen.
- Diese Architektur soll Kunden als Grundlage für die Anpassung an ihre spezifischen Anforderungen dienen. Sie sollte nicht unverändert in einer Produktionsumgebung verwendet werden.
- Dieses Dokument wurde als Referenz entwickelt und sollte nicht dazu verwendet werden, alle Mittel zu definieren, mit denen ein Kunde bestimmte Konformitätsanforderungen und -vorschriften erfüllen kann. Kunden sollten bei genehmigten Kundenimplementierungen rechtliche Unterstützung von ihrer Organisation einholen.
