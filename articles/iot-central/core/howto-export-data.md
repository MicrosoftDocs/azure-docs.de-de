---
title: Exportieren von Daten aus Azure IoT Central | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie den neuen Datenexport verwenden, um Ihre IoT-Daten nach Azure und benutzerdefinierten Cloudzielen zu exportieren.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 11/05/2020
ms.topic: how-to
ms.service: iot-central
ms.custom: contperf-fy21q1
ms.openlocfilehash: b84f1efd77ca757fd2ceaa8bb5605e3fc78297d0
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032370"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export"></a>Exportieren von IoT-Daten zu Cloudzielen mithilfe des Datenexports

> [!Note]
> In diesem Artikel werden die Features für den Datenexport in IoT Central beschrieben.
>
> - Informationen zu den Features für den Legacy-Datenexport finden Sie unter [Exportieren von IoT-Daten zu Cloudzielen mithilfe des Datenexports (Legacy)](./howto-export-data-legacy.md).
> - Wenn Sie etwas über die Unterschiede zwischen den Features für den Datenexport und den Legacy-Datenexport erfahren möchten, sehen Sie sich die [Vergleichstabelle](#comparison-of-legacy-data-export-and-data-export) unten an.

In diesem Artikel wird beschrieben, wie Sie das neue Datenexportfeature in Azure IoT Central verwenden können. Verwenden Sie dieses Feature für den fortlaufenden Export von gefilterten und angereicherten IoT-Daten aus Ihrer IoT Central-Anwendung. Beim Datenexport werden Änderungen nahezu in Echtzeit in andere Teile Ihrer Cloudlösung gepusht, damit Sie umsetzbare Einblicke, Analyseergebnisse und Speicherinformationen erhalten.

Beispielsweise können Sie folgende Aktionen ausführen:

- Fortlaufendes Exportieren von Telemetriedaten und Eigenschaftsänderungen im JSON-Format nahezu in Echtzeit.
- Filtern der Datenströme zum Exportieren von Daten, die die benutzerdefinierten Bedingungen erfüllen.
- Anreichern der Datenströme mit benutzerdefinierten Werten und Eigenschaftswerten aus dem Gerät.
- Senden der Daten an Ziele wie Azure Event Hubs, Azure Service Bus, Azure Blob Storage und Webhookendpunkte.

> [!Tip]
> Wenn Sie den Datenexport aktivieren, erhalten Sie nur die Daten ab dem jeweiligen Aktivierungszeitpunkt. Momentan können Daten nicht für Zeiten abgerufen werden, in denen der Datenexport deaktiviert war. Wenn Sie mehr Verlaufsdaten beibehalten möchten, aktivieren Sie den Datenexport frühzeitig.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die Datenexportfeatures verwenden können, müssen Sie eine [V3-Anwendung](howto-get-app-info.md) haben und über die Berechtigung [Datenexport](howto-manage-users-roles.md) verfügen.

## <a name="set-up-export-destination"></a>Einrichten des Exportziels

Ihr Exportziel muss vorhanden sein, bevor Sie Ihren Datenexport konfigurieren. Die folgenden Zieltypen sind zurzeit verfügbar:

- Azure Event Hubs
- Azure Service Bus-Warteschlange
- Azure Service Bus-Thema
- Azure Blob Storage
- Webhook

### <a name="create-an-event-hubs-destination"></a>Erstellen eines Event Hubs-Ziels

Wenn Sie keinen vorhandenen Event Hubs-Namespace als Exportziel haben, führen Sie die folgenden Schritte aus:

1. Erstellen Sie einen [neuen Event Hubs-Namespace im Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Weitere Informationen erhalten Sie in der [Azure Event Hubs-Dokumentation](../../event-hubs/event-hubs-create.md).

1. Erstellen Sie einen Event Hub in Ihrem Event Hubs-Namespace. Wechseln Sie zu Ihrem Namespace, und wählen Sie oben **+ Event Hub** aus, um eine Event Hub-Instanz zu erstellen.

1. Generieren Sie einen Schlüssel, der verwendet werden soll, wenn Sie Ihren Datenexport in IoT Central einrichten:

    - Wählen Sie die von Ihnen erstellte Event Hub-Instanz aus.
    - Wählen Sie **Einstellungen > Richtlinien für gemeinsamen Zugriff** aus.
    - Erstellen Sie einen neuen Schlüssel, oder wählen Sie einen vorhandenen Schlüssel aus, der über Berechtigungen zum **Senden** verfügt.
    - Kopieren Sie die primäre oder die sekundäre Verbindungszeichenfolge. Mithilfe dieser Verbindungszeichenfolge richten Sie ein neues Ziel in IoT Central ein.
    - Alternativ können Sie eine Verbindungszeichenfolge für den gesamten Event Hubs-Namespace generieren:
        1. Wechseln Sie im Azure-Portal zu Ihrem Event Hubs-Namespace.
        2. Wählen Sie unter **Einstellungen** die Option **Freigegebene Zugriffsrichtlinien** aus.
        3. Erstellen Sie einen neuen Schlüssel, oder wählen Sie einen vorhandenen Schlüssel aus, der über Berechtigungen zum **Senden** verfügt.
        4. Kopieren Sie die primäre oder die sekundäre Verbindungszeichenfolge.
        
### <a name="create-a-service-bus-queue-or-topic-destination"></a>Erstellen eines Ziels für Service Bus-Warteschlangen oder -Themen

Wenn Sie keinen vorhandenen Service Bus-Namespace als Exportziel haben, führen Sie die folgenden Schritte aus:

1. Erstellen Sie einen [neuen Service Bus-Namespace im Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Weitere Informationen erhalten Sie in der [Azure Service Bus-Dokumentation](../../service-bus-messaging/service-bus-create-namespace-portal.md).

1. Wenn Sie eine Warteschlange oder ein Thema als Exportziel erstellen möchten, wechseln Sie zu Ihrem Service Bus-Namespace, und wählen Sie **+ Warteschlange** oder **+ Thema** aus.

1. Generieren Sie einen Schlüssel, der verwendet werden soll, wenn Sie Ihren Datenexport in IoT Central einrichten:

    - Wählen Sie die erstellte Warteschlange oder das erstellte Thema aus.
    - Wählen Sie **Einstellungen/Richtlinien für gemeinsamen Zugriff** aus.
    - Erstellen Sie einen neuen Schlüssel, oder wählen Sie einen vorhandenen Schlüssel aus, der über Berechtigungen zum **Senden** verfügt.
    - Kopieren Sie die primäre oder die sekundäre Verbindungszeichenfolge. Mithilfe dieser Verbindungszeichenfolge richten Sie ein neues Ziel in IoT Central ein.
    - Alternativ können Sie eine Verbindungszeichenfolge für den gesamten Service Bus-Namespace generieren:
        1. Navigieren Sie im Azure-Portal zu Ihrem Service Bus-Namespace.
        2. Wählen Sie unter **Einstellungen** die Option **Freigegebene Zugriffsrichtlinien** aus.
        3. Erstellen Sie einen neuen Schlüssel, oder wählen Sie einen vorhandenen Schlüssel aus, der über Berechtigungen zum **Senden** verfügt.
        4. Kopieren Sie die primäre oder die sekundäre Verbindungszeichenfolge.

### <a name="create-an-azure-blob-storage-destination"></a>Erstellen Sie ein Azure Blob Storage-Ziel.

Wenn es noch kein Azure-Speicherkonto als Exportziel gibt, führen Sie die folgenden Schritte aus:

1. Erstellen Sie ein [neues Speicherkonto im Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Sie können sich zum Erstellen neuer [Azure Blob Storage-Konten](../../storage/blobs/storage-quickstart-blobs-portal.md) oder neuer [Azure Data Lake Storage v2-Speicherkonten](../../storage/common/storage-account-create.md) genauer informieren. Beim Datenexport können Daten nur in Speicherkonten geschrieben werden, die Blockblobs unterstützen. In der nachstehenden Liste sind die bekannten kompatiblen Speicherkontotypen aufgeführt:

    |Leistungsstufe|Kontotyp|
    |-|-|
    |Standard|Universell v2|
    |Standard|Universell v1|
    |Standard|Blob Storage|
    |Premium|Blockblobspeicher|

1. Wenn Sie einen Container in Ihrem Speicherkonto erstellen möchten, wechseln Sie zu diesem Konto. Wählen Sie unter **Blob-Dienst** die Option **Blobs durchsuchen**. Wählen Sie oben die Option **+ Container** aus, um einen neuen Container zu erstellen.

1. Generieren Sie eine Verbindungszeichenfolge für Ihr Speicherkonto, indem Sie zu **Einstellungen > Zugriffsschlüssel** wechseln. Kopieren Sie eine der beiden Verbindungszeichenfolgen.

### <a name="create-a-webhook-endpoint"></a>Erstellen eines Webhookendpunkts

Sie können Daten in einen öffentlich verfügbaren HTTP-Webhookendpunkt exportieren. Sie können einen Webhooktestendpunkt mit [RequestBin](https://requestbin.net/) erstellen. „RequestBin“ drosselt die Anforderung, wenn das Anforderungslimit erreicht wird:

1. Öffnen Sie [RequestBin](https://requestbin.net/).
2. Erstellen Sie ein neues RequestBin-Element, und kopieren Sie die **Bin-URL**. Sie verwenden diese URL beim Testen Ihres Datenexports.

## <a name="set-up-data-export"></a>Einrichten des Datenexports

Sie haben ein Ziel für den Export Ihrer Daten eingerichtet und richten jetzt den Datenexport in ihrer IoT Central Anwendung mit den folgenden Schritten ein:

1. Melden Sie sich bei Ihrer IoT Central-Anwendung an.

1. Wählen Sie im linken Bereich die Option **Datenexport** aus.

    > [!Tip]
    > Falls **Datenexport** dort nicht angezeigt wird, haben Sie keine Berechtigungen zum Konfigurieren des Datenexports in Ihrer App. Wenden Sie sich an Ihren Administrator, damit dieser den Datenexport einrichtet.

1. Wählen Sie **+ Neuer Export** aus.

1. Geben Sie einen Anzeigenamen für Ihren neuen Export ein, und stellen Sie sicher, dass der Datenexport **Aktiviert** ist.

1. Wählen Sie den zu exportierenden Datentyp aus. In der folgenden Tabelle sind die unterstützten Datenexporttypen aufgeführt:

    | Datentyp | Beschreibung | Datenformat |
    | :------------- | :---------- | :----------- |
    |  Telemetrie | Exportiert Telemetrienachrichten von Geräten nahezu in Echtzeit. Jede exportierte Nachricht enthält den vollständigen Inhalt der ursprünglichen Gerätenachricht (normalisiert).   |  [Format der Telemetrienachricht](#telemetry-format)   |
    | Eigenschaftsänderungen | Exportiert Änderungen zu den Geräte- und Cloudeigenschaften nahezu in Echtzeit. Bei schreibgeschützten Geräteeigenschaften werden Änderungen an den gemeldeten Werten exportiert. Bei Lese-/Schreibeigenschaften werden sowohl gemeldete als auch gewünschte Werte exportiert. | [Format der Eigenschaftsänderungsnachricht](#property-changes-format) |

<a name="DataExportFilters"></a>
1. Fügen Sie optional Filter hinzu, um die Menge der exportierten Daten zu verringern. Für jeden Datenexporttyp gibt es verschiedene Arten von Filtern:

    Zum Filtern von Telemetriedaten können Sie Folgendes ausführen:

    - **Filtern** des exportierten Datenstroms, damit er nur Telemetriedaten von Geräten enthält, die dem Gerätenamen, der Geräte-ID und der Filterbedingung der Gerätevorlage übereinstimmen.
    - **Filtern** über Funktionen: Wenn Sie ein Telemetrieelement in der Dropdownliste **Name** auswählen, enthält der exportierte Datenstrom nur Telemetriedaten, die die Filterbedingung erfüllen. Wenn Sie ein Gerät oder ein Cloudeigenschaftselement in der Dropdownliste **Name** auswählen, enthält der exportierte Datenstrom nur Telemetriedaten von Geräten mit Eigenschaften, die die Filterbedingung erfüllen.
    - **Nachrichteneigenschaftenfilter:** Geräte, die die Geräte-SDKs verwenden, können *Nachrichteneigenschaften* oder *Anwendungseigenschaften* für jede Telemetrienachricht senden. Die Eigenschaften sind eine Sammlung von Schlüssel-Wert-Paaren, die die Nachricht mit benutzerdefinierten Bezeichnern kennzeichnen. Zum Erstellen eines Nachrichteneigenschaftenfilters geben Sie den gesuchten Nachrichteneigenschaftsschlüssel ein, und geben Sie eine Bedingung an. Nur Telemetrienachrichten mit Eigenschaften, die die angegebene Filterbedingung erfüllen, werden exportiert. Die folgenden Vergleichsoperatoren für Zeichenfolgen werden unterstützt: „ist gleich“, „ist nicht gleich“, „enthält“, „enthält nicht“, „ist vorhanden“, „ist nicht vorhanden“. [Weitere Informationen zu Anwendungseigenschaften finden Sie in der IoT Hub-Dokumentation](../../iot-hub/iot-hub-devguide-messages-construct.md).

    Verwenden Sie zum Filtern von Eigenschaftsänderungen einen **Funktionsfilter**. Wählen Sie ein Eigenschaftselement in der Dropdownliste aus. Der exportierte Datenstrom enthält nur Änderungen an der ausgewählten Eigenschaft, die die Filterbedingung erfüllt.

<a name="DataExportEnrichmnents"></a>
1. Reichern Sie optional exportierte Nachrichten mit zusätzlichen Metadaten für Schlüssel-Wert-Paare an. Die folgenden Anreicherungen stehen für die Datenexporttypen „Telemetrie“ und „Eigenschaftsänderungen“ zur Verfügung:

    - **Benutzerdefinierte Zeichenfolge:** Fügt jeder Nachricht eine benutzerdefinierte statische Zeichenfolge hinzu. Geben Sie einen beliebigen Schlüssel und einen beliebigen Zeichenfolgenwert ein.
    - **Property:** Fügt jeder Nachricht die aktuelle vom Gerät gemeldete Eigenschaft oder den Cloudeigenschaftswert hinzu. Geben Sie einen beliebigen Schlüssel ein, und wählen Sie eine Geräte- oder Cloudeigenschaft aus. Wenn die exportierte Nachricht aus einem Gerät stammt, das die angegebene Eigenschaft nicht hat, erhält die exportierte Nachricht die Anreicherung nicht.

1. Fügen Sie ein neues Ziel oder ein Ziel hinzu, das Sie bereits erstellt haben. Wählen Sie den Link **Create a new one** (Neue erstellen) aus, und fügen Sie die folgenden Informationen hinzu:

    - **Zielname:** der Anzeigename des Ziels in IoT Central.
    - **Zieltyp:** Wählen Sie den Typ des Ziels aus. Wenn Sie Ihr Ziel noch nicht eingerichtet haben, finden Sie weitere Informationen unter [Einrichten des Exportziels](#set-up-export-destination).
    - Fügen Sie bei Azure Event Hubs, Azure Service Bus-Warteschlange oder -Thema die Verbindungszeichenfolge für Ihre Ressource ein, und geben Sie bei Bedarf den Namen des Event Hubs, der Warteschlange oder des Themas ein (Groß-/Kleinschreibung beachten).
    - Fügen Sie bei Azure Blob Storage die Verbindungszeichenfolge für Ihre Ressource ein, und geben Sie bei Bedarf den Containernamen ein (Groß-/Kleinschreibung beachten).
    - Bei Webhook fügen Sie die Callback-URL für Ihren Webhookendpunkt ein. Optional können Sie die Webhookautorisierung (OAuth 2.0 und Autorisierungstoken) konfigurieren und benutzerdefinierte Header hinzufügen. 
        - Bei OAuth 2.0 wird nur der Flow für Anmeldeinformationen von Clients unterstützt. Wenn das Ziel gespeichert ist, kommuniziert IoT Central mit Ihrem OAuth-Anbieter, um ein Autorisierungstoken abzurufen. Dieses Token wird bei jeder an dieses Ziel gesendeten Nachricht an den Autorisierungsheader angefügt.
        - Für das Autorisierungstoken können Sie einen Tokenwert angeben, der bei jeder an dieses Ziel gesendeten Nachricht direkt an den Autorisierungsheader angefügt wird.
    - Klicken Sie auf **Erstellen**.

1. Wählen Sie **+ Ziel** und dann ein Ziel aus der Dropdownliste aus. Sie können bis zu fünf Ziele zu einem einzelnen Export hinzufügen.

1. Wenn Sie das Einrichten Ihres Exports abgeschlossen haben, wählen Sie **Speichern** aus. Nach einigen Minuten werden Ihre Daten in Ihren Zielen angezeigt.

## <a name="export-contents-and-format"></a>Exportieren von Inhalten und des Formats

### <a name="azure-blob-storage-destination"></a>Azure Blob Storage-Ziel

Daten werden einmal pro Minute exportiert, wobei jede Datei den Batch der Änderungen seit dem vorherigen Export enthält. Exportierte Daten werden im JSON-Format gespeichert. Die Standardpfade zu den exportierten Daten in Ihrem Speicherkonto lauten:

- Telemetriedaten: _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- Eigenschaftsänderungen: _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

Wenn Sie die exportierten Dateien im Azure-Portal durchsuchen möchten, navigieren Sie zu der gewünschten Datei, und wählen Sie **Blob bearbeiten** aus.

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Azure Event Hubs- und Azure Service Bus-Ziele

Daten werden nahezu in Echtzeit exportiert. Die Daten befinden sich im Nachrichtentext und liegen im JSON-Format vor, das als UTF-8 codiert ist.

Die Sammlung von Anmerkungen bzw. Systemeigenschaften der Nachricht enthält die Felder `iotcentral-device-id`, `iotcentral-application-id`, `iotcentral-message-source` und `iotcentral-message-type` mit denselben Werten wie die entsprechenden Felder im Nachrichtentext.

### <a name="webhook-destination"></a>Webhookziel

Bei Webhookzielen werden Daten ebenfalls nahezu in Echtzeit exportiert. Die Daten im Nachrichtentext haben dasselbe Format wie bei Event Hubs und Service Bus.

### <a name="telemetry-format"></a>Telemetrieformat

Jede exportierte Nachricht enthält eine normalisierte Form der vollständigen Nachricht, die das Gerät im Nachrichtentext gesendet hat. Die Nachricht ist im JSON-Format und als UTF-8 codiert. Jede Nachricht enthält folgende Informationen:

- `applicationId`: Die ID der IoT Central-Anwendung.
- `messageSource`: Die Quelle für die Nachricht – `telemetry`.
- `deviceId`:  Die ID des Geräts, von dem die Telemetrienachricht gesendet wurde.
- `schema`: Den Namen und die Version des Nutzdatenschemas.
- `templateId`: Die ID der Gerätevorlage, die dem Gerät zugeordnet ist.
- `enrichments`: Alle im Export eingerichteten Anreicherungen.
- `messageProperties`: Zusätzliche Eigenschaften, die das Gerät zusammen mit der Nachricht gesendet hat. Diese Eigenschaften werden manchmal auch als *Anwendungseigenschaften* bezeichnet. [Weitere Informationen zu IoT Hub-Dokumenten](../../iot-hub/iot-hub-devguide-messages-construct.md).

Bei Event Hubs und Service Bus exportiert IoT Central eine neue Nachricht schnell, nachdem sie von einem Gerät eingetroffen ist. In die Benutzereigenschaften (auch als „Anwendungseigenschaften“ bezeichnet) jeder Nachricht werden die Eigenschaften `iotcentral-device-id`, `iotcentral-application-id` und `iotcentral-message-source` automatisch einbezogen.

Bei Blob Storage werden Nachrichten im Batch zusammengefasst und einmal pro Minute exportiert.

Das folgende Beispiel zeigt eine exportierte Telemetrienachricht:

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```

## <a name="property-changes-format"></a>Format für Eigenschaftsänderungen

Jede Nachricht bzw. jeder Datensatz stellt eine Änderung an einem Gerät oder einer Cloudeigenschaft dar. Bei Geräteeigenschaften werden nur Änderungen des gemeldeten Werts als separate Nachricht exportiert. Die exportierte Nachricht enthält folgende Informationen:

- `applicationId`: Die ID der IoT Central-Anwendung.
- `messageSource`: Die Quelle für die Nachricht – `properties`.
- `messageType`: Entweder `cloudPropertyChange`, `devicePropertyDesiredChange` oder `devicePropertyReportedChange`.
- `deviceId`:  Die ID des Geräts, von dem die Telemetrienachricht gesendet wurde.
- `schema`: Den Namen und die Version des Nutzdatenschemas.
- `templateId`: Die ID der Gerätevorlage, die dem Gerät zugeordnet ist.
- `enrichments`: Alle im Export eingerichteten Anreicherungen.

Bei Event Hubs und Service Bus exportiert IoT Central neue Nachrichtendaten nahezu in Echtzeit an Ihren Event Hub oder Ihre Service Bus-Warteschlange bzw. Ihr Service Bus-Thema. In die Benutzereigenschaften (auch als „Anwendungseigenschaften“ bezeichnet) jeder Nachricht werden die Eigenschaften `iotcentral-device-id`, `iotcentral-application-id`, `iotcentral-message-source` und `iotcentral-message-type` automatisch einbezogen.

Bei Blob Storage werden Nachrichten im Batch zusammengefasst und einmal pro Minute exportiert.

Das folgende Beispiel zeigt eine exportierte Eigenschaftsänderungsnachricht, die in Azure Blob Storage empfangen wurde.

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "name": "MachineSerialNumber",
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="comparison-of-legacy-data-export-and-data-export"></a>Vergleich zwischen Legacy-Datenexport und Datenexport

Die folgende Tabelle zeigt die Unterschiede zwischen dem [Legacy-Datenexport](howto-export-data-legacy.md) und den Features des neuen Datenexports:

| Funktionen  | Legacy-Datenexport | Neuer Datenexport |
| :------------- | :---------- | :----------- |
| Verfügbare Datentypen | Telemetrie, Geräte, Gerätevorlagen | Telemetrie, Eigenschaftsänderungen |
| Filterung | Keiner | Hängt vom exportierten Datentyp ab. Für Telemetrie, Filtern nach Telemetrie, Nachrichteneigenschaften, Eigenschaftswerte |
| Anreicherungen | Keiner | Anreichern mit einer benutzerdefinierten Zeichenfolge oder einem Eigenschaftswert auf dem Gerät |
| Destinations | Azure Event Hubs, Azure Service Bus-Warteschlangen und -Themen, Azure Blob Storage | Wie bei Legacy-Datenexport plus Webhooks|
| Unterstützte Anwendungsversionen | V2 und V3 | Nur V3 |
| Relevante Grenzwerte | 5 Exporte pro App, 1 Ziel pro Export | 10 Exporte-Ziel-Verbindungen pro App |

## <a name="next-steps"></a>Nächste Schritte

Sie wissen jetzt, wie Sie den neuen Datenexport verwenden können. Als Nächstes sollten Sie sich über das [Analysieren von Gerätedaten in IoT Central](./howto-create-analytics.md) informieren.
