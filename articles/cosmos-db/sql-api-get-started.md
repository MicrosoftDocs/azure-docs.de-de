---
title: Erstellen einer .NET-Konsolen-App zum Verwalten von Daten in einem Azure Cosmos DB-SQL-API-Konto
description: In diesem Tutorial werden eine Onlinedatenbank und eine C#-Konsolenanwendung mit der SQL-API erstellt.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: sngun
ms.openlocfilehash: 7574985dbcc502d03bc886c7651c859b22968c5f
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596091"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Erstellen einer .NET-Konsolen-App zum Verwalten von Daten in einem Azure Cosmos DB-SQL-API-Konto

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET (Vorschauversion)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (Vorschauversion)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Willkommen zum Tutorial zu den ersten Schritten mit der SQL-API von Azure Cosmos DB. Nachdem Sie dieses Tutorial abgeschlossen haben, verfügen Sie über eine Konsolenanwendung, mit der Azure Cosmos DB-Ressourcen erstellt und abgefragt werden können.

Dieses Tutorial veranschaulicht folgende Vorgehensweisen:

> [!div class="checklist"]
>
> - Erstellen eines Azure Cosmos DB-Kontos und Herstellen einer Verbindung
> - Konfigurieren einer Visual Studio-Projektmappe
> - Erstellen einer Datenbank
> - Erstellen einer Sammlung
> - Erstellen von JSON-Dokumenten
> - Abfragen der Sammlung
> - Aktualisieren eines JSON-Dokuments
> - Löschen eines Dokuments
> - Löschen der Datenbank

## <a name="prerequisites"></a>Voraussetzungen

Installation von Visual Studio 2017 mit dem Workflow für die Azure-Entwicklung:
- Sie können die **kostenlose** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) herunterladen und verwenden. Aktivieren Sie beim Setup von Visual Studio die Option **Azure-Entwicklung**. 

Azure-Abonnement oder kostenloses Cosmos DB-Testkonto:
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
  
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  
  
- Falls Sie den Azure Cosmos DB-Emulator verwenden, führen Sie die Schritte unter [Azure Cosmos DB-Emulator](local-emulator.md) aus, um den Emulator einzurichten. Starten Sie dann das Tutorial unter [Einrichten einer Visual Studio-Projektmappe](#SetupVS).
  
## <a name="get-the-completed-solution"></a>Abrufen der abgeschlossenen Projektmappe

Wenn Sie keine Zeit haben, das Tutorial abzuschließen, oder nur die Codebeispiele benötigen, können Sie die komplette Projektmappe von [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) herunterladen. 

So führen Sie die heruntergeladene abgeschlossene Projektmappe aus 

1. Stellen Sie sicher, dass die [Voraussetzungen](#prerequisites) installiert sind. 
1. Öffnen Sie die heruntergeladene Projektmappendatei *GetStarted.sln* in Visual Studio.
1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **GetStarted**, und wählen Sie **NuGet-Pakete verwalten** aus.
1. Wählen Sie auf der Registerkarte **NuGet** die Option **Wiederherstellen** aus, um die Verweise auf das Azure Cosmos DB .NET SDK wiederherzustellen.
1. Aktualisieren Sie in der Datei *App.config* die Werte `EndpointUrl` und `PrimaryKey`, wie im Abschnitt [Herstellen einer Verbindung mit dem Azure Cosmos DB-Konto](#Connect) beschrieben.
1. Wählen Sie **Debuggen** > **Starten ohne Debugging** aus, oder drücken Sie **STRG**+**F5**, um die App zu erstellen und auszuführen.

## <a name="create-an-azure-cosmos-db-account"></a>Erstellen eines Azure Cosmos DB-Kontos

Befolgen Sie diese Anweisungen, um ein Azure Cosmos DB-Konto im Azure-Portal zu erstellen. Wenn Sie bereits über ein Azure Cosmos DB-Konto verfügen, das Sie verwenden können, fahren Sie fort mit [Einrichten der Visual Studio-Projektmappe](#SetupVS). 

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Einrichten der Visual Studio-Projektmappe

1. Klicken Sie in Visual Studio 2017 auf **Datei** > **Neu** > **Projekt**.
   
1. Wählen Sie im Dialogfeld **Neues Projekt** die Option **Visual C#**  > **Konsolen-App (.NET Framework)** aus, geben Sie Ihrem Projekt den Namen *AzureCosmosDBApp*, und wählen Sie dann **OK** aus.
   
   ![Screenshot des Fensters „Neues Projekt“](./media/sql-api-get-started/nosql-tutorial-new-project-2.png)
   
1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **AzureCosmosDBApp**, und wählen Sie **NuGet-Pakete verwalten** aus.
   
   ![Kontextmenü „Projekt“](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges.png)
   
1. Wählen Sie auf der Registerkarte **NuGet** die Option **Durchsuchen** aus, und geben Sie in das Suchfeld *azure documentdb* ein.
   
1. Suchen und wählen Sie **Microsoft.Azure.DocumentDB** aus. Anschließend wählen Sie **Installieren** aus, wenn es nicht bereits installiert ist.
   
   Die Paket-ID für die SQL-API-Clientbibliothek von Azure Cosmos DB lautet [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/).

   > [!NOTE]
   > Wenn Sie .NET Core verwenden, beachten Sie die [.NET Core-Dokumentation](./sql-api-dotnetcore-get-started.md).

   ![Screenshot: NuGet-Menü zum Suchen nach dem Azure Cosmos DB-Client-SDK](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)
   
   Wenn Sie eine Meldung im Zusammenhang mit der Überprüfung von Änderungen an der Projektmappe erhalten, wählen Sie **OK** aus. Wenn Sie eine Meldung im Zusammenhang mit der Akzeptierung der Lizenz erhalten, wählen Sie **Ich stimme zu** aus.

## <a id="Connect"></a>Herstellen einer Verbindung mit dem Azure Cosmos DB-Konto

Beginnen Sie jetzt mit dem Schreiben von Code. Die vollständige Datei *Project.cs* für dieses Tutorial finden Sie auf [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs).

1. Wählen Sie im **Projektmappen-Explorer** *Program.cs* aus, und fügen Sie im Code-Editor die folgenden Verweise am Anfang der Datei hinzu:
   
   ```csharp
   using System.Net;
   using Microsoft.Azure.Documents;
   using Microsoft.Azure.Documents.Client;
   using Newtonsoft.Json;
   ```
   
1. Als nächstes fügen Sie die folgenden beiden Konstanten und die Variable `client` zu `public class Program` hinzu.
   
   ```csharp
   
   public class Program
   {
      private const string EndpointUrl = "<your endpoint URL>";
      private const string PrimaryKey = "<your primary key>";
      private DocumentClient client;
   ```
   
1. Die Endpunkt-URL und der Primärschlüssel ermöglichen es Ihrer App, sich mit Ihrem Azure Cosmos DB-Konto zu verbinden, und dem Azure Cosmos DB-Konto, der Verbindung zu vertrauen. Kopieren Sie die Schlüssel aus dem [Azure-Portal](https://portal.azure.com), und fügen Sie sie in Ihren Code ein. 

   
   1. Wählen Sie im linken Navigationsbereich Ihres Azure Cosmos DB-Kontos die Option **Schlüssel**.
      
      ![Anzeigen und Kopieren von Zugriffsschlüsseln im Azure-Portal](./media/sql-api-get-started/nosql-tutorial-keys.png)
      
   1. Kopieren Sie unter **Lese-/Schreibschlüssel** den Wert von **URI**, indem Sie rechts die Schaltfläche „Kopieren“ verwenden, und fügen Sie ihn in `<your endpoint URL>` in *Program.cs* ein. Beispiel:  
      
      `private const string EndpointUrl = "https://mysqlapicosmosdb.documents.azure.com:443/";`
      
   1. Kopieren Sie den Wert von **PRIMÄRSCHLÜSSEL**, und fügen Sie ihn in `<your primary key>` in *Program.cs* ein. Beispiel:  
      
      `private const string PrimaryKey = "19ZDNJAiYL26tmnRvoez6hmtIfBGwjun50PWRjNYMC2ig8Ob9hYk7Fq1RYSv8FcIYnh1TdBISvCh7s6yyb0000==";`
   
1. Fügen Sie nach der `Main`-Methode eine neue asynchrone Aufgabe namens `GetStartedDemo` hinzu, die ein neues `DocumentClient` namens `client` instanziiert.
   
   ```csharp
      private async Task GetStartedDemo()
      {
        client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
      }
   ```

   Wenn Sie ein Proxyobjekt verwenden, um eine Verbindung mit Azure Cosmos DB herzustellen, sollten Sie stattdessen den folgenden Codeblock verwenden, um das DocumentClient-Objekt zu erstellen. Für das Beispiel in diesem Dokument wird kein Proxyobjekt verwendet, somit dient das folgende Beispiel nur zur Veranschaulichung:

   ```csharp
   HttpClientHandler handler = new HttpClientHandler()
   {
     Proxy = proxyObject
     UseProxy = true,
   };

   //Pass handler to the constructor of DocumentClient.
   DocumentClient client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey, handler);
   
   ```
   
1. Fügen Sie den folgenden Code zur Methode `Main` hinzu, um die Aufgabe `GetStartedDemo` auszuführen. Die `Main`-Methode fängt Ausnahmen ab und gibt sie in der Konsole aus.
   
   ```csharp
      static void Main(string[] args)
      {
        try
        {
           Program p = new Program();
           p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine($"{de.StatusCode} error occurred: {de.Message}, Message: {baseException.Message}");
        }
        catch (Exception e)
        {
           Exception baseException = e.GetBaseException();
           Console.WriteLine($"Error: {e.Message}, Message: {baseException.Message}");
        }
        finally
        {
           Console.WriteLine("End of demo, press any key to exit.");
           Console.ReadKey();
        }
      }
   ```
   
1. Drücken Sie **F5**, um die App auszuführen. 
   
1. Wenn die Meldung **End of demo, press any key to exit** (Ende der Demo. Zum Beenden beliebige Taste drücken) im Konsolenfenster angezeigt wird, wurde die Verbindung erfolgreich hergestellt. Drücken Sie eine beliebige Taste, um das Konsolenfenster zu schließen. 

Sie haben erfolgreich eine Verbindung mit Ihrem Azure Cosmos DB-Konto hergestellt. Arbeiten Sie jetzt mit einigen Azure Cosmos DB-Ressourcen.  

## <a name="create-a-database"></a>Erstellen einer Datenbank

Eine Azure Cosmos DB-[Datenbank](databases-containers-items.md#azure-cosmos-databases) ist ein logischer Container für JSON-Dokumentspeicher, der auf Sammlungen aufgeteilt ist. Sie erstellen eine Datenbank, indem Sie die Methode [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync) der Klasse `DocumentClient` verwenden. 

1. Bevor Sie den Code zum Erstellen einer Datenbank hinzufügen, fügen Sie eine Hilfsmethode zum Schreiben in die Konsole hinzu. Kopieren Sie die folgende `WriteToConsoleAndPromptToContinue`-Methode und fügen Sie sie nach der `GetStartedDemo`-Methode in Ihren Code ein.
   
   ```csharp
   private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
   {
      Console.WriteLine(format, args);
      Console.WriteLine("Press any key to continue...");
      Console.ReadKey();
   }
   ```
   
1. Kopieren Sie die folgende Zeile und fügen Sie sie nach der Zeile `client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);` in Ihre `GetStartedDemo`-Methode ein. Dieser Code erstellt eine Datenbank namens `FamilyDB`.
   
   ```csharp
      await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });
   ```
   
1. Drücken Sie **F5**, um die App auszuführen.

Sie haben erfolgreich eine Azure Cosmos DB-Datenbank erstellt. Die Datenbank wird im [Azure-Portal](https://portal.azure.com) angezeigt, wenn Sie **Daten-Explorer** im linken Navigationsbereich Ihres Azure Cosmos DB-Kontos auswählen. 

## <a id="CreateColl"></a>Erstellen einer Sammlung

Eine Sammlung ist ein Container für JSON-Dokumente und die zugehörige JavaScript-Anwendungslogik. Sie erstellen eine Sammlung, indem Sie die Methode [CreateDocumentCollectionIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync#overloads) der Klasse `DocumentClient` verwenden. 

> [!IMPORTANT]
> **CreateDocumentCollectionIfNotExistsAsync** erstellt eine neue Sammlung mit reserviertem Durchsatz. Dies wirkt sich auf die Kosten aus. Weitere Informationen finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 

1. Kopieren Sie den folgenden Code und fügen Sie ihn nach der Zeile `await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });` in Ihre `GetStartedDemo`-Methode ein. Dieser Code erstellt eine Dokumentsammlung namens `FamilyCollection`.
   
   ```csharp
      await client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });
   ```
   
1. Drücken Sie **F5**, um die App auszuführen.

Sie haben erfolgreich eine Azure Cosmos DB-Dokumentsammlung erstellt. Die Sammlung wird unter Ihrer **FamilyDB**-Datenbank im **Daten-Explorer** im Azure-Portal angezeigt.  

## <a id="CreateDoc"></a>Erstellen von JSON-Dokumenten

Dokumente sind benutzerdefinierte beliebige JSON-Inhalte. Dokumente müssen über eine ID-Eigenschaft verfügen, die in JSON als `id` serialisiert wird. Sie erstellen Dokumente mit der Methode [CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync#overloads) der Klasse `DocumentClient`. 

> [!TIP]
> Wenn Sie bereits Daten besitzen, die Sie in Ihrer Datenbank speichern möchten, können Sie die Daten mithilfe des Azure Cosmos DB-[Datenmigrationstools](import-data.md) importieren.
>

Der folgende Code erstellt zwei Dokumente und fügt sie in Ihre Datenbanksammlung ein. Zuerst erstellen Sie eine `Family`-Klasse sowie die Unterklassen `Parent`, `Child`, `Pet` und `Address`, die Sie in `Family` verwenden können. Anschließend erstellen Sie eine `CreateFamilyDocumentIfNotExists`-Methode und erstellen dann zwei Dokumente, die Sie einfügen. 

1. Kopieren Sie die folgenden Klassen `Family`, `Parent`, `Child`, `Pet` und `Address`. Fügen Sie sie dann nach der `WriteToConsoleAndPromptToContinue`-Methode in Ihren Code ein.
   
   ```csharp
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        public override string ToString()
        {
            return JsonConvert.SerializeObject(this);
        }
    }

    public class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    public class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    public class Pet
    {
        public string GivenName { get; set; }
    }

    public class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }
   ```
   
1. Kopieren Sie die folgende `CreateFamilyDocumentIfNotExists`-Methode und fügen Sie sie nach der Klasse `Address` ein, die Sie gerade hinzugefügt haben.
   
   ```csharp
    private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
    {
        try
        {
            await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
            WriteToConsoleAndPromptToContinue($"Found {family.Id}");
        }
        catch (DocumentClientException de)
        {
            if (de.StatusCode == HttpStatusCode.NotFound)
            {
                await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
                WriteToConsoleAndPromptToContinue($"Created Family {family.Id}");
            }
            else
            {
                throw;
            }
        }
    }
   ```
   
1. Kopieren Sie den folgenden Code und fügen Sie ihn am Ende Ihrer `GetStartedDemo`-Methode nach der Zeile `await client.CreateDocumentCollectionIfNotExistsAsync` ein. Dieser Code erstellt zwei Dokumente und fügt sie ein: eines für die Familie Andersen und eines für die Familie Wakefield.
   
   ```csharp
    Family andersenFamily = new Family
    {
        Id = "AndersenFamily",
        LastName = "Andersen",
        Parents = new Parent[]
        {
            new Parent { FirstName = "Thomas" },
            new Parent { FirstName = "Mary Kay" }
        },
        Children = new Child[]
        {
            new Child
            {
                FirstName = "Henriette Thaulow",
                Gender = "female",
                Grade = 5,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Fluffy" }
                }
            }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = true
    };

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", andersenFamily);

    Family wakefieldFamily = new Family
    {
        Id = "WakefieldFamily",
        LastName = "Wakefield",
        Parents = new Parent[]
        {
            new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
            new Parent { FamilyName = "Miller", FirstName = "Ben" }
        },
        Children = new Child[]
        {
            new Child
            {
                FamilyName = "Merriam",
                FirstName = "Jesse",
                Gender = "female",
                Grade = 8,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Goofy" },
                    new Pet { GivenName = "Shadow" }
                }
            },
            new Child
            {
                FamilyName = "Miller",
                FirstName = "Lisa",
                Gender = "female",
                Grade = 1
            }
        },
        Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
        IsRegistered = false
    };

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);
   ```
   
1. Drücken Sie **F5**, um die App auszuführen.

Sie haben erfolgreich zwei Azure Cosmos DB-Dokumente erstellt. Die Dokumente werden unter Ihrer **FamilyDB**-Datenbank und **FamilyCollection**-Sammlung im **Daten-Explorer** im Azure-Portal angezeigt.   

![Diagramm zur Veranschaulichung der hierarchischen Beziehung zwischen dem Konto, der Datenbank, der Sammlung und den Dokumenten](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Abfragen von Azure Cosmos DB-Ressourcen

Azure Cosmos DB unterstützt umfassende [Abfragen](how-to-sql-query.md) der in Sammlungen gespeicherten JSON-Dokumente. Der folgende Beispielcode verwendet die LINQ- und Azure Cosmos DB SQL-Syntax, um eine Abfrage für die Beispieldokumente auszuführen.

1. Kopieren Sie die folgende `ExecuteSimpleQuery`-Methode und fügen Sie sie nach der `CreateFamilyDocumentIfNotExists`-Methode in Ihren Code ein.
   
   ```csharp
    private void ExecuteSimpleQuery(string databaseName, string collectionName)
    {
        // Set some common query options.
        FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Find the Andersen family by its LastName.
        IQueryable<Family> familyQuery = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
            .Where(f => f.LastName == "Andersen");

        // Execute the query synchronously. 
        // You could also execute it asynchronously using the IDocumentQuery<T> interface.
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine($"\tRead {family}");
        }

        // Now execute the same query using direct SQL.
        IQueryable<Family> familyQueryInSql = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
            "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
            queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
            Console.WriteLine($"\tRead {family}");
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
    }
   ```
   
1. Kopieren Sie den folgenden Code und fügen Sie ihn am Ende Ihrer `GetStartedDemo`-Methode nach der Zeile `await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);` ein.
   
   ```csharp
      ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. Drücken Sie **F5**, um die App auszuführen.

Die vorherige Abfrage gibt das komplette Element für die Familie Andersen zurück. Sie haben erfolgreich eine Abfrage für eine Azure Cosmos DB-Sammlung durchgeführt.

Das folgende Diagramm veranschaulicht, wie die Azure Cosmos DB-SQL-Abfragesyntax für die Sammlung aufgerufen wird. Die gleiche Logik gilt für die LINQ-Abfrage.

![Diagramm zur Veranschaulichung des Bereichs und der Bedeutung der Abfrage, die vom NoSQL-Tutorial zum Erstellen einer C#-Konsolenanwendung verwendet wird.](./media/sql-api-get-started/nosql-tutorial-collection-documents.png)

Das Schlüsselwort [FROM](how-to-sql-query.md#FromClause) in der SQL-Abfrage ist optional, da Azure Cosmos DB bereits Abfragen auf eine einzelne Sammlung begrenzt. Sie können `FROM Families f` mit `FROM root r` oder einem anderen Variablennamen Ihrer Wahl austauschen. Azure Cosmos DB leitet ab, dass `Families`, `root` oder der variable Name, den Sie ausgewählt haben, auf die aktuelle Sammlung verweist bzw. verweisen.

## <a id="ReplaceDocument"></a>Aktualisieren eines JSON-Dokuments

Die Azure Cosmos DB SQL-API unterstützt das Aktualisieren und Ersetzen von JSON-Dokumenten.  

1. Kopieren Sie die folgende `ReplaceFamilyDocument`-Methode und fügen Sie sie nach der `ExecuteSimpleQuery`-Methode in Ihren Code ein.
   
   ```csharp
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
       await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
       WriteToConsoleAndPromptToContinue($"Replaced Family {familyName}");
    }
   ```
   
1. Kopieren Sie den folgenden Code und fügen Sie ihn am Ende Ihrer `GetStartedDemo`-Methode nach der Zeile `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");` ein. Der Code aktualisiert die Daten in einem der Dokumente und führt dann die Abfrage erneut aus, um das geänderte Dokument anzuzeigen.
   
   ```csharp
   // Update the Grade of the Andersen Family child
   andersenFamily.Children[0].Grade = 6;
   await ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily", andersenFamily);
   ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. Drücken Sie **F5**, um die App auszuführen.

Die Ausgabe der Abfrage zeigt, dass `Grade` für das Kind der Familie Andersen von `5` auf `6` aktualisiert wurde. Sie haben ein Azure Cosmos DB-Dokument erfolgreich aktualisiert und ersetzt. 

## <a id="DeleteDocument"></a>Löschen eines JSON-Dokuments

Die Azure Cosmos DB SQL-API unterstützt das Löschen von JSON-Dokumenten.  

1. Kopieren Sie die folgende `DeleteFamilyDocument`-Methode und fügen Sie sie nach der `ReplaceFamilyDocument`-Methode ein.
   
   ```csharp
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
        await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
        Console.WriteLine($"Deleted Family {documentName}");
    }
   ```
   
1. Kopieren Sie den folgenden Code und fügen Sie ihn am Ende Ihrer `GetStartedDemo`-Methode nach der zweiten Zeile `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");` ein.
   
   ```csharp
   await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");
   ```
   
1. Drücken Sie **F5**, um die App auszuführen.

Sie haben erfolgreich ein Azure Cosmos DB-Dokument gelöscht. 

## <a id="DeleteDatabase"></a>Löschen der Datenbank

Löschen Sie die von Ihnen erstellte Datenbank, um sie und alle ihre untergeordneten Ressourcen, einschließlich der Sammlung und der Dokumente, zu entfernen. 

1. Kopieren Sie den folgenden Code und fügen Sie ihn am Ende Ihrer `GetStartedDemo`-Methode nach der Zeile `await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");` ein. 
   
   ```csharp
   // Clean up - delete the database
   await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));
   ```
   
1. Drücken Sie **F5**, um die App auszuführen.

Sie haben erfolgreich die Azure Cosmos DB-Datenbank gelöscht. Sie können im **Daten-Explorer** für Ihr Azure Cosmos DB-Konto sehen, dass die FamilyDB-Datenbank gelöscht wird. 

## <a id="Run"></a>Ausführen der gesamten C#-Konsolenanwendung

Drücken Sie in Visual Studio **F5**, um die vollständige C#-Konsolenanwendung im Debugmodus zu erstellen und auszuführen. Die folgende Ausgabe sollte im Konsolenfenster angezeigt werden:

```bash
Created Family AndersenFamily
Press any key to continue ...
 Created Family WakefieldFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Replaced Family AndersenFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Deleted Family AndersenFamily
End of demo, press any key to exit.
```

Glückwunsch! Sie haben das Tutorial abgeschlossen und verfügen über eine funktionierende C#-Konsolenanwendung, die Azure Cosmos DB-Ressourcen erstellt, abfragt, aktualisiert und löscht.  

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zu Azure Cosmos DB finden Sie unter [Willkommen bei Azure Cosmos DB](introduction.md).
* Ein komplexeres ASP.NET MVC-Tutorial finden Sie unter [ASP.NET MVC-Tutorial: Entwicklung von Webanwendungen mit Azure Cosmos DB](sql-api-dotnet-application.md).
* Informationen zum Durchführen von Leistungs- und Skalierungstests mit Azure Cosmos DB finden Sie unter [Leistungs- und Skalierungstests mit Azure Cosmos DB](performance-testing.md).
* Informationen zum Überwachen von Anforderungen, Nutzung und Speicher in Azure Cosmos DB finden Sie unter [Überwachen von Konten](monitor-accounts.md).
* Führen Sie im [Query Playground](https://www.documentdb.com/sql/demo) Abfragen für ein Beispieldataset durch.

