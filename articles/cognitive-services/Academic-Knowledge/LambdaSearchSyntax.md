---
title: Lambda-Suchsyntax – Academic Knowledge-API
titlesuffix: Azure Cognitive Services
description: Erfahren Sie mehr über die Lamdba-Suchsyntax, die Sie in der Academic Knowledge-API verwenden können.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 4d4c540e00794bfdf1df265457798cc13530c828
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61337787"
---
# <a name="lambda-search-syntax"></a>Lambda-Suchsyntax

Jede *Lambda*-Suchabfragezeichenfolge beschreibt ein Diagrammmuster. Eine Abfrage muss mindestens einen Startknoten umfassen, der angibt, von welchen Diagrammknoten der Durchlauf gestartet wird. Um einen Startknoten anzugeben, rufen Sie die *MAG.StartFrom()* -Methode auf, und fügen Sie die ID(s) von einem oder mehreren Knoten oder einem Abfrageobjekt ein, um die Sucheinschränkungen anzugeben. Die *StartFrom()* -Methode hat drei Überladungen. Alle von ihnen haben zwei Argumente, wobei das zweite optional ist. Das erste Argument kann eine lange Ganzzahl, eine enumerierbare Sammlung von langen Ganzzahlen oder eine Zeichenfolge sein, die ein JSON-Objekt darstellt, mit der gleichen Semantik wie in *json*-Suche:
```
StartFrom(long cellid, IEnumerable<string> select = null)
StartFrom(IEnumerable<long> cellid, IEnumerable<string> select = null)
StartFrom(string queryObject, IEnumerable<string> select = null)
```

Der Prozess des Schreibens einer Lambda-Suchanfrage besteht darin, von einem Knoten zum anderen zu gehen. Um den Typ des zu durchlaufenden Edge anzugeben, verwenden Sie *FollowEdge()* und fügen Sie die gewünschten Edgetypen ein. *FollowEdge()* nimmt eine beliebige Anzahl von Zeichenfolgenargumenten:
```
FollowEdge(params string[] edgeTypes)
```
> [!NOTE]
> Wenn die zu verfolgenden Edge-Typen keine Rolle spielen, lassen Sie einfach *FollowEdge()* zwischen zwei Knoten weg: die Abfrage wird alle möglichen Edges zwischen diesen beiden Knoten durchlaufen.

Über *VisitNode()* können wir die Durchlaufaktionen festlegen, die auf einem Knoten ausgeführt werden sollen, d.h. ob an diesem Knoten angehalten und der aktuelle Pfad als Ergebnis zurückgegeben werden soll oder ob das Diagramm weiter durchsucht werden soll.  Der Enumerationstyp *Action* definiert zwei Arten von Aktionen: *Action.Return* und *Action.Continue*. Wir können einen solchen Enum-Wert direkt in *VisitNode()* eingeben oder mit dem bitweisen AND-Operator „&“ kombinieren. Wenn zwei Aktion kombiniert werden, bedeutet dies, dass beide Aktionen ausgeführt werden. Hinweis: Verwenden Sie keinen bitweisen OR-Operator „|“ bei Aktionen. Anderenfalls wird die Abfrage beendet, ohne dass etwas zurückgegeben wird. Das Überspringen von *VisitNode()* zwischen zwei *FollowEdge()* -Aufrufen führt dazu, dass die Abfrage das Diagramm nach Ankunft an einem Knoten ohne Bedingung durchsucht.

```
VisitNode(Action action, IEnumerable<string> select = null)
```

Für *VisitNode()* können wir auch einen Lambda-Ausdruck vom Typ *Expression\<Func\<INode, Action\>\>* eingeben, der eine *INode* nimmt und eine Durchlaufaktion zurückgibt:

```
VisitNode(Expression<Func<INode, Action>> action, IEnumerable<string> select = null)
```

## <a name="inode"></a>*INode* 

*INode* bietet *schreibgeschützte* Datenzugriffsschnittstellen und einige integrierte Hilfsfunktionen auf einem Knoten. 

### <a name="basic-data-access-interfaces"></a>Grundlegende Datenzugriffsschnittstellen

##### <a name="long-cellid"></a>long CellID

Die 64-Bit-ID des Knotens. 

##### <a name="t-getfieldtstring-fieldname"></a>T GetField\<T\>(string fieldName)

Ruft den Wert der spezifizierten Eigenschaft ab. *T* ist der gewünschte Typ, als der das Feld interpretiert werden soll. Wenn der gewünschte Typ nicht implizit aus dem Typ des Feldes konvertiert werden kann, wird eine automatische Typumwandlung versucht. Hinweis: Wenn die Eigenschaft mehrere Werte hat, wird die Liste durch *GetField\<string\>* als Json-Zeichenfolge ["val1", "val2", ...] serialisiert. Wenn die Eigenschaft nicht vorhanden ist, wird eine Ausnahme ausgelöst und die Durchsuchung des aktuellen Diagramms abgebrochen.

##### <a name="bool-containsfieldstring-fieldname"></a>bool ContainsField(string fieldName)

Gibt an, ob ein Feld mit dem angegebenen Namen im aktuellen Knoten vorhanden ist.

##### <a name="string-getstring-fieldname"></a>string get(string fieldName)

Funktioniert wie *GetField\<string\>(fieldName)* . Allerdings werden keine Ausnahmen ausgelöst, wenn das Feld nicht gefunden wird. Stattdessen wird eine leere Zeichenfolge ("") zurück.

##### <a name="bool-hasstring-fieldname"></a>bool has(string fieldName)

Gibt an, ob die angegebene Eigenschaft im aktuellen Knoten vorhanden ist. Identisch mit *ContainsField(fieldName)* .

##### <a name="bool-hasstring-fieldname-string-value"></a>bool has(string fieldName, string value)

Gibt an, ob die Eigenschaft einen angegebenen Wert hat. 

##### <a name="int-countstring-fieldname"></a>int count(string fieldname)

Ruft die Anzahl der Werte einer angegebenen Eigenschaft ab. Wenn die Eigenschaft nicht vorhanden ist, wird 0 zurückgegeben.

### <a name="built-in-helper-functions"></a>Integrierte Hilfsfunktionen

##### <a name="action-returnifbool-condition"></a>Action return_if(bool condition)

Gibt *Action.Return* zurück, wenn die Bedingung *true* ist. Wenn die Bedingung *false* ist und dieser Ausdruck nicht mit anderen Aktionen durch einen bitweisen AND-Operator verbunden wird, wird die Diagrammuntersuchung abgebrochen.

##### <a name="action-continueifbool-condition"></a>Action continue_if(bool condition)

Gibt *Action.Continue* zurück, wenn die Bedingung *true* ist. Wenn die Bedingung *false* ist und dieser Ausdruck nicht mit anderen Aktionen durch einen bitweisen AND-Operator verbunden wird, wird die Diagrammuntersuchung abgebrochen.

##### <a name="bool-dicedouble-p"></a>bool dice(double p)

Generiert eine Zufallszahl, die größer als oder gleich 0.0 und kleiner als 1.0 ist. Diese Funktion gibt nur *true* zurück, wenn die Anzahl kleiner als oder gleich *p* ist.

Im Vergleich zur *JSON*-Suche ist die *Lambda*suche ausdrucksvoller: C#-Lambdaausdrücke können direkt verwendet werden, um Abfragemuster anzugeben. Hier sind zwei Beispiele.

```
MAG.StartFrom(@"{
    type  : ""ConferenceSeries"",
    match : {
        FullName : ""graph""
    }
}", new List<string>{ "FullName", "ShortName" })
.FollowEdge("ConferenceInstanceIDs")
.VisitNode(v => v.return_if(v.GetField<DateTime>("StartDate").ToString().Contains("2014")),
        new List<string>{ "FullName", "StartDate" })
```

```
MAG.StartFrom(@"{
    type  : ""Affiliation"",
    match : {
        Name : ""microsoft""
    }
}").FollowEdge("PaperIDs")
.VisitNode(v => v.return_if(v.get("NormalizedTitle").Contains("graph") || v.GetField<int>("CitationCount") > 100),
        new List<string>{ "OriginalTitle", "CitationCount" })
```
