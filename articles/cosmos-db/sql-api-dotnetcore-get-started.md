---
title: 'Tutorial: Erstellen einer .NET Core-App zum Verwalten von Daten, die im SQL-API-Konto für Azure Cosmos DB gespeichert sind'
description: In diesem Tutorial werden eine Onlinedatenbank und eine C#-Konsolenanwendung mit dem SQL-API-.NET Core SDK für Azure Cosmos DB erstellt.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/21/2019
ms.author: sngun
Customer intent: As a developer, I want to build a .NET Core application to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities that Azure Cosmos DB offers.
ms.openlocfilehash: fdd66a28ea38e7ae34ce70958ac57f29bc66ceb7
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969248"
---
# <a name="tutorial-build-a-net-core-app-to-manage-data-stored-in-a-sql-api-account"></a>Tutorial: Erstellen einer .NET Core-App zum Verwalten von Daten, die im SQL-API-Konto gespeichert sind

> [!div class="op_single_selector"]
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (Vorschauversion)](sql-api-dotnet-core-get-started-preview.md)
> * [.NET](sql-api-get-started.md)
> * [.NET (Vorschauversion)](sql-api-dotnet-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Als Entwickler besitzen Sie möglicherweise Anwendungen, die NoSQL-Dokumentdaten verwenden. Sie können das SQL-API-Konto in Azure Cosmos DB verwenden, um diese Dokumentdaten zu speichern und darauf zuzugreifen. In diesem Tutorial erfahren Sie, wie Sie eine .NET Core-App zum Erstellen und Abfragen von Daten erstellen, die im SQL-API-Konto von Azure Cosmos DB gespeichert sind. 

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Erstellen eines Azure Cosmos-Kontos und Herstellen der Verbindung
> * Konfigurieren Ihrer Visual Studio-Projektmappe
> * Erstellen einer Onlinedatenbank
> * Erstellen einer Sammlung
> * Erstellen von JSON-Dokumenten
> * Ausführen von CRUD-Vorgängen für die Elemente, den Container und die Datenbank

Sie haben keine Zeit, um die Anwendung zu erstellen? Die vollständige Lösung ist auf [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started)verfügbar. 

## <a name="prerequisites"></a>Voraussetzungen

* Ein aktives Azure-Konto. Wenn Sie keines besitzen, können Sie sich für ein [kostenloses Konto](https://azure.microsoft.com/free/)registrieren. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Sie können die kostenlose [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) herunterladen und verwenden. Wenn Sie eine App für die universelle Windows-Plattform (UWP) entwickeln, verwenden Sie **Visual Studio 2017 mit Version 15.4** oder höher. Aktivieren Sie beim Setup von Visual Studio die Option **Azure-Entwicklung**.

    * Für MacOS oder Linux können Sie .NET Core-Apps über die Befehlszeile entwickeln, indem Sie das [.NET Core SDK](https://www.microsoft.com/net/core#macos) für die Plattform Ihrer Wahl installieren. 

    * Für Windows können Sie .NET Core-Apps über die Befehlszeile entwickeln, indem Sie das [.NET Core SDK](https://www.microsoft.com/net/core#windows) installieren. 

## <a name="create-an-azure-cosmos-account"></a>Erstellen eines Azure Cosmos-Kontos

Erstellen Sie mit den folgenden Schritten ein Azure Cosmos-Konto:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Einrichten Ihrer Visual Studio-Projektmappe

1. Öffnen Sie auf Ihrem Computer **Visual Studio 2017**.

2. Wählen Sie im Menü **Datei** die Option **Neu** und anschließend **Projekt** aus.

3. Navigieren Sie im Dialogfeld **Neues Projekt** zu **Vorlagen** > **Visual C#** > **.NET Core** > **Konsolenanwendung (.NET Core)**, nennen Sie Ihr Projekt **DocumentDBGettingStarted**, und wählen Sie anschließend **OK** aus.

   ![Screenshot des Fensters „Neues Projekt“](./media/sql-api-dotnetcore-get-started/nosql-tutorial-new-project-2.png)

4. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **DocumentDBGettingStarted**.

5. Wählen Sie im gleichen Menü die Option **NuGet-Pakete verwalten** aus.

   ![Screenshot des Kontextmenüs für das Projekt](./media/sql-api-dotnetcore-get-started/nosql-tutorial-manage-nuget-pacakges.png)

6. Wählen Sie auf der Registerkarte **NuGet** im oberen Fensterbereich die Option **Durchsuchen** aus, und geben Sie **azure documentdb** in das Suchfeld ein. Stellen Sie sicher, dass das Kontrollkästchen **Vorabversion einbeziehen** aktiviert ist.

7. Suchen Sie in den Ergebnissen nach **Microsoft.Azure.DocumentDB.Core**, und wählen Sie **Installieren** aus.

   Die Paket-ID für die Bibliothek lautet [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core). Wenn Sie eine .NET Framework-Version (etwa net461) verwenden möchten, die von diesem .NET Core NuGet-Paket nicht unterstützt wird, verwenden Sie das Paket [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB), das alle .NET Framework-Versionen ab .NET Framework 4.5 unterstützt.

8. Akzeptieren Sie die NuGet-Paketinstallationen und den Lizenzvertrag, wenn Sie dazu aufgefordert werden.

Nachdem nun die Einrichtung abgeschlossen ist, beginnen wir damit, Code zu schreiben. Ein vollständiges Codeprojekt dieses Tutorials finden Sie auf [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-core-getting-started).

## <a id="Connect"></a>Herstellen einer Verbindung mit einem Azure Cosmos-Konto

Stellen Sie eine Verbindung mit einem Azure Cosmos-Konto her, indem Sie die erforderlichen Abhängigkeiten importieren. Um die Abhängigkeiten zu importieren, fügen Sie den folgenden Code am Anfang Ihrer Datei „Program.cs“ hinzu:

```csharp
using System;

// ADD THIS PART TO YOUR CODE
using System.Linq;
using System.Threading.Tasks;
using System.Net;
using Microsoft.Azure.Documents;
using Microsoft.Azure.Documents.Client;
using Newtonsoft.Json;
```

Fügen Sie als nächstes diese beiden Konstanten und Ihre Variable *Client* unter der öffentlichen *Program*-Klasse hinzu.

```csharp
class Program
{
    // ADD THIS PART TO YOUR CODE
    private const string EndpointUri = "<your endpoint URI>";
    private const string PrimaryKey = "<your key>";
    private DocumentClient client;
```

Navigieren Sie als Nächstes zum [Azure-Portal](https://portal.azure.com), um Ihren URI und den Primärschlüssel abzurufen. Der URI und der Primärschlüssel von Azure Cosmos DB sind erforderlich, damit Ihre Anwendung weiß, womit die Verbindung hergestellt werden soll, und damit Azure Cosmos DB weiß, dass die Verbindung Ihrer Anwendung vertrauenswürdig ist.

Navigieren Sie im Azure-Portal zu Ihrem Azure Cosmos-Konto, und wählen Sie **Schlüssel** aus.

Kopieren Sie den URI vom Portal, und fügen Sie ihn in `<your endpoint URI>` in der Datei „program.cs“ ein. Kopieren Sie anschließend den PRIMÄRSCHLÜSSEL aus dem Portal, und fügen Sie ihn in `<your key>` ein. Entfernen Sie „<“ und „>“, behalten Sie aber die doppelten Anführungszeichen um Ihren Endpunkt und Schlüssel bei.

![Abrufen von Schlüsseln aus dem Azure-Portal][keys]

Zu Beginn erstellen wir für die Anwendung zu den ersten Schritten eine neue Instanz von **DocumentClient**.

Fügen Sie unterhalb der **Main**-Methode die neue asynchrone Aufgabe mit der Bezeichnung **GetStartedDemo** hinzu. Sie dient zum Instanziieren Ihres neuen Elements vom Typ **DocumentClient**.

```csharp
static void Main(string[] args)
{
}

// ADD THIS PART TO YOUR CODE
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);
}
```

Fügen Sie den folgenden Code hinzu, um die asynchrone Aufgabe über die **Main** -Methode auszuführen. Die **Main**-Methode fängt Ausnahmen ab und gibt sie in der Konsole aus.

```csharp
static void Main(string[] args)
{
        // ADD THIS PART TO YOUR CODE
        try
        {
                Program p = new Program();
                p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
                Exception baseException = de.GetBaseException();
                Console.WriteLine("{0} error occurred: {1}, Message: {2}", de.StatusCode, de.Message, baseException.Message);
        }
        catch (Exception e)
        {
                Exception baseException = e.GetBaseException();
                Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
        }
        finally
        {
                Console.WriteLine("End of demo, press any key to exit.");
                Console.ReadKey();
        }
```

Wählen Sie die Schaltfläche **DocumentDBGettingStarted** aus, um die Anwendung zu erstellen und auszuführen.

## <a id="CreateDatabase"></a>Erstellen einer Datenbank

Bevor Sie den Code zum Erstellen einer Datenbank hinzufügen, fügen Sie eine Hilfsmethode zum Schreiben in die Konsole hinzu. Kopieren Sie die **WriteToConsoleAndPromptToContinue**-Methode, und fügen Sie sie unterhalb der **GetStartedDemo**-Methode ein.

```csharp
// ADD THIS PART TO YOUR CODE
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

Erstellen Sie Ihre Azure Cosmos DB-Datenbank mithilfe der `CreateDatabaseAsync`-Methode der **DocumentClient**-Klasse. Eine Datenbank ist ein logischer Container für JSON-Dokumentspeicher, der auf Sammlungen aufgeteilt ist. Kopieren Sie den folgenden Code, und fügen Sie ihn in die **GetStartedDemo** -Methode unterhalb der Clienterstellung ein. Eine Datenbank mit dem Namen *FamilyDB*wird erstellt.

```csharp
private async Task GetStartedDemo()
{
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });
```

Wählen Sie die Schaltfläche **DocumentDBGettingStarted** aus, um die Anwendung auszuführen.

## <a id="CreateColl"></a>Erstellen einer Sammlung

Erstellen Sie eine Sammlung mithilfe der `CreateDocumentCollectionAsync`-Methode der **DocumentClient**-Klasse. Eine Sammlung ist ein Container für JSON-Dokumente und die zugehörige JavaScript-Anwendungslogik.

> [!WARNING]
> **CreateDocumentCollectionAsync** erstellt eine neue Sammlung mit reserviertem Durchsatz. Dies wirkt sich auf die Kosten aus. Weitere Informationen finden Sie auf der [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/cosmos-db/).

Kopieren Sie den folgenden Code, und fügen Sie ihn unterhalb der Datenbankerstellung in die **GetStartedDemo** -Methode ein. Dieser Code erstellt eine Dokumentsammlung namens *FamilyCollection_oa*.

```csharp
    this.client = new DocumentClient(new Uri(EndpointUri), PrimaryKey);

    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });

    // ADD THIS PART TO YOUR CODE
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"), new DocumentCollection { Id = "FamilyCollection_oa" });
```

Wählen Sie die Schaltfläche **DocumentDBGettingStarted** aus, um die Anwendung auszuführen.

## <a id="CreateDoc"></a>Erstellen von JSON-Dokumenten

Erstellen Sie ein Dokument mithilfe der `CreateDocumentAsync`-Methode der **DocumentClient**-Klasse. Dokumente sind benutzerdefinierter (beliebiger) JSON-Inhalt. Sie können jetzt eines oder mehrere Dokumente einfügen. 

Als Erstes erstellen Sie eine **Family**-Klasse, die in Azure Cosmos DB gespeicherte Objekte darstellt. Außerdem erstellen Sie die Unterklassen **Parent**, **Child**, **Pet** und **Address**, die in **Family** verwendet werden. Die Dokumente müssen über eine **Id**-Eigenschaft verfügen, die in JSON als **id** serialisiert wird. Erstellen Sie diese Klassen, indem Sie die folgenden internen Unterklassen nach der Methode **GetStartedDemo** hinzufügen. Kopieren Sie die Klassen **Family**, **Parent**, **Child**, **Pet** und **Address**, und fügen Sie sie unterhalb der **WriteToConsoleAndPromptToContinue**-Methode ein.

```csharp
private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
{
    Console.WriteLine(format, args);
    Console.WriteLine("Press any key to continue ...");
    Console.ReadKey();
}

// ADD THIS PART TO YOUR CODE
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

Kopieren Sie die **CreateFamilyDocumentIfNotExists**-Methode, und fügen Sie sie unterhalb der **CreateDocumentCollectionIfNotExists**-Methode ein.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
{
    try
    {
        await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
        this.WriteToConsoleAndPromptToContinue("Found {0}", family.Id);
    }
    catch (DocumentClientException de)
    {
        if (de.StatusCode == HttpStatusCode.NotFound)
        {
            await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
            this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
        }
        else
        {
            throw;
        }
    }
}
```

Fügen Sie zwei Dokumente ein: eines für die Familie Andersen und eines für die Familie Wakefield. Kopieren Sie den auf `// ADD THIS PART TO YOUR CODE` folgenden Code, und fügen Sie ihn unterhalb der Erstellung der Dokumentsammlung in die **GetStartedDemo**-Methode ein.

```csharp
    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB_oa" });

await this.CreateDocumentCollectionIfNotExists("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
Family andersenFamily = new Family
{
        Id = "Andersen.1",
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

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", andersenFamily);

Family wakefieldFamily = new Family
{
        Id = "Wakefield.7",
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

await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);
```

Wählen Sie die Schaltfläche **DocumentDBGettingStarted** aus, um die Anwendung auszuführen.

![Hierarchische Beziehung zwischen dem Konto, der Onlinedatenbank und der Sammlung](./media/sql-api-dotnetcore-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Abfragen von Azure Cosmos DB-Ressourcen

Azure Cosmos DB unterstützt umfassende Abfragen der in jeder Sammlung gespeicherten JSON-Dokumente. Der folgende Beispielcode enthält die verschiedenen Abfragen – sowohl per SQL-Syntax von Azure Cosmos DB als auch per LINQ –, die Sie nun für die im vorherigen Schritt eingefügten Dokumente ausführen können.

Kopieren Sie die **ExecuteSimpleQuery**-Methode, und fügen Sie sie unterhalb der **CreateFamilyDocumentIfNotExists**-Methode ein.

```csharp
// ADD THIS PART TO YOUR CODE
private void ExecuteSimpleQuery(string databaseName, string collectionName)
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Here we find the Andersen family via its LastName
        IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
                .Where(f => f.LastName == "Andersen");

        // The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine("\tRead {0}", family);
        }

        // Now execute the same query via direct SQL
        IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
                UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
                "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
                queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
                Console.WriteLine("\tRead {0}", family);
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
}
```

Kopieren Sie den folgenden Code, und fügen Sie ihn unterhalb der Erstellung des zweiten Dokuments in die **GetStartedDemo** -Methode ein.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

// ADD THIS PART TO YOUR CODE
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Wählen Sie die Schaltfläche **DocumentDBGettingStarted** aus, um die Anwendung auszuführen.

Das folgende Diagramm veranschaulicht, wie die Azure Cosmos DB-SQL-Abfragesyntax für die erstellte Sammlung aufgerufen wird. Die gleiche Logik gilt für die LINQ-Abfrage.

![Diagramm zur Veranschaulichung des Bereichs und der Bedeutung der Abfrage, die vom NoSQL-Tutorial zum Erstellen einer C#-Konsolenanwendung verwendet wird.](./media/sql-api-dotnetcore-get-started/nosql-tutorial-collection-documents.png)

Das Schlüsselwort `FROM` ist in der Abfrage optional, da Azure Cosmos DB bereits Abfragen auf eine einzelne Sammlung begrenzt. Aus diesem Grund ist "FROM Familien f" austauschbar mit "FROM Stamm r" oder einem anderen variablen Namen, den Sie auswählen. Azure Cosmos DB leitet ab, dass Familien, der Stamm oder der variable Name, den Sie ausgewählt haben, standardmäßig auf die aktuelle Sammlung verweist bzw. verweisen.

## <a id="ReplaceDocument"></a>Ersetzen eines JSON-Dokuments

Azure Cosmos DB unterstützt das Ersetzen von JSON-Dokumenten.  

Kopieren Sie die **ReplaceFamilyDocument**-Methode, und fügen Sie sie unterhalb der **ExecuteSimpleQuery**-Methode ein.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
{
    await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
    this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
}
```

Kopieren Sie den folgenden Code, und fügen Sie ihn unterhalb der Abfrageausführung in die **GetStartedDemo** -Methode ein. Nach dem Ersetzen des Dokuments wird dieselbe Abfrage erneut ausgeführt, um das geänderte Dokument anzuzeigen.

```csharp
await this.CreateFamilyDocumentIfNotExists("FamilyDB_oa", "FamilyCollection_oa", wakefieldFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO YOUR CODE
// Update the Grade of the Andersen Family child
andersenFamily.Children[0].Grade = 6;

await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");
```

Wählen Sie die Schaltfläche **DocumentDBGettingStarted** aus, um die Anwendung auszuführen.

## <a id="DeleteDocument"></a>Löschen eines JSON-Dokuments

Azure Cosmos DB unterstützt das Löschen von JSON-Dokumenten.  

Kopieren Sie die **DeleteFamilyDocument**-Methode, und fügen Sie sie unterhalb der **ReplaceFamilyDocument**-Methode ein.

```csharp
// ADD THIS PART TO YOUR CODE
private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
{
    await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
    Console.WriteLine("Deleted Family {0}", documentName);
}
```

Kopieren Sie den folgenden Code, und fügen Sie ihn unterhalb der zweiten Abfrageausführung in die **GetStartedDemo** -Methode ein.

```csharp
await this.ReplaceFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1", andersenFamily);

this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

// ADD THIS PART TO CODE
await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");
```

Wählen Sie die Schaltfläche **DocumentDBGettingStarted** aus, um die Anwendung auszuführen.

## <a id="DeleteDatabase"></a>Löschen der Datenbank

Das Löschen der erstellten Datenbank entfernt die Datenbank und alle untergeordneten Ressourcen (Sammlungen, Dokumente usw.). Kopieren Sie den folgenden Code, und fügen Sie ihn unterhalb des zu löschenden Dokuments in die **GetStartedDemo** -Methode ein, um die gesamte Datenbank und alle untergeordneten Ressourcen zu löschen.

```csharp
this.ExecuteSimpleQuery("FamilyDB_oa", "FamilyCollection_oa");

await this.DeleteFamilyDocument("FamilyDB_oa", "FamilyCollection_oa", "Andersen.1");

// ADD THIS PART TO CODE
// Clean up/delete the database
await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB_oa"));
```

Wählen Sie die Schaltfläche **DocumentDBGettingStarted** aus, um die Anwendung auszuführen.

## <a id="Run"></a>Ausführen Ihrer C#-Konsolenanwendung

Wählen Sie die Schaltfläche **DocumentDBGettingStarted** in Visual Studio aus, um die Anwendung im Debugmodus zu erstellen. Die Ausgabe der GetStarted-App sollte im Konsolenfenster angezeigt werden. Die Ausgabe zeigt die Ergebnisse der hinzugefügten Abfragen an. Sie sollte mit unten stehendem Beispieltext übereinstimmen.

```bash
Created FamilyDB_oa
Press any key to continue ...
Created FamilyCollection_oa
Press any key to continue ...
Created Family Andersen.1
Press any key to continue ...
Created Family Wakefield.7
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Replaced Family Andersen.1
Press any key to continue ...
Running LINQ query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
    Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Deleted Family Andersen.1
End of demo, press any key to exit.
```

Sie haben dieses Tutorial nun abgeschlossen und verfügen über eine funktionierende C#-Konsolenanwendung.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcengruppe, das Azure Cosmos-Konto und die dazugehörigen Ressourcen nicht mehr benötigen, können Sie sie löschen. Wählen Sie dazu die Ressourcengruppe für den virtuellen Computer und anschließend **Löschen** aus, und bestätigen Sie den Namen der zu löschenden Ressourcengruppe.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie eine .NET Core-App zum Verwalten von Daten erstellen, die im SQL-API-Konto von Azure Cosmos DB gespeichert sind. Sie können jetzt mit dem nächsten Artikel fortfahren:

> [!div class="nextstepaction"]
> [Erstellen einer Java-Konsolen-App mit dem SQL-API-Konto von Azure Cosmos DB](sql-api-java-get-started.md)

[create-sql-api-dotnet.md#create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-dotnetcore-get-started/nosql-tutorial-keys.png
