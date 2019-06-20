---
title: Visualisieren von Datenanomalien in Echtzeitereignissen – Azure Event Hubs | Microsoft-Dokumentation
description: Tutorial – Visualisieren von Datenanomalien in Echtzeitereignissen, die an Microsoft Azure Event Hubs gesendet werden
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.author: shvija
ms.topic: tutorial
ms.service: event-hubs
ms.custom: seodec18
ms.date: 02/26/2019
ms.openlocfilehash: d6786e4e3382c7c4d7a6a6a28c3cd3621df221c1
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64867135"
---
# <a name="tutorial-visualize-data-anomalies-in-real-time-events-sent-to-azure-event-hubs"></a>Tutorial: Visualisieren von Datenanomalien in Echtzeitereignissen, die an Azure Event Hubs gesendet werden

Mit Azure Event Hubs können Sie Azure Stream Analytics verwenden, um die eingehenden Daten zu überprüfen und die Anomalien zu extrahieren, die Sie dann in Power BI visualisieren können. Angenommen, Sie verfügen über Tausende von Geräten, die ständig Echtzeitdaten mit Millionen von Ereignissen pro Sekunde an einen Event Hub senden. Wie können Sie eine so große Datenmenge auf Anomalien bzw. Fehler in den Daten überprüfen? Was passiert beispielsweise, wenn die Geräte Kreditkartentransaktionen senden und Sie innerhalb eines Zeitintervalls von fünf Sekunden Daten von Orten in mehreren Ländern/Regionen erfassen müssen, an denen Transaktionen erfolgen? Dies kann der Fall sein, wenn eine Person Kreditkarten stiehlt und dann nutzt, um weltweit gleichzeitig Artikel einzukaufen. 

In diesem Tutorial simulieren Sie dieses Beispiel. Sie führen eine Anwendung aus, mit der Kreditkartentransaktionen erstellt und an einen Event Hub gesendet werden. Anschließend lesen Sie den Datenstrom in Echtzeit mit Azure Stream Analytics, wobei die gültigen Transaktionen von den ungültigen Transaktionen getrennt werden, und verwenden anschließend Power BI, um die als ungültig gekennzeichneten Transaktionen visuell zu identifizieren.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Erstellen eines Event Hubs-Namespace
> * Erstellen eines Ereignis-Hubs
> * Ausführen der App zum Senden der Kreditkartentransaktionen
> * Konfigurieren eines Stream Analytics-Auftrags zum Verarbeiten dieser Transaktionen
> * Konfigurieren einer Power BI-Visualisierung zum Anzeigen der Ergebnisse

Sie benötigen ein Azure-Abonnement, um dieses Tutorial durcharbeiten zu können. Falls Sie kein Abonnement besitzen, können Sie ein [kostenloses Konto erstellen][], bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

- Installieren Sie [Visual Studio](https://www.visualstudio.com/). 
- Sie benötigen ein Power BI-Konto zum Analysieren der Ausgabe eines Stream Analytics-Auftrags. Sie können [Power BI kostenlos testen](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="set-up-resources"></a>Einrichten von Ressourcen

Für dieses Tutorial benötigen Sie einen Event Hubs-Namespace und einen Event Hub. Sie können diese Ressourcen über die Azure-Befehlszeilenschnittstelle oder mit Azure PowerShell erstellen. Verwenden Sie für alle Ressourcen die gleiche Ressourcengruppe und den gleichen Speicherort. Am Ende dieses Tutorials können Sie dann sämtliche Elemente in einem Schritt entfernen, indem Sie die Ressourcengruppe löschen.

In den folgenden Abschnitten wird beschrieben, wie Sie diese erforderlichen Schritte ausführen. Befolgen Sie die Anleitung für die CLI *oder* für PowerShell, um die folgenden Schritte auszuführen:

1. Erstellen Sie eine [Ressourcengruppe](../azure-resource-manager/resource-group-overview.md). 

2. Erstellen eines Event Hubs-Namespace 

3. Erstellen einer Event Hub-Instanz

> [!NOTE]
> In jedem Skript sind Variablen festgelegt, die Sie später in diesem Tutorial benötigen. Hierzu gehören der Ressourcengruppenname ($resourceGroup), der Event Hub-Namespace ( **$eventHubNamespace**) und der Event Hub-Name ( **$eventHubName**). Hierauf wird später in diesem Artikel über die Dollarzeichen-Präfixe ($) verwiesen, damit Sie wissen, dass sie im Skript festgelegt wurden.

<!-- some day they will approve the tab control; 
  When that happens, put CLI and PSH in tabs. -->

### <a name="set-up-your-resources-using-azure-cli"></a>Einrichten Ihrer Ressourcen per Azure CLI

Kopieren Sie dieses Skript, und fügen Sie es in Cloud Shell ein. Sofern Sie bereits angemeldet sind, wird das Skript Zeile für Zeile ausgeführt.

Die Variablen, die global eindeutig sein müssen, verfügen über den Zusatz `$RANDOM`. Wenn das Skript ausgeführt und die Variablen festgelegt werden, wird eine zufällige numerische Zeichenfolge generiert und am Ende der feststehenden Zeichenfolge angefügt, um sie eindeutig zu machen.

```azurecli-interactive
# Set the values for location and resource group name.
location=westus
resourceGroup=ContosoResourcesEH

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The Event Hubs namespace name must be globally unique, so add a random number to the end.
eventHubNamespace=ContosoEHNamespace$RANDOM
echo "Event Hub Namespace = " $eventHubNamespace

# Create the Event Hubs namespace.
az eventhubs namespace create --resource-group $resourceGroup \
   --name $eventHubNamespace \
   --location $location \
   --sku Standard

# The event hub name must be globally unique, so add a random number to the end.
eventHubName=ContosoEHhub$RANDOM
echo "event hub name = " $eventHubName

# Create the event hub.
az eventhubs eventhub create --resource-group $resourceGroup \
    --namespace-name $eventHubNamespace \
    --name $eventHubName \
    --message-retention 3 \
    --partition-count 2

# Get the connection string that authenticates the app with the Event Hubs service.
connectionString=$(az eventhubs namespace authorization-rule keys list \
   --resource-group $resourceGroup \
   --namespace-name $eventHubNamespace \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString \
   --output tsv)
echo "Connection string = " $connectionString 
```

### <a name="set-up-your-resources-using-azure-powershell"></a>Einrichten Ihrer Ressourcen per Azure PowerShell

Kopieren Sie dieses Skript, und fügen Sie es in Cloud Shell ein. Sofern Sie bereits angemeldet sind, wird das Skript Zeile für Zeile ausgeführt.

Die Variablen, die global eindeutig sein müssen, verfügen über den Zusatz `$(Get-Random)`. Wenn das Skript ausgeführt und die Variablen festgelegt werden, wird eine zufällige numerische Zeichenfolge generiert und am Ende der feststehenden Zeichenfolge angefügt, um sie eindeutig zu machen.

```azurepowershell-interactive
# Log in to Azure account.
Login-AzAccount

# Set the values for the location and resource group.
$location = "West US"
$resourceGroup = "ContosoResourcesEH"

# Create the resource group to be used  
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The Event Hubs namespace name must be globally unique, so add a random number to the end.
$eventHubNamespace = "contosoEHNamespace$(Get-Random)"
Write-Host "Event Hub Namespace is " $eventHubNamespace

# The event hub name must be globally unique, so add a random number to the end.
$eventHubName = "contosoEHhub$(Get-Random)"
Write-Host "Event hub Name is " $eventHubName

# Create the Event Hubs namespace.
New-AzEventHubNamespace -ResourceGroupName $resourceGroup `
     -NamespaceName $eventHubNamespace `
     -Location $location

# Create the event hub.
$yourEventHub = New-AzEventHub -ResourceGroupName $resourceGroup `
    -NamespaceName $eventHubNamespace `
    -Name $eventHubName `
    -MessageRetentionInDays 3 `
    -PartitionCount 2

# Get the event hub key, and retrieve the connection string from that object.
# You need this to run the app that sends test messages to the event hub.
$eventHubKey = Get-AzEventHubKey -ResourceGroupName $resourceGroup `
    -Namespace $eventHubNamespace `
    -AuthorizationRuleName RootManageSharedAccessKey

# Save this value somewhere local for later use.
Write-Host "Connection string is " $eventHubKey.PrimaryConnectionString
```

## <a name="run-app-to-produce-test-event-data"></a>Ausführen der App zum Produzieren von Testereignisdaten

Die Event Hubs-[Beispiele auf GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet) enthalten auch eine [App für die Anomalieerkennung](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/AnomalyDetector), die Testdaten für Sie produzieren kann. Hiermit wird die Nutzung von Kreditkarten simuliert, indem Kreditkartentransaktionen auf den Event Hub geschrieben werden. Gelegentlich werden auch mehrere Transaktionen für dieselbe Kreditkarte an mehreren Orten geschrieben, damit diese als Anomalien gekennzeichnet werden. Führen Sie diese Schritte aus, um die App auszuführen: 

1. Laden Sie die [Azure Event Hubs-Beispiele](https://github.com/Azure/azure-event-hubs/archive/master.zip) von GitHub herunter, und entzippen Sie sie lokal.

2. Wechseln Sie zum Ordner „\azure-event-hubs-master\samples\DotNet\AnomalyDetector\“, und doppelklicken Sie auf „AnomalyDetector.sln“, um die Projektmappe in Visual Studio zu öffnen. 

3. Öffnen Sie „Program.cs“, und ersetzen Sie die **Event Hubs-Verbindungszeichenfolge** durch die Verbindungszeichenfolge, die Sie beim Ausführen des Skripts gespeichert haben. 

4. Ersetzen Sie den **Event Hub-Namen** durch Ihren Event Hub-Namen. Drücken Sie F5, um die Anwendung auszuführen. Es wird damit begonnen, Ereignisse an Ihren Event Hub zu senden. Dieser Prozess wird fortgesetzt, bis 1.000 Ereignisse gesendet wurden. Es gibt einige Fälle, in denen die App ausgeführt werden muss, damit Sie Daten abrufen können. Diese Fälle sind in den folgenden Anweisungen angegeben (falls erforderlich).

## <a name="set-up-azure-stream-analytics"></a>Einrichten von Azure Stream Analytics

Sie können für Ihre Daten nun das Streamen in Ihren Event Hub durchführen. Um diese Daten in einer Power BI-Visualisierung zu verwenden, richten Sie zuerst einen Stream Analytics-Auftrag zum Abrufen der Daten ein, die dann in die Power BI-Visualisierung eingefügt werden.

### <a name="create-the-stream-analytics-job"></a>Erstellen des Stream Analytics-Auftrags

1. Klicken Sie im Azure-Portal auf **Ressource erstellen**. Geben Sie im Suchfeld den Text **stream analytics** ein, und drücken Sie die **EINGABETASTE**. Wählen Sie **Stream Analytics-Auftrag** aus. Klicken Sie im Bereich mit dem Stream Analytics-Auftrag auf **Erstellen**. 

2. Geben Sie die folgenden Informationen für den Auftrag ein:

   **Auftragsname**: Verwenden Sie den Namen **contosoEHjob**. Dieses Feld enthält den Namen des Auftrags, der global eindeutig sein muss.

   **Abonnement**: Wählen Sie Ihr Abonnement aus.

   **Ressourcengruppe**: Verwenden Sie die Ressourcengruppe, die auch von Ihrem Event Hub (**ContosoResourcesEH**) verwendet wird.

   **Standort**: Verwenden Sie den Standort, der auch im Einrichtungsskript verwendet wurde (**USA, Westen**).

   ![Screenshot: Erstellen eines neuen Stream Analytics-Auftrags](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-add-job.png)

    Übernehmen Sie in den restlichen Feldern die Standardwerte. Klicken Sie auf **Create**. 

### <a name="add-an-input-to-the-stream-analytics-job"></a>Hinzufügen einer Eingabe zum Stream Analytics-Auftrag

Wenn Sie sich nicht im Bereich **Stream Analytics-Auftrag** des Portals befinden, gelangen Sie zurück zu Ihrem Stream Analytics-Auftrag, indem Sie im Portal auf **Ressourcengruppen** klicken und dann Ihre Ressourcengruppe auswählen (**ContosoResourcesEH**). Mit dieser Aktion werden alle Ressourcen der Gruppe angezeigt, und Sie können Ihren Stream Analytics-Auftrag auswählen. 

Die Eingaben für den Stream Analytics-Auftrag sind die Kreditkartentransaktionen vom Event Hub.

> [!NOTE]
> Die Werte für Variablen, die mit dem Dollarzeichen ($) beginnen, werden in den Startskripts in den vorherigen Abschnitten festgelegt. Sie müssen beim Angeben dieser Felder hier die gleichen Werte verwenden, also den Event Hubs-Namespace und den Event Hub-Namen.

1. Klicken Sie unter **Auftragstopologie** auf **Eingaben**.

2. Klicken Sie im Bereich **Eingaben** auf **Datenstromeingabe hinzufügen**, und wählen Sie die Option „Event Hubs“. Füllen Sie im angezeigten Fenster die folgenden Felder aus:

   **Eingabealias**: Verwenden Sie **contosoinputs**. Dieses Feld ist der Name des Eingabestreams, der beim Definieren der Abfrage für die Daten verwendet wird.

   **Abonnement**: Wählen Sie Ihr Abonnement aus.

   **Event Hubs-Namespace**: Wählen Sie Ihren Event Hub-Namespace ($**eventHubNamespace**) aus. 

   **Event Hub-Name**: Klicken Sie auf **Vorhandene verwenden**, und wählen Sie Ihren Event Hub ($**eventHubName**) aus.

   **Event Hubs-Richtlinienname**: Wählen Sie **RootManageSharedAccessKey**.

   **Event Hubs-Consumergruppe**: Lassen Sie dieses Feld leer, um die Standardconsumergruppe zu verwenden.

   Übernehmen Sie in den restlichen Feldern die Standardwerte.

   ![Screenshot: Hinzufügen eines Eingabestreams zum Stream Analytics-Auftrag](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-inputs.png)

5. Klicken Sie auf **Speichern**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Hinzufügen einer Ausgabe zum Stream Analytics-Auftrag

1. Klicken Sie unter **Auftragstopologie** auf **Ausgaben**. Dieses Feld ist der Name des Ausgabestreams, der beim Definieren der Abfrage für die Daten verwendet wird.

2. Klicken Sie im Bereich **Ausgaben** auf **Hinzufügen**, und wählen Sie dann **Power BI** aus. Füllen Sie im angezeigten Fenster die folgenden Felder aus:

   **Ausgabealias**: Verwenden Sie **contosooutputs**. Dieses Feld enthält den eindeutigen Alias für die Ausgabe. 

   **Datasetname**: Verwenden Sie **contosoehdataset**. Dieses Feld enthält den Namen des Datasets, das in Power BI verwendet werden soll. 

   **Tabellenname**: Verwenden Sie **contosoehtable**. Dieses Feld enthält den Namen der Tabelle, die in Power BI verwendet werden soll. 

   Übernehmen Sie in den restlichen Feldern die Standardwerte.

   ![Screenshot: Einrichten der Ausgabe für einen Stream Analytics-Auftrag](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-outputs.png)

3. Klicken Sie auf **Autorisieren**, und melden Sie sich an Ihrem Power BI-Konto an.

4. Übernehmen Sie in den restlichen Feldern die Standardwerte.

5. Klicken Sie auf **Speichern**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurieren der Abfrage des Stream Analytics-Auftrags

Diese Abfrage wird verwendet, um die Daten abzurufen, die schließlich an die Power BI-Visualisierung gesendet werden. Es werden die Elemente **contosoinputs** und **contosooutputs** verwendet, die Sie zuvor beim Einrichten des Auftrags definiert haben. Mit dieser Abfrage werden die Kreditkartentransaktionen abgerufen, die als betrügerisch eingestuft werden. Hierbei handelt es sich um Fälle, in denen dieselbe Kreditkartennummer innerhalb eines Intervalls von fünf Sekunden über mehrere Transaktionen an unterschiedlichen Standorten verfügt.

1. Klicken Sie unter **Auftragstopologie** auf **Abfrage**.

2. Ersetzen Sie die Abfrage durch Folgendes: 

   ```SQL
   /* criteria for fraud:
      credit card purchases with the same card
      in different locations within 5 seconds
   */
   SELECT System.Timestamp AS WindowEnd, 
     COUNT(*) as FraudulentUses      
   INTO contosooutputs
   FROM contosoinputs CS1 TIMESTAMP BY [Timestamp]
       JOIN contosoinputs CS2 TIMESTAMP BY [Timestamp]
       /* where the credit card # is the same */
       ON CS1.CreditCardId = CS2.CreditCardId
       /* and time between the two is between 0 and 5 seconds */
       AND DATEDIFF(second, CS1, CS2) BETWEEN 0 AND 5
       /* where the location is different */
   WHERE CS1.Location != CS2.Location
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

4. Klicken Sie auf **Speichern**.

### <a name="test-the-query-for-the-stream-analytics-job"></a>Testen der Abfrage für den Stream Analytics-Auftrag 

1. Führen Sie die App für die Anomalieerkennung aus, um Daten an den Event Hub zu senden, während Sie den Test einrichten und ausführen. 

2. Klicken Sie im Bereich „Abfrage“ auf die Punkte neben der Eingabe **contosoinputs**, und wählen Sie anschließend die Option **Beispieldaten aus Eingabe**.

3. Geben Sie an, dass Sie Daten für einen Zeitraum von drei Minuten benötigen, und klicken Sie anschließend auf **OK**. Warten Sie auf die Benachrichtigung, dass die Beispieldaten erfasst wurden.

4. Klicken Sie auf **Test**, und vergewissern Sie sich, dass Sie Ergebnisse erhalten. Die Ergebnisse werden im Abschnitt **Ergebnisse** unten rechts unterhalb der Abfrage angezeigt.

5. Schließen Sie den Abfragebereich.

### <a name="run-the-stream-analytics-job"></a>Ausführen des Stream Analytics-Auftrags

Klicken Sie im Stream Analytics-Auftrag auf **Starten**, **Jetzt** und dann erneut auf **Starten**. Sobald der Auftrag erfolgreich gestartet wurde, ändert sich der Status des Auftrags von **Beendet** in **Wird ausgeführt**.

## <a name="set-up-the-power-bi-visualizations"></a>Einrichten der Power BI-Visualisierungen

1. Führen Sie die App für die Anomalieerkennung aus, um Daten an den Event Hub zu senden, während Sie die Power BI-Visualisierung einrichten. Unter Umständen müssen Sie sie mehrmals ausführen, da bei jeder Ausführung nur 1.000 Transaktionen generiert werden.

2. Melden Sie sich bei Ihrem [Power BI](https://powerbi.microsoft.com/)-Konto an.

3. Navigieren Sie zu **Mein Arbeitsbereich**.

4. Klicken Sie auf **Datasets**.

   Es sollte das Dataset angezeigt werden, das Sie beim Erstellen der Ausgabe für den Stream Analytics-Auftrag (**contosoehdataset**) angegeben haben. Es kann 5 bis 10 Minuten dauern, bis das Dataset zum ersten Mal angezeigt wird.

5. Klicken Sie auf **Dashboards** und dann auf **Erstellen**, und wählen Sie die Option **Dashboard**.

   ![Screenshot: Schaltflächen „Dashboards“ und „Erstellen“](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-dashboard.png)

6. Geben Sie den Namen des Dashboards an, und klicken Sie dann auf **Erstellen**. Verwenden Sie den Namen **Kreditkartenanomalien**.

   ![Screenshot: Angeben eines Dashboardnamens](./media/event-hubs-tutorial-visualize-anomalies/power-bi-dashboard-name.png)

7. Klicken Sie auf der Seite „Dashboard“ auf **Kachel hinzufügen**, wählen Sie im Abschnitt **ECHTZEITDATEN** die Option **Benutzerdefinierte Streamingdaten**, und klicken Sie dann auf **Weiter**.

   ![Screenshot: Angeben der Quelle für die Kachel](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-card-real-time-data.png)

8. Wählen Sie Ihr Dataset (**contosoehdataset**) aus, und klicken Sie auf **Weiter**.

   ![Screenshot: Angeben des Datasets](./media/event-hubs-tutorial-visualize-anomalies/power-bi-dashboard-select-dataset.png)

9. Wählen Sie als Visualisierungstyp die Option **Karte**. Klicken Sie unter **Felder** auf **Wert hinzufügen**, und wählen Sie dann **fraudulentuses**.

   ![Screenshot: Angeben des Visualisierungstyps und der Felder](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-card-tile.png)

   Klicken Sie auf **Weiter**.

10. Legen Sie den Titel auf **Fraudulent uses** (Betrügerische Nutzung) und den Untertitel auf **Sum in last few minutes** (Summe in den letzten Minuten) fest. Klicken Sie auf **Anwenden**. Die Kachel wird in Ihrem Dashboard gespeichert.

    ![Screenshot: Angeben des Titels und Untertitels für die Dashboardkachel](./media/event-hubs-tutorial-visualize-anomalies/power-bi-tile-details.png)

    > [!IMPORTANT]
    > Wenn Sie die Beispielanwendung ausführen und Daten an den Event Hub streamen, ändert sich die Zahl auf dieser Kachel schnell (sekündlich). Der Grund dafür ist, dass die Stream Analytics-Abfrage den Wert tatsächlich **jede Sekunde** aktualisiert. Aktualisieren Sie die Abfrage, sodass ein rollierendes Fenster von 3 Minuten verwendet wird, um die Summe der letzten Minuten anzuzeigen. 
11. Fügen Sie eine weitere Visualisierung hinzu. Wiederholen Sie die ersten Schritte:

    * Klicken Sie auf **Kachel hinzufügen**.
    * Wählen Sie **Benutzerdefinierte Streamingdaten**. 
    * Klicken Sie auf **Weiter**.
    * Wählen Sie Ihr Dataset aus, und klicken Sie dann auf **Weiter**. 

12. Wählen Sie unter **Visualisierungstyp** die Option **Liniendiagramm**.

13. Klicken Sie unter **Achse** auf **Wert hinzufügen**, und wählen Sie dann **windowend**. 

14. Klicken Sie unter **Werte** auf **Wert hinzufügen**, und wählen Sie **fraudulentuses**.

15. Wählen Sie unter **Das anzuzeigende Zeitfenster** die letzten fünf Minuten aus. Klicken Sie auf **Weiter**.

16. Geben Sie für den Titel **Show fraudulent uses over time** (Betrügerische Nutzung in Abhängigkeit der Zeit) an, und lassen Sie den Untertitel für die Kachel leer. Klicken Sie anschließend auf **Übernehmen**. Sie gelangen zurück zum Dashboard.

17. Führen Sie die App für die Anomalieerkennung erneut aus, um einige Daten an den Event Hub zu senden. Sie sehen, wie sich die Kachel **Fraudulent uses** (Betrügerische Nutzungen) ändert, während die Daten analysiert werden, und wie im Liniendiagramm Daten angezeigt werden. 

    ![Screenshot: Anzeigen der Power BI-Ergebnisse](./media/event-hubs-tutorial-visualize-anomalies/power-bi-results.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie alle von Ihnen erstellten Ressourcen entfernen möchten, können Sie die Power BI-Visualisierungsdaten entfernen und dann die Ressourcengruppe löschen. Beim Löschen der Ressourcengruppe werden alle in der Gruppe enthaltenen Ressourcen gelöscht. In diesem Fall werden der Event Hub, der Event Hub-Namespace, der Stream Analytics-Auftrag und die Ressourcengruppe selbst entfernt. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Bereinigen von Ressourcen in der Power BI-Visualisierung

Melden Sie sich an Ihrem Power BI-Konto an. Navigieren Sie zu **Mein Arbeitsbereich**. Klicken Sie in der Zeile mit Ihrem Dashboardnamen auf das Papierkorbsymbol. Navigieren Sie als Nächstes zu **Datasets**, und klicken Sie auf das Papierkorbsymbol, um das Dataset (**contosoehdataset**) zu löschen.

### <a name="clean-up-resources-using-azure-cli"></a>Bereinigen von Ressourcen mithilfe der Azure-Befehlszeilenschnittstelle

Um die Ressourcengruppe zu entfernen, verwenden Sie den Befehl [az group delete](/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="clean-up-resources-using-powershell"></a>Bereinigen von Ressourcen über PowerShell

Verwenden Sie zum Entfernen der Ressourcengruppe den Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:
> [!div class="checklist"]
> * Erstellen eines Event Hubs-Namespace
> * Erstellen eines Ereignis-Hubs
> * Ausführen der App, mit der Ereignisse simuliert und an den Event Hub gesendet werden
> * Konfigurieren eines Stream Analytics-Auftrags zum Verarbeiten von an den Hub gesendeten Ereignissen
> * Konfigurieren einer Power BI-Visualisierung zum Anzeigen der Ergebnisse

Fahren Sie mit dem nächsten Artikel fort, um mehr über Azure Event Hubs zu erfahren.

> [!div class="nextstepaction"]
> [Erste Schritte beim Senden von Nachrichten an Azure Event Hubs in .NET Standard](event-hubs-dotnet-standard-getstarted-send.md)

[kostenloses Konto erstellen]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
