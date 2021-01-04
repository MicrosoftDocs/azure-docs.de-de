---
title: 'Tutorial: Erstellen einer .NET-Konsolen-App zum Verwalten von Daten in einem Azure Cosmos DB-SQL-API-Konto'
description: 'Tutorial: Hier erfahren Sie, wie Sie mithilfe einer C#-Konsolenanwendung Azure Cosmos DB-SQL-API-Ressourcen erstellen.'
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: kirankk
ms.custom: devx-track-csharp
ms.openlocfilehash: 49fa928285b29eaff806b009cf327e84e17491c6
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098721"
---
# <a name="tutorial-build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Tutorial: Erstellen einer .NET-Konsolen-App zum Verwalten von Daten in einem Azure Cosmos DB-SQL-API-Konto
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](./create-sql-api-java.md)
> * [Async Java](./create-sql-api-java.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Willkommen zum Tutorial zu den ersten Schritten mit der SQL-API von Azure Cosmos DB. Im Rahmen dieses Tutorials erstellen Sie eine Konsolenanwendung, mit der Azure Cosmos DB-Ressourcen erstellt und abgefragt werden können.

In diesem Tutorial wird Version 3.0 oder höher des [Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) verwendet. Sie können mit [.NET Framework oder .NET Core](https://dotnet.microsoft.com/download) arbeiten.

In diesem Lernprogramm wird Folgendes behandelt:

> [!div class="checklist"]
>
> * Erstellen eines Azure Cosmos-Kontos und Herstellen der Verbindung
> * Konfigurieren Ihres Projekts in Visual Studio
> * Erstellen einer Datenbank und eines Containers
> * Hinzufügen von Elementen zum Container
> * Abfragen des Containers
> * Durchführen von Erstellungs-, Lese-, Aktualisierungs- und Löschvorgängen (Create, Read, Update, Delete (CRUD)) für das Element
> * Löschen der Datenbank

Sie haben nicht genügend Zeit? Keine Sorge! Die vollständige Lösung ist auf [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started)verfügbar. Im Abschnitt [Abrufen der vollständigen Projektmappe für das Tutorial](#GetSolution) finden Sie eine Kurzanleitung.

Lassen Sie uns anfangen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein aktives Azure-Konto. Wenn Sie keines besitzen, können Sie sich für ein [kostenloses Konto](https://azure.microsoft.com/free/)registrieren.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Schritt 1: Erstellen eines Azure Cosmos DB-Kontos

Wir erstellen nun ein Azure Cosmos DB-Konto. Überspringen Sie diesen Abschnitt, wenn Sie bereits über ein Konto verfügen, das Sie verwenden möchten. Führen Sie zum Verwenden des Azure Cosmos DB-Emulators die Schritte unter [Azure Cosmos DB-Emulator](local-emulator.md) aus, um den Emulator einzurichten. Fahren Sie anschließend mit [Schritt 2: Einrichten Ihres Visual Studio-Projekts](#SetupVS) fort.

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="step-2-set-up-your-visual-studio-project"></a><a id="SetupVS"></a>Schritt 2: Einrichten Ihres Visual Studio-Projekts

1. Öffnen Sie Visual Studio, und wählen Sie **Neues Projekt erstellen** aus.
1. Wählen Sie unter **Neues Projekt erstellen** die Option **Konsolen-App (.NET Framework)** für C# und dann **Weiter** aus.
1. Geben Sie Ihrem Projekt den Namen *CosmosGettingStartedTutorial* , und wählen Sie anschließend **Erstellen** aus.

    :::image type="content" source="./media/sql-api-get-started/configure-cosmos-getting-started-2019.png" alt-text="Konfigurieren des Projekts":::

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die neue Konsolenanwendung, die sich unter Ihrer Visual Studio-Projektmappe befindet, und wählen Sie **NuGet-Pakete verwalten** aus.
1. Wählen Sie im **NuGet-Paket-Manager** die Option **Durchsuchen** aus, und suchen Sie dann nach *Microsoft.Azure.Cosmos*. Wählen Sie **Microsoft.Azure.Cosmos** und dann die Option **Installieren** aus.

   :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-manage-nuget-2019.png" alt-text="Installieren von NuGet für Azure Cosmos DB-Client-SDK":::

   Die Paket-ID für die SQL-API-Clientbibliothek von Azure Cosmos DB lautet [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).

Prima. Damit ist die Einrichtung abgeschlossen und wir können mit dem Schreiben von Code beginnen. Informationen zum abgeschlossenen Projekt dieses Tutorials finden Sie unter [Developing a .NET console app using Azure Cosmos DB](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) (Entwickeln einer .NET-Konsolen-App mit Azure Cosmos DB).

## <a name="step-3-connect-to-an-azure-cosmos-db-account"></a><a id="Connect"></a>Schritt 3: Herstellen einer Verbindung mit einem Azure Cosmos DB-Konto

1. Ersetzen Sie in der Datei *Program.cs* die Verweise am Anfang der C#-Anwendung durch die folgenden Verweise:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Fügen Sie in Ihrer `Program`-Klasse die folgenden Konstanten und Variablen hinzu.

    ```csharp
    public class Program
    {
        // ADD THIS PART TO YOUR CODE

        // The Azure Cosmos DB endpoint for running this sample.
        private static readonly string EndpointUri = "<your endpoint here>";
        // The primary key for the Azure Cosmos account.
        private static readonly string PrimaryKey = "<your primary key>";

        // The Cosmos client instance
        private CosmosClient cosmosClient;

        // The database we will create
        private Database database;

        // The container we will create.
        private Container container;

        // The name of the database and container we will create
        private string databaseId = "FamilyDatabase";
        private string containerId = "FamilyContainer";
    }
    ```

   > [!NOTE]
   > Falls Sie mit der vorherigen Version des .NET SDK vertraut sind, kennen Sie unter Umständen bereits die Begriffe *Sammlung* und *Dokument*. Azure Cosmos DB unterstützt mehrere API-Modelle. Daher werden in Version 3.0 des .NET SDK die generischen Begriffe *Container* und *Element* verwendet. Ein *Container* kann eine Sammlung, ein Graph oder eine Tabelle sein. Ein *Element* kann ein Dokument, ein Edge/Vertex oder eine Zeile sein und stellt den Inhalt eines Containers dar. Weitere Informationen finden Sie unter [Arbeiten mit Datenbanken, Containern und Elementen in Azure Cosmos DB](account-databases-containers-items.md).

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Suchen Sie nach Ihrem Azure Cosmos DB-Konto, und wählen Sie anschließend die Option **Schlüssel** aus.

   :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-portal-keys.png" alt-text="Abrufen der Azure Cosmos DB-Schlüssel über das Azure-Portal":::

1. Ersetzen Sie in *Program.cs* die Zeichenfolge `<your endpoint URL>` durch den Wert von **URI**. Ersetzen Sie `<your primary key>` durch den Wert von **PRIMARY KEY** (PRIMÄRSCHLÜSSEL).

1. Fügen Sie unterhalb der **Main** -Methode eine neue asynchrone Aufgabe mit der Bezeichnung **GetStartedDemoAsync** hinzu. Sie dient zum Instanziieren des neuen `CosmosClient`-Elements.

    ```csharp
    public static async Task Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    /*
        Entry point to call methods that operate on Azure Cosmos DB resources in this sample
    */
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    }
    ```

    **GetStartedDemoAsync** wird als Einstiegspunkt verwendet, der Methoden für Azure Cosmos DB-Ressourcen aufruft.

1. Fügen Sie den folgenden Code hinzu, um die asynchrone Aufgabe **GetStartedDemoAsync** über die **Main** -Methode auszuführen. Die **Main** -Methode fängt Ausnahmen ab und gibt sie in der Konsole aus.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Wählen Sie F5 aus, um die Anwendung auszuführen.

    Die Konsole zeigt die folgende Meldung an: **End of demo, press any key to exit.** (Ende der Demo. Zum Beenden beliebige Taste drücken.). Mit dieser Meldung wird bestätigt, dass Ihre Anwendung eine Verbindung mit Azure Cosmos DB hergestellt hat. Das Konsolenfenster kann nun geschlossen werden.

Glückwunsch! Sie haben erfolgreich eine Verbindung mit einem Azure Cosmos DB-Konto hergestellt.

## <a name="step-4-create-a-database"></a>Schritt 4: Erstellen einer Datenbank

Eine Datenbank ist ein logischer Container für Elemente, die auf Container aufgeteilt sind. Entweder mit der `CreateDatabaseIfNotExistsAsync`- oder der `CreateDatabaseAsync`-Methode der [CosmosClient](/dotnet/api/microsoft.azure.cosmos.cosmosclient)-Klasse kann eine Datenbank erstellt werden.

1. Kopieren Sie die `CreateDatabaseAsync`-Methode, und fügen Sie sie unterhalb Ihrer `GetStartedDemoAsync`-Methode ein.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

    Falls noch nicht vorhanden, wird mit `CreateDatabaseAsync` eine neue Datenbank mit der ID `FamilyDatabase` erstellt, die die ID aus dem Feld `databaseId` enthält.

1. Kopieren Sie den Code, und fügen Sie ihn unterhalb der Stelle ein, an der Sie CosmosClient zum Aufrufen der eben hinzugefügten **CreateDatabaseAsync** -Methode instanziieren.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    Die Datei *Program.cs* sollte nun wie der unten angegebene Code mit eingefügtem Endpunkt und Primärschlüssel aussehen.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStartedTutorial
    {
        class Program
        {
            // The Azure Cosmos DB endpoint for running this sample.
            private static readonly string EndpointUri = "<your endpoint here>";
            // The primary key for the Azure Cosmos account.
            private static readonly string PrimaryKey = "<your primary key>";

            // The Cosmos client instance
            private CosmosClient cosmosClient;

            // The database we will create
            private Database database;

            // The container we will create.
            private Container container;

            // The name of the database and container we will create
            private string databaseId = "FamilyDatabase";
            private string containerId = "FamilyContainer";

            public static async Task Main(string[] args)
            {
                try
                {
                    Console.WriteLine("Beginning operations...");
                    Program p = new Program();
                    await p.GetStartedDemoAsync();
                }
                catch (CosmosException de)
                {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: {0}\n", e);
                }
                finally
                {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
                }
            }

            /// <summary>
            /// Entry point to call methods that operate on Azure Cosmos DB resources in this sample
            /// </summary>
            public async Task GetStartedDemoAsync()
            {
                // Create a new instance of the Cosmos Client
                this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
                await this.CreateDatabaseAsync();
            }

            /// <summary>
            /// Create the database if it does not exist
            /// </summary>
            private async Task CreateDatabaseAsync()
            {
                // Create a new database
                this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
                Console.WriteLine("Created Database: {0}\n", this.database.Id);
            }
        }
    }
    ```

1. Wählen Sie F5 aus, um die Anwendung auszuführen.

   > [!NOTE]
   > Wird der Fehler „503 Dienst nicht verfügbar“ angezeigt, werden die erforderlichen [Ports](sql-sdk-connection-modes.md#service-port-ranges) für den direkten Konnektivitätsmodus unter Umständen durch eine Firewall blockiert. Öffnen Sie zum Beheben des Problems entweder die erforderlichen Ports, oder verwenden Sie die Gatewaymoduskonnektivität wie im folgenden Code gezeigt:
   ```csharp
     // Create a new instance of the Cosmos Client in Gateway mode
     this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey, new CosmosClientOptions()
            {
                ConnectionMode = ConnectionMode.Gateway
            });
   ```

Glückwunsch! Sie haben erfolgreich eine Azure Cosmos-Datenbank erstellt.  

## <a name="step-5-create-a-container"></a><a id="CreateColl"></a>Schritt 5: Erstellen eines Containers

> [!WARNING]
> Mit der `CreateContainerIfNotExistsAsync`-Methode wird ein neuer Container erstellt, und dies wirkt sich auf die Kosten aus. Weitere Informationen finden Sie auf unserer [Preisseite](https://azure.microsoft.com/pricing/details/cosmos-db/).
>
>

Ein Container kann mit der Funktion [**CreateContainerIfNotExistsAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) oder [**CreateContainerAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) in der `CosmosDatabase`-Klasse erstellt werden. Ein Container besteht aus Elementen (JSON-Dokumente im Falle der SQL-API) und zugeordneter serverseitiger Anwendungslogik in JavaScript. Dazu gehören beispielsweise gespeicherte Prozeduren, benutzerdefinierte Funktionen und Auslöser.

1. Kopieren Sie die `CreateContainerAsync`-Methode, und fügen Sie sie unterhalb Ihrer `CreateDatabaseAsync`-Methode ein. Mit `CreateContainerAsync` wird ein neuer Container mit der ID `FamilyContainer` erstellt, falls er nicht bereits vorhanden ist. Hierfür wird die ID verwendet, die im Feld `containerId` angegeben ist (partitioniert mit der `LastName`-Eigenschaft).

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. Kopieren Sie den Code, und fügen Sie ihn unterhalb der Stelle ein, an der Sie CosmosClient zum Aufrufen der eben hinzugefügten **CreateContainer** -Methode instanziiert haben.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();

        //ADD THIS PART TO YOUR CODE
        await this.CreateContainerAsync();
    }
    ```

1. Wählen Sie F5 aus, um die Anwendung auszuführen.

Glückwunsch! Sie haben erfolgreich einen Azure Cosmos-Container erstellt.  

## <a name="step-6-add-items-to-the-container"></a><a id="CreateDoc"></a>Schritt 6: Hinzufügen von Elementen zum Container

Mit der [**CreateItemAsync**](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_)-Methode der `CosmosContainer`-Klasse kann ein Element erstellt werden. Bei Verwendung der SQL-API werden Elemente als Dokumente projiziert. Dabei handelt es sich um beliebigen benutzerdefinierten JSON-Inhalt. Sie können jetzt ein Element in Ihren Azure Cosmos-Container einfügen.

Zunächst erstellen wir eine `Family`-Klasse, mit der in diesem Beispiel in Azure Cosmos DB gespeicherte Objekte dargestellt werden. Darüber hinaus erstellen wir die Unterklassen `Parent`, `Child`, `Pet` und `Address`, die in `Family` genutzt werden. Das Element muss über eine `Id`-Eigenschaft verfügen, die in JSON-Code als `id` serialisiert ist.

1. Drücken Sie STRG+UMSCHALT+A, um die Option **Neues Element hinzufügen** zu öffnen. Fügen Sie Ihrem Projekt eine neue `Family.cs`-Klasse hinzu.

    :::image type="content" source="./media/sql-api-get-started/cosmos-getting-started-add-family-class-2019.png" alt-text="Screenshot: Hinzufügen der neuen Klasse „Family.cs“ zum Projekt":::

1. Kopieren Sie die Klassen `Family`, `Parent`, `Child`, `Pet` und `Address`, und fügen Sie sie in `Family.cs` ein.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]


1. Wechseln Sie zurück zur Datei *Program.cs* , und fügen Sie nach Ihrer `CreateContainerAsync`-Methode die `AddItemsToContainerAsync`-Methode hinzu.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]


    Mit dem Code wird sichergestellt, dass noch kein Element mit der gleichen ID vorhanden ist. Sie fügen zwei Elemente ein: eines für die *Familie Andersen* und eines für die *Familie Wakefield*.

1. Fügen Sie in der `GetStartedDemoAsync` Methode einen Aufruf für `AddItemsToContainerAsync` hinzu.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainerAsync();
    }
    ```

1. Wählen Sie F5 aus, um die Anwendung auszuführen.

Glückwunsch! Sie haben erfolgreich zwei Azure Cosmos-Elemente erstellt.  

## <a name="step-7-query-azure-cosmos-db-resources"></a><a id="Query"></a>Schritt 7: Abfragen von Azure Cosmos DB-Ressourcen

Azure Cosmos DB unterstützt umfassende Abfragen der in jedem Container gespeicherten JSON-Dokumente. Weitere Informationen finden Sie unter [Erste Schritte mit SQL-Abfragen](./sql-query-getting-started.md). Der folgende Beispielcode zeigt, wie Sie eine Abfrage für die im vorherigen Schritt eingefügten Elemente ausführen.

1. Kopieren Sie die `QueryItemsAsync`-Methode, und fügen Sie sie nach Ihrer `AddItemsToContainerAsync`-Methode ein.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. Fügen Sie in der ``GetStartedDemoAsync`` Methode einen Aufruf für ``QueryItemsAsync`` hinzu.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.QueryItemsAsync();
    }
    ```

1. Wählen Sie F5 aus, um die Anwendung auszuführen.

Glückwunsch! Sie haben erfolgreich einen Azure Cosmos-Container abgefragt.

## <a name="step-8-replace-a-json-item"></a><a id="ReplaceItem"></a>Schritt 8: Ersetzen eines JSON-Elements

Sie aktualisieren nun ein Element in Azure Cosmos DB. Sie ändern die `IsRegistered`-Eigenschaft des `Family`-Elements und des `Grade`-Elements von einem der Kinder.

1. Kopieren Sie die `ReplaceFamilyItemAsync`-Methode, und fügen Sie sie nach Ihrer `QueryItemsAsync`-Methode ein.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Fügen Sie in der `GetStartedDemoAsync` Methode einen Aufruf für `ReplaceFamilyItemAsync` hinzu.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();

        //ADD THIS PART TO YOUR CODE
        await this.ReplaceFamilyItemAsync();
    }
    ```

1. Wählen Sie F5 aus, um die Anwendung auszuführen.

Glückwunsch! Sie haben erfolgreich ein Azure Cosmos-Element ersetzt.

## <a name="step-9-delete-item"></a><a id="DeleteDocument"></a>Schritt 9: Delete item (Element löschen)

Als Nächstes löschen Sie ein Element in Azure Cosmos DB.

1. Kopieren Sie die `DeleteFamilyItemAsync`-Methode, und fügen Sie sie nach Ihrer `ReplaceFamilyItemAsync`-Methode ein.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Fügen Sie in der `GetStartedDemoAsync` Methode einen Aufruf für `DeleteFamilyItemAsync` hinzu.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();
        await this.ReplaceFamilyItemAsync();

        //ADD THIS PART TO YOUR CODE
        await this.DeleteFamilyItemAsync();
    }
    ```

1. Wählen Sie F5 aus, um die Anwendung auszuführen.

Glückwunsch! Sie haben erfolgreich ein Azure Cosmos-Element gelöscht.

## <a name="step-10-delete-the-database"></a><a id="DeleteDatabase"></a>Schritt 10: Löschen der Datenbank

Im nächsten Schritt löschen Sie die Datenbank. Wenn Sie die erstellte Datenbank löschen, werden auch alle untergeordneten Ressourcen gelöscht. Zu diesen Ressourcen zählen Container, Elemente sowie alle gespeicherten Prozeduren, benutzerdefinierten Funktionen und Trigger. Außerdem löschen Sie die `CosmosClient`-Instanz.

1. Kopieren Sie die `DeleteDatabaseAndCleanupAsync`-Methode, und fügen Sie sie nach Ihrer `DeleteFamilyItemAsync`-Methode ein.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Fügen Sie in der ``GetStartedDemoAsync`` Methode einen Aufruf für ``DeleteDatabaseAndCleanupAsync`` hinzu.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

1. Wählen Sie F5 aus, um die Anwendung auszuführen.

Glückwunsch! Sie haben erfolgreich eine Azure Cosmos-Datenbank gelöscht.

## <a name="step-11-run-your-c-console-application-all-together"></a><a id="Run"></a>Schritt 11: Ausführen der gesamten C#-Konsolenanwendung!

Drücken Sie in Visual Studio F5, um die Anwendung im Debugmodus zu erstellen und auszuführen.

Die Ausgabe der gesamten App sollte in einem Konsolenfenster angezeigt werden. Die Ausgabe enthält die Ergebnisse der hinzugefügten Abfragen. Sie sollte dem unten angegebenen Beispieltext entsprechen.

```cmd
Beginning operations...

Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.43 RUs.

Created item in database with id: Wakefield.7 Operation consumed 14.29 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

Updated Family [Wakefield,Wakefield.7].
        Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"},{"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6,"Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1,"Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}

Deleted Family [Wakefield,Wakefield.7]

Deleted Database: FamilyDatabase

End of demo, press any key to exit.
```

Glückwunsch! Sie haben dieses Tutorial abgeschlossen und verfügen über eine funktionierende C#-Konsolenanwendung.

## <a name="get-the-complete-tutorial-solution"></a><a id="GetSolution"></a> Herunterladen der vollständigen Projektmappe für das Tutorial

Falls Sie die Schritte in diesem Tutorial aus Zeitmangel nicht durchführen konnten oder nur die Codebeispiele herunterladen möchten, können Sie den Download durchführen.

Für die Erstellung der Projektmappe `GetStarted` müssen die folgenden Voraussetzungen erfüllt sein:

* Ein aktives Azure-Konto. Wenn Sie keines besitzen, können Sie sich für ein [kostenloses Konto](https://azure.microsoft.com/free/)registrieren.
* Ein [Azure Cosmos DB-Konto][cosmos-db-create-account]
* [GetStarted-Projektmappe](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) (erhältlich auf GitHub)

Um die Verweise auf das Azure Cosmos DB .NET SDK in Visual Studio wiederherzustellen, klicken Sie mit der rechten Maustaste im **Projektmappen-Explorer** auf die Projektmappe und wählen dann die Option **NuGet-Pakete wiederherstellen** aus. Aktualisieren Sie als Nächstes in der Datei *App.config* die Werte `EndPointUri` und `PrimaryKey` wie unter [Schritt 3: Herstellen einer Verbindung mit einem Azure Cosmos DB-Konto](#Connect) beschrieben.

Das ist alles, nun müssen Sie nur noch die Erstellung durchführen.

## <a name="next-steps"></a>Nächste Schritte

* Möchten Sie ein komplexeres ASP.NET MVC-Tutorial durcharbeiten? Siehe [Tutorial: Entwickeln einer ASP.NET Core MVC-Webanwendung mit Azure Cosmos DB unter Verwendung des .NET SDK](sql-api-dotnet-application.md).
* Möchten Sie Leistungs- und Skalierungstests mit Azure Cosmos DB durchführen? Weitere Informationen finden Sie unter [Leistungs- und Skalierungstests mit Azure Cosmos DB](performance-testing.md).
* Informationen zur Überwachung von Azure Cosmos DB-Anforderungen sowie der Nutzung und Speicherung finden Sie unter [Überwachen von Leistungs- und Speichermetriken in Azure Cosmos DB](./monitor-cosmos-db.md).
* Abfragen für unser Beispieldataset können Sie über den [Abfrageplayground](https://www.documentdb.com/sql/demo) durchführen.
* Weitere Informationen zu Azure Cosmos DB finden Sie unter [Willkommen bei Azure Cosmos DB](./introduction.md).

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account