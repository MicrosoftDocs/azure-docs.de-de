---
title: C#-Tutorial zu AutoVervollständigen und Vorschlägen
titleSuffix: Azure Cognitive Search
description: Hier erfahren Sie, wie Sie AutoVervollständigen und Vorschläge hinzufügen, um die Suchbegriffeingaben von Benutzern per Dropdownliste zu erfassen. Dieses Tutorial basiert auf einem vorhandenen hotels-Projekt.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 10/05/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 202a7f6b01423045fe7c72db5b42c29ae58f648d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013835"
---
# <a name="tutorial-add-autocomplete-and-suggestions-using-the-net-sdk"></a>Tutorial: Hinzufügen von AutoVervollständigen und Vorschlägen mithilfe des .NET SDK

Es wird beschrieben, wie Sie die AutoVervollständigen-Funktion (Textvervollständigung und vorgeschlagene Ergebnisse) implementieren, die genutzt wird, wenn ein Benutzer mit der Eingabe in einem Suchfeld beginnt. In diesem Tutorial veranschaulichen wir die Ergebnisse von automatisch vervollständigten Abfragen und Ergebnisvorschlägen zunächst separat und anschließend zusammen. Ein Benutzer muss ggf. nur zwei oder drei Zeichen eingeben, um alle verfügbaren Ergebnisse zu ermitteln.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Hinzufügen von Vorschlägen
> * Hinzufügen der Hervorhebung für die Vorschläge
> * Hinzufügen von AutoVervollständigen
> * Kombinieren von AutoVervollständigen und Vorschlägen

## <a name="overview"></a>Übersicht

In diesem Tutorial wird die Anwendung aus dem vorherigen Tutorial [Hinzufügen von Paginierung zu Suchergebnissen](tutorial-csharp-paging.md) um automatische Vervollständigung und Ergebnisvorschläge erweitert.

Eine fertige Version des Codes in diesem Tutorial finden Sie im folgenden Projekt:

* [3-add-typeahead (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/3-add-typeahead)

## <a name="prerequisites"></a>Voraussetzungen

* Projektmappe [2a-add-paging (GitHub)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11/2a-add-paging). Dieses Projekt kann entweder Ihre eigene im vorherigen Tutorial erstellte Version oder eine Kopie von GitHub sein.

Dieses Tutorial wurde aktualisiert, sodass es das Paket [Azure.Search.Documents (Version 11)](https://www.nuget.org/packages/Azure.Search.Documents/) verwendet. Eine frühere Version des .NET SDK finden Sie im [Codebeispiel Microsoft.Azure.Search (Version 10)](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v10).

## <a name="add-suggestions"></a>Hinzufügen von Vorschlägen

Wir beginnen mit der einfachsten Möglichkeit zum Anbieten von Alternativen für den Benutzer: eine Dropdownliste mit Vorschlägen für Ergebnisse.

1. Ändern Sie in der Datei „index.cshtml“ das `@id`-Element der **TextBoxFor**-Anweisung in **azureautosuggest**.

    ```cs
     @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautosuggest" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. Geben Sie nach dieser Anweisung nach dem schließenden **&lt;/div&gt;** -Tag dieses Skript ein. Dieses Skript nutzt das [AutoVervollständigen-Widget](https://api.jqueryui.com/autocomplete/) aus der jQuery UI-Open-Source-Bibliothek, um die Dropdownliste der vorgeschlagenen Ergebnisse darzustellen.

    ```javascript
    <script>
        $("#azureautosuggest").autocomplete({
            source: "/Home/SuggestAsync?highlights=false&fuzzy=false",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

    Mit der ID `"azureautosuggest"` wird das oben genannte Skript mit dem Suchfeld verknüpft. Die source-Option des Widgets wird auf eine Suggest-Methode festgelegt, die die Vorschlags-API mit zwei Abfrageparametern aufruft: **highlights** und **fuzzy**. In diesem Fall werden beide auf „false“ festgelegt. Außerdem sind mindestens zwei Zeichen erforderlich, um die Suche zu initiieren.

### <a name="add-references-to-jquery-scripts-to-the-view"></a>Hinzufügen von Verweisen auf jQuery-Skripts zur Ansicht

1. Ändern Sie zum Zugreifen auf die jQuery-Bibliothek den Abschnitt &lt;head&gt; der Ansichtsdatei in den folgenden Code:

    ```html
    <head>
        <meta charset="utf-8">
        <title>Typeahead</title>
        <link href="https://code.jquery.com/ui/1.12.1/themes/start/jquery-ui.css"
              rel="stylesheet">
        <script src="https://code.jquery.com/jquery-1.10.2.js"></script>
        <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>

        <link rel="stylesheet" href="~/css/hotels.css" />
    </head>
    ```

2. Da ein neuer jQuery-Verweis eingeführt wird, müssen Sie auch den jQuery-Standardverweis in der Datei „_Layout.cshtml“ (im Ordner **Views/Shared**) entfernen bzw. auskommentieren. Suchen Sie nach den folgenden Zeilen, und kommentieren Sie wie gezeigt die erste Skriptzeile aus. Mit dieser Änderung wird vermieden, dass für Verweise auf jQuery ein Konflikt auftritt.

    ```html
    <environment include="Development">
        <!-- <script src="~/lib/jquery/dist/jquery.js"></script> -->
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>
    </environment>
    ```

    Sie können nun die vordefinierten jQuery-Funktionen für AutoVervollständigen verwenden.

### <a name="add-the-suggest-action-to-the-controller"></a>Hinzufügen der „Suggest“-Aktion zum Controller

1. Fügen Sie im Home-Controller die **SuggestAsync**-Aktion hinzu (nach der **PageAsync**-Aktion).

    ```cs
    public async Task<ActionResult> SuggestAsync(bool highlights, bool fuzzy, string term)
    {
        InitSearch();

        // Setup the suggest parameters.
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

        // Only one suggester can be specified per index. It is defined in the index schema.
        // The name of the suggester is set when the suggester is specified by other API calls.
        // The suggester for the hotel database is called "sg", and simply searches the hotel name.
        var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", options).ConfigureAwait(false);

        // Convert the suggested query results to a list that can be displayed in the client.
        List<string> suggestions = suggestResult.Value.Results.Select(x => x.Text).ToList();

        // Return the list of suggestions.
        return new JsonResult(suggestions);
    }
    ```

    Mit dem Parameter **Size** wird angegeben, wie viele Ergebnisse zurückgegeben werden sollen (wenn nichts angegeben ist, wird der Standardwert „5“ verwendet). Beim Erstellen des Indexes wird eine _Vorschlagsfunktion_ für den Suchindex angegeben. Im von Microsoft gehosteten Index für die Beispielhotels ist der Name der Vorschlagsfunktion „sg“, und es wird nur im Feld **HotelName** nach übereinstimmenden Vorschlägen gesucht.

    Per Fuzzyübereinstimmung können auch Ergebnisse, die nicht ganz richtig sind, bis zu einer Editierdistanz in die Ausgabe einbezogen werden. Wenn der Parameter **highlights** auf „true“ festgelegt ist, werden fettgedruckte HTML-Tags der Ausgabe hinzugefügt. Wir legen beide Parameter im nächsten Abschnitt auf „true“ fest.

2. Unter Umständen erhalten Sie einige Syntaxfehler. Fügen Sie in diesem Fall oben in der Datei die folgenden beiden **using**-Anweisungen hinzu.

    ```cs
    using System.Collections.Generic;
    using System.Linq;
    ```

3. Führen Sie die App aus. Werden einige Optionen angezeigt, wenn Sie beispielsweise „po“ eingeben? Versuchen Sie es jetzt mit „pa“.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-po.png" alt-text="Die Eingabe von „*po*“ ergibt zwei Vorschläge" border="false":::

    Beachten Sie, dass die eingegebenen Buchstaben der Anfang eines Worts sein _müssen_ und nicht einfach in einem Wort enthalten sein dürfen.

4. Legen Sie im Skript der Ansicht **&fuzzy** auf „true“ fest, und führen Sie die App erneut aus. Geben Sie jetzt „po“ ein. Sie sehen, dass die Suche annimmt, dass ein Buchstabe falsch ist.
 
    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-fuzzy.png" alt-text="Eingeben von „*pa*“, wobei „fuzzy“ auf „true“ festgelegt ist" border="false":::

    Wenn Sie Interesse daran haben, können Sie sich unter [Lucene-Abfragesyntax in Azure Cognitive Search](./query-lucene-syntax.md) ausführlicher über die Logik informieren, die für Fuzzysuchen verwendet wird.

## <a name="add-highlighting-to-the-suggestions"></a>Hinzufügen der Hervorhebung für die Vorschläge

Sie können die Darstellung der Vorschläge für den Benutzer verbessern, indem Sie den Parameter **highlights** auf „true“ festlegen. Zuerst müssen wir der Ansicht aber Code hinzufügen, um den fettgedruckten Text anzuzeigen.

1. Fügen Sie in der Ansicht (index.cshtml) nach dem oben beschriebenen Skript `"azureautosuggest"` das folgende Skript hinzu.

    ```javascript
    <script>
        var updateTextbox = function (event, ui) {
            var result = ui.item.value.replace(/<\/?[^>]+(>|$)/g, "");
            $("#azuresuggesthighlights").val(result);
            return false;
        };

        $("#azuresuggesthighlights").autocomplete({
            html: true,
            source: "/home/suggest?highlights=true&fuzzy=false&",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },
            select: updateTextbox,
            focus: updateTextbox
        }).data("ui-autocomplete")._renderItem = function (ul, item) {
            return $("<li></li>")
                .data("item.autocomplete", item)
                .append("<a>" + item.label + "</a>")
                .appendTo(ul);
        };
    </script>
    ```

1. Ändern Sie nun die ID des Textfelds so, dass sie wie folgt lautet.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azuresuggesthighlights" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. Wenn Sie die App erneut ausführen, sollte Ihr eingegebener Text in den Vorschlägen fett angezeigt werden. Versuchen Sie es mit der Eingabe von „pa“.
 
    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-highlight.png" alt-text="Eingeben von „*pa*“ mit Hervorhebung" border="false":::

   Die Logik, die oben im Skript für die Hervorhebung verwendet wird, ist nicht narrensicher. Wenn Sie einen Begriff eingeben, der in demselben Namen zweimal enthalten ist, sieht der Fettdruck nicht wie gewünscht aus. Versuchen Sie es mit der Eingabe von „mo“.

   Entwickler müssen eine Antwort auf die Frage finden, wann ein Skript „gut genug“ funktioniert und wann darin enthaltene Fehler korrigiert werden sollten. In diesem Tutorial wird nicht weiter auf Hervorhebungen eingegangen. Die Ermittlung eines präzisen Algorithmus sollte aber ggf. in Erwägung gezogen werden, wenn Hervorhebungen für Ihre Daten nicht effektiv sind. Weitere Informationen finden Sie unter [Treffermarkierung](search-pagination-page-layout.md#hit-highlighting).

## <a name="add-autocomplete"></a>Hinzufügen von AutoVervollständigen

Eine andere Variante, die sich leicht von Vorschlägen unterscheidet, ist AutoVervollständigen (auch als „Textvervollständigung“ bezeichnet). Bei dieser Methode wird ein Abfragebegriff vervollständigt. Sie beginnen wieder mit der einfachsten Implementierung, bevor Sie die Benutzeroberfläche dann verbessern.

1. Geben Sie das folgende Skript nach Ihren vorherigen Skripts in die Ansicht ein.

    ```javascript
    <script>
        $("#azureautocompletebasic").autocomplete({
            source: "/Home/Autocomplete",
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            }
        });
    </script>
    ```

1. Ändern Sie nun die ID des Textfelds so, dass sie wie folgt lautet.

    ```cs
    @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocompletebasic" }) <input value="" class="searchBoxSubmit" type="submit">
    ```

1. Fügen Sie im Home-Controller die **AutocompleteAsync**-Aktion hinzu (nach der **SuggestAsync**-Aktion).

    ```cs
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

    Beachten Sie, dass wir für die Suche mit AutoVervollständigen die gleiche *suggester*-Funktion mit dem Namen „sg“ wie für die Vorschläge verwenden (es sollen also nur die Hotelnamen automatisch vervollständigt werden).

    Es sind verschiedene **AutocompleteMode**-Einstellungen vorhanden, von denen wir **OneTermWithContext** verwenden. Eine Beschreibung zusätzlicher Optionen finden Sie im Artikel zur [AutoVervollständigen-API](/rest/api/searchservice/autocomplete).

1. Führen Sie die App aus. Beachten Sie, dass es sich bei den Optionen in der Dropdownliste um einzelne Wörter handelt. Versuchen Sie es mit der Eingabe von Wörtern, die mit „re“ beginnen. Sie sehen, dass sich die Anzahl von Optionen reduziert, je mehr Buchstaben Sie eingeben.

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-autocompletebasic.png" alt-text="Eingabe mit einfacher AutoVervollständigen-Funktion" border="false":::

    Es sieht so aus, als ob das Skript für Vorschläge, das Sie weiter oben ausgeführt haben, hilfreicher als dieses Skript für AutoVervollständigen ist. Um die automatische Vervollständigung benutzerfreundlicher zu gestalten, sollten Sie Sie mit den empfohlenen Ergebnissen verwenden.

## <a name="combine-autocompletion-and-suggestions"></a>Kombinieren von AutoVervollständigen und Vorschlägen

Die Kombination von AutoVervollständigen und Vorschlägen ist die komplexeste verfügbare Option und führt wahrscheinlich zur höchsten Benutzerfreundlichkeit. Unser Ziel ist es, zusammen mit dem eingegebenen Text die erste Wahl von Azure Cognitive Search zur automatischen Vervollständigung des Texts anzuzeigen. Darüber hinaus möchten wir auch Vorschläge in Form einer Dropdownliste anzeigen.

Es gibt Bibliotheken, die diese Funktionalität enthalten. Dies wird häufig als „automatische Inline-Vervollständigung“ oder auf ähnliche Weise bezeichnet. Wir implementieren dieses Feature aber auf native Weise, damit Sie sich mit den APIs vertraut machen können. Wir beginnen in diesem Beispiel zuerst mit der Arbeit am Controller.

1. Fügen Sie dem Controller eine Aktion hinzu, mit der nur ein AutoVervollständigen-Ergebnis sowie eine angegebene Anzahl von Vorschlägen zurückgegeben wird. Wir geben dieser Aktion den Namen **AutoCompleteAndSuggestAsync**. Fügen Sie im Home-Controller die folgende Aktion nach Ihren anderen neuen Aktionen hinzu.

    ```cs
    public async Task<ActionResult> AutoCompleteAndSuggestAsync(string term)
    {
        InitSearch();

        // Setup the type-ahead search parameters.
        var ap = new AutocompleteOptions()
        {
            Mode = AutocompleteMode.OneTermWithContext,
            Size = 1,
        };
        var autocompleteResult = await _searchClient.AutocompleteAsync(term, "sg", ap);

        // Setup the suggest search parameters.
        var sp = new SuggestOptions()
        {
            Size = 8,
        };

        // Only one suggester can be specified per index. The name of the suggester is set when the suggester is specified by other API calls.
        // The suggester for the hotel database is called "sg" and simply searches the hotel name.
        var suggestResult = await _searchClient.SuggestAsync<Hotel>(term, "sg", sp).ConfigureAwait(false);

        // Create an empty list.
        var results = new List<string>();

        if (autocompleteResult.Value.Results.Count > 0)
        {
            // Add the top result for type-ahead.
            results.Add(autocompleteResult.Value.Results[0].Text);
        }
        else
        {
            // There were no type-ahead suggestions, so add an empty string.
            results.Add("");
        }

        for (int n = 0; n < suggestResult.Value.Results.Count; n++)
        {
            // Now add the suggestions.
            results.Add(suggestResult.Value.Results[n].Text);
        }

        // Return the list.
        return new JsonResult(results);
    }
    ```

    Eine AutoVervollständigen-Option wird oben in der Liste **results** zurückgegeben – gefolgt von allen Vorschlägen.

1. In der Ansicht implementieren wir zuerst ein Feature, bei dem ein hellgraues AutoVervollständigen-Wort direkt unter dem fetteren Text gerendert wird, der vom Benutzer eingegeben wird. HTML verfügt über ein Verfahren für die relative Positionierung zu diesem Zweck. Ändern Sie die **TextBoxFor**-Anweisung (und die umgebenden &lt;div&gt;-Anweisungen) wie unten angegeben. Beachten Sie hierbei, dass ein zweites Suchfeld mit dem Namen **underneath** direkt unter unserem regulären Suchfeld angeordnet ist, indem dieses Suchfeld gegenüber der Standardposition um 39 Pixel verschoben wird.

    ```cs
    <div id="underneath" class="searchBox" style="position: relative; left: 0; top: 0">
    </div>

    <div id="searchinput" class="searchBoxForm" style="position: relative; left: 0; top: -39px">
        @Html.TextBoxFor(m => m.searchText, new { @class = "searchBox", @id = "azureautocomplete" }) <input value="" class="searchBoxSubmit" type="submit">
    </div>
    ```

    Beachten Sie auch, dass wir die ID erneut ändern, und zwar in **azureautocomplete**.

1. Geben Sie in der Ansicht nach allen anderen Skripts, die Sie bisher eingegeben haben, außerdem das folgende Skript ein. Das Skript ist aufgrund der Vielzahl verarbeiteter Eingabeverhalten lang und komplex.

    ```javascript
    <script>
        $('#azureautocomplete').autocomplete({
            delay: 500,
            minLength: 2,
            position: {
                my: "left top",
                at: "left-23 bottom+10"
            },

            // Use Ajax to set up a "success" function.
            source: function (request, response) {
                var controllerUrl = "/Home/AutoCompleteAndSuggestAsync?term=" + $("#azureautocomplete").val();
                $.ajax({
                    url: controllerUrl,
                    dataType: "json",
                    success: function (data) {
                        if (data && data.length > 0) {

                            // Show the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = data[0];

                            // Remove the top suggestion as it is used for inline autocomplete.
                            var array = new Array();
                            for (var n = 1; n < data.length; n++) {
                                array[n - 1] = data[n];
                            }

                            // Show the drop-down list of suggestions.
                            response(array);
                        } else {

                            // Nothing is returned, so clear the autocomplete suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }
                });
            }
        });

        // Complete on TAB.
        // Clear on ESC.
        // Clear if backspace to less than 2 characters.
        // Clear if any arrow key hit as user is navigating the suggestions.
        $("#azureautocomplete").keydown(function (evt) {

            var suggestedText = document.getElementById("underneath").innerHTML;
            if (evt.keyCode === 9 /* TAB */ && suggestedText.length > 0) {
                $("#azureautocomplete").val(suggestedText);
                return false;
            } else if (evt.keyCode === 27 /* ESC */) {
                document.getElementById("underneath").innerHTML = "";
                $("#azureautocomplete").val("");
            } else if (evt.keyCode === 8 /* Backspace */) {
                if ($("#azureautocomplete").val().length < 2) {
                    document.getElementById("underneath").innerHTML = "";
                }
            } else if (evt.keyCode >= 37 && evt.keyCode <= 40 /* Any arrow key */) {
                document.getElementById("underneath").innerHTML = "";
            }
        });

        // Character replace function.
        function setCharAt(str, index, chr) {
            if (index > str.length - 1) return str;
            return str.substr(0, index) + chr + str.substr(index + 1);
        }

        // This function is needed to clear the "underneath" text when the user clicks on a suggestion, and to
        // correct the case of the autocomplete option when it does not match the case of the user input.
        // The interval function is activated with the input, blur, change, or focus events.
        $("#azureautocomplete").on("input blur change focus", function (e) {

            // Set a 2 second interval duration.
            var intervalDuration = 2000, 
                interval = setInterval(function () {

                    // Compare the autocorrect suggestion with the actual typed string.
                    var inputText = document.getElementById("azureautocomplete").value;
                    var autoText = document.getElementById("underneath").innerHTML;

                    // If the typed string is longer than the suggestion, then clear the suggestion.
                    if (inputText.length > autoText.length) {
                        document.getElementById("underneath").innerHTML = "";
                    } else {

                        // If the strings match, change the case of the suggestion to match the case of the typed input.
                        if (autoText.toLowerCase().startsWith(inputText.toLowerCase())) {
                            for (var n = 0; n < inputText.length; n++) {
                                autoText = setCharAt(autoText, n, inputText[n]);
                            }
                            document.getElementById("underneath").innerHTML = autoText;

                        } else {
                            // The strings do not match, so clear the suggestion.
                            document.getElementById("underneath").innerHTML = "";
                        }
                    }

                    // If the element loses focus, stop the interval checking.
                    if (!$input.is(':focus')) clearInterval(interval);

                }, intervalDuration);
        });
    </script>
    ```

    Achten Sie auf die Verwendung der Funktion **interval**. Hiermit wird sowohl der zugrunde liegende Text gelöscht, wenn er nicht mehr mit der Eingabe des Benutzers übereinstimmt, als auch die gleiche Schreibweise (Groß- oder Kleinbuchstaben) bei der Eingabe des Benutzers festgelegt („pa“ entspricht beim Suchen „PA“, „pA“ und „Pa“), damit der Überlagerungstext stimmt.

    Lesen Sie sich die Kommentare im Skript durch, um sich besser damit vertraut zu machen.

1. Abschließend müssen wir noch eine kleinere Änderung an zwei HTML-Klassen vornehmen, um sie transparent zu machen. Fügen Sie den Klassen **searchBoxForm** und **searchBox** in der Datei „hotels.css“ die folgende Zeile hinzu.

    ```html
    background: rgba(0,0,0,0);
    ```

1. Führen Sie anschließend die App aus. Geben Sie im Suchfeld „pa“ ein. Erhalten Sie „palace“ als AutoVervollständigen-Vorschlag und zwei Namen von Hotels, die „pa“ enthalten?

    :::image type="content" source="media/tutorial-csharp-create-first-app/azure-search-suggest-autocomplete.png" alt-text="Eingabe mit Inline-Autovervollständigen und Vorschlägen" border="false":::

1. Versuchen Sie, den AutoVervollständigen-Vorschlag mit der TAB-TASTE zu akzeptieren und Vorschläge mit den Pfeiltasten und der TAB-TASTE auszuwählen. Starten Sie dann einen neuen Versuch, indem Sie die Maus und einen einzelnen Klick verwenden. Vergewissern Sie sich, dass alle Situationen vom Skript richtig verarbeitet werden.

    Unter Umständen treffen Sie die Entscheidung, dass es einfacher ist, eine Bibliothek zu laden, die über dieses Feature verfügt. Aber Sie kennen jetzt wenigstens ein Verfahren, wie Sie die Inline-Funktion „AutoVervollständigen“ einrichten können.

## <a name="takeaways"></a>Wesentliche Punkte

Beachten Sie die folgenden Erkenntnisse aus diesem Projekt:

* Bei der AutoVervollständigen-Funktion (auch als „Textvervollständigung“ bezeichnet) und der Verwendung von Vorschlägen ist es für Benutzer möglich, mit nur wenigen eingegebenen Zeichen auf die gewünschten Daten zuzugreifen.
* Wenn AutoVervollständigen und Vorschläge kombiniert werden, führt dies zu einer hohen Benutzerfreundlichkeit.
* Testen Sie die AutoVervollständigen-Funktionen immer mit allen Eingabearten.
* Die Funktion **setInterval** kann beim Überprüfen und Korrigieren von Elementen der Benutzeroberfläche hilfreich sein.

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Tutorial sehen wir uns ein anderes Verfahren an, mit dem wir die Benutzerfreundlichkeit erhöhen können. Wir verwenden Facetten, um Suchen mit nur einem Klick einzugrenzen.

> [!div class="nextstepaction"]
> [C#-Tutorial: Verwenden von Facetten zur Vereinfachung der Navigation – Azure Cognitive Search](tutorial-csharp-facets.md)
