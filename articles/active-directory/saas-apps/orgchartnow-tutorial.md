---
title: 'Tutorial: Azure Active Directory-Integration mit OrgChart Now | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und OrgChart Now konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: fc6b90be8e951c0e30c99d0bb16f85e49cf991f2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92522324"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Tutorial: Azure Active Directory-Integration mit OrgChart Now

In diesem Tutorial erfahren Sie, wie Sie OrgChart Now in Azure Active Directory (Azure AD) integrieren.
Die Integration von OrgChart Now in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf OrgChart Now hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei OrgChart Now anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit OrgChart Now konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* OrgChart Now-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* OrgChart Now unterstützt **SP** - und **IDP** -initiiertes einmaliges Anmelden.

## <a name="adding-orgchart-now-from-the-gallery"></a>Hinzufügen von OrgChart Now über den Katalog

Zum Konfigurieren der Integration von OrgChart Now in Azure AD müssen Sie OrgChart Now aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um OrgChart Now aus dem Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory** .

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **OrgChart Now** ein, wählen Sie im Ergebnisbereich **OrgChart Now** aus, und klicken Sie anschließend auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

     ![OrgChart Now in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei OrgChart Now basierend auf einem Testbenutzer mit dem Namen **Britta Simon** .
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in OrgChart Now eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei OrgChart Now müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für OrgChart Now](#configure-orgchart-now-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines OrgChart Now-Testbenutzers](#create-orgchart-now-test-user)** , um eine Entsprechung von Britta Simon in OrgChart Now zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in OrgChart Now die folgenden Schritte aus:

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **OrgChart Now** auf **Einmaliges Anmelden** .

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für OrgChart Now](common/idp-identifier.png)

    Geben Sie im Textfeld **Bezeichner** eine URL ein: `https://sso2.orgchartnow.com`.

5. Klicken Sie auf **Zusätzliche URLs festlegen** , und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    ![image](common/both-preintegrated-signon.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`

    > [!NOTE]
    > `<YourEntityID>` ist der **Azure AD-Bezeichner** , der im Abschnitt **OrgChart Now einrichten** kopiert wurde (Beschreibung weiter unten in diesem Tutorial).

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML** -Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

7. Kopieren Sie im Abschnitt **OrgChart Now einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-orgchart-now-single-sign-on"></a>Konfigurieren des einmaliges Anmeldens für OrgChart Now

Zum Konfigurieren des einmaligen Anmeldens aufseiten von **OrgChart Now** müssen Sie die heruntergeladene **Verbundmetadaten-XML** und die kopierten URLs aus dem Azure-Portal an das [Supportteam von OrgChart Now](mailto:ocnsupport@officeworksoftware.com) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** , **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** **brittasimon@yourcompanydomain.extension** ein.  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf OrgChart Now gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen** , **Alle Anwendungen** und **OrgChart Now** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **OrgChart Now** aus.

    ![OrgChart Now-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen** .

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen** .

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

### <a name="create-orgchart-now-test-user"></a>Erstellen eines OrgChart Now-Testbenutzers

Damit sich Azure AD-Benutzer an OrgChart Now anmelden können, müssen sie in OrgChart Now bereitgestellt werden. 

1. OrgChart Now unterstützt die Just-In-Time-Bereitstellung, die standardmäßig aktiviert ist. Wenn noch kein Benutzer vorhanden ist, wird beim Zugreifen auf OrgChart Now ein neuer Benutzer erstellt. Mit dem Feature für die Just-in-Time-Bereitstellung wird nur dann ein Benutzer mit **Lesezugriff** erstellt, wenn eine SSO-Anforderung von einem bekannten IDP stammt und die E-Mail-Adresse in der Benutzerliste der SAML-Assertion nicht gefunden wird. Für dieses Feature zur automatischen Bereitstellung müssen Sie in OrgChart Now eine Zugriffsgruppe mit dem Titel **General** (Allgemein) erstellen. Führen Sie die unten angegebenen Schritte aus, um eine Zugriffsgruppe zu erstellen:

    a. Navigieren Sie zur Option **Gruppen verwalten** , nachdem Sie oben rechts auf der Benutzeroberfläche auf das **Zahnrad** geklickt haben.

    ![OrgChart Now-Gruppen](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Wählen Sie das Symbol **Hinzufügen** , und geben Sie der Gruppe den Namen **General** . Klicken Sie anschließend auf **OK** . 

    ![OrgChart Now – Hinzufügen](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Wählen Sie die Ordner aus, für die Benutzer allgemeinen oder schreibgeschützten Zugriff erhalten sollen:

    ![OrgChart Now-Ordner](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. Sperren Sie die Ordner mit **Lock** (Sperren), damit nur Administratorbenutzer diese ändern können. Wählen Sie anschließend **OK** .

    ![OrgChart Now – Sperren](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Wenn Sie Benutzer vom Typ **Admin** und **Read/Write** (Lesen/Schreiben) erstellen möchten, müssen Sie einen Benutzer manuell erstellen, um Zugriff auf seine Berechtigungsstufe per SSO zu erhalten. Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:

    a. Melden Sie sich als Sicherheitsadministrator an OrgChart Now an.

    b.  Klicken Sie oben rechts auf **Settings** (Einstellungen), und navigieren Sie anschließend zu **Manage Users** (Benutzer verwalten).

    ![OrgChart Now-Einstellungen](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Klicken Sie auf **Add** (Hinzufügen), und führen Sie die folgenden Schritte aus:

    ![OrgChart Now – Verwalten](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * Geben Sie im Textfeld **User ID** (Benutzer-ID) die Benutzer-ID im Format **brittasimon\@contoso.com** ein.

    * Geben Sie im Textfeld **Email Address** (E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein, z. B. **brittasimon\@contoso.com** .

    * Klicken Sie auf **Hinzufügen** .

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „OrgChart Now“ klicken, sollten Sie automatisch bei der OrgChart Now-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)