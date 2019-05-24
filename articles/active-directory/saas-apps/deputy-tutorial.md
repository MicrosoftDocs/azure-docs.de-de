---
title: 'Tutorial: Azure Active Directory-Integration mit Deputy | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Deputy konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5665c3ac-5689-4201-80fe-fcc677d4430d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b0b3921640ebf6ed21860cfac75ffca195096db
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65863035"
---
# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>Tutorial: Azure Active Directory-Integration mit Deputy

In diesem Tutorial erfahren Sie, wie Sie Deputy in Azure Active Directory (Azure AD) integrieren.
Die Integration von Deputy in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Deputy hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Deputy anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Deputy konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein Deputy-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Deputy unterstützt **SP-** und **IDP-initiiertes** einmaliges Anmelden.

## <a name="adding-deputy-from-the-gallery"></a>Hinzufügen von Deputy aus dem Katalog

Zum Konfigurieren der Integration von Deputy in Azure AD müssen Sie Deputy aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um Deputy aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Deputy** ein, wählen Sie im Ergebnisbereich **Deputy** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Deputy in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Deputy mithilfe eines Testbenutzers namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Deputy eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Deputy müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Deputy](#configure-deputy-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Deputy-Testbenutzers](#create-deputy-test-user)**, um ein Pendant von Britta Simon in Deputy zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Deputy die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Deputy** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für Deputy](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein:

    |  |
    | ----|
    | `https://<subdomain>.<region>.au.deputy.com` |
    | `https://<subdomain>.<region>.ent-au.deputy.com` |
    | `https://<subdomain>.<region>.na.deputy.com`|
    | `https://<subdomain>.<region>.ent-na.deputy.com`|
    | `https://<subdomain>.<region>.eu.deputy.com` |
    | `https://<subdomain>.<region>.ent-eu.deputy.com` |
    | `https://<subdomain>.<region>.as.deputy.com` |
    | `https://<subdomain>.<region>.ent-as.deputy.com` |
    | `https://<subdomain>.<region>.la.deputy.com` |
    | `https://<subdomain>.<region>.ent-la.deputy.com` |
    | `https://<subdomain>.<region>.af.deputy.com` |
    | `https://<subdomain>.<region>.ent-af.deputy.com` |
    | `https://<subdomain>.<region>.an.deputy.com` |
    | `https://<subdomain>.<region>.ent-an.deputy.com` |
    | `https://<subdomain>.<region>.deputy.com` |

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein:
    
    | |
    |----|
    | `https://<subdomain>.<region>.au.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.ent-au.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.na.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.ent-na.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.eu.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.ent-eu.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.as.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-as.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.la.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.ent-la.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.af.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.ent-af.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.an.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.ent-an.deputy.com/exec/devapp/samlacs` |
    | `https://<subdomain>.<region>.deputy.com/exec/devapp/samlacs` |

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für Deputy](common/metadata-upload-additional-signon.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<your-subdomain>.<region>.deputy.com`

    >[!NOTE]
    > Das Regionssuffix für Deputy ist optional und muss bei Verwendung auf eines der folgenden Suffixe festgelegt werden: au | na | eu |as |la |af |an |ent-au |ent-na |ent-eu |ent-as | ent-la | ent-af | ent-an

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Wenden Sie sich an das [Supportteam für den Deputy-Client](https://www.deputy.com/call-centers-customer-support-scheduling-software), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

7. Kopieren Sie im Abschnitt **Deputy einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-deputy-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Deputy

1. Navigieren Sie zu folgender URL:[https://(Ihre-Unterdomäne).deputy.com/exec/config/system_config]( https://(your-subdomain).deputy.com/exec/config/system_config). Wechseln Sie zu **Sicherheitseinstellungen**, und klicken Sie auf **Bearbeiten**.
   
    ![Configure single sign-on](./media/deputy-tutorial/tutorial_deputy_004.png)

2. Führen Sie auf der Seite **Sicherheitseinstellungen** die folgenden Schritte aus.

    ![Configure single sign-on](./media/deputy-tutorial/tutorial_deputy_005.png)
    
    a. Aktivieren der **Anmeldung für soziale Netzwerke**.
   
    b. Öffnen Sie das vom Azure-Portal heruntergeladene Base64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **OpenSSL-Zertifikat** ein.
   
    c. Geben Sie im Textfeld für die SAML-SSO-URL Folgendes ein: `https://<your subdomain>.deputy.com/exec/devapp/samlacs?dpLoginTo=<saml sso url>`
    
    d. Ersetzen Sie im Textfeld für die SAML-SSO-URL `<your subdomain>` durch Ihre Unterdomäne.
   
    e. Ersetzen Sie im Textfeld für die SAML-SSO-URL `<saml sso url>` durch die **Anmelde-URL**, die Sie aus dem Azure-Portal kopiert haben.
   
    f. Klicken Sie auf **Einstellungen speichern**.

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Deputy gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **Deputy** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Deputy**aus.

    ![Deputy-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-deputy-test-user"></a>Erstellen eines Deputy-Testbenutzers

Damit sich Azure AD-Benutzer bei Deputy anmelden können, müssen sie in Deputy bereitgestellt werden. Im Fall von Deputy ist die Bereitstellung eine manuelle Aufgabe.

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:

1. Melden Sie sich bei der Deputy-Unternehmenswebsite als Administrator an.

2. Klicken Sie oben im Navigationsbereich auf **Personen**.
   
    ![Personen](./media/deputy-tutorial/tutorial_deputy_001.png "Personen")

3. Klicken Sie auf die Schaltfläche **Personen hinzufügen**, und klicken Sie auf **Einzelne Person hinzufügen**.
   
    ![Personen hinzufügen](./media/deputy-tutorial/tutorial_deputy_002.png "Personen hinzufügen")

4. Führen Sie die folgenden Schritte aus, und klicken Sie auf **Speichern und einladen**.
   
    ![Neuer Benutzer](./media/deputy-tutorial/tutorial_deputy_003.png "Neuer Benutzer")

    a. Geben Sie im Textfeld **Name** den Namen des Benutzers ein, z.B. **BrittaSimon**.
   
    b. Geben Sie im Textfeld **E-Mail** die E-Mail-Adresse eines Azure AD-Kontos ein, das Sie bereitstellen möchten.
   
    c. Geben Sie im Textfeld **Firma** den Namen des Unternehmens ein.
   
    d. Klicken Sie auf die Schaltfläche **Speichern und einladen**.

5. Der Besitzer des Azure AD-Kontos erhält eine E-Mail mit einem Link zur Bestätigung des Kontos, bevor es aktiv wird. Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Deputy-Benutzerkonten oder mithilfe der von Deputy bereitgestellten APIs erstellen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Deputy“ klicken, sollten Sie automatisch bei der Deputy-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

