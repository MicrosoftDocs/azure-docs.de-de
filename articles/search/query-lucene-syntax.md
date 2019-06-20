---
title: 'Lucene-Abfragesyntax: Azure Search'
description: Referenz für die vollständige Lucene-Syntax, die mit Azure Search verwendet wird.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/13/2019
author: brjohnstmsft
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
ms.openlocfilehash: 26935b53d8f852289513a5a7b5d31e3befe3e3b2
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002247"
---
# <a name="lucene-query-syntax-in-azure-search"></a>Lucene-Abfragesyntax in Azure Search
Sie können für Azure Search basierend auf der umfassenden Syntax des [Lucene-Abfrageparsers](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) spezielle Abfragen schreiben: Platzhaltersuche, Fuzzysuche, NEAR-Suche und Suche mit regulären Ausdrücken sind einige Beispiele hierfür. Der Großteil der Syntax des Lucene-Abfrageparsers wird [in Azure Search unverändert implementiert](search-lucene-query-architecture.md). Die einzige Ausnahme sind *Bereichssuchen*, die in Azure Search mit `$filter`-Ausdrücken erstellt werden. 

## <a name="how-to-invoke-full-parsing"></a>Aufrufen der vollständigen Analyse

Legen Sie den Suchparameter `queryType` fest, um den gewünschten Parser anzugeben. Gültige Werte sind `simple|full` (der Standardwert ist `simple`) und `full` für Lucene. 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>Beispiel für die vollständige Syntax

Im folgenden Beispiel wird mithilfe der Lucene-Abfragesyntax (dies wird im Parameter `queryType=full` deutlich) eine Suche nach Dokumenten im Index durchgeführt. Bei dieser Abfrage werden Hotels zurückgegeben, bei denen das Kategoriefeld den Begriff „budget“ enthält und alle durchsuchbaren Felder die Wörter „recently renovated“ enthalten. Dokumenten mit dem Ausdruck „recently renovated“ wird aufgrund des Werts für die Begriffsverstärkung (3) ein höherer Rang zugewiesen.  

Der Parameter `searchMode=all` ist in diesem Beispiel wichtig. Bei Abfragen mit Operatoren sollten Sie generell `searchMode=all` festlegen, um sicherzustellen, dass *alle* Kriterien abgeglichen werden.

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2019-05-06&querytype=full
```

 Alternativ können Sie POST verwenden:  

```
POST /indexes/hotels/docs/search?api-version=2019-05-06
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

Weitere Beispiele finden Sie unter [Beispiele für die Lucene-Abfragesyntax zum Erstellen von Abfragen in Azure Search](search-query-lucene-examples.md). Ausführliche Informationen zur Angabe sämtlicher Abfrageparameter finden Sie unter [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Durchsuchen von Dokumenten (REST-API für den Azure Search-Dienst)).

> [!NOTE]  
>  Azure Search unterstützt darüber hinaus die [einfache Abfragesyntax](query-simple-syntax.md), eine unkomplizierte und stabile Abfragesprache, die für die einfache Schlüsselwortsuche verwendet werden kann.  

##  <a name="bkmk_syntax"></a> Grundlagen der Syntax  
 Die folgenden Syntaxgrundlagen gelten für alle Abfragen mit der Lucene-Syntax.  

### <a name="operator-evaluation-in-context"></a>Operatorauswertung im Kontext

Die Platzierung bestimmt, ob ein Symbol als Operator oder einfach als ein weiteres Zeichen in einer Zeichenfolge interpretiert wird.

In der vollständigen Lucene-Syntax wird das Tildezeichen (~) beispielsweise sowohl für die Fuzzysuche als auch die NEAR-Suche verwendet. Wenn das Tildezeichen (~) nach einem Ausdruck in Anführungszeichen steht, ruft es die NEAR-Suche auf. Steht es am Ende eines Begriffs, ruft es die Fuzzysuche auf.

In einem Begriff (z. B. „Business~Analyst“) wird das Zeichen nicht als Operator interpretiert. Sofern es sich um eine Begriffs- oder Ausdrucksabfrage handelt, wird in diesem Fall bei der [Volltextsuche](search-lucene-query-architecture.md) mit [lexikalischer Analyse](search-lucene-query-architecture.md#stage-2-lexical-analysis) das „~“ entfernt, und der Begriff „Business~Analyst“ wird in zwei Begriffe aufgeteilt: „Business“ ODER „Analyst“.

Im obigen Beispiel geht es um das Tildezeichen (~), das gleiche Prinzip gilt jedoch für alle Operatoren.

### <a name="escaping-special-characters"></a>Verwenden von Escapezeichen für Sonderzeichen

 Sonderzeichen müssen mit Escapezeichen versehen werden, damit sie als Teil des Suchtexts interpretiert werden. Als Escapezeichen können Sie Sonderzeichen den umgekehrten Schrägstrich (\\) voranstellen. Die folgenden Sonderzeichen müssen mit Escapezeichen versehen werden:  
`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /`  

 Verwenden Sie beispielsweise \\\*, um ein Platzhalterzeichen mit Escapezeichen zu versehen.

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Codierung von unsicheren und reservierten Zeichen in URLs

 Stellen Sie sicher, dass alle unsicheren und reservierten Zeichen in einer URL codiert werden. „#“ ist beispielsweise ein Fragment-/Ankerbezeichner in einer URL und daher ein unsicheres Zeichen. Bei der Verwendung in einer URL muss das Zeichen in `%23` codiert werden. „&“ und „=“ sind Beispiele für reservierte Zeichen, da sie in Azure Search zum Trennen von Parametern und Angeben von Werten dienen. Weitere Informationen finden Sie unter [RFC1738: Uniform Resource Locator (URL)](https://www.ietf.org/rfc/rfc1738.txt).

 Unsichere Zeichen sind ``" ` < > # % { } | \ ^ ~ [ ]``. Reservierte Zeichen sind `; / ? : @ = + &`.

### <a name="precedence-operators-grouping-and-field-grouping"></a>Rangfolgenoperatoren: Gruppierung und Feldgruppierung  
 Sie können mithilfe von Klammern Unterabfragen erstellen, die Operatoren innerhalb der Anweisung in Klammern enthalten. Beispielsweise sucht `motel+(wifi||luxury)` nach Dokumenten, die den Begriff „motel“ und entweder „wifi“ oder „luxury“ (oder beides) enthalten.

Die Feldgruppierung funktioniert ähnlich, beschränkt die Gruppierung jedoch auf ein einzelnes Feld. Beispielsweise durchsucht `hotelAmenities:(gym+(wifi||pool))` das Feld „hotelAmenities“ nach „gym“ und „wifi“ oder nach „gym“ und „pool“.  

### <a name="searchmode-parameter-considerations"></a>Überlegungen zu SearchMode-Parametern  
 Die in [Simple query syntax in Azure Search](query-simple-syntax.md) (Einfache Abfragesyntax in Azure Search) beschriebene Auswirkung des `searchMode` auf Abfragen gilt auch für die Lucene-Abfragesyntax. Insbesondere kann die Verwendung von `searchMode` in Verbindung mit NOT-Operatoren zu Abfrageergebnissen führen, die ungewöhnlich erscheinen, wenn Sie die Auswirkungen des Parameters nicht verstanden haben. Wenn Sie den Standardwert `searchMode=any` beibehalten und einen NOT-Operator verwenden, wird der Vorgang als OR-Aktion berechnet, sodass „"New York" NOT "Seattle"“ alle Städte zurückgibt, die nicht Seattle sind.  

##  <a name="bkmk_boolean"></a> Boolesche Operatoren (AND, OR, NOT) 
 Geben Sie boolesche Operatoren in Textform (AND, OR, NOT) immer in Großbuchstaben an.  

### <a name="or-operator-or-or-"></a>OR-Operator `OR` oder `||`

Der OR-Operator ist ein vertikaler Balken bzw. ein senkrechter Strich. Beispiel: `wifi || luxury` sucht nach Dokumenten, die „wifi“ oder „luxury“ oder beides enthalten. Da OR der standardmäßige Konjunktionsoperator ist, könnten Sie ihn auch weglassen, d. h. `wifi luxury` entspricht `wifi || luxuery`.

### <a name="and-operator-and--or-"></a>AND-Operator `AND`, `&&` oder `+`

Der AND-Operator ist ein kaufmännisches Und-Zeichen oder ein Pluszeichen. Beispiel: `wifi && luxury` sucht nach Dokumenten, die sowohl „wifi“ als auch „luxury“ enthalten. Das Pluszeichen (+) wird für erforderliche Begriffe verwendet. Beispielsweise legt `+wifi +luxury` fest, dass beide Begriffe im Feld eines einzelnen Dokuments vorkommen müssen.


### <a name="not-operator-not--or--"></a>NOT-Operator `NOT`, `!` oder `-`

Der NOT-Operator ist ein Ausrufezeichen oder ein Minuszeichen. Beispiel: `wifi !luxury` sucht nach Dokumenten, die den Begriff „wifi“ enthalten und/oder den Begriff „luxury“ nicht enthalten. Die Option `searchMode` steuert, ob ein Begriff mit dem NOT-Operator mit den anderen Begriffen in der Abfrage per AND oder OR verknüpft wird, wenn kein Operator „+“ oder „||“ angegeben ist. Zur Erinnerung: `searchMode` kann auf `any` (Standardwert) oder `all` festgelegt werden.

`searchMode=any` erhöht die Trefferquote von Abfragen, da mehr Ergebnisse einbezogen werden, und wird standardmäßig als „OR NOT“ interpretiert. Beispielsweise findet `wifi -luxury` Dokumente, die entweder den Begriff *wifi* enthalten oder den Begriff *luxury* nicht enthalten.

`searchMode=all` erhöht die Genauigkeit der Abfragen, da weniger Ergebnisse einbezogen werden, und wird standardmäßig als „AND NOT“ interpretiert. Beispielsweise findet `wifi -luxury` Dokumente, die den Begriff `wifi` enthalten und den Begriff `luxury` nicht enthalten. Dies ist wohl ein intuitiveres Verhalten für den „-“-Operator. Wenn Sie nicht die Trefferquote, sondern die Genauigkeit der Suche optimieren möchten *und* Ihre Benutzer häufig den `-`-Operator bei der Suche verwenden, sollten Sie daher die Verwendung von `searchMode=all` anstelle von `searchMode=any` in Betracht ziehen.

##  <a name="bkmk_querysizelimits"></a> Einschränkungen der Abfragegröße  
 Für die Größe der Abfragen, die Sie an Azure Search senden können, gilt ein Grenzwert. Insbesondere können Sie maximal 1.024 Klauseln (durch AND, OR usw. getrennte Ausdrücke) verwenden. Für die Größe der einzelnen Begriffe in einer Abfrage gilt zudem ein Grenzwert von ungefähr 32 KB. Wenn Ihre Anwendung programmgesteuert Suchabfragen generiert, sollten Sie durch den Anwendungsentwurf sicherstellen, dass sie keine Abfragen unbegrenzter Größe erzeugt.  

##  <a name="bkmk_searchscoreforwildcardandregexqueries"></a> Bewerten von Platzhalterabfragen und Abfragen mit regulären Ausdrücken
 Azure Search verwendet die häufigkeitsbasierte Bewertung ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) für Textabfragen. Für Platzhalterabfragen und Abfragen mit regulären Ausdrücken, bei denen die Anzahl von Begriffen groß sein kann, wird der Häufigkeitsfaktor jedoch ignoriert. Dadurch wird verhindert, dass Übereinstimmungen für seltenere Begriffe bei der Rangzuweisung bevorzugt behandelt werden. Alle Übereinstimmungen werden bei Platzhalterabfragen und Abfragen mit regulären Ausdrücken gleich behandelt.

##  <a name="bkmk_fields"></a>Feldbezogene Suche  
Sie können einen feldbezogenen Suchvorgang mit der `fieldName:searchExpression`-Syntax definieren, wobei es sich bei dem Suchausdruck um ein einzelnes Wort, einen einfachen Ausdruck oder einen komplexeren Ausdruck in Klammern handeln kann, optional mit booleschen Operatoren. Beispiele hierfür sind:  

- genre:jazz NOT history  

- artists:("Miles Davis" "John Coltrane")

Achten Sie darauf, dass Sie mehrere Zeichenfolgen in Anführungszeichen setzen, wenn beide Zeichenfolgen als einzelne Entität ausgewertet werden sollen (in diesem Fall die Suche nach zwei verschiedenen Künstlern im Feld `artists`).  

Das in `fieldName:searchExpression` angegebene Feld muss ein Feld vom Typ `searchable` sein.  Einzelheiten zur Verwendung von Indexattributen in Felddefinitionen finden Sie unter [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) (Erstellen eines Index).  

> [!NOTE]
> Bei der Verwendung von feldbezogenen Suchausdrücken brauchen Sie den Parameter `searchFields` nicht zu verwenden, da in jedem feldbezogenen Suchausdruck explizit ein Feldname angegeben ist. Allerdings können Sie den Parameter `searchFields` trotzdem verwenden, wenn Sie eine Abfrage ausführen möchten, bei der einige Teile auf ein bestimmtes Feld beschränkt sind, der Rest sich jedoch auf mehrere Felder beziehen kann. Zum Beispiel würde `jazz` in der Abfrage `search=genre:jazz NOT history&searchFields=description` nur mit dem Feld `genre`, und `NOT history` mit dem Feld `description` abgeglichen werden. Der in `fieldName:searchExpression` angegebene Feldname hat immer Vorrang vor dem Parameter `searchFields`, weshalb `genre` in diesem Beispiel nicht in den Parameter `searchFields` aufgenommen werden muss.

##  <a name="bkmk_fuzzy"></a> Fuzzysuche  
 Bei einer Fuzzysuche werden Übereinstimmungen in Ausdrücken gefunden, die ähnlich aufgebaut sind. Laut [Lucene-Dokumentation](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) basiert die Fuzzysuche auf der [Damerau-Levenshtein-Distanz](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance). Durch eine Fuzzysuche kann ein Begriff auf bis zu 50 Begriffe erweitert werden, die den Distanzkriterien entsprechen. 

 Verwenden Sie für eine Fuzzysuche das Tildezeichen „~“ am Ende eines einzelnen Worts mit einem optionalen Parameter, einer Zahl zwischen 0 und 2 (Standardwert), der die Edit-Distanz angibt. Beispielsweise würden bei „blue~“ oder „blue~1“ die Werte „blue“, „blues“ und „glue“ zurückgegeben.

 Die Fuzzysuche kann nur auf Begriffe, nicht auf Ausdrücke, angewendet werden. Sie können aber die Tilde an jeden Begriff in einem mehrteiligen Namen oder Ausdruck einzeln anfügen. So würde beispielsweise „Unviersty~ of~ „Wshington~“ mit „University of Washington“ übereinstimmen.
 

##  <a name="bkmk_proximity"></a> NEAR-Suche  
 NEAR-Suchen werden verwendet, um Begriffe zu suchen, die in einem Dokument nahe beieinander liegen. Fügen Sie ein Tildesymbol „~“ Symbol am Ende eines Ausdrucks ein, gefolgt von der Anzahl der Wörter, die den NEAR-Bereich bilden. Beispielsweise finden Sie mit der Abfrage `"hotel airport"~5` die Begriffe „hotel“ und „airport“, wenn sie in einem Abstand von fünf Wörtern voneinander in einem Dokument vorkommen.  


##  <a name="bkmk_termboost"></a> Begriffsverstärkung  
 Die Begriffsverstärkung (Term Boosting) bezieht sich auf das Höherbewerten eines Dokuments, wenn es den verstärkten Begriff enthält, im Verhältnis zu Dokumenten, die den Begriff nicht enthalten. Dies unterscheidet sich insofern von Bewertungsprofilen, als dass bei Bewertungsprofilen bestimmte Felder statt bestimmter Begriffe verstärkt werden.  

Im folgenden Beispiel werden die Unterschiede veranschaulicht. Angenommen, Sie haben ein Bewertungsprofil, das Übereinstimmungen in einem bestimmten Feld verstärkt, beispielsweise *genre* im [musicstoreindex-Beispiel](index-add-scoring-profiles.md#bkmk_ex). Mit der Begriffsverstärkung könnten Sie bestimmte Suchbegriffe noch höher bewerten als andere. Mit `rock^2 electronic` werden beispielsweise Dokumente, die die Suchbegriffe im Feld „genre“ enthalten, höher eingestuft als andere durchsuchbare Felder im Index. Darüber hinaus wird Dokumenten, die den Suchbegriff *rock* enthalten, aufgrund des Werts für die Begriffsverstärkung (2) ein höherer Rang zugewiesen als Dokumenten mit dem anderen Suchbegriff *electronic*.  

 Verwenden Sie zum Verstärken eines Begriffs das Caretzeichen „^“ mit einem Verstärkungsfaktor (einer Zahl) am Ende des Begriffs, nach dem Sie suchen. Sie können auch Ausdrücke verstärken. Je höher der Verstärkungsfaktor, desto relevanter wird der Begriff im Verhältnis zu anderen Suchbegriffen. Der Standardverstärkungsfaktor ist 1. Der Verstärkungsfaktor muss positiv sein, kann aber kleiner als 1 sein (z. B. 0.20).  

##  <a name="bkmk_regex"></a> Suche mit regulären Ausdrücken  
 Bei einer Suche mit regulärem Ausdruck werden Übereinstimmungen basierend auf dem Inhalt zwischen Schrägstrichen „/“ gefunden, wie in der [RegExp-Klasse](https://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html)dokumentiert.  

 Geben Sie beispielsweise `/[mh]otel/` an, um nach Dokumenten zu suchen, die das Wort „motel“ oder „hotel“ enthalten.  Suchen mit regulären Ausdrücken werden mit einzelnen Wörtern abgeglichen.   

##  <a name="bkmk_wildcard"></a> Platzhaltersuche  
 Sie können die allgemein bekannte Syntax für die Platzhaltersuche nach mehreren (*) oder einzelnen (?) Zeichen verwenden. Beachten Sie, dass der Lucene-Abfrageparser die Verwendung dieser Symbole bei einem einzelnen Begriff, nicht bei einem Ausdruck, unterstützt.  

 Geben Sie beispielsweise „note*“ an, um nach Dokumenten zu suchen, die Wörter mit der Vorsilbe „note“ (z. B. „Notebook“ oder „Notepad“) enthalten.  

> [!NOTE]  
>  Sie können die Symbole * oder ? nicht als erstes Zeichen in einer Suche verwenden.  
>  Bei Platzhaltersuchabfragen wird keine Textanalyse durchgeführt. Zur Abfragezeit werden Begriffe der Platzhalterabfrage mit analysierten Begriffen im Suchindex verglichen und erweitert.

## <a name="see-also"></a>Weitere Informationen  

+ [Dokumente durchsuchen](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [OData expression syntax for filters and sorting](query-odata-filter-orderby-syntax.md) (OData-Ausdrucksfilter für Filter und die Sortierung)   
+ [Simple query syntax in Azure Search](query-simple-syntax.md) (Einfache Abfragesyntax in Azure Search)   
