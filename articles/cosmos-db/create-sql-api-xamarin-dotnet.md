---
title: 'Azure Cosmos DB: Erstellen einer Todo-App mit Xamarin'
description: Enthält ein Xamarin-Codebeispiel, das Sie zum Verbinden mit Azure Cosmos DB und zum Senden entsprechender Abfragen verwenden können.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/09/2020
ms.author: anfeldma
ms.custom: devx-track-csharp
ms.openlocfilehash: 91e89eaf215468f171974e5f3fd383691fdd6ebe
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096975"
---
# <a name="quickstart-build-a-todo-app-with-xamarin-using-azure-cosmos-db-sql-api-account"></a>Schnellstart: Erstellen einer To-Do-App mit Xamarin mithilfe eines Azure Cosmos DB-SQL-API-Kontos
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK V4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Azure Cosmos DB ist ein global verteilter Datenbankdienst von Microsoft mit mehreren Modellen. Sie können schnell Dokument-, Schlüssel/Wert- und Graph-Datenbanken erstellen und abfragen und dabei stets die Vorteile der globalen Verteilung und der horizontalen Skalierung nutzen, die Azure Cosmos DB zugrunde liegen.

> [!NOTE]
> Beispielcode für eine vollständige kanonische Xamarin-Beispiel-App, die mehrere Azure-Angebote (einschließlich CosmosDB) veranschaulicht, finden Sie auf [GitHub](https://github.com/xamarinhq/app-geocontacts). Diese App veranschaulicht das Anzeigen geografisch verteilter Kontakte und zeigt, wie diesen Kontakten das Aktualisieren ihres Standorts ermöglicht werden kann.

In dieser Schnellstartanleitung wird veranschaulicht, wie Sie mithilfe über das Azure-Portal ein SQL-API-Konto, eine Dokumentdatenbank und einen Container für Azure Cosmos DB erstellen. Anschließend führen Sie die Erstellung und Bereitstellung einer mobilen Aufgabenlisten-App durch, die auf der [SQL-.NET-API](sql-api-sdk-dotnet.md) und [Xamarin](/xamarin/) basiert, indem Sie [Xamarin.Forms](/xamarin/) und das [Architekturmuster MVVM](/xamarin/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm) verwenden.

:::image type="content" source="./media/create-sql-api-xamarin-dotnet/ios-todo-screen.png" alt-text="Xamarin-Todo-App unter iOS":::

## <a name="prerequisites"></a>Voraussetzungen

Falls Sie unter Windows entwickeln und Visual Studio 2019 noch nicht installiert haben, können Sie die **kostenlose** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) herunterladen und verwenden. Stellen Sie sicher, dass Sie während des Visual Studio-Setups die Workloads **Azure-Entwicklung** und **Mobile-Entwicklung mit .NET** aktivieren.

Bei Verwendung eines Macintosh können Sie die **kostenlose** Version von [Visual Studio für Mac](https://www.visualstudio.com/vs/mac/) herunterladen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>Erstellen eines Datenbankkontos

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Hinzufügen eines Containers

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Hinzufügen von Beispieldaten

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Abfragen Ihrer Daten

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Wir klonen jetzt die Xamarin-SQL-API-App von GitHub, überprüfen den Code, beschaffen die API-Schlüssel und starten die Ausführung. Sie werden feststellen, wie einfach Sie programmgesteuert mit Daten arbeiten können.

1. Öffnen Sie eine Eingabeaufforderung, erstellen Sie einen neuen Ordner namens „git-samples“, und schließen Sie die Eingabeaufforderung.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Öffnen Sie ein Git-Terminalfenster (z.B. git bash), und verwenden Sie den Befehl `cd`, um in den neuen Ordner zu gelangen und dort die Beispiel-App zu installieren.

    ```bash
    cd "C:\git-samples"
    ```

3. Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen. Dieser Befehl erstellt eine Kopie der Beispiel-App auf Ihrem Computer.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-xamarin-getting-started.git
    ```

4. Öffnen Sie in Visual Studio die Datei **C:\git-samples\azure-cosmos-db-sql-xamarin-getting-started\src\ToDoItems.sln**. 

## <a name="obtain-your-api-keys"></a>Beschaffen Ihrer API-Schlüssel

Wechseln Sie zurück zum Azure-Portal, um die Informationen zum API-Schlüssel zu beschaffen und in die App zu kopieren.

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) in Ihrem Azure Cosmos DB-SQL-API-Konto im linken Navigationsbereich auf **Schlüssel** , und klicken Sie anschließend auf **Lese-/Schreibschlüssel**. Kopieren Sie im nächsten Schritt mithilfe der Schaltflächen zum Kopieren auf der rechten Seite des Bildschirms den URI und den Primärschlüssel in die Datei „APIKeys.cs“.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/keys.png" alt-text="Anzeigen und Kopieren eines Zugriffsschlüssels im Azure-Portal auf dem Blatt „Schlüssel“":::

2. Öffnen Sie in Visual Studio die Datei **ToDoItems.Core/Helpers/APIKeys.cs**.

3. Kopieren Sie im Azure-Portal mit der Schaltfläche zum Kopieren den **URI** -Wert, und legen Sie ihn in „APIKeys.cs“ als Wert für die Variable `CosmosEndpointUrl` fest.

    ```csharp
    //#error Enter the URL of your Azure Cosmos DB endpoint here
            public static readonly string CosmosEndpointUrl = "[URI Copied from Azure Portal]";
    ```

4. Kopieren Sie im Azure-Portal mit der Schaltfläche zum Kopieren den Wert des **PRIMÄRSCHLÜSSELS** , und legen Sie ihn in „APIKeys.cs“ als Wert für `Cosmos Auth Key` fest.

    ```csharp
    //#error Enter the read/write authentication key of your Azure Cosmos DB endpoint here
            public static readonly string CosmosAuthKey = "[PRIMARY KEY copied from Azure Portal";
    ```

[!INCLUDE [cosmos-db-auth-key-info](../../includes/cosmos-db-auth-key-info.md)]

## <a name="review-the-code"></a>Überprüfen des Codes

Mit dieser Lösung wird veranschaulicht, wie Sie eine Todo-App erstellen, indem Sie die Azure Cosmos DB-SQL-API und Xamarin.Forms verwenden. Die App hat zwei Registerkarten. Die erste Registerkarte enthält eine Listenansicht mit den Todo-Elementen, die noch nicht abgearbeitet wurden. Auf der zweiten Registerkarte werden die Todo-Elemente angezeigt, die bereits abgeschlossen wurden. Zusätzlich zum Anzeigen der nicht abgeschlossenen Todo-Elemente auf der ersten Registerkarte können Sie auch neue Elemente hinzufügen, vorhandene Elemente bearbeiten und Elemente als abgeschlossen kennzeichnen.

:::image type="content" source="./media/create-sql-api-xamarin-dotnet/android-todo-screen.png" alt-text="Kopieren von JSON-Daten und Klicken auf „Speichern“ im Daten-Explorer im Azure-Portal":::

Der Code in der Lösung ToDoItems enthält Folgendes:

* **ToDoItems.Core**
   * Dies ist ein .NET Standard-Projekt mit einem Xamarin.Forms-Projekt und freigegebenem Anwendungslogikcode, mit dem Todo-Elemente in Azure Cosmos DB verwaltet werden.
* **ToDoItems.Android**
  * Dieses Projekt enthält die Android-App.
* **ToDoItems.iOS**
  * Dieses Projekt enthält die iOS-App.

Wir sehen uns jetzt kurz an, wie die App mit Azure Cosmos DB kommuniziert.

* Das NuGet-Paket [Microsoft.Azure.DocumentDb.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/) muss allen Projekten hinzugefügt werden.
* Mit der Klasse `ToDoItem` im Ordner **ToDoItems.Core/Models** werden die Dokumente im oben erstellten Container **Items** modelliert. Beachten Sie, dass bei der Benennung von Eigenschaften die Groß-/Kleinschreibung berücksichtigt wird.
* Mit der Klasse `CosmosDBService` im Ordner **ToDoItems.Core/Services** wird die Kommunikation mit Azure Cosmos DB gekapselt.
* Die `CosmosDBService`-Klasse enthält die Typvariable `DocumentClient`. Die Variable `DocumentClient` wird verwendet, um Anforderungen für das Azure Cosmos DB-Konto zu konfigurieren und auszuführen, und wird instanziiert:

    ```csharp
    docClient = new DocumentClient(new Uri(APIKeys.CosmosEndpointUrl), APIKeys.CosmosAuthKey);
    ```

* Beim Abfragen eines Containers nach Dokumenten wird die `DocumentClient.CreateDocumentQuery<T>`-Methode verwendet, wie hier in der Funktion `CosmosDBService.GetToDoItems` zu sehen:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=GetToDoItems)] 

    `CreateDocumentQuery<T>` akzeptiert einen URI, der auf den im vorherigen Abschnitt erstellten Container zeigt. Außerdem können Sie LINQ-Operatoren angeben, z.B. eine `Where`-Klausel. In diesem Fall werden nur Todo-Elemente zurückgegeben, die noch nicht abgeschlossen wurden.

    Die Funktion `CreateDocumentQuery<T>` wird synchron ausgeführt und gibt das `IQueryable<T>`-Element zurück. Mit der `AsDocumentQuery`-Methode wird `IQueryable<T>` aber in ein `IDocumentQuery<T>`-Objekt konvertiert, das asynchron ausgeführt werden kann. Auf diese Weise wird der UI-Thread für mobile Anwendungen nicht blockiert.

    Mit der Funktion `IDocumentQuery<T>.ExecuteNextAsync<T>` wird die Seite mit den Ergebnissen aus Azure Cosmos DB abgerufen, und mit `HasMoreResults` wird überprüft, ob noch weitere Ergebnisse zurückgegeben werden müssen.

> [!TIP]
> Mehrere Funktionen, die für Azure Cosmos-Container und -Dokumente eingesetzt werden, akzeptieren einen URI als Parameter, um die Adresse des Containers bzw. Dokuments anzugeben. Dieser URI wird mit der `URIFactory`-Klasse erstellt. Mit dieser Klasse können URIs für Datenbanken, Container und Dokumente erstellt werden.

* Die Funktion `ComsmosDBService.InsertToDoItem` veranschaulicht, wie Sie ein neues Dokument einfügen:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=InsertToDoItem)] 

    Der Element-URI und das einzufügende Element werden angegeben.

* Die Funktion `CosmosDBService.UpdateToDoItem` veranschaulicht, wie Sie ein vorhandenes Dokument durch ein neues ersetzen:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=UpdateToDoItem)] 

    Hier wird ein neuer URI benötigt, um das zu ersetzende Dokument eindeutig zu identifizieren. Er wird mit `UriFactory.CreateDocumentUri` abgerufen, und es werden der Datenbank- und der Containername sowie die ID des Dokuments an ihn übergeben.

    Mit `DocumentClient.ReplaceDocumentAsync` wird das Dokument, das anhand des URI identifiziert wurde, durch das als Parameter angegebene Dokument ersetzt.

* Das Löschen eines Elements wird mit der Funktion `CosmosDBService.DeleteToDoItem` veranschaulicht:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=DeleteToDoItem)] 

    Beachten Sie, dass auch hier wieder ein eindeutiger Dokument-URI erstellt und an die Funktion `DocumentClient.DeleteDocumentAsync` übergeben wird.

## <a name="run-the-app"></a>Ausführen der App

Sie haben die App nun mit allen erforderlichen Informationen für die Kommunikation mit Azure Cosmos DB aktualisiert.

Die folgenden Schritte veranschaulichen, wie Sie die App mit dem Debugger von Visual Studio für Mac ausführen.

> [!NOTE]
> Die Nutzung der Android-Version der App ist nahezu identisch, und alle Unterschiede sind in den Schritten unten angegeben. Wenn Sie das Debuggen mit Visual Studio unter Windows durchführen möchten, finden Sie die Dokumentation für [iOS hier](/xamarin/ios/deploy-test/debugging-in-xamarin-ios?tabs=vswin) und für [Android hier](/xamarin/android/deploy-test/debugging/).

1. Wählen Sie zuerst die gewünschte Zielplattform aus, indem Sie auf die hervorgehobene Dropdownliste klicken und dann entweder „ToDoItems.iOS“ für iOS oder „ToDoItems.Android“ für Android wählen.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ide-select-platform.png" alt-text="Auswählen einer Plattform für das Debuggen in Visual Studio für Mac":::

2. Drücken Sie zum Starten des Debugvorgangs für die App entweder BEFEHLSTASTE+EINGABETASTE, oder klicken Sie auf die Wiedergabeschaltfläche.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ide-start-debug.png" alt-text="Starten des Debuggens in Visual Studio für Mac":::

3. Nachdem der iOS-Simulator bzw. der Android-Emulator gestartet wurde, werden in der App zwei Registerkarten angezeigt: für iOS am unteren Rand und für Android am oberen Rand. Auf der ersten Registerkarte werden Todo-Elemente angezeigt, die nicht abgeschlossen wurden, und auf der zweiten sind die abgeschlossenen Todo-Elemente aufgeführt.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ios-droid-started.png" alt-text="Starten des Bildschirms der Todo-App":::

4. Für iOS können Sie ein Todo-Element abschließen, indem Sie es nach links ziehen und dann auf die Schaltfläche **Complete** (Abschließen) tippen. Unter Android können Sie ein Element abschließen, indem Sie länger auf das Element drücken und dann auf die Schaltfläche „Complete“ (Abschließen) tippen.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-complete.png" alt-text="Abschließen eines Todo-Elements":::

5. Tippen Sie auf ein Todo-Element, um es zu bearbeiten. Es wird ein neuer Bildschirm angezeigt, auf dem Sie die neuen Werte eingeben können. Wenn Sie auf die Schaltfläche „Speichern“ tippen, werden die Änderungen dauerhaft in Azure Cosmos DB gespeichert.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-edit.png" alt-text="Bearbeiten eines Todo-Elements":::

6. Tippen Sie zum Hinzufügen eines Todo-Elements oben rechts auf dem Startbildschirm auf die Schaltfläche **Hinzufügen**. Es wird eine neue, leere Bearbeitungsseite angezeigt.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-add.png" alt-text="Hinzufügen eines Todo-Elements":::

## <a name="review-slas-in-the-azure-portal"></a>Überprüfen von SLAs im Azure-Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie gelernt, wie Sie ein Azure Cosmos-Konto sowie einen Container mit dem Daten-Explorer erstellen und eine Xamarin-App erstellen und bereitstellen. Jetzt können Sie zusätzliche Daten in Ihr Azure Cosmos-Konto importieren.

> [!div class="nextstepaction"]
> [Import data into Azure Cosmos DB (Importieren von Daten in Azure Cosmos DB)](import-data.md)