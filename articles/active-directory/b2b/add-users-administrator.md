---
title: Hinzufügen von B2B-Zusammenarbeitsbenutzern über das Azure-Portal – Azure Active Directory | Microsoft-Dokumentation
description: Hier wird gezeigt, wie ein Administrator seinem Verzeichnis unter Verwendung von Azure AD B2B-Zusammenarbeit (Azure Active Directory) Gastbenutzer aus einer Partnerorganisation hinzufügen kann.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66b3e68ff2199c6a8bf4da9e02caaf93ee69342b
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65812827"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Hinzufügen von Azure Active Directory B2B-Zusammenarbeitsbenutzern über das Azure-Portal

Als Benutzer mit einer der eingeschränkten Administratorverzeichnisrollen können Sie B2B-Zusammenarbeitsbenutzer über das Azure-Portal einladen. Gastbenutzer können zu einem Verzeichnis, zu einer Gruppe oder zu einer Anwendung eingeladen werden. Nachdem Sie einen Benutzer mithilfe einer der verfügbaren Methoden eingeladen haben, wird das Konto des eingeladenen Benutzers in Azure Active Directory (Azure AD) mit dem Benutzertyp *Gast* hinzugefügt. Der Gastbenutzer muss daraufhin seine Einladung annehmen, um auf Ressourcen zugreifen zu können.

Nachdem Sie dem Verzeichnis einen Gastbenutzer hinzugefügt haben, können Sie dem Gastbenutzer einen direkten Link zu einer freigegebenen App senden, oder der Gastbenutzer kann auf die Einlösungs-URL in der Einladungs-E-Mail klicken. Weitere Informationen zum Annahmevorgang finden Sie unter [Azure Active Directory B2B-Zusammenarbeit: Einlösen von Einladungen](redemption-experience.md).

> [!IMPORTANT]
> Sie sollten die Schritte in [ Hinzufügen der Datenschutzinformationen Ihrer Organisation in Azure Active Directory](https://aka.ms/adprivacystatement) ausführen. Im Rahmen der erstmaligen Annahme einer Einladung muss ein eingeladener Benutzer Ihren Datenschutzbestimmungen zustimmen, um fortfahren zu können. 

## <a name="before-you-begin"></a>Voraussetzungen

Stellen Sie sicher, dass die Einstellungen für externe Zusammenarbeit Ihrer Organisation so konfiguriert sind, dass Sie Gäste einladen dürfen. Standardmäßig können alle Benutzer und Administratoren Gäste einladen. Die Richtlinien für externe Zusammenarbeit Ihrer Organisation können jedoch so konfiguriert sein, dass bestimmte Benutzer oder Administratoren keine Gäste einladen dürfen. Informationen dazu, wie Sie diese Richtlinien anzeigen und festlegen, finden Sie unter [Delegieren von Einladungen zur Azure Active Directory B2B-Zusammenarbeit](delegate-invitations.md).

## <a name="add-guest-users-to-the-directory"></a>Hinzufügen von Gastbenutzern zum Verzeichnis

Gehen Sie wie folgt vor, um B2B-Zusammenarbeitsbenutzer dem Verzeichnis hinzuzufügen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Benutzer an, dem eine eingeschränkte Azure AD-Administratorrolle oder die Rolle „Gasteinladender“ zugewiesen ist.
2. Klicken Sie im Navigationsbereich auf **Azure Active Directory**.
3. Wählen Sie unter **Verwalten** die Option **Benutzer** aus.
4. Klicken Sie auf **Neuer Gastbenutzer**.

   ![Position von „Neuer Gastbenutzer“ auf der Benutzeroberfläche](./media/add-users-administrator/NewGuestUser-Directory.png) 
 
   > [!NOTE]
   > Die Option **Neuer Gastbenutzer** ist auch auf der Seite **Organisationsbeziehungen** verfügbar. Wählen Sie in **Azure Active Directory** unter **Verwalten** die Option **Organisationsbeziehungen** aus.

5. Geben Sie unter **Benutzername** die E-Mail-Adresse des externen Benutzers ein. Geben Sie optional eine Begrüßungsnachricht ein. Beispiel:

   ![Position von „Neuer Gastbenutzer“ auf der Benutzeroberfläche](./media/add-users-administrator/InviteGuest.png) 

    > [!NOTE]
    > Gruppen-E-Mail-Adressen werden nicht unterstützt. Geben Sie die E-Mail-Adresse einer Einzelperson ein. Einige E-Mail-Anbieter erlauben Benutzern auch, ihren E-Mail-Adressen ein Pluszeichen (+) und zusätzlichen Text hinzuzufügen. Dies kann beispielsweise beim Filtern im Posteingang nützlich sein. Allerdings unterstützt Azure AD derzeit keine Pluszeichen in E-Mail-Adressen. Um Probleme bei der Übermittlung zu vermeiden, lassen Sie das Pluszeichen und alle nachfolgenden Zeichen bis zum @-Symbol weg.

6. Klicken Sie auf **Einladen**. Die Einladung wird daraufhin automatisch an den Gastbenutzer gesendet. 
 
Nach dem Senden der Einladung wird das Benutzerkonto dem Verzeichnis automatisch als Gast hinzugefügt.


![B2B-Benutzer mit Benutzertyp „Gast“](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Hinzufügen von Gastbenutzern zu einer Gruppe
Gehen Sie wie folgt vor, um einer Gruppe Benutzer für die B2B-Zusammenarbeit manuell hinzuzufügen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Azure AD-Administrator an.
2. Klicken Sie im Navigationsbereich auf **Azure Active Directory**.
3. Wählen Sie unter **Verwalten** die Option **Gruppen** aus.
4. Wählen Sie eine Gruppe aus, oder klicken Sie auf **Neue Gruppe**, um eine neue Gruppe zu erstellen. Es empfiehlt sich, in der Gruppenbeschreibung anzugeben, dass die Gruppe B2B-Gastbenutzer enthält.
5. Wählen Sie **Mitglieder** aus. 
6. Führen Sie einen der folgenden Schritte aus:
   - Falls der Gastbenutzer bereits im Verzeichnis vorhanden ist, suchen Sie nach dem B2B-Benutzer. Wählen Sie den Benutzer aus, und klicken Sie auf **Auswählen**, um ihn der Gruppe hinzuzufügen.
   - Wenn der Gastbenutzer im Verzeichnis nicht bereits vorhanden ist, laden Sie ihn in die Gruppe ein. Geben Sie dazu seine E-Mail-Adresse in das Suchfeld ein, verfassen Sie anschließend optional eine persönliche Nachricht, und klicken Sie dann auf **Auswählen**. Die Einladung wird automatisch an den eingeladenen Benutzer gesendet.
     
     ![Schaltfläche „Einladen“ zum Hinzufügen von Gastmitgliedern hinzufügen](./media/add-users-administrator/GroupInvite.png)
   
Für Azure AD B2B-Zusammenarbeit können auch dynamische Gruppen verwendet werden. Weitere Informationen finden Sie unter [Dynamische Gruppen und Azure Active Directory B2B-Zusammenarbeit](use-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Hinzufügen von Gastbenutzern zu einer Anwendung

Gehen Sie wie folgt vor, um einer Anwendung Benutzer für die B2B-Zusammenarbeit hinzuzufügen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Azure AD-Administrator an.
2. Klicken Sie im Navigationsbereich auf **Azure Active Directory**.
3. Klicken Sie unter **Verwalten** auf **Unternehmensanwendungen** > **Alle Anwendungen**.
4. Wählen Sie die Anwendung aus, der Sie Gastbenutzer hinzufügen möchten.
5. Klicken Sie auf dem Anwendungsdashboard auf **Benutzer gesamt**, um den Bereich **Benutzer und Gruppen** zu öffnen.

    ![Schaltfläche „Benutzer gesamt“ zum Öffnen von „Benutzern und Gruppen“](./media/add-users-administrator/AppUsersAndGroups.png)

6. Klicken Sie auf **Benutzer hinzufügen**.
7. Klicken Sie unter **Zuweisung hinzufügen** auf **Benutzer und Gruppen**.
8. Führen Sie einen der folgenden Schritte aus:
   - Falls der Gastbenutzer bereits im Verzeichnis vorhanden ist, suchen Sie nach dem B2B-Benutzer. Wählen Sie den Benutzer aus, und klicken Sie dann auf **Auswählen** > **Zuweisen**, um ihn der App hinzuzufügen.
   - Falls der Gastbenutzer noch nicht im Verzeichnis vorhanden ist, geben Sie unter **Mitglied auswählen oder externen Benutzer einladen** die E-Mail-Adresse des Benutzers ein. Geben Sie im Nachrichtenfeld optional eine persönliche Nachricht ein. Klicken Sie unter dem Nachrichtenfeld auf **Einladen**.
           
       ![Schaltfläche „Einladen“ zum Hinzufügen von Gastmitgliedern hinzufügen](./media/add-users-administrator/AppInviteUsers.png)
   
      Klicken Sie auf **Auswählen** > **Zuweisen**, um den Benutzer der App hinzuzufügen. Der eingeladenen Benutzer erhält die Einladung automatisch.

9. Der Gastbenutzer wird in der Anwendungsliste **Benutzer und Gruppen** mit der zugewiesenen Rolle **Standardzugriff** angezeigt. Wenn Sie die Rolle ändern möchten, führen Sie folgende Schritte aus:
   - Wählen Sie den Gastbenutzer aus, und klicken Sie dann auf **Bearbeiten**. 
   - Klicken Sie unter **Zuweisung bearbeiten** auf **Rolle auswählen**, und wählen Sie die Rolle aus, die Sie dem ausgewählten Benutzer zuweisen möchten.
   - Klicken Sie auf **Auswählen**.
   - Klicken Sie auf **Zuweisen**.
 
## <a name="resend-invitations-to-guest-users"></a>Erneutes Senden von Einladungen an Gastbenutzer

Wenn ein Gastbenutzer seine Einladung noch nicht angenommen hat, können Sie die Einladungs-E-Mail erneut senden.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Azure AD-Administrator an.
2. Klicken Sie im Navigationsbereich auf **Azure Active Directory**.
3. Wählen Sie unter **Verwalten** die Option **Benutzer** aus.
5. Wählen Sie das Benutzerkonto aus.
6. Klicken Sie unter **Verwalten** auf **Profil**.
7. Sofern der Benutzer die Einladung noch nicht angenommen hat, steht die Option **Einladung erneut senden** zur Verfügung. Klicken Sie auf diese Schaltfläche, um die Einladung erneut zu senden.

   ![Option „Einladung erneut senden“ im Benutzerprofil](./media/add-users-administrator/Resend-Invitation.png)

> [!NOTE]
> Wenn Sie eine Einladung erneut senden, in der der Benutzer ursprünglich zu einer bestimmten App weitergeleitet wurde, gelangt der Benutzer über den Link in der neuen Einladung stattdessen zur obersten Ebene des Zugriffsbereichs.

## <a name="next-steps"></a>Nächste Schritte

- Unter [Wie fügen Information-Worker B2B-Zusammenarbeitsbenutzer zu Azure Active Directory hinzu?](add-users-information-worker.md) erfahren Sie, wie Benutzer ohne Azure AD-Administratorrolle B2B-Gastbenutzer hinzufügen können.
- Informationen zur Einladungs-E-Mail finden Sie unter [Die Elemente der Einladungs-E-Mail für die B2B-Zusammenarbeit – Azure Active Directory](invitation-email-elements.md).

