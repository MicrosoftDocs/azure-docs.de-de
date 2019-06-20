---
title: 'Tutorial: Azure Active Directory-Integration mit Kanbanize | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Kanbanize konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b436d2f6-bfa5-43fd-a8f9-d2144dc25669
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22c136225e5a8526afd482e5ef8400198947422f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60264236"
---
# <a name="tutorial-azure-active-directory-integration-with-kanbanize"></a>Tutorial: Azure Active Directory-Integration mit Kanbanize

In diesem Tutorial erfahren Sie, wie Sie Kanbanize in Azure Active Directory (Azure AD) integrieren.

Die Integration von Kanbanize in Azure AD bietet die folgenden Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf Kanbanize hat.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Kanbanize anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren der Azure AD-Integration in Kanbanize benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein für das einmalige Anmelden aktiviertes Kanbanize-Abonnement

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie eine [einmonatige Testversion anfordern](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptbestandteilen:

1. Hinzufügen von Kanbanize aus dem Katalog
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD

## <a name="adding-kanbanize-from-the-gallery"></a>Hinzufügen von Kanbanize aus dem Katalog
Zum Konfigurieren der Integration von Kanbanize in Azure AD müssen Sie Kanbanize aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Kanbanize aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Schaltfläche „Azure Active Directory“][1]

2. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“][2]
    
3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“][3]

4. Geben Sie im Suchfeld **Kanbanize** ein, klicken Sie im Ergebnisbereich auf **Kanbanize** und anschließend auf **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Kanbanize in der Ergebnisliste](./media/kanbanize-tutorial/tutorial_kanbanize_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden mit Azure AD bei Kanbanize mithilfe einer Testbenutzerin namens Britta Simon.

Damit das einmalige Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in Kanbanize als Gegenstück für einen Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Kanbanize muss eine Linkbeziehung eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Kanbanize müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
3. **[Erstellen eines Kanbanize-Testbenutzers](#create-a-kanbanize-test-user)** , um eine Entsprechung von Britta Simon in Kanbanize zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal und konfigurieren das einmalige Anmelden in Ihrer Kanbanize-Anwendung.

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in Kanbanize die folgenden Schritte aus:**

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Kanbanize** auf **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden][4]

2. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um einmaliges Anmelden zu aktivieren.
 
    ![Dialogfeld „Einmaliges Anmelden“](./media/kanbanize-tutorial/tutorial_kanbanize_samlbase.png)

3. Führen Sie im Abschnitt **Domäne und URLs für Kanbanize** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_url.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL nach folgendem Muster ein: `https://<subdomain>.kanbanize.com/`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL nach folgendem Muster ein: `https://<subdomain>.kanbanize.com/saml/acs`

    c. Aktivieren Sie **Erweiterte URL-Einstellungen anzeigen**.

    d.  Geben Sie im Textfeld **Relayzustand** eine URL ein: `/ctrl_login/saml_login`

    e. Wenn Sie die Anwendung im **SP**-initiierten Modus konfigurieren möchten, geben Sie im Textfeld **Anmelde-URL** eine URL unter Verwendung des folgenden Musters ein: `https://<subdomain>.kanbanize.com`
     
    > [!NOTE] 
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch den tatsächlichen Bezeichner, die Antwort-URL und die Anmelde-URL. Wenden Sie sich an das [Kundensupportteam von Kanbanize](mailto:support@ms.kanbanize.com), um diese Werte zu erhalten. 

5. Die Kanbanize-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt ein Beispiel für diese Attributzuordnungen: Der Standardwert von **Benutzer-ID** lautet **user.userprincipalname**, aber Kanbanize erwartet, dass dieser Wert der E-Mail-Adresse des Benutzers zugeordnet ist. Hierfür können Sie das **user.mail**-Attribut aus der Liste verwenden oder den entsprechenden Attributwert gemäß der Konfiguration in Ihrer Organisation angeben.
    
    ![Configure single sign-on](./media/kanbanize-tutorial/tutorial_Kanbanize_attribute.png)

6. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Zertifikat (Base64)** , und speichern Sie die Zertifikatdatei auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](./media/kanbanize-tutorial/tutorial_kanbanize_certificate.png) 

7. Klicken Sie auf die Schaltfläche **Save** .

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/kanbanize-tutorial/tutorial_general_400.png)
    
8. Klicken Sie im Abschnitt **Kanbanize-Konfiguration** auf **Kanbanize konfigurieren**, um das Fenster **Anmeldung konfigurieren** zu öffnen. Kopieren Sie die **Abmelde-URL, die SAML-Entitäts-ID und die URL für den SAML-SSO-Dienst** aus dem Abschnitt **Kurzübersicht**.

    ![Kanbanize-Konfiguration](./media/kanbanize-tutorial/tutorial_kanbanize_configure.png)

9. Melden Sie sich in einem anderen Webbrowserfenster als Sicherheitsadministrator bei Kanbanize an. 

10. Klicken Sie rechts oben auf der Seite auf das Logo **Einstellungen**.

    ![Kanbanize-Einstellungen](./media/kanbanize-tutorial/tutorial_kanbanize_set.png)

11. Klicken Sie auf der Seite „Verwaltungsbereich“ auf der linken Seite des Menüs auf **Integrationen**, und aktivieren Sie dann **Einmaliges Anmelden**. 

    ![Kanbanize-Integrationen](./media/kanbanize-tutorial/tutorial_kanbanize_admin.png)

12. Klicken Sie im Abschnitt „Integrationen“ auf **KONFIGURIEREN**, um die **Konfigurationsseite für einmaliges Anmelden** zu öffnen.

    ![Kanbanize.Konfiguration](./media/kanbanize-tutorial/tutorial_kanbanize_config.png)

13. Führen Sie auf der **Konfigurationsseite für einmaliges Anmelden** unter **Konfigurationen** die folgenden Schritte aus:

    ![Kanbanize-Integrationen](./media/kanbanize-tutorial/tutorial_kanbanize_save.png)

    a. Fügen Sie in das Textfeld **IdP-Entitäts-ID** den Wert der **SAML-Entitäts-ID** ein, den Sie aus dem Azure-Portal kopiert haben.

    b. Fügen Sie in das Textfeld **IdP Login URL** (IdP-Anmelde-URL) den Wert der **SAML-Dienst-URL für einmaliges Anmelden** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie in das Textfeld **IdP-Abmeldeendpunkt** den Wert der **Abmelde-ID** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Geben Sie im Textfeld **Attribute name for Email** (Attributname für E-Mail) den Wert `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` ein.

    e. Geben Sie im Textfeld **Attribute name for First Name** (Attributname für Vornamen) den Wert `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` ein.

    f. Geben Sie im Textfeld **Attribute name for Last Name** (Attributname für Nachnamen) den Wert `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` ein. 
    > [!Note]
    > Sie können diese Werte durch Kombination der Werte von Namespace und Name des entsprechenden Attributs aus dem Abschnitt „Benutzerattribute“ im Azure-Portal abrufen.

    g. Öffnen Sie in Editor das Base64-codierte Zertifikat, das Sie aus dem Azure-Portal heruntergeladen haben, kopieren Sie den Inhalt, und fügen Sie ihn anschließend (ohne Anfangs und Endmarker) in das Feld  **Idp X.509 Certificate** (X.509-Zertifikat des IDP) ein.

    h. Aktivieren Sie **Enable login with both SSO and Kanbanize** (Anmeldung mit SSO und Kanbanize aktivieren).
    
    i. Klicken Sie auf **Einstellungen speichern**.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

   ![Erstellen eines Azure AD-Testbenutzers][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Bereich des Azure-Portals auf die Schaltfläche **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](./media/kanbanize-tutorial/create_aaduser_01.png)

2. Navigieren Sie zu **Benutzer und Gruppen**, und klicken Sie dann auf **Alle Benutzer**, um die Liste mit den Benutzern anzuzeigen.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](./media/kanbanize-tutorial/create_aaduser_02.png)

3. Klicken Sie oben im Dialogfeld **Alle Benutzer** auf **Hinzufügen**, um das Dialogfeld **Benutzer** zu öffnen.

    ![Schaltfläche „Hinzufügen“](./media/kanbanize-tutorial/create_aaduser_03.png)

4. Führen Sie im Dialogfeld **Neuer Benutzer** die folgenden Schritte aus:

    ![Dialogfeld „Benutzer“](./media/kanbanize-tutorial/create_aaduser_04.png)

    a. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie im Feld **Benutzername** die E-Mail-Adresse des Benutzers Britta Simon ein.

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.

    d. Klicken Sie auf **Create**.
 
### <a name="create-a-kanbanize-test-user"></a>Erstellen eines Kanbanize-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in Kanbanize. Kanbanize unterstützt die Just-In-Time-Bereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Wenn noch kein Benutzer vorhanden ist, wird beim Zugreifen auf Kanbanize ein neuer Benutzer erstellt.

>[!Note]
>Wenn Sie einen Benutzer manuell erstellen müssen, wenden Sie sich an das  [Kanbanize-Clientsupportteam](mailto:support@ms.kanbanize.com).

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon, das einmalige Anmelden von Azure zu verwenden, indem Sie ihr Zugriff auf Kanbanize gewähren.

![Zuweisen der Benutzerrolle][200] 

**Um Britta Simon Kanbanize zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Öffnen Sie im Azure-Portal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen**, und klicken Sie auf **Alle Anwendungen**.

    ![Benutzer zuweisen][201] 

2. Wählen Sie in der Anwendungsliste **Kanbanize** aus.

    ![Kanbanize-Link in der Anwendungsliste](./media/kanbanize-tutorial/tutorial_kanbanize_app.png)  

3. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Link „Benutzer und Gruppen“][202]

4. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“][203]

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

6. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.
    
### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Kanbanize“ klicken, sollten Sie automatisch bei Ihrer Kanbanize-Anwendung angemeldet werden.
Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kanbanize-tutorial/tutorial_general_01.png
[2]: ./media/kanbanize-tutorial/tutorial_general_02.png
[3]: ./media/kanbanize-tutorial/tutorial_general_03.png
[4]: ./media/kanbanize-tutorial/tutorial_general_04.png

[100]: ./media/kanbanize-tutorial/tutorial_general_100.png

[200]: ./media/kanbanize-tutorial/tutorial_general_200.png
[201]: ./media/kanbanize-tutorial/tutorial_general_201.png
[202]: ./media/kanbanize-tutorial/tutorial_general_202.png
[203]: ./media/kanbanize-tutorial/tutorial_general_203.png

