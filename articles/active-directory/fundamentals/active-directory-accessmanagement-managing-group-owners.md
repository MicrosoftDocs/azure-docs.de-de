---
title: Hinzufügen oder Entfernen von Gruppenbesitzern – Azure Active Directory | Microsoft-Dokumentation
description: Hier finden Sie Anweisungen zum Hinzufügen oder Entfernen von Gruppenbesitzern in Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd684e1bd48f877a74280b33b4df65d7baaa0fe7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65507170"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Hinzufügen oder Entfernen von Gruppenbesitzern in Azure Active Directory
Azure AD-Gruppen (Azure Active Directory) befinden sich im Besitz von Gruppenbesitzern und werden von diesen verwaltet. Gruppenbesitzer können Benutzer oder Dienstprinzipale sein, die in der Lage sind, die Gruppe einschließlich der Mitgliedschaft zu verwalten. Nur bestehende Gruppenbesitzer oder Gruppen verwaltende Administratoren können Gruppenzuordnungen vornehmen. Die Gruppenbesitzer müssen der Gruppe nicht angehören.

Wenn eine Gruppe keinen Besitzer hat, können Gruppen verwaltende Administratoren die Gruppe weiterhin verwalten.

## <a name="add-an-owner-to-a-group"></a>Hinzufügen eines Besitzers zu einer Gruppe
Nachfolgend finden Sie Anweisungen zum Hinzufügen eines Benutzers als Besitzer zu einer Gruppe über das Azure AD-Portal. Um einen Dienstprinzipal als Besitzer einer Gruppe hinzuzufügen, befolgen Sie die Anweisungen, um dies mit [PowerShell](https://docs.microsoft.com/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0) durchzuführen.

### <a name="to-add-a-group-owner"></a>So fügen Sie einen Gruppenbesitzer hinzu
1. Melden Sie sich mit dem Konto eines globalen Administrators für das Verzeichnis beim [Azure-Portal](https://portal.azure.com) an.

2. Klicken Sie auf **Azure Active Directory**, klicken Sie auf **Gruppen**, und wählen Sie anschließend die Gruppe aus, der Sie einen Besitzer hinzufügen möchten. (In diesem Beispiel wird *MDM policy - West* verwendet.)

3. Klicken Sie auf der Übersichtsseite für **MDM policy - West** auf **Besitzer**.

    ![Übersichtsseite für „MDM policy - West“ mit hervorgehobener Option „Besitzer“](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. Klicken Sie auf der Seite **MDM policy - West - Besitzer** auf **Besitzer hinzufügen**, suchen Sie nach dem Benutzer, der als neuer Gruppenbesitzer fungieren soll, wählen Sie ihn aus, und klicken Sie anschließend auf **Auswählen**.

    ![Seite „MDM policy - West - Besitzer“ mit hervorgehobener Option „Besitzer hinzufügen“](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Wenn Sie nach dem Auswählen des neuen Besitzers die Seite **Besitzer** aktualisieren, sehen Sie, dass der Name der Liste mit den Besitzern hinzugefügt wurde.

## <a name="remove-an-owner-from-a-group"></a>Entfernen eines Besitzers aus einer Gruppe
Entfernen Sie einen Besitzer mithilfe von Azure AD aus einer Gruppe.

### <a name="to-remove-an-owner"></a>So entfernen Sie einen Besitzer
1. Melden Sie sich mit dem Konto eines globalen Administrators für das Verzeichnis beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie **Azure Active Directory** > **Gruppen** und anschließend die Gruppe aus, aus der Sie einen Besitzer löschen möchten. (In diesem Beispiel wird *MDM policy - West* (MDM-Richtlinie – Westen) verwendet.)

3. Klicken Sie auf der Übersichtsseite für **MDM policy - West** auf **Besitzer**.

    ![Übersichtsseite für „MDM policy - West“ mit hervorgehobener Option „Besitzer“](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. Wählen Sie auf der Seite **MDM policy - West - Besitzer** den Benutzer aus, den Sie als Gruppenbesitzer entfernen möchten, klicken Sie auf der Informationsseite des Benutzers auf **Entfernen**, und klicken Sie anschließend auf **Ja**, um Ihre Entscheidung zu bestätigen.

    ![Informationsseite des Benutzers mit hervorgehobener Option „Entfernen“](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    Wenn Sie nach dem Entfernen des Besitzers zur Seite **Besitzer** zurückkehren, sehen Sie, dass der Name aus der Liste mit den Besitzern entfernt wurde.

## <a name="next-steps"></a>Nächste Schritte
- [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](active-directory-manage-groups.md)

- [Azure Active Directory-Cmdlets zum Konfigurieren von Gruppeneinstellungen](../users-groups-roles/groups-settings-cmdlets.md)

- [Verwenden von Gruppen zum Zuweisen des Zugriffs auf eine integrierte SaaS-App](../users-groups-roles/groups-saasapps.md)

- [Integrieren lokaler Identitäten in Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

- [Azure Active Directory-Cmdlets zum Konfigurieren von Gruppeneinstellungen](../users-groups-roles/groups-settings-v2-cmdlets.md)
