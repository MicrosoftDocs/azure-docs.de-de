---
title: 'Tutorial: Azure Active Directory-Integration in „Sauce Labs - Mobile and Web Testing“ | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und „Sauce Labs - Mobile and Web Testing“ konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 087e2d7f2db8f27378b54675095e97256d6aae9b
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895106"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Tutorial: Azure Active Directory-Integration in „Sauce Labs - Mobile and Web Testing“

In diesem Tutorial erfahren Sie, wie Sie „Sauce Labs - Mobile and Web Testing“ in Azure Active Directory (Azure AD) integrieren.
Die Integration von „Sauce Labs - Mobile and Web Testing“ in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer auf „Sauce Labs - Mobile and Web Testing“ zugreifen kann.
* Sie können Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei „Sauce Labs - Mobile and Web Testing“ anzumelden (Single Sign-On, SSO; einmaliges Anmelden).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in „Sauce Labs - Mobile and Web Testing“ zu konfigurieren, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Sauce Labs-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* „Sauce Labs - Mobile and Web Testing“ unterstützt **IDP** -initiiertes einmaliges Anmelden.
* „Sauce Labs - Mobile and Web Testing“ unterstützt die **Just-in-Time** -Benutzerbereitstellung.

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Hinzufügen von „Sauce Labs - Mobile and Web Testing“ aus dem Katalog

Zum Konfigurieren der Integration von „Sauce Labs - Mobile and Web Testing“ in Azure AD müssen Sie „Sauce Labs - Mobile and Web Testing“ aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um „Sauce Labs - Mobile and Web Testing“ aus dem Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie in das Suchfeld **Sauce Labs - Mobile and Web Testing** ein, wählen Sie im Ergebnisbereich **Sauce Labs - Mobile and Web Testing** aus, und klicken Sie auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

    ![„Sauce Labs - Mobile and Web Testing“ in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei „Sauce Labs - Mobile and Web Testing“ mithilfe eines Testbenutzers namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in „Sauce Labs - Mobile and Web Testing“ eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei „Sauce Labs - Mobile and Web Testing“ müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für „Sauce Labs - Mobile and Web Testing“](#configure-sauce-labs---mobile-and-web-testing-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Sauce Labs-Testbenutzers](#create-sauce-labs---mobile-and-web-testing-test-user)** , um eine Entsprechung von Britta Simon in „Sauce Labs - Mobile and Web Testing“ zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei „Sauce Labs - Mobile and Web Testing“ die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Sauce Labs - Mobile and Web Testing** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Im Abschnitt **Grundlegende SAML-Konfiguration** muss der Benutzer keine Schritte ausführen, weil die App bereits in Azure integriert ist.

    ![SSO-Informationen zur Domäne und zu den URLs für „Sauce Labs - Mobile and Web Testing“](common/preintegrated.png)

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML** -Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **Sauce Labs - Mobile and Web Testing einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-sauce-labs---mobile-and-web-testing-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für „Sauce Labs - Mobile and Web Testing“

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Sauce Labs-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf das **Benutzersymbol** , und wählen Sie die Registerkarte **Team Management** (Teamverwaltung) aus.

    ![Screenshot, auf dem das Benutzersymbol und das Dropdownmenü „Team Management“ (Teamverwaltung) ausgewählt sind](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

3. Geben Sie in das Textfeld Ihren **Domänennamen** ein.

    ![Screenshot: Beispieldomänenname im Textfeld](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

4. Klicken Sie auf die Registerkarte **Configure** (Konfigurieren).

    ![Screenshot: Registerkarte „Configure“ (Konfigurieren), die unter „Single Sign On is Enabled“ (Einmaliges Anmelden ist aktiviert.) ausgewählt ist](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

5. Führen Sie im Abschnitt **Configure Single Sign On** (Einmaliges Anmelden konfigurieren) die folgenden Schritte aus:

    ![Einmaliges Anmelden konfigurieren](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. Klicken Sie auf **Browse** (Durchsuchen), und laden Sie die aus Azure AD heruntergeladene Metadatendatei hoch.

    b. Aktivieren Sie das Kontrollkästchen **ALLOW JUST-IN-TIME PROVISIONING** (Just-in-Time-Bereitstellung zulassen).

    c. Klicken Sie auf **Speichern**.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** , **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** den Namen `brittasimon@yourcompanydomain.extension` ein.  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie Zugriff auf „Sauce Labs - Mobile and Web Testing“ gewähren.

1. Wählen Sie im Azure-Portal die Option **Unternehmensanwendungen** aus, und wählen Sie dann **Alle Anwendungen** und **Sauce Labs - Mobile and Web Testing** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Sauce Labs - Mobile and Web Testing** aus.

    ![Link für „Sauce Labs - Mobile and Web Testing“ in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-sauce-labs---mobile-and-web-testing-test-user"></a>Erstellen eines Sauce Labs-Testbenutzers

In diesem Abschnitt wird ein Benutzer mit dem Namen Britta Simon in „Sauce Labs - Mobile and Web Testing“ erstellt. „Sauce Labs - Mobile and Web Testing“ unterstützt die Just-In-Time-Benutzerbereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in „Sauce Labs - Mobile and Web Testing“ vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

> [!Note]
> Wenn Sie einen Benutzer manuell erstellen möchten, wenden Sie sich an das [Sauce Labs-Supportteam](mailto:support@saucelabs.com).

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel für „Sauce Labs - Mobile and Web Testing“ klicken, sollten Sie automatisch bei der Sauce Labs-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)