---
title: Verwalten von Benutzern in Azure Blockchain Workbench
description: Erfahren Sie, wie Sie Benutzer in Azure Blockchain Workbench verwalten.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/09/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 15babefda36ba37cf6df7820ac888668e4a502be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65509923"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Verwalten von Benutzern in Azure Blockchain Workbench

Azure Blockchain Workbench umfasst eine Benutzerverwaltung für Personen und Organisationen, die Teil Ihres Konsortiums sind.

## <a name="prerequisites"></a>Voraussetzungen

Eine Blockchain Workbench-Bereitstellung ist erforderlich. Weitere Informationen zu Details dieser Bereitstellung finden Sie unter [Azure Blockchain Workbench-Bereitstellung](deploy.md).

## <a name="add-azure-ad-users"></a>Hinzufügen von Azure AD-Benutzern

Azure Blockchain Workbench verwendet Azure Active Directory (Azure AD) für Authentifizierung, Zugriffssteuerung und Rollen. Benutzer im Blockchain Workbench-Azure AD-Mandanten können sich authentifizieren und Blockchain Workbench nutzen. Fügen Sie der Anwendungsrolle „Administrator“ Benutzer, damit diese interagieren und Aktionen ausführen können.

Blockchain Workbench-Benutzer müssen im Azure AD-Mandanten vorhanden sein, bevor Sie ihnen Anwendungen und Rollen zuweisen können. Um Benutzer zu Azure AD hinzuzufügen, führen Sie die folgenden Schritte aus:

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2.  Wählen Sie in der oberen rechten Ecke Ihr Konto aus, und wechseln Sie zu dem Azure AD-Mandanten, der mit Blockchain Workbench verknüpft ist.
3.  Wählen Sie **Azure Active Directory > Benutzer** aus. Sie sehen eine Liste der Benutzer in Ihrem Verzeichnis.
4.  Um Benutzer zum Verzeichnis hinzuzufügen, wählen Sie **Neuer Benutzer** aus. Für externe Benutzer wählen Sie **Neuer Gastbenutzer** aus.

    ![Neuer Benutzer](./media/manage-users/add-ad-user.png)

5.  Füllen Sie die erforderlichen Felder für den neuen Benutzer aus. Klicken Sie auf **Erstellen**.

Weitere Details zur Verwaltung von Benutzern in Azure AD finden Sie in der [Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md)-Dokumentation.

## <a name="manage-blockchain-workbench-administrators"></a>Verwalten von Blockchain Workbench-Administratoren

Nachdem Benutzer in das Verzeichnis aufgenommen wurden, müssen Sie im nächsten Schritt auswählen, welche Benutzer die Blockchain Workbench-Administratoren sind. Benutzer in der Gruppe **Administrator** sind der **Administratoranwendungsrolle** in Blockchain Workbench zugeordnet. Administratoren können Benutzer hinzufügen oder entfernen, Benutzer bestimmten Szenarien zuweisen und neue Anwendungen erstellen.

So fügen Sie Benutzer zur Gruppe **Administrator** im Azure AD-Verzeichnis hinzu:

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2.  Vergewissern Sie sich, dass Sie sich im mit Blockchain Workbench verknüpften Azure AD-Mandanten befinden, indem Sie in der oberen rechten Ecke Ihr Konto auswählen.
3.  Wählen Sie **Azure Active Directory > Unternehmensanwendungen** aus.
4.  Wählen Sie die Azure AD-Clientanwendung für Blockchain Workbench aus.
    
    ![Alle Registrierungen von Unternehmensanwendungen](./media/manage-users/select-blockchain-client-app.png)

5.  Wählen Sie **Benutzer und Gruppen > Benutzer hinzufügen** aus.
6.  Wählen Sie unter **Zuweisung hinzufügen** die Option **Benutzer** aus. Wählen oder suchen Sie den Benutzer, den Sie als Administrator hinzufügen möchten. Klicken Sie auf **Auswählen**, wenn Sie fertig sind.

    ![Hinzufügen einer Zuweisung](./media/manage-users/add-user-assignment.png)

9.  Überprüfen Sie, ob **Rolle** auf **Administrator** festgelegt ist.
10. Wählen Sie **Zuweisen** aus. Die hinzugefügten Benutzer werden in der Liste mit der zugewiesenen Administratorrolle angezeigt.

    ![Client-App-Benutzer in Blockchain](./media/manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Verwalten von Blockchain Workbench-Mitgliedern

Verwenden Sie die Blockchain Workbench-Anwendung, um Benutzer und Organisationen zu verwalten, die Teil Ihres Konsortiums sind. Sie können Benutzer zu Anwendungen und Rollen hinzufügen oder entfernen.

1. [Öffnen Sie Blockchain Workbench](deploy.md#blockchain-workbench-web-url) in Ihrem Browser, und melden Sie sich als Administrator an.

    ![Blockchain Workbench](./media/manage-users/blockchain-workbench-applications.png)

    Jeder Anwendung werden Mitglieder hinzugefügt. Mitgliedern können eine oder mehrere Anwendungsrollen zugewiesen sein, um Verträge zu initiieren oder Aktionen auszuführen.

2. Zur Verwaltung von Mitgliedern für eine Anwendung wählen Sie im Bereich **Anwendungen** eine Anwendungskachel aus.

    Die Anzahl der Mitglieder, die der ausgewählten Anwendung zugewiesen sind, wird in der Mitgliederkachel angezeigt.

    ![Auswählen einer Anwendung](./media/manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Hinzufügen eines Mitglieds zur Anwendung

1. Wählen Sie die Mitgliederkachel, um eine Liste der aktuellen Mitglieder anzuzeigen.
2. Wählen Sie **Mitglieder hinzufügen** aus.

    ![Hinzufügen von Mitgliedern](./media/manage-users/application-add-members.png)

3. Suchen Sie nach dem Namen des Benutzers.  Es werden nur Azure AD-Benutzer aufgelistet, die im Blockchain Workbench-Mandanten vorhanden sind. Wenn der Benutzer nicht gefunden wird, müssen Sie [Azure AD-Benutzer hinzufügen](#add-azure-ad-users).

    ![Hinzufügen von Mitgliedern](./media/manage-users/find-user.png)

4. Wählen Sie in der Dropdownliste eine **Rolle** aus.

    ![Auswählen von Rollenmitgliedern](./media/manage-users/application-select-role.png)

5. Wählen Sie **Hinzufügen**, um das Mitglied mit der zugehörigen Rolle zur Anwendung hinzuzufügen.

#### <a name="remove-member-from-application"></a>Entfernen eines Mitglieds aus der Anwendung

1. Wählen Sie die Mitgliederkachel, um eine Liste der aktuellen Mitglieder anzuzeigen.
2. Wählen Sie für den Benutzer, den Sie entfernen möchten, in der Dropdownliste für Rolle die Option **Entfernen** aus.

    ![Entfernen eines Mitglieds](./media/manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Ändern oder Hinzufügen einer Rolle

1. Wählen Sie die Mitgliederkachel, um eine Liste der aktuellen Mitglieder anzuzeigen.
2. Klicken Sie für den Benutzer, den Sie ändern möchten, in die Dropdownliste, und wählen Sie die neue Rolle aus.

    ![Ändern einer Rolle](./media/manage-users/application-change-role.png)

## <a name="next-steps"></a>Nächste Schritte

In dieser Anleitung haben Sie erfahren, wie Benutzer für Azure Blockchain Workbench verwaltet werden. Informationen zum Erstellen einer Blockchainanwendung finden Sie in der nächsten Anleitung.

> [!div class="nextstepaction"]
> [Erstellen einer Blockchainanwendung in Azure Blockchain Workbench](create-app.md)
