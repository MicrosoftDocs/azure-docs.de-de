---
title: 'B2B Collaboration: API und Anpassung – Azure Active Directory | Microsoft-Dokumentation'
description: Die Azure Active Directory B2B-Zusammenarbeit ermöglicht Geschäftspartnern den gezielten Zugriff auf Ihre Unternehmensanwendungen und unterstützt so Ihre unternehmensübergreifenden Beziehungen.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 04/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0369988bc6f6503f9940e6aabccb91ab843d63f5
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65811880"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Azure Active Directory B2B-Zusammenarbeit: API und Anpassung

Viele Kunden haben uns berichtet, dass sie den Einladungsprozess auf die Anforderungen ihres Unternehmens anpassen möchten. Mit unserer API können Sie genau das tun. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Funktionen der Einladungs-API

Die API bietet die folgenden Funktionen:

1. Laden Sie einen externen Benutzer mit einer *beliebigen* E-Mail-Adresse ein.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Legen Sie fest, an welche URL Benutzer weitergeleitet werden, nachdem sie Ihre Einladung angenommen haben.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Senden Sie die Standardeinladung über uns.

    ```
    "sendInvitationMessage": true
    ```

   Fügen Sie eine anpassbare Nachricht an den Empfänger hinzu.

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. Sie können Benutzer auf Cc setzen, die Sie über die Einladung dieses Benutzers informieren möchten.

5. Sie können Ihren Einladungs- und Onboardingworkflow auch vollständig benutzerdefiniert anpassen, indem Sie Benachrichtigungen nicht über Azure AD senden.

    ```
    "sendInvitationMessage": false
    ```

   In diesem Fall gibt die API eine Einlösungs-URL zurück, die Sie in eine E-Mail-Vorlage, eine Sofortnachricht oder eine andere Verteilungsmethode Ihrer Wahl einbetten können.

6. Wenn Sie Administrator sind, können Sie den Benutzer als Mitglied einladen.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Autorisierungsmodell

Die API kann in folgenden Autorisierungsmodi ausgeführt werden:

### <a name="app--user-mode"></a>Modus „App und Benutzer“

In diesem Modus benötigt der jeweilige Benutzer der API die Berechtigungen, B2B-Einladungen zu erstellen.

### <a name="app-only-mode"></a>Reiner App-Modus

Im reinen App-Modus benötigt die App den Berechtigungsbereich „User.Invite.All“, damit die Einladung erfolgreich ist.

Weitere Informationen finden Sie unter https://developer.microsoft.com/graph/docs/authorization/permission_scopes.


## <a name="powershell"></a>PowerShell

Sie können PowerShell verwenden, um externe Benutzer ganz einfach in eine Organisation einzuladen und dieser hinzuzufügen. Erstellen Sie eine Einladung mit dem Cmdlet:

```powershell
New-AzureADMSInvitation
```

Sie können die folgenden Optionen verwenden:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

### <a name="invitation-status"></a>Einladungsstatus

Nachdem Sie einem externen Benutzer eine Einladung gesendet haben, können Sie mithilfe des Cmdlets **Get-AzureADUser** feststellen, ob er sie angenommen hat. Die folgenden Eigenschaften von „Get-AzureADUser“ werden aufgefüllt, wenn einem externen Benutzer eine Einladung gesendet wird:

* **UserState** zeigt an, ob der Status der Einladung **PendingAcceptance** (Annahme ausstehend) oder **Accpeted** (Angenommen) ist.
* **UserStateChangedOn** zeigt den Zeitstempel der neuesten Änderungen der Eigenschaft **UserState** an.

Sie können dann mithilfe der Option **Filter** die Ergebnisse nach **UserState** filtern. Das folgende Beispiel zeigt, wie Sie Ergebnisse so filtern, dass nur Benutzer mit einer ausstehenden Einladung angezeigt werden. Das Beispiel zeigt außerdem die Option **Format-List**, mit der Sie die anzuzeigenden Eigenschaften angeben können. 
 

```powershell
Get-AzureADUser -Filter "UserState eq 'PendingAcceptance'" | Format-List -Property DisplayName,UserPrincipalName,UserState,UserStateChangedOn
```

> [!NOTE]
> Stellen Sie sicher, dass Sie die neueste Version des AzureAD PowerShell-Moduls oder des AzureADPreview PowerShell-Moduls verwenden. 

## <a name="see-also"></a>Weitere Informationen

Sehen Sie sich die Einladungs-API-Referenz in [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) an.

## <a name="next-steps"></a>Nächste Schritte

- [Was ist die Azure AD B2B-Zusammenarbeit?](what-is-b2b.md)
- [Die Elemente der Einladungs-E-Mail für die B2B-Zusammenarbeit](invitation-email-elements.md)
- [B2B-Zusammenarbeit: Einlösen von Einladungen](redemption-experience.md)
- [Hinzufügen von Benutzern der B2B-Zusammenarbeit ohne Einladung](add-user-without-invite.md)