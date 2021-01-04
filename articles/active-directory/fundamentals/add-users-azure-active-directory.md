---
title: Hinzufügen oder Löschen von Benutzern – Azure Active Directory | Microsoft-Dokumentation
description: Anweisungen zum Hinzufügen neuer Benutzer oder Löschen vorhandener Benutzer mithilfe von Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 11/12/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: de529a8ffb0c72854904717c71dbc322c919f1a8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996694"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Hinzufügen oder Löschen von Benutzern in Azure Active Directory

Sie können Ihrer Azure Active Directory-Organisation neue Benutzer hinzufügen oder vorhandene Benutzer aus der Azure AD-Organisation löschen. Sie müssen ein Benutzeradministrator oder globaler Administrator sein, um Benutzer hinzufügen oder löschen zu können.

## <a name="add-a-new-user"></a>Hinzufügen eines neuen Benutzers

Sie können einen neuen Benutzer im Azure Active Directory-Portal erstellen.

Um einen neuen Benutzer hinzuzufügen, führen Sie diese Schritte aus:

1. Melden Sie sich als Benutzeradministrator für die Organisation beim [Azure-Portal](https://portal.azure.com/) an.

1. Suchen Sie auf einer beliebigen Seite nach *Azure Active Directory*, und wählen Sie es aus.

1. Wählen Sie **Benutzer** und dann **Neuer Benutzer** aus.

    ![Hinzufügen eines Benutzers über „Benutzer“ – „Alle Benutzer“ in Azure AD](media/add-users-azure-active-directory/add-user-in-users-all-users.png)

1. Geben Sie auf der Seite **Benutzer** Informationen für diesen Benutzer ein:

   - **Name**: Erforderlich. Der Vor- und Nachname des neuen Benutzers. Beispielsweise *Mary Parker*.

   - **Benutzername**. Erforderlich. Der Benutzername des neuen Benutzers. Beispiel: `mary@contoso.com`.

     Der Domänenteil des Benutzernamens muss entweder der anfängliche Standarddomänenname sein, *\<yourdomainname>.onmicrosoft.com*, oder ein benutzerdefinierter Domänennamen, z. B. *contoso.com*. Weitere Informationen zum Erstellen eines benutzerdefinierten Domänennamens finden Sie unter [Hinzufügen Ihres benutzerdefinierten Domänennamens über das Azure Active Directory-Portal](add-custom-domain.md).

   - **Gruppen**. Optional können Sie den Benutzer einer oder mehreren vorhandenen Gruppen hinzufügen. Sie können den Benutzer auch später zu Gruppen hinzufügen. Weitere Informationen zum Hinzufügen von Benutzern zu Gruppen finden Sie unter [Erstellen einer einfachen Gruppe und Hinzufügen von Mitgliedern mithilfe von Azure Active Directory](active-directory-groups-create-azure-portal.md).

   - **Verzeichnisrolle**: Wenn Sie Azure AD-Administratorberechtigungen für den Benutzer benötigen, können Sie diese einer Azure AD-Rolle hinzufügen. Sie können dem Benutzer die Rolle „Globaler Administrator“ oder mindestens eine der eingeschränkten Administratorrollen in Azure AD zuweisen. Weitere Informationen zum Zuweisen von Rollen finden Sie unter [Zuweisen von Rollen zu Benutzern](active-directory-users-assign-role-azure-portal.md).

   - **Angaben zum Beruf**: Hier können Sie weitere Informationen zum Benutzer hinzufügen oder dies später erledigen. Weitere Informationen zum Hinzufügen von Benutzerinformationen finden Sie unter [Hinzufügen oder Ändern von Benutzerprofilinformationen](active-directory-users-profile-azure-portal.md).

1. Kopieren Sie das automatisch generierte Kennwort aus dem Feld **Kennwort**. Sie müssen dieses Kennwort dem Benutzer für die erste Anmeldung bereitstellen.

1. Klicken Sie auf **Erstellen**.

Der Benutzer wird erstellt und Ihrer Azure AD-Organisation hinzugefügt.

## <a name="add-a-new-guest-user"></a>Hinzufügen eines neuen Gastbenutzers

Sie können auch einen neuen Gastbenutzer einladen, mit Ihrer Organisation zusammenzuarbeiten, indem Sie auf der Seite **Neuer Benutzer** die Option **Benutzer einladen** auswählen. Wenn die Einstellungen für externe Zusammenarbeit Ihrer Organisation so konfiguriert sind, dass Sie Gäste einladen dürfen, wird dem Benutzer eine E-Mail-Einladung geschickt, die dieser akzeptieren muss, um mit der Zusammenarbeit zu beginnen. Weitere Informationen zum Einladen von B2B-Zusammenarbeitsbenutzern finden Sie unter [Einladen von B2B-Benutzern zu Azure Active Directory](../external-identities/add-users-administrator.md).

## <a name="add-a-consumer-user"></a>Hinzufügen eines Consumerbenutzers

Möglicherweise gibt es Szenarien, in denen Sie manuell Consumerkonten im Azure Active Directory B2C-Verzeichnis (Azure AD B2C) erstellen möchten. Weitere Informationen zum Erstellen von Consumerkonten finden Sie unter [Erstellen und Löschen von Consumerbenutzern in Azure AD B2C](../../active-directory-b2c/manage-users-portal.md).

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Hinzufügen eines neuen Benutzers in einer Hybridumgebung

Wenn Sie eine Umgebung mit sowohl Azure Active Directory (Cloud) als auch Windows Server Active Directory (lokal) haben, können Sie neue Benutzer hinzufügen, indem Sie die vorhandenen Benutzerkontodaten synchronisieren. Weitere Informationen zu Hybridumgebungen und Benutzern finden Sie unter [Integrieren Ihrer lokalen Verzeichnisse in Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Löschen eines Benutzers

Sie können einen vorhandenen Benutzer im Azure Active Directory-Portal löschen.

Um einen Benutzer zu löschen, befolgen Sie diese Schritte:

1. Melden Sie sich mit dem Konto eines Benutzeradministrators für die Organisation beim [Azure-Portal](https://portal.azure.com/) an.

1. Suchen Sie auf einer beliebigen Seite nach *Azure Active Directory*, und wählen Sie es aus.

1. Suchen Sie den Benutzer, den Sie aus Ihrem Azure AD-Mandanten löschen möchten, und wählen Sie ihn aus. Beispielsweise _Mary Parker_.

1. Wählen Sie **Benutzer löschen** aus.

    ![Seite „Benutzer – Alle Benutzer“ mit Hervorhebung von „Benutzer löschen“](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

Der Benutzer wird gelöscht und nicht mehr auf der Seite **Benutzer – Alle Benutzer** angezeigt. Sie können den Benutzer für die nächsten 30 Tage auf der Seite **Gelöschte Benutzer** anzeigen und während dieses Zeitraums wiederherstellen. Weitere Informationen zum Wiederherstellen eines Benutzers finden Sie unter [Wiederherstellen oder Entfernen eines kürzlich gelöschten Benutzers mithilfe von Azure Active Directory](active-directory-users-restore.md).

Wenn ein Benutzer gelöscht wird, werden alle vom Benutzer verwendeten Lizenzen den anderen Benutzer zur Verfügung gestellt.

>[!Note]
>Sie müssen Windows Server Active Directory verwenden, um die Identität, Kontaktinformationen oder Jobinformationen für Benutzer zu aktualisieren, deren Autoritätsquelle Windows Server Active Directory ist. Nachdem Sie das Update abgeschlossen haben, müssen Sie bis zum Abschluss des nächsten Synchronisierungszyklus warten, bevor die Änderungen angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Benutzer hinzugefügt haben, können Sie die folgenden grundlegenden Prozesse ausführen:

- [Hinzufügen oder Ändern von Profilinformationen](active-directory-users-profile-azure-portal.md)

- [Zuweisen von Rollen zu Benutzern](active-directory-users-assign-role-azure-portal.md)

- [Erstellen einer einfachen Gruppe und Hinzufügen von Mitgliedern](active-directory-groups-create-azure-portal.md)

- [Arbeiten mit dynamischen Gruppen und Benutzern](../enterprise-users/groups-create-rule.md)

Oder Sie können andere Benutzerverwaltungsaufgaben durchführen wie das [Hinzufügen von Gastbenutzern aus einem anderen Verzeichnis](../external-identities/what-is-b2b.md) oder das [Wiederherstellen eines gelöschten Benutzers](active-directory-users-restore.md). Weitere Informationen zu anderen verfügbaren Aktionen finden Sie unter [Dokumentation zur Azure Active Directory-Benutzerverwaltung](../enterprise-users/index.yml).