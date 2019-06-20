---
title: Upgraden der Konfiguration eines Azure Service Fabric-Clusters | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe einer Resource Manager-Vorlage die Konfiguration upgraden, die einen Service Fabric-Cluster in Azure ausführt.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 77b9b20f99f00ef87c4907c2890cb3a21d20ec75
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62096265"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Upgraden der Konfiguration eines Clusters in Azure 

In diesem Artikel wird beschrieben, wie Sie die verschiedenen Fabric-Einstellungen für Ihren Service Fabric-Cluster anpassen. Für in Azure gehostete Cluster können Sie Einstellungen über das [Azure-Portal](https://portal.azure.com) oder mithilfe einer Azure Resource Manager-Vorlage anpassen.

> [!NOTE]
> Im Portal sind nicht alle Einstellungen verfügbar, [und es empfiehlt sich die Anpassung mithilfe einer Azure Resource Manager-Vorlage](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code). Das Portal kann nur für das Szenario „Service Fabric Dev\Test“ verwendet werden.
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Anpassen von Clustereinstellungen mithilfe von Resource Manager-Vorlagen
Azure-Cluster können über die JSON-Resource Manager-Vorlage konfiguriert werden. Weitere Informationen zu den verschiedenen Einstellungen finden Sie unter [Konfigurationseinstellungen für Cluster](service-fabric-cluster-fabric-settings.md). In den folgenden Schritten wird beispielhaft gezeigt, wie die neue Einstellung *MaxDiskQuotaInMB* mit dem Azure-Ressourcen-Explorer dem Abschnitt *Diagnose* hinzugefügt wird.

1. Besuchen Sie https://resources.azure.com.
2. Navigieren Sie zu Ihrem Abonnement. Erweitern Sie dazu **Abonnements** ->  **\<Ihr Abonnement>**  -> **Ressourcengruppen** ->  **\<Ihre Ressourcengruppe>**  -> **Anbieter** -> **Microsoft.ServiceFabric** -> **Cluster** ->  **\<Name Ihres Clusters>** .
3. Wählen Sie in der oberen rechten Ecke **Lesen/Schreiben** aus.
4. Wählen Sie **Bearbeiten** aus, aktualisieren Sie das JSON-Element `fabricSettings`, und fügen Sie ein neues Element hinzu:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Sie können auch auf eine der folgenden Arten mit Azure Resource Manager Clustereinstellungen anpassen:

- Verwenden Sie das [Azure-Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template), um die Resource Manager-Vorlage zu exportieren und zu aktualisieren.
- Verwenden Sie [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell), um die Resource Manager-Vorlage zu exportieren und zu aktualisieren.
- Verwenden Sie die [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli), um die Resource Manager-Vorlage zu exportieren und zu aktualisieren.
- Verwenden Sie die Azure PowerShell-Befehle [Set-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Set-azServiceFabricSetting) und [Remove-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Remove-azServiceFabricSetting), um die Einstellung direkt zu ändern.
- Verwenden Sie die [az sf cluster setting](https://docs.microsoft.com/cli/azure/sf/cluster/setting)-Befehle der Azure CLI, um die Einstellung direkt zu ändern.

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über die [Service Fabric-Clustereinstellungen](service-fabric-cluster-fabric-settings.md).
* Machen Sie sich mit der Vorgehensweise zum [Skalieren Ihres Clusters](service-fabric-cluster-scale-up-down.md) vertraut.
