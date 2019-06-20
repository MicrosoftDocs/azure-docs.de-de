---
title: Upgrade auf Azure Search .NET SDK Version 3 – Azure Search
description: Migrieren von Code zu Azure Search .NET SDK Version 3 von älteren Versionen. Hier finden Sie Informationen zu Neuheiten und erfahren, welche Änderungen am Code erforderlich sind.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: d41c2b541bf80448d180a1d081c255e5bf754e5e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65147331"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-3"></a>Upgrade auf Version 3 des Azure Search .NET SDK

<!--- DETAILS in the word doc
cosmosdb
NER v1 skill 
Indexer execution result errors no longer have status
the data source API will no longer return in the response of any REST operation, the connection string specified by the user.
--->

Wenn Sie die Version „2.0-preview“ oder eine frühere Version des [Azure Search .NET SDK](https://aka.ms/search-sdk) verwenden, unterstützt dieser Artikel Sie beim Upgrade Ihrer Anwendung auf Version 3.0.

Eine allgemeinere exemplarische Vorgehensweise für das SDK sowie Beispiele finden Sie unter [Verwenden von Azure Search aus einer .NET-Anwendung](search-howto-dotnet-sdk.md).

Version 3 des Azure Search .NET SDK enthält einige Änderungen zu früheren Versionen. Dabei handelt es sich hauptsächlich um kleinere Änderungen, sodass zum Ändern Ihres Codes nur ein geringer Aufwand erforderlich sein sollte. Anweisungen zum Ändern Ihres Codes, um die neue SDK-Version zu verwenden, finden Sie unter [Schritte zum Upgrade](#UpgradeSteps) .

> [!NOTE]
> Wenn Sie Version 1.0.2-preview oder eine frühere Version verwenden, sollten Sie zuerst auf Version 1.1 und dann auf Version 3 upgraden. Anweisungen finden Sie unter [Upgrade auf Version 1.1 des Azure Search-.NET SDK](search-dotnet-sdk-migration-version-1.md).
>
> Ihre Azure Search-Dienstinstanz unterstützt mehrere REST-API-Versionen, einschließlich der neuesten. Sie können auch ältere Versionen weiterhin verwenden, aber es wird empfohlen, den Code zur neuesten Version zu migrieren. Wenn Sie die REST-API verwenden, müssen Sie die API-Version bei jeder Anforderung über den api-version-Parameter angeben. Wenn Sie das .NET SDK verwenden, legt die Version des verwendeten SDK die Version der REST-API fest. Wenn Sie ein älteres SDK verwenden, können Sie diesen Code weiterhin ohne Änderungen ausführen, selbst wenn der Dienst aktualisiert wird, um eine neuere Version der API zu unterstützen.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>Neuerungen in Version 3
Version 3 des Azure Search .NET SDK ist für die neueste allgemein verfügbare Version der Azure Search-REST-API (2016-09-01) konzipiert. Dadurch können viele neue Features von Azure Search in einer .NET-Anwendung verwendet werden. Hierzu zählen etwa folgende:

* [Benutzerdefinierte Analysen](https://aka.ms/customanalyzers)
* Unterstützung von Indexern für [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) und [Azure Table Storage](search-howto-indexing-azure-tables.md)
* Anpassen von Indexern über [Feldzuordnungen](search-indexer-field-mappings.md)
* ETags-Unterstützung für sichere gleichzeitige Aktualisierung von Indexdefinitionen, Indexern und Datenquellen
* Unterstützung der deklarativen Erstellung von Indexfelddefinitionen mittels Dekorierung Ihrer Modellklasse und Verwendung der neuen `FieldBuilder`-Klasse.
* Unterstützung für .NET Core und .NET Portable Profile 111

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Schritte zum Upgrade
Aktualisieren Sie zunächst die NuGet-Referenz für `Microsoft.Azure.Search` , indem Sie entweder die NuGet-Paket-Manager-Konsole verwenden oder mit der rechten Maustaste auf die Projektverweise klicken und in Visual Studio „NuGet-Pakete verwalten...“ auswählen.

Nachdem NuGet die neuen Pakete und deren Abhängigkeiten heruntergeladen hat, erstellen Sie Ihr Projekt neu. Je nachdem, wie Ihr Code strukturiert ist, verläuft die Neuerstellung erfolgreich. Wenn dies der Fall ist, sind Sie startbereit!

Wenn beim Erstellen ein Fehler auftritt, wird etwa folgende Buildfehlermeldung angezeigt:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

Der nächste Schritt besteht darin, diesen Buildfehler zu beheben. Unter [Wichtige Änderungen in Version 3](#ListOfChanges) finden Sie Informationen zur Fehlerursache und -behebung.

Möglicherweise werden zusätzliche Buildwarnungen im Zusammenhang mit veralteten Methoden oder Eigenschaften angezeigt. Die Warnung enthält Anweisungen dazu, was Sie anstelle der veralteten Funktion verwenden sollten. Wenn Ihre Anwendung z.B. die `IndexingParameters.Base64EncodeKeys`-Eigenschaft verwendet, sollten Sie folgende Warnung erhalten: `"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

Sobald Sie alle Buildfehler behoben haben, können Sie Änderungen an Ihrer Anwendung vornehmen, um nach Bedarf die neue Funktionalität zu nutzen. Neue Funktionen im SDK werden ausführlich unter [Neuerungen in Version 3](#WhatsNew) behandelt.

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>Wichtige Änderungen in Version 3
Version 3 enthält nur einige wenige wichtige Änderungen, die neben der Neuerstellung der Anwendung ggf. auch Codeänderungen erfordern.

### <a name="indexesgetclient-return-type"></a>Rückgabetyp Indexes.GetClient
Die `Indexes.GetClient` -Methode hat einen neuen Rückgabetyp. Zuvor wurde `SearchIndexClient` zurückgegeben, dies wurde in Version 2.0-preview jedoch in `ISearchIndexClient` geändert, und diese Änderung wird auch in Version 3 übernommen. Dies dient zur Unterstützung von Kunden, die die `GetClient`-Methode für Komponententests durch Rückgabe einer simulierten Implementierung von `ISearchIndexClient` simulieren möchten.

#### <a name="example"></a>Beispiel
Angenommen, Sie verwenden folgenden Code:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Zum Beheben von Buildfehlern können Sie diesen Code folgendermaßen ändern:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>„AnalyzerName“, „DataType“ und andere können nicht mehr implizit in Zeichenfolgen konvertiert werden.
Viele Typen im Azure Search .NET SDK werden von `ExtensibleEnum` abgeleitet. Zuvor konnten diese Typen alle implizit in den Typ `string` konvertiert werden. In der `Object.Equals`-Implementierung für diese Klassen wurde allerdings ein Fehler entdeckt, dessen Behebung die Deaktivierung dieser impliziten Konvertierung erforderlich machte. Die explizite Konvertierung in `string` ist jedoch weiterhin zulässig.

#### <a name="example"></a>Beispiel
Angenommen, Sie verwenden folgenden Code:

```csharp
var customTokenizerName = TokenizerName.Create("my_tokenizer"); 
var customTokenFilterName = TokenFilterName.Create("my_tokenfilter"); 
var customCharFilterName = CharFilterName.Create("my_charfilter"); 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        customTokenizerName,  
        new[] { customTokenFilterName },  
        new[] { customCharFilterName }), 
}; 
```

Zum Beheben von Buildfehlern können Sie diesen Code folgendermaßen ändern:

```csharp
const string CustomTokenizerName = "my_tokenizer"; 
const string CustomTokenFilterName = "my_tokenfilter"; 
const string CustomCharFilterName = "my_charfilter"; 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        CustomTokenizerName,  
        new TokenFilterName[] { CustomTokenFilterName },  
        new CharFilterName[] { CustomCharFilterName })
}; 
```

### <a name="removed-obsolete-members"></a>Veraltete Member wurden entfernt.

Unter Umständen treten Buildfehler im Zusammenhang mit Methoden oder Eigenschaften auf, die in Version 2.0-preview als veraltet markiert waren und nun in Version 3 entfernt wurden. Solche Fehler lassen sich wie folgt beheben:

- Falls Sie den Konstruktor `ScoringParameter(string name, string value)` verwendet haben, verwenden Sie stattdessen Folgendes: `ScoringParameter(string name, IEnumerable<string> values)`.
- Falls Sie die `ScoringParameter.Value`-Eigenschaft verwendet haben, verwenden Sie stattdessen die `ScoringParameter.Values`-Eigenschaft oder die `ToString`-Methode.
- Falls Sie die `SearchRequestOptions.RequestId`-Eigenschaft verwendet haben, verwenden Sie stattdessen die `ClientRequestId`-Eigenschaft.

### <a name="removed-preview-features"></a>Entfernte Vorschaufeatures

Hinweis für Benutzer, die ein Upgrade von Version 2.0-preview auf Version 3 durchführen: Die JSON- und CSV-Analyseunterstützung für Blob-Indexer wurde entfernt, da sich dieses Feature noch in der Vorschauphase befindet. Im Einzelnen wurden folgende Methoden der `IndexingParametersExtensions`-Klasse entfernt:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Falls bei Ihrer Anwendung eine harte Abhängigkeit von diesen Features besteht, können Sie kein Upgrade auf Version 3 des Azure Search .NET SDK durchführen. Sie können weiterhin Version 2.0-preview verwenden. Beachten Sie jedoch, dass die Verwendung von Vorschau-SDKs in Produktionsanwendungen **nicht empfehlenswert** ist. Vorschaufeatures dienen ausschließlich zur Evaluierung und ändern sich unter Umständen.

## <a name="conclusion"></a>Zusammenfassung
Weitere Informationen zur Verwendung des Azure Search-.NET SDK finden Sie unter [Hilfe und Anleitung zu .NET](search-howto-dotnet-sdk.md).

Wir freuen uns auf Ihr Feedback zum SDK! Wenn Probleme auftreten, können Sie sich gerne über [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search) an uns wenden. Wenn Sie einen Fehler finden, können Sie das Problem im [Azure .NET SDK-GitHub-Repository](https://github.com/Azure/azure-sdk-for-net/issues)melden. Stellen Sie sicher, dass Sie dem Titel des Problems „[Azure Search]“ voranstellen.

Vielen Dank für die Nutzung von Azure Search!
