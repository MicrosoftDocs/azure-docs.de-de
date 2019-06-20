---
title: Verarbeiten von konfigurierbaren schwellenwertbasierten Regeln in Azure Stream Analytics
description: Dieser Artikel beschreibt, wie Verweisdaten verwendet werden, um eine Warnungslösung zu erreichen, die konfigurierbare schwellenwertbasiere Regeln in Azure Stream Analytics aufweist.
services: stream-analytics
author: zhongc
ms.author: zhongc
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: ce2cf6ebdfd74549114e94e4c7356e387576d3c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60761725"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Verarbeiten von konfigurierbaren schwellenwertbasierten Regeln in Azure Stream Analytics
Dieser Artikel beschreibt, wie Verweisdaten verwendet werden, um eine Warnungslösung zu erreichen, die konfigurierbare schwellenwertbasiere Regeln in Azure Stream Analytics verwendet.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Szenario: Warnungen basierend auf anpassbaren Regelschwellenwerten
Sie müssen möglicherweise eine Warnung als Ausgabe erzeugen, wenn eingehende gestreamte Ereignisse einen bestimmten Wert erreicht haben oder wenn ein aggregierter Wert basierend auf den eingehenden gestreamten Ereignissen einen bestimmten Schwellenwert überschreitet. Es ist einfach, eine Stream Analytics-Abfrage einzurichten, die einen Wert mit einem statischen Schwellenwert vergleicht, der festgelegt und vordefiniert ist. Ein fester Schwellenwert kann in der Streaming-Abfragesyntax mit einfachen numerischen Vergleichen (größer als, kleiner als, gleich) hartcodiert werden.

In einigen Fällen müssen die Schwellenwerte leichter konfigurierbar sein, ohne bei jeder Änderung eines Schwellenwerts die Abfragesyntax zu bearbeiten. In anderen Fällen müssen möglicherweise zahlreiche Geräte oder Benutzer von der gleichen Abfrage mit jeweils unterschiedlichen Schwellenwerten für jede Art von Gerät verarbeitet werden. 

Dieses Muster kann verwendet werden, um dynamisch Schwellenwerte zu konfigurieren, durch Filtern der Eingabedaten einzeln auszuwählen, für welche Art von Gerät der Schwellenwert gilt, und einzeln auszuwählen, welche Felder in die Ausgabe aufgenommen werden sollen.

## <a name="recommended-design-pattern"></a>Empfohlenes Entwurfsmuster
Verwenden Sie eine Verweisdateneingabe für einen Stream Analytics-Auftrag zum Nachschlagen der Warnungsschwellenwerte:
- Speichern Sie die Schwellenwerte in den Verweisdaten, ein Wert pro Schlüssel.
- Verknüpfen Sie die Streamingdaten-Eingabeereignisse mit den Verweisdaten in der Schlüsselspalte.
- Verwenden Sie den Schlüsselwert der Verweisdaten als Schwellenwert.

## <a name="example-data-and-query"></a>Beispieldaten und -abfragen
Im Beispiel werden Warnungen generiert, wenn das Aggregat der von Geräten eingehenden Daten in einem Zeitfenster von einer Minute mit den Werten übereinstimmt, die in der Regel festgelegt wurden, die als Verweisdaten angegeben wurde.

In der Abfrage können Sie für jede „deviceID“ und jeden „metricName“ unter der „deviceID“ 0 bis 5 Dimensionen zum Gruppieren konfigurieren. Nur die Ereignisse mit den entsprechenden Filterwerten werden gruppiert. Nach der Gruppierung werden Fensteraggregate für Minimum, Maximum und Durchschnitt über ein rollierendes Zeitfenster von 60 Sekunden berechnet. Dann werden Filter für die aggregierten Werte gemäß dem konfigurierten Schwellenwert im Verweis berechnet, um das Warnungsausgabeereignis zu generieren.

Beispiel: Angenommen, ein Stream Analytics-Auftrag hat eine Verweisdateneingabe mit dem Namen **rules** und eine Streaming-Dateneingabe mit dem Namen **metrics**. 

## <a name="reference-data"></a>Verweisdaten
Die Verweisdaten dieses Beispiels zeigen, wie eine schwellenwertbasierte Regel dargestellt werden könnte. Eine JSON-Datei enthält die Verweisdaten und wird im Azure-Blobspeicher gespeichert. Dieser Blobspeichercontainer dient als Verweisdateneingabe mit dem Namen **rules**. Mit der Zeit könnten Sie diese JSON-Datei überschreiben und die Regelkonfiguration ersetzen, ohne den Streamingauftrag zu beenden oder zu starten.

- Die Beispielregel wird verwendet, um eine anpassbare Warnung darzustellen, wenn die CPU-Auslastung den Wert `90` Prozent überschreitet (Durchschnitt ist größer als oder gleich). Das Feld `value` kann nach Bedarf konfiguriert werden.
- Beachten Sie, dass die Regel ein Feld **operator** aufweist, das später in der Abfragesyntax mit `AVGGREATEROREQUAL` dynamisch interpretiert wird. 
- Die Regel filtert die Daten mit einem bestimmten Dimensionsschlüssel `2` mit dem Wert `C1`. Andere Felder sind leere Zeichenfolgen. Damit wird angegeben, dass der Eingabestream nicht nach diesen Ereignisfeldern gefiltert werden soll. Sie können zusätzliche CPU-Regeln einrichten, um andere übereinstimmende Felder nach Bedarf zu filtern.
- Nicht alle Spalten müssen in das Ausgabewarnungsereignis aufgenommen werden. In diesem Fall wird die `includedDim`-Schlüsselnummer `2` auf `TRUE` festgelegt, um darzustellen, dass Feldnummer 2 der Ereignisdaten im Stream in die qualifizierenden Ausgabeereignisse eingeschlossen wird. Die anderen Felder werden nicht in die Warnungsausgabe aufgenommen. Die Feldliste kann allerdings angepasst werden.


```json
{
    "ruleId": 1234, 
    "deviceId" : "978648", 
    "metricName": "CPU", 
    "alertName": "hot node AVG CPU over 90",
    "operator" : "AVGGREATEROREQUAL",
    "value": 90, 
    "includeDim": {
        "0": "FALSE", 
        "1": "FALSE", 
        "2": "TRUE", 
        "3": "FALSE", 
        "4": "FALSE"
    },
    "filter": {
        "0": "", 
        "1": "",
        "2": "C1", 
        "3": "", 
        "4": ""
    }    
}
```

## <a name="example-streaming-query"></a>Beispiel einer Streamingabfrage
Diese Stream Analytics-Beispielabfrage verknüpft die **rules**-Verweisdaten aus dem Beispiel oben mit einem Eingabestream von Daten mit dem Namen **metrics**.

```sql
WITH transformedInput AS
(
    SELECT
        dim0 = CASE rules.includeDim.[0] WHEN 'TRUE' THEN metrics.custom.dimensions.[0].value ELSE NULL END,
        dim1 = CASE rules.includeDim.[1] WHEN 'TRUE' THEN metrics.custom.dimensions.[1].value ELSE NULL END,
        dim2 = CASE rules.includeDim.[2] WHEN 'TRUE' THEN metrics.custom.dimensions.[2].value ELSE NULL END,
        dim3 = CASE rules.includeDim.[3] WHEN 'TRUE' THEN metrics.custom.dimensions.[3].value ELSE NULL END,
        dim4 = CASE rules.includeDim.[4] WHEN 'TRUE' THEN metrics.custom.dimensions.[4].value ELSE NULL END,
        metric = metrics.metric.value,
        metricName = metrics.metric.name,
        deviceId = rules.deviceId, 
        ruleId = rules.ruleId, 
        alertName = rules.alertName,
        ruleOperator = rules.operator, 
        ruleValue = rules.value
    FROM 
        metrics
        timestamp by eventTime
    JOIN 
        rules
        ON metrics.deviceId = rules.deviceId AND metrics.metric.name = rules.metricName
    WHERE
        (rules.filter.[0] = '' OR metrics.custom.filters.[0].value = rules.filter.[0]) AND 
        (rules.filter.[1] = '' OR metrics.custom.filters.[1].value = rules.filter.[1]) AND
        (rules.filter.[2] = '' OR metrics.custom.filters.[2].value = rules.filter.[2]) AND
        (rules.filter.[3] = '' OR metrics.custom.filters.[3].value = rules.filter.[3]) AND
        (rules.filter.[4] = '' OR metrics.custom.filters.[4].value = rules.filter.[4])
)

SELECT
    System.Timestamp as time, 
    transformedInput.deviceId as deviceId,
    transformedInput.ruleId as ruleId,
    transformedInput.metricName as metric,
    transformedInput.alertName as alert,
    AVG(metric) as avg,
    MIN(metric) as min, 
    MAX(metric) as max, 
    dim0, dim1, dim2, dim3, dim4
FROM
    transformedInput
GROUP BY
    transformedInput.deviceId,
    transformedInput.ruleId,
    transformedInput.metricName,
    transformedInput.alertName,
    dim0, dim1, dim2, dim3, dim4,
    ruleOperator, 
    ruleValue, 
    TumblingWindow(second, 60)
HAVING
    (
        (ruleOperator = 'AVGGREATEROREQUAL' AND avg(metric) >= ruleValue) OR
        (ruleOperator = 'AVGEQUALORLESS' AND avg(metric) <= ruleValue) 
    )
```

## <a name="example-streaming-input-event-data"></a>Beispiel von Streaming-Eingabeereignisdaten
Diese JSON-Beispieldaten stellen die **metrics**-Eingabedaten dar, die in der obigen Streamingabfrage verwendet werden. 

- Drei Beispielereignisse sind für die Zeitspanne von einer Minute aufgeführt, Wert `T14:50`. 
- Alle drei weisen den gleichen `deviceId`-Wert `978648` auf.
- Die CPU-Metrikwerte sind in jedem Ereignis anders: `98`, `95` bzw. `80`. Nur die ersten beiden Beispielereignisse überschreiten die CPU-Warnungsregel, die in der Regel eingerichtet wurde.
- Das Feld „includeDim“ in der Regel war Schlüsselnummer 2. Das entsprechende Feld für Schlüssel 2 in den Beispielereignissen heißt `NodeName`. Die drei Beispielereignisse weisen die Werte `N024`, `N024` bzw. `N014` auf. In der Ausgabe sehen Sie nur den Knoten `N024`, da dies der einzige Wert ist, der den Warnungskriterien für hohe CPU-Auslastung entspricht. `N014` erfüllt den Schwellenwert für hohe CPU-Auslastung nicht.
- Die Warnungsregel ist so konfiguriert, dass mit `filter` nur nach Schlüsselnummer 2 gefiltert wird. Dies entspricht dem Feld `cluster` in den Beispielereignissen. Die drei Beispielereignisse haben den Wert `C1` und entsprechen den Filterkriterien.

```json
{
    "eventTime": "2018-04-30T14:50:23.1324132Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N1"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N1"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 98,
        "count": 1.0,
        "min": 98,
        "max": 98,
        "stdDev": 0.0
    }
}
{
    "eventTime": "2018-04-30T14:50:24.1324138Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N2"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N2"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 95,
        "count": 1,
        "min": 95,
        "max": 95,
        "stdDev": 0
    }
}
{
    "eventTime": "2018-04-30T14:50:37.1324130Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N3"
            },
            "1": {
                "name": "Cluster",
                "value": "C1 "
            },
            "2": {
                "name": "NodeName",
                "value": "N014"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N3"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 80,
        "count": 1,
        "min": 80,
        "max": 80,
        "stdDev": 0
    }
}
```

## <a name="example-output"></a>Beispielausgabe
Diese Beispielausgabe von JSON-Daten zeigt, dass ein einzelnes Warnungsereignis basierend auf der CPU-Schwellenwertregel erzeugt wurde, die in den Verweisdaten definiert ist. Das Ausgabeereignis enthält den Namen der Warnung sowie das Aggregat (Durchschnitt, Minimum, Maximum) der betrachteten Felder. Die Ausgabeereignisdaten enthalten aufgrund der Regelkonfiguration Feldschlüsselnummer 2 `NodeName` mit dem Wert `N024`. (JSON wurde geändert, um Zeilenumbrüche zur besseren Lesbarkeit anzuzeigen.)

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```