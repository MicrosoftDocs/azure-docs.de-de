---
title: 'Einladen von internen Benutzern zur B2B-Zusammenarbeit: Azure AD'
description: Wenn Sie interne Benutzerkonten für Partner, Distributoren, Lieferanten, Anbieter und andere Gäste verwenden, können Sie die Umstellung auf die Azure AD B2B-Zusammenarbeit durchführen, indem Sie die Gäste zur Anmeldung mit ihren eigenen externen Anmeldeinformationen einladen. Verwenden Sie entweder PowerShell oder die Einladungs-API von Microsoft Graph.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 802307a21873d15242c2e387ec0defe35f50bb20
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576429"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>Einladen von internen Benutzern zur B2B-Zusammenarbeit

Bevor die Azure AD B2B-Zusammenarbeit verfügbar war, konnten Organisationen mit Distributoren, Lieferanten, Anbietern und anderen Gästen zusammenarbeiten, indem sie interne Anmeldeinformationen für sie eingerichtet haben. Wenn Sie solche internen Gastbenutzer haben, können Sie sie zur Verwendung von B2B Collaboration einladen. Diese B2B-Gastbenutzer können ihre eigenen Identitäten und Anmeldeinformationen nutzen, um sich anzumelden, und Sie müssen keine Kennwörter oder Kontolebenszyklen verwalten.

Das Senden einer Einladung an ein vorhandenes internes Konto ermöglicht es Ihnen, die Objekt-ID, den UPN, Gruppenmitgliedschaften und App-Zuweisungen für den Benutzer beizubehalten. Sie müssen den Benutzer nicht manuell löschen und erneut einladen und auch die Ressourcen nicht neu zuweisen. Verwenden Sie zum Einladen des Benutzers die Einladungs-API, um zusammen mit der Einladung sowohl das interne Benutzerobjekt als auch die E-Mail-Adresse des Gastbenutzers zu übergeben. Wenn der Benutzer die Einladung annimmt, stellt der B2B-Dienst das vorhandene interne Benutzerobjekt auf einen B2B-Benutzer um. Danach muss sich der Benutzer bei Cloudressourcendiensten mit seinen B2B-Anmeldeinformationen anmelden.

## <a name="things-to-consider"></a>Zu beachtende Aspekte

- **Zugriff auf lokale Ressourcen:** Nachdem der Benutzer zu B2B Collaboration eingeladen wurde, kann er weiterhin seine internen Anmeldeinformationen für den Zugriff auf lokale Ressourcen verwenden. Dies können Sie verhindern, indem Sie das Kennwort für das interne Konto zurücksetzen oder ändern. Eine Ausnahme ist die [Authentifizierung mit Einmalkennung per E-Mail](one-time-passcode.md): Wenn die Authentifizierungsmethode des Benutzers in die Einmalkennung geändert wird, kann er seine internen Anmeldeinformationen nicht mehr verwenden.

- **Abrechnung**: Dieses Feature ändert nicht den Benutzertyp für den Benutzer, sodass das Abrechnungsmodell des Benutzers nicht automatisch auf das Abrechnungsmodell [External Identities monthly active user (MAU)](external-identities-pricing.md) (Monatlich aktive Benutzer für externe Identitäten (MAU)) umgestellt wird. Ändern Sie den Benutzertyp für den Benutzer in `guest`, um das MAU-Abrechnungsmodell zu aktivieren. Beachten Sie außerdem, dass der Azure AD-Mandant mit [einem Azure-Abonnement](external-identities-pricing.md#link-your-azure-ad-tenant-to-a-subscription) verknüpft sein muss, um das MAU-Abrechnungsmodell zu aktivieren.

- **Die Einladung ist unidirektional:** Sie können interne Benutzer zur Verwendung der B2B-Zusammenarbeit einladen, aber Sie können die B2B-Anmeldeinformationen nach dem Hinzufügen nicht mehr entfernen. Um für den Benutzer wieder die Umstellung auf einen rein internen Benutzer durchzuführen, müssen Sie das Benutzerobjekt löschen und ein neues erstellen.

- **Teams:** Wenn der Benutzer mit seinen externen Anmeldeinformationen auf Teams zugreift, ist sein Mandant anfangs nicht in der Teams-Mandantenauswahl verfügbar. Der Benutzer kann mithilfe einer URL auf Teams zugreifen, die den Mandantenkontext enthält (z. B. `https://team.microsoft.com/?tenantId=<TenantId>`). Danach wird der Mandant in der Teams-Mandantenauswahl verfügbar.

- **Lokal synchronisierte Benutzer**: Für Benutzerkonten, die zwischen dem lokalen Standort und der Cloud synchronisiert werden, bleibt das lokale Verzeichnis die Autoritätsquelle, nachdem die Einladung zur B2B-Zusammenarbeit erfolgt ist. Alle Änderungen, die Sie am lokalen Konto vornehmen, werden mit dem Cloudkonto synchronisiert, z. B. das Deaktivieren oder Löschen des Kontos. Aus diesem Grund können Sie durch das einfache Löschen des lokalen Kontos nicht verhindern, dass sich der Benutzer bei seinem lokalen Konto anmeldet, während das Cloudkonto beibehalten wird. Sie können aber das Kennwort für das lokale Konto auf eine zufällige GUID oder einen anderen unbekannten Wert festlegen.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>Gewusst wie: Einladen von internen Benutzern zur B2B-Zusammenarbeit

Sie können PowerShell oder die Einladungs-API nutzen, um eine B2B-Einladung an einen internen Benutzer zu senden. Stellen Sie sicher, dass die E-Mail-Adresse, die Sie für die Einladung verwenden möchten, für das interne Benutzerobjekt als externe E-Mail-Adresse festgelegt ist.

- Sie müssen die E-Mail-Adresse in der „User.OtherMails“-Eigenschaft für die Einladung verwenden.
- Die Domäne in der „Mail“-Eigenschaft des Benutzers muss mit dem Konto übereinstimmen, dass dieser für die Anmeldung verwendet. Andernfalls kann der Benutzer von einigen Diensten, z. B. Teams, nicht authentifiziert werden.

Standardmäßig wird dem Benutzer beim Einladevorgang eine E-Mail mit einem Hinweis zur Einladung gesendet, aber Sie können diese E-Mail auch unterdrücken und eine eigene Nachricht senden.

> [!NOTE]
> Sie können zum Senden einer eigenen E-Mail oder einer anderen Nachricht `New-AzureADMSInvitation` mit `-SendInvitationMessage:$false` verwenden, um Benutzer im Hintergrund einzuladen. Anschließend senden Sie Ihre E-Mail an den Benutzer, für den eine Umstellung durchgeführt wurde. Weitere Informationen finden Sie unter [Azure AD B2B-Zusammenarbeit: API und Anpassung](customize-invitation-api.md).

## <a name="use-powershell-to-send-a-b2b-invitation"></a>Verwenden von PowerShell zum Senden einer B2B-Einladung

Sie benötigen die Azure AD PowerShell-Modulversion 2.0.2.130 oder höher. Verwenden Sie den folgenden Befehl, um ein Update auf das neueste Azure AD PowerShell-Modul durchzuführen und den internen Benutzer zu B2B Collaboration einzuladen:

```powershell
Uninstall-Module AzureAD
Install-Module AzureAD
$ADGraphUser = Get-AzureADUser -objectID "UPN of Internal User"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>Verwenden der Einladungs-API zum Senden einer B2B-Einladung

Das folgende Beispiel veranschaulicht, wie Sie die Einladungs-API aufrufen, um einen internen Benutzer als B2B-Benutzer einzuladen.

```json
POST https://graph.microsoft.com/v1.0/invitations
Authorization: Bearer eyJ0eX...
ContentType: application/json
{
    "invitedUserEmailAddress": "<<external email>>",
    "sendInvitationMessage": true,
    "invitedUserMessageInfo": {
        "messageLanguage": "en-US",
        "ccRecipients": [
            {
                "emailAddress": {
                    "name": null,
                    "address": "<<optional additional notification email>>"
                }
            }
        ],
        "customizedMessageBody": "<<custom message>>"
    },
    "inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",
    "invitedUser": {
        "id": "<<ID for the user you want to convert>>"
    }
}
```

Die Antwort auf die API entspricht der Antwort, die Sie erhalten, wenn Sie einen neuen Gastbenutzer für das Verzeichnis einladen.
## <a name="next-steps"></a>Nächste Schritte

- [B2B-Zusammenarbeit: Einlösen von Einladungen](redemption-experience.md)
