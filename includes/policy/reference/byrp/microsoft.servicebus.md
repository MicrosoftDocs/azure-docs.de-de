---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b561124e094e130eb0bdbe400f68e5df473c01a4
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94986679"
---
|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Alle Autorisierungsregeln außer RootManageSharedAccessKey sollten aus dem Service Bus-Namespace entfernt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1817ec0-a368-432a-8057-8371e17ac6ee) |Service Bus-Clients dürfen keine Zugriffsrichtlinie auf Namespace-Ebene verwenden, die Zugriff auf alle Warteschlangen und Themen in einem Namespace bereitstellt. Um dem Sicherheitsmodell der geringsten Rechte zu entsprechen, müssen Sie Zugriffsrichtlinien auf Entitätsebene erstellen, damit nur der jeweiligen Entität Zugriff auf Warteschlangen und Themen gewährt wird. |Audit, Deny, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditNamespaceAccessRules_Audit.json) |
|[Diagnoseeinstellungen für Service Bus in Event Hub bereitstellen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6b51af03-9277-49a9-a3f8-1c69c9ff7403) |Hiermit werden die Diagnoseeinstellungen für Service Bus zum Streamen in eine regionale Event Hub-Instanz bereitgestellt, wenn eine Service Bus-Instanz erstellt oder aktualisiert wird, in der diese Diagnoseeinstellungen fehlen. |DeployIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ServiceBus_DeployDiagnosticLog_Deploy_EventHub.json) |
|[Diagnoseeinstellungen für Service Bus in Log Analytics-Arbeitsbereich bereitstellen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04d53d87-841c-4f23-8a5b-21564380b55e) |Hiermit werden die Diagnoseeinstellungen für Service Bus zum Streamen in einen regionalen Log Analytics-Arbeitsbereich bereitgestellt, wenn eine Service Bus-Instanz erstellt oder aktualisiert wird, in der diese Diagnoseeinstellungen fehlen. |DeployIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ServiceBus_DeployDiagnosticLog_Deploy_LogAnalytics.json) |
|[Diagnoseprotokolle in Service Bus aktivieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Hiermit wird die Aktivierung von Diagnoseprotokollen überwacht. Auf diese Weise können Sie vergangene Aktivitäten nachvollziehen, wenn es zu einem Sicherheitsincident kommt oder Ihr Netzwerk gefährdet ist. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[Service Bus sollte einen VNET-Dienstendpunkt verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |Diese Richtlinie überwacht alle Service Bus-Instanzen, die nicht für die Verwendung eines VNET-Dienstendpunkts konfiguriert sind. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
