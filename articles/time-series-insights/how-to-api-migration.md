---
title: Migrieren zu den neuen Azure Time Series Insights Gen2-API-Versionen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Time Series Insights Gen2-Umgebungen aktualisieren, um neue allgemein verfügbare Versionen nutzen zu können.
ms.service: time-series-insights
services: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: shresha
ms.openlocfilehash: 3aedbef079ba62f42ea79afdcd9995d7ee23d9fa
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020724"
---
# <a name="migrating-to-new-azure-time-series-insights-gen2-api-versions"></a>Migrieren zu den neuen Azure Time Series Insights Gen2-API-Versionen

## <a name="overview"></a>Übersicht

Wenn Sie eine Azure Time Series Insights Gen2-Umgebung erstellt haben, als diese Generation sich in der öffentlichen Vorschau befand (vor dem 16. Juli 2020), aktualisieren Sie die Umgebung mithilfe der Schritte in diesem Artikel, um die neuen allgemein verfügbaren Versionen von APIs nutzen zu können. Diese Änderung wirkt sich nicht auf Benutzer aus, die die Gen1-Version von Azure Time Series Insights verwenden.

> [!IMPORTANT]
> Die in diesem Artikel beschriebenen Updates aktualisieren NUR die API-Versionen, die von Ihrer TSI-Umgebung verwendet werden. Diese Änderung steht nicht im Zusammenhang mit den neuen [JSON-Vereinfachungs- und -Escaperegeln](./concepts-json-flattening-escaping-rules.md), die für Gen2-Umgebungen eingeführt wurden.

Die neue API-Version lautet `2020-07-31` und verwendet eine aktualisierte [Syntax für Zeitreihenausdrücke](/rest/api/time-series-insights/reference-time-series-expression-syntax).

Benutzer müssen die für ihre Umgebung geltenden [Variablen des Zeitreihenmodells](./concepts-variables.md), gespeicherten Abfragen, Power BI-Abfragen und benutzerdefinierten Tools migrieren, die Aufrufe an die API-Endpunkte senden. Wenn Sie Fragen oder Bedenken in Bezug auf den Migrationsprozess haben, reichen Sie über das Azure-Portal ein Supportticket ein, und beziehen Sie sich auf dieses Dokument.

> [!IMPORTANT]
> Die API-Vorschauversion `2018-11-01-preview` wird bis 31. Oktober 2020 weiter unterstützt. Führen Sie alle anwendbaren Schritte dieser Migration vor diesem Datum aus, um Dienstunterbrechungen zu vermeiden.

## <a name="migrate-time-series-model-and-saved-queries"></a>Migrieren des Zeitreihenmodells und der gespeicherten Abfragen

Um Benutzer beim Migrieren ihrer [Variablen für Zeitreihenmodelle](./concepts-variables.md) und ihrer gespeicherten Abfragen zu unterstützen, steht im [Azure Time Series Insights-Explorer](https://insights.timeseries.azure.com) ein integriertes Tool zur Verfügung. Navigieren Sie zu der Umgebung, die Sie migrieren möchten, und führen Sie die folgenden Schritte aus. **Sie können die Migration teilweise ausführen und zu einem späteren Zeitpunkt abschließen, aber keine der angewendeten Aktualisierungen kann wieder rückgängig gemacht werden.**

> [!NOTE]
> Sie müssen Mitwirkender in der Umgebung sein, um das Zeitreihenmodell und die gespeicherten Abfragen zu aktualisieren. Wenn Sie kein Mitwirkender sind, können Sie nur Ihre persönlichen gespeicherten Abfragen migrieren. Überprüfen Sie die [Zugriffsrichtlinien für die Umgebung](./concepts-access-policies.md) und Ihre Zugriffsebene, bevor Sie fortfahren.

1. Sie werden vom Explorer aufgefordert, die von den Variablen Ihres Zeitreihenmodells und den gespeicherten Abfragen verwendete Syntax zu aktualisieren.

    [![Eingabeaufforderung](media/api-migration/ux-prompt.png)](media/v2-update-overview/overview-one.png#lightbox)

    Wenn Sie die Benachrichtigung versehentlich schließen, finden Sie sie auch im Benachrichtigungspanel.

1. Klicken Sie auf **Updates anzeigen**, um das Migrationstool zu öffnen.

1. Klicken Sie auf **Typen herunterladen**. Da die Migration Ihre aktuellen Typen überschreibt, um die Variablensyntax zu ändern, müssen Sie eine Kopie Ihrer aktuellen Typen speichern. Das Tool benachrichtigt Sie, wenn Typen heruntergeladen wurden.

    [![Typen herunterladen](media/api-migration/ux-migration-tool.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Klicken Sie auf **Variablen aktualisieren**. Das Tool benachrichtigt Sie, wenn Variablen aktualisiert wurden.

    > [!IMPORTANT]
    > Wenn Sie Typen aktualisieren, müssen benutzerdefinierte Anwendungen, die eine ältere API-Version (`2018-11-01-preview`) verwenden, jetzt die neue API-Version (`2020-07-31`) verwenden, um funktionsfähig zu bleiben. Wenn Sie nicht sicher sind, welche API-Version Sie verwenden, wenden Sie sich an Ihren Administrator, bevor Sie eine Aktualisierung durchführen. Sie können das Migrationstool schließen und später zu diesen Schritten zurückkehren. Informieren Sie sich über das [Migrieren von benutzerdefinierten Anwendungen](#migrate-custom-applications).

    [![Aktualisieren von Variablen](media/api-migration/ux-migration-tool-downloaded-types.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Klicken Sie auf **Gespeicherte Abfragen aktualisieren**. Bei Aktualisierung von gespeicherten Abfragen erhalten Sie vom Tool eine Benachrichtigung.

    [![Aktualisieren von gespeicherten Abfragen](media/api-migration/ux-migration-tool-updated-variables.png)](media/v2-update-overview/overview-one.png#lightbox)

1. Klicken Sie auf **Fertig**.

    [![Abgeschlossene Migration](media/api-migration/ux-migration-tool-updated-saved-queries.png)](media/v2-update-overview/overview-one.png#lightbox)

Überprüfen Sie die aktualisierte Umgebung, indem Sie einige der neu erstellten Variablen und gespeicherten Abfragen auswerten. Wenn Sie bei der Auswertung unerwartete Verhaltensweisen feststellen, senden Sie uns Feedback über das Feedbacktool im Explorer.

## <a name="migrate-power-bi-queries"></a>Migrieren von Power BI-Abfragen

Wenn Sie mithilfe des Power BI-Connectors Abfragen generiert haben, verwenden diese die API-Vorschauversion und die alte Syntax für Zeitreihenausdrücke, um Aufrufe in Azure Time Series Insights auszuführen. Diese Abfragen werden Daten so lange erfolgreich abrufen können, bis die Vorschau-API als veraltet gekennzeichnet wird.

Um Abfragen auf die neue API-Version und die neue Syntax für Zeitreihenausdrücke zu aktualisieren, müssen Sie die Abfragen über den Explorer neu generieren. Informieren Sie sich über das [Erstellen von Abfragen mit dem Power BI-Connector](./how-to-connect-power-bi.md).

> [!NOTE]
> Sie müssen die Power BI Desktop-Version von Juli 2020 verwenden. Andernfalls wird möglicherweise ein [Fehler zu einer ungültigen Version der Abfragenutzlast](./how-to-diagnose-troubleshoot.md#problem-power-bi-connector-shows-unable-to-connect) angezeigt.

## <a name="migrate-custom-applications"></a>Migrieren von benutzerdefinierten Anwendungen

Wenn Ihre benutzerdefinierte Anwendung Aufrufe an die folgenden REST-Endpunkte sendet, ist es ausreichend, die API-Version im URI auf `2020-07-31` zu aktualisieren:

- Time Series-Modell-APIs
  - Modelleinstellungen-APIs
    - [Get](/rest/api/time-series-insights/dataaccessgen2/modelsettings/get)
    - [Aktualisieren](/rest/api/time-series-insights/dataaccessgen2/modelsettings/update)
  - Instanz-APIs
    - [Alle Batchvorgänge](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch)
    - [Liste](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/list)
    - [Suche](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search)
    - [Vorschlagen](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest)
  - Hierarchie-APIs
    - [Alle Batchvorgänge](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch)
    - [Liste](/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/list)
  - Typ-APIs
    - [DELETE-, GET-Vorgänge](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    - [Liste](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/list)

Für die folgenden REST-Endpunkte müssen Sie die API-Version im URI auf `2020-07-31` aktualisieren und sicherstellen, dass alle Vorkommen der `tsx`-Eigenschaft die aktualisierte [Syntax für Zeitreihenausdrücke](/rest/api/time-series-insights/reference-time-series-expression-syntax) verwenden:

- Typ-APIs
  - [PUT-Vorgang](/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput)
- Abfrage-APIs
  - [GetEvents](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)
  - [GetSeries](/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries)
  - [GetAggregateSeries](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)

### <a name="examples"></a>Beispiele

#### <a name="typesbatchput"></a>TypesBatchPut

Alter Anforderungstext (verwendet von `2018-11-01-preview`):

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.[Temperature_Celsius].Double"
          },
          "filter": {
            "tsx": "$event.[Mode].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

Aktualisierter Anforderungstext (verwendet von `2020-07-31`):

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event['Temperature_Celsius'].Double"
          },
          "filter": {
            "tsx": "$event['Mode'].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

Alternativ dazu kann der `filter` auch `$event.Mode.String = 'outdoor'` lauten. Der `value` muss Klammern als Escapezeichen für das Sonderzeichen (`_`) verwenden.

#### <a name="getevents"></a>GetEvents

Alter Anforderungstext (verwendet von `2018-11-01-preview`):

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.[Value].Double != null) OR ($event.[Status].String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

Aktualisierter Anforderungstext (verwendet von `2020-07-31`):

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.Value.Double != null) OR ($event.Status.String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

Alternativ dazu kann der `filter` auch `($event['Value'].Double != null) OR ($event['Status'].String = 'Good')` lauten.

#### <a name="getseries"></a>GetSeries

Alter Anforderungstext (verwendet von `2018-11-01-preview`):

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event.[Bar-Pressure-Offset]"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

Aktualisierter Anforderungstext (verwendet von `2020-07-31`):

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event['Bar-Pressure-Offset']"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

Alternativ dazu kann der `value` auch `$event['Bar-Pressure-Offset'].Double` lauten. Wenn kein Datentyp angegeben ist, wird immer „Double“ angenommen. Die Klammern müssen als Escapezeichen für das Sonderzeichen (`-`) verwendet werden.

#### <a name="aggregateseries"></a>AggregateSeries

Alter Anforderungstext (verwendet von `2018-11-01-preview`):

```JSON
{
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.[Temp].Double, toDouble($event.[Temp].Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

Aktualisierter Anforderungstext (verwendet von `2020-07-31`):

```JSON
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.Temp.Double, toDouble($event.Temp.Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

Alternativ dazu kann der `value` auch `coalesce($event['Temp'].Double, toDouble($event['Temp'].Long))` lauten.

### <a name="potential-errors"></a>Potenzielle Fehler

#### <a name="invalidinput"></a>InvalidInput

Wenn der folgende Fehler angezeigt wird, verwenden Sie die neue API-Version (`2020-07-31`), aber die TSX-Syntax wurde noch nicht aktualisiert. Sehen Sie sich die oben aufgeführten Beispiele für die [Syntax für Zeitreihenausdrücke](/rest/api/time-series-insights/reference-time-series-expression-syntax) und die Migration an. Stellen Sie sicher, dass alle `tsx`-Eigenschaften richtig aktualisiert wurden, bevor Sie die API-Anforderung erneut übermitteln.

```JSON
{
    "error": {
        "code": "InvalidInput",
        "message": "Unable to parse 'value' time series expression (TSX) in variable 'Temperature'.",
        "target": "projectedVariables.Temperature.value",
        "innerError": {
            "parseErrorDetails": [
                {
                    "pos": [
                        0,
                        5
                    ],
                    "line": 1,
                    "msg": "Unsupported Time Series Expression version TSX01 used instead of TSX00.",
                    "code": "UnsupportedTSXVersionTSX01",
                    "target": "$event"
                }
            ],
            "code": "TsxParseError"
        }
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

- Testen Sie Ihre Umgebung mit dem [Azure Time Series Insights-Explorer](./concepts-ux-panels.md) oder mit Ihrer eigenen benutzerdefinierten Anwendung.