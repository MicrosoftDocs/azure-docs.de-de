---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 354811b1db31731913f2a99287b0e4a71b2aff61
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464136"
---
Stellen Sie Folgendes sicher, bevor Sie beginnen:

* Ihr Microsoft Azure-Abonnement ist für eine Azure Stack Edge-Ressource aktiviert. Stellen Sie sicher, dass Sie ein unterstütztes Abonnement verwendet haben, z. B. [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp) oder [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Sie verfügen für die Azure Stack Edge-/Azure Storage Gateway-, IoT Hub- und Azure Storage-Ressourcen über Zugriff als Besitzer oder Mitwirkender auf Ressourcengruppenebene.

  * Für die Erstellung von Azure Stack Edge-Ressourcen müssen Sie mindestens über Berechtigungen als Mitwirkender auf der Ressourcengruppenebene verfügen. Vergewissern Sie sich außerdem, dass der Anbieter `Microsoft.DataBoxEdge` registriert ist. Eine Registrierungsanleitung finden Sie unter [Register resource provider](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers) (Registrieren des Ressourcenanbieters).
  * Für die Erstellung von IoT Hub-Ressourcen muss der Anbieter „Microsoft.Devices“ registriert sein. Eine Registrierungsanleitung finden Sie unter [Register resource provider](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers) (Registrieren des Ressourcenanbieters).
  * Für die Erstellung von Speicherkontoressourcen sind ebenfalls mindestens Berechtigungen als Mitwirkender auf der Ressourcengruppenebene erforderlich. Azure Storage ist standardmäßig als Ressourcenanbieter registriert.
* Sie haben als Administrator oder Benutzer Zugriff auf die Azure Active Directory Graph-API. Weitere Informationen finden Sie unter [Azure Active Directory Graph-API](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
* Sie besitzen ein Microsoft Azure-Speicherkonto mit Anmeldeinformationen für den Zugriff.

