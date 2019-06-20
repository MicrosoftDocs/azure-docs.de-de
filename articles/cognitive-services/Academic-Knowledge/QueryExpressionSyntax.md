---
title: Syntax für Abfrageausdrücke – Academic Knowledge-API
titlesuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie die Syntax für Abfrageausdrücke in der Academic Knowledge-API verwenden.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 95c2e9d3f54f967b3ebb434c742f69251b80573e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61336755"
---
# <a name="query-expression-syntax"></a>Syntax für Abfrageausdrücke

Wir haben gesehen, wie die Antwort auf eine **interpret**-Anforderung einen Abfrageausdruck enthält. Die Grammatik, die die Abfrage des Benutzers interpretierte, erstellte für jede Interpretation einen Abfrageausdruck. Ein Abfrageausdruck kann dann verwendet werden, um eine **evaluate**-Anforderung zum Abrufen von Entitätssuchergebnissen auszugeben.

Sie können auch eigene Abfrageausdrücke erstellen und diese in einer **evaluate**-Anforderung verwenden. Dies kann nützlich sein, wenn Sie eine eigene Benutzeroberfläche erstellen, die als Antwort auf die Aktionen des Benutzers einen Abfrageausdruck erstellt. Zu diesem Zweck müssen Sie die Syntax für Abfrageausdrücke kennen.  

Jedes Entitätsattribut, das in einen Abfrageausdruck aufgenommen werden kann, hat einen bestimmten Datentyp und eine Reihe von möglichen Abfrageoperatoren. Die Entitätsattribute und unterstützten Operatoren für jedes Attribut sind in [Entitätsattribute](EntityAttributes.md) angegeben. Eine Einzelwertabfrage Wert benötigt das Attribut, um die *Equals*-Operation zu unterstützen. Eine Präfixabfrage benötigt das Attribut, um die *StartsWith*-Operation zu unterstützen. Wertebereichsabfragen benötigen das Attribut, um die *IsBetween*-Operation zu unterstützen.

Einige der Entitätsdaten werden als zusammengesetzte Attribute gespeichert, die durch einen Punkt '.' im Attributnamen gekennzeichnet sind. Beispielsweise werden Informationen zu Autor/Zuordnung als ein zusammengesetztes Attribut dargestellt. Es umfasst vier Komponenten: AuN, AuId, AfN, AfId. Diese Komponenten sind separate Daten, die einen einzelen Entitätsattributwert bilden.


**Zeichenfolgenattribut: Einzelwert** (einschließlich Übereinstimmungen mit Synonymen)  
Ti='indexing by latent semantic analysis'  
Composite(AA.AuN='sue dumais')

**Zeichenfolgenattribut: Exakter Einzelwert** (nur Übereinstimmungen mit kanonischen Werten)  
Ti=='indexing by latent semantic analysis'  
Composite(AA.AuN=='susan t dumais')
     
**Zeichenfolgenattribut: Präfixwert**   
Ti='indexing by latent seman'...  
Composite(AA.AuN='sue du'...)

**Numerisches Attribut: Einzelwert**  
Y=2010
 
**Numerisches Attribut: Bereichswert**  
Y>2005  
Y>=2005  
Y<2010  
Y<=2010  
Y=\[2010, 2012\) (enthält nur den linken Begrenzungswert: 2010, 2011)  
Y=\[2010, 2012\] (enthält beide Begrenzungswerte: 2010, 2011, 2012)
 
**Numerisches Attribut: Präfixwert**  
Y='19'... (jeder beliebige numerische Wert, der mit 19 beginnt) 
 
**Datumsattribut: Einzelwert**  
D='2010-02-04'

**Datumsattribut: Bereichswert**  
D>'2010-02-03'  
D=['2010-02-03','2010-02-05']

**And/Or-Abfragen:**  
And(Y=1985, Ti='disordered electronic systems')  
Or(Ti='disordered electronic systems', Ti='fault tolerance principles and practice')  
And(Or(Y=1985,Y=2008), Ti='disordered electronic systems')
 
**Zusammengesetzte Abfragen:**  
Um Komponenten eines zusammengesetzten Attributs abzufragen, müssen Sie den Teil des Abfrageausdrucks, der sich auf das zusammengesetzte Attribut bezieht, in die Composite()-Funktion einfügen. 

Um beispielsweise nach Dokumenten nach Autorennamen zu suchen, verwenden Sie die folgende Abfrage:
```
Composite(AA.AuN='mike smith')
```
<br>Um nach Dokumenten eines bestimmten Autors zu suchen, während der Autor an einer bestimmten Institution war, verwenden Sie die folgende Abfrage:
```
Composite(And(AA.AuN='mike smith',AA.AfN='harvard university'))
```
<br>Die Composite()-Funktion verknüpft die beiden Teile des zusammengesetzten Attributs. Das bedeutet, dass nur Dokumente abgerufen werden, bei denen einer der Autoren „Mike Smith“ ist, während er in Harvard war. 

Um nach Dokumenten eines bestimmten Autors in Zuordnung zu (anderen) Autoren einer bestimmten Institution zu suchen, verwenden Sie die folgende Abfrage:
```
And(Composite(AA.AuN='mike smith'),Composite(AA.AfN='harvard university'))
```
<br>In dieser Version erhalten wir alle Dokumente, bei denen einer der Autoren „Mike Smith“ ist und eine seiner Zuordnungen „Harvard“ lautet, da „Composite()“ auf den Autor und die Zugehörigkeit einzeln vor „And()“ angewendet wird. Dies ähnelt dem vorherigen Abfragebeispiel, aber es ist nicht dasselbe.

Betrachten Sie ganz allgemein das folgende Beispiel: Wir haben ein zusammengesetztes Attribut C mit zwei Komponenten A und B. Eine Entität kann mehrere Werte für C haben. Dies sind unsere Entitäten:
```
E1: C={A=1, B=1}  C={A=1,B=2}  C={A=2,B=3}
E2: C={A=1, B=3}  C={A=3,B=2}
```

<br>Die Abfrage 
```
Composite(And(C.A=1, C.B=2))
```

<br>entspricht nur Entitäten, die einen Wert für C haben, bei dem die Komponente C.A 1 und die Komponente C.B 2 ist. Nur E1 stimmt mit dieser Abfrage überein.

Die Abfrage 
```
And(Composite(C.A=1), Composite(C.B=2))
```
<br>entspricht Entitäten, die einen Wert für C haben, bei dem C.A 1 ist, und außerdem einen Wert für C haben, bei dem C.B 2 ist. Sowohl E1 als auch E2 entsprechen dieser Abfrage.

Hinweis:  
- Sie können nicht auf einen Teil eines zusammengesetzten Attributs außerhalb einer Composite()-Funktion verweisen.
- Sie können nicht auf Teile von zwei verschiedenen zusammengesetzten Attributen innerhalb derselben Composite()-Funktion verweisen.
- Sie können nicht auf ein Attribut verweisen, das nicht Teil eines zusammengesetzten Attributs innerhalb einer Composite()-Funktion ist.
