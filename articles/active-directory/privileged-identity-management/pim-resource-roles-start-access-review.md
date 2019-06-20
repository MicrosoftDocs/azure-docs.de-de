---
title: Erstellen einer Zugriffsüberprüfung für Azure-Ressourcenrollen in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie eine Zugriffsüberprüfung für Azure-Ressourcenrollen in Azure AD Privileged Identity Management (PIM) erstellen.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/29/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78e4de5bbc56f95c0e903b1dac4e8481373716f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65143499"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-pim"></a>Erstellen einer Zugriffsüberprüfung für Azure-Ressourcenrollen in PIM

Für Mitarbeiter kann sich der Zugriff auf privilegierte Azure-Ressourcenrollen im Laufe der Zeit ändern. Daher sollten Sie den Zugriff in regelmäßigen Abständen überprüfen, um das mit veralteten Rollenzuweisungen verbundene Risiko zu verringern. Mit Azure Active Directory (Azure AD) Privileged Identity Management (PIM) können Sie eine Zugriffsüberprüfung für privilegierte Azure-Ressourcenrollen erstellen. Sie können auch wiederholte Zugriffsüberprüfungen konfigurieren, die automatisch ausgeführt werden.

In diesem Artikel wird beschrieben, wie Sie eine oder mehrere Zugriffsüberprüfungen für privilegierte Azure-Ressourcenrollen erstellen.

## <a name="prerequisites"></a>Voraussetzungen

- [Administrator für privilegierte Rollen](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Öffnen von Zugriffsüberprüfungen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit einem Benutzer an, der ein Mitglied der Rolle „Administrator für privilegierte Rollen“ ist.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Klicken Sie im linken Menü auf **Azure-Ressourcen**.

1. Klicken Sie auf die Ressource, die Sie verwalten möchten, z.B. ein Abonnement oder eine Verwaltungsgruppe.

1. Klicken Sie unter „Verwalten“ auf **Zugriffsüberprüfungen**.

    ![Azure-Ressourcen: Zugriffsüberprüfungen](./media/pim-resource-roles-start-access-review/access-reviews.png)


[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]


## <a name="start-the-access-review"></a>Starten der Zugriffsüberprüfung

Klicken Sie nach dem Festlegen der Einstellungen für eine Zugriffsüberprüfung auf **Starten**. Die Zugriffsüberprüfung wird in der Liste mit einer Angabe des Status angezeigt.

![Liste mit den Zugriffsüberprüfungen](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Standardmäßig sendet Azure AD kurz nach dem Start der Überprüfung eine E-Mail an die Prüfer. Wenn Sie nicht möchten, dass Azure AD die E-Mail sendet, stellen Sie sicher, dass die Prüfer darüber in Kenntnis gesetzt werden, dass sie eine ausstehende Zugriffsüberprüfung abschließen müssen. Sie können ihnen die Anweisungen zum [Durchführen einer Zugriffsüberprüfung für Azure-Ressourcenrollen in PIM](pim-resource-roles-perform-access-review.md) anzeigen.

## <a name="manage-the-access-review"></a>Verwalten der Zugriffsüberprüfung

Sie können den Fortschritt der Überprüfungen durch die Prüfer auf der Seite **Übersicht** der Zugriffsüberprüfung nachverfolgen. Zugriffsrechte werden im Verzeichnis erst geändert, wenn die [Überprüfung abgeschlossen](pim-resource-roles-complete-access-review.md) ist.

![Fortschritt der Zugriffsüberprüfungen](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Führen Sie bei einer einmaligen Überprüfung nach Ablauf des Zeitraums für die Zugriffsüberprüfung oder nach Beenden der Zugriffsüberprüfung durch den Administrator die Schritte unter [Abschließen einer Zugriffsüberprüfung für Azure-Ressourcenrollen in PIM](pim-resource-roles-complete-access-review.md) aus, um die Ergebnisse anzuzeigen und anzuwenden.  

Um eine Serie von Zugriffsüberprüfungen zu verwalten, navigieren Sie zur Zugriffsüberprüfung. Dort finden Sie unter den geplanten Überprüfungen die anstehenden Überprüfungen, und Sie können das Enddatum bearbeiten oder Prüfer entsprechend hinzufügen/entfernen.

Basierend auf Ihrer Auswahl unter **Einstellungen nach Abschluss** wird nach dem Enddatum der Überprüfung oder bei manueller Beendigung der Überprüfung die automatische Anwendung ausgeführt. Der Status der Überprüfung ändert sich von **Abgeschlossen** über Zwischenzustände wie **Wird angewandt** schließlich in den Status **Angewandt**. Erwartungsgemäß sollten abgelehnte Benutzer (sofern vorhanden) innerhalb weniger Minuten aus den Rollen entfernt werden.

## <a name="next-steps"></a>Nächste Schritte

- [Durchführen einer Zugriffsüberprüfung für Azure-Ressourcenrollen in PIM](pim-resource-roles-perform-access-review.md)
- [Abschließen einer Zugriffsüberprüfung für Azure-Ressourcenrollen in PIM](pim-resource-roles-complete-access-review.md)
- [Erstellen einer Zugriffsüberprüfung für Azure AD-Rollen in PIM](pim-how-to-start-security-review.md)
