---
title: Festlegen einer Gültigkeitsdauer für den Ablauf von Daten in Azure Cosmos DB
description: Mit TTL bietet Microsoft Azure Cosmos DB die Möglichkeit, im System vorhandene Dokumente nach einem bestimmten Zeitraum automatisch zu löschen.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 692e0ec575904ff0a70b8c73268d2df62e776bb6
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978789"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Gültigkeitsdauer (TTL) in Azure Cosmos DB 

Mit der **Gültigkeitsdauer** (Time to Live, TTL) bietet Azure Cosmos DB die Möglichkeit, Elemente nach einem bestimmten Zeitraum automatisch aus einem Container zu löschen. Standardmäßig können Sie die Gültigkeitsdauer auf der Containerebene festlegen und den Wert für einzelne Elemente außer Kraft setzen. Wenn Sie eine Gültigkeitsdauer für einen Container oder ein Element festgelegt haben, werden die betroffenen Elemente nach Ablauf der angegebenen Zeit (seit der letzten Änderung des Elements) automatisch von Azure Cosmos DB gelöscht. Der Wert für die Gültigkeitsdauer wird in Sekunden konfiguriert. Wenn Sie eine Gültigkeitsdauer konfigurieren, löscht das System die abgelaufenen Elemente automatisch auf der Grundlage des entsprechenden Werts, ohne dass ein Löschvorgang explizit von der Clientanwendung initiiert werden muss.

## <a name="time-to-live-for-containers-and-items"></a>Gültigkeitsdauer für Container und Elemente

Der Wert für die Gültigkeitsdauer wird in Sekunden festgelegt und als Deltawert relativ zum Zeitpunkt der letzten Änderung des Elements interpretiert. Die Gültigkeitsdauer kann für einen Container oder für ein Element im Container festgelegt werden:

1. **Gültigkeitsdauer für einen Container** (festgelegt mithilfe von `DefaultTimeToLive`):

   - Wenn diese Angabe fehlt (oder auf Null festgelegt ist), läuft die Gültigkeit von Elementen nicht automatisch ab.

   - Ist die Angabe vorhanden und der Wert auf „-1“ (unendlich) festgelegt, läuft die Gültigkeit von Elementen standardmäßig nicht ab.

   - Ist die Angabe vorhanden und der Wert auf eine beliebige Zahl *n* festgelegt, läuft die Gültigkeit von Elementen *n* Sekunden nach dem Zeitpunkt der letzten Änderung ab.

2. **Gültigkeitsdauer für ein Element** (festgelegt mithilfe von `ttl`):

   - Diese Eigenschaft ist nur relevant, wenn `DefaultTimeToLive` für den übergeordneten Container vorhanden und nicht auf Null festgelegt ist.

   - Ist die Angabe vorhanden, setzt sie den Wert `DefaultTimeToLive` des übergeordneten Containers außer Kraft.

## <a name="time-to-live-configurations"></a>Konfigurationen für die Gültigkeitsdauer

* Wenn die Gültigkeitsdauer für einen Container auf *n* festgelegt ist, läuft die Gültigkeit der Elemente in diesem Container nach *n* Sekunden ab.  Falls der gleiche Container Elemente enthält, für die eine eigene Gültigkeitsdauer festgelegt ist oder für die durch den Wert „-1“ angegeben wurde, dass ihre Gültigkeit nicht abläuft, oder wenn einige Elemente die Gültigkeitsdauereinstellung mit einer anderen Zahl außer Kraft gesetzt haben, läuft die Gültigkeit dieser Elemente auf der Grundlage des eigenen für die Gültigkeitsdauer konfigurierten Werts ab. 

* Ist für einen Container keine Gültigkeitsdauer festgelegt, wird die für ein Element in diesem Container festgelegte Gültigkeitsdauer ignoriert. 

* Wurde die Gültigkeitsdauer für einen Container auf „-1“ festgelegt, läuft die Gültigkeit eines Elements in diesem Container, dessen Gültigkeitsdauer auf „n“ festgelegt ist, nach n Sekunden ab. Die Gültigkeit der übrigen Elemente läuft dagegen nicht ab. 

Das Löschen von Elementen auf der Grundlage der Gültigkeitsdauer ist kostenlos. Bei Elementen, die nach Ablauf der Gültigkeitsdauer gelöscht werden, fallen keine zusätzlichen Kosten an (sprich: es werden keine zusätzlichen RUs genutzt).

## <a name="next-steps"></a>Nächste Schritte

Der folgende Artikel enthält Informationen zum Konfigurieren der Gültigkeitsdauer:

* [Konfigurieren der Gültigkeitsdauer in Azure Cosmos DB](how-to-time-to-live.md)
