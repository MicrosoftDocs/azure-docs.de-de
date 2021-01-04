---
title: Hinzufügen von AutoVervollständigen zu einem Suchfeld
titleSuffix: Azure Cognitive Search
description: Aktivieren Sie Abfrageaktionen mit Suche während der Eingabe in Azure Cognitive Search, indem Sie Vorschlagsfunktionen erstellen und Anforderungen formulieren, die in einem Suchfeld automatisch mit vollständigen Begriffen oder Ausdrücken vervollständigt werden. Sie können auch Vorschläge für Übereinstimmungen zurückgeben.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 25c87971455ed3c5f59c92748794720d61e599e3
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96339607"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps-using-azure-cognitive-search"></a>Hinzufügen von AutoVervollständigen und Vorschlägen zu Client-Apps mithilfe von Azure Cognitive Search

„Suche während der Eingabe“ ist ein gängiges Verfahren für mehr Produktivität bei von Benutzern initiierten Abfragen. In Azure Cognitive Search wird dieses Vorgehen durch die Funktion *AutoVervollständigen* unterstützt, die einen Begriff oder einen Ausdruck auf der Grundlage von partiellen Eingaben vervollständigt (aus „Micro“ wird z. B. „Microsoft“). Eine weitere Benutzerfunktion sind *Vorschläge*, d. h. eine Auswahlliste übereinstimmender Dokumente (Buchtitel werden mit einer ID zurückgegeben, sodass Sie einen Link zu einer Detailseite für ein Buch erstellen können). Sowohl AutoVervollständigen als auch Vorschläge basieren auf einer Entsprechung im Index. Der Dienst bietet keine Abfragen an, die keine Ergebnisse zurückgeben.

Zum Implementieren dieser Verfahren in Azure Cognitive Search benötigen Sie Folgendes:

+ Die Definition einer *Vorschlagsfunktion*, die im Indexschema eingebettet ist.
+ Eine *Abfrage* mit Angabe der [AutoVervollständigen](/rest/api/searchservice/autocomplete)- oder [Vorschlags](/rest/api/searchservice/suggestions)-API für die Anforderung.
+ Ein *UI-Steuerelement*, um Interaktionen bei der Suche während der Eingabe in Ihrer Client-App zu verarbeiten. Wir empfehlen für diesen Zweck eine vorhandene JavaScript-Bibliothek.

In Azure Cognitive Search werden automatisch vervollständigte Abfragen und vorgeschlagene Ergebnisse aus dem Suchindex abgerufen, und zwar aus ausgewählten Feldern, die Sie bei einer Vorschlagsfunktion registriert haben. Eine Vorschlagsfunktion ist Bestandteil des Index und gibt an, welche Felder Inhalte zum Vervollständigen einer Abfrage und/oder zum Vorschlagen eines Ergebnisses bereitstellen. Wenn der Index erstellt und geladen wird, wird intern eine Vorschlagsdatenstruktur zum Speichern von Präfixen erstellt, die für die Übereinstimmung bei partiellen Abfragen verwendet werden. Bei Vorschlägen ist es für die Sucherfahrung äußerst wichtig, geeignete Felder auszuwählen, die einmalig sind oder sich zumindest nicht wiederholen. Weitere Informationen finden Sie unter [Erstellen einer Vorschlagsfunktion](index-add-suggesters.md).

Im weiteren Verlauf dieses Artikels geht es um Abfragen und Clientcode. Zum Veranschaulichen der wichtigsten Punkte werden JavaScript und C# verwendet. Anhand von REST-API-Beispielen wird jeder Vorgang knapp und präzise dargestellt. Links zu End-to-End-Codebeispielen finden Sie unter [Weitere Schritte](#next-steps).

## <a name="set-up-a-request"></a>Einrichten einer Anforderung

Die Elemente einer Anforderung beinhalten eine der APIs für die Suche während der Eingabe, eine partielle Abfrage und eine Vorschlagsfunktion. Im folgenden Skript werden die Komponenten einer Anforderung veranschaulicht, wobei die AutoVervollständigen-REST-API als Beispiel verwendet wird.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

**suggesterName** gibt die Felder der Vorschlagsfunktion aus, die zum Vervollständigen von Begriffen oder Vorschlägen verwendet werden. Speziell bei Vorschlägen sollte die Feldliste aus solchen Feldern bestehen, die bei übereinstimmenden Ergebnissen klare Auswahlmöglichkeiten bieten. Auf einer Website zum Vertrieb von Computerspielen ist das Feld möglicherweise der Titel des Spiels.

Der Parameter **search** stellt die partielle Abfrage bereit, bei der Zeichen über das „jQuery Autocomplete“-Steuerelement an die Abfrageanforderung übergeben werden. Im obigen Beispiel ist „minecraf“ eine statische Abbildung dessen, was das Steuerelement möglicherweise übergeben hat.

Die APIs erzwingen keine Mindestanforderungen für die Länge der partiellen Abfrage. Sie kann auch aus nur einem Zeichen bestehen. „jQuery Autocomplete“ stellt jedoch eine Mindestlänge bereit. In der Regel sind es mindestens zwei oder drei Zeichen.

Übereinstimmungen finden sich am Anfang eines Begriffs an beliebiger Stelle in der Eingabezeichenfolge. Bei der Suche nach „the quick brown fox“ zum Beispiel findet sowohl die Funktion „AutoVervollständigen“, als auch die Vorschlagsfunktion die partiellen Versionen von „the“, „quick“, „brown“ oder „fox“, aber keine partiellen Infixe wie „rown“ oder „ox“. Außerdem legt jede Übereinstimmung den Bereich für nachfolgende Erweiterungen fest. Die partielle Abfrage von „quick br“ findet „quick brown“ oder „quick bread“. Sie findet aber weder „brown“ noch „bread“ alleine, solange nicht „quick“ davorsteht.

### <a name="apis-for-search-as-you-type"></a>APIs für die Suche während der Eingabe

Folgen Sie diesen Links zu den REST- und .NET SDK-Referenzseiten:

+ [Vorschläge-REST-API](/rest/api/searchservice/suggestions) 
+ [AutoVervollständigen-REST-API](/rest/api/searchservice/autocomplete) 
+ [SuggestAsync-Methode](/dotnet/api/azure.search.documents.searchclient.suggestasync)
+ [AutocompleteAsync-Methode](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)

## <a name="structure-a-response"></a>Struktur einer Antwort

Antworten auf AutoVervollständigen und Vorschläge sind das, was Sie wahrscheinlich von dem Muster erwarten: [AutoVervollständigen](/rest/api/searchservice/autocomplete#response) gibt eine Liste von Begriffen zurück. [Vorschläge](/rest/api/searchservice/suggestions#response) geben Begriffe und eine Dokument-ID zurück, damit Sie das Dokument abrufen können (mit der [Lookup Document](/rest/api/searchservice/lookup-document)-API kann das spezielle Dokument für eine Detailseite abgerufen werden).

Antworten werden durch die Parameter in der Abfrage gebildet. Legen Sie bei AutoVervollständigen unter [**autocompleteMode**](/rest/api/searchservice/autocomplete#autocomplete-modes) fest, ob die Textvervollständigung für einen Begriff oder zwei Begriffe erfolgen soll. Bei Vorschlägen bestimmt das von Ihnen ausgewählte Feld den Inhalt der Antwort.

Bei Vorschlägen sollten Sie die Antwort verfeinern, um Duplikate oder scheinbar zusammenhanglose Ergebnisse zu vermeiden. Beziehen Sie zum Steuern der Ergebnisse mehr Parameter in die Abfrage ein. Die folgenden Parameter gelten sowohl für AutoVervollständigen als auch für Vorschläge, sind möglicherweise aber für Vorschläge eher erforderlich, besonders wenn eine Vorschlagsfunktion mehrere Felder enthält.

| Parameter | Verwendung |
|-----------|-------|
| **$select** | Wenn eine Vorschlagsfunktion mehrere **Quellfelder** enthält, wählen Sie mit **$select** aus, welche Felder Werte beitragen (`$select=GameTitle`). |
| **searchFields** | Beschränken Sie die Abfrage auf spezielle Felder. |
| **$filter** | Wenden Sie Übereinstimmungskriterien auf den Ergebnissatz an (`$filter=Category eq 'ActionAdventure'`). |
| **$top** | Beschränken Sie die Ergebnisse auf eine bestimmte Zahl (`$top=5`).|

## <a name="add-user-interaction-code"></a>Hinzufügen von Benutzerinteraktionscode

Das automatische Ausfüllen eines Abfragebegriffs oder die Dropdownauswahl in einer Liste übereinstimmender Links setzt einen Benutzerinteraktionscode (in der Regel JavaScript) voraus, der Anforderungen von externen Quellen (z. B. AutoVervollständigen- oder Vorschlagsabfragen bei einem Azure Search Cognitive-Index) verarbeiten kann.

Auch wenn Sie diesen Code nativ schreiben könnten, ist es viel einfacher, Funktionen aus einer vorhandenen JavaScript-Bibliothek zu verwenden. In diesem Artikel werden zwei Funktionen, eine für Vorschläge, die andere für AutoVervollständigen, veranschaulicht. 

+ Das [AutoVervollständigen-Widget (jQuery UI)](https://jqueryui.com/autocomplete/) wird im Vorschlagsbeispiel verwendet. Sie können ein Suchfeld erstellen und dann in einer JavaScript-Funktion, die das AutoVervollständigen-Widget verwendet, darauf verweisen. Mit den Eigenschaften des Widgets werden die Quelle (eine AutoVervollständigen- oder Vorschlagsfunktion), die Mindestlänge der eingegebenen Zeichen, bevor eine Aktion erfolgt, und die Positionierung festgelegt.

+ Im AutoVervollständigen-Beispiel wird das [XDSoft-Plug-In „Autocomplete“](https://xdsoft.net/jqplugins/autocomplete/) verwendet.

Mit diesen Bibliotheken wird das Suchfeld erstellt, das sowohl Vorschläge als auch AutoVervollständigen unterstützt. Im Suchfeld erfasste Eingaben werden mit Vorschlags- und AutoVervollständigen-Aktionen gekoppelt.

## <a name="suggestions"></a>Vorschläge

In diesem Abschnitt wird die Implementierung der vorgeschlagenen Ergebnisse Schritt für Schritt beschrieben, beginnend mit der Definition des Suchfelds. Es wird auch gezeigt, wie ein Skript die erste JavaScript-Bibliothek zum AutoVervollständigen aufruft, auf die in diesem Artikel verwiesen wird.

### <a name="create-a-search-box"></a>Erstellen eines Suchfelds

Unter der Annahme, dass die [jQuery UI Autocomplete-Bibliothek](https://jqueryui.com/autocomplete/) und ein MVC-Projekt in C# verwendet werden, könnten Sie das Suchfeld mithilfe von JavaScript in der Datei **Index.cshtml** definieren. Die Bibliothek fügt dem Suchfeld durch asynchrone Aufrufe an den MVC-Controller zum Abrufen von Vorschlägen die Interaktion „Suche während der Eingabe“ hinzu.

In der Datei **Index.cshtml** im Ordner „\Views\Home“ könnte eine Zeile zum Erstellen eines Suchfelds wie folgt aussehen:

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

Dieses Beispiel umfasst ein einfaches Feld für die Texteingabe mit einer Klasse für das Format, einer von JavaScript referenzierten ID und Platzhaltertext.  

Betten Sie in derselben Datei JavaScript-Code ein, der auf das Suchfeld verweist. Die folgende Funktion ruft die Vorschlags-API auf, die anhand der partiellen Begriffseingabe vorgeschlagene übereinstimmende Dokumente anfordert:

```javascript
$(function () {
    $("#searchbox1").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

`source` teilt der Funktion „jQuery UI Autocomplete“ mit, wo die Liste der unter dem Suchfeld anzuzeigenden Elemente abgerufen werden soll. Da es sich um ein MVC-Projekt handelt, ruft es die **Vorschlags** funktion in der Datei **HomeController.cs** auf, die die Logik für die Rückgabe der Abfragevorschläge enthält. Diese Funktion übergibt auch einige Parameter zur Steuerung von Hervorhebungen, für die Fuzzyübereinstimmung und für Benennungen. Die JavaScript-API zur automatischen Vervollständigung fügt den Parameter für Begriffe hinzu.

`minLength: 3` stellt sicher, dass Empfehlungen nur angezeigt werden, wenn mindestens drei Zeichen in das Suchfeld eingegeben wurden.

### <a name="enable-fuzzy-matching"></a>Aktivieren der Fuzzyübereinstimmung

Mit der Fuzzysuche können Sie Ergebnisse basierend auf nahen Übereinstimmungen abrufen, auch wenn der Benutzer ein Wort im Suchfeld falsch schreibt. Die Edit-Distanz beträgt 1. Das heißt, dass es zwischen der Eingabe des Benutzers und einer Übereinstimmung eine maximale Abweichung von einem Zeichen geben darf. 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>Aktivieren der Hervorhebung

Durch die Hervorhebung wird auf die Zeichen im Ergebnis, die der Eingabe entsprechen, ein Schriftschnitt angewendet. Wenn beispielsweise die partielle Eingabe „micro“ lautet", würde das Ergebnis als „**micro** soft“, „**micro** scope“ usw. angezeigt. Die Hervorhebung basiert auf den HighlightPreTag- und HighlightPostTag-Parametern, die zusammen mit der Vorschlagsfunktion definiert werden.

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>Vorschlagsfunktion

Wenn Sie C# und eine MVC-Anwendung verwenden, können Sie in der Datei **HomeController.cs** im Verzeichnis des Controllers eine Klasse für vorgeschlagene Ergebnisse erstellen. In .NET basiert eine Vorschlagsfunktion auf der [SuggestAsync-Methode](/dotnet/api/azure.search.documents.searchclient.suggestasync). Weitere Informationen zum .NET SDK finden Sie unter [Verwenden von Azure Cognitive Search aus einer .NET-Anwendung](search-howto-dotnet-sdk.md).

Mit der `InitSearch`-Methode wird für den Azure Cognitive Search-Dienst ein authentifizierter Client für den HTTP-Index erstellt. Eigenschaften in der Klasse [SuggestOptions](/dotnet/api/azure.search.documents.suggestoptions) bestimmen, welche Felder durchsucht und in den Ergebnissen zurückgegeben werden. Die Eigenschaften legen auch fest, wie viele Übereinstimmungen gesucht werden und ob die Fuzzyübereinstimmung verwendet wird. 

Für AutoVervollständigen ist die Fuzzyübereinstimmung auf einen Bearbeitungsabstand (ein ausgelassenes oder falsch platziertes Zeichen) beschränkt. Beachten Sie, dass die Fuzzyübereinstimmung bei AutoVervollständigen-Abfragen manchmal unerwartete Ergebnisse liefern kann, abhängig von der Größe des Indexers und der Art und Weise, wie er partitioniert wird. Weitere Informationen finden Sie unter [Partitions- und Shardkonzepte](search-capacity-planning.md#concepts-search-units-replicas-partitions-shards).

```csharp
public async Task<ActionResult> SuggestAsync(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    var options = new SuggestOptions()
    {
        UseFuzzyMatching = fuzzy,
        Size = 8,
    };

    if (highlights)
    {
        options.HighlightPreTag = "<b>";
        options.HighlightPostTag = "</b>";
    }

    // Only one suggester can be specified per index.
    // The suggester for the Hotels index enables autocomplete/suggestions on the HotelName field only.
    // During indexing, HotelNames are indexed in patterns that support autocomplete and suggested results.
    var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", options).ConfigureAwait(false);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = suggestResult.Value.Results.Select(x => x.Text).ToList();

    // Return the list of suggestions.
    return new JsonResult(suggestions);
}
```

Die SuggestAsync-Funktion akzeptiert zwei Parameter, die bestimmen, ob Treffermarkierungen zurückgegeben werden sollen oder ob zusätzlich zum eingegebenen Suchbegriff die Fuzzyübereinstimmung verwendet werden soll. Die vorgeschlagenen Ergebnisse können bis zu acht Übereinstimmungen enthalten. Die Methode erstellt ein [SuggestOptions-Objekt](/dotnet/api/azure.search.documents.suggestoptions), das an die Vorschlags-API übergeben wird. Das Ergebnis wird anschließend in JSON konvertiert, damit es im Client angezeigt werden kann.

## <a name="autocomplete"></a>AutoVervollständigen

Bisher war der UX-Suchcode auf Vorschläge ausgerichtet. Der nächste Codeblock zeigt das AutoVervollständigen mit der XDSoft-Funktion „jQuery UI Autocomplete“, die eine Anforderung zum AutoVervollständigen für Azure Cognitive Search übergibt. Wie bei den Vorschlägen in einer C#-Anwendung befindet sich der Code, der die Interaktion von Benutzern unterstützt, in der Datei **index.cshtml**.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.8 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#searchbox1").autocompleteInline({
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
    $("#searchbox1").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#searchbox1").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#searchbox1").val("");
        }
    });
});
```

### <a name="autocomplete-function"></a>Funktion „AutoVervollständigen“

AutoVervollständigen basiert auf der [AutocompleteAsync-Methode](/dotnet/api/azure.search.documents.searchclient.autocompleteasync). Wie bei den Vorschlägen gehört dieser Codeblock in die Datei **HomeController.cs**.

```csharp
public async Task<ActionResult> AutoCompleteAsync(string term)
{
    InitSearch();

    // Setup the autocomplete parameters.
    var ap = new AutocompleteOptions()
    {
        Mode = AutocompleteMode.OneTermWithContext,
        Size = 6
    };
    var autocompleteResult = await _searchClient.AutocompleteAsync(term, "sg", ap).ConfigureAwait(false);

    // Convert the autocompleteResult results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Value.Results.Select(x => x.Text).ToList();

    return new JsonResult(autocomplete);
}
```

Die Funktion „AutoVervollständigen“ übernimmt die Eingabe des Suchbegriffs. Die Methode erstellt ein [AutoCompleteParameters-Objekt](/rest/api/searchservice/autocomplete). Das Ergebnis wird anschließend in JSON konvertiert, damit es im Client angezeigt werden kann.

## <a name="next-steps"></a>Nächste Schritte

Mithilfe dieser Links können Sie End-to-End-Anweisungen oder Code anzeigen, der beide Suchvorgänge während der Eingabe veranschaulicht. Beide Codebeispiele enthalten Hybridimplementierungen von Vorschlagsfunktion und der Funktion „AutoVervollständigen“.

+ [Tutorial: Erstellen Ihrer ersten App in C# (Lektion 3)](tutorial-csharp-type-ahead-and-suggestions.md)
+ [C#-Codebeispiel: azure-search-dotnet-samples/create-first-app/3-add-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10/3-add-typeahead)