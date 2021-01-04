---
title: 'Tutorial: Verwenden von IoT Hub-Ereignissen zum Auslösen von Azure Logic Apps'
description: In diesem Tutorial wird gezeigt, wie Sie mit dem Ereignisroutingdienst von Azure Event Grid automatisierte Prozesse zum Durchführen von Azure Logic Apps-Aktionen anhand von IoT Hub-Ereignissen erstellen.
services: iot-hub, event-grid
author: philmea
ms.service: iot-hub
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: philmea
ms.custom: devx-track-azurecli
ms.openlocfilehash: 857ae8d824443e9a8abdac7c4a66e2b014be2be0
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566349"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Tutorial: Senden von E-Mail-Benachrichtigungen zu Azure IoT Hub-Ereignissen mit Event Grid und Logic Apps

Azure Event Grid ermöglicht es Ihnen, auf Ereignisse in IoT Hub zu reagieren, indem Sie Aktionen in Ihren nachgeschalteten Geschäftsanwendungen auslösen.

In diesem Artikel wird Schritt für Schritt eine Beispielkonfiguration beschrieben, bei der IoT Hub und Event Grid verwendet werden. Am Ende verfügen Sie über eine Azure-Logik-App, durch die eine Benachrichtigungs-E-Mail gesendet wird, wenn ein Gerät eine Verbindung mit Ihrem IoT-Hub herstellt oder die Verbindung mit Ihrem IoT-Hub trennt. Event Grid kann verwendet werden, um zeitnah eine Benachrichtigung zu erhalten, wenn die Verbindung mit kritischen Geräten getrennt wird. Es kann mehrere Minuten dauern, bis Metriken und Diagnosen in Protokollen/Warnungen angezeigt werden. (Die Dauer kann 20 Minuten oder mehr betragen, dies ist jedoch keine verbindliche Angabe.) Für kritische Infrastruktur ist das möglicherweise nicht akzeptabel.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Ein E-Mail-Konto eines beliebigen, von Azure Logic Apps unterstützten E-Mail-Anbieters (beispielsweise Office 365 Outlook oder Outlook.com). Mit diesem E-Mail-Konto werden die Ereignisbenachrichtigungen gesendet.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

Mit dem Azure Cloud Shell-Terminal im Portal können Sie schnell einen neuen IoT-Hub erstellen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 

1. Wählen Sie rechts oben auf der Seite die Schaltfläche „Cloud Shell“ aus.

   ![Schaltfläche „Cloud Shell“](./media/publish-iot-hub-events-to-logic-apps/portal-cloud-shell.png)

1. Führen Sie den folgenden Befehl aus, um eine neue Ressourcengruppe zu erstellen:

   ```azurecli
   az group create --name {your resource group name} --location westus
   ```
    
1. Führen Sie den folgenden Befehl aus, um einen IoT-Hub zu erstellen:

   ```azurecli
   az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1 
   ```

1. Minimieren Sie das Cloud Shell-Terminal. Die Shell wird im weiteren Verlauf dieses Tutorials noch benötigt.

## <a name="create-a-logic-app"></a>Erstellen einer Logik-App

Erstellen Sie als Nächstes eine Logik-App, und fügen Sie einen HTTP-basierten Event Grid-Trigger für die Verarbeitung von Anforderungen des IoT-Hubs hinzu. 

### <a name="create-a-logic-app-resource"></a>Erstellen einer Logik-App-Ressource

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Ressource erstellen** aus, geben Sie im Suchfeld den Suchbegriff „Logik-App“ ein, und drücken Sie die EINGABETASTE. Wählen Sie aus den Ergebnissen den Eintrag **Logik-App** aus.

   ![Erstellen einer Logik-App](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. Wählen Sie auf dem nächsten Bildschirm die Option **Erstellen** aus. 

1. Geben Sie der Logik-App einen Namen, der in Ihrem Abonnement eindeutig ist, und wählen Sie dann dasselbe Abonnement, dieselbe Ressourcengruppe und denselben Standort wie für Ihren IoT Hub aus. 

   ![Felder für die Erstellung der Logik-App](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Klicken Sie auf **Überprüfen + erstellen**.

1. Überprüfen Sie Ihre Einstellungen, und wählen Sie anschließend **Erstellen** aus.

1. Wählen Sie nach Erstellung der Ressource die Option **Zu Ressource wechseln** aus. 

1. Navigieren Sie auf der Seite des Designers für Logik-Apps nach unten zu **Vorlagen**. Wählen Sie die Option **Leere Logik-App** aus, um Ihre Logik-App von Grund auf neu zu erstellen.

### <a name="select-a-trigger"></a>Auswählen eines Triggers

Ein Trigger ist ein bestimmtes Ereignis, durch das Ihre Logik-App gestartet wird. In diesem Tutorial empfängt der Trigger, der den Workflow einleitet, eine Anforderung über HTTP.  

1. Geben Sie auf der Suchleiste für Connectors und Trigger die Zeichenfolge **HTTP** ein.

1. Scrollen Sie durch die Ergebnisse, und wählen Sie **Anforderung – Beim Empfang einer HTTP-Anforderung** als Trigger aus. 

   ![Auswählen des HTTP-Anforderungstriggers](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. Wählen Sie **Beispielnutzlast zum Generieren eines Schemas verwenden** aus. 

   ![Verwenden einer Beispielnutzlast](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. Fügen Sie den JSON-Code des *Schemas für das Ereignis „Gerät verbunden“* JSON in das Textfeld ein, und wählen Sie **Fertig** aus:

   ```json
     [{  
      "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
      "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
      "subject": "devices/LogicAppTestDevice",
      "eventType": "Microsoft.Devices.DeviceConnected",
      "eventTime": "2018-06-02T19:17:44.4383997Z",
      "data": {
          "deviceConnectionStateEventInfo": {
            "sequenceNumber":
              "000000000000000001D4132452F67CE200000002000000000000000000000001"
          },
        "hubName": "egtesthub1",
        "deviceId": "LogicAppTestDevice",
        "moduleId" : "DeviceModuleID"
      }, 
      "dataVersion": "1",
      "metadataVersion": "1"
    }]
   ```

   Dieses Ereignis wird veröffentlicht, wenn ein Gerät mit einem IoT-Hub verbunden ist.

> [!NOTE]
> Möglicherweise erhalten Sie die Popupbenachrichtigung **Denken Sie daran, in Ihre Anforderung einen Content-Type-Header einzuschließen, der auf „application/json“ festgelegt ist**. Sie können diese Empfehlung problemlos ignorieren und mit dem nächsten Abschnitt fortfahren. 

### <a name="create-an-action"></a>Erstellen einer App

Aktionen sind alle Schritte, die auftreten, nachdem der Trigger den Logik-App-Workflow gestartet hat. Im Rahmen dieses Tutorials besteht die Aktion im Senden einer E-Mail-Benachrichtigung von Ihrem E-Mail-Anbieter. 

1. Wählen Sie **Neuer Schritt** aus. Ein Fenster mit der Option **Aktion auswählen** wird geöffnet.

1. Suchen Sie nach **Outlook**.

1. Suchen Sie abhängig von Ihrem E-Mail-Anbieter nach dem entsprechenden Connector, und wählen Sie diesen aus. In diesem Tutorial wird **Outlook.com** verwendet. Die Schritte für andere E-Mail-Anbieter sind ähnlich. 

   ![Auswählen des E-Mail-Anbieter-Connectors](./media/publish-iot-hub-events-to-logic-apps/outlook-step.png)

1. Wählen Sie die Aktion **E-Mail senden (V2)** aus. 

1. Wählen Sie **Anmelden** aus, und melden Sie sich bei Ihrem E-Mail-Konto an. Wählen Sie **Ja** aus, damit die App auf Ihre Informationen zugreifen kann.

1. Erstellen Sie die E-Mail-Vorlage. 

   * **An**: Geben Sie die E-Mail-Adresse zum Empfangen der Benachrichtigungs-E-Mails ein. Verwenden Sie für dieses Tutorial ein E-Mail-Konto, auf das Sie zu Testzwecken zugreifen können. 

   * **Antragsteller:** Geben Sie den Text für den Betreff ein. Wenn Sie auf das Textfeld „Betreff“ klicken, können Sie dynamischen Inhalt auswählen, der eingefügt werden soll. In diesem Tutorial wird beispielsweise `IoT Hub alert: {eventType}` verwendet. Wählen Sie den Hyperlink **Dynamischen Inhalt hinzufügen** aus, falls kein dynamischer Inhalt angezeigt wird. Hiermit wird die Option ein- bzw. ausgeschaltet.

   * **Text**: Schreiben Sie den Text für Ihre E-Mail. Wählen Sie im Auswahltool JSON-Eigenschaften aus, um dynamische Inhalte basierend auf Ereignisdaten einzuschließen. Wählen Sie unter dem Textfeld **Text** den Hyperlink **Dynamischen Inhalt hinzufügen** aus, falls der dynamische Inhalt nicht angezeigt wird. Falls die gewünschten Felder nicht angezeigt werden, klicken Sie im Fenster „Dynamische Inhalte“ auf *Mehr*, um die Felder der vorherigen Aktion einzufügen.

   Ihre E-Mail-Vorlage könnte folgendem Beispiel entsprechen:

   ![Ausfüllen der E-Mail-Informationen](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. Wählen Sie im Logik-App-Designer die Option **Speichern** aus.  

### <a name="copy-the-http-url"></a>Kopieren der HTTP-URL

Bevor Sie den Logik-App-Designer verlassen, kopieren Sie die URL, auf die Ihre Logik-App auf einen Trigger lauscht. Verwenden Sie diese URL zum Konfigurieren von Event Grid. 

1. Erweitern Sie das Triggerkonfigurationsfeld **Beim Empfang einer HTTP-Anforderung**, indem Sie darauf klicken. 

1. Kopieren Sie den Wert **HTTP POST-URL** durch Auswählen der Kopierschaltfläche daneben. 

   ![Kopieren der HTTP POST-URL](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. Speichern Sie diese URL, damit Sie sie im nächsten Abschnitt verwenden können. 

## <a name="configure-subscription-for-iot-hub-events"></a>Konfigurieren des Abonnements für IoT Hub-Ereignisse

In diesem Abschnitt konfigurieren Sie Ihren IoT Hub zum Veröffentlichen von Ereignissen, sobald diese auftreten. 

1. Navigieren Sie im Azure-Portal zu Ihrem IoT Hub. Hierfür können Sie die Option **Ressourcengruppen**, die Ressourcengruppe für dieses Tutorial und dann in der Liste mit den Ressourcen Ihren IoT-Hub auswählen.

1. Wählen Sie **Ereignisse** aus.

   ![Öffnen der Details zu Event Grid](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

1. Klicken Sie auf **Ereignisabonnement**. 

   ![Erstellen eines neuen Ereignisabonnements](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

1. Erstellen Sie das Ereignisabonnement mit den folgenden Werten: 

   1. Gehen Sie im Abschnitt **DETAILS ZUM EREIGNISABONNEMENT** wie folgt vor:
      1. Geben Sie einen **Namen** für das Ereignisabonnement an. 
      2. Wählen Sie als **Ereignisschema** die Option **Event Grid-Schema** aus. 
   2. Gehen Sie im Abschnitt **THEMENDETAILS** wie folgt vor:
      1. Vergewissern Sie sich, dass der **Thementyp** auf **IoT Hub** festgelegt ist. 
      2. Vergewissern Sie sich, dass der Name des IoT-Hubs als Wert für das Feld **Quellressource** festgelegt ist. 
      3. Geben Sie einen Namen für das zu erstellende **Systemthema** ein. Informationen zu Systemthemen finden Sie unter [Übersicht über Systemthemen](system-topics.md).
   3. Gehen Sie im Abschnitt **EREIGNISTYPEN** wie folgt vor:
      1. Wählen Sie die Dropdownliste **Nach Ereignistypen filtern** aus.
      1. Deaktivieren Sie die Kontrollkästchen **Gerät erstellt** und **Gerät gelöscht**, sodass nur noch die Kontrollkästchen **Gerät verbunden** und **Gerät getrennt** aktiviert sind.

         ![Auswählen von Abonnementereignistypen](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)
   
   4. Gehen Sie im Abschnitt **ENDPUNKTDETAILS** wie folgt vor: 
       1. Wählen Sie **Webhook** als **Endpunkttyp** aus.
       2. Klicken Sie auf **Endpunkt auswählen**. Fügen Sie die URL ein, die Sie aus Ihrer Logik-App kopiert haben, und bestätigen Sie die Auswahl.

         ![Auswählen der Endpunkt-URL](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

         Nach Abschluss des Vorgangs sollte der Bereich wie im folgenden Beispiel aussehen: 

         ![Beispielformular für ein Ereignisabonnement](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

1.  Klicken Sie auf **Erstellen**.

## <a name="simulate-a-new-device-connecting-and-sending-telemetry"></a>Simulieren eines neuen Geräts, das eine Verbindung herstellt und Telemetriedaten sendet

Testen Sie Ihre Logik-App, indem Sie über die Azure-Befehlszeilenschnittstelle schnell eine Geräteverbindung simulieren. 

1. Wählen Sie die Schaltfläche „Cloud Shell“ aus, um das Terminal wieder zu öffnen.

1. Führen Sie den folgenden Befehl aus, um eine simulierte Geräteidentität zu erstellen:
    
     ```azurecli 
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName}
    ```

1. Führen Sie den folgenden Befehl aus, um zu simulieren, dass Ihr Gerät eine Verbindung mit IoT Hub herstellt und Telemetriedaten sendet:

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. Wenn das simulierte Gerät eine Verbindung mit dem IoT Hub herstellt, erhalten Sie eine E-Mail mit einem Hinweis auf ein Ereignis vom Typ „DeviceConnected“.

1. Nach Abschluss der Simulation erhalten Sie eine E-Mail mit einem Hinweis auf ein Ereignis vom Typ „DeviceDisconnected“. 

    ![Beispiel für eine Warn-E-Mail](./media/publish-iot-hub-events-to-logic-apps/alert-mail.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In diesem Tutorial werden Ressourcen verwendet, für die Gebühren in Ihrem Azure-Abonnement anfallen. Wenn Sie das Ausprobieren des Tutorials und Testen Ihrer Ergebnisse abgeschlossen haben, deaktivieren oder löschen Sie die Ressourcen, die Sie nicht beibehalten möchten. 

Löschen Sie die Ressourcengruppe, falls Sie alle für dieses Tutorial erstellten Ressourcen löschen möchten. 

1. Wählen Sie die Option **Ressourcengruppen** und anschließend die Ressourcengruppe aus, die Sie für dieses Tutorial erstellt haben.

2. Wählen Sie im Bereich „Ressourcengruppe“ die Option **Ressourcengruppe löschen** aus. Sie werden aufgefordert, den Namen der Ressourcengruppe einzugeben. Anschließend können Sie ihn löschen. Alle darin enthaltenen Ressourcen werden ebenfalls entfernt.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über das [Reagieren auf Ereignisse in IoT Hub mithilfe von Event Grid zum Auslösen von Aktionen](../iot-hub/iot-hub-event-grid.md).
* [Erfahren Sie, wie Sie Ereignisse im Zusammenhang mit der Herstellung und Trennung von Geräteverbindungen sortieren.](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* Erfahren Sie mehr über die Möglichkeiten mit [Event Grid](overview.md).

Eine vollständige Liste der unterstützten Logic App-Connectors finden Sie in der [Übersicht über Connectors](/connectors/).
