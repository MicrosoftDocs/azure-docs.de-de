---
title: Erweitern von Azure IoT Central mit benutzerdefinierten Regeln und Benachrichtigungen | Microsoft-Dokumentation
description: Als Lösungsentwickler möchten Sie eine IoT Central-Anwendung konfigurieren, die E-Mail-Benachrichtigungen sendet, wenn ein Gerät keine Telemetriedaten mehr übermittelt. Diese Lösung verwendet Azure Stream Analytics und Azure Functions.
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 6140a8aea3fe0fe0a8f1c01cd1c97404c41f7a69
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65804798"
---
# <a name="extend-azure-iot-central-with-custom-rules-that-send-notifications"></a>Erweitern von Azure IoT Central mit benutzerdefinierten Regeln zum Senden von Benachrichtigungen

Diese Schrittanleitung veranschaulicht, wie Sie als Lösungsentwickler Ihre IoT Central-Anwendung um benutzerdefinierte Regeln und Benachrichtigungen erweitern können. Das Beispiel zeigt, wie eine Benachrichtigung an einen Bediener gesendet wird, wenn ein Gerät keine Telemetriedaten mehr sendet. Die Lösung verwendet eine [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/)-Abfrage, um zu erkennen, wenn ein Gerät das Senden von Telemetriedaten einstellt. Der Stream Analytics-Auftrag verwendet [Azure Functions](https://docs.microsoft.com/azure/azure-functions/), um mithilfe von [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/) Benachrichtigungs-E-Mails zu senden.

Diese Schrittanleitung zeigt, wie Sie IoT Central mithilfe von integrierten Regeln und Aktionen über die bisherige Funktionalität hinaus erweitern.

In dieser Schrittanleitung erfahren Sie Folgendes:

* Streamen von Telemetriedaten aus einer IoT Central-Anwendung mithilfe des *kontinuierlichen Datenexports*
* Erstellen einer Stream Analytics-Abfrage, die erkennt, wenn ein Gerät keine Daten mehr sendet
* Senden einer E-Mail-Benachrichtigung mithilfe der Dienste Azure Functions und SendGrid

## <a name="prerequisites"></a>Voraussetzungen

Um die in dieser Anleitung aufgeführten Schritte ausführen zu können, benötigen Sie ein aktives Azure-Abonnement.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

### <a name="iot-central-application"></a>IoT Central-Anwendung

Erstellen Sie über die Seite [Azure IoT Central – Eigene Anwendungen](https://aka.ms/iotcentral) eine IoT Central-Anwendung mit folgenden Einstellungen:

| Einstellung | Wert |
| ------- | ----- |
| Zahlungsplan | Nutzungsbasierte Bezahlung |
| Anwendungsvorlage | Beispiel „Contoso“ |
| Anwendungsname | Standardwert übernehmen oder eigenen Namen angeben |
| URL | Standardwert übernehmen oder eigenes eindeutiges URL-Präfix angeben |
| Verzeichnis | Ihr Azure Active Directory-Mandant |
| Azure-Abonnement | Ihr Azure-Abonnement |
| Region | USA (Ost) |

Bei den Beispielen und Screenshots in diesem Artikel wird die Region **USA, Osten** verwendet. Wählen Sie einen Standort in Ihrer Nähe, und stellen Sie sicher, dass Sie alle Ressourcen in derselben Region erstellen.

### <a name="resource-group"></a>Ressourcengruppe

Verwenden Sie das [Azure-Portal zum Erstellen einer Ressourcengruppe](https://portal.azure.com/#create/Microsoft.ResourceGroup) namens **DetectStoppedDevices**. Diese soll die weiteren Ressourcen enthalten, die Sie erstellen. Erstellen Sie Ihre Azure-Ressourcen am gleichen Standort wie Ihre IoT Central-Anwendung.

### <a name="event-hubs-namespace"></a>Event Hubs-Namespace

Verwenden Sie das [Azure-Portal zum Erstellen eines Event Hubs-Namespace](https://portal.azure.com/#create/Microsoft.EventHub) mit den folgenden Einstellungen:

| Einstellung | Wert |
| ------- | ----- |
| NAME    | Namen Ihres Namespace auswählen |
| Tarif | Basic |
| Abonnement | Ihr Abonnement |
| Ressourcengruppe | DetectStoppedDevices |
| Location | USA (Ost) |
| Durchsatzeinheiten | 1 |

### <a name="stream-analytics-job"></a>Stream Analytics-Auftrag

Verwenden Sie das [Azure-Portal zum Erstellen eines Stream Analytics-Namespace](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) mit den folgenden Einstellungen:

| Einstellung | Wert |
| ------- | ----- |
| NAME    | Namen Ihres Auftrags auswählen |
| Abonnement | Ihr Abonnement |
| Ressourcengruppe | DetectStoppedDevices |
| Location | USA (Ost) |
| Hosting-Umgebung | Cloud |
| Streamingeinheiten | 3 |

### <a name="function-app"></a>Funktionen-App

Verwenden Sie das [Azure-Portal zum Erstellen einer Funktions-App](https://portal.azure.com/#create/Microsoft.FunctionApp) mit den folgenden Einstellungen:

| Einstellung | Wert |
| ------- | ----- |
| App-Name    | Namen Ihrer Funktions-App auswählen |
| Abonnement | Ihr Abonnement |
| Ressourcengruppe | DetectStoppedDevices |
| Betriebssystem | Windows |
| Hostingplan | Verbrauchstarif |
| Location | USA (Ost) |
| Laufzeitstapel | .NET |
| Storage | Neu erstellen |

### <a name="sendgrid-account"></a>SendGrid-Konto

Verwenden Sie das [Azure-Portal zum Erstellen eines SendGrid-Kontos](https://portal.azure.com/#create/Sendgrid.sendgrid) mit den folgenden Einstellungen:

| Einstellung | Wert |
| ------- | ----- |
| NAME    | Namen Ihres SendGrid-Kontos auswählen |
| Kennwort | Kennwort erstellen |
| Abonnement | Ihr Abonnement |
| Ressourcengruppe | DetectStoppedDevices |
| Tarif | F1 Free |
| Kontaktinformationen | Erforderliche Informationen ausfüllen |

Wenn Sie alle erforderlichen Ressourcen erstellt haben, sieht Ihre Ressourcengruppe **DetectStoppedDevices** so aus wie im folgenden Screenshot:

![Ressourcengruppe für das Erkennen von angehaltenen Geräten](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Erstellen eines Ereignis-Hubs

Sie können eine IoT Central-Anwendung konfigurieren, um Telemetriedaten kontinuierlich an einen Event Hub zu exportieren. In diesem Abschnitt erstellen Sie einen Event Hub, um Telemetriedaten aus Ihrer IoT Central-Anwendung zu empfangen. Der Event Hub übermittelt die Telemetriedaten zur Verarbeitung an Ihren Stream Analytics-Auftrag.

1. Navigieren Sie im Azure-Portal zu Ihrem Event Hubs-Namespace, und klicken Sie auf **+ Event Hub**.
1. Nennen Sie den Event Hub **centralexport**, und klicken Sie auf **Erstellen**.

Ihr Event Hubs-Namespace sieht wie im folgenden Screenshot aus:

![Event Hubs-Namespace](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>Abrufen des SendGrid-API-Schlüssels

Ihre Funktions-App benötigt einen SendGrid-API-Schlüssel, um E-Mail-Nachrichten zu senden. So erstellen Sie einen SendGrid-API-Schlüssel:

1. Navigieren Sie im Azure-Portal zu Ihrem SendGrid-Konto. Wählen Sie dann **Verwalten** aus, um auf Ihr SendGrid-Konto zuzugreifen.
1. Wählen Sie in Ihrem SendGrid-Konto erst **Einstellungen** und dann **API-Schlüssel** aus. Klicken Sie auf **API-Schlüssel erstellen**:

    ![Erstellen des SendGrid-API-Schlüssels](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. Erstellen Sie auf der Seite **API-Schlüssel erstellen** einen Schlüssel namens **AzureFunctionAccess** mit der Berechtigung **Vollzugriff**.
1. Notieren Sie sich den API-Schlüssel, da Sie ihn beim Konfigurieren Ihrer Funktions-App benötigen werden.

## <a name="define-the-function"></a>Definieren der Funktion

Diese Lösung verwendet eine Azure Functions-App, um eine E-Mail-Benachrichtigung zu senden, wenn der Stream Analytics-Auftrag ein angehaltenes Gerät erkennt. So erstellen Sie Ihre Funktions-App:

1. Navigieren Sie im Azure-Portal zur **App Service**-Instanz in der Ressourcengruppe **DetectStoppedDevices**.
1. Klicken Sie auf das **+** -Zeichen, um eine neue Funktion zu erstellen.
1. Wählen Sie auf der Seite **ENTWICKLUNGSUMGEBUNG AUSWÄHLEN** die Option **Im Portal** aus, und klicken Sie auf **Weiter**.
1. Wählen Sie auf der Seite **FUNKTION ERSTELLEN** die Option **Webhook + API** aus, und klicken Sie dann auf **Erstellen**.

Das Portal erstellt eine Standardfunktion namens **HttpTrigger1**:

![Standardmäßige HTTP-Triggerfunktion](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Konfigurieren von Funktionsbindungen

Um E-Mails mit SendGrid zu senden, müssen Sie die Bindungen für Ihre Funktion wie folgt konfigurieren:

1. Klicken Sie auf **Integrieren**, wählen Sie die Ausgabe **HTTP ($return)** aus, und klicken Sie dann auf **Löschen**.
1. Klicken Sie auf **+ Neue Ausgabe**, wählen Sie **SendGrid** aus, und klicken Sie auf **Auswählen**. Klicken Sie auf **Installieren**, um die SendGrid-Erweiterung zu installieren.
1. Wenn die Installation abgeschlossen ist, wählen Sie **Funktionsrückgabewert verwenden** aus. Fügen Sie einen gültigen **Empfänger** aus, der E-Mail-Benachrichtigungen erhalten soll.  Fügen Sie einen gültigen **Absender** hinzu, der als E-Mail-Absender verwendet werden soll.
1. Klicken Sie neben **App-Einstellung für SendGrid-API-Schlüssel** auf **Neu**. Geben Sie **SendGridAPIKey** als Schlüssel sowie den SendGrid-API-Schlüssel, den Sie in einem vorherigen Schritt notiert haben, als Wert ein. Klicken Sie anschließend auf **Erstellen**.
1. Klicken Sie auf **Speichern**, um die SendGrid-Bindungen für Ihre Funktion zu speichern.

Die Integrationseinstellungen sehen wie im folgenden Screenshot aus:

![Integrationen der Funktions-App](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>Hinzufügen des Funktionscodes

Um Ihre Funktion zu implementieren, fügen Sie folgenden C#-Code hinzu, um eingehende HTTP-Anforderungen zu analysieren und E-Mails zu senden:

1. Wählen Sie in Ihrer Funktions-App die Funktion **HttpTrigger1** aus, und ersetzen Sie den C#-Code durch folgenden Code:

    ```csharp
    #r "Newtonsoft.Json"
    #r "..\bin\SendGrid.dll"

    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    Möglicherweise wird eine Fehlermeldung angezeigt, bis Sie den neuen Code speichern.

1. Klicken Sie auf **Speichern**, um die Funktion zu speichern.

### <a name="test-the-function-works"></a>Testen der Funktion

Um die Funktion im Portal zu testen, wählen Sie zuerst unten im Code-Editor die Option **Protokolle** aus. Klicken Sie dann im Code-Editor auf der rechten Seite auf **Testen**. Verwenden Sie folgenden JSON-Code als **Anforderungstext**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

Die Meldungen des Funktionsprotokolls werden im Bereich **Protokolle** angezeigt:

![Ausgabe des Funktionsprotokolls](media/howto-create-custom-rules/function-app-logs.png)

Nach einigen Minuten erhält die E-Mail-Adresse des **Empfängers** eine E-Mail mit folgendem Inhalt:

```txt
The following device(s) have stopped sending telemetry:

Device ID   Time
test-device-1   2019-05-02T14:23:39.527Z
test-device-2   2019-05-02T14:23:50.717Z
test-device-3   2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Hinzufügen einer Stream Analytics-Abfrage

Die Lösung verwendet eine Stream Analytics-Abfrage, um zu erkennen, wenn ein Gerät mehr als 120 Sekunden lang keine Telemetriedaten sendet. Die Abfrage verwendet die Telemetriedaten aus dem Event Hub als Eingabe. Der Auftrag sendet die Abfrageergebnisse an die Funktions-App. In diesem Abschnitt konfigurieren Sie den Stream Analytics-Auftrag:

1. Navigieren Sie im Azure-Portal zu Ihrem Stream Analytics-Auftrag. Wählen Sie unter **Auftragstopologie** den Eintrag **Eingaben** aus, klicken Sie auf **+ Datenstromeingabe hinzufügen**, und wählen Sie dann **Event Hub** aus.
1. Verwenden Sie die Informationen in der folgenden Tabelle, um die Eingabe mithilfe des zuvor erstellten Event Hubs zu konfigurieren, und klicken Sie dann auf **Speichern**:

    | Einstellung | Wert |
    | ------- | ----- |
    | Eingabealias | centraltelemetry |
    | Abonnement | Ihr Abonnement |
    | Event Hub-Namespace | Ihr Event Hub-Namespace |
    | Event Hub-Name | Vorhandenen Wert verwenden: **centralexport** |

1. Wählen Sie unter **Auftragstopologie** den Eintrag **Ausgaben** aus, klicken Sie auf **+ Hinzufügen**, und wählen Sie dann **Azure-Funktion** aus.
1. Verwenden Sie die Informationen in der folgenden Tabelle, um die Ausgabe zu konfigurieren, und klicken Sie dann auf **Speichern**:

    | Einstellung | Wert |
    | ------- | ----- |
    | Ausgabealias | emailnotification |
    | Abonnement | Ihr Abonnement |
    | Funktionen-App | Ihre Funktions-App |
    | Funktion  | HttpTrigger1 |

1. Wählen Sie unter **Auftragstopologie** die Option **Abfrage** aus, und ersetzen Sie die vorhandene Abfrage durch folgende SQL-Abfrage:

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. Wählen Sie **Speichern** aus.
1. Um den Stream Analytics-Auftrag zu starten, klicken Sie nacheinander auf **, Übersicht**, **Start**, **Jetzt** und **Start**:

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Konfigurieren des Exports nach IoT Central

Navigieren Sie zu der [IoT Central-Anwendung](https://aka.ms/iotcentral), die Sie aus der Contoso-Vorlage erstellt haben. In diesem Abschnitt konfigurieren Sie die Anwendung so, dass die Telemetriedaten aus den simulierten Geräten an Ihren Event Hub gestreamt werden. So konfigurieren Sie den Export:

1. Navigieren Sie zur Seite **Kontinuierlicher Datenexport**, klicken Sie auf **+ Neu**, und wählen Sie **Azure Event Hubs** aus.
1. Verwenden Sie die folgenden Einstellungen, um den Export zu konfigurieren, und klicken Sie dann auf **Speichern**:

    | Einstellung | Wert |
    | ------- | ----- |
    | Anzeigename | Exportieren nach Event Hubs |
    | Enabled | Andererseits |
    | Event Hubs-Namespace | Der Name Ihres Event Hubs-Namespace |
    | Event Hub | centralexport |
    | Messungen | Andererseits |
    | Geräte | Aus |
    | Gerätevorlagen | Aus |

![Konfiguration des kontinuierlichen Datenexports](media/howto-create-custom-rules/cde-configuration.png)

Warten Sie, bis der Exportstatus **Wird ausgeführt** lautet, bevor Sie fortfahren.

## <a name="test"></a>Test

Um die Lösung zu testen, können Sie den kontinuierlichen Datenexport aus IoT Central deaktivieren, um angehaltene Geräte zu simulieren:

1. Navigieren Sie in Ihrer IoT Central-Anwendung zur Seite **Kontinuierlicher Datenexport**, und wählen Sie die Exportkonfiguration **In Event Hubs exportieren** aus.
1. Legen Sie **Aktiviert** auf **Aus** fest, und klicken Sie auf **Speichern**.
1. Nach Ablauf von mindestens zwei Minuten erhält die als **Empfänger** angegebene E-Mail-Adresse mindestens eine E-Mail, deren Inhalt dem folgenden Beispiel ähnelt:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID   Time
    7b169aee-c843-4d41-9f25-7a02671ee659    2019-05-09T14:28:59.954Z
    ```

## <a name="tidy-up"></a>Aufräumen

Um nach dieser Vorgehensweise aufzuräumen und unnötige Kosten zu vermeiden, löschen Sie die Ressourcengruppe **DetectStoppedDevices** im Azure-Portal.

Sie können die IoT Central-Anwendung in der Anwendung selbst auf der Seite **Verwaltung** löschen.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schrittanleitung wurde Folgendes vermittelt:

* Streamen von Telemetriedaten aus einer IoT Central-Anwendung mithilfe des *kontinuierlichen Datenexports*
* Erstellen einer Stream Analytics-Abfrage, die erkennt, wenn ein Gerät keine Daten mehr sendet
* Senden einer E-Mail-Benachrichtigung mithilfe der Dienste Azure Functions und SendGrid

Sie wissen nun, wie Sie benutzerdefinierte Regeln und Benachrichtigungen erstellen. Als Nächstes empfiehlt die Lektüre des Artikels [Visualisieren und Analysieren der Azure IoT Central-Daten in einem Power BI-Dashboard](howto-connect-powerbi.md).
