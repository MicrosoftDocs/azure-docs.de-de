---
title: Arbeiten mit der verwalteten Clientbibliothek von Mobile App Service-Apps | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine .NET-Clientbibliothek für Mobile App Service-Apps von Azure mit Windows- und Xamarin-Apps verwenden.
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 09/24/2018
ms.author: crdun
ms.openlocfilehash: 8f014f1cb40e1a629d1989f00805fc91015a3ae9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62119302"
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Verwenden des verwalteten Clients für Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Übersicht
In dieser Anleitung wird die Ausführung gängiger Aufgaben mithilfe der verwalteten Clientbibliothek für Mobile App Service-Apps unter Azure in Windows- und Xamarin-Apps beschrieben. Wenn Sie keine Erfahrungen mit Mobile Apps haben, sollten Sie eventuell zunächst das [Schnellstarttutorial für Azure Mobile Apps][1] absolvieren. In diesem Handbuch konzentrieren wir uns auf das clientseitige verwaltete SDK. Weitere Informationen zu den serverseitigen SDKs für Mobile Apps finden Sie in der Dokumentation zum [.NET Server SDK][2] oder [Node.js Server SDK][3].

## <a name="reference-documentation"></a>Referenzdokumentation
Die Referenzdokumentation für das Client-SDK finden Sie hier: [.NET-Client-Referenz für Azure Mobile Apps][4].
Sie finden auch mehrere Clientbeispiele im [GitHub-Repository „Azure-Samples“][5] (Azure-Beispiele).

## <a name="supported-platforms"></a>Unterstützte Plattformen
Die .NET-Plattform unterstützt die folgenden Plattformen:

* Xamarin Android-Releases für API 19 bis 24 (KitKat bis Nougat)
* Xamarin iOS-Releases für iOS-Versionen 8.0 und höher
* Universelle Windows-Plattform
* Windows Phone 8.1
* Windows Phone 8.0 außer für Silverlight-Anwendungen

Die Authentifizierung für den „Serverfluss“ verwendet eine Webansicht für die dargestellte Benutzeroberfläche.  Wenn das Gerät keine Benutzeroberfläche in Form einer Webansicht darstellen kann, sind andere Authentifizierungsmethoden erforderlich.  Dieses SDK eignet sich daher nicht für Geräte vom Typ „Überwachung“ oder für ähnlich eingeschränkte Geräte.

## <a name="setup"></a>Einrichtung und Voraussetzungen
Es wird davon ausgegangen, dass Sie Ihr Mobile App-Back-End-Projekt bereits erstellt und veröffentlicht haben und dass es mindestens eine Tabelle enthält.  Der Code in diesem Thema verwendet eine Tabelle mit dem Namen `TodoItem` und den folgenden Spalten: `Id`, `Text` und `Complete`. Dies ist die gleiche Tabelle, die Sie beim Durcharbeiten des [Schnellstarttutorials für Azure Mobile Apps][1] erstellt haben.

Der entsprechende typisierte clientseitige Typ in C# ist die folgende Klasse:

```csharp
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }
}
```

Das [JsonPropertyAttribute][6] wird verwendet, um die *PropertyName*-Zuordnung zwischen dem Clientfeld und dem Tabellenfeld zu definieren.

Informationen zum Erstellen von Tabellen in Ihrem Mobile Apps-Back-End finden Sie im Thema zum [.NET Server SDK][7] oder zum [Node.js Server SDK][8]. Wenn Sie Ihr Mobile App-Back-End im Azure-Portal mithilfe des Schnellstarts erstellt haben, können Sie auch die Einstellung **Einfache Tabellen** im [Azure-Portal]verwenden.

### <a name="how-to-install-the-managed-client-sdk-package"></a>Gewusst wie: Installieren des SDK-Pakets für verwaltete Clients
Verwenden Sie eine der folgenden Methoden, um das SDK-Paket für verwaltete Clients für Mobile Apps von [NuGet][9]zu installieren:

* **Visual Studio**: Klicken Sie mit der rechten Maustaste auf Ihr Projekt, klicken Sie auf **NuGet-Pakete verwalten**, suchen Sie nach dem `Microsoft.Azure.Mobile.Client`-Paket, und klicken Sie anschließend auf **Installieren**.
* **Xamarin Studio**: Klicken Sie mit der rechten Maustaste auf Ihr Projekt, klicken Sie auf **Hinzufügen** > **NuGet-Pakete hinzufügen**, suchen Sie nach dem `Microsoft.Azure.Mobile.Client`-Paket, und klicken Sie dann auf **Paket hinzufügen**.

Denken Sie daran, in der Datei Ihrer Hauptaktivität die folgende **using** -Anweisung hinzuzufügen:

```csharp
using Microsoft.WindowsAzure.MobileServices;
```

> [!NOTE]
> Beachten Sie, dass alle Unterstützungspakete, auf die in Ihrem Android-Projekt verwiesen wird, die gleiche Version aufweisen müssen. Das SDK hat eine `Xamarin.Android.Support.CustomTabs`-Abhängigkeit für die Android-Plattform. Wenn also Ihr Projekt neuere Unterstützungspakete verwendet, müssen Sie dieses Paket direkt mit der erforderlichen Version installieren, um Konflikte zu vermeiden.

### <a name="symbolsource"></a>Vorgehensweise: Arbeiten mit Debugsymbolen in Visual Studio
Die Symbole für den Namespace „Microsoft.Azure.Mobile“ sind unter [SymbolSource][10] verfügbar.  Integrieren Sie SymbolSource gemäß den [SymbolSource-Anweisungen][11] in Visual Studio.

## <a name="create-client"></a>Erstellen des Mobile Apps-Clients
Der folgende Code erstellt das [MobileServiceClient][12]-Objekt, das für den Zugriff auf Ihr Mobile App-Back-End verwendet wird.

```csharp
var client = new MobileServiceClient("MOBILE_APP_URL");
```

Ersetzen Sie im obigen Code `MOBILE_APP_URL` durch die URL des Mobile App-Back-Ends. Sie finden die URL auf dem Blatt für das Mobile App-Back-End im [Azure-Portal]. Beim MobileServiceClient-Objekt muss es sich um ein Singleton-Objekt handeln.

## <a name="work-with-tables"></a>Arbeiten mit Tabellen
Der folgende Abschnitt enthält Informationen zum Suchen und Abrufen von Datensätzen sowie Ändern der Daten in der Tabelle.  Die folgenden Themen werden behandelt:

* [Erstellen eines Tabellenverweises](#instantiating)
* [Abfragen von Daten](#querying)
* [Zurückgegebene Daten filtern](#filtering)
* [Zurückgegebene Daten sortieren](#sorting)
* [Daten seitenweise zurückgeben](#paging)
* [Bestimmte Spalten auswählen](#selecting)
* [Suchen eines Datensatzes nach ID](#lookingup)
* [Umgang mit nicht typisierten Abfragen](#untypedqueries)
* [Einfügen von Daten](#inserting)
* Aktualisieren von Daten
* [Löschen von Daten](#deleting)
* [Lösung von Konflikten und optimistische Parallelität](#optimisticconcurrency)
* [Binden an eine Windows-Benutzeroberfläche](#binding)
* [Ändern der Seitengröße](#pagesize)

### <a name="instantiating"></a>Vorgehensweise: Erstellen eines Tabellenverweises
Jeglicher Code zum Abrufen oder Ändern von Daten in einer Back-End-Tabelle ruft Funktionen des `MobileServiceTable` -Objekts auf. Rufen Sie einen Verweis auf die Tabelle ab, indem Sie wie folgt die Methode [GetTable] aufrufen:

```csharp
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

Das zurückgegebene Objekt verwendet das typisierte Serialisierungsmodell. Ein nicht typisiertes Serialisierungsmodell wird ebenfalls unterstützt. Das folgende Beispiel [erstellt einen Verweis auf eine nicht typisierte Tabelle]:

```csharp
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

Bei nicht typisierten Abfragen müssen Sie die zugrunde liegende OData-Abfragezeichenfolge angeben.

### <a name="querying"></a>Vorgehensweise: Abfragen von Daten aus Ihrer mobilen App
Dieser Abschnitt beschreibt, wie Sie Abfragen an Ihr Mobile App-Back-End stellen können. Dies umfasst folgende Funktionen:

* [Zurückgegebene Daten filtern](#filtering)
* [Zurückgegebene Daten sortieren](#sorting)
* [Daten seitenweise zurückgeben](#paging)
* [Bestimmte Spalten auswählen](#selecting)
* [Daten nach ID abrufen](#lookingup)

> [!NOTE]
> Es wird die Verwendung einer servergesteuerten Seitengröße erzwungen, um zu verhindern, dass alle Zeilen zurückgegeben werden.  Durch Paging wird verhindert, dass Standardabfragen für große Datensätze den Dienst negativ beeinflussen.  Verwenden Sie die Methoden `Skip` und `Take`, um mehr als 50 Zeilen zurückzugeben, wie unter [Seitenweises Zurückgeben von Daten](#paging) beschrieben.

### <a name="filtering"></a>Vorgehensweise: Filtern zurückgegebener Daten
Der folgende Code zeigt, wie Sie Daten mithilfe einer `Where` -Klausel in einer Abfrage filtern. Die Abfrage gibt alle Elemente aus `todoTable` zurück, deren `Complete`-Eigenschaft gleich `false` ist. Die [Where] -Funktion wendet ein Zeilenfilterungsprädikat auf die Tabellenabfrage an.

```csharp
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

Sie können den URI der an das Back-End gesendeten Anforderung anzeigen, indem Sie Software zur Überprüfung von Nachrichten verwenden, z. B. Browserentwicklertools oder [Fiddler]. Beachten Sie im Anforderungs-URI, dass die Abfragezeichenfolge verändert ist:

```csharp
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

Diese OData-Anforderung wird vom Server-SDK in eine SQL-Abfrage übersetzt:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

Die an die `Where` -Methode übergebene Funktion kann beliebig viele Bedingungen enthalten.

```csharp
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

Dieses Beispiel würde vom Server-SDK in eine SQL-Abfrage übersetzt:

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

Diese Abfrage kann auch in mehrere Klauseln aufgeteilt werden:

```csharp
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

Beide Methoden sind äquivalent und können frei austauschbar verwendet werden.  Die erste Option &mdash; Verketten mehrerer Prädikate in einer Abfrage &mdash; ist kompakter und wird daher empfohlen.

Die `Where` -Klausel unterstützt Vorgänge, die in die OData-Teilmenge übersetzt werden können. Folgende Vorgänge sind möglich:

* Relationale Operatoren (==, !=, <, <=, >, >=)
* Arithmetische Operatoren (+, -, /, *, %)
* Zahlengenauigkeit (Math.Floor, Math.Ceiling)
* Zeichenfolgenfunktionen (Length, Substring, Replace, IndexOf, StartsWith, EndsWith)
* Datumseigenschaften (Year, Month, Day, Hour, Minute, Second)
* Zugriff auf Eigenschaften eines Objekts
* Ausdrücke, die beliebige dieser Vorgänge kombinieren

Wenn Sie wissen möchten, was das Server-SDK unterstützt, lesen Sie die [OData v3-Dokumentation].

### <a name="sorting"></a>Vorgehensweise: Sortieren zurückgegebener Daten
Der folgende Code zeigt, wie Sie Daten mithilfe einer [OrderBy]- oder einer [OrderByDescending]-Klausel in einer Abfrage sortieren können. Die Abfrage gibt Elemente aus der Tabelle `todoTable` aufsteigend sortiert nach dem `Text`-Feld zurück.

```csharp
// Sort items in ascending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderBy(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();

// Sort items in descending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderByDescending(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();
```

### <a name="paging"></a>Vorgehensweise: Seitenweises Zurückgeben von Daten
Standardmäßig gibt das Back-End nur die ersten 50 Zeilen zurück. Sie können die [Take] -Methode aufrufen, um die Anzahl der zurückgegebenen Zeilen zu erhöhen. Verwenden Sie `Take` zusammen mit der [Skip] -Methode, um eine bestimmte "Seite" des gesamten Datasets anzufordern, das von der Abfrage zurückgegeben wird. Die folgende Abfrage liefert die ersten drei Elemente aus der Tabelle zurück.

```csharp
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Die folgende geänderte Abfrage überspringt die ersten drei Ergebnisse und gibt die nächsten drei Ergebnisse zurück. Diese Abfrage erzeugt die zweite „Seite“ der Daten für eine Seitengröße von drei Elementen.

```csharp
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

Die [IncludeTotalCount] -Methode ruft die Gesamtanzahl für *alle* Datensätze ab, die bei Ignorieren aller angegebenen Paging-/Limit-Klauseln zurückgegeben worden wären:

```csharp
query = query.IncludeTotalCount();
```

In tatsächlichen Apps können Sie ähnliche Abfragen wie im vorherigen Beispiel mit einem Pagingsteuerelement oder einer vergleichbaren Benutzeroberfläche ausführen, um zwischen Seiten zu navigieren.

> [!NOTE]
> Um die Begrenzung auf 50 Zeilen in einem Mobile App-Back-End zu überschreiben, müssen Sie [EnableQueryAttribute] auf die öffentliche GET-Methode anwenden und das Pagingverhalten festlegen. Bei Anwendung des Attributs auf die Methode wird durch Folgendes die maximale Anzahl zurückgegebener Zeilen auf 1000 beschränkt:
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="selecting"></a>Vorgehensweise: Auswählen bestimmter Spalten
Sie können angeben, welche Eigenschaften im Ergebnis enthalten sein sollen, indem Sie eine [Select] -Klausel zu Ihrer Abfrage hinzufügen. Der folgende Code zeigt, wie Sie nur ein Feld auswählen oder auch mehrere Felder auswählen und formatieren können:

```csharp
// Select one field -- just the Text
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => todoItem.Text);
List<string> items = await query.ToListAsync();

// Select multiple fields -- both Complete and Text info
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => string.Format("{0} -- {1}",
                    todoItem.Text.PadRight(30), todoItem.Complete ?
                    "Now complete!" : "Incomplete!"));
List<string> items = await query.ToListAsync();
```

Alle bisher beschriebenen Funktionen sind additiv, wir können also damit fortfahren, sie zu verketten. Jeder verkettete Aufruf betrifft einen größeren Teil der Abfrage. Ein weiteres Beispiel:

```csharp
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="lookingup"></a>Vorgehensweise: Abrufen von Daten nach ID
Die [LookupAsync] -Funktion kann verwendet werden, um Objekte mit einer bestimmten ID aus der Datenbank abzufragen.

```csharp
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="untypedqueries"></a>Vorgehensweise: Ausführen nicht typisierter Abfragen
Beim Ausführen einer Abfrage mit einem nicht typisierten Tabellenobjekt müssen Sie die OData-Abfragezeichenfolge durch Aufruf von [ReadAsync]explizit angeben, wie im folgenden Beispiel veranschaulicht:

```csharp
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

Sie erhalten JSON-Werte zurück, die Sie wie einen Eigenschaftenbehälter verwenden können. Weitere Informationen zu JToken und Newtonsoft Json.NET finden Sie unter [Json.NET] .

### <a name="inserting"></a>Vorgehensweise: Einfügen von Daten in ein Mobile App-Back-End
Alle Clienttypen müssen einen Member mit dem Namen **Id** enthalten. Dies ist standardmäßig eine Zeichenfolge. Diese **Id** ist für die Durchführung von CRUD-Vorgängen und die Offlinesynchronisierung erforderlich. Der folgende Code zeigt, wie Sie mit der [InsertAsync]-Methode neue Zeilen in eine Tabelle einfügen. Der Parameter enthält die einzufügenden Daten als .NET-Objekt.

```csharp
await todoTable.InsertAsync(todoItem);
```

Wenn während einer Einfügung kein eindeutiger benutzerdefinierter ID-Wert in das `todoItem` eingeschlossen wird, wird vom Server eine GUID generiert.
Sie können die generierte ID abrufen, indem Sie das Objekt nach Rückgabe des Aufrufs untersuchen.

Zum Einfügen von nicht typisierten Daten können Sie Json.NET verwenden:

```csharp
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

Das folgende Beispiel verwendet eine E-Mail-Adresse als eindeutige ID-Zeichenfolge:

```csharp
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>Arbeiten mit ID-Werten
Mobile Apps unterstützt eindeutige benutzerdefinierte Zeichenfolgenwerte für die Spalte **id** der Tabelle. Mit einem Zeichenfolgenwert können Anwendungen benutzerdefinierte Werte wie E-Mail-Adressen oder Benutzernamen für die ID verwenden.  Zeichenfolgen-IDs bieten Ihnen die folgenden Vorteile:

* IDs werden ohne Roundtrip zur Datenbank generiert.
* Datensätze aus unterschiedlichen Tabellen oder Datenbanken lassen sich leichter zusammenführen.
* ID-Werte lassen sich möglicherweise leichter in die Anwendungslogik integrieren.

Wenn der ID-Wert einer Zeichenfolge für keine eingefügten Datensätze festgelegt ist, generiert das Mobile App-Back-End einen eindeutigen Wert für die ID. Sie können die [Guid.NewGuid] -Methode verwenden, um eigene ID-Werte entweder auf dem Client oder im Back-End zu generieren.

```csharp
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="modifying"></a>Vorgehensweise: Ändern von Daten in einem Mobile App-Back-End
Der folgende Code zeigt, wie Sie mit der [UpdateAsync] -Methode einen vorhandenen Datensatz, der die gleiche ID aufweist, mit neuen Daten aktualisieren können. Der Parameter enthält die zu aktualisierenden Daten als .NET-Objekt.

```csharp
await todoTable.UpdateAsync(todoItem);
```

Zum Aktualisieren von nicht typisierten Daten können Sie [Json.NET] wie folgt verwenden:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

Beim Ausführen eines Updates muss ein `id` -Feld angegeben werden. Das Back-End verwendet das `id` -Feld, um die zu aktualisierende Zeile zu identifizieren. Sie können das `id`-Feld aus dem Ergebnis des `InsertAsync`-Aufrufs abrufen. Wenn Sie versuchen, ein Element ohne Angabe des `id`-Werts zu aktualisieren, wird eine `ArgumentException` ausgelöst.

### <a name="deleting"></a>Vorgehensweise: Löschen von Daten in einem Mobile App-Back-End
Der folgende Code zeigt, wie Sie vorhandene Instanzen mit der [DeleteAsync] -Methode löschen können. Die Instanz wird durch das `id`-Feld identifiziert, das im `todoItem` festgelegt ist.

```csharp
await todoTable.DeleteAsync(todoItem);
```

Zum Löschen von nicht typisierten Daten können Sie Json.NET wie folgt verwenden:

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

Bei einer Löschanforderung müssen Sie eine ID angeben. Andere Eigenschaften werden nicht an den Dienst übergeben oder vom Dienst ignoriert. Das Ergebnis eines `DeleteAsync`-Aufrufs lautet normalerweise `null`. Sie erhalten die zu übergebende ID im Ergebnis des `InsertAsync` -Aufrufs. Es wird eine `MobileServiceInvalidOperationException` ausgelöst, wenn Sie versuchen, ein Element ohne Angabe des `id`-Felds zu löschen.

### <a name="optimisticconcurrency"></a>Vorgehensweise: Verwenden der optimistischen Parallelität zur Lösung von Konflikten
Zwei oder mehr Clients können gleichzeitig versuchen, das gleiche Element zu bearbeiten. Ohne Konflikterkennung würde der letzte Schreibvorgang alle vorherigen Aktualisierungen überschreiben. **Steuerung für optimistische Parallelität** nimmt an, dass jede Transaktion Commits ausführen kann und sperrt daher keine Ressourcen.  Vor dem Commit einer Transaktion prüft die Steuerung für optimistische Parallelität, ob die Daten von einer anderen Transaktion geändert wurden. Falls die Daten geändert wurden, wird für die Transaktion, die den Commit durchführen sollte, ein Rollback durchgeführt.

Mobile Apps unterstützt die Steuerung für optimistische Parallelität, indem Änderungen an Elementen in der Spalte `version` mit den Systemeigenschaften nachverfolgt werden, die für jede Tabelle im Mobile App-Back-End definiert wird. Bei jeder Aktualisierung eines Datensatzes wird die `version` -Eigenschaft des entsprechenden Datensatzes von Mobile Apps auf einen neuen Wert festgelegt. Bei jeder Aktualisierungsanforderung wird die `version` -Eigenschaft des in der Anforderung enthaltenen Datensatzes mit der Eigenschaft des Datensatzes auf dem Server verglichen. Wenn die mit der Anforderung übergebene Version nicht mit dem Back-End übereinstimmt, löst die Clientbibliothek eine `MobileServicePreconditionFailedException<T>` -Ausnahme aus. Der in der Ausnahme enthaltene Typ ist der Datensatz des Back-Ends, der die Serverversion des entsprechenden Datensatzes enthält. Anschließend kann die Anwendung anhand dieser Informationen entscheiden, ob die Updateanforderung erneut mit dem korrekten `version` -Wert vom Back-End ausgeführt werden soll, um Commits für die Änderungen auszuführen.

Definieren Sie eine Spalte in der Tabellenklasse für die `version` -Systemeigenschaft, um die optimistische Parallelität zu aktivieren. Beispiel:

```csharp
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }

    // *** Enable Optimistic Concurrency *** //
    [JsonProperty(PropertyName = "version")]
    public string Version { set; get; }
}
```

Anwendungen, die nicht typisierte Tabellen verwenden, können die optimistische Nebenläufigkeit aktivieren, indem sie das `Version`-Flag in den `SystemProperties` der Tabelle wie folgt festlegen.

```csharp
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

Zusätzlich zum Aktivieren der optimistischen Parallelität müssen Sie beim Aufrufen von [UpdateAsync] auch die `MobileServicePreconditionFailedException<T>`-Ausnahme im Code abfangen.  Lösen Sie den Konflikt durch Anwenden der richtigen `version` auf den aktualisierten Datensatz, und rufen Sie [UpdateAsync] mit dem aufgelösten Datensatz auf. Der folgende Code zeigt, wie ein erkannter Schreibkonflikt gelöst werden kann:

```csharp
private async void UpdateToDoItem(TodoItem item)
{
    MobileServicePreconditionFailedException<TodoItem> exception = null;

    try
    {
        //update at the remote table
        await todoTable.UpdateAsync(item);
    }
    catch (MobileServicePreconditionFailedException<TodoItem> writeException)
    {
        exception = writeException;
    }

    if (exception != null)
    {
        // Conflict detected, the item has changed since the last query
        // Resolve the conflict between the local and server item
        await ResolveConflict(item, exception.Item);
    }
}


private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
{
    //Ask user to choose the resolution between versions
    MessageDialog msgDialog = new MessageDialog(
        String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
        serverItem.Text, localItem.Text),
        "CONFLICT DETECTED - Select a resolution:");

    UICommand localBtn = new UICommand("Commit Local Text");
    UICommand ServerBtn = new UICommand("Leave Server Text");
    msgDialog.Commands.Add(localBtn);
    msgDialog.Commands.Add(ServerBtn);

    localBtn.Invoked = async (IUICommand command) =>
    {
        // To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
        // catching a MobileServicePreConditionFailedException.
        localItem.Version = serverItem.Version;

        // Updating recursively here just in case another change happened while the user was making a decision
        UpdateToDoItem(localItem);
    };

    ServerBtn.Invoked = async (IUICommand command) =>
    {
        RefreshTodoItems();
    };

    await msgDialog.ShowAsync();
}
```

Weitere Informationen finden Sie im Thema [Offlinedatensynchronisierung in Azure Mobile Apps] .

### <a name="binding"></a>Vorgehensweise: Binden von Mobile Apps-Daten an eine Windows-Benutzeroberfläche
In diesem Abschnitt wird das Anzeigen zurückgegebener Datenobjekte mithilfe von UI-Elementen in einer Windows-App beschrieben.  Der folgende Beispielcode wird mit einer Abfrage für unvollständige Elemente an die Quelle der Liste gebunden. Die [MobileServiceCollection] erstellt eine Bindungssammlung, die Mobile Apps unterstützt.

```csharp
// This query filters out completed TodoItems.
MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToCollectionAsync();

// itemsControl is an IEnumerable that could be bound to a UI list control
IEnumerable itemsControl  = items;

// Bind this to a ListBox
ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Einige Steuerelemente in der verwalteten Laufzeit unterstützen eine Schnittstelle namens [ISupportIncrementalLoading]. Mit dieser Schnittstelle können Steuerelemente weitere Daten anfordern, wenn ein Benutzer blättert. [MobileServiceIncrementalLoadingCollection]bietet integrierte Unterstützung für diese Schnittstelle für universelle Windows-Apps und verarbeitet die Aufrufe dieser Steuerelemente automatisch. Verwenden Sie `MobileServiceIncrementalLoadingCollection` folgendermaßen in Windows-Apps:

```csharp
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Um die neue Sammlung in Windows Phone 8- und Silverlight-Apps zu nutzen, verwenden Sie die `ToCollection`-Erweiterungsmethoden für `IMobileServiceTableQuery<T>` und `IMobileServiceTable<T>`. Zum Laden von Daten rufen Sie `LoadMoreItemsAsync()`auf.

```csharp
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

Wenn Sie die durch den Aufruf von `ToCollectionAsync` bzw. `ToCollection` erstellte Sammlung verwenden, erhalten Sie eine Sammlung, die an UI-Steuerelemente gebunden werden kann.  Diese Sammlung unterstützt das Paging.  Da die Sammlung Daten aus dem Netzwerk lädt, tritt bei Ladevorgängen manchmal ein Fehler auf. Zur Behandlung solcher Fehler überschreiben Sie die `OnException`-Methode für `MobileServiceIncrementalLoadingCollection`, um Ausnahmen zu behandeln, die aus Aufrufen von `LoadMoreItemsAsync` entstehen.

Stellen Sie sich vor, Ihre Tabelle hat viele Felder, aber Sie möchten nur einen Teil der Felder in Ihrem Steuerelement anzeigen. Folgen Sie der Anleitung im vorherigen Abschnitt[Auswählen bestimmter Spalten](#selecting), um bestimmte Spalten für die Anzeige auf der Benutzeroberfläche auszuwählen.

### <a name="pagesize"></a>Ändern der Seitengröße
Azure Mobile Apps gibt standardmäßig maximal 50 Elemente pro Anforderung zurück.  Sie können die Paginggröße ändern, indem Sie die maximale Seitengröße sowohl auf dem Client als auch auf dem Server erhöhen.  Um die angeforderte Seitengröße zu erhöhen, geben Sie `PullOptions` an, wenn Sie `PullAsync()` verwenden:

```csharp
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

Wenn Sie die `PageSize` auf dem Server auf einen Wert gleich oder größer 100 festgelegt haben, gibt eine Anforderung bis zu 100 Elemente zurück.

## <a name="#offlinesync"></a>Mit Offlinetabellen arbeiten
Offlinetabellen verwenden eine lokalen SQLite-Speicher zum Speichern von Daten für die Offline-Verwendung.  Alle Tabellenvorgänge werden gegen den lokalen SQLite-Speicher statt den Remote-Serverspeicher ausgeführt.  Um eine Offlinetabelle zu erstellen, müssen Sie zuerst das Projekt vorbereiten:

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf die Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten für Projektmappe...** . Suchen Sie anschließend nach dem NuGet-Paket **Microsoft.Azure.Mobile.Client.SQLiteStore**, und installieren Sie es für alle Projekte der Projektmappe.
2. (Optional) Um Windows-Geräte zu unterstützen, können Sie eines der folgenden SQLite-Laufzeitpakete installieren:

   * **Windows 8.1-Runtime:** Installieren Sie [SQLite für Windows 8.1][3].
   * **Windows Phone 8.1:** Installieren Sie [SQLite für Windows Phone 8.1][4].
   * **Universelle Windows-Plattform:** Installieren Sie [SQLite für die universelle Windows-Plattform][5].
3. (Optional). Klicken Sie bei Windows-Geräten auf **Verweise** > **Verweis hinzufügen...** , erweitern Sie den Ordner **Windows**, und wählen Sie **Erweiterungen**. Aktivieren Sie anschließend das richtige **SDK SQLite für Windows** zusammen mit dem SDK **Visual C++ 2013 Runtime for Windows**.
    Die Namen der SQLite-SDKs unterscheiden sich bei den einzelnen Windows-Plattformen.

Bevor ein Tabellenverweis erstellt werden, muss der lokale Speicher vorbereitet werden:

```csharp
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

Die Speicherinitialisierung erfolgt normalerweise sofort nach dem Erstellen des Clients.  Der **OfflineDbPath** sollte ein geeigneter Dateiname für die Verwendung auf allen Plattformen sein, die Sie unterstützen.  Wenn der Pfad ein vollqualifizierter Pfad ist (d. h. er beginnt mit einem Schrägstrich), dann wird der Pfad verwendet.  Wenn der Pfad nicht vollqualifiziert ist, wird die Datei in einem plattformspezifischen Verzeichnis platziert.

* Bei IOS- und Android-Geräten ist der Standardpfad der Ordner „Persönliche Dateien“.
* Bei Windows-Geräten ist der Standardpfad der anwendungsspezifische Ordner „AppData“.

Ein Tabellenverweis kann mit der `GetSyncTable<>`-Methode:abgerufen werden:

```csharp
var table = client.GetSyncTable<TodoItem>();
```

Für die Verwendung einer Offlinetabelle müssen Sie sich nicht authentifizieren.  Sie müssen sich nur authentifizieren, wenn Sie mit dem Back-End-Dienst kommunizieren.

### <a name="syncoffline"></a>Synchronisieren einer Offlinetabelle
Offlinetabellen werden nicht standardmäßig mit dem Back-End synchronisiert.  Die Synchronisierung ist in zwei Bereiche unterteilt.  Sie können Änderungen separat vom Herunterladen neuer Elemente übertragen.  Hier sehen Sie eine typische Synchronisierungsmethode:

```csharp
public async Task SyncAsync()
{
    ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

    try
    {
        await this.client.SyncContext.PushAsync();

        await this.todoTable.PullAsync(
            //The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
            //Use a different query name for each unique query in your program
            "allTodoItems",
            this.todoTable.CreateQuery());
    }
    catch (MobileServicePushFailedException exc)
    {
        if (exc.PushResult != null)
        {
            syncErrors = exc.PushResult.Errors;
        }
    }

    // Simple error/conflict handling. A real application would handle the various errors like network conditions,
    // server conflicts and others via the IMobileServiceSyncHandler.
    if (syncErrors != null)
    {
        foreach (var error in syncErrors)
        {
            if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
            {
                //Update failed, reverting to server's copy.
                await error.CancelAndUpdateItemAsync(error.Result);
            }
            else
            {
                // Discard local change.
                await error.CancelAndDiscardItemAsync();
            }

            Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
        }
    }
}
```

Wenn das erste Argument für `PullAsync` null ist, wird die inkrementelle Synchronisierung nicht verwendet.  Jeder Synchronisierungsvorgang ruft alle Datensätze ab.

Das SDK führt vor dem Abrufen von Datensätzen eine implizite `PushAsync()` durch.

Die Konfliktbehandlung erfolgt über eine `PullAsync()`-Methode.  Sie können Konflikte ebenso behandeln wie Onlinetabellen.  Der Konflikt entsteht, wenn `PullAsync()` statt „insert“, „update“ oder „delete“ aufgerufen wird. Wenn mehrere Konflikte auftreten, werden sie in eine MobileServicePushFailedException gebündelt.  Behandeln Sie jeden Fehler einzeln.

## <a name="#customapi"></a>Arbeiten mit einer benutzerdefinierten API
Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Durch die Verwendung einer benutzerdefinierten API erhalten Sie mehr Kontrolle über das Messaging, einschließlich Lesen und Einstellen der HTTP-Nachrichten-Header sowie Definieren eines von JSON abweichenden Nachrichtentextformats.

Sie rufen eine benutzerdefinierte API auf, indem Sie auf dem Client eine der [InvokeApiAsync] -Methoden aufrufen. Mit der folgenden Codezeile wird beispielsweise eine POST-Anforderung an die **completeAll** -API im Back-End gesendet:

```javascript
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

Diese Form ist ein typisierter Methodenaufruf, der erfordert, dass der **MarkAllResult**-Rückgabetyp definiert ist. Typisierte und nicht typisierte Methoden werden unterstützt.

Die InvokeApiAsync()-Methode stellt „/api/“ der API voran, die Sie aufrufen möchten, sofern diese API nicht mit einem Schrägstrich „/“ beginnt.
Beispiel:

* `InvokeApiAsync("completeAll",...)` ruft /api/completeAll auf dem Back-End auf.
* `InvokeApiAsync("/.auth/me",...)` ruft /.auth/me auf dem Back-End auf.

Sie können mit InvokeApiAsync jede WebAPI aufrufen, einschließlich WebAPIs, die nicht in Azure Mobile Apps definiert sind.  Bei Verwendung von InvokeApiAsync() werden die entsprechenden Header, einschließlich der Authentifizierungsheader, mit der Anforderung gesendet.

## <a name="authentication"></a>Authentifizieren von Benutzern
Mobile Apps unterstützt die Authentifizierung und Autorisierung von App-Benutzern mithilfe verschiedener externer Identitätsanbieter: Facebook, Google, Microsoft-Konto, Twitter und Azure Active Directory. Sie können Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Außerdem können Sie die Identität authentifizierter Benutzer verwenden, um Autorisierungsregeln in Serverskripts zu implementieren. Weitere Informationen finden Sie im Lernprogramm [Hinzufügen von Authentifizierung zur App].

Zwei Authentifizierungsflüsse werden unterstützt: *vom Client verwalteter* und *vom Server verwalteter* Fluss. Der vom Server verwaltete Fluss bietet die einfachste Authentifizierungsform, da in diesem Fall die Authentifizierungs-Webschnittstelle des Anbieters verwendet wird. Der vom Client verwaltete Fluss ermöglicht eine tiefere Integration mit gerätespezifischen Fähigkeiten, da in diesem Fall anbieterspezifische und gerätespezifische SDKs verwendet werden.

> [!NOTE]
> In Ihren Produktions-Apps sollten Sie einen vom Client verwalteten Fluss nutzen.

Zum Einrichten der Authentifizierung müssen Sie Ihre App bei mindestens einem Identitätsanbieter registrieren.  Der Identitätsanbieter generiert eine Client-ID und einen geheimen Clientschlüssel für Ihre App.  Diese Werte werden in Ihrem Back-End festgelegt, um die Authentifizierung/Autorisierung über Azure App Service zu aktivieren.  Weitere Informationen finden Sie in den detaillierten Anweisungen im Tutorial [Hinzufügen von Authentifizierung zur App].

In diesem Abschnitt werden die folgenden Themen behandelt:

* [Vom Client verwaltete Authentifizierung](#clientflow)
* [Vom Server verwaltete Authentifizierung](#serverflow)
* [Zwischenspeichern des Authentifizierungstokens](#caching)

### <a name="clientflow"></a>Vom Client verwaltete Authentifizierung
Ihre App kann den Identitätsanbieter unabhängig kontaktieren und das zurückgegebene Token bei der Anmeldung bei Ihrem Back-End vorlegen. Mit diesem Clientfluss können Sie die einmalige Anmeldung für Ihre Benutzer implementieren oder zusätzliche Benutzerdaten vom Identitätsanbieter abrufen. Da das Identitätsanbieter-SDK ein intuitiveres Benutzererlebnis und zusätzliche Anpassungsoptionen bietet, ist die Clientflussauthentifizierung dem Serverfluss vorzuziehen.

Für die folgenden clientflussbezogenen Authentifizierungsmuster werden Beispiele bereitgestellt:

* [Active Directory-Authentifizierungsbibliothek](#adal)
* [Facebook oder Google](#client-facebook)

#### <a name="adal"></a>Authentifizieren von Benutzern mit der Active Directory Authentication Library
Nutzen Sie die Active Directory-Authentifizierungsbibliothek (Active Directory Authentication Library, ADAL), um die Benutzerauthentifizierung auf dem Client mithilfe der Azure Active Directory-Authentifizierung einzuleiten.

1. Konfigurieren Sie Ihr mobiles App-Back-End für die AAD-Anmeldung, indem Sie die im Tutorial [Konfigurieren von App Service für eine Active Directory-Anmeldung] beschriebenen Schritte ausführen. Schließen Sie auch den optionalen Schritt zur Registrierung einer nativen Clientanwendung ab.
2. Öffnen Sie Ihr Projekt in Visual Studio oder Xamarin Studio, und fügen Sie einen Verweis auf das NuGet-Paket `Microsoft.IdentityModel.Clients.ActiveDirectory` hinzu. Nehmen Sie in die Suche auch Vorabversionen auf.
3. Fügen Sie je nach verwendeter Plattform den folgenden Code zu Ihrer Anwendung hinzu. Nehmen Sie dabei die folgenden Änderungen vor:

   * Ersetzen Sie **INSERT-AUTHORITY-HERE** durch den Namen des Mandanten, in dem Sie Ihre Anwendung bereitgestellt haben. Das Format muss https://login.microsoftonline.com/contoso.onmicrosoft.com sein. Sie können diesen Wert im [Azure-Portal] in Ihrer Azure Active Directory-Instanz auf der Registerkarte „Domäne“ kopieren.
   * Ersetzen Sie **INSERT-RESOURCE-ID-HERE** durch die Client-ID für Ihr mobiles App-Back-End. Sie können die Client-ID im Portal auf der Registerkarte **Erweitert** unter **Azure Active Directory-Einstellungen** abrufen.
   * Ersetzen Sie **INSERT-CLIENT-ID-HERE** durch die Client-ID, die Sie aus der nativen Clientanwendung kopiert haben.
   * Ersetzen Sie mithilfe des HTTPS-Schemas **INSERT-REDIRECT-URI-HERE** durch den Endpunkt */.auth/login/done* Ihrer Website. Dieser Wert sollte *https://contoso.azurewebsites.net/.auth/login/done* ähnlich sein.

     Der für jede Plattform erforderliche Code:

     **Windows:**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync()
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        while (user == null)
        {
            string message;
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await App.MobileService.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                message = string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
     }
     ```

     **Xamarin.iOS**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync(UIViewController view)
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(view));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
     }
     ```

     **Xamarin.Android**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync()
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(this));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(ex.Message);
            builder.SetTitle("You must log in. Login Required");
            builder.Create().Show();
        }
     }
     protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
     {

        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
     }
     ```

#### <a name="client-facebook"></a>Einmaliges Anmelden mit einem Token von Facebook oder Google
Der folgende Codeausschnitt zeigt den Clientfluss für Facebook oder Google.

```csharp
var token = new JObject();
// Replace access_token_value with actual value of your access token obtained
// using the Facebook or Google SDK.
token.Add("access_token", "access_token_value");

private MobileServiceUser user;
private async Task AuthenticateAsync()
{
    while (user == null)
    {
        string message;
        try
        {
            // Change MobileServiceAuthenticationProvider.Facebook
            // to MobileServiceAuthenticationProvider.Google if using Google auth.
            user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
            message = string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

### <a name="serverflow"></a>Vom Server verwaltete Authentifizierung
Rufen Sie nach der Registrierung Ihres Identitätsanbieters die [LoginAsync]-Methode für den MobileServiceClient mit dem [MobileServiceAuthenticationProvider]-Wert Ihres Anbieters auf. Der folgende Beispielcode startet eine Serverfluss-Anmeldung über Facebook.

```csharp
private MobileServiceUser user;
private async System.Threading.Tasks.Task Authenticate()
{
    while (user == null)
    {
        string message;
        try
        {
            user = await client
                .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
            message =
                string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den Wert für [MobileServiceAuthenticationProvider] oben entsprechend Ihrem Anbieter.

In einem Serverfluss verwaltet Azure App Service den OAuth-Authentifizierungsfluss durch Anzeigen der Anmeldeseite des ausgewählten Anbieters.  Sobald der Identitätsanbieter eine Antwort zurückgibt, generiert Azure App Service ein App Service-Authentifizierungstoken. Die [LoginAsync]-Methode gibt einen [MobileServiceUser] zurück, der sowohl die [UserId] des authentifizierten Benutzers als auch das [MobileServiceAuthenticationToken] in Form eines JSON-Webtokens (JWT) bereitstellt. Dieses Token kann zwischengespeichert und wiederverwendet werden, bis es abläuft. Weitere Informationen finden Sie unter [Zwischenspeichern des Authentifizierungstokens](#caching).

### <a name="caching"></a>Zwischenspeichern des Authentifizierungstokens
In einigen Fällen kann der Aufruf der Anmeldemethode nach der ersten erfolgreichen Authentifizierung vermieden werden, indem das Authentifizierungstoken des Anbieters gespeichert wird.  Microsoft Store- und UWP-Apps können nach einer erfolgreichen Anmeldung [Kennworttresor] zum Zwischenspeichern des aktuellen Authentifizierungstokens verwenden:

```csharp
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

Die Benutzer-ID wird als Benutzername der Anmeldeinformationen und das Token als Kennwort gespeichert. Bei nachfolgenden Systemstarts können Sie **PasswordVault** auf zwischengespeicherte Anmeldeinformationen überprüfen. Das folgende Beispiel verwendet zwischengespeicherte Anmeldeinformationen, sobald diese gefunden werden, und versucht andernfalls, eine erneute Authentifizierung beim Back-End:

```csharp
// Try to retrieve stored credentials.
var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
if (creds != null)
{
    // Create the current user from the stored credentials.
    client.currentUser = new MobileServiceUser(creds.UserName);
    client.currentUser.MobileServiceAuthenticationToken =
        vault.Retrieve("Facebook", creds.UserName).Password;
}
else
{
    // Regular login flow and cache the token as shown above.
}
```

Wenn Sie einen Benutzer abmelden, müssen Sie auch die gespeicherten Anmeldeinformationen wie folgt entfernen:

```csharp
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Xamarin-Apps verwenden die [Xamarin.Auth] -APIs zum sicheren Speichern von Anmeldeinformationen in einem **Account** -Objekt. Ein Beispiel für die Verwendung dieser APIs finden Sie in der Codedatei [AuthStore.cs] im [ContosoMoments-Beispiel für das Teilen von Fotos](https://github.com/azure-appservice-samples/ContosoMoments).

Wenn Sie die vom Client verwaltete Authentifizierung verwenden, können Sie auch das von einem Anbieter wie Facebook oder Twitter erhaltene Zugriffstoken zwischenspeichern. Dieses Token kann angegeben werden, um ein neues Authentifizierungstoken wie folgt vom Back-End anzufordern:

```csharp
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="pushnotifications"></a>Pushbenachrichtigungen
Die folgenden Themen behandeln Pushbenachrichtigungen:

* [Registrieren für Pushbenachrichtigungen](#register-for-push)
* [Abrufen einer Microsoft Store-Paket-SID](#package-sid)
* [Registrieren von Pushvorlagen zum Senden plattformübergreifender Benachrichtigungen](#register-xplat)

### <a name="register-for-push"></a>Vorgehensweise: Registrieren für Pushbenachrichtigungen
Mit dem Mobile Apps-Client können Sie die App für Pushbenachrichtigungen mit Azure Notification Hubs registrieren. Wenn Sie sich registrieren, erhalten Sie ein Handle, das vom plattformspezifischen Pushbenachrichtigungsdienst bezogen wird. Diesen Wert müssen Sie zusammen mit allen übrigen Tags beim Registrieren angeben. Der folgende Code registriert Ihre Windows-App für Pushbenachrichtigungen mit dem Windows-Benachrichtigungsdienst (Windows Notification Service, WNS):

```csharp
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

Für Pushvorgänge an den WNS MÜSSEN Sie [eine Microsoft Store-Paket-SID abrufen](#package-sid).  Weitere Informationen zu Windows-Apps, z. B. zum Registrieren für Vorlagenregistrierungen, finden Sie unter [Hinzufügen von Pushbenachrichtigungen zur App].

Die Anforderung von Tags vom Client wird nicht unterstützt.  Tag-Anforderungen werden automatisch aus der Registrierung gelöscht.
Wenn Sie Ihr Gerät mit Tags registrieren möchten, erstellen Sie eine benutzerdefinierte API, die die Notification Hubs-API verwendet, um die Registrierung in Ihrem Namen auszuführen.  Rufen Sie anstelle der `RegisterNativeAsync()`-Methode die benutzerdefinierte API auf.

### <a name="package-sid"></a>Gewusst wie: Abrufen einer Microsoft Store-Paket-SID
Für Pushbenachrichtigungen in Microsoft Store-Apps ist eine Paket-SID erforderlich.  Registrieren Sie Ihre Anwendung beim Microsoft Store, um eine Paket-SID zu erhalten.

Dieser Wert wird wie folgt abgerufen:

1. Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf das Microsoft Store-App-Projekt, und klicken Sie dann auf **Store** > **App mit Store verknüpfen...** .
2. Klicken Sie im Assistenten auf **Weiter**, melden Sie sich mit Ihrem Microsoft-Konto an, geben Sie unter **App-Namen reservieren** einen Namen für Ihre App ein, und klicken Sie dann auf **Reservieren**.
3. Nachdem die App-Registrierung erfolgreich erstellt wurde, wählen Sie den Namen der App aus, und klicken Sie auf **Weiter** und dann auf **Zuordnen**.
4. Melden Sie sich mit Ihrem Microsoft-Konto beim [Windows Dev Center] an. Klicken Sie unter **Meine Apps**auf die erstellte App-Registrierung.
5. Klicken Sie auf **App-Verwaltung** > **App-Identität**, und scrollen Sie nach unten zu Ihrer **Paket-SID**.

In vielen Anwendungsfällen wird die Paket-SID als URI behandelt. In diesem Fall müssen Sie *ms-app://* als Schema verwenden. Notieren Sie sich die Version der Paket-SID, die durch Verkettung dieses Werts als Präfix gebildet wird.

Xamarin-Apps erfordern zusätzlichen Code, um eine App registrieren zu können, die auf iOS- oder Android-Plattformen ausgeführt wird. Weitere Informationen finden Sie im Thema zu Ihrer Plattform:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="register-xplat"></a>Vorgehensweise: Registrieren von Pushvorlagen zum Senden plattformübergreifender Benachrichtigungen
Verwenden Sie die `RegisterAsync()` -Methode wie folgt, um Vorlagen zu registrieren:

```csharp
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

Ihre Vorlagen sollten den Typ `JObject` aufweisen und können mehrere Vorlagen im folgenden JSON-Format enthalten:

```csharp
public JObject myTemplates()
{
    // single template for Windows Notification Service toast
    var template = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(message)</text></binding></visual></toast>";

    var templates = new JObject
    {
        ["generic-message"] = new JObject
        {
            ["body"] = template,
            ["headers"] = new JObject
            {
                ["X-WNS-Type"] = "wns/toast"
            },
            ["tags"] = new JArray()
        },
        ["more-templates"] = new JObject {...}
    };
    return templates;
}
```

Die **RegisterAsync()** -Methode akzeptiert auch sekundäre Kacheln:

```csharp
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

Während der Registrierung werden aus Sicherheitsgründen alle Tags entfernt. Informationen zum Hinzufügen von Tags zu Installationen bzw. Vorlagen innerhalb von Installationen finden Sie unter „Verwenden des .NET-Back-End-Server SDK für Azure Mobile Apps“.

Informationen zum Senden von Benachrichtigungen unter Verwendung dieser registrierten Vorlagen finden Sie unter [Notification Hubs-APIs].

## <a name="misc"></a>Verschiedene Themen
### <a name="errors"></a>Vorgehensweise: Fehlerbehandlung
Tritt ein Fehler im Back-End auf, löst das Client-SDK eine `MobileServiceInvalidOperationException`aus.  Das folgende Beispiel zeigt, wie eine vom Back-End zurückgegebene Ausnahme behandelt wird:

```csharp
private async void InsertTodoItem(TodoItem todoItem)
{
    // This code inserts a new TodoItem into the database. When the operation completes
    // and App Service has assigned an Id, the item is added to the CollectionView
    try
    {
        await todoTable.InsertAsync(todoItem);
        items.Add(todoItem);
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // Handle error
    }
}
```

Sie finden ein weiteres Beispiel für die Behandlung von Fehlerbedingungen im [Beispiel für Mobile Apps-Dateien]. Das [LoggingHandler]-Beispiel stellt einen Delegathandler für die Protokollierung der Anforderungen an das Back-End bereit.

### <a name="headers"></a>Vorgehensweise: Anpassen von Anforderungsheadern
Um Ihr spezielles App-Szenario zu unterstützen, müssen Sie unter Umständen die Kommunikation mit dem Mobile App-Back-End anpassen. Sie können z. B. benutzerdefinierte Header zu allen ausgehenden Anforderungen hinzufügen oder den Statuscode von Antworten ändern. Sie können einen benutzerdefinierten [DelegatingHandler] bereitstellen, wie im folgenden Beispiel gezeigt:

```csharp
public async Task CallClientWithHandler()
{
    MobileServiceClient client = new MobileServiceClient("AppUrl", new MyHandler());
    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
    var newItem = new TodoItem { Text = "Hello world", Complete = false };
    await todoTable.InsertAsync(newItem);
}

public class MyHandler : DelegatingHandler
{
    protected override async Task<HttpResponseMessage>
        SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
    {
        // Change the request-side here based on the HttpRequestMessage
        request.Headers.Add("x-my-header", "my value");

        // Do the request
        var response = await base.SendAsync(request, cancellationToken);

        // Change the response-side here based on the HttpResponseMessage

        // Return the modified response
        return response;
    }
}
```


<!-- Anchors. -->


<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-windows-store-dotnet-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[4]: https://msdn.microsoft.com/library/azure/mt419521(v=azure.10).aspx
[5]: https://github.com/Azure-Samples
[6]: https://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: https://github.com/SymbolSource/SymbolSource
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[Hinzufügen von Authentifizierung zur App]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Offlinedatensynchronisierung in Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Hinzufügen von Pushbenachrichtigungen zur App]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Register your app to use a Microsoft account login]: ../app-service/configure-authentication-provider-microsoft.md
[Konfigurieren von App Service für eine Active Directory-Anmeldung]: ../app-service/configure-authentication-provider-aad.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/library/azure/jj554275(v=azure.10).aspx
[erstellt einen Verweis auf eine nicht typisierte Tabelle]: https://msdn.microsoft.com/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/library/azure/mt691741(v=azure.10).aspx
[Take]: https://msdn.microsoft.com/library/azure/dn250574(v=azure.10).aspx
[Select]: https://msdn.microsoft.com/library/azure/dn250569(v=azure.10).aspx
[Skip]: https://msdn.microsoft.com/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/library/azure/dn250536.(v=azure.10)aspx
[UserID]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Where]: https://msdn.microsoft.com/library/azure/dn250579(v=azure.10).aspx
[Azure-Portal]: https://portal.azure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: https://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Windows Dev Center]: https://dev.windows.com/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[Kennworttresor]: https://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: https://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Notification Hubs-APIs]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Beispiel für Mobile Apps-Dateien]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[OData v3-Dokumentation]: https://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: https://www.telerik.com/fiddler
[Json.NET]: https://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
