---
title: Grammatikformat – Knowledge Exploration Service-API
titlesuffix: Azure Cognitive Services
description: Hier finden Sie Informationen zum Grammatikformat in der KES-API (Knowledge Exploration Service).
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 844bd9a88c52fd398fc66c71e59da513c0d7d90d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814873"
---
# <a name="grammar-format"></a>Grammatikformat

Bei der Grammatik handelt es sich um eine XML-Datei, die den gewichteten Satz von Abfragen in natürlicher Sprache angibt, die der Dienst interpretieren kann, und die angibt, wie diese Abfragen in natürlicher Sprache in Semantikabfrageausdrücke konvertiert werden.  Die Grammatiksyntax basiert auf [SRGS](https://www.w3.org/TR/speech-grammar/). Dabei handelt es sich um einen W3C-Standard für Spracherkennungsgrammatiken mit Erweiterungen zur Unterstützung von Datenindexintegration und Semantikfunktionen.

In diesem Artikel werden die einzelnen syntaktischen Elemente beschrieben, die in einer Grammatik verwendet werden können.  Eine vollständige Grammatik, die die Verwendung dieser Elemente im Kontext veranschaulicht, finden Sie in [diesem Beispiel](#example).

### <a name="grammar-element"></a>grammar-Element

Das `grammar`-Element ist das übergeordnete Element in der XML-Grammatikspezifikation.  Das erforderliche `root`-Attribut gibt den Namen der Stammregel an, die den Startpunkt der Grammatik definiert.

```xml
<grammar root="GetPapers">
```

### <a name="import-element"></a>import-Element

Das `import`-Element importiert eine Schemadefinition aus einer externen Datei, um Attributverweise zu ermöglichen. Das Element muss ein untergeordnetes Element des übergeordneten `grammar`-Elements sein und sich vor jeglichen `attrref`-Elementen befinden. Das erforderliche `schema`-Attribut gibt den Namen einer Schemadatei an, die sich im gleichen Verzeichnis befindet wie die XML-Grammatikdatei. Das erforderliche `name`-Element gibt das Schemaalias an, das nachfolgende `attrref`-Elemente verwenden, wenn sie auf in diesem Schema definierte Attribute verweisen.

```xml
  <import schema="academic.schema" name="academic"/>
```

### <a name="rule-element"></a>rule-Element

Das `rule`-Element definiert eine Grammatikregel. Dabei handelt es sich um eine strukturelle Einheit, die einen Satz von Abfrageausdrücken angibt, die das System interpretieren kann.  Das Element muss ein untergeordnetes Element des übergeordneten `grammar`-Elements sein.  Das erforderliche `id`-Attribut gibt den Namen der Regel an, auf die in `grammar`- oder `ruleref`-Elementen verwiesen wird.

Ein `rule`-Element definiert eine Gruppe zulässiger Erweiterungen.  Texttoken werden direkt mit der Eingabeabfrage abgeglichen.  `item`-Elemente geben Wiederholungen an und ändern Interpretationswahrscheinlichkeiten.  `one-of`-Elemente geben alternative Optionen an.  `ruleref`-Elemente ermöglichen die Erstellung komplexerer Erweiterungen auf der Grundlage einfacher Erweiterungen.  `attrref`-Elemente ermöglichen Abgleiche mit Attributwerten aus dem Index.  `tag`-Elemente geben die Semantik der Interpretation an und können die Interpretationswahrscheinlichkeit ändern.

```xml
<rule id="GetPapers">...</rule>
```

### <a name="example-element"></a>example-Element

Das optionale `example`-Element gibt Beispielausdrücke an, die von der enthaltenden `rule`-Definition akzeptiert werden können.  Dies kann zu Dokumentationszwecken und/oder für automatisierte Tests verwendet werden.

```xml
<example>papers about machine learning by michael jordan</example>
```

### <a name="item-element"></a>item-Element

Das `item`-Element gruppiert eine Sequenz von Grammatikkonstrukten.  Damit können Wiederholungen der Erweiterungssequenz oder Alternativen in Verbindung mit dem `one-of`-Element angegeben werden.

Wenn ein `item`-Element kein untergeordnetes Element eines `one-of`-Elements ist, kann es die Wiederholung der eingeschlossenen Sequenz angeben, indem dem `repeat`-Attribut eine Anzahl zugewiesen wird.  Die Anzahl „*n*“ (wobei *n* für eine ganze Zahl steht) gibt an, dass die Sequenz genau *n*-mal vorkommen muss.  Bei Verwendung der Anzahl „*m*-*n*“ kann die Sequenz zwischen *m*- und *n*-mal vorkommen (jeweils einschließlich).  Die Anzahl „*m*-“ gibt an, dass die Sequenz mindestens *m*-mal vorkommen muss.  Mit dem optionalen `repeat-logprob`-Attribut kann die Interpretationswahrscheinlichkeit für jede weitere Wiederholung geändert werden, die über den Mindestwert hinausgeht.

```xml
<item repeat="1-" repeat-logprob="-10">...</item>
```

Wenn `item`-Elemente als untergeordnete Elemente eines `one-of`-Elements verwendet werden, definieren sie die Gruppe von Erweiterungsalternativen.  Bei dieser Verwendung gibt das optionale `logprob`-Attribut die relative Logarithmus-Wahrscheinlichkeit für die verschiedenen Optionen an.  Bei einer Wahrscheinlichkeit *p* zwischen 0 und 1 kann die entsprechende Logarithmuswahrscheinlichkeit als log(*p*) berechnet werden, wobei „log()“ die natürliche Logarithmusfunktion ist.  Ohne Angabe wird für `logprob` standardmäßig „0“ verwendet, und die Interpretationswahrscheinlichkeit wird nicht geändert.  Beachten Sie, dass die Logarithmus-Wahrscheinlichkeit immer ein negativer Gleitkommawert oder Null ist.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="one-of-element"></a>one-of-Element

Das `one-of`-Element gibt alternative Erweiterungen für eines der untergeordneten `item`-Elemente an.  In einem `one-of`-Element dürfen sich nur `item`-Elemente befinden.  Relative Wahrscheinlichkeiten für die verschiedenen Optionen können über das `logprob`-Attribut in jedem untergeordneten `item`-Element angegeben werden.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="ruleref-element"></a>ruleref-Element

Das `ruleref`-Element gibt gültige Erweiterungen über Verweise auf ein anderes `rule`-Element an.  Durch die Verwendung von `ruleref`-Elementen können komplexere Ausdrücke auf der Grundlage einfacherer Regeln erstellt werden.  Das erforderliche `uri`-Attribut gibt den Namen des referenzierten `rule`-Elements unter Verwendung der Syntax „#*Regelname*“ an.  Verwenden Sie zum Erfassen der Semantikausgabe der referenzierten Regel das optionale `name`-Attribut, um den Namen einer Variablen anzugeben, der die Semantikausgabe zugewiesen ist.
 
```xml
<ruleref uri="#GetPaperYear" name="year"/>
```

### <a name="attrref-element"></a>attrref-Element

Das `attrref`-Element verweist auf ein Indexattribut und ermöglicht Abgleiche mit Attributwerten aus dem Index.  Das erforderliche `uri`-Attribut gibt den Indexschemanamen und den Attributnamen unter Verwendung der Syntax „*Schemaname*#*Attributname*“ an.  Dabei muss ein vorangestelltes `import`-Element vorhanden sein, das das Schema mit dem Namen *Schemaname* importiert.  Der Attributname ist der Name eines im entsprechenden Schema definierten Attributs.

Zusätzlich zum Abgleich mit der Benutzereingabe gibt das `attrref`-Element auch ein strukturiertes Abfrageobjekt als Ausgabe zurück, das die Teilmenge der Objekte im Index auswählt, die dem Eingabewert entsprechen.  Verwenden Sie das optionale `name`-Attribut, um den Namen der Variablen anzugeben, in der die Ausgabe des Abfrageobjekts gespeichert werden soll.  Das Abfrageobjekt kann mit anderen Abfrageobjekten kombiniert werden, um komplexere Ausdrücke zu erstellen.  Ausführliche Informationen finden Sie unter [Semantic Interpretation](SemanticInterpretation.md) (Semantikinterpretation).  

```xml
<attrref uri="academic#Keyword" name="keyword"/>
```

#### <a name="query-completion"></a>Abfragevervollständigung

Zur Unterstützung von Abfragevervollständigungen beim Interpretieren partieller Benutzerabfragen muss jedes Attribut „starts_with“ als Vorgang in der Schemadefinition enthalten.  Wenn ein Benutzerabfragepräfix vorhanden ist, gleicht `attrref` alle Werte im Index ab, die das Präfix vervollständigen, und gibt jeden vollständigen Wert als separate Interpretation der Grammatik zurück.  

Beispiele:
* Der Abgleich von `<attrref uri="academic#Keyword" name="keyword"/>` mit dem Abfragepräfix „Dat“ generiert eine Interpretation für Dokumente zu „Datenbank“, eine Interpretation für Dokumente zu „Data Mining“ usw.
* Der Abgleich von `<attrref uri="academic#Year" name="year"/>` mit dem Abfragepräfix „200“ generiert eine Interpretation für Dokumente des Jahres „2000“, eine Interpretation für Dokumente des Jahres „2001“ usw.

#### <a name="matching-operations"></a>Abgleichvorgänge

Neben exakten Übereinstimmungen unterstützen bestimmte Attributtypen über das optionale `op`-Attribut auch Präfix- und Ungleichheitsübereinstimmungen.  Ist im Index kein Objekt mit passendem Wert vorhanden, wird der Grammatikpfad blockiert, und der Dienst generiert keine Interpretationen, wenn er diesen Grammatikpfad durchläuft.   Das `op`-Attribut wird standardmäßig auf „eq“ festgelegt.

```xml
in <attrref uri="academic#Year" name="year"/>
before <attrref uri="academic#Year" op="lt" name="year"/
```

Die folgende Tabelle enthält die unterstützten `op`-Werte für die jeweiligen Attributtypen.  Für die Verwendung muss der entsprechende Indexvorgang in die Schemaattributdefinition eingeschlossen werden.

| Attributtyp | op-Wert | BESCHREIBUNG | Indexoperation
|----|----|----|----|
| string | eq | Zeichenfolge – exakte Übereinstimmung | equals |
| string | starts_with | Zeichenfolge – Präfixübereinstimmung | starts_with |
| Int32, Int64, Double | eq |  Numerischer Wert – Gleichheitsübereinstimmung | equals |
| Int32, Int64, Double | lt, le, gt, ge | Numerischer Wert – Ungleichheitsübereinstimmung (<, <=, >, >=) | is_between |
| Int32, Int64, Double | starts_with | Präfixübereinstimmung des Werts in Dezimalschreibweise | starts_with |

Beispiele:
* `<attrref uri="academic#Year" op="lt" name="year"/>` führt einen Abgleich mit der Eingabezeichenfolge „2000“ durch und gibt alle Dokumente zurück, die vor dem Jahr 2000 veröffentlicht wurden.
* `<attrref uri="academic#Year" op="lt" name="year"/>` führt keinen Abgleich mit der Eingabezeichenfolge „20“ durch, da der Index keine Dokumente vor dem Jahr 20 enthält.
* `<attrref uri="academic#Keyword" op="starts_with" name="keyword"/>` führt einen Abgleich mit der Eingabezeichenfolge „dat“ durch und gibt in einer einzelnen Interpretation Dokumente zu „Datenbank“, „Data Mining“ usw. zurück.  Hierbei handelt es sich um einen seltenen Anwendungsfall.
* `<attrref uri="academic#Year" op="starts_with" name="year"/>` führt einen Abgleich mit der Eingabezeichenfolge „20“ durch und gibt in einer einzelnen Interpretation Dokumente zurück, die zwischen 200 und 299, zwischen 2000 und 2999 usw. veröffentlicht wurden.  Hierbei handelt es sich um einen seltenen Anwendungsfall.

### <a name="tag-element"></a>tag-Element

Das `tag`-Element gibt an, wie ein Pfad durch die Grammatik interpretiert werden soll.  Es enthält eine Reihe von Anweisungen, die jeweils mit einem Semikolon enden.  Bei einer Anweisung kann es sich um eine Zuweisung eines Literals oder einer Variable zu einer anderen Variable handeln.  Sie kann aber auch die Ausgabe einer Funktion mit null oder mehr Parametern einer Variablen zuweisen.  Jeder Funktionsparameter kann mit einem Literal oder einer Variablen angegeben werden.  Wenn die Funktion keine Ausgabe zurückgibt, erfolgt keine Zuweisung.  Der Variablenbereich ist für die enthaltende Regel lokal.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Jedes `rule`-Element in der Grammatik verfügt über eine vordefinierte Variable namens „out“, die die Semantikausgabe der Regel darstellt.  Zur Berechnung des Werts werden die einzelnen Semantikanweisungen ausgewertet, die vom Pfad durch das `rule`-Element durchlaufen werden, das der Eingabe der Benutzerabfrage entspricht.  Der Wert, der der Variablen „out“ am Ende der Auswertung zugewiesen wird, ist die Semantikausgabe der Regel.  Die Semantikausgabe der grammatikbasierten Interpretation einer Benutzerabfrage ist die Semantikausgabe der Stammregel.

Einige Anweisungen ändern möglicherweise die Wahrscheinlichkeit eines Interpretationspfads, indem sie ein zusätzliches Offset für die Logarithmus-Wahrscheinlichkeit einführen.  Einige Anweisungen lehnen die Interpretation möglicherweise vollständig ab, wenn die angegebenen Bedingungen nicht erfüllt sind.

Eine Liste mit unterstützten Semantikfunktionen finden Sie unter [Semantic Functions](SemanticInterpretation.md#semantic-functions) (Semantikfunktionen).

## <a name="interpretation-probability"></a>Interpretationswahrscheinlichkeit

Die Wahrscheinlichkeit eines Interpretationspfads durch die Grammatik ist die kumulative Logarithmus-Wahrscheinlichkeit aller `<item>`-Elemente und Semantikfunktionen des Pfads.  Sie beschreibt die relative Wahrscheinlichkeit für die Übereinstimmung einer bestimmten Eingabesequenz.

Bei einer Wahrscheinlichkeit *p* zwischen 0 und 1 kann die entsprechende Logarithmus-Wahrscheinlichkeit als log(*p*) berechnet werden, wobei „log()“ die natürliche Logarithmus-Funktion ist.  Mithilfe von Logarithmus-Wahrscheinlichkeiten kann das System durch einfache Addition die gemeinsame Wahrscheinlichkeit eines Interpretationspfads ermitteln.  Darüber hinaus wird dadurch ein Gleitkommaunterlauf vermieden, wie er bei solchen Berechnungen der gemeinsamen Wahrscheinlichkeit häufig zu beobachten ist.  Beachten Sie, dass die Logarithmus-Wahrscheinlichkeit immer ein negativer Gleitkommawert oder Null ist und höhere Werte eine höhere Wahrscheinlichkeit angeben.

## <a name="example"></a>Beispiel

Im Anschluss finden Sie ein XML-Beispiel aus dem Bereich wissenschaftlicher Veröffentlichungen, das die verschiedenen Grammatikelemente veranschaulicht:

```xml
<grammar root="GetPapers">

  <!-- Import academic data schema-->
  <import schema="academic.schema" name="academic"/>
  
  <!-- Define root rule-->
  <rule id="GetPapers">
    <example>papers about machine learning by michael jordan</example>
    
    papers
    <tag>
      yearOnce = false;
      isBeyondEndOfQuery = false;
      query = All();
    </tag>
  
    <item repeat="1-" repeat-logprob="-10">
      <!-- Do not complete additional attributes beyond end of query -->
      <tag>AssertEquals(isBeyondEndOfQuery, false);</tag>
        
      <one-of>
        <!-- about <keyword> -->
        <item logprob="-0.5">
          about <attrref uri="academic#Keyword" name="keyword"/>
          <tag>query = And(query, keyword);</tag>
        </item>
        
        <!-- by <authorName> [while at <authorAffiliation>] -->
        <item logprob="-1">
          by <attrref uri="academic#Author.Name" name="authorName"/>
          <tag>authorQuery = authorName;</tag>
          <item repeat="0-1" repeat-logprob="-1.5">
            while at <attrref uri="academic#Author.Affiliation" name="authorAffiliation"/>
            <tag>authorQuery = And(authorQuery, authorAffiliation);</tag>
          </item>
          <tag>
            authorQuery = Composite(authorQuery);
            query = And(query, authorQuery);
          </tag>
        </item>
        
        <!-- written (in|before|after) <year> -->
        <item logprob="-1.5">
          <!-- Allow this grammar path to be traversed only once -->
          <tag>
            AssertEquals(yearOnce, false);
            yearOnce = true;
          </tag>
          <ruleref uri="#GetPaperYear" name="year"/>
          <tag>query = And(query, year);</tag>
        </item>
      </one-of>

      <!-- Determine if current parse position is beyond end of query -->
      <tag>isBeyondEndOfQuery = GetVariable("IsBeyondEndOfQuery", "system");</tag>
    </item>
    <tag>out = query;</tag>
  </rule>
  
  <rule id="GetPaperYear">
    <tag>year = All();</tag>
    written
    <one-of>
      <item>
        in <attrref uri="academic#Year" name="year"/>
      </item>
      <item>
        before
        <one-of>
          <item>[year]</item>
          <item><attrref uri="academic#Year" op="lt" name="year"/></item>
        </one-of>
      </item>
      <item>
        after
        <one-of>
          <item>[year]</item>
          <item><attrref uri="academic#Year" op="gt" name="year"/></item>
        </one-of>
      </item>
    </one-of>
    <tag>out = year;</tag>
  </rule>
</grammar>
```
