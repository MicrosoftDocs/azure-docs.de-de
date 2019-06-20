---
title: Anzeigen von Ergebnissen des Azure IoT Hub-Nachrichtenroutings (.NET) | Microsoft-Dokumentation
description: Anzeigen von Ergebnissen des Azure IoT Hub-Nachrichtenroutings
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: 1417ecdaf6a85f491e1accfb9564e27d15e13445
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66162806"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>Tutorial: Teil 2: Anzeigen der weitergeleiteten Nachrichten

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rules-for-routing-the-messages"></a>Regeln für das Routing der Nachrichten

Dies sind die Regeln für das Nachrichtenrouting. Sie wurden in Teil 1 dieses Tutorials eingerichtet, und im zweiten Teil erfahren Sie, wie sie funktionieren.

|value |Ergebnis|
|------|------|
|level="storage" |Schreibvorgang in Azure Storage.|
|level="critical" |Schreibvorgang in eine Service Bus-Warteschlange. Eine Logik-App ruft die Nachricht aus der Warteschlange ab und sendet sie mithilfe von Office 365.|
|die Standardeinstellung |Anzeigen dieser Daten mithilfe von Power BI.|

Sie erstellen jetzt die Ressourcen, an die die Nachrichten geleitet werden, führen eine App zum Senden der Nachrichten an den Hub aus, und erleben das Routing in Aktion.

## <a name="create-a-logic-app"></a>Erstellen einer Logik-App  

Die Service Bus-Warteschlange sollte zum Empfangen von als kritisch eingestuften Nachrichten verwendet werden. Sie richten eine Logik-App ein, um die Service Bus-Warteschlange zu überwachen und eine E-Mail zu senden, wenn der Warteschlange eine Nachricht hinzugefügt wird.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **+ Ressource erstellen**. Geben Sie **Logik-App** in das Suchfeld ein, und drücken Sie die EINGABETASTE. Wählen Sie aus den angezeigten Suchergebnissen „Logik-App“ aus, und wählen Sie dann **Erstellen**, um zum Bereich **Logik-App erstellen** zu gelangen. Füllen Sie die Felder aus.

   **Name**: Dieses Feld enthält den Namen der Logik-App. In diesem Tutorial wird **ContosoLogicApp** verwendet.

   **Abonnement**: Wählen Sie Ihr Azure-Abonnement.

   **Ressourcengruppe**: Wählen Sie **Vorhandene verwenden** und dann Ihre Ressourcengruppe aus. In diesem Tutorial wird **ContosoResources** verwendet.

   **Standort**: Verwenden Sie Ihren Standort. In diesem Tutorial wird **USA, Westen** verwendet.

   **Log Analytics**: Diese Option sollte deaktiviert werden.

   ![Bildschirm „Logik-App erstellen“](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   Klicken Sie auf **Erstellen**.

2. Wechseln Sie jetzt zur Logik-App. Am einfachsten wechseln Sie zur Logik-App, indem Sie die Option **Ressourcengruppen** und Ihre Ressourcengruppe (in diesem Tutorial wird **ContosoResources** verwendet) und dann in der Liste mit den Ressourcen die Logik-App auswählen. Die Seite „Designer für Logik-Apps“ wird angezeigt (möglicherweise müssen Sie nach rechts scrollen, um die ganze Seite zu sehen). Scrollen Sie auf der Seite „Designer für Logik-Apps“ nach unten, bis die Kachel **Leere Logik-App +** angezeigt wird. Wählen Sie sie aus. Die Standardregisterkarte ist „Für Sie“. Wählen Sie die Option **Alle**, wenn dieser Bereich leer ist, um alle verfügbaren Connectors und Trigger anzuzeigen.

3. Wählen in der Liste mit den Connectors den Eintrag **Service Bus** aus.

   ![Liste mit Connectors](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. Eine Liste von Triggern wird angezeigt. Wählen Sie **Wenn eine Nachricht in einer Warteschlange empfangen wird (automatisch abschließen)/Service Bus** aus.

   ![Liste mit Triggern für Service Bus](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. Geben Sie im nächsten Bildschirm den Namen für die Verbindung ein. In diesem Tutorial wird **ContosoConnection** verwendet.

   ![Einrichten der Verbindung für die Service Bus-Warteschlange](./media/tutorial-routing-view-message-routing-results/logic-app-define-connection.png)

   Wählen Sie den Service Bus-Namespace aus. In diesem Tutorial wird **ContosoSBNamespace** verwendet. Wenn Sie den Namespace auswählen, fragt das Portal den Service Bus-Namespace ab, um die Schlüssel zu erhalten. Wählen Sie **RootManageSharedAccessKey** und dann **Erstellen** aus.

   ![Fertigstellen der Verbindungseinrichtung](./media/tutorial-routing-view-message-routing-results/logic-app-finish-connection.png)

6. Wählen Sie im nächsten Bildschirm aus der Dropdownliste den Namen der Warteschlange (in diesem Tutorial wird **contososbqueue** verwendet). Für die restlichen Felder können Sie die Standardwerte verwenden.

   ![Warteschlangenoptionen](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

7. Richten jetzt die Aktion ein, die eine E-Mail sendet, wenn in der Warteschlange eine Nachricht empfangen wird. Wählen Sie im Designer für Logik-Apps die Option **+ Neuer Schritt**, um einen Schritt hinzuzufügen, und dann **Alle**, um alle verfügbaren Optionen anzuzeigen. Suchen Sie im Bereich **Aktion auswählen** nach **Office 365 Outlook**, und wählen Sie diese Option aus. Wählen Sie im Bildschirm mit den Triggern die Option **E-Mail senden/Office 365 Outlook** aus.  

   ![Office 365-Optionen](./media/tutorial-routing-view-message-routing-results/logic-app-select-outlook.png)

8. Melden Sie sich an Ihrem Office 365-Konto an, um die Verbindung einzurichten. Falls ein Timeout eintritt, können Sie es einfach erneut versuchen. Geben Sie die E-Mail-Adressen für die E-Mail-Empfänger ein. Geben Sie auch einen Betreff an sowie eine Nachricht ein, die den Empfängern im Nachrichtentext angezeigt werden soll. Geben Sie zu Testzwecken Ihre eigene E-Mail-Adresse als Empfänger ein.

   Wählen Sie **Dynamischen Inhalt hinzufügen**, um Inhalte aus der Nachricht anzuzeigen, die Sie hinzufügen können. Wählen Sie **Inhalt** aus – damit wird die Nachricht in die E-Mail einbezogen.

   ![E-Mail-Optionen für die Logik-App](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png)

9. Wählen Sie **Speichern** aus. Schließen Sie dann den Logik-App-Designer.

## <a name="set-up-azure-stream-analytics"></a>Einrichten von Azure Stream Analytics

Um die Daten in einer Power BI-Visualisierung anzuzeigen, richten Sie zuerst einen Stream Analytics-Auftrag zum Abrufen der Daten ein. Denken Sie daran, dass nur Nachrichten mit „**level** = **normal**“ an den Standardendpunkt gesendet und vom Stream Analytics-Auftrag für die Power BI-Visualisierung abgerufen werden.

### <a name="create-the-stream-analytics-job"></a>Erstellen des Stream Analytics-Auftrags

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Ressource erstellen** > **Internet der Dinge** > **Stream Analytics-Auftrag**.

2. Geben Sie die folgenden Informationen für den Auftrag ein.

   **Auftragsname**: Der Name des Auftrags. Der Name muss global eindeutig sein. In diesem Tutorial wird **contosoJob** verwendet.

   **Abonnement**: Azure-Abonnement, das Sie für das Tutorial verwenden.

   **Ressourcengruppe**: Verwenden Sie dieselbe Ressourcengruppe wie für Ihre IoT Hub-Instanz. In diesem Tutorial wird **ContosoResources** verwendet.

   **Standort**: Verwenden Sie den gleichen Standort, der im Einrichtungsskript verwendet wurde. In diesem Tutorial wird **USA, Westen** verwendet.

   ![Erstellen des Stream Analytics-Auftrags](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. Klicken Sie auf **Erstellen**, um den Auftrag zu erstellen. Wählen Sie **Ressourcengruppen**, um wieder zum Auftrag zu gelangen. In diesem Tutorial wird **ContosoResources** verwendet. Wählen Sie die Ressourcengruppe und anschließend in der Ressourcenliste den Stream Analytics-Auftrag aus.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Hinzufügen einer Eingabe zum Stream Analytics-Auftrag

4. Wählen Sie unter **Auftragstopologie** die Option **Eingaben** aus.

5. Wählen Sie im Bereich **Eingaben** die Option **Datenstromeingabe hinzufügen** und dann „IoT Hub“ aus. Füllen Sie im daraufhin angezeigten Bildschirm die folgenden Felder aus:

   **Eingabealias**: In diesem Tutorial wird **contosoinputs** verwendet.

   **IoT Hub aus Ihrem Abonnement auswählen**: Wählen Sie dieses Optionsfeld aus.

   **Abonnement**: Wählen Sie das Azure-Abonnement aus, das Sie für dieses Tutorial verwenden.

   **IoT Hub**: Wählen Sie die IoT Hub-Instanz aus. In diesem Tutorial wird **ContosoTestHub** verwendet.

   **Endpunkt**: Wählen Sie **Messaging** aus. (Wenn Sie die Vorgangsüberwachung auswählen, erhalten Sie nicht die Daten, die Sie senden, sondern die Telemetriedaten zu Ihrer IoT Hub-Instanz.) 

   **Name der SAS-Richtlinie**: Wählen Sie **iothubowner** aus. Das Portal füllt den Schlüssel für die Richtlinie für den gemeinsamen Zugriff für Sie aus.

   **Consumergruppe**: Wählen Sie die Consumergruppe aus, die Sie in Schritt 1 dieses Tutorials eingerichtet haben. In diesem Tutorial wird **contosoconsumers** verwendet.
   
   Übernehmen Sie in den restlichen Feldern die Standardwerte. 

   ![Einrichten der Eingaben für den Stream Analytics-Auftrag](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

6. Wählen Sie **Speichern** aus.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Hinzufügen einer Ausgabe zum Stream Analytics-Auftrag

1. Wählen Sie unter **Auftragstopologie** die Option **Ausgaben** aus.

2. Wählen Sie im Bereich **Ausgaben** die Option **Hinzufügen** und dann **Power BI** aus. Füllen Sie im daraufhin angezeigten Bildschirm die folgenden Felder aus:

   **Ausgabealias**: Der eindeutige Alias für die Ausgabe. In diesem Tutorial wird **contosooutputs** verwendet. 

   **Datasetname**: Name des Datasets, das in Power BI verwendet werden soll. In diesem Tutorial wird **contosodataset** verwendet. 

   **Tabellenname**: Name der Tabelle, die in Power BI verwendet werden soll. In diesem Tutorial wird **contosotable** verwendet.

   Übernehmen Sie in den restlichen Feldern die Standardwerte.

3. Wählen Sie **Autorisieren**, und melden Sie sich an Ihrem Power BI-Konto an. (Hierfür benötigen Sie ggf. mehr als einen Versuch.)

   ![Einrichten der Ausgaben für den Stream Analytics-Auftrag](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. Wählen Sie **Speichern** aus.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurieren der Abfrage des Stream Analytics-Auftrags

1. Wählen Sie unter **Auftragstopologie** die Option **Abfrage** aus.

2. Ersetzen Sie `[YourInputAlias]` durch den Eingabealias des Auftrags. In diesem Tutorial wird **contosoinputs** verwendet.

3. Ersetzen Sie `[YourOutputAlias]` durch den Ausgabealias des Auftrags. In diesem Tutorial wird **contosooutputs** verwendet.

   ![Einrichten der Abfrage für den Stream Analytics-Auftrag](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. Wählen Sie **Speichern** aus.

5. Schließen Sie den Abfragebereich. Sie gelangen wieder zur Ansicht mit den Ressourcen in der Ressourcengruppe. Wählen Sie den Stream Analytics-Auftrag aus. In diesem Tutorial hat er den Namen **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Ausführen des Stream Analytics-Auftrags

Wählen Sie im Stream Analytics-Auftrag **Starten** > **Jetzt** > **Starten**. Sobald der Auftrag erfolgreich gestartet wurde, ändert sich der Status des Auftrags von **Beendet** in **Wird ausgeführt**.

Um den Power BI-Bericht einzurichten, benötigen Sie Daten. Richten Sie Power BI daher erst ein, wenn Sie das Gerät erstellt haben und die Gerätesimulationsanwendung ausgeführt wird.

## <a name="run-simulated-device-app"></a>Ausführen der App zur Simulation von Geräten

In Teil 1 dieses Tutorials haben Sie ein Gerät für eine Simulation mit einem IoT-Gerät eingerichtet. In diesem Abschnitt laden Sie die .NET-Konsolen-App herunter, mit der ein Gerät zum Senden von Gerät-zu-Cloud-Nachrichten an einen IoT-Hub simuliert wird. Hierbei wird vorausgesetzt, dass Sie die App und die Ressourcen nicht bereits in Teil 1 dieses Tutorials heruntergeladen haben.

Mit dieser Anwendung werden Nachrichten unter Verwendung aller unterschiedlichen Nachrichtenroutingmethoden gesendet. Der Download beinhaltet auch einen Ordner, der die vollständige Azure Resource Manager-Vorlage und die Parameterdatei sowie die Azure CLI- und PowerShell-Skripts enthält.

Wenn Sie die Dateien in Schritt 1 dieses Tutorials nicht aus dem Repository heruntergeladen haben, können Sie den Download jetzt unter [IoT Device Simulation](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) (IoT-Gerätesimulation) durchführen. Bei Auswahl dieses Links wird ein Repository mit mehreren Anwendungen heruntergeladen. Die von Ihnen benötigte Lösung ist „iot-hub/Tutorials/Routing/IoT_SimulatedDevice.sln“. 

Doppelklicken Sie auf die Lösungsdatei (IoT_SimulatedDevice.sln), um den Code in Visual Studio zu öffnen. Öffnen Sie anschließend „Program.cs“. Ersetzen Sie `{iot hub hostname}` durch den IoT Hub-Hostnamen. Das Format des IoT Hub-Hostnamens ist **{iot-hub-name}.azure-devices.net**. Für dieses Tutorial lautet der Hubhostname **ContosoTestHub.azure-devices.net**. Ersetzen Sie jetzt `{device key}` durch den Geräteschlüssel, den Sie zuvor beim Einrichten des simulierten Geräts gespeichert haben. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Ausführen und Testen

Führen Sie die Konsolenanwendung aus. Warten Sie ein paar Minuten. Sie sehen die Nachrichten, die an den Konsolenbildschirm der Anwendung gesendet werden.

Die App sendet jede Sekunde eine neue Gerät-zu-Cloud-Nachricht an die IoT Hub-Instanz. Die Nachricht enthält ein JSON-serialisiertes Objekt mit Geräte-ID, Temperatur und Luftfeuchtigkeit sowie der Nachrichtenebene, die standardmäßig `normal` lautet. Nach dem Zufallsprinzip wird die Ebene `critical` oder `storage` zugewiesen, sodass die Nachricht an das Speicherkonto oder die Service Bus-Warteschlange weitergeleitet wird (diese löst das Senden einer E-Mail durch Ihre Logik-App aus). Die Ablesewerte für die Standardeinstellung (`normal`) werden in dem BI-Bericht angezeigt, den Sie als Nächstes einrichten.

Wenn alles ordnungsgemäß eingerichtet wurde, sollten Sie jetzt die folgenden Ergebnisse sehen:

1. Ab jetzt erhalten Sie E-Mails mit kritischen Nachrichten.

   ![Resultierende E-Mails](./media/tutorial-routing-view-message-routing-results/results-in-email.png)

   Dieses Ergebnis bedeutet, dass die folgenden Anweisungen wahr (true) sind. 

   * Die Weiterleitung an die Service Bus-Warteschlange funktioniert ordnungsgemäß.
   * Die Logik-App, die die Nachricht aus der Service Bus-Warteschlange abruft, funktioniert ordnungsgemäß.
   * Der Logik-App-Connector zu Outlook funktioniert ordnungsgemäß. 

2. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Ressourcengruppen** und dann Ihre Ressourcengruppe aus. In diesem Tutorial wird **ContosoResources** verwendet. Wählen Sie das Speicherkonto, die Option **Blobs** und dann den Container aus. In diesem Tutorial wird **contosoresults** verwendet. Es wird ein Ordner angezeigt, und Sie können ein Drilldown durch die Verzeichnisse ausführen, bis Dateien angezeigt werden. Öffnen Sie eine dieser Dateien. Sie enthalten die Einträge, die an das Speicherkonto weitergeleitet wurden. 

   ![Ergebnisdateien im Speicher](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

Dieses Ergebnis bedeutet, dass die folgende Anweisung wahr (true) ist.

   * Die Weiterleitung an das Speicherkonto funktioniert ordnungsgemäß.

Richten Sie die Power BI-Visualisierung ein, während die Anwendung weiter ausgeführt wird, um die Nachrichten anzuzeigen, die per Standardrouting weitergeleitet werden.

## <a name="set-up-the-power-bi-visualizations"></a>Einrichten der Power BI-Visualisierungen

1. Melden Sie sich bei Ihrem [Power BI](https://powerbi.microsoft.com/)-Konto an.

2. Wechseln Sie zum **Arbeitsbereiche**, und wählen Sie den Arbeitsbereich aus, den Sie festgelegt haben, als Sie die Ausgabe für den Stream Analytics-Auftrag erstellt haben. In diesem Tutorial wird **My Workspace** verwendet. 

3. Wählen Sie die Option **Datasets**. Wenn Sie keine Datasets haben, können Sie einige Minuten warten und dann erneut nachsehen.

   Es sollte das Dataset aufgelistet sein, das Sie beim Erstellen der Ausgabe für den Stream Analytics-Auftrag angegeben haben. In diesem Tutorial wird **contosodataset** verwendet. (Es kann 5-10 Minuten dauern, bis das Dataset zum ersten Mal angezeigt wird.)

4. Wählen Sie unter **AKTIONEN** das erste Symbol aus, um einen Bericht zu erstellen.

   ![Power BI-Arbeitsbereich mit Option „Aktionen“ und Berichtssymbol](./media/tutorial-routing-view-message-routing-results/power-bi-actions.png)

5. Erstellen Sie ein Liniendiagramm, um die Temperatur in Echtzeit im Zeitverlauf anzuzeigen.

   * Fügen Sie auf der Seite zum Erstellen von Berichten ein Liniendiagramm hinzu, indem Sie das Symbol für Liniendiagramme auswählen.

     ![Visualisierungen und Felder](./media/tutorial-routing-view-message-routing-results/power-bi-visualizations-and-fields.png)

   * Erweitern Sie im Bereich **Felder** die Tabelle, die Sie beim Erstellen der Ausgabe für den Stream Analytics-Auftrag angegeben haben. In diesem Tutorial wird **contosotable** verwendet.

   * Ziehen Sie **EventEnqueuedUtcTime** im Bereich **Visualisierungen** auf **Achse**.

   * Ziehen Sie **Temperatur** auf **Werte**.

   Ein Liniendiagramm wird erstellt. Die x-Achse zeigt Datum und Uhrzeit in der Zeitzone UTC. Die y-Achse zeigt vom Sensor empfangene Temperatur an.

6. Erstellen Sie ein weiteres Liniendiagramm, um die Luftfeuchtigkeit in Echtzeit im Zeitverlauf anzuzeigen. Um das zweite Diagramm zu erstellen, gehen Sie wie oben beschrieben vor. Platzieren Sie **EventEnqueuedUtcTime** auf der X-Achse und **humidity** auf der Y-Achse.

   ![Endgültiger Power BI-Bericht mit den beiden Diagrammen](./media/tutorial-routing-view-message-routing-results/power-bi-report.png)

7. Wählen Sie **Speichern**, um den Bericht zu speichern.

In beiden Diagrammen sollten Daten angezeigt werden. Dieses Ergebnis bedeutet, dass die folgenden Anweisungen wahr (true) sind:

   * Die Weiterleitung an den Standardendpunkt funktioniert ordnungsgemäß.
   * Das Streaming des Azure Stream Analytics-Auftrags funktioniert ordnungsgemäß.
   * Die Power BI-Visualisierung ist ordnungsgemäß eingerichtet.

Sie können die Diagramme aktualisieren, um die neuesten Daten anzuzeigen. Wählen Sie zu diesem Zweck oben im Power BI-Fenster die Schaltfläche „Aktualisieren“ aus. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen 

Löschen Sie die Ressourcengruppe, falls Sie alle Ressourcen entfernen möchten, die Sie in den beiden Teilen dieses Tutorials erstellt haben. Diese Aktion löscht alle in der Gruppe enthaltenen Ressourcen. In diesem Fall werden folgende Elemente entfernt: die IoT Hub-Instanz, der Service Bus-Namespace und die Service Bus-Warteschlange, die Logik-App, das Speicherkonto und die Ressourcengruppe selbst. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Bereinigen von Ressourcen in der Power BI-Visualisierung

Melden Sie sich bei Ihrem [Power BI](https://powerbi.microsoft.com/)-Konto an. Wechseln Sie zu Ihrem Arbeitsbereich. In diesem Tutorial wird **My Workspace** verwendet. Um die Power BI-Visualisierung zu entfernen, wechseln Sie zu „Datasets“ und wählen das Papierkorbsymbol, um das Dataset zu löschen. In diesem Tutorial wird **contosodataset** verwendet. Wenn Sie das Dataset entfernen, wird auch der Bericht entfernt.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Verwenden der Azure CLI zum Bereinigen von Ressourcen

Um die Ressourcengruppe zu entfernen, verwenden Sie den Befehl [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup` wurde zu Beginn dieses Tutorials auf **ContosoResources** festgelegt.

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>Verwenden von PowerShell zum Bereinigen von Ressourcen

Verwenden Sie zum Entfernen der Ressourcengruppe den Befehl [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup). `$resourceGroup` wurde zu Beginn dieses Tutorials auf **ContosoResources** festgelegt.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In diesem zweiteiligen Tutorial haben Sie erfahren, wie Sie die Nachrichtenweiterleitung verwenden, um IoT Hub-Nachrichten an verschiedene Ziele weiterzuleiten. Hierfür haben Sie folgende Aufgaben durchgeführt.  

**Teil 1: Erstellen von Ressourcen, Einrichten des Nachrichtenroutings**
> [!div class="checklist"]
> * Sie erstellen die Ressourcen: einen IoT-Hub, ein Speicherkonto, eine Service Bus-Warteschlange und ein simuliertes Gerät.
> * Sie konfigurieren die Endpunkte und Nachrichtenrouten in IoT Hub für das Speicherkonto und die Service Bus-Warteschlange.

**Teil 2: Senden von Nachrichten an den Hub, Anzeigen von Routingergebnissen**
> [!div class="checklist"]
> * Sie erstellen eine Logik-App, die ausgelöst wird und eine E-Mail sendet, wenn der Service Bus-Warteschlange eine Nachricht hinzugefügt wird.
> * Sie laden eine App herunter und führen sie aus, die ein IoT-Gerät simuliert, das für die verschiedenen Weiterleitungsoptionen Nachrichten an den Hub sendet.
> * Sie erstellen eine Power BI-Visualisierung für die Daten, die an den Standardendpunkt gesendet werden.
> * Sie zeigen die Ergebnisse an:
> * – in der Service Bus-Warteschlange und in E-Mails
> * – im Speicherkonto
> * – in der Power BI-Visualisierung

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie den Status eines IoT-Geräts verwalten. 

> [!div class="nextstepaction"]
> [Tutorial: Set up and use metrics and diagnostic logs with an IoT hub](tutorial-use-metrics-and-diags.md) (Einrichten und Verwenden von Metriken und Diagnoseprotokollen mit einem IoT-Hub)
