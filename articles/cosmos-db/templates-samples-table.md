---
title: Resource Manager-Vorlagen für die Tabellen-API von Azure Cosmos DB
description: Verwenden Sie Azure Resource Manager-Vorlagen zum Erstellen und Konfigurieren der Tabellen-API von Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mjbrown
ms.openlocfilehash: e8fb47cc54d520d44085c8d007a4dfe92972d1d4
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340582"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Verwalten von Ressourcen für die Tabellen-API von Azure Cosmos DB mithilfe von Azure Resource Manager-Vorlagen
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

In diesem Artikel erfahren Sie, wie Sie Ihre Azure Cosmos DB-Konten, -Datenbanken und -Container mithilfe von Azure Resource Manager-Vorlagen bereitstellen und verwalten.

Dieser Artikel enthält nur Beispiele für Tabellen-API-Konten. Weitere Beispiele für andere Arten von API-Konten finden Sie in den Artikeln zur Verwendung von Azure Resource Manager-Vorlagen mit der Azure Cosmos DB-API für [Cassandra](./templates-samples-cassandra.md), [Gremlin](./templates-samples-gremlin.md), [MongoDB](./templates-samples-mongodb.md) und [SQL](./manage-with-templates.md).

> [!IMPORTANT]
>
> * Die Länge von Kontonamen ist auf 44 Zeichen (ausschließlich Kleinbuchstaben) beschränkt.
> * Sie müssen die Vorlage mit aktualisierter Angabe der RU/s erneut bereitstellen, um die Durchsatzwerte ändern zu können.
> * Wenn Sie einem Azure Cosmos-Konto Speicherorte hinzufügen oder aus ihm entfernen, können Sie nicht gleichzeitig andere Eigenschaften ändern. Diese Vorgänge müssen separat ausgeführt werden.

Wenn Sie eine der unten aufgeführten Azure Cosmos DB-Ressourcen erstellen möchten, kopieren Sie die folgende Beispielvorlage in eine neue JSON-Datei. Optional können Sie eine JSON-Parameterdatei erstellen, die beim Bereitstellen mehrerer Instanzen derselben Ressource mit unterschiedlichen Namen und Werten verwendet werden soll. Für das Bereitstellen von Azure Resource Manager-Vorlagen stehen Ihnen viele Möglichkeiten zur Verfügung, beispielsweise das [Azure-Portal](../azure-resource-manager/templates/deploy-portal.md), die [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) und [GitHub](../azure-resource-manager/templates/deploy-to-azure-button.md).

> [!TIP]
> Aktivieren Sie im Azure-Portal den Durchsatz auf Kontoebene, um bei Verwendung der Tabellen-API die gemeinsame Nutzung des Durchsatzes zu aktivieren.

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-table-with-autoscale-throughput"></a>Azure Cosmos-Konto für eine Tabelle mit automatisch skaliertem Durchsatz

Mit dieser Vorlage wird ein Azure Cosmos-Konto für die Tabellen-API mit einer Tabelle mit automatisch skaliertem Durchsatz erstellt. Diese Vorlage ist auch für die Bereitstellung mit nur einem Klick im Katalog der Azure-Schnellstartvorlagen verfügbar.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Bereitstellen in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-table-with-standard-provisioned-throughput"></a>Azure Cosmos-Konto für Tabellen mit standardmäßig bereitgestelltem Durchsatz

Mit dieser Vorlage wird ein Azure Cosmos-Konto für die Tabellen-API mit einer Tabelle mit Standarddurchsatz erstellt. Diese Vorlage ist auch für die Bereitstellung mit nur einem Klick im Katalog der Azure-Schnellstartvorlagen verfügbar.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Bereitstellen in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

## <a name="next-steps"></a>Nächste Schritte

Hier sind einige zusätzlichen Ressourcen:

* [Dokumentation zu Azure Resource Manager](../azure-resource-manager/index.yml)
* [Schema von Azure Cosmos DB-Ressourcenanbietern](/azure/templates/microsoft.documentdb/allversions)
* [Schnellstartvorlagen für Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Beheben gängiger Azure-Bereitstellungsfehler mit Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)