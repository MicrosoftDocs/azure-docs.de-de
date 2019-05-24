---
title: 'ASP.NET MVC-Tutorial für Azure Cosmos DB: Entwicklung von Webanwendungen'
description: Enthält ein ASP.NET MVC-Tutorial zum Erstellen einer MVC-Webanwendung mit Azure Cosmos DB. Speichern von JSON- und Zugriffsdaten über eine Todo-App, die in Azure Websites gehostet wird – Schrittanleitung im ASP NET MVC-Tutorial
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: 15cf3b1316cc35e22538ca353302c4a82d2d418b
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65979015"
---
# <a name="_Toc395809351"></a>ASP.NET MVC-Tutorial: Entwicklung von Webanwendungen mit Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Um zu verdeutlichen, wie Sie Azure Cosmos DB effizient zum Speichern und Abfragen von JSON-Dokumenten einsetzen, wird in diesem Artikel anhand einer umfassenden exemplarischen Vorgehensweise das Erstellen einer Todo-App mithilfe von Azure Cosmos DB erläutert. Die Aufgaben werden als JSON-Dokumente in Azure Cosmos DB gespeichert.

![Screenshot der in diesem Tutorial erstellten MVC-Webanwendung für Todo-Listen – Schrittanleitung im ASP NET MVC-Tutorial](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png)

In dieser exemplarischen Vorgehensweise wird veranschaulicht, wie Sie mit dem Azure Cosmos DB-Dienst Daten aus einer in Azure gehosteten ASP.NET MVC-Webanwendung speichern und darauf zugreifen. Ein Tutorial, das nur Azure Cosmos DB und nicht die ASP.NET MVC-Komponenten behandelt, finden Sie unter [Erstellen einer Azure Cosmos DB-C#-Konsolenanwendung](sql-api-get-started.md).

> [!TIP]
> Dieses Lernprogramm setzt vorherige Erfahrung mit der Verwendung von ASP.NET MVC und Azure-Websites voraus. Wenn Sie noch nicht mit ASP.NET oder den [erforderlichen Tools](#_Toc395637760) vertraut sind, sollten Sie das vollständige Beispielprojekt von [GitHub][GitHub] herunterladen und den Anweisungen in diesem Beispiel folgen. Nachdem Sie das Projekt erstellt haben, können Sie den Artikel lesen, um Einblick in den Code im Kontext des Projekts zu erhalten.
> 
> 

## <a name="_Toc395637760"></a>Voraussetzungen für dieses Datenbanktutorial
Bevor Sie diesen Artikel durcharbeiten, sollten Sie sicherstellen, dass Folgendes vorhanden ist:

* Ein aktives Azure-Konto.  Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  
* Microsoft Azure SDK für .NET für Visual Studio-2017, verfügbar über den Visual Studio-Installer.

Alle Screenshots in diesem Artikel wurden mithilfe von Microsoft Visual Studio Community 2017 erstellt. Wenn Ihr System mit einer anderen Version konfiguriert ist, weichen Ihre Bildschirme und Optionen möglicherweise leicht ab. Wenn Sie die oben aufgeführten Voraussetzungen erfüllen, sollte diese Lösung jedoch funktionieren.

## <a name="_Toc395637761"></a>Schritt 1: Erstellen eines Azure Cosmos DB-Datenbankkontos
Wir beginnen, indem wir ein Azure Cosmos DB-Konto erstellen. Falls Sie bereits ein SQL-Konto für Azure Cosmos DB besitzen oder den Azure Cosmos DB-Emulator für dieses Tutorial verwenden, können Sie mit [Erstellen einer neuen ASP.NET MVC-Anwendung](#_Toc395637762) fortfahren.

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

<br/>
Im Folgenden wird erläutert, wie eine neue ASP.NET MVC-Anwendung von Grund auf erstellt wird. 

## <a name="_Toc395637762"></a>Schritt 2: Erstellen einer neuen ASP.NET MVC-Anwendung

1. Zeigen Sie in Visual Studio im Menü **Datei** auf **Neu**, und klicken Sie dann auf **Projekt**. Das Dialogfeld **Neues Projekt** wird angezeigt.

2. Erweitern Sie im Bereich **Projekttypen** die Einträge **Vorlagen**, **Visual C#** und **Web**, und wählen Sie dann **ASP.NET-Webanwendung** aus.

      ![Screenshot des Dialogfelds „Neues Projekt“, bei dem der Projekttyp der ASP.NET-Webanwendung hervorgehoben ist](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png)

3. Geben Sie im Feld **Name** den Namen des Projekts ein. In diesem Lernprogramm wird der Name "Todo" (Aufgabe) verwendet. Wenn Sie einen anderen Namen verwenden, müssen Sie immer dann, wenn im Lernprogramm der Todo-Namespace genannt wird, die bereitgestellten Codebeispiele auf den von Ihnen verwendeten Namen für Ihre Anwendung ändern. 
4. Klicken Sie auf **Durchsuchen**, um zu dem Ordner zu navigieren, in dem Sie das Projekt erstellen möchten, und klicken Sie dann auf **OK**.
   
      Das Dialogfeld **Neue ASP.NET-Webanwendung** wird angezeigt.
   
    ![Screenshot des Dialogfelds „Neue ASP.NET-Webanwendung“ mit hervorgehobener MVC-Anwendungsvorlage](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-MVC.png)
5. Wählen Sie im Vorlagenbereich **MVC**aus.

6. Klicken Sie auf **OK** , und lassen Sie Visual Studio die leere ASP.NET MVC-Gerüstvorlage erstellen. 

          
7. Wenn Visual Studio die Bausteine der MVC-Anwendung erstellt hat, steht Ihnen eine leere ASP.NET-Anwendung zur Verfügung, die lokal ausgeführt werden kann.
   
    Die lokale Ausführung des Projekts wird übersprungen, da sicher alle von Ihnen die ASP.NET-Anwendung "Hello World" gesehen haben. Wir fahren jetzt direkt mit dem Hinzufügen von Azure Cosmos DB zu diesem Projekt und dem Erstellen unserer Anwendung fort.

## <a name="_Toc395637767"></a>Schritt 3: Hinzufügen von Azure Cosmos DB zu Ihrem MVC-Webanwendungsprojekt
Da nun ein Großteil des für diese Lösung benötigten ASP.NET MVC-Frameworks steht, kommen wir zum wesentlichen Teil dieses Tutorials: dem Hinzufügen von Azure Cosmos DB zu unserer MVC-Webanwendung.

1. Das Azure Cosmos DB .NET SDK wird als NuGet-Paket verteilt. Verwenden Sie zum Abrufen des NuGet-Pakets in Visual Studio den NuGet-Paket-Manager in Visual Studio, indem Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt klicken und dann auf **NuGet-Pakete verwalten** klicken.
   
    ![Screenshot der Kontextmenüoptionen für das Webanwendungsprojekt im Projektmappen-Explorer, bei dem „NuGet-Pakete verwalten“ hervorgehoben ist.](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-manage-nuget.png)
   
    Das Dialogfeld **NuGet-Pakete verwalten** wird geöffnet.
2. Geben Sie im NuGet-Feld **Durchsuchen** den Suchbegriff ***Azure DocumentDB*** ein. (Der Paketname wurde nicht für Azure Cosmos DB aktualisiert.)
   
    Installieren Sie das in den Ergebnissen aufgeführte Paket **Microsoft.Azure.DocumentDB** von Microsoft. Das Azure Cosmos DB-Paket sowie alle Abhängigkeiten, z.B. Newtonsoft.Json, werden heruntergeladen und installiert. Klicken Sie im **Vorschaufenster** auf **OK** und im Fenster **Zustimmung zur Lizenz** auf **Ich stimme zu**, um die Installation abzuschließen.
   
    ![Screenshot des Fensters „NuGet-Pakete verwalten“, auf dem die Microsoft Azure Cosmos DB-Clientbibliothek hervorgehoben ist](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-install-nuget.png)
   
      Alternativ können Sie die Paketverwaltungskonsole verwenden, um das Paket zu installieren. Klicken Sie dafür im Menü **Extras** auf **NuGet-Paket-Manager** und dann auf **Paket-Manager-Konsole**. Geben Sie in der Befehlszeile Folgendes ein.
   
        Install-Package Microsoft.Azure.DocumentDB
        
3. Wenn das Paket installiert ist, sollte die Visual Studio-Projektmappe ungefähr wie folgt aussehen und die folgenden zwei neuen Verweise enthalten: Microsoft.Azure.Documents.Client und Newtonsoft.Json.
   
    ![Screenshot der zwei Verweise, die dem JSON-Datenprojekt im Projektmappen-Explorer hinzugefügt wurden](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-added-references.png)

## <a name="_Toc395637763"></a>Schritt 4: Einrichten der ASP.NET MVC-Anwendung
Jetzt werden der MVC-Anwendung die Modelle, Ansichten und Controller hinzugefügt:

* [Hinzufügen eines Modells](#_Toc395637764).
* [Hinzufügen eines Controllers](#_Toc395637765).
* [Hinzufügen von Ansichten](#_Toc395637766).

### <a name="_Toc395637764"></a>Hinzufügen eines JSON-Datenmodells
Beginnen wir zunächst mit dem Erstellen des **M** in MVC, des Modells. 

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner **Modelle**, und klicken Sie dann auf **Hinzufügen** und anschließend auf **Klasse**.
   
      Das Dialogfeld **Neues Element hinzufügen** wird angezeigt.
2. Nennen Sie die neue Klasse **Item.cs**, und klicken Sie auf **Hinzufügen**. 
3. In dieser neuen **Item.cs** -Datei fügen Sie Folgendes nach der letzten *using-Anweisung*hinzu.
   
        using Newtonsoft.Json;
4. Ersetzen Sie jetzt diesen Code 
   
        public class Item
        {
        }
   
    durch den folgenden Code.
   
        public class Item
        {
            [JsonProperty(PropertyName = "id")]
            public string Id { get; set; }
   
            [JsonProperty(PropertyName = "name")]
            public string Name { get; set; }
   
            [JsonProperty(PropertyName = "description")]
            public string Description { get; set; }
   
            [JsonProperty(PropertyName = "isComplete")]
            public bool Completed { get; set; }
        }
   
    Alle Daten in Azure Cosmos DB werden über das Netzwerk übertragen und als JSON gespeichert. Um zu kontrollieren, wie Ihre Objekte von JSON.NET serialisiert bzw. deserialisiert wurden, können Sie das **JsonProperty**-Attribut verwenden, wie in der erstellten **Item**-Klasse dargestellt. Dies ist nicht **unbedingt** erforderlich, aber es ist nützlich, wenn sichergestellt werden soll, dass für die Eigenschaften die camelCase-Benennungskonventionen des JSON-Codes befolgt wurden. 
   
    Sie können nicht nur das Format des Eigenschaftennamens bei der Weitergabe an JSON steuern, sondern die .NET-Eigenschaften sogar vollständig umbenennen, wie in diesem Beispiel mithilfe der **Description** -Eigenschaft geschehen. 

### <a name="_Toc395637765"></a>Hinzufügen eines Controllers
Das **M** ist damit abgedeckt, und nun wird das **C** (eine Controllerklasse) in MVC erstellt.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner **Controller**, und klicken Sie dann auf **Hinzufügen** und **Controller**.
   
    Das Dialogfeld **Gerüst hinzufügen** wird angezeigt.
2. Wählen Sie **MVC 5-Controller – Leer** aus, und klicken Sie dann auf **Hinzufügen**.
   
    ![Screenshot des Dialogfelds „Gerüst hinzufügen“, bei dem die Option „MVC 5-Controller – Leer“ hervorgehoben ist](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png)
3. Geben Sie dem neuen Controller den Namen **ItemController.**
   
    ![Screenshot des Dialogfelds „Controller hinzufügen“](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-controller.png)
   
    Nachdem die Datei erstellt wurde, sollte die Visual Studio-Projektmappe ungefähr wie folgt aussehen, und die neue Datei "ItemController.cs" sollte im **Projektmappen-Explorer**angezeigt werden. Die neue Item.cs-Datei, die zuvor erstellt wurde, wird ebenfalls angezeigt.
   
    ![Screenshot der Visual Studio-Projektmappe – Projektmappen-Explorer, in dem die neue ItemController.cs-Datei und die Item.cs-Datei hervorgehoben sind](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-item-solution-explorer.png)
   
    Sie können ItemController.cs schließen, darauf kommen wir zu einem späteren Zeitpunkt zurück. 

### <a name="_Toc395637766"></a>Hinzufügen von Ansichten
Nun wird das **V** in MVC erstellt, die Ansichten (Views):

* [Hinzufügen einer Elementindexansicht](#AddItemIndexView).
* [Hinzufügen einer neuen Elementansicht](#AddNewIndexView).
* [Hinzufügen einer Elementbearbeitungsansicht](#_Toc395888515).

#### <a name="AddItemIndexView"></a>Hinzufügen einer Elementindexansicht
1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Ansichten**, klicken Sie mit der rechten Maustaste auf den leeren Ordner **Item**, der zuvor beim Hinzufügen des **ItemController**-Elements von Visual Studio erstellt wurde, und klicken Sie auf **Hinzufügen** und dann auf **Ansicht**.
   
    ![Screenshot des Projektmappen-Explorers, in dem der von Visual Studio erstellte Ordner „Element“ gezeigt wird und die Befehle „Hinzufügen“ > „Ansicht“ hervorgehoben sind](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view.png)
2. Gehen Sie im Dialogfeld **Ansicht hinzufügen** folgendermaßen vor:
   
   * Geben Sie im Feld **Ansichtsname** den Namen ***Index*** ein.
   * Wählen Sie im Feld **Vorlage** die Option ***Liste*** aus.
   * Wählen Sie im Feld **Modellklasse** die Option ***Item (todo.Models)*** aus.
   * Geben Sie im Feld für die Layoutseite ***~/Views/Shared/_Layout.cshtml*** ein.
     
   ![Screenshot des Dialogfelds „Ansicht hinzufügen“](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-view-dialog.png)
3. Wenn alle Werte festgelegt sind, klicken Sie auf **Hinzufügen** , und Visual Studio erstellt eine neue Vorlagenansicht. Wenn dies abgeschlossen ist, wird die erstellte CSHTML-Datei geöffnet. Diese Datei kann in Visual Studio geschlossen werden, da wir erst später darauf zurückkommen.

#### <a name="AddNewIndexView"></a>Hinzufügen einer neuen Elementansicht
Ähnlich wie eine Ansicht vom Typ **Elementindex** wird nun eine neue Ansicht zum Erstellen neuer **Elemente** erstellt.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste erneut auf den Ordner **Element**, und klicken Sie dann auf **Hinzufügen** und anschließend auf **Ansicht**.
2. Gehen Sie im Dialogfeld **Ansicht hinzufügen** folgendermaßen vor:
   
   * Geben Sie im Feld **Ansichtsname** den Namen ***Create*** ein.
   * Wählen Sie im Feld **Vorlage** die Option ***Erstellen*** aus.
   * Wählen Sie im Feld **Modellklasse** die Option ***Item (todo.Models)*** aus.
   * Geben Sie im Feld für die Layoutseite ***~/Views/Shared/_Layout.cshtml*** ein.
   * Klicken Sie auf **Hinzufügen**.
   
#### <a name="_Toc395888515"></a>Hinzufügen einer Elementbearbeitungsansicht
Fügen Sie abschließend eine letzte Ansicht zum Bearbeiten eines **Elements** auf dieselbe Art wie vorher hinzu.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste erneut auf den Ordner **Element**, und klicken Sie dann auf **Hinzufügen** und anschließend auf **Ansicht**.
2. Gehen Sie im Dialogfeld **Ansicht hinzufügen** folgendermaßen vor:
   
   * Geben Sie im Feld **Ansichtsname** den Namen ***Edit*** ein.
   * Wählen Sie im Feld **Vorlage** die Option ***Bearbeiten*** aus.
   * Wählen Sie im Feld **Modellklasse** die Option ***Item (todo.Models)*** aus.
   * Geben Sie im Feld für die Layoutseite ***~/Views/Shared/_Layout.cshtml*** ein.
   * Klicken Sie auf **Hinzufügen**.

Wenn dies abgeschlossen ist, schließen Sie alle CSHTML-Dokumente in Visual Studio, da wir erst später zu diesen Ansichten zurückkehren.

## <a name="_Toc395637769"></a>Schritt 5: Einrichten von Azure Cosmos DB
Nachdem nun alle grundlegenden Dinge zu MVC behandelt wurden, fügen wir den Code für Azure Cosmos DB hinzu. 

In diesem Abschnitt fügen wir Code zur Ausführung folgender Aufgaben hinzu:

* [Auflisten unvollständiger Elemente](#_Toc395637770).
* [Hinzufügen von Elementen](#_Toc395637771).
* [Bearbeiten von Elementen](#_Toc395637772).

### <a name="_Toc395637770"></a>Auflisten unvollständiger Elemente in Ihrer MVC-Webanwendung
Hier muss zunächst eine Klasse hinzugefügt werden, die die gesamte Logik zur Verbindung mit und Verwendung von Azure Cosmos DB enthält. Für dieses Lernprogramm kapseln wir die gesamte Logik in einer Repository-Klasse namens DocumentDBRepository. 

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und klicken Sie auf **Hinzufügen** und anschließend auf **Klasse**. Nennen Sie die neue Klasse **DocumentDBRepository**, und klicken Sie auf **Hinzufügen**.
2. In der neu erstellten **DocumentDBRepository**-Klasse fügen Sie die folgenden *using-Anweisungen* über die *Namespace*deklaration hinzu.
   
        using Microsoft.Azure.Documents; 
        using Microsoft.Azure.Documents.Client; 
        using Microsoft.Azure.Documents.Linq; 
        using System.Configuration;
        using System.Linq.Expressions;
        using System.Threading.Tasks;
        using System.Net;
        
    Ersetzen Sie jetzt diesen Code 
   
        public class DocumentDBRepository
        {
        }
   
    durch den folgenden Code.
   
        public static class DocumentDBRepository<T> where T : class
        {
            private static readonly string DatabaseId = ConfigurationManager.AppSettings["database"];
            private static readonly string CollectionId = ConfigurationManager.AppSettings["collection"];
            private static DocumentClient client;
   
            public static void Initialize()
            {
                client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
                CreateDatabaseIfNotExistsAsync().Wait();
                CreateCollectionIfNotExistsAsync().Wait();
            }
   
            private static async Task CreateDatabaseIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
   
            private static async Task CreateCollectionIfNotExistsAsync()
            {
                try
                {
                    await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId));
                }
                catch (DocumentClientException e)
                {
                    if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
                    {
                        await client.CreateDocumentCollectionAsync(
                            UriFactory.CreateDatabaseUri(DatabaseId),
                            new DocumentCollection { Id = CollectionId },
                            new RequestOptions { OfferThroughput = 1000 });
                    }
                    else
                    {
                        throw;
                    }
                }
            }
        }
   
    
3. Nun werden einige Werte aus der Konfiguration gelesen. Öffnen Sie die Datei **Web.config** Ihrer Anwendung, und fügen Sie die folgenden Zeilen unter dem Abschnitt `<AppSettings>` hinzu.
   
        <add key="endpoint" value="enter the URI from the Keys blade of the Azure Portal"/>
        <add key="authKey" value="enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure  Portal"/>
        <add key="database" value="ToDoList"/>
        <add key="collection" value="Items"/>
4. Aktualisieren Sie nun die Werte für *endpoint* und *authKey*, indem Sie das Blatt „Schlüssel“ im Azure-Portal verwenden. Verwenden Sie den **URI** aus dem Blatt „Schlüssel“ als Wert für die endpoint-Einstellung, und verwenden Sie den **PRIMÄRSCHLÜSSEL** oder den **SEKUNDÄRSCHLÜSSEL** aus dem Blatt „Schlüssel“ als Wert für die authKey-Einstellung.

    Auf diese Weise wird das Azure Cosmos DB-Repository verknüpft. Als Nächstes fügen wir die Anwendungslogik hinzu.

1. Die erste Aufgabe, die mit einer To-Do-List-Anwendung ausgeführt werden soll, ist die Anzeige nicht abgeschlosssener Elemente.  Kopieren Sie den folgenden Codeausschnitt und fügen Sie ihn in eine beliebige Stelle innerhalb der **DocumentDBRepository** Klasse ein.
   
        public static async Task<IEnumerable<T>> GetItemsAsync(Expression<Func<T, bool>> predicate)
        {
            IDocumentQuery<T> query = client.CreateDocumentQuery<T>(
                UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId))
                .Where(predicate)
                .AsDocumentQuery();
   
            List<T> results = new List<T>();
            while (query.HasMoreResults)
            {
                results.AddRange(await query.ExecuteNextAsync<T>());
            }
   
            return results;
        }
2. Öffnen Sie den zuvor hinzugefügten **ItemController** und fügen Sie die folgenden *using-Anweisungen* über der Namespace-Deklaration hinzu.
   
        using System.Net;
        using System.Threading.Tasks;
        using todo.Models;
   
    Wenn das Projekt nicht "Todo" heißt, müssen Sie es auf "Todo.Modelle" aktualisieren, um den Namen Ihres Projekts wiederzugeben.
   
    Ersetzen Sie jetzt diesen Code
   
        //GET: Item
        public ActionResult Index()
        {
            return View();
        }
   
    durch den folgenden Code.
   
        [ActionName("Index")]
        public async Task<ActionResult> IndexAsync()
        {
            var items = await DocumentDBRepository<Item>.GetItemsAsync(d => !d.Completed);
            return View(items);
        }
3. Öffnen Sie **Global.asax.cs**, und fügen Sie der **Application_Start**-Methode die folgende Zeile hinzu: 
   
        DocumentDBRepository<todo.Models.Item>.Initialize();

Jetzt sollte Ihre Lösung ohne Fehler erstellen können.

Wenn Sie die Anwendung jetzt ausführen, werden Sie zum **HomeController** und der Ansicht **Index** dieses Controllers geleitet. Das ist das Standardverhalten für das MVC-Vorlagenprojekt, das am Anfang ausgewählt wurde, das möchten wir jedoch nicht! Zum Ändern dieses Verhaltens muss die Weiterleitung dieser MVC-Anwendung geändert werden.

Öffnen Sie ***App\_Start\RouteConfig.cs***, navigieren Sie zur Zeile, die mit „defaults“ beginnt, und ändern Sie diese auf folgende Weise:

        defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Damit weiß ASP.NET MVC, dass Sie keinen Wert in der URL angegeben haben, um das Routingverhalten zu steuern, bei dem anstelle von **Home** **Item** als Controller und den Benutzer-**Index** als Ansicht verwendet wird.

Wenn Sie jetzt die Anwendung ausführen, wird Ihr **ItemController** aufgerufen. Darin wird die „Repository“-Klasse aufgerufen und die „GetItems“-Methode verwendet, um alle unvollständigen Elemente an die Ansicht **Ansichten**\\**Element**\\**Index** zurückzugeben. 

Wenn Sie dieses Projekt jetzt erstellen und ausführen, sollte ein Ergebnis ähnlich dem folgenden angezeigt werden.    

![Screenshot der in diesem Datenbanktutorial erstellten Aufgabenliste-Webanwendung](./media/sql-api-dotnet-application/build-and-run-the-project-now.png)

### <a name="_Toc395637771"></a>Hinzufügen von Elementen
Als Nächstes werden einige Elemente zur Datenbank hinzugefügt, damit mehr als eine leere Tabelle angezeigt wird.

Wir fügen den Elementen DBRepository und ItemController von Azure Cosmos DB Code hinzu, um den Datensatz in Azure Cosmos DB beizubehalten.

1. Fügen Sie Ihrer **DocumentDBRepository** -Klasse die folgende Methode hinzu.
   
       public static async Task<Document> CreateItemAsync(T item)
       {
           return await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId), item);
       }
   
   Mit dieser Methode wird ein übergebenes Objekt in Azure Cosmos DB gespeichert.
2. Öffnen Sie die Datei „ItemController.cs“ und fügen Sie den folgenden Codeausschnitt in die Klasse ein. So weiß ASP.NET MVC, wie der Vorgang **Erstellen** durchgeführt werden muss. In diesem Fall soll lediglich die zuvor erstellte zugehörige Ansicht "Create.cshtml" gerendert werden.
   
        [ActionName("Create")]
        public async Task<ActionResult> CreateAsync()
        {
            return View();
        }
   
    Es ist noch weiterer Code in diesem Controller notwendig, mit dem die Übermittlung aus der Ansicht **Erstellen** akzeptiert wird.
3. Fügen Sie den nächsten Codeblock zur ItemController.cs-Klasse hinzu, sodass ASP.NET MVC mitgeteilt wird, was mit einem Formular POST für diesen Controller geschehen soll.
   
        [HttpPost]
        [ActionName("Create")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> CreateAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.CreateItemAsync(item);
                return RedirectToAction("Index");
            }
   
            return View(item);
        }
   
    Dieser Code ruft das DocumentDBRepository-Element auf und verwendet die CreateItemAsync-Methode, um das neue Todo-Element in der Datenbank beizubehalten. 
   
    **Sicherheitshinweis**: Das Attribut **ValidateAntiForgeryToken** wird hier verwendet, um den Schutz dieser Anwendung vor websiteübergreifenden Anforderungsfälschungen zu unterstützen. Das Hinzufügen dieses Attributs reicht jedoch nicht aus, Ihre Ansichten müssen auch mit diesem Antifälschungstoken zusammenarbeiten. Weitere Informationen zum Thema und Beispiele für eine ordnungsgemäße Implementierung finden Sie unter [Verhindern der websiteübergreifenden Anforderungsfälschung][Preventing Cross-Site Request Forgery]. Der auf [GitHub][GitHub] bereitgestellte Quellcode enthält die vollständige Implementierung.
   
    **Sicherheitshinweis**: Zum Schutz vor Overposting-Angriffen wird außerdem das Attribut **Bind** im Methodenparameter verwendet. Weitere Informationen finden Sie unter [Grundlegende CRUD-Vorgänge in ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC].

Damit ist der für das Hinzufügen neuer Elemente zur Datenbank erforderliche Code komplett.

### <a name="_Toc395637772"></a>Bearbeiten von Elementen
Eine letzte Sache bleibt noch zu tun, nämlich das Hinzufügen der Möglichkeit, **Elemente** in der Datenbank zu bearbeiten und sie als abgeschlossen zu markieren. Die Bearbeitungsansicht wurde bereits zum Projekt hinzugefügt, daher muss lediglich Code zum Controller und zur **DocumentDBRepository** -Klasse hinzugefügt werden.

1. Fügen Sie der **DocumentDBRepository** -Klasse folgenden Code hinzu;
   
        public static async Task<Document> UpdateItemAsync(string id, T item)
        {
            return await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id), item);
        }
   
        public static async Task<T> GetItemAsync(string id)
        {
            try
            {
                Document document = await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(DatabaseId, CollectionId, id));
                return (T)(dynamic)document;
            }
            catch (DocumentClientException e)
            {
                if (e.StatusCode == HttpStatusCode.NotFound)
                {
                    return null;
                }
                else
                {
                    throw;
                }
            }
        }
   
    Mit der ersten dieser Methoden ruft **GetItem** ein Element aus Azure Cosmos DB ab, das an den **ItemController** und dann an die Ansicht **Bearbeiten** zurückgegeben wird.
   
    Mit der zweiten von uns hinzugefügten Methode wird das **Dokument** in Azure Cosmos DB durch die Version des vom **ItemController** übergebenen **Dokuments** ersetzt.
2. Fügen Sie der **ItemController** -Klasse folgenden Code hinzu:
   
        [HttpPost]
        [ActionName("Edit")]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> EditAsync([Bind(Include = "Id,Name,Description,Completed")] Item item)
        {
            if (ModelState.IsValid)
            {
                await DocumentDBRepository<Item>.UpdateItemAsync(item.Id, item);
                return RedirectToAction("Index");
            }
   
            return View(item);
        }
   
        [ActionName("Edit")]
        public async Task<ActionResult> EditAsync(string id)
        {
            if (id == null)
            {
                return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
            }
   
            Item item = await DocumentDBRepository<Item>.GetItemAsync(id);
            if (item == null)
            {
                return HttpNotFound();
            }
   
            return View(item);
        }
   
    Die erste Methode verarbeitet den „Http GET“-Aufruf, der erfolgt, wenn der Benutzer in der Ansicht **Index** auf den Link **Bearbeiten** klickt. Mit dieser Methode wird ein [**Dokument**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx) aus Azure Cosmos DB abgerufen und an die Ansicht **Bearbeiten** übergeben.
   
    Die Ansicht **Bearbeiten** führt dann einen „Http POST“-Aufruf für den **IndexController** durch. 
   
    Die zweite Methode, die wir hinzugefügt haben, verarbeitet die Weitergabe des aktualisierten Objekts an Azure Cosmos DB, damit es in der Datenbank gespeichert werden kann.

Dies ist alles, was zum Ausführen unserer Anwendung, zum Auflisten nicht abgeschlossener **Elemente**, zum Hinzufügen neuer **Elemente** und zum Bearbeiten von **Elementen** notwendig ist.

## <a name="_Toc395637773"></a>Schritt 6: Lokales Ausführen der Anwendung
Um die Anwendung lokal zu testen, führen Sie die folgenden Schritte aus:

1. Drücken Sie in Visual Studio F5 , um die Anwendung im Debugmodus zu erstellen. Die Anwendung sollte erstellt, und ein Browser mit der zuvor angezeigten leeren Rasterseite sollte geöffnet werden:
   
    ![Screenshot der in diesem Datenbanktutorial erstellten Aufgabenlisten-Webanwendung](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png)
   
     
2. Klicken Sie auf den Link **Neu erstellen**, und fügen Sie Werte in die Felder **Name** und **Beschreibung** ein. Lassen Sie das Kontrollkästchen **Abgeschlossen** deaktiviert, da das neue hinzugefügte **Element** sonst den Status „Abgeschlossen“ hat und nicht in der Anfangsliste erscheint.
   
    ![Screenshot der Ansicht „Erstellen“](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-new-item.png)
3. Klicken Sie auf **Erstellen**. Sie werden zur Ansicht **Index** zurückgeleitet, und Ihr **Element** wird in der Liste angezeigt.
   
    ![Screenshot der Ansicht „Index“](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png)
   
    Sie können weitere **Elemente** zu Ihrer "Todo"-Liste hinzufügen.
    
4. Wenn Sie auf **Bearbeiten** neben einem **Element** in der Liste klicken, werden Sie zur Ansicht **Edit** weitergeleitet, in der Sie beliebige Eigenschaften Ihres Objekts aktualisieren können, einschließlich der Markierung als **Abgeschlossen**. Wenn Sie das Flag **Abgeschlossen** markieren und auf **Speichern** klicken, wird das **Element** aus der Liste der nicht abgeschlossenen Aufgaben entfernt.
   
    ![Screenshot der Ansicht „Index“ mit aktiviertem Feld „Abgeschlossen“](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png)
5. Nachdem Sie die App getestet haben, drücken Sie STRG+F5, um das Debuggen der App zu beenden. Jetzt können Sie Ihre App bereitstellen.

## <a name="_Toc395637774"></a>Schritt 7: Bereitstellen der Anwendung in Azure App Service 
Nachdem die vollständige Anwendung korrekt mit Azure Cosmos DB zusammenarbeitet, stellen wir diese Web-App in Azure App Service bereit.  

1. Zum Veröffentlichen der Anwendung müssen Sie lediglich im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt klicken und **Veröffentlichen** wählen.
   
    ![Screenshot der Option „Veröffentlichen“ im Projektmappen-Explorer](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-publish.png)

2. Klicken Sie im Dialogfeld **Veröffentlichen** auf **Microsoft Azure App Service**, und wählen Sie dann **Neu erstellen** aus, um ein App Service-Profil zu erstellen, oder klicken Sie auf **Vorhandenes auswählen**, um ein vorhandenes Profil zu verwenden.

    ![Dialogfeld „Veröffentlichen“ in Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-publish-to-existing.png)

3. Wenn Sie bereits über ein Azure App Service-Profil verfügen, geben Sie den Abonnementnamen ein. Sortieren Sie mithilfe des Filters **Ansicht** nach der Ressourcengruppe oder dem Ressourcentyp, und wählen Sie dann Ihren Azure App Service aus. 
   
    ![Dialogfeld „App Service“ in Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service.png)

4. Um ein neues Azure App Service-Profil zu erstellen, klicken Sie im Dialogfeld **Veröffentlichen** auf **Neu erstellen**. Geben Sie im Dialogfeld **App Service erstellen** den Namen Ihrer Web-App und das entsprechende Abonnement, die Ressourcengruppe und den App Service-Plan ein, und klicken Sie auf **Erstellen**.

    ![Dialogfeld „App Service erstellen“ in Visual Studio](./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service.png)

Visual Studio schließt die Veröffentlichung Ihrer Webanwendung in wenigen Sekunden ab und startet einen Browser, in dem das Ergebnis Ihrer Arbeit in Azure ausgeführt wird.



## <a name="_Toc395637775"></a>Nächste Schritte
Glückwunsch! Sie haben soeben Ihre erste ASP.NET MVC-Webanwendung unter Verwendung von Azure Cosmos DB erstellt und in Azure veröffentlicht. Der Quellcode für die vollständige Anwendung, einschließlich der Detail- und Löschfunktionen, die in diesem Lernprogramm nicht enthalten waren, kann von [GitHub][GitHub] heruntergeladen oder geklont werden. Wenn Sie diese Funktionen also zu Ihrer App hinzufügen möchten, laden Sie den Code herunter, und fügen Sie ihn dieser App hinzu.

Wenn Sie Ihrer Anwendung zusätzliche Funktionen hinzufügen möchten, können Sie sich die APIs in der [Azure Cosmos DB .NET-Bibliothek](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) ansehen. Sie können auch gerne eigene Beiträge zur Azure Cosmos DB .NET-Bibliothek auf [GitHub][GitHub] schreiben. 

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: https://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: https://go.microsoft.com/fwlink/?LinkId=317598
[GitHub]: https://github.com/Azure-Samples/documentdb-net-todo-app
