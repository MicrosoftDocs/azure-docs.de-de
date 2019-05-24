---
title: 'Tutorial: Azure Active Directory-Integration mit Adobe Creative Cloud | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Adobe Creative Cloud konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35bd52904ab081e41cb43a346288234c18a7f43b
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65899086"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Tutorial: Azure Active Directory-Integration mit Adobe Creative Cloud

In diesem Tutorial erfahren Sie, wie Sie Adobe Creative Cloud in Azure Active Directory (Azure AD) integrieren.
Die Integration von Adobe Creative Cloud in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer auf Adobe Creative Cloud Zugriff besitzt.
* Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Adobe Creative Cloud anzumelden (einmaliges Anmelden).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Adobe Creative Cloud konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Adobe Creative Cloud-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Adobe Creative Cloud unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Hinzufügen von Adobe Creative Cloud aus dem Katalog

Zum Konfigurieren der Integration von Adobe Creative Cloud in Azure AD müssen Sie Adobe Creative Cloud aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Adobe Creative Cloud aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Adobe Creative Cloud** ein, wählen Sie im Ergebnisbereich **Adobe Creative Cloud** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Adobe Creative Cloud in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie anhand eines Testbenutzers namens **Britta Simon** das einmalige Anmelden von Azure AD bei Adobe Creative Cloud.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Adobe Creative Cloud eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Adobe Creative Cloud müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Adobe Creative Cloud](#configure-adobe-creative-cloud-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Adobe Creative Cloud-Testbenutzers](#create-adobe-creative-cloud-test-user)**, um eine Entsprechung von Britta Simon in Adobe Creative Cloud zu erhalten, die mit der Darstellung des Benutzers in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in Adobe Creative Cloud die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Adobe Creative Cloud** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![Informationen zu einmaligem Anmelden für Adobe Creative Cloud-Domäne und -URLs](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** den Wert wie folgt ein: `https://adobe.com`.

    b. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > Der ID-Wert ist nicht der tatsächliche Wert. Aktualisieren Sie den Wert mit dem tatsächlichen Bezeichner. Diesen Wert erhalten Sie vom [Supportteam für den Adobe Creative Cloud-Client](https://www.adobe.com/au/creativecloud/business/teams/plans.html). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Die Adobe Creative Cloud-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Sie können die Werte dieser Attribute im Abschnitt **Benutzerattribute** auf der Anwendungsintegrationsseite verwalten. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![image](common/edit-attribute.png)

6. Konfigurieren Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** das SAML-Tokenattribut wie in der obigen Abbildung gezeigt, und führen Sie die folgenden Schritte aus:

    | NAME | Quellattribut|
    |----- | --------- |
    | FirstName | user.givenname |
    | Nachname | user.surname |
    | E-Mail | user.mail

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    f. Klicken Sie auf **OK**.

    g. Klicken Sie auf **Speichern**.

    > [!NOTE]
    > Benutzer benötigen eine gültige Office 365-ExO-Lizenz, damit der E-Mail-Anspruchswert in der SAML-Antwort aufgefüllt wird.

7. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

8. Kopieren Sie im Abschnitt **Adobe Creative Cloud einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-adobe-creative-cloud-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Adobe Creative Cloud

1. Melden Sie sich in einem anderen Browserfenster bei der [Adobe-Administratorkonsole](https://adminconsole.adobe.com) als Administrator an.

2. Wechseln Sie auf der oberen Navigationsleiste zu **Settings** (Einstellungen), und wählen Sie dann **Identity** (Identität). Die Domänenliste wird geöffnet. Klicken Sie für Ihre Domäne auf den Link **Configure** (Konfigurieren). Führen Sie im Abschnitt **SSO-Konfiguration erforderlich** die folgenden Schritte aus. Weitere Informationen finden Sie unter [Setup a domain](https://helpx.adobe.com/enterprise/using/set-up-domain.html) (Einrichten einer Domäne).

    ![Einstellungen](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "Einstellungen")

    a. Klicken Sie auf **Durchsuchen**, um das aus Azure AD heruntergeladene Zertifikat in das **IDP-Zertifikat** hochzuladen.

    b. Fügen Sie im Textfeld **IDP-Aussteller** den Wert der **SAML-Entitäts-ID** ein, den Sie aus dem Abschnitt **Anmelden konfigurieren** im Azure-Portal kopiert haben.

    c. Fügen Sie im Textfeld **IDP-Anmelde-URL** den Wert der **SAML-SSO-Dienst-URL** ein, den Sie aus dem Abschnitt **Anmelden konfigurieren** im Azure-Portal kopiert haben.

    d. Wählen Sie **HTTP-Umleitung** als **IDP-Bindung** aus.

    e. Wählen Sie **E-Mail-Adresse** als **Einstellung für die Benutzeranmeldung** aus.

    f. Klicken Sie auf die Schaltfläche **Save** .

3. Im Dashboard wird jetzt die XML-Datei **Metadaten herunterladen** angezeigt. Sie enthält die EntityDescriptor-URL und die AssertionConsumerService-URL von Adobe. Öffnen Sie die Datei, und konfigurieren sie diese in der Azure AD-Anwendung.

    ![Einmaliges Anmelden auf App-Seite konfigurieren](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Verwenden Sie den von Adobe bereitgestellten EntityDescriptor-Wert für **Bezeichner** im Dialogfeld **App-Einstellungen konfigurieren**.

    b. Verwenden Sie den von Adobe bereitgestellten AssertionConsumerService-Wert für **Antwort-URL** im Dialogfeld **App-Einstellungen konfigurieren**.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** Folgendes ein: **brittasimon\@ihreunternehmensdomäne.erweiterung**.  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon das einmalige Anmelden bei Azure, indem Sie Zugriff auf Adobe Creative Cloud gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**, **Alle Anwendungen** und dann **Adobe Creative Cloud** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Adobe Creative Cloud** aus.

    ![Der Adobe Creative Cloud-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-adobe-creative-cloud-test-user"></a>Erstellen eines Adobe Creative Cloud-Testbenutzers

Damit sich Azure AD-Benutzer bei Adobe Creative Cloud anmelden können, müssen sie in Adobe Creative Cloud bereitgestellt werden. Im Fall von Adobe Creative Cloud ist die Bereitstellung eine manuelle Aufgabe.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>So stellen Sie Benutzerkonten bereit:

1. Melden Sie sich bei der [Adobe-Administratorkonsole](https://adminconsole.adobe.com) als Administrator an.

2. Fügen Sie den Benutzer in der Adobe-Konsole als „Federated ID“ (Verbund-ID) hinzu, und weisen Sie diesem ein Produktprofil zu. Ausführliche Informationen zum Hinzufügen von Benutzern finden Sie unter [Add users in Adobe Admin Console](https://helpx.adobe.com/enterprise/using/users.html#Addusers) (Hinzufügen von Benutzern in der Adobe-Administratorkonsole). 

3. Geben Sie nun Ihre E-Mail-Adresse und Ihren UPN in das Adobe-Anmeldeformular ein, und drücken Sie die TAB-TASTE. Sie sollten zurück zum Azure AD-Verbund gelangen:
   * Webzugriff: www\.adobe.com > sign-in
   * Im Desktop-App-Hilfsprogramm > sign-in
   * In der Anwendung > help > sign-in

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Adobe Creative Cloud“ klicken, sollten Sie automatisch bei Ihrer Adobe Creative Cloud-Anwendung angemeldet werden. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
  
- [Set up a domain (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html) (Einrichten einer Domäne)
  
- [Configure Azure for use with Adobe SSO (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html) (Konfigurieren von Azure für die Verwendung mit Adobe SSO)
