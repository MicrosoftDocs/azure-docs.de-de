---
title: 'Tutorial: Azure Active Directory-Integration von ScaleX Enterprise | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und ScaleX Enterprise konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c2379a8d-a659-45f1-87db-9ba156d83183
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64edf2aa47211c1d2a598417a7b2edc00f260075
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60321166"
---
# <a name="tutorial-azure-active-directory-integration-with-scalex-enterprise"></a>Tutorial: Azure Active Directory-Integration von ScaleX Enterprise

In diesem Tutorial erfahren Sie, wie Sie ScaleX Enterprise in Azure Active Directory (Azure AD) integrieren.

Die Integration von ScaleX Enterprise in Azure AD bietet die folgenden Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf ScaleX Enterprise hat.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei ScaleX Enterprise anzumelden (einmaliges Anmelden, Single Sign-On).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im Azure-Portal.

Weitere Informationen zum Integrieren von SaaS-Apps in Azure AD finden Sie unter Was bedeuten Anwendungszugriff und einmaliges Anmelden mit [Azure Active Directory](../manage-apps/what-is-single-sign-on.md)?

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit ScaleX Enterprise konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein ScaleX Enterprise-Abonnement, das für das einmalige Anmelden aktiviert ist

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/)eine einmonatige Testversion anfordern.

## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptbestandteilen:

1. Hinzufügen von ScaleX Enterprise aus dem Katalog
1. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD

## <a name="adding-scalex-enterprise-from-the-gallery"></a>Hinzufügen von ScaleX Enterprise aus dem Katalog
Zum Konfigurieren der Integration von ScaleX Enterprise in Azure AD müssen Sie ScaleX Enterprise aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um ScaleX Enterprise aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Active Directory][1]

1. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![ANWENDUNGEN][2]
    
1. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Hinzufügen**.

    ![ANWENDUNGEN][3]

1. Geben Sie im Suchfeld den Suchbegriff **ScaleX Enterprise** ein.

    ![Erstellen eines Azure AD-Testbenutzers](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_search.png)

1. Wählen Sie im Ergebnisbereich die Option **ScaleX Enterprise** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Erstellen eines Azure AD-Testbenutzers](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei ScaleX Enterprise basierend auf einer Testbenutzerin mit dem Namen Britta Simon.

Damit das einmalige Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in ScaleX Enterprise als Gegenstück zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in ScaleX Enterprise muss eine Linkbeziehung eingerichtet werden.

Diese Linkbeziehung wird hergestellt, indem Sie den Wert für den **Benutzernamen** in Azure AD als Wert für den **Benutzernamen** in ScaleX Enterprise zuweisen.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei ScaleX Enterprise müssen Sie die folgenden Bausteine ausführen:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
1. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit der Testbenutzerin Britta Simon zu testen.
1. **[Erstellen eines ScaleX Enterprise-Testbenutzers](#creating-a-scalex-enterprise-test-user)** , um eine Entsprechung von Britta Simon in ScaleX Enterprise zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Testing Single Sign-On](#testing-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens von Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal und konfigurieren das einmalige Anmelden in Ihrer ScaleX Enterprise-Anwendung.

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in ScaleX Enterprise die folgenden Schritte aus:**

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **ScaleX Enterprise** auf **Einmaliges Anmelden**.

    ![Configure single sign-on][4]

1. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um das einmalige Anmelden zu aktivieren.
 
    ![Configure single sign-on](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_samlbase.png)

1. Führen Sie im Abschnitt **Domäne und URLs für ScaleX Enterprise** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten Modus** konfigurieren möchten:

    ![Configure single sign-on](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_url1.png)

    a. Geben Sie im Textfeld **Bezeichner** einen Wert nach folgendem Muster ein: `https://platform.rescale.com/saml2/<company id>/`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL nach folgendem Muster ein: `https://platform.rescale.com/saml2/<company id>/acs/`

1. Aktivieren Sie **Erweiterte URL-Einstellungen anzeigen**, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    ![Configure single sign-on](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_url2.png)

    Geben Sie im Textfeld **Anmelde-URL** den Wert im folgenden Format ein: `https://platform.rescale.com/saml2/<company id>/sso/`.
     
    > [!NOTE] 
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch den tatsächlichen Bezeichner, die tatsächliche Antwort-URL bzw. die tatsächliche Anmelde-URL. Wenden Sie sich an das [Supportteam von ScaleX Enterprise Client](https://info.rescale.com/contact_sales), um diese Werte zu erhalten. 

1. Die ScaleX-Anwendung erwartet die SAML-Assertions in einem bestimmten Format. Daher müssen Sie die Zuordnungen benutzerdefinierter Attribute in Ihrer SAML-Tokenattributkonfiguration bearbeiten. Klicken Sie auf das Kontrollkästchen **Alle weiteren Benutzerattribute anzeigen und bearbeiten**, um die Einstellungen für benutzerdefinierte Attribute zu öffnen.

    ![Configure single sign-on](./media/scalexenterprise-tutorial/scalex_attributes.png)
    
    a. Klicken Sie mit der rechten Maustaste auf das Attribut **name**, und klicken Sie auf „Löschen“.

    ![Configure single sign-on](./media/scalexenterprise-tutorial/delete_attribute_name.png)

    b. Klicken Sie auf das Attribut **emailaddress**, um das Fenster „Attribut bearbeiten“ zu öffnen. Ändern Sie den Wert von **user.mail** zu **user.userprincipalname**, und klicken Sie auf „OK“.

    ![Configure single sign-on](./media/scalexenterprise-tutorial/edit_email_attribute.png) 
    
1. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Zertifikat (Base64)** , und speichern Sie die Zertifikatdatei auf Ihrem Computer.

    ![Configure single sign-on](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_certificate.png) 

1. Klicken Sie auf die Schaltfläche **Save** .

    ![Configure single sign-on](./media/scalexenterprise-tutorial/tutorial_general_400.png)
    
1. Klicken Sie im Abschnitt **ScaleX Enterprise-Konfiguration** auf **ScaleX Enterprise konfigurieren**, um das Fenster **Anmeldung konfigurieren** zu öffnen. Kopieren Sie die **SAML-Entitäts-ID** und die **URL für den SAML-SSO-Dienst** aus dem Abschnitt **Kurzübersicht**.

    ![Configure single sign-on](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_configure.png) 

1. Um das einmalige Anmelden auf der Seite von **ScaleX Enterprise** zu konfigurieren, melden Sie sich bei der ScaleX Enterprise-Unternehmenswebsite als Administrator an.

1. Klicken Sie auf das Menü oben rechts, und wählen Sie **Contoso Administration** aus.

    > [!NOTE] 
    > „Contoso“ ist nur ein Beispiel. Auf der Seite steht der tatsächliche Name Ihres Unternehmens. 

    ![Configure single sign-on](./media/scalexenterprise-tutorial/Test_Admin.png) 

1. Wählen Sie aus dem oberen Menü die Option **Integrations** aus, und wählen Sie dann **Single Sign-On** aus.

    ![Configure single sign-on](./media/scalexenterprise-tutorial/admin_sso.png) 

1. Füllen Sie das Formular wie folgt aus:

    ![Configure single sign-on](./media/scalexenterprise-tutorial/scalex_admin_save.png) 
    
    a. Wählen Sie **Create any user who can authenticate with SSO** aus.

    b. **Service Provider saml** (Dienstanbieter-SAML): Fügen Sie den Wert ***urn:oasis:names:tc:SAML:2.0:nameid-format:persistent*** ein.

    c. **Name of Identity Provider email field in ACS response** (E-Mail-Feld für den Namen des Identitätsanbieters in der ACS-Antwort): Fügen Sie den Wert `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` ein.

    d. **Identity Provider EntityDescriptor Entity ID** (EntityDescriptor-Entitäts-ID des Identitätsanbieters): Fügen Sie die aus dem Azure-Portal kopierte **SAML-Entitäts-ID** ein.

    e. **Identity Provider SingleSignOnService URL** (SingleSignOnService-URL des Identitätsanbieters): Fügen Sie die **URL für den SAML-SSO-Dienst** aus dem Azure-Portal ein.

    f. **Identity Provider public X509 certificate** (Öffentliches X.509-Zertifikat des Identitätsanbieters): Öffnen Sie das aus Azure heruntergeladene X.509-Zertifikat im Editor, und fügen Sie den Inhalt in dieses Feld ein. Stellen Sie sicher, dass das Zertifikat keine Zeilenumbrüche enthält.
    
    g. Aktivieren Sie die folgenden Kontrollkästchen: **Enabled**, „Encrypt NameID“ und „Sign AuthnRequests“.

    h. Klicken Sie auf **Update SSO Settings**, um die Einstellungen zu speichern.

> [!TIP]
> Während der Einrichtung der App können Sie im [Azure-Portal](https://portal.azure.com) nun eine Kurzfassung dieser Anweisungen lesen.  Nachdem Sie diese App aus dem Abschnitt **Active Directory > Unternehmensanwendungen** heruntergeladen haben, klicken Sie einfach auf die Registerkarte **Einmaliges Anmelden**, und rufen Sie die eingebettete Dokumentation über den Abschnitt **Konfiguration** um unteren Rand der Registerkarte auf. Weitere Informationen zur eingebetteten Dokumentation finden Sie hier: [Dokumentation zu eingebettetem Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

![Azure AD-Benutzer erstellen][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Portals** auf das Symbol für **Azure Active Directory**.

    ![Erstellen eines Azure AD-Testbenutzers](./media/scalexenterprise-tutorial/create_aaduser_01.png) 

1. Wechseln Sie zu **Benutzer und Gruppen**, und klicken Sie auf **Alle Benutzer**, um die Liste der Benutzer anzuzeigen.
    
    ![Erstellen eines Azure AD-Testbenutzers](./media/scalexenterprise-tutorial/create_aaduser_02.png) 

1. Klicken Sie oben im Dialogfeld auf **Hinzufügen**, um das Dialogfeld **Benutzer** zu öffnen.
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/scalexenterprise-tutorial/create_aaduser_03.png) 

1. Führen Sie auf der Dialogfeldseite **Benutzer** die folgenden Schritte aus:
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/scalexenterprise-tutorial/create_aaduser_04.png) 

    a. Geben Sie in das Textfeld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie in das Textfeld **Benutzername** die **E-Mail-Adresse** von Britta Simon ein.

    c. Wählen Sie **Kennwort anzeigen** aus, und notieren Sie sich den Wert des **Kennworts**.

    d. Klicken Sie auf **Create**.
 
### <a name="creating-a-scalex-enterprise-test-user"></a>Erstellen eines ScaleX Enterprise-Testbenutzers

Damit sich Azure AD-Benutzer bei ScaleX Enterprise anmelden können, müssen sie in ScaleX Enterprise bereitgestellt werden. Bei ScaleX Enterprise erfolgt die Bereitstellung automatisch, sodass keine manuellen Schritte erforderlich sind. Alle Benutzer, die sich erfolgreich mit Anmeldeinformationen für das einmalige Anmelden authentifizieren können, werden automatisch auf der Seite von ScaleX bereitgestellt.

### <a name="assigning-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf ScaleX Enterprise gewähren.

![Benutzer zuweisen][200] 

**Um Britta Simon zu ScaleX Enterprise zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Öffnen Sie im Azure-Portal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen**, und klicken Sie auf **Alle Anwendungen**.

    ![Benutzer zuweisen][201] 

1. Wählen Sie in der Anwendungsliste **ScaleX Enterprise**aus.

    ![Configure single sign-on](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_app.png) 

1. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Benutzer zuweisen][202] 

1. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Benutzer zuweisen][203]

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

1. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.

### <a name="testing-single-sign-on"></a>Testen der einmaligen Anmeldung

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Klicken Sie im Zugriffsbereich auf die Kachel „ScaleX Enterprise“ – Sie werden automatisch bei Ihrer ScaleX Enterprise-Anwendung angemeldet. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/scalexenterprise-tutorial/tutorial_general_01.png
[2]: ./media/scalexenterprise-tutorial/tutorial_general_02.png
[3]: ./media/scalexenterprise-tutorial/tutorial_general_03.png
[4]: ./media/scalexenterprise-tutorial/tutorial_general_04.png

[100]: ./media/scalexenterprise-tutorial/tutorial_general_100.png

[200]: ./media/scalexenterprise-tutorial/tutorial_general_200.png
[201]: ./media/scalexenterprise-tutorial/tutorial_general_201.png
[202]: ./media/scalexenterprise-tutorial/tutorial_general_202.png
[203]: ./media/scalexenterprise-tutorial/tutorial_general_203.png

