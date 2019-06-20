---
title: Strukturierte Abfrageausdrücke – Knowledge Exploration Service-API
titlesuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie strukturierte Abfrageausdrücke in der Knowledge Exploration Service-API (KES) verwenden können.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: a544cdca1ef4be56fcf368a39040f4ee85076a9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60815101"
---
# <a name="structured-query-expression"></a>Strukturierter Abfrageausdruck

Ein strukturierter Abfrageausdruck gibt eine Gruppe von Vorgängen an, die anhand des Datenindex ausgewertet werden sollen.  Er besteht aus Attributabfrageausdrücken und übergeordneten Funktionen.  Mithilfe der [*evaluate*](evaluateMethod.md)-Methode können die Objekte berechnet werden, die dem Ausdruck entsprechen.  Im Folgenden finden Sie ein Beispiel aus dem Bereich wissenschaftlicher Veröffentlichungen, der von Jaime Teevan verfasste Veröffentlichungen seit dem Jahr 2013 zurückgibt.

`And(Composite(Author.Name=='jaime teevan'),Y>=2013)`

Strukturierte Abfrageausdrücke können durch [*interpret*](interpretMethod.md)-Anforderungen abgerufen werden, bei denen die semantischen Ausgabe der einzelnen Interpretationen einen strukturierten Abfrageausdruck darstellt. Dieser gibt die Indexobjekte zurück, die der Eingabeabfrage in natürlicher Sprache entsprechen.  Alternativ können diese mithilfe der in diesem Abschnitt erläuterten Syntax manuell erstellt werden.

## <a name="attribute-query-expression"></a>Attributabfrageausdruck

Ein Attributabfrageausdruck identifiziert eine Reihe von Objekten basierend darauf, ob diese einem bestimmten Attribut entsprechen.  Je nach Attributtyp und indiziertem Vorgang, der im [Schema](SchemaFormat.md) angegeben wird, werden verschiedene übereinstimmende Vorgänge unterstützt:

| Type | Vorgang | Beispiele |
|------|-------------|------------|
| string | equals | Title='latent semantic analysis'  (kanonisch + Synonyme) |
| string | equals | Author.Name=='susan t dumais'  (nur kanonisch)|
| string | starts_with | Title='latent s'... |
| Int32/Int64/Double | equals | Year=2000 |
| Int32/Int64/Double | starts_with | Year='20'... (beliebiger Dezimalwert beginnend mit „20“) |
| Int32/Int64/Double | is_between | Year&lt;2000 <br/> Year&lt;=2000 <br/> Year&gt;2000 <br/> Year&gt;=2000 <br/> Year=[2010,2012) *(enthält nur den linken Begrenzungswert: 2010, 2011)* <br/> Year=[2000,2012] *(enthält beide Begrenzungswerte: 2010, 2011, 2012)* |
| Date | equals | BirthDate='1984-05-14' |
| Date | is_between | BirthDate&lt;='2008/03/14' <br/> PublishDate=['2000-01-01','2009-12-31'] |
| Guid | equals | Id='602DD052-CC47-4B23-A16A-26B52D30C05B' |


Der Ausdruck „Title='latent s'...“ z.B. entspricht allen wissenschaftlichen Veröffentlichungen, deren Titel mit der Zeichenfolge „latent s“ beginnen.  Um diesen Ausdruck auswerten zu können, muss das Title-Attribut den Vorgang „starts_with“ im Schema angeben, das zum Erstellen des Index verwendet wird.

Bei Attributen mit zugeordneten Synonymen kann ein Abfrageausdruck Objekte angeben, dessen kanonischer Wert einer bestimmten Zeichenfolge mit dem Operator „==“ entspricht, oder Objekte, bei denen keiner der kanonischen bzw. Synonymwerte dem Operator „=“ entspricht.  Bei beiden Objekten muss der equals-Operator in der Attributdefinition angegeben werden.


## <a name="functions"></a>Functions

Es gibt eine Reihe von integrierten Funktionen, die die Erstellung von komplexeren Abfrageausdrücken über grundlegende Attributabfragen ermöglichen.

### <a name="and-function"></a>And-Funktion

`And(expr1, expr2)`

Gibt die Überschneidung der beiden Eingabeabfrageausdrücke zurück.

Das folgende Beispiel gibt wissenschaftliche Veröffentlichungen zurück, die im Jahr 2000 zum Thema Informationsgewinnung veröffentlicht wurden:

`And(Year=2000, Keyword=='information retrieval')`

### <a name="or-function"></a>Or-Funktion

`Or(expr1, expr2)`

Gibt die Kombination der beiden Eingabeabfrageausdrücke zurück.

Das folgende Beispiel gibt wissenschaftliche Veröffentlichungen zurück, die im Jahr 2000 zum Thema Informationsgewinnung oder Benutzermodellierung veröffentlicht wurden:

`And(Year=2000, Or(Keyword='information retrieval', Keyword='user modeling'))`

### <a name="composite-function"></a>Composite-Funktion

`Composite(expr)`

Gibt eine Abfrage zurück, die einen inneren Ausdruck aus Abfragen mit untergeordneten Attributen eines gemeinsamen zusammengesetzten Attributs kapselt.  Für die Kapselung muss das zusammengesetzte Attribut aller übereinstimmenden Datenobjekte mindestens einen Wert aufweisen, der jeweils den inneren Ausdruck erfüllt.  Beachten Sie, dass ein Abfrageausdruck untergeordneter Attribute eines zusammengesetzten Attributs mit der Composite()-Funktion gekapselt werden muss, bevor er mit anderen Abfrageausdrücken kombiniert werden kann.

Der folgende Ausdruck beispielsweise gibt wissenschaftliche Veröffentlichungen von „harry shum“ zurück, während er gleichzeitig auf „microsoft“ festgelegt war:

```
Composite(And(Author.Name="harry shum", 
              Author.Affiliation="microsoft"))
```

Der folgende Ausdruck gibt hingegen wissenschaftliche Veröffentlichungen zurück, bei denen einer der Autoren „harry shum“ und eine der Zugehörigkeiten „microsoft“ ist:

```
And(Composite(Author.Name="harry shum"), 
    Composite(Author.Affiliation="microsoft"))
```
