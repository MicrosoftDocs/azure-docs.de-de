---
title: 'Tutorial: Azure Active Directory-Integration mit DocuSign | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und DocuSign konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 72505cfc0a86c00882de37c35dff61a12a9c3fbe
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65899625"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Tutorial: Azure Active Directory-Integration von DocuSign

In diesem Tutorial erfahren Sie, wie Sie DocuSign in Azure Active Directory (Azure AD) integrieren.
Die Integration von DocuSign in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf DocuSign haben soll.
* Sie können es Benutzern ermöglichen, dass sie mit ihren Azure AD-Konten automatisch bei DocuSign angemeldet werden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit DocuSign konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* DocuSign-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* DocuSign unterstützt **SP**-initiiertes einmaliges Anmelden.

* DocuSign unterstützt die **Just-in-Time**-Benutzerbereitstellung.

## <a name="adding-docusign-from-the-gallery"></a>Hinzufügen von DocuSign aus dem Katalog

Zum Konfigurieren der Integration von DocuSign in Azure AD müssen Sie DocuSign über den Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um DocuSign aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld den Namen **DocuSign** ein, wählen Sie im Ergebnisbereich den Eintrag **DocuSign** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![DocuSign in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei DocuSign mithilfe einer Testbenutzerin namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in DocuSign eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei DocuSign müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für DocuSign](#configure-docusign-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines DocuSign-Testbenutzers](#create-docusign-test-user)**, um eine Entsprechung von Britta Simon in DocuSign zu erhalten, die mit der Darstellung des Benutzers in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit DocuSign die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **DocuSign** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für DocuSign](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Dies wird später in diesem Tutorial beschrieben (siehe Abschnitt **SAML 2.0-Endpunkte anzeigen**).

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **DocuSign einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-docusign-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für DocuSign

1. Melden Sie sich in einem anderen Webbrowserfenster am **DocuSign-Verwaltungsportal** als Administrator an.

2. Klicken Sie oben rechts auf der Profilseite auf das **Logo**, und klicken Sie dann auf **Zur Admin-Umgebung wechseln**.
  
    ![Konfigurieren der einmaligen Anmeldung][51]

3. Klicken Sie auf der Seite Ihrer Domänenlösungen auf **Domänen**.

    ![Konfigurieren der einmaligen Anmeldung][50]

4. Klicken Sie unter dem Abschnitt **Domänen** auf **Domäne anfordern**.

    ![Konfigurieren der einmaligen Anmeldung][52]

5. Geben Sie im Dialogfeld **Domäne anfordern** im Textfeld **Domänenname** Ihre Unternehmensdomäne ein, und klicken Sie dann auf **Anfordern**. Achten Sie darauf, die Domäne zu überprüfen, und vergewissern Sie sich, dass der Status „Aktiv“ lautet.

    ![Konfigurieren der einmaligen Anmeldung][53]

6. Klicken Sie auf der Seite Ihrer Domänenlösungen auf **Identitätsanbieter**.
  
    ![Konfigurieren der einmaligen Anmeldung][54]

7. Klicken Sie unter dem Abschnitt **Identitätsanbieter** auf **Identitätsanbieter hinzufügen**. 

    ![Konfigurieren der einmaligen Anmeldung][55]

8. Führen Sie auf der Seite mit den **Identitätsanbietereinstellungen** die folgenden Schritte aus:

    ![Konfigurieren der einmaligen Anmeldung][56]

    a. Geben Sie im Textfeld **Name** einen eindeutigen Namen für die Konfiguration ein. Verwenden Sie keine Leerzeichen.

    b. Fügen Sie in das Textfeld **Aussteller des Identitätsanbieters** den Wert vom **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie in das Textfeld **Anmelde-URL des Identitätsanbieters** den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Fügen Sie in das Textfeld **Abmelde-URL des Identitätsanbieters** den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    e. Wählen Sie **Authentifizierungsanforderung signieren** aus.

    f. Wählen Sie für **Send AuthN request by** (Authentifizierungsanforderung senden per) die Option **POST** aus.

    g. Wählen Sie für **Send logout request by** (Abmeldeanforderung senden per) die Option **GET** aus.

    h. Klicken Sie im Abschnitt **Benutzerdefinierte Attributzuordnung** auf **Neue Zuordnung hinzufügen**.

    ![Konfigurieren der einmaligen Anmeldung][62]

    i. Wählen Sie das Feld aus, das Sie dem Azure AD-Anspruch zuordnen möchten. In diesem Beispiel wird der Anspruch **emailaddress** mit dem Wert **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** zugeordnet. Dies ist der Standardanspruchsname aus Azure AD für den E-Mail-Anspruch. Klicken Sie dann auf **Speichern**.

    ![Konfigurieren der einmaligen Anmeldung][57]

    > [!NOTE]
    > Verwenden Sie den entsprechenden **Benutzerbezeichner** , um den Benutzer aus Azure AD der DocuSign-Benutzerzuordnung zuzuordnen. Wählen Sie das richtige Feld, und geben Sie den entsprechenden Wert basierend auf den Einstellungen Ihrer Organisation ein.

    j. Klicken Sie im Abschnitt **Zertifikat des Identitätsanbieters** auf **Zertifikat hinzufügen**. Laden Sie dann das aus dem Azure AD-Portal heruntergeladene Zertifikat hoch, und klicken Sie auf **Speichern**.

    ![Konfigurieren der einmaligen Anmeldung][58]

    k. Klicken Sie im Abschnitt **Identitätsanbieter** auf **Aktionen**, und klicken Sie dann auf **Endpunkte**.

    ![Konfigurieren der einmaligen Anmeldung][59]

    l. Führen Sie im **DocuSign-Verwaltungsportal** im Abschnitt **SAML 2.0-Endpunkte anzeigen** die folgenden Schritte aus:

    ![Konfigurieren der einmaligen Anmeldung][60]

    * Kopieren Sie die **Aussteller-URL des Dienstanbieters**, und fügen Sie die URL dann im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Bezeichner** ein.

    * Kopieren Sie die **Service Provider Login URL** (Anmelde-URL des Dienstanbieters), und fügen Sie sie dann im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Anmelde-URL** ein.

    * Klicken Sie unten auf der Seite auf **Schließen**

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** den Namen brittasimon@yourcompanydomain.extension ein. Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf DocuSign gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **DocuSign**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **DocuSign** aus.

    ![DocuSign-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-docusign-test-user"></a>Erstellen eines DocuSign-Testbenutzers

In diesem Abschnitt wird eine Benutzerin mit dem Namen „Britta Simon“ in DocuSign erstellt. DocuSign unterstützt die Just-in-Time-Benutzerbereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in DocuSign vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

>[!Note]
>Setzen Sie sich mit dem[Supportteam von DocuSign](https://support.docusign.com/) in Verbindung, wenn Sie einen Benutzer manuell erstellen müssen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „DocuSign“ klicken, sollten Sie automatisch bei Ihrer DocuSign-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
