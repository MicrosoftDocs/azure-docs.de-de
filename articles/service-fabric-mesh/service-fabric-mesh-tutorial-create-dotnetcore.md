---
title: Erstellen und Bereitstellen einer App mit mehreren Diensten für Service Fabric Mesh
description: In diesem Tutorial erstellen Sie eine Azure Service Fabric Mesh-Anwendung mit mehreren Diensten, die eine mit einem Back-End-Webdienst kommunizierende ASP.NET Core-Website umfasst. Diese Anwendung debuggen Sie anschließend lokal und veröffentlichen sie in Azure.
author: georgewallace
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-csharp
ms.openlocfilehash: b0bdb3c09aead812e1c16f4d0d17aae58e141809
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626792"
---
# <a name="tutorial-create-debug-deploy-and-upgrade-a-multi-service-service-fabric-mesh-app"></a>Tutorial: Erstellen, Debuggen, Bereitstellen und Aktualisieren einer Service Fabric Mesh-App mit mehreren Diensten

> [!IMPORTANT]
> Die Vorschauversion von Azure Service Fabric Mesh wurde eingestellt. Neue Bereitstellungen über die Service Fabric Mesh-API werden nicht mehr gestattet. Unterstützung für vorhandene Bereitstellungen wird bis zum 28. April 2021 fortgesetzt.
> 
> Einzelheiten finden Sie unter [Ausmusterung der Vorschauversion von Azure Service Fabric Mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Dieses Tutorial ist der erste Teil einer Serie. Sie erfahren, wie Sie mit Visual Studio eine Azure Service Fabric Mesh-App erstellen, die über ein ASP.NET-Web-Front-End und einen ASP.NET Core Web-API-Back-End-Dienst verfügt. Anschließend debuggen Sie die App in Ihrem lokalen Entwicklungscluster. Sie veröffentlichen die App in Azure, nehmen dann Konfigurations- und Codeänderungen vor und aktualisieren die App. Abschließend löschen Sie nicht verwendete Azure-Ressourcen, damit diese Ihnen nicht in Rechnung gestellt werden.

Wenn Sie fertig sind, sind Sie mit den meisten Phasen der App-Lebenszyklusverwaltung vertraut und haben eine App erstellt, die einen Aufruf zwischen Diensten in einer Service Fabric Mesh-App veranschaulicht.

Wenn Sie die Aufgabenanwendung nicht manuell erstellen möchten, können Sie den [Quellcode für die fertige Anwendung herunterladen](https://github.com/azure-samples/service-fabric-mesh) und direkt mit [Lokales Debuggen der App](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md) fortfahren.

Im ersten Teil der Serie lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer Service Fabric Mesh-App mit einem ASP.NET-Web-Front-End mithilfe von Visual Studio
> * Erstellen eines Modells für die Darstellung von Aufgaben
> * Erstellen eines Back-End-Diensts und Abrufen von Daten aus diesem Dienst
> * Hinzufügen eines Controllers und eines Datenkontexts als Teil des Model View Controller-Musters für den Back-End-Dienst
> * Erstellen einer Webseite zum Anzeigen von Aufgaben
> * Erstellen von Umgebungsvariablen, die den Back-End-Dienst identifizieren

In dieser Tutorialserie lernen Sie Folgendes:
> [!div class="checklist"]
> * Erstellen einer Service Fabric-App in Visual Studio
> * [Debuggen einer Service Fabric Mesh-App, die in Ihrem lokalen Entwicklungscluster ausgeführt wird](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Bereitstellen einer Service Fabric Mesh-App](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Aktualisieren einer Service Fabric Mesh-App](service-fabric-mesh-tutorial-upgrade.md)
> * [Bereinigen von Service Fabric Mesh-Ressourcen](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen können, müssen Sie Folgendes tun:

* Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

* Stellen Sie sicher, dass Sie [Ihre Entwicklungsumgebung eingerichtet haben](service-fabric-mesh-howto-setup-developer-environment-sdk.md). Dies umfasst auch das Installieren der Service Fabric-Laufzeit, des SDK und die Installation von Docker sowie Visual Studio 2017.

## <a name="create-a-service-fabric-mesh-project-in-visual-studio"></a>Erstellen eines Service Fabric-Projekts in Visual Studio

Klicken Sie in Visual Studio auf **Datei** > **Neu** > **Projekt...** .

Geben Sie im oberen Bereich des Dialogfelds **Neues Projekt** im Feld **Suche** die Zeichenfolge `mesh` ein. Wählen Sie die Vorlage **Service Fabric Mesh-Anwendung** aus. (Sollte die Vorlage nicht angezeigt werden, vergewissern Sie sich, dass Sie das Mesh SDK und die Vorschauversion der VS-Tools gemäß der Anleitung zum [Einrichten der Entwicklungsumgebung](service-fabric-mesh-howto-setup-developer-environment-sdk.md) installiert haben.)  

Geben Sie im Feld **Name** die Zeichenfolge `todolistapp` ein, und legen Sie im Feld **Speicherort** den Ordnerpfad fest, unter dem Sie die Dateien für das Projekt speichern möchten.

Vergewissern Sie sich, dass das Kontrollkästchen **Projektmappenverzeichnis erstellen** aktiviert ist, und klicken Sie auf **OK**, um das Service Fabric Mesh-Projekt zu erstellen.

![Screenshot: Erstellen eines Service Fabric Mesh-Projekts](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-project.png)

Als Nächstes wird das Dialogfeld **Neuer Service Fabric-Dienst** angezeigt.

### <a name="create-the-web-front-end-service"></a>Erstellen des Web-Front-End-Diensts

Wählen Sie im Dialogfeld **Neuer Service Fabric-Dienst** den Projekttyp **ASP.NET Core** aus, und vergewissern Sie sich, dass das **Containerbetriebssystem** auf **Windows** festgelegt ist.

Legen Sie den Dienstnamen im Feld **Dienstname** auf **WebFrontEnd** fest. Klicken Sie auf **OK**, um den ASP.NET Core-Dienst zu erstellen.

![Visual Studio-Dialogfeld für das neue Service Fabric Mesh-Projekt](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-service-fabric-service.png)

Als Nächstes wird das Dialogfeld „ASP.NET Core-Webanwendung“ angezeigt. Wählen Sie **Webanwendung** aus, und klicken Sie dann auf **OK**.

![Screenshot: Hervorhebung der Webapplicationvorlage](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-aspnetcore-app.png)

Sie verfügen nun über eine Service Fabric Mesh-Anwendung. Erstellen Sie als Nächstes das Modell für Aufgabeninformationen.

## <a name="create-the-to-do-items-model"></a>Erstellen des Aufgabenmodells

Der Einfachheit halber werden die Aufgaben in einer Liste im Arbeitsspeicher gespeichert. Erstellen Sie für die Aufgaben eine Klassenbibliothek und eine Liste. Vergewissern Sie sich in Visual Studio, dass die Projektmappe **todolistapp** geladen ist, und klicken Sie auf **Datei** > **Hinzufügen** > **Neues Projekt**.

Geben Sie im oberen Bereich des Dialogfelds **Neues Projekt hinzufügen** im Feld **Suche** die Zeichenfolge `C# .net core class` ein. Wählen Sie die Vorlage **Klassenbibliothek (.NET Core)** aus.

Geben Sie im Feld **Name** die Zeichenfolge `Model` ein. Klicken Sie auf **OK**, um die Klassenbibliothek zu erstellen.

Klicken Sie im Projektmappen-Explorer unter **Model** mit der rechten Maustaste auf **Class1.cs**, und klicken Sie anschließend auf **Umbenennen**. Ändern Sie den Namen der Klasse in **ToDoItem.cs**. Klicken Sie auf **Ja**, wenn Sie gefragt werden, ob alle Verweise umbenannt werden sollen.

Ersetzen Sie den Inhalt von `class ToDoItem` (leer) durch Folgendes:

```csharp
public class ToDoItem
{
    public string Description { get; set; }
    public int Index { get; set; }
    public bool Completed { get; set; }

    public ToDoItem(string description)
    {
        Description = description;
        Index = 0;
    }

    public static ToDoItem Load(string description, int index, bool completed)
    {
        ToDoItem newItem = new ToDoItem(description)
        {
            Index = index,
            Completed = completed
        };

        return newItem;
    }
}
```

Diese Klasse steht für To-Do-Elemente.

Klicken Sie in Visual Studio mit der rechten Maustaste auf die Klassenbibliothek **Model**, und klicken Sie anschließend auf **Hinzufügen** > **Klasse...** , um eine Liste für die Aufgaben zu erstellen. Das Dialogfeld **Neues Element hinzufügen** wird angezeigt. Legen Sie den Namen unter **Name** auf `ToDoList.cs` fest, und klicken Sie auf **Hinzufügen**.

Ersetzen Sie `class ToDoList` (leer) in **ToDoList.cs** durch Folgendes:

```csharp
public class ToDoList
{
    private List<ToDoItem> _items;

    public string Name { get; set; }
    public IEnumerable<ToDoItem> Items { get => _items; }

    public ToDoList(string name)
    {
        Name = name;
        _items = new List<ToDoItem>();
    }

    public ToDoItem Add(string description)
    {
        var item = new ToDoItem(description);
        _items.Add(item);
        item.Index = _items.IndexOf(item);
        return item;
    }
    public void Add(ToDoItem item)
    {
        _items.Add(item);
        item.Index = _items.Count - 1;
    }

    public ToDoItem RemoveAt(int index)
    {
        if (index >= 0 && index < _items.Count)
        {
            var result = _items[index];
            _items.RemoveAt(index);

            // Reorder items
            for (int i = index; i < _items.Count; i++)
            {
                _items[i].Index = i;
            }

            return result;
        }
        else
        {
            throw new IndexOutOfRangeException();
        }
    }
}
```

Erstellen Sie als Nächstes den Service Fabric-Dienst, der die Aufgaben nachverfolgt.

## <a name="create-the-back-end-service"></a>Erstellen des Back-End-Diensts

Klicken Sie in Visual Studio im Fenster **Projektmappen-Explorer** mit der rechten Maustaste auf **todolistapp**, und klicken Sie anschließend auf **Hinzufügen** > **Neuer Service Fabric-Dienst...** .

Das Dialogfeld **Neuer Service Fabric-Dienst** wird angezeigt. Wählen Sie den Projekttyp **ASP.NET Core** aus, und vergewissern Sie sich, dass das **Containerbetriebssystem** auf **Windows** festgelegt ist. Legen Sie den Dienstnamen im Feld **Dienstname** auf **ToDoService** fest. Klicken Sie auf **OK**, um den ASP.NET Core-Dienst zu erstellen.

Als Nächstes wird das Dialogfeld **Neue ASP.NET Core-Webanwendung** angezeigt. Klicken Sie in diesem Dialogfeld auf **API** und anschließend auf **OK**. Daraufhin wird der Projektmappe ein Projekt für den Dienst hinzugefügt.

![Visual Studio-Dialogfeld für neue ASP.NET Core-Anwendung](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-webapi.png)

Da für den Back-End-Dienst keinerlei Benutzeroberfläche zur Verfügung steht, deaktivieren Sie die Option, durch die beim Start des Diensts der Browser gestartet wird. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **ToDoService**, und klicken Sie auf **Eigenschaften**. Klicken Sie im daraufhin angezeigten Eigenschaftenfenster auf der linken Seite auf die Registerkarte **Debuggen**, und deaktivieren Sie das Kontrollkästchen **Browser starten**. Drücken Sie **STRG+S** , um die Änderung zu speichern.

Da dieser Dienst die Aufgabeninformationen verwaltet, fügen Sie einen Verweis auf die Klassenbibliothek „Model“ hinzu. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **ToDoService**, und klicken Sie anschließend auf **Hinzufügen** > **Verweis...** . Das Dialogfeld **Verweis-Manager** wird angezeigt.

Aktivieren Sie im **Verweis-Manager** das Kontrollkästchen für **Model**, und klicken Sie auf **OK**.

### <a name="add-a-data-context"></a>Hinzufügen eines Datenkontexts

Erstellen Sie als Nächstes einen Datenkontext, der die Bereitstellung der Daten aus dem Datenmodell koordiniert.

Klicken Sie zum Hinzufügen der Datenkontextklasse im Projektmappen-Explorer mit der rechten Maustaste auf **ToDoService**, und klicken Sie anschließend auf **Hinzufügen** > **Klasse**.
Vergewissern Sie sich im daraufhin angezeigten Dialogfeld **Neues Element hinzufügen**, dass **Klasse** ausgewählt ist, legen Sie den Namen im Feld **Name** auf `DataContext.cs` fest, und klicken Sie anschließend auf **Hinzufügen**.

Ersetzen Sie in **DataContext.cs** den Inhalt von `class DataContext` (leer) durch Folgendes:

```csharp
public static class DataContext
{
    public static Model.ToDoList ToDoList { get; } = new Model.ToDoList("Azure learning List");

    static DataContext()
    {
        // Seed to-do list
        ToDoList.Add(Model.ToDoItem.Load("Learn about microservices", 0, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric", 1, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric Mesh", 2, false));
    }
}
```

Dieser minimale Datenkontext füllt einige Beispielaufgaben auf und ermöglicht den Zugriff auf sie.

### <a name="add-a-controller"></a>Hinzufügen eines Controllers

Beim Erstellen des Projekts **ToDoService** wurde durch die Vorlage ein Standardcontroller bereitgestellt, der die HTTP-Anforderungen verarbeitet und die HTTP-Antwort erstellt. Öffnen Sie im **Projektmappen-Explorer** unter **ToDoService** den Ordner **Controller** mit der Datei **ValuesController.cs**. 

Klicken Sie mit der rechten Maustaste auf **ValuesController.cs**, und klicken Sie anschließend auf **Umbenennen**. Benennen Sie die Datei in `ToDoController.cs` um. Wenn Sie gefragt werden, ob alle Verweise umbenannt werden sollen, klicken Sie auf **Ja**.

Öffnen Sie die Datei **ToDoController.cs**, und ersetzen Sie den Inhalt von `class ToDoController` durch Folgendes:

```csharp
[Route("api/[controller]")]
public class ToDoController : Controller
{
    // GET api/todo
    [HttpGet]
    public IEnumerable<Model.ToDoItem> Get()
    {
        return DataContext.ToDoList.Items;
    }

    // GET api/todo/5
    [HttpGet("{index}")]
    public Model.ToDoItem Get(int index)
    {
        return DataContext.ToDoList.Items.ElementAt(index);
    }

    //// POST api/values
    //[HttpPost]
    //public void Post([FromBody]string value)
    //{
    //}

    //// PUT api/values/5
    //[HttpPut("{id}")]
    //public void Put(int id, [FromBody]string value)
    //{
    //}

    // DELETE api/values/5
    [HttpDelete("{index}")]
    public void Delete(int index)
    {
    }
}
```

Da in diesem Tutorial die Kommunikation mit einem anderen Dienst im Mittelpunkt steht, werden Aktionen wie Hinzufügen, Löschen usw. hier nicht implementiert.

## <a name="create-the-web-page-that-displays-to-do-items"></a>Erstellen der Webseite zum Anzeigen von Aufgaben

Nach der Implementierung des Back-End-Diensts können Sie als Nächstes die Website programmieren, auf der die bereitgestellten Aufgaben angezeigt werden. Die folgenden Schritte werden im Projekt **WebFrontEnd** ausgeführt.

Die Webseite, auf der die Aufgaben angezeigt werden, benötigt Zugriff auf die Klasse **ToDoItem** und auf die entsprechende Liste.
Fügen Sie im **Projektmappen-Explorer** einen Verweis auf das Projekt „Model“ hinzu. Klicken Sie hierzu mit der rechten Maustaste auf **WebFrontEnd**, und klicken Sie anschließend auf **Hinzufügen** > **Verweis...** . Das Dialogfeld **Verweis-Manager** wird angezeigt.

Klicken Sie im **Verweis-Manager** auf das Kontrollkästchen für **Model** und anschließend auf **OK**.

Navigieren Sie im **Projektmappen-Explorer** zu **WebFrontEnd** > **Seiten** > **Index.cshtml**, um die Seite „Index.cshtml“ zu öffnen. Öffnen Sie **Index.cshtml**.

Ersetzen Sie den Inhalt der gesamten Datei durch den folgenden HTML-Code, um eine einfache Tabelle für die Aufgabenanzeige zu definieren:

```HTML
@page
@model IndexModel
@{
    ViewData["Title"] = "Home page";
}

<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

Klicken Sie im **Projektmappen-Explorer** auf das Dropdownsymbol der Datei **Index.cshtml**, und öffnen Sie dann **Index.cshtml.cs**.

Fügen Sie am Anfang von **Index.cshtml.cs** Folgendes hinzu: `using System.Net.Http;`.

Ersetzen Sie den Inhalt von `public class IndexModel` durch Folgendes:

```csharp
public class IndexModel : PageModel
{
    public Model.ToDoItem[] Items = new Model.ToDoItem[] { };

    public void OnGet()
    {
        HttpClient client = new HttpClient();

        using (HttpResponseMessage response = client.GetAsync(backendUrl).GetAwaiter().GetResult())
        {
            if (response.StatusCode == System.Net.HttpStatusCode.OK)
            {
                Items = Newtonsoft.Json.JsonConvert.DeserializeObject<Model.ToDoItem[]>(response.Content.ReadAsStringAsync().Result);
            }
        }
    }

    private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
    private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
}
```

### <a name="create-environment-variables"></a>Erstellen von Umgebungsvariablen

Die URL für den Back-End-Dienst wird für die Kommunikation mit diesem Dienst benötigt. Im Rahmen dieses Tutorials liest der folgende Codeauszug (der weiter oben als Bestandteil des Indexmodells definiert ist) Umgebungsvariablen zur Erstellung der URL:

```csharp
private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
```

Die URL setzt sich aus dem Dienstnamen und dem Port zusammen. Diese Informationen befinden sich in der Datei „service.yaml“ des Projekts **ToDoService**.

> [!IMPORTANT]
> In den folgenden Schritten werden YAML-Dateien geändert.
> Die Variablen müssen in der Datei „service.yaml“ mithilfe von Leerzeichen (nicht mithilfe von Tabulatoren) eingerückt werden, da sie sonst nicht kompiliert werden kann. Visual Studio fügt bei der Erstellung der Umgebungsvariablen möglicherweise Tabulatoren ein. Ersetzen Sie alle Tabulatoren durch Leerzeichen. Die App wird gestartet, obwohl in der Debugausgabe von **Build** Fehler angezeigt werden. Dies erfolgt aber erst, nachdem Sie die Tabstopps in Leerzeichen konvertiert und den Buildvorgang erneut durchgeführt haben. Um sicherzustellen, dass die Datei „service.yaml“ keine Leerzeichen enthält, können Sie die Leerzeichen im Visual Studio-Editor über **Bearbeiten**  > **Erweitert**  > **Leerstelle anzeigen** einblenden.
> Beachten Sie, dass Dateien vom Typ „service.yaml“ mit dem englischen Gebietsschema verarbeitet werden. Wenn Sie ein Dezimaltrennzeichen benötigen, verwenden Sie anstelle eines Kommas beispielsweise einen Punkt.

Navigieren Sie im **Projektmappen-Explorer** zum Projekt **ToDoService**, und öffnen Sie **Service Resources** (Dienstressourcen) > **service.yaml**.

![Abbildung 1: Die Datei „service.yaml“ des Projekts „ToDoService“](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-port.png)

 Der Dienstname (`ToDoService`) befindet sich unter `services:` (siehe Ziffer 1 in der obigen Abbildung).

* Der Port (`80`) befindet sich unter `endpoints:` (siehe Ziffer 2 in der obigen Abbildung). Die Portnummer Ihres Projekts weicht hiervon vermutlich ab.

Im nächsten Schritt müssen wir im Projekt „WebFrontEnd“ Umgebungsvariablen für den Dienstnamen und die Portnummer definieren, damit der Back-End-Dienst aufgerufen werden kann.

Navigieren Sie im **Projektmappen-Explorer** zu **WebFrontEnd** > **Service Resources** (Dienstressourcen) > **service.yaml**, um die Variablen zu definieren, die die Adresse des Back-End-Diensts angeben.

Fügen Sie in der Datei „service.yaml“ unter `environmentVariables:` die folgenden Variablen hinzu. (Sie müssen zuerst das Zeichen `#` entfernen, um für `environmentVariables:` die Auskommentierung aufzuheben.) Da die Abstände wichtig sind, sollten Sie die von Ihnen hinzugefügten Variablen nach den anderen Variablen unter `environmentVariables:` ausrichten. Es ist sehr wichtig, dass der Wert für ApiHostPort mit dem Portwert für ToDoServiceListener übereinstimmt, der in der Datei „service.yaml“ von ToDoService enthalten war.

```yaml
- name: ApiHostPort
  value: 
- name: ToDoServiceName
  value: ToDoService
```

> [!Tip]
> Es gibt zwei Möglichkeiten, um den Wert für `ToDoServiceName` anzugeben: 
> - Nur den Dienstnamen. Er wird sowohl in einem Debugszenario unter Windows 10 als auch beim Bereitstellen des Diensts in Azure Service Fabric Mesh aufgelöst.
> - Vollqualifiziert als „servicename.appname“. Dies funktioniert nur beim Debuggen unter Windows 10.
> Es ist eine bewährte Methode, nur den Dienstnamen für die Dienstauflösung zu nutzen.

Die Datei **service.yaml** Ihres Projekts **WebFrontEnd** sollte in etwa wie folgt aussehen, wobei sich Ihr Wert für `ApiHostPort` jedoch wahrscheinlich unterscheidet:

![„service.yaml“ im Projekt „WebFrontEnd“](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-envvars.png)


Nun können Sie das Image der Service Fabric Mesh-Anwendung erstellen und zusammen mit dem Back-End-Webdienst in Ihrem lokalen Cluster bereitstellen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Teil des Tutorials haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer Service Fabric Mesh-App mit einem ASP.NET-Web-Front-End
> * Erstellen eines Modells für die Darstellung von Aufgaben
> * Erstellen eines Back-End-Diensts und Abrufen von Daten aus diesem Dienst
> * Hinzufügen eines Controllers und eines Datenkontexts als Teil des Model View Controller-Musters für den Back-End-Dienst
> * Erstellen einer Webseite zum Anzeigen von Aufgaben
> * Erstellen von Umgebungsvariablen, die den Back-End-Dienst identifizieren

Fahren Sie mit dem nächsten Tutorial fort:
> [!div class="nextstepaction"]
> [Debuggen einer Service Fabric Mesh-Anwendung, die in Ihrem lokalen Entwicklungscluster ausgeführt wird](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)