---
title: Erstellen von Arbeitsbereichen mit der Azure CLI
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Machine Learning-Erweiterung für die Azure-Befehlszeilenschnittstelle verwenden, um einen neuen Azure Machine Learning-Arbeitsbereich zu erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: Blackmist
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.openlocfilehash: 66a9c9d605911a9d3b30a55d47e16026e26e502a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96447250"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Erstellen eines Arbeitsbereichs für Azure Machine Learning mit der Azure CLI


In diesem Artikel erlernen Sie das Erstellen eines Azure Machine Learning-Arbeitsbereichs mit der Azure CLI. Die Azure CLI bietet Befehle zum Verwalten von Azure-Ressourcen. Die Erweiterung der CLI für maschinelles Lernen unterstützt Befehle für die Arbeit mit Azure Machine Learning-Ressourcen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein **Azure-Abonnement**. Wenn Sie keins besitzen, probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) aus.

* Um die CLI-Befehle in diesem Dokument aus Ihrer **lokalen Umgebung** zu verwenden, benötigen Sie die [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

    Wenn Sie die [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/) verwenden, befindet sich die CLI in der Cloud, und der Zugriff erfolgt über den Browser.

## <a name="limitations"></a>Einschränkungen

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="connect-the-cli-to-your-azure-subscription"></a>Herstellen einer Verbindung zwischen der CLI und Ihrem Azure-Abonnement

> [!IMPORTANT]
> Wenn Sie Azure Cloud Shell verwenden, können Sie diesen Abschnitt überspringen. Die Cloud Shell authentifiziert Sie automatisch mit dem Konto, mit dem Sie sich bei Ihrem Azure-Abonnement anmelden.

Ihnen stehen mehrere Möglichkeiten zur Verfügung, sich über die CLI bei Ihrem Azure-Abonnement zu authentifizieren. Am einfachsten ist die interaktive Authentifizierung mithilfe eines Browsers. Öffnen Sie zur interaktiven Authentifizierung eine Befehlszeile oder ein Terminal, und verwenden Sie den folgenden Befehl:

```azurecli-interactive
az login
```

Die CLI öffnet Ihren Standardbrowser, sofern sie dazu in der Lage ist, und lädt eine Anmeldeseite. Andernfalls müssen Sie einen Browser öffnen und die Anweisungen in der Befehlszeile befolgen. Die Anweisungen umfassen das Navigieren zu [https://aka.ms/devicelogin](https://aka.ms/devicelogin) und Eingeben eines Autorisierungscodes.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

Andere Methoden zur Authentifizierung finden Sie unter [Anmelden mit der Azure CLI](/cli/azure/authenticate-azure-cli?preserve-view=true&view=azure-cli-latest).

## <a name="install-the-machine-learning-extension"></a>Installieren der Erweiterung für maschinelles Lernen

Um die Erweiterung für maschinelles Lernen zu installieren, verwenden Sie den folgenden Befehl:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>Erstellen eines Arbeitsbereichs

Der Azure Machine Learning-Arbeitsbereich basiert auf den folgenden Azure-Diensten oder -Entitäten:

> [!IMPORTANT]
> Wenn Sie keinen vorhandenen Azure-Dienst angeben, wird während der Erstellung des Arbeitsbereichs automatisch ein Dienst erstellt. Sie müssen immer eine Ressourcengruppe angeben. Wenn Sie Ihr eigenes Speicherkonto anfügen, stellen Sie sicher, dass es die folgenden Kriterien erfüllt:
>
> * Das Speicherkonto ist _kein_ Premium-Konto (Premium_LRS oder Premium_GRS).
> * Sowohl Azure Blob- als auch Azure-Dateifunktionen sind aktiviert.
> * Der hierarchische Namespace (ADLS Gen 2) ist deaktiviert.
>
> Diese Anforderungen gelten nur für das _Standard_ speicherkonto, das vom Arbeitsbereich verwendet wird.

| Dienst | Parameter zum Angeben einer vorhandenen Instanz |
| ---- | ---- |
| **Azure-Ressourcengruppe** | `-g <resource-group-name>`
| **Azure Storage-Konto** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure Key Vault** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Der Azure Machine Learning-Arbeitsbereich muss innerhalb einer Ressourcengruppe erstellt werden. Sie können eine vorhandene Ressourcengruppe verwenden oder eine neue erstellen. Um __eine neue Ressourcengruppe zu erstellen__, verwenden Sie den folgenden Befehl. Ersetzen Sie `<resource-group-name>` durch den Namen, der für diese Ressourcengruppe verwendet werden soll. Ersetzen Sie `<location>` durch die Azure-Region, die für diese Ressourcengruppe verwendet werden soll:

> [!TIP]
> Wählen Sie eine Region aus, in der Azure Machine Learning verfügbar ist. Weitere Informationen finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

Die Antwort dieses Befehls ähnelt dem folgenden JSON-Code:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Weitere Informationen zum Arbeiten mit Ressourcengruppen finden Sie unter [az group](/cli/azure/group?preserve-view=true&view=azure-cli-latest).

### <a name="automatically-create-required-resources"></a>Automatisches Erstellen der erforderlichen Ressourcen

Um einen neuen Arbeitsbereich zu erstellen, in dem die __Dienste automatisch erstellt werden__, verwenden Sie den folgenden Befehl:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> Für den Namen des Arbeitsbereichs wird die Groß-/Kleinschreibung nicht beachtet.

Die Ausgabe dieses Befehls ähnelt dem folgenden JSON-Code:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

### <a name="virtual-network-and-private-endpoint"></a>Virtuelles Netzwerk und privater Endpunkt

> [!IMPORTANT]
> Die Verwendung eines Azure Machine Learning-Arbeitsbereichs mit Private Link ist in den Azure Government- und Azure China 21Vianet-Regionen nicht verfügbar.

Wenn Sie den Zugriff auf Ihren Arbeitsbereich auf ein virtuelles Netzwerk beschränken möchten, können Sie die folgenden Parameter verwenden:

* `--pe-name`: Der Name des erstellten privaten Endpunkts.
* `--pe-auto-approval`: Gibt an, ob private Endpunktverbindungen mit dem Arbeitsbereich automatisch genehmigt werden sollen.
* `--pe-resource-group`: Die für den privaten Endpunkt zu erstellende Ressourcengruppe. Muss dieselbe Gruppe sein, die auch das virtuelle Netzwerk enthält.
* `--pe-vnet-name`: Das vorhandene virtuelle Netzwerk, in dem der private Endpunkt erstellt werden soll.
* `--pe-subnet-name`: Der Name des Subnetzes, in dem der private Endpunkt erstellt werden soll. Der Standardwert ist `default`.

Weitere Informationen zur Verwendung eines privaten Endpunkts und eines virtuellen Netzwerks mit Ihrem Arbeitsbereich finden Sie unter [Übersicht zu Isolation und Datenschutz bei virtuellen Netzwerken](how-to-network-security-overview.md).

### <a name="customer-managed-key-and-high-business-impact-workspace"></a>Kundenseitig verwalteter Schlüssel und Arbeitsbereich mit starken geschäftlichen Auswirkungen

Standardmäßig werden Metadaten für den Arbeitsbereich auf einer Azure Cosmos DB-Instanz gespeichert, die von Microsoft verwaltet wird. Diese Daten werden mit von Microsoft verwalteten Schlüsseln verschlüsselt.

> [!NOTE]
> Azure Cosmos DB wird __nicht__ verwendet, um Daten zur Modellleistung oder bei Experimenten oder Ihren Modellimplementierungen protokollierte Informationen zu speichern. Weitere Informationen zur Überwachung dieser Elemente finden Sie im Abschnitt [Überwachung und Protokollierung](concept-azure-machine-learning-architecture.md) des Artikels zur Architektur und zu den Konzepten.

Anstatt den von Microsoft verwalteten Schlüssel zu verwenden, können Sie Ihren eigenen Schlüssel bereitstellen. Hierbei wird die Azure Cosmos DB-Instanz erstellt, die Metadaten in Ihrem Azure-Abonnement speichert. Verwenden Sie den `--cmk-keyvault`-Parameter, um den Azure Key Vault anzugeben, der den Schlüssel enthält, und `--resource-cmk-uri`, um die URL des Schlüssels innerhalb des Tresors anzugeben.

Bevor Sie die Parameter `--cmk-keyvault` und `--resource-cmk-uri` verwenden, müssen Sie zunächst die folgenden Aktionen ausführen:

1. Autorisieren Sie die __Machine Learning-App__ (in der Identitäts- und Zugriffsverwaltung) mit den Berechtigungen für Mitwirkende in Ihrem Abonnement.
1. Befolgen Sie die Schritte in [Konfigurieren von kundenseitig verwalteten Schlüsseln](../cosmos-db/how-to-setup-cmk.md), um:
    * Registrieren des Azure Cosmos DB-Anbieters
    * Erstellen und Konfigurieren einer Azure Key Vault-Instanz
    * Generieren eines Schlüssels

Sie müssen die Azure Cosmos DB-Instanz nicht manuell erstellen. Es wird eine während der Erstellung des Arbeitsbereichs für Sie erstellt. Diese Azure Cosmos DB-Instanz wird in einer separaten Ressourcengruppe mit einem Namen erstellt, der auf diesem Muster basiert: `<your-resource-group-name>_<GUID>`.

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Verwenden Sie den `--hbi-workspace`-Parameter, um die von Microsoft in Ihrem Arbeitsbereich gesammelten Daten zu beschränken. 

> [!IMPORTANT]
> Die Auswahl von „starken geschäftlichen Auswirkungen“ kann nur beim Erstellen eines Arbeitsbereichs erfolgen. Diese Einstellung kann nach dem Erstellen des Arbeitsbereichs nicht mehr geändert werden.

Weitere Informationen zu kundenseitig verwalteten Schlüsseln und Arbeitsbereichen mit starken geschäftlichen Auswirkungen finden Sie unter [Enterprise-Sicherheit für Machine Learning](concept-data-encryption.md#encryption-at-rest).

### <a name="use-existing-resources"></a>Verwenden vorhandener Ressourcen

Um einen Arbeitsbereich zu erstellen, der vorhandene Ressourcen verwendet, müssen Sie die ID für die Ressourcen angeben. Um die ID für die Dienste abzurufen, verwenden Sie die folgenden Befehle:

> [!IMPORTANT]
> Sie müssen nicht alle vorhandenen Ressourcen angeben. Sie können eine oder mehrere angeben. Beispielsweise können Sie ein vorhandenes Speicherkonto angeben, und der Arbeitsbereich erstellt die sonstigen Ressourcen.

+ **Azure Storage-Konto**: `az storage account show --name <storage-account-name> --query "id"`

    Die Antwort dieses Befehls ähnelt dem folgenden Text und ist die ID für Ihr Speicherkonto:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

    > [!IMPORTANT]
    > Wenn Sie ein vorhandenes Azure Storage-Konto verwenden möchten, darf es sich nicht um ein Premium-Konto (Premium_LRS oder Premium_GRS) handeln. Es darf auch keinen hierarchischen Namespace aufweisen (mit Azure Data Lake Storage Gen2 verwendet). Weder Storage Premium noch hierarchische Namespaces werden mit dem _Standard_ speicherkonto des Arbeitsbereichs unterstützt. Sie können Storage Premium noch hierarchische Namespaces mit _nicht standardmäßigen_ Speicherkonten verwenden.

+ **Azure Application Insights**:

    1. Installieren Sie die Erweiterung Application Insights:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Rufen Sie die ID Ihres Application Insights-Diensts ab:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        Die Antwort dieses Befehls ähnelt dem folgenden Text und ist die ID für Ihren Application Insights-Dienst:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault**: `az keyvault show --name <key-vault-name> --query "ID"`

    Die Antwort dieses Befehls ähnelt dem folgenden Text und ist die ID für Ihren Schlüsseltresor:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure Container Registry**: `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    Die Antwort dieses Befehls ähnelt dem folgenden Text und ist die ID für die Containerregistrierung:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > Für die Containerregistrierung muss das [Administratorkonto](../container-registry/container-registry-authentication.md#admin-account) aktiviert sein, bevor sie mit einem Azure Machine Learning-Arbeitsbereich verwendet werden kann.

Sobald Sie über die IDs für die Ressourcen verfügen, die Sie mit dem Arbeitsbereich verwenden möchten, verwenden Sie den grundlegenden `az workspace create -w <workspace-name> -g <resource-group-name>`-Befehl, und fügen Sie die Parameter und IDs für die vorhandenen Ressourcen hinzu. Beispielsweise wird mit dem folgenden Befehl ein Arbeitsbereich erstellt, in dem eine vorhandene Containerregistrierung verwendet wird:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

Die Ausgabe dieses Befehls ähnelt dem folgenden JSON-Code:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="list-workspaces"></a>Auflisten von Arbeitsbereichen

Um alle Arbeitsbereiche für Ihr Azure-Abonnement aufzulisten, verwenden Sie den folgenden Befehl:

```azurecli-interactive
az ml workspace list
```

Die Ausgabe dieses Befehls ähnelt dem folgenden JSON-Code:

```json
[
  {
    "resourceGroup": "myresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "myml"
  },
  {
    "resourceGroup": "anotherresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "anotherml"
  }
]
```

Weitere Informationen finden Sie in der Dokumentation zu [az ml workspace list](/cli/azure/ext/azure-cli-ml/ml/workspace?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list).

## <a name="get-workspace-information"></a>Abrufen von Informationen zum Arbeitsbereich

Um Informationen zu einem Arbeitsbereich zu erhalten, verwenden Sie den folgenden Befehl:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

Die Ausgabe dieses Befehls ähnelt dem folgenden JSON-Code:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Weitere Informationen finden Sie in der Dokumentation zu [az ml workspace show](/cli/azure/ext/azure-cli-ml/ml/workspace?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show).

## <a name="update-a-workspace"></a>Aktualisieren eines Arbeitsbereichs

Verwenden Sie den folgenden Befehl, um einen Arbeitsbereich zu aktualisieren:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

Die Ausgabe dieses Befehls ähnelt dem folgenden JSON-Code:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Weitere Informationen finden Sie in der Dokumentation zu [az ml workspace update](/cli/azure/ext/azure-cli-ml/ml/workspace?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update).

## <a name="share-a-workspace-with-another-user"></a>Teilen eines Arbeitsbereich mit anderen Benutzern

Verwenden Sie den folgenden Befehl, um einen Arbeitsbereich mit einem anderen Benutzer in Ihrem Abonnement zu teilen:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Weitere Informationen zur rollenbasierten Zugriffssteuerung (Roles-Based Access Control, RBAC) in Azure mit Azure Machine Learning finden Sie unter [Verwalten des Zugriffs auf einen Azure Machine Learning-Arbeitsbereich](how-to-assign-roles.md).

Weitere Informationen finden Sie in der Dokumentation zu [az ml workspace share](/cli/azure/ext/azure-cli-ml/ml/workspace?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share).

## <a name="sync-keys-for-dependent-resources"></a>Synchronisieren von Schlüsseln für abhängige Ressourcen

Wenn Sie Zugriffsschlüssel für eine der Ressourcen ändern, die von Ihrem Arbeitsbereich verwendet werden, verwenden Sie den folgenden Befehl, um die neuen Schlüssel mit dem Arbeitsbereich zu synchronisieren. Wenn Sie erzwingen möchten, dass der Arbeitsbereich die neuen Schlüssel sofort synchronisiert, verwenden Sie den folgenden Befehl:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Weitere Informationen zum Ändern von Schlüsseln finden Sie unter [Erneutes Generieren von Speicherzugriffsschlüsseln](how-to-change-storage-access-key.md).

Weitere Informationen finden Sie in der Dokumentation zu [az ml workspace sync-keys](/cli/azure/ext/azure-cli-ml/ml/workspace?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys).

## <a name="delete-a-workspace"></a>Löschen eines Arbeitsbereichs

Um einen Arbeitsbereich zu löschen, wenn er nicht mehr benötigt wird, verwenden Sie den folgenden Befehl:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> Beim Löschen eines Arbeitsbereichs werden Application Insights, Speicherkonto, Schlüsseltresor oder Containerregistrierung, die vom Arbeitsbereich verwendet werden, nicht gelöscht.

Sie können auch die Ressourcengruppe löschen, wodurch der Arbeitsbereich und alle anderen Azure-Ressourcen in der Ressourcengruppe gelöscht werden. Um die Ressourcengruppe zu löschen, verwenden Sie folgenden Befehl:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Weitere Informationen finden Sie in der Dokumentation zu [az ml workspace delete](/cli/azure/ext/azure-cli-ml/ml/workspace?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete).

## <a name="troubleshooting"></a>Problembehandlung

### <a name="resource-provider-errors"></a>Fehler der Ressourcenanbieter

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Verschieben des Arbeitsbereichs

> [!WARNING]
> Das Verschieben des Azure Machine Learning-Arbeitsbereichs in ein anderes Abonnement oder das Verschieben des besitzenden Abonnements in einen neuen Mandanten wird nicht unterstützt. Andernfalls können Fehler auftreten.

### <a name="deleting-the-azure-container-registry"></a>Löschen der Azure Container Registry

Der Azure Machine Learning-Arbeitsbereich verwendet für einige Operationen die Azure Container Registry (ACR). Es wird automatisch eine ACR-Instanz erstellt, wenn erstmals eine erforderlich ist.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI-Erweiterung für maschinelles Lernen finden Sie in der Dokumentation zu [az ml](/cli/azure/ext/azure-cli-ml/ml?preserve-view=true&view=azure-cli-latest).