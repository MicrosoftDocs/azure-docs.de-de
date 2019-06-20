---
title: Einfache Abfragesyntax – Azure Search
description: Referenz für die einfache Abfragesyntax, die für Volltextsuchanfragen in Azure Search verwendet wird.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
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
ms.openlocfilehash: 75e2d7c493b535c984b0ef61dd9a9fae53aee80a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65024198"
---
# <a name="simple-query-syntax-in-azure-search"></a>Einfache Abfragesyntax in Azure Search
Azure Search implementiert zwei Lucene-basierte Abfragesprachen: [Einfacher Abfrageparser](https://lucene.apache.org/core/4_7_0/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) und der [Lucene-Abfrageparser](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). In Azure Search schließt die einfache Abfragesyntax die Fuzzy-/Slop-Optionen aus.  

> [!NOTE]  
>  Für komplexere Abfragen bietet Azure Search eine alternative [Lucene-Abfragesyntax](query-lucene-syntax.md). Weitere Informationen über die Abfrageanalysearchitektur und die Vorteile der einzelnen Syntaxen finden Sie unter [Textsuche Funktionsweise der Volltextsuche in Azure Search](search-lucene-query-architecture.md).

## <a name="how-to-invoke-simple-parsing"></a>Aufrufen der einfachen Analyse

Die einfache Syntax ist die Standardeinstellung. Der Aufruf ist nur notwendig, wenn Sie die Syntax von vollständig auf einfach zurücksetzen. Um die Syntax explizit festzulegen, verwenden Sie den `queryType`-Suchparameter. Gültige Werte sind `simple|full`, mit `simple` als Standard und `full` für Lucene. 

## <a name="query-behavior-anomalies"></a>Anomalien beim Abfrageverhalten

Jeder Text mit einem oder mehreren Begriffen gilt als gültiger Ausgangspunkt für die Ausführung der Abfrage. Azure Search findet Dokumente, die einen oder alle der Begriffe enthalten, einschließlich aller Variationen, die bei der Analyse des Textes gefunden wurden. 

So einfach das klingt, es gibt einen Aspekt der Abfrageausführung in der Azure Search, der *möglicherweise* unerwartete Ergebnisse liefert, die die Suchergebnisse eher erhöhen als verringern, da mehr Begriffe und Operatoren zur Eingabezeichenkette hinzugefügt werden. Ob diese Erweiterung tatsächlich stattfindet, hängt von der Einbeziehung eines NOT-Operators ab, kombiniert mit einer `searchMode`-Parametereinstellung, die bestimmt, wie NOT in Form von AND- oder OR-Verhalten interpretiert wird. Mit dem Standard, `searchMode=Any`- und einem NOT-Operator wird die Operation als OR-Aktion berechnet, sodass `"New York" NOT Seattle` alle Städte zurückgibt, die nicht Seattle sind.  

Typischerweise ist es wahrscheinlicher, dass Sie dieses Verhalten in Benutzerinteraktionsmustern für Anwendungen sehen, die über Inhalte suchen, bei denen Benutzer eher einen Operator in eine Abfrage einbeziehen, als E-Commerce-Websites, die über mehr integrierte Navigationsstrukturen verfügen. Weitere Informationen finden Sie unter [NOT-Operator](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Boolesche Operatoren (AND, OR, NOT) 

Sie können Operatoren in eine Abfragezeichenfolge einbetten, um einen umfangreichen Satz von Kriterien zu erstellen, anhand derer passende Dokumente gefunden werden. 

### <a name="and-operator-"></a>AND-Operator `+`

Der AND-Operator ist ein Pluszeichen (+). Zum Beispiel sucht `wifi+luxury` nach Dokumenten, die sowohl `wifi` als auch `luxury` enthalten.

### <a name="or-operator-"></a>OR-Operator`|`

Der OR-Operator ist ein vertikaler Balken oder ein senkrechter Strich. Zum Beispiel sucht `wifi | luxury` nach Dokumenten, die entweder `wifi`oder `luxury` oder beides enthalten.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT-Operator `-`

Der NOT-Operator ist ein Minuszeichen (-). Beispielsweise sucht `wifi –luxury` nach Dokumenten, die den Begriff `wifi` enthalten und/oder `luxury` nicht enthalten (und/oder von `searchMode` gesteuert werden).

> [!NOTE]  
>  Die Option `searchMode` steuert, ob ein Begriff mit dem NOT-Operator in Abwesenheit eines `+`- oder `|`-Operators mit den anderen Begriffen in der Abfrage als AND oder OR betrachtet wird. Zur Erinnerung: `searchMode` kann entweder auf `any` (Standard) oder `all` festgelegt werden. Wenn Sie `any` verwenden, erhöht dies den Abruf von Abfragen, indem es mehr Ergebnisse einbezieht, und standardmäßig wird `-` als „OR NOT“ interpretiert. So stimmt beispielsweise `wifi -luxury` mit Dokumenten überein, die entweder den Begriff `wifi` enthalten oder die den Begriff `luxury` nicht enthalten. Wenn Sie `all` verwenden, erhöht dies die Genauigkeit der Abfragen, indem es weniger Ergebnisse einbezieht, und wird standardmäßig als „AND NOT“ interpretiert. So stimmt beispielsweise `wifi -luxury` mit Dokumenten überein, die den Begriff `wifi` enthalten und die nicht den Begriff „Luxus“ enthalten. Dies ist wohl ein intuitiveres Verhalten für den `-`-Operator. Daher sollten Sie die Verwendung von `searchMode=all` anstelle von `searchMode=any` in Betracht ziehen, wenn Sie die Suche hinsichtlich der Genauigkeit statt des Rückrufs optimieren möchten, *und* Ihre Benutzer häufig den `-`-Operator bei der Suche verwenden.

## <a name="suffix-operator"></a>Suffixoperator

Der Suffixoperator ist ein Sternchen `*`. Beispielsweise sucht `lux*` nach Dokumenten, die einen Begriff haben, der mit `lux` beginnt, wobei Groß-/Kleinschreibung ignoriert wird.  

## <a name="phrase-search-operator"></a>Phrase-Suchoperator

Der phrase-Operator schließt einen Ausdruck in Anführungszeichen `" "` ein. Zum Beispiel: `Roach Motel` (ohne Anführungszeichen) würde nach Dokumenten suchen, die `Roach` und/oder `Motel` enthalten, und zwar überall in beliebiger Reihenfolge, `"Roach Motel"` (mit Anführungszeichen) stimmt nur mit Dokumenten überein, die den gesamten Ausdruck zusammen und in dieser Reihenfolge enthalten (die Textanalyse gilt weiterhin).

## <a name="precedence-operator"></a>Precedence-Operator

Der precedence-Operator schließt die Zeichenfolge in Klammern `( )` ein. Zum Beispiel sucht `motel+(wifi | luxury)` nach Dokumenten, die den Begriff „Motel“ und entweder `wifi` oder `luxury` (oder beides) enthalten.  

## <a name="escaping-search-operators"></a>Escaping-Suchoperatoren  

 Um die obigen Symbole als eigentlichen Teil des Suchtextes zu verwenden, sollten sie durch Voranstellen eines umgekehrten Schrägstrichs escaped werden. Beispielsweise führt `luxury\+hotel` zum Begriff `luxury+hotel`. Um es für die typischeren Fälle einfach zu machen, gibt es zwei Ausnahmen von dieser Regel, bei denen ein Escape nicht erforderlich ist:  

- Der NOT-Operator `-` muss nur dann escaped werden, wenn es sich um das erste Zeichen nach Leerzeichen handelt, nicht, wenn es sich um die Mitte eines Begriffs handelt. Beispielsweise ist `wi-fi` ein einzelner Begriff, während GUIDs (z.B. `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) wie ein einzelner Token behandelt werden.
- Der Suffix-Operator `*` muss nur dann escaped werden, wenn es sich um das letzte Zeichen vor dem Leerzeichen handelt, nicht, wenn es sich um die Mitte eines Begriffs handelt. Beispielsweise wird `wi*fi` als ein einzelnes Token behandelt.

> [!NOTE]  
>  Obwohl ein Escapen Token zusammenhält, kann die Textanalyse sie je nach Analysenmodus aufteilen. Weitere Informationen finden Sie unter [Sprachunterstützung (REST-API für Azure Search-Dienst)](index-add-language-analyzers.md).  

## <a name="see-also"></a>Weitere Informationen  

+ [Dokumente suchen (REST-API für Azure Suchdienst)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Lucene-Abfragesyntax](query-lucene-syntax.md)
+ [OData-Ausdruckssyntax](query-odata-filter-orderby-syntax.md) 
