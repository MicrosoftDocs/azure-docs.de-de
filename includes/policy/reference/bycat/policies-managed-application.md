---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3cd3b12a22742b696b0f7b511a47d16b911a3b68
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100096503"
---
|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Die Anwendungsdefinition für die verwaltete Anwendung sollte ein vom Kunden angegebenes Speicherkonto verwenden.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9db7917b-1607-4e7d-a689-bca978dd0633) |Verwenden Sie Ihr eigenes Speicherkonto, um die Anwendungsdefinitionsdaten zu steuern, wenn dies gesetzlich vorgeschrieben ist oder als Konformitätsanforderung gilt. Sie können die Definition der verwalteten Anwendung in einem von Ihnen während der Erstellung angegebenen Speicherkonto speichern. Dadurch können sein Speicherort und der Zugriff zur Erfüllung Ihrer gesetzlichen Konformitätsanforderungen vollständig von Ihnen verwaltet werden. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/ApplicationDefinition_Missing_StorageAccount_Deny.json) |
|[Zuordnungen für eine verwaltete Anwendung bereitstellen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17763ad9-70c0-4794-9397-53d765932634) |Hiermit wird eine Zuordnungsressource bereitgestellt, die der angegebenen verwalteten Anwendung ausgewählte Ressourcentypen zuordnet.  Diese Richtlinienbereitstellung unterstützt keine geschachtelten Ressourcentypen. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/AssociationForManagedApplication_Deploy.json) |
