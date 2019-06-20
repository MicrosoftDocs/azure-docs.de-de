---
title: Grundlegendes zu Azure IoT Hub-Aufträgen | Microsoft-Dokumentation
description: 'Entwicklerhandbuch: Planen der Ausführung von Aufträgen auf mehreren Geräten, die mit Ihrer IoT Hub-Instanz verbunden sind. Aufträge können Tags und gewünschte Eigenschaften aktualisieren und auf mehreren Geräten direkte Methoden aufrufen.'
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 147dd0f454bd85673bcba5cd6148c5da9716c580
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65409056"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Planen von Aufträgen auf mehreren Geräten

Azure IoT Hub bietet eine Reihe von Bausteinen, z.B. [Eigenschaften und Tags von Gerätezwillingen](iot-hub-devguide-device-twins.md) und [Direkte Methoden](iot-hub-devguide-direct-methods.md). Mit Back-End-Apps können Geräteadministratoren und -bediener in der Regel IoT-Geräte mithilfe eines Massenvorgangs und zu einem geplanten Zeitpunkt aktualisieren und mit ihnen interagieren. Aufträge führen Updates von Gerätezwillingen und direkten Methoden für eine Gruppe von Geräten zu einem geplanten Zeitpunkt aus. So kann ein Bediener beispielsweise mithilfe einer Back-End-App einen Auftrag für den Neustart einer Gruppe von Geräten im dritten Stock von Gebäude 43 zu einem Zeitpunkt initiieren und nachverfolgen, zu dem die Betriebsabläufe im Gebäude nicht gestört werden.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Erwägen Sie das Arbeiten mit Aufträgen, wenn Sie den Fortschritt der folgenden Aktivitäten für eine Gruppe von Geräten planen und nachverfolgen müssen:

* Aktualisieren gewünschter Eigenschaften
* Aktualisieren von Tags
* Aufrufen direkter Methoden

## <a name="job-lifecycle"></a>Auftragslebenszyklus

Aufträge werden vom Lösungs-Back-End eingeleitet und vom IoT Hub verwaltet. Sie können einen Auftrag über einen dienstseitigen URI (`PUT https://<iot hub>/jobs/v2/<jobID>?api-version=2018-06-30`) auslösen und den Fortschritt eines in der Ausführung befindlichen Auftrags über einen dienstseitigen URI (`GET https://<iot hub>/jobs/v2/<jobID?api-version=2018-06-30`) abfragen. Um den Status der zurzeit ausgeführten Aufträge nach der Initiierung eines Auftrags zu aktualisieren, führen Sie eine Auftragsabfrage aus.

> [!NOTE]
> Wenn Sie einen Auftrag initiieren, dürfen Eigenschaftennamen und -werte nur druckbare alphanumerische US-ASCII-Zeichen mit Ausnahme der folgenden enthalten: `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`.

## <a name="jobs-to-execute-direct-methods"></a>Aufträge zum Ausführen direkter Methoden

Der folgende Ausschnitt enthält die HTTPS 1.1-Anforderungsdetails, um eine [direkte Methode](iot-hub-devguide-direct-methods.md) für eine Gruppe von Geräten mithilfe eines Auftrags auszuführen:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleDeviceMethod",
    "cloudToDeviceMethod": {
        "methodName": "<methodName>",
        "payload": <payload>,
        "responseTimeoutInSeconds": methodTimeoutInSeconds
    },
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

Die Abfragebedingung kann auch wie unten dargestellt für eine einzelne Geräte-ID oder eine Liste mit Geräte-IDs gelten. Im Folgenden werden einige Beispiele vorgestellt:

```
"queryCondition" = "deviceId = 'MyDevice1'"
"queryCondition" = "deviceId IN ['MyDevice1','MyDevice2']"
"queryCondition" = "deviceId IN ['MyDevice1']"
```

Unter [IoT Hub-Abfragesprache](iot-hub-devguide-query-language.md) wird die IoT Hub-Abfragesprache ausführlicher erläutert.

Der folgende Codeausschnitt zeigt die Anforderung und Antwort für einen Auftrag, der zum Aufrufen einer direkten Methode namens „testMethod“ auf allen Geräten auf „contoso-hub-1“ geplant wurde:

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job01?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=68iv------------------------------------v8Hxalg%3D&se=1556849884&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 317

{
    "jobId": "job01",
    "type": "scheduleDeviceMethod",
    "cloudToDeviceMethod": {
        "methodName": "testMethod",
        "payload": {},
        "responseTimeoutInSeconds": 30
    },
    "queryCondition": "*", 
    "startTime": "2019-05-04T15:53:00.077Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 65
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 01:46:18 GMT

{"jobId":"job01","type":"scheduleDeviceMethod","status":"queued"}
```

## <a name="jobs-to-update-device-twin-properties"></a>Aufträge zum Aktualisieren der Eigenschaften von Gerätezwillingen

Der folgende Ausschnitt enthält die HTTPS 1.1-Anforderungsdetails, um die Eigenschaften von Gerätezwillingen mithilfe eines Auftrags zu aktualisieren:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleUpdateTwin",
    "updateTwin": <patch>                 // Valid JSON object
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

> [!NOTE]
> Für die Eigenschaft *updateTwin* ist eine gültige ETag-Übereinstimmung erforderlich, z.B. `etag="*"`.

Der folgende Codeausschnitt zeigt die Anforderung und-Antwort für einen Auftrag, der zum Aktualisieren der Eigenschaften von Gerätezwillingen für „test-device“ auf „contoso-hub-1“ geplant wurde:

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job02?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=BN0U-------------------------------------RuA%3D&se=1556925787&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 339

{
    "jobId": "job02",
    "type": "scheduleUpdateTwin",
    "updateTwin": {
      "properties": {
        "desired": {
          "test1": "value1"
        }
      },
     "etag": "*"
     },
    "queryCondition": "deviceId = 'test-device'",
    "startTime": "2019-05-08T12:19:56.868Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 63
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 22:45:13 GMT

{"jobId":"job02","type":"scheduleUpdateTwin","status":"queued"}
```

## <a name="querying-for-progress-on-jobs"></a>Abfragen des Fortschritts von Aufträgen

Der folgende Ausschnitt enthält die HTTPS 1.1-Anforderungsdetails für das Abfragen von Aufträgen:

```
GET /jobs/v2/query?api-version=2018-06-30[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
```

Das „continuationToken“ wird aus der Antwort bereitgestellt.

Sie können mithilfe der [IoT Hub-Abfragesprache für Geräte- und Modulzwillinge, Aufträge und Nachrichtenrouting](iot-hub-devguide-query-language.md) den Auftragsausführungsstatus für die einzelnen Geräte abfragen.

## <a name="jobs-properties"></a>Auftragseigenschaften

Die folgende Liste enthält die Eigenschaften und entsprechenden Beschreibungen, die beim Abfragen von Aufträgen oder Auftragsergebnissen verwendet werden können.

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| **jobId** |Für den Auftrag von der Anwendung angegebene ID. |
| **startTime** |Von der Anwendung angegebene Startzeit (ISO-8601) für den Auftrag. |
| **endTime** |Von IoT Hub angegebenes Datum (ISO-8601) für den Abschluss des Auftrags. Gilt nur, nachdem der Auftrag den Zustand „Abgeschlossen“ erreicht hat. |
| **type** |Auftragstypen: |
| | **scheduleUpdateTwin**: Ein Auftrag zum Aktualisieren einer Gruppe gewünschter Eigenschaften oder Tags. |
| | **scheduleDeviceMethod**: Ein Auftrag zum Aufrufen einer Gerätemethode für eine Gruppe von Gerätezwillingen. |
| **status** |Aktueller Status des Auftrags. Mögliche Werte für den Status: |
| | **pending**: Geplant und auf Auswahl durch den Auftragsdienst wartend. |
| | **scheduled**: Für einen Zeitpunkt in der Zukunft geplant. |
| | **running**: Derzeit aktiver Auftrag. |
| | **canceled**: Der Auftrag wurde abgebrochen. |
| | **failed**: Fehler beim Ausführen des Auftrags. |
| | **completed**: Der Auftrag wurde abgeschlossen. |
| **deviceJobStatistics** |Statistiken zur Ausführung des Auftrags. |
| | **deviceJobStatistics**-Eigenschaften: |
| | **deviceJobStatistics.deviceCount**: Anzahl der Geräte im Auftrag. |
| | **deviceJobStatistics.failedCount**: Anzahl der Geräte, auf denen der Auftrag fehlgeschlagen ist. |
| | **deviceJobStatistics.succeededCount**: Anzahl der Geräte, auf denen der Auftrag erfolgreich war. |
| | **deviceJobStatistics.runningCount**: Anzahl der Geräte, auf denen der Auftrag derzeit ausgeführt wird. |
| | **deviceJobStatistics.pendingCount**: Anzahl der Geräte, auf denen die Ausführung des Auftrags aussteht. |

### <a name="additional-reference-material"></a>Weiteres Referenzmaterial

Weitere Referenzthemen im IoT Hub-Entwicklerhandbuch:

* Unter [IoT Hub-Endpunkte](iot-hub-devguide-endpoints.md) werden die verschiedenen Endpunkte beschrieben, die jeder IoT Hub für Laufzeit- und Verwaltungsvorgänge verfügbar macht.

* Unter [Drosselung und Kontingente](iot-hub-devguide-quotas-throttling.md) werden die Kontingente für den IoT Hub-Dienst und das Drosselungsverhalten beschrieben, die bei Verwendung des Diensts zu erwarten sind.

* Unter [Verstehen und Verwenden von Azure IoT Hub SDKs](iot-hub-devguide-sdks.md) werden die verschiedenen Sprach-SDKs aufgelistet, die Sie bei der Entwicklung von Geräte- und Dienst-Apps für die Interaktion mit IoT Hub verwenden können.

* Unter [IoT Hub-Abfragesprache für Gerätezwillinge, Aufträge und Nachrichtenrouting](iot-hub-devguide-query-language.md) wird die IoT Hub-Abfragesprache beschrieben. Verwenden Sie diese Abfragesprache, um aus IoT Hub Informationen zu Gerätezwillingen und Aufträgen abzurufen.

* [Kommunikation mit Ihrem IoT Hub mithilfe des Protokolls MQTT](iot-hub-mqtt-support.md) enthält weitere Informationen zur IoT Hub-Unterstützung für das MQTT-Protokoll.

## <a name="next-steps"></a>Nächste Schritte

Um einige der in diesem Artikel beschriebenen Konzepte auszuprobieren, sehen Sie sich das folgende IoT Hub-Tutorial an:

* [Planen und Übertragen von Aufträgen](iot-hub-node-node-schedule-jobs.md)