---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110530"
---
### <a name="access-tokens"></a>Zugriffstoken

Zugriffstoken sind eine robustere Methode zur Authentifizierung bei Azure Spatial Anchors. Vor allem, wenn Sie Ihre Anwendung für eine Produktionsbereitstellung vorbereiten. Die Zusammenfassung dieses Ansatzes besteht darin, einen Back-End-Dienst einzurichten, bei dem sich Ihre Clientanwendung sicher authentifizieren kann. Ihr Back-End-Dienst kommuniziert zur Runtime mit AAD und mit dem STS-Dienst (Secure Token Service) von Azure Spatial Anchors, um ein Zugriffstoken anzufordern. Dieses Token wird dann der Clientanwendung bereitgestellt und im SDK zur Authentifizierung bei Azure Spatial Anchors verwendet.
