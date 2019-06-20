---
title: 'Upgrade auf Azure Search .NET SDK Version 9: Azure Search'
description: Migrieren Sie Code von älteren Versionen zu Azure Search .NET SDK Version 9. Hier finden Sie Informationen zu Neuheiten und erfahren, welche Änderungen am Code erforderlich sind.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: a59deed4ac0cec669ddc5e0335f7274586c702e8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65541760"
---
# <a name="upgrade-to-the-azure-search-net-sdk-version-9"></a>Upgrade auf Version 9 des Azure Search .NET SDK

Wenn Sie die Version 7.0-preview oder eine frühere Version des [Azure Search .NET SDK](https://aka.ms/search-sdk) verwenden, unterstützt dieser Artikel Sie beim Upgrade Ihrer Anwendung auf Version 9.

> [!NOTE]
> Wenn Sie die Version 8.0-preview verwenden möchten, um Funktionen zu testen, die noch nicht allgemein verfügbar sind, können Sie ebenfalls die Anweisungen in diesem Artikel nutzen, um von früheren Versionen auf 8.0-preview zu aktualisieren.

Eine allgemeinere exemplarische Vorgehensweise für das SDK sowie Beispiele finden Sie unter [Verwenden von Azure Search aus einer .NET-Anwendung](search-howto-dotnet-sdk.md).

Version 9 des Azure Search .NET SDK enthält viele Änderungen zu früheren Versionen. Einige davon stellen einen Breaking Change dar, sie sollten aber trotzdem nur geringfügige Änderungen an Ihrem Code erfordern. Anweisungen zum Ändern Ihres Codes, um die neue SDK-Version zu verwenden, finden Sie unter [Schritte zum Upgrade](#UpgradeSteps) .

> [!NOTE]
> Wenn Sie Version 4.0-preview oder eine frühere Version verwenden, sollten Sie zuerst ein Upgrade auf Version 5 und anschließend auf Version 9 durchführen. Anweisungen finden Sie unter [Upgrade auf Version 5 des Azure Search .NET SDK](search-dotnet-sdk-migration-version-5.md).
>
> Ihre Azure Search-Dienstinstanz unterstützt mehrere REST-API-Versionen, einschließlich der neuesten. Sie können auch ältere Versionen weiterhin verwenden, aber es wird empfohlen, den Code zur neuesten Version zu migrieren. Wenn Sie die REST-API verwenden, müssen Sie die API-Version bei jeder Anforderung über den api-version-Parameter angeben. Wenn Sie das .NET SDK verwenden, legt die Version des verwendeten SDK die Version der REST-API fest. Wenn Sie ein älteres SDK verwenden, können Sie diesen Code weiterhin ohne Änderungen ausführen, selbst wenn der Dienst aktualisiert wird, um eine neuere Version der API zu unterstützen.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>Neuerungen in Version 9
Version 9 des Azure Search .NET SDK ist für die neueste allgemein verfügbare Version der Azure Search-REST-API (2019-05-06) konzipiert. Dadurch können neue Features von Azure Search in einer .NET-Anwendung verwendet werden. Hierzu zählen etwa folgende:

* [Kognitive Suche](cognitive-search-concept-intro.md) ist eine KI-Funktion in Azure Search zum Extrahieren von Text aus Bildern, Blobs und anderen unstrukturierten Datenquellen, um den Inhalt anzureichern und in einem Azure Search-Index besser durchsuchbar zu machen.
* Die Unterstützung für [komplexe Typen](search-howto-complex-data-types.md) ermöglicht Ihnen, nahezu jede geschachtelte JSON-Struktur in einem Azure Search-Index zu modellieren.
* [AutoVervollständigen](search-autocomplete-tutorial.md) bietet eine Alternative zur **Vorschlags**-API für das Implementieren der Suche während der Eingabe. AutoVervollständigen vervollständigt die von einem Benutzer aktuell eingegebenen Wörter bzw. Ausdrücke.
* Der [Analysemodus JsonLines](search-howto-index-json-blobs.md) ist Teil der Azure-Blobindizierung. Dabei wird ein Suchdokument pro JSON-Entität erstellt, die durch eine neue Zeile getrennt ist.

### <a name="new-preview-features-in-version-80-preview"></a>Neue Previewfunktionen in Version 8.0-preview
Version 8.0-preview des Azure Search .NET SDK zielt auf die API-Version 2017-11-11-Preview ab. Diese Version umfasst dieselben Funktionen wie Version 9 und darüber hinaus:

* [Kundenverwaltete Verschlüsselungsschlüssel](search-security-manage-encryption-keys.md) für die dienstseitige Verschlüsselung ruhender Daten ist eine neue Previewfunktion. Zusätzlich zur integrierten Verschlüsselung ruhender Daten von Microsoft können Sie eine zusätzliche Ebene der Verschlüsselung anwenden, bei der nur Sie der alleinige Besitzer der Schlüssel sind.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Schritte zum Upgrade
Aktualisieren Sie zunächst die NuGet-Referenz für `Microsoft.Azure.Search` , indem Sie entweder die NuGet-Paket-Manager-Konsole verwenden oder mit der rechten Maustaste auf die Projektverweise klicken und in Visual Studio „NuGet-Pakete verwalten...“ auswählen.

Nachdem NuGet die neuen Pakete und deren Abhängigkeiten heruntergeladen hat, erstellen Sie Ihr Projekt neu. Je nachdem, wie Ihr Code strukturiert ist, verläuft die Neuerstellung erfolgreich. Wenn dies der Fall ist, sind Sie startbereit!

Wenn Ihr Build zu einem Fehler führt, müssen Sie sämtliche Buildfehler beheben. Einzelheiten zum Beheben potenzieller Buildfehler finden Sie unter [Breaking Changes in Version 9](#ListOfChanges).

Möglicherweise werden zusätzliche Buildwarnungen im Zusammenhang mit veralteten Methoden oder Eigenschaften angezeigt. Die Warnung enthält Anweisungen dazu, was Sie anstelle der veralteten Funktion verwenden sollten. Wenn Ihre Anwendung z. B. die `DataSourceType.DocumentDb`-Eigenschaft verwendet, sollten Sie folgende Warnung erhalten: „Dieser Member ist veraltet. Verwenden Sie stattdessen CosmosDb.“

Sobald Sie alle Buildfehler und -warnungen behoben haben, können Sie Änderungen an Ihrer Anwendung vornehmen, um die neue Funktionalität zu nutzen. Neue Features im SDK werden unter [Neuerungen in Version 9](#WhatsNew) ausführlich behandelt.

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>Breaking Changes in Version 9

Version 9 enthält einige Breaking Changes, die neben der Neuerstellung der Anwendung ggf. auch Codeänderungen erfordern.

> [!NOTE]
> Die unten stehende Liste von Änderungen ist nicht vollständig. Einige Änderungen führen wahrscheinlich nicht zu Buildfehlern, sie sind aber technisch gesehen „breaking“, da sie die binäre Kompatibilität mit Assemblys verletzen, die von früheren Versionen der Azure Search .NET SDK-Assemblys abhängen. Solche Änderungen werden hier nicht aufgelistet. Erstellen Sie Ihre Anwendung neu, wenn Sie auf Version 9 upgraden, um binäre Kompatibilitätsprobleme zu vermeiden.

### <a name="immutable-properties"></a>Unveränderliche Eigenschaften

Die öffentlichen Eigenschaften mehrerer Modellklassen sind jetzt unveränderlich. Wenn Sie zu Testzwecken benutzerdefinierte Instanzen dieser Klassen erstellen möchten, können Sie die neuen parametrisierten Konstruktoren verwenden:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Änderungen an „Field“

Die `Field`-Klasse wurde so geändert, dass sie nun auch komplexe Felder darstellen kann.

Die folgenden `bool`-Eigenschaften lassen jetzt NULL-Werte zu:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Der Grund hierfür ist, dass diese Eigenschaften jetzt bei komplexen Felder `null` sein müssen. Wenn Ihr Code diese Eigenschaften liest, muss er darauf vorbereitet sein, `null` zu behandeln. Beachten Sie, dass alle anderen Eigenschaften von `Field` schon immer NULL-Werte zugelassen haben und dies auch weiterhin tun. Einige davon werden bei komplexen Feldern auch `null` – insbesondere in folgenden Fällen:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

Der parameterlose Konstruktor von `Field` wurde in `internal` geändert. Von nun an erfordert jedes `Field` zum Zeitpunkt der Erstellung einen expliziten Namen und Datentyp.

### <a name="simplified-batch-and-results-types"></a>Vereinfachte Batch- und Ergebnistypen

In Versionen bis einschließlich 7.0-preview wurden die verschiedenen Klassen, die Gruppen von Dokumenten kapseln, in parallelen Klassenhierarchien gegliedert:

  -  `DocumentSearchResult` und `DocumentSearchResult<T>` erben von `DocumentSearchResultBase`.
  -  `DocumentSuggestResult` und `DocumentSuggestResult<T>` erben von `DocumentSuggestResultBase`.
  -  `IndexAction` und `IndexAction<T>` erben von `IndexActionBase`.
  -  `IndexBatch` und `IndexBatch<T>` erben von `IndexBatchBase`.
  -  `SearchResult` und `SearchResult<T>` erben von `SearchResultBase`.
  -  `SuggestResult` und `SuggestResult<T>` erben von `SuggestResultBase`.

Die abgeleiteten Typen ohne einen generischen Typparameter waren für „dynamisch typisierte“ Szenarien vorgesehen, und es wurde die Verwendung des Typs `Document` vorausgesetzt.

Ab Version 8.0-preview wurden alle Basisklassen und nicht generischen abgeleiteten Klassen entfernt. Für Szenarien mit dynamischer Typisierung können Sie `IndexBatch<Document>`, `DocumentSearchResult<Document>` usw. verwenden.
 
### <a name="removed-extensibleenum"></a>ExtensibleEnum wurde entfernt

Die Basisklasse `ExtensibleEnum` wurde entfernt. Alle Klassen, die davon abgeleitet wurden, sind jetzt Strukturen, z. B. `AnalyzerName`, `DataType` und `DataSourceType`. Ihre `Create`-Methoden wurden ebenfalls entfernt. Sie können Aufrufe von `Create` einfach entfernen, da diese Typen implizit aus Zeichenfolgen konvertierbar sind. Wenn dies zu Compilerfehlern führt, können Sie den Konvertierungsoperator über eine Umwandlung explizit aufrufen, um Typen zu unterscheiden. Sie können Code beispielsweise wie folgt ändern:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", AnalyzerName.Create("my_email_analyzer")) { IsSearchable = true }
    },
    ...
}
```

Und zwar in diesen Code:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", (AnalyzerName)"my_email_analyzer") { IsSearchable = true }
    },
    ...
}
```

Eigenschaften, die optionale Werte dieser Typen enthielten, werden nun explizit so typisiert, dass sie NULL-Werte zulassen – sie bleiben also weiterhin optional.

### <a name="removed-facetresults-and-hithighlights"></a>FacetResults und HitHighlights wurden entfernt

Die Klassen `FacetResults` und `HitHighlights` wurden entfernt. Facettenergebnisse werden jetzt als `IDictionary<string, IList<FacetResult>>` typisiert und Trefferhervorhebungen als `IDictionary<string, IList<string>>`. Eine schnelle Möglichkeit zur Behebung von Buildfehlern, die durch diese Änderung eingeführt wurden, besteht darin, `using`-Aliase am Anfang jeder Datei hinzuzufügen, in denen die entfernten Typen verwendet werden. Beispiel:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Änderung in SynonymMap 

Der Konstruktor `SynonymMap` verfügt nicht mehr über den Parameter `enum` für `SynonymMapFormat`. Diese Enumeration hatte nur einen einzelnen Wert und war daher überflüssig. Sollte diese Änderung bei Ihnen zu Buildfehlern führen, entfernen Sie einfach die Verweise auf den Parameter `SynonymMapFormat`.

### <a name="miscellaneous-model-class-changes"></a>Verschiedene Änderungen an Modellklassen

Die `AutocompleteMode`-Eigenschaft von `AutocompleteParameters` lässt keine NULL-Werte mehr zu. Wenn Sie über Code verfügen, der diese Eigenschaft `null` zuweist, können Sie ihn einfach entfernen. Die Eigenschaft wird dann automatisch mit dem Standardwert initialisiert.

Die Reihenfolge der Parameter für den `IndexAction`-Konstruktor wurde geändert, da dieser Konstruktor nun automatisch generiert wird. Es wird empfohlen, anstelle dieses Konstruktors die Factorymethoden `IndexAction.Upload`, `IndexAction.Merge` usw. zu verwenden.

### <a name="removed-preview-features"></a>Entfernte Vorschaufeatures

Wenn Sie von Version 8.0-preview auf Version 9 aktualisieren, sollten Sie beachten, dass die Verschlüsselung mit vom Kunden verwalteten Schlüsseln entfernt wurde, da diese Funktion sich noch in der Vorschauphase befindet. Insbesondere wurden die `EncryptionKey`-Eigenschaften von `Index` und `SynonymMap` entfernt.

Falls bei Ihrer Anwendung eine harte Abhängigkeit von dieser Funktion besteht, können Sie kein Upgrade auf Version 9 des Azure Search .NET SDK durchführen. Sie können weiterhin Version 8.0-preview verwenden. Beachten Sie jedoch, dass die Verwendung von Vorschau-SDKs in Produktionsanwendungen **nicht empfehlenswert** ist. Vorschaufeatures dienen ausschließlich zur Evaluierung und ändern sich unter Umständen.

> [!NOTE]
> Wenn Sie verschlüsselte Indizes oder Synonymzuordnungen mit Version 8.0-preview des SDK erstellt haben, können Sie diese weiterhin verwenden und auch ihre Definitionen mit der SDK-Version 9 ändern, ohne dass dies negative Auswirkungen auf deren Verschlüsselungsstatus hat. Version 9 des SDK sendet die `encryptionKey`-Eigenschaft nicht an die REST-API. Aus diesem Grund ändert die REST-API auch nicht den Verschlüsselungsstatus der Ressource. 

### <a name="behavioral-change-in-data-retrieval"></a>Behavior Change beim Datenabruf

Wenn Sie die „dynamisch typisierten“ APIs `Search`, `Suggest` oder `Get` verwenden, die Instanzen des Typs `Document` zurückgeben, sollten Sie beachten, dass diese leere JSON-Arrays nun in `object[]` deserialisieren und nicht in `string[]`.

## <a name="conclusion"></a>Zusammenfassung
Weitere Informationen zur Verwendung des Azure Search-.NET SDK finden Sie unter [Hilfe und Anleitung zu .NET](search-howto-dotnet-sdk.md).

Wir freuen uns auf Ihr Feedback zum SDK! Wenn Probleme auftreten, können Sie sich gerne über [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search) an uns wenden. Wenn Sie einen Fehler finden, können Sie das Problem im [Azure .NET SDK-GitHub-Repository](https://github.com/Azure/azure-sdk-for-net/issues)melden. Stellen Sie sicher, dass Sie dem Titel des Problems „[Azure Search]“ voranstellen.

Vielen Dank für die Nutzung von Azure Search!
