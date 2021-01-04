---
title: Erstellen einer Zugriffsüberprüfung für Azure-Ressourcenrollen in PIM – Azure AD | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie eine Zugriffsüberprüfung für Azure-Ressourcenrollen in Azure AD Privileged Identity Management (PIM) erstellen.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 12/08/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a618da7c9a66b8f687c1b75914530080ed56bea
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905824"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Erstellen einer Zugriffsüberprüfung für Azure-Ressourcenrollen in Privileged Identity Management

Für Mitarbeiter kann sich der Zugriff auf privilegierte Azure-Ressourcenrollen im Laufe der Zeit ändern. Daher sollten Sie den Zugriff in regelmäßigen Abständen überprüfen, um das mit veralteten Rollenzuweisungen verbundene Risiko zu verringern. Mit Azure Active Directory (Azure AD) Privileged Identity Management (PIM) können Sie eine Zugriffsüberprüfung für privilegierte Azure-Ressourcenrollen erstellen. Sie können auch wiederholte Zugriffsüberprüfungen konfigurieren, die automatisch ausgeführt werden.

In diesem Artikel wird beschrieben, wie Sie eine oder mehrere Zugriffsüberprüfungen für privilegierte Azure-Ressourcenrollen erstellen.

## <a name="prerequisites"></a>Voraussetzungen

[Administrator für privilegierte Rollen](../roles/permissions-reference.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Öffnen von Zugriffsüberprüfungen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit einem Benutzer an, der ein Mitglied der Rolle „Administrator für privilegierte Rollen“ ist.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Wählen Sie im linken Menü die Option **Azure-Ressourcen** aus.

1. Wählen Sie die Ressource aus, die Sie verwalten möchten, z. B. ein Abonnement.

1. Wählen Sie unter „Verwalten“ die Option **Zugriffsüberprüfungen** aus.

    ![Azure-Ressourcen – Liste der Zugriffsüberprüfungen mit dem Status aller Überprüfungen](./media/pim-resource-roles-start-access-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>Starten der Zugriffsüberprüfung

Klicken Sie nach dem Festlegen der Einstellungen für eine Zugriffsüberprüfung auf **Starten**. Die Zugriffsüberprüfung wird in der Liste mit einer Angabe des Status angezeigt.

![Liste der Zugriffsüberprüfungen mit dem Status einer gestarteten Überprüfung](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Standardmäßig sendet Azure AD kurz nach dem Start der Überprüfung eine E-Mail an die Prüfer. Wenn Sie nicht möchten, dass Azure AD die E-Mail sendet, stellen Sie sicher, dass die Prüfer darüber in Kenntnis gesetzt werden, dass sie eine ausstehende Zugriffsüberprüfung abschließen müssen. Sie können ihnen die Anweisungen zum [Durchführen einer Zugriffsüberprüfung für Azure-Ressourcenrollen in PIM](pim-resource-roles-perform-access-review.md) anzeigen.

## <a name="manage-the-access-review"></a>Verwalten der Zugriffsüberprüfung

Sie können den Fortschritt der Überprüfungen durch die Prüfer auf der Seite **Übersicht** der Zugriffsüberprüfung nachverfolgen. Zugriffsrechte werden im Verzeichnis erst geändert, wenn die [Überprüfung abgeschlossen](pim-resource-roles-complete-access-review.md) ist.

![Übersichtsseite der Zugriffsüberprüfung mit den Details der Überprüfung](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Führen Sie bei einer einmaligen Überprüfung nach Ablauf des Zeitraums für die Zugriffsüberprüfung oder nach Beenden der Zugriffsüberprüfung durch den Administrator die Schritte unter [Abschließen einer Zugriffsüberprüfung für Azure-Ressourcenrollen in PIM](pim-resource-roles-complete-access-review.md) aus, um die Ergebnisse anzuzeigen und anzuwenden.  

Um eine Serie von Zugriffsüberprüfungen zu verwalten, navigieren Sie zur Zugriffsüberprüfung. Dort finden Sie unter den geplanten Überprüfungen die anstehenden Überprüfungen, und Sie können das Enddatum bearbeiten oder Prüfer entsprechend hinzufügen/entfernen.

Basierend auf Ihrer Auswahl unter **Einstellungen nach Abschluss** wird nach dem Enddatum der Überprüfung oder bei manueller Beendigung der Überprüfung die automatische Anwendung ausgeführt. Der Status der Überprüfung ändert sich von **Abgeschlossen** über Zwischenzustände wie **Wird angewandt** schließlich in den Status **Angewandt**. Erwartungsgemäß sollten abgelehnte Benutzer (sofern vorhanden) innerhalb weniger Minuten aus den Rollen entfernt werden.

## <a name="next-steps"></a>Nächste Schritte

- [Durchführen einer Zugriffsüberprüfung für Azure-Ressourcenrollen in PIM](pim-resource-roles-perform-access-review.md)
- [Abschließen einer Zugriffsüberprüfung für Azure-Ressourcenrollen in PIM](pim-resource-roles-complete-access-review.md)
- [Erstellen einer Zugriffsüberprüfung für Azure AD-Rollen in PIM](pim-how-to-start-security-review.md)
