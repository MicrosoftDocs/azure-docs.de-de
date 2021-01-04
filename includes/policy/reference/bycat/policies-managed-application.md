---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a4f06313b577ffba8a10b4ecfff17e0117550d04
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998793"
---
|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Die Anwendungsdefinition für die verwaltete Anwendung sollte ein vom Kunden angegebenes Speicherkonto verwenden.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9db7917b-1607-4e7d-a689-bca978dd0633) |Verwenden Sie Ihr eigenes Speicherkonto, um die Anwendungsdefinitionsdaten zu steuern, wenn dies gesetzlich vorgeschrieben ist oder als Konformitätsanforderung gilt. Sie können die Definition der verwalteten Anwendung in einem von Ihnen während der Erstellung angegebenen Speicherkonto speichern. Dadurch können sein Speicherort und der Zugriff zur Erfüllung Ihrer gesetzlichen Konformitätsanforderungen vollständig von Ihnen verwaltet werden. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/ApplicationDefinition_Missing_StorageAccount_Deny.json) |
|[Zuordnungen für eine verwaltete Anwendung bereitstellen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17763ad9-70c0-4794-9397-53d765932634) |Hiermit wird eine Zuordnungsressource bereitgestellt, die der angegebenen verwalteten Anwendung ausgewählte Ressourcentypen zuordnet.  Diese Richtlinienbereitstellung unterstützt keine geschachtelten Ressourcentypen. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/AssociationForManagedApplication_Deploy.json) |
