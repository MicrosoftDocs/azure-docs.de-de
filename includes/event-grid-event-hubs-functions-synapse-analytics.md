---
title: include file
description: include file
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 12/07/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d6b8b35b36830568d6ff8c46a381fec0cfc57e84
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854155"
---
:::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/overview.png" alt-text="Anwendungsübersicht":::

Dieses Diagramm zeigt den Workflow der Lösung, die Sie in diesem Tutorial erstellen: 

1. Daten, die an einen Azure Event Hub gesendet werden, werden in einem Azure Blob Storage erfasst.
2. Wenn die Datenerfassung abgeschlossen ist, wird ein Ereignis erzeugt und an ein Azure Event Grid gesendet. 
3. Das Event Grid leitet diese Ereignisdaten an eine Azure-Funktions-App weiter.
4. Die Funktions-App verwendet die Blob-URL in den Event-Daten, um den Blob aus dem Speicher abzurufen. 
5. Die Funktions-App migriert die Blobdaten zu einer Azure Synapse Analytics-Instanz. 

In diesem Artikel führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> - Bereitstellen der erforderlichen Infrastruktur für das Tutorial
> - Veröffentlichen von Code in einer Functions-App
> - Erstellen eines Event Grid-Abonnements 
> - Streamen von Beispieldaten an Event Hubs
> - Überprüfen erfasster Daten in Azure Synapse Analytics

## <a name="prerequisites"></a>Voraussetzungen
Für dieses Tutorial benötigen Sie Folgendes:

- Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
- [Visual Studio 2019](https://www.visualstudio.com/vs/) mit Workloads für .NET-Desktopentwicklung, Azure-Entwicklung, ASP.NET- und Webentwicklung, Node.js-Entwicklung und Python-Entwicklung.
- Laden Sie das Beispielprojekt [EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) auf Ihren Computer herunter.
    - WindTurbineDataGenerator: Ein einfacher Verleger, der Beispieldaten einer Windturbine an einen Capture-fähigen Event Hub sendet.
    - FunctionDWDumper: Eine Azure-Funktion, die eine Event Grid-Benachrichtigung erhält, wenn im Azure Storage-Blob eine Avro-Datei erfasst wird. Sie erhält den URI-Pfad des Blobs, liest dessen Inhalt und pusht die Daten an Azure Synapse Analytics (dedizierter SQL-Pool).

## <a name="deploy-the-infrastructure"></a>Bereitstellen der Infrastruktur
In diesem Schritt stellen Sie die erforderliche Infrastruktur mit einer [Resource Manager-Vorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json) bereit. Wenn Sie die Vorlage bereitstellen, werden die folgenden Ressourcen erstellt:

* Event Hub mit aktiviertem Capture-Feature.
* Speicherkonto für die erfassten Dateien. 
* App Service-Plan zum Hosten der Funktions-App
* Funktions-App zum Verarbeiten des Ereignisses
* SQL Server zum Hosten des Data Warehouse
* Azure Synapse Analytics (dedizierter SQL-Pool) zum Speichern der migrierten Daten

### <a name="use-azure-cli-to-deploy-the-infrastructure"></a>Bereitstellen der Infrastruktur über die Azure-Befehlszeilenschnittstelle

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 
2. Wählen Sie oben die Schaltfläche **Cloud Shell** aus.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/azure-portal.png" alt-text="Azure portal":::
3. Die Cloud Shell wird unten im Browser geöffnet.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/launch-cloud-shell.png" alt-text="Cloud Shell":::
4. Wenn Sie in der Cloud Shell eine Option zur Auswahl zwischen **Bash** und **PowerShell** sehen, wählen Sie **Bash** aus. 
5. Wenn Sie die Cloud Shell zum ersten Mal verwenden, erstellen Sie ein Speicherkonto, indem Sie **Speicher erstellen** auswählen. Zum Speichern von Daten benötigt Azure Cloud Shell ein Azure-Speicherkonto. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/create-storage-cloud-shell.png" alt-text="Erstellen von Speicher für Cloud Shell":::
6. Warten Sie, bis die Cloud Shell initialisiert wird. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/cloud-shell-initialized.png" alt-text="Cloud Shell initialisiert":::
1. Erstellen Sie eine Azure-Ressourcengruppe, indem Sie den folgenden CLI-Befehl ausführen: 
    1. Kopieren Sie den folgenden Befehl, und fügen Sie ihn in das Cloud Shell-Fenster ein. Ändern Sie ggf. den Namen der Ressourcengruppe und des Standorts.

        ```azurecli
        az group create -l eastus -n rgDataMigration
        ```
    2. Drücken Sie die **EINGABETASTE**. 

        Beispiel:
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n rgDataMigration
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/rgDataMigration",
          "location": "eastus",
          "managedBy": null,
          "name": "rgDataMigration",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. Stellen Sie alle im vorherigen Abschnitt genannten Ressourcen (Event Hub, Speicherkonto, Funktions-App, Azure Synapse Analytics) bereit, indem Sie den folgenden CLI-Befehl ausführen: 
    1. Kopieren Sie den folgenden Befehl in das Cloud Shell-Fenster. Alternativ können Sie auch in einen Editor Ihrer Wahl kopieren/einfügen, Werte festlegen und dann den Befehl in die Cloud Shell kopieren. 

        > [!IMPORTANT]
        > Geben Sie vor dem Ausführen des Befehls Werte für die folgenden Entitäten an: 
        > - Name der Ressourcengruppe, die Sie zuvor erstellt haben.
        > - Name des Event Hub-Namespace. 
        > - Name für den Event Hub. Sie können den Wert unverändert lassen (hubdatamigration).
        > - Name für den SQL Server.
        > - Name und Kennwort des SQL-Benutzers 
        > - Name für die Datenbank.
        > - Name des Speicherkontos. 
        > - Name der Funktionen-App. 


        ```azurecli
        az deployment group create \
            --resource-group rgDataMigration \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    3.  Drücken Sie **EINGABETASTE** i, Cloud Shell-Fenster zum Ausführen des Befehls. Dieser Vorgang kann einige Zeit in Anspruch nehmen, da Sie zahlreiche Ressourcen erstellen. Stellen Sie im Ergebnis des Befehls sicher, dass es keine Fehler gegeben hat. 
1. Schließen Sie die Cloud Shell-Instanz, indem Sie die Schaltfläche **Cloud Shell** im Portal (oder) **X** in der rechten oberen Ecke des Cloud Shell-Fensters auswählen. 

### <a name="verify-that-the-resources-are-created"></a>Überprüfen, ob die Ressourcen erstellt wurden

1. Wählen Sie im Azure-Portals im linken Menü **Ressourcengruppe**. 
2. Filtern Sie die Liste der Ressourcengruppen, indem Sie den Namen Ihrer Ressourcengruppe in das Suchfeld eingeben. 
3. Wählen Sie Ihre Ressourcengruppe in der Liste aus.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-resource-group.png" alt-text="Ressourcengruppe auswählen":::
4. Überprüfen Sie, ob die folgenden Ressourcen in der Ressourcengruppe aufgeführt sind:

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png" alt-text="Ressourcen in der Ressourcengruppe" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png":::

### <a name="create-a-table-in-azure-synapse-analytics"></a>Erstellen einer Tabelle in Azure Synapse Analytics
Erstellen Sie in Ihrem Data Warehouse durch Ausführen des Skripts [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) eine Tabelle. Verwenden Sie zum Ausführen des Skripts Visual Studio oder den Abfrage-Editor im Portal. Die folgenden Schritte veranschaulichen die Verwendung des Query-Editors: 

1. Wählen Sie in der Liste der Ressourcen in der Ressourcengruppe Ihren **dedizierten SQL-Pool** aus. 
2. Wählen Sie auf der Seite **Dedicated SQL pool** (Dedizierter SQL-Pool) im Abschnitt **Allgemeine Aufgaben** im linken Menü die Option **Abfrage-Editor (Vorschau)** aus. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/sql-data-warehouse-page.png" alt-text="Seite „Azure Synapse Analytics“":::
2. Geben Sie den Namen für **Benutzer** und **Kennwort** für die SQL Server ein, und wählen **OK**. Sollte eine Meldung angezeigt werden, in der es darum geht, Ihrem Client Zugriff auf den SQL-Server zu gewähren, gehen Sie wie folgt vor:
    1. Wählen Sie den folgenden Link aus: **Serverfirewall festlegen**. 
    2. Wählen Sie auf der Seite **Firewalleinstellungen** auf der Symbolleiste die Option **Client-IP hinzufügen** und anschließend auf der Symbolleiste die Option **Speichern** aus. 
    3. Wählen Sie in der Erfolgsmeldung die Option **OK** aus.
    4. Kehren Sie zur Seite **Dedicated SQL pool** (Dedizierter SQL-Pool) zurück, und wählen Sie im linken Menü die Option **Abfrage-Editor (Vorschau)** aus. 
    5. Geben Sie **Benutzer** und **Kennwort** ein, und wählen Sie anschließend **OK** aus. 
1. Fügen Sie das folgende SQL-Skript ins Abfragefenster ein, und führen Sie es aus: 

    ```sql
    CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
        [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
        [MeasureTime] datetime NULL, 
        [GeneratedPower] float NULL, 
        [WindSpeed] float NULL, 
        [TurbineSpeed] float NULL
    )
    WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    ```

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/run-sql-query.png" alt-text="SQL-Abfrage ausführen":::
5. Halten Sie diese Registerkarte oder dieses Fenster offen, damit Sie überprüfen können, ob die Daten am Ende des Tutorials erstellt wurden. 

### <a name="update-the-function-runtime-version"></a>Aktualisieren der Funktionsruntimeversion

1. Öffnen Sie im Webbrowser einen weiteren Tab, und navigieren Sie zum [Azure-Portal](https://portal.azure.com).
1. Wählen Sie im Azure-Portals im linken Menü **Ressourcengruppe**.
1. Wählen Sie die Ressourcengruppe aus, in der die Funktions-App enthalten ist. 
1. Wählen Sie in der Ressourcenliste der Ressourcengruppe die **Funktions-App** aus.
1. Wählen Sie im linken Menü unter **Einstellungen** die Option **Konfiguration** aus. 
1. Wechseln Sie im rechten Bereich zur Registerkarte **Runtimeeinstellungen der Funktion**. 
1. Aktualisieren Sie die **Runtimeversion** auf **~3**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-runtime-version.png" alt-text="Aktualisieren der Funktionsruntimeversion":::
6. Wählen Sie auf der Symbolleiste **Speichern** aus. 
1. Wählen Sie im Bestätigungspopupfenster **Änderungen speichern** die Option **Weiter** aus. 

## <a name="publish-the-azure-functions-app"></a>Veröffentlichen der Azure Functions-App

1. Starten Sie Visual Studio.
2. Öffnen Sie die Lösung **EventHubsCaptureEventGridDemo.sln**, die Sie aus [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) als Teil der Voraussetzungen heruntergeladen haben. Sie befindet sich im Ordner `/samples/e2e/EventHubsCaptureEventGridDemo`. 
3. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **FunctionEGDWDumper**, und wählen Sie **Veröffentlichen** aus.
4. Wenn der folgende Bildschirm angezeigt wird, wählen Sie **Start**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/start-publish-button.png" alt-text="Schaltfläche „Start“ im Abschnitt „Veröffentlichen“":::
5. Wählen Sie im Dialogfeld **Veröffentlichen** unter **Ziel** die Option **Azure** und dann **Weiter** aus. 
6. Wählen Sie **Azure-Funktions-App (Windows)** und dann **Weiter** aus.
7. Wählen Sie auf der Registerkarte **Functions-Instanz** Ihr Azure-Abonnement aus, erweitern Sie die Ressourcengruppe, und wählen Sie Ihre Funktions-App und anschließend **Fertig stellen** aus. Sie müssen sich bei Ihrem Azure-Konto anmelden, wenn Sie dies nicht bereits getan haben. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-select-function-app.png" alt-text="Auswählen Ihrer Funktionen-App":::
8. Wählen Sie auf der Seite **Veröffentlichen** im Abschnitt **Dienstabhängigkeiten** für **Speicher** die Option **Konfigurieren** aus. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-storage-configure-link.png" alt-text="Auswählen des Links „Konfigurieren“ für die Speicherdienstabhängigkeit":::
1. Führen Sie auf der Seite **Abhängigkeit konfigurieren** die folgenden Schritte aus: 
    1. Wählen Sie das zuvor erstellte **Speicherkonto** und anschließend **Weiter** aus. 

        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-dependency-storage.png" alt-text="Speicherkonto auswählen":::
    10. Geben Sie einen **Namen für die Verbindungszeichenfolge** an, legen Sie die **Option zum Speichern der Verbindungszeichenfolge** auf **Keine** fest, und wählen Sie anschließend **Weiter** aus. 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-connection-string.png" alt-text="Angeben des Namens der Verbindungszeichenfolge":::      
    1. Deaktivieren Sie die Kontrollkästchen **C#-Codedatei** und **Geheimnisspeicher**, und wählen Sie anschließend **Fertig stellen** aus.  
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-changes-summary.png" alt-text="Überprüfen der Änderungszusammenfassung":::
1. Wenn Visual Studio das Profil konfiguriert hat, wählen Sie **Veröffentlichen**.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-publish.png" alt-text="Wählen Sie &quot;Veröffentlichen&quot;":::
2. Wählen Sie auf der Registerkarte, auf der die Seite **Azure-Funktion** geöffnet ist, im linken Menü die Option **Funktionen** aus. Vergewissern Sie sich, dass die Funktion **EventGridTriggerMigrateData** in der Liste enthalten ist. Wiederholen Sie andernfalls die Veröffentlichung über Visual Studio, und aktualisieren Sie anschließend die Seite im Portal. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-function-creation.png" alt-text="Bestätigen der Funktionserstellung":::    

Nach dem Veröffentlichen der Funktion können Sie das Ereignis abonnieren.

## <a name="subscribe-to-the-event"></a>Abonnieren des Ereignisses

1. Navigieren Sie in einer neuen Registerkarte oder einem neuen Fenster eines Webbrowsers zum [Azur-Portal](https://portal.azure.com).
2. Wählen Sie im Azure-Portals im linken Menü **Ressourcengruppe**. 
3. Filtern Sie die Liste der Ressourcengruppen, indem Sie den Namen Ihrer Ressourcengruppe in das Suchfeld eingeben. 
4. Wählen Sie Ihre Ressourcengruppe in der Liste aus.
1. Wählen Sie in der Ressourcenliste den **Event Hubs-Namespace** aus.
1. Wählen Sie auf der Seite **Event Hubs-Namespace** im Menü auf der linken Seite die Option **Ereignisse** und anschließend auf der Symbolleiste die Option **+ Ereignisabonnement** aus. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-add-subscription-link.png" alt-text="Link zum Hinzufügen eines Ereignisabonnements auf der Seite „Ereignisse“ für einen Event Hubs-Namespace":::
1. Gehen Sie auf der Seite **Ereignisabonnement erstellen** wie folgt vor:
    1. Geben Sie einen Namen für das **Ereignisabonnement** ein. 
    1. Geben Sie unter Name einen Namen für das **Systemthema** ein. Ein Systemthema stellt einen Endpunkt bereit, den der Absender zum Senden von Ereignissen verwenden kann. Weitere Informationen finden Sie unter [Systemthemen in Azure Event Grid](../articles/event-grid/system-topics.md).
    1. Wählen Sie für **Endpunkttyp** die Option **Azure-Funktion** aus.
    1. Wählen Sie für **Endpunkt** den Link aus.
    1. Gehen Sie auf der Seite **Azure-Funktion auswählen** wie folgt vor, falls diese Angaben nicht automatisch ausgefüllt werden:
        1. Wählen Sie das Azure-Abonnement aus, das die Azure-Funktion enthält. 
        1. Wählen Sie die Ressourcengruppe für die Funktion aus. 
        1. Wählen Sie die Funktions-App aus.
        1. Wählen Sie den Bereitstellungsslot aus. 
        1. Wählen Sie die Funktion **EventGridTriggerMigrateData** aus. 
    1. Wählen Sie auf der Seite **Azure-Funktion auswählen** die Option **Auswahl bestätigen** aus.
    1. Wählen Sie anschließend auf der Seite **Ereignisabonnement erstellen** die Option **Erstellen** aus. 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png" alt-text="Erstellen eines Ereignisabonnements unter Verwendung der Funktion" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png":::
1. Vergewissern Sie sich, dass das Ereignisabonnement erstellt wurde. Wechseln Sie auf der Seite **Ereignisse** für den Event Hubs-Namespace zur Registerkarte **Ereignisabonnements**. 
    
    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png" alt-text="Bestätigen des Ereignisabonnements" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png":::
1. Wählen Sie in der Liste der Ressourcen in der Ressourcengruppe den App Service-Plan (nicht App Service) aus. 

## <a name="run-the-app-to-generate-data"></a>Ausführen der App zum Generieren von Daten
Ihr Event Hub, Ihr dedizierter SQL-Pool (vormals SQL Data Warehouse), Ihre Azure-Funktions-App sowie Ihr Ereignisabonnement sind nur fertig eingerichtet. Vor dem Ausführen einer Anwendung, die Daten für Event Hubs generiert, müssen Sie einige Werte konfigurieren.

1. Navigieren Sie im Azure-Portal zu der Ressourcengruppe, die Sie zuvor erstellt haben. 
2. Wähen Sie den Event Hubs-Namespace aus.
3. Wählen Sie auf der Seite **Event Hubs-Namespace** die Option **SAS-Richtlinien** im linken Menü aus.
4. Wählen Sie in der Richtlinienliste **RootManageSharedAccessKey** aus. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-namespace-shared-access-policies.png" alt-text="SAS-Richtlinienseite für einen Event Hubs-Namespace":::    
1. Wählen Sie die Schaltfläche „Kopieren“ neben dem Textfeld **Verbindungszeichenfolge-primärer Schlüssel** aus. 
1. Kehren Sie zur Visual Studio-Lösung zurück. 
1. Klicken Sie mit der rechten Maustaste auf das Projekt **WindTurbineDataGenerator**, und wählen Sie **Als Startprojekt festlegen** aus. 
1. Öffnen Sie **program.cs** im Projekt „WindTurbineDataGenerator“.
1. Ersetzen Sie `<EVENT HUBS NAMESPACE CONNECTION STRING>` durch die aus dem Portal kopierte Verbindungszeichenfolge. 
1. Ersetzen Sie `<EVENT HUB NAME>` durch den Namen des Event Hubs. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```
6. Erstellen Sie die Projektmappe. Führen Sie die Anwendung **WindTurbineGenerator.exe** aus. 
7. Warten Sie einigen Minuten, und fragen Sie dann auf dem anderen Browsertab mit dem geöffneten Abfragefenster die migrierten Daten aus der Tabelle in Ihrem Data Warehouse ab.

    ```sql
    select * from [dbo].[Fact_WindTurbineMetrics]    
    ```

    ![Abfrageergebnisse](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

## <a name="monitor-the-solution"></a>Überwachen der Lösung
Dieser Abschnitt unterstützt Sie bei der Überwachung oder Problembehandlung im Zusammenhang mit der Lösung. 

### <a name="view-captured-data-in-the-storage-account"></a>Anzeigen erfasster Daten im Speicherkonto
1. Navigieren Sie zur Ressourcengruppe, und wählen Sie das Speicherkonto aus, das zum Erfassen von Ereignisdaten verwendet wird. 
1. Wählen Sie auf der Seite **Speicherkonto** im linken Menü die Option **Storage-Explorer (Vorschau)** aus.
1. Erweitern Sie **BLOBCONTAINER**, und wählen Sie **windturbinecapture** aus. 
1. Öffnen Sie im rechten Bereich den Ordner mit dem Namen Ihres **Event Hubs-Namespace**. 
1. Öffnen Sie den Ordner mit dem Namen Ihres Event Hubs (**hubdatamigration**). 
1. Navigieren Sie durch die Ordner, um zu den AVRO-Dateien zu gelangen. Hier sehen Sie ein Beispiel:

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png" alt-text="Erfasste Datei im Speicher" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png":::
    

### <a name="verify-that-the-event-grid-trigger-invoked-the-function"></a>Vergewissern, dass die Funktion durch den Event Grid-Trigger aufgerufen wurde
1. Navigieren Sie zur Ressourcengruppe, und wählen Sie die Funktions-App aus. 
1. Wählen Sie im linken Menü die Option **Funktionen** aus.
1. Wählen Sie in der Liste die Funktion **EventGridTriggerMigrateData** aus. 
1. Wählen Sie auf der Seite **Funktion** im linken Menü die Option **Überwachen** aus. 
1. Wählen Sie **Konfigurieren** aus, um Application Insights für die Erfassung von Aufrufprotokollen zu konfigurieren. 
1. Erstellen Sie eine neue Ressource vom Typ **Application Insights**, oder wählen Sie eine bereits vorhandene Ressource aus. 
1. Kehren Sie zur Seite **Überwachen** für die Funktion zurück. 
1. Vergewissern Sie sich, dass die Clientanwendung (**windturbinedatagenerator**), von der Ereignisse gesendet werden, weiterhin ausgeführt wird. Falls nicht, führen Sie die App aus. 
1. Warten Sie mindestens fünf Minuten, und wählen Sie die Schaltfläche **Aktualisieren** aus, um Funktionsaufrufe zu sehen.    

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-invocations.png" alt-text="Funktionsaufrufe":::
1. Wählen Sie einen Aufruf aus, um Details anzuzeigen.

    Event Grid verteilt Ereignisdaten an die Abonnenten. Das folgende Beispiel zeigt Ereignisdaten, die erzeugt werden, wenn Daten, die über einen Event Hub übertragen werden, in einem Blob erfasst werden. Beachten Sie ins Besondere, dass die `fileUrl`-Eigenschaft im `data`-Objekt auf den Blob im Speicher verweist. Die Funktions-App verwendet diese URL zum Abrufen der Blobdatei mit den erfassten Daten.

    ```json
    {
        "topic": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourcegroups/rgDataMigration/providers/Microsoft.EventHub/namespaces/spehubns1207",
        "subject": "hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "id": "4538f1a5-02d8-4b40-9f20-36301ac976ba",
        "data": {
            "fileUrl": "https://spehubstorage1207.blob.core.windows.net/windturbinecapture/spehubns1207/hubdatamigration/0/2020/12/07/21/49/12.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "0",
            "sizeInBytes": 473444,
            "eventCount": 2800,
            "firstSequenceNumber": 55500,
            "lastSequenceNumber": 58299,
            "firstEnqueueTime": "2020-12-07T21:49:12.556Z",
            "lastEnqueueTime": "2020-12-07T21:50:11.534Z"
        },
        "dataVersion": "1",
        "metadataVersion": "1",
        "eventTime": "2020-12-07T21:50:12.7065524Z"
    }
    ```

### <a name="verify-that-the-data-is-stored-in-the-dedicated-sql-pool"></a>Vergewissern, dass die Daten im dedizierten SQL-Pool gespeichert wurden
Fragen Sie auf dem Browsertab mit dem geöffneten Abfragefenster die migrierten Daten aus der Tabelle in Ihrem dedizierten SQL-Pool ab.

![Abfrageergebnisse](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

