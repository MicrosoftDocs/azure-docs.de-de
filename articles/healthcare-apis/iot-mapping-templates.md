---
title: 'Konzepte: Zuordnungsvorlagen im Feature „Azure IoT-Konnektor für FHIR“ (Vorschauversion) in Azure-API für FHIR'
description: Hier erfahren Sie, wie Sie in Azure IoT-Konnektor für FHIR (Vorschauversion) zwei Typen von Zuordnungsvorlagen erstellen können. Mit den Vorlagen für die Gerätezuordnung werden Gerätedaten in ein normalisiertes Schema transformiert. Mit den Vorlagen für die FHIR-Zuordnung wird eine normalisierte Nachricht in eine FHIR-basierte Observation-Ressource transformiert.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: punagpal
ms.openlocfilehash: f348a8d8755402d6426f19eabc432f54e3fb8e42
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659657"
---
# <a name="azure-iot-connector-for-fhir-preview-mapping-templates"></a>Zuordnungsvorlagen von Azure IoT-Konnektor für FHIR (Vorschauversion)
In diesem Artikel erfahren Sie, wie Sie Azure IoT-Konnektor für Fast Healthcare Interoperability Resources (FHIR&#174;)* mithilfe von Zuordnungsvorlagen konfigurieren.

Azure IoT-Konnektor für FHIR erfordert zwei Typen von JSON-basierten Zuordnungsvorlagen. Der erste heißt **Gerätezuordnung** und ist dafür zuständig, die an den Azure Event Hubs-Endpunkt `devicedata` gesendeten Gerätenutzlasten zuzuordnen. Mit dieser Art von Vorlage werden Typen, Gerätebezeichner, gemessene Zeitdaten sowie Messwerte extrahiert. Der zweite Typ ist die **FHIR-Zuordnung** und steuert die Zuordnung der FHIR-Ressourcen. Mit dieser Vorlagenart können Sie die Länge des Beobachtungszeitraums, den zum Speichern der Werte verwendeten FHIR-Datentyp sowie die Terminologiecodes konfigurieren. 

Die Zuordnungsvorlagen werden auf Grundlage des Typs zu einem JSON-Dokument zusammengesetzt. Anschließend werden diese JSON-Dokumente über das Azure-Portal zu Ihrem Azure IoT-Konnektor für FHIR hinzugefügt. Das Dokument für die Gerätezuordnung wird über die Seite **Configure Device mapping** (Gerätezuordnung konfigurieren) und das Dokument für die FHIR-Zuordnung über die Seite **Configure FHIR mapping** (FHIR-Zuordnung konfigurieren) hinzugefügt.

> [!NOTE]
> Zuordnungsvorlagen werden in einem zugrunde liegenden Blobspeicher gespeichert und per Computeausführung daraus geladen. Nach dem Update sollten sie sofort wirksam werden. 

## <a name="device-mapping"></a>Gerätezuordnung
Die Gerätezuordnung ist die Zuordnungsfunktion zum Extrahieren von Geräteinhalten in ein gängiges Format zwecks weiterer Auswertung. Jede empfangene Nachricht wird für alle Vorlagen ausgewertet. So kann eine einzelne eingehende Nachricht auf mehrere ausgehende Nachrichten projiziert werden, die später verschiedenen Beobachtungen in FHIR zugeordnet werden. Das Ergebnis ist ein normalisiertes Datenobjekt, das den Wert oder die Werte darstellt, die von den Vorlagen analysiert werden. Das normalisierte Datenmodell verfügt über einige erforderliche Eigenschaften, die gefunden und extrahiert werden müssen:

| Eigenschaft | Beschreibung |
| - | - |
|**Type**|Der Name/Typ, der die Messung klassifiziert. Mit diesem Wert wird die erforderliche FHIR-Zuordnungsvorlage gebunden.  Mehrere Vorlagen können Ergebnisse an denselben Typ ausgeben, sodass Sie unterschiedliche Darstellungen auf mehreren Geräten einer einzelnen gemeinsamen Ausgabe zuordnen können.|
|**OccurenceTimeUtc**|Der Zeitpunkt, zu dem die Messung vorgenommen wurde|
|**DeviceId**|Der Bezeichner für das Gerät. Dieser Wert sollte mit einem Bezeichner in der Geräteressource übereinstimmen, die auf dem FHIR-Zielserver vorhanden ist.|
 |**Eigenschaften**|Extrahieren Sie mindestens eine Eigenschaft, damit der Wert in der erstellten Observation-Ressource gespeichert werden kann.  Eigenschaften sind eine Sammlung von Schlüssel-Wert-Paaren, die bei der Normalisierung extrahiert wurden.|

Nachstehend finden Sie ein Beispieldiagramm für den Ablauf einer Normalisierung.

![Normalisierungsbeispiel](media/concepts-iot-mapping-templates/normalization-example.png)

Die Inhaltsnutzlast selbst ist eine Azure Event Hubs-Nachricht, die aus drei Teilen besteht: Body, Properties und SystemProperties. `Body` besteht aus einem Bytearray, das eine UTF-8-codierte Zeichenfolge darstellt. Während der Vorlagenauswertung wird das Bytearray automatisch in den Zeichenfolgenwert konvertiert. `Properties` ist eine Schlüssel-Wert-Sammlung, die vom Nachrichtenersteller verwendet wird. `SystemProperties` ist ebenfalls eine Schlüssel-Wert-Sammlung, die vom Azure Event Hubs-Framework reserviert wird und deren Einträge automatisch aufgefüllt werden.

```json
{
    "Body": {
        "content": "value"
    },
    "Properties": {
        "key1": "value1",
        "key2": "value2"
    },
    "SystemProperties": {
        "x-opt-sequence-number": 1,
        "x-opt-enqueued-time": "2019-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```

### <a name="mapping-with-json-path"></a>Zuordnen mit einem JSON-Pfad
Die drei aktuell unterstützten Vorlagentypen für Geräteinhalte benötigen einen JSON-Pfad, um die erforderliche Vorlage mit den extrahierten Werten abzugleichen. Weitere Informationen zum JSON-Pfad finden Sie [hier](https://goessner.net/articles/JsonPath/). Alle drei Vorlagentypen verwenden die [JSON.NET-Implementierung](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) zum Auflösen von JSON-Pfadausdrücken.

#### <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate
Mit JsonPathContentTemplate können Werte mithilfe des JSON-Pfads abgeglichen und aus einer Event Hubs-Nachricht extrahiert werden.

| Eigenschaft | BESCHREIBUNG |<div style="width:150px">Beispiel</div>
| --- | --- | --- 
|**TypeName**|Der den Messungen zugewiesene Typ, die mit der Vorlage übereinstimmen|`heartrate`
|**TypeMatchExpression**|Der JSON-Pfadausdruck, der gegen die Event Hubs-Nutzlast ausgewertet wird. Wird ein entsprechendes JToken gefunden, gilt die Vorlage als Übereinstimmung. Alle nachfolgenden Ausdrücke werden für das extrahierte JToken ausgewertet, das hier abgeglichen wurde.|`$..[?(@heartRate)]`
|**TimestampExpression**|Der JSON-Pfadausdruck, mit dem der Zeitstempelwert für die Eigenschaft „OccurenceTimeUtc“ der Messung extrahiert wird|`$.endDate`
|**DeviceIdExpression**|Der JSON-Pfadausdruck zum Extrahieren des Gerätebezeichners|`$.deviceId`
|**PatientIdExpression**|*Optional*: Der JSON-Pfadausdruck zum Extrahieren des Patientenbezeichners|`$.patientId`
|**EncounterIdExpression**|*Optional*: Der JSON-Pfadausdruck zum Extrahieren des Untersuchungsbezeichners|`$.encounterId`
|**Values[].ValueName**|Der Name, der dem vom nachfolgenden Ausdruck extrahierten Wert zugeordnet werden soll. Hiermit wird der erforderliche Wert oder die erforderliche Komponente in der FHIR-Zuordnungsvorlage gebunden. |`hr`
|**Values[].ValueExpression**|Der JSON-Pfadausdruck zum Extrahieren des erforderlichen Werts|`$.heartRate`
|**Values[].Required**|Mit dieser Eigenschaft wird festgelegt, ob der Wert in der Nutzlast vorhanden sein muss.  Wird er nicht gefunden, wird keine Messung generiert, und InvalidOperationException wird ausgelöst.|`true`

##### <a name="examples"></a>Beispiele
---
**Heart rate**

*Meldung*
```json
{
    "Body": {
        "heartRate": "78",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Vorlage*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**Blood pressure**

*Meldung*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Vorlage*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@systolic && @diastolic)]",
    "deviceIdExpression": "$.deviceid",
    "timestampExpression": "$.endDate",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
---

**Projizieren mehrerer Messungen aus einer einzelnen Nachricht**

*Meldung*
```json
{
    "Body": {
        "heartRate": "78",
        "steps": "2",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Vorlage 1*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
*Vorlage 2*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.steps",
                "valueName": "steps"
            }
        ]
    }
}
```
---

**Projizieren mehrerer Messungen aus einem Array in einer Nachricht**

*Meldung*
```json
{
    "Body": [
        {
            "heartRate": "78",
            "endDate": "2019-02-01T22:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "81",
            "endDate": "2019-02-01T23:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "72",
            "endDate": "2019-02-01T24:46:01.8750000Z",
            "deviceId": "device123"
        }
    ],
    "Properties": {},
    "SystemProperties": {}
}
```
*Vorlage*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

#### <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate

IotJsonPathContentTemplate ähnelt JsonPathContentTemplate, mit dem Unterschied, dass DeviceIdExpression und TimestampExpression nicht erforderlich sind.

Bei Verwendung dieser Vorlage wird davon ausgegangen, dass die zurzeit ausgewerteten Nachrichten mithilfe der [Azure IoT Hub-Geräte-SDKs](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) oder des Features [Daten exportieren (Legacy)](../iot-central/core/howto-export-data-legacy.md) von [Azure IoT Central](../iot-central/core/overview-iot-central.md) gesendet wurden. Werden diese SDKs eingesetzt, sind die Geräteidentität (vorausgesetzt, der Gerätebezeichner aus Azure IoT Hub/Central ist auf dem FHIR-Zielserver als Bezeichner für eine Geräteressource registriert) und der Zeitstempel der Nachricht bekannt. Wenn Sie Azure IoT Hub-Geräte-SDKs verwenden, jedoch benutzerdefinierte Eigenschaften für die im Nachrichtentext angegebene Geräteidentität oder den Messungszeitstempel konfigurieren, können Sie dennoch JsonPathContentTemplate nutzen.

*Hinweis: Bei Verwendung von IotJsonPathContentTemplate sollte TypeMatchExpression für die gesamte Nachricht als JToken aufgelöst werden. Weitere Informationen finden Sie in den folgenden Beispielen.* 
##### <a name="examples"></a>Beispiele
---
**Heart rate**

*Meldung*
```json
{
    "Body": {
        "heartRate": "78"        
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*Vorlage*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.Body.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**Blood pressure**

*Meldung*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87"
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*Vorlage*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@Body.systolic && @Body.diastolic)]",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.Body.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.Body.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```

#### <a name="iotcentraljsonpathcontenttemplate"></a>IotCentralJsonPathContentTemplate

Für IotCentralJsonPathContentTemplate sind außerdem DeviceIdExpression und TimestampExpression nicht erforderlich, und diese Vorlage wird verwendet, wenn die zurzeit ausgewerteten Nachrichten mithilfe des Features [Daten exportieren](../iot-central/core/howto-export-data.md) von [Azure IoT Central](../iot-central/core/overview-iot-central.md) gesendet werden. Bei Verwendung dieses Features sind die Geräteidentität (vorausgesetzt, dass der Gerätebezeichner aus Azure IoT Central auf dem FHIR-Zielserver als Bezeichner für eine Geräteressource registriert wurde) und der Zeitstempel der Nachricht bekannt. Wenn Sie das Feature „Datenexport“ von Azure IoT Central einsetzen, im Nachrichtentext für die Geräteidentität oder den Messungszeitstempel aber benutzerdefinierte Eigenschaften verwenden, können Sie JsonPathContentTemplate trotzdem nutzen.

*Hinweis: Bei Verwendung von IotCentralJsonPathContentTemplate sollte TypeMatchExpression für die gesamte Nachricht als JToken aufgelöst werden. Weitere Informationen finden Sie in den folgenden Beispielen.* 
##### <a name="examples"></a>Beispiele
---
**Heart rate**

*Meldung*
```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "HeartRate": "88",
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
*Vorlage*
```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@telemetry.HeartRate)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.HeartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**Blood pressure**

*Meldung*
```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "BloodPressure": {
            "Diastolic": "87",
            "Systolic": "123"
        }
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
*Vorlage*
```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "bloodPressure",
        "typeMatchExpression": "$..[?(@telemetry.BloodPressure.Diastolic && @telemetry.BloodPressure.Systolic)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Diastolic",
                "valueName": "bp_diastolic"
            },
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Systolic",
                "valueName": "bp_systolic"
            }
        ]
    }
}
```

## <a name="fhir-mapping"></a>FHIR-Zuordnung
Nachdem der Geräteinhalt in ein normalisiertes Modell extrahiert wurde, werden die Daten dem Gerätebezeichner, dem Messtyp und dem Zeitraum entsprechend gesammelt und gruppiert. Die Ausgabe dieser Gruppierung wird zwecks Konvertierung in eine FHIR-Ressource (derzeit [Observation](https://www.hl7.org/fhir/observation.html)) gesendet. Hier steuert die FHIR-Zuordnungsvorlage, wie die Daten einer FHIR-Observation-Ressource zugeordnet werden. Sollte eine Observation-Ressource für einen bestimmten Zeitpunkt oder über einen Zeitraum von einer Stunde erstellt werden? Welche Codes sollen der Ressource hinzugefügt werden? Soll der Wert als [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) oder [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity) dargestellt werden? Diese Datentypen sind alle Optionen, die von der FHIR-Zuordnungskonfiguration gesteuert werden.

### <a name="codevaluefhirtemplate"></a>CodeValueFhirTemplate
CodeValueFhirTemplate ist zurzeit die einzige Vorlage, die von der FHIR-Zuordnung unterstützt wird.  Damit können Sie Codes, den tatsächlichen Zeitraum und den Beobachtungswert definieren. Mehrere Werttypen werden unterstützt: [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData), [CodeableConcept](https://www.hl7.org/fhir/datatypes.html#CodeableConcept) und [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity). Neben diesen konfigurierbaren Werten werden der Bezeichner für die Observation-Ressource und die Verknüpfung mit den richtigen Device- und Patient-Ressourcen automatisch verarbeitet.

| Eigenschaft | BESCHREIBUNG 
| --- | ---
|**TypeName**| Der Typ der Messung, an die diese Vorlage gebunden werden soll. Es muss mindestens eine Gerätezuordnungsvorlage vorhanden sein, die diesen Typ ausgibt.
|**PeriodInterval**|Der Zeitraum, für den die erstellte Beobachtung gelten soll. Unterstützte Werte sind 0 (sofort), 60 (eine Stunde), 1440 (ein Tag).
|**Kategorie**|Eine beliebige Anzahl von [CodeableConcept](http://hl7.org/fhir/datatypes-definitions.html#codeableconcept)-Elementen, um den Typ der erstellten Beobachtung zu klassifizieren.
|**Codes**|Mindestens ein [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding)-Element, das auf die erstellte Beobachtung angewendet werden soll
|**Codes[].Code**|Der Code für das [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding)-Element
|**Codes[].System**|Das System für das [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding)-Element
|**Codes[].Display**|Die Anzeige des [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding)-Elements
|**Wert**|Der Wert, der extrahiert und in der Beobachtung dargestellt werden soll. Weitere Informationen finden Sie unter [Werttypvorlagen](#valuetypes).
|**Komponenten**|*Optional:* Mindestens eine Komponente, die bei der Beobachtung erstellt werden soll
|**Components[].Codes**|Mindestens ein [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding)-Element, das auf die Komponente angewendet werden soll
|**Components[].Value**|Der Wert, der extrahiert und in der Komponente dargestellt werden soll. Weitere Informationen finden Sie unter [Werttypvorlagen](#valuetypes).

### <a name="value-type-templates"></a>Werttypvorlagen <a name="valuetypes"></a>
Nachstehend werden die aktuell unterstützten Werttypvorlagen aufgeführt. In Zukunft können weitere Vorlagen hinzugefügt werden.
#### <a name="sampleddata"></a>SampledData
Stellt den FHIR-Datentyp [SampledData](http://hl7.org/fhir/datatypes.html#SampledData) dar. Beobachtungsmessungen werden ab einem bestimmten Zeitpunkt in einen Wertestream geschrieben und anschließend während des definierten Zeitraums inkrementiert. Wenn kein Wert vorhanden ist, wird ein `E` in den Datenstrom geschrieben. Wenn aufgrund des Zeitraums zwei weitere Werte die gleiche Position im Datenstrom belegen, wird der neuere Wert herangezogen. Die gleiche Logik kommt zum Einsatz, wenn eine SampledData verwendende Beobachtung aktualisiert wird.

| Eigenschaft | BESCHREIBUNG 
| --- | ---
|**DefaultPeriod**|Der zu verwendende Standardzeitraum in Millisekunden 
|**Einheit**|Die Einheit, die für den Ursprung von SampledData festgelegt werden soll 

#### <a name="quantity"></a>Menge
Stellt den FHIR-Datentyp [Quantity](http://hl7.org/fhir/datatypes.html#Quantity) dar. Wenn mehr als ein Wert in der Gruppierung vorhanden ist, wird nur der erste Wert verwendet. Wenn ein neuer Wert empfangen wird, der derselben Beobachtung zugeordnet ist, wird der alte Wert überschrieben.

| Eigenschaft | BESCHREIBUNG 
| --- | --- 
|**Einheit**| Darstellung der Einheit
|**Code**| Codierte Version der Einheit
|**System**| Das System, das die codierte Form definiert

### <a name="codeableconcept"></a>CodeableConcept
Stellt den FHIR-Datentyp [CodeableConcept](http://hl7.org/fhir/datatypes.html#CodeableConcept) dar. Der tatsächliche Wert wird nicht verwendet.

| Eigenschaft | BESCHREIBUNG 
| --- | --- 
|**Text**|Nur-Text-Darstellung 
|**Codes**|Mindestens ein [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding)-Element, das auf die erstellte Beobachtung angewendet werden soll
|**Codes[].Code**|Der Code für das [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding)-Element
|**Codes[].System**|Das System für das [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding)-Element
|**Codes[].Display**|Die Anzeige des [Coding](http://hl7.org/fhir/datatypes-definitions.html#coding)-Elements

### <a name="examples"></a>Beispiele
**Heartrate – SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "8867-4",
                "system": "http://loinc.org",
                "display": "Heart rate"
            }
        ],
        "periodInterval": 60,
        "typeName": "heartrate",
        "value": {
            "defaultPeriod": 5000,
            "unit": "count/min",
            "valueName": "hr",
            "valueType": "SampledData"
        }
    }
}
```
---
**Steps – SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "55423-8",
                "system": "http://loinc.org",
                "display": "Number of steps"
            }
        ],        
        "periodInterval": 60,
        "typeName": "stepsCount",
        "value": {
            "defaultPeriod": 5000,
            "unit": "",
            "valueName": "steps",
            "valueType": "SampledData"
        }
    }
}
```
---
**Blood pressure – SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 60,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "SampledData"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "SampledData"
                }
            }
        ]
    }
}
```
---
**Blood pressure – Quantity**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 0,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "Quantity"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "Quantity"
                }
            }
        ]
    }
}
```
---
**Device removed – CodeableConcept**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "deviceEvent",
                "system": "https://www.mydevice.com/v1",
                "display": "Device Event"
            }
        ],
        "periodInterval": 0,
        "typeName": "deviceRemoved",
        "value": {
            "text": "Device Removed",
            "codes": [
                {
                    "code": "deviceRemoved",
                    "system": "https://www.mydevice.com/v1",
                    "display": "Device Removed"
                }
            ],
            "valueName": "deviceRemoved",
            "valueType": "CodeableConcept"
        }
    }
}
```
---

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie häufig gestellte Fragen zu Azure IoT-Konnektor für FHIR (Vorschauversion).

>[!div class="nextstepaction"]
>[Azure IoT-Konnektor für FHIR – Häufig gestellte Fragen](fhir-faq.md)

*Im Azure-Portal wird Azure IoT-Konnektor für FHIR als „IoT-Konnektor (Vorschau)“ bezeichnet. FHIR ist eine eingetragene Marke von HL7 und wird mit Genehmigung von HL7 verwendet.