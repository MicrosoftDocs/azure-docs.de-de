---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 72616d5d79b4dbdb8e14463c706c1626eeb50e97
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177788"
---
## <a name="set-up-authentication"></a>Einrichten der Authentifizierung

Um auf den Dienst zugreifen zu können, müssen Sie einen Kontoschlüssel, ein Zugriffstoken oder ein Azure Active Directory-Authentifizierungstoken angeben.

### <a name="account-keys"></a>Kontoschlüssel

Kontoschlüssel sind eine Anmeldeinformationen, die es Ihrer Anwendung ermöglichen, sich beim Azure Spatial Anchors-Dienst zu authentifizieren. Der Zweck von Kontoschlüsseln besteht darin, Ihnen einen schnellen Einstieg zu ermöglichen. Vor allem während der Entwicklungsphase der Integration Ihrer Anwendung mit Azure Spatial Anchors. Sie können Kontoschlüssel verwenden, indem Sie sie während der Entwicklung in Ihre Clientanwendungen einbetten. Nach Abschluss der Entwicklung wird dringend empfohlen, zu einem Authentifizierungsmechanismus für die Produktionsebene zu wechseln, der von Zugriffstoken oder Azure Active Directory-basierter Benutzerauthentifizierung unterstützt wird. Um einen Kontoschlüssel für die Entwicklung zu erhalten, besuchen Sie Ihr Azure Spatial Anchors-Konto, und navigieren Sie zur Registerkarte „Schlüssel“.
