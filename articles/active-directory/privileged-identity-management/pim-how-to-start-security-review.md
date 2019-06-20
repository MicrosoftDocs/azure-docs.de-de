---
title: Erstellen einer Zugriffsüberprüfung für Azure AD-Rollen in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Zugriffsüberprüfung für Azure AD-Rollen in Azure AD Privileged Identity Management (PIM) erstellen.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/27/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a0680ddf2c9e654455933bf09699ab81e8ab65d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65141718"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-pim"></a>Erstellen einer Zugriffsüberprüfung für Azure AD-Rollen in PIM

Für Mitarbeiter kann sich der Zugriff auf privilegierte Azure AD-Rollen im Laufe der Zeit ändern. Daher sollten Sie den Zugriff in regelmäßigen Abständen überprüfen, um das mit veralteten Rollenzuweisungen verbundene Risiko zu verringern. Mit Azure Active Directory (Azure AD) Privileged Identity Management (PIM) können Sie Zugriffsüberprüfungen für privilegierte Azure AD-Rollen erstellen. Sie können auch wiederholte Zugriffsüberprüfungen konfigurieren, die automatisch ausgeführt werden.

In diesem Artikel wird beschrieben, wie Sie eine oder mehrere Zugriffsüberprüfungen für privilegierte Azure AD-Rollen erstellen.

## <a name="prerequisites"></a>Voraussetzungen

- [Administrator für privilegierte Rollen](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Öffnen von Zugriffsüberprüfungen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit einem Benutzer an, der ein Mitglied der Rolle „Administrator für privilegierte Rollen“ ist.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Klicken Sie im linken Menü auf **Azure AD-Rollen**, und klicken Sie dann auf **Zugriffsüberprüfungen**.

1. Klicken Sie unter „Verwalten“ auf **Zugriffsüberprüfungen**.

    ![Azure AD-Rollen – Zugriffsüberprüfungen](./media/pim-how-to-start-security-review/access-reviews.png)


[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]


## <a name="start-the-access-review"></a>Starten der Zugriffsüberprüfung

Klicken Sie nach dem Festlegen der Einstellungen für eine Zugriffsüberprüfung auf **Starten**. Die Zugriffsüberprüfung wird in der Liste mit einer Angabe des Status angezeigt.

![Liste mit den Zugriffsüberprüfungen](./media/pim-how-to-start-security-review/access-reviews-list.png)

Standardmäßig sendet Azure AD kurz nach dem Start der Überprüfung eine E-Mail an die Prüfer. Wenn Sie nicht möchten, dass Azure AD die E-Mail sendet, stellen Sie sicher, dass die Prüfer darüber in Kenntnis gesetzt werden, dass sie eine ausstehende Zugriffsüberprüfung abschließen müssen. Sie können ihnen die Anweisungen zum [Durchführen einer Zugriffsüberprüfung für Azure AD-Rollen in PIM](pim-how-to-perform-security-review.md) zeigen.

## <a name="manage-the-access-review"></a>Verwalten der Zugriffsüberprüfung

Sie können den Fortschritt der Überprüfungen durch die Prüfer auf der Seite **Übersicht** der Zugriffsüberprüfung nachverfolgen. Zugriffsrechte werden im Verzeichnis erst geändert, wenn die [Überprüfung abgeschlossen](pim-how-to-complete-review.md) ist.

![Fortschritt der Zugriffsüberprüfungen](./media/pim-how-to-start-security-review/access-review-overview.png)

Führen Sie bei einer einmaligen Überprüfung nach Ablauf des Zeitraums für die Zugriffsüberprüfung oder nach Beenden der Zugriffsüberprüfung durch den Administrator die Schritte unter [Abschließen einer Zugriffsüberprüfung für Azure AD-Rollen in PIM](pim-how-to-complete-review.md) aus, um die Ergebnisse anzuzeigen und anzuwenden.  

Um eine Serie von Zugriffsüberprüfungen zu verwalten, navigieren Sie zur Zugriffsüberprüfung. Dort finden Sie unter den geplanten Überprüfungen die anstehenden Überprüfungen, und Sie können das Enddatum bearbeiten oder Prüfer entsprechend hinzufügen/entfernen.

Basierend auf Ihrer Auswahl unter **Einstellungen nach Abschluss** wird nach dem Enddatum der Überprüfung oder bei manueller Beendigung der Überprüfung die automatische Anwendung ausgeführt. Der Status der Überprüfung ändert sich von **Abgeschlossen** über Zwischenzustände wie **Wird angewandt** schließlich in den Status **Angewandt**. Erwartungsgemäß sollten abgelehnte Benutzer (sofern vorhanden) innerhalb weniger Minuten aus den Rollen entfernt werden.

## <a name="next-steps"></a>Nächste Schritte

- [Überprüfen des Zugriffs auf Azure AD-Rollen](pim-how-to-perform-security-review.md)
- [Abschließen einer Zugriffsüberprüfung für Azure AD-Rollen](pim-how-to-complete-review.md)
- [Erstellen einer Zugriffsüberprüfung für Azure AD-Rollen](pim-resource-roles-start-access-review.md)
