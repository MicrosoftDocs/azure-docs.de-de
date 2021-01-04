---
title: 'Schnellstart: Anzeigen von Gruppen und Mitgliedern in Azure AD'
description: Enthält eine Anleitung dazu, wie Sie nach den Gruppen Ihrer Organisation und den zugewiesenen Mitgliedern suchen und diese anzeigen können.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18, seo-update-azuread-jan
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: ddedd753eb98a8656006c9dddc412e44330e1a21
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996727"
---
<!--As a brand-new Azure AD administrator, I need to view my organization’s groups along with the assigned members, so I can manage permissions to apps and services for people in my organization-->

# <a name="quickstart-view-your-organizations-groups-and-members-in-azure-active-directory"></a>Schnellstart: Anzeigen der Gruppen und Mitglieder Ihrer Organisation in Azure Active Directory
Über das Azure-Portal können Sie die vorhandenen Gruppen und Gruppenmitglieder Ihrer Organisation anzeigen. Gruppen werden zur Verwaltung von Benutzern (Mitgliedern) verwendet, die alle die gleichen Zugriffsstufen und Berechtigungen für potenziell eingeschränkte Apps und Dienste benötigen.

In dieser Schnellstartanleitung zeigen Sie alle vorhandenen Gruppen Ihrer Organisation sowie die zugewiesenen Mitglieder an.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen. 

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie beginnen, müssen Sie den folgenden Schritt ausführen:

- Erstellen eines Azure Active Directory-Mandanten. Weitere Informationen finden Sie unter [Quickstart: Access Azure Active Directory to create a new tenant](active-directory-access-create-new-tenant.md) (Schnellstart: Zugreifen auf Azure Active Directory zum Erstellen eines neuen Mandanten).

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.
Melden Sie sich mit dem globalen Administratorkonto für das Verzeichnis beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="create-a-new-group"></a>Erstellen einer neuen Gruppe 
Erstellen Sie eine neue Gruppe mit dem Namen _MDM policy - West_ (MDM-Richtlinie – Westen). Weitere Informationen zum Erstellen einer Gruppe finden Sie unter [Erstellen einer Gruppe und Hinzufügen von Mitgliedern in Azure Active Directory](active-directory-groups-create-azure-portal.md).

1. Wählen Sie **Azure Active Directory** > **Gruppen** und anschließend **Neue Gruppe** aus.

2. Geben Sie auf der Seite **Gruppe** die entsprechenden Informationen ein:
    
    - **Gruppentyp:** Wählen Sie **Sicherheit**.
    
    - **Gruppenname:** Geben Sie _MDM policy – West_ (MDM-Richtlinie – Westen) ein.
    
    - **Mitgliedschaftstyp:** Wählen Sie **Zugewiesen**.

3. Klicken Sie auf **Erstellen**.

## <a name="create-a-new-user"></a>Erstellen eines neuen Benutzers
Erstellen Sie einen neuen Benutzer mit dem Namen _Alain Charon_. Ein Benutzer muss vorhanden sein, bevor Sie ihn als Mitglied einer Gruppe hinzufügen können. Überprüfen Sie zuerst die Registerkarte „Benutzerdefinierte Domänennamen“, um den Namen der überprüften Domäne zu erfahren, in der Benutzer erstellt werden sollen. Weitere Informationen zum Erstellen eines Benutzers finden Sie unter [Schnellstart: Hinzufügen neuer Benutzer in Azure Active Directory](add-users-azure-active-directory.md).

1. Wählen Sie **Azure Active Directory** > **Benutzer** und anschließend **Neuer Benutzer** aus.

2. Geben Sie auf der Seite **Benutzer** die entsprechenden Informationen ein:

    - **Name:** Geben Sie _Alain Charon_ ein.

    - **Benutzername:** Geben Sie *alain\@contoso.com* ein.

3. Kopieren Sie das automatisch generierte Kennwort im Feld **Kennwort**, und wählen Sie dann **Erstellen** aus.

## <a name="add-a-group-member"></a>Hinzufügen eines Gruppenmitglieds
Sie haben eine Gruppe und einen Benutzer erstellt und können nun _Alain Charon_ als Mitglied zur Gruppe _MDM policy - West_ (MDM-Richtlinie – Westen) hinzufügen. Weitere Informationen zum Hinzufügen von Gruppenmitgliedern finden Sie unter [Verwalten der Gruppenmitgliedschaft für Benutzer in Ihrem Azure Active Directory-Mandanten](active-directory-groups-members-azure-portal.md).

1. Klicken Sie auf **Azure Active Directory** > **Gruppen**.

2. Suchen Sie auf der Seite **Gruppen – Alle Gruppen** nach der Gruppe **MDM policy - West** (MDM-Richtlinie – Westen), und wählen Sie sie aus.

3. Wählen Sie auf der Übersichtsseite für **MDM policy - West** im Bereich **Verwalten** die Option **Mitglieder** aus.

4. Wählen Sie **Mitglieder hinzufügen**, suchen Sie nach **Alain Charon**, und wählen Sie den Benutzer aus.

5. Wählen Sie **Auswählen**.

## <a name="view-all-groups"></a>Anzeigen aller Gruppen
Im Azure-Portal auf der Seite **Gruppen – Alle Gruppen** können Sie alle Gruppen Ihrer Organisation anzeigen.

- Wählen Sie **Azure Active Directory** > **Gruppen** aus.

    Die Seite **Gruppen – Alle Gruppen** wird mit all Ihren aktiven Gruppen angezeigt.

    ![Seite „Gruppen – Alle Gruppen“ mit allen vorhandenen Gruppen](media/active-directory-groups-view-azure-portal/groups-all-groups-blade-with-all-groups.png)

## <a name="search-for-the-group"></a>Suchen der Gruppe
Suchen Sie auf der Seite **Gruppen – Alle Gruppen** die Gruppe **MDM policy - West** (MDM-Richtlinie – Westen).

1. Geben Sie auf der Seite **Gruppen – Alle Gruppen** den Begriff _MDM_ ins Feld **Suche** ein.

    Die Suchergebnisse, einschließlich der Gruppe _MDM policy - West_ (MDM-Richtlinie – Westen), werden unterhalb des Felds **Suche** angezeigt.

    ![Seite „Gruppen – Alle Gruppen“ mit ausgefülltem Suchfeld](media/active-directory-groups-view-azure-portal/search-for-specific-group.png)

3. Wählen Sie die Gruppe **MDM policy – West** (MDM-Richtlinie – Westen) aus.

4. Zeigen Sie die Gruppeninformationen (u.a. die Anzahl von Mitgliedern dieser Gruppe) auf der Übersichtsseite für **MDM policy - West** (MDM-Richtlinie – Westen) an.

    ![Übersichtsseite für „MDM policy – West“ (MDM-Richtlinie – Westen) mit Mitgliedsinformationen](media/active-directory-groups-view-azure-portal/group-overview-blade.png)

## <a name="view-group-members"></a>Anzeigen von Gruppenmitgliedern
Sie haben die Gruppe gefunden und können nun alle zugewiesenen Mitglieder anzeigen.

- Wählen Sie im Bereich **Verwalten** die Option **Mitglieder** aus, und überprüfen Sie dann die vollständige Liste der dieser bestimmten Gruppe zugewiesenen Mitgliedernamen, einschließlich _Alain Charon_.

    ![Liste der Mitglieder, die der Gruppe „MDM policy - West“(MDM-Richtlinie – Westen) zugewiesen sind](media/active-directory-groups-view-azure-portal/groups-all-members.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Diese Gruppe wird in mehreren Anleitungen verwendet, die im Abschnitt **Anleitungen** dieser Dokumentation verfügbar sind. Wenn Sie diese Gruppe jedoch nicht verwenden möchten, können Sie sie und die zugewiesenen Mitglieder mithilfe der folgenden Schritte löschen:

1. Suchen Sie auf der Seite **Gruppen – Alle Gruppen** nach der Gruppe **MDM policy - West** (MDM-Richtlinie – Westen).

2.  Wählen Sie die Gruppe **MDM policy – West** (MDM-Richtlinie – Westen) aus.

    Die Übersichtsseite für **MDM policy - West** (MDM-Richtlinie – Westen) wird angezeigt.

3. Klicken Sie auf **Löschen**.

    Die Gruppe und die zugewiesenen Mitglieder werden gelöscht.

    ![Übersichtsseite „MDM policy - West“ (MDM-Richtlinie – Westen) mit hervorgehobenem Link „Löschen“](media/active-directory-groups-view-azure-portal/group-overview-blade-delete.png)

    >[!Important]
    >Dadurch wird nicht der Benutzer Alain Charon, sondern nur seine Mitgliedschaft in der gelöschten Gruppe gelöscht.

## <a name="next-steps"></a>Nächste Schritte
Fahren Sie mit dem nächsten Artikel fort, um zu erfahren, wie Sie Ihrem Azure AD-Verzeichnis ein Abonnement zuordnen.

> [!div class="nextstepaction"]
> [Zuordnen eines Azure-Abonnements](active-directory-how-subscriptions-associated-directory.md)
