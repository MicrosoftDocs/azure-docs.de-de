---
title: 'Azure PowerShell-Beispiele: Abrufen von Details zu einem Azure-Clouddienst (erweiterter Support)'
description: Beispielskripts zum Abrufen von Informationen zu einer Bereitstellung von Azure Cloud Services (erweiterter Support)
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: fc16c9e0d47cfe015f7ec5ceb766053635840abb
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475335"
---
# <a name="retrieve-information-about-your-azure-cloud-service-extended-support-deployments"></a>Abrufen von Informationen zu Ihren Azure-Clouddienstbereitstellungen mit erweitertem Support

In diesen Beispielen werden verschiedene Möglichkeiten zum Abrufen von Informationen zu vorhandenen Azure-Clouddienstbereitstellungen mit erweitertem Support behandelt.

## <a name="get-all-cloud-services-under-a-resource-group"></a>Abrufen aller Cloud Services-Instanzen in einer Ressourcengruppe

```powershell
Get-AzCloudService -ResourceGroupName "ContosOrg"

ResourceGroupName Name              Location    ProvisioningState
----------------- ----              --------    -----------------
ContosOrg         ContosoCS         eastus2euap Succeeded
ContosOrg         ContosoCSTest     eastus2euap Failed
```

## <a name="get-single-cloud-service"></a>Abrufen eines einzelnen Clouddiensts
```powershell
Get-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS"

ResourceGroupName Name              Location    ProvisioningState
----------------- ----              --------    -----------------
ContosOrg         ContosoCS         eastus2euap Succeeded

PS C:\> $cloudService = Get-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS"
PS C:\> $cloudService | Format-List
ResourceGroupName : ContosOrg
Configuration     : xxxxxxxx
ConfigurationUrl  :
ExtensionProfile  : xxxxxxxx
Id                : xxxxxxxx
Location          : East US
Name              : ContosoCS
NetworkProfile    : xxxxxxxx
OSProfile         : xxxxxxxx
PackageUrl        : xxxxxxxx
ProvisioningState : Succeeded
RoleProfile       : xxxxxxxx
StartCloudService :
Tag               : {
                      "Owner": "Contos"
                    }
Type              : Microsoft.Compute/cloudServices
UniqueId          : xxxxxxxx
UpgradeMode       : Auto
```

## <a name="get-cloud-service-instance-view"></a>Abrufen der Clouddienst-Instanzansicht
```powershell
PS C:\>$cloudServiceInstanceView = Get-AzCloudServiceInstanceView -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS"

PS C:\>$cloudServiceInstanceView
RoleInstanceStatusesSummary                                   Statuses
---------------------------                                   --------
{{ProvisioningState/succeeded : 4}, {PowerState/started : 4}} {Provisioning succeeded, Started, Current Upgrade Domain of cloud service is -1., Max Upgrade Domain of cloud service is 1.}

PS C:\>$cloudServiceInstanceView.ToJsonString()
{
  "roleInstance": {
    "statusesSummary": [
      {
        "code": "ProvisioningState/succeeded",
        "count": 4
      },
      {
        "code": "PowerState/started",
        "count": 4
      }
    ]
  },
  "statuses": [
    {
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "time": "2020-10-28T13:26:48.8109686Z"
    },
    {
      "code": "PowerState/started",
      "displayStatus": "Started",
      "level": "Info",
      "time": "2020-10-28T13:26:48.8109686Z"
    },
    {
      "code": "CurrentUpgradeDomain/-1",
      "displayStatus": "Current Upgrade Domain of cloud service is -1.",
      "level": "Info"
    },
    {
      "code": "MaxUpgradeDomain/1",
      "displayStatus": "Max Upgrade Domain of cloud service is 1.",
      "level": "Info"
    }
  ]
}
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure Cloud Services (erweiterter Support) finden Sie unter [Informationen zu Azure Cloud Services (erweiterter Support)](overview.md).
- Besuchen Sie das [Beispielrepository zu Cloud Services (erweiterter Support)](https://github.com/Azure-Samples/cloud-services-extended-support).
