---
title: Problembehandlung in Analysetools für Benutzerverhalten in Azure Application Insights
description: Leitfaden zur Problembehandlung – Analysieren von Website- und Appnutzung mithilfe von Application Insights.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/11/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: eabc47c2acb33d8c6ee03477b5e8c7783edebbb7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371851"
---
# <a name="troubleshoot-user-behavior-analytics-tools-in-application-insights"></a>Problembehandlung in Analysetools für Benutzerverhalten in Application Insights
Haben Sie Fragen zu [Analysetools für das Benutzerverhalten in Application Insights](usage-overview.md): [Benutzer, Sitzungen, Ereignisse](usage-segmentation.md), [Trichter](usage-funnels.md), [Benutzerabläufe](usage-flows.md), [Vermerkdauer](usage-retention.md) oder Kohorten? Hier finden Sie ein paar Antworten.

## <a name="counting-users"></a>Zählen von Benutzern
**Die Analysetools für Benutzerverhalten zeigen an, dass meine App einen Benutzer/eine Sitzung aufweist. Ich weiß aber, dass es mehrere Benutzer/Sitzungen sind. Wie kann ich diesen falschen Zahlenwert korrigieren?**

Alle Telemetrieereignisse in Application Insights weisen als Standardeigenschaften eine [anonyme Benutzer-ID](../../azure-monitor/app/data-model-context.md) und eine [Sitzungs-ID](../../azure-monitor/app/data-model-context.md) auf. Standardmäßig zählen alle Tools zur Nutzungsanalyse die Benutzer und Sitzungen auf der Grundlage dieser IDs. Wenn diese Standardeigenschaften nicht mit eindeutigen IDs für jeden Benutzer und jede Sitzung Ihrer App aufgefüllt werden, wird in den Tools zur Nutzungsanalyse eine falsche Anzahl für Benutzer und Sitzungen angezeigt.

Wenn Sie eine Web-App überwachen, besteht die einfachste Lösung darin, Ihrer App das [Application Insights JavaScript-SDK](../../azure-monitor/app/javascript.md) hinzuzufügen und dafür zu sorgen, dass der Skriptcodeausschnitt auf jeder Seite geladen wird, die Sie überwachen möchten. Das JavaScript-SDK generiert automatisch anonyme Benutzer- und Sitzungs-IDs und füllt die Telemetrieereignisse dann automatisch mit diesen IDs auf, wenn sie von Ihrer App gesendet werden.

Wenn Sie einen Webdienst (keine Benutzeroberfläche) überwachen, [erstellen Sie einen Telemetrieinitialisierer, der die Eigenschaften „anonyme Benutzer-ID“ und „Sitzungs-ID“](usage-send-user-context.md) entsprechend den Auffassungen von eindeutigen Benutzern und Sitzungen Ihres Diensts auffüllt.

Wenn Ihre App [authentifizierte Benutzer-IDs](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) sendet, können Sie anhand von authentifizierten Benutzer-IDs im Tool Benutzer zählen. Wählen Sie in der Dropdownliste „Anzeigen“ „Authentifizierte Benutzer“ aus.

Die Analysetools für Benutzerverhalten unterstützen das Zählen von Benutzern oder Sitzungen aktuell nur auf Grundlage der Eigenschaften „anonyme Benutzer-ID“, „authentifizierte Benutzer-ID“ oder „Sitzungs-ID“.

## <a name="naming-events"></a>Benennen von Ereignissen
**Meine App weist Tausende verschiedener Namen für Seitenansichten und benutzerdefinierte Ereignisse auf. Sie sind nur schwer zu unterscheiden, und die Analysetools für Benutzerverhalten zeigen häufig keine Reaktion. Wie kann ich diese Benennungsprobleme beheben?**

Namen für Seitenansichten und benutzerdefinierte Ereignisse werden überall in den Analysetools für Benutzerverhalten verwendet. Die gute Benennung von Ereignissen ist entscheidend dafür, Werte aus diesen Tools abzurufen. Das Ziel ist ein Gleichgewicht zwischen zu wenigen, übermäßig allgemeinen Namen („Klick auf Schaltfläche“) und zu vielen, übermäßig spezifischen Namen („Klick auf Schaltfläche ‚Bearbeiten‘ auf http:\//www.contoso.com/index“).

Um Änderungen an den Namen von Seitenansichten und benutzerdefinierten Ereignissen vorzunehmen, die von Ihrer App gesendet werden, müssen Sie den Quellcode Ihrer App ändern und sie erneut bereitstellen. **Alle Telemetriedaten in Application Insights werden 90 Tage lang gespeichert und können nicht gelöscht werden**, sodass Änderungen, die Sie an Ereignisnamen vornehmen, erst nach 90 Tagen vollständig umgesetzt sind. Für die 90 Tage nach dem Vornehmen einer Änderung treten in Ihrer Telemetrie sowohl die alten als auch die neuen Namen auf, also passen Sie die Abfragen entsprechend an und kommunizieren Sie dies in Ihren Teams.

Wenn Ihre App zu viele Namen von Seitenansichten sendet, überprüfen Sie, ob diese Namen von Seitenansichten manuell im Code angegeben sind oder automatisch vom Application Insights JavaScript-SDK gesendet werden:

* Wenn die Namen von Seitenansichten manuell mithilfe der [`trackPageView` API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md) im Code angegeben wurden, ändern Sie die Namen in weniger spezifische. Vermeiden Sie häufige Fehler, wie das Einsetzen der URLs in die Namen von Seitenansichten. Verwenden Sie stattdessen den URL-Parameter der `trackPageView` API. Verschieben Sie andere Details aus dem Namen der Seitenansicht in benutzerdefinierte Eigenschaften.

* Wenn das Application Insights JavaScript-SDK Namen von Seitenansichten automatisch sendet, können Sie entweder die Titel Ihrer Seiten ändern oder auf das manuelle Senden der Namen von Seitenansichten umsteigen. Das SDK sendet standardmäßig den [Titel](https://developer.mozilla.org/docs/Web/HTML/Element/title) jeder Seite als Namen der Seitenansicht. Sie können Ihre Titel in allgemeinere ändern, gehen Sie jedoch umsichtig vor, da diese Änderung sich u.a. auf SEO auswirken kann. Beim manuellen Angeben von Namen von Seitenansichten mithilfe der `trackPageView`-API werden die automatisch gesammelten Namen überschrieben, sodass Sie in der Telemetrie allgemeinere Namen senden können, ohne die Seitentitel zu ändern.   

Wenn Ihre App zu viele Namen von benutzerdefinierten Ereignissen sendet, ändern Sie den Namen im Code in einen weniger spezifischen. Vermeiden Sie auch hier das direkte Einfügen von URLs und sonstigen seitenbezogenen oder dynamischen Informationen in die Namen von benutzerdefinierten Ereignissen. Verschieben Sie stattdessen mithilfe der `trackEvent`-API diese Details in benutzerdefinierte Eigenschaften des benutzerdefinierten Ereignisses. Beispielsweise schlagen wir anstelle von `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")` etwas in der Art von `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })` vor.

## <a name="next-steps"></a>Nächste Schritte

* [Verwendungsanalyse mit Application Insights](usage-overview.md)

## <a name="get-help"></a>Hier erhalten Sie Hilfe
* [Stapelüberlauf](https://stackoverflow.com/questions/tagged/ms-application-insights)

