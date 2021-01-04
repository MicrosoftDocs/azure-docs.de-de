---
title: Anzeigen und Zuweisen von Berechtigungen der Administratorrolle – Azure AD | Microsoft-Dokumentation
description: Sie können jetzt Mitglieder einer Azure AD-Administratorrolle im Portal anzeigen und verwalten. Für Benutzer vorgesehen, die häufig Rollenzuweisungen verwalten.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9330c6d34dcf5a799d65ebba176565ff031cb956
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376781"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Anzeigen und Zuweisen von Benutzerrollen in Azure Active Directory

Sie können jetzt im Azure Active Directory-Portal sämtliche Mitglieder der Administratorrollen anzeigen und verwalten. Wenn Sie häufig Rollenzuweisungen verwalten, bevorzugen Sie diese Funktionalität wahrscheinlich. Und wenn Sie sich jemals gefragt haben, welchen Zweck diese Rollen haben, können Sie für jede der Azure AD-Administratorrollen eine ausführliche Liste mit Berechtigungen anzeigen.

## <a name="view-all-roles"></a>Anzeigen aller Rollen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie **Azure Active Directory** aus.

1. Wählen Sie **Rollen und Administratoren** aus, um die Liste mit allen verfügbaren Rollen anzuzeigen.

1. Wählen Sie die Auslassungspunkte rechts neben jeder Zeile aus, um die Berechtigungen für die Rolle anzuzeigen. Wählen Sie eine Rolle aus, um die Benutzer anzuzeigen, die der Rolle zugewiesen sind. Wenn Ihre Darstellung von der folgenden Abbildung abweicht, lesen Sie den Hinweis unter [Anzeigen von Zuweisungen für privilegierte Rollen](#view-assignments-for-privileged-roles), um zu überprüfen, ob Sie sich in Privileged Identity Management (PIM) befinden.

    ![Liste der Rollen im Azure AD-Portal](./media/manage-roles-portal/view-roles-in-azure-active-directory.png)

## <a name="view-my-roles"></a>Anzeigen meiner Rollen

Sie können auch Ihre eigenen Berechtigungen ohne großen Aufwand anzeigen. Wählen Sie auf der Seite **Rollen und Administratoren** die Option **Ihre Rolle** , um die Rollen anzuzeigen, die Ihnen derzeit zugewiesen sind.

## <a name="view-assignments-for-privileged-roles"></a>Anzeigen von Zuweisungen für privilegierte Rollen

Für weitere Verwaltungsfunktionen können Sie **Manage in PIM** (In PIM verwalten) auswählen. Administratoren für privilegierte Rollen können Zuweisungen vom Typ „Permanent“ (in der Rolle stets aktiv) in „Berechtigt“ (nur bei Erweiterung in die Rolle) ändern. Wenn Sie nicht über Privileged Identity Management verfügen, können Sie trotzdem **Manage in PIM** (In PIM verwalten) auswählen, um sich für eine Testversion zu registrieren. Für Privileged Identity Management ist ein [Azure AD Premium P2-Lizenzplan](../privileged-identity-management/subscription-requirements.md) erforderlich.

![Liste der Mitglieder einer Administratorrolle](./media/manage-roles-portal/member-list.png)

Wenn Sie globaler Administrator oder Administrator für privilegierte Rollen sind, können Sie ganz einfach Mitglieder hinzufügen oder entfernen, die Liste filtern oder ein Mitglied auswählen, um dessen aktive zugewiesene Rollen anzuzeigen.

> [!Note]
> Wenn Sie über eine Azure AD Premium P2-Lizenz verfügen und Privileged Identity Management bereits nutzen, werden alle Rollenverwaltungsaufgaben in Privileged Identity Management und nicht in Azure AD ausgeführt.
>
> ![In PIM verwaltete Azure AD-Rollen für Benutzer, die PIM bereits nutzen und über eine Premium P2-Lizenz verfügen](./media/manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="view-a-users-role-permissions"></a>Anzeigen der Rollenberechtigungen eines Benutzers

Wenn Sie die Mitglieder einer Rolle anzeigen, wählen Sie **Beschreibung** aus, um die vollständige Liste der von der Rollenzuweisung gewährten Berechtigungen anzuzeigen. Die Seite enthält Links zu relevanter Dokumentation, die Ihnen bei der Verwaltung von Verzeichnisrollen als Unterstützung dienen soll.

![Screenshot der Seite „Globaler Administrator | Beschreibung“](./media/manage-roles-portal/role-description.png)

## <a name="download-role-assignments"></a>Herunterladen von Rollenzuweisungen

Um alle Zuweisungen für eine bestimmte Rolle herunterzuladen, wählen Sie auf der Seite **Rollen und Administratoren** eine Rolle aus, und klicken Sie dann auf **Rollenzuweisungen herunterladen**. Es wird eine CSV-Datei heruntergeladen, die Zuweisungen in allen Bereichen für diese Rolle auflistet.

![Herunterladen sämtlicher Zuweisungen für eine Rolle](./media/manage-roles-portal/download-role-assignments.png)

## <a name="assign-a-role"></a>Zuweisen einer Rolle

1. Melden Sie sich mit Berechtigungen vom Typ „Globaler Administrator“ oder Administrator für privilegierte Rollen“ beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie **Azure Active Directory** aus.

1. Wählen Sie **Rollen und Administratoren** aus, um die Liste mit allen verfügbaren Rollen anzuzeigen.

1. Wählen Sie eine Rolle aus, um ihre Zuweisungen anzuzeigen.

    ![Screenshot der Seite „Benutzeradministrator | Zuweisungen“, Aktion „Im PIM verwalten“ ausgewählt](./media/manage-roles-portal/member-list.png)

1. Wählen Sie  **Zuweisungen hinzufügen** und anschließend die Rollen aus, die Sie zuweisen möchten. Für weitere Verwaltungsfunktionen können Sie **Manage in PIM** (In PIM verwalten) auswählen. Wenn Ihre Darstellung von der folgenden Abbildung abweicht, lesen Sie den Hinweis unter [Anzeigen von Zuweisungen für privilegierte Rollen](#view-assignments-for-privileged-roles), um zu überprüfen, ob Sie sich in PIM befinden.

    ![Liste der Berechtigungen für eine Administratorrolle](./media/manage-roles-portal/directory-role-select-role.png)

## <a name="next-steps"></a>Nächste Schritte

* Im [Forum für Azure AD-Administratorrollen](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) können Sie sich gerne mit uns in Verbindung setzen.
* Weitere Informationen zu Rollen und zur Zuweisung von Administratorrollen finden Sie unter [Zuweisen von Administratorrollen](permissions-reference.md).
* Informationen zu Standardbenutzerberechtigungen finden Sie unter [Vergleich von Standardbenutzerberechtigungen für Gäste und Mitglieder](../fundamentals/users-default-permissions.md).
