---
title: Hinzufügen von Vorschlägen und AutoVervollständigen in einem Suchfeld – Azure Search
description: Aktivieren Sie automatisch vervollständigte Abfrageaktionen in Azure Search, indem Sie Vorschlagsfunktionen erstellen und Anforderungen formulieren, die ein Suchfeld mit abgeschlossenen Begriffen oder Ausdrücken ausfüllen.
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: mcarter
ms.custom: seodec2018
ms.openlocfilehash: 9385ed5127b8cc1aaf84c887b652fd8970883ba6
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524025"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-search-application"></a>Hinzufügen von Vorschlägen oder AutoVervollständigen zur Azure Search-Anwendung

In diesem Artikel erfahren Sie, wie Sie mit [Vorschlägen](https://docs.microsoft.com/rest/api/searchservice/suggestions) und [AutoVervollständigen](https://docs.microsoft.com/rest/api/searchservice/autocomplete) ein leistungsfähiges Suchfeld erstellen können, das Vorschläge während der Eingabe unterstützt.

+ *Vorschläge* sind vorgeschlagene Ergebnisse, die während der Eingabe generiert werden, wobei jeder Vorschlag ein einzelnes Ergebnis aus dem Index darstellt, das Ihrer bisherigen Eingabe entspricht. 

+ *AutoVervollständigen* vervollständigt die von einem Benutzer aktuell eingegebenen Wörter bzw. Ausdrücke. Anstatt Ergebnisse zurückzugeben, vervollständigt sie eine Abfrage, die Sie dann ausführen können, um Ergebnisse zurückzugeben. Wie bei Vorschlägen basiert ein vervollständigtes Wort oder ein vervollständigter Ausdruck in einer Abfrage auf einer Übereinstimmung im Index. Der Dienst schlägt keine Abfragen vor, die keine Ergebnisse im Index zurückgeben.

Sie können den Beispielcode in **DotNetHowToAutocomplete** herunterladen und ausführen, um diese Features auszuwerten. Der Beispielcode ist auf einen vordefinierten Index ausgerichtet, der mit [NYCJobs-Demodaten](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) gefüllt ist. Der NYCJobs-Index enthält ein [Vorschlagsfunktionskonstrukt](index-add-suggesters.md), das eine Voraussetzung für die Verwendung von Vorschlägen oder AutoVervollständigen ist. Sie können den in einem Sandboxdienst gehosteten vorbereiteten Index verwenden oder einen [eigenen Index](#configure-app) mit einem Datenlader in der NYCJobs-Beispiellösung füllen. 

Das Beispiel **DotNetHowToAutocomplete** zeigt sowohl Vorschläge als auch AutoVervollständigen in C#- und JavaScript-Sprachversionen. C#-Entwickler können eine ASP.NET MVC-basierte Anwendung durchlaufen, die das [Azure Search .NET SDK](https://aka.ms/search-sdk) verwendet. Die Logik für die automatisch vervollständigten und vorgeschlagenen Abfrageaufrufe finden Sie in der Datei „HomeController.cs“. JavaScript-Entwickler finden eine entsprechende Abfragelogik in „IndexJavaScript.cshtml“, die direkte Aufrufe an die [Azure Search REST-API](https://docs.microsoft.com/rest/api/searchservice/) einschließt. 

Für beide Sprachversionen basiert die Front-End-Benutzeroberfläche auf den Bibliotheken [jQuery UI](https://jqueryui.com/autocomplete/) und [XDSoft](https://xdsoft.net/jqplugins/autocomplete/). Mit diesen Bibliotheken wird das Suchfeld erstellt, das sowohl Vorschläge als auch AutoVervollständigen unterstützt. Die im Suchfeld gesammelten Eingaben werden mit Vorschlägen und AutoVervollständigen-Aktionen verknüpft, wie sie z. B. in „HomeController.cs“ oder „IndexJavaScript.cshtml“ definiert sind.

In dieser Übung werden die folgenden Aufgaben behandelt:

> [!div class="checklist"]
> * Implementieren eines Eingabefelds für die Suche in JavaScript und Senden von Anforderungen für vorgeschlagene Übereinstimmungen oder automatisch vervollständigte Begriffe
> * Definieren von Vorschlägen und AutoVervollständigen-Aktionen in C# in „HomeController.cs“
> * Direktes Aufrufen der REST-APIs in JavaScript, um dieselbe Funktionalität bereitzustellen

## <a name="prerequisites"></a>Voraussetzungen

Für diese Übung ist ein Azure Search-Dienst optional, da die Lösung einen Live-Sandboxdienst verwendet, der einen vorbereiteten NYCJobs-Demoindex bereitstellt. Wenn Sie dieses Beispiel mit einem eigenen Suchdienst ausführen möchten, finden Sie Anweisungen unter [Konfigurieren des NYCJobs-Indexes](#configure-app).

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) (beliebige Edition). Der Beispielcode und die Anleitung wurden in der kostenlosen Community-Edition getestet.

* Laden Sie das [DotNetHowToAutoComplete-Beispiel](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) herunter.

Das Beispiel ist ausführlich und umfasst Vorschläge, AutoVervollständigen, Facettennavigation und clientseitigen Cachedienst. Sie sollten die Infodatei und Kommentare lesen, um eine vollständige Beschreibung dessen zu erhalten, was das Beispiel bietet.

## <a name="run-the-sample"></a>Ausführen des Beispiels

1. Öffnen Sie **AutocompleteTutorial.sln** in Visual Studio. Die Lösung enthält ein ASP.NET MVC-Projekt mit einer Verbindung mit dem NYCJobs-Demoindex.

2. Drücken Sie die Taste F5, um das Projekt auszuführen, und laden Sie die Seite in einen Browser Ihrer Wahl.

Im oberen Bereich wird Ihnen eine Option zur Auswahl von C# oder JavaScript angezeigt. Die Option C# wird über den Browser im HomeController aufgerufen und verwendet das .NET SDK von Azure Search zum Abrufen von Ergebnissen. 

Die Option JavaScript ruft die REST-API für Azure Search direkt über den Browser auf. Diese Option erzielt eine deutlich bessere Leistung, da der Controller aus dem Flow herausgenommen wird. Sie können die Option auswählen, die Ihren Anforderungen und Sprachpräferenzen entspricht. Auf der Seite gibt es mehrere Beispiele zur automatischen Vervollständigung mit einer entsprechenden Anleitung. Jedes Beispiel enthält empfohlenen Beispieltext, den Sie ausprobieren können.  

Versuchen Sie, in jedes Suchfeld einige Buchstaben einzugeben, um zu sehen, was geschieht.

## <a name="search-box"></a>Suchfeld

Sowohl für die C#- als auch für die JavaScript-Version ist die Implementierung der Suchfelder identisch. 

Öffnen Sie die Datei **Index.cshtml** unter dem Ordner „\Views\Home“, um den Code anzuzeigen:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Dies ist ein einfaches Feld für die Texteingabe mit einer Klasse für das Format, einer von JavaScript referenzierten ID und Platzhaltertext.  Das Besondere ist im eingebetteten JavaScript zu finden.

Im C#-Sprachbeispiel wird in der Datei „Index.cshtml“ JavaScript verwendet, um die [Bibliothek jQuery UI AutoVervollständigen](https://jqueryui.com/autocomplete/) nutzen zu können. Diese Bibliothek fügt die Funktion der automatischen Vervollständigung zum Suchfeld hinzu, indem im MVC-Controller asynchrone Aufrufe zum Abrufen von Vorschlägen durchgeführt werden. Die JavaScript-Sprachversion befindet sich in „IndexJavaScript.cshtml“. Sie enthält das folgende Skript für die Suchleiste sowie REST API-Aufrufe für Azure Search.

Betrachten wir den JavaScript-Code für das erste Beispiel, das die Funktion „jQuery UI AutoVervollständigen“ aufruft und eine Anforderung für Vorschläge übergibt:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Der obige Code wird im Browser beim Laden der Seite ausgeführt, um die jQuery UI zur automatische Vervollständigung für das Eingabefeld „example1a“ zu konfigurieren.  `minLength: 3` stellt sicher, dass Empfehlungen nur angezeigt werden, wenn mindestens drei Zeichen in das Suchfeld eingegeben wurden.  Der Quellwert ist wichtig:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

Die obige Zeile teilt der Funktion „jQuery UI AutoVervollständigen“ mit, wo die Liste der Elemente abgerufen werden kann, die unter dem Suchfeld angezeigt werden sollen. Da es sich um ein MVC-Projekt handelt, ruft es die Vorschlagsfunktion in „HomeController.cs“ auf, die die Logik für die Rückgabe der Abfragevorschläge enthält (der nächste Abschnitt enthält weitere Informationen zur Vorschlagsfunktion). Diese Funktion übergibt auch einige Parameter zur Steuerung von Hervorhebungen, für die Fuzzyübereinstimmung und für Benennungen. Die JavaScript-API zur automatischen Vervollständigung fügt den Parameter für Begriffe hinzu.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Erweitern des Beispiels zur Unterstützung von Fuzzyübereinstimmungen

Mit der Fuzzysuche können Sie Ergebnisse basierend auf nahen Übereinstimmungen abrufen, auch wenn der Benutzer ein Wort im Suchfeld falsch schreibt. Obwohl nicht erforderlich, verbessert es die Stabilität eines Typeahead-Erlebnisses erheblich. Lassen Sie uns dies ausprobieren, indem wir die Quellzeile ändern, um die Fuzzyübereinstimmung zu aktivieren.

Ändern Sie die folgende Zeile:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

Und zwar in diesen Code:

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

Starten Sie die Anwendung durch Drücken der Taste F5.

Geben Sie ein Wort wie z.B. „execative“ ein, und beachten Sie, wie Ergebnisse für „executive“ zurückgegeben werden, auch wenn diese nicht genau mit den von Ihnen eingegebenen Buchstaben übereinstimmen.

### <a name="jquery-autocomplete--backed-by-azure-search-autocomplete"></a>Unterstützung von jQuery-AutoVervollständigen durch Azure Search-AutoVervollständigen

Bisher wurde der UX-Code der Suche auf die Vorschläge ausgerichtet. Der nächste Codeblock zeigt die Funktion jQuery UI AutoVervollständigen (Zeile 91 in „index.cshtml“), die eine Anforderung für Azure Search-AutoVervollständigen übergibt:

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#example2").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#example2").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#example2").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#example2").val("");
        }
    });
});
```

## <a name="c-example"></a>C#-Beispiel

Nachdem wird den JavaScript-Code für die Webseite überprüft haben, schauen wir uns nun den serverseitigen C#-Controllercode an, der die Vorschläge tatsächlich über das .NET SDK von Azure Search abruft.

Öffnen Sie die Datei **HomeController.cs** im Verzeichnis „Controllers“. 

Zunächst bemerken Sie möglicherweise im oberen Bereich der Klasse eine Methode mit dem Namen `InitSearch`. Mit dieser Methode wird für den Azure Search-Dienst ein authentifizierter Client für den HTTP-Index erstellt. Weitere Informationen finden Sie unter [Verwenden von Azure Search aus einer .NET-Anwendung](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

Beachten Sie in Zeile 41 die Vorschlagsfunktion. Sie basiert auf der [DocumentsOperationsExtensions.Suggest-Methode](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

Die Funktion „Suggest“ (Vorschlagen) verwendet zwei Parameter, die bestimmen, ob Treffermarkierungen zurückgegeben werden sollen oder ob zusätzlich zur Eingabe des Suchbegriffs die Fuzzyübereinstimmung verwendet werden soll. Die Methode erstellt das Objekt [SuggestParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), das anschließend an die Suggest-API übergeben wird. Das Ergebnis wird anschließend in JSON konvertiert, damit es im Client angezeigt werden kann.

Beachten Sie in Zeile 69 die Funktion „AutoVervollständigen“. Sie basiert auf der [DocumentsOperationsExtensions.Autocomplete-Methode](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet).

```csharp
public ActionResult AutoComplete(string term)
{
    InitSearch();
    //Call autocomplete API and return results
    AutocompleteParameters ap = new AutocompleteParameters()
    {
        AutocompleteMode = AutocompleteMode.OneTermWithContext,
        UseFuzzyMatching = false,
        Top = 5
    };
    AutocompleteResult autocompleteResult = _indexClient.Documents.Autocomplete(term, "sg", ap);

    // Conver the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

Die Funktion „AutoVervollständigen“ übernimmt die Eingabe des Suchbegriffs. Die Methode erstellt ein [AutoCompleteParameters-Objekt](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Das Ergebnis wird anschließend in JSON konvertiert, damit es im Client angezeigt werden kann.

(Optional) Fügen Sie am Anfang der Funktion „Suggest“ (Vorschlagen) einen Breakpoint hinzu und durchlaufen Sie den Code schrittweise. Beachten Sie die vom SDK zurückgegebene Antwort und wie diese in das von der Methode zurückgegebene Ergebnis konvertiert wird.

Die anderen Beispiele auf der Seite folgen beim Hinzufügen von Treffermarkierungen und bei Facets zur Unterstützung des clientseitigen Zwischenspeicherns der Ergebnisse der automatischen Vervollständigung dem gleichen Muster. Überprüfen Sie alle Beispiele, um zu verstehen, wie sie funktionieren und wie Sie diese in Ihrer Suchumgebung nutzen können.

## <a name="javascript-example"></a>JavaScript-Beispiel

Eine JavaScript-Implementierung von AutoVervollständigen und Vorschlägen ruft die REST-API auf und verwendet dabei einen URI als Quelle, um den Index und den Vorgang anzugeben. 

Öffnen Sie zur Überprüfung der JavaScript-Implementierung **IndexJavaScript.cshtml**. Beachten Sie, dass die Funktion jQuery UI AutoVervollständigen auch für das Suchfeld verwendet wird, um Suchbegriffseingaben zu sammeln und asynchrone Anrufe an Azure Search zu starten, um vorgeschlagene Übereinstimmungen oder vervollständigte Begriffe abzurufen. 

Schauen Sie sich den JavaScript-Code im ersten Beispiel an:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: function (request, response) {
        $.ajax({
            type: "POST",
            url: suggestUri,
            dataType: "json",
            headers: {
                "api-key": searchServiceApiKey,
                "Content-Type": "application/json"
            },
            data: JSON.stringify({
                top: 5,
                fuzzy: false,
                suggesterName: "sg",
                search: request.term
            }),
                success: function (data) {
                    if (data.value && data.value.length > 0) {
                        response(data.value.map(x => x["@@search.text"]));
                    }
                }
            });
        },
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Wenn Sie diesen mit dem Beispiel oben vergleichen, in dem der Home-Controller aufgerufen wird, werden Sie einige Ähnlichkeiten feststellen.  Die Konfiguration für die automatische Vervollständigung bei `minLength` und `position` ist identisch. 

Die wesentliche Änderung ist in der Quelle zu finden. Statt die Suggest-Methode im Home-Controller aufzurufen, wird in einer JavaScript-Funktion eine REST-Anforderung erstellt und mit AJAX ausgeführt. Anschließend wird die Antwort als „Erfolgreich“ verarbeitet und als Quelle verwendet.

REST-Aufrufe verwenden URIs zur Angabe, ob ein API-Aufruf für [AutoVervollständigen](https://docs.microsoft.com/rest/api/searchservice/autocomplete) oder [Vorschläge](https://docs.microsoft.com/rest/api/searchservice/suggestions) ausgeführt wird. Die folgenden URIs befinden sich in den Zeilen 9 und 10.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

In Zeile 148 befindet sich ein Skript, das den `autocompleteUri` aufruft. Der erste Anruf von `suggestUri` befindet sich in Zeile 39.

> [!Note]
> Das Ausführen von REST-Aufrufen an den Dienst in JavaScript wird hier als praktische Veranschaulichung der REST-API bereitgestellt, sollte aber nicht als bewährte Methode oder Empfehlung verstanden werden. Die Einbeziehung eines API-Schlüssels und Endpunkts in ein Skript öffnet Ihren Dienst für Denial-of-Service-Angriffe für jeden, der diese Werte aus dem Skript lesen kann. Während es sicher ist, JavaScript für Lernzwecke zu verwenden, z. B. für Indizes, die für den kostenfreien Dienst gehostet werden, empfehlen wir die Verwendung von Java oder C# für Indizierungs- und Abfrageoperationen im Produktionscode. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>Konfigurieren von NYCJobs zum Ausführen für Ihren Dienst

Bisher haben Sie den gehosteten NYCJobs-Demoindex verwendet. Wenn Sie die vollständige Transparenz des gesamten Codes wünschen, einschließlich des Index, befolgen Sie diese Anweisungen, um den Index in Ihrem eigenen Suchdienst zu erstellen und zu laden.

1. [Erstellen Sie einen Azure Search-Dienst](search-create-service-portal.md), oder suchen Sie in Ihrem aktuellen Abonnement [nach einem vorhandenen Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). In diesem Beispiel können Sie einen kostenlosen Dienst verwenden. 

   > [!Note]
   > Bei Verwendung des kostenlosen Azure Search-Diensts gilt eine Obergrenze von drei Indizes. Der Datenlader NYCJobs erstellt zwei Indizes. Vergewissern Sie sich, dass Ihr Dienst über genügend freie Kapazität für die neuen Indizes verfügt.

1. Laden Sie den [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)-Beispielcode herunter.

1. Öffnen Sie in Visual Studio im Ordner „DataLoader“ des NYCJobs-Beispielcodes die Datei **DataLoader.sln**.

1. Fügen Sie die Verbindungsinformationen für Ihren Azure Search-Dienst hinzu. Öffnen Sie die Datei „App.config“ im DataLoader-Projekt, und ändern Sie die App-Einstellungen für „TargetSearchServiceName“ und „TargetSearchServiceApiKey“, um Ihren Azure Search-Dienst und Ihren API-Schlüssel für den Azure Search-Dienst widerzuspiegeln. Diese können im Azure-Portal gefunden werden.

1. Drücken Sie F5, um die Anwendung zu starten, erstellen Sie zwei Indizes und importieren Sie die NYCJob-Beispieldaten.

1. Öffnen Sie **AutocompleteTutorial.sln**, und bearbeiten Sie die Web.config im Projekt **AutocompleteTutorial**. Ändern Sie die Werte „SearchServiceName“ und „SearchServiceApiKey“ in Werte, die für Ihren Suchdienst gültig sind.

1. Drücken Sie die Taste F5, um die Anwendung auszuführen. Die Beispiel-Web-App wird im Standardbrowser geöffnet. Die Benutzeroberfläche ist identisch mit der Sandboxversion, nur der Index und die Daten werden auf Ihrem Dienst gehostet.

## <a name="next-steps"></a>Nächste Schritte

Dieses Beispiel veranschaulicht die grundlegenden Schritte zum Erstellen eines Suchfelds, das die automatische Vervollständigung und Vorschläge unterstützt. Sie haben gesehen, wie Sie eine ASP.NET MVC-Anwendung erstellen und mit dem .NET SDK oder der REST-API für Azure Search Vorschläge abrufen können.

Im nächsten Schritt versuchen Sie, Vorschläge und AutoVervollständigen in Ihre Suchumgebung zu integrieren. Die folgenden Referenzartikel sollten dabei helfen.

> [!div class="nextstepaction"]
> [REST-API zur automatischen Vervollständigung](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [REST-API für Vorschlage](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Facets-Indexattribut in einer REST-API zur Indexerstellung](https://docs.microsoft.com/rest/api/searchservice/create-index)

