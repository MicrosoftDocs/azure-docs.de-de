---
title: Einladen von Gastbenutzern und Zuweisen von Azure-Ressourcenrollen in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie in Azure AD Privileged Identity Management (PIM) externe Gastbenutzer einladen und Azure-Ressourcenrollen zuweisen.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0afec1d6eded25a2d9b2389c950e2e21e06e0d54
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307060"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-pim"></a>Einladen von Gastbenutzern und Zuweisen von Azure-Ressourcenrollen in PIM

Bei Azure Active Directory (Azure AD) Business-to-Business (B2B) handelt es sich um eine Gruppe von Funktionen in Azure AD, über die Organisationen unter Verwendung eines beliebigen Kontos mit externen Gastbenutzern (Gästen) und Anbietern zusammenarbeiten können. Wenn Sie B2B mit Azure AD Privileged Identity Management (PIM) kombinieren, gelten Ihre Konformitäts- und Governanceanforderungen weiterhin für Gäste. Beispielsweise können Sie die folgenden PIM-Funktionen für Azure-Identitätsaufgaben mit Gästen verwenden:

- Zuweisen des Zugriffs auf bestimmte Azure-Ressourcen
- Aktivieren des Just-in-Time-Zugriffs
- Angeben der Zuweisungsdauer und des Enddatums
- Erzwingen von MFA für aktive Zuweisung oder Aktivierung
- Ausführen von Zugriffsüberprüfungen
- Verwenden von Warnungen und Überwachungsprotokollen

In diesem Artikel wird beschrieben, wie Sie einen Gast in Ihre Organisation einladen und ihren Zugriff auf Azure-Ressourcen mithilfe von Privileged Identity Management verwalten.

## <a name="when-would-you-invite-guests"></a>Wann laden Sie Gäste ein?

Einige Beispielszenarios, in denen Sie möglicherweise Gäste in Ihre Organisation einladen:

- Zugriff für einen externen selbstständigen Anbieter, der nur über ein E-Mail-Konto verfügt, auf Ihre Azure-Ressourcen für ein Projekt
- Zugriff für einen externen Partner in einer großen Organisation, die lokale Active Directory-Verbunddienste (AD FS) verwendet, auf Ihre Kostenmanagementanwendung
- Temporärer Zugriff für Supporttechniker außerhalb Ihrer Organisation (z.B. Microsoft-Support) auf Ihre Azure-Ressource zur Behebung von Problemen

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Wie funktioniert Kollaboration mit B2B-Gästen?

Wenn Sie B2B Collaboration verwenden, können Sie einen externen Benutzer als Gast in Ihre Organisation einladen. Der Gast scheint in Ihrer Organisation vorhanden zu sein, ihm sind jedoch keine Anmeldeinformationen zugeordnet. Wenn ein Gast authentifiziert werden soll, muss er in seiner eigenen Organisation und nicht in Ihrer Organisation authentifiziert werden. Dies bedeutet, dass ein Gast, der keinen Zugriff mehr auf seine eigene Organisation hat, auch den Zugriff auf Ihre Organisation verlieren wird. Wenn der Gast beispielsweise seine Organisation verlässt, verliert er automatisch den Zugriff auf alle Ressourcen, die Sie in Azure AD für ihn freigegeben haben, ohne dass Sie etwas tun müssen. Weitere Informationen zu B2B finden Sie unter [Was ist der Gastzugriff in Azure Active Directory-B2B?](../b2b/what-is-b2b.md)

![B2B und Gast](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Überprüfen von Einstellungen für die Kollaboration mit Gästen

Sie sollten Ihre Einstellungen für die Kollaboration mit Gästen überprüfen, um sicherzustellen, dass Sie Gäste in Ihre Organisation einladen können.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.

1. Klicken Sie auf **Azure Active Directory** > **Benutzereinstellungen**.

1. Klicken Sie auf **Externe Einstellungen zur Kollaboration verwalten**.

    ![Einstellungen für externe Kollaboration](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Stellen Sie sicher, dass der Schalter **Administratoren und Benutzer mit der Rolle „Gasteinladender“ können einladen** auf **Ja** festgelegt ist.

## <a name="invite-a-guest-and-assign-a-role"></a>Einladen eines Gasts und zuweisen einer Rolle

Mithilfe von PIM können Sie einen Gast einladen und ihn genau wie einen Mitgliedsbenutzer als berechtigt für eine Azure-Ressourcenrolle festlegen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit einem Benutzer an, der ein Mitglied der Rolle [Administrator für privilegierte Rollen](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) oder [Benutzeradministrator](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) ist.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Klicken Sie auf **Azure-Ressourcen**.

1. Verwenden Sie den **Ressourcenfilter** zum Filtern der Liste verwalteter Ressourcen.

1. Klicken Sie auf die Ressource, die Sie verwalten möchten, z.B. eine Ressource, eine Ressourcengruppe, ein Abonnement oder eine Verwaltungsgruppe.

    Sie sollten den Umfang ausschließlich auf die vom Gast benötigten Ressourcen beschränken.

1. Klicken Sie unter „Verwalten“ auf **Rollen**, um die Liste der Rollen für Azure-Ressourcen anzuzeigen.

    ![Azure-Ressourcenrollen](./media/pim-resource-roles-external-users/resources-roles.png)

1. Klicken Sie auf die minimale Rolle, die der Benutzer benötigt.

    ![Ausgewählte Rolle](./media/pim-resource-roles-external-users/selected-role.png)

1. Klicken Sie auf der Seite der Rolle auf **Mitglied hinzufügen**, um den Bereich „Neue Zuweisung“ zu öffnen.

1. Klicken Sie auf **Mitglied oder Gruppe auswählen**.

    ![Mitglied oder Gruppe auswählen](./media/pim-resource-roles-external-users/select-member-group.png)

1. Klicken Sie auf **Einladen**, um einen Gast einzuladen.

    ![Gast einladen](./media/pim-resource-roles-external-users/invite-guest.png)

1. Nachdem Sie einen Gast ausgewählt haben, klicken Sie auf **Einladen**.

    Der Gast sollte als ausgewähltes Mitglied hinzugefügt werden.

1. Klicken Sie im Bereich **Mitglied oder Gruppe auswählen** auf **Auswählen**.

1. Wählen Sie im Bereich **Mitgliedschaftseinstellungen** den Zuweisungstyp und die Dauer aus.

    ![Mitgliedschaftseinstellungen](./media/pim-resource-roles-external-users/membership-settings.png)

1. Klicken Sie zum Abschließen der Zuweisung auf **Fertig** und dann auf **Hinzufügen**.

    Die Rollenzuweisung für den Gast wird in der Rollenliste angezeigt.

    ![Rollenzuweisung für Gast](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Aktivieren der Rolle als Gast

Als externer Benutzer müssen Sie zunächst die Einladung in Ihre Azure AD-Organisation annehmen und möglicherweise Ihre Rolle aktivieren.

1. Öffnen Sie die E-Mail mit Ihrer Einladung. Die E-Mail sieht etwa wie folgt aus.

    ![E-Mail-Einladung](./media/pim-resource-roles-external-users/email-invite.png)

1. Klicken Sie auf den Link **Get Started** (Start) in der E-Mail.

1. Überprüfen Sie die Berechtigungen, und klicken Sie dann auf **Annehmen**.

    ![Berechtigungen überprüfen](./media/pim-resource-roles-external-users/invite-accept.png)

1. Möglicherweise werden Sie aufgefordert, den Nutzungsbedingungen zuzustimmen und anzugeben, ob Sie angemeldet bleiben möchten.

    Das Azure-Portal wird geöffnet. Wenn Sie nur für eine Rolle berechtigt sind, haben Sie keinen Zugriff auf Ressourcen.

1. Um Ihre Rolle zu aktivieren, öffnen Sie die E-Mail mit dem Link „Rolle aktivieren“. Die E-Mail sieht etwa wie folgt aus.

    ![E-Mail-Einladung](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Klicken Sie auf **Rolle aktivieren**, um Ihre berechtigten Rollen in PIM zu öffnen.

    ![Meine Rollen – Berechtigt](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. Klicken Sie unter „Aktion“ auf den Link **Aktivieren**.

    Je nach den Rolleneinstellungen müssen Sie möglicherweise bestimmte Informationen angeben, um die Rolle zu aktivieren.

1. Nachdem Sie die Einstellungen für die Rolle angegeben haben, klicken Sie auf **Aktivieren**, um die Rolle zu aktivieren.

    ![Rolle aktivieren](./media/pim-resource-roles-external-users/activate-role.png)

    Sofern der Administrator Ihre Anforderung nicht genehmigen muss, sollten Sie nun Zugriff auf die angegebenen Ressourcen haben.

## <a name="view-activity-for-a-guest"></a>Anzeigen der Aktivität für einen Gast

Genau wie ein Mitgliedsbenutzer können Sie Überwachungsprotokolle anzeigen, um die Aktivitäten von Gästen nachzuverfolgen.

1. Öffnen Sie PIM als Administrator, und wählen Sie die freigegebene Ressource aus.

1. Klicken Sie auf **Ressourcenüberwachung**, um die Aktivität für diese Ressource anzuzeigen. Im folgenden Beispiel wird die Aktivität für eine Ressourcengruppe angezeigt.

    ![Ressourcenüberwachung](./media/pim-resource-roles-external-users/audit-resource.png)

1. Klicken Sie zum Anzeigen der Aktivität für den Gast auf **Azure Active Directory** > **Benutzer** > Gastname.

1. Klicken Sie auf **Überwachungsprotokolle**, um die Überwachungsprotokolle für die Organisation anzuzeigen. Bei Bedarf können Sie Filter angeben.

    ![Organisationsüberwachung](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen von Azure AD-Administratorrollen in PIM](pim-how-to-add-role-to-user.md)
- [Was ist der Gastzugriff in Azure Active Directory-B2B?](../b2b/what-is-b2b.md)
