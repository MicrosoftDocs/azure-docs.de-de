---
title: 'Tutorial: Azure Active Directory-Integration in Adobe Sign | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Adobe Sign konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91d4cf874d4d98735582d2690072e6a04683c1f5
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988726"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Tutorial: Azure Active Directory-Integration mit Adobe Sign

In diesem Tutorial erfahren Sie, wie Sie Azure Active Directory (Azure AD) in Adobe Sign integrieren.
Die Integration von Azure AD in Adobe Sign bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Adobe Sign hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Adobe Sign anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in Adobe Sign konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein Adobe Sign-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Adobe Sign unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="adding-adobe-sign-from-the-gallery"></a>Hinzufügen von Adobe Sign aus dem Katalog

Zum Konfigurieren der Integration von Azure AD in Adobe Sign müssen Sie Adobe Sign aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte durch, um Adobe Sign aus dem Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Adobe Sign** ein, wählen Sie im Ergebnisbereich **Adobe Sign** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Adobe Sign in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Adobe Sign anhand eines Testbenutzers namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Adobe Sign eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens mit Azure AD bei Adobe Sign müssen die folgenden Schritte durchgeführt werden:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Adobe Sign](#configure-adobe-sign-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Adobe Sign-Testbenutzers](#create-adobe-sign-test-user)** , um eine Entsprechung von Britta Simon in Adobe Sign zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Adobe Sign die folgenden Schritte aus:

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Adobe Sign** auf **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Adobe Sign](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<companyname>.echosign.com/`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<companyname>.echosign.com`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Supportteam von Adobe Sign](https://helpx.adobe.com/in/contact/support.html), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Adobe Sign einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-adobe-sign-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Adobe Sign

1. Wenden Sie sich vor der Konfiguration an das [Supportteam für den Adobe Sign-Client](https://helpx.adobe.com/in/contact/support.html), um Ihre Domäne zur Adobe Sign-Zulassungsliste hinzufügen zu lassen. So fügen Sie die Domäne hinzu:

    a. Das [Adobe Sign-Supportteam](https://helpx.adobe.com/in/contact/support.html) sendet Ihnen ein zufällig generiertes Token. Für Ihre Domäne sieht das Token wie folgt aus: **adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Veröffentlichen Sie das Überprüfungstoken in einem DNS-Texteintrag, und benachrichtigen Sie das [Adobe Sign-Supportteam](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Die Bearbeitung kann ein paar Tage oder länger dauern. Beachten Sie, dass Verzögerungen bei der DNS-Verteilung bedeuten, dass ein in DNS veröffentlichter Wert möglicherweise mindestens eine Stunde nicht angezeigt wird. Ihr IT-Administrator weiß, wie dieses Token in einem DNS-Texteintrag veröffentlicht wird.
    
    c. Wenn Sie das [Adobe Sign-Supportteam](https://helpx.adobe.com/in/contact/support.html) nach der Tokenveröffentlichung über das Supportticket benachrichtigen, überprüft das Team die Domäne und fügt sie Ihrem Konto hinzu.
    
    d. So veröffentlichen Sie generell ein Token in einem DNS-Eintrag

    * Melden Sie sich in Ihrem Domänenkonto an.
    * Suchen Sie die Seite zum Aktualisieren des DNS-Eintrags. Diese Seite heißt möglicherweise „DNS-Verwaltung“, „Namensserververwaltung“, „Erweiterte Einstellungen“ usw.
    * Suchen Sie die TXT-Einträge für Ihre Domäne.
    * Fügen Sie einen TXT-Eintrag mit dem vollständigen Tokenwert hinzu, den Adobe bereitgestellt hat.
    * Speichern Sie die Änderungen.

1. Melden Sie sich in einem anderen Webbrowserfenster auf der Adobe Sign-Unternehmenswebsite als Administrator an.

1. Rufen Sie im SAML-Menü **Account Settings** (Kontoeinstellungen)  >  **SAML Settings** (SAML-Einstellungen) auf.
   
    ![Screenshot von Adobe Sign-SAML-Einstellungsseite](./media/adobe-echosign-tutorial/ic789520.png "Account") (Konto)

1. Führen Sie im Abschnitt für die **SAML-Einstellungen** die folgenden Schritte aus:
  
   ![Screenshot von SAML-Einstellungen](./media/adobe-echosign-tutorial/ic789521.png "SAML Settings")
   
   ![Screenshot von SAML-Einstellungen](./media/adobe-echosign-tutorial/ic789522.png "SAML Settings")

   a. Wählen Sie als **SAML Mode** (SAML-Modus) **SAML Mandatory** (SAML erforderlich) aus.
   
   b. Aktivieren Sie **Allow Echosign Account Administrators to log in using their Echosign Credentials** (EchoSign-Kontoadministratoren dürfen sich mit ihren EchoSign-Anmeldeinformationen anmelden).
   
   c. Wählen Sie unter **User Creation** (Benutzererstellung) **Automatically add users authenticated through SAML** (SAML-authentifizierte Benutzer automatisch hinzufügen) aus.

   d. Fügen Sie den **Azure AD-Bezeichner**, den Sie aus dem Azure-Portal kopiert haben, in das Textfeld **Idp Entity ID** (IdP-Entitäts-ID) ein.
    
   e. Fügen Sie in das Textfeld **Idp Login URL** (IdP-Anmelde-URL) die **Anmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.
   
   f. Fügen Sie in das Textfeld **Idp Logout URL** (IdP-Abmelde-URL) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

   g. Öffnen Sie die heruntergeladene **Zertifikat (Base64)** -Datei im Editor. Kopieren Sie den Inhalt in die Zwischenablage, und fügen Sie ihn in das Textfeld **IdP Certificate** (IdP-Zertifikat) ein.

   h. Klicken Sie auf **Save changes** (Änderungen speichern).

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens mit Azure, indem Sie ihr Zugriff auf Adobe Sign gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **Adobe Sign**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Geben Sie in der Anwendungsliste **Adobe Sign** ein, und wählen Sie den Eintrag aus.

    ![Adobe Sign-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-adobe-sign-test-user"></a>Erstellen eines Adobe Sign-Testbenutzers

Damit sich Azure AD-Benutzer bei Adobe Sign anmelden können, müssen sie in Adobe Sign bereitgestellt werden. Diese Aufgabe muss manuell ausgeführt werden.

>[!NOTE]
>Sie können Azure AD-Benutzerkonten auch mit anderen Tools zum Erstellen von Adobe Sign-Benutzerkonten oder mit den von Adobe Sign bereitgestellten APIs erstellen. 

1. Melden Sie sich auf der **Adobe Sign**-Unternehmenswebsite als Administrator an.

2. Wählen Sie oben im Menü **Account** (Konto) aus. Wählen Sie im linken Bereich **Users & Groups** (Benutzer & Gruppen)  >  **Create a new user** (Neuen Benutzer erstellen) aus.
   
    ![Screenshot von Adobe Sign-Unternehmenswebsite mit Hervorhebung von „Account“ (Konto), „Users & Groups“ (Benutzer & Gruppen) und „Create a new user“ (Neuen Benutzer erstellen) – ](./media/adobe-echosign-tutorial/ic789524.png "Account") (Konto)
   
3. Führen Sie im Abschnitt **Create New User** die folgenden Schritte aus:
   
    ![Screenshot vom Abschnitt „Create New User“ (Neuen Benutzer erstellen) – ](./media/adobe-echosign-tutorial/ic789525.png "Create User") (Benutzer erstellen)
   
    a. Geben Sie in die Textfelder **Email Address** (E-Mail-Adresse), **First Name** (Vorname) und **Last Name** (Nachname) die entsprechenden Informationen eines gültigen Azure AD-Kontos ein, das Sie bereitstellen möchten.
   
    b. Klicken Sie auf **Create User** (Benutzer erstellen).

>[!NOTE]
>Der Besitzer des Azure Active Directory-Kontos erhält eine E-Mail mit einem Bestätigungslink für das Konto, bevor es aktiv wird. 

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Adobe Sign“ klicken, sollten Sie automatisch bei Ihrer Adobe Sign-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

