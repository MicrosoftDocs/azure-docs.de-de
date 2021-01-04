---
title: Zuweisen von Azure-Ressourcenrollen zu Gästen in PIM – Azure AD | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie in Azure AD Privileged Identity Management (PIM) externe Gastbenutzer einladen und Azure-Ressourcenrollen zuweisen.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4e2e18f3bb9d1c972d805a60493897d605921e4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010638"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>Laden Sie Gastbenutzer ein, und weisen Sie ihnen Azure-Ressourcenrollen in Privileged Identity Management zu.

Azure Active Directory (Azure AD)-Gastbenutzer sind Teil der Business-to-Business (B2B)-Zusammenarbeitsfunktionen innerhalb von Azure AD, sodass Sie externe Gastbenutzer und Anbieter als Gäste in Azure AD verwalten können. Wenn Sie B2B-Zusammenarbeit mit Azure AD Privileged Identity Management (PIM) kombinieren, können Sie Ihre Compliance- und Governanceanforderungen auf Gäste erweitern. Beispielsweise können Sie die folgenden Privileged Identity Management-Funktionen für Azure-Identitätsaufgaben mit Gästen verwenden:

- Zuweisen des Zugriffs auf bestimmte Azure-Ressourcen
- Aktivieren des Just-in-Time-Zugriffs
- Angeben der Zuweisungsdauer und des Enddatums
- Mehrstufige Authentifizierung bei aktiver Zuweisung oder Aktivierung erforderlich
- Ausführen von Zugriffsüberprüfungen
- Verwenden von Warnungen und Überwachungsprotokollen

In diesem Artikel wird beschrieben, wie Sie einen Gast in Ihre Organisation einladen und ihren Zugriff auf Azure-Ressourcen mithilfe von Privileged Identity Management verwalten.

## <a name="when-would-you-invite-guests"></a>Wann laden Sie Gäste ein?

Einige Beispiele für Situationen, in denen Sie möglicherweise Gäste in Ihre Organisation einladen:

- Zugriff für einen externen selbstständigen Anbieter, der nur über ein E-Mail-Konto verfügt, auf Ihre Azure-Ressourcen für ein Projekt
- Zugriff für einen externen Partner in einer großen Organisation, die lokale Active Directory-Verbunddienste (AD FS) verwendet, auf Ihre Kostenmanagementanwendung
- Temporärer Zugriff für Supporttechniker außerhalb Ihrer Organisation (z.B. Microsoft-Support) auf Ihre Azure-Ressource zur Behebung von Problemen

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Wie funktioniert Kollaboration mit B2B-Gästen?

Wenn Sie B2B Collaboration verwenden, können Sie einen externen Benutzer als Gast in Ihre Organisation einladen. Der Gast kann als Benutzer in Ihrer Organisation verwaltet werden, aber ein Gast muss in seiner eigenen Organisation authentifiziert sein, und nicht in Ihrer Azure AD-Organisation. Dies bedeutet, dass ein Gast, der keinen Zugriff mehr auf seine eigene Organisation hat, auch den Zugriff auf Ihre Organisation verlieren wird. Wenn der Gast beispielsweise seine Organisation verlässt, verliert er automatisch den Zugriff auf alle Ressourcen, die Sie in Azure AD für ihn freigegeben haben, ohne dass Sie etwas tun müssen. Weitere Informationen zur B2B-Zusammenarbeit finden Sie unter [Was ist der Gastbenutzerzugriff in Azure Active Directory-B2B?](../external-identities/what-is-b2b.md).

![Diagramm, das zeigt, wie ein Gastbenutzer in seinem eigenen Heimatverzeichnis authentifiziert wird](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Überprüfen von Einstellungen für die Kollaboration mit Gästen

Sie sollten Ihre Einstellungen für die Kollaboration mit Gästen überprüfen, um sicherzustellen, dass Sie Gäste in Ihre Organisation einladen können.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.

1. Wählen Sie **Azure Active Directory** > **Benutzereinstellungen** aus.

1. Wählen Sie **Externe Einstellungen zur Kollaboration verwalten** aus.

    ![Seite „Einstellungen für externe Zusammenarbeit“ mit Einstellungen für Berechtigungen, Einladungen und Einschränkungen der Zusammenarbeit](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Stellen Sie sicher, dass der Schalter **Administratoren und Benutzer mit der Rolle „Gasteinladender“ können einladen** auf **Ja** festgelegt ist.

## <a name="invite-a-guest-and-assign-a-role"></a>Einladen eines Gasts und zuweisen einer Rolle

Mithilfe von Privileged Identity Management können Sie einen Gast einladen und ihn als berechtigt für eine Azure-Ressourcenrolle festlegen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit einem Benutzer an, der ein Mitglied der Rolle [Administrator für privilegierte Rollen](../roles/permissions-reference.md#privileged-role-administrator) oder [Benutzeradministrator](../roles/permissions-reference.md#user-administrator) ist.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Wählen Sie **Azure-Ressourcen** aus.

1. Verwenden Sie den **Ressourcenfilter** zum Filtern der Liste verwalteter Ressourcen.

1. Wählen Sie die Ressource aus, die Sie verwalten möchten, z. B. eine Ressource, eine Ressourcengruppe, ein Abonnement oder eine Verwaltungsgruppe.

    Sie sollten den Umfang ausschließlich auf die vom Gast benötigten Ressourcen beschränken.

1. Wählen Sie unter „Verwalten“ den Eintrag **Rollen** aus, um die Liste der Rollen für Azure-Ressourcen anzuzeigen.

    ![Liste der Azure-Ressourcenrollen mit Anzahl der aktiven und berechtigten Benutzer](./media/pim-resource-roles-external-users/resources-roles.png)

1. Wählen Sie die minimale Rolle aus, die der Benutzer benötigt.

    ![Ausgewählte Rollenseite mit Auflistung der aktuellen Mitglieder dieser Rolle](./media/pim-resource-roles-external-users/selected-role.png)

1. Wählen Sie auf der Seite der Rolle **Mitglied hinzufügen** aus, um den Bereich „Neue Zuweisung“ zu öffnen.

1. Klicken Sie auf **Mitglied oder Gruppe auswählen**.

    ![Neue Zuweisung: Auswählen eines Mitglieder- oder Gruppenbereichs mit Auflistung von Benutzern und Gruppen zusammen mit einer Einladungsoption](./media/pim-resource-roles-external-users/select-member-group.png)

1. Klicken Sie auf **Einladen**, um einen Gast einzuladen.

    ![Seite zum Einladen eines Gasts mit Feldern zur Eingabe einer E-Mail-Adresse und einer persönlichen Nachricht](./media/pim-resource-roles-external-users/invite-guest.png)

1. Nachdem Sie einen Gast ausgewählt haben, klicken Sie auf **Einladen**.

    Der Gast sollte als ausgewähltes Mitglied hinzugefügt werden.

1. Klicken Sie im Bereich **Mitglied oder Gruppe auswählen** auf **Auswählen**.

1. Wählen Sie im Bereich **Mitgliedschaftseinstellungen** den Zuweisungstyp und die Dauer aus.

    ![Neue Zuweisung: Seite für Mitgliedschaftseinstellungen mit Optionen zur Angabe von Zuweisungstyp, Startdatum und Enddatum](./media/pim-resource-roles-external-users/membership-settings.png)

1. Wählen Sie zum Abschließen der Zuweisung **Fertig** und dann **Hinzufügen** aus.

    Die Rollenzuweisung für den Gast wird in der Rollenliste angezeigt.

    ![Rollenseite, auf der der Gast als berechtigt aufgelistet ist](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Aktivieren der Rolle als Gast

Wenn Sie ein externer Benutzer sind, müssen Sie die Einladung, ein Gast in der Azure AD-Organisation zu sein, annehmen und möglicherweise Ihre Rollenzuweisung aktivieren.

1. Öffnen Sie die E-Mail mit Ihrer Einladung. Die E-Mail sieht etwa wie folgt aus.

    ![E-Mail-Einladung mit Verzeichnisname, persönlicher Nachricht und einem „Erste-Schritte“-Link](./media/pim-resource-roles-external-users/email-invite.png)

1. Wählen Sie den Link **Los geht's** in der E-Mail aus.

1. Überprüfen Sie die Berechtigungen, und klicken Sie dann auf **Annehmen**.

    ![Überprüfen der Berechtigungenseite in einem Browser mit einer Liste von Berechtigungen, die die Organisation überprüfen möchte](./media/pim-resource-roles-external-users/invite-accept.png)

1. Möglicherweise werden Sie aufgefordert, den Nutzungsbedingungen zuzustimmen und anzugeben, ob Sie angemeldet bleiben möchten. Wenn Sie für eine Rolle *berechtigt* sind, haben Sie im Azure-Portal noch keinen Zugriff auf Ressourcen.

1. Um Ihre Rollenzuweisung zu aktivieren, öffnen Sie die E-Mail mit dem Link „Rolle aktivieren“. Die E-Mail sieht etwa wie folgt aus.

    ![E-Mail, die angibt, dass Sie für eine Rolle berechtigt sind, mit einem Link „Rolle aktivieren“](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Wählen Sie **Rolle aktivieren** aus, um Ihre berechtigten Rollen in Privileged Identity Management zu öffnen.

    ![Seite „Meine Rollen“ in Privileged Identity Management mit Auflistung Ihrer berechtigten Rollen](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. Wählen Sie unter „Aktion“ den Link **Aktivieren** aus.

    Je nach den Rolleneinstellungen müssen Sie möglicherweise bestimmte Informationen angeben, um die Rolle zu aktivieren.

1. Nachdem Sie die Einstellungen für die Rolle angegeben haben, klicken Sie auf **Aktivieren**, um die Rolle zu aktivieren.

    ![Aktivierungsseite mit Auflistung des Bereichs und Optionen zur Angabe von Startzeit, Dauer und Grund](./media/pim-resource-roles-external-users/activate-role.png)

    Sofern der Administrator Ihre Anforderung nicht genehmigen muss, sollten Sie nun Zugriff auf die angegebenen Ressourcen haben.

## <a name="view-activity-for-a-guest"></a>Anzeigen der Aktivität für einen Gast

Sie können Überwachungsprotokolle anzeigen, um die Aktivitäten von Gästen nachzuverfolgen.

1. Öffnen Sie Privileged Identity Management als Administrator, und wählen Sie die freigegebene Ressource aus.

1. Wählen Sie **Ressourcenüberwachung** aus, um die Aktivität für diese Ressource anzuzeigen. Im folgenden Beispiel wird die Aktivität für eine Ressourcengruppe angezeigt.

    ![Azure-Ressourcen: Ressourcenüberwachungsseite mit Auflistung von Zeit, Anforderer und Aktion](./media/pim-resource-roles-external-users/audit-resource.png)

1. Wählen Sie zum Anzeigen der Aktivität für den Gast **Azure Active Directory** > **Benutzer** > *Gastname* aus.

1. Wählen Sie **Überwachungsprotokolle** aus, um die Überwachungsprotokolle für die Organisation anzuzeigen. Bei Bedarf können Sie Filter angeben.

    ![Verzeichnisüberwachungsprotokolle mit Angaben zu Datum, Ziel, „Initiiert von (Akteur)“ und Aktivität](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen von Azure AD-Administratorrollen in Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Was ist der Gastbenutzerzugriff in Azure AD-B2B-Zusammenarbeit?](../external-identities/what-is-b2b.md)