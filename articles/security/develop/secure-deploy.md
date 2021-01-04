---
title: Bereitstellen von sicheren Anwendungen in Microsoft Azure
description: In diesem Artikel sind die bewährten Methoden beschrieben, die in der Freigabe- und in der Reaktionsphase eines Webanwendungsprojekts beachtet werden sollten.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 4c71ddbf1d2b435697b2707acf0b1262f2c5dc31
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517188"
---
# <a name="deploy-secure-applications-on-azure"></a>Bereitstellen von sicheren Anwendungen in Azure
In diesem Artikel werden Sicherheitsaktivitäten und -kontrollen vorgestellt, die Sie berücksichtigen sollten, wenn Sie Anwendungen für die Cloud bereitstellen. Es werden Sicherheitsfragen und -konzepte behandelt, die Sie während der Freigabe- und der Reaktionsphase von Microsoft [Security Development Lifecycle (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) berücksichtigen sollten. Das Ziel ist, Ihnen das Festlegen von Aktivitäten und Azure-Diensten zu ermöglichen, mit denen Sie eine sicherere Anwendung bereitstellen können.

In diesem Artikel werden die folgenden SDL-Phasen behandelt:

- Release
- Antwort

## <a name="release"></a>Release
Der Schwerpunkt der Freigabephase liegt darin, ein Projekt für die öffentliche Freigabe vorzubereiten.
Dazu gehört das Planen von Möglichkeiten, Wartungsaufgaben nach der Freigabe effektiv auszuführen und Sicherheitsrisiken zu beheben, die später auftreten können.

### <a name="check-your-applications-performance-before-you-launch"></a>Testen der Leistung Ihrer Anwendung vor deren Inbetriebnahme

Testen Sie die Leistung Ihrer Anwendung, bevor Sie sie in Betrieb nehmen oder Updates für die in Betrieb befindliche Anwendung bereitstellen. Führen Sie cloudbasierte [Auslastungstests](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) mit Visual Studio aus, um Leistungsprobleme in Ihrer Anwendung zu finden, die Bereitstellungsqualität zu verbessern, sowie sicherzustellen, dass Ihre Anwendung immer aktiv oder verfügbar ist und dass Ihre Anwendung den Datenverkehr für die Inbetriebnahme bewältigen kann.

### <a name="install-a-web-application-firewall"></a>Installieren einer Web Application Firewall

Webanwendungen sind zunehmend Ziele böswilliger Angriffe, die allgemein bekannte Sicherheitslücken ausnutzen. Zu diesen Exploits gehören üblicherweise Angriffe durch Einschleusung von SQL-Befehlen oder Angriffe durch websiteübergreifende Skripts. Ein Verhindern dieser Angriffe in Anwendungscode kann sehr herausfordernd sein. Hierzu können rigorose Wartungs-, Patch- und Überwachungsmaßnahmen auf vielen Ebenen der Anwendungstopologie erforderlich sein. Eine zentralisierte Web Application Firewall (WAF) ermöglicht es, die Sicherheitsverwaltung einfacher zu gestalten. Mit einer WAF-Lösung können Sie außerdem auf ein Sicherheitsrisiko reagieren, indem Sie ein bekanntes Sicherheitsrisiko an einem zentralen Ort patchen, statt jede einzelne Webanwendung zu schützen.

Die [Azure Application Gateway-WAF](../../web-application-firewall/ag/ag-overview.md) bietet zentralisierten Schutz Ihrer Webanwendungen vor gängigen Exploits und Sicherheitsrisiken. Der WAF basiert auf Regeln aus den [OWASP-Kernregelsätzen](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) (3.0 oder 2.2.9).

### <a name="create-an-incident-response-plan"></a>Erstellen eines Plans für Reaktion auf Vorfälle

Das Vorbereiten eines Plans für Reaktion auf Vorfälle ist entscheidend, damit Sie in der Lage sind, neue Bedrohungen zu bewältigen, die im Laufe der Zeit auftreten können. Zum Vorbereiten eines solchen Plans gehören das Bestimmen geeigneter Sicherheitsnotfallkontakte und das Erstellen von Sicherheitswartungsplänen für Code, der von anderen Gruppen in der Organisation übernommen wurde, und für lizenzierten Code von Drittanbietern.

### <a name="conduct-a-final-security-review"></a>Durchführen einer abschließenden Sicherheitsüberprüfung

Eine wohlüberlegte Überprüfung aller durchgeführten Sicherheitsaktivitäten trägt dazu bei, die Einsatzbereitschaft für Ihre Softwareversion oder Anwendung sicherzustellen. Die abschließende Sicherheitsüberprüfung (FSR) beinhaltet in der Regel das Untersuchen der jeweiligen Bedrohungsmodelle, der Ausgaben von Tools und der Leistung anhand der Qualitätsschwellenwerte (Quality Gates) und Fehlerbalken (Bug Bars), die in der Anforderungsphase definiert wurden.

### <a name="certify-release-and-archive"></a>Zertifizieren vor der Freigabe und Archivieren

Das Zertifizieren von Software vor einer Freigabe ermöglicht es sicherzustellen, dass die Sicherheits- und Datenschutzanforderungen erfüllt werden. Das Archivieren aller relevanten Daten ist für das Ausführen von Wartungsaufgaben nach der Freigabe unerlässlich. Archivieren ermöglicht außerdem, die langfristigen Kosten zu senken, die mit kontinuierlicher Softwareentwicklung verbunden sind.

## <a name="response"></a>Antwort
Für die Reaktionsphase nach der Freigabe geht es im Wesentlichen darum, dass das Entwicklungsteam in der Lage und verfügbar ist, angemessen auf alle Berichte über neu auftretende Softwarebedrohungen und Sicherheitsrisiken zu reagieren.

### <a name="execute-the-incident-response-plan"></a>Ausführen des Plans für Reaktion auf Vorfälle

Die Fähigkeit, den in der Freigabephase eingeführten Plan für Reaktion auf Vorfälle zu implementieren, ist unerlässlich, um Kunden vor auftretenden Software- oder Datenschutzsicherheitsrisiken zu schützen.

### <a name="monitor-application-performance"></a>Überwachen der Anwendungsleistung

Die kontinuierliche Überwachung Ihrer Anwendung nach deren Bereitstellung hilft Ihnen wahrscheinlich, Leistungsprobleme und Sicherheitsrisiken zu erkennen.
Azure-Dienste, die bei der Anwendungsüberwachung unterstützen, sind:

  - Azure Application Insights
  - Azure Security Center

#### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) ist ein erweiterbarer, für Webentwickler konzipierter Dienst zur Verwaltung der Anwendungsleistung (Application Performance Management, APM) auf mehreren Plattformen. Verwenden Sie ihn, um Ihre aktiven Webanwendung zu überwachen. Application Insights erkennt automatisch Leistungsanomalien. Er verfügt über leistungsstarke Analysetools, mit denen Sie Probleme diagnostizieren sowie nachvollziehen können, wie Ihre App von den Benutzern tatsächlich verwendet wird. Der Dienst unterstützt Sie bei der kontinuierlichen Verbesserung der Leistung und Benutzerfreundlichkeit Ihrer App.

#### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../../security-center/security-center-introduction.md) unterstützt Sie durch mehr Transparenz und bessere Kontrolle über die Sicherheit Ihrer Azure-Ressourcen samt Webanwendungen dabei, Bedrohungen zu erkennen, zu verhindern und zu beheben. Azure Security Center hilft dabei, Bedrohungen erkennen, die andernfalls möglicherweise nicht bemerkt werden. Es arbeitet mit verschiedenen Sicherheitslösungen.

Der Free-Tarif des Security Center bietet Sicherheit nur für Ihre Azure-Ressourcen. Im [Security Center Standard-Tarif](../../security-center/security-center-get-started.md) werden diese Fähigkeiten auf lokale Ressourcen und andere Clouds ausgedehnt.
Security Center Standard unterstützt Sie bei folgenden Aufgaben:

  - Suchen nach und Beheben von Sicherheitsrisiken
  - Anwenden von Zugriffs- und Anwendungskontrollmechanismen, um böswillige Aktivitäten zu blockieren
  - Erkennen von Bedrohungen durch Verwenden von Analysen und Intelligence
  - Schnelles Reagieren, wenn ein Angriff ausgeführt wird

## <a name="next-steps"></a>Nächste Schritte
In den folgenden Artikeln empfehlen wir die Sicherheitskontrollen und -aktivitäten, die Ihnen beim Entwerfen und Entwickeln von sicheren Anwendungen helfen können.

- [Entwerfen sicherer Anwendungen](secure-design.md)
- [Entwickeln sicherer Anwendungen](secure-develop.md)