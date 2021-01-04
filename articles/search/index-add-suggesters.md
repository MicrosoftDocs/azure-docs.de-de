---
title: Erstellen einer Vorschlagsfunktion
titleSuffix: Azure Cognitive Search
description: Aktivieren Sie Eingabevorschläge für Abfrageaktionen in der kognitiven Azure-Suche, indem Sie Vorschlagsfunktionen erstellen und Anforderungen formulieren, die automatisch Vervollständigungen oder Vorschläge für Abfrageausdrücke abrufen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4390291eb96c11b8fb7fdb48eb92abaf802b80c0
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030780"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Erstellen einer Vorschlagsfunktion zum Ermöglichen von AutoVervollständigen und vorgeschlagenen Ergebnissen in einer Abfrage

In Azure Cognitive Search ermöglicht eine *Vorschlagsfunktion* die Suche während der Eingabe. Ein Vorschlag ist eine interne Datenstruktur, die aus einer Feldsammlung besteht. Die Felder werden einer zusätzlichen Tokenisierung unterzogen, wobei Präfixsequenzen zur Unterstützung von Übereinstimmungen bei Teilausdrücken generiert werden.

Wenn eine Vorschlagsfunktion z. B. ein Feld „Stadt“ enthält, werden resultierende Präfixkombinationen von „Sea“, „Seat“, „seatt“ und „seattl“ für den Begriff „Seattle“ erstellt. Präfixe werden in invertierten Indizes gespeichert – eines für jedes Feld, das in der Feldsammlung der Vorschlagsfunktion angegeben ist.

## <a name="typeahead-experiences-in-cognitive-search"></a>Vorschlagssuche in Cognitive Search

Eine Vorschlagsfunktion unterstützt zwei Umgebungen: *AutoVervollständigen* zum Vervollständigen einer partiellen Eingabe zu einem Gesamtbegriff für eine Abfrage und *Vorschläge*, die einen direkten Aufruf eines bestimmten Ergebnisses ermöglichen. AutoVervollständigen führt zu einer Abfrage. Vorschläge führen zu einem übereinstimmenden Dokument.

Der folgende Screenshot aus dem Beispiel [Erstellen Ihrer ersten App in C#](tutorial-csharp-type-ahead-and-suggestions.md) veranschaulicht beide Varianten. AutoVervollständigen erwartet einen potenziellen Begriff und ergänzt etwa „Zw“ mit „illing“. Vorschläge sind Minisuchergebnisse, bei denen ein Feld wie „Hotelname“ für ein entsprechendes Hotelsuchdokument aus dem Index steht. Für Vorschläge können Sie alle Felder bereitstellen, die beschreibende Informationen enthalten.

![Visueller Vergleich von AutoVervollständigen und vorgeschlagenen Abfragen](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Visueller Vergleich von AutoVervollständigen und vorgeschlagenen Abfragen")

Sie können diese Features einzeln oder zusammen verwenden. Um dieses Verhalten in der kognitiven Azure-Suche zu implementieren, gibt es eine Index- und eine Abfragekomponente. 

+ Fügen Sie eine Vorschlagsfunktion einer Suchindexdefinition hinzu. Im weiteren Verlauf dieses Artikels geht es um die Erstellung einer Vorschlagsfunktion.

+ Sie können eine Abfrage, die eine Vorschlagsfunktion unterstützt, mit einer der [unten aufgeführten APIs](#how-to-use-a-suggester) in Form einer Vorschlags- oder AutoVervollständigen-Anforderung aufrufen.

Die Unterstützung der Suche während der Eingabe wird auf Feldebene für Zeichenfolgenfelder aktiviert. Sie können beide Verhaltensweisen für Eingabevorschläge in derselben Suchlösung implementieren, wenn Sie ein ähnliches Erlebnis wie im Screenshot wünschen. Beide Anforderungen zielen auf die *Dokumentensammlung* eines bestimmten Index ab, und die Antworten werden zurückgegeben, nachdem ein Benutzer eine Zeichenfolge aus mindestens 3 Zeichen eingegeben hat.

## <a name="how-to-create-a-suggester"></a>Erstellen einer Vorschlagsfunktion

Um eine Vorschlagsfunktion zu erstellen, fügen Sie sie einer [Indexdefinition](/rest/api/searchservice/create-index) hinzu. Eine Vorschlagsfunktion erhält einen Namen und eine Sammlung von Feldern, über die Typeahead aktiviert wird. [Legen Sie die einzelnen Eigenschaften fest](#property-reference). Der beste Zeitpunkt zum Erstellen einer Vorschlagsfunktion ist bei der Definition des Felds, für das sie verwendet wird.

+ Verwenden Sie nur Zeichenfolgenfelder.

+ Wenn das Zeichenfolgenfeld Teil eines komplexen Typs ist (z. B. ein Ort-Feld innerhalb einer Adresse), schließen Sie das übergeordnete Element in das Feld ein: `"Address/City"` (REST und C# und Python) oder `["Address"]["City"]` (JavaScript).

+ Verwenden Sie für das Feld das standardmäßige Lucene-Standardanalysetool (`"analyzer": null`) oder ein [Sprachanalysetool](index-add-language-analyzers.md) (z. B. `"analyzer": "en.Microsoft"`).

Wenn Sie versuchen, eine Vorschlagsfunktion mithilfe bereits vorhandener Felder zu erstellen, wird dies von der API nicht zugelassen. Präfixe werden während der Indizierung erstellt, wenn partielle Begriffe in Kombinationen aus mehreren Zeichen zusammen mit ganzen Begriffen in Token umgewandelt werden. Wenn vorhandene Felder bereits tokenisiert sind, müssen Sie den Index neu erstellen, wenn Sie ihn zu einer Vorschlagsfunktion hinzufügen möchten. Weitere Informationen finden Sie unter [Neuerstellen eines Azure Cognitive Search-Indexes](search-howto-reindex.md).

### <a name="choose-fields"></a>Auswählen von Feldern

Auch wenn eine Vorschlagsfunktion über mehrere Eigenschaften verfügt, handelt es sich in erster Linie jedoch um eine Sammlung von Zeichenfolgenfeldern, für die Sie eine Umgebung für die Suche während der Eingabe aktivieren. Es gibt für jeden Index eine Vorschlagsfunktion. Daher muss die Liste der Vorschlagsfunktionen alle Felder enthalten, die Inhalte für Vorschläge und AutoVervollständigen beitragen.

AutoVervollständigen profitiert von einem größeren Pool von Feldern, aus denen Begriffe abgerufen werden können, da der zusätzliche Inhalt mehr Potenzial für die Vervollständigung von Begriffen bietet.

Andererseits führen Vorschläge zu besseren Ergebnissen, wenn die Feldauswahl selektiv ist. Denken Sie daran, dass es sich bei einem Vorschlag um einen Proxy für ein Suchdokument handelt. Daher benötigen Sie Felder, die am besten ein Einzelergebnis darstellen. Namen, Titel oder andere eindeutige Felder, die für Unterscheidung zwischen mehreren Übereinstimmungen sorgen, funktionieren am besten. Wenn Felder aus wiederkehrenden Werten bestehen, setzen sich die Vorschläge aus identischen Ergebnissen zusammen, und ein Benutzer weiß nicht, auf welches er klicken soll.

Um beide Anforderungen für die Suche während der Eingabe zu erfüllen, fügen Sie alle Felder hinzu, die Sie zum AutoVervollständigen benötigen, und steuern Sie dann mit **$Select**, **$top**, **$filter** und **searchFields** die Ergebnisse für Vorschläge.

### <a name="choose-analyzers"></a>Auswählen der Analysetools

Durch die Auswahl eines Analysetools legen Sie fest, wie Felder in Token umgewandelt und anschließend mit einem Präfix versehen werden. Beispielsweise führt die Verwendung eines Sprachanalysetools für eine Zeichenfolge mit Bindestrichen wie „App-basiert“ zu den Tokenkombinationen „App“, „basiert“, „App-basiert“. Wenn Sie das Standard-Lucene-Analysetool verwenden, ist die Zeichenfolge mit dem Bindestrich nicht enthalten. 

Berücksichtigen Sie bei der Auswertung von Analysetools auch die Verwendung der [API für die Textanalyse](/rest/api/searchservice/test-analyzer), um zu erkennen, wie Begriffe verarbeitet werden. Beim Erstellen eines Indexes können Sie verschiedene Analysetools für eine Zeichenfolge ausprobieren, um sich die ausgegebenen Token anzusehen.

Felder, die [benutzerdefinierte Analysetools](index-add-custom-analyzers.md) oder [vordefinierte Analysetools](index-add-custom-analyzers.md#predefined-analyzers-reference) verwenden (mit Ausnahme von Standard-Lucene) sind explizit nicht zulässig, um mangelhafte Ergebnisse zu vermeiden.

> [!NOTE]
> Wenn Sie Einschränkungen der Analysetools umgehen müssen, z. B. wenn Sie ein Schlüsselwort oder eine NGram-Analyse für bestimmte Abfrageszenarien benötigen, sollten Sie zwei separate Felder für denselben Inhalt verwenden. Eines der Felder kann so über Vorschlagsfunktionen verfügen, während das andere mit einer benutzerdefinierten Analysetoolkonfiguration eingerichtet wird.

## <a name="create-using-the-portal"></a>Erstellen über das Portal

Wenn Sie einen Index mit **Index hinzufügen** oder dem **Datenimport**-Assistenten erstellen, können Sie dabei eine Vorschlagsfunktion aktivieren:

1. Geben Sie in der Indexdefinition einen Namen für die Vorschlagsfunktion ein.

1. Aktivieren Sie in den einzelnen Felddefinitionen für neue Felder das Kontrollkästchen in der Spalte „Vorschläge“. Nur Zeichenfolgenfelder verfügen über ein Kontrollkästchen. 

Wie bereits erwähnt, wirkt sich die Auswahl des Analysetools auf die Tokenisierung und das Voranstellen von Präfixen aus. Beachten Sie beim Aktivieren der Vorschlagsfunktion die gesamte Felddefinition. 

## <a name="create-using-rest"></a>Erstellen mit der REST-API

Fügen Sie Vorschlagsfunktionen in der REST-API über [Index erstellen](/rest/api/searchservice/create-index) oder [Index aktualisieren](/rest/api/searchservice/update-index) hinzu. 

  ```json
  {
    "name": "hotels-sample-index",
    "fields": [
      . . .
          {
              "name": "HotelName",
              "type": "Edm.String",
              "facetable": false,
              "filterable": false,
              "key": false,
              "retrievable": true,
              "searchable": true,
              "sortable": false,
              "analyzer": "en.microsoft",
              "indexAnalyzer": null,
              "searchAnalyzer": null,
              "synonymMaps": [],
              "fields": []
          },
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["HotelName"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```

## <a name="create-using-net"></a>Erstellen mit .NET

Definieren Sie in C# ein [SearchSuggester-Objekt](/dotnet/api/azure.search.documents.indexes.models.searchsuggester). `Suggesters` ist eine Sammlung für ein SearchIndex-Objekt, es akzeptiert aber nur ein Element. Fügen Sie eine Vorschlagsfunktion zur Indexdefinition hinzu.

```csharp
private static void CreateIndex(string indexName, SearchIndexClient indexClient)
{
    FieldBuilder fieldBuilder = new FieldBuilder();
    var searchFields = fieldBuilder.Build(typeof(Hotel));

    var definition = new SearchIndex(indexName, searchFields);

    var suggester = new SearchSuggester("sg", new[] { "HotelName", "Category", "Address/City", "Address/StateProvince" });
    definition.Suggesters.Add(suggester);

    indexClient.CreateOrUpdateIndex(definition);
}
```

## <a name="property-reference"></a>Eigenschaftsverweis

|Eigenschaft      |BESCHREIBUNG      |
|--------------|-----------------|
|`name`        | Dies wird in der Definition der Vorschlagsfunktion angegeben, kann aber auch für eine AutoVervollständigen- oder Vorschlagsanforderung aufgerufen werden. |
|`sourceFields`| In der Vorschlagsfunktionsdefinition angegeben. Es handelt sich dabei um eine Liste mit mindestens einem Feld, die als Quelle für den Inhalt von Vorschlägen dient. Felder müssen vom Typ `Edm.String` und `Collection(Edm.String)` sein. Wenn ein Analysetool für das Feld angegeben wird, muss es sich um ein benanntes lexikalisches Analysetool aus [dieser Liste](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername) (kein benutzerdefiniertes Analysetool) handeln.<p/> Folgende Vorgehensweise wird empfohlen: Geben Sie nur die Felder an, die sich für eine erwartete und angemessene Antwort eignen, sei es eine vollständige Zeichenfolge in einer Suchleiste oder eine Dropdownliste.<p/>Ein Hotelname ist ein guter Kandidat, weil er präzise ist. Ausführliche Felder wie Beschreibungen und Kommentare sind zu informationsreich. Sich wiederholende Felder wie Kategorien und Tags sind ebenso weniger effektiv. In den Beispielen schließen wir ohnehin „category“ ein, um zu zeigen, dass Sie mehrere Felder einbeziehen können. |
|`searchMode`  | Parameter nur für REST, wird aber auch im Portal angezeigt. Dieser Parameter ist im .NET SDK nicht verfügbar. Gibt die Strategie an, mit der nach möglichen Ausdrücken gesucht wird. Aktuell wird nur der Modus `analyzingInfixMatching` unterstützt, der derzeit am Anfang eines Begriffs Übereinstimmungen findet.|

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Verwenden einer Vorschlagsfunktion

Eine Vorschlagsfunktion wird in einer Abfrage verwendet. Nachdem Sie eine Vorschlagsfunktion erstellt haben, rufen Sie eine der folgenden APIs auf, um eine Umgebung für die Suche während der Eingabe bereitzustellen:

+ [Vorschläge-REST-API](/rest/api/searchservice/suggestions)
+ [AutoVervollständigen-REST-API](/rest/api/searchservice/autocomplete)
+ [SuggestAsync-Methode](/dotnet/api/azure.search.documents.searchclient.suggestasync)
+ [AutocompleteAsync-Methode](/dotnet/api/azure.search.documents.searchclient.autocompleteasync)

In einer Suchanwendung sollte für Clientcode eine Bibliothek wie [jQuery UI Autocomplete](https://jqueryui.com/autocomplete/) verwendet werden, um die partielle Abfrage zu erfassen und die Übereinstimmung bereitzustellen. Weitere Informationen zu dieser Aufgabe finden Sie unter [Hinzufügen von AutoVervollständigen oder Vorschlägen zu Clientcode](search-autocomplete-tutorial.md).

Die Verwendung der API wird im folgenden Aufruf der AutoVervollständigen-REST-API veranschaulicht. In diesem Beispiel ergeben sich zwei wichtige Erkenntnisse. Erstens: Wie bei allen Abfragen wird der Vorgang für die Dokumentensammlung eines Indexes ausgeführt. Die Abfrage enthält einen **Such** parameter, der in diesem Fall die partielle Abfrage bereitstellt. Zweitens: Sie müssen der Anforderung den Namen der Vorschlagsfunktion (**suggesterName**) hinzufügen. Wenn eine Vorschlagsfunktion nicht im Index definiert ist, tritt bei einem Aufruf von „AutoVervollständigen“ oder „Vorschläge“ ein Fehler auf.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>Beispielcode

+ Das Beispiel [Erstellen Ihrer ersten App in C# (Lektion 3 – Hinzufügen der Suche während der Eingabe)](tutorial-csharp-type-ahead-and-suggestions.md) veranschaulicht vorgeschlagene Abfragen, AutoVervollständigen und Facettennavigation. Dieses Codebeispiel wird in einem Sandboxdienst für den Azure Cognitive Search-Dienst ausgeführt und verwendet einen vorinstallierten Hotelindex, für den eine Vorschlagsfunktion bereits erstellt wurde, sodass Sie zum Ausführen der Anwendung lediglich F5 drücken müssen. Es ist weder ein Abonnement noch eine Anmeldung erforderlich.

## <a name="next-steps"></a>Nächste Schritte

Wir empfehlen den folgenden Artikel, um mehr über das Formulieren von Anforderungen zu erfahren.

> [!div class="nextstepaction"]
> [Hinzufügen von AutoVervollständigen und Vorschlägen zu Clientcode](search-autocomplete-tutorial.md)