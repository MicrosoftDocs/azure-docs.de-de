---
title: 'Tutorial: Azure Active Directory-Integration mit SuccessFactors | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SuccessFactors konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/3/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2281fb046ca9b96aa5800150d630b9086236c5c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65866657"
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>Tutorial: Azure Active Directory-Integration mit SuccessFactors

In diesem Tutorial erfahren Sie, wie Sie SuccessFactors in Azure Active Directory (Azure AD) integrieren.
Die Integration von SuccessFactors in Azure AD bietet folgende Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf SuccessFactors haben soll.
* Sie können es Ihren Benutzern ermöglichen, dass sie mit ihren Azure AD-Konten automatisch bei SuccessFactors angemeldet werden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit SuccessFactors konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* SuccessFactors-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SuccessFactors unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="adding-successfactors-from-the-gallery"></a>Hinzufügen von SuccessFactors über den Katalog

Zum Konfigurieren der Integration von SuccessFactors in Azure AD müssen Sie SuccessFactors über den Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um SuccessFactors über den Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **SuccessFactors** ein, wählen Sie im Ergebnisbereich **SuccessFactors** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![SuccessFactors in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei SuccessFactors basierend auf einer Testbenutzerin mit dem Namen **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SuccessFactors eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit SuccessFactors zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für SuccessFactors](#configure-successfactors-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines SuccessFactors-Testbenutzers](#create-successfactors-test-user)**, um in SuccessFactors eine Entsprechung von Britta Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie folgende Schritte aus, um das einmalige Anmelden von Azure AD mit SuccessFactors zu konfigurieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **SuccessFactors** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und den URLs für SuccessFactors](common/sp-identifier-reply.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein:

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. Geben Sie im Textfeld **Identifier** (Bezeichner) eine URL nach folgendem Muster ein:

    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. Geben Sie im Textfeld **Antwort-URL** eine URL nach folgendem Muster ein:

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL, den tatsächlichen Bezeichner und die tatsächliche Antwort-URL. Die entsprechenden Werte erfahren Sie vom [SuccessFactors-Supportteam](https://www.successfactors.com/content/ssf-site/en/support.html).

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **SuccessFactors einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-successfactors-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für SuccessFactors

1. Melden Sie sich in einem anderen Webbrowserfenster als Administrator beim **SuccessFactors-Verwaltungsportal** an.

2. Rufen Sie **Anwendungssicherheit** auf, und navigieren **Sie zum Feature für einmaliges Anmelden**.

3. Geben Sie unter **Reset Token** (Token zurücksetzen) einen beliebigen Wert an, und klicken Sie auf **Save Token** (Token speichern), um SAML-SSO zu aktivieren.

    ![App-seitiges Konfigurieren des einmaligen Anmeldens][11]

    > [!NOTE]
    > Dieser Wert wird als Ein-/Ausschalter verwendet. Sobald ein beliebiger Wert gespeichert wird, wird SAML-SSO aktiviert. Wird kein Wert gespeichert, ist SAML-SSO deaktiviert.

4. Sehen Sie sich den folgenden Screenshot an, und führen Sie die folgenden Aktionen aus:

    ![App-seitiges Konfigurieren des einmaligen Anmeldens][12]
  
    a. Wählen Sie das Optionsfeld **SAML v2 SSO** aus.
  
    b. Legen Sie den **Namen der SAML-Assertionspartei** fest (beispielsweise auf SAML-Aussteller und Unternehmensname).

    c. Fügen Sie im Textfeld **Issuer URL** (Aussteller-URL) den **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Wählen Sie unter **Require Mandatory Signature** (Pflichtsignatur erforderlich) **Assertion** aus.

    e. Wählen Sie für **Enable SAML Flag** (SAML-Flag aktivieren) die Option **Aktiviert** aus.

    f. Wählen Sie für **Login Request Signature(SF Generated/SP/RP)** (Anmeldeanforderungssignatur (SF-generiert/SP/RP)) die Option **Nein** aus.

    g. Wählen Sie für **SAML Profile** (SAML-Profil) die Option **Browser/Post Profile** (Browser/Post-Profil) aus.

    h. Wählen Sie für **Enforce Certificate Valid Period** (Gültigkeitszeitraum des Zertifikats erzwingen) die Option **Nein** aus.

    i. Kopieren Sie den Inhalt der heruntergeladenen Zertifikatdatei aus dem Azure-Portal, und fügen Sie ihn in das Textfeld **SAML Verifying Certificate** (SAML-Verifizierungszertifikat) ein.

    > [!NOTE] 
    > Der Inhalt des Zertifikats muss über Tags für Beginn und Ende des Zertifikats verfügen.

5. Navigieren Sie zu „SAML V2“, und führen Sie die folgenden Schritte aus:

    ![App-seitiges Konfigurieren des einmaligen Anmeldens][13]

    a. Wählen Sie für **Support SP-initiated Global Logout** (SP-initiiertes globales Abmelden unterstützen) die Option **Ja** aus.

    b. Fügen Sie in das Textfeld **Global Logout Service URL (LogoutRequest destination)** (URL des globalen Abmeldediensts (LogoutRequest-Ziel)) den Wert für die **Sign-Out URL** (Abmelde-URL) ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Wählen Sie für **Require sp must encrypt all NameID elements** (SP muss alle NameID-Elemente verschlüsseln) die Option **Nein** aus.

    d. Wählen Sie für **NameID Format** (NameID-Format) die Option **unspecified** (keine Angabe) aus.

    e. Wählen Sie für **Enable sp initiated login (AuthnRequest)** (SP-initiierte Anmeldung aktivieren (AuthnRequest)) die Option **Ja** aus.

    f. Fügen Sie den Wert der **Anmelde-URL**, den Sie aus dem Azure-Portal kopiert haben, in das Textfeld **Send request as Company-Wide issuer** (Anforderung als unternehmensweiter Aussteller senden) ein.

6. Führen Sie die folgenden Schritte aus, wenn bei den Anmeldebenutzernamen die Groß-/Kleinschreibung nicht beachtet werden soll.

    ![Configure single sign-on][29]

    a. Rufen Sie **Unternehmenseinstellungen** (im unteren Bereich der Seite) auf.

    b. Aktivieren Sie das Kontrollkästchen **Enable Non-Case-Sensitive Username**(Groß-/Kleinschreibung bei Benutzernamen nicht beachten).

    c. Klicken Sie auf **Speichern**.

    > [!NOTE]
    > Wenn Sie diese Funktion aktivieren, überprüft das System, ob ein doppelter SAML-Anmeldename erstellt wird. Dies wäre etwa der Fall, wenn der Kunde die Benutzernamen „Benutzer1“ und „benutzer1“ verwendet. Ohne Beachtung der Groß-/Kleinschreibung werden diese zu Duplikaten. In diesem Fall gibt das System eine Fehlermeldung aus, und das Feature wird nicht aktiviert. Der Kunde muss für einen der Benutzernamen eine abweichende Schreibweise verwenden.

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf SuccessFactors erteilen.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **SuccessFactors**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste die Option **SuccessFactors**aus.

    ![SuccessFactors-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-successfactors-test-user"></a>Erstellen eines SuccessFactors-Testbenutzers

Damit sich Azure AD-Benutzer bei SuccessFactors anmelden können, müssen sie in SuccessFactors bereitgestellt werden. Im Fall von SuccessFactors ist die Bereitstellung eine manuelle Aufgabe.

Wenn Sie in SuccessFactors erstellte Benutzer abrufen möchten, wenden Sie sich an das [SuccessFactors-Supportteam](https://www.successfactors.com/content/ssf-site/en/support.html).

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „SuccessFactors“ klicken, sollten Sie automatisch bei Ihrer SuccessFactors-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
