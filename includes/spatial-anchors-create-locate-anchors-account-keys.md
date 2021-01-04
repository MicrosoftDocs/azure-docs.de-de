---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 7210b05566f5cd6f3c56792bce0904b3c9b645da
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993122"
---
## <a name="set-up-authentication"></a>Einrichten der Authentifizierung

Um auf den Dienst zugreifen zu können, müssen Sie einen Kontoschlüssel, ein Zugriffstoken oder ein Azure Active Directory-Authentifizierungstoken angeben. Weitere Informationen hierzu finden Sie auf der [Konzeptseite für die Authentifizierung](../articles/spatial-anchors/concepts/authentication.md).

### <a name="account-keys"></a>Kontoschlüssel

Kontoschlüssel sind eine Anmeldeinformationen, die es Ihrer Anwendung ermöglichen, sich beim Azure Spatial Anchors-Dienst zu authentifizieren. Der Zweck von Kontoschlüsseln besteht darin, Ihnen einen schnellen Einstieg zu ermöglichen. Vor allem während der Entwicklungsphase der Integration Ihrer Anwendung mit Azure Spatial Anchors. Sie können Kontoschlüssel verwenden, indem Sie sie während der Entwicklung in Ihre Clientanwendungen einbetten. Nach Abschluss der Entwicklung wird dringend empfohlen, zu einem Authentifizierungsmechanismus für die Produktionsebene zu wechseln, der von Zugriffstoken oder Azure Active Directory-basierter Benutzerauthentifizierung unterstützt wird. Um einen Kontoschlüssel für die Entwicklung zu erhalten, besuchen Sie Ihr Azure Spatial Anchors-Konto, und navigieren Sie zur Registerkarte „Schlüssel“.