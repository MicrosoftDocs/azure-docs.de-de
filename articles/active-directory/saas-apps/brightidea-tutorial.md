---
title: 'Tutorial: Azure Active Directory-Integration mit Brightidea | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Brightidea konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3adae3e0-f43b-492f-b373-6a512d2d6046
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc45c63d3168a15b4701dcaff7bb531f759d47ad
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65900041"
---
# <a name="tutorial-azure-active-directory-integration-with-brightidea"></a>Tutorial: Azure Active Directory-Integration mit Brightidea

In diesem Tutorial erfahren Sie, wie Sie Brightidea in Azure Active Directory (Azure AD) integrieren.
Die Integration von Brightidea in Azure AD hat folgende Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Brightidea haben soll.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei Brightidea anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Brightidea konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein SSO-fähiges Brightidea-Abonnement

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.


* Brightidea unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.
* Brightidea unterstützt die **Just-In-Time**-Benutzerbereitstellung.


## <a name="adding-brightidea-from-the-gallery"></a>Hinzufügen von Brightidea über den Katalog

Zum Konfigurieren der Integration von Brightidea in Azure AD müssen Sie Brightidea über den Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um Brightidea über den Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie **Brightidea** in das Suchfeld ein, wählen Sie im Ergebnisbereich **Brightidea** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Brightidea in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Brightidea anhand eines Testbenutzers namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Brightidea eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Brightidea zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Brightidea](#configure-brightidea-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Brightidea-Testbenutzers](#create-brightidea-test-user)**, um in Brightidea ein Pendant von Britta Simon zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Brightidea zu konfigurieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Brightidea** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie über eine **Metadatendatei des Dienstanbieters** verfügen und die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Klicken Sie auf **Metadatendatei hochladen**.

    ![Metadatendatei hochladen](common/upload-metadata.png)

    b. Klicken Sie auf das **Ordnerlogo**, wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen**.

    ![Metadatendatei auswählen](common/browse-upload-metadata.png)

    c. Nachdem die Metadatendatei erfolgreich hochgeladen wurde, werden die Werte **Bezeichner** und **Antwort-URL** im Textfeld des Brightidea-Abschnitts automatisch aufgefüllt:

    ![image](common/idp-intiated.png)

    > [!Note]
    > Sollten die Werte **Bezeichner** und **Antwort-URL** nicht automatisch aufgefüllt werden, geben Sie die erforderlichen Werte manuell ein.

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    ![image](common/metadata-upload-additional-signon.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.brightidea.com`

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **Brightidea einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-brightidea-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Brightidea

1. Melden Sie sich in einem anderen Webbrowserfenster unter Verwendung der Administratoranmeldeinformationen bei Brightidea an.

2. Navigieren Sie in Ihrem Brightidea-System zu **Enterprise Setup** (Unternehmenseinrichtung) -> Registerkarte **Authentication** (Authentifizierung). Dort befinden sich zwei untergeordnete Registerkarten: „Auth Selection“ (Authentifizierungsauswahl) und „SAML Profiles“ (SAML-Profile).

    ![Brightidea-Konfiguration](./media/brightidea-tutorial/configure1.png)

3. Wählen Sie **Auth Selection** (Authentifizierungsauswahl) aus. Standardmäßig werden nur zwei Standardmethoden angezeigt: „Brightidea Login“ (Brightidea-Anmeldung) und „Registration“ (Registrierung). Wenn eine SSO-Methode hinzugefügt wird, wird sie in der Liste angezeigt.

    ![Brightidea-Konfiguration](./media/brightidea-tutorial/configure2.png)

4. Wählen Sie **SAML Profiles** (SAML-Profile) aus, und gehen Sie wie folgt vor:

    ![Brightidea-Konfiguration](./media/brightidea-tutorial/configure3.png)

    a. Klicken Sie auf **Download Metadata** (Metadaten herunterladen), und laden Sie sie im Abschnitt **Grundlegende SAML-Konfiguration** des Azure-Portals hoch.

    b. Klicken Sie unter **Identity Provider Setting** (Identitätsanbietereinstellung) auf die Schaltfläche **Add New** (Neu hinzufügen), und führen Sie die folgenden Schritte aus:
    
    ![Brightidea-Konfiguration](./media/brightidea-tutorial/configure4.png)
    
   * Geben Sie den **Namen des SAML-Profils**ein (beispielsweise `Azure Ad SSO`).
    
   * Klicken Sie unter **Upload Metadata** (Metadaten hochladen) auf die Dateiauswahloption, und laden Sie die heruntergeladene Metadatendatei aus dem Azure-Portal hoch.

     > [!NOTE]
     > Nach dem Hochladen der Metadatendatei werden die restlichen Felder (**Single Sign-on Service, Identity Provider Issuer, Upload Public Key** („SSO-Dienst“, „Aussteller des Identitätsanbieters“, „Öffentlichen Schlüssel hochladen“)) automatisch aufgefüllt.

   * Geben Sie im Textfeld **Email** (E-Mail-Adresse) den Wert `mail` ein.
     
   * Geben Sie im Textfeld **Screen Name** (Anzeigename) den Wert `givenName` ein.
     
   * Klicken Sie auf **Änderungen speichern**.  

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Brightidea gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Brightidea** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste die Option **Brightidea** aus.

    ![Brightidea-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste den Eintrag **Britta Simon** aus, und klicken Sie dann am unteren Bildschirmrand auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-brightidea-test-user"></a>Erstellen eines Brightidea-Testbenutzers

In diesem Abschnitt wird in Brightidea ein Benutzer namens Britta Simon erstellt. Brightidea unterstützt die Just-In-Time-Benutzerbereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Sollte ein Benutzer noch nicht in Brightidea vorhanden sein, wird nach der Authentifizierung ein neuer Benutzer erstellt.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Brightidea“ klicken, sollten Sie automatisch bei der Brightidea-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

