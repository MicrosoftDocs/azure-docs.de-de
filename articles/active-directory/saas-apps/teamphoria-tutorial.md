---
title: 'Tutorial: Azure Active Directory-Integration mit Teamphoria | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Teamphoria konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 11d0a0a67d1ce5049166cab3d9ca3e4903b6b460
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65866178"
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>Tutorial: Azure Active Directory-Integration mit Teamphoria

In diesem Tutorial erfahren Sie, wie Sie Teamphoria in Azure Active Directory (Azure AD) integrieren.
Die Integration von Teamphoria in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Teamphoria hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Teamphoria anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für die Konfiguration der Azure AD-Integration mit Teamphoria benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Teamphoria-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Teamphoria unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="adding-teamphoria-from-the-gallery"></a>Hinzufügen von Teamphoria aus dem Katalog

Zum Konfigurieren der Integration von Teamphoria in Azure AD müssen Sie Teamphoria aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Teamphoria aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Teamphoria** ein, wählen Sie im Ergebnisbereich **Teamphoria** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Teamphoria in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Teamphoria basierend auf einem Testbenutzer mit dem Namen **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Teamphoria eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Teamphoria müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Teamphoria](#configure-teamphoria-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Teamphoria-Testbenutzers](#create-teamphoria-test-user)**, um in Teamphoria ein Pendant von Britta Simon zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Teamphoria die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Teamphoria** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Teamphoria](common/sp-intiated.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<sub-domain>.teamphoria.com/login`.

    > [!NOTE]
    > Der Wert der Anmelde-URL entspricht nicht dem tatsächlichen Wert. Sie müssen den Wert mit der richtigen Anmelde-URL aktualisieren. Wenden Sie sich an das [Supportteam für Teamphoria-Clients](https://www.teamphoria.com/), um die Anmelde-URL abzurufen. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Teamphoria einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-teamphoria-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Teamphoria

1. Um einmaliges Anmelden auf **Teamphoria**-Seite zu konfigurieren, melden Sie sich als Administrator bei Ihrer Teamphoria-Anwendung an.

1. Wechseln Sie auf der linken Symbolleiste zu **ADMIN SETTINGS** (Administratoreinstellungen), und klicken Sie auf der Registerkarte „Configure“ (Konfigurieren) auf **SINGLE SIGN-ON** (Einmaliges Anmelden), um das SSO-Konfigurationsfenster zu öffnen.

    ![Configure single sign-on](./media/teamphoria-tutorial/admin_sso_configure.png)

1. Klicken Sie in der oberen rechten Ecke auf die Option **ADD NEW IDENTITY PROVIDER** (Neue Identitätsanbieter hinzufügen), um das Formular zum Hinzufügen der Einstellungen für SSO zu öffnen.

    ![Configure single sign-on](./media/teamphoria-tutorial/add_new_identity_provider.png)

1. Geben Sie die Details wie unten beschrieben in den Feldern ein.

    ![Configure single sign-on](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **DISPLAY NAME** (Anzeigename): Geben Sie den Anzeigenamen des Plug-Ins auf der Administratorseite ein.

    b. **BUTTON NAME** (Schaltflächenname): Der Name der Registerkarte, die auf der Anmeldeseite für die Anmeldung über SSO angezeigt wird.

    c. **CERTIFICATE** (Zertifikat): Öffnen Sie das zuvor aus dem Azure-Portal heruntergeladene Zertifikat im Editor, kopieren Sie den Inhalt, und fügen Sie ihn in dieses Feld ein.

    d. **ENTRY POINT** (Einstiegspunkt): Fügen Sie die aus dem Azure-Portal kopierte **Anmelde-URL** ein.

    e. Setzen Sie die Option auf **ON** (Ein), und klicken Sie auf **SAVE** (Speichern).

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** **brittasimon@yourcompanydomain.extension** ein.  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie den Zugriff auf Teamphoria gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Teamphoria** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste den Eintrag **Teamphoria** aus.

    ![Teamphoria-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-teamphoria-test-user"></a>Erstellen eines Teamphoria-Testbenutzers

Damit sich Azure AD-Benutzer bei Teamphoria anmelden können, müssen sie in Teamphoria bereitgestellt werden. Im Fall von Teamphoria ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei der Teamphoria-Unternehmenswebsite als Administrator an.

1. Klicken Sie in der linken Symbolleiste auf **ADMIN**-Einstellungen, und klicken Sie auf der Registerkarte **MANAGE** (Verwalten) auf **USERS** (Benutzer), um die Administratorseite für Benutzer zu öffnen.

    ![Mitarbeiter hinzufügen](./media/teamphoria-tutorial/admin_manage_users.png)

1. Klicken Sie auf die Option **MANUAL INVITE** (Manuell einladen).

    ![Invite People](./media/teamphoria-tutorial/admin_manage_add_users.png)

1. Führen Sie auf dieser Seite die folgende Aktion aus.

    ![Invite People](./media/teamphoria-tutorial/manual_user_invite.png)

    a. Geben Sie im Textfeld **EMAIL ADDRESS** (E-MAIL-ADRESSE) die **E-Mail-Adresse** des Benutzers (etwa BrittaSimon) ein.

    b. Geben Sie im Textfeld **FIRST NAME** (VORNAME) den Vornamen des Benutzers ein (z. B. **Britta**).

    c. Geben Sie im Textfeld **LAST NAME** (NACHNAME) den Nachnamen des Benutzers ein (z. B. **Simon**).

    d. Klicken Sie auf **INVITE 1 USER** (1 Benutzer einladen). Der Benutzer muss die Einladung annehmen, um im System erstellt zu werden.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Teamphoria“ klicken, sollten Sie automatisch bei der Teamphoria-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

