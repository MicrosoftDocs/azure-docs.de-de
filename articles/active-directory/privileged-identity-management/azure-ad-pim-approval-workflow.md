---
title: Genehmigen oder Ablehnen von Anforderungen für Azure AD-Rollen in PIM – Azure AD | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Anforderungen für Azure AD-Rollen in Azure AD Privileged Identity Management (PIM) genehmigen oder ablehnen.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/07/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d74a243ab10d0fffca960dc5149999560a7b54cd
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94980585"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Genehmigen oder Ablehnen von Anforderungen für Azure AD-Rollen in PIM

Mit Azure Active Directory (Azure AD) Privileged Identity Management (PIM) können Sie Rollen konfigurieren, um die Genehmigung für die Aktivierung anzufordern, und einen oder mehrere Benutzer oder Gruppen als delegierte genehmigende Personen auswählen. Delegierte genehmigende Personen haben zum Genehmigen von Anforderungen 24 Stunden Zeit. Wenn eine Anforderung nicht innerhalb von 24 Stunden genehmigt wird, muss der berechtigte Benutzer eine neue Anforderung senden. Das 24-Stunden-Zeitfenster für die Genehmigung ist nicht konfigurierbar.

## <a name="determine-your-version-of-pim"></a>Ermitteln der PIM-Version

Ab November 2019 wird der Bereich „Azure AD-Rollen“ von Privileged Identity Management auf eine neue Version aktualisiert, die der Oberfläche von Azure-Rollen entspricht. Dadurch werden zusätzliche Features verfügbar, und es kommt zu [Änderungen an der vorhandenen API](azure-ad-roles-features.md#api-changes). Welche Verfahren Sie in diesem Artikel während der Einführung der neuen Version befolgen, hängt von Ihrer aktuellen Version von Privileged Identity Management ab. Führen Sie die Schritte in diesem Abschnitt aus, um Ihre Version von Privileged Identity Management zu ermitteln. Nachdem Sie Ihre Version von Privileged Identity Management kennen, können Sie die Verfahren in diesem Artikel auswählen, die dieser Version entsprechen.

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com/) als Benutzer mit der Rolle [Administrator für privilegierte Rollen](../roles/permissions-reference.md#privileged-role-administrator) an.
1. Öffnen Sie **Azure AD Privileged Identity Management**. Wenn sich oben auf der Übersichtsseite ein Banner befindet, befolgen Sie die Anweisungen auf der Registerkarte **Neue Version** dieses Artikels. Andernfalls befolgen Sie die Anweisungen auf der Registerkarte **Vorherige Version**.

    [![Wählen Sie „Azure AD“ > „Privileged Identity Management“ aus.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Führen Sie die in diesem Artikel beschriebenen Schritte aus, um Anforderungen für Azure AD-Rollen zu genehmigen oder abzulehnen.

## <a name="new-version"></a>[Neue Version](#tab/new)

### <a name="view-pending-requests"></a>Anzeigen ausstehender Anforderungen

Als delegierte genehmigende Person erhalten Sie eine E-Mail-Benachrichtigung, wenn Ihre Genehmigung einer Anforderung für eine Azure AD-Rolle aussteht. Sie können diese ausstehenden Anforderungen in Privileged Identity Management anzeigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Wählen Sie **Anforderungen genehmigen** aus.

    ![Anforderungen genehmigen: Seite mit einer Anforderung zum Überprüfen von Azure AD-Rollen](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    Im Abschnitt **Anforderungen für Rollenaktivierungen** wird eine Liste der Anforderungen angezeigt, die Sie noch genehmigen müssen.

### <a name="approve-requests"></a>Genehmigen von Anforderungen

1. Suchen Sie nach der zu genehmigenden Anforderung, und wählen Sie diese aus. Daraufhin wird eine Seite zum Genehmigen oder Ablehnen von Anforderungen angezeigt.

    ![Screenshot: Seite „Anforderungen genehmigen – Azure AD-Rollen“](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. Geben Sie im Feld **Begründung** die geschäftliche Begründung ein.

1. Wählen Sie **Genehmigen** aus. Sie erhalten eine Azure-Benachrichtigung über Ihre Genehmigung.

    ![Genehmigungsbenachrichtigung, dass die Anforderung genehmigt wurde](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png))

### <a name="deny-requests"></a>Ablehnen von Anforderungen

1. Suchen Sie nach der Anforderung, die Sie ablehnen möchten, und wählen Sie diese aus. Daraufhin wird eine Seite zum Genehmigen oder Ablehnen von Anforderungen angezeigt.

    ![Anforderungen genehmigen: Bereich zum Genehmigen oder Ablehnen von Anforderungen mit Details und dem Feld „Begründung“](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Geben Sie im Feld **Begründung** die geschäftliche Begründung ein.

1. Wählen Sie **Ablehnen** aus. Es wird eine Benachrichtigung mit Ihrer Ablehnung angezeigt.

### <a name="workflow-notifications"></a>Workflowbenachrichtigungen

Nachfolgend finden Sie einige Informationen zu Workflowbenachrichtigungen:

- Die genehmigenden Personen werden per E-Mail benachrichtigt, wenn für die Anforderung einer Rolle die Überprüfung aussteht. E-Mail-Benachrichtigungen enthalten einen direkten Link zur Anforderung, damit diese von der genehmigenden Person genehmigt oder abgelehnt werden kann.
- Anforderungen werden von der ersten genehmigenden Person bearbeitet, die sich mit der Genehmigung oder Ablehnung befasst.
- Wenn eine genehmigende Person auf die Anforderung reagiert, werden alle genehmigenden Personen über die Aktion informiert.
- Globale Administratoren und Administratoren für privilegierte Rollen werden benachrichtigt, wenn ein genehmigter Benutzer in seiner Rolle aktiv wird.

>[!NOTE]
>Wenn ein globaler Administrator oder ein Administrator für privilegierte Rollen der Meinung ist, dass ein genehmigter Benutzer nicht aktiv sein sollte, kann er die aktive Rollenzuweisung in Privileged Identity Management entfernen. Administratoren werden nur über ausstehende Anforderungen benachrichtigt, wenn sie genehmigende Personen sind. Sie können aber ausstehende Anforderungen aller Benutzer anzeigen und abbrechen, indem sie die ausstehenden Anforderungen in Privileged Identity Management anzeigen.

## <a name="previous-version"></a>[Vorherige Version](#tab/previous)

### <a name="view-pending-requests"></a>Anzeigen ausstehender Anforderungen

Als delegierte genehmigende Person erhalten Sie eine E-Mail-Benachrichtigung, wenn Ihre Genehmigung einer Anforderung für eine Azure AD-Rolle aussteht. Sie können diese ausstehenden Anforderungen in Privileged Identity Management anzeigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Klicken Sie auf **Azure AD-Rollen**.

1. Klicken Sie auf **Anforderungen genehmigen**.

    ![Azure AD-Rollen – Anforderungen genehmigen](./media/azure-ad-pim-approval-workflow/approve-requests.png)

    Es wird eine Liste der Anforderungen angezeigt, die Sie noch genehmigen müssen.

### <a name="approve-requests"></a>Genehmigen von Anforderungen

1. Wählen Sie die Anforderungen aus, die Sie genehmigen möchten, und klicken Sie dann auf **Genehmigen**, um den Bereich „Ausgewählte Anforderungen genehmigen“ zu öffnen.

    ![Liste „Anforderungen genehmigen“ mit hervorgehobener Option „Genehmigen“](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. Geben Sie im Feld **Genehmigungsgrund** einen Grund ein.

    ![Bereich zum Genehmigen ausgewählter Anforderungen mit einem Genehmigungsgrund](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Klicken Sie auf **Approve**.

    Das Statussymbol wird mit Ihrer Genehmigung aktualisiert.

    ![Bereich zum Genehmigen ausgewählter Anforderungen nach Klicken auf die Schaltfläche „Genehmigen“](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

### <a name="deny-requests"></a>Ablehnen von Anforderungen

1. Wählen Sie die Anforderungen aus, die Sie ablehnen möchten, und klicken Sie dann auf **Ablehnen**, um den Bereich „Ausgewählte Anforderungen ablehnen“ zu öffnen.

    ![Liste „Anforderungen genehmigen“ mit hervorgehobener Option „Ablehnen“](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Geben Sie im Feld **Ablehnungsgrund** einen Grund ein.

    ![Bereich zum Ablehnen ausgewählter Anforderungen mit einem Ablehnungsgrund](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Wählen Sie **Ablehnen** aus.

    Das Statussymbol wird mit Ihrer Ablehnung aktualisiert.

---

## <a name="next-steps"></a>Nächste Schritte

- [E-Mail-Benachrichtigungen in Privileged Identity Management](pim-email-notifications.md)
- [Genehmigen oder Ablehnen von Anforderungen für Azure-Ressourcenrollen in PIM](pim-resource-roles-approval-workflow.md)
