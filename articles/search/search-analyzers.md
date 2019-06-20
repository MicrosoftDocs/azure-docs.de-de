---
title: Analysetools für linguistische Analyse und Textverarbeitung – Azure Search
description: Weisen Sie durchsuchbaren Textfeldern in einem Index Analysen zu, um die Lucene-Standardanalyse durch benutzerdefinierte, vordefinierte oder sprachspezifische Alternativen zu ersetzen.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: heidist
manager: cgronlun
author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: f76d944f614f07a4428d4e4100f6a08a375d96dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65795794"
---
# <a name="analyzers-for-text-processing-in-azure-search"></a>Analysetools für Textverarbeitung in Azure Search

Ein *Analysetool* ist eine Komponente der [Volltext-Suchmaschine](search-lucene-query-architecture.md), mit der Text in Abfragezeichenfolgen und indizierten Dokumenten verarbeitet wird. Verschiedene Analysetools bearbeiten Text je nach Szenario auf verschiedene Arten. Sprachanalysetools verarbeiten Text mit linguistischen Regeln, um die Suchqualität zu verbessern, während andere Analysetools grundlegendere Aufgaben wie z.B. das Konvertieren von Zeichen in Kleinschreibung ausführen. 

Sprachanalysetools sind die am häufigsten verwendeten, und jedem durchsuchbaren Feld in einem Azure Search-Index ist ein Standard-Sprachanalysetool zugewiesen. Die folgenden Sprachtransformationen sind während der Textanalyse typisch:

+ Nicht unbedingt benötigte Wörter (Stoppwörter) und Satzzeichen werden entfernt.
+ Wörter mit Bindestrichen und Ausdrücke werden in Einzelwörter unterteilt.
+ Wörter in Großbuchstaben werden in Kleinbuchstaben umgewandelt.
+ Wörter werden auf ihre jeweilige Stammform reduziert, sodass unabhängig von der Zeitform eine Übereinstimmung gefunden werden kann.

Linguistische Analysetools konvertieren eine Texteingabe in primitive oder Stammformulare, die beim Speichern und Abrufen von Informationen effizient sind. Die Konvertierung erfolgt während der Indizierung, wenn der Index erstellt wird, und dann erneut bei der Suche, wenn der Index gelesen wird. Sie erhalten die erwarteten Suchergebnisse eher, wenn Sie für beide Vorgänge dieselben Textanalysetools verwenden.

## <a name="default-analyzer"></a>Standardanalysemodul  

Azure Search verwendet standardmäßig das [Standardanalysetool von Apache Lucene (Standard-Lucene)](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html). Dieses unterteilt Text gemäß den Regeln der [Unicode-Textsegmentierung](https://unicode.org/reports/tr29/) in einzelne Elemente. Darüber hinaus konvertiert das Standardanalyseprogramm alle Zeichen in Kleinbuchstaben. Während der Indizierung und der Abfrageverarbeitung durchlaufen sowohl indizierte Dokumente als auch Suchbegriffe die Analyse.  

Es wird automatisch auf jedes durchsuchbare Feld verwendet. Dieser Standard kann feldspezifisch überschrieben werden. Alternative Analysetools können ein [Sprachanalysetool](index-add-language-analyzers.md), ein [benutzerdefiniertes Analysetool](index-add-custom-analyzers.md) oder ein vordefiniertes Analysetool aus der [Liste der verfügbaren Analysetools](index-add-custom-analyzers.md#AnalyzerTable) sein.


## <a name="types-of-analyzers"></a>Typen von Analysetools

Die folgende Liste gibt Aufschluss darüber, welche Analysetools in Azure Search unterstützt werden.

| Category (Kategorie) | BESCHREIBUNG |
|----------|-------------|
| [Lucene-Standardanalyse](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Standard. Muss nicht angegeben oder konfiguriert werden. Diese allgemeine Analyse eignet sich für die meisten Sprachen und Szenarien.|
| Vordefinierte Analysen | Werden als fertiges Produkt angeboten und in der Regel unverändert verwendet. <br/>Es gibt spezialisierte und sprachspezifische Typen. Sie sind „vordefiniert“, da Sie ihren Namen angeben und sie nicht weiter konfigurieren oder anpassen. <br/><br/>[Spezialisierte (sprachunabhängige) Analysetools](index-add-custom-analyzers.md#AnalyzerTable) werden verwendet, wenn Texteingaben eine spezielle oder minimale Verarbeitung erfordern. Zu sprachunabhängigen vordefinierten Analysen zählen **Asciifolding**, **Keyword**, **Pattern**, **Simple**, **Stop** und **Whitespace**.<br/><br/>[Sprachanalyzer](index-add-language-analyzers.md) werden verwendet, wenn umfassende linguistische Unterstützung für einzelne Sprachen benötigt werden. Azure Search unterstützt 35 Lucene-Sprachanalysen und 50 Microsoft-Analysen für die Verarbeitung natürlicher Sprache. |
|[Benutzerdefinierte Analysen](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Eine benutzerdefinierte Konfiguration einer Kombination vorhandener Elemente, bestehend aus einem Tokenizer (erforderlich) und optionalen Filtern („char“ oder „token“).|

Ein paar vordefinierte Analysetools, wie z.B. **Pattern** oder **Stop**, unterstützen eine begrenzte Anzahl von Konfigurationsoptionen. Um diese Optionen festzulegen, erstellen Sie ein benutzerdefiniertes Analysetool, bestehend aus dem vordefinierten Analysetool und einer der unter [Vordefinierte Analysetoolreferenz](index-add-custom-analyzers.md#AnalyzerTable) dokumentierten alternativen Optionen. Versehen Sie Ihre neue Konfiguration wie üblich mit einem Namen (etwa *myPatternAnalyzer*), damit sie von der Lucene-Musteranalyse zu unterscheiden ist.

## <a name="how-to-specify-analyzers"></a>Angeben von Analyzern

1. Erstellen Sie für benutzerdefinierte Analysetools in der Indexdefinition einen Abschnitt namens **analyzer**. Weitere Informationen finden Sie unter [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) (Index erstellen) sowie unter [Add a custom analyzer](index-add-custom-analyzers.md) (Benutzerdefiniertes Analysetool hinzufügen).

2. Legen Sie für eine [Felddefinition](https://docs.microsoft.com/rest/api/searchservice/create-index) im Index die **analyzer**-Eigenschaft auf den Namen eines Zielanalysetools fest, z.B. `"analyzer" = "keyword"`. Hierbei können Sie den Namen eines vordefinierten Analyzers, Sprachanalyzers oder benutzerdefinierte Analyzers verwenden, der auch im Indexschema definiert wurde. Planen Sie das Zuweisen des Analysetools in der Indexdefinitionsphase, bevor der Index im Dienst erstellt wird.

3. Anstelle einer einzelnen Eigenschaft des Typs **analyzer** können Sie optional mithilfe der Feldparameter **indexAnalyzer** und **searchAnalyzer** auch verschiedene Analysetools für Indizierung und Abfragen festlegen. Sie verwenden verschiedene Analysetools für Datenvorbereitung und -abruf, wenn eine dieser Aktivitäten eine bestimmte Transformation erfordert, die die andere nicht benötigt.

Das Zuweisen von **analyzer** oder **indexAnalyzer** zu einem Feld, das physisch bereits erstellt wurde, ist nicht zulässig. Wenn etwas unklar ist, können Sie der Aufschlüsselung in der folgenden Tabelle entnehmen, welche Aktionen aus welchen Gründen das erneute Erstellen erfordern.
 
 | Szenario | Auswirkung | Schritte |
 |----------|--------|-------|
 | Hinzufügen eines neuen Felds | Minimal | Wenn das Feld im Schema noch nicht vorhanden ist, muss keine Feldüberarbeitung vorgenommen werden, da das Feld in Ihrem Index noch nicht physisch vorhanden ist. Sie können mit [Update Index](https://docs.microsoft.com/rest/api/searchservice/update-index) (Index aktualisieren) einem vorhandenen Index ein neues Feld hinzufügen und es mit [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) auffüllen.|
 | Fügen Sie einen **analyzer** oder **indexAnalyzer** einem vorhandenen indizierten Feld hinzu. | [rebuild](search-howto-reindex.md) | Der invertierte Index für dieses Feld muss von Grund auf neu erstellt werden, und der Inhalt für diese Felder muss neu indiziert werden. <br/> <br/>Für Indizes in der aktiven Entwicklung [löschen](https://docs.microsoft.com/rest/api/searchservice/delete-index) und [erstellen](https://docs.microsoft.com/rest/api/searchservice/create-index) Sie den Index, um die neue Felddefinition zu übernehmen. <br/> <br/>Für Indizes in der Produktion können Sie ein erneutes Erstellen aufschieben, indem Sie ein neues Feld erstellen, um die überarbeitete Definition anzugeben und anstelle der alten zu verwenden. Integrieren Sie das neue Feld mit [Update Index](https://docs.microsoft.com/rest/api/searchservice/update-index) (Index aktualisieren), und füllen Sie es mit [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents). Später können Sie den Index als Bestandteil der geplanten Indexwartung bereinigen, um veraltete Felder zu entfernen. |

## <a name="when-to-add-analyzers"></a>Gründe zum Hinzufügen von Analysetools

Der beste Zeitpunkt zum Hinzufügen und Zuweisen von Analysetools ist während der aktiven Entwicklung, wenn das Löschen und Neuerstellen von Indizes Routine ist.

Wenn eine Indexdefinition festgelegt wird, können Sie einem Index neue Analysekonstrukte anfügen, aber Sie müssen das **allowIndexDowntime**-Flag an [Update Index](https://docs.microsoft.com/rest/api/searchservice/update-index) (Index aktualisieren) übergeben, wenn Sie diesen Fehler vermeiden möchten:

*„Indexupdate ist nicht zulässig, weil es eine Downtime verursachen würde. Um einem vorhandenen Index neue Analysetools, Tokenizer, Tokenfilter oder Zeichenfilter hinzuzufügen, legen Sie für den Abfrageparameter „allowIndexDowntime“ in der Indexupdateanforderung „True“ fest. Beachten Sie, dass dieser Vorgang Ihren Index für mindestens ein paar Sekunden offline schaltet, sodass Indizierungs- und Abfrageanforderungen nicht gelingen. Leistung und Schreibverfügbarkeit des Indexes können nach der Indexaktualisierung mehrere Minuten lang eingeschränkt sein, bei sehr großen Indizes auch länger.“*

Gleiches gilt beim Zuweisen eines Analysetools zu einem Feld. Ein Analysetool ist ein wesentlicher Bestandteil der Felddefinition, sodass Sie es nur hinzufügen können, wenn das Feld erstellt wird. Wenn Sie Analysetools vorhandenen Feldern hinzufügen möchten, müssen Sie den Index [löschen und neu erstellen](search-howto-reindex.md) oder ein neues Feld mit dem gewünschten Analysetool hinzufügen.

Wie bereits erwähnt, ist die **searchAnalyzer**-Variante eine Ausnahme. Von den drei Methoden zum Angeben von Analysetools (**analyzer**, **indexAnalyzer**, **searchAnalyzer**) kann nur das **searchAnalyzer**-Attribut bei einem vorhandenen Feld geändert werden.

## <a name="recommendations-for-working-with-analyzers"></a>Empfehlungen zum Arbeiten mit Analysetools

Dieser Abschnitt enthält Tipps zur Verwendung von Analyzern.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Eine einzelne Analyse für Lese-/Schreibvorgänge, sofern keine besonderen Anforderungen gelten

In Azure Search können Sie verschiedene Analysetools für das Indizieren und Suchen über zusätzliche **indexAnalyzer**- und **searchAnalyzer**-Feldparameter angeben. Wenn keine Angabe vorhanden ist, wird das mit der **analyzer**-Eigenschaft festgelegte Analysetool sowohl für die Indizierung als auch für die Suche verwendet. Ohne Angabe von `analyzer` wird die Standardanalyse von Lucene verwendet.

Allgemeine Regel: Verwenden Sie für die Indizierung und für Abfragen die gleiche Analyse, es sei denn, es müssen besondere Anforderungen erfüllt werden. Führen Sie die Tests sorgfältig durch. Wenn sich die Textverarbeitung zur Such- und Indizierungszeit unterscheidet, besteht das Risiko der Nichtübereinstimmung zwischen Abfrageausdrücken und indizierten Ausdrücken, wenn die Konfigurationen des Analyzers für die Suche und die Indizierung nicht abgestimmt sind.

### <a name="test-during-active-development"></a>Testen während der aktiven Entwicklung

Wenn Sie die Standardanalyse überschreiben, muss der Index neu erstellt werden. Überlegen Sie sich möglichst während der aktiven Entwicklung, welche Analysen Sie verwenden möchten, bevor Sie einen Index in die Produktionsumgebung einbringen.

### <a name="inspect-tokenized-terms"></a>Überprüfen von tokenisierten Begriffen

Wenn eine Suche nicht die erwarteten Ergebnisse zurückgibt, ist das wahrscheinlichste Szenario, dass Abweichungen zwischen Begriffseingaben für die Abfrage und tokenisierten Begriffen im Index vorliegen. Wenn die Token nicht identisch sind, können Übereinstimmungen nicht materialisiert werden. Zur Überprüfung der Tokenizer-Ausgabe wird empfohlen, die [Analyse-API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) als Untersuchungstool zu verwenden. Die Antwort besteht aus Token, die von einem bestimmten Analyzer generiert wurden.

<a name="examples"></a>

## <a name="rest-examples"></a>REST-Beispiele

Die folgenden Beispiele zeigen Analysedefinitionen für einige gängige Szenarien.

+ [Beispiel eines benutzerdefinierten Analysetools](#Custom-analyzer-example)
+ [Beispiel für das Zuweisen eines Analysetools zu einem Feld](#Per-field-analyzer-assignment-example)
+ [Mischen von Analysetools für Indizierung und Suchvorgänge](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Beispiel für ein Sprachanalysetool](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>Beispiel eines benutzerdefinierten Analysetools

Dieses Beispiel zeigt eine Analysedefinition mit benutzerdefinierten Optionen. Benutzerdefinierte Optionen für char-Filter, Tokenizer und token-Filter werden separat als benannte Konstrukte angegeben, auf die dann in der Analysedefinition verwiesen wird. Vordefinierte Elemente werden unverändert verwendet und einfach über ihren Namen angegeben.

Das Beispiel im Detail:

* Analysen sind eine Eigenschaft der Feldklasse für ein durchsuchbares Feld.
* Eine benutzerdefinierte Analyse ist Teil einer Indexdefinition. Sie kann geringfügig (etwa durch Anpassung einer einzelnen Option in einem einzelnen Filter) oder an mehreren Stellen angepasst werden.
* In diesem Fall ist „my_analyzer“ die benutzerdefinierte Analyse, die wiederum den angepassten Standard-Tokenizer „my_standard_tokenizer“ und zwei token-Filter („lowercase“ und den angepassten asciifolding-Filter „my_asciifolding“) verwendet.
* Zudem werden 2 benutzerdefinierte char-Filter definiert: „map_dash“ und „remove_whitespace“. Der erste ersetzt alle Striche durch Unterstriche, der zweite entfernt alle Leerzeichen. Leerzeichen müssen in den Zuordnungsregeln UTF-8-kodiert sein. Die char-Filter werden vor der Tokenisierung angewendet und wirken sich auf die resultierenden Token aus (der Standardtokenisierer trennt bei Gedankenstrichen und Leerzeichen, nicht aber bei Unterstrichen).

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizerV2",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
~~~~

<a name="Per-field-analyzer-assignment-example"></a>

### <a name="per-field-analyzer-assignment-example"></a>Beispiel für die Zuweisung eines Analysetools je Feld

Die Standardanalyse ist die Standardeinstellung. Angenommen, Sie möchten die Standardeinstellung durch eine andere vordefinierte Analyse (beispielsweise „pattern“) ersetzen. Wenn Sie keine benutzerdefinierten Optionen festlegen, müssen Sie nur den Namen in der Felddefinition angeben.

Das Element „analyzer“ überschreibt die Standardanalyse für das jeweilige Feld. Es findet keine globale Überschreibung statt. In diesem Beispiel verwendet `text1` die Analyse „pattern“ und `text2` die Standardanalyse, da hierfür keine Analyse angegeben ist.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
~~~~

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Mischen von Analysetools für Indizierung und Suchvorgänge

Die APIs enthalten zusätzliche Indexattribute, die es ermöglichen, unterschiedliche Analyzer für Indizierung und Suchvorgänge anzugeben. **searchAnalyzer**- und **indexAnalyzer**-Attribut müssen als Paar angegeben werden und ersetzen das Einzelattribut **analyzer**.


~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
~~~~

<a name="Language-analyzer-example"></a>

### <a name="language-analyzer-example"></a>Beispiel für ein Sprachanalysetool

Für Felder mit Zeichenfolgen in unterschiedlichen Sprachen kann eine Sprachanalyse verwendet werden, während für andere Felder weiterhin die Standardanalyse (oder eine andere vordefinierte oder benutzerdefinierte Analyse) verwendet wird. Bei Verwendung einer Sprachanalyse muss sie sowohl für die Indizierung als auch für Suchvorgänge verwendet werden. Bei Feldern mit Sprachanalyse können für Indizierung und Suche keine unterschiedlichen Analysen verwendet werden.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
~~~~

## <a name="c-examples"></a>C#-Beispiele

Wenn Sie die.NET SDK-Codebeispiele verwenden, können Sie diese Beispiele anhängen, um Analysetools zu verwenden oder zu konfigurieren.

+ [Zuweisen eines integrierten Analysetools](#Assign-a-language-analyzer)
+ [Konfigurieren eines Analysetools](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Zuweisen eines Sprachanalysetools

Jedes in unveränderter Form ohne Konfiguration verwendete Analysetool wird in einer Felddefinition angegeben. Es gibt keine Anforderung für die Erstellung eines Analysetoolkonstrukts. 

In diesem Beispiel werden Beschreibungsfeldern Microsoft-Analysetools für Englisch und Französisch zugewiesen. Es ist ein Codeausschnitt aus einer größeren Definition des Hotelindex, der unter Verwendung der Hotelklasse in der Datei „hotels.cs“ des Beispiels [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) erstellt wird.

Rufen Sie das [Analysetool](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet) unter Angabe des Typs [AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) auf, mit dem ein in Azure Search unterstützten Textanalysetool bereitgestellt wird.

```csharp
    public partial class Hotel
    {
       . . . 

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        [JsonProperty("description")]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("description_fr")]
        public string DescriptionFr { get; set; }

      . . .
    }
```
<a name="Define-a-custom-analyzer"></a>

### <a name="define-a-custom-analyzer"></a>Definieren eines benutzerdefinierten Analysetools

Wenn eine Anpassung oder Konfiguration erforderlich ist, müssen Sie ein Analysetoolkonstrukt zu einem Index hinzufügen. Nach der Definition können Sie es der Felddefinition hinzufügen, wie im vorherigen Beispiel gezeigt.

Erstellen Sie ein [CustomAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet)-Objekt. Weitere Beispiele finden Sie unter [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/src/SDKs/Search/DataPlane/Search.Tests/Tests/CustomAnalyzerTests.cs).

```csharp
{
   var definition = new Index()
   {
         Name = "hotels",
         Fields = FieldBuilder.BuildForType<Hotel>(),
         Analyzers = new[]
            {
               new CustomAnalyzer()
               {
                     Name = "url-analyze",
                     Tokenizer = TokenizerName.UaxUrlEmail,
                     TokenFilters = new[] { TokenFilterName.Lowercase }
               }
            },
   };

   serviceClient.Indexes.Create(definition);
```

## <a name="next-steps"></a>Nächste Schritte

+ Lesen Sie unsere umfassende Erläuterung der [Funktionsweise der Volltextsuche in Azure Search](search-lucene-query-architecture.md). In diesem Artikel werden anhand von Beispielen Verhaltensweisen erklärt, die auf den ersten Blick vielleicht nicht intuitiv erscheinen.

+ Probieren Sie andere Abfragesyntax in den Beispielen unter [Search Documents](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) (Suchen nach Dokumenten) oder unter [Simple query syntax in Azure Search](query-simple-syntax.md) (Einfache Abfragesyntax in Azure Search) im Suchexplorer im Portal aus.

+ Informieren Sie sich darüber, wie Sie [sprachspezifische lexikalische Analysen](index-add-language-analyzers.md) anwenden.

+ [Konfigurieren Sie benutzerdefinierte Analysen](index-add-custom-analyzers.md) für eine minimale oder besondere Verarbeitung einzelner Felder.

## <a name="see-also"></a>Weitere Informationen

 [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) (Suchen nach Dokumenten (Azure Search Service-REST-API)) 

 [Einfache Abfragesyntax](query-simple-syntax.md) 

 [Vollständige Lucene-Abfragesyntax](query-lucene-syntax.md) 
 
 [Verarbeiten von Suchergebnissen](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
