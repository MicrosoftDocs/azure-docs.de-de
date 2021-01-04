---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7b76b04e952d500490473edf6d1c890b9f0be614
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94990912"
---
|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Diagnoseeinstellungen für Suchdienste in Event Hub bereitstellen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d5da587-71bd-41f5-ac95-dd3330c2d58d) |Hiermit werden die Diagnoseeinstellungen für Suchdienste zum Streamen in eine regionale Event Hub-Instanz bereitgestellt, wenn ein Suchdienst erstellt oder aktualisiert wird, in dem diese Diagnoseeinstellungen fehlen. |DeployIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Search_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Diagnoseeinstellungen für Suchdienste in Log Analytics-Arbeitsbereich bereitstellen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08ba64b8-738f-4918-9686-730d2ed79c7d) |Hiermit werden die Diagnoseeinstellungen für Suchdienste zum Streamen in einen regionalen Log Analytics-Arbeitsbereich bereitgestellt, wenn ein Suchdienst erstellt oder aktualisiert wird, in dem diese Diagnoseeinstellungen fehlen. |DeployIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Search_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[In den Search-Diensten sollten Diagnoseprotokolle aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Hiermit wird die Aktivierung von Diagnoseprotokollen überwacht. Auf diese Weise können Sie vergangene Aktivitäten nachvollziehen, wenn es zu einem Sicherheitsincident kommt oder Ihr Netzwerk gefährdet ist. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
