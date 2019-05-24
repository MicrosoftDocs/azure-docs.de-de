---
title: Bereitstellen von Azure-Vorlagen mit SAS-Token und PowerShell | Microsoft-Dokumentation
description: Verwenden Sie Azure Resource Manager und Azure PowerShell, um Ressourcen in Azure mithilfe einer Vorlage bereitzustellen, die mit SAS-Token geschützt ist.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
ms.openlocfilehash: 818aa63ced56d7cf382536f10bb6150199ab74e9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66128429"
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-powershell"></a>Bereitstellen privater Resource Manager-Vorlagen mit SAS-Token und Azure PowerShell

Wenn sich Ihre Vorlage in einem Speicherkonto befindet, können Sie den Zugriffs auf die Vorlage beschränken und ein SAS-Token (Shared Access Signature) während der Bereitstellung angeben. In diesem Thema wird erläutert, wie Azure PowerShell mit Resource Manager-Vorlagen in Azure verwendet wird, um während der Bereitstellung ein SAS-Token bereitzustellen. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-private-template-to-storage-account"></a>Hinzufügen einer privaten Vorlage zum Speicherkonto

Sie können Ihre Vorlagen einem Speicherkonto hinzufügen und sie während der Bereitstellung mit einem SAS-Token verknüpfen.

> [!IMPORTANT]
> Wenn Sie die nachstehenden Schritte befolgen, hat nur der Kontobesitzer Zugriff auf das Blob mit der Vorlage. Wenn Sie jedoch ein SAS-Token für das Blob erstellen, können andere Benutzer über diesen URI auf das Blob zugreifen. Wenn ein anderer Benutzer den URI abfängt, hat dieser Benutzer Zugriff auf die Vorlage. Ein SAS-Token ist eine gute Möglichkeit zum Einschränken des Zugriffs auf Ihre Vorlagen. Sie sollten allerdings Kennwörter auf keinen Fall direkt in die Vorlage einschließen.
> 
> 

Mit dem folgenden Beispiel wird ein privater Speicherkontocontainer eingerichtet und eine Vorlage hochgeladen:
   
```powershell
# create a storage account for templates
New-AzResourceGroup -Name ManageGroup -Location "South Central US"
New-AzStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name} -Type Standard_LRS -Location "West US"
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# create a container and upload template
New-AzStorageContainer -Name templates -Permission Off
Set-AzStorageBlobContent -Container templates -File c:\MyTemplates\storage.json
```

## <a name="provide-sas-token-during-deployment"></a>Bereitstellen eines SAS-Tokens während der Bereitstellung
Generieren Sie zum Bereitstellen einer privaten Vorlage in einem Speicherkonto ein SAS-Token, und fügen Sie es dem URI für die Vorlage hinzu. Legen Sie die Ablaufzeit so fest, dass ausreichend Zeit für die Bereitstellung bleibt.
   
```powershell
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken -Container templates -Blob storage.json -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri $templateuri
```

Ein Beispiel der Verwendung eines SAS-Tokens mit verknüpften Vorlagen finden Sie unter [Verwenden von verknüpften Vorlagen mit Azure Resource Manager](resource-group-linked-templates.md).


## <a name="next-steps"></a>Nächste Schritte
* Eine Einführung in die Bereitstellung von Vorlagen finden Sie unter [Bereitstellen von Ressourcen mit Resource Manager-Vorlagen und Azure PowerShell](resource-group-template-deploy.md).
* Ein vollständiges Beispielskript, mit dem eine Vorlage bereitgestellt wird, finden Sie unter [Bereitstellung über Resource Manager-Vorlage – PowerShell-Skript](resource-manager-samples-powershell-deploy.md).
* Informationen zum Definieren von Parametern in der Vorlage finden Sie unter [Erstellen von Vorlagen](resource-group-authoring-templates.md#parameters).
