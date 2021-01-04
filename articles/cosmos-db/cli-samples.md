---
title: 'Azure CLI-Beispiele für Azure Cosmos DB: Core-API (SQL)'
description: 'Azure CLI-Beispiele für Azure Cosmos DB: Core-API (SQL)'
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7c7baf4de56edd25537f93095bfa6ae0402214e2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93073178"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-core-sql-api"></a>Azure CLI-Beispiele für Azure Cosmos DB: Core-API (SQL)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Die folgende Tabelle enthält Links zu Azure CLI-Beispielskripts für Azure Cosmos DB. Navigieren Sie über die Links auf der rechten Seite zu API-spezifischen Beispielen. Allgemeine Beispiele sind in allen APIs identisch. Referenzseiten für alle CLI-Befehle für Azure Cosmos DB sind in der [Referenz zur Azure CLI](/cli/azure/cosmosdb) verfügbar. Die CLI-Skriptbeispiele für Azure Cosmos DB finden Sie auch im [GitHub-Repository zur Azure Cosmos DB-CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Für diese Beispiele wird mindestens Version 2.12.1 der Azure CLI benötigt. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

Azure CLI-Beispiele für andere APIs finden Sie unter [CLI-Beispiele für die Cassandra-API](cli-samples-cassandra.md), [CLI-Beispiele für die MongoDB-API](cli-samples-mongodb.md), [CLI-Beispiele für die Gremlin-API](cli-samples-gremlin.md) und [CLI-Beispiele für die Tabellen-API](cli-samples-table.md).

## <a name="common-samples"></a>Allgemeine Beispiele

Diese Beispiele gelten für alle Azure Cosmos DB-APIs.

|Aufgabe | BESCHREIBUNG |
|---|---|
| [Hinzufügen von Failoverregionen](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Hinzufügen einer Region, Ändern der Failoverpriorität und Auslösen eines manuellen Failovers|
| [Kontoschlüssel und Verbindungszeichenfolgen](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Auflisten von Kontoschlüsseln und schreibgeschützten Schlüsseln, erneutes Generieren von Schlüsseln und Auflisten von Verbindungszeichenfolgen|
| [Sichern mit einer IP-Firewall](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Erstellen eines Cosmos-Kontos mit konfigurierter IP-Firewall|
| [Sichern eines neuen Kontos mit Dienstendpunkten](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Erstellen eines Cosmos-Kontos und Sichern des Kontos mit Dienstendpunkten|
| [Sichern eines vorhandenen Kontos mit Dienstendpunkten](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Aktualisieren eines Cosmos-Kontos beim Konfigurieren des Subnetzes, um es mit Dienstendpunkten zu sichern|
|||

## <a name="core-sql-api-samples"></a>Core-API-Beispiele (SQL)

|Aufgabe | BESCHREIBUNG |
|---|---|
| [Erstellen eines Kontos, einer Datenbank und eines Containers in Azure Cosmos DB](scripts/cli/sql/create.md?toc=%2fcli%2fazure%2ftoc.json)| Erstellen eines Kontos, einer Datenbank und eines Containers in Azure Cosmos DB für die Core-API (SQL) |
| [Konto, Datenbank und Container mit Autoskalierung in Azure Cosmos erstellen](scripts/cli/sql/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Erstellen eines Kontos, einer Datenbank und eines Containers mit Autoskalierung in Azure Cosmos DB für die Core-API (SQL) |
| [Durchsatzvorgänge](scripts/cli/sql/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Lesen, Aktualisieren und Migrieren zwischen automatisch skaliertem und standardmäßigem Durchsatz in einer Datenbank und einem Container|
| [Sperren von Ressourcen für die Löschung](scripts/cli/sql/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Verhindern des Löschens von Ressourcen mit Ressourcensperren|
|||
