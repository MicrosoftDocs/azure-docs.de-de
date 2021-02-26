---
title: 'B2B-Zusammenarbeit: API und Anpassung – Azure Active Directory'
description: Die Azure Active Directory B2B-Zusammenarbeit ermöglicht Geschäftspartnern den gezielten Zugriff auf Ihre Unternehmensanwendungen und unterstützt so Ihre unternehmensübergreifenden Beziehungen.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8160859bb782ee8ffc4fef5ee03b61b6f54be1bb
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99548660"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Azure Active Directory B2B-Zusammenarbeit: API und Anpassung

Viele Kunden haben uns berichtet, dass sie den Einladungsprozess auf die Anforderungen ihres Unternehmens anpassen möchten. Mit unserer API können Sie genau das tun. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](/graph/api/resources/invitation)

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

## <a name="determine-if-a-user-was-already-invited-to-your-directory"></a>Ermitteln von bereits in Ihr Verzeichnis eingeladenen Benutzern

Mit der Einladungs-API können Sie ermitteln, ob ein Benutzer bereits in Ihrem Ressourcenmandanten vorhanden ist. Dies kann hilfreich sein, wenn Sie eine App entwickeln, die zum Einladen eines Benutzers die Einladungs-API verwendet. Wenn der Benutzer bereits in Ihrem Ressourcenverzeichnis vorhanden ist, erhält er keine Einladung. Sie können also zuerst eine Abfrage ausführen, um zu ermitteln, ob die E-Mail-Adresse bereits als Benutzerprinzipalname (UPN) oder andere Anmeldeeigenschaft vorhanden ist.

1. Stellen Sie sicher, dass die E-Mail-Domäne des Benutzers nicht zur überprüften Domäne Ihres Ressourcenmandanten gehört.
2. Verwenden Sie zum Abfragen des Benutzers im Ressourcenmandanten die folgende Abfrage, wobei {0} der E-Mail-Adresse des einzuladenden Benutzers entspricht:

   ```
   “userPrincipalName eq '{0}' or mail eq '{0}' or proxyAddresses/any(x:x eq 'SMTP:{0}') or signInNames/any(x:x eq '{0}') or otherMails/any(x:x eq '{0}')"
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

Sehen Sie sich die Einladungs-API-Referenz in [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](/graph/api/resources/invitation) an.

## <a name="next-steps"></a>Nächste Schritte

- [Was ist die Azure AD B2B-Zusammenarbeit?](what-is-b2b.md)
- [Die Elemente der Einladungs-E-Mail von B2B-Zusammenarbeit](invitation-email-elements.md)
- [B2B-Zusammenarbeit: Einlösen von Einladungen](redemption-experience.md)
- [Hinzufügen von Benutzern der B2B-Zusammenarbeit ohne Einladung](add-user-without-invite.md)