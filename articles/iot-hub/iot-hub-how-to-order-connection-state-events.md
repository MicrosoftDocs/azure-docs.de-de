---
title: Sortieren von Geräteverbindungsereignissen von Azure IoT Hub mithilfe von Azure Cosmos DB | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Geräteverbindungsereignisse von Azure IoT Hub mit Azure Cosmos DB sortieren und aufzeichnen, um den aktuellen Verbindungsstatus zu verwalten
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: asrastog
ms.openlocfilehash: ff8f8c6656c4cd095749b3e048c72572d113f1ad
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "66015262"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Sortieren von Geräteverbindungsereignissen von Azure IoT Hub mithilfe von Azure Cosmos DB

Azure Event Grid unterstützt Sie beim Erstellen ereignisbasierter Anwendungen und der einfachen Integration von IoT-Ereignissen in Ihre Geschäftslösungen. Dieser Artikel führt Sie durch eine Konfiguration, mit der der letzte Geräteverbindungsstatus in Cosmos DB nachverfolgt und gespeichert werden kann. Wir verwenden die Sequenznummer aus den Ereignissen „Gerät verbunden“ und „Gerät getrennt“ und speichern den letzten Zustand in Cosmos DB. Dazu verwenden wir eine gespeicherte Prozedur, bei der es sich um Anwendungslogik handelt, die für eine Sammlung in Cosmos DB ausgeführt wird.

Die Sequenznummer ist eine Zeichenfolgendarstellung einer Hexadezimalzahl. Sie können einen Zeichenfolgenvergleich verwenden, um die größere Zahl zu ermitteln. Wenn Sie die Zeichenfolge in eine hexadezimale Zahl konvertieren, entsteht eine 256-Bit-Zahl. Die Sequenznummer ist immer ansteigend, wobei das letzte Ereignis eine größere Zahl als andere Ereignisse hat. Dies ist bei häufigen Geräteverbindungen und -trennungen nützlich, wenn Sie sicherstellen möchten, dass nur das letzte Ereignis zum Auslösen einer Downstreamaktion verwendet wird, da Azure Event Grid das Sortieren von Ereignissen nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

* Ein aktives Azure-Konto. Wenn Sie noch nicht über eines verfügen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/pricing/free-trial/).

* Ein aktives Azure Cosmos DB-SQL-API-Konto. Wenn Sie noch keines erstellt haben, finden Sie unter [Erstellen eines Datenbankkontos](../cosmos-db/create-sql-api-dotnet.md#create-an-azure-cosmos-db-account) eine exemplarische Vorgehensweise.

* Eine Sammlung in der Datenbank. Eine exemplarische Vorgehensweise finden Sie unter [Hinzufügen einer Sammlung](../cosmos-db/create-sql-api-dotnet.md#add-a-database-and-a-collection). Wenn Sie Ihre Sammlung erstellen, verwenden Sie `/id` als Partitionsschlüssel.

* Eine IoT Hub-Instanz in Azure. Wenn Sie noch keinen erstellt haben, finden Sie unter [Erste Schritte mit IoT Hub](iot-hub-csharp-csharp-getstarted.md) eine exemplarische Vorgehensweise.

## <a name="create-a-stored-procedure"></a>Erstellen einer gespeicherten Prozedur

Zunächst erstellen Sie eine gespeicherte Prozedur und legen fest, dass diese eine Logik ausführt, mit der Sequenznummern eingehender Ereignisse verglichen werden und das letzte Ereignis pro Gerät in der Datenbank gespeichert wird.

1. Wählen Sie in der Cosmos DB-SQL-API **Daten-Explorer** > **Elemente** > **Neue gespeicherte Prozedur** aus.

   ![Erstellen einer gespeicherten Prozedur](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Geben Sie **LatestDeviceConnectionState** für die ID der gespeicherten Prozedur ein, und fügen Sie Folgendes in **Text der gespeicherten Prozedur** ein. Beachten Sie, dass dieser Code allen vorhandenen Code im Text der gespeicherten Prozedur ersetzen sollte. Dieser Code behält eine Zeile pro Geräte-ID bei und speichert den letzten Verbindungsstatus der Geräte-ID durch Ermitteln der höchsten Sequenznummer.

    ```javascript
    // SAMPLE STORED PROCEDURE
    function UpdateDevice(deviceId, moduleId, hubName, connectionState, connectionStateUpdatedTime, sequenceNumber) {
      var collection = getContext().getCollection();
      var response = {};

      var docLink = getDocumentLink(deviceId, moduleId);

      var isAccepted = collection.readDocument(docLink, function(err, doc) {
        if (err) {
          console.log('Cannot find device ' + docLink + ' - ');
          createDocument();
        } else {
          console.log('Document Found - ');
          replaceDocument(doc);
        }
      });

      function replaceDocument(document) {
        console.log(
          'Old Seq :' +
            document.sequenceNumber +
            ' New Seq: ' +
            sequenceNumber +
            ' - '
        );
        if (sequenceNumber > document.sequenceNumber) {
          document.connectionState = connectionState;
          document.connectionStateUpdatedTime = connectionStateUpdatedTime;
          document.sequenceNumber = sequenceNumber;

          console.log('replace doc - ');

          isAccepted = collection.replaceDocument(docLink, document, function(
            err,
            updated
          ) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(updated);
            }
          });
        } else {
          getContext()
            .getResponse()
            .setBody('Old Event - current: ' + document.sequenceNumber + ' Incoming: ' + sequenceNumber);
        }
      }
      function createDocument() {
        document = {
          id: deviceId + '-' + moduleId,
          deviceId: deviceId,
          moduleId: moduleId,
          hubName: hubName,
          connectionState: connectionState,
          connectionStateUpdatedTime: connectionStateUpdatedTime,
          sequenceNumber: sequenceNumber
        };
        console.log('Add new device - ' + collection.getAltLink());
        isAccepted = collection.createDocument(
          collection.getAltLink(),
          document,
          function(err, doc) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(doc);
            }
          }
        );
      }

      function getDocumentLink(deviceId, moduleId) {
        return collection.getAltLink() + '/docs/' + deviceId + '-' + moduleId;
      }
    }
    ```

3. Speichern der gespeicherten Prozedur:

    ![Speichern der gespeicherten Prozedur](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>Erstellen einer Logik-App

Erstellen Sie zuerst eine Logik-App, und fügen Sie einen Ereignisrastertrigger hinzu, der die Ressourcengruppe für Ihren virtuellen Computer überwacht.

### <a name="create-a-logic-app-resource"></a>Erstellen einer Logik-App-Ressource

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **+Ressource erstellen**, dann **Integration** und **Logik-App** aus.

   ![Erstellen einer Logik-App](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Geben Sie der Logik-App einen Namen, der in Ihrem Abonnement eindeutig ist, und wählen Sie dann dasselbe Abonnement, dieselbe Ressourcengruppe und denselben Standort wie für Ihren IoT Hub aus.

   ![Neue Logik-App](./media/iot-hub-how-to-order-connection-state-events/new-logic-app.png)

3. Wählen Sie **Erstellen** aus, um die Logik-App zu erstellen.

   Sie haben nun eine Azure-Ressource für Ihre Logik-App erstellt. Nachdem Azure Ihre Logik-App bereitgestellt hat, zeigt der Designer für Logik-Apps Vorlagen für allgemeine Muster an, die Ihnen einen schnelleren Einstieg ermöglichen.

   > [!NOTE]
   > Um Ihre Logik-App zu finden und wieder zu öffnen, wählen Sie **Ressourcengruppen** und dann die Ressourcengruppe aus, die Sie für diese Vorgehensweise verwenden. Wählen Sie dann Ihre neue Logik-App aus. Dadurch wird der Logik-App-Designer geöffnet.

4. Scrollen Sie im Logik-App-Designer nach rechts, bis die allgemeinen Auslöser angezeigt werden. Wählen Sie unter **Vorlagen** die Option **Leere Logik-App** aus, um Ihre Logik-App von Grund auf zu erstellen.

### <a name="select-a-trigger"></a>Auswählen eines Triggers

Ein Trigger ist ein bestimmtes Ereignis, durch das Ihre Logik-App gestartet wird. In diesem Tutorial empfängt der Trigger, der den Workflow einleitet, eine Anforderung über HTTP.

1. Geben Sie auf der Suchleiste für Connectors und Trigger die Zeichenfolge **HTTP** ein, und drücken Sie die EINGABETASTE.

2. Wählen Sie **Anforderung – Beim Empfang einer HTTP-Anforderung** als Trigger aus.

   ![Auswählen des HTTP-Anforderungstriggers](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Wählen Sie **Beispielnutzlast zum Generieren eines Schemas verwenden** aus.

   ![Beispielnutzlast zum Generieren eines Schemas verwenden](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

4. Fügen Sie den folgenden Beispiel-JSON-Code in das Textfeld ein, und wählen Sie dann **Fertig** aus:

   ```json
   [{
    "id": "fbfd8ee1-cf78-74c6-dbcf-e1c58638ccbd",
    "topic":
      "/SUBSCRIPTIONS/DEMO5CDD-8DAB-4CF4-9B2F-C22E8A755472/RESOURCEGROUPS/EGTESTRG/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/MYIOTHUB",
    "subject": "devices/Demo-Device-1",
    "eventType": "Microsoft.Devices.DeviceConnected",
    "eventTime": "2018-07-03T23:20:11.6921933+00:00",
    "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
      "hubName": "MYIOTHUB",
      "deviceId": "48e44e11-1437-4907-83b1-4a8d7e89859e",
      "moduleId": ""
    },
    "dataVersion": "1",
    "metadataVersion": "1"
   }]
   ```

   ![Einfügen einer Beispiel-JSON-Nutzlast](./media/iot-hub-how-to-order-connection-state-events/paste-sample-payload.png)

5. Möglicherweise erhalten Sie die Popupbenachrichtigung **Denken Sie daran, in Ihre Anforderung einen Content-Type-Header einzuschließen, der auf „application/json“ festgelegt ist**. Sie können diese Empfehlung problemlos ignorieren und mit dem nächsten Abschnitt fortfahren.

### <a name="create-a-condition"></a>Erstellen einer Bedingung

In Ihrem Logik-App-Workflow helfen Bedingungen bei der Ausführung bestimmter Aktionen nach dem Übergeben einer bestimmten Bedingung. Sobald die Bedingung erfüllt ist, kann eine gewünschte Aktion definiert werden. In diesem Tutorial wird als Bedingung überprüft, ob der eventType „Gerät verbunden“ oder „Gerät getrennt“ lautet. Als Aktion wird die gespeicherte Prozedur in Ihrer Datenbank ausgeführt.

1. Wählen Sie **+ Neuer Schritt** und dann **Integriert** aus. Anschließend suchen Sie **Bedingung** und wählen dies aus. Klicken Sie auf **Wert auswählen**, woraufhin ein Feld mit dem dynamischen Inhalt – den auswählbaren Feldern – geöffnet wird. Füllen Sie die Felder wie unten gezeigt aus, damit bei den Ereignissen „Gerät verbunden“ und „Gerät getrennt“ nur dies ausgeführt wird:

   * Wert auswählen: **eventType** – Wählen Sie dies aus den Feldern des dynamischen Inhalts aus, die angezeigt werden, wenn Sie auf dieses Feld klicken.
   * Ändern Sie „ist gleich“ in **Endet auf**.
   * Wert auswählen: **nected**.

     ![Ausfüllen der Bedingung](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

2. Klicken Sie im Dialogfeld **Bei TRUE** auf **Aktion hinzufügen**.
  
   ![Hinzufügen einer Aktion wenn TRUE](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

3. Suchen Sie nach Cosmos DB, und wählen Sie **Azure Cosmos DB – Gespeicherte Prozedur ausführen** aus.

   ![Suchen nach Cosmos DB](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

4. Geben Sie **cosmosdb-connection** für den **Verbindungsnamen** ein, wählen Sie den Eintrag in der Tabelle und dann **Erstellen** aus. Es wird der Bereich **Ausführen einer gespeicherten Prozedur** angezeigt. Geben Sie die Werte für die Felder ein:

   **Datenbank-ID**: ToDoList

   **Sammlungs-ID**: Items

   **Sproc-ID**: LatestDeviceConnectionState

5. Wählen Sie **Neuen Parameter hinzufügen** aus. Aktivieren Sie in der daraufhin angezeigten Dropdownliste die Kontrollkästchen neben **Partitionsschlüssel** und **Parameter für die gespeicherte Prozedur**, und klicken Sie dann an eine andere Stelle auf dem Bildschirm. Es wird ein Feld für den Partitionsschlüsselwert und ein Feld für Parameter für die gespeicherte Prozedur hinzugefügt.

   ![Ausfüllen einer Logik-App-Aktion](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Geben Sie jetzt den Partitionsschlüsselwert und die Parameter wie unten dargestellt ein. Vergewissern Sie sich, dass Sie die Klammern und doppelten Anführungszeichen wie abgebildet einfügen. Möglicherweise müssen Sie auf **Dynamischen Inhalt hinzufügen** klicken, um die gültigen Werte zu erhalten, die Sie hier verwenden können.

   ![Ausfüllen einer Logik-App-Aktion](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure-2.png)

7. Stellen Sie am oberen Rand des Fensters, wo **For Each** steht, unter **Ausgabe von vorherigen Schritten auswählen** sicher, dass **Text** ausgewählt ist.

   ![Ausfüllen einer For-Each-Logik-App](./media/iot-hub-how-to-order-connection-state-events/logicapp-foreach-body.png)

8. Speichern Sie Ihre Logik-App.

### <a name="copy-the-http-url"></a>Kopieren der HTTP-URL

Bevor Sie den Logik-App-Designer verlassen, kopieren Sie die URL, auf der Ihre Logik-App auf einen Trigger lauscht. Verwenden Sie diese URL zum Konfigurieren von Event Grid.

1. Erweitern Sie das Triggerkonfigurationsfeld **Beim Empfang einer HTTP-Anforderung**, indem Sie darauf klicken.

2. Kopieren Sie den Wert **HTTP POST-URL** durch Auswählen der Kopierschaltfläche daneben.

   ![Kopieren der HTTP POST-URL](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Speichern Sie diese URL, damit Sie sie im nächsten Abschnitt verwenden können.

## <a name="configure-subscription-for-iot-hub-events"></a>Konfigurieren des Abonnements für IoT Hub-Ereignisse

In diesem Abschnitt konfigurieren Sie Ihren IoT Hub zum Veröffentlichen von Ereignissen, sobald diese auftreten.

1. Navigieren Sie im Azure-Portal zu Ihrem IoT Hub.

2. Wählen Sie **Ereignisse** aus.

   ![Öffnen der Details zu Event Grid](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Wählen Sie **+ Ereignisabonnement** aus.

   ![Erstellen eines neuen Ereignisabonnements](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Füllen Sie **Details zum Ereignisabonnement** aus: Geben Sie einen aussagekräftigen Namen ein, und wählen Sie **Event Grid-Schema** aus.

5. Füllen Sie die **Ereignistypen**-Felder aus. Deaktivieren Sie **Alle Ereignistypen abonnieren**, und wählen Sie im Menü die Optionen **Gerät verbunden** und **Gerät getrennt** aus.

   ![Festlegen von zu suchenden Ereignistypen](./media/iot-hub-how-to-order-connection-state-events/set-event-types.png)

6. Wählen Sie für **Endpunktdetails** als Endpunkttyp die Option **Webhook** aus, klicken Sie auf „Endpunkt auswählen“, fügen Sie die URL ein, die Sie aus Ihrer Logik-App kopiert haben, und bestätigen Sie die Auswahl.

   ![Auswählen der Endpunkt-URL](./media/iot-hub-how-to-order-connection-state-events/endpoint-url.png)

7. Das Formular sollte in etwa wie im folgenden Beispiel aussehen:

   ![Beispielformular für ein Ereignisabonnement](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

   Wählen Sie **Erstellen** aus, um das Ereignisabonnement zu speichern.

## <a name="observe-events"></a>Überwachen von Ereignissen

Nachdem Ihr Ereignisabonnement eingerichtet wurde, testen Sie es, indem Sie ein Gerät verbinden.

### <a name="register-a-device-in-iot-hub"></a>Registrieren eines Geräts bei IoT Hub

1. Wählen Sie von Ihrem IoT Hub aus **IoT-Geräte** aus.

2. Wählen Sie oben im Bereich **+ Hinzufügen** aus.

3. Geben Sie für **Geräte-ID** die Zeichenfolge `Demo-Device-1` ein.

4. Wählen Sie **Speichern** aus.

5. Sie können mehrere Geräte mit unterschiedlichen Geräte-IDs hinzufügen.

   ![Zum Hub hinzugefügte Geräte](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Klicken Sie erneut auf das Gerät. Jetzt werden die Verbindungszeichenfolgen und Schlüssel ausgefüllt. Kopieren Sie **Verbindungszeichenfolge – Primärschlüssel** zur späteren Verwendung.

   ![Verbindungszeichenfolge für Gerät](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

HostName=test-eventgrid-hub.azure-devices.net;DeviceId=Demo-Device-1;SharedAccessKey=cv8uPNixe7E2R9EHtimoY/PlJfBV/lOYCMajVOp/Cuw=

### <a name="start-raspberry-pi-simulator"></a>Starten des Raspberry Pi-Simulators

1. Wir verwenden den Raspberry Pi-Websimulator, um eine Geräteverbindung zu simulieren.

[Raspberry Pi-Simulator starten](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Ausführen einer Beispielanwendung im Raspberry Pi-Websimulator

Dadurch wird ein Ereignis „Gerät verbunden“ ausgelöst.

1. Ersetzen Sie im Codebereich den Platzhalter in Zeile 15 durch die Verbindungszeichenfolge für das Azure IoT Hub-Gerät, die Sie am Ende des vorherigen Abschnitts gespeichert haben.

   ![Einfügen der Geräte-Verbindungszeichenfolge](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Führen Sie die Anwendung durch Auswählen von **Ausführen** aus.

Es wird eine ähnliche Ausgabe wie die folgende angezeigt, die die Sensordaten und Nachrichten zeigt, die an Ihren IoT Hub gesendet werden.

   ![Ausführen der Anwendung](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Klicken Sie auf **Beenden**, um den Simulator zu beenden und ein Ereignis **Gerät getrennt** auszulösen.

Sie haben damit eine Beispielanwendung ausgeführt, die Sensordaten sammelt und an Ihren IoT Hub sendet.

### <a name="observe-events-in-cosmos-db"></a>Untersuchen der Ereignisse in Cosmos DB

Sie können die Ergebnisse der ausgeführten gespeicherten Prozedur in Ihrem Cosmos DB-Dokument anzeigen. Das Ergebnis sieht so aus: Jede Zeile enthält den letzten Geräteverbindungsstatus pro Gerät.

   ![Ergebnis](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Verwenden der Azure-CLI

Anstelle des [Azure-Portals](https://portal.azure.com) können Sie die IoT Hub-Schritte auch mithilfe der Azure-Befehlszeilenschnittstelle erreichen. Einzelheiten dazu finden Sie in den Azure CLI-Seiten zum [Erstellen eines Ereignisabonnements](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription) und [Erstellen eines IoT-Geräts](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity#ext-azure-cli-iot-ext-az-iot-hub-device-identity-create).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In diesem Tutorial werden Ressourcen verwendet, für die Gebühren in Ihrem Azure-Abonnement anfallen. Wenn Sie mit dem Ausprobieren des Tutorials und Testen Ihrer Ergebnisse fertig sind, deaktivieren oder löschen Sie die Ressourcen, die nicht beibehalten werden sollen.

Wenn Sie die Arbeit an Ihrer Logik-App nicht verlieren möchten, deaktivieren Sie sie, anstatt sie zu löschen.

1. Navigieren Sie zu Ihrer Logik-App.

2. Wählen Sie auf dem Blatt **Übersicht** eine der Optionen **Löschen** oder **Deaktivieren** aus.

    Jedes Abonnement kann über einen kostenlosen IoT Hub verfügen. Wenn Sie für dieses Tutorial einen kostenlosen Hub erstellt haben, müssen Sie ihn nicht löschen, um Gebühren zu vermeiden.

3. Navigieren Sie zu Ihrem IoT Hub.

4. Wählen Sie auf dem Blatt **Übersicht** die Option **Löschen** aus.

    Auch wenn Sie Ihren IoT Hub behalten, sollten Sie das Ereignisabonnement, das Sie erstellt haben, löschen.

5. Wählen Sie in Ihren IoT Hub die Option **Event Grid** aus.

6. Wählen Sie das Ereignisabonnement aus, das Sie entfernen möchten.

7. Klicken Sie auf **Löschen**.

Wenn Sie ein Azure Cosmos DB-Konto aus dem Azure-Portal entfernen möchten, klicken Sie mit der rechten Maustaste auf den Kontonamen, und klicken Sie anschließend auf **Konto löschen**. Ausführliche Anweisungen finden Sie unter [Löschen eines Azure Cosmos DB-Kontos](https://docs.microsoft.com/azure/cosmos-db/manage-account).

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über das [Reagieren auf IoT Hub-Ereignisse mithilfe von Event Grid zum Auslösen von Aktionen](../iot-hub/iot-hub-event-grid.md).

* [Ausprobieren des Tutorials zu IoT Hub-Ereignissen](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* Erfahren Sie mehr über die Möglichkeiten mit [Event Grid](../event-grid/overview.md).