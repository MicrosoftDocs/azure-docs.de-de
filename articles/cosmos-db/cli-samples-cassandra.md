---
title: 'Azure CLI-Beispiele für Azure Cosmos DB: Cassandra-API'
description: 'Azure CLI-Beispiele für Azure Cosmos DB: Cassandra-API'
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2dbae196b981b792b47e6c12e500398f772d8451
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342129"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-cassandra-api"></a>Azure CLI-Beispiele für Azure Cosmos DB: Cassandra-API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Die folgende Tabelle enthält Links zu Azure CLI-Beispielskripts für Azure Cosmos DB. Navigieren Sie über die Links auf der rechten Seite zu API-spezifischen Beispielen. Allgemeine Beispiele sind in allen APIs identisch. Referenzseiten für alle CLI-Befehle für Azure Cosmos DB sind in der [Referenz zur Azure CLI](/cli/azure/cosmosdb) verfügbar. Die CLI-Skriptbeispiele für Azure Cosmos DB finden Sie auch im [GitHub-Repository zur Azure Cosmos DB-CLI](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

Für diese Beispiele wird mindestens Version 2.12.1 der Azure CLI benötigt. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

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

## <a name="cassandra-api-samples"></a>Cassandra-API-Beispiele

|Aufgabe | BESCHREIBUNG |
|---|---|
| [Erstellen eines Kontos, eines Keyspace und einer Tabelle in Azure Cosmos](scripts/cli/cassandra/create.md?toc=%2fcli%2fazure%2ftoc.json)| Erstellen eines Kontos, eines Keyspace und einer Tabelle in Azure Cosmos DB für die Cassandra-API |
| [Konto, Keyspace und Tabelle mit Autoskalierung in Azure Cosmos erstellen](scripts/cli/cassandra/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| Erstellen eines Kontos, eines Keyspace und einer Tabelle mit Autoskalierung in Azure Cosmos DB für die Cassandra-API |
| [Durchsatzvorgänge](scripts/cli/cassandra/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Lesen, Aktualisieren und Migrieren zwischen automatisch skaliertem und standardmäßigem Durchsatz in einem Keyspace und einer Tabelle|
| [Sperren von Ressourcen für die Löschung](scripts/cli/cassandra/lock.md?toc=%2fcli%2fazure%2ftoc.json)| Verhindern des Löschens von Ressourcen mit Ressourcensperren|
|||
