---
title: 'Tutorial: Azure Active Directory-Integration in Proxyclick | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Proxyclick konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 23ae1a2c1371cda9435ea76f02cebc79c141c904
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92522239"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Tutorial: Azure Active Directory-Integration in Proxyclick

In diesem Tutorial erfahren Sie, wie Sie Proxyclick in Azure Active Directory (Azure AD) integrieren.
Diese Integration bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Proxyclick hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Proxyclick anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten an einem zentralen Ort verwalten: im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Proxyclick konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie sich für eine [einmonatige Testversion](https://azure.microsoft.com/pricing/free-trial/) registrieren.
* Ein Proxyclick-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Proxyclick unterstützt SP- und IdP-initiiertes einmaliges Anmelden.

## <a name="add-proxyclick-from-the-gallery"></a>Hinzufügen von Proxyclick aus dem Katalog

Zum Einrichten der Integration von Proxyclick in Azure AD müssen Sie Proxyclick aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Klicken Sie im linken Bereich des [Azure-Portals](https://portal.azure.com) auf **Azure Active Directory** :

    ![Wählen Sie „Azure Active Directory“.](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** > **Alle Anwendungen** :

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Um eine Anwendung hinzuzufügen, wählen Sie oben im Fenster die Option **Neue Anwendung** :

    ![Auswählen von „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld das Wort **Proxyclick** ein. Wählen Sie in den Suchergebnissen den Eintrag **Proxyclick** aus, und wählen Sie dann **Hinzufügen** aus.

     ![Suchergebnisse](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Proxyclick mithilfe eines Testbenutzers namens Britta Simon.
Damit einmaliges Anmelden funktioniert, müssen Sie eine Beziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Proxyclick einrichten.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Proxyclick müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden der Funktion zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Proxyclick](#configure-proxyclick-single-sign-on)** auf der Anwendungsseite
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD für den Benutzer zu aktivieren.
5. **[Erstellen eines Proxyclick-Testbenutzers](#create-a-proxyclick-test-user)** , der mit der Darstellung des Benutzers in Azure AD verknüpft ist
6. **[Testen des einmaligen Anmeldens](#test-single-sign-on)** , um sicherzustellen, dass die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Proxyclick die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für Proxyclick die Option **Einmaliges Anmelden** aus:

    ![„Einmaliges Anmelden“ auswählen](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren:

    ![SSO-Methode auswählen](common/select-saml-option.png)

3. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Symbol **Bearbeiten** aus, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen:

    ![Symbol „Bearbeiten“](common/edit-urls.png)

4. Führen Sie im Dialogfeld **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im IdP-initiierten Modus konfigurieren möchten:

    ![Dialogfeld „Grundlegende SAML-Konfiguration“](common/idp-intiated.png)

    1. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein:
   
       `https://saml.proxyclick.com/init/<companyId>`

    1. Geben Sie im Feld **Antwort-URL** eine URL im folgenden Format ein:

       `https://saml.proxyclick.com/consume/<companyId>`

5. Wenn Sie die Anwendung im SP-initiierten Modus konfigurieren möchten, wählen Sie **Zusätzliche URLs festlegen** aus. Geben Sie im Feld **Anmelde-URL** eine URL im folgenden Format ein:
   
   `https://saml.proxyclick.com/init/<companyId>`

    ![SSO-Informationen zur Domäne und zu den URLs für Proxyclick](common/metadata-upload-additional-signon.png)

    

    > [!NOTE]
    > Diese Werte sind Platzhalter. Sie müssen die tatsächlichen Werte für Bezeichner, Antwort-URL und Anmelde-URL verwenden. Die Schritte zum Abrufen dieser Werte werden später in diesem Tutorial beschrieben.

6. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** den Link **Herunterladen** neben **Zertifikat (Base64)** gemäß Ihren Anforderungen aus, und speichern Sie das Zertifikat auf Ihrem Computer:

    ![Downloadlink für Zertifikat](common/certificatebase64.png)

7. Kopieren Sie im Abschnitt **Proxyclick einrichten** die entsprechenden URLs gemäß Ihren Anforderungen:

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    1. **Anmelde-URL**

    1. **Azure AD-Bezeichner**

    1. **Abmelde-URL** :

### <a name="configure-proxyclick-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Proxyclick

1. Melden Sie sich in einem neuen Webbrowserfenster auf der Proxyclick-Unternehmenswebsite als Administrator an.

2. Wählen Sie **Account & Settings** (Konto & Einstellungen) aus:

    ![Auswählen von „Account & Settings“ (Konto & Einstellungen)](./media/proxyclick-tutorial/configure1.png)

3. Scrollen Sie nach unten zu **Integrations** (Integrationen), und wählen Sie **SAML** (SAML) aus:

    ![Auswählen von „SAML“](./media/proxyclick-tutorial/configure2.png)

4. Führen Sie im Abschnitt **SAML** (SAML) die folgenden Schritte aus:

    ![Abschnitt „SAML“](./media/proxyclick-tutorial/configure3.png)

    1. Kopieren Sie den Wert für **SAML Consumer URL** (SAML-Consumer-URL), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Antwort-URL** ein.

    1. Kopieren Sie den Wert für **SAML SSO Redirect URL** (Umleitungs-URL für SAML-SSO), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in die Felder **Anmelde-URL** und **Bezeichner** ein.

    1. Wählen Sie in der Liste **SAML Request Method** (SAML-Anforderungsmethode) die Option **HTTP Redirect** (HTTP-Umleitung) aus.

    1. Fügen Sie im Feld **Issuer** (Aussteller) den Wert für den **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    1. Fügen Sie im Feld **SAML 2.0 Endpoint URL** (SAML 2.0-Endpunkt-URL) die **Anmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.

    1. Öffnen Sie im Editor die Zertifikatdatei, die Sie aus dem Azure-Portal heruntergeladen haben. Fügen Sie den Inhalt dieser Datei in das Feld **Certificate** (Zertifikat) ein.

    1. Klicken Sie auf **Save changes** (Änderungen speichern).

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal eine Testbenutzerin namens „Britta Simon“.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** aus, und wählen Sie dann **Benutzer** und anschließend **Alle Benutzer** aus:

    ![„Alle Benutzer“ auswählen](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus:

    ![„Neuer Benutzer“ auswählen](common/new-user.png)

3. Führen Sie im Dialogfeld **Benutzer** die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    1. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.
  
    1. Geben Sie in das Feld **Benutzername** den Namen **BrittaSimon@\<yourcompanydomain>.\<extension>** ein. (Beispiel: BrittaSimon@contoso.com.)

    1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den im Feld **Kennwort** angezeigten Wert.

    1. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Proxyclick gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Proxyclick** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Liste der Anwendungen den Eintrag **Proxyclick** aus.

    ![Liste der Anwendungen](common/all-applications.png)

3. Wählen Sie im linken Bereich die Option **Benutzer und Gruppen** aus:

    ![Benutzer und Gruppen auswählen](common/users-groups-blade.png)

4. Wählen Sie die Schaltfläche **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Auswählen von „Benutzer hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Fenster auf die Schaltfläche **Auswählen** .

6. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Klicken Sie unten im Fenster auf die Schaltfläche **Auswählen** .

7. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="create-a-proxyclick-test-user"></a>Erstellen eines Proxyclick-Testbenutzers

Damit sich Azure AD-Benutzer bei Proxyclick anmelden können, müssen Sie sie in Proxyclick hinzufügen. Sie müssen diese manuell hinzufügen.

Zum Erstellen eines Benutzerkontos führen Sie die folgenden Schritte aus:

1. Melden Sie sich auf der Proxyclick-Unternehmenswebsite als Administrator an.

1. Wählen Sie oben im Fenster die Option **Colleagues** (Kollegen) aus:

    ![Auswählen von „Colleagues“ (Kollegen)](./media/proxyclick-tutorial/user1.png)

1. Wählen Sie **Add Colleague** (Kollegen hinzufügen) aus:

    ![Auswählen von „Add Colleague“ (Kollegen hinzufügen)](./media/proxyclick-tutorial/user2.png)

1. Führen Sie im Abschnitt **Add a colleague** (Kollegen hinzufügen) die folgenden Schritte aus:

    ![Abschnitt „Add a colleague“ (Kollegen hinzufügen)](./media/proxyclick-tutorial/user3.png)

    1. Geben Sie im Feld **Email** (E-Mail) die E-Mail-Adresse des Benutzers ein. In diesem Fall: **brittasimon\@contoso.com** .

    1. Geben Sie im Textfeld **First Name** (Vorname) den Vornamen des Benutzers ein. In diesem Fall ist dies **Britta** .

    1. Geben Sie im Textfeld **Last Name** (Nachname) den Nachnamen des Benutzers ein. In diesem Fall ist dies **Simon** .

    1. Wählen Sie **Add User** (Benutzer hinzufügen) aus.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

Jetzt müssen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich testen.

Wenn Sie im Zugriffsbereich die Kachel „Proxyclick“ auswählen, sollten Sie automatisch bei der Proxyclick-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Zugreifen auf und Verwenden von Apps im Portal „Meine Apps“](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Tutorials zur Integration von SaaS-Anwendungen in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)