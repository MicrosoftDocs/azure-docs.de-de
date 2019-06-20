---
title: Nachschlagen des Benutzernamens während der Authentifizierung der Anmeldedaten – Azure Active Directory | Microsoft-Dokumentation
description: Informationen dazu, wie Meldungen auf dem Bildschirm das Nachschlagen des Benutzernamens bei der Anmeldung widerspiegeln
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: db627359b75aa0ea19e30a8d22bcacaa3409cb4a
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418225"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Verhalten bei der Startbereichsermittlung für Azure Active Directory-Anmeldeseiten

Wir ändern das Anmeldeverhalten in Azure Active Directory (Azure AD), um neue Authentifizierungsmethoden zuzulassen und die Benutzerfreundlichkeit zu verbessern. Azure AD ermittelt während der Anmeldung, wo ein Benutzer überall authentifiziert werden muss. Dabei trifft Azure AD intelligente Entscheidungen durch das Lesen der Organisations- und Benutzereinstellungen zum auf der Anmeldeseite eingegebenen Benutzernamen. Dies ist ein weiterer Schritt zu einer kennwortlosen Zukunft, in der zusätzliche Anmeldeinformationen wie FIDO 2.0 möglich sind.

## <a name="home-realm-discovery-behavior"></a>Verhalten bei der Startbereichsermittlung

In der Vergangenheit wurde die Startbereichsermittlung von der Domäne gesteuert, die bei der Anmeldung angegeben wird, oder bei einigen älteren Anwendungen von einer Richtlinie für die Startbereichsermittlung. So konnte beispielsweise bei unserem Ermittlungsverhalten ein Azure Active Directory-Benutzer seinen Benutzernamen falsch eingeben und wurde trotzdem zur Seite mit den Anmeldeinformationen seiner Organisation weitergeleitet. Dies erfolgt immer dann, wenn der Benutzer den Domänennamen der Organisation „contoso.com“ richtig angibt. Dieses Verhalten bietet jedoch nicht dieselben Möglichkeiten zum Anpassen der Umgebung für einzelne Benutzer.

Um eine größere Anzahl von Anmeldeinformationen zu unterstützen und die Benutzerfreundlichkeit zu erhöhen, wurde das Nachschlageverhalten von Azure Active Directory während des Anmeldeprozesses aktualisiert. Beim neuen Veralten werden intelligente Entscheidungen durch das Lesen von Einstellungen auf Mandanten- und Benutzerebene basierend auf dem Benutzernamen getroffen, der auf der Anmeldeseite eingegeben wird. Um dies zu ermöglichen, überprüft Azure Active Directory, ob der auf der Anmeldeseite eingegebene Benutzername in der angegebenen Domäne vorhanden ist, oder leitet den Benutzer um, damit er seine Anmeldeinformationen angeben kann.

Ein weiterer Vorteil dieses Verhaltens sind bessere Fehlermeldungen. Es folgen einige Beispiele für die verbesserten Fehlermeldungen bei Anmeldung bei einer Anwendung, die nur Azure Active Directory-Benutzer unterstützt.

- Der Benutzername wurde falsch eingegeben oder noch nicht mit Azure AD synchronisiert:
  
    ![Der Benutzername wurde falsch eingegeben oder nicht gefunden](./media/signin-realm-discovery/typo-username.png)
  
- Der Domänenname wurde falsch eingegeben:
  
    ![Der Domänenname wurde falsch eingegeben oder nicht gefunden](./media/signin-realm-discovery/typo-domain.png)
  
- Der Benutzer versucht, sich mit einer bekannten Consumerdomäne anzumelden:
  
    ![Anmeldung mit einem bekannten Consumerdomäne](./media/signin-realm-discovery/consumer-domain.png)
  
- Das Kennwort wurde falsch eingegeben, aber der Benutzername ist richtig:  
  
    ![Kennwort bei richtigem Benutzernamen falsch eingegeben](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Dieses Feature könnte sich auf Verbunddomänen auswirken, die sich auf die alte Startbereichsermittlung auf Domänenebene verlassen, um den Verbund zu erzwingen. Informationen zu Updates beim Hinzufügen von Unterstützung für Verbunddomänen finden Sie unter [Startbereichsermittlung während der Anmeldung für Microsoft 365-Dienste](https://azure.microsoft.com/updates/signin-hrd/). Mittlerweile haben einige Organisationen ihre Mitarbeiter geschult, sich mit einem Benutzernamen anzumelden, der in Azure Active Directory nicht vorhanden ist, aber den richtigen Domänennamen enthält, weil Benutzer derzeit über die Domänennamen zum Domänenendpunkt ihrer Organisation geleitet werden. Das neue Anmeldeverhalten lässt dies nicht zu. Der Benutzer wird aufgefordert, den Benutzernamen zu korrigieren, und darf sich nicht mit einem Benutzernamen anmelden, der nicht in Azure Active Directory vorhanden ist.
>
> Wenn Sie oder Ihre Organisation Verfahren nutzen, die das alte Verhalten erfordern, sollten Organisationsadministratoren unbedingt die Dokumentation zu Anmeldung und Authentifizierung für Ihre Mitarbeiter anpassen und Schulungen für die Anmeldung mit ihrem Azure Active Directory-Benutzernamen durchführen.
  
Wenn Sie Probleme mit dem neuen Verhalten haben, hinterlassen Sie Anmerkungen im Abschnitt **Feedback** dieses Artikels.  

## <a name="next-steps"></a>Nächste Schritte

[Anpassen des Brandings Ihrer Anmeldung](../fundamentals/add-custom-domain.md)
