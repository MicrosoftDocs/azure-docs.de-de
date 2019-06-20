---
title: Häufig gestellte Fragen zu Power BI-Arbeitsbereichssammlungen
description: Häufig gestellte Fragen im Zusammenhang mit den Power BI-Arbeitsbereichssammlungen
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: article
ms.workload: powerbi
ms.date: 09/25/2017
ms.openlocfilehash: e3c28cc28261e65e945887d7ad9bfa5fd3984605
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64701580"
---
# <a name="power-bi-workspace-collections-faq"></a>Häufig gestellte Fragen zu Power BI-Arbeitsbereichssammlungen

> [!IMPORTANT]
> Power BI-Arbeitsbereichssammlungen sind veraltet und nur noch bis Juni 2018 (oder bis zum Termin in Ihrem Vertrag) verfügbar. Es wird empfohlen, die Migration zu Power BI Embedded zu planen, um Unterbrechungen für Ihre Anwendung zu vermeiden. Informationen zur Migration Ihrer Daten zu Power BI Embedded finden Sie unter [Migrieren von Inhalten aus Power BI-Arbeitsbereichssammlungen zu Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="what-is-microsoft-power-bi-workspace-collections"></a>Was sind Microsoft Power BI-Arbeitsbereichssammlungen?

Power BI-Arbeitsbereichssammlungen sind ein Azure-Dienst, mit dessen Hilfe Anwendungsentwickler beeindruckende, vollständig interaktive Berichte und Visualisierungen in die für Kunden zugänglichen Apps einbinden können, ohne die Zeit und Kosten für die Erstellung eigener, ganz neuer Steuerelemente aufwenden zu müssen. Die Power BI-Arbeitsbereichssammlungen sind nun mit SLA in neun Rechenzentren auf der ganzen Welt verfügbar. Darüber hinaus haben wir Funktionen wie die Unterstützung von Datensicherheit über RLS (Sicherheit auf Zeilenebene) in den Power BI-Arbeitsbereichssammlungen verbessert, um erweiterte Filtervorgänge zu ermöglichen. Auch das Preismodell für die Power BI-Arbeitsbereichssammlungen wurde vereinfacht und aktualisiert.

## <a name="who-would-want-to-use-microsoft-power-bi-workspace-collections-and-why"></a>Für wen bietet sich die Verwendung der Power BI-Arbeitsbereichssammlungen an und warum?

Die Microsoft Power BI-Arbeitsbereichssammlungen sind für Anwendungsentwickler konzipiert, die ihren Benutzern beeindruckende und interaktive Benutzeroberflächen zur Datenvisualisierung auf allen Geräten bieten möchten, ohne sie selbst erstellen zu müssen. Mit den Power BI-Arbeitsbereichssammlungen können Entwickler topaktuelle Ansichten mit DirectQuery bereitstellen. Zudem können Entwickler Power BI mithilfe der Azure Resource Manager-APIs und der Power BI-APIs programmgesteuert bereitstellen, verwalten und automatisieren. Wie alle Power BI-Komponenten wird der Embedded-Dienst automatisch skaliert, um der Nutzung und den Anforderungen Ihrer Anwendung gerecht zu werden. Der Dienst Power BI-Arbeitsbereichssammlungen umfasst ein Preismodell mit nutzungsbasierter Bezahlung.

## <a name="how-does-power-bi-workspace-collections-relate-to-the-power-bi-service"></a>In welchem Zusammenhang stehen die Power BI-Arbeitsbereichssammlungen und der Power BI-Dienst?

Bei den Power BI-Arbeitsbereichssammlungen und Power BI handelt es sich um separate Angebote. Die Power BI-Arbeitsbereichssammlungen umfassen ein Preismodell mit nutzungsbasierter Bezahlung, werden über das Azure-Portal bereitgestellt und dienen dazu, ISVs das Einbetten von Datenvisualisierungen in Kundenanwendungen zu ermöglichen. Der Power BI-Dienst wird über das Microsoft 365 Admin Center abgerechnet und bereitgestellt. Dieser Dienst ist ein eigenständiges universelles BI-Angebot, das in erster Linie für die interne Verwendung innerhalb von Unternehmen bereitgestellt wird. Weitere Informationen zum Power BI-Dienst finden Sie unter [www.powerbi.com](https://powerbi.microsoft.com).

## <a name="how-does-power-bi-workspace-collections-improve-my-app"></a>Wie verbessern Power BI-Arbeitsbereichssammlungen meine App?

Anwendungen sind deutlich leistungsfähiger, wenn Benutzerentscheidungen mithilfe beeindruckender, interaktiver Datenvisualisierungen direkt in der Anwendung beeinflusst werden können. Mit Power BI-Arbeitsbereichssammlungen können Sie Ihre App durch interaktive, stets aktuelle und umfassende Datenvisualisierungen erweitern, sodass Sie die Zweckmäßigkeit der App sowie Kundenzufriedenheit und -treue erhöhen und mühelos kontextbezogene Analysen auf jedem Gerät bereitstellen können.

## <a name="are-there-any-rules-or-restrictions-about-how-i-can-use-power-bi-workspace-collections-in-my-app"></a>Gibt es Regeln oder Einschränkungen für die Verwendung der Power BI-Arbeitsbereichssammlungen in meiner App?

Die Power BI-Arbeitsbereichssammlungen sind für Anwendungen konzipiert, die zur Verwendung durch Dritte bereitgestellt werden. Wenn Sie den Dienst Power BI-Arbeitsbereichssammlungen zum Erstellen einer internen Geschäftsanwendung verwenden möchten, benötigt jeder Ihrer internen Benutzer eine Power BI Pro-Benutzerabonnementlizenz, und Ihrer Organisation werden zusätzlich zu den Gebühren für die Power BI Pro-Benutzerabonnementlizenzen auch die Kosten für die Nutzung des Diensts Power BI-Arbeitsbereichssammlungen durch Ihre Benutzer in Rechnung gestellt. Um zu verhindern, dass sowohl für Power BI Pro-Benutzerabonnementlizenzen als auch für die Nutzung der Power BI-Arbeitsbereichssammlungen für interne Anwendungen Kosten anfallen, bietet der Power BI-Dienst eigene Funktionen zur Einbettung von Inhalten, die getrennt von den Power BI-Arbeitsbereichssammlungen kostenlos für Benutzer mit Power BI-Benutzerabonnementlizenz bereitgestellt werden (dev.powerbi.com).

## <a name="can-power-bi-workspace-collections-be-used-to-create-internal-applications"></a>Können die Power BI-Arbeitsbereichssammlungen zum Erstellen interner Anwendungen verwendet werden?

Nein. Die Power BI-Arbeitsbereichssammlungen sind nur für die Verwendung durch externe Benutzer gedacht und sollten nicht in internen Geschäftsanwendungen verwendet werden. Um Power BI-Inhalte zur Verwendung in internen Geschäftsanwendungen einzubetten, sollten Sie den Power BI-Dienst nutzen. Außerdem müssen alle Benutzer, die diese Inhalte verwenden, über eine gültige Lizenz im Rahmen eines Power BI Free- oder Power BI Pro-Benutzerabonnements verfügen. Weitere Informationen zur Integration interner Anwendungen in den Power BI-Dienst finden Sie unter [https://dev.powerbi.com](https://dev.powerbi.com).

## <a name="is-this-service-available-globally"></a>Ist dieser Dienst global verfügbar?

Der Dienst Power BI-Arbeitsbereichssammlungen ist jetzt in den meisten Rechenzentren verfügbar. Aktuelle Informationen zur Verfügbarkeit finden Sie [hier](https://azure.microsoft.com/status/).

## <a name="what-is-the-available-sla-for-the-service"></a>Welche SLA ist für den Dienst verfügbar?

Power BI-Arbeitsbereichssammlungen mit der Standard-Azure SLA. Weitere Informationen finden Sie unter [Vereinbarungen zum Servicelevel](https://azure.microsoft.com/support/legal/sla/) .

## <a name="what-is-a-report-session-and-how-is-it-billed"></a>Was ist eine Berichtssitzung, und wie wird sie abgerechnet?

Eine Sitzung besteht aus einer Reihe von Interaktionen zwischen einem Benutzer und einem Bericht der Power BI-Arbeitsbereichssammlungen. Sobald ein Bericht der Power BI-Arbeitsbereichssammlungen für einen Benutzer angezeigt wird, wird eine Sitzung initiiert, und dem Inhaber des Abonnements wird eine Sitzung in Rechnung gestellt. Sitzungen werden unabhängig von der Anzahl visueller Elemente in einem Bericht oder der Aktualisierungshäufigkeit des Berichts zu einem Pauschalpreis in Rechnung gestellt. Eine Sitzung endet, wenn der Benutzer den Bericht schließt oder das Sitzungstimeout von einer Stunde abläuft.

## <a name="do-you-offer-any-tools-or-guidance-to-help-me-estimate-how-many-renderssession-i-should-expect-how-will-i-know-how-many-renders-have-been-completed"></a>Gibt es Tools oder Anleitungen, die mir helfen, die voraussichtliche Anzahl von Renderobjekten pro Sitzung abzuschätzen? Wie kann ich feststellen, wie viele Rendervorgänge abgeschlossen wurden?

Das Azure-Portal stellt Abrechnungsinformationen bereit, die Aufschluss darüber geben, wie viele Renderobjekte pro Berichtssitzung für Ihr Abonnement ausgeführt wurden.

## <a name="do-i-need-a-power-bi-subscription-in-order-to-develop-applications-with-power-bi-workspace-collections-how-do-i-get-started"></a>Benötige ich zum Entwickeln von Anwendungen mit den Power BI-Arbeitsbereichssammlungen ein Power BI-Abonnement? Wie fange ich an?

Als Anwendungsentwickler benötigen Sie kein Power BI-Abonnement, um die Berichte und Visualisierungen zu erstellen, die Sie in Ihrer Anwendung verwenden möchten. Sie benötigen ein Microsoft Azure-Abonnement und die kostenlose Power BI Desktop-Anwendung.

Einzelheiten zur Verwendung von Power BI-Arbeitsbereichssammlungen finden Sie in der Dokumentation zum Dienst.

## <a name="i-have-an-azure-subscription-can-i-use-power-bi-workspace-collections-using-my-existing-subscription"></a>Ich habe ein Azure-Abonnement. Kann ich Power BI-Arbeitsbereichssammlungen mit meinem vorhandenen Abonnement verwenden?

Ja. Sie können den Dienst Power BI-Arbeitsbereichssammlungen mit Ihrem vorhandenen Azure-Abonnement bereitstellen und verwenden.

## <a name="does-my-application-end-user-need-a-power-bi-license"></a>Benötigt ein Endbenutzer meiner Anwendung eine Power BI-Lizenz?

Nein. Benutzer Ihrer Anwendung müssen kein eigenes Power BI-Abonnement erwerben, um auf die In-App-Datenvisualisierungen zuzugreifen. Im Modell Power BI-Arbeitsbereichssammlungen wird der Dienst dem Anwendungsanbieter über den Azure-Verbrauchszähler in Rechnung gestellt. Weitere Informationen finden Sie auf der [Seite mit Preis- und Lizenzierungsinformationen](https://go.microsoft.com/fwlink/?LinkId=760527).

## <a name="how-does-user-authentication-work-with-power-bi-workspace-collections"></a>Wie funktioniert die Benutzerauthentifizierung bei Power BI-Arbeitsbereichssammlungen?

Der Dienst Power BI-Arbeitsbereichssammlungen nutzt App-Token zur Authentifizierung und Autorisierung anstelle der expliziten Benutzerauthentifizierung. Im App-Token-Modell verwaltet Ihre Anwendung die Authentifizierung und Autorisierung für Ihre Benutzer. Bei Bedarf erstellt und sendet Ihre App

dann App-Tokens, die unseren Dienst anweisen, den angeforderten Bericht zu rendern. Bei diesem Konzept muss Ihre App nicht Azure AD für die Authentifizierung und Autorisierung der Benutzer verwenden, auch wenn dies natürlich möglich ist. Weitere Informationen zu App-Token finden Sie [hier](app-token-flow.md). Darüber hinaus haben wir in Power BI-Arbeitsbereichssammlungen die Sicherheit auf Zeilenebene (Row-Level Security, RLS) für Filterszenarios mit erweiterter Sicherheit eingeführt.

## <a name="what-data-sources-are-currently-supported-with-power-bi-workspace-collections"></a>Welche Datenquellen werden derzeit mit Power BI-Arbeitsbereichssammlungen unterstützt?

Wir unterstützen den Zugriff auf Clouddatenquellen, die die Basisanmeldeinformationen über DirectQuery verwenden. Das bedeutet, dass Quellen wie Azure SQL-Datenbank und Azure SQL Data Warehouse zurzeit unterstützt werden. In den nächsten Monaten fügen wir Unterstützung für andere Datenquellen und Zugriffstypen hinzu. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit einer Datenquelle](connect-datasource.md).

## <a name="how-does-the-tenancy-model-work-for-power-bi-workspace-collections"></a>Wie funktioniert das Mandantenmodell für Power BI-Arbeitsbereichssammlungen?

Beim Modell Power BI-Arbeitsbereichssammlungen müssen sich Ihre Kunden nicht unbedingt in Azure AD-Mandanten befinden. Sie können festlegen, ob Azure AD für Ihre Kunden erforderlich ist oder nicht. Daher richtet sich das für Power BI-Arbeitsbereichssammlungen erforderliche Mandantenmodell nach der Architektur Ihrer Anwendung und Ihrer Infrastruktur.

Entwickler/Mitarbeiter, die Ihre Anwendung erstellen oder daran arbeiten, benötigen ein AAD-Benutzerkonto, damit sie Ihr Azure-Abonnement und Ihre Arbeitsbereichssammlungen über das Azure-Portal verwalten können. Programmgesteuerte APIs, mit denen Entwickler Berichte importieren, Verbindungszeichenfolgen ändern und Einbettungs-URLs abrufen können, verwenden stattdessen App-Token zur Authentifizierung und benötigen daher AAD nicht.

## <a name="where-can-i-learn-more"></a>Wo kann ich mehr erfahren?

Auf der [Dokumentationsseite zu Power BI-Arbeitsbereichssammlungen](get-started.md). Weitere aktuelle Informationen finden Sie im [Power BI-Blog](https://powerbi.microsoft.com/blog/) sowie im Power BI Developer Center unter „dev.powerbi.com“. Bei [Stack Overflow](https://stackoverflow.com/questions/tagged/powerbi) können Sie außerdem Fragen stellen.

## <a name="how-do-i-get-started"></a>Wie fange ich an?

Sie können jetzt kostenlos beginnen! Wenn Sie ein Azure-Abonnement besitzen, können Sie Power BI-Arbeitsbereichssammlungen jetzt direkt über das Azure-Portal bereitstellen. Sie können auch ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen. Sobald Sie den Dienst Power BI-Arbeitsbereichssammlungen bereitgestellt haben, können Sie Power BI-REST-APIs problemlos direkt nutzen oder das Developer SDK auf [GitHub](https://go.microsoft.com/fwlink/?LinkID=746472) verwenden. Es werden Beispiele für die Nutzung des Developer SDK bereitgestellt.

## <a name="see-also"></a>Weitere Informationen

[What is Microsoft Power BI Workspace Collections (Was sind Microsoft Power BI-Arbeitsbereichssammlungen?)](what-are-power-bi-workspace-collections.md)
[Get started with Microsoft Power BI Workspace Collections (Erste Schritte mit Microsoft Power BI-Arbeitsbereichssammlungen)](get-started.md)
[Get started with sample (Erste Schritte mit Beispiel)](get-started-sample.md)   
[JavaScript-Einbettungsbeispiel](https://microsoft.github.io/PowerBI-JavaScript/demo/)  

Weitere Fragen? [Power BI-Community ausprobieren](https://community.powerbi.com/)