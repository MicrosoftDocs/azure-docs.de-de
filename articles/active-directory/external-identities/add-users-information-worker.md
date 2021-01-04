---
title: Hinzufügen von B2B-Zusammenarbeitsbenutzern als Information-Worker – Azure AD
description: B2B-Zusammenarbeit ermöglicht es Information-Workern und App-Besitzern, Azure AD Gastbenutzer für den Zugriff hinzuzufügen | Microsoft-Dokumentation
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 12/19/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 352d5218f91fa55134a7515b6aeee49857847468
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173208"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>Beschreibt, wie Benutzer in Ihrer Organisation Gastbenutzer zu einer App einladen können.

Nachdem ein Gastbenutzer dem Verzeichnis in Azure AD hinzugefügt wurde, kann ein Anwendungsbesitzer dem Gastbenutzer einen direkten Link zu der App senden, die er freigeben möchte. Azure AD-Administratoren können auch eine Self-Service-Verwaltung für katalog- oder SAML-basierte Apps in ihrem Azure AD-Mandanten einrichten. Auf diese Weise können Anwendungsbesitzer ihre eigenen Gastbenutzer verwalten, selbst wenn die Gastbenutzer noch nicht zum Verzeichnis hinzugefügt wurden. Wenn eine App für Self-Service konfiguriert ist, verwendet der Anwendungsbesitzer den Zugriffsbereich, um einen Gastbenutzer zu einer App einzuladen oder einen Gastbenutzer einer Gruppe hinzuzufügen, die Zugriff auf die App besitzt. Für die Self-Service-App-Verwaltung von katalog- und SAML-basierten Apps muss ein Administrator eine Ersteinrichtung vornehmen. Nachfolgend finden Sie eine Zusammenfassung der Setupschritte (detailliertere Anweisungen finden Sie unter [Voraussetzungen](#prerequisites) weiter unten auf dieser Seite):

 - Aktivieren von Self-Service-Gruppenverwaltung für Ihren Mandanten
 - Erstellen einer Gruppe, die der App zugewiesen wird, und Erklären des Benutzers zum Besitzer
 - Konfigurieren der App für Self-Service und Zuweisen der Gruppe zur App

> [!NOTE]
> In diesem Artikel wird beschrieben, wie Sie die Self-Service-Verwaltung für katalog- und SAML-basierte Apps einrichten, die Sie Ihrem Azure AD-Mandanten hinzugefügt haben. Sie können auch [die Self-Service-Verwaltung für Microsoft 365-Gruppen einrichten](../enterprise-users/groups-self-service-management.md), damit Ihre Benutzer den Zugriff auf ihre eigenen Microsoft 365-Gruppen verwalten können. Weitere Möglichkeiten, über die Benutzer Office-Dateien und -Apps für Gastbenutzer freigeben können, finden Sie unter [Gastzugriff in Microsoft 365-Gruppen](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) und [Freigeben von SharePoint-Dateien oder -Ordnern](https://support.office.com/article/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c).

## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>Einladen eines Gastbenutzers zur App über den Zugriffsbereich

Nach dem Konfigurieren einer App für Self-Service können Anwendungsbesitzer ihren eigenen Zugriffsbereich verwenden, um einen Gastbenutzer zu der App einzuladen, die sie freigeben möchten. Der Gastbenutzer muss Azure AD nicht unbedingt im Vorfeld hinzugefügt werden. 

1. Öffnen Sie Ihren Zugriffsbereich, indem Sie zu `https://myapps.microsoft.com` navigieren.
2. Zeigen Sie auf die App, wählen Sie die Auslassungspunkte ( **...** ) aus, und wählen Sie dann **App verwalten** aus.
 
   ![Screenshot mit dem Untermenü „App verwalten“ für die Salesforce-App](media/add-users-iw/access-panel-manage-app.png)
 
3. Wählen Sie am Anfang der Benutzerliste **+** aus.
   
   ![Screenshot mit dem Pluszeichen zum Hinzufügen von Mitgliedern zur App](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. Geben Sie im Suchfeld **Mitglieder hinzufügen** die E-Mail-Adresse für den Gastbenutzer ein. Geben Sie optional eine Begrüßungsnachricht ein.
   
   ![Screenshot mit dem Fenster „Mitglieder hinzufügen“ zum Hinzufügen eines Gasts](media/add-users-iw/access-panel-invitation.png)
   
5. Wählen Sie **Hinzufügen** aus, um eine Einladung an den Gastbenutzer zu senden. Nach dem Senden der Einladung wird das Benutzerkonto dem Verzeichnis automatisch als Gast hinzugefügt.

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>Einladen eines Benutzers zum Beitreten zu einer Gruppe, die Zugriff auf die App besitzt
Nach dem Konfigurieren einer App für Self-Service können Anwendungsbesitzer Gastbenutzer zu den von ihnen verwalteten Gruppen einladen, die Zugriff auf die Apps besitzen, die sie freigeben möchten. Die Gastbenutzer müssen nicht bereits im Verzeichnis vorhanden sein. Der Anwendungsbesitzer führt die folgenden Schritte aus, um einen Gastbenutzer zu der Gruppe einzuladen, damit er auf die App zugreifen kann.

1. Stellen Sie sicher, dass Sie Besitzer der Self-Service-Gruppe sind, die Zugriff auf die App besitzt, die Sie freigeben möchten.
2. Öffnen Sie Ihren Zugriffsbereich, indem Sie zu `https://myapps.microsoft.com` navigieren.
3. Wählen Sie die **Gruppen**-App aus.
   
   ![Screenshot mit der Gruppen-App im Zugriffsbereich](media/add-users-iw/access-panel-groups.png)
   
4. Wählen Sie unter **Gruppen in meinem Besitz** die Gruppe aus, die Zugriff auf die App besitzt, die Sie freigeben möchten.
   
   ![Screenshot, der anzeigt, wo eine Gruppe unter „Gruppen in meinem Besitz“ ausgewählt wird](media/add-users-iw/access-panel-groups-i-own.png)
   
5. Wählen Sie am Anfang der Gruppenmitgliederliste **+** aus.
   
   ![Screenshot mit dem Pluszeichen zum Hinzufügen von Mitgliedern zur Gruppe](media/add-users-iw/access-panel-groups-add-member.png)
   
6. Geben Sie im Suchfeld **Mitglieder hinzufügen** die E-Mail-Adresse für den Gastbenutzer ein. Geben Sie optional eine Begrüßungsnachricht ein.
   
   ![Screenshot mit dem Fenster „Mitglieder hinzufügen“ zum Hinzufügen eines Gasts](media/add-users-iw/access-panel-invitation.png)
   
7. Wählen Sie **Hinzufügen** aus, um die Einladung automatisch an den Gastbenutzer zu senden. Nach dem Senden der Einladung wird das Benutzerkonto dem Verzeichnis automatisch als Gast hinzugefügt.


## <a name="prerequisites"></a>Voraussetzungen

Für die Self-Service-App-Verwaltung ist ein anfängliches Setup durch einen globalen Administrator und einen Azure AD-Administrator erforderlich. Im Rahmen dieses Setups konfigurieren Sie die App für Self-Service und weisen der App eine Gruppe zu, die der Besitzer der Anwendung verwalten kann. Sie können die Gruppe auch so konfigurieren, dass jede beliebige Person die Mitgliedschaft anfordern kann, aber die Zustimmung eines Gruppenbesitzers erforderlich ist. (Weitere Informationen zur [Self-Service-Gruppenverwaltung](../enterprise-users/groups-self-service-management.md).) 

> [!NOTE]
> Sie können Gastbenutzer nicht einer dynamischen Gruppe oder einer Gruppe hinzufügen, die mit einer lokalen Active Directory-Instanz synchronisiert wird.

### <a name="enable-self-service-group-management-for-your-tenant"></a>Aktivieren von Self-Service-Gruppenverwaltung für Ihren Mandanten
1. Melden Sie sich als globaler Administrator am [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Navigationsbereich **Azure Active Directory** aus.
3. Wählen Sie **Gruppen** aus.
4. Wählen Sie unter **Einstellungen** die Option **Allgemein** aus.
5. Wählen Sie unter **Self-Service-Gruppenverwaltung** neben **Besitzer können Anforderungen für eine Gruppenmitgliedschaft im Zugriffsbereich verwalten** die Option **Ja** aus.
6. Wählen Sie **Speichern** aus.

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>Erstellen einer Gruppe, die der App zugewiesen wird, und Erklären des Benutzers zum Besitzer
1. Melden Sie sich als Azure AD-Administrator oder globaler Administrator am [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Navigationsbereich **Azure Active Directory** aus.
3. Wählen Sie **Gruppen** aus.
4. Wählen Sie **Neue Gruppe** aus.
5. Wählen Sie unter **Gruppentyp** die Option **Sicherheit** aus.
6. Geben Sie einen **Gruppennamen** und eine **Gruppenbeschreibung** ein.
7. Wählen Sie unter **Mitgliedschaftstyp** die Option **Zugewiesen** aus.
8. Wählen Sie **Erstellen** aus, und schließen Sie die Seite **Gruppe**.
9. Öffnen Sie die Gruppe auf der Seite **Gruppen – Alle Gruppen**. 
10. Wählen Sie unter **Verwalten** die Option **Besitzer** > **Besitzer hinzufügen** aus. Suchen Sie nach dem Benutzer, der den Zugriff auf die Anwendung verwalten soll. Wählen Sie den Benutzer aus, und klicken Sie dann auf **Auswählen**.

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>Konfigurieren der App für Self-Service und Zuweisen der Gruppe zur App
1. Melden Sie sich als Azure AD-Administrator oder globaler Administrator am [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie im Navigationsbereich auf **Azure Active Directory**.
3. Klicken Sie unter **Verwalten** auf **Unternehmensanwendungen** > **Alle Anwendungen**.
4. Suchen Sie in der Anwendungsliste nach der App, und öffnen Sie diese.
5. Wählen Sie unter **Verwalten** die Option **Einmaliges Anmelden** aus, und konfigurieren Sie die Anwendung für einmaliges Anmelden. (Details finden Sie unter [Verwalten des einmaligen Anmeldens für Unternehmens-Apps](../manage-apps/add-application-portal-setup-sso.md).)
6. Wählen Sie unter **Verwalten** die Option **Self-Service** aus, und richten Sie den Self-Service-App-Zugriff ein. (Details finden Sie unter [Verwenden des Self-Service-App-Zugriffs](../manage-apps/access-panel-manage-self-service-access.md).) 

    > [!NOTE]
    > Wählen Sie für die Einstellung **Welcher Gruppe sollen zugewiesene Benutzer hinzugefügt werden?** die Gruppe aus, die Sie im vorherigen Abschnitt erstellt haben.
7. Wählen Sie unter **Verwalten** die Option **Benutzer und Gruppen** aus, und stellen Sie sicher, dass die von Ihnen erstellte Self-Service-Gruppe in der Liste angezeigt wird.
8. Wählen Sie zum Hinzufügen der App zum Zugriffsbereich des Gruppenbesitzers **Benutzer hinzufügen** > **Benutzer und Gruppen** aus. Suchen Sie nach dem Gruppenbesitzer, und wählen Sie den Benutzer aus. Klicken Sie dann auf **Auswählen** und **Zuweisen**, um den Benutzer der App hinzuzufügen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Artikel zur Azure AD B2B-Zusammenarbeit:

- [Was ist die Azure AD B2B-Zusammenarbeit?](what-is-b2b.md)
- [Wie fügen Azure Active Directory-Administratoren B2B-Zusammenarbeitsbenutzer hinzu?](add-users-administrator.md)
- [B2B-Zusammenarbeit: Einlösen von Einladungen](redemption-experience.md)
- [Preise für externe Identitäten](external-identities-pricing.md)