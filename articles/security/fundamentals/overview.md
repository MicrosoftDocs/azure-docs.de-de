---
title: Einführung in die Azure-Sicherheit | Microsoft-Dokumentation
description: Lesen Sie diese Übersicht, um sich mit Azure-Sicherheit, den verschiedenen Diensten und der Funktionsweise vertraut zu machen.
services: security
documentationcenter: na
author: UnifyCloud
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2019
ms.author: TomSh
ms.openlocfilehash: 749c32091ed10d5bb39d7b67c8b737e002fc9909
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94693731"
---
# <a name="introduction-to-azure-security"></a>Einführung in die Azure-Sicherheit
## <a name="overview"></a>Übersicht
Wir wissen, dass Sicherheit in der Cloud an erster Stelle steht und wie wichtig es ist, dass Sie präzise und zeitnahe Informationen zur Azure-Sicherheit finden. Eines der schlagkräftigsten Argumente dafür, Azure für Anwendungen und Dienste zu verwenden, ist die Vielzahl an Sicherheitstools und -funktionen. Diese Tools und Funktionen ermöglichen die Erstellung sicherer Lösungen auf der Grundlage der sicheren Azure-Plattform. Microsoft Azure bietet sowohl Vertraulichkeit, Integrität und Verfügbarkeit von Kundendaten als auch eine transparente Verantwortlichkeit.

Dieser Artikel bietet einen umfassenden Überblick über die Sicherheitsfeatures von Azure.

### <a name="azure-platform"></a>Azure Platform
Azure ist eine öffentliche Clouddienstplattform, die eine breite Palette an Betriebssystemen, Programmiersprachen, Frameworks, Tools, Datenbanken und Geräten unterstützt. Es kann Linux-Container mit Docker-Integration ausführen, Apps mit JavaScript, Python, .NET, PHP, Java und Node.js sowie Back-Ends für iOS-, Android- und Windows-Geräte erstellen.

Die öffentlichen Azure-Clouddienste unterstützen dieselben Technologien, die bereits von Millionen von Entwicklern und IT-Profis zuverlässig eingesetzt werden. Wenn Sie IT-Ressourcen darauf erstellen oder zu einem öffentlichen Clouddienstanbieter migrieren, verlassen Sie sich darauf, dass die jeweilige Organisation Ihre Anwendungen und Daten mit den Diensten und Steuerungsmöglichkeiten schützen kann, die sie zum Verwalten der Sicherheit Ihrer cloudbasierten Ressourcen bereitstellt.

Die Infrastruktur von Azure ist von den Hardwareressourcen bis zu den Anwendungen vollständig auf das gleichzeitige Hosten von Millionen von Kunden ausgelegt und stellt für Unternehmen eine vertrauenswürdige Grundlage zur Erfüllung ihrer Sicherheitsanforderungen dar.

Darüber hinaus bietet Azure Ihnen ein breites Spektrum an konfigurierbaren Sicherheitsoptionen, die Sie selbst steuern können, um die Sicherheit an die individuellen Anforderungen der Bereitstellungen Ihrer Organisation anzupassen. Dieses Dokument hilft Ihnen dabei zu verstehen, wie Ihnen die Azure-Sicherheitsfunktionen dabei helfen können, diese Anforderungen zu erfüllen.

> [!Note]
> Der Hauptfokus in diesem Dokument richtet sich auf Steuerelemente für Kunden, mit denen Sie die Sicherheit für Ihre Anwendungen und Dienste anpassen und erhöhen können.
>
> Informationen zu den Schutzmaßnahmen von Microsoft für die eigentliche Azure-Plattform finden Sie unter [Sicherheit der Azure-Infrastruktur](infrastructure.md).

## <a name="summary-of-azure-security-capabilities"></a>Zusammenfassung der Azure-Sicherheitsfunktionen

### <a name="features-to-secure-the-azure-platform"></a>Features zum Schutz der Azure-Plattform
Die folgenden Features gewährleisten eine sichere Verwaltung der Azure-Plattform. Unter den bereitgestellten Links erfahren Sie, wie Microsoft Kundenvertrauensaspekte in vier Bereichen behandelt: sichere Plattform, Datenschutz und Kontrollen, Compliance und Transparenz.

| [Sichere Plattform](https://www.microsoft.com/trustcenter/Security/default.aspx)  | [Datenschutz und Kontrollen](https://www.microsoft.com/trustcenter/Privacy/default.aspx)  |[Compliance](https://www.microsoft.com/trustcenter/Compliance/default.aspx)   | [Transparenz](https://www.microsoft.com/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [Sicherheitsentwicklungszyklus](https://www.microsoft.com/sdl/), interne Überwachungen | [Ständiges Verwalten Ihrer Daten](https://www.microsoft.com/trustcenter/Privacy/You-own-your-data) | [Trust Center](https://www.microsoft.com/trustcenter/default.aspx) |[Wie Microsoft Kundendaten in Azure-Diensten schützt](https://www.microsoft.com/trustcenter/Transparency/default.aspx) |
| [Obligatorische Sicherheitsschulung, Hintergrundüberprüfungen](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) |  [Kontrolle des Datenspeicherorts](https://www.microsoft.com/trustcenter/Privacy/Where-your-data-is-located) |  [Hub für allgemeine Kontrollen](https://www.microsoft.com/trustcenter/Common-Controls-Hub) |[Wie Microsoft den Datenspeicherort in Azure-Diensten verwaltet](https://azuredatacentermap.azurewebsites.net/)|
| [Penetrationstests](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx), [Angriffserkennung, DDoS](https://www.microsoft.com/trustcenter/Security/ThreatManagement), [Überwachungen und Protokollierung](https://www.microsoft.com/trustcenter/Security/AuditingAndLogging) | [Bereitstellen des Datenzugriff zu Ihren Bedingungen](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [Checkliste zur Kaufprüfung von Clouddiensten](https://www.microsoft.com/trustcenter/Compliance/Due-Diligence-Checklist) |[Wer kann bei Microsoft unter welchen Bedingungen auf Ihre Daten zugreifen?](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [Hochmodernes Rechenzentrum](https://www.microsoft.com/cloud-platform/global-datacenters), physische Sicherheit, [Sicheres Netzwerk](network-overview.md) | [Antworten für Justizbehörden](https://www.microsoft.com/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [Compliance nach Dienst, Speicherort und Branche](https://www.microsoft.com/trustcenter/Compliance/default.aspx) |[Wie Microsoft Kundendaten in Azure-Diensten schützt](https://www.microsoft.com/trustcenter/Transparency/default.aspx)|
|  [Reaktion auf Sicherheitsvorfälle](https://aka.ms/SecurityResponsepaper), [Geteilte Verantwortung](./shared-responsibility.md) |[Strenge Datenschutzstandards](https://www.microsoft.com/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [Überprüfen der Zertifizierung für Azure-Dienste, Transparenzhub](https://www.microsoft.com/trustcenter/Compliance/default.aspx)|

### <a name="features-to-secure-data-and-application"></a>Features zum Schutz von Daten und Anwendungen
In Abhängigkeit vom Clouddienstmodell besteht dahingehend eine variable Verantwortlichkeit, wer für die Verwaltung der Sicherheit der Anwendungen oder Dienste zuständig ist. Azure Platform verfügt über Funktionen, die Sie beim Erfüllen dieser Aufgaben durch integrierte Features sowie durch Partnerlösungen unterstützen, die in ein Azure-Abonnement implementiert werden können.

Die integrierten Funktionen sind in sechs Funktionsbereiche unterteilt: Vorgänge, Anwendungen, Speicher, Netzwerk, Compute und Identität. Weitere Details zu den Features und Funktionen, die in der Azure-Plattform in diesen sechs Bereichen zur Verfügung stehen, werden als zusammenfassende Informationen bereitgestellt.

## <a name="operations"></a>Operationen (Operations)
Dieser Abschnitt enthält zusätzliche Informationen zu den wichtigsten Features von Sicherheitsvorgängen und zusammenfassende Informationen zu diesen Funktionen.

### <a name="security-and-audit-dashboard"></a>Dashboard „Sicherheit und Überwachung“
Die [Sicherheits- und Überwachungslösung](../../security-center/security-center-introduction.md) ermöglicht mit [integrierten Suchabfragen](/archive/blogs/msoms/easy-microsoft-operations-management-suite-search-queries) für relevante Probleme, die Ihre Aufmerksamkeit erfordern, einen umfassenden Einblick in die Lage der IT-Sicherheit Ihres Unternehmens. Das Dashboard [Sicherheit und Überwachung](../../azure-monitor/overview.md) ist die Startseite für sämtliche Sicherheitsaspekte in Azure Monitor-Protokolle. Hier erhalten Sie einen allgemeinen Überblick über den Sicherheitszustand Ihres Computers. Darüber hinaus können Sie alle Ereignisse der letzten 24 Stunden, sieben Tage oder eines anderen benutzerdefinierten Zeitraums anzeigen.

Darüber hinaus können Sie die Sicherheit und Compliance konfigurieren, um [automatisch bestimmte Aktionen auszuführen](/archive/blogs/robdavies/simple-look-at-oms-alert-remediation-with-runbooks-part-1), wenn ein bestimmtes Ereignis erkannt wird.

### <a name="azure-resource-manager"></a>Azure Resource Manager
Mit dem [Azure Resource Manager](../../azure-resource-manager/management/deployment-models.md) können Sie als Gruppe mit den Ressourcen in Ihrer Lösung arbeiten. Sie können alle Ressourcen für Ihre Lösung in einem einzigen koordinierten Vorgang bereitstellen, aktualisieren oder löschen. Sie verwenden eine [Azure Resource Manager-Vorlage](/archive/blogs/canitpro/devops-basics-infrastructure-as-code-arm-templates) für die Bereitstellung, die für unterschiedliche Umgebungen geeignet sein kann, z. B. Testing, Staging und Produktion. Der Ressourcen-Manager bietet Sicherheits-, Überwachungs- und Kennzeichnungsfunktionen, mit denen Sie Ihre Ressourcen nach der Bereitstellung verwalten können.

Auf Azure Resource Manager-Vorlagen basierte Bereitstellungen helfen dabei, die Sicherheit von Lösungen zu verbessern, die in Azure bereitgestellt werden, da standardmäßige Einstellungen für die Sicherheitskontrolle in standardisierte vorlagenbasierte Bereitstellungen integriert werden können. Dies verringert das Risiko von Fehlern bei der Sicherheitskonfiguration, die möglicherweise bei manuellen Bereitstellungen auftreten können.

### <a name="application-insights"></a>Application Insights
[Application Insights](/azure/application-insights/) ist ein erweiterbarer, für Webentwickler konzipierter Dienst zur Verwaltung der Anwendungsleistung (Application Performance Management, APM). Mit Application Insights können Sie Ihre aktiven Webanwendungen überwachen und automatisch Leistungsanomalien erkennen. Er verfügt über leistungsstarke Analysetools, mit denen Sie Probleme diagnostizieren und nachvollziehen können, wie Ihre App von den Benutzern verwendet wird. Er überwacht Ihre Anwendung während der gesamten Ausführung – sowohl in der Testphase als auch nach der Veröffentlichung oder Bereitstellung.

Application Insights erstellt Diagramme und Tabellen, die beispielsweise Aufschluss darüber geben, zu welchen Tageszeiten das Benutzerinteresse besonders groß ist, wie gut die App reagiert und wie gut sie von externen Diensten versorgt wird, von denen sie unter Umständen abhängig ist.

Im Falle von Abstürzen, Fehlern oder Leistungsproblemen können Sie die Telemetriedaten im Detail durchsuchen, um die Fehlerursache zu ermitteln. Darüber hinaus informiert Sie der Dienst per E-Mail, falls sich die Verfügbarkeit oder Leistung Ihrer App ändert. Application Insights wird daher zu einem wertvollen Sicherheitstool, da es bei der Verfügbarkeit hilft, die zu den drei Sicherheitsbereichen zählt: Vertraulichkeit, Integrität und Verfügbarkeit.

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](/azure/monitoring-and-diagnostics/) bietet Visualisierung, Abfrage, Weiterleitung, Warnung, automatische Skalierung und Automatisierung für Daten sowohl aus der Azure-Infrastruktur ([Aktivitätsprotokoll](../../azure-monitor/platform/platform-logs-overview.md)) als auch aus jeder einzelnen Azure-Ressource ([Diagnoseprotokolle](../../azure-monitor/platform/platform-logs-overview.md)). Mit Azure Monitor können Sie sich bei sicherheitsrelevanten Ereignissen warnen lassen, die in Azure-Protokollen generiert werden.

### <a name="azure-monitor-logs"></a>Azure Monitor-Protokolle
[Azure Monitor-Protokolle](https://azure.microsoft.com/documentation/services/log-analytics/) bietet eine IT-Verwaltungslösung für lokale Infrastrukturen und cloudbasierte Drittanbieterinfrastrukturen (z.B. AWS) zusätzlich zu Azure-Ressourcen. Daten von Azure Monitor können direkt an Azure Monitor-Protokolle weitergeleitet werden, sodass Sie die Metriken und Protokolle für Ihre gesamte Umgebung an einem Ort finden.

Azure Monitor-Protokolle kann ein hilfreiches Tool bei forensischen und anderen Sicherheitsanalysen sein, da Sie mit dem Tool schnell große Mengen von sicherheitsbezogenen Einträgen mit einem flexiblen Abfrageansatz durchsuchen können. Darüber hinaus können lokale [Firewall- und Proxyprotokolle in Azure exportiert und für die Analyse mit Azure Monitor-Protokolle zur Verfügung gestellt werden.](../../azure-monitor/platform/agent-windows.md)

### <a name="azure-advisor"></a>Azure Advisor
Beim [Azure Advisor](../../advisor/index.yml) handelt es sich um einen personalisierten Cloudberater, der Sie beim Optimieren von Azure-Bereitstellungen unterstützt. Er analysiert Ihre Ressourcenkonfiguration und Nutzungstelemetrie. Anschließend schlägt er Lösungen vor, um die [Leistung](../../advisor/advisor-performance-recommendations.md), [Sicherheit](../../advisor/advisor-security-recommendations.md) und [Hochverfügbarkeit](../../advisor/advisor-high-availability-recommendations.md) Ihrer Ressourcen zu verbessern, und sucht gleichzeitig nach Möglichkeiten, Ihre [Azure-Gesamtausgaben zu reduzieren](../../advisor/advisor-cost-recommendations.md). Der Azure Advisor bietet Sicherheitsempfehlungen, die den Gesamtsicherheitsstatus für Lösungen erheblich verbessern können, die Sie in Azure bereitstellen. Diese Empfehlungen stammen aus der Sicherheitsanalyse, die vom [Azure Security Center](../../security-center/security-center-introduction.md) durchgeführt wurde.

### <a name="azure-security-center"></a>Azure Security Center
[Security Center](../../security-center/security-center-introduction.md) unterstützt Sie bei der Vermeidung, Erkennung und Behandlung von Bedrohungen. Mit dieser Cloudlösung gewinnen Sie mehr Transparenz und bessere Kontrolle über die Sicherheit Ihrer Azure-Ressourcen. Es bietet integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Azure-Abonnements, hilft bei der Erkennung von Bedrohungen, die andernfalls möglicherweise unbemerkt bleiben, und kann gemeinsam mit einem breiten Spektrum an Sicherheitslösungen verwendet werden.

Außerdem hilft Security Center bei Sicherheitsvorgängen, indem Ihnen ein einzelnes Dashboard bereitgestellt wird, das als Oberfläche für Warnungen und Empfehlungen dient, auf die sofort reagiert werden kann. Häufig lassen sich Probleme mit einem einzelnen Klick innerhalb der Security Center-Konsole beseitigen.
## <a name="applications"></a>Anwendungen
Dieser Abschnitt enthält zusätzliche Informationen zu den wichtigsten Features der Anwendungssicherheit und zusammenfassende Informationen zu diesen Funktionen.

### <a name="web-application-vulnerability-scanning"></a>Sicherheitsrisikoprüfung für Webanwendungen
Eine der einfachsten Möglichkeiten, mit der Sie Ihre [App Service-App](../../app-service/overview.md) auf Sicherheitslücken testen können, ist die Verwendung der [Integration in Tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/), die diese per Mausklick auf Schwachstellen überprüft. Sie können die Testergebnisse in einem leicht verständlichen Bericht anzeigen lassen, und erfahren, wie Sie jede Sicherheitslücke mit einer Schritt-für-Schritt-Anleitung beheben können.

### <a name="penetration-testing"></a>Penetrationstests
Wenn Sie lieber Ihre eigenen Penetrationstests ausführen möchten oder eine andere Scanner Suite oder einen anderen Anbieter verwenden möchten, befolgen Sie den [Azure Penetrationstests-Genehmigungsprozess](./pen-testing.md) und erhalten Sie vorherige Genehmigungen zum Ausführen der gewünschten Penetrationstests.

### <a name="web-application-firewall"></a>Web Application Firewall
Die Web Application Firewall (WAF) in [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) hilft beim Schutz von Webanwendungen vor gängigen webbasierten Angriffen wie der Einschleusung von SQL-Code, Angriffen durch websiteübergreifende Skripts und der Übernahme von Sitzungen. Dazu bietet sie Schutz vor den Sicherheitsrisiken, die das [Open Web Application Security Project (OWASP) als die zehn häufigsten ermittelt hat](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

### <a name="authentication-and-authorization-in-azure-app-service"></a>Authentifizierung und Autorisierung in Azure App Service
Mithilfe des [Authentifizierungs-/Autorisierungsfeatures von App Service](../../app-service/overview-authentication-authorization.md) kann Ihre Anwendung Benutzer anmelden, sodass Sie keine Codeänderungen für das Back-End der App vornehmen müssen. Es stellt eine einfache Möglichkeit zum Schutz Ihrer Anwendung und für die Arbeit mit benutzerspezifischen Daten bereit.

### <a name="layered-security-architecture"></a>Mehrstufige Sicherheitsarchitektur
Da [App Service-Umgebungen](../../app-service/environment/app-service-app-service-environment-intro.md) eine in einem [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md) bereitgestellte isolierte Laufzeitumgebung bieten, können Entwickler eine mehrstufige Sicherheitsarchitektur erstellen, in der sie abgestuften Netzwerkzugriff für jede Anwendungsschicht gewähren können. Üblicherweise sollten API-Back-Ends nicht dem allgemeinen Internetzugriff preisgegeben werden, und APIs sollten nur von Upstream-Web-Apps aufgerufen werden können. Um den öffentlichen Zugriff auf API-Anwendungen zu beschränken, können [Netzwerksicherheitsgruppen (Network Security Groups, NSGs)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) in Azure Virtual Network-Subnetzen mit App Service-Umgebungen verwendet werden.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Webserver- und Anwendungsdiagnose
App Service-Web-Apps bieten Diagnosefunktionen zum Protokollieren von Informationen sowohl über den Webserver als auch über die Webanwendung. Diese sind logisch in [Webserverdiagnose](../../app-service/troubleshoot-diagnostic-logs.md) und [Anwendungsdiagnose](/previous-versions/system-center/system-center-2012-R2/hh530058(v=sc.12)) unterteilt. Ein Webserver bezieht zwei wichtige Fortschritte bei der Diagnose und Problembehandlung von Websites und Anwendungen ein.

Das erste neue Feature sind Zustandsinformationen in Echtzeit zu Anwendungspools, Workerprozessen, Websites, Anwendungsdomänen und aktiven Anforderungen. Der zweite Vorteil sind ausführliche Ablaufverfolgungsereignisse, die eine Anforderung während des vollständigen Prozesses (Anforderung und Antwort) nachverfolgen.

IIS 7 kann zur Aktivierung der Auflistung dieser Ablaufverfolgungsereignisse so konfiguriert werden, dass für jede bestimmte Anforderung, die auf der abgelaufenen Zeit oder auf Fehlerantwortcodes basiert, automatisch vollständige Ablaufverfolgungsprotokolle im XML-Format erfasst werden.

#### <a name="web-server-diagnostics"></a>Webserverdiagnose
Sie können die folgenden Protokollarten aktivieren oder deaktivieren:

-   Detaillierte Fehlerprotokollierung – Detaillierte Fehlerinformationen für HTTP-Statuscodes, die auf einen Fehler hinweisen (Statuscode 400 oder höher). Diese können Informationen enthalten, mit deren Hilfe sich bestimmen lässt, warum der Server den Fehlercode zurückgegeben hat.

-   Ablaufverfolgung von Anforderungsfehlern – Detaillierte Informationen zu fehlgeschlagenen Anforderungen, einschließlich der Verfolgung von IIS-Komponenten, die zur Verarbeitung der Anforderung verwendet wurden, sowie die in jeder Komponente benötigte Zeit. Dies kann nützlich sein, wenn Sie versuchen, die Leistung von Websites zu verbessern oder herauszufinden, warum ein bestimmter HTTP-Fehler zurückgegeben wird.

-   Webserverprotokollierung – Informationen über HTTP-Transaktionen im erweiterten W3C-Protokolldateiformat. Dies ist hilfreich, wenn Sie allgemeine Website-Kennzahlen ermitteln möchten, wie die Anzahl der verarbeiteten Anfragen oder wie viele Anforderungen von einer bestimmten IP-Adresse stammen.

#### <a name="application-diagnostics"></a>Anwendungsdiagnose
Mit der Option [Anwendungsdiagnose](../../app-service/troubleshoot-diagnostic-logs.md) können Sie die von einer Webanwendung erzeugten Informationen erfassen. ASP.NET-Anwendungen können die Klasse [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) verwenden, um Informationen im Anwendungs-Diagnoseprotokoll aufzuzeichnen. Bei der Anwendungsdiagnose gibt es zwei Hauptarten von Ereignissen, die sich auf die Anwendungsleistung oder die Anwendungsausfälle und -fehler beziehen. Die Ausfälle und Fehler können weiter in Verbindungsfehler, Sicherheitsfehler und Ausfallprobleme unterteilt werden. Ausfallprobleme beziehen sich in der Regel auf ein Problem mit dem Anwendungscode.

Bei der Anwendungsdiagnose können Ereignisse in den folgenden Kategorien angezeigt werden:

-   Alle (alle Ereignisse werden angezeigt)
-   Anwendungsfehler (Ausnahmeereignisse werden angezeigt)
-   Leistung (Leistungsereignisse werden angezeigt)

## <a name="storage"></a>Storage
Dieser Abschnitt enthält zusätzliche Informationen zu den wichtigsten Features der Azure-Speichersicherheit und zusammenfassende Informationen zu diesen Funktionen.

### <a name="azure-role-based-access-control-azure-rbac"></a>Rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)
Sie können Ihr Speicherkonto mit der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) sichern. Das Einschränken des Zugriffs auf der Grundlage der Sicherheitsprinzipien [Need to know](https://en.wikipedia.org/wiki/Need_to_know) und [Least Privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege) ist für Organisationen unerlässlich, die Sicherheitsrichtlinien für den Datenzugriff erzwingen möchten. Diese Zugriffsrechte werden gewährt, indem Gruppen und Anwendungen die jeweils geeignete Azure-Rolle für einen bestimmten Bereich zugewiesen wird. Sie können [in Azure integrierte Rollen](../../role-based-access-control/built-in-roles.md) (z. B. „Speicherkontomitwirkender“) verwenden, um Benutzern Berechtigungen zuzuweisen. Zugriff auf die Speicherschlüssel für ein Speicherkonto mit dem [Azure Resource Manager](../../storage/blobs/security-recommendations.md)-Modell kann über rollenbasierte Azure RBAC gesteuert werden.

### <a name="shared-access-signature"></a>Shared Access Signature (SAS)
[Shared Access Signatures (SAS)](../../storage/common/storage-sas-overview.md) ermöglichen den delegierten Zugriff auf Ressourcen in Ihrem Speicherkonto. Eine SAS bietet die Möglichkeit, einem Client für einen bestimmten Zeitraum spezielle eingeschränkte Berechtigungen für Objekte in Ihrem Speicherkonto zu erteilen. Dazu müssen Sie nicht Ihre Kontozugriffsschlüssel freigeben.

### <a name="encryption-in-transit"></a>Verschlüsselung während der Übertragung
Verschlüsselung während der Übertragung ist ein Mechanismus zum Schutz der Daten bei der Übertragung über Netzwerke hinweg. Mit Azure Storage können Sie Daten mit folgenden Verfahren schützen:
-   [Verschlüsselung auf Transportebene](../../storage/blobs/security-recommendations.md)(etwa HTTPS), wenn Sie Daten in oder aus Azure Storage übertragen.

-   [Wire-Verschlüsselung](../../storage/blobs/security-recommendations.md) (etwa [SMB 3.0-Verschlüsselung](../../storage/blobs/security-recommendations.md) für [Azure-Dateifreigaben](../../storage/files/storage-dotnet-how-to-use-files.md)).

-   Clientseitiger Verschlüsselung, um die Daten zu verschlüsseln, bevor sie in den Speicher übertragen werden, und nach der Übertragung aus dem Speicher zu entschlüsseln.

### <a name="encryption-at-rest"></a>Verschlüsselung ruhender Daten
Für viele Organisationen ist die Verschlüsselung von ruhenden Daten ein obligatorischer Schritt in Richtung Datenschutz, Compliance und Datenhoheit. Drei Azure-Features zur Speichersicherheit ermöglichen die Verschlüsselung „ruhender“ Daten:

-   [Storage Service Encryption](../../storage/common/storage-service-encryption.md) können Sie anfordern, dass der Speicherdienst die Daten beim Schreiben in Azure Storage automatisch verschlüsselt.

-   [Client-side Encryption](../../storage/common/storage-client-side-encryption.md) ermöglicht ebenfalls eine Verschlüsselung ruhender Daten.

-   [Azure-Datenträgerverschlüsselung](./azure-disk-encryption-vms-vmss.md) können Sie die Betriebssystemdatenträger und andere Datenträger verschlüsseln, die von einem virtuellen IaaS-Computer verwendet werden.

### <a name="storage-analytics"></a>Speicheranalyse
Die [Azure-Speicheranalyse](/rest/api/storageservices/fileservices/storage-analytics) führt die Protokollierung aus und stellt Metrikdaten für ein Speicherkonto bereit. Mit diesen Daten können Sie Anforderungen verfolgen, Verwendungstrends analysieren und Probleme mit dem Speicherkonto diagnostizieren. Storage Analytics protokolliert ausführliche Informationen zu erfolgreichen und nicht erfolgreichen Anforderungen an einen Speicherdienst. Anhand dieser Informationen können einzelne Anforderungen überwacht und Probleme mit einem Speicherdienst untersucht werden. Anforderungen werden auf Grundlage der besten Leistung protokolliert. Die folgenden Typen authentifizierter Anforderungen werden protokolliert:
-   Erfolgreiche Anforderungen

-   Fehlerhafte Anforderungen, einschließlich Timeout-, Drosselungs-, Netzwerk- und Autorisierungsfehler sowie anderer Fehler

-   Anforderungen mithilfe einer SAS (Shared Access Signature), einschließlich fehlerhafter und erfolgreicher Anforderungen

-   Anforderungen von Analysedaten

### <a name="enabling-browser-based-clients-using-cors"></a>Aktivieren browserbasierter Clients über CORS
[Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS)](/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) ist ein Mechanismus, der es Domänen gestattet, sich gegenseitig die Zugriffsberechtigung für die Ressourcen der anderen Domänen zu erteilen. Der Benutzer-Agent sendet zusätzliche Header, um sicherzustellen, dass der von einer bestimmten Domäne geladene JavaScript-Code auf Ressourcen zugreifen kann, die sich in einer anderen Domäne befinden. Die letztere Domäne antwortet dann mit zusätzlichen Headern, die der ursprünglichen Domäne den Zugriff auf ihre Ressourcen gestattet oder verweigert.

Die Azure-Speicherdienste unterstützten jetzt CORS, damit nach dem Festlegen der CORS-Regeln für den Dienst eine ordnungsgemäß authentifizierte Anforderung ausgewertet wird, die von einer anderen Domäne an den Dienst gesendet wurde, um zu ermitteln, ob die Anforderung gemäß den von Ihnen festgelegten Regeln zulässig ist.

## <a name="networking"></a>Netzwerk
Dieser Abschnitt enthält zusätzliche Informationen zu den wichtigsten Features der Azure-Netzwerksicherheit und zusammenfassende Informationen zu diesen Funktionen.

### <a name="network-layer-controls"></a>Kontrollen der Vermittlungsschicht
Netzwerkzugriffssteuerung bedeutet, die Konnektivität zu oder ausgehend von bestimmten Geräten oder Subnetzen zu begrenzen. Dies stellt den Kern der Netzwerksicherheit dar. Das Ziel der Netzwerkzugriffssteuerung ist sicherzustellen, dass Ihre virtuellen Computer und Dienste nur denjenigen Benutzern und Geräten zugänglich sind, denen Sie den Zugriff auch erlauben möchten.

#### <a name="network-security-groups"></a>Netzwerksicherheitsgruppen
Eine [Netzwerksicherheitsgruppe (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) ist eine einfache Firewall, die zustandsbehaftete Pakete filtert und die Zugriffssteuerung auf Grundlage eines [5-Tupels](https://www.techopedia.com/definition/28190/5-tuple) ermöglicht. NSGs bieten weder eine Inspektion auf Anwendungsebene noch authentifizierte Zugriffssteuerungen. Sie können dazu verwendet werden, um den Datenverkehr zu steuern, der zwischen Subnetzen in einem Azure Virtual Network und zwischen einem Azure Virtual Network und dem Internet bewegt wird.

#### <a name="route-control-and-forced-tunneling"></a>Routensteuerung und Tunnelerzwingung
Die Steuerung des Routingverhaltens in Ihren virtuellen Azure-Netzwerken ist eine entscheidende Funktion in den Bereichen Netzwerksicherheit und Zugriffssteuerung. Wenn Sie z. B. sicherstellen möchten, dass der gesamte Datenverkehr zu und ausgehend von Ihrem Azure Virtual Network Ihr virtuelles Sicherheitsgerät passiert, müssen Sie das Routingverhalten steuern und anpassen können. Sie erreichen dies über die Konfiguration der benutzerdefinierten Routen in Azure.

[Benutzerdefinierte Routen](../../virtual-network/virtual-networks-udr-overview.md) ermöglichen es Ihnen, eingehende und ausgehende Pfade für den Datenverkehr anzupassen, der zu oder von einzelnen virtuellen Computern oder Subnetzen bewegt wird, um möglichst die sicherste Route zu gewährleisten. Mithilfe der [Tunnelerzwingung](https://www.petri.com/azure-forced-tunneling) können Sie sicherstellen, dass Ihre Dienste nicht über die Erlaubnis verfügen, eine Verbindung mit Geräten im Internet zu initiieren.

Dieser Mechanismus unterscheidet sich von der Möglichkeit zur Annahme eingehender Verbindungen und der Antwort auf diese. Front-End-Webserver müssen auf Anforderungen von Internethosts antworten. Aus dem Internet stammender, auf diesen Webservern eingehender Datenverkehr wird daher erlaubt und die Webserver dürfen antworten.

Die Tunnelerzwingung wird häufig verwendet, um für ausgehenden Datenverkehr für das Internet zu erzwingen, dass dieser lokale Sicherheitsproxys und Firewalls durchläuft.

#### <a name="virtual-network-security-appliances"></a>Appliances für die Sicherheit des virtuellen Netzwerks
Obwohl Netzwerksicherheitsgruppen, benutzerdefinierte Routen und die Tunnelerzwingung Sicherheit in der Vermittlungs- und Transportschicht des [OSI-Modells](https://en.wikipedia.org/wiki/OSI_model)bieten, kann es vorkommen, dass Sie Sicherheit auf höheren Ebenen aktivieren möchten. Sie können mit einer Azure-Partnerlösung für Appliances für die Sicherheit des Netzwerks auf diese erweiterten Netzwerksicherheitsfeatures zugreifen. Die aktuellsten Azure-Partnerlösungen für Netzwerksicherheit finden Sie im [Azure Marketplace](https://azure.microsoft.com/marketplace/) , und indem Sie nach den Stichworten „Sicherheit“ und „Netzwerksicherheit“ suchen.

### <a name="azure-virtual-network"></a>Virtuelles Azure-Netzwerk
Ein virtuelles Azure-Netzwerk (VNet) ist eine Darstellung Ihres eigenen Netzwerks in der Cloud. Es ist eine logische Isolierung der Azure-Netzworkfabric für Ihr Abonnement. Sie können die IP-Adressblöcke, DNS-Einstellungen, Sicherheitsrichtlinien und Routentabellen in diesem Netzwerk vollständig steuern. Sie können Ihr VNet in Subnetze segmentieren und virtuelle Azure IaaS-Computer (VMs) und/oder [Cloud Services (PaaS-Rolleninstanzen)](../../cloud-services/cloud-services-choose-me.md) in Azure Virtual Networks platzieren.

Zudem können Sie das virtuelle Netzwerk mit einer der [Konnektivitätsoptionen](../../vpn-gateway/index.yml) in Azure mit Ihrem lokalen Netzwerk verbinden. Im Wesentlichen können Sie Ihr Netzwerk mit vollständiger Kontrolle über IP-Adressblöcke auf Azure ausdehnen und von der Azure-Skalierung auf Unternehmensebene profitieren.

Azure-Netzwerke unterstützen verschiedene Szenarien für den sicheren Remotezugriff. Dazu zählen:

-   [Verbinden einzelner Arbeitsstationen mit einem Azure Virtual Network](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

-   [Verbinden eines lokalen Netzwerks mit einem Azure Virtual Network mithilfe eines VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

-   [Verbinden eines lokalen Netzwerks mit einem Azure Virtual Network mithilfe eines dedizierten WAN-Links](../../expressroute/expressroute-introduction.md)

-   [Verbinden von Azure Virtual Networks untereinander](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

### <a name="vpn-gateway"></a>VPN Gateway
Wenn Sie Netzwerkdatenverkehr zwischen Ihrem Azure Virtual Network und Ihrem lokalen Standort senden möchten, müssen Sie für Ihr Azure Virtual Network ein VPN Gateway erstellen. Ein [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) ist eine Art von Gateway für virtuelle Netzwerke, mit dem verschlüsselter Datenverkehr über eine öffentliche Verbindung gesendet wird. Sie können VP Gateways auch verwenden, um Datenverkehr zwischen Azure Virtual Networks über das Azure-Netzwerkfabric zu senden.

### <a name="express-route"></a>ExpressRoute
Microsoft Azure [ExpressRoute](../../expressroute/expressroute-introduction.md) ist ein dedizierter WAN-Link, mit dem Sie Ihre lokalen Netzwerke über eine dedizierte private Verbindung, die von einem Konnektivitätsanbieter bereitgestellt wird, in die Microsoft Cloud erweitern können.

![ExpressRoute](./media/overview/azure-security-figure-1.png)

Mit ExpressRoute können Sie Verbindungen mit Microsoft-Clouddiensten herstellen, z. B. Microsoft Azure, Microsoft 365 und CRM Online. Die Konnektivität kann über ein Any-to-Any-Netzwerk (IP VPN), ein Point-to-Point-Ethernet-Netzwerk oder eine virtuelle Querverbindung über einen Konnektivitätsanbieter in einer Co-Location-Einrichtung bereitgestellt werden.

ExpressRoute-Verbindungen verlaufen nicht über das öffentliche Internet und können somit als sicherer angesehen werden als VPN-basierte Lösungen. Auf diese Weise können ExpressRoute-Verbindungen eine höhere Sicherheit, größere Zuverlässigkeit und schnellere Geschwindigkeit bei geringerer Latenz als herkömmliche Verbindungen über das Internet bieten.


### <a name="application-gateway"></a>Application Gateway
Microsoft [Azure Application Gateway](../../application-gateway/overview.md) verfügt über einen [ADC (Application Delivery Controller)](https://en.wikipedia.org/wiki/Application_delivery_controller) als Dienst und damit für Ihre Anwendung über verschiedene Lastenausgleichsfunktionen auf Schicht 7.

![Application Gateway](./media/overview/azure-security-figure-2.png)

Sie können damit die Produktivität von Webfarmen steigern, indem sie die CPU-intensive TLS-Terminierung an das Application Gateway auslagern (auch als „TLS-Auslagerung“ oder „TLS-Bridging“ bekannt). Darüber hinaus werden noch weitere Routingfunktionen der Ebene 7 bereitgestellt. Hierzu zählen etwa die Roundrobin-Verteilung des eingehenden Datenverkehrs, cookiebasierte Sitzungsaffinität, Routing auf URL-Pfadbasis und die Möglichkeit zum Hosten mehrerer Websites hinter einer einzelnen Application Gateway-Instanz. Azure Application Gateway verwendet einen Load Balancer auf der Schicht 7 (Anwendungsschicht).

Das Application Gateway ermöglicht ein Failover sowie schnelles Routing von HTTP-Anforderungen zwischen verschiedenen Servern in der Cloud und der lokalen Umgebung.

Application Gateway bietet zahlreiche Application Delivery Controller-Funktionen (ADC), u.a. HTTP-Lastenausgleich, cookiebasierte Sitzungsaffinität, [TLS-Auslagerung](../../web-application-firewall/ag/tutorial-restrict-web-traffic-powershell.md), benutzerdefinierte Integritätstests und Unterstützung für mehrere Websites.

### <a name="web-application-firewall"></a>Web Application Firewall
Web Application Firewall ist ein Feature von [Azure Application Gateway](../../application-gateway/overview.md), das Schutz für Webanwendungen bietet, die Application Gateway für ADC-Standardfunktionen (Application Delivery Control, Steuerung der Anwendungsbereitstellung) nutzen. Web Application Firewall schützt sie vor den nach OWASP 10 häufigsten Web-Sicherheitslücken.

![Web Application Firewall](./media/overview/azure-security-figure-3.png)

-   Schutz vor Einschleusung von SQL-Befehlen

-   Schutz vor allgemeinen Webangriffen wie Befehlseinschleusung, HTTP Request Smuggling, HTTP Response Splitting und Remote File Inclusion

-   Schutz vor Verletzungen des HTTP-Protokolls

-   Schutz vor HTTP-Protokollanomalien, z.B. fehlende user-agent- und accept-Header des Hosts

-   Verhindern von Bots, Crawlern und Scannern

-   Erkennung häufiger Fehler bei der Anwendungskonfiguration (d. h. Apache, IIS usw.)

Eine zentrale Webanwendungs-Firewall zum Schutz vor Webangriffen vereinfacht die Sicherheitsverwaltung erheblich und verleiht der Anwendung eine bessere Sicherung gegen die Bedrohungen durch Angriffe. Mit einer WAF-Lösung können Sie ebenfalls schneller auf ein Sicherheitsrisiko reagieren, da eine bekannte Schwachstelle an einem zentralen Ort gepatcht wird, statt jede einzelne Webanwendung separat zu sichern. Vorhandene Anwendungsgateways können problemlos in ein Anwendungsgateway mit Web Application Firewall konvertiert werden.

### <a name="traffic-manager"></a>Traffic Manager
Microsoft [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) ermöglicht die Verteilung von Benutzerdatenverkehr für Dienstendpunkte in unterschiedlichen Rechenzentren. Zu den von Traffic Manager unterstützten Dienstendpunkten zählen virtuelle Azure-Computer, Web-Apps und Clouddienste. Darüber hinaus kann Traffic Manager auch mit externen, Azure-fremden Endpunkten verwendet werden. Traffic Manager verwendet das Domain Name System (DNS), um Clientanforderungen auf der Grundlage einer [Datenverkehrsrouting-Methode](../../traffic-manager/traffic-manager-routing-methods.md) und der Integrität der Endpunkte an den optimalen Endpunkt weiterzuleiten.

Traffic Manager bietet eine Reihe von Datenverkehrsrouting-Methoden, die verschiedene Anwendungsanforderungen erfüllen und die [Überwachung](../../traffic-manager/traffic-manager-monitoring.md) der Integrität von Endpunkten sowie automatisches Failover ermöglichen. Traffic Manager zeichnet sich durch eine geringe Fehleranfälligkeit aus, selbst wenn es zu einem Ausfall einer ganzen Azure-Region kommt.

### <a name="azure-load-balancer"></a>Azure Load Balancer
Der [Azure Load Balancer](../../load-balancer/load-balancer-overview.md) bietet Hochverfügbarkeit und Netzwerkleistung für Ihre Anwendungen. Es ist ein Layer-4-Lastenausgleichsmodul (TCP, UDP), das eingehenden Datenverkehr auf funktionierende Dienstinstanzen verteilt, die in einer Lastenausgleichsgruppe definiert sind. Azure Load Balancer kann für Folgendes konfiguriert werden:

-   Lastenausgleich des eingehenden Internetdatenverkehrs für virtuelle Computer. Diese Konfiguration wird als [öffentlicher Lastenausgleich](../../load-balancer/components.md#frontend-ip-configurations) bezeichnet.

-   Lastenausgleich für Datenverkehr zwischen virtuellen Computern in einem virtuellen Netzwerk, zwischen virtuellen Computern in Clouddiensten oder zwischen lokalen und virtuellen Computern in einem standortübergreifenden virtuellen Netzwerk. Diese Konfiguration wird als [interner Lastenausgleich](../../load-balancer/components.md#frontend-ip-configurations)bezeichnet.

- Weiterleiten von externem Datenverkehr an eine bestimmte Instanz eines virtuellen Computers

### <a name="internal-dns"></a>Internes DNS
Sie können die Liste der in einem VNet verwendeten DNS-Server im Verwaltungsportal oder mithilfe der Netzwerkkonfigurationsdatei verwalten. Kunden können bis zu 12 DNS-Server für jedes VNet hinzufügen. Beim Angeben von DNS-Servern müssen Sie darauf achten, dass Sie die DNS-Server des Kunden in der richtigen Reihenfolge für dessen Umgebung auflisten. DNS-Serverlisten werden nicht per Roundrobin verarbeitet. Die DNS-Server werden in der Reihenfolge verwendet, in der sie angegeben sind. Wenn der erste DNS-Server in der Liste erreicht werden kann, verwendet der Client diesen DNS-Server unabhängig davon, ob der DNS-Server ordnungsgemäß funktioniert. Um die Reihenfolge der DNS-Server für das virtuelle Netzwerk des Kunden zu ändern, entfernen Sie die DNS-Server aus der Liste, und fügen Sie sie in der vom Kunden gewünschten Reihenfolge wieder hinzu. DNS unterstützt den Verfügbarkeitsaspekt der drei Sicherheitsbereiche „Vertraulichkeit“, „Integrität“ und „Verfügbarkeit“.

### <a name="azure-dns"></a>Azure DNS
Das [Domain Name System](/previous-versions/windows/it-pro/windows-server-2003/cc772774(v=ws.10)) (DNS) ist für die Übersetzung (oder Auflösung) eines Website- oder Dienstnamens in die IP-Adresse verantwortlich. [Azure DNS ist ein Hostingdienst](../../dns/dns-overview.md) für DNS-Domänen, der die Namensauflösung mithilfe der Microsoft Azure-Infrastruktur durchführt. Durch das Hosten Ihrer Domänen in Azure können Sie Ihre DNS-Einträge mithilfe der gleichen Anmeldeinformationen, APIs, Tools und Abrechnung wie für die anderen Azure-Dienste verwalten. DNS unterstützt den Verfügbarkeitsaspekt der drei Sicherheitsbereiche „Vertraulichkeit“, „Integrität“ und „Verfügbarkeit“.

### <a name="azure-monitor-logs-nsgs"></a>Netzwerksicherheitsgruppen in Azure Monitor-Protokolle
Sie können die folgenden Diagnoseprotokoll-Kategorien für Netzwerksicherheitsgruppen aktivieren:

-   Ereignis: Enthält Einträge, für die anhand der MAC-Adresse NSG-Regeln auf virtuelle Computer und Instanzrollen angewendet werden. Der Status für diese Regeln wird alle 60 Sekunden erfasst.

-   Regelzähler: Enthält Einträge darüber, wie oft jede NSG-Regel angewendet wurde, um Datenverkehr zuzulassen oder zu verweigern.

### <a name="security-center"></a>Security Center
[Azure Security Center](../../security-center/security-center-introduction.md) analysiert ständig den Sicherheitsstatus Ihrer Azure-Ressourcen anhand bewährter Methoden für Netzwerksicherheit. Werden potenzielle Sicherheitslücken erkannt, erstellt Security Center [Empfehlungen](../../security-center/security-center-recommendations.md), die Sie beim Konfigurieren der erforderlichen Steuerelemente zum Härten und Schützen Ihrer Ressourcen unterstützen.

## <a name="compute"></a>Compute
Dieser Abschnitt enthält zusätzliche Informationen zu den wichtigsten Features in diesem Bereich und zusammenfassende Informationen zu diesen Funktionen.

### <a name="antimalware--antivirus"></a>Antischadsoftware und Antivirus
Mit Azure IaaS können Sie zum Schützen der virtuellen Computer vor Dateien mit schädlichem Inhalt, Adware und anderen Bedrohungen Antischadsoftware von Anbietern wie Microsoft, Symantec, Trend Micro, McAfee und Kaspersky verwenden. [Microsoft Antimalware](antimalware.md) for Azure Cloud Services and Virtual Machines ist eine Echtzeit-Schutzfunktion zum Bestimmen und Entfernen von Viren, Spyware und anderer Schadsoftware. Microsoft Antimalware gibt konfigurierbare Warnungen aus, wenn bekannte schädliche oder unerwünschte Software versucht, sich selbst auf Ihren Azure-Systemen zu installieren oder dort auszuführen. Microsoft Antimalware kann auch über das Azure Security Center bereitgestellt werden.

### <a name="hardware-security-module"></a>Hardwaresicherheitsmodul
Verschlüsselung und Authentifizierung verbessern die Sicherheit erst dann, wenn die Schlüssel selbst geschützt sind. Sie können die Verwaltung und Sicherheit Ihrer geschäftskritischen geheimen Daten und Schlüssel vereinfachen, indem Sie diese in [Azure Key Vault](../../key-vault/general/overview.md) speichern. Mit Key Vault können Sie Ihre Schlüssel in Hardwaresicherheitsmodulen (Hardware Security Modules, HSMs) speichern, die gemäß FIPS 140-2 Level 2-Standards zertifiziert sind. Sie können Ihre SQL Server-Verschlüsselungsschlüssel für Sicherungen oder [transparente Datenverschlüsselung](/sql/relational-databases/security/encryption/transparent-data-encryption) gemeinsam mit den Schlüsseln oder geheimen Daten Ihrer Anwendungen in Key Vault speichern. Der Zugriff auf diese geschützten Elemente sowie die zugehörigen Berechtigungen werden über [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)verwaltet.

### <a name="virtual-machine-backup"></a>Sicherung virtueller Computer
[Azure Backup](../../backup/backup-overview.md) ist eine Lösung, die Ihre Anwendungsdaten schützt – ganz ohne Investitionskosten und mit sehr niedrigen Betriebskosten. Anwendungsfehler können Ihre Daten beschädigen, und menschliche Fehler können Bugs in Ihren Anwendungen verursachen, die zu Sicherheitsproblemen führen können. Mit Azure Backup sind Ihre virtuellen Windows- und Linux-Computer geschützt.

### <a name="azure-site-recovery"></a>Azure Site Recovery
Ein wichtiger Teil der [BCDR](../../best-practices-availability-paired-regions.md)-Strategie (Geschäftskontinuität/Notfallwiederherstellung) Ihrer Organisation ist die Ermittlung, wie geschäftliche Workloads und Apps verfügbar gehalten werden können, wenn geplante und ungeplante Ausfälle auftreten. [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) unterstützt die Orchestrierung von Replikation, Failover und Wiederherstellung von Workloads und Apps, damit diese Komponenten über einen sekundären Standort zur Verfügung stehen, wenn der primäre Standort ausfällt.

### <a name="sql-vm-tde"></a>SQL VM TDE
[Transparent Data Encryption (TDE)](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault) und Column Level Encryption (CLE) sind SQL Server-Verschlüsselungsfeatures. Bei dieser Art der Verschlüsselung müssen Kunden die kryptografischen Schlüssel verwalten und speichern, die Sie für die Verschlüsselung verwenden.

Der Azure-Schlüsseltresor-Dienst (Azure Key Vault, AKV) ist dafür ausgelegt, die Sicherheit und Verwaltung dieser Schlüssel an einem sicheren und hoch verfügbaren Speicherort zu verbessern. Mit dem SQL Server-Connector kann SQL Server diese Schlüssel aus Azure Key Vault verwenden.

Wenn Sie SQL Server mit lokalen Computern ausführen, können Sie die Schritte zum Zugreifen auf den Azure Key Vault von Ihrer lokalen SQL Server-Instanz ausführen. Sie können für SQL Server auf virtuellen Azure-Computern aber Zeit sparen, indem Sie die Funktion für die Azure-Schlüsseltresor-Integration verwenden. Mit einigen Azure PowerShell-Cmdlets zum Aktivieren dieser Funktion können Sie die Konfiguration automatisieren, die ein virtueller SQL-Computer zum Zugreifen auf Ihren Schlüsseltresor benötigt.

### <a name="vm-disk-encryption"></a>Datenträgerverschlüsselung für virtuelle Computer
[Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) ist eine neue Funktion, mit der Sie die Datenträger von virtuellen Windows- und Linux-IaaS-Computern verschlüsseln können. Diese Funktion verwendet das Branchen-Standardfeature BitLocker von Windows und das Feature DM-Crypt von Linux, um Volumeverschlüsselung für das Betriebssystem und die Datenträger bereitzustellen. Die Lösung ist in Azure Key Vault integriert, damit Sie die Verschlüsselungsschlüssel und Geheimnisse für die Datenträgerverschlüsselung in Ihrem Key Vault-Abonnement steuern und verwalten können. Diese Lösung stellt außerdem sicher, dass alle ruhenden Daten auf den Datenträgern der virtuellen Computer in Azure Storage verschlüsselt sind.

### <a name="virtual-networking"></a>Virtuelle Netzwerke
Virtuelle Computer benötigen Netzwerkkonnektivität. Azure erfordert von einem virtuellen Computer eine Verbindung mit einem virtuellen Azure-Netzwerk, damit die Konnektivitätsanforderung unterstützt wird. Ein virtuelles Azure-Netzwerk ist ein logisches Konstrukt, das auf dem physischen Azure-Netzwerkfabric basiert. Jedes logische [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md) ist von allen anderen virtuellen Azure-Netzwerken isoliert. Mit dieser Isolierung wird sichergestellt, dass der Netzwerkverkehr in Ihren Bereitstellungen nicht für andere Microsoft Azure-Kunden zugänglich ist.

### <a name="patch-updates"></a>Patch-Updates
Patch-Updates bilden die Grundlage zum Ermitteln und Beheben potenzieller Probleme und zur Vereinfachung des Verwaltungsprozesses von Softwareupdates. Beides wird dadurch erreicht, dass einerseits die Anzahl von Softwareupdates verringert wird, die Sie in Ihrem Unternehmen bereitstellen müssen, und andererseits die Möglichkeiten verbessert werden, die Einhaltung der Vorgaben zu überwachen.

### <a name="security-policy-management-and-reporting"></a>Verwaltung von Sicherheitsrichtlinien und Berichtserstellung
[Security Center](../../security-center/security-center-introduction.md) unterstützt Sie durch mehr Transparenz und bessere Kontrolle über die Sicherheit Ihrer Azure-Ressourcen bei der Vermeidung, Erkennung und Behandlung von Bedrohungen. Es bietet integrierte Sicherheitsüberwachung und Richtlinienverwaltung für Ihre Azure-Abonnements, hilft bei der Erkennung von Bedrohungen, die andernfalls möglicherweise unbemerkt bleiben, und kann gemeinsam mit einem breiten Spektrum an Sicherheitslösungen verwendet werden.

## <a name="identity-and-access-management"></a>Identitäts- und Zugriffsverwaltung
Das Schützen von Systemen, Anwendungen und Daten beginnt mit der identitätsbasierten Zugriffssteuerung. Die Features zur Identitäts- und Zugriffsverwaltung, die in Microsoft Business-Produkten und -Diensten integriert sind, unterstützen Sie beim Schützen Ihrer organisationsbezogenen und privaten Informationen vor nicht autorisiertem Zugriff, während sie für berechtigte Benutzer jederzeit und überall verfügbar sind.

### <a name="secure-identity"></a>Schützen der Identität
Microsoft verwendet mehrere Sicherheitsmaßnahmen und -technologien für seine Produkte und Dienste für die Identitäts- und Zugriffsverwaltung.

-   [Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) erfordert, dass Benutzer mehrere Zugriffsmethoden (lokal und in der Cloud) verwenden. Mit einer Reihe einfacher Verifizierungsoptionen wird für eine leistungsstarke Authentifizierung gesorgt und gleichzeitig die Benutzeranforderungen an einen einfachen Anmeldeprozess erfüllt.

-   [Microsoft Authenticator](https://aka.ms/authenticator) bietet eine benutzerfreundliche Umgebung für die Multi-Factor Authentication, die mit Microsoft Azure Active Directory und Microsoft-Konten funktioniert und Unterstützung für Wearables und fingerabdruckbasierende Genehmigungen bietet.

-   [Erzwingung der Kennwortrichtlinie](../../active-directory/authentication/concept-sspr-policy.md) erhöht die Sicherheit herkömmlicher Kennwörter, indem Anforderungen hinsichtlich Länge und Komplexität sowie eine regelmäßige erzwungene Rotation und Kontosperrungen nach fehlgeschlagenen Authentifizierungsversuchen durchgesetzt werden.

-   Die [tokenbasierte Authentifizierung](../../active-directory/develop/authentication-vs-authorization.md) ermöglicht die Authentifizierung über Azure Active Directory.

-   Die [rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)](../../role-based-access-control/built-in-roles.md) ermöglicht es Ihnen, den Zugriff auf Grundlage einer zugewiesenen Benutzerrolle zu gewähren. Dies erleichtert es Ihnen, Benutzern nur den zum Ausführen ihrer Aufgaben erforderlichen Zugriff zu erteilen. Sie können Azure RBAC gemäß dem Geschäftsmodell und der Risikotoleranz Ihrer Organisation anpassen.

-   [Integrierte Identitätsverwaltung (Hybrididentität)](../../active-directory/hybrid/plan-hybrid-identity-design-considerations-overview.md) ermöglicht es Ihnen, den Benutzerzugriff auf interne Rechenzentren und Cloudplattformen zu kontrollieren, indem Sie eine einzelne Benutzeridentität für die Authentifizierung und Autorisierung für alle Ressourcen erstellen.

### <a name="secure-apps-and-data"></a>Schützen von Apps und Daten
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) ist eine umfassende Cloudlösung zur Identitäts- und Zugriffsverwaltung, die Sie beim Sichern des Zugriffs auf Daten in lokalen Anwendungen sowie in der Cloud unterstützt und die Verwaltung von Benutzern und Gruppen unterstützt. Es kombiniert wesentliche Verzeichnisdienste, erweiterte Identitätskontrolle, Sicherheit und Anwendungszugriffsverwaltung und gestaltet es für Entwickler einfach, eine richtlinienbasierte Identitätsverwaltung in ihre Apps zu integrieren. Sie können Ihre Azure Active Directory-Anwendung erweitern, indem Sie mit den Basic-, Premium P1- und Premium P2-Editionen von Azure Active Directory kostenpflichtige Funktionen hinzufügen.

| Kostenlose/Gemeinsame Features     | Basic-Features    |Premium P1-Features |Premium P2-Features | Azure Active Directory-Einbindung – nur auf Windows 10 bezogene Features|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Verzeichnisobjekte](../../active-directory/fundamentals/active-directory-whatis.md), [Benutzer-/Gruppenverwaltung (hinzufügen/aktualisieren/löschen)/Benutzerbasierte Bereitstellung, Geräteregistrierung](../../active-directory/fundamentals/active-directory-whatis.md), [Einmaliges Anmelden (Single Sign-On, SSO)](../../active-directory/fundamentals/active-directory-whatis.md), [Self-Service-Kennwortänderung für Cloudbenutzer](../../active-directory/fundamentals/active-directory-whatis.md), [Verbinden (Synchronisierungsmodul, das lokale Verzeichnisse auf Azure Active Directory ausweitet)](../../active-directory/fundamentals/active-directory-whatis.md), [Sicherheits-/Nutzungsberichte](../../active-directory/fundamentals/active-directory-whatis.md)       |  [Gruppenbasierte Zugriffsverwaltung/Bereitstellung](../../active-directory/fundamentals/active-directory-whatis.md), [Self-Service-Kennwortzurücksetzung für Cloudbenutzer](../../active-directory/fundamentals/active-directory-whatis.md), [Unternehmensbranding (Anpassen von Anmeldeseiten/Zugriffsbereich)](../../active-directory/fundamentals/active-directory-whatis.md), [Anwendungsproxy](../../active-directory/fundamentals/active-directory-whatis.md), [SLA 99,9 %](../../active-directory/fundamentals/active-directory-whatis.md) |  [Self-Service-Verwaltung von Gruppen und Apps/Self-Service-Hinzufügung von Anwendungen/Dynamische Gruppen](../../active-directory/fundamentals/active-directory-whatis.md), [Self-Service-Kennwortzurücksetzung, -änderung, -entsperrung mit lokalem Rückschreiben](../../active-directory/fundamentals/active-directory-whatis.md), [Multi-Factor Authentication (cloudbasiert und lokal [MFA-Server])](../../active-directory/fundamentals/active-directory-whatis.md), [MIM-CAL + MIM-Server](../../active-directory/fundamentals/active-directory-whatis.md), [Cloud App Discovery](../../active-directory/fundamentals/active-directory-whatis.md), [Connect Health](../../active-directory/fundamentals/active-directory-whatis.md), [Automatischer Kennwortrollover für Gruppenkonten](../../active-directory/fundamentals/active-directory-whatis.md)|   [Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md), [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)|   [Verbinden eines Geräts mit Azure AD, Desktop-SSO, Microsoft Passport für Azure AD, BitLocker-Wiederherstellung (Administrator)](../../active-directory/fundamentals/active-directory-whatis.md), [Automatische Registrierung für MDM, BitLocker-Wiederherstellung (Self-Service), zusätzliche lokale Administratoren für Windows 10-Geräte über die Einbindung in Azure AD](../../active-directory/fundamentals/active-directory-whatis.md)|


- [Cloud App Discovery](/cloud-app-security/set-up-cloud-discovery) ist ein Premium-Feature von Azure Active Directory, mit dem Sie Cloudanwendungen identifizieren können, die von den Mitarbeitern in Ihrer Organisation verwendet werden.

- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) ist ein Sicherheitsdienst, der die Funktionen von Azure Active Directory zur Erkennung von Anomalien verwendet, um eine konsolidierte Ansicht zu erkannten Risiken und potenziellen Sicherheitslücken zu bieten, die sich auf die Identitäten Ihrer Organisation auswirken könnten.

- Mit [Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) können Sie virtuelle Azure-Computer in eine Domäne einbinden, ohne Domänencontroller bereitstellen zu müssen. Die Benutzer melden sich mit den Active Directory-Anmeldeinformationen ihres Unternehmens an diesen virtuellen Computern an und können nahtlos auf Ressourcen zugreifen.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) ist ein hoch verfügbarer, globaler Identitätsverwaltungsdienst für kundenorientierte Apps, der für Hunderte Millionen von Identitäten skaliert und plattformübergreifend (mobil und Web) integriert werden kann. Ihre Kunden können sich über anpassbare Benutzeroberflächen, die vorhandene Konten für soziale Netzwerke verwenden, bei all Ihren Apps anmelden. Sie können aber auch neue eigenständige Anmeldeinformationen erstellen.

- Die [Azure Active Directory B2B-Zusammenarbeit](../../active-directory/external-identities/what-is-b2b.md) ist eine sichere Lösung zur Partnerintegration, die Partnern den gezielten Zugriff auf Ihre Unternehmensanwendungen und Unternehmensdaten über ihre selbstverwalteten Identitäten ermöglicht und so Ihre unternehmensübergreifenden Beziehungen unterstützt.

- [Azure Active Directory Join](../../active-directory/devices/overview.md) ermöglicht Ihnen das Erweitern von Cloudfunktionen auf Windows 10-Geräte für die zentrale Verwaltung. Dadurch erhalten Benutzer die Möglichkeit, über Azure Active Directory eine Verbindung mit der Cloud des Unternehmens oder der Organisation herzustellen. Dies vereinfacht den Zugriff auf Apps und Ressourcen.

- Der [Azure Active Directory-Anwendungsproxy](../../active-directory/manage-apps/application-proxy.md) ermöglicht das einmalige Anmelden (SSO) und den sicheren Remotezugriff für lokal gehostete Webanwendungen.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [gemeinsame Verantwortung in der Cloud](shared-responsibility.md).

- Erfahren Sie, wie [Azure Security Center](https://azure.microsoft.com/services/security-center/) Sie durch mehr Transparenz und eine bessere Kontrolle über die Sicherheit Ihrer Azure-Ressourcen bei der Vermeidung, Erkennung und Behandlung von Bedrohungen unterstützt.