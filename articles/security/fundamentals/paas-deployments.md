---
title: Bewährte Methoden für sichere PaaS-Bereitstellungen – Microsoft Azure
description: Erfahren Sie mehr über bewährte Methoden für das Entwerfen, Erstellen und Verwalten sicherer Cloudanwendungen in Azure, und informieren Sie sich über die Sicherheitsvorteile von PaaS im Vergleich zu anderen Clouddienstmodellen.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: techlake
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 77a2a32b9a6358c39a14cfe37eeb44f7cb90af0a
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841987"
---
# <a name="securing-paas-deployments"></a>Schützen von PaaS-Bereitstellungen

Dieser Artikel beschäftigt sich mit Folgendem:

- Sicherheitsvorteile von in der Cloud gehosteten Anwendungen
- Bewertung der Sicherheitsvorteile von PaaS (Platform-as-a-Service) im Vergleich zu anderen Clouddienstmodellen
- Verlagerung des Sicherheitsfokus von netzwerkorientierter zu identitätsorientierter Bereichssicherheit
- Implementierung allgemeiner Sicherheitsempfehlungen für PaaS

[Entwickeln sicherer Anwendungen in Azure](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/) ist ein allgemeiner Leitfaden und behandelt die Sicherheitsfragen und Steuerelemente, die Sie beim Entwickeln von Anwendungen für die Cloud in den einzelnen Phasen des Softwareentwicklungszyklus berücksichtigen sollten.

## <a name="cloud-security-advantages"></a>Sicherheitsvorteile der Cloud
Es ist wichtig, die [Aufteilung der Zuständigkeiten](shared-responsibility.md) zwischen Ihnen und Microsoft zu verstehen. In einer lokalen Umgebung sind Sie für den gesamten Stapel zuständig. Bei einem Wechsel in die Cloud wird dagegen ein Teil der Zuständigkeit auf Microsoft übertragen.

Die [Nutzung der Cloud bringt einige Sicherheitsvorteile mit sich](shared-responsibility.md#cloud-security-advantages). In einer lokalen Umgebung werden in Organisationen häufig nicht alle Zuständigkeitsbereiche abgedeckt, und für die Sicherheit stehen nur begrenzte Ressourcen zur Verfügung. So entsteht eine Umgebung, in der Angreifer Sicherheitslücken auf allen Ebenen ausnutzen können.

Mithilfe der cloudbasierten Sicherheitsfunktionen und Clouddaten eines Anbieters können Organisationen ihre Bedrohungserkennung und ihre Reaktionszeiten verbessern.  Durch die Übertragung von Aufgaben an den Cloudanbieter profitieren Organisationen von einer höheren Sicherheit und können Sicherheitsressourcen und entsprechende Finanzmittel für andere geschäftliche Prioritäten nutzen.

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Sicherheitsvorteile eines PaaS-Clouddienstmodells
Sehen wir uns die Sicherheitsvorteile einer Azure-PaaS-Bereitstellung im Vergleich zu einer lokalen Bereitstellung an.

![Sicherheitsvorteile von PaaS](./media/paas-deployments/advantages-of-paas.png)

Beginnen wir ganz unten im Stapel: bei der physischen Infrastruktur. Hier kümmert sich Microsoft um allgemeine Risiken und Zuständigkeiten. Die Microsoft-Cloud wird kontinuierlich von Microsoft überwacht und lässt sich daher nur schwer angreifen. Für einen Angreifer stellt die Microsoft-Cloud somit kein sinnvolles Ziel dar. Sofern der Angreifer nicht über sehr viel Geld und Ressourcen verfügt, wird er sich wahrscheinlich ein anderes Ziel suchen.  

In der Mitte des Stapels gibt es keinen Unterschied zwischen einer PaaS-Bereitstellung und einer lokalen Bereitstellung. Auf der Anwendungsebene und der Konto- und Zugriffsverwaltungsebene sind die Risiken ähnlich. Bewährte Methoden für die Beseitigung oder Minimierung dieser Risiken finden Sie in diesem Artikel im Abschnitt „Nächste Schritte“.

An der Spitze des Stapels (Datengovernance und Rechteverwaltung) können Sie sich mit einem einzelnen Risiko auseinandersetzen, das mittels Schlüsselverwaltung behandelt werden kann. (Die Schlüsselverwaltung wird in den bewährten Methoden behandelt.) Bei der Schlüsselverwaltung handelt es sich zwar um eine zusätzliche Aufgabe, dafür gibt es in einer PaaS-Bereitstellung jedoch Bereiche, um die Sie sich nicht mehr kümmern müssen, sodass Sie frei gewordene Ressourcen für die Schlüsselverwaltung nutzen können.

Dank verschiedener netzwerkbasierter Technologien bietet die Azure-Plattform außerdem starken DDoS-Schutz. Bei allen Arten von netzwerkbasierten DDoS-Schutzmethoden gelten allerdings gewisse Einschränkungen für Links und Datencenter. Zur Vermeidung der Auswirkungen groß angelegter DDoS-Angriffe können Sie dank der Kernfunktion der Azure-Cloud schnell und automatisch aufskalieren, um DDoS-Angriffe abzuwehren. Ausführlichere Informationen zur entsprechenden Vorgehensweise finden Sie in den Artikeln zu empfohlenen Vorgehensweisen.

## <a name="modernizing-the-defenders-mindset"></a>Eine neue Mentalität für Verteidiger
Mit PaaS-Bereitstellungen verändert sich der gesamte Sicherheitsansatz. Sie müssen sich nun nicht mehr um alles selbst kümmern, sondern können einen Teil der Verantwortung an Microsoft abgeben.

Ein weiterer wichtiger Unterschied zwischen PaaS und herkömmlichen lokalen Bereitstellungen ist die Neuauslegung des primären Sicherheitsbereichs. In der Vergangenheit war Ihr Netzwerk der primäre lokale Sicherheitsbereich, und bei den meisten lokalen Sicherheitskonzepten wird das Netzwerk als Dreh- und Angelpunkt für die Sicherheit verwendet. Bei PaaS-Bereitstellungen empfiehlt es sich dagegen, die Identität als primären Sicherheitsbereich zu betrachten.

## <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>Übernehmen einer Identitätsrichtlinie als primärer Sicherheitsbereich
Eines der fünf grundlegenden Merkmale von Cloud Computing ist umfassender Netzwerkzugriff, was eine netzwerkorientierte Betrachtung weniger relevant macht. Cloud Computing dient zu einem großen Teil dazu, Benutzern unabhängig von ihrem Standort Zugriff auf Ressourcen zu ermöglichen. Die meisten Benutzer befinden sich irgendwo im Internet.

Die folgende Abbildung zeigt die Entwicklung von einem netzwerkorientierten zu einem identitätsorientierten Sicherheitsbereich. Bei der Sicherheit geht es immer weniger um die Verteidigung Ihres Netzwerks und immer mehr um die Verteidigung Ihrer Daten sowie um die Verwaltung der Sicherheit Ihrer Apps und Benutzer. Der wesentliche Unterschied besteht darin, dass Sie die Sicherheit stärker dem annähern möchten, was für Ihr Unternehmen wichtig ist.

![Identität als neuer Sicherheitsbereich](./media/paas-deployments/identity-perimeter.png)

Zu Anfang verfügten Azure PaaS-Dienste wie etwa Webrollen und Azure SQL nur über eine rudimentäre oder über gar keine traditionelle Netzwerkbereichsverteidigung. Es wurde allgemein angenommen, dass das Element für das Internet verfügbar gemacht wird (Webrolle) und dass die Authentifizierung den neuen Sicherheitsbereich bildet (beispielsweise Blob oder Azure SQL).

Bei modernen Sicherheitsmaßnahmen wird davon ausgegangen, dass der Angreifer in den Netzwerkbereich eingedrungen ist. Aus diesem Grund ist bei modernen Verteidigungsmethoden die Identität in den Fokus gerückt. Organisationen müssen einen identitätsbasierten Sicherheitsbereich mit starker Authentifizierung und Autorisierung (bzw. mit entsprechenden bewährten Methoden) einrichten.

Prinzipien und Muster für den Netzwerkbereich sind bereits seit Jahrzehnten verfügbar. Im Gegensatz dazu hat die Branche relativ wenig Erfahrung mit der Identität als primärer Sicherheitsbereich. Wir haben jedoch genug Erfahrung gesammelt, um einige allgemeine Empfehlungen abzugeben, die sich in der Praxis bewährt haben und für nahezu alle PaaS-Dienste gelten.

Mit der nachfolgenden bewährten Methode können Sie den identitätsorientierten Sicherheitsbereich verwalten.

**Bewährte Methode**: Schützen Sie Ihre Schlüssel und Anmeldeinformationen, um Ihre PaaS-Bereitstellung zu schützen.   
**Detail**: Der Verlust von Schlüsseln und Anmeldeinformationen ist ein verbreitetes Problem. Sie können eine zentrale Lösung verwenden, bei der Schlüssel und Geheimnisse in Hardwaresicherheitsmodulen (HSMs) gespeichert werden. [Azure Key Vault](../../key-vault/general/overview.md) schützt Ihre Schlüssel und Geheimnisse durch Verschlüsseln von Authentifizierungsschlüsseln, Schlüsseln für Speicherkonten, Datenverschlüsselungsschlüsseln, PFX-Dateien und Kennwörtern durch Verwendung von Schlüsseln, die durch HSMs geschützt sind.

**Bewährte Methode**: Speichern Sie Anmeldeinformationen und andere Geheimnisse nicht im Quellcode oder in GitHub.   
**Detail**: Noch schlimmer als der Verlust von Schlüsseln und Anmeldeinformationen ist es, wenn nicht autorisierte Personen an Ihre Schlüssel und Anmeldeinformationen gelangen. Angreifer können mithilfe von Bottechnologien Coderepositorys wie GitHub nach darin gespeicherten Schlüsseln und Geheimnissen durchsuchen. Speichern Sie daher keine Schlüssel oder Geheimnisse in diesen öffentlichen Coderepositorys.

**Bewährte Methode**: Schützen Sie Ihre VM-Verwaltungsschnittstellen in hybriden PaaS- und IaaS-Diensten mithilfe einer Verwaltungsschnittstelle, über die Sie diese virtuellen Computer remote verwalten können.   
**Detail**: Sie können Remoteverwaltungsprotokolle wie [SSH](https://en.wikipedia.org/wiki/Secure_Shell), [RDP](https://support.microsoft.com/kb/186607) und [PowerShell-Remoting](/powershell/module/microsoft.powershell.core/enable-psremoting) verwenden. Im Allgemeinen empfiehlt es sich, über das Internet keinen direkten Remotezugriff auf virtuelle Computer zu ermöglichen.

Verwenden Sie nach Möglichkeit alternative Konzepte wie die Verwendung von virtuellen privaten Netzwerken in einem Azure Virtual Network. Sollten keine Alternativen verfügbar sein, verwenden Sie unbedingt komplexe Passphrasen und eine zweistufige Authentifizierung (beispielsweise [Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)).

**Bewährte Methode**: Verwenden Sie sichere Authentifizierungs- und Autorisierungsplattformen.   
**Detail**: Verwenden Sie Verbundidentitäten in Azure AD anstelle von benutzerdefinierten Benutzerspeichern. Bei Verbundidentitäten profitieren Sie von einem plattformbasierten Konzept und delegieren die Verwaltung autorisierter Identitäten an Ihre Partner. Das Verbundidentitätskonzept ist besonders wichtig, wenn Mitarbeiter gekündigt werden und diese Information in mehreren Identitäts- und Autorisierungssystemen berücksichtigt werden müssen.

Verwenden Sie von der Plattform bereitgestellte Authentifizierungs- und Autorisierungsmechanismen anstelle von benutzerdefiniertem Code. Der Grund: Bei der Entwicklung von benutzerdefiniertem Authentifizierungscode können Ihnen unter Umständen Fehler unterlaufen. Die meisten Ihrer Entwickler sind keine Sicherheitsexperten und deshalb wahrscheinlich nicht mit den Feinheiten und neuesten Entwicklungen im Authentifizierungs- und Autorisierungsbereich vertraut. Die Sicherheit von kommerziellem Code (etwa von Microsoft) wird häufig intensiv geprüft.

Verwenden Sie eine zweistufige Authentifizierung. Die zweistufige Authentifizierung ist der aktuelle Standard für die Authentifizierung und Autorisierung, da sie die Sicherheitsprobleme eliminiert, die für Authentifizierungsmethoden mit Benutzername und Kennwort typisch sind. Der Zugriff auf Azure-Verwaltungsschnittstellen (Portal/Remote-PowerShell) und auf kundenorientierte Dienste sollte für die Verwendung von [Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md) konzipiert und konfiguriert sein.

Verwenden Sie standardmäßige Authentifizierungsprotokolle wie OAuth2 und Kerberos. Diese Protokolle wurden umfassend überprüft und sind wahrscheinlich Teil Ihrer Plattformbibliotheken für die Authentifizierung und Autorisierung.

## <a name="use-threat-modeling-during-application-design"></a>Verwenden von Bedrohungsmodellen beim Anwendungsentwurf
Der Microsoft [Security Development Lifecycle](https://www.microsoft.com/en-us/sdl) gibt an, dass sich Teams während der Entwurfsphase auf einen Prozess einlassen sollten, der als Bedrohungsmodellierung bezeichnet wird. Zur Unterstützung dieses Prozesses hat Microsoft das [SDL Threat Modeling Tool](../develop/threat-modeling-tool.md) erstellt. Durch Modellieren des Anwendungsentwurfs und Aufzählen von [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)-Bedrohungen für alle Vertrauensstellungsgrenzen können Entwurfsfehler frühzeitig abgefangen werden.

In der folgende Tabelle sind die STRIDE-Bedrohungen sowie einige Beispiele für vorbeugende Maßnahmen aufgeführt, bei denen Azure-Funktionen verwendet werden. Diese vorbeugenden Maßnahmen funktionieren nicht in allen Situationen.

| Bedrohung | Sicherheitseigenschaft | Mindern potenzieller Risiken für die Azure-Plattform |
| --- | --- | --- |
| Spoofing | Authentifizierung | Legen Sie fest, dass HTTPS-Verbindungen erforderlich sind. |
| Manipulation | Integrität | Überprüfen Sie die TLS-/SSL-Zertifikate. |
| Nichtanerkennung | Unleugbarkeit | Aktivieren Sie die [Überwachung und Diagnose](/azure/architecture/best-practices/monitoring) von Azure. |
| Veröffentlichung von Informationen | Vertraulichkeit | Verschlüsseln Sie vertrauliche ruhende Daten mithilfe von [Dienstzertifikaten](/rest/api/appservice/certificates). |
| Denial of Service | Verfügbarkeit | Überwachen Sie Leistungsmetriken auf potenzielle Denial-of-Service-Angriffe. Implementieren Sie Verbindungsfilter. |
| Rechteerweiterungen | Authorization | Verwenden Sie [Privileged Identity Management](../../active-directory/privileged-identity-management/subscription-requirements.md). |

## <a name="develop-on-azure-app-service"></a>Entwickeln unter Azure App Service
[Azure App Service](../../app-service/overview.md) ist ein PaaS-Angebot, mit dem Sie webbasierte und mobile Apps für beliebige Plattformen oder Geräte erstellen und eine Verbindung mit Daten herstellen können, die in der Cloud oder lokal gespeichert sind. App Service umfasst die Webfunktionen und mobilen Funktionen, die wir vorher separat als Azure Websites und Azure Mobile Services bereitgestellt haben. Außerdem sind neue Funktionen zum Automatisieren von Geschäftsprozessen und Hosten von Cloud-APIs enthalten. Als einzelner integrierter Dienst stellt App Service einen umfangreichen Satz von Funktionen für mobile, Web- und Integrationsszenarien bereit.

Folgende Methoden haben sich bei der Verwendung von App Service bewährt.

**Bewährte Methode**: [Authentifizieren über Azure Active Directory](../../app-service/overview-authentication-authorization.md).   
**Detail**: App Service bietet einen OAuth 2.0-Dienst für Ihren Identitätsanbieter. In OAuth 2.0 liegt der Schwerpunkt auf der Vereinfachung der Cliententwicklung. Gleichzeitig werden bestimmte Autorisierungsabläufe für Webanwendungen, Desktopanwendungen und Mobiltelefone bereitgestellt. Azure AD verwendet OAuth 2.0, um Ihnen die Autorisierung des Zugriffs auf mobile und Webanwendungen zu ermöglichen.

**Bewährte Methode**: Schränken Sie den Zugriff auf Grundlage der Sicherheitsprinzipien „Need-to-know“ und „geringste Rechte“ ein.   
**Detail**: Das Einschränken des Zugriffs ist für Organisationen zwingend erforderlich, die Sicherheitsrichtlinien für den Datenzugriff durchsetzen möchten. Sie können Azure RBAC verwenden, um Benutzern, Gruppen und Anwendungen Berechtigungen für einen bestimmten Bereich zu erteilen. Weitere Informationen zum Gewähren des Zugriffs auf Anwendungen für Benutzer finden Sie unter [Erste Schritte mit der Zugriffsverwaltung](../../role-based-access-control/overview.md).

**Bewährte Methode**: Schützen Sie Ihre Schlüssel.   
**Detail**: Azure Key Vault unterstützt Sie dabei, kryptografische Schlüssel und Geheimnisse zu schützen, die von Cloudanwendungen und -diensten verwendet werden. Mit Key Vault können Sie Schlüssel und Geheimnisse (beispielsweise Authentifizierungsschlüssel, Schlüssel für Speicherkonten, Datenverschlüsselungsschlüssel, PFX-Dateien und Kennwörter) verschlüsseln, indem Sie Schlüssel verwenden, die durch Hardwaresicherheitsmodule (HSMs) geschützt werden. Zur Steigerung der Sicherheit können Sie Schlüssel in HSMs importieren oder in diesen generieren. Weitere Informationen finden Sie unter [Azure Key Vault](../../key-vault/general/overview.md). Sie können auch Key Vault zum Verwalten Ihrer TLS-Zertifikate mit der automatischen Verlängerung verwenden.

**Bewährte Methode**: Schränken Sie eingehende Quell-IP-Adressen ein.   
**Detail**: Die [App Service-Umgebung](../../app-service/environment/intro.md) verfügt über ein Feature zur Integration virtueller Netzwerke, mit dem Sie eingehende Quell-IP-Adressen über Netzwerksicherheitsgruppen einschränken können. Mit virtuellen Netzwerken können Sie Azure-Ressourcen in einem Netzwerk platzieren, das nicht über das Internet geroutet werden kann, und zu dem Sie den Zugang kontrollieren. Weitere Informationen hierzu finden Sie unter [Integrieren Ihrer App in ein Azure Virtual Network](../../app-service/web-sites-integrate-with-vnet.md).

**Bewährte Methode**: Überwachen Sie den Sicherheitsstatus Ihrer App Service-Umgebungen.   
**Detail**: Verwenden Sie Azure Security Center, um Ihre App Service-Umgebungen zu überwachen. Werden potenzielle Sicherheitslücken erkannt, erstellt Security Center [Empfehlungen](../../security-center/asset-inventory.md), die Sie beim Konfigurieren der erforderlichen Steuerelemente unterstützen.

> [!NOTE]
> Die Überwachung von App Service ist als Vorschauversion und im [Standard-Tarif](../../security-center/security-center-pricing.md) von Security Center verfügbar.
>
>

## <a name="install-a-web-application-firewall"></a>Installieren einer Web Application Firewall
Webanwendungen sind zunehmend Ziele böswilliger Angriffe, die allgemein bekannte Sicherheitslücken ausnutzen. Zu diesen Sicherheitslücken (Exploits) gehören üblicherweise Angriffe durch Einschleusung von SQL-Befehlen oder Angriffe durch websiteübergreifende Skripts, um nur einige zu nennen. Die Verhinderung solcher Angriffe im Anwendungscode ist oft schwierig und erfordert strenge Wartung, Patching und Überwachung auf vielen Ebenen der Anwendungstopologie. Eine zentrale Web Application Firewall vereinfacht die Sicherheitsverwaltung erheblich und bietet Anwendungsadministratoren einen besseren Schutz vor Bedrohungen und Angriffen. Mit einer WAF-Lösung können Sie ebenfalls schneller auf ein Sicherheitsrisiko reagieren, da eine bekannte Schwachstelle an einem zentralen Ort gepatcht wird, statt jede einzelne Webanwendung separat zu sichern. Vorhandene Anwendungsgateways lassen sich problemlos in ein Anwendungsgateway mit Web Application Firewall konvertieren.

[Web Application Firewall (WAF)](../../web-application-firewall/afds/afds-overview.md) ist ein Feature von Application Gateway, das zentralisierten Schutz Ihrer Webanwendungen vor allgemeinen Exploits und Sicherheitsrisiken bietet. Die WAF basiert auf den Regeln der [OWASP-Kernregelsätze (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) der Version 3.0 oder 2.2.9.

## <a name="monitor-the-performance-of-your-applications"></a>Überwachen der Leistung Ihrer Anwendungen
Überwachung ist das Erfassen und Analysieren von Daten, um die Leistung, Integrität und Verfügbarkeit Ihrer Anwendung zu bestimmen. Eine effektive Überwachungsstrategie hilft Ihnen, den detaillierten Einsatz der verschiedenen Komponenten Ihrer Anwendung zu verstehen. Sie hilft Ihnen, die Betriebszeit zu erhöhen, da Sie über kritische Probleme benachrichtigt werden und diese beheben können, bevor sie auftreten. Außerdem können Sie Anomalien erkennen, die möglicherweise sicherheitsrelevant sind.

Verwenden Sie [Azure Application Insights](https://azure.microsoft.com/documentation/services/application-insights), um die Verfügbarkeit, Leistung und Nutzung Ihrer in der Cloud oder lokal gehosteten Anwendung zu überwachen. Durch Verwendung von Application Insights können Sie Fehler in Ihrer Anwendung schnell erkennen und diagnostizieren, ohne darauf warten zu müssen, dass diese Fehler von Benutzern gemeldet werden. Mit den gesammelten Informationen können Sie fundierte Entscheidungen zu Wartung und Verbesserung Ihrer Anwendung vornehmen.

Application Insights verfügt über umfassende Tools für die Interaktion mit den gesammelten Daten. Application Insights speichert die Daten in einem gemeinsamen Repository. Sie können gemeinsame Funktionen wie Warnungen, Dashboards und umfassende Analysen mit der Kusto-Abfragesprache nutzen.

## <a name="perform-security-penetration-testing"></a>Ausführen von Penetrationstests
Das Überprüfen von Abwehrmaßnahmen ist genauso wichtig wie das Testen jeder anderen Funktionalität. Richten Sie [Penetrationstests](pen-testing.md) als standardmäßigen Bestandteil des Build- und Bereitstellungsprozesses ein. Planen Sie regelmäßige Sicherheitstests und Überprüfungen auf Sicherheitsrisiken für bereitgestellte Anwendungen, und überwachen Sie das System auf offene Ports, Endpunkte und Angriffe.

Fuzzing ist eine Methode zum Auffinden von Programmfehlern (Codefehlern) durch Bereitstellung fehlerhafter Eingabedaten für Programmschnittstellen (Einstiegspunkte), die diese Daten analysieren und nutzen. [Microsoft Security Risk Detection](https://www.microsoft.com/en-us/security-risk-detection/) ist ein cloudbasiertes Tool, mit dem Sie vor der Bereitstellung von Software in Azure nach enthaltenen Fehlern und anderen Sicherheitsrisiken suchen können. Das Tool dient zum Erkennen von Sicherheitsrisiken vor der Bereitstellung von Software, sodass Sie nach der Veröffentlichung keinen Fehler patchen, mit Abstürzen umgehen oder auf einen Angriff reagieren müssen.


## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel standen die Sicherheitsvorteile einer Azure-PaaS-Bereitstellung sowie bewährte Methoden für die Sicherheit von Cloudanwendungen im Vordergrund. Als Nächstes gehen wir auf bewährte Vorgehensweisen zum Schutz Ihrer webbasierten und mobilen PaaS-Lösungen mithilfe bestimmter Azure-Dienste ein. Wir beginnen mit Azure App Service, Azure SQL-Datenbank und Azure Synapse Analytics sowie Azure Storage. Sobald Artikel zu empfohlenen Vorgehensweisen für andere Azure-Dienste verfügbar sind, werden in der folgenden Liste entsprechende Links bereitgestellt:

- [Azure App Service](paas-applications-using-app-services.md)
- [Azure SQL Database und Azure Synapse Analytics](paas-applications-using-sql.md)
- [Azure Storage (in englischer Sprache)](paas-applications-using-storage.md)
- Azure Cache for Redis
- Azure-Servicebus
- Web Application Firewalls

Der Artikel [Entwickeln sicherer Anwendungen in Azure](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/) behandelt die Sicherheitsfragen und Steuerelemente, die Sie beim Entwickeln von Anwendungen für die Cloud in den einzelnen Phasen des Softwareentwicklungszyklus berücksichtigen sollten.

Weitere bewährte Methoden für die Sicherheit, die Sie beim Entwerfen, Bereitstellen und Verwalten Ihrer Cloudlösungen mithilfe von Azure verwenden können, finden Sie unter [Sicherheit in Azure: bewährte Methoden und Muster](best-practices-and-patterns.md).

Die folgenden Ressourcen enthalten allgemeinere Informationen zur Sicherheit in Azure und verwandten Microsoft-Diensten:
* [Blog des Azure-Sicherheitsteams](/archive/blogs/azuresecurity/): Hier finden Sie Informationen über den aktuellen Stand der Azure-Sicherheit.
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx): Hier können Sie Microsoft-Sicherheitsrisiken, z.B. Probleme mit Azure, melden oder eine E-Mail an secure@microsoft.com schreiben.