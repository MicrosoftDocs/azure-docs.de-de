---
title: Festlegen einer Gültigkeitsdauer für den Ablauf von Daten in Azure Cosmos DB
description: Mit TTL bietet Microsoft Azure Cosmos DB die Möglichkeit, im System vorhandene Dokumente nach einem bestimmten Zeitraum automatisch zu löschen.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/04/2020
ms.reviewer: sngun
ms.openlocfilehash: cf9d0aea9ab9e79a5f184a42e1bb785b6fb870a7
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360087"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Gültigkeitsdauer (TTL) in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Mit der **Gültigkeitsdauer** (Time to Live, TTL) bietet Azure Cosmos DB die Möglichkeit, Elemente nach einem bestimmten Zeitraum automatisch aus einem Container zu löschen. Standardmäßig können Sie die Gültigkeitsdauer auf der Containerebene festlegen und den Wert für einzelne Elemente außer Kraft setzen. Wenn Sie eine Gültigkeitsdauer für einen Container oder ein Element festgelegt haben, werden die betroffenen Elemente nach Ablauf der angegebenen Zeit (seit der letzten Änderung des Elements) automatisch von Azure Cosmos DB gelöscht. Der Wert für die Gültigkeitsdauer wird in Sekunden konfiguriert. Wenn Sie eine Gültigkeitsdauer konfigurieren, löscht das System die abgelaufenen Elemente automatisch auf der Grundlage des entsprechenden Werts, ohne dass ein Löschvorgang explizit von der Clientanwendung initiiert werden muss. Der maximale Wert für TTL ist 2147483647.

Das Löschen abgelaufener Elemente ist ein im Hintergrund ausgeführter Vorgang, bei dem die verbleibenden [Anforderungseinheiten](request-units.md) (also die nicht von Benutzeranforderungen verbrauchten Anforderungseinheiten) verbraucht werden. Wenn der Container mit Anforderungen überlastet ist und nicht genügend Anforderungseinheiten (RUs) verfügbar sind, wird die Datenlöschung auch nach Ablauf der Lebensdauer verzögert. Die Daten werden gelöscht, wenn genügend Anforderungseinheiten zum Ausführen des Löschvorgangs verfügbar sind. Obwohl die Datenlöschung verzögert wird, werden die Daten nach Ablauf der Lebensdauer über keine Abfragen (über keine API) zurückgegeben.

> Dieser Inhalt bezieht sich auf die Gültigkeitsdauer des Azure Cosmos DB-Transaktionsspeichers. Wenn Sie nach der Gültigkeitsdauer des Analysespeichers suchen, die NoETL-HTAP-Szenarien über [Azure Synapse Link](./synapse-link.md) ermöglicht, klicken Sie [hier](./analytical-store-introduction.md#analytical-ttl).

## <a name="time-to-live-for-containers-and-items"></a>Gültigkeitsdauer für Container und Elemente

Der Wert für die Gültigkeitsdauer wird in Sekunden festgelegt und als Deltawert relativ zum Zeitpunkt der letzten Änderung des Elements interpretiert. Die Gültigkeitsdauer kann für einen Container oder für ein Element im Container festgelegt werden:

1. **Gültigkeitsdauer für einen Container** (festgelegt mithilfe von `DefaultTimeToLive`):

   - Wenn diese Angabe fehlt (oder auf Null festgelegt ist), läuft die Gültigkeit von Elementen nicht automatisch ab.

   - Ist die Angabe vorhanden und der Wert auf „-1“ (unendlich) festgelegt, läuft die Gültigkeit von Elementen standardmäßig nicht ab.

   - Ist die Angabe vorhanden und der Wert auf eine beliebige Zahl *n* *ungleich null* festgelegt, läuft die Gültigkeit von Elementen *n* Sekunden nach dem Zeitpunkt der letzten Änderung ab.

2. **Gültigkeitsdauer für ein Element** (festgelegt mithilfe von `ttl`):

   - Diese Eigenschaft ist nur relevant, wenn `DefaultTimeToLive` für den übergeordneten Container vorhanden und nicht auf Null festgelegt ist.

   - Ist die Angabe vorhanden, setzt sie den Wert `DefaultTimeToLive` des übergeordneten Containers außer Kraft.

## <a name="time-to-live-configurations"></a>Konfigurationen für die Gültigkeitsdauer

- Wenn die Gültigkeitsdauer für einen Container auf *n* festgelegt ist, läuft die Gültigkeit der Elemente in diesem Container nach *n* Sekunden ab.  Falls der gleiche Container Elemente enthält, für die eine eigene Gültigkeitsdauer festgelegt ist oder für die durch den Wert „-1“ angegeben wurde, dass ihre Gültigkeit nicht abläuft, oder wenn einige Elemente die Gültigkeitsdauereinstellung mit einer anderen Zahl außer Kraft gesetzt haben, läuft die Gültigkeit dieser Elemente auf der Grundlage des eigenen für die Gültigkeitsdauer konfigurierten Werts ab.

- Ist für einen Container keine Gültigkeitsdauer festgelegt, wird die für ein Element in diesem Container festgelegte Gültigkeitsdauer ignoriert.

- Wurde die Gültigkeitsdauer für einen Container auf „-1“ festgelegt, läuft die Gültigkeit eines Elements in diesem Container, dessen Gültigkeitsdauer auf „n“ festgelegt ist, nach n Sekunden ab. Die Gültigkeit der übrigen Elemente läuft dagegen nicht ab.

## <a name="examples"></a>Beispiele

Dieser Abschnitt zeigt einige Beispiele mit anderen Gültigkeitsdauerwerten, die Containern und Elementen zugewiesen werden:

### <a name="example-1"></a>Beispiel 1

Gültigkeitsdauer (TTL) für Container wird auf null festgelegt (DefaultTimeToLive = null)

|Gültigkeitsdauer (TTL) für Element| Ergebnis|
|---|---|
|ttl = null|Gültigkeitsdauer (TTL) ist deaktiviert. Das Element läuft nie ab (Standardeinstellung).|
|ttl = -1|Gültigkeitsdauer (TTL) ist deaktiviert. Das Element läuft nie ab.|
|ttl = 2000|Gültigkeitsdauer (TTL) ist deaktiviert. Das Element läuft nie ab.|

### <a name="example-2"></a>Beispiel 2

Gültigkeitsdauer (TTL) für Container wird auf -1 festgelegt (DefaultTimeToLive = -1)

|Gültigkeitsdauer (TTL) für Element| Ergebnis|
|---|---|
|ttl = null|Gültigkeitsdauer (TTL) ist aktiviert. Das Element läuft nie ab (Standardeinstellung).|
|ttl = -1|Gültigkeitsdauer (TTL) ist aktiviert. Das Element läuft nie ab.|
|ttl = 2000|Gültigkeitsdauer (TTL) ist aktiviert. Das Element läuft nach 2000 Sekunden ab.|

### <a name="example-3"></a>Beispiel 3

Gültigkeitsdauer (TTL) für Container wird auf 1000 festgelegt (DefaultTimeToLive = 1000)

|Gültigkeitsdauer (TTL) für Element| Ergebnis|
|---|---|
|ttl = null|Gültigkeitsdauer (TTL) ist aktiviert. Das Element läuft nach 1000 Sekunden ab (Standardeinstellung).|
|ttl = -1|Gültigkeitsdauer (TTL) ist aktiviert. Das Element läuft nie ab.|
|ttl = 2000|Gültigkeitsdauer (TTL) ist aktiviert. Das Element läuft nach 2000 Sekunden ab.|

## <a name="next-steps"></a>Nächste Schritte

Der folgende Artikel enthält Informationen zum Konfigurieren der Gültigkeitsdauer:

- [Konfigurieren der Gültigkeitsdauer in Azure Cosmos DB](how-to-time-to-live.md)