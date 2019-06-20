---
title: Exportieren Ihrer Daten in Azure Event Hubs und Azure Service Bus | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Daten aus Ihrer Azure IoT Central-Anwendung in Azure Event Hubs und Azure Service Bus exportiert werden.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 78edeb0c418f5c426771d241464d389f8a632e96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65463996"
---
# <a name="export-your-data-in-azure-iot-central"></a>Exportieren von Daten in Azure IoT Central

*Dieses Thema gilt für Administratoren.*

In diesem Artikel wird beschrieben, wie Sie in Azure IoT Central das Feature für den kontinuierlichen Datenexport verwenden, um Daten regelmäßig in Ihre eigenen **Azure Event Hubs**- und **Azure Service Bus**-Instanzen zu exportieren. Sie können **Messungen**, **Geräte** und **Gerätevorlagen** an Ihr eigenes Ziel exportieren, um Einblicke in Pfade und Analysen zu erhalten. Dies umfasst das Auslösen benutzerdefinierter Regeln in Azure Stream Analytics, das Auslösen benutzerdefinierter Workflows in Azure Logic Apps oder das Transformieren der Daten und deren Weiterleitung durch Azure Functions. 

> [!Note]
> Zur Erinnerung: Wenn Sie den fortlaufenden Datenexport aktivieren, erhalten Sie nur die Daten ab dem jeweiligen Aktivierungszeitpunkt. Derzeit können Daten nicht für Zeiten abgerufen werden, in denen der Datenexport deaktiviert war. Aktivieren Sie den fortlaufenden Datenexport frühzeitig, um umfassendere Verlaufsdaten zu erhalten.


## <a name="prerequisites"></a>Voraussetzungen

- Sie müssen Administrator in Ihrer IoT Central-Anwendung sein.

## <a name="set-up-export-destination"></a>Einrichten des Exportziels

Wenn Sie kein vorhandenes Event Hubs-/Service Bus-Ziel für den Export haben, gehen Sie wie folgt vor:

## <a name="create-event-hubs-namespace"></a>Erstellen eines Event Hubs-Namespaces

1. Erstellen Sie einen [neuen Event Hubs-Namespace im Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Weitere Informationen erhalten Sie in der [Azure Event Hubs-Dokumentation](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).
2. Wählen Sie ein Abonnement aus. 

    > [!Note] 
    > Jetzt können Sie Daten in andere Abonnements exportieren, die **nicht identisch sind** mit dem für Ihre IoT Central-Anwendung mit nutzungsbasierter Bezahlung. In diesem Fall stellen Sie die Verbindung mithilfe einer Verbindungszeichenfolge her.
3. Erstellen Sie einen Event Hub in Ihrem Event Hubs-Namespace. Wechseln Sie zu Ihrem Namespace, und wählen Sie oben **+ Event Hub** aus, um eine Event Hub-Instanz zu erstellen.

## <a name="create-service-bus-namespace"></a>Erstellen eines Service Bus-Namespaces

1. Erstellen Sie einen [neuen Service Bus-Namespace im Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Weitere Informationen erhalten Sie in der [Azure Service Bus-Dokumentation](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).
2. Wählen Sie ein Abonnement aus. 

    > [!Note] 
    > Jetzt können Sie Daten in andere Abonnements exportieren, die **nicht identisch sind** mit dem für Ihre IoT Central-Anwendung mit nutzungsbasierter Bezahlung. In diesem Fall stellen Sie die Verbindung mithilfe einer Verbindungszeichenfolge her.

3. Wechseln Sie zu Ihrem Service Bus-Namespace, und wählen Sie oben **+ Warteschlange** oder **+ Thema** aus, um eine Warteschlange oder ein Thema als Exportziel zu erstellen.


## <a name="set-up-continuous-data-export"></a>Einrichten des fortlaufenden Datenexports

Nachdem Sie nun ein Event Hubs-/Service Bus-Ziel für den Export von Daten haben, gehen Sie folgendermaßen vor, um den kontinuierlichen Datenexport einzurichten. 

1. Melden Sie sich bei Ihrer IoT Central-Anwendung an.

2. Wählen Sie im linken Menü **Kontinuierlicher Datenexport** aus.

    > [!Note]
    > Wenn im linken Menü „Kontinuierlicher Datenexport“ nicht angezeigt wird, sind Sie kein Administrator in Ihrer App. Wenden Sie sich an Ihren Administrator, damit dieser den Datenexport einrichtet.

    ![Erstellen eines neun Event Hubs für kontinuierlichen Datenexport](media/howto-export-data/export_menu1.png)

3. Wählen Sie oben rechts die Schaltfläche **+ Neu** aus. Wählen Sie entweder **Azure Event Hubs** oder **Azure Service Bus** als Ziel für Ihren Export aus. 

    > [!NOTE] 
    > Die maximale Anzahl von Exporten pro App beträgt 5. 

    ![Erstellen eine neuen kontinuierlichen Datenexports](media/howto-export-data/export_new1.png)

4. Wählen Sie im Dropdown-Listenfeld Ihren **Event Hubs-Namespace/Service Bus-Namespace** aus. Sie können auch die letzte Option in der Liste auswählen, bei der es sich um **Verbindungszeichenfolge eingeben** handelt. 

    > [!NOTE] 
    > Ihnen werden nur Speicherkonten/Event Hubs-Namespaces/Service Bus-Namespaces aus **demselben Abonnement wie dem Ihrer IoT Central-App** angezeigt. Wenn Sie in ein Ziel außerhalb dieses Abonnements exportieren möchten, wählen Sie **Verbindungszeichenfolge eingeben** aus, und fahren Sie mit Schritt 5 fort.

    > [!NOTE] 
    > Bei 7-Tage-Test-Apps ist eine Verbindungszeichenfolge die einzige Möglichkeit zur Konfiguration des kontinuierlichen Datenexports. Dies liegt daran, dass 7-Tage-Test-Apps kein Azure-Abonnement zugeordnet ist.

    ![Erstellen eines neun Event Hubs für kontinuierlichen Datenexport](media/howto-export-data/export_create1.png)

5. (Optional) Wenn Sie **Verbindungszeichenfolge eingeben** ausgewählt haben, wird ein neues Feld angezeigt, in das Sie Ihre Verbindungszeichenfolge einfügen können. Um die Verbindungszeichenfolge für
    - Ihren Event Hubs oder Service Bus abzurufen, wechseln Sie im Azure-Portal zu dem Namespace.
        - Wählen Sie unter **Einstellungen** die Option **Freigegebene Zugriffsrichtlinien** aus.
        - Wählen Sie die Standardrichtlinie **RootManageSharedAccessKey** aus, oder erstellen Sie eine neue.
        - Kopieren Sie die primäre oder die sekundäre Verbindungszeichenfolge.
 
6. Wählen Sie einen Event Hub, eine Warteschlange oder ein Thema im Dropdown-Listenfeld aus.

7. Geben Sie unter **Data to export** (Zu exportierende Daten) die Typen der zu exportierenden Daten an, indem Sie den Typ jeweils auf **Ein** festlegen.

6. Um den kontinuierlichen Datenexport zu aktivieren, stellen Sie sicher, dass **Datenexport** auf **Ein** festgelegt ist. Wählen Sie **Speichern** aus.

    ![Konfigurieren des fortlaufenden Datenexports](media/howto-export-data/export_list1.png)

7. Nach einigen Minuten werden Ihre Daten an Ihrem ausgewählten Ziel angezeigt.


## <a name="export-to-azure-event-hubs-and-azure-service-bus"></a>Exportieren in Azure Event Hubs und Azure Service Bus

Messungen, Geräte und Gerätevorlagen werden nahezu in Echtzeit in Ihren Event Hub, Ihre Service Bus-Warteschlange oder ein Thema exportiert. Exportierte Messdaten enthalten die Gesamtheit der Nachricht, die Ihre Geräte an IoT Central gesendet haben, nicht nur die Werte der Messungen selbst. Exportierte Gerätedaten enthalten Änderungen an Eigenschaften und Einstellungen aller Geräte, und exportierte Gerätevorlagen enthalten Änderungen an allen Gerätevorlagen. Die exportierten Daten befinden sich in der Eigenschaft „body“ und haben das JSON-Format.

> [!NOTE]
> Wenn Sie eine Service Bus-Instanz als Exportziel auswählen, dürfen für Warteschlangen und Themen **weder „Sitzungen“ noch „Duplikaterkennung“ aktiviert sein**. Wenn eine dieser Optionen aktiviert ist, werden einige Nachrichten nicht in der Warteschlange oder im Thema eingehen.

### <a name="measurements"></a>Messungen

Eine neue Nachricht wird schnell exportiert, nachdem IoT Central die Nachricht von einem Gerät empfangen hat. Jede exportierte Nachricht in Event Hubs und Service Bus enthält die vollständige Nachricht, die das Gerät gesendet hat, in der Eigenschaft „body“ im JSON-Format. 

> [!NOTE]
> Die Geräte, die die Messdaten senden, werden durch Geräte-IDs (siehe folgende Abschnitte) dargestellt. Um die Namen der Geräte zu erhalten, exportieren Sie Gerätedaten. Korrelieren Sie anschließend die einzelnen Nachrichten, indem Sie jeweils die **connectionDeviceId** verwenden, die mit der **deviceId** der Gerätenachricht übereinstimmt.

Das folgende Beispiel zeigt eine Nachricht zu Messdaten, die im Event Hub, in der Service-Bus-Warteschlange oder im Thema empfangen wurden.

```json
{
  "body": {
    "humidity": 29.06963648666288,
    "temp": 8.4503795661685,
    "pressure": 1075.8334910110093,
    "magnetometerX": 408.6966458887116,
    "magnetometerY": -532.8809796603962,
    "magnetometerZ": 174.70653875528205,
    "accelerometerX": 1481.546749013788,
    "accelerometerY": -1139.4316656437406,
    "accelerometerZ": 811.6928695575307,
    "gyroscopeX": 442.19879163299856,
    "gyroscopeY": 123.23710975717177,
    "gyroscopeZ": 708.5397575786151,
    "deviceState": "DANGER"
  },
  "annotations": {
    "iothub-connection-device-id": "<connectionDeviceId>",
    "iothub-connection-auth-method": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "<generationId>",
    "iothub-enqueuedtime": 1539381029965,
    "iothub-message-source": "Telemetry",
    "x-opt-sequence-number": 25325,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539381030200
  },
  "sequenceNumber": 25325,
  "enqueuedTimeUtc": "2018-10-12T21:50:30.200Z",
  "offset": "<offset>",
  "properties": {
    "content_type": "application/json",
    "content_encoding": "utf-8"
  }
}
```

### <a name="devices"></a>Geräte

Nachrichten, die Gerätedaten enthalten, werden alle paar Minuten an Ihren Event Hub, Ihre Service Bus-Warteschlange oder Ihr Thema gesendet. Das bedeutet, dass alle paar Minuten ein Batch von Nachrichten mit Daten zu Folgendem eingeht
- Neuen Geräten, die hinzugefügt wurden
- Geräten mit geänderten Eigenschafts- und Einstellungswerten

Jede Nachricht stellt eine oder mehrere Änderungen an einem Gerät seit der letzten exportierten Nachricht dar. Zu den Informationen, die in jeder Nachricht gesendet werden, gehören die folgenden:
- `id` des Geräts in IoT Central
- `name` des Geräts
- `deviceId` von [Device Provisioning Service](https://aka.ms/iotcentraldocsdps)
- Gerätevorlageninformationen
- Eigenschaftswerte
- Einstellungswerte

> [!NOTE]
> Geräte, die seit dem letzten Batch gelöscht wurden, werden nicht exportiert. Derzeit gibt es in exportierten Nachrichten keine Kennzeichnung gelöschter Geräte.
>
> Die Gerätevorlage, der die einzelnen Geräte zugewiesen sind, wird durch eine Gerätevorlagen-ID dargestellt. Um den Namen der Gerätevorlage abzurufen, müssen Sie auch Gerätevorlagendaten exportieren.

Das folgende Beispiel zeigt eine Nachricht zu Gerätedaten im Event Hub, in der Service-Bus-Warteschlange oder im Thema:


```json
{
  "body": {
    "id": "<id>",
    "name": "<deviceName>",
    "simulated": true,
    "deviceId": "<deviceId>",
    "deviceTemplate": {
      "id": "<templateId>",
      "version": "1.0.0"
    },
    "properties": {
      "cloud": {
        "location": "Seattle"
      },
      "device": {
        "dieNumber": 5445.5862873026645
      }
    },
    "settings": {
      "device": {
        "fanSpeed": 0
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "devices",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 39740,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274959654
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 39740,
  "enqueuedTimeUtc": "2018-10-11T16:22:39.654Z",
  "offset": "<offset>",
}
```

### <a name="device-templates"></a>Gerätevorlagen

Nachrichten, die Gerätevorlagendaten enthalten, werden alle paar Minuten an Ihren Event Hub, Ihre Service Bus-Warteschlange oder Ihr Thema gesendet. Das bedeutet, dass alle paar Minuten ein Batch von Nachrichten mit Daten zu Folgendem eingeht
- Neuen Gerätevorlagen, die hinzugefügt wurden
- Gerätevorlagen, deren Definition für Messungen, Eigenschaften und Einstellungen geändert wurden

Jede Nachricht stellt eine oder mehrere Änderungen an einer Gerätevorlage seit der letzten exportierten Nachricht dar. Zu den Informationen, die in jeder Nachricht gesendet werden, gehören die folgenden:
- `id` der Gerätevorlage
- `name` der Gerätevorlage
- `version` der Gerätevorlage
- Typen der Messdaten und Mindest- bzw. Maximalwerte
- Typen von Eigenschaftsdaten und Standardwerte
- Typen von Einstellungsdaten und Standardwerte

> [!NOTE]
> Gerätevorlagen, die seit dem letzten Batch gelöscht wurden, werden nicht exportiert. Derzeit gibt es in exportierten Nachrichten keine Kennzeichnung gelöschter Gerätevorlagen.

Das folgende Beispiel zeigt eine Nachricht zu Gerätevorlagendaten im Event Hub, in der Service-Bus-Warteschlange oder im Thema:

```json
{
  "body": {
    "id": "<id>",
    "version": "1.0.0",
    "name": "<templateName>",
    "measurements": {
      "telemetry": {
        "humidity": {
          "dataType": "double",
          "name": "humidity"
        },
        "pressure": {
          "dataType": "double",
          "name": "pressure"
        },
        "temp": {
          "dataType": "double",
          "name": "temperature"
        }
      }
    },
    "properties": {
      "cloud": {
        "location": {
          "dataType": "string",
          "name": "Location"
        }
      },
      "device": {
        "dieNumber": {
          "dataType": "double",
          "name": "Die Number"
        }
      }
    },
    "settings": {
      "device": {
        "fanSpeed": {
          "dataType": "double",
          "name": "Fan Speed",
          "initialValue": 0
        }
      }
    }
  },
  "annotations": {
    "iotcentral-message-source": "deviceTemplates",
    "x-opt-partition-key": "<partitionKey>",
    "x-opt-sequence-number": 25315,
    "x-opt-offset": "<offset>",
    "x-opt-enqueued-time": 1539274985085
  },
  "partitionKey": "<partitionKey>",
  "sequenceNumber": 25315,
  "enqueuedTimeUtc": "2018-10-11T16:23:05.085Z",
  "offset": "<offset>",
}
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie jetzt mit dem Exportieren Ihrer Daten in Azure Event Hubs und Azure Service Bus vertraut sind, können Sie mit dem nächsten Schritt fortfahren:

> [!div class="nextstepaction"]
> [Auslösen von Azure Functions](howto-trigger-azure-functions.md)
