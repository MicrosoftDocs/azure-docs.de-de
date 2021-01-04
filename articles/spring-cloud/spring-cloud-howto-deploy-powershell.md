---
title: Bereitstellen von Azure Spring Cloud mit Azure PowerShell
description: Bereitstellen von Azure Spring Cloud mit Azure PowerShell
author: bmitchell287
ms.author: brendm
ms.topic: conceptual
ms.service: spring-cloud
ms.devlang: azurepowershell
ms.date: 11/16/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3cb320a37818084f2fbcad22a3cc992655b19c3d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95547915"
---
# <a name="deploy-azure-spring-cloud-with-azure-powershell"></a>Bereitstellen von Azure Spring Cloud mit Azure PowerShell

In diesem Artikel wird beschrieben, wie Sie mithilfe des PowerShell-Moduls [Az.SpringCloud](/powershell/module/Az.SpringCloud) eine Instanz von Azure Spring Cloud erstellen.

## <a name="requirements"></a>Requirements (Anforderungen)

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Solange nur eine Vorschauversion des PowerShell-Moduls **Az.SpringCloud** verfügbar ist, müssen Sie es separat mithilfe des Cmdlets `Install-Module` installieren. Sobald dieses PowerShell-Modul allgemein verfügbar ist, wird es in die zukünftigen Releases des Az PowerShell-Moduls integriert und in Azure Cloud Shell standardmäßig zur Verfügung gestellt.

  ```azurepowershell-interactive
  Install-Module -Name Az.SpringCloud
  ```

* Wenn Sie über mehrere Azure-Abonnements verfügen, müssen Sie das entsprechende Abonnement auswählen, in dem die Ressourcen fakturiert werden sollen. Wählen Sie mit dem Cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) ein bestimmtes Abonnement aus.

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md). Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen als Gruppe bereitgestellt und verwaltet werden.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem angegebenen Namen am angegebenen Standort erstellt.

```azurepowershell-interactive
New-AzResourceGroup -Name <resource group name> -Location eastus
```

## <a name="provision-a-new-instance-of-azure-spring-cloud"></a>Bereitstellen einer neuen Instanz von Azure Spring Cloud

Zum Erstellen einer neuen Instanz von Azure Spring Cloud verwenden Sie das Cmdlet [New-AzSpringCloud](/powershell/module/az.springcloud/new-azspringcloud). Im folgenden Beispiel wird ein Azure Spring Cloud-Dienst mit dem angegebenen Namen in der zuvor erstellten Ressourcengruppe erstellt.

```azurepowershell-interactive
New-AzSpringCloud -ResourceGroupName <resource group name> -name <service instance name> -Location eastus
```

## <a name="create-a-new-azure-spring-cloud-app"></a>Erstellen einer neuen Azure Spring Cloud-App

Zum Erstellen einer neuen App verwenden Sie das Cmdlet [New-AzSpringCloudApp](/powershell/module/az.springcloud/new-azspringcloudapp). Im folgenden Beispiel wird eine Azure Spring Cloud-App namens `gateway` erstellt.

```azurepowershell-interactive
New-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="create-a-new-azure-spring-cloud-deployment"></a>Erstellen einer neuen Azure Spring Cloud-Bereitstellung

Zum Erstellen einer neuen Bereitstellung verwenden Sie das Cmdlet [New-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/new-azspringcloudappdeployment). Im folgenden Beispiel wird eine Azure Spring Cloud-Bereitstellung namens `default` für die App `gateway` erstellt.

```azurepowershell-interactive
New-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -Name <service instance name> -AppName gateway -DeploymentName default
```

## <a name="get-an-azure-spring-cloud-service"></a>Abrufen eines Azure Spring Cloud-Diensts

Zum Abrufen eines Azure Spring Cloud-Diensts und seiner Eigenschaften verwenden Sie das Cmdlet [Get-AzSpringCloud](/powershell/module/az.springcloud/get-azspringcloud). Im folgenden Beispiel werden Informationen zum angegebenen Azure Spring Cloud-Dienst abgerufen.

```azurepowershell-interactive
Get-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

## <a name="get-an-azure-spring-cloud-app"></a>Abrufen einer Azure Spring Cloud-App

Zum Abrufen einer Azure Spring Cloud-App und ihrer Eigenschaften verwenden Sie das Cmdlet [Get-AzSpringCloudApp](/powershell/module/az.springcloud/get-azspringcloudapp). Im folgenden Beispiel werden Informationen zur Spring Cloud-App `gateway` abgerufen.

```azurepowershell-interactive
Get-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="get-an-azure-spring-cloud-deployment"></a>Abrufen einer Azure Spring Cloud-Bereitstellung

Zum Abrufen einer Azure Spring Cloud-Bereitstellung und ihrer Eigenschaften verwenden Sie das Cmdlet [Get-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/get-azspringcloudappdeployment). Im folgenden Beispiel werden Informationen zur Spring Cloud-Bereitstellung `default` abgerufen.

```azurepowershell-interactive
Get-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die in diesem Artikel erstellten Ressourcen nicht benötigt werden, können Sie sie löschen, indem Sie die folgenden Beispiele ausführen.

### <a name="delete-an-azure-spring-cloud-deployment"></a>Löschen einer Azure Spring Cloud-Bereitstellung

Zum Entfernen einer Azure Spring Cloud-Bereitstellung verwenden Sie das Cmdlet [Remove-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/remove-azspringcloudappdeployment). Im folgenden Beispiel wird eine Azure Spring Cloud-Bereitstellung mit dem Namen `default` für den angegebenen Dienst und die angegebene App gelöscht.

```azurepowershell-interactive
Remove-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

### <a name="delete-an-azure-spring-cloud-app"></a>Löschen einer Azure Spring Cloud-App

Zum Entfernen einer Spring Cloud-App verwenden Sie das Cmdlet [Remove-AzSpringCloudApp](/powershell/module/Az.SpringCloud/remove-azspringcloudapp). Im folgenden Beispiel wird die App `gateway` im angegebenen Dienst und der angegebenen Ressourcengruppe gelöscht.

```azurepowershell
Remove-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

### <a name="delete-an-azure-spring-cloud-service"></a>Löschen eines Azure Spring Cloud-Diensts

Zum Entfernen eines Azure Spring Cloud-Diensts verwenden Sie das Cmdlet [Remove-AzSpringCloud](/powershell/module/Az.SpringCloud/remove-azspringcloud). Im folgenden Beispiel wird der angegebene Azure Spring Cloud-Dienst gelöscht.

```azurepowershell
Remove-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

### <a name="delete-the-resource-group"></a>Löschen der Ressourcengruppe

> [!CAUTION]
> Im folgenden Beispiel werden die angegebene Ressourcengruppe und alle darin enthaltenen Ressourcen gelöscht.
> Falls in der angegebenen Ressourcengruppe Ressourcen enthalten sind, die nicht zum Umfang dieses Artikels gehören, werden sie ebenfalls gelöscht.

```azurepowershell-interactive
Remove-AzResourceGroup -Name <resource group name>
```

## <a name="next-steps"></a>Nächste Schritte

[Azure Spring Cloud-Entwicklerressourcen](spring-cloud-resources.md)
