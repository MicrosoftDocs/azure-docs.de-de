---
title: Teilen von benutzerdefinierten Azure Time Series Insights-Ansichten mit parametrisierten URLs | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie parametrisierte URLs in Azure Time Series Insights entwickeln, damit eine Kundenansicht schnell geteilt werden kann.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: conceptual
ms.workload: big-data
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: dfc04397b1d7e9f3256810cbe469067ae52c99bd
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66238972"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Teilen einer benutzerdefinierten Ansicht mit einer parametrisierten URL

Sie können eine parametrisierte URL der benutzerdefinierten Ansicht programmgesteuert erstellen, um im Time Series Insights-Explorer eine benutzerdefinierte Ansicht zu teilen.

Der Time Series Insights-Explorer unterstützt URL-Abfrageparameter, um Ansichten auf der Benutzeroberfläche direkt über die URL anzugeben. Indem Sie nur die URL verwenden, können Sie beispielsweise eine Zielumgebung, ein Suchprädikat und eine gewünschte Zeitspanne angeben. Wenn ein Benutzer auf die benutzerdefinierte URL klickt, stellt die Oberfläche einen direkten Link zu diesem Objekt im Time Series Insights-Portal bereit. Es gelten die Richtlinien für den Datenzugriff.

> [!TIP]
> * Zeigen Sie die kostenlose [Time Series Insights-Demo](https://insights.timeseries.azure.com/samples) an.
> * Lesen Sie die zugehörige [Time Series Insights-Explorer](./time-series-insights-explorer.md)-Dokumentation.

## <a name="environment-id"></a>Umgebungs-ID

Mit dem Parameter `environmentId=<guid>` wird die ID der Zielumgebung angegeben. Sie ist eine Komponente des FQDN für den Datenzugriff und im Azure-Portal oben rechts in der Umgebungsübersicht zu finden. Es ist die gesamte Angabe, die vor `env.timeseries.azure.com` steht.

Ein Beispielparameter für die Umgebungs-ID ist `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Time

Sie können mit einer parametrisierten URL absolute oder relative Zeitwerte angeben.

### <a name="absolute-time-values"></a>Absolute Zeitwerte

Verwenden Sie für absolute Zeitwerte die Parameter `from=<integer>` und `to=<integer>`.

* `from=<integer>` ist ein Wert in JavaScript-Millisekunden für die Startzeit der Suchzeitspanne.
* `to=<integer>` ist ein Wert in JavaScript-Millisekunden für die Endzeit der Suchzeitspanne.

Informationen zum Identifizieren der JavaScript-Millisekunden für ein Datum finden Sie unter [Epoch & Unix Timestamp Converter](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html) (Epochen/Unix-Zeitstempelkonvertierung).

### <a name="relative-time-values"></a>Relative Zeitwerte

Verwenden Sie für einen relativen Zeitwert `relativeMillis=<value>`, wobei *value* für einen Wert in JavaScript-Millisekunden der letzten Daten auf dem Back-End steht.

Für `&relativeMillis=3600000` werden beispielsweise die Daten der letzten 60 Minuten angezeigt.

Die akzeptierten Werte entsprechen dem Menü **quick time** (Zeitauswahl) im Time Series Insights-Explorer und umfassen Folgendes:

* `1800000` (Letzte 30 Minuten)
* `3600000` (Letzte 60 Minuten)
* `10800000` (Letzte 3 Stunden)
* `21600000` (Letzte 6 Stunden)
* `43200000` (Letzte 12 Stunden)
* `86400000` (Letzte 24 Stunden)
* `604800000` (Letzte 7 Tage)
* `2592000000` (Letzte 30 Stunden)

### <a name="optional-parameters"></a>Optionale Parameter

Mit dem Parameter `timeSeriesDefinitions=<collection of term objects>` werden die Begriffe einer Time Series Insights-Ansicht angegeben:

| Parameter | URL-Element | BESCHREIBUNG |
| --- | --- | --- |
| **name** | `\<string>` | Der Name der *Bedingung*. |
| **splitBy** | `\<string>` | Der Spaltenname für *Teilen nach*. |
| **measureName** | `\<string>` | Der Spaltenname des *Measure*. |
| **predicate** | `\<string>` | Die *where*-Klausel für die serverseitige Filterung. |
| **useSum** | `true` | Ein optionaler Parameter, der die Verwendung von „Summe“ für Ihr Measure angibt. </br>  Hinweis: Wenn `Events` als Measure ausgewählt wurde, ist standardmäßig „Anzahl“ ausgewählt.  </br>  Wenn `Events` nicht ausgewählt wurde, ist standardmäßig „Durchschnitt“ ausgewählt. |

* Das Schlüssel-Wert-Paar `multiChartStack=<true/false>` aktiviert das Stapeln im Diagramm.
* Das Schlüssel-Wert-Paar `multiChartSameScale=<true/false>` aktiviert dieselbe Y-Achsenskala begriffsübergreifend Innerhalb eines optionalen Parameters.  
* Mit `timeBucketUnit=<Unit>&timeBucketSize=<integer>` können Sie den Intervallschieberegler anpassen, um eine präzisere oder eine glattere, stärker aggregierte Darstellung des Diagramms zu erhalten.  
* Mit dem Parameter `timezoneOffset=<integer>` können Sie die gewünschte Zeitzone für das Diagrammdarstellung als UTC-Versatz festlegen.

| Paar(e) | BESCHREIBUNG |
| --- | --- |
| `multiChartStack=false` | Weil `true` standardmäßig aktiviert ist,übergeben Sie `false` an den Stapel. |
| `multiChartStack=false&multiChartSameScale=true` | Das Stapeln muss aktiviert sein, um ausdrucksübergreifend die gleiche Y-Achsenskala verwenden zu können.  Diese Funktion ist standardmäßig auf `false` festgelegt. Durch Übergabe von „true“ wird sie aktiviert. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Einheiten: Tage, Stunden, Minuten, Sekunden und Millisekunden.  Die Einheit muss immer groß geschrieben werden. </br> Übergeben Sie zum Definieren der Anzahl von Einheiten die gewünschte ganze Zahl für „TimeBucketSize“.  Hinweis: Die Glättung kann für bis zu sieben Tage erfolgen.  |
| `timezoneOffset=-<integer>` | Bei der ganzen Zahl handelt es sich immer um Millisekunden. </br> Diese Funktion unterscheidet sich geringfügig von der aktivierten Funktion im Time Series Insights-Explorer, die es ermöglicht, die Ortszeit (gemäß Browser) oder UTC auszuwählen. |

### <a name="examples"></a>Beispiele

Um Zeitreihendefinitionen einer Time Series Insights-Umgebung als URL-Parameter hinzuzufügen, fügen Sie Folgendes an:

```plaintext
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Verwenden Sie das Beispiel zu Zeitreihendefinitionen für:

* Die Umgebungs-ID
* Die Daten der letzten 60 Minuten
* Die Begriffe („F1PressureID“, „F2TempStation“ und „F3VibrationPL“), aus denen die optionalen Parameter bestehen

Mit diesen Elementen können Sie die folgende parametrisierte URL für eine Ansicht erstellen:

```plaintext
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

> [!TIP]
> Schauen Sie sich den Explorer live [über die URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]) an.

Die vorstehende URL beschreibt und erstellt die Time Series Insights-Explorer-Ansicht:

[![Begriffe im Time Series Insights-Explorer](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

Die vollständige Ansicht (einschließlich des Diagramms):

[![Diagrammansicht](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über das [Abfragen von Daten mithilfe von C#](time-series-insights-query-data-csharp.md).

* Informieren Sie sich über den [Time Series Insights-Explorer](./time-series-insights-explorer.md).
