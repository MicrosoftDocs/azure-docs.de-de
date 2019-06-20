---
title: Analysieren von JSON- und AVRO-Daten in Azure Stream Analytics
description: Dieser Artikel beschreibt, wie Sie mit komplexen Datentypen wie Arrays, JSON- und CSV-formatierten Daten arbeiten.
services: stream-analytics
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ad30d363c8e3ea0264ba79db5417e572614a6739
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496832"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Analysieren von JSON- und AVRO-Daten in Azure Stream Analytics

Azure Stream Analytics unterstützt die Verarbeitung von Ereignissen in den Datenformaten CSV, JSON und Avro. Sowohl JSON- als auch Avro-Daten können komplexe Typen enthalten, z.B. geschachtelte Objekte (Datensätze) und Arrays.

## <a name="array-data-types"></a>Arraydatentypen

Arraydatentypen sind eine geordnete Sammlung von Werten. Im Folgenden werden einige typische Vorgänge mit Arraywerten beschrieben. Diese Beispiele setzen voraus, dass die Eingabeereignisse eine Eigenschaft namens „arrayField“ besitzen, ein Arraydatentyp.

In diesen Beispielen werden die Funktionen [GetArrayElement](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelement-azure-stream-analytics), [GetArrayElements](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelements-azure-stream-analytics), [GetArrayLength](https://msdn.microsoft.com/azure/stream-analytics/reference/getarraylength-azure-stream-analytics) und der [APPLY](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics)-Operator verwendet.

## <a name="examples"></a>Beispiele
Wählen Sie das Arrayelement am angegebenen Index aus (Auswählen des ersten Arrayelements):

```SQL
SELECT
    GetArrayElement(arrayField, 0) AS firstElement
FROM input
```

Arraylänge auswählen:

```SQL
SELECT
    GetArrayLength(arrayField) AS arrayLength
FROM input
```

Wählen Sie alle Arrayelemente als einzelne Ereignisse aus. Der [APPLY](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics)-Operator extrahiert zusammen mit der integrierten [GetArrayElements](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelements-azure-stream-analytics)-Funktion alle Elemente des Arrays als einzelne Ereignisse:

```SQL
SELECT
    arrayElement.ArrayIndex,
    arrayElement.ArrayValue
FROM input as event
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement
```

## <a name="record-data-types"></a>Datensatz-Datentypen
Mit Datensatz-Datentypen werden JSON- und Avro-Arrays dargestellt, wenn entsprechende Formate in Eingabedatenströmen verwendet werden. Diese Beispiele zeigen einen Beispielsensor, der Eingabeereignisse im JSON-Format liest. Hier ist das Beispiel eines einzelnen Ereignisses:

```json
{
    "DeviceId" : "12345",
    "Location" :
    {
        "Lat": 47,
        "Long": 122
    },
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99
    }
}
```

## <a name="examples"></a>Beispiele
Verwenden Sie die Punktnotation (.), um auf geschachtelte Felder zuzugreifen. Beispielsweise wählt diese Abfrage in den obigen JSON-Daten die Breiten- und Längengradkoordinaten unter der Location-Eigenschaft aus:

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long
FROM input
```

Verwenden Sie die Funktion [GetRecordPropertyValue](https://msdn.microsoft.com/azure/stream-analytics/reference/getrecordpropertyvalue-azure-stream-analytics), wenn der Eigenschaftsname unbekannt ist. Stellen Sie sich z.B. einmal vor, dass ein Beispieldatenstrom mit Referenzdaten verknüpft werden muss, die Schwellenwerte für jeden Gerätesensor enthalten:

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 75
}
```

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName
FROM input
JOIN thresholds
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
```

Um Datensatzfelder in separate Ereignisse zu konvertieren, verwenden Sie den [APPLY](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics)-Operator zusammen mit der [GetRecordProperties](https://msdn.microsoft.com/azure/stream-analytics/reference/getrecordproperties-azure-stream-analytics)-Funktion. Um etwa einen Beispielstrom in einen Datenstrom von Ereignissen mit einzelnen Sensormesswerten zu konvertieren, könnte diese Abfrage verwendet werden:

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

Sie können alle Eigenschaften eines geschachtelten Datensatzes mit dem Platzhalter „*“ auswählen. Betrachten Sie das folgende Beispiel:

```SQL
SELECT input.SensorReadings.*
FROM input
```

Es wird folgendes Ergebnis ausgegeben:

```json
{
    "Temperature" : 80,
    "Humidity" : 70,
    "CustomSensor01" : 5,
    "CustomSensor022" : 99
}
```

## <a name="see-also"></a>Siehe auch
[Datentypen in Azure Stream Analytics](https://msdn.microsoft.com/azure/stream-analytics/reference/data-types-azure-stream-analytics)
