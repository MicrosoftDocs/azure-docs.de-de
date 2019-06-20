---
title: Strukturieren von Ereignissen in Azure Time Series Insights (Vorschauversion) | Microsoft-Dokumentation
description: Grundlagen zum Strukturieren von Ereignissen mit Azure Time Series Insights Preview.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: f0e1a79073596dcabfacb7163e12b33bb582b7c3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66238927"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Strukturieren von Ereignissen in Azure Time Series Insights Preview

Dieser Artikel bietet Hilfestellung beim Strukturieren Ihrer JSON-Datei, um die Effizienz Ihrer Azure Time Series Insights Preview-Abfragen zu maximieren.

## <a name="best-practices"></a>Bewährte Methoden

Sie berücksichtigen beim Senden von Ereignissen an Time Series Insights Preview verschiedene Aspekte. Insbesondere sollte Folgendes sichergestellt werden:

* Senden Sie Daten so effizient wie möglich über das Netzwerk.
* Speichern Sie Ihre Daten in einer Weise, die Ihnen dabei hilft, sie für Ihr Szenario besser geeignet zu aggregieren.

Gehen Sie wie folgt vor, um die bestmögliche Abfrageleistung zu erzielen:

* Senden Sie keine unnötigen Eigenschaften. Die Abrechnung von Time Series Insights Preview erfolgt nach Nutzung. Es wird empfohlen, die Daten, die Sie abfragen möchten, zu speichern und zu verarbeiten.
* Verwenden Sie Instanzenfelder für statische Daten. Diese Vorgehensweise hilft Ihnen dabei, das Senden von statischen Daten über das Netzwerk zu vermeiden. Instanzenfelder, eine Komponente das Zeitreihenmodells, funktionieren wie Verweisdaten im allgemein verfügbaren Time Series Insights-Dienst. Weitere Informationen zu Instanzenfeldern finden Sie unter [Zeitreihenmodelle](./time-series-insights-update-tsm.md).
* Teilen Sie Dimensionseigenschaften zwischen zwei oder mehr Ereignissen. Diese Vorgehensweise hilft Ihnen dabei, Daten effizienter über das Netzwerk zu senden.
* Verwenden Sie keine tiefe Arrayschachtelung. Time Series Insights Preview unterstützt bis zu zwei Ebenen für geschachtelte Arrays, die Objekte enthalten. Time Series Insights Preview sorgt für flache Arrays in Nachrichten, indem eine Aufteilung in mehrere Ereignisse mit Eigenschaft-Wert-Paaren durchgeführt wird.
* Wenn nur wenige Messwerte für viele oder alle Ereignisse vorhanden sind, ist es besser, diese Messwerte als separate Eigenschaften innerhalb desselben Objekts zu senden. Durch das separate Senden wird die Anzahl von Ereignissen verringert und die Abfrageleistung möglicherweise gesteigert, weil weniger Ereignisse verarbeitet werden müssen.

## <a name="example"></a>Beispiel

Das folgende Beispiel basiert auf einem Szenario, in dem zwei oder mehr Geräte Messungen oder Signale senden. Bei diesen Messungen oder Signalen kann es sich um Werte wie *Flussrate*, *Öldruck*, *Temperatur*und *Feuchtigkeit* handeln.

Im folgenden Beispiel wird eine einzelne Azure IoT Hub-Nachricht verwendet, bei der das äußere Array einen gemeinsam verwendeten Abschnitt für allgemeine Dimensionswerte enthält. Das äußere Array verwendet Zeitreiheninstanzdaten, um die Effizienz der Nachricht zu erhöhen. Eine Zeitreiheninstanz enthält Gerätemetadaten, die sich nicht bei jedem Ereignis ändern, stellt aber nützliche Eigenschaften für die Datenanalyse bereit. Um beim Senden über das Netzwerk Bytes einzusparen und somit die Effizienz der Nachricht zu steigern, erwägen Sie die Batchverarbeitung allgemeiner Dimensionswerte und die Verwendung von Zeitreiheninstanz-Metadaten.

### <a name="example-json-payload"></a>Beispiel-JSON-Nutzlast

```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 1.0172575712203979,
                "Engine Oil Pressure psi ": 34.7
            },
            {
                "Flow Rate ft3/s": 2.445906400680542,
                "Engine Oil Pressure psi ": 49.2
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

### <a name="time-series-instance"></a>Zeitreiheninstanz 
> [!NOTE]
> Die Time Series-ID ist *deviceId*.

```JSON
{
    "timeSeriesId": [
      "FXXX"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "REVOLT SIMULATOR",
      "L2": "Battery System",
    }
  },
  {
    "timeSeriesId": [
      "FYYY"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "COMMON SIMULATOR",
      "L2": "Battery System",
    }
  },
```

Time Series Insights Preview verknüpft eine Tabelle (nach deren Vereinfachung) während der Abfragezeit. Die Tabelle enthält zusätzliche Spalten, z. B. **Typ**. Im folgenden Beispiel wird veranschaulicht, wie Sie Ihre Telemetriedaten [strukturieren](./time-series-insights-send-events.md#json) können.

| deviceId  | Type | L1 | L2 | timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SIMULATOR | Battery System | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SIMULATOR |   Battery System |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA    COMMON | SIMULATOR |    Battery System |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

Beachten Sie im vorangehenden Beispiel die folgenden Punkte:

* Statische Eigenschaften werden in Time Series Insights Preview gespeichert, um über das Netzwerk gesendete Daten zu optimieren.
* Time Series Insights Preview-Daten werden zur Abfragezeit verknüpft, indem die in der Instanz definierte Time Series-ID verwendet wird.
* Es werden zwei Schachtelungsebenen verwendet, wobei es sich um die maximal von Time Series Insights Preview unterstützte Anzahl handelt. Tief verschachtelte Arrays müssen vermieden werden.
* Da nur wenige Messwerte vorliegen, werden sie als separate Eigenschaften innerhalb desselben Objekts gesendet. In dem Beispiel sind **series.Flow Rate psi**, **series.Engine Oil Pressure psi** und **series.Flow Rate ft3/s** eindeutige Spalten.

>[!IMPORTANT]
> Instanzenfelder werden nicht mit Telemetriedaten gespeichert. Sie werden mit Metadaten im **Zeitreihenmodell** gespeichert.
> Die voranstehende Tabelle stellt die Abfrageansicht dar.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Umsetzen dieser Richtlinien finden Sie unter [Azure Time Series Insights Preview-Abfragesyntax](./time-series-insights-query-data-csharp.md). Dort erfahren Sie mehr über die Abfragesyntax der REST-API für den Datenzugriff in Time Series Insights Preview.
- Informationen zu unterstützten JSON-Formen finden Sie unter [Unterstützte JSON-Formen](./time-series-insights-send-events.md#json).
