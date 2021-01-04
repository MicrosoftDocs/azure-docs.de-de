---
title: Aktivieren meiner Azure AD-Rollen in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure AD-Rollen in Azure AD Privileged Identity Management (PIM) aktivieren.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/18/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 079dcdc9d1667809779489c5d1126bc85cafb881
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511315"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Aktivieren meiner Azure AD-Rollen in PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) vereinfacht die Art und Weise, in der Unternehmen den privilegierten Zugriff auf Ressourcen in Azure AD und anderen Microsoft-Onlinediensten wie Microsoft 365 oder Microsoft Intune verwalten.  

Falls Sie zu einer Administratorrolle *berechtigt* sind, müssen Sie die Rollenzuweisung *aktivieren*, wenn Sie privilegierte Aufgaben ausführen möchten. Wenn Sie beispielsweise gelegentlich Microsoft 365-Funktionen verwalten, werden Sie von den Administratoren für privilegierte Rollen Ihrer Organisation möglicherweise nicht als permanenter globaler Administrator festgelegt, da sich diese Rolle auch auf andere Dienste auswirkt. Stattdessen erteilen sie Ihnen Berechtigungen für Azure AD-Rollen (beispielsweise Exchange Online-Administrator). Sie können eine Aktivierung dieser Rolle anfordern, wenn Sie diese Berechtigungen benötigen, und verfügen damit für einen bestimmten Zeitraum über Administratorkontrolle.

Dieser Artikel richtet sich an Administratoren, die ihre Azure AD-Rolle in Privileged Identity Management aktivieren müssen.

## <a name="determine-your-version-of-pim"></a>Ermitteln der PIM-Version

Ab November 2019 werden die Azure AD Rollen von Privileged Identity Management auf eine neue Version aktualisiert, die dem Verhalten von Azure-Ressourcenrollen entspricht. Dadurch werden zusätzliche Features verfügbar, und es kommt zu [Änderungen an der vorhandenen API](azure-ad-roles-features.md#api-changes). Welche Verfahren Sie in diesem Artikel während der Einführung der neuen Version befolgen, hängt von Ihrer aktuellen Version von Privileged Identity Management ab. Führen Sie die Schritte in diesem Abschnitt aus, um Ihre Version von Privileged Identity Management zu ermitteln. Nachdem Sie Ihre Version von Privileged Identity Management kennen, können Sie die Verfahren in diesem Artikel auswählen, die dieser Version entsprechen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit der Rolle [Administrator für privilegierte Rollen](../roles/permissions-reference.md#privileged-role-administrator) an.
1. Öffnen Sie **Azure AD Privileged Identity Management**. Wenn sich oben auf der Übersichtsseite ein Banner befindet, befolgen Sie die Anweisungen auf der Registerkarte **Neue Version** dieses Artikels. Andernfalls befolgen Sie die Anweisungen auf der Registerkarte **Vorherige Version**.

    [![Auswählen von „Azure AD“ > „Privileged Identity Management“](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Neue Version](#tab/new)

## <a name="activate-a-role-for-new-version"></a>Aktivieren einer Rolle für die neue Version

Wenn Sie eine Azure AD-Rolle annehmen müssen, können Sie in Privileged Identity Management die Aktivierung anfordern, indem Sie die Option **Meine Rollen** öffnen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Öffnen Sie **Azure AD Privileged Identity Management**. Informationen zum Hinzufügen der Privileged Identity Management-Kachel zu Ihrem Dashboard finden Sie unter [Einstieg in die Verwendung von PIM](pim-getting-started.md).

1. Wählen Sie **Meine Rollen** aus, und wählen Sie dann **Azure AD-Rollen** aus, um eine Liste der Azure AD-Rollen anzuzeigen, für die Sie berechtigt sind.

    ![Seite „Meine Rollen“ mit den Rollen, die aktiviert werden können](./media/pim-how-to-activate-role/my-roles.png)

1. Suchen Sie in der Liste **Azure AD-Rollen** die zu aktivierende Rolle.

    ![Azure AD-Rollen: Liste meiner berechtigten Rollen](./media/pim-how-to-activate-role/activate-link.png)

1. Wählen Sie **Aktivieren** aus, um den Bereich „Aktivieren“ zu öffnen.

    ![Azure AD Rollen: Aktivierungsseite mit Dauer und Bereich](./media/pim-how-to-activate-role/activate-page.png)

1. Wählen Sie **Zusätzliche Überprüfung erforderlich.** aus, und gehen Sie gemäß den Anweisungen vor, um die zusätzliche Sicherheitsüberprüfung zu durchlaufen. Sie müssen sich nur einmal pro Sitzung authentifizieren.

    ![Anzeige, die eine Sicherheitsüberprüfung wie z. B. einen PIN-Code verlangt](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Wählen Sie nach der mehrstufigen Authentifizierung die Option **Aktivieren** aus, bevor Sie den Vorgang fortsetzen.

    ![Bestätigen meiner Identität mit MFA vor der Rollenaktivierung](./media/pim-how-to-activate-role/activate-role-mfa-banner.png)

1. Wenn Sie einen reduzierten Bereich angeben möchten, wählen Sie **Bereich** aus, um den Filterbereich zu öffnen. Im Filterbereich können Sie die Azure AD-Ressourcen angeben, auf die Sie Zugriff benötigen. Es empfiehlt sich, Zugriff auf möglichst wenige benötigte Ressourcen anzufordern.

1. Falls erforderlich, geben Sie einen Startzeitpunkt für die Aktivierung an. Die Azure AD-Rolle wird dann nach dem ausgewählten Zeitpunkt aktiviert.

1. Geben Sie im Feld **Grund** den Grund für die Aktivierungsanforderung ein.

1. Wählen Sie **Aktivieren** aus.

    Wenn für die Aktivierung der [Rolle eine Genehmigung erforderlich ist](pim-resource-roles-approval-workflow.md), werden Sie über eine Benachrichtigung in der oberen rechten Ecke des Browsers darüber informiert, dass die Genehmigung der Anforderung aussteht.

    ![Ausstehende Genehmigung für die Aktivierungsanforderung](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests-for-new-version"></a>Anzeigen des Status Ihrer Anforderungen für die neue Version

Sie können den Status Ihrer ausstehenden Aktivierungsanforderungen anzeigen.

1. Öffnen Sie Azure AD Privileged Identity Management.

1. Wählen Sie **Meine Anforderungen** aus, um eine Liste mit Ihren Anforderungen von Azure AD-Rollen und Azure-Ressourcenrollen anzuzeigen.

    ![Meine Anforderungen: Azure AD-Seite mit Ihren ausstehenden Anforderungen](./media/pim-how-to-activate-role/my-requests-page.png)

1. Scrollen Sie nach rechts, um die Spalte **Anforderungsstatus** anzuzeigen.

## <a name="cancel-a-pending-request-for-new-version"></a>Abbrechen einer ausstehenden Anforderung für die neue Version

Sollten Sie die Aktivierung einer Rolle, für die eine Genehmigung erforderlich ist, nicht mehr benötigen, können Sie eine ausstehende Anforderung jederzeit abbrechen.

1. Öffnen Sie Azure AD Privileged Identity Management.

1. Wählen Sie **Meine Anforderungen** aus.

1. Wählen Sie für die Rolle, für die Sie eine ausstehende Anforderung abbrechen möchten, den Link **Abbrechen** aus.

    Durch das Auswählen von „Abbrechen“ wird die Anforderung abgebrochen. Um die Rolle erneut zu aktivieren, müssen Sie eine neue Anforderung zur Aktivierung übermitteln.

   ![Liste „Meine Anforderungen“ mit hervorgehobener Aktion „Abbrechen“](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot-for-new-version"></a>Problembehandlung bei der neuen Version

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Keine Gewährung von Berechtigungen nach der Aktivierung einer Rolle

Wenn Sie in Privileged Identity Management eine Rolle aktivieren, wird die Aktivierung möglicherweise nicht sofort an alle Portale weitergegeben, für die diese privilegierte Rolle benötigt wird. In einigen Fällen kann die Web-Zwischenspeicherung im Portal auch dann dazu führen, dass die Änderung nicht sofort wirksam ist, wenn diese weitergegeben wurde. Falls die Aktivierung verzögert ist, sollten Sie Folgendes tun.

1. Melden Sie sich vom Azure-Portal ab und dann wieder an.

1. Überprüfen Sie in Privileged Identity Management, ob Sie als Mitglied der Rolle aufgeführt sind.

# <a name="previous-version"></a>[Vorherige Version](#tab/previous)

## <a name="activate-a-role-previous-version"></a>Aktivieren einer Rolle (vorherige Version)

Wenn Sie eine Azure AD-Rolle übernehmen müssen, können Sie in Privileged Identity Management über die Navigationsoption **Meine Rollen** die Aktivierung anfordern.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Öffnen Sie **Azure AD Privileged Identity Management**. Informationen zum Hinzufügen der Privileged Identity Management-Kachel zu Ihrem Dashboard finden Sie unter [Einstieg in die Verwendung von PIM](pim-getting-started.md).

1. Wählen Sie **Azure AD-Rollen** aus.

1. Wählen Sie **Meine Rollen** aus, um eine Liste der Azure AD-Rollen anzuzeigen, für die Sie berechtigt sind.

    ![„Azure AD-Rollen – Meine Rollen“ zeigt eine Liste mit berechtigten oder aktiven Rollen](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Suchen Sie nach einer Rolle, die Sie aktivieren möchten.

    ![„Azure AD-Rollen – Meine berechtigten Rollen“ zeigt eine Liste mit „Link aktivieren“](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Wählen Sie **Aktivieren** aus, um den Detailbereich für die Rollenaktivierung zu öffnen.

1. Falls Ihre Rolle eine mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) erfordert, wählen Sie **Überprüfen Sie Ihre Identität, bevor Sie den Vorgang fortsetzen.** aus. Sie müssen sich nur einmal pro Sitzung authentifizieren.

    ![Überprüfung meiner Identität mit MFA vor der Rollenaktivierung](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Wählen Sie **Meine Identität überprüfen** aus, und folgen Sie den Anweisungen zur Bereitstellung einer zusätzlichen Sicherheitsüberprüfung.

    ![Zusätzliche Sicherheitsüberprüfung mit der Frage nach Kontaktmöglichkeiten](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Wählen Sie **Aktivieren** aus, um den Aktivierungsbereich zu öffnen.

    ![Aktivierungsfeld zum Angeben einer Startzeit, Dauer, eines Tickets und eines Grunds](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. Falls erforderlich, geben Sie einen Startzeitpunkt für die Aktivierung an.

1. Geben Sie die Dauer der Aktivierung an.

1. Geben Sie im Feld **Grund für Aktivierung** den Grund für die Aktivierungsanforderung ein. Für einige Rollen müssen Sie eine Ticketnummer zur Problembehebung angeben.

    ![Ausgefülltes Aktivierungsfeld mit benutzerdefinierter Startzeit, Dauer, Ticket und Grund](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Wählen Sie **Aktivieren** aus.

    Wenn die Rolle nicht genehmigungspflichtig ist, wird ein Bereich **Aktivierungsstatus** angezeigt, der den Status der Aktivierung angibt.

    ![Die Statusaktivierungsseite zeigt die drei Phasen der Aktivierung](./media/pim-how-to-activate-role/activation-status.png)

    Wählen Sie nach Abschluss aller Phasen den Link **Abmelden** aus, um sich vom Azure-Portal abzumelden. Wenn Sie sich erneut am Portal anmelden, können Sie die Rolle nun verwenden.

    Wenn für die Aktivierung der [Rolle eine Genehmigung erforderlich ist](./azure-ad-pim-approval-workflow.md), wird in der oberen rechten Ecke des Browsers eine Azure-Benachrichtigung angezeigt, in der Sie darüber informiert werden, dass die Genehmigung der Anforderung aussteht.

## <a name="view-the-status-of-your-requests-previous-version"></a>Anzeigen des Status Ihrer Anforderungen (vorherige Version)

Sie können den Status Ihrer ausstehenden Aktivierungsanforderungen anzeigen.

1. Öffnen Sie Azure AD Privileged Identity Management.

1. Wählen Sie **Azure AD-Rollen** aus.

1. Wählen Sie **Meine Anforderungen** aus, um eine Liste Ihrer Anforderungen anzuzeigen.

    ![Azure AD-Rollen – Meine Anforderungsliste](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role-previous-version"></a>Deaktivieren einer Rolle (vorherige Version)

Wenn eine Rolle aktiviert wurde, wird sie nach Erreichen des Zeitlimits (berechtigte Dauer) automatisch deaktiviert.

Wenn Sie Ihre Administratoraufgaben früher als geplant beendet haben, können Sie eine Rolle auch manuell in Azure AD Privileged Identity Management deaktivieren.

1. Öffnen Sie Azure AD Privileged Identity Management.

1. Wählen Sie **Azure AD-Rollen** aus.

1. Wählen Sie **Meine Rollen** aus.

1. Wählen Sie **Aktive Rollen** aus, um die Liste Ihrer aktiven Rollen anzuzeigen.

1. Suchen Sie nach der Rolle, die Sie nicht mehr benötigen, und wählen Sie **Deaktivieren** aus.

## <a name="cancel-a-pending-request-previous-version"></a>Abbrechen einer ausstehenden Anforderung (vorherige Version)

Sollten Sie die Aktivierung einer Rolle, für die eine Genehmigung erforderlich ist, nicht mehr benötigen, können Sie eine ausstehende Anforderung jederzeit abbrechen.

1. Öffnen Sie Azure AD Privileged Identity Management.

1. Wählen Sie **Azure AD-Rollen** aus.

1. Wählen Sie **Meine Anforderungen** aus.

1. Wählen Sie für die Rolle, für die Sie eine ausstehende Anforderung abbrechen möchten, die Schaltfläche **Abbrechen** aus.

    Durch Auswählen von **Abbrechen** wird die Anforderung abgebrochen. Um die Rolle erneut zu aktivieren, müssen Sie eine neue Anforderung zur Aktivierung übermitteln.

   ![Meine Anforderungsliste mit hervorgehobener Schaltfläche „Abbrechen“](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot-previous-version"></a>Problembehandlung (vorherige Version)

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Keine Gewährung von Berechtigungen nach der Aktivierung einer Rolle

Wenn Sie in Privileged Identity Management eine Rolle aktivieren, wird die Aktivierung möglicherweise nicht sofort an alle Portale weitergegeben, für die diese privilegierte Rolle benötigt wird. In einigen Fällen kann die Web-Zwischenspeicherung im Portal auch dann dazu führen, dass die Änderung nicht sofort wirksam ist, wenn diese weitergegeben wurde. Falls die Aktivierung verzögert ist, sollten Sie Folgendes tun.

1. Melden Sie sich vom Azure-Portal ab und dann wieder an.

    Wenn Sie eine Azure AD-Rolle aktivieren, werden die Phasen Ihrer Aktivierung angezeigt. Nach Abschluss aller Phasen wird der Link **Abmelden** angezeigt. Sie können diesen Link verwenden, um sich abzumelden. Hiermit werden die meisten Fälle von Verzögerungen bei der Aktivierung gelöst.

1. Überprüfen Sie in Privileged Identity Management, ob Sie als Mitglied der Rolle aufgeführt sind.

 ---

## <a name="next-steps"></a>Nächste Schritte

- [Aktivieren meiner Azure-Ressourcenrollen in Privileged Identity Management](pim-how-to-activate-role.md)
