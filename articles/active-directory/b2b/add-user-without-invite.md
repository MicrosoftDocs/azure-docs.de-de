---
title: Hinzufügen von B2B-Gastbenutzern ohne Einladungslink oder -E-Mail – Azure Active Directory | Microsoft-Dokumentation
description: Sie können festlegen, dass ein Gastbenutzer Ihrem Azure AD andere Gastbenutzer hinzufügen kann, ohne dass eine Einladung in der Azure Active Directory B2B-Zusammenarbeit erforderlich ist.
services: active-directory
documentationcenter: ''
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5db5eb9c0e0493d906345892fcc5f2872a3e0e14
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65812457"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation-link-or-email"></a>Hinzufügen von Gastbenutzern der B2B-Zusammenarbeit ohne Einladungslink oder -E-Mail

Sie können nun Gastbenutzer einladen, indem Sie ihnen einen direkten Link zu einer freigegebenen App senden. Eine E-Mail-Einladung ist nur noch in einigen Sonderfällen erforderlich. Gastbenutzer können nahtlos auf die App zugreifen, wenn sie auf den App-Link klicken und dann die Datenschutzrichtlinien lesen und akzeptieren. Weitere Informationen finden Sie unter [Azure Active Directory B2B collaboration invitation redemption](redemption-experience.md) (Azure Active Directory B2B-Zusammenarbeit: Einlösen von Einladungen).   

Früher mussten Sie Gastbenutzern keine E-Mail-Einladung senden. Aus Ihrer Organisation oder einer Partnerorganisation wurde ein Einladender der Verzeichnisrolle **Gasteinladender** hinzugefügt. Dieser fügte über die Benutzeroberfläche oder PowerShell dem Verzeichnis, den Gruppen oder den Anwendungen einen Gastbenutzer hinzu. Wenn Sie PowerShell verwenden, müssen Sie keine Einladung per E-Mail versenden. Beispiel: 

1. Ein Benutzer in der Hostorganisation (z.B. WoodGrove) lädt einen Benutzer aus der Partnerorganisation (z.B. Sam@litware.com) als Gast ein.
2. Der Administrator der Hostorganisation [legt Richtlinien fest](delegate-invitations.md), die es Sam erlauben, weitere Benutzer aus der Partnerorganisation (Litware) zu identifizieren und hinzuzufügen. Sam muss der Rolle **Gasteinladender** hinzugefügt werden.
3. Jetzt kann Sam dem Verzeichnis, den Gruppen und den Anwendungen von WoodGrove weitere Benutzer von Litware hinzufügen, ohne dass Einladungen eingelöst werden müssen. Wenn Sam über entsprechende Enumerationsberechtigungen in Litware verfügt, erfolgt dies automatisch.
 
Dieses Verfahren funktioniert weiterhin. Es gibt jedoch einen kleinen Unterschied. Wenn Sie PowerShell verwenden, werden Sie feststellen, dass ein eingeladener Gast nun den Status **PendingAcceptance** hat und nicht direkt **Accepted**. Obwohl die Annahme noch aussteht, kann sich der Gastbenutzer in der App anmelden und auf diese zugreifen, ohne auf den Einladungslink in der E-Mail zu klicken. Dieser Status bedeutet, dass der Benutzer noch nicht den [Zustimmungsprozess](redemption-experience.md#privacy-policy-agreement) durchlaufen hat, während dem er den Datenschutzrichtlinien der Organisation zustimmt. Der Gastbenutzer sieht bei der ersten Anmeldung diesen Zustimmungsbildschirm. 

Wenn Sie einen Benutzer in das Verzeichnis einladen, muss der Gastbenutzer direkt auf die Azure-Portal-URL des spezifischen Ressourcenmandanten zugreifen (z.B. https://portal.azure.com/*ressourcenmandant*.onmicrosoft.com), um die Datenschutzrichtlinien anzusehen und diesen zuzustimmen.

## <a name="next-steps"></a>Nächste Schritte

- [Was ist die Azure AD B2B-Zusammenarbeit?](what-is-b2b.md)
- [B2B-Zusammenarbeit: Einlösen von Einladungen](redemption-experience.md)
- [Delegieren von Einladungen zur Azure Active Directory B2B-Zusammenarbeit](delegate-invitations.md)
- [Wie fügen Information-Worker B2B-Zusammenarbeitsbenutzer hinzu?](add-users-information-worker.md)

