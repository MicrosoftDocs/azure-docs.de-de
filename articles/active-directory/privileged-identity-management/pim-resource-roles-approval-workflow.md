---
title: Genehmigen oder Ablehnen von Anforderungen für Azure-Ressourcenrollen in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Anforderungen für Azure-Ressourcenrollen in Azure AD Privileged Identity Management (PIM) genehmigen oder ablehnen.
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
ms.openlocfilehash: 1f645b7077ef43dc7eb4d70261b6b601b5e4af1b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60288482"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>Genehmigen oder Ablehnen von Anforderungen für Azure-Ressourcenrollen in PIM

Mit Azure Active Directory (Azure AD) Privileged Identity Management (PIM) können Sie Rollen konfigurieren, um die Genehmigung für die Aktivierung anzufordern, und einen oder mehrere Benutzer oder Gruppen als delegierte genehmigende Personen auswählen. Delegierte genehmigende Personen haben zum Genehmigen von Anforderungen 24 Stunden Zeit. Wenn eine Anforderung nicht innerhalb von 24 Stunden genehmigt wird, muss der berechtigte Benutzer eine neue Anforderung senden. Das 24-Stunden-Zeitfenster für die Genehmigung ist nicht konfigurierbar.

Führen Sie die in diesem Artikel beschriebenen Schritte aus, um Anforderungen für Azure-Ressourcenrollen zu genehmigen oder abzulehnen.

## <a name="view-pending-requests"></a>Anzeigen ausstehender Anforderungen

Als delegierte genehmigende Person erhalten Sie eine E-Mail-Benachrichtigung, wenn Ihre Genehmigung einer Anforderung für eine Azure-Ressourcenrolle aussteht. Sie können diese ausstehenden Anforderungen in PIM anzeigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Klicken Sie auf **Anforderungen genehmigen**.

    ![Azure-Ressourcen – Anforderungen genehmigen](./media/pim-resource-roles-approval-workflow/resources-approve-requests.png)

    Im Abschnitt **Anforderungen für Rollenaktivierungen** wird eine Liste der Anforderungen angezeigt, die Sie noch genehmigen müssen.

## <a name="approve-requests"></a>Genehmigen von Anforderungen

1. Suchen Sie nach der Anforderung, die Sie genehmigen möchten, und klicken Sie darauf. Es wird ein Genehmigungsbereich angezeigt.

    ![Bereich „Anforderungen genehmigen“](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Geben Sie im Feld **Begründung** einen Grund ein.

1. Klicken Sie auf **Approve**.

    Es wird eine Benachrichtigung mit Ihrer Genehmigung angezeigt.

    ![Genehmigungsbenachrichtigung](./media/pim-resource-roles-approval-workflow/resources-approve-notification.png)

## <a name="deny-requests"></a>Ablehnen von Anforderungen

1. Suchen Sie nach der Anforderung, die Sie ablehnen möchten, und klicken Sie darauf. Es wird ein Genehmigungsbereich angezeigt.

    ![Bereich „Anforderungen genehmigen“](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. Geben Sie im Feld **Begründung** einen Grund ein.

1. Klicken Sie auf **Ablehnen**.

    Es wird eine Benachrichtigung mit Ihrer Ablehnung angezeigt.

## <a name="workflow-notifications"></a>Workflowbenachrichtigungen

Nachfolgend finden Sie einige Informationen zu Workflowbenachrichtigungen:

- Alle Mitglieder der Liste mit den genehmigenden Personen werden per E-Mail benachrichtigt, wenn für die Anforderung einer Rolle die Überprüfung aussteht. E-Mail-Benachrichtigungen enthalten einen direkten Link zur Anforderung, damit diese von der genehmigenden Person genehmigt oder abgelehnt werden kann.
- Anforderungen werden von dem Mitglied bearbeitet, das in der Liste der genehmigenden Personen an erster Stelle steht.
- Wenn eine genehmigende Person auf die Anforderung reagiert, werden alle Mitglieder der Liste über die Aktion informiert.
- Ressourcenadministratoren werden benachrichtigt, wenn ein genehmigtes Mitglied in seiner Rolle aktiv wird.

>[!Note]
>Wenn ein Ressourcenadministrator der Meinung ist, dass ein genehmigtes Mitglied nicht aktiv sein sollte, kann er die aktive Rollenzuweisung in PIM entfernen. Ressourcenadministratoren werden zwar nur über ausstehende Anforderungen benachrichtigt, wenn sie Mitglieder der Liste mit den genehmigenden Personen sind, aber sie können ausstehende Anforderungen aller Benutzer anzeigen und abbrechen, indem sie diese in PIM aufrufen. 

## <a name="next-steps"></a>Nächste Schritte

- [Verlängern oder Erneuern von Azure-Ressourcenrollen in PIM](pim-resource-roles-renew-extend.md)
- [E-Mail-Benachrichtigungen in PIM](pim-email-notifications.md)
- [Genehmigen oder Ablehnen von Anforderungen für Azure AD-Rollen in PIM](azure-ad-pim-approval-workflow.md)
