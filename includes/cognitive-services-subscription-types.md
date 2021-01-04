---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: aedfe8783beacfe2e6679848ef4c2defa24d2da0
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95562428"
---
<!-- > [!NOTE]
> Subscription owners can disable the creation of Cognitive Services resources for resource groups and subscriptions by applying [Azure policy](../articles/governance/policy/overview.md#policy-definition), assigning a “Not allowed resource types” policy definition, and specifying **Microsoft.CognitiveServices/accounts** as the target resource type. -->
Sie können über zwei unterschiedliche Ressourcen auf Azure Cognitive Services zugreifen: Eine Ressource für mehrere Dienste oder eine Ressource für nur einen Dienst.

* Ressource für mehrere Dienste:
    * Greifen Sie mit einem einzigen Schlüssel und Endpunkt auf mehrere Azure Cognitive Services zu.
    * Konsolidiert die Abrechnung der von Ihnen verwendeten Dienste.
* Ressource für einen einzelnen Dienst:
    * Greifen Sie mit einem für den jeweiligen Dienst erstellten eindeutigen Schlüssel und Endpunkt auf einen einzelnen Dienst der Azure Cognitive Services zu. 
    * Verwenden Sie den Free-Tarif, um den Dienst zu testen.