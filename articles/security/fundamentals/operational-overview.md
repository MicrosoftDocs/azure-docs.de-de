---
title: Azure Operational Security – Übersicht | Microsoft-Dokumentation
description: In dieser Übersicht erfahren Sie mehr über die Betriebssicherheit in Azure. Die Betriebssicherheit umfasst Dienste, Steuerelemente und Features für den Schutz von Assets.
services: security
documentationcenter: na
author: unifycloud
manager: rkarlin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: tomsh
ms.openlocfilehash: 9dcb1113bb37cb0f5f65c54ae9d6ca76a88330c1
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698664"
---
# <a name="azure-operational-security-overview"></a>Azure Operational Security – Übersicht

Die [Azure-Betriebssicherheit](./operational-security.md) umfasst alle Dienste, Steuerelemente und Features, die Benutzern zum Schützen ihrer Daten, Anwendungen und anderen Ressourcen in Microsoft Azure zur Verfügung stehen. Dieses Framework umfasst das gesamte Wissen, das Microsoft durch eine Vielzahl einzigartiger Funktionen erworben hat. Zu diesen Funktionen gehören Microsoft Security Development Lifecycle (SDL), das Microsoft Security Response Center-Programm und umfassende Kenntnisse zur Bedrohungslandschaft im Bereich Cybersicherheit.

## <a name="azure-management-services"></a>Azure-Verwaltungsdienste

Das IT-Betriebsteam ist für die Verwaltung der Rechenzentrumsinfrastruktur, der Anwendungen und der Daten verantwortlich, also auch für die Stabilität und Sicherheit dieser Systeme. Für die Gewinnung von Erkenntnissen zur Sicherheit in immer komplexeren IT-Umgebungen müssen Organisationen aber häufig Daten aus mehreren Sicherheits- und Verwaltungssystemen zusammentragen.

[Microsoft Azure Monitor-Protokolle](../../azure-monitor/overview.md) ist eine cloudbasierte IT-Verwaltungslösung, die Ihnen die Verwaltung und den Schutz Ihrer lokalen und cloudbasierten Infrastruktur erleichtert. Die Kernfunktionen werden durch die folgenden Dienste bereitgestellt, die in Azure ausgeführt werden. Azure umfasst eine Vielzahl von Diensten, mit denen Sie Ihre lokale und cloudbasierte Infrastruktur verwalten und schützen können. Jeder Dienst stellt eine bestimmte Verwaltungsfunktion bereit. Sie können mehrere Dienste kombinieren, um verschiedene Verwaltungsszenarien umzusetzen. 

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](../../azure-monitor/overview.md) erfasst Daten aus verwalteten Quellen in zentralen Datenspeichern. Bei diesen Daten kann es sich um Ereignisse, Leistungsdaten oder benutzerdefinierte Daten handeln, die über die API bereitgestellt wurden. Die gesammelten Daten können für Warnungen und Analysen genutzt und exportiert werden.

Sie können Daten aus einer Vielzahl von Quellen zusammenführen und Daten aus Ihren Azure-Diensten mit Ihrer vorhandenen lokalen Umgebung kombinieren. Azure Monitor-Protokolle trennt zudem die Datensammlung klar von der Aktion, die für diese Daten ausgeführt wird, sodass alle Aktionen für alle Arten von Daten verfügbar sind.

### <a name="automation"></a>Automation

[Azure Automation](../../automation/automation-intro.md) ermöglicht es Benutzern, die manuellen, langwierigen, fehleranfälligen und häufig wiederholten Aufgaben zu automatisieren, die in einer Cloud- und Unternehmensumgebung normalerweise ausgeführt werden. Der Dienst spart Zeit und verbessert die Zuverlässigkeit administrativer Aufgabe. Der Dienst kann solche Aufgaben auch planen, sodass sie in regelmäßigen Abständen automatisch ausgeführt werden. Sie können Prozesse mithilfe von Runbooks oder die Konfigurationsverwaltung mithilfe der Konfiguration für den gewünschten Zustand automatisieren.

### <a name="backup"></a>Backup

[Azure Backup](../../backup/backup-overview.md) ist der Azure-basierte Dienst, den Sie zum Sichern (bzw. Schützen) und Wiederherstellen Ihrer Daten in der Microsoft Cloud verwenden können. Azure Backup ersetzt Ihre vorhandene lokale bzw. standortexterne Sicherungslösung durch eine zuverlässige, sichere und wirtschaftliche Cloudlösung.

Azure Backup bietet verschiedene Komponenten, die Sie herunterladen und auf dem jeweiligen Computer, Server oder in der Cloud bereitstellen. Die Komponente (der Agent), die Sie bereitstellen, richtet sich danach, was geschützt werden soll. Alle Azure Backup-Komponenten (unabhängig davon, ob Daten lokal oder in der Cloud geschützt werden sollen) können genutzt werden, um Daten in einem Azure Recovery Services-Tresor in Azure zu sichern.

Weitere Informationen finden Sie in der [Tabelle mit den Azure Backup-Komponenten](../../backup/backup-overview.md#what-can-i-back-up).

### <a name="site-recovery"></a>Site Recovery

[Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery) bietet Geschäftskontinuität durch Orchestrierung der Replikation von lokalen virtuellen und physischen Computern in Azure oder an einem sekundären Standort. Falls Ihr primärer Standort nicht verfügbar ist, wird ein Failover auf den sekundären Standort durchgeführt, damit die Benutzer weiterarbeiten können. Sind die Systeme wieder verfügbar, erfolgt ein Failback. Verwenden Sie Azure Security Center, um eine intelligentere und effektivere Bedrohungserkennung vorzunehmen.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD)](../../active-directory/manage-apps/what-is-application-management.md) ist ein umfassender Identitätsdienst, der Folgendes bietet:

-   Er ermöglicht die Identitäts- und Zugriffsverwaltung (Identity & Access Management, IAM) in Azure.
-   Er stellt eine zentrale Zugriffsverwaltung, SSO-Funktionalität für einmaliges Anmelden und Berichterstellung bereit.
-   Er unterstützt die integrierte Zugriffsverwaltung für [Tausende von Anwendungen](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory) im Azure Marketplace, einschließlich Salesforce, Google Apps, Box und Concur.

Azure AD enthält auch eine vollständige Suite mit [Funktionen zur Identitätsverwaltung](./identity-management-overview.md#security-monitoring-alerts-and-machine-learning-based-reports), wie z.B. die folgenden:

- [Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)
- [Self-service password management (Self-Service-Kennwortverwaltung)](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Self-Service-Gruppenverwaltung](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md)
- [Verwaltung privilegierter Konten](../../active-directory/privileged-identity-management/pim-configure.md)
- [Rollenbasierte Zugriffssteuerung von Azure (Azure-RBAC)](../../role-based-access-control/overview.md)
- [Überwachung der Anwendungsnutzung](../../active-directory/hybrid/whatis-hybrid-identity.md)
- [Umfassende Überwachung](../../active-directory/reports-monitoring/concept-audit-logs.md)
- [Sicherheitsüberwachung und -warnungen](../../security-center/security-center-managing-and-responding-alerts.md)

Mit Azure Active Directory können Sie für alle Anwendungen, die Sie für Ihre Partner und Kunden (Geschäftskunden oder Endverbraucher) veröffentlichen, dieselben Identitäts- und Zugriffsverwaltungsfunktionen verwenden.  Dadurch können Sie die Betriebskosten erheblich reduzieren.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../../security-center/security-center-introduction.md) unterstützt Sie durch mehr Transparenz und bessere Kontrolle über die Sicherheit Ihrer Azure-Ressourcen dabei, Bedrohungen zu erkennen, zu verhindern und zu beheben. Der Dienst bietet eine integrierte Sicherheitsüberwachung und Richtlinienverwaltung für all Ihre Abonnements. Er unterstützt Sie bei der Erkennung von Bedrohungen, die andernfalls möglicherweise unbemerkt bleiben, und kann gemeinsam mit einem umfassenden Spektrum von Sicherheitslösungen verwendet werden.

[Schützen Sie die Daten von virtuellen Computern](../../security-center/security-center-introduction.md) in Azure, indem Sie Einblicke in die Sicherheitseinstellungen Ihrer virtuellen Computer erhalten und die Computer auf Bedrohungen überwachen. Security Center kann auf Ihren virtuellen Computern Folgendes überwachen:

- Sicherheitseinstellungen des Betriebssystems mit den empfohlenen Konfigurationsregeln
- Systemsicherheit und fehlende kritische Updates
- Empfehlungen zum Endpunktschutz
- Validierung der Datenträgerverschlüsselung
- Netzwerkbasierte Angriffe

Security Center verwendet die [rollenbasierte Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/role-assignments-portal.md). Azure RBAC stellt [integrierte Rollen](../../role-based-access-control/built-in-roles.md) bereit, die Benutzern, Gruppen und Diensten in Azure zugewiesen werden können.

Security Center bewertet die Konfiguration Ihrer Ressourcen, um die Sicherheitsprobleme und Sicherheitsrisiken zu identifizieren. In Security Center werden Ihnen nur dann Informationen zu einer Ressource angezeigt, wenn Ihnen für das Abonnement oder die Ressourcengruppe, zu der eine Ressource gehört, die Rolle „Besitzer“, „Mitwirkender“ oder „Leser“ zugewiesen ist.

>[!Note]
>Informationen zu Rollen und zulässigen Aktionen in Security Center finden Sie unter [Berechtigungen in Azure Security Center](../../security-center/security-center-permissions.md).

Security Center verwendet den Microsoft Monitoring Agent. Dies ist der gleiche Agent, den auch der Azure Monitor-Dienst nutzt. Die von diesem Agent gesammelten Daten werden entweder in einem vorhandenen Log Analytics-[Arbeitsbereich](../../azure-monitor/platform/manage-access.md), der mit Ihrem Azure-Abonnement verknüpft ist, oder unter Berücksichtigung des geografischen Standorts des virtuellen Computers in einem neuen Arbeitsbereich gespeichert.

## <a name="azure-monitor"></a>Azure Monitor

Leistungsprobleme in Ihrer Cloud-App können Ihr Unternehmen beeinträchtigen. Durch mehrere miteinander verbundene Komponenten und häufige Versionswechsel können Leistungseinbußen jederzeit vorkommen. Bei der Entwicklung einer App werden von Ihren Benutzern in der Regel Probleme entdeckt, die Sie beim Testen nicht gefunden haben. Sie sollten sofort über diese Probleme Bescheid wissen und über Tools zu deren Diagnose und Beseitigung verfügen.

[Azure Monitor](../../azure-monitor/overview.md) ist ein grundlegendes Tool zum Überwachen von Diensten, die in Azure ausgeführt werden. Dieses Tool bietet Ihnen Daten auf Infrastrukturebene über den Durchsatz eines Diensts und über dessen Umgebung. Wenn Sie Ihre Apps vollständig in Azure verwalten und entscheiden müssen, ob Sie Ihre Ressourcen zentral hoch- oder herunterskalieren, bietet Azure Monitor den richtigen Ausgangspunkt.

Sie können anhand der Überwachungsdaten auch umfassende Erkenntnisse zu Ihrer Anwendung gewinnen. Mithilfe dieser Kenntnisse können Sie die Leistung oder Wartungsfreundlichkeit der Anwendung verbessern oder Aktionen automatisieren, die andernfalls manuell ausgeführt werden müssten.

Azure Monitor enthält die folgenden Komponenten.

### <a name="azure-activity-log"></a>Azure-Aktivitätsprotokoll

Das [Azure-Aktivitätsprotokoll](../../azure-monitor/platform/platform-logs-overview.md) bietet Einblicke in die Vorgänge, die für Ressourcen in Ihrem Abonnement durchgeführt wurden. Das Aktivitätsprotokoll wurde bisher als „Überwachungsprotokoll“ oder „Vorgangsprotokoll“ bezeichnet, da es Ereignisse der Steuerungsebene für Ihre Abonnements enthält.

### <a name="azure-diagnostic-logs"></a>Azure-Diagnoseprotokolle

[Azure-Diagnoseprotokolle](../../azure-monitor/platform/platform-logs-overview.md) werden von einer Ressource ausgegeben und stellen umfangreiche und in kurzen Abständen erfasste Betriebsdaten der Ressource bereit. Der Inhalt dieser Protokolle variiert je nach Ressourcentyp.

Windows-Ereignissystemprotokolle sind eine Kategorie von Diagnoseprotokollen für virtuelle Computer. Blob-, Tabellen- und Warteschlangenprotokolle sind Kategorien von Diagnoseprotokollen für Speicherkonten.

Diagnoseprotokolle unterscheiden sich vom [Aktivitätsprotokoll](../../azure-monitor/platform/platform-logs-overview.md). Das Aktivitätsprotokoll bietet Einblicke in Vorgänge, die für Ressourcen Ihres Abonnements durchgeführt wurden. Diagnoseprotokolle bieten Einblicke in Vorgänge, die Ihre Ressource selbst ausgeführt hat.

### <a name="metrics"></a>Metriken

Azure Monitor stellt Telemetriedaten bereit, mit denen Sie sich einen Überblick über die Leistung und Integrität Ihrer Workloads in Azure verschaffen können. Die wichtigsten Arten von Azure-Telemetriedaten sind [Metriken](../../azure-monitor/platform/data-platform.md) (auch Leistungsindikatoren genannt), die von den meisten Azure-Ressourcen gesendet werden. Azure Monitor bietet Ihnen verschiedene Möglichkeiten, diese Metriken für die Überwachung und Problembehandlung zu konfigurieren und zu nutzen.

### <a name="azure-diagnostics"></a>Azure-Diagnose

Die Azure-Diagnose ermöglicht die Sammlung von Diagnosedaten für eine bereitgestellte Anwendung. Sie können die Diagnoseerweiterung von einer Reihe von Quellen aus verwenden. Derzeit werden [Rollen für den Azure-Clouddienst](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service), [virtuelle Azure-Computer](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service) unter Microsoft Windows und [Azure Service Fabric](../../azure-monitor/platform/diagnostics-extension-overview.md) unterstützt.

## <a name="azure-network-watcher"></a>Azure Network Watcher

Kunden erstellen ein End-to-End-Netzwerk in Azure, indem sie verschiedene Netzwerkressourcen wie z.B. virtuelle Netzwerke, Azure ExpressRoute, Azure Application Gateway und Load Balancer orchestrieren und kombinieren. Überwachung steht für jede der Netzwerkressourcen zur Verfügung.

Das End-to-End-Netzwerk kann komplexe Konfigurationen und Interaktionen zwischen Ressourcen aufweisen. Das Ergebnis sind komplizierte Szenarien, die eine szenariobasierte Überwachung über [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) erfordern.

Network Watcher vereinfacht die Überwachung und Diagnose Ihres Azure-Netzwerks. Sie können die Diagnose- und Visualisierungstools in Network Watcher für Folgendes verwenden:

- Remotepaketerfassung auf einem virtuellen Azure-Computer
- Gewinnen von Einblicken in den Netzwerkdatenverkehr mithilfe von Datenflussprotokollen
- Diagnostizieren von Azure VPN Gateway und Verbindungen

Network Watcher verfügt derzeit über die folgenden Funktionen:

- [Topologie](../../network-watcher/view-network-topology.md): Bietet eine Ansicht der verschiedenen Verbindungen und Beziehungen zwischen Netzwerkressourcen in einer Ressourcengruppe.
- [Variable Paketerfassung](../../network-watcher/network-watcher-packet-capture-overview.md): Erfasst Paketdaten, die im virtuellen Computer ein- und ausgehen. Erweiterte Filteroptionen und präzise Steuerelemente ermöglichen beispielsweise das Festlegen von Zeit- und Größeneinschränkungen und bieten damit viel Flexibilität. Die Paketdaten können in einem Blobspeicher oder auf dem lokalen Datenträger im CAP-Format gespeichert werden.
- [IP-Datenflussüberprüfung](../../network-watcher/network-watcher-ip-flow-verify-overview.md): Überprüft basierend auf 5-Tupel-Paketparametern für Datenflussinformationen (IP-Zieladresse, IP-Quelladresse, Zielport, Quellport und Protokoll), ob ein Paket zugelassen oder abgelehnt wird. Wenn eine Sicherheitsgruppe ein Paket ablehnt, werden die Regel und die Gruppe zurückgegeben, die das Paket abgelehnt haben.
- [Nächster Hop](../../network-watcher/network-watcher-next-hop-overview.md): Ermittelt den nächsten Hop für Pakete, die im Azure-Netzwerkfabric weitergeleitet werden, sodass Sie eine Diagnose zur Ermittlung von falsch konfigurierten benutzerdefinierten Routen durchführen können.
- [Sicherheitsgruppenansicht](../../network-watcher/network-watcher-security-group-view-overview.md): Ruft die geltenden und angewendeten Sicherheitsregeln ab, die auf einen virtuellen Computer angewendet werden.
- [Datenflussprotokolle für Netzwerksicherheitsgruppen](../../network-watcher/network-watcher-nsg-flow-logging-overview.md): Ermöglichen die Erfassung von Protokollen zum Datenverkehr, der von den Sicherheitsregeln in der Gruppe zugelassen oder abgelehnt wird. Der Datenfluss wird durch 5-Tupel-Informationen definiert: IP-Quelladresse, IP-Zieladresse, Quellport, Zielport und Protokoll.
- [Problembehandlung für virtuelle Netzwerkgateways und -verbindungen](../../network-watcher/network-watcher-troubleshoot-manage-rest.md): Ermöglicht die Behebung von Problemen, die bei virtuellen Netzwerkgateways und -verbindungen auftreten können.
- [Grenzwerte für Netzwerkabonnements](../../network-watcher/network-watcher-monitoring-overview.md): Ermöglicht die Anzeige der Verwendung von Netzwerkressourcen mit bestimmten Grenzwerten.
- [Diagnoseprotokolle](../../network-watcher/network-watcher-monitoring-overview.md): Stellt einen zentralen Bereich für das Aktivieren oder Deaktivieren von Diagnoseprotokollen für Netzwerkressourcen in einer Ressourcengruppe bereit.

Weitere Informationen finden Sie unter [Konfigurieren von Network Watcher](../../network-watcher/network-watcher-create.md).

## <a name="cloud-service-provider-access-transparency"></a>Transparenz für den Clouddienstanbieter-Zugriff

[Kunden-Lockbox für Microsoft Azure](customer-lockbox-overview.md) ist ein in das Azure-Portal integrierter Dienst, der Ihnen in dem seltenen Fall, dass ein Microsoft-Supportmitarbeiter zum Beheben eines Problems Zugriff auf Ihre Daten benötigt, explizite Kontrolle verleiht.
Es gibt sehr wenige Situationen, wie z.B. das Debuggen eines RAS-Problems, in denen ein Microsoft-Supportmitarbeiter erhöhte Berechtigungen zum Beheben dieses Problems benötigt. In solchen Fällen verwenden Microsoft-Techniker den Just-In-Time-Zugriffsdienst, der begrenzte, zeitlich beschränkte Autorisierung mit auf den Dienst beschränktem Zugriff bereitstellt.  
Microsoft hat zwar immer die Zustimmung des Kunden für den Zugriff eingeholt, mit Kunden Lockbox haben Sie jetzt jedoch die Möglichkeit, solche Anforderungen aus dem Azure-Portal zu überprüfen und zu genehmigen oder zu verweigern. Microsoft-Supporttechniker erhalten keinen Zugriff, bis Sie die Anforderung genehmigen.

## <a name="standardized-and-compliant-deployments"></a>Standardisierte und konforme Bereitstellungen

[Azure Blueprints](../../governance/blueprints/overview.md) ermöglicht es Cloudarchitekten und zentralen IT-Gruppen, eine wiederholbare Gruppe von Azure-Ressourcen zu definieren, mit der die Standards, Muster und Anforderungen einer Organisation implementiert und erzwungen werden.  
Dadurch können DevOps-Teams schnell neue Umgebungen erstellen und einrichten und sich dabei darauf verlassen, dass die verwendete Infrastruktur mit den Organisationsanforderungen konform ist.
Blaupausen bieten eine deklarative Möglichkeit zum Orchestrieren der Bereitstellung mehrerer Ressourcenvorlagen und anderer Artefakte wie etwa:

- Rollenzuweisungen
- Richtlinienzuweisungen
- Azure-Ressourcen-Manager-Vorlagen
- Ressourcengruppen

## <a name="devops"></a>DevOps

Vor der Einführung der [DevOps](https://www.visualstudio.com/learn/what-is-devops/)-Anwendungsentwicklung waren die Teams für das Zusammenstellen der Geschäftsanforderungen für ein Softwareprogramm und das Schreiben von Code zuständig. Danach testete ein separates QA-Team das Programm in einer isolierten Entwicklungsumgebung. Wenn die Anforderungen erfüllt waren, gab das QA-Team den Code für das Operations-Team zur Bereitstellung frei. Die Bereitstellungsteams waren noch weiter in einzelne Gruppen unterteilt, z.B. für Netzwerk und Datenbank. Jedes Mal, wenn ein unabhängiges Team ein Softwareprogramm vorgesetzt bekam, waren Engpässe die Folge.

Mit dem DevOps-Konzept können Teams schneller und kostengünstiger Lösungen entwickeln und bereitstellen, die mehr Sicherheit und Qualität bieten. Kunden erwarten beim Konsumieren von Software und Diensten eine dynamische und zuverlässige Benutzeroberfläche. Teams müssen sehr schnell Softwareupdates entwickeln und die Auswirkungen der Updates bewerten. Sie müssen schnell mit neuen Entwicklungsiterationen reagieren, um Probleme zu beheben oder den Nutzen der Anwendung zu erhöhen.  

Cloudplattformen wie Microsoft Azure haben dazu geführt, dass herkömmliche Engpässe entfernt wurden und die Infrastruktur allgemein verfügbar ist. Die Software ist in jedem Unternehmen ein bedeutendes Unterscheidungsmerkmal und ein wichtiger Faktor in Bezug auf das Geschäftsergebnis. Organisationen, Entwickler und IT-Experten können bzw. sollten die DevOps-Bewegung nicht mehr ignorieren.

Erfahrene DevOps-Praktiker nutzen beispielsweise die folgenden Vorgehensweisen. Bei diesen Vorgehensweisen legen [Personen](https://www.visualstudio.com/learn/what-is-devops-culture/) Strategien anhand des Geschäftsszenarios fest. Tools können zur Automatisierung der unterschiedlichen Vorgehensweisen beitragen.

- Verfahren für [Planung und Projektmanagement auf „agile“ Weise](https://www.visualstudio.com/learn/what-is-agile/) werden genutzt, um Arbeit in Form von „Sprints“ zu planen und zu isolieren, die Teamkapazität zu verwalten und Teams bei der schnellen Anpassung an sich ändernde Geschäftsanforderungen zu unterstützen.
- Mit der [Versionskontrolle (normalerweise per Git)](https://www.visualstudio.com/learn/what-is-git/) können Teams, die sich an beliebigen Orten weltweit befinden, Quellen gemeinsam verwenden und Tools für die Softwareentwicklung integrieren, um die Releasepipeline zu automatisieren.
- [Continuous Integration](https://www.visualstudio.com/learn/what-is-continuous-integration/) unterstützt das laufende Zusammenführen und Testen von Code, sodass Fehler zu einem frühen Zeitpunkt erkannt werden können.  Weitere Vorteile sind, dass weniger Zeit für die Behebung von Zusammenführungsproblemen aufgewendet werden muss und Entwicklungsteams schnell Feedback erhalten.
- [Continuous Delivery](https://www.visualstudio.com/learn/what-is-continuous-delivery/) (fortlaufende Bereitstellung) von Softwarelösungen in Produktions- und Testumgebungen ermöglicht Organisationen eine schnelle Behebung von Fehlern und Reaktion auf sich ständig ändernde Geschäftsanforderungen.
- Die [Überwachung](https://www.visualstudio.com/learn/what-is-monitoring/) von ausgeführten Anwendungen, einschließlich der Anwendungsintegrität und der Anwendungsnutzung durch Kunden in Produktionsumgebungen, unterstützt Unternehmen dabei, Hypothesen aufzustellen und Strategien schnell zu validieren oder zu widerlegen.  In verschiedenen Protokollierungsformaten werden umfassende Daten erfasst und gespeichert.
- [Infrastruktur als Code (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) ist eine Methode, die die Automatisierung und Überprüfung der Erstellung und Beendigung von Netzwerken und virtuellen Computern ermöglicht, um die Bereitstellung von sicheren und stabilen Anwendungsplattformen zu unterstützen.
- Die [Microservices](https://www.visualstudio.com/learn/what-are-microservices/)-Architektur wird genutzt, um Geschäftsanwendungsfälle in Form von kleinen wiederverwendbaren Diensten zu isolieren.  Diese Architektur ermöglicht Skalierbarkeit und Effizienz.

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Sicherheits- und Überwachungslösung finden Sie in den folgenden Artikeln:

- [Sicherheit und Konformität](https://azure.microsoft.com/overview/trusted-cloud/)
- [Azure Security Center](../../security-center/security-center-introduction.md)
- [Azure Monitor](../../azure-monitor/overview.md)