---
title: Bereitstellen von Azure Cache for Redis mithilfe einer Azure Resource Manager-Vorlage
description: Erfahren Sie, wie Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage) verwenden, um eine Azure Cache for Redis-Ressource bereitzustellen. Vorlagen werden für gängige Szenarien bereitgestellt.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: subject-armqs
ms.date: 08/18/2020
ms.openlocfilehash: 8bd9a45ec7c43d9338dec184afd784d2b163c410
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92735981"
---
# <a name="quickstart-create-an-azure-cache-for-redis-using-an-arm-template"></a>Schnellstart: Erstellen einer Azure Cache for Redis-Ressource mithilfe einer ARM-Vorlage

Erfahren Sie, wie Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage) erstellen, die eine Azure Cache for Redis-Ressource bereitstellt. Der Cache kann mit einem vorhandenen Speicherkonto verwendet werden, um Diagnosedaten aufzunehmen. Sie erfahren darüber hinaus, wie Sie definieren, welche Ressourcen bereitgestellt werden und wie Sie Parameter definieren, die angegeben werden, wenn die Bereitstellung ausgeführt wird. Sie können diese Vorlage für Ihre eigenen Bereitstellungen verwenden oder an Ihre Anforderungen anpassen. Derzeit werden für alle Caches in derselben Region für ein Abonnement dieselben Diagnoseeinstellungen verwendet. Ein Aktualisieren eines Cache in der Region wirkt sich auf alle anderen Caches in der Region aus.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

* **Azure-Abonnement** : Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* **Ein Speicherkonto** : Informationen zur Erstellung eines Speicherkontos finden Sie unter [Erstellen eines Azure Storage-Kontos](../storage/common/storage-account-create.md?tabs=azure-portal). Das Speicherkonto wird für Diagnosedaten verwendet.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-redis-cache/).

:::code language="json" source="~/quickstart-templates/101-redis-cache/azuredeploy.json":::

Die folgenden Ressourcen sind in der Vorlage definiert:

* [Microsoft.Cache/Redis](/azure/templates/microsoft.cache/redis)
* [Microsoft.Insights/diagnosticsettings](/azure/templates/microsoft.insights/diagnosticsettings)

Resource Manager-Vorlagen für den neuen [Tarif „Premium“](cache-overview.md#service-tiers) sind ebenfalls verfügbar.

* [Erstellen eines Azure Cache for Redis vom Typ „Premium“ mit Clustering](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
* [Erstellen eines Azure Cache for Redis vom Typ „Premium“ mit Datenpersistenz](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
* [Erstellen eines in einem virtuellen Netzwerk bereitgestellten Premium Redis Caches](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)

Die neuesten Vorlagen finden Sie in [Azure-Schnellstartvorlagen](https://azure.microsoft.com/documentation/templates/) unter _Azure Cache for Redis_.

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Wählen Sie das folgende Image aus, um sich bei Azure anzumelden und die Vorlage zu öffnen.

    [![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)
1. Wählen Sie die folgenden Werte aus, bzw. geben Sie sie ein:

    * **Abonnement** : Wählen Sie ein Azure-Abonnement zum Erstellen der Datenfreigabe und anderer Ressourcen aus.
    * **Ressourcengruppe** : Klicken Sie auf **Neu erstellen** , um eine neue Ressourcengruppe zu erstellen, oder wählen Sie eine vorhandene Ressourcengruppe aus.
    * **Standort** : Wählen Sie einen Standort für die Ressourcengruppe aus. Das Speicherkonto und der Redis Cache müssen sich in der gleichen Region befinden. Standardmäßig verwendet der Redis Cache denselben Speicherort wie die Ressourcengruppe. Geben Sie daher denselben Speicherort wie für das Speicherkonto an.
    * **Name des Redis Cache** : Geben Sie einen Namen für den Redis Cache ein.
    * **Vorhandenes Diagnosespeicherkonto** : Geben Sie die Ressourcen-ID eines Speicherkontos ein. Die Syntax lautet `/subscriptions/&lt;SUBSCRIPTION ID>/resourceGroups/&lt;RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/&lt;STORAGE ACCOUNT NAME>`.

    Übernehmen Sie für den Rest der Einstellungen die Standardwerte.
1. Wählen Sie **Ich stimme den oben genannten Geschäftsbedingungen zu** und anschließend **Kaufen** aus.

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Öffnen Sie den Redis Cache, den Sie erstellt haben.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe. Dadurch werden die Ressourcen in der Ressourcengruppe gelöscht.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie erfahren, wie Sie eine Azure Resource Manager-Vorlage erstellen, die einen Azure Cache for Redis bereitstellt. Um zu erfahren, wie Sie eine Azure Resource Manager-Vorlage erstellen, die eine Azure Web App mit Azure Cache for Redis bereitstellt, lesen Sie [Erstellen einer Web-App und eines Azure Cache for Redis mithilfe einer Vorlage](./cache-web-app-arm-with-redis-cache-provision.md).
