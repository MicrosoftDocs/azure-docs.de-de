---
title: Problembehandlung für die B2B-Zusammenarbeit – Azure Active Directory | Microsoft-Dokumentation
description: Lösungen für häufig auftretende Probleme bei der Azure Active Directory B2B-Zusammenarbeit
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: mimart
author: v-miegge
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4185d29ff1770ed9549b4b63a2e5da579bcf054f
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65767155"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Problembehandlung für die Azure Active Directory B2B-Zusammenarbeit

Hier finden Sie Lösungen für häufig auftretende Probleme bei der Azure Active Directory B2B-Zusammenarbeit.

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Ich habe einen externen Benutzer hinzugefügt, dieser wird aber im globalen Adressbuch oder in der Personenauswahl nicht angezeigt.

Falls externe Benutzer in der Liste nicht angezeigt werden: Es kann einige Minuten dauern, bis das Objekt repliziert wurde.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>Ein B2B-Gastbenutzer wird in der SharePoint Online/OneDrive-Personenauswahl nicht angezeigt.

Die Möglichkeit, in der SharePoint Online-Personenauswahl (SPO) nach vorhandenen Gastbenutzern zu suchen, ist dem Verhalten in älteren Versionen entsprechend standardmäßig deaktiviert.

Sie können dieses Feature mit der Einstellung „ShowPeoplePickerSuggestionsForGuestUsers“ auf Mandanten- und Websitesammlungsebene aktivieren. Sie können dieses Feature mithilfe der Cmdlets „Set-SPOTenant“ und „Set-SPOSite“ festlegen, damit Mitglieder das Verzeichnis nach allen vorhandenen Gastbenutzern durchsuchen können. Änderungen im Mandantenbereich wirken sich nicht auf bereits bereitgestellte SPO-Websites aus.

## <a name="invitations-have-been-disabled-for-directory"></a>Einladungen wurden für das Verzeichnis deaktiviert.

Wenn Sie darüber informiert werden, dass Sie nicht über die Berechtigung verfügen, Benutzer einzuladen, überprüfen Sie unter „Benutzereinstellungen“, ob Ihr Benutzerkonto für die Einladung externer Benutzer autorisiert ist.

![Screenshot mit Einstellungen für externe Benutzer](media/troubleshoot/external-user-settings.png)

Wenn Sie diese Einstellungen kürzlich geändert oder einem Benutzer die Rolle „Gasteinladender“ zugewiesen haben, kann es 15–60 Minuten dauern, bis die Änderungen wirksam werden.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>Der von mir eingeladene Benutzer erhält beim Einlösen eine Fehlermeldung.

Häufige Fehler sind z.B. folgende:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>Der Administrator des Eingeladenen hat die Option deaktiviert, Benutzer im Mandanten zu erstellen, die per E-Mail verifiziert werden.

Dieser Fehler kann auftreten, wenn Benutzer eingeladen werden, deren Organisation Azure Active Directory verwenden, das Konto des entsprechenden Benutzers aber dort nicht vorhanden ist (Benutzer ist z.B. in Azure AD für contoso.com nicht vorhanden). Der Administrator von contoso.com hat möglicherweise eine Richtlinie festgelegt, die das Erstellen von Benutzern verhindert. Der Benutzer muss sich bei seinem Administrator informieren, ob externe Benutzer zulässig sind. Der Administrator des externen Benutzers muss ggf. per E-Mail verifizierte Benutzer in seiner Domäne zulassen (Informationen dazu finden Sie in diesem [Artikel](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) zum Zulassen per E-Mail verifizierter Benutzer).

![Fehler, der besagt, dass der Mandant keine E-Mail-verifizierten Benutzer zulässt](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>Ein Benutzer ist in einer Verbunddomäne noch nicht vorhanden.

Wenn Sie die Verbundauthentifizierung verwenden und der Benutzer noch nicht in Azure Active Directory vorhanden ist, kann dieser Benutzer nicht eingeladen werden.

Um dieses Problem zu lösen, muss der Administrator des externen Benutzers das Benutzerkonto mit Azure Active Directory synchronisieren.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Wie wird „\#“ – normalerweise ein ungültiges Zeichen – in Azure AD synchronisiert?

„\#“ ist ein reserviertes Zeichen in UPNs für Azure AD B2B-Zusammenarbeit oder externe Benutzer, da das eingeladene Konto user@contoso.com zu „user_contoso.com#EXT#@fabrikam.onmicrosoft.com“ wird. Aus diesem Grund dürfen sich lokale UPNs mit \# nicht im Azure-Portal anmelden. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Ich erhalte eine Fehlermeldung, wenn ich externe Benutzer zu einer synchronisierten Gruppe hinzufüge.

Externe Benutzer können nur zu „zugewiesenen“ Gruppen oder „Sicherheitsgruppen“ hinzugefügt werden, nicht zu lokal verwalteten Gruppen.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Mein externer Benutzer hat keine E-Mail zur Einlösung erhalten.

Der Eingeladene sollte seinen Spamfilter überprüfen oder sich mit seinem ISP in Verbindung setzen, um sicherzustellen, dass die folgende Adresse zulässig ist: Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Die benutzerdefinierte Nachricht wird nicht immer in Einladungsnachrichten eingefügt.

Zur Einhaltung von Datenschutzgesetzen fügen unsere APIs in den folgenden Situationen keine benutzerdefinierten Meldungen in die E-Mail-Einladung ein:

- Der einladende Benutzer besitzt keine E-Mail-Adresse im einladenden Mandanten.
- Ein AppService-Prinzipal sendet die Einladung.

Wenn dies für Sie ein wichtiges Szenario ist, können Sie das Senden von E-Mail-Einladungen durch unsere API unterdrücken und sie über ein E-Mail-Verfahren Ihrer Wahl senden. Vergewissern Sie sich unbedingt bei der Rechtsabteilung Ihrer Organisation, dass auf diese Weise gesendete E-Mail-Nachrichten auch den Datenschutzgesetzen entsprechen.

## <a name="you-receive-an-aadsts65005-error-when-you-try-to-log-in-to-an-azure-resource"></a>Beim Versuch, sich bei einer Azure-Ressource anzumelden, erhalten Sie den Fehler „AADSTS65005“.

Ein Benutzer mit einem Gastkonto kann sich nicht anmelden und erhält die folgende Fehlermeldung:

    AADSTS65005: Using application 'AppName' is currently not supported for your organization contoso.com because it is in an unmanaged state. An administrator needs to claim ownership of the company by DNS validation of contoso.com before the application AppName can be provisioned.

Der Benutzer verfügt über ein Azure-Benutzerkonto und ist ein viraler Mandant, der verworfen wurde oder nicht verwaltet wird. Darüber hinaus stehen im Mandanten keine globalen Administratoren oder Unternehmensadministratoren zur Verfügung.

Um dieses Problem zu beheben, müssen Sie den verworfenen Mandanten übernehmen. Weitere Informationen finden Sie unter [Übernehmen eines nicht verwalteten Verzeichnisses als Administrator in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover). Darüber hinaus müssen Sie für das betreffende Domänensuffix auf den DNS mit Internetzugriff zugreifen, um direkt nachzuweisen, dass Sie Kontrolle über den Namespace haben. Nachdem der Mandant wieder einen verwalteten Zustand aufweist, sollten Sie mit dem Kunden erörtern, ob die beste Option für seine Organisation darin besteht, die Benutzer und den überprüften Domänennamen zu belassen.

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>Ein Gastbenutzer mit einem Just-in-Time-Mandanten oder „viralen“ Mandanten kann sein Kennwort nicht zurücksetzen

Wenn der Identitätsmandant ein Just-In-Time-Mandant (JIT) oder ein „viraler“ Mandant ist (es sich also um einen separaten, nicht verwalteten Azure-Mandanten handelt), kann nur der Gastbenutzer sein Kennwort zurücksetzen. In einigen Fällen [übernimmt die Organisation die Verwaltung der viralen Mandanten](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover), die erstellt werden, wenn Mitarbeiter ihre geschäftliche E-Mail-Adresse für die Anmeldung bei Diensten verwenden. Wenn die Organisation einen viralen Mandanten übernommen hat, kann nur ein Administrator in dieser Organisation das Kennwort des Benutzers zurücksetzen oder SSPR aktivieren. Bei Bedarf können Sie als einladende Organisation das Gastkonto für den Benutzer aus dem Verzeichnis entfernen und erneut eine Einladung senden.

## <a name="next-steps"></a>Nächste Schritte

[Support für die B2B-Zusammenarbeit](get-support.md)
