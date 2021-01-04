---
title: IoT-Remoteüberwachung und -Benachrichtigungen mit Azure Logic Apps | Microsoft-Dokumentation
description: Verwenden Sie Azure Logic Apps für die IoT-Temperaturüberwachung auf Ihrem IoT Hub und zum automatischen Senden von E-Mail-Benachrichtigungen an Ihr Postfach, sobald Anomalien gefunden werden.
author: robinsh
keywords: IoT-Überwachung, IoT-Benachrichtigungen, IoT-Temperaturüberwachung
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: cd14ff0688f4230aeedac748ca4b32609bdd2938
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490321"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>IoT-Remoteüberwachung und -Benachrichtigungen mit Azure Logic Apps zum Herstellen einer Verbindung zwischen Ihrem IoT Hub und Postfach

![Lückenloses Diagramm](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure Logic Apps](../logic-apps/index.yml) unterstützt Sie bei der übergreifenden Orchestrierung von Workflows für lokale und cloudbasierte Dienste, für einzelne oder mehrere Unternehmen sowie für verschiedene Protokolle. Eine Logik-App beginnt mit einem Trigger, gefolgt von mindestens einer Aktion. Mehrere Aktionen können mithilfe integrierter Steuerungen sequenziert werden. Hierzu zählen etwa Bedingungen und Iteratoren. Diese Flexibilität macht Logic Apps zu einer idealen IoT-Lösung für die IoT-Überwachung. Beispielsweise kann das Eintreffen von Telemetriedaten eines Geräts bei einem IoT Hub-Endpunkt Logik-App-Workflows auslösen, um die Daten in einem Azure Storage-Blob zu speichern, E-Mail-Benachrichtigungen mit einer Warnung vor Datenanomalien zu senden, den Einsatz eines Technikers zu planen, wenn ein Gerät einen Fehler meldet, und vieles mehr.

## <a name="what-you-learn"></a>Lerninhalt

Sie erfahren, wie Sie eine Logik-App erstellen, die Ihren IoT Hub und Ihr Postfach für die Temperaturüberwachung und Benachrichtigungen verbindet.

Der auf Ihrem Gerät ausgeführte Clientcode legt für jede an Ihren IoT-Hub gesendete Telemetrienachricht die Anwendungseigenschaft `temperatureAlert` fest. Wenn der Clientcode eine Temperatur von über 30 °C erkennt, wird diese Eigenschaft auf `true` festgelegt. Andernfalls wird die Eigenschaft auf `false` festgelegt.

Folgendes sieht Nachrichten ähnlich, die bei Ihrem IoT-Hub eingehen und die Telemetriedaten in Text und die `temperatureAlert`-Eigenschaft in den Anwendungseigenschaften enthalten (Systemeigenschaften werden nicht angezeigt):

```json
{
  "body": {
    "messageId": 18,
    "deviceId": "Raspberry Pi Web Client",
    "temperature": 27.796111770668457,
    "humidity": 66.77637926438427
  },
  "applicationProperties": {
    "temperatureAlert": "false"
  }
}
```

Weitere Informationen über das IoT Hub-Nachrichtenformat finden Sie unter [Erstellen und Lesen von IoT Hub-Nachrichten](iot-hub-devguide-messages-construct.md).

In diesem Artikel haben Sie das Routing Ihres IoT-Hubs zum Senden von Nachrichten mit der `temperatureAlert`-Eigenschaft mit dem Wert `true` an einen Service Bus-Endpunkt eingerichtet. Anschließend haben Sie eine Logik-App eingerichtet, die bei auf dem Service Bus-Endpunkt eingehenden Nachrichten ausgelöst wird und Ihnen eine E-Mail-Benachrichtigung sendet.

## <a name="what-you-do"></a>Aufgaben

* Erstellen eines Service Bus-Namespace und Hinzufügen einer Service Bus-Warteschlange
* Hinzufügen eines benutzerdefinierten Endpunkts und einer Routingregel zu Ihrem IoT-Hub, sodass Nachrichten, die eine Temperaturwarnung enthalten, an die Service Bus-Warteschlange weitergeleitet werden
* Erstellen, Konfigurieren und Testen einer Logik-App, um Nachrichten aus Ihrer Service Bus-Warteschlange zu nutzen und Benachrichtigungs-E-Mails an den gewünschten Empfänger zu senden

## <a name="what-you-need"></a>Voraussetzungen

* Sie müssen das Tutorial [Verbinden des Raspberry Pi-Onlinesimulators mit Azure IoT Hub (Node.js)](iot-hub-raspberry-pi-web-simulator-get-started.md) oder eines der gerätespezifischen Tutorials wie [Verbinden von Raspberry Pi mit Azure IoT Hub (Node.js)](iot-hub-raspberry-pi-kit-node-get-started.md) abgeschlossen haben. In diesen werden folgende Anforderungen beschrieben:

  * Ein aktives Azure-Abonnement.
  * Ein Azure IoT Hub in Ihrem Abonnement.
  * Eine auf Ihrem Gerät ausgeführte Clientanwendung, die Telemetrienachrichten an Ihre Azure IoT Hub-Instanz sendet.

## <a name="create-service-bus-namespace-and-queue"></a>Erstellen eines Service Bus-Namespace und einer Warteschlange

Erstellen Sie einen Service Bus-Namespace und eine Service Bus-Warteschlange. Im weiteren Verlauf dieses Themas erstellen Sie in Ihrem IoT-Hub eine Routingregel, um Nachrichten, die eine Temperaturwarnung enthalten, an die Service Bus-Warteschlange weiterzuleiten, wo sie von einer Logik-App verwertet werden und das Senden einer Benachrichtigungs-E-Mail auslösen.

### <a name="create-a-service-bus-namespace"></a>Erstellen eines Service Bus-Namespace

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) die Option **+ Ressource erstellen** > **Integration** > **Service Bus** aus.

1. Geben Sie im Bereich **Namespace erstellen** folgende Informationen an:

   **Name** : Der Name des Service Bus-Namespace. Der Namespace muss innerhalb von Azure eindeutig sein.

   **Tarif:** Wählen Sie **Basic** aus der Dropdownliste aus. Der Tarif „Basic“ ist für dieses Tutorial ausreichend.

   **Ressourcengruppe** : Verwenden Sie dieselbe Ressourcengruppe wie für Ihren IoT Hub.

   **Standort** : Verwenden Sie denselben Speicherort, den Ihr IoT-Hub verwendet.

   ![Erstellen eines Service Bus-Namespaces im Azure-Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. Klicken Sie auf **Erstellen**. Warten Sie, bis die Bereitstellung abgeschlossen ist, bevor Sie mit dem nächsten Schritt fortfahren.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>Hinzufügen einer Service Bus-Warteschlange zum Namespace

1. Öffnen Sie den Service Bus-Namespace. Wählen Sie dazu am besten im Ressourcenbereich die Option **Ressourcengruppen** , Ihre Ressourcengruppe und anschließend in der Ressourcenliste den Service Bus-Namespace aus.

1. Wählen Sie im Bereich **Service Bus-Namespace** die Option **+ Warteschlange** aus.

1. Geben Sie einen Namen für die Warteschlange ein, und wählen Sie dann **Erstellen** aus. Nach erfolgreicher Erstellung der Warteschlange wird der Bereich **Warteschlange erstellen** geschlossen.

   ![Hinzufügen einer Service Bus-Warteschlange im Azure-Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. Wählen Sie im Bereich **Service Bus-Namespace** unter **Entitäten** die Option **Warteschlangen** aus. Öffnen Sie die Service Bus-Warteschlange über die Liste, und wählen Sie anschließend **Freigegebene Zugriffsrichtlinien** >  **+ Hinzufügen** aus.

1. Geben Sie einen Namen für die Richtlinie ein, aktivieren Sie **Verwalten** , und wählen Sie dann **Erstellen** aus.

   ![Hinzufügen einer Service Bus-Richtlinie im Azure-Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>Hinzufügen eines benutzerdefinierten Endpunkts und einer Routingregel zu Ihrem IoT-Hub

Fügen Sie Ihrem IoT-Hub einen benutzerdefinierten Endpunkt für die Service Bus-Warteschlange hinzu, und erstellen Sie eine Nachrichtenroutingregel, sodass Nachrichten, die eine Temperaturwarnung enthalten, an diesen Endpunkt weitergeleitet werden, wo sie von Ihrer Logik-App verwertet werden. Die Routingregel verwendet eine Routingabfrage (`temperatureAlert = "true"`), um Nachrichten auf der Grundlage des Werts der Anwendungseigenschaft `temperatureAlert` weiterzuleiten, die vom auf dem Gerät ausgeführten Clientcode festgelegt wurde. Weitere Informationen finden Sie unter [Abfrage des Nachrichtenroutings basierend auf Nachrichteneigenschaften](./iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-properties).

### <a name="add-a-custom-endpoint"></a>Hinzufügen eines benutzerdefinierten Endpunkts

1. Öffnen Sie Ihren IoT Hub. Wählen Sie dazu am besten im Ressourcenbereich die Option **Ressourcengruppen** , Ihre Ressourcengruppe und anschließend in der Ressourcenliste Ihren IoT-Hub aus.

1. Wählen Sie unter **Messaging** die Option **Nachrichtenrouting** aus. Wählen Sie im Bereich **Nachrichtenrouting** die Registerkarte **Benutzerdefinierte Endpunkte** und anschließend **+ Hinzufügen** aus. Wählen Sie in der Dropdownliste die Option **Service Bus-Warteschlange** aus.

   ![Screenshot der Option „Service Bus-Warteschlange“](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. Geben Sie im Bereich **Service Bus-Endpunkt hinzufügen** folgende Informationen ein:

   **Endpunktname** : Der Name des Endpunkts.

   **Service Bus-Namespace** : Wählen Sie den erstellten Namespace aus.

   **Service Bus-Warteschlange** : Wählen Sie die erstellte Warteschlange aus.

   ![Hinzufügen eines Endpunkts zu Ihren IoT Hub im Azure-Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. Klicken Sie auf **Erstellen**. Fahren Sie nach erfolgreicher Erstellung des Endpunkts mit dem nächsten Schritt fort.

### <a name="add-a-routing-rule"></a>Hinzufügen einer Routingregel

1. Wählen Sie im Bereich **Nachrichtenrouting** die Registerkarte **Routen** und anschließend **+ Hinzufügen** aus.

1. Geben Sie im Bereich **Route hinzufügen** folgende Informationen ein:

   **Name** : Der Name der Routingregel.

   **Endpunkt** : Wählen Sie den erstellten Endpunkt aus.

   **Datenquelle** : Wählen Sie **Gerätetelemetrienachrichten** aus.

   **Routingabfrage** : Geben Sie `temperatureAlert = "true"` ein.

   ![Hinzufügen einer Routingregel im Azure-Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. Wählen Sie **Speichern** aus. Der Bereich **Nachrichtenrouting** kann nun geschlossen werden.

## <a name="create-and-configure-a-logic-app"></a>Erstellen und Konfigurieren einer Logik-App

Im vorherigen Abschnitt haben Sie Ihren IoT-Hub eingerichtet, um Nachrichten, die eine Temperaturwarnung enthalten, an Ihre Service Bus-Warteschlange weiterzuleiten. In diesem Abschnitt richten Sie eine Logik-App ein, um die Service Bus-Warteschlange zu überwachen und eine E-Mail-Benachrichtigung zu senden, wenn der Warteschlange eine Nachricht hinzugefügt wird.

### <a name="create-a-logic-app"></a>Erstellen einer Logik-App

1. Wählen Sie **Ressource erstellen** > **Integration** > **Logik-App** aus.

1. Geben Sie Folgendes ein:

   **Name** : Der Name der Logik-App.

   **Ressourcengruppe** : Verwenden Sie dieselbe Ressourcengruppe wie für Ihren IoT Hub.

   **Standort** : Verwenden Sie denselben Speicherort, den Ihr IoT-Hub verwendet.

   ![Erstellen einer Logik-App im Azure-Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. Klicken Sie auf **Erstellen**.

### <a name="configure-the-logic-app-trigger"></a>Konfigurieren des Logik-App-Triggers

1. Öffnen Sie die Logik-App. Wählen Sie dazu am besten im Ressourcenbereich die Option **Ressourcengruppen** , Ihre Ressourcengruppe und anschließend in der Ressourcenliste Ihre Logik-App aus. Wenn Sie die Logik-App auswählen, wird der Logic Apps-Designer geöffnet.

1. Scrollen Sie im Logic Apps-Designer nach unten zu **Vorlagen** , und wählen Sie **Leere Logik-App** aus.

   ![Beginnen Sie im Azure-Portal mit einer leeren Logik-App.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. Wählen Sie die Registerkarte **Alle** und anschließend **Service Bus** aus.

   ![Wählen Sie „Service Bus“ aus, um mit der Erstellung Ihrer Logik-App im Azure-Portal zu beginnen.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. Wählen Sie unter **Trigger** die Option **Service Bus – Bei Empfang mindestens einer Nachricht in der Warteschlange (autom. abschließen)** aus.

   ![Auswählen des Triggers für Ihre Logik-App im Azure-Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Erstellen Sie eine Service Bus-Verbindung.
   1. Geben Sie einen Verbindungsnamen ein, und wählen Sie Ihren Service Bus-Namespace aus der Liste aus. Der nächste Bildschirm wird geöffnet.

      ![Screenshot der Option „Bei Empfang mindestens einer Nachricht in der Warteschlange (autom. abschließen)“](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Wählen Sie die Service Bus-Richtlinie (RootManageSharedAccessKey) aus. Wählen Sie anschließend **Erstellen** aus.

      ![Erstellen einer Service Bus-Verbindung für Ihre Logik-App im Azure-Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. Wählen Sie im letzten Bildschirm in der Dropdownliste **Warteschlangenname** die Warteschlange aus, die Sie erstellt haben. Geben Sie unter **Maximale Nachrichtenanzahl** den Wert `175` ein.

      ![Angeben der maximalen Nachrichtenanzahl für die Service Bus-Verbindung in der Logik-App](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Wählen Sie im Logic Apps-Designer im Menü am oberen Rand die Option **Speichern** aus, um Ihre Änderungen zu speichern.

### <a name="configure-the-logic-app-action"></a>Konfigurieren der Logik-App-Aktion

1. Erstellen Sie eine SMTP-Dienstverbindung.

   1. Wählen Sie **Neuer Schritt** aus. Wählen Sie unter **Aktion auswählen** die Registerkarte **Alle** aus.

   1. Geben Sie `smtp` in das Suchfeld ein, wählen Sie in den Suchergebnissen den Dienst **SMTP** aus, und wählen Sie anschließend **E-Mail senden** aus.

      ![Erstellen einer SMTP-Verbindung in Ihrer Logik-App im Azure-Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. Geben Sie die SMTP-Informationen für Ihr Postfach ein, und wählen Sie **Erstellen** aus.

      ![Eingeben der Informationen für die SMTP-Verbindung in Ihrer Logik-App im Azure-Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      Rufen Sie die SMTP-Informationen für [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en) und [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html) ab.

      > [!NOTE]
      > Möglicherweise müssen Sie TLS/SSL deaktivieren, um die Verbindung herstellen zu können. Wenn dies der Fall ist und Sie TLS nach dem Herstellen der Verbindung wieder aktivieren möchten, führen Sie den optionalen Schritt am Ende dieses Abschnitts aus.

   1. Wählen Sie in der Dropdownliste **Neuen Parameter hinzufügen** des Schritts **E-Mail senden** die Optionen **Von** , **An** , **Betreff** und **Text** aus. Klicken oder tippen Sie auf eine beliebige Stelle des Bildschirms, um das Auswahlfeld zu schließen.

      ![Auswählen der E-Mail-Felder für die SMTP-Verbindung](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. Geben Sie für **Von** und **An** Ihre E-Mail-Adresse und `High temperature detected` für **Betreff** und **Text** ein. Sollte das Dialogfeld **Fügen Sie dynamischen Inhalt aus den Apps und Connectors hinzu, die in diesem Flow verwendet werden.** angezeigt werden, wählen Sie **Ausblenden** aus, um es zu schließen. In diesem Tutorial werden keine dynamischen Inhalte verwendet.

      ![Ausfüllen der E-Mail-Felder für die SMTP-Verbindung](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. Wählen Sie **Speichern** aus, um die SMTP-Verbindung zu speichern.

1. (Optional) Wenn Sie für die Verbindungsherstellung mit Ihrem E-Mail-Anbieter TLS deaktivieren mussten und es nun wieder aktivieren möchten, gehen Sie wie folgt vor:

   1. Wählen Sie im Bereich **Logik-App** unter **Entwicklungstools** die Option **API-Verbindungen** aus.

   1. Wählen Sie in der Liste mit den API-Verbindungen die SMTP-Verbindung aus.

   1. Wählen Sie im Bereich **SMTP – API-Verbindung bearbeiten** unter **Allgemein** die Option **API-Verbindung bearbeiten** aus.

   1. Aktivieren Sie im Bereich **API-Verbindung bearbeiten** das Kontrollkästchen **SSL aktivieren?** , geben Sie erneut das Kennwort für Ihr E-Mail-Konto ein, und wählen Sie **Speichern** aus.

      ![Bearbeiten der SMTP-API-Verbindung in Ihrer Logik-App im Azure-Portal](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

Ihre Logik-App kann nun Temperaturwarnungen aus der Service Bus-Warteschlange verarbeiten und Benachrichtigungen an Ihr E-Mail-Konto senden.

## <a name="test-the-logic-app"></a>Testen der Logik-App

1. Starten Sie die Clientanwendung auf Ihrem Gerät.

1. Bringen Sie bei Verwendung eines physischen Geräts vorsichtig eine Wärmequelle in die Nähe des Wärmesensors, bis die Temperatur 30 °C übersteigt. Bei Verwendung des Onlinesimulators gibt der Clientcode willkürlich Telemetrienachrichten mit mehr als 30 °C aus.

1. Daraufhin sollten Sie von der Logik-App gesendete E-Mail-Benachrichtigung erhalten.

   > [!NOTE]
   > Ihr E-Mail-Dienstanbieter muss ggf. die Identität des Absenders überprüfen, um sicherzustellen, dass die gesendete E-Mail von Ihnen stammt.

## <a name="next-steps"></a>Nächste Schritte

Sie haben erfolgreich eine Logik-App erstellt, die Ihren IoT Hub und Ihr Postfach für die Temperaturüberwachung und Benachrichtigungen verbindet.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]