---
title: 'Tutorial: Azure Active Directory-Integration mit Land Gorilla Client | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Land Gorilla konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d12519a2bd0a5e3951d0ca9ae51a54bd728a782b
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65987892"
---
# <a name="tutorial-azure-active-directory-integration-with-land-gorilla-client"></a>Tutorial: Azure Active Directory-Integration mit Land Gorilla Client

In diesem Tutorial erfahren Sie, wie Sie Land Gorilla Client in Azure Active Directory (Azure AD) integrieren.

Die Integration von Land Gorilla Client in Azure AD bietet die folgenden Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf Land Gorilla Client hat.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Land Gorilla Client anzumelden (einmaliges Anmelden, SSO).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im Azure-Verwaltungsportal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).


## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Land Gorilla Client konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein Land Gorilla Client-Abonnement, das für das einmalige Anmelden aktiviert ist


> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/)eine einmonatige Testversion anfordern.


## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptbestandteilen:

1. Hinzufügen von Land Gorilla Client aus dem Katalog
1. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## <a name="adding-land-gorilla-client-from-the-gallery"></a>Hinzufügen von Land Gorilla Client aus dem Katalog
Zum Konfigurieren der Integration von Land Gorilla Client in Azure AD müssen Sie Land Gorilla Client aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

**Um Land Gorilla Client aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Verwaltungsportals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Active Directory][1]

1. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![ANWENDUNGEN][2]
    
1. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Hinzufügen**.

    ![ANWENDUNGEN][3]

1. Geben Sie **Land Gorilla Client** in das Suchfeld ein.

    ![Erstellen eines Azure AD-Testbenutzers](./media/landgorilla-tutorial/tutorial_landgorilla_search.png)

1. Wählen Sie im Ergebnisbereich die Option **Land Gorilla Client** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Erstellen eines Azure AD-Testbenutzers](./media/landgorilla-tutorial/tutorial_landgorilla_addfromgallery.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden über Azure AD bei Land Gorilla Client mithilfe eines Testbenutzers namens B. Simon.

Damit das einmalige Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in Land Gorilla Client als Entsprechung für einen Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Land Gorilla Client muss eine Linkbeziehung eingerichtet werden.

Diese Linkbeziehung wird hergestellt, indem Sie den Wert des **Benutzernamens** in Azure AD als Wert des **Benutzernamens** in Land Gorilla Client zuweisen.

Zum Konfigurieren und Testen des einmaligen Anmeldens bei Land Gorilla Client über Azure AD müssen Sie die folgenden Bausteine ausführen:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
1. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)** – um das einmalige Anmelden über Azure AD mit einer begrenzten Gruppe zu testen.
1. **[Erstellen eines Land Gorilla-Testbenutzers](#creating-a-land-gorilla-test-user)** – um das einmalige Anmelden über Azure AD mit dem Testbenutzer B. Simon zu testen.
1. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)** , um B. Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Testen der einmaligen Anmeldung](#testing-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens von Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden über Azure AD im Azure-Verwaltungsportal und konfigurieren das einmalige Anmelden in Ihrer Land Gorilla Client-Anwendung.

**Führen Sie zum Konfigurieren des einmaligen Anmeldens bei Land Gorilla Client über Azure AD die folgenden Schritte aus:**

1. Klicken Sie im Azure-Verwaltungsportal auf der Anwendungsintegrationsseite für **Land Gorilla Client** auf **Einmaliges Anmelden**.

    ![Configure single sign-on][4]

1. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um einmaliges Anmelden zu aktivieren.
 
    ![Configure single sign-on](./media/landgorilla-tutorial/tutorial_landgorilla_samlbase.png)

1. Führen Sie die folgenden Schritte auf der Seite **Domäne und URLs für Land Gorilla Client** aus:

    ![Configure single sign-on](./media/landgorilla-tutorial/tutorial_landgorilla_url_02.png)

    a. Geben Sie im Textfeld **Bezeichner** den Wert nach einem der folgenden Muster ein: 
    
    `https://<customer domain>.landgorilla.com/` 
    
    `https://www.<customer domain>.landgorilla.com`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL nach einem der folgenden Muster ein:

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`
    
    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`

    > [!NOTE] 
    > Hinweis: Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem eigentlichen Bezeichner und der Antwort-URL aktualisieren. Hier empfehlen wir Ihnen, den eindeutigen Wert der Zeichenfolge im Bezeichner zu verwenden. Wenden Sie sich an das [Team von Land Gorilla Client](https://www.landgorilla.com/support/), um diese Werte zu erhalten. 

1. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Metadaten-XML**, und speichern Sie die XML-Datei dann auf Ihrem Computer.

    ![Configure single sign-on](./media/landgorilla-tutorial/tutorial_landgorilla_certificate.png) 

1. Klicken Sie auf die Schaltfläche **Save** .

    ![Configure single sign-on](./media/landgorilla-tutorial/tutorial_general_400.png) 

1. Um die Konfiguration des einmaligen Anmeldens für Ihre Anwendung seitens Land Gorilla abzuschließen, wenden Sie sich an das [Supportteam von Land Gorilla Client](https://www.landgorilla.com/support/), und stellen Sie die heruntergeladene **Metadaten-XML-Datei** bereit.


### <a name="creating-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers
In diesem Abschnitt wird im Azure-Verwaltungsportal eine Testbenutzerin namens B. Simon erstellt.

![Azure AD-Benutzer erstellen][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Verwaltungsportals** auf das Symbol für **Azure Active Directory**.

    ![Erstellen eines Azure AD-Testbenutzers](./media/landgorilla-tutorial/create_aaduser_01.png) 

1. Wechseln Sie zu **Benutzer und Gruppen**, und klicken Sie auf **Alle Benutzer**, um die Liste der Benutzer anzuzeigen.
    
    ![Erstellen eines Azure AD-Testbenutzers](./media/landgorilla-tutorial/create_aaduser_02.png) 

1. Klicken Sie oben im Dialogfeld auf **Hinzufügen**, um das Dialogfeld **Benutzer** zu öffnen.
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/landgorilla-tutorial/create_aaduser_03.png) 

1. Führen Sie auf der Dialogfeldseite **Benutzer** die folgenden Schritte aus:
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/landgorilla-tutorial/create_aaduser_04.png) 

    a. Geben Sie im Textfeld **Name** den Text **B. Simon** ein.

    b. Geben Sie im Textfeld **Benutzername** die **E-Mail-Adresse** von B. Simon ein.

    c. Wählen Sie **Kennwort anzeigen** aus, und notieren Sie sich den Wert des **Kennworts**.

    d. Klicken Sie auf **Create**. 

### <a name="creating-a-land-gorilla-test-user"></a>Erstellen eines Land Gorilla-Testbenutzers

Wenden Sie sich an das [Supportteam von Land Gorilla](https://www.landgorilla.com/support/), um die Benutzer der Land Gorilla-Plattform hinzufügen zu lassen.
    
### <a name="assigning-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Land Gorilla Client gewähren.

![Benutzer zuweisen][200] 

**Um B. Simon zu Land Gorilla Client zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Öffnen Sie im Azure-Verwaltungsportal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen**, und klicken Sie auf **Alle Anwendungen**.

    ![Benutzer zuweisen][201] 

1. Wählen Sie in der Anwendungsliste **Land Gorilla Client** aus.

    ![Configure single sign-on](./media/landgorilla-tutorial/tutorial_landgorilla_app.png) 

1. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Benutzer zuweisen][202] 

1. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Benutzer zuweisen][203]

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **B. Simon** aus.

1. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.
    


### <a name="testing-single-sign-on"></a>Testen der einmaligen Anmeldung

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Land Gorilla Client“ klicken, sollten Sie automatisch bei Ihrer Land Gorilla Client-Anwendung angemeldet werden.


## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/landgorilla-tutorial/tutorial_general_01.png
[2]: ./media/landgorilla-tutorial/tutorial_general_02.png
[3]: ./media/landgorilla-tutorial/tutorial_general_03.png
[4]: ./media/landgorilla-tutorial/tutorial_general_04.png

[100]: ./media/landgorilla-tutorial/tutorial_general_100.png
[200]: ./media/landgorilla-tutorial/tutorial_general_200.png
[201]: ./media/landgorilla-tutorial/tutorial_general_201.png
[202]: ./media/landgorilla-tutorial/tutorial_general_202.png
[203]: ./media/landgorilla-tutorial/tutorial_general_203.png
