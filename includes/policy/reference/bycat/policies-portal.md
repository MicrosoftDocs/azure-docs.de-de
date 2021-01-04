---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c3c9e698aeddbe4818e7b77596eed81e60cb38e5
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "96007928"
---
|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Freigegebene Dashboards dürfen keine Markdownkacheln mit Inline-Inhalten aufweisen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Verhindert das Erstellen eines freigegebenen Dashboards mit Inlineinhalten auf Markdownkacheln und erzwingt, dass der Inhalt als online gehostete Markdowndatei gespeichert werden soll. Wenn Sie Inlineinhalte auf der Markdownkachel verwenden, können Sie die Verschlüsselung des Inhalts nicht verwalten. Das Konfigurieren eines eigenen Speichers ermöglicht das Verschlüsseln, das doppelte Verschlüsseln und sogar das Verwenden eigener Schlüssel. Wenn Sie diese Richtlinie aktivieren, können Benutzer die Version 2020-09-01-preview oder eine höhere Version der REST-API für freigegebene Dashboards verwenden. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
