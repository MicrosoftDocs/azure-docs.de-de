---
title: 'Schnellstart: Hinzufügen von Gastbenutzern im Azure-Portal: Azure Active Directory'
description: In diesem Schnellstart erfahren Sie, wie Azure AD-Administratoren B2B-Gastbenutzer im Azure-Portal hinzufügen, und wie der B2B-Einladungsworkflow aussieht.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: quickstart
ms.date: 07/02/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: b91ec337a3237e1bde54b42463d97a803f113c7c
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65813134"
---
# <a name="quickstart-add-guest-users-to-your-directory-in-the-azure-portal"></a>Schnellstart: Hinzufügen von Gastbenutzern zu Ihrem Verzeichnis im Azure-Portal

Sie können jeden zur Zusammenarbeit mit Ihrer Organisation einladen, indem Sie ihn als Gastbenutzer Ihrem Verzeichnis hinzufügen. Senden Sie den gewünschten Benutzern entweder eine Einladungs-E-Mail mit dem Einlöselink oder den direkten Link zu der App, die Sie freigeben möchten. Gastbenutzer können sich mit ihrem Geschäfts-, Schul- oder Unikonto bzw. mit ihrer Identität bei einem sozialen Netzwerk anmelden.

In diesem Schnellstart fügen Sie Azure AD einen neuen Gastbenutzer hinzu, senden eine Einladung und erfahren, wie der Gastbenutzer die Einladung einlöst.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für die Durchführung des Szenarios im Rahmen dieses Tutorials benötigen Sie Folgendes:

 - Eine Rolle, mit der Sie Benutzer in Ihrem Mandantenverzeichnis erstellen können, z.B. die globale Administratorrolle oder eine Verzeichnisrolle für eingeschränkte Administratoren
 - Ein gültiges E-Mail-Konto, das Sie Ihrem Mandantenverzeichnis hinzufügen können und mit dem Sie die Testeinladungs-E-Mail erhalten

## <a name="add-a-new-guest-user-in-azure-ad"></a>Hinzufügen eines neuen Gastbenutzers in Azure AD

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als Azure AD-Administrator an.
2. Wählen Sie im linken Bereich **Azure Active Directory** aus.
3.  Wählen Sie unter **Verwalten** die Option **Benutzer** aus.

    ![Screenshot, der zeigt, wo Sie die Option „Benutzer“ auswählen können.](media/quickstart-add-users-portal/quickstart-users-portal-user.png)

4.  Klicken Sie auf **Neuer Gastbenutzer**.

    ![Screenshot: Auswahl der Option „Neuer Gastbenutzer“](media/quickstart-add-users-portal/quickstart-users-portal-user-3.png)

5.  Geben Sie unter **Benutzername** die E-Mail-Adresse des externen Benutzers ein. Geben Sie unter **Persönliche Nachricht zur Einladung hinzufügen** eine Begrüßungsnachricht ein. 

    ![Screenshot, der zeigt, wo Sie die Einladungsnachricht für den Gastbenutzer eingeben können.](media/quickstart-add-users-portal/quickstart-users-portal-user-4.png)

6. Klicken Sie auf **Einladen**. Die Einladung wird daraufhin automatisch an den Gastbenutzer gesendet. Oben rechts wird die Meldung **Benutzer erfolgreich eingeladen.** angezeigt. 
7.  Nach dem Senden der Einladung wird das Benutzerkonto dem Verzeichnis automatisch als Gast hinzugefügt.

## <a name="assign-an-app-to-the-guest-user"></a>Zuweisen einer App zum Gastbenutzer
Fügen Sie Ihrem Testmandanten die Salesforce-App hinzu, und weisen Sie den Testgastbenutzer der App zu.
1.  Melden Sie sich im Azure-Portal als Azure AD-Administrator an.
2.  Wählen Sie im linken Bereich **Unternehmensanwendungen** aus.
3.  Wählen Sie **Neue Anwendung** aus.
4. Suchen Sie unter **Aus Katalog hinzufügen** nach **Salesforce**, und wählen Sie die Option aus.

    ![Screenshot, der das Suchfeld für „Aus Katalog hinzufügen“ zeigt.](media/quickstart-add-users-portal/quickstart-users-portal-select-salesforce.png)
5. Wählen Sie **Hinzufügen**.
6. Wählen Sie unter **Verwalten** **Einmaliges Anmelden** und unter **SSO-Modus** **Kennwortbasierte Anmeldung** aus, und klicken Sie auf **Speichern**.
7. Wählen Sie unter **Verwalten** **Benutzer und Gruppen** > **Benutzer hinzufügen** > **Benutzer und Gruppen** aus.
8. Verwenden Sie das Suchfeld, um bei Bedarf nach dem Testbenutzer zu suchen, und wählen Sie den Testbenutzer in der Liste aus. Klicken Sie dann auf **Auswählen**.
9. Wählen Sie **Zuweisen** aus. 

## <a name="accept-the-invitation"></a>Annehmen der Einladung
Melden Sie sich nun als Gastbenutzer an, um die Einladung anzusehen.
1.  Melden Sie sich im E-Mail-Konto des Testgastbenutzers an.
2.  Im Posteingang befindet sich die E-Mail „Sie sind eingeladen“.

    ![Screenshot, der die B2B-Einladungs-E-Mail zeigt.](media/quickstart-add-users-portal/quickstart-users-portal-email-small.png)

3.  Wählen Sie im E-Mail-Text **Loslegen** aus. Daraufhin wird im Browser die Seite **Berechtigungen überprüfen** geöffnet. 

    ![Screenshot, der die Seite „Berechtigungen überprüfen“zeigt.](media/quickstart-add-users-portal/quickstart-users-portal-accept.png)

4. Wählen Sie **Akzeptieren** aus. Daraufhin wird der Zugriffsbereich geöffnet, in dem die Anwendungen aufgelistet werden, auf die der Gastbenutzer zugreifen kann.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Löschen Sie den Testgastbenutzer und die Test-App, wenn sie nicht mehr benötigt werden.
1.  Melden Sie sich im Azure-Portal als Azure AD-Administrator an.
2.  Wählen Sie im linken Bereich **Azure Active Directory** aus.
3.  Wählen Sie unter **Verwalten** **Unternehmensanwendungen** aus.
4.  Öffnen Sie die Anwendung **Salesforce**, und wählen Sie dann **Löschen** aus.
5.  Wählen Sie im linken Bereich **Azure Active Directory** aus.
6.  Wählen Sie unter **Verwalten** die Option **Benutzer** aus.
7.  Wählen Sie den Testbenutzer und dann **Benutzer löschen** aus.

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie einen Gastbenutzer im Azure-Portal erstellt und eine Einladung zur Freigabe von Apps gesendet. Dann haben Sie gesehen, wie die Einladung aus Sicht des Gastbenutzers angenommen wird, und überprüft, ob die App im Zugriffsbereich des Gastbenutzers angezeigt wird. Weitere Informationen zum Hinzufügen von Gastbenutzern für die Zusammenarbeit finden Sie unter [Hinzufügen von Benutzern für die Azure Active Directory B2B-Zusammenarbeit im Azure-Portal](add-users-administrator.md).
