---
title: Verschieben eines Azure Cosmos DB-Kontos in eine andere Region
description: Hier erfahren Sie, wie Sie ein Azure Cosmos DB-Konto in eine andere Region verschieben.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: 29b5c5d3cf55cd11fe505c0d9ab9b894dc2ad267
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342027"
---
# <a name="move-an-azure-cosmos-db-account-to-another-region"></a>Verschieben eines Azure Cosmos DB-Kontos in eine andere Region
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In diesem Artikel wird die Vorgehensweise für folgende Vorgänge beschrieben:

- Verschieben eines Bereichs, in dem Daten in Azure Cosmos DB repliziert werden.
- Migrieren von Kontometadaten (Azure Resource Manager) und Daten aus einer Region in eine andere.

## <a name="move-data-from-one-region-to-another"></a>Verschieben von Daten aus einer Region in eine andere

Azure Cosmos DB unterstützt nativ die Datenreplikation, sodass das Verschieben von Daten aus einer Region in eine andere ein einfacher Vorgang ist. Sie können ihn über das Azure-Portal, mit Azure PowerShell oder mithilfe der Azure CLI ausführen. Dazu sind die folgenden Schritte erforderlich:

1. Fügen Sie dem Konto eine neue Region hinzu.

    Informationen zum Hinzufügen einer neuen Region zu einem Azure Cosmos DB-Konto finden Sie unter [Hinzufügen/Entfernen von Regionen zu einem Azure Cosmos DB-Konto](how-to-manage-database-account.md#addremove-regions-from-your-database-account).

1. Führen Sie ein manuelles Failover in die neue Region aus.

    Wenn die zu entfernende Region zurzeit die Schreibregion für das Konto ist, müssen Sie ein Failover auf die neue Region starten, die im vorherigen Schritt hinzugefügt wurde. Dies ist ein Vorgang ohne Ausfallzeit. Wenn Sie eine Leseregion in ein Konto mit mehreren Regionen verschieben, können Sie diesen Schritt überspringen. 
    
    Informationen zum Starten eines Failovers finden Sie unter [Ausführen eines manuellen Failovers für ein Azure Cosmos-Konto](how-to-manage-database-account.md#manual-failover).

1. Entfernen Sie die ursprüngliche Region.

    Informationen zum Entfernen einer Region aus einem Azure Cosmos DB-Konto finden Sie unter [Hinzufügen/Entfernen von Regionen aus Ihrem Azure Cosmos DB-Konto](how-to-manage-database-account.md#addremove-regions-from-your-database-account).

## <a name="migrate-azure-cosmos-db-account-metadata"></a>Migrieren von Azure Cosmos DB-Kontometadaten

Azure Cosmos DB unterstützt das Migrieren von Kontometadaten aus einer Region in eine andere nicht nativ. Wenn Sie die Kontometadaten und die Kundendaten aus einer Region in eine andere migrieren möchten, müssen Sie ein neues Konto in der gewünschten Region erstellen und dann die Daten manuell kopieren. 

Bei einer Migration nahezu ohne Ausfallzeit für die SQL-API ist die Verwendung des [Änderungsfeeds](change-feed.md) oder eines Tools erforderlich, von dem sie verwendet wird. Wenn Sie die MongoDB-API, die Cassandra-API oder eine andere API migrieren oder mehr über Optionen zum Migrieren von Daten zwischen Konten erfahren möchten, lesen Sie [Optionen zum Migrieren von lokalen oder Clouddaten zu Azure Cosmos DB](cosmosdb-migrationchoices.md). 

Die folgenden Schritte veranschaulichen, wie ein Azure Cosmos DB-Konto für die SQL-API und die zugehörigen Daten aus einer Region in eine andere migriert werden:

1. Erstellen Sie ein neues Azure Cosmos DB-Konto in der gewünschten Region.

    Informationen zum Erstellen eines neuen Kontos über das Azure-Portal, PowerShell oder die Azure CLI finden Sie unter [Erstellen eines Azure Cosmos DB-Kontos](how-to-manage-database-account.md#create-an-account).

1. Erstellen Sie eine neue Datenbank und einen Container.

    Informationen zum Erstellen einer neuen Datenbank und eines Containers finden Sie unter [Erstellen eines Azure Cosmos-Containers](how-to-create-container.md).

1. Migrieren Sie Daten mithilfe des Live Data Migrator-Tools für Azure Cosmos DB.

    Informationen zum Migrieren von Daten nahezu ohne Ausfallzeit finden Sie unter [Azure Cosmos DB Live Data Migrator tool](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator) (Live Data Migrator-Tool für Azure Cosmos DB).

1. Aktualisieren Sie die Anwendungsverbindungszeichenfolge.

    Während das Live Data Migrator-Tool für Azure Cosmos DB weiterhin ausgeführt wird, aktualisieren Sie die Verbindungsinformationen in der neuen Bereitstellung Ihrer Anwendung. Sie können die Endpunkte und Schlüssel für Ihre Anwendung über das Azure-Portal abrufen.

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Zugriffssteuerung im Azure-Portal, zur Veranschaulichung der NoSQL-Datenbanksicherheit.":::

1. Leiten Sie Anforderungen an die neue Anwendung um.

    Nachdem die neue Anwendung mit Azure Cosmos DB verbunden wurde, können Sie Clientanforderungen an Ihre neue Bereitstellung umleiten.

1. Löschen Sie alle Ressourcen, die Sie nicht mehr benötigen.

    Wenn Anforderungen jetzt an die neue Instanz vollständig umgeleitet werden, können Sie das alte Azure Cosmos DB-Konto und das Live Data Migrator-Tool löschen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen und Beispiele zum Verwalten des Azure Cosmos-Kontos sowie von Datenbanken und Containern finden Sie in den folgenden Artikeln:

* [Verwalten eines Azure Cosmos-Kontos](how-to-manage-database-account.md)
* [Änderungsfeed in Azure Cosmos DB](change-feed.md)
