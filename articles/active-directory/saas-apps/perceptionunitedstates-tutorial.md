---
title: 'Tutorial: Azure Active Directory-Integration mit Perception United States (Non-UltiPro) | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Perception United States (Non-UltiPro) konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 3fc16a869c176074467a0b21880937d7753602b2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515524"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Tutorial: Azure Active Directory-Integration mit Perception United States (Non-UltiPro)

In diesem Tutorial erfahren Sie, wie Sie Perception United States (Non-UltiPro) in Azure Active Directory (Azure AD) integrieren.
Die Integration von Perception United States (Non-UltiPro) in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Perception United States (Non-UltiPro) hat.
* Sie können es Ihren Benutzern ermöglichen, dass sie mit ihren Azure AD-Konten automatisch bei Perception United States (Non-UltiPro) angemeldet werden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in Perception United States (Non-UltiPro) konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Perception United States (Non-UltiPro)-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Perception United States (Non-UltiPro) unterstützt **IDP** -initiiertes einmaliges Anmelden.

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Hinzufügen von Perception United States (Non-UltiPro) aus dem Katalog

Zum Konfigurieren der Integration von Perception United States (Non-UltiPro) in Azure AD müssen Sie Perception United States (Non-UltiPro) aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Perception United States (Non-UltiPro) aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory** .

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Perception United States (Non-UltiPro)** ein, wählen Sie im Ergebnisbereich **Perception United States (Non-UltiPro)** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

     ![Perception United States (Non-UltiPro) in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Perception United States (Non-UltiPro) mithilfe einer Testbenutzerin namens **Britta Simon** .
Damit einmaliges Anmelden funktioniert, muss zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Perception United States (Non-UltiPro) eine Linkbeziehung eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Perception United States (Non-UltiPro) müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Perception United States (Non-UltiPro)](#configure-perception-united-states-non-ultipro-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Perception United States (Non-UltiPro)-Testbenutzers](#create-perception-united-states-non-ultipro-test-user)** , um eine Entsprechung von Britta Simon in Perception United States (Non-UltiPro) zu erhalten, die mit der Darstellung des Benutzers in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei Perception United States (Non-UltiPro) die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Perception United States (Non-UltiPro)** die Option **Einmaliges Anmelden** .

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Perception United States (Non-UltiPro)](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL ein: `https://perception.kanjoya.com/sp`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://perception.kanjoya.com/sso?idp=<entity_id>`

    c. Für die Anwendung **Perception United States (Non-UltiPro)** benötigen Sie den Wert für den **Azure AD-Bezeichner** als „<entity_id>“ für die URI-Codierung. Sie erhalten diesen Wert unter **Perception United States (Non-UltiPro) einrichten** . Verwenden Sie den folgenden Link, um den URI-codierten Wert abzurufen: **http://www.url-encode-decode.com/** .

    d. Kombinieren Sie den URI-codierten Wert nach dem Abrufen mit der **Antwort-URL** , wie unten beschrieben.

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    e. Fügen Sie den obigen Wert im Textfeld **Antwort-URL** ein.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML** -Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **Perception United States (Non-UltiPro) einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL   

### <a name="configure-perception-united-states-non-ultipro-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Perception United States (Non-UltiPro)

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Perception United States (Non-UltiPro)-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf der Hauptsymbolleiste auf **Account Settings** .

    ![Screenshot, auf dem „Account Settings“ (Kontoeinstellungen) auf der Hauptsymbolleiste ausgewählt ist](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

3. Führen Sie auf der Seite **Account Settings** die folgenden Schritte aus:

    ![Perception United States (Non-UltiPro)-Benutzer](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. Geben Sie im Textfeld **Company Name** den Namen des **Unternehmens** ein.
    
    b. Geben Sie im Textfeld **Account Name** den Namen des **Kontos** ein.

    c. Geben Sie im Textfeld **Default Reply-To Email** die gültige **E-Mail-Adresse** ein.

    d. Wählen Sie für **SSO Identity Provider** die Option **SAML 2.0** aus.

4. Führen Sie auf der Seite **SSO Configuration** die folgenden Schritte aus:

    ![Perception United States (Non-UltiPro)-SSO-Konfiguration](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Wählen Sie als **SAML NameID Type** die Option **EMAIL** aus.

    b. Geben Sie im Textfeld **SSO Configuration Name** den Namen Ihrer **Konfiguration** ein.
    
    c. Fügen Sie in das Textfeld **Name des Identitätsanbieters** den Wert von **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben. 

    d. Geben Sie im Textfeld **SAML-Domäne** die Domäne im Format @contoso.com ein.

    e. Klicken Sie auf **Upload Again** , um die **Metadaten-XML** -Datei hochzuladen.

    f. Klicken Sie auf **Aktualisieren** .

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** , **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** den Namen brittasimon@yourcompanydomain.extension ein. Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Perception United States (Non-UltiPro) gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen** , **Alle Anwendungen** und **Perception United States (Non-UltiPro)** .

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste den Eintrag **Perception United States (Non-UltiPro)** aus.

    ![Perception United States (Non-UltiPro)-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen** .

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen** .

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

### <a name="create-perception-united-states-non-ultipro-test-user"></a>Erstellen eines Perception United States (Non-UltiPro)-Testbenutzers

In diesem Abschnitt erstellen Sie in Perception United States (Non-UltiPro) einen Benutzer namens Britta Simon. Arbeiten Sie mit dem [Supportteam von Perception United States (Non-UltiPro)](https://www.ultimatesoftware.com/Contact/ContactUs) zusammen, um die Benutzer zur Perception United States (Non-UltiPro)-Plattform hinzuzufügen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Perception United States (Non-UltiPro)“ klicken, sollten Sie automatisch bei Ihrer Perception United States (Non-UltiPro)-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)