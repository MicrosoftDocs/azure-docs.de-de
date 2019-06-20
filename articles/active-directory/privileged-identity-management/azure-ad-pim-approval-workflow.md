---
title: Genehmigen oder Ablehnen von Anforderungen für Azure AD-Rollen in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Anforderungen für Azure AD-Rollen in Azure AD Privileged Identity Management (PIM) genehmigen oder ablehnen.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
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
ms.openlocfilehash: 47e9033b33c70a72a1685696f75a442e88eed033
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60289723"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-pim"></a>Genehmigen oder Ablehnen von Anforderungen für Azure AD-Rollen in PIM

Mit Azure Active Directory (Azure AD) Privileged Identity Management (PIM) können Sie Rollen konfigurieren, um die Genehmigung für die Aktivierung anzufordern, und einen oder mehrere Benutzer oder Gruppen als delegierte genehmigende Personen auswählen. Delegierte genehmigende Personen haben zum Genehmigen von Anforderungen 24 Stunden Zeit. Wenn eine Anforderung nicht innerhalb von 24 Stunden genehmigt wird, muss der berechtigte Benutzer eine neue Anforderung senden. Das 24-Stunden-Zeitfenster für die Genehmigung ist nicht konfigurierbar.

Führen Sie die in diesem Artikel beschriebenen Schritte aus, um Anforderungen für Azure AD-Rollen zu genehmigen oder abzulehnen.

## <a name="view-pending-requests"></a>Anzeigen ausstehender Anforderungen

Als delegierte genehmigende Person erhalten Sie eine E-Mail-Benachrichtigung, wenn Ihre Genehmigung einer Anforderung für eine Azure AD-Rolle aussteht. Sie können diese ausstehenden Anforderungen in PIM anzeigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Klicken Sie auf **Azure AD-Rollen**.

1. Klicken Sie auf **Anforderungen genehmigen**.

    ![PIM: Azure AD-Rollen – Rollen](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    Es wird eine Liste der Anforderungen angezeigt, die Sie noch genehmigen müssen.

## <a name="approve-requests"></a>Genehmigen von Anforderungen

1. Wählen Sie die Anforderungen aus, die Sie genehmigen möchten, und klicken Sie dann auf **Genehmigen**, um den Bereich „Ausgewählte Anforderungen genehmigen“ zu öffnen.

    ![Liste im Bereich „Anforderungen genehmigen“ in PIM](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. Geben Sie im Feld **Genehmigungsgrund** einen Grund ein.

    ![„Ausgewählte Anforderungen genehmigen“ in PIM](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Klicken Sie auf **Approve**.

    Das Statussymbol wird mit Ihrer Genehmigung aktualisiert.

    ![„Ausgewählte Anforderungen genehmigen“ in PIM](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Ablehnen von Anforderungen

1. Wählen Sie die Anforderungen aus, die Sie ablehnen möchten, und klicken Sie dann auf **Ablehnen**, um den Bereich „Ausgewählte Anforderungen ablehnen“ zu öffnen.

    ![Liste im Bereich „Anforderungen genehmigen“ in PIM](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Geben Sie im Feld **Ablehnungsgrund** einen Grund ein.

    ![„Ausgewählte Anforderungen ablehnen“ in PIM](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Klicken Sie auf **Ablehnen**.

    Das Statussymbol wird mit Ihrer Ablehnung aktualisiert.

## <a name="next-steps"></a>Nächste Schritte

- [E-Mail-Benachrichtigungen in PIM](pim-email-notifications.md)
- [Genehmigen oder Ablehnen von Anforderungen für Azure-Ressourcenrollen in PIM](pim-resource-roles-approval-workflow.md)
