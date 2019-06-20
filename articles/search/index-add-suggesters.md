---
title: Hinzufügen von Eingabevorschlägen für Abfragen zu einem Index – Azure Search
description: Aktivieren Sie Eingabevorschläge für Abfrageaktionen in Azure Search, indem Sie Vorschlagsfunktionen erstellen und Anforderungen formulieren, die automatisch Vervollständigungen oder Vorschläge für Abfrageausdrücke abrufen.
ms.date: 05/02/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: eb6667a1429382ed566826de64ad7ffbe83183cf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65521885"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Hinzufügen von Vorschlagsfunktionen zu einem Index für Eingabevorschläge in Azure Search

Eine**Vorschlagsfunktion** ist ein Konstrukt in einem [Azure Search-Index](search-what-is-an-index.md), das eine „Vorschläge während der Eingabe“-Umgebung unterstützt. Sie enthält eine Liste der Felder, für die Sie Eingabevorschläge für die Abfrage aktivieren möchten. In einem Index unterstützt dieselbe Vorschlagsfunktion diese beiden Varianten von Eingabevorschlägen: *AutoVervollständigen* vervollständigt den Begriff oder Ausdruck, den Sie eingeben, *Vorschläge* liefert eine kurze Liste von Ergebnissen. 

Der folgende Screenshot veranschaulicht diese beiden Features für Eingabevorschläge. Auf dieser Xbox-Suchseite führen die AutoVervollständigen-Elemente Sie zu einer neuen Suchergebnisseite für die Abfrage, während die Vorschläge tatsächliche Ergebnisse sind, über die Sie zu einer Seite für das entsprechende Spiel gelangen. Sie können AutoVervollständigen auf ein Element in einer Suchleiste beschränken oder eine Liste wie die hier gezeigte bereitstellen. Für Vorschläge können Sie jeden Bereich eines Dokuments anzeigen, der das Ergebnis am besten beschreibt.

![Visueller Vergleich von automatisch vervollständigten Abfragen und Vorschlägen für Abfragen](./media/index-add-suggesters/visual-comparison-suggest-complete.png "Visueller Vergleich von automatisch vervollständigten Abfragen und Vorschlägen für Abfragen")

Um dieses Verhalten in Azure Search zu implementieren, gibt es eine Index- und eine Abfragekomponente. 

+ Bei der Indexkomponente handelt es sich um eine Vorschlagsfunktion. Sie können das Portal, eine REST-API oder das .NET SDK verwenden, um eine Vorschlagsfunktion zu erstellen. 

+ Die Abfragekomponente ist eine in der Abfrageanforderung angegebene Aktion (entweder ein Vorschlag oder eine AutoVervollständigen-Aktion). 

Die „Search-as-you-Type“-Unterstützung (Vorschläge bei der Eingabe) wird auf Feldebene aktiviert. Sie können beide Verhaltensweisen für Eingabevorschläge in derselben Suchlösung implementieren, wenn Sie ein ähnliches Erlebnis wie im Screenshot wünschen. Beide Anforderungen zielen auf die *Dokumentensammlung* eines bestimmten Index ab, und die Antworten werden zurückgegeben, nachdem ein Benutzer eine Zeichenfolge aus mindestens 3 Zeichen eingegeben hat.

## <a name="create-a-suggester"></a>Erstellen einer Vorschlagsfunktion

Eine Vorschlagsfunktion verfügt zwar über mehrere Eigenschaften, in erster Linie handelt es sich aber um eine Sammlung von Feldern, für die Sie die Umgebung für Eingabevorschläge aktivieren. In einer Reise-App können Eingabevorschläge beispielsweise für die Suche nach Zielen, Städten und Attraktionen aktiviert sein. Somit würden alle drei Felder in die Feldsammlung aufgenommen.

Um eine Vorschlagsfunktion zu erstellen, fügen Sie sie zu einem Indexschema hinzu. Sie können eine Vorschlagsfunktion in einem Index verwenden (nämlich eine Vorschlagsfunktion in der Sammlung der Vorschlagsfunktionen). 

### <a name="use-the-rest-api"></a>Verwenden der REST-API

In der REST-API können Sie Vorschlagsfunktionen über [Index erstellen](https://docs.microsoft.com/rest/api/searchservice/create-index) oder [Index aktualisieren](https://docs.microsoft.com/rest/api/searchservice/update-index) hinzufügen. 

  ```json
  {
    "name": "hotels",
    "fields": [
      . . .
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["hotelName", "category"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```
Nachdem eine Vorschlagsfunktion erstellt wurde, fügen Sie die [Vorschlags-API](https://docs.microsoft.com/rest/api/searchservice/suggestions) oder die [AutoVervollständigen-API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) zu Ihrer Abfragelogik hinzu, um das Feature aufzurufen.

### <a name="use-the-net-sdk"></a>Verwenden des .NET SDK

Definieren Sie in C# ein [Suggester-Objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters` ist eine Sammlung, aber sie kann nur ein Element enthalten. 

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelId", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

## <a name="property-reference"></a>Eigenschaftsverweis

Bei Vorschlagsfunktionen ist zu beachten, dass ein Name (Vorschlagsfunktion werden in einer Anforderung anhand des Namens referenziert), ein Suchmodus („SearchMode“, derzeit nur ein einziger verfügbar: „analyzingInfixMatching“) und die Liste der Felder, für die Eingabevorschläge aktiviert sind, vorhanden sein muss. 

Zu den Eigenschaften, mit denen ein Vorschlag definiert wird, zählen unter anderem:

|Eigenschaft      |BESCHREIBUNG      |
|--------------|-----------------|
|`name`        |Der Name der Vorschlagsfunktion. Sie verwenden den Namen der Vorschlagsfunktion beim Aufrufen der [Vorschläge-REST-API](https://docs.microsoft.com/rest/api/searchservice/suggestions) oder [AutoVervollständigen-REST-API](https://docs.microsoft.com/rest/api/searchservice/autocomplete).|
|`searchMode`  |Die Strategie, mit der nach möglichen Ausdrücken gesucht wird. Derzeit wird nur der Modus `analyzingInfixMatching` unterstützt. Darin werden Ausdrücke am Anfang oder in der Mitte von Sätzen flexibel verglichen.|
|`sourceFields`|Eine Liste mit einem oder mehreren Feldern, die als Quelle für den Inhalt von Vorschlägen dienen. Als Vorschlagsquellen sind nur Felder vom Typ `Edm.String` und `Collection(Edm.String)` zulässig. Es können nur Felder ohne benutzerdefinierte Sprachanalyse verwendet werden.<p/>Geben Sie nur die Felder an, die sich für eine erwartete und angemessene Antwort eignen, sei es eine vollständige Zeichenfolge in einer Suchleiste oder eine Dropdownliste.<p/>Ein Hotelname ist ein guter Kandidat, weil er präzise ist. Ausführliche Felder wie Beschreibungen und Kommentare sind zu informationsreich. Sich wiederholende Felder wie Kategorien und Tags sind ebenso weniger effektiv. In den Beispielen schließen wir ohnehin „category“ ein, um zu zeigen, dass Sie mehrere Felder einbeziehen können. |

## <a name="when-to-create-a-suggester"></a>Wann wird eine Vorschlagsfunktion erstellt?

Um das Neuerstellen eines Index zu vermieden, müssen gleichzeitig eine Vorschlagsfunktion und die in `sourceFields` angegebenen Felder erstellt werden.

Wenn Sie eine Vorschlagsfunktion zu einem bestehenden Index hinzufügen, in dem bestehende Felder in `sourceFields` enthalten sind, ändert sich die Felddefinition grundlegend und eine Neuerstellung ist erforderlich. Weitere Informationen finden Sie unter [Neuerstellen eines Azure Search-Indexes](search-howto-reindex.md).

## <a name="how-to-use-a-suggester"></a>Wie wird eine Vorschlagsfunktion verwendet?

Wie zuvor erwähnt, können Sie eine Vorschlagsfunktion für Vorschläge, AutoVervollständigen oder beides verwenden. 

Eine Vorschlagsfunktion wird in einer Anforderung zusammen mit dem Vorgang referenziert. Geben Sie beispielsweise in einem GET-REST-Aufruf entweder `suggest` oder `autocomplete` für die Dokumentensammlung an. Für REST: Nachdem eine Vorschlagsfunktion erstellt wurde, verwenden Sie die [Vorschlags-API](https://docs.microsoft.com/rest/api/searchservice/suggestions) oder die [AutoVervollständigen-API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) in Ihrer Abfragelogik.

Für .NET verwenden Sie [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet) oder [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet).

Ein Beispiel, das beide Anforderungen veranschaulicht, finden Sie unter [Beispiel für das Hinzufügen von AutoVervollständigen und Vorschlägen in Azure Search](search-autocomplete-tutorial.md).

## <a name="sample-code"></a>Beispielcode

Das Beispiel [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) enthält sowohl C#- als auch Java-Code und veranschaulicht ein Vorschlagsfunktionskonstrukt, vorgeschlagene Abfragen, AutoVervollständigen und Facet-Navigation. 

Es werden ein Azure Search-Sandboxdienst und ein vorinstallierter Index verwendet, sodass Sie lediglich F5 zum Ausführen drücken müssen. Es ist weder ein Abonnement noch eine Anmeldung erforderlich.

## <a name="next-steps"></a>Nächste Schritte

Wir empfehlen, das folgende Beispiel anzuschauen, um zu verstehen, wie die Anforderungen formuliert werden.

> [!div class="nextstepaction"]
> [Beispiele für Vorschläge und AutoVervollständigen](search-autocomplete-tutorial.md) 
