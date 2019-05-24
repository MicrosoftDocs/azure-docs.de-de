---
title: 'Tutorial: Azure Active Directory-Integration mit Cisco WebEx Meetings | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Cisco WebEx Meetings konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 308f745489fba2e2b539a2f2615b65228565dcf9
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65900095"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex-meetings"></a>Tutorial: Azure Active Directory-Integration mit Cisco WebEx Meetings

In diesem Tutorial erfahren Sie, wie Sie Cisco WebEx Meetings in Azure Active Directory (Azure AD) integrieren.
Die Integration von Cisco WebEx Meetings in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Cisco WebEx Meetings hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Cisco WebEx Meetings anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Cisco WebEx Meetings konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein Cisco WebEx Meetings-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Cisco WebEx Meetings unterstützt **SP**-initiiertes einmaliges Anmelden.

* Cisco WebEx Meetings unterstützt die **Just-in-Time**-Benutzerbereitstellung.

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Hinzufügen von Cisco WebEx Meetings aus dem Katalog

Zum Konfigurieren der Integration von Cisco WebEx Meetings in Azure AD müssen Sie Cisco WebEx Meetings aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um Cisco WebEx Meetings aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Cisco WebEx Meetings** ein, wählen Sie im Ergebnisbereich **Cisco WebEx Meetings** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Cisco WebEx Meetings in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden mit Azure AD bei Cisco WebEx Meetings mithilfe einer Testbenutzerin namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Cisco WebEx Meetings eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Cisco WebEx Meetings müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Cisco WebEx Meetings](#configure-cisco-webex-meetings-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Cisco WebEx Meetings-Testbenutzers](#create-cisco-webex-meetings-test-user)**, um eine Entsprechung von Britta Simon in Cisco WebEx Meetings zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Cisco WebEx Meetings die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Cisco WebEx Meetings** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie im Azure-Portal auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Laden Sie im Abschnitt **Grundlegende SAML-Konfiguration** die heruntergeladene **Metadatendatei des Dienstanbieters** herunter, und konfigurieren Sie die Anwendung folgendermaßen:

    >[!Note]
    >Sie erhalten die Metadatendatei des Dienstanbieters. Dies wird im Abschnitt **Konfigurieren des einmaligen Anmeldens für Cisco WebEx Meetings** des Tutorials beschrieben. 

    a. Klicken Sie auf **Metadatendatei hochladen**.

    ![Metadatendatei hochladen](common/upload-metadata.png)

    b. Klicken Sie auf das **Ordnerlogo**, wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen**.

    ![Metadatendatei auswählen](common/browse-upload-metadata.png)

    c. Nachdem Sie die Metadatendatei des Dienstanbieters hochgeladen haben, werden im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für **Bezeichner** und **Antwort-URL** automatisch ausgefüllt, wie unten dargestellt:

    ![SSO-Informationen zur Domäne und zu den URLs für Cisco WebEx Meetings](common/sp-identifier-reply.png)

    Fügen Sie im Textfeld **Anmelde-URL** den Wert von **Antwort-URL** ein, der automatisch aus der hochgeladenen SP-Metadatendatei ausgefüllt wird.

5. Die Cisco WebEx Meetings-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. Klicken Sie auf das Symbol  **Bearbeiten**, um die Attribute hinzuzufügen.

    ![image](common/edit-attribute.png)

6. Löschen Sie die Standardattribute aus dem Abschnitt **Benutzeransprüche**. Die Cisco WebEx Meetings-Anwendung erwartet, dass einige weitere Attribute in der SAML-Antwort zurückgegeben werden. Führen Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die folgenden Schritte aus, um das SAML-Tokenattribut wie in der folgenden Tabelle gezeigt hinzuzufügen:
    
    | NAME | Quellattribut|
    | ---------------|  --------- |
    |   firstname    | user.givenname |
    |   lastname    | user.surname |
    |   email       | user.mail |
    |   uid    | user.mail |

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![image](./media/cisco-webex-tutorial/tutorial-cisco-webex-addnewclaim.png)

    ![image](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    f. Klicken Sie auf **OK**.

    g. Klicken Sie auf **Speichern**.

7. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

8. Kopieren Sie im Abschnitt **Cisco WebEx Meetings einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-cisco-webex-meetings-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Cisco WebEx Meetings

1. Melden Sie sich bei [Cisco Cloud Collaboration Management](https://www.webex.com/go/connectadmin) mit Ihren Administratoranmeldeinformationen an.

2. Wechseln Sie zu **Security Settings** (Sicherheitseinstellungen), und navigieren Sie zu **Federated Web SSO Configuration** (Verbundkonfiguration für Web-SSO).
 
    ![Configure single sign-on](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

3. Führen Sie im Abschnitt **Federated Web SSO Configuration** (Verbundkonfiguration für Web-SSO) die folgenden Schritte aus:

    ![Configure single sign-on](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

    a. Geben Sie im Textfeld für das Verbundprotokoll den Namen Ihres Protokolls ein.

    b. Klicken Sie auf den Link **Import SAML Metadata** (SAML-Metadaten importieren), um die Metadatendatei hochzuladen, die Sie aus dem Azure-Portal heruntergeladen haben.

    c. Klicken Sie auf die Schaltfläche **Export** (Exportieren), um die Metadatendatei des Dienstanbieters herunterzuladen, und laden Sie sie im Azure-Portal im Abschnitt **SAML-Basiskonfiguration** hoch.

    d. Geben Sie im Textfeld **AuthContextClassRef** die Zeichenfolge `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` ein. Wenn Sie MFA mit Azure AD aktivieren möchten, geben Sie die beiden Werte wie folgt ein: `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`.

    e. Wählen Sie **Auto Account Creation** (Automatische Kontoerstellung) aus.

    >[!NOTE]
    >Zum Aktivieren der **Just-in-Time**-Benutzerbereitstellung müssen Sie **Auto Account Creation** (Automatische Kontoerstellung) aktivieren. Zusätzlich müssen noch SAML-Tokenattribute in der SAML-Antwort übergeben werden.

    f. Klicken Sie auf **Speichern**. 

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Cisco WebEx Meetings gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **Cisco WebEx Meetings** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Cisco WebEx Meetings** aus.

    ![Cisco WebEx Meetings-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-cisco-webex-meetings-test-user"></a>Erstellen eines Cisco WebEx Meetings-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in Cisco WebEx Meetings. Cisco WebEx Meetings unterstützt die **Just-in-Time**-Bereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Falls ein Benutzer nicht bereits in Cisco WebEx Meetings vorhanden ist, wird beim Versuch, auf Cisco WebEx Meetings zuzugreifen, ein neuer Benutzer erstellt.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Cisco WebEx Meetings“ klicken, sollten Sie automatisch bei der Cisco WebEx Meetings-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

