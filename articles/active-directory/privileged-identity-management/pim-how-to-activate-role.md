---
title: Aktivieren meiner Azure AD-Rollen in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure AD-Rollen in Azure AD Privileged Identity Management (PIM) aktivieren.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa820d6c140251fce6b09110e65b45005b53afcc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60289619"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Aktivieren meiner Azure AD-Rollen in PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) vereinfacht die Art und Weise, in der Unternehmen den privilegierten Zugriff auf Ressourcen in Azure AD und anderen Onlinediensten von Microsoft wie Office 365 oder Microsoft Intune verwalten.  

Wenn Sie für eine Administratorrolle berechtigt sind, können Sie diese Rolle aktivieren, um privilegierte Aufgaben durchzuführen. Wenn Sie z. B. gelegentlich Office 365-Funktionen verwalten, dürfen Ihre privilegierte Rollenadministratoren Ihrer Organisation Sie nicht als permanenten globalen Administrator festlegen, da sich diese Rolle auf andere Dienste auswirkt. Stattdessen gewähren sie Ihnen Berechtigungen für Azure AD-Rollen wie z. B. Exchange Online-Administrator. Sie können eine Aktivierung dieser Rolle anfordern, wenn Sie diese Berechtigungen benötigen, und verfügen damit für einen bestimmten Zeitraum über Administratorkontrolle.

Dieser Artikel richtet sich an Administratoren, die ihre Azure AD-Rolle in PIM aktivieren müssen.

## <a name="activate-a-role"></a>Aktivieren einer Rolle

Wenn Sie eine Azure AD-Rolle übernehmen müssen, können Sie in PIM über die Navigationsoption **Meine Rollen** deren Aktivierung anfordern.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Öffnen Sie **Azure AD Privileged Identity Management**. Informationen dazu, wie Sie die PIM-Kachel zu Ihrem Dashboard hinzufügen, finden Sie unter [Einstieg in die Verwendung von PIM](pim-getting-started.md).

1. Klicken Sie auf **Azure AD-Rollen**.

1. Klicken Sie auf **Meine Rollen**, um eine Liste der Azure AD-Rollen anzuzeigen, für die Sie berechtigt sind.

    ![Azure AD-Rollen – Meine Rollen](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Suchen Sie nach einer Rolle, die Sie aktivieren möchten.

    ![Azure AD-Rollen – Liste „Meine Rollen“](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Klicken Sie auf **Aktivieren**, um den Detailbereich für die Rollenaktivierung zu öffnen.

1. Wenn Ihre Rolle eine mehrstufige Authentifizierung erfordert, klicken Sie auf **Überprüfen Sie Ihre Identität, bevor Sie den Vorgang fortsetzen**. Sie müssen sich nur einmal pro Sitzung authentifizieren.

    ![Überprüfung mit MFA vor der Rollenaktivierung](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Klicken Sie auf **Meine Identität überprüfen**, und folgen Sie den Anweisungen zur Bereitstellung einer zusätzlichen Sicherheitsüberprüfung.

    ![Zusätzliche Sicherheitsüberprüfung](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Klicken Sie auf **Aktivieren**, um den Bereich für die Aktivierung zu öffnen.

    ![Bereich „Aktivierung“](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. Falls erforderlich, geben Sie einen Startzeitpunkt für die Aktivierung an.

1. Geben Sie die Dauer der Aktivierung an.

1. Geben Sie im Feld **Grund für Aktivierung** den Grund für die Aktivierungsanforderung ein. Für einige Rollen müssen Sie eine Ticketnummer zur Problembehebung angeben.

    ![Ausgefüllter Bereich „Aktivierung“](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Klicken Sie auf **Aktivieren**.

    Wenn die Rolle nicht genehmigungspflichtig ist, wird ein Bereich **Aktivierungsstatus** angezeigt, der den Status der Aktivierung angibt.

    ![Aktivierungsstatus](./media/pim-how-to-activate-role/activation-status.png)

    Nachdem alle Phasen abgeschlossen wurden, klicken Sie auf den Link **Abmelden**, um sich vom Azure-Portal abzumelden. Wenn Sie sich erneut am Portal anmelden, können Sie die Rolle nun verwenden.

    Wenn für die Aktivierung der [Rolle eine Genehmigung erforderlich ist](./azure-ad-pim-approval-workflow.md), werden Sie über eine Benachrichtigung in der oberen rechten Ecke des Browsers darüber informiert, dass die Genehmigung der Anforderung aussteht.

    ![Benachrichtigung über ausstehende Anforderung](./media/pim-how-to-activate-role/directory-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>Anzeigen des Status Ihrer Anforderungen

Sie können den Status Ihrer ausstehenden Aktivierungsanforderungen anzeigen.

1. Öffnen Sie Azure AD Privileged Identity Management.

1. Klicken Sie auf **Azure AD-Rollen**.

1. Klicken Sie auf **Meine Anforderungen**, um eine Liste Ihrer Anforderungen anzuzeigen.

    ![Azure AD-Rollen – Meine Anforderungen](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>Deaktivieren einer Rolle

Wenn eine Rolle aktiviert wurde, wird sie nach Erreichen des Zeitlimits (berechtigte Dauer) automatisch deaktiviert.

Wenn Sie Ihre Administratoraufgaben früher als geplant beendet haben, können Sie eine Rolle auch manuell in Azure AD Privileged Identity Management deaktivieren.

1. Öffnen Sie Azure AD Privileged Identity Management.

1. Klicken Sie auf **Azure AD-Rollen**.

1. Klicken Sie auf **Meine Rollen**.

1. Klicken sie auf **Aktive Rollen**, um die Liste Ihrer aktiven Rollen anzuzeigen.

1. Suchen Sie nach der Rolle, die Sie nicht mehr benötigen, und klicken Sie auf **Deaktivieren**.

## <a name="cancel-a-pending-request"></a>Abbrechen einer ausstehenden Anforderung

Sollten Sie die Aktivierung einer Rolle, für die eine Genehmigung erforderlich ist, nicht mehr benötigen, können Sie eine ausstehende Anforderung jederzeit abbrechen.

1. Öffnen Sie Azure AD Privileged Identity Management.

1. Klicken Sie auf **Azure AD-Rollen**.

1. Klicken Sie auf **Meine Anforderungen**.

1. Klicken Sie für die Rolle, für die Sie eine ausstehende Anforderung abbrechen möchten, auf **Abbrechen**.

    Durch das Klicken auf „Abbrechen“ wird die Anforderung abgebrochen. Um die Rolle erneut zu aktivieren, müssen Sie eine neue Anforderung zur Aktivierung übermitteln.

   ![Abbrechen einer ausstehenden Anforderung](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>Problembehandlung

### <a name="permissions-not-granted-after-activating-a-role"></a>Keine Gewährung von Berechtigungen nach der Aktivierung einer Rolle

Nach der Aktivierung einer Rolle in PIM dauert es mindestens zehn Minuten, bevor Sie auf das gewünschte Verwaltungsportal zugreifen oder Funktionen für eine spezifische Verwaltungsworkload ausführen können. Sobald die Aktivierung abgeschlossen ist, melden Sie sich vom Azure-Portal ab und dann erneut an, um die neu aktivierte Rolle zu nutzen.

Weitere Schritte zur Problembehandlung finden Sie unter [Behandeln von Problemen mit erhöhten Berechtigungen](https://social.technet.microsoft.com/wiki/contents/articles/37568.troubleshooting-elevated-permissions-with-azure-ad-privileged-identity-management.aspx).

## <a name="next-steps"></a>Nächste Schritte

- [Aktivieren von Azure-Ressourcenrollen in PIM](pim-resource-roles-activate-your-roles.md)
