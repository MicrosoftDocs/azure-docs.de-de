---
title: Schritte zur Optimierung nach der Migration mit der Azure Cosmos DB-API für MongoDB
description: Dieses Dokument enthält Optimierungstechniken nach der Migration von MongoDB zur Azure Cosmos DB-API für MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/20/2020
ms.author: chrande
ms.openlocfilehash: 300177b9d5a20ce8082db57837be3ff461fd51a0
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93361655"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Schritte zur Optimierung nach der Migration bei Verwendung der Azure Cosmos DB-API für MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Nach der Migration der in einer MongoDB-Datenbank gespeicherten Daten zur Azure Cosmos DB-API für MongoDB können Sie eine Verbindung mit Azure Cosmos DB herstellen und die Daten verwalten. Diese Anleitung enthält die Schritte, die Sie nach der Migration in Erwägung ziehen sollten. Informationen zu den Migrationsschritten finden Sie im [Tutorial zum Migrieren von MongoDB zur Azure Cosmos DB-API für MongoDB](../dms/tutorial-mongodb-cosmos-db.md).

In diesem Leitfaden lernen Sie Folgendes:

- [Verbinden Ihrer Anwendung](#connect-your-application)
- [Optimieren der Indizierungsrichtlinie](#optimize-the-indexing-policy)
- [Konfigurieren der globalen Verteilung für die Azure Cosmos DB-API für MongoDB](#globally-distribute-your-data)
- [Festlegen der Konsistenzebene](#set-consistency-level)

> [!NOTE]
> Die einzige obligatorische Aufgabe nach der Migration auf Anwendungsebene ist die Änderung der Verbindungszeichenfolge in Ihrer Anwendung, damit diese auf Ihr neues Azure Cosmos DB-Konto verweist. Alle anderen Migrationsschritte sind Empfehlungen zur Optimierung.
>

## <a name="connect-your-application"></a>Verbinden Ihrer Anwendung

1. Melden Sie sich in einem neuen Fenster beim [Azure-Portal](https://www.portal.azure.com/) an.
2. Öffnen Sie im [Azure-Portal](https://www.portal.azure.com/) im linken Bereich das Menü **Alle Ressourcen** , und suchen Sie das Azure Cosmos DB-Konto, zu dem Sie Ihre Daten migriert haben.
3. Öffnen Sie das Blatt **Verbindungszeichenfolge**. Der rechte Bereich enthält alle Informationen, die Sie zum erfolgreichen Verbinden des Kontos benötigen.
4. Verwenden Sie die Verbindungsinformationen in der Konfiguration Ihrer Anwendung (oder an anderen relevanten Stellen) für die Verbindung Ihrer App zur Azure Cosmos DB-API für MongoDB.
:::image type="content" source="./media/mongodb-post-migration/connection-string.png" alt-text="Screenshot: Einstellungen für eine Verbindungszeichenfolge":::

Weitere Informationen finden Sie auf der Seite [Verbinden einer MongoDB-Anwendung mit Azure Cosmos DB](connect-mongodb-account.md).

## <a name="optimize-the-indexing-policy"></a>Optimieren der Indizierungsrichtlinie

Sämtliche Datenfelder werden standardmäßig automatisch während der Migration von Daten zu Azure Cosmos DB indiziert. In vielen Fällen ist diese standardmäßige Indizierungsrichtlinie akzeptabel. Im Allgemeinen können Sie durch das Entfernen von Indizes Schreibanforderungen optimieren, während die Standardindizierungsrichtlinie (d. h. die automatische Indizierung) Leseanforderungen optimiert.

Weitere Informationen zur Indizierung finden Sie in den Artikeln [Indizieren von Daten in der Azure Cosmos DB-API für MongoDB](mongodb-indexing.md) und [Indizierung in Azure Cosmos DB](index-overview.md).

## <a name="globally-distribute-your-data"></a>Globales Verteilen Ihrer Daten

Azure Cosmos DB ist in allen [Azure-Regionen](https://azure.microsoft.com/regions/#services) weltweit verfügbar. Nachdem Sie die Standardkonsistenzebene für Ihr Azure Cosmos DB-Konto ausgewählt haben, können Sie diesem eine oder mehrere Azure-Regionen zuordnen (je nachdem, welche Anforderungen an eine globale Verteilung bestehen). Für Hochverfügbarkeit und Geschäftskontinuität empfehlen wir immer eine Ausführung in mindestens zwei Regionen. Sehen Sie sich die Tipps zum [Optimieren der Kosten für Bereitstellungen in mehreren Regionen in Azure Cosmos DB](optimize-cost-regions.md) an.

Informationen zum globalen Verteilen Ihrer Daten finden Sie unter [Globales Verteilen von Daten in der Azure Cosmos DB-API für MongoDB](tutorial-global-distribution-mongodb.md).

## <a name="set-consistency-level"></a>Festlegen der Konsistenzebene

Azure Cosmos DB bietet 5 klar definierte [Konsistenzebenen](consistency-levels.md). Informationen zur Zuordnung zwischen den Konsistenzebenen von MongoDB und Azure Cosmos DB finden Sie unter [Konsistenzebenen und Azure Cosmos DB-APIs](./consistency-levels.md). Die Standardkonsistenzebene ist „Sitzung“. Das Ändern der Konsistenzebene ist optional, kann Ihre App jedoch optimieren. So ändern Sie die Konsistenzebene im Azure-Portal

1. Wechseln Sie zum Blatt **Standardkonsistenz** unter „Einstellungen“.
2. Wählen Sie Ihre [Konsistenzebene](consistency-levels.md) aus.

Die meisten Benutzer behalten als Konsistenzebene die Standardeinstellung „Sitzung“ bei. Sie sollten jedoch die [Kompromisse in Bezug auf Verfügbarkeit und Leistung für verschiedene Konsistenzebenen](./consistency-levels.md) beachten.

## <a name="next-steps"></a>Nächste Schritte

* [Verbinden einer MongoDB-Anwendung mit Azure Cosmos DB](connect-mongodb-account.md)
* [Herstellen einer Verbindung mit einem Azure Cosmos DB-Konto mithilfe von Studio 3T](mongodb-mongochef.md)
* [Globales Verteilen von Lesevorgängen mit der Azure Cosmos DB-API für MongoDB](mongodb-readpreference.md)
* [Ablauf von Daten mit der Azure Cosmos DB-API für MongoDB](mongodb-time-to-live.md)
* [Konsistenzebenen in Azure Cosmos DB](consistency-levels.md)
* [Indizierung in Azure Cosmos DB](index-overview.md)
* [Anforderungseinheiten in Azure Cosmos DB](request-units.md)