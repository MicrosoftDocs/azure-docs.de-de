---
title: Analysieren von JSON- und AVRO-Daten in Azure Stream Analytics
description: Dieser Artikel beschreibt, wie Sie mit komplexen Datentypen wie Arrays, JSON- und CSV-formatierten Daten arbeiten.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 01/29/2020
ms.custom: devx-track-js
ms.openlocfilehash: 6c2eb4225cb014b3251d12470e4e9827150a5cf2
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123352"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Analysieren von JSON- und AVRO-Daten in Azure Stream Analytics

Azure Stream Analytics unterstützt die Verarbeitung von Ereignissen in den Datenformaten CSV, JSON und AVRO. Sowohl JSON- als auch AVRO-Daten können strukturiert sein und komplexe Typen enthalten, z. B. geschachtelte Objekte (Datensätze) und Arrays. 

>[!NOTE]
>Von Event Hub Capture erstellte AVRO-Dateien verwenden ein bestimmtes Format, bei dem Sie die Funktion *benutzerdefinierter Deserialisierer* verwenden müssen. Weitere Informationen finden Sie unter [Lesen von Eingabe in beliebigen Formaten mithilfe benutzerdefinierter .NET-Deserialisierer](./custom-deserializer-examples.md).
>
>Die AVRO-Deserialisierung von Stream Analytics unterstützt den Typ Zuordnung nicht. Stream Analytics kann Erfassungs-Blobs von EventHub nicht lesen, da die EventHub-Erfassung Zuordnung verwendet.


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
        "CustomSensor02" : 99,
        "SensorMetadata" : 
        {
        "Manufacturer":"ABC",
        "Version":"1.2.45"
        }
    }
}
```

### <a name="access-nested-fields-in-known-schema"></a>Zugreifen auf geschachtelte Felder in einem bekannten Schema
Verwenden Sie die Punktnotation (.), um ganz einfach über Ihre Abfrage auf geschachtelte Felder zuzugreifen. Beispielsweise wählt diese Abfrage in den obigen JSON-Daten die Breiten- und Längengradkoordinaten unter der Location-Eigenschaft aus. Die Punktnotation kann verwendet werden, um wie im Folgenden dargestellt zwischen mehreren Ebenen zu navigieren.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.Temperature,
    SensorReadings.SensorMetadata.Version
FROM input
```

Es wird folgendes Ergebnis ausgegeben:

|DeviceID|Lat|Long|Temperatur|Version|
|-|-|-|-|-|
|12345|47|122|80|1.2.45|


### <a name="select-all-properties"></a>Auswählen aller Eigenschaften
Sie können alle Eigenschaften eines geschachtelten Datensatzes mit dem Platzhalter „*“ auswählen. Betrachten Sie das folgenden Beispiel:

```SQL
SELECT
    DeviceID,
    Location.*
FROM input
```

Es wird folgendes Ergebnis ausgegeben:

|DeviceID|Lat|Long|
|-|-|-|
|12345|47|122|


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Zugreifen auf geschachtelte Felder, wenn der Eigenschaftenname eine Variable ist

Verwenden Sie die Funktion [GetRecordPropertyValue](/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics), wenn der Eigenschaftenname eine Variable ist. Dies ermöglicht die Erstellung dynamischer Abfragen, ohne Eigenschaftsnamen hart codieren zu müssen.

Stellen Sie sich z. B. einmal vor, dass der Beispieldatenstrom **mit Referenzdaten verknüpft werden muss** , die Schwellenwerte für jeden Gerätesensor enthalten. Nachfolgend finden Sie einen Ausschnitt aus solchen Referenzdaten.

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 85
},
{
    "DeviceId" : "12345",
    "SensorName" : "Humidity",
    "Value" : 65
}
```

Das Ziel besteht darin, unser Beispieldataset vom Anfang des Artikels mit diesen Referenzdaten zu verknüpfen und ein Ereignis für jedes Sensormaß auszugeben, das über dem Schwellenwert liegt. Dies bedeutet, dass unser obiges einzelnes Ereignis dank der Verknüpfung mehrere Ausgabeereignisse generieren kann, wenn mehrere Sensoren über ihren jeweiligen Schwellenwerten liegen. Informationen, wie Sie ähnliche Ergebnisse ohne eine Verknüpfung erzielen können, finden Sie im Abschnitt weiter unten.

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName,
    "Alert : Sensor above threshold" AS AlertMessage
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
```

**GetRecordPropertyValue** wählt die Eigenschaft in *SensorReadings* aus, deren Name mit dem Eigenschaftsnamen aus den Referenzdaten übereinstimmt Anschließend wird der zugehörige Wert aus *SensorReadings* extrahiert.

Es wird folgendes Ergebnis ausgegeben:

|DeviceID|SensorName|AlertMessage|
|-|-|-|
|12345|Luftfeuchtigkeit|Warnung: Sensor über Schwellenwert|

### <a name="convert-record-fields-into-separate-events"></a>Konvertieren von Eintragsfeldern in separate Ereignisse

Um Datensatzfelder in separate Ereignisse zu konvertieren, verwenden Sie den [APPLY](/stream-analytics-query/apply-azure-stream-analytics)-Operator zusammen mit der [GetRecordProperties](/stream-analytics-query/getrecordproperties-azure-stream-analytics)-Funktion.

Mit den ursprünglichen Beispieldaten könnte die folgende Abfrage verwendet werden, um Eigenschaften in verschiedene Ereignisse zu extrahieren.

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

Es wird folgendes Ergebnis ausgegeben:

|DeviceID|SensorName|AlertMessage|
|-|-|-|
|12345|Temperatur|80|
|12345|Luftfeuchtigkeit|70|
|12345|CustomSensor01|5|
|12345|CustomSensor02|99|
|12345|SensorMetadata|[object Object]|

Wenn Sie [WITH](/stream-analytics-query/with-azure-stream-analytics) verwenden, ist es dann möglich, diese Ereignisse an verschiedene Ziele weiterzuleiten:

```SQL
WITH Stage0 AS
(
    SELECT
        event.DeviceID,
        sensorReading.PropertyName,
        sensorReading.PropertyValue
    FROM input as event
    CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
)

SELECT DeviceID, PropertyValue AS Temperature INTO TemperatureOutput FROM Stage0 WHERE PropertyName = 'Temperature'
SELECT DeviceID, PropertyValue AS Humidity INTO HumidityOutput FROM Stage0 WHERE PropertyName = 'Humidity'
```

### <a name="parse-json-record-in-sql-reference-data"></a>Analysieren des JSON-Datensatzes in SQL-Verweisdaten
Bei der Verwendung von Azure SQL-Datenbank als Referenzdaten in Ihrem Auftrag können Sie eine Spalte mit Daten im JSON-Format nutzen. Ein entsprechendes Beispiel ist nachfolgend dargestellt.

|DeviceID|Daten|
|-|-|
|12345|{"key" : "value1"}|
|54321|{"key" : "value2"}|

Sie können den JSON-Datensatz in der Spalte *Data* analysieren, indem Sie eine einfache benutzerdefinierte JavaScript-Funktion schreiben.

```javascript
function parseJson(string) {
return JSON.parse(string);
}
```

Sie können dann in Ihrer Stream Analytics-Abfrage wie nachfolgend gezeigt einen Schritt erstellen, um auf die Felder Ihrer JSON-Einträge zuzugreifen:

 ```SQL
 WITH parseJson as
 (
 SELECT DeviceID, udf.parseJson(sqlRefInput.Data) as metadata,
 FROM sqlRefInput
 )
 
 SELECT metadata.key
 INTO output
 FROM streamInput
 JOIN parseJson 
 ON streamInput.DeviceID = parseJson.DeviceID
```

## <a name="array-data-types"></a>Arraydatentypen

Arraydatentypen sind eine geordnete Sammlung von Werten. Im Folgenden werden einige typische Vorgänge mit Arraywerten beschrieben. In diesen Beispielen werden die Funktionen [GetArrayElement](/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](/stream-analytics-query/getarraylength-azure-stream-analytics) und der [APPLY](/stream-analytics-query/apply-azure-stream-analytics)-Operator verwendet.

Hier ist ein Beispiel für ein einzelnes Ereignis. Sowohl `CustomSensor03` als auch `SensorMetadata` sind vom Typ **Array** :

```json
{
    "DeviceId" : "12345",
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "CustomSensor03": [12,-5,0]
     },
    "SensorMetadata":[
        {          
            "smKey":"Manufacturer",
            "smValue":"ABC"                
        },
        {
            "smKey":"Version",
            "smValue":"1.2.45"
        }
    ]
}
```

### <a name="working-with-a-specific-array-element"></a>Arbeiten mit einem spezifischen Arrayelement

Wählen Sie das Arrayelement am angegebenen Index aus (Auswählen des ersten Arrayelements):

```SQL
SELECT
    GetArrayElement(SensorReadings.CustomSensor03, 0) AS firstElement
FROM input
```

Es wird folgendes Ergebnis ausgegeben:

|firstElement|
|-|
|12|

### <a name="select-array-length"></a>Auswählen der Arraylänge

```SQL
SELECT
    GetArrayLength(SensorReadings.CustomSensor03) AS arrayLength
FROM input
```

Es wird folgendes Ergebnis ausgegeben:

|arraylength|
|-|
|3|

### <a name="convert-array-elements-into-separate-events"></a>Konvertieren von Arrayelementen in separate Ereignisse

Wählen Sie alle Arrayelemente als einzelne Ereignisse aus. Der [APPLY](/stream-analytics-query/apply-azure-stream-analytics)-Operator extrahiert zusammen mit der integrierten [GetArrayElements](/stream-analytics-query/getarrayelements-azure-stream-analytics)-Funktion alle Elemente des Arrays als einzelne Ereignisse:

```SQL
SELECT
    DeviceId,
    CustomSensor03Record.ArrayIndex,
    CustomSensor03Record.ArrayValue
FROM input
CROSS APPLY GetArrayElements(SensorReadings.CustomSensor03) AS CustomSensor03Record

```

Es wird folgendes Ergebnis ausgegeben:

|deviceId|ArrayIndex|ArrayValue|
|-|-|-|
|12345|0|12|
|12345|1|-5|
|12345|2|0|

```SQL
SELECT   
    i.DeviceId, 
    SensorMetadataRecords.ArrayValue.smKey as smKey,
    SensorMetadataRecords.ArrayValue.smValue as smValue
FROM input i
CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords
 ```
 
Es wird folgendes Ergebnis ausgegeben:

|deviceId|smKey|smValue|
|-|-|-|
|12345|Hersteller|ABC|
|12345|Version|1.2.45|

Wenn die extrahierten Felder in Spalten angezeigt werden müssen, können Sie das Dastaset mithilfe der [WITH](/stream-analytics-query/with-azure-stream-analytics)-Syntax zusätzlich zum [JOIN](/stream-analytics-query/join-azure-stream-analytics)-Vorgang pivotieren. Diese Verknüpfung erfordert eine [Zeitbegrenzungs](/stream-analytics-query/join-azure-stream-analytics#BKMK_DateDiff)-Bedingung, die eine Duplizierung verhindert:

```SQL
WITH DynamicCTE AS (
    SELECT   
        i.DeviceId,
        SensorMetadataRecords.ArrayValue.smKey as smKey,
        SensorMetadataRecords.ArrayValue.smValue as smValue
    FROM input i
    CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords 
)

SELECT
    i.DeviceId,
    i.Location.*,
    V.smValue AS 'smVersion',
    M.smValue AS 'smManufacturer'
FROM input i
LEFT JOIN DynamicCTE V ON V.smKey = 'Version' and V.DeviceId = i.DeviceId AND DATEDIFF(minute,i,V) BETWEEN 0 AND 0 
LEFT JOIN DynamicCTE M ON M.smKey = 'Manufacturer' and M.DeviceId = i.DeviceId AND DATEDIFF(minute,i,M) BETWEEN 0 AND 0
```

Es wird folgendes Ergebnis ausgegeben:

|deviceId|Lat|Long|smVersion|smManufacturer|
|-|-|-|-|-|
|12345|47|122|1.2.45|ABC|

## <a name="see-also"></a>Weitere Informationen
[Datentypen in Azure Stream Analytics](/stream-analytics-query/data-types-azure-stream-analytics)