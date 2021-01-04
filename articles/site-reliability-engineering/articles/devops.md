---
title: 'Häufig gestellte Fragen: SRE und DevOps | Microsoft-Dokumentation'
titleSuffix: Azure
description: 'Häufig gestellte Fragen: Grundlegendes zur Beziehung zwischen SRE und DevOps'
author: dnblankedelman
manager: ScottCa
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 09/14/2020
ms.author: dnb
ms.openlocfilehash: 62872443c386c45d9821053dc901ad8e0055b74f
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92674277"
---
# <a name="frequently-asked-questions-whats-the-relationship-between-sre-and-devops"></a>Häufig gestellte Fragen: In welcher Beziehung stehen SRE und DevOps zueinander?

Es gibt eine Reihe allgemeiner Fragen hinsichtlich der Beziehung zwischen dem Websitezuverlässigkeits-Engineering (Site Reliability Engineering, SRE) und DevOps. „Welche Gemeinsamkeiten gibt es? Inwiefern unterscheiden sie sich? Können in einer Organisation beide verwendet werden?“ Dieser Artikel enthält einige der Antworten der SRE- und DevOps-Communitys, die diese Beziehung näher erläutern.

## <a name="how-are-they-the-same"></a>Welche Gemeinsamkeiten gibt es?

SRE und DevOps sind beide moderne Vorgehensweisen, die erstellt und entwickelt wurden, um folgende Herausforderungen zu meistern:

- steigende Komplexität von Produktionsumgebungen und Entwicklungsprozessen
- erhöhte wirtschaftliche Abhängigkeit von der Zuverlässigkeit dieser Umgebungen
- Umgehung des Problems, dass die Arbeitskraft nicht linear mit der Größe der Umgebung skaliert werden kann
- schnellere Entwicklung bei gleichbleibender Betriebsstabilität

Bei beiden Vorgehensweisen werden wichtige Aspekte berücksichtigt, die für die Bewältigung dieser Herausforderungen wichtig sind. Dazu gehören Überwachung und Einblick, Automatisierung, Dokumentation und Softwareentwicklungstools für die Zusammenarbeit.

Die Tools und Arbeitsbereiche von SRE und DevOps überschneiden sich. Laut _The Site Reliability Workbook_ „vertrauen SRE und DevOps auf dieselben Dinge, allerdings aus unterschiedlichen Gründen.“

## <a name="three-different-ways-to-compare-the-two-operations-practices"></a>Drei verschiedene Möglichkeiten zum Vergleichen der Vorgehensweisen

Die Ähnlichkeiten zwischen SRE und DevOps sind eindeutig. Interessanter ist es, inwiefern sich die beiden unterscheiden. Hier bieten wir drei Ansätze, um sich mit der Beziehung zwischen den beiden Vorgehensweisen auseinanderzusetzen. Vielleicht sind Sie anderer Meinung, aber diese Ansätze bieten jeweils einen guten Ausgangspunkt für eine Diskussion.

### <a name="class-sre-implements-interface-devops"></a>„class SRE implements interface DevOps“

Im ersten Kapitel des Buchs _The Site Reliability Workbook_ , das in unserer [Ressourcenbuchliste](../resources/books.md) enthalten ist, werden SRE und DevOps erläutert. In diesem Kapitel wird der Satz „class SRE implements interface DevOps“ als Untertitel verwendet. Dieser (von Entwicklern geprägte) Satz bedeutet, dass SRE als bestimmte Implementierung der DevOps-Philosophie betrachtet werden kann. Im Kapitel wird erläutert, dass es im Gegensatz zu den präskriptiven Anweisungen beim SRE im Zusammenhang mit „DevOps keine detaillierten Informationen zum Ausführen von Vorgängen gibt.“ Eine mögliche Antwort auf die Frage hinsichtlich der Beziehung zwischen den beiden ist also, dass SRE als eine von vielen möglichen Implementierungen von DevOps angesehen werden kann.

### <a name="sre-is-to-reliability-as-devops-is-to-delivery"></a>Zuverlässigkeit ist für SRE das, was die Bereitstellung für DevOps ist

Dieser Vergleich ist etwas schwammig, da es mehrere Definitionen sowohl für SRE als auch für DevOps gibt. Dennoch ist er sehr nützlich Anfangs kommt folgende Frage auf: „Wie würden Sie die zentralen Ziele der beiden Vorgehensweisen in ein oder zwei Wörtern beschreiben?“

In der [Dokumentation zum Websitezuverlässigkeits-Engineering](../index.yml) findet man für SRE die folgende Definition:

> Websitezuverlässigkeits-Engineering (Site Reliability Engineering, SRE) ist ein Engineeringbereich, bei dem es darum geht, Organisationen beim nachhaltigen Erzielen der geeigneten Zuverlässigkeitsebene für ihre Systeme, Dienste und Produkte zu unterstützen.

Infolgedessen könnte man einfach sagen, dass der Kerngedanke beim SRE die Zuverlässigkeit ist. Dies wird noch deutlicher, da dieses Wort auch in der vollständigen Bezeichnung vorkommt.

Im [Azure DevOps-Ressourcencenter](/azure/devops/learn/) lautet die Definition für DevOps wie folgt:

> DevOps vereint Personen, Prozesse und Technologien, damit Kunden kontinuierlich hochwertige Produkte erhalten.

Daher kann man sagen, dass der Kerngedanke von DevOps die Bereitstellung ist.

Aus diesem Grund „ist SRE für die Zuverlässigkeit das, was DevOps für die Bereitstellung ist.“

### <a name="direction-of-attention"></a>Fokussierung

Diese Antwort ist ein Zitat bzw. eine Paraphrase aus einem Beitrag von Thomas Limoncelli in dem in unserer [Ressourcenbuchliste](../resources/books.md) aufgeführten Buch _Seeking SRE_ . Er merkt an, dass sich DevOps-Techniker größtenteils auf die SDLC-Pipeline (Vorgehensmodell zur Softwareentwicklung) und gelegentlich die Verantwortlichkeiten in Bezug auf Produktionsvorgänge konzentrieren, während das Hauptaugenmerk beim SRE auf Produktionsvorgängen und gelegentlich auf der SDLC-Pipeline liegt.

Noch wichtiger ist jedoch das von ihm erstellte Diagramm mit dem Softwareentwicklungsprozess auf der einen und den Produktionsvorgängen auf der anderen Seite. Beide sind durch die übliche Pipeline verbunden, die erstellt wurde, um den Code von einem Entwickler zu übermitteln, die gewünschte Anzahl von Tests und Phasen zu durchlaufen und diesen Code dann in die Produktionsumgebung zu verschieben.

Limoncelli stellt fest, dass DevOps-Techniker in der Entwicklungsumgebung beginnen und die Schritte für die Produktion automatisieren. Wenn sie damit fertig sind, optimieren sie Engpässe.

SREs hingegen konzentrieren sich auf die Produktionsvorgänge und befassen sich ausführlich mit der Pipeline, um das Endergebnis weiter verbessern zu können (im Grunde genommen in entgegengesetzter Richtung).

Genau dieser Unterschied hinsichtlich der Richtung kann beim Unterscheiden zwischen SRE und DevOps helfen.

## <a name="coexistence-in-the-same-organization"></a>Verwendung in derselben Organisation

Die letzte Frage, die wir beantworten möchten, lautet wie folgt: „Ist es möglich, sowohl SRE als auch DevOps in derselben Organisation zu nutzen?“

Die Antwort auf diese Frage lautet: Ja!

Wir hoffen, dass diese Antworten deutlich gemacht haben, inwiefern sich die beiden Vorgehensweisen ähneln bzw. unterscheiden. Organisationen mit etablierten DevOps-Methoden können in geringem Umfang mit SRE-Methoden experimentieren (z. B. SLIs und SLOs testen), ohne dabei Stellen oder gar ganze Teams für das Websitezuverlässigkeits-Engineering einrichten zu müssen. Dies ist ein gängiges Muster für die Einführung von SRE-Methoden.

## <a name="next-steps"></a>Nächste Schritte

Möchten Sie noch mehr über SRE oder DevOps erfahren? Lesen Sie die [Dokumentation zum Websitezuverlässigkeits-Engineering](../index.yml) und die Informationen im [Azure DevOps-Ressourcencenter](/azure/devops/learn/).