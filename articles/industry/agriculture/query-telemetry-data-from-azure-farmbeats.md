---
title: Abfragen erfasster Telemetriedaten
description: In diesem Artikel wird beschrieben, wie erfasste Telemetriedaten abgefragt werden.
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: b9067e2f78c8098d4a21263ac89caf03da631274
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677478"
---
# <a name="query-ingested-telemetry-data"></a>Abfragen erfasster Telemetriedaten

In diesem Artikel erfahren Sie, wie Sie erfasste Sensordaten aus Azure FarmBeats abfragen.

Das Erfassen von Daten von IoT-Ressourcen (Internet der Dinge) wie Geräten und Sensoren ist ein gängiges FarmBeats-Szenario. Sie erstellen Metadaten für Geräte und Sensoren und erfassen dann die historischen Daten in einem kanonischen Format in FarmBeats. Sobald die Sensordaten auf dem FarmBeats-Datenhub verfügbar sind, können Sie sie abfragen, um umsetzbare Erkenntnisse zu generieren oder Modelle zu erstellen.

## <a name="before-you-begin"></a>Voraussetzungen

Für diesen Artikel muss FarmBeats installiert sein, und Sie müssen bereits Sensortelemetriedaten von Ihren IoT-Geräten in FarmBeats erfasst haben.

Informationen zum Erfassen von Sensortelemetriedaten finden Sie unter [Erfassen historischer Telemetriedaten](ingest-historical-telemetry-data-in-azure-farmbeats.md).

Bevor Sie fortfahren, müssen Sie auch sicherstellen, dass Sie mit den FarmBeats-REST-APIs vertraut sind, da Sie die erfasste Telemetrie mithilfe der APIs abfragen werden. Weitere Informationen zu den FarmBeats-APIs finden Sie unter [FarmBeats-REST-APIs](rest-api-in-azure-farmbeats.md). **Stellen Sie sicher, dass Sie API-Anforderungen an Ihren FarmBeats-Datenhubendpunkt stellen können** .

## <a name="query-ingested-sensor-telemetry-data"></a>Abfragen erfasster Sensortelemetriedaten

Es gibt zwei Möglichkeiten, auf die Telemetriedaten in FarmBeats zuzugreifen und diese abzufragen:

- API und
- Time Series Insights (TSI).

### <a name="query-using-rest-api"></a>Abfragen über die REST-API

Führen Sie die Schritte aus, um die erfassten Sensortelemetriedaten über die FarmBeats-REST-APIs abzufragen:

1. Identifizieren Sie den relevanten Sensor. Hierzu können Sie eine GET-Anforderung für die /Sensor-API ausgeben.

> [!NOTE]
> Die Werte **id** und **sensorModelId** des gewünschten Sensorobjekts.

2. Geben Sie den Aufruf „GET/{id}“ an die /SensorModel-API für die in Schritt 1 notierte **sensorModelId** aus. „Sensor Model“ verfügt über alle Metadaten und Details zu den erfassten Telemetriedaten des Sensors. Beispielsweise enthält **Sensor Measure** im Objekt **Sensor Model** Details zu den vom Sensor gesendet Measures und deren Typen und Einheiten. Beispiel:

  ```json
  {
      "name": "moist_soil_last <name of the sensor measure - this is what we will receive as part of the queried telemetry data>",
      "dataType": "Double <Data Type - eg. Double>",
      "type": "SoilMoisture <Type of measure eg. temperature, soil moisture etc.>",
      "unit": "Percentage <Unit of measure eg. Celsius, Percentage etc.>",
      "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation>",
      "description": "<Description of the measure>"
  }
  ```
Notieren Sie sich die Antwort vom Aufruf „GET/{id}“ für „Sensor Model“.

3. Führen Sie einen POST-Aufruf für die /Telemetry-API mit der folgenden Eingabenutzlast aus.

  ```json
  {
    "sensorId": "<id of the sensor as noted in step 1>",
    "searchSpan": {
      "from": "<desired start timestamp in ISO 8601 format; default is UTC>",
      "to": "<desired end timestamp in ISO 8601 format; default is UTC>"
    },
    "filter": {
      "tsx": "string"
    },
    "projectedProperties": [
      {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    ]
  }
  ```
4. Die Antwort von der /Telemetry-API ähnelt der folgenden:

  ```json
  {
    "timestamps": [
      "2020-XX-XXT07:30:00Z",
      "2020-XX-XXT07:45:00Z"
    ],
    "properties": [
      {
        "values": [
          "<id of the sensor>",
          "<id of the sensor>"
        ],
        "name": "Id",
        "type": "String"
      },
      {
        "values": [
          2.1,
          2.2
        ],
        "name": "moist_soil_last <name of the SensorMeasure as defined in the SensorModel object>",
        "type": "Double <Data Type of the value - eg. Double>"
      }
    ]
  }
  ```
In der oben angegebenen Beispielantwort enthält die abgefragte Sensortelemetrie Daten für zwei Zeitstempel zusammen mit dem Measurenamen („moist_soil_last“) und den Werten der gemeldeten Telemetriedaten in den beiden Zeitstempeln. Sie müssen auf das zugehörige Sensormodell verweisen (wie in Schritt 2 beschrieben), um den Typ und die Einheit der gemeldeten Werte zu interpretieren.

### <a name="query-using-azure-time-series-insights-tsi"></a>Abfragen über Azure Time Series Insights (TSI)

FarmBeats nutzt [Azure Time Series Insights (TSI)](https://azure.microsoft.com/services/time-series-insights/) zum Erfassen, Speichern, Abfragen und Visualisieren von Daten im IoT-Bereich. Diese Daten sind hochgradig kontextualisiert und für Zeitreihen optimiert.

Telemetriedaten werden in einem Event Hub empfangen und dann verarbeitet und an eine TSI-Umgebung in der FarmBeats-Ressourcengruppe übermittelt. Die Daten können dann direkt aus TSI abgefragt werden. Weitere Informationen finden Sie in der [TSI-Dokumentation](../../time-series-insights/time-series-insights-explorer.md)

Führen Sie die Schritte aus, um Daten in TSI visuell darzustellen:

1. Wechseln Sie zu **Azure Portal** > **FarmBeats-DataHub-Ressourcengruppe** . Wählen Sie die **Time Series Insights** -Umgebung (tsi-xxxx) und dann **Datenzugriffsrichtlinien** aus. Fügen Sie einen Benutzer mit den Zugriffberechtigungen „Leser“ oder „Mitwirkender“ hinzu.
2. Wechseln Sie zur Seite **Übersicht** der **Time Series Insights** -Umgebung (tsi-xxxx), und wählen Sie die **Time Series Insights-Explorer-URL** aus. Sie können die erfassten Telemetriedaten nun visualisieren.

Neben dem Speichern, Abfragen und Visualisieren von Telemetriedaten ermöglicht TSI auch die Integration in ein Power BI-Dashboard. Weitere Informationen finden Sie [hier]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi).

## <a name="next-steps"></a>Nächste Schritte

Sie haben nun Sensordaten von Ihrer Azure FarmBeats-Instanz abgefragt. Als Nächstes können Sie sich damit vertraut machen, wie Sie [Karten für Ihre landwirtschaftlichen Betriebe generieren](generate-maps-in-azure-farmbeats.md#generate-maps).