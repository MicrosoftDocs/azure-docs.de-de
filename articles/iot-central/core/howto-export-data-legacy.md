---
title: Exportieren von Daten aus Azure IoT Central (Legacy) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Daten aus Ihrer Azure IoT Central-Anwendung in Azure Event Hubs, Azure Service Bus und Azure Blob Storage exportiert werden.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/25/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 9e5f4fd14f56f0a2dff45dd2650ea552b07fecd7
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94987353"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-legacy"></a>Exportieren von IoT-Daten zu Cloudzielen mithilfe des Datenexports (Legacy)

> [!Note]
> In diesem Artikel werden die Features für den Legacy-Datenexport in IoT Central beschrieben.
>
> - Informationen zu den neuen Datenexportfeatures (Vorschauversion) finden Sie unter [Exportieren von IoT-Daten zu Cloudzielen mithilfe des Datenexports](./howto-export-data.md).
> - Wenn Sie etwas über die Unterschiede zwischen den Features für den Datenexport (Vorschauversion) und dem Legacy-Datenexport erfahren möchten, sehen Sie sich die [Vergleichstabelle](./howto-export-data.md#comparison-of-legacy-data-export-and-data-export) an.

In diesem Artikel wird beschrieben, wie Sie das Datenexportfeature in Azure IoT Central verwenden. Mit diesem Feature können Sie Ihre Daten fortlaufend in **Azure Event Hubs**, **Azure Service Bus** oder **Azure Blob Storage**-Instanzen exportieren. Beim Datenexport wird das JSON-Format verwendet, und im Export können Telemetriedaten, Geräteinformationen und Gerätevorlageninformationen enthalten sein. Verwenden Sie die exportierten Daten für folgende Zwecke:

- Umsetzbare Einblicke und Analyseergebnisse. Diese Option umfasst das Auslösen benutzerdefinierter Regeln in Azure Stream Analytics, das Auslösen benutzerdefinierter Workflows in Azure Logic Apps oder deren Weiterleitung durch Azure Functions, damit sie transformiert werden.
- Analyse von kalten Datenpfaden wie Trainingsmodelle in Azure Machine Learning oder die langfristige Trendanalyse in Microsoft Power BI.

> [!Note]
> Wenn Sie den Datenexport aktivieren, erhalten Sie nur die Daten ab dem jeweiligen Aktivierungszeitpunkt. Momentan können Daten nicht für Zeiten abgerufen werden, in denen der Datenexport deaktiviert war. Wenn Sie mehr Verlaufsdaten beibehalten möchten, aktivieren Sie den Datenexport frühzeitig.

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen ein Administrator für Ihre IoT Central-Anwendung sein oder über Berechtigungen für den Datenexport verfügen.

## <a name="set-up-export-destination"></a>Einrichten des Exportziels

Ihr Exportziel muss vorhanden sein, bevor Sie Ihren Datenexport konfigurieren.

### <a name="create-event-hubs-namespace"></a>Erstellen eines Event Hubs-Namespaces

Wenn Sie keinen vorhandenen Event Hubs-Namespace als Exportziel haben, führen Sie die folgenden Schritte aus:

1. Erstellen Sie einen [neuen Event Hubs-Namespace im Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Weitere Informationen erhalten Sie in der [Azure Event Hubs-Dokumentation](../../event-hubs/event-hubs-create.md).

2. Wählen Sie ein Abonnement aus. Sie können Daten in andere Abonnements exportieren, die mit dem Abonnement Ihrer IoT Central-Anwendung nicht identisch sind. In diesem Fall stellen Sie die Verbindung mithilfe einer Verbindungszeichenfolge her.

3. Erstellen Sie einen Event Hub in Ihrem Event Hubs-Namespace. Wechseln Sie zu Ihrem Namespace, und wählen Sie oben **+ Event Hub** aus, um eine Event Hub-Instanz zu erstellen.

### <a name="create-service-bus-namespace"></a>Erstellen eines Service Bus-Namespaces

Wenn Sie keinen vorhandenen Service Bus-Namespace als Exportziel haben, führen Sie die folgenden Schritte aus:

1. Erstellen Sie einen [neuen Service Bus-Namespace im Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Weitere Informationen erhalten Sie in der [Azure Service Bus-Dokumentation](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Wählen Sie ein Abonnement aus. Sie können Daten in andere Abonnements exportieren, die mit dem Abonnement Ihrer IoT Central-Anwendung nicht identisch sind. In diesem Fall stellen Sie die Verbindung mithilfe einer Verbindungszeichenfolge her.

3. Wenn Sie eine Warteschlange oder ein Thema als Exportziel erstellen möchten, wechseln Sie zu Ihrem Service Bus-Namespace, und wählen Sie **+ Warteschlange** oder **+ Thema** aus.

Wenn Sie eine Service Bus-Instanz als Exportziel auswählen, dürfen für Warteschlangen und Themen weder „Sitzungen“ noch „Duplikaterkennung“ aktiviert sein. Wenn eine dieser Optionen aktiviert ist, werden einige Nachrichten nicht in der Warteschlange oder im Thema eingehen.

### <a name="create-storage-account"></a>Speicherkonto erstellen

Wenn es noch kein Azure-Speicherkonto als Exportziel gibt, führen Sie die folgenden Schritte aus:

1. Erstellen Sie ein [neues Speicherkonto im Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Sie können sich zum Erstellen neuer [Azure Blob Storage-Konten](../../storage/blobs/storage-quickstart-blobs-portal.md) oder neuer [Azure Data Lake Storage v2-Speicherkonten](../../storage/common/storage-account-create.md) genauer informieren. Beim Datenexport können Daten nur in Speicherkonten geschrieben werden, die Blockblobs unterstützen. In der nachstehenden Liste sind die bekannten kompatiblen Speicherkontotypen aufgeführt:

    |Leistungsstufe|Kontotyp|
    |-|-|
    |Standard|Universell v2|
    |Standard|Universell v1|
    |Standard|Blob Storage|
    |Premium|Blockblobspeicher|

2. Erstellen Sie einen Container in Ihrem Speicherkonto. Wechseln Sie zum Speicherkonto. Wählen Sie unter **Blob-Dienst** die Option **Blobs durchsuchen**. Wählen Sie oben die Option **+ Container** aus, um einen neuen Container zu erstellen.

## <a name="set-up-data-export"></a>Einrichten des Datenexports

Sie haben jetzt ein Exportziel für Ihre Daten. Führen Sie als Nächstes die folgenden Schritte aus, um den Datenexport einzurichten.

1. Melden Sie sich bei Ihrer IoT Central-Anwendung an.

2. Wählen Sie im linken Bereich die Option **Datenexport** aus.

    > [!Tip]
    > Falls **Datenexport** dort nicht angezeigt wird, haben Sie keine Berechtigungen zum Konfigurieren des Datenexports in Ihrer App. Wenden Sie sich an Ihren Administrator, damit dieser den Datenexport einrichtet.

3. Wählen Sie die Schaltfläche **+ Neu** aus. Wählen Sie einen der Einträge **Azure Blob Storage**, **Azure Event Hubs**, **Azure Service Bus-Warteschlange** oder **Azure Service Bus-Thema** als Ziel für Ihren Export aus. Die maximale Anzahl von Exporten pro Anwendung beträgt 5.

4. Geben Sie einen Namen für den Export ein. Wählen Sie im Dropdown-Listenfeld Ihren **Namespace** aus, oder entscheiden Sie sich für **Verbindungszeichenfolge eingeben**.

    - Es werden Ihnen nur Speicherkonten, Event Hubs-Namespaces und Service Bus-Namespaces aus demselben Abonnement wie dem Ihrer IoT Central-Anwendung angezeigt. Wenn Sie in ein Ziel außerhalb dieses Abonnements exportieren möchten, wählen Sie **Verbindungszeichenfolge eingeben** aus, und fahren Sie mit Schritt 6 fort.
    - Bei Apps, die mit dem Tarif „Free“ erstellt werden, ist die einzige Möglichkeit zum Konfigurieren des Datenexports die Verwendung einer Verbindungszeichenfolge. Apps unter dem Free-Tarif ist kein Azure-Abonnement zugeordnet.

    ![Erstellen eines neuen Event Hubs](media/howto-export-data-legacy/export-event-hub.png)

5. Wählen Sie im Dropdown-Listenfeld einen Event Hub, eine Warteschlange, ein Thema oder einen Container aus.

6. (Optional) Wenn Sie **Verbindungszeichenfolge eingeben** ausgewählt haben, wird ein neues Feld angezeigt, in das Sie Ihre Verbindungszeichenfolge einfügen können. Um die Verbindungszeichenfolge für

    - Ihre Event Hubs oder Ihren Service Bus abzurufen, wechseln Sie im Azure-Portal zum Namespace:
        - So verwenden Sie eine Verbindungszeichenfolge für den gesamten Namespace:
            1. Wählen Sie unter **Einstellungen** die Option **Freigegebene Zugriffsrichtlinien** aus.
            2. Erstellen Sie einen neuen Schlüssel, oder wählen Sie einen vorhandenen Schlüssel aus, der über Berechtigungen zum **Senden** verfügt.
            3. Kopieren Sie die primäre oder die sekundäre Verbindungszeichenfolge.
        - Um die Verbindungszeichenfolge für eine bestimmte Event Hub-Instanz oder Service Bus-Warteschlange oder ein Thema zu verwenden, navigieren Sie zu **Entitäten > Event Hubs** oder **Entitäten > Warteschlangen** oder **Entitäten > Themen**. Wählen Sie eine bestimmte Instanz aus, und führen Sie dieselben Schritte wie oben aus, um eine Verbindungszeichenfolge zu erhalten.
    - Ihr Speicherkonto abzurufen, wechseln Sie im Azure-Portal zu diesem Konto:
        - Es werden nur Verbindungszeichenfolgen für das gesamte Speicherkonto unterstützt. Verbindungszeichenfolgen, die auf einen einzelnen Container begrenzt sind, werden nicht unterstützt.
          1. Wählen Sie unter **Einstellungen** die Option **Zugriffsschlüssel** aus.
          2. Kopieren Sie entweder die Verbindungszeichenfolge „key1“ oder die Verbindungszeichenfolge „key2“.

    Fügen Sie sie in die Verbindungszeichenfolge ein. Geben Sie in die Instanz ein, oder in **Containername** unter Beachtung der Groß-/Kleinschreibung.

7. Wählen Sie unter **Zu exportierende Daten** die Typen der zu exportierenden Daten aus, indem Sie den Typ jeweils auf **Ein** festlegen.

8. Zum Aktivieren des Datenexports müssen Sie sicherstellen, dass der Umschalter **Aktiviert** auf **Ein** festgelegt ist. Wählen Sie **Speichern** aus.

9. Nach einigen Minuten werden Ihre Daten im ausgewählten Ziel angezeigt.

## <a name="export-contents-and-format"></a>Exportieren von Inhalten und des Formats

Exportierte Telemetriedaten enthalten die gesamte Nachricht, die Ihre Geräte an IoT Central gesendet haben, und nicht nur die eigentlichen Telemetriewerte. Exportierte Gerätedaten enthalten Änderungen an Eigenschaften und Metadaten aller Geräte, und exportierte Gerätevorlagendaten enthalten Änderungen an allen Gerätevorlagen.

Bei Event Hubs und Service Bus werden Daten nahezu in Echtzeit exportiert. Die Daten sind in der Eigenschaft `body` enthalten und liegen im JSON-Format vor. Weiter unten finden Sie Beispiele dafür.

Bei Blob Storage werden Daten einmal pro Minute exportiert, wobei jede Datei den Batch der Änderungen seit der letzten exportierten Datei enthält. Exportierte Daten werden in drei Ordnern im JSON-Format gespeichert. Die Standardpfade in Ihrem Speicherkonto sind:

- Telemetriedaten: _{container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- Geräte: _{container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- Gerätevorlagen: _{container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

Wenn Sie die exportierten Dateien im Azure-Portal durchsuchen möchten, navigieren Sie zu der gewünschten Datei, und wählen Sie die Registerkarte **Blob bearbeiten** aus.

## <a name="telemetry"></a>Telemetrie

Bei Event Hubs und Service Bus exportiert IoT Central eine neue Nachricht schnell, nachdem sie von einem Gerät eingetroffen ist. Jede exportierte Nachricht enthält die vom Gerät gesendete vollständige Nachricht in der Eigenschaft „body“ im JSON-Format.

Bei Blob Storage werden Nachrichten im Batch zusammengefasst und einmal pro Minute exportiert. Für die exportierten Dateien wird dasselbe Format wie für die Nachrichtendateien verwendet, die bei der [IoT Hub-Nachrichtenweiterleitung](../../iot-hub/tutorial-routing.md) in Blob Storage exportiert wurden.

> [!NOTE]
> Stellen Sie bei Blob Storage sicher, dass Ihre Geräte Nachrichten senden, die `contentType: application/JSON` und `contentEncoding:utf-8` (oder `utf-16` und `utf-32`) enthalten. Ein Beispiel finden Sie in der [Dokumentation zu IoT Hub](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body).

Das Gerät, das die Telemetriedaten gesendet hat, wird durch die Geräte-ID dargestellt (siehe die folgenden Abschnitte). Wenn Sie die Namen der Geräte abrufen möchten, exportieren Sie Gerätedaten. Korrelieren Sie anschließend die einzelnen Nachrichten, indem Sie jeweils die **connectionDeviceId** verwenden, die mit der **deviceId** der Gerätenachricht übereinstimmt.

Das folgende Beispiel zeigt eine Nachricht, die von einem Event Hub, einer Service-Bus-Warteschlange oder einem Thema empfangen wurde:

```json
{
  "temp":81.129693132351775,
  "humid":59.488071477541247,
  "EventProcessedUtcTime":"2020-04-07T09:41:15.2877981Z",
  "PartitionId":0,
  "EventEnqueuedUtcTime":"2020-04-07T09:38:32.7380000Z"
}
```

Diese Nachricht enthält nicht die Geräte-ID des sendenden Geräts.

Wenn Sie die Geräte-ID aus den Nachrichtendaten in einer Azure Stream Analytics Abfrage abrufen möchten, verwenden Sie die Funktion [GetMetadataPropertyValue](/stream-analytics-query/getmetadatapropertyvalue). Ein Beispiel dazu zeigt die Abfrage in [Erweitern von Azure IoT Central mit benutzerdefinierten Regeln mithilfe von Stream Analytics, Azure Functions und SendGrid](./howto-create-custom-rules.md).

Verwenden Sie [systemProperties](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md), um die Geräte-ID in einem Azure Databricks- oder Apache Spark-Arbeitsbereich abzurufen. Ein Beispiel dazu zeigt der Databricks-Arbeitsbereich in [Erweitern von Azure IoT Central mit benutzerdefinierten Analysen mithilfe von Azure Databricks](./howto-create-custom-analytics.md).

Das folgende Beispiel zeigt einen in Blob Storage exportierten Datensatz:

```json
{
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{

  },
  "SystemProperties":{
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

## <a name="devices"></a>Geräte

Jede Nachricht bzw. jeder Datensatz in einer Momentaufnahme stellt eine oder mehrere Änderungen an einem Gerät und den zugehörigen Geräte- und Cloudeigenschaften seit der letzten exportierten Nachricht dar. Die Meldung enthält Folgendes:

- `id` des Geräts in IoT Central
- `displayName` des Geräts
- Gerätevorlagen-ID in `instanceOf`
- `simulated`-Flag: „true“, wenn es sich um ein simuliertes Gerät handelt
- `provisioned`-Flag: „true“, wenn das Gerät bereitgestellt wurde
- `approved`-Flag: „true“, wenn für das Gerät das Senden von Daten genehmigt wurde
- Eigenschaftswerte
- `properties`, einschließlich Werte für Geräte- und Cloudeigenschaften

Gelöschte Geräte werden nicht exportiert. Derzeit gibt es in exportierten Nachrichten keine Kennzeichnung gelöschter Geräte.

Bei Event Hubs und Service Bus sendet IoT Central Nachrichten, die Gerätedaten enthalten, nahezu in Echtzeit an Ihren Event Hub, Ihre Service Bus-Warteschlange oder Ihr Thema.

Bei Blob Storage wird eine neue Momentaufnahme, die alle Änderungen seit der letzten geschriebenen Änderung enthält, einmal pro Minute exportiert.

Die folgende Beispielnachricht zeigt Informationen zu Geräten und Eigenschaftendaten in einer Event Hub- oder Service Bus-Warteschlange oder einem Thema:

```json
{
  "body":{
    "id": "<device Id>",
    "etag": "<etag>",
    "displayName": "Sensor 1",
    "instanceOf": "<device template Id>",
    "simulated": false,
    "provisioned": true,
    "approved": true,
    "properties": {
        "sensorComponent": {
            "setTemp": "30",
            "fwVersion": "2.0.1",
            "status": { "first": "first", "second": "second" },
            "$metadata": {
                "setTemp": {
                    "desiredValue": "30",
                    "desiredVersion": 3,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 3
                },
                "fwVersion": { "ackVersion": 3 },
                "status": {
                    "desiredValue": {
                        "first": "first",
                        "second": "second"
                    },
                    "desiredVersion": 2,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 2
                }
            },
            
        }
    },
    "installDate": { "installDate": "2020-02-01" }
},
  "annotations":{
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2020-02-01T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Diese Momentaufnahme ist eine Beispielnachricht, in der Geräte und Eigenschaftendaten in Blob Storage angezeigt werden. Exportierte Dateien enthalten eine einzige Zeile pro Datensatz.

```json
{
  "id": "<device Id>",
  "etag": "<etag>",
  "displayName": "Sensor 1",
  "instanceOf": "<device template Id>",
  "simulated": false,
  "provisioned": true,
  "approved": true,
  "properties": {
      "sensorComponent": {
          "setTemp": "30",
          "fwVersion": "2.0.1",
          "status": { "first": "first", "second": "second" },
          "$metadata": {
              "setTemp": {
                  "desiredValue": "30",
                  "desiredVersion": 3,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 3
              },
              "fwVersion": { "ackVersion": 3 },
              "status": {
                  "desiredValue": {
                      "first": "first",
                      "second": "second"
                  },
                  "desiredVersion": 2,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 2
              }
          },
          
      }
  },
  "installDate": { "installDate": "2020-02-01" }
}
```

## <a name="device-templates"></a>Gerätevorlagen

Jede Nachricht bzw. jeder Momentaufnahmen-Datensatz stellt eine oder mehrere Änderungen an einer veröffentlichten Gerätevorlage seit der letzten exportierten Nachricht dar. Mit jeder Nachricht bzw. jedem Datensatz werden folgende Informationen gesendet:

- `id` der Gerätevorlage, die mit der `instanceOf` des Gerätestreams oben übereinstimmt
- `displayName` der Gerätevorlage
- Das `capabilityModel` des Geräts, einschließlich seiner `interfaces`, sowie die Definitionen für Telemetriedaten, Eigenschaften und Befehle
- Definitionen für `cloudProperties`
- Außerkraftsetzungen und Anfangswerte, zusammen mit dem `capabilityModel`

Gelöschte Gerätevorlagen werden nicht exportiert. Derzeit gibt es in exportierten Nachrichten keine Kennzeichnung gelöschter Gerätevorlagen.

Bei Event Hubs und Service Bus sendet IoT Central Nachrichten, die Gerätevorlagendaten enthalten, nahezu in Echtzeit an Ihren Event Hub, Ihre Service Bus-Warteschlange oder Ihr Thema.

Bei Blob Storage wird eine neue Momentaufnahme, die alle Änderungen seit der letzten geschriebenen Änderung enthält, einmal pro Minute exportiert.

Dieses Beispiel zeigt eine Nachricht zu Gerätevorlagendaten im Event Hub, in der Service-Bus-Warteschlange oder im Thema:

```json
{
  "body":{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "sensorComponent",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorConfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  },
    "annotations":{
      "iotcentral-message-source":"deviceTemplates",
      "x-opt-partition-key":"<partitionKey>",
      "x-opt-sequence-number":25315,
      "x-opt-offset":"<offset>",
      "x-opt-enqueued-time":1539274985085
    },
    "partitionKey":"<partitionKey>",
    "sequenceNumber":25315,
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

Diese Beispielmomentaufnahme zeigt eine Nachricht, die Geräte und Eigenschaftendaten enthält, in Blob Storage. Exportierte Dateien enthalten eine einzige Zeile pro Datensatz.

```json
{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "Sensor component",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorconfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  }
```

## <a name="data-format-change-notice"></a>Änderungshinweis zum Datenformat

> [!Note]
> Das Datenformat des Telemetriedatenstroms wird von dieser Änderung nicht beeinträchtigt. Nur die Geräte- und Gerätevorlagenstreams von Daten sind hiervon betroffen.

Wenn es in Ihrer Vorschauanwendung einen Datenexport gibt, bei dem die Datenströme für *Geräte* und *Gerätevorlagen* aktiviert wurden, aktualisieren Sie Ihren Export bis zum **30. Juni 2020**. Diese Anforderung gilt für Exporte in Azure Blob Storage, Azure Event Hubs und Azure Service Bus.

Ab dem 3. Februar 2020 verfügen alle neuen Exporte in Anwendungen, für die Geräte und Gerätevorlagen aktiviert sind, über das oben beschriebene Datenformat. Für alle vor diesem Datum erstellten Exporte wird bis zum 30. Juni 2020 das alte Datenformat beibehalten. Dann werden diese Exporte automatisch zum neuen Datenformat migriert. Das neue Datenformat stimmt mit den Objekten [device](/rest/api/iotcentral/devices/get) (Gerät), [device property](/rest/api/iotcentral/devices/getproperties) (Geräteeigenschaft), [device cloud property](/rest/api/iotcentral/devices/getcloudproperties) (Gerätecloudeigenschaft) und [device template](/rest/api/iotcentral/devicetemplates/get) (Gerätevorlage) in der öffentlichen IoT Central-API überein.

Wichtige Unterschiede zwischen dem alten und dem neuen Datenformat für **Devices** (Geräte) sind:
- `@id` für Gerät entfernt, `deviceId` in `id` umbenannt 
- `provisioned`-Flag hinzugefügt, um den Bereitstellungsstatus des Geräts zu beschreiben
- `approved`-Flag hinzugefügt, um den Genehmigungsstatus des Geräts zu beschreiben
- `properties` mit Geräte- und Cloudeigenschaften, Übereinstimmung mit Entitäten in der öffentlichen API

Wichtige Unterschiede zwischen dem alten und dem neuen Datenformat für **Device templates** (Gerätevorlagen) sind:

- `@id` für Gerätevorlage in `id` umbenannt
- `@type` für die Gerätevorlage in `types` umbenannt (ist jetzt ein Array)

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>Devices (Geräte): Format gilt ab dem 3. Februar 2020 als veraltet

```json
{
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{
    "$cloudProperties":{
        "Color":"blue"
    },
    "EnvironmentalSensor":{
      "thsensormodel":{
        "reported":{
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{
        "reported":{
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{
      "totalStorage":{
        "reported":{
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{
        "reported":{
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>Device templates (Gerätevorlagen): Format gilt ab dem 3. Februar 2020 als veraltet

```json
{
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{
                  "@value":"50"
                }
              },
              "visualizationDetail":{
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[
      {
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit dem Exportieren Ihrer Daten in Azure Event Hubs, Azure Service Bus und Azure Blob Storage vertraut gemacht haben, können Sie jetzt zum nächsten Schritt übergehen:

> [!div class="nextstepaction"]
> [Ausführen einer benutzerdefinierten Analyse mit Databricks](./howto-create-custom-analytics.md)