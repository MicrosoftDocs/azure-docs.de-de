---
title: Aktivieren von Azure-Ressourcenrollen in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihre Azure-Ressourcenrollen in Azure AD Privileged Identity Management (PIM) aktivieren.
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
ms.openlocfilehash: 04e8615cc5534255308c35fa1f675ef3a85aa84e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60438452"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Aktivieren von Azure-Ressourcenrollen in PIM

Mit Azure Active Directory (Azure AD) Privileged Identity Management (PIM) können für Azure-Ressourcen berechtigte Rollenmitglieder die Aktivierung für ein künftiges Datum und eine künftige Uhrzeit planen. Zudem können sie eine bestimmte Aktivierungsdauer innerhalb des (von Administratoren konfigurierten) Bereichs auswählen.

Dieser Artikel richtet sich an Mitglieder, die ihre Azure-Ressourcenrolle in PIM aktivieren müssen.

## <a name="activate-a-role"></a>Aktivieren einer Rolle

Wenn Sie eine Azure-Ressourcenrolle übernehmen müssen, können Sie in PIM über die Navigationsoption **Meine Rollen** deren Aktivierung anfordern.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Öffnen Sie **Azure AD Privileged Identity Management**. Informationen dazu, wie Sie die PIM-Kachel zu Ihrem Dashboard hinzufügen, finden Sie unter [Einstieg in die Verwendung von PIM](pim-getting-started.md).

1. Klicken Sie auf **Meine Rollen**.

    ![Azure AD-Rollen und Azure-Ressourcenrollen – Meine Rollen](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Klicken Sie auf **Azure-Ressourcenrollen**, um eine Liste der Azure-Ressourcenrollen anzuzeigen, für die Sie berechtigt sind.

   ![Azure-Ressourcenrollen](./media/pim-resource-roles-activate-your-roles/resources-my-roles-azure-resources.png) 

1. Wechseln Sie in der Liste **Azure-Ressourcenrollen** zur Rolle, die Sie aktivieren möchten.

    ![Azure-Ressourcenrollen: Liste „Meine Rollen“](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Klicken Sie auf **Aktivieren**, um den Bereich „Aktivieren“ zu öffnen.

1. Wenn Ihre Rolle eine mehrstufige Authentifizierung erfordert, klicken Sie auf **Überprüfen Sie Ihre Identität, bevor Sie den Vorgang fortsetzen**. Sie müssen sich nur einmal pro Sitzung authentifizieren.

    ![Überprüfung mit MFA vor der Rollenaktivierung](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Klicken Sie auf **Meine Identität überprüfen**, und folgen Sie den Anweisungen zur Bereitstellung einer zusätzlichen Sicherheitsüberprüfung.

    ![Zusätzliche Sicherheitsüberprüfung](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Wenn Sie einen reduzierten Bereich angeben möchten, klicken Sie auf **Bereich**, um „Ressourcenfilter“ zu öffnen.

    Es wird empfohlen, nur Zugriff auf die Ressourcen anzufordern, die Sie benötigen. Im Bereich „Ressourcenfilter“ können Sie die Ressourcengruppen bzw. angeben, auf die Sie Zugriff haben möchten.

    ![Aktivieren: Ressourcenfilter](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Falls erforderlich, geben Sie einen Startzeitpunkt für die Aktivierung an. Das Mitglied wird nach dem ausgewählten Zeitpunkt aktiviert.

1. Geben Sie im Feld **Grund** den Grund für die Aktivierungsanforderung ein.

    ![Ausgefüllter Bereich „Aktivieren“](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Klicken Sie auf **Aktivieren**.

    Wenn für die Rolle keine Genehmigung erforderlich ist, wird sie aktiviert und der Liste der aktiven Rollen hinzugefügt. Wenn Sie die Rolle verwenden möchten, führen Sie die im nächsten Abschnitt beschriebenen Schritte aus.

    Wenn für die Aktivierung der [Rolle eine Genehmigung erforderlich ist](pim-resource-roles-approval-workflow.md), werden Sie über eine Benachrichtigung in der oberen rechten Ecke des Browsers darüber informiert, dass die Genehmigung der Anforderung aussteht.

    ![Benachrichtigung über ausstehende Anforderung](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Verwenden einer Rolle sofort nach der Aktivierung

Im Falle einer Verzögerung nach der Aktivierung führen Sie diese Schritte nach der Aktivierung aus, um Ihre Azure-Ressourcenrollen sofort zu nutzen.

1. Öffnen Sie Azure AD Privileged Identity Management.

1. Klicken Sie auf **Meine Rollen**, um eine Liste der Azure AD-Rollen und Azure-Ressourcenrollen anzuzeigen, für die Sie berechtigt sind.

1. Klicken Sie auf **Azure-Ressourcenrollen**.

1. Klicken Sie auf die Registerkarte **Aktive Rollen**.

1. Sobald die Rolle aktiv ist, melden Sie sich vom Portal ab und dann erneut an.

    Die Rolle sollte jetzt zur Verwendung zur Verfügung stehen.

## <a name="view-the-status-of-your-requests"></a>Anzeigen des Status Ihrer Anforderungen

Sie können den Status Ihrer ausstehenden Aktivierungsanforderungen anzeigen.

1. Öffnen Sie Azure AD Privileged Identity Management.

1. Klicken Sie auf **Meine Anforderungen**, um eine Liste mit Ihren Anforderungen von Azure AD-Rollen und Azure-Ressourcenrollen anzuzeigen.

    ![Azure AD-Rollen und Azure-Ressourcenrollen – Meine Anforderungen](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Scrollen Sie nach rechts, um die Spalte **Anforderungsstatus** anzuzeigen.

## <a name="cancel-a-pending-request"></a>Abbrechen einer ausstehenden Anforderung

Sollten Sie die Aktivierung einer Rolle, für die eine Genehmigung erforderlich ist, nicht mehr benötigen, können Sie eine ausstehende Anforderung jederzeit abbrechen.

1. Öffnen Sie Azure AD Privileged Identity Management.

1. Klicken Sie auf **Meine Anforderungen**.

1. Klicken Sie für die Rolle, für die Sie eine ausstehende Anforderung abbrechen möchten, auf den Link **Abbrechen**.

    Durch das Klicken auf „Abbrechen“ wird die Anforderung abgebrochen. Um die Rolle erneut zu aktivieren, müssen Sie eine neue Anforderung zur Aktivierung übermitteln.

   ![Abbrechen einer ausstehenden Anforderung](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Problembehandlung

### <a name="permissions-not-granted-after-activating-a-role"></a>Keine Gewährung von Berechtigungen nach der Aktivierung einer Rolle

Nach der Aktivierung einer Rolle in PIM dauert es mindestens zehn Minuten, bevor Sie auf das gewünschte Verwaltungsportal zugreifen oder Funktionen für eine spezifische Verwaltungsworkload ausführen können. Sobald die Aktivierung abgeschlossen ist, melden Sie sich vom Azure-Portal ab und dann erneut an, um die neu aktivierte Rolle zu nutzen.

Weitere Schritte zur Problembehandlung finden Sie unter [Behandeln von Problemen mit erhöhten Berechtigungen](https://social.technet.microsoft.com/wiki/contents/articles/37568.troubleshooting-elevated-permissions-with-azure-ad-privileged-identity-management.aspx).

### <a name="cannot-activate-a-role-due-to-a-resource-lock"></a>Aktivieren einer Rolle aufgrund gesperrter Ressource nicht möglich

Wenn Sie eine Meldung erhalten, dass eine Azure-Ressource gesperrt ist, wenn Sie versuchen, eine Rolle zu aktivieren, kann dies daran liegen, dass eine Ressource im Rahmen einer Rollenzuordnung gesperrt wurde. Sperren schützen Ressourcen vor versehentlichem Löschen oder unerwarteten Änderungen. Eine Sperre verhindert auch, dass PIM am Ende des Aktivierungszeitraums eine Rollenzuordnung für die Ressource aufhebt. Da PIM nicht richtig funktionieren kann, wenn eine Sperre angewendet wird, verhindert PIM für Benutzer das Aktivieren von Rollen in der Ressource. Es gibt zwei Möglichkeiten, dieses Problem zu beheben:

- Löschen Sie die Sperre, wie in [Sperren von Ressourcen, um unerwartete Änderungen zu verhindern](../../azure-resource-manager/resource-group-lock-resources.md) beschrieben.
- Wenn Sie die Sperre beibehalten möchten, richten Sie eine dauerhafte Rollenzuweisung ein, oder verwenden Sie ein Break-Glass-Konto.

## <a name="next-steps"></a>Nächste Schritte

- [Verlängern oder Erneuern von Azure-Ressourcenrollen in PIM](pim-resource-roles-renew-extend.md)
- [Aktivieren meiner Azure AD-Rollen in PIM](pim-how-to-activate-role.md)
