---
title: Azure Resource Manager-Vorlagen für die Gremlin-API von Azure Cosmos DB
description: Verwenden Sie Azure Resource Manager-Vorlagen zum Erstellen und Konfigurieren der Gremlin-API von Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 9f62399e3a1ef2a4ceaa8bdf64196bdb634fb4b6
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968889"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-azure-resource-manager-templates"></a>Verwalten von Ressourcen für die Gremlin-API von Azure Cosmos DB mithilfe von Azure Resource Manager-Vorlagen

## Erstellen der Azure Cosmos DB-API für MongoDB-Konto, -Datenbank und -Sammlung <a id="create-resource"></a>

Erstellen Sie Azure Cosmos DB-Ressourcen mithilfe einer Azure Resource Manager-Vorlage. Mit dieser Vorlage wird ein Azure Cosmos-Konto für die Gremlin-API mit zwei Graphen erstellt, die sich auf Datenbankebene einen Durchsatz von 400 RU/s teilen. Kopieren Sie die Vorlage, und stellen Sie sie wie unten gezeigt bereit, oder besuchen Sie den [Azure-Schnellstartkatalog](https://azure.microsoft.com/resources/templates/101-cosmosdb-gremlin/), und führen Sie die Bereitstellung über das Azure-Portal durch. Sie können die Vorlage auch auf Ihren lokalen Computer herunterladen oder eine neue Vorlage erstellen und den lokalen Pfad mit dem Parameter `--template-file` angeben.

[!code-json[create-cosmos-gremlin](~/quickstart-templates/101-cosmosdb-gremlin/azuredeploy.json)]

## <a name="deploy-with-azure-cli"></a>Bereitstellen über die Azure-Befehlszeilenschnittstelle

Um die Resource Manager-Vorlage mit der Azure CLI bereitzustellen, **kopieren** Sie das Skript, und klicken Sie auf **Ausprobieren**, um Azure Cloud-Shell zu öffnen. Klicken Sie zum Einfügen des Skripts mit der rechten Maustaste auf die Shell, und wählen Sie **Einfügen** aus:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first graph name: ' graph1Name
read -p 'Enter the second graph name: ' graph2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-gremlin/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   graph1Name=$graph1Name graph2Name=$graph2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Der Befehl `az cosmosdb show` zeigt das neu erstellte Azure Cosmos-Konto, nachdem es bereitgestellt wurde. Wenn Sie eine lokal installierte Version von Azure CLI anstelle von CloudShell verwenden möchten, lesen Sie den Artikel [Azure-Befehlszeilenschnittstelle (CLI)](/cli/azure/).

## Aktualisieren des Durchsatzes (RU/s) für eine Datenbank <a id="database-ru-update"></a>

Mit der folgenden Vorlage wird der Durchsatz einer Datenbank aktualisiert. Kopieren Sie die Vorlage, und stellen Sie sie wie unten gezeigt bereit, oder besuchen Sie den [Azure-Schnellstartkatalog](https://azure.microsoft.com/resources/templates/101-cosmosdb-gremlin-database-ru-update/), und führen Sie die Bereitstellung über das Azure-Portal durch. Sie können die Vorlage auch auf Ihren lokalen Computer herunterladen oder eine neue Vorlage erstellen und den lokalen Pfad mit dem Parameter `--template-file` angeben.

[!code-json[cosmosdb-gremlin-database-ru-update](~/quickstart-templates/101-cosmosdb-gremlin-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-azure-cli"></a>Bereitstellen einer Datenbankvorlage über die Azure CLI

Um die Resource Manager-Vorlage mit der Azure CLI bereitzustellen, kopieren Sie das Skript, und klicken Sie auf **Ausprobieren**, um Azure Cloud-Shell zu öffnen. Klicken Sie zum Einfügen des Skripts mit der rechten Maustaste auf die Shell, und wählen Sie **Einfügen** aus:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-gremlin-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## Aktualisieren des Durchsatzes (RU/s) für einen Graphen <a id="graph-ru-update"></a>

Mit der folgenden Vorlage wird der Durchsatz eines Graphen aktualisiert. Kopieren Sie die Vorlage, und stellen Sie sie wie unten gezeigt bereit, oder besuchen Sie den [Azure-Schnellstartkatalog](https://azure.microsoft.com/resources/templates/101-cosmosdb-gremlin-graph-ru-update/), und führen Sie die Bereitstellung über das Azure-Portal durch. Sie können die Vorlage auch auf Ihren lokalen Computer herunterladen oder eine neue Vorlage erstellen und den lokalen Pfad mit dem Parameter `--template-file` angeben.

[!code-json[cosmosdb-gremlin-graph-ru-update](~/quickstart-templates/101-cosmosdb-gremlin-graph-ru-update/azuredeploy.json)]

### <a name="deploy-graph-template-via-azure-cli"></a>Bereitstellen einer Graphenvorlage über die Azure CLI

Um die Resource Manager-Vorlage mit der Azure CLI bereitzustellen, kopieren Sie das Skript, und klicken Sie auf **Ausprobieren**, um Azure Cloud-Shell zu öffnen. Klicken Sie zum Einfügen des Skripts mit der rechten Maustaste auf die Shell, und wählen Sie **Einfügen** aus:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the graph name: ' graphName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-gremlin-graph-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName graphName=$graphName throughput=$throughput
```

## <a name="next-steps"></a>Nächste Schritte

Hier sind einige zusätzlichen Ressourcen:

- [Dokumentation zu Azure Resource Manager](/azure/azure-resource-manager/)
- [Schema von Azure Cosmos DB-Ressourcenanbietern](/azure/templates/microsoft.documentdb/allversions)
- [Schnellstartvorlagen für Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Beheben gängiger Azure-Bereitstellungsfehler mit Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)