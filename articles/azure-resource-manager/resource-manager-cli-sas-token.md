---
title: Bereitstellen von Azure-Vorlagen mit SAS-Token und Azure CLI | Microsoft-Dokumentation
description: Verwenden Sie Azure Resource Manager und Azure CLI, um Ressourcen in Azure mithilfe einer Vorlage bereitzustellen, die mit SAS-Token geschützt ist.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: tomfitz
ms.openlocfilehash: c869b76a0d1ba10bc27aefa60cbe4ed5b8d8201a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61061357"
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-cli"></a>Bereitstellen privater Resource Manager-Vorlagen mit SAS-Token und Azure CLI

Wenn sich Ihre Vorlage in einem Speicherkonto befindet, können Sie den Zugriffs auf die Vorlage beschränken und ein SAS-Token (Shared Access Signature) während der Bereitstellung angeben. In diesem Thema wird erläutert, wie Azure PowerShell mit Resource Manager-Vorlagen in Azure verwendet wird, um während der Bereitstellung ein SAS-Token bereitzustellen. 

## <a name="add-private-template-to-storage-account"></a>Hinzufügen einer privaten Vorlage zum Speicherkonto

Sie können Ihre Vorlagen einem Speicherkonto hinzufügen und sie während der Bereitstellung mit einem SAS-Token verknüpfen.

> [!IMPORTANT]
> Wenn Sie die nachstehenden Schritte befolgen, hat nur der Kontobesitzer Zugriff auf das Blob mit der Vorlage. Wenn Sie jedoch ein SAS-Token für das Blob erstellen, können andere Benutzer über diesen URI auf das Blob zugreifen. Wenn ein anderer Benutzer den URI abfängt, hat dieser Benutzer Zugriff auf die Vorlage. Ein SAS-Token ist eine gute Möglichkeit zum Einschränken des Zugriffs auf Ihre Vorlagen. Sie sollten allerdings Kennwörter auf keinen Fall direkt in die Vorlage einschließen.
> 
> 

Mit dem folgenden Beispiel wird ein privater Speicherkontocontainer eingerichtet und eine Vorlage hochgeladen:
   
```azurecli
az group create --name "ManageGroup" --location "South Central US"
az storage account create \
    --resource-group ManageGroup \
    --location "South Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
az storage blob upload \
    --container-name templates \
    --file vmlinux.json \
    --name vmlinux.json \
    --connection-string $connection
```

### <a name="provide-sas-token-during-deployment"></a>Bereitstellen eines SAS-Tokens während der Bereitstellung
Generieren Sie zum Bereitstellen einer privaten Vorlage in einem Speicherkonto ein SAS-Token, und fügen Sie es dem URI für die Vorlage hinzu. Legen Sie die Ablaufzeit so fest, dass ausreichend Zeit für die Bereitstellung bleibt.
   
```azurecli
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name vmlinux.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name vmlinux.json \
    --output tsv \
    --connection-string $connection)
az group deployment create --resource-group ExampleGroup --template-uri $url?$token
```

Ein Beispiel der Verwendung eines SAS-Tokens mit verknüpften Vorlagen finden Sie unter [Verwenden von verknüpften Vorlagen mit Azure Resource Manager](resource-group-linked-templates.md).

## <a name="next-steps"></a>Nächste Schritte
* Eine Einführung in die Bereitstellung von Vorlagen finden Sie unter [Bereitstellen von Ressourcen mit Resource Manager-Vorlagen und Azure PowerShell](resource-group-template-deploy-cli.md).
* Ein vollständiges Beispielskript, mit dem eine Vorlage bereitgestellt wird, finden Sie unter [Bereitstellung über Resource Manager-Vorlage – PowerShell-Skript](resource-manager-samples-cli-deploy.md).
* Informationen zum Definieren von Parametern in der Vorlage finden Sie unter [Erstellen von Vorlagen](resource-group-authoring-templates.md#parameters).
