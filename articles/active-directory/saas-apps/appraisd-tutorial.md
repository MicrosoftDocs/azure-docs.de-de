---
title: 'Tutorial: Azure Active Directory-Integration mit Appraisd | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Appraisd konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ee7e58aa6298977b279d0d538fefb24da657388
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357021"
---
# <a name="tutorial-integrate-appraisd-with-azure-active-directory"></a>Tutorial: Integrieren von Appraisd in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Appraisd in Azure Active Directory (Azure AD) integrieren. Die Integration von Appraisd in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Appraisd hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Appraisd anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Appraisd-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung. Appraisd unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

## <a name="adding-appraisd-from-the-gallery"></a>Hinzufügen von Appraisd aus dem Katalog

Zum Konfigurieren der Integration von Appraisd in Azure AD müssen Sie Appraisd aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Appraisd** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Appraisd** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Appraisd mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Appraisd eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Appraisd müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
2. **[Konfigurieren von Appraisd](#configure-appraisd)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Appraisd-Testbenutzers](#create-appraisd-test-user)** , um in Appraisd ein Pendant von B. Simon zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Appraisd** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Im Abschnitt **SAML-Basiskonfiguration** ist die Anwendung vorkonfiguriert und die notwendigen URLs sind bereits mit Azure vorausgefüllt. Der Benutzer muss die Konfiguration speichern, indem er auf die Schaltfläche „Speichern“ klickt, und die folgenden Schritte ausführen:

    a. Klicken Sie auf **Zusätzliche URLs festlegen**.

    b. Geben Sie im Textfeld **Relayzustand** eine URL ein: `<TENANTCODE>`

    c. Wenn Sie die Anwendung im **SP**-initiierten Modus konfigurieren möchten, geben Sie im Textfeld **Anmelde-URL** eine URL unter Verwendung des folgenden Musters ein: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > Sie können die tatsächlichen Werte für „Anmelde-URL“ und „Relayzustand“ auf der SSO-Konfigurationsseite für Appraisd abrufen, was im weiteren Verlauf des Tutorials erläutert wird.

1. Appraisd erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.  **nameidentifier** ist hier  **user.userprincipalname** zugeordnet. Die Appraisd-Anwendung erwartet, dass  **nameidentifier** der Wert **user.mail** zugeordnet ist. Sie müssen die Attributzuordnung daher entsprechend ändern, indem Sie auf das Symbol  **Bearbeiten** klicken.

    ![image](common/edit-attribute.png)

1. Suchen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** den Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

   ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Appraisd einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

   ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="configure-appraisd"></a>Konfigurieren von Appraisd

1. Wenn Sie die Konfiguration in Appraisd automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Appraisd einrichten**, um zur Anwendung Appraisd weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Appraisd anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 7.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie Appraisd manuell einrichten möchten, öffnen Sie ein neues Webbrowserfenster, melden Sie sich bei der Appraisd-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

4. Klicken Sie oben rechts auf der Seite auf das Symbol **Settings** (Einstellungen), und navigieren Sie dann zu **Configuration** (Konfiguration).

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

5. Klicken Sie im linken Bereich des Menüs auf **SAM Single Sign-On** (Einmaliges Anmelden mit SAML).

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

6. Führen Sie auf der **Konfigurationsseite für einmaliges Anmelden mit SAML 2.0** die folgenden Schritte aus:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Kopieren Sie den Wert für den **Standardrelayzustand**, und fügen Sie ihn in das Textfeld **Relayzustand** im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ein.

    b. Kopieren Sie den Wert für die **Vom Dienst initiierte Anmelde-URL**, und fügen Sie ihn in das Textfeld **Anmelde-URL** im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ein.

7. Scrollen Sie auf derselben Seite nach unten, und führen Sie unter **Benutzer werden identifiziert** die folgenden Schritte aus:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. Fügen Sie in das Textfeld **URL für einmaliges Anmelden des Identitätsanbieters** den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben, und klicken Sie auf **Speichern**.

    b. Fügen Sie in das Textfeld **Identity Provider Issuer URL** (Aussteller-URL des Identitätsanbieters) den Wert für **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben, und klicken Sie auf **Save** (Speichern).

    c. Öffnen Sie in Editor das Base64-codierte Zertifikat, das Sie aus dem Azure-Portal heruntergeladen haben, kopieren Sie den Inhalt, und fügen Sie ihn anschließend in das Feld **X.509-Zertifikat** ein, und klicken Sie auf **Speichern**.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B. Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B. Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Appraisd gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **Appraisd** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann am unteren Bildschirmrand auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-appraisd-test-user"></a>Erstellen eines Appraisd-Testbenutzers

Damit sich Azure AD-Benutzer bei Appraisd anmelden können, müssen sie in Appraisd bereitgestellt werden. Im Fall von Appraisd muss die Bereitstellung manuell ausgeführt werden.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Appraisd als Sicherheitsadministrator an.

2. Klicken Sie oben rechts auf der Seite auf das Symbol **Settings** (Einstellungen), und navigieren Sie dann zu **Administration Centre**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Klicken Sie auf der Symbolleiste am oberen Rand der Seite auf **People** (Personen), und navigieren Sie dann zu **Add a new user** (Neuen Benutzer hinzufügen).

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Führen Sie auf der Seite **Add a new user** (Neuen Benutzer hinzufügen) die folgenden Schritte aus:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. Geben Sie im Textfeld **First name** (Vorname) den Vornamen des Benutzers ein, z. B. **Britta**.

    b. Geben Sie im Textfeld **Last name** (Nachname) den Nachnamen des Benutzers ein, z. B. **Simon**.

    c. Geben Sie im Textfeld **Email** (E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein, z.B. `B. Simon@contoso.com`.

    d. Klicken Sie auf **Benutzer hinzufügen**.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens

Wenn Sie im Zugriffsbereich die Kachel „Appraisd“ auswählen, sollten Sie automatisch bei der Appraisd-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)