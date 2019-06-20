---
title: Azure Application Insights-Telemetriedatenmodell – Anforderungstelemetrie | Microsoft-Dokumentation
description: Application Insights-Datenmodell für Anforderungstelemetrie
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: fef016d87cc60bc916fdcb08f92171e115221fe5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60900514"
---
# <a name="request-telemetry-application-insights-data-model"></a>Anforderungstelemetrie: Application Insights-Datenmodell

Ein Anforderungstelemetrieelement (in [Application Insights](../../azure-monitor/app/app-insights-overview.md)) stellt die logische Reihenfolge der Ausführung dar, die durch eine externe Anforderung bei der Anwendung ausgelöst wird. Jede Anforderungsausführung wird durch eine eindeutige `ID` und `url` identifiziert, die alle Ausführungsparameter enthalten. Sie können Anforderungen nach logischen `name`-Werten gruppieren und die `source` der jeweiligen Anforderung definieren. Die Codeausführung kann zu `success` oder `fail` führen und verfügt über eine bestimmte `duration`. Sowohl erfolgreiche als auch erfolglose Ausführungen können weiter nach `resultCode` gruppiert werden. Die Startzeit für die Anforderungstelemetrie wird auf Umschlagebene definiert.

Die Anforderungstelemetrie unterstützt das Standarderweiterbarkeitsmodell mit benutzerdefinierten `properties` und `measurements`.

## <a name="name"></a>NAME

Der Name der Anforderung gibt den Codepfad für die Verarbeitung der Anforderung an. Ein niedriger Kardinalitätswert ermöglicht die bessere Gruppierung von Anforderungen. Für HTTP-Anforderungen gibt er die HTTP-Methode und die URL-Pfadvorlage wie `GET /values/{id}` ohne den tatsächlichen `id`-Wert an.

Mit dem Application Insights-Web-SDK wird der Anforderungsname im Hinblick auf die Groß-/Kleinschreibung unverändert gesendet. Bei der Gruppierung auf der Benutzeroberfläche wird die Groß-/Kleinschreibung beachtet, sodass `GET /Home/Index` und `GET /home/INDEX` separat gezählt werden, obwohl sie häufig zur gleichen Controller- und Aktionsausführung führen. Dies liegt daran, dass bei URLs allgemein die [Groß-/Kleinschreibung beachtet wird](https://www.w3.org/TR/WD-html40-970708/htmlweb.html). Sie können prüfen, ob für die in Großbuchstaben eingegebenen URLs alle `404` erfolgt sind. Weitere Informationen zur Anforderungsnamensammlung mit dem ASP.NET-Web-SDK finden Sie in diesem [Blogbeitrag](https://apmtips.com/blog/2015/02/23/request-name-and-url/).

Max. Länge: 1024 Zeichen

## <a name="id"></a>ID

Bezeichner einer Anforderungsaufrufinstanz. Wird für die Korrelation zwischen dem Anforderungselement und anderen Telemetrieelementen verwendet. Die ID muss global eindeutig sein. Weitere Informationen hierzu finden Sie auf der Seite [Korrelation](../../azure-monitor/app/correlation.md).

Max. Länge: 128 Zeichen

## <a name="url"></a>url

Anforderungs-URL mit allen Abfragezeichenfolgen-Parametern.

Max. Länge: 2.048 Zeichen

## <a name="source"></a>`Source`

Die Quelle der Anforderung. Beispiele sind der Instrumentierungsschlüssel des Aufrufers oder die IP-Adresse des Aufrufers. Weitere Informationen hierzu finden Sie auf der Seite [Korrelation](../../azure-monitor/app/correlation.md).

Max. Länge: 1024 Zeichen

## <a name="duration"></a>Duration

Dauer der Anforderung im Format `DD.HH:MM:SS.MMMMMM`. Muss positiv sein und unter `1000` Tagen liegen. Dieses Feld ist erforderlich, da die Anforderungstelemetrie den Vorgang mit dem Beginn und dem Ende darstellt.

## <a name="response-code"></a>Antwortcode

Das Ergebnis einer Anforderungsausführung. HTTP-Statuscode für HTTP-Anforderungen. Kann ein `HRESULT`-Wert oder ein Ausnahmetyp für andere Anforderungstypen sein.

Max. Länge: 1024 Zeichen

## <a name="success"></a>Erfolgreich

Angabe eines erfolgreichen oder fehlerhaften Aufrufs. Dies ist ein Pflichtfeld. Wenn der Wert nicht explizit auf `false` festgelegt ist, gilt eine Anforderung als erfolgreich. Legen Sie diesen Wert auf `false` fest, wenn der Vorgang durch eine Ausnahme unterbrochen oder ein Fehlerergebniscode zurückgegeben wurde.

Für Webanwendungen definiert Application Insights eine Anforderung als erfolgreich, wenn der Antwortcode kleiner als `400` oder gleich `401` ist. In manchen Fällen entspricht diese Standardzuordnung jedoch nicht der Semantik der Anwendung. Der Antwortcode `404` kann „keine Datensätze“ angeben, was Teil eines normalen Datenflusses sein kann. Er kann auch einen fehlerhaften Link angeben. Für die fehlerhaften Links können Sie sogar eine erweiterte Logik implementieren. Durch Analysieren des URL-Verweisers können Sie fehlerhafte Links nur als Fehler markieren, wenn diese sich auf derselben Website befinden. Oder Sie können sie als Fehler markieren, wenn der Zugriff darauf über die mobile Anwendung des Unternehmens erfolgt. Auf ähnliche Weise geben `301` und `302` einen Fehler an, wenn der Zugriff über einen Client erfolgt, auf dem die Umleitung nicht unterstützt wird.

Teilweise akzeptierter Inhalt `206` gibt möglicherweise einen Fehler einer gesamten Anforderung an. Ein Application Insights-Endpunkt empfängt beispielsweise einen Batch mit Telemetrieelementen als einzelne Anforderung. Er gibt `206` zurück, wenn einige Elemente in dem Batch nicht erfolgreich verarbeitet wurden. Eine steigende Rate von `206` deutet auf ein Problem hin, das untersucht werden muss. Eine ähnliche Logik gilt für `207` (Multistatus), wo der Erfolg der schlechteste von verschiedenen Antwortcodes sein kann.

Weitere Informationen zu Ergebniscodes und Statuscodes von Anforderungen finden Sie in diesem [Blogbeitrag](https://apmtips.com/blog/2016/12/03/request-success-and-response-code/).

## <a name="custom-properties"></a>Benutzerdefinierte Eigenschaften

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Benutzerdefinierte Messungen

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Schreiben benutzerdefinierter Anforderungstelemetriedaten](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)
- Lesen Sie die Informationen zu den Application Insights-Typen und zum Datenmodell unter [Datenmodell](data-model.md).
- Informieren Sie sich über das [Konfigurieren von ASP.NET Core-Anwendungen](../../azure-monitor/app/asp-net.md) mit Application Insights.
- Lesen Sie die Informationen zu den von Application Insights unterstützten [Plattformen](../../azure-monitor/app/platforms.md).
