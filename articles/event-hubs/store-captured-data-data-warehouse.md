---
title: Migrieren von Ereignisdaten zu SQL Data Warehouse – Azure Event Hubs | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie Daten aus Ihrem Event Hub mithilfe einer durch eine Event Grid-Instanz ausgelösten Azure-Funktion in einer SQL Data Warehouse-Instanz erfassen.
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
ms.author: shvija
ms.custom: seodec18
ms.date: 12/06/2018
ms.topic: tutorial
ms.service: event-hubs
ms.openlocfilehash: 90a17839afdddb4d6ad8abfa57963b4c76b100ed
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65604295"
---
# <a name="migrate-captured-event-hubs-data-to-a-sql-data-warehouse-using-event-grid-and-azure-functions"></a>Migrieren erfasster Event Hubs-Daten zu einer SQL Data Warehouse-Instanz mithilfe von Event Grid und Azure Functions

Event Hubs [Capture](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) ist die einfachste Methode, um Streamingdaten in Event Hubs automatisch an Azure Blob Storage oder an Azure Data Lake Store zu übermitteln. Die Daten können anschließend verarbeitet und an beliebige andere Speicherorte (etwa SQL Data Warehouse oder Cosmos DB) übermittelt werden. In diesem Tutorial wird gezeigt, wie Sie Daten aus Ihrem Event Hub mithilfe einer durch eine [Event Grid](https://docs.microsoft.com/azure/event-grid/overview)-Instanz ausgelösten Azure-Funktion in einer SQL Data Warehouse-Instanz erfassen.

![Visual Studio](./media/store-captured-data-data-warehouse/EventGridIntegrationOverview.PNG)

*   Zuerst erstellen Sie einen Event Hub. Dabei aktivieren Sie das Feature **Capture** und legen Azure Blob Storage als Ziel fest. Von „WindTurbineGenerator“ generierte Daten werden an den Event Hub gestreamt und als Avro-Dateien automatisch in Azure Storage erfasst. 
*   Als Nächstes erstellen Sie ein Azure Event Grid-Abonnement mit dem Event Hubs-Namespace als Quelle und dem Azure-Funktionsendpunkt als Ziel.
*   Wenn durch das Capture-Feature von Event Hubs eine neue Avro-Datei an das Azure Storage-Blob übermittelt wird, benachrichtigt Event Grid die Azure-Funktion mit dem Blob-URI. Die Funktion migriert daraufhin Daten aus dem Blob in eine SQL Data Warehouse-Instanz.

In diesem Tutorial führen Sie die folgenden Aktionen aus: 

> [!div class="checklist"]
> * Bereitstellen der Infrastruktur
> * Veröffentlichen von Code in einer Functions-App
> * Erstellen eines Event Grid-Abonnements auf der Grundlage der Functions-App
> * Streamen von Beispieldaten an Event Hub 
> * Überprüfen erfasster Daten in SQL Data Warehouse

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- [Visual studio 2019](https://www.visualstudio.com/vs/). Achten Sie bei der Installation darauf, folgende Workloads zu installieren: .NET-Desktopentwicklung, Azure-Entwicklung, ASP.NET- und Webentwicklung, Node.js-Entwicklung, Python-Entwicklung.
- Laden Sie das [Git-Beispiel](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) herunter. Die Beispiellösung umfasst folgende Komponenten:
    - *WindTurbineDataGenerator:* Ein einfacher Verleger, der Beispieldaten einer Windturbine an einen Capture-fähigen Event Hub sendet.
    - *FunctionDWDumper:* Eine Azure-Funktion, die eine Event Grid-Benachrichtigung erhält, wenn im Azure Storage-Blob eine Avro-Datei erfasst wird. Sie erhält den URI-Pfad des Blobs, liest dessen Inhalt und pusht die Daten an eine SQL Data Warehouse-Instanz.

### <a name="deploy-the-infrastructure"></a>Bereitstellen der Infrastruktur
Verwenden Sie Azure PowerShell oder die Azure-Befehlszeilenschnittstelle sowie [diese Azure Resource Manager-Vorlage](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json), um die für dieses Tutorial benötigte Infrastruktur bereitzustellen. Diese Vorlage erstellt die folgenden Ressourcen:

-   Event Hub mit aktiviertem Capture-Feature
-   Speicherkonto für die erfassten Ereignisdaten
-   Azure App Service-Plan zum Hosten der Functions-App
-   Functions-App für die Verarbeitung der erfassten Ereignisdateien
-   SQL Server zum Hosten des Data Warehouse
-   SQL Data Warehouse zum Speichern der migrierten Daten

Die folgenden Abschnitte enthalten Azure CLI- und Azure PowerShell-Befehle für die Bereitstellung der benötigten Infrastruktur für das Tutorial. Aktualisieren Sie vor dem Ausführen der Befehle die Namen der folgenden Objekte: 

- Azure-Ressourcengruppe 
- Region für die Ressourcengruppe
- Event Hubs-Namespace
- Event Hub
- Azure SQL Server
- SQL-Benutzer (und Kennwort)
- Azure SQL-Datenbank
- Azure Storage 
- Azure Functions-App

Die Erstellung aller Azure-Artefakte dauert etwas. Warten Sie, bis das Skript abgeschlossen ist, bevor Sie fortfahren. Sollte die Bereitstellung nicht erfolgreich sein, löschen Sie die Ressourcengruppe, beheben Sie das gemeldete Problem, und führen Sie den Befehl erneut aus. 

#### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle
Wenn Sie die Vorlage mithilfe der Azure CLI bereitstellen möchten, verwenden Sie die folgenden Befehle:

```azurecli-interactive
az group create -l westus -n rgDataMigrationSample

az group deployment create \
  --resource-group rgDataMigrationSample \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

#### <a name="azure-powershell"></a>Azure PowerShell
Wenn Sie die Vorlage mithilfe von PowerShell bereitstellen möchten, verwenden Sie die folgenden Befehle:

```powershell
New-AzResourceGroup -Name rgDataMigration -Location westcentralus

New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```


### <a name="create-a-table-in-sql-data-warehouse"></a>Erstellen einer Tabelle in SQL Data Warehouse 
Erstellen Sie in Ihrer SQL Data Warehouse-Instanz eine Tabelle, indem Sie das Skript [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) mithilfe von [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md), [SQL Server Management Studio](../sql-data-warehouse/sql-data-warehouse-query-ssms.md) oder mithilfe des Abfrage-Editors im Portal ausführen. 

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

## <a name="publish-code-to-the-functions-app"></a>Veröffentlichen von Code in der Functions-App

1. Öffnen Sie die Projektmappe *EventHubsCaptureEventGridDemo.sln* in Visual Studio 2019.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf *FunctionEGDWDumper*, und wählen Sie **Veröffentlichen** aus.

   ![Veröffentlichen einer Funktions-App](./media/store-captured-data-data-warehouse/publish-function-app.png)

1. Wählen Sie **Azure-Funktionen-App** und **Vorhandenes Element auswählen**. Wählen Sie **Veröffentlichen**.

   ![Ziel-Funktions-App](./media/store-captured-data-data-warehouse/pick-target.png)

1. Wählen Sie die Funktions-App aus, die Sie über die Vorlage bereitgestellt haben. Klicken Sie auf **OK**.

   ![Auswählen der Funktions-App](./media/store-captured-data-data-warehouse/select-function-app.png)

1. Wenn Visual Studio das Profil konfiguriert hat, wählen Sie **Veröffentlichen**.

   ![Wählen Sie "Veröffentlichen"](./media/store-captured-data-data-warehouse/select-publish.png)

Nachdem Sie die Funktion veröffentlicht haben, können Sie das Erfassungsereignis von Event Hubs abonnieren.


## <a name="create-an-event-grid-subscription-from-the-functions-app"></a>Erstellen eines Event Grid-Abonnements auf der Grundlage der Functions-App
 
1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/). Wählen Sie die Ressourcengruppe und die Funktions-App aus.

   ![Anzeigen der Funktions-App](./media/store-captured-data-data-warehouse/view-function-app.png)

1. Wählen Sie die Funktion aus.

   ![Auswählen der Funktion](./media/store-captured-data-data-warehouse/select-function.png)

1. Wählen Sie **Event Grid-Abonnement hinzufügen** aus.

   ![Abonnement hinzufügen](./media/store-captured-data-data-warehouse/add-event-grid-subscription.png)

1. Benennen Sie das Event Grid-Abonnement. Verwenden Sie **Event Hubs-Namespaces** als Ereignistyp. Geben Sie Werte zum Auswählen der Instanz des Event Hubs-Namespace an. Übernehmen Sie als Abonnenten-Endpunkt den angegebenen Wert. Klicken Sie auf **Erstellen**.

   ![Erstellen des Abonnements](./media/store-captured-data-data-warehouse/set-subscription-values.png)

## <a name="generate-sample-data"></a>Generieren von Beispieldaten  
Sie haben den Event Hub, SQL Data Warehouse, die Azure Functions-App und das Event Grid-Abonnement eingerichtet. Nun können Sie „WindTurbineDataGenerator.exe“ ausführen, um Datenströme an den Event Hub zu generieren, nachdem Sie die Verbindungszeichenfolge und den Namen Ihres Event Hubs im Quellcode aktualisiert haben. 

1. Wählen Sie im Portal den Event Hub-Namespace aus. Wählen Sie **Verbindungszeichenfolgen**.

   ![Auswählen von „Verbindungszeichenfolgen“](./media/store-captured-data-data-warehouse/event-hub-connection.png)

2. Wählen Sie **RootManageSharedAccessKey**.

   ![Auswählen des Schlüssels](./media/store-captured-data-data-warehouse/show-root-key.png)

3. Kopieren Sie den Wert für **Verbindungszeichenfolge – Primärschlüssel**.

   ![Kopieren des Schlüssels](./media/store-captured-data-data-warehouse/copy-key.png)

4. Kehren Sie zum Visual Studio-Projekt zurück. Öffnen Sie *program.cs* im Projekt *WindTurbineDataGenerator*.

5. Ersetzen Sie die Werte für **EventHubConnectionString** und **EventHubName** durch die Verbindungszeichenfolge und den Namen Ihres Event Hubs. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Erstellen Sie die Lösung, und führen Sie die Anwendung „WindTurbineGenerator.exe“ aus. 

## <a name="verify-captured-data-in-data-warehouse"></a>Überprüfen erfasster Daten im Data Warehouse
Fragen Sie nach ein paar Minuten die Tabelle in Ihrer SQL Data Warehouse-Instanz ab. Wie Sie sehen, wurden die von „WindTurbineDataGenerator“ generierten Daten an Ihren Event Hub gestreamt, in einem Azure Storage-Container erfasst und anschließend durch die Azure-Funktion in die SQL Data Warehouse-Tabelle migriert.  

## <a name="next-steps"></a>Nächste Schritte 
Sie können leistungsstarke Datenvisualisierungstools für Ihr Data Warehouse nutzen, um verwertbare Erkenntnisse zu gewinnen.

In [diesem Artikel](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-integrate-power-bi) erfahren Sie, wie Sie Power BI mit SQL Data Warehouse verwenden.



