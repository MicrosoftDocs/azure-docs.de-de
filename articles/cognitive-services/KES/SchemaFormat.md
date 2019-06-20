---
title: Schemaformat – Knowledge Exploration Service-API
titlesuffix: Azure Cognitive Services
description: Hier finden Sie Informationen zum Schemaformat in der KES-API (Knowledge Exploration Service).
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 51a812762659bcc67762b82e9c120772065aab53
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814414"
---
# <a name="schema-format"></a>Schemaformat

Das Schema wird in einer JSON-Datei angegeben, die die Attributstruktur der Objekte in der für die Indexerstellung verwendeten Datendatei beschreibt.  Für jedes Attribut gibt das Schema Name, Datentyp, optionale Vorgänge und die Liste der optionalen Synonyme an.  Ein Objekt kann 0 oder mehr Werte der einzelnen Attribute enthalten.  Im Folgenden wird ein vereinfachtes Beispiel aus einem Bereich wissenschaftlicher Veröffentlichungen vorgestellt:

``` json
{
  "attributes":[
    {"name":"Title", "type":"String"},
    {"name":"Year", "type":"Int32"},
    {"name":"Author", "type":"Composite"},
    {"name":"Author.Id", "type":"Int64", "operations":["equals"]},
    {"name":"Author.Name", "type":"String"},
    {"name":"Author.Affiliation", "type":"String"},
    {"name":"Keyword", "type":"String", "synonyms":"Keyword.syn"}
  ]
}
```

Attributnamen sind Bezeichner, bei denen die Groß-/Kleinschreibung berücksichtigt wird, die mit einem Buchstaben beginnen und ausschließlich aus Buchstaben (A-Z), Ziffern (0-9) und Unterstrichen (\_) bestehen.  Mit dem reservierten logprob-Attribut werden die Wahrscheinlichkeiten relativer natürlicher Logarithmen unter Objekten angegeben.

## <a name="attribute-type"></a>Attributtyp

Es folgt eine Liste der unterstützten Attributdatentypen:

| Type | BESCHREIBUNG | Vorgänge | Beispiel |
|------|-------------|------------|---------|
| `String` | Zeichenfolge (1 bis 1024 Zeichen) | equals, starts_with | „Hallo Welt“ |
| `Int32` | Ganze 32-Bit-Zahl mit Vorzeichen | equals, starts_with, is_between | 2016 |
| `Int64` | Ganze 64-Bit-Zahl mit Vorzeichen | equals, starts_with, is_between | 9876543210 |
| `Double` | Gleitkommawert mit doppelter Genauigkeit | equals, starts_with, is_between | 1.602e-19 |
| `Date` | Datum (1400-01-01 bis 9999-12-31) | equals, is_between | „2016-03-14“ |
| `Guid` | GUID (Globally Unique Identifier) | equals | „602DD052-CC47-4B23-A16A-26B52D30C05B“ |
| `Blob` | Intern komprimierte nicht indizierte Daten | *Keine* | „Unterstützen Sie jede Person und Organisation weltweit darin, ihre Ziele zu erreichen.“ |
| `Composite` | Komposition von mehreren untergeordneten Attributen| *N/V* | { "Name":"harry shum", "Affiliation":"microsoft" } |

Mit Zeichenfolgenattributen werden Zeichenfolgenwerte dargestellt, die als Teil der Benutzerabfrage angezeigt werden können.  Diese unterstützen *equals*-Vorgänge sowie *starts_with*-Vorgänge mit genauer Übereinstimmung bei Szenarien zur Vervollständigung von Abfragen. „micros“ wird beispielsweise in „microsoft“ vervollständigt.  Unterstützung für Übereinstimmung ohne Berücksichtigung von Groß-/Kleinschreibung und Fuzzyübereinstimmung bei der Fehlerbehandlung wird in zukünftigen Releases bereitgestellt.

Int32/Int64/Double-Attribute werden zur Darstellung numerischer Werte verwendet.  Der *is_between*-Vorgang ermöglicht Unterstützung für Ungleichheit (lt, le, gt, ge) zur Laufzeit.  Der *starts_with*-Vorgang unterstützt Szenarien zur Vervollständigung von Abfragen. „20“ wird beispielsweise in „2016“ oder „3.“ in „3.14“ vervollständigt.

Mit Datumsattributen werden effizient Datumswerte codiert.  Der *is_between*-Vorgang ermöglicht Unterstützung für Ungleichheit (lt, le, gt, ge) zur Laufzeit.
  
GUID-Attribute dienen zur effizienten Darstellung von GUID-Werten mit Standardunterstützung für den *equals*-Vorgang.

Blobattribute werden verwendet, um potenziell große Datenblobs für Suchen zur Laufzeit mit dem entsprechenden Objekt ohne Unterstützung für Indizierungsvorgänge basierend auf dem Inhalt der Blobwerte zu codieren.

### <a name="composite-attributes"></a>Zusammengesetzte Attribute

Zusammengesetzte Attribute werden verwendet, um eine Gruppierung von Attributwerten darzustellen.  Der Name der einzelnen untergeordneten Attribute beginnt mit den Namen des zusammengesetzten Attributs gefolgt von „.“.  Werte für zusammengesetzte Attribute werden als JSON-Objekt angegeben, das geschachtelte Attributwerte enthält.  Zusammengesetzte Attribute weisen möglicherweise mehrere Objektwerte auf.  Zusammengesetzte Attribute enthalten möglicherweise jedoch keine untergeordneten Attribute, die selbst zusammengesetzte Attribute sind.

Im obigen Beispiel der wissenschaftlichen Veröffentlichungen kann der Dienst hierdurch Werke mit „harry shum“ abfragen, während die Suche gleichzeitig auf „microsoft“ festgelegt ist.  Ohne zusammengesetzte Attribute kann der Dienst nur Werke abfragen, bei denen einer der Autoren „harry shum“ und der andere Autor „microsoft“ ist.  Weitere Informationen finden Sie unter [Zusammengesetzte Abfragen](SemanticInterpretation.md#composite-function).

## <a name="attribute-operations"></a>Attributvorgänge

Standardmäßig wird jedes Attribut zur Unterstützung aller Vorgänge indiziert, die im Attributdatentyp verfügbar sind.  Wenn ein bestimmter Vorgang nicht erforderlich ist, kann die Gruppe von indizierten Vorgängen explizit auf die Verringerung der Indexgröße festgelegt werden.  Im folgenden Ausschnitt des obigen Beispielschemas wird das Author.Id-Attribut indiziert, um nur den *equals*-Vorgang, jedoch nicht die zusätzliche *starts_with*- und *is_between*-Vorgänge für Int32-Attribute zu unterstützen.
```json
{"name":"Author.Id", "type":"Int32", "operations":["equals"]}
```

Wenn ein Attribut innerhalb einer Grammatik verwiesen wird, muss der *starts_with*-Vorgang im Schema angegeben werden, damit der Dienst Vervollständigungen von Teilabfragen generieren kann.  

## <a name="attribute-synonyms"></a>Attributsynonyme

In der Vielzahl der Fälle empfiehlt es sich, mittels eines Synonyms auf ein bestimmtes Zeichenfolgenattributwert zu verweisen.  Beispielsweise könnten Benutzer auf „Microsoft“ als „MSFT“ oder „MS“ verweisen.  In diesen Fällen kann die Attributdefinition den Namen einer Schemadatei angeben, die sich im selben Verzeichnis wie die Schemadatei befindet.  Jede Zeile in der Synonymdatei stellt einen Synonymeintrag im folgenden JSON-Format dar: `["<canonical>", "<synonym>"]`.  Im Beispielschema ist „AuthorName.syn“ eine JSON-Datei, die Synonymwerte für das Attribut „Author.Name“ enthält.

`{"name":"Author.Name", "type":"String", "synonyms":"AuthorName.syn"}`


Ein kanonischer Wert kann mehrere Synonyme aufweisen.  Mehrere kanonische Werten können auf ein gemeinsames Synonym verweisen.  In solchen Fällen löst der Dienst die Mehrdeutigkeit durch verschiedene Interpretationen auf.  Im Folgenden wird die Beispielsynonymdatei „AuthorName.syn“ mit dem oben genannten Schema gezeigt:
```json
["harry shum","heung-yeung shum"]
["harry shum","h shum"]
["henry shum","h shum"]
...
```
