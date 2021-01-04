---
title: Wiederherstellen oder dauerhaftes Entfernen gelöschter Benutzer – Azure AD
description: Vorgehensweise beim Anzeigen von wiederherstellbaren Benutzern, Wiederherstellen eines gelöschten Benutzers oder endgültigen Löschen eines Benutzers mithilfe von Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/23/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4d42e1cafa58aaf6a47188c9e86c24ac175047
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92514388"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Wiederherstellen oder Entfernen eines kürzlich gelöschten Benutzers mit Azure Active Directory
Nachdem Sie einen Benutzer gelöscht haben, verbleibt das Konto 30 Tage lang im angehaltenen Zustand. Während dieses Zeitfensters von 30 Tagen kann das Benutzerkonto mit allen zugehörigen Eigenschaften wiederhergestellt werden. Nachdem die 30 Tage verstrichen sind, wird der permanente Löschvorgang automatisch gestartet.

Sie können Ihre wiederherstellbaren Benutzer anzeigen, einen gelöschten Benutzer wiederherstellen oder einen Benutzer endgültig löschen, indem Sie im Azure-Portal Azure Active Directory (Azure AD) verwenden.

>[!Important]
>Weder Sie selbst noch der Microsoft-Kundendienst kann einen endgültig gelöschten Benutzer wiederherstellen.

## <a name="required-permissions"></a>Erforderliche Berechtigungen
Sie müssen über eine der folgenden Rollen verfügen, um Benutzer wiederherstellen und endgültig löschen zu können.

- Globaler Administrator

- Partnersupport der Ebene 1

- Partnersupport der Ebene 2

- Benutzeradministrator

## <a name="view-your-restorable-users"></a>Anzeigen Ihrer wiederherstellbaren Benutzer
Sie können alle Benutzer anzeigen, die vor weniger als 30 Tagen gelöscht wurden. Diese Benutzer können wiederhergestellt werden.

### <a name="to-view-your-restorable-users"></a>So zeigen Sie Ihre wiederherstellbaren Benutzer an
1. Melden Sie sich mit dem Konto eines globalen Administrators für die Organisation beim [Azure-Portal](https://portal.azure.com/) an.

2. Wählen Sie **Azure Active Directory** und dann **Benutzer** und **Gelöschte Benutzer**.

    Überprüfen Sie die Liste mit den Benutzern, die wiederhergestellt werden können.

    ![Seite „Benutzer – Gelöschte Benutzer“ mit Benutzern, für die die Wiederherstellung noch möglich ist](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Wiederherstellen eines kürzlich gelöschten Benutzers

Wenn ein Benutzerkonto in der Organisation gelöscht wird, weist das Konto den Status „Angehalten“ auf, und alle zugehörigen Organisationsinformationen werden beibehalten. Wenn Sie einen Benutzer wiederherstellen, werden diese Organisationsinformationen ebenfalls wiederhergestellt.

> [!Note]
> Nach der Wiederherstellung eines Benutzers werden die Lizenzen, die dem Benutzer zum Zeitpunkt der Löschung zugewiesen waren, auch dann wiederhergestellt, wenn keine Arbeitsplätze für diese Lizenzen verfügbar sind. Wenn Sie dann mehr Lizenzen verwenden, als Sie erworben haben, erfüllt Ihre Organisation möglicherweise vorübergehend die Complianceanforderungen für die Lizenzverwendung nicht mehr.

### <a name="to-restore-a-user"></a>So stellen Sie einen Benutzer wieder her
1. Suchen Sie auf der Seite **Benutzer – Gelöschte Benutzer** nach einem verfügbaren Benutzer, und wählen Sie ihn aus. Beispielsweise _Mary Parker_.

2. Wählen Sie **Benutzer wiederherstellen** aus.

    ![Seite „Benutzer – Gelöschte Benutzer“ mit hervorgehobener Option „Endgültig löschen“](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>Dauerhaftes Löschen eines Benutzers
Sie können einen Benutzer endgültig aus Ihrer Organisation löschen, ohne auf die automatische Löschung nach 30 Tagen zu warten. Ein endgültig gelöschter Benutzer kann weder von Ihnen selbst, noch von einem anderen Administrator oder vom Microsoft-Kundendienst wiederhergestellt werden.

>[!Note]
>Wenn Sie einen Benutzer versehentlich endgültig löschen, müssen Sie einen neuen Benutzer erstellen und alle Informationen manuell eingeben, die zuvor vorhanden waren. Weitere Informationen zum Erstellen eines neuen Benutzers finden Sie unter [Vorgehensweise: Hinzufügen oder Löschen von Benutzern in Azure Active Directory](add-users-azure-active-directory.md).

### <a name="to-permanently-delete-a-user"></a>So löschen Sie einen Benutzer endgültig

1. Suchen Sie auf der Seite **Benutzer – Gelöschte Benutzer** nach einem verfügbaren Benutzer, und wählen Sie ihn aus. Beispiel: _Rae Huff_.

2. Wählen Sie **Endgültig löschen** aus.

    ![Seite „Benutzer – Gelöschte Benutzer“ mit hervorgehobener Option „Benutzer löschen“](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie die Benutzer wiederhergestellt oder gelöscht haben, können Sie folgenden Aktionen ausführen:

- [Hinzufügen oder Löschen von Benutzern](add-users-azure-active-directory.md)

- [Zuweisen von Rollen zu Benutzern](active-directory-users-assign-role-azure-portal.md)

- [Hinzufügen oder Ändern von Profilinformationen](active-directory-users-profile-azure-portal.md)

- [Hinzufügen von Gastbenutzern aus einer anderen Organisation](../external-identities/what-is-b2b.md)

Weitere Informationen zu anderen verfügbaren Aufgaben der Benutzerverwaltung finden Sie in der [Dokumentation zur Azure AD-Benutzerverwaltung](../enterprise-users/index.yml).
