---
title: 'Tutorial: Azure Active Directory-Integration mit Cisco Webex | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Cisco Webex konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05127e8ecfe68b4cb6330f838f252557bbd5e11d
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65900751"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Tutorial: Azure Active Directory-Integration in Cisco Webex

In diesem Tutorial erfahren Sie, wie Sie Cisco Webex in Azure Active Directory (Azure AD) integrieren.
Die Integration von Cisco Webex in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer auf Cisco Webex Zugriff hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Cisco Webex anzumelden (einmaliges Anmelden).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Cisco Webex konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein Cisco Webex-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Cisco Webex unterstützt **SP**-initiiertes einmaliges Anmelden.

* Cisco Webex unterstützt die **automatisierte** Benutzerbereitstellung.

## <a name="adding-cisco-webex-from-the-gallery"></a>Hinzufügen von Cisco Webex aus dem Katalog

Zum Konfigurieren der Integration von Cisco Webex in Azure AD müssen Sie Cisco Webex aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um Cisco Webex aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Cisco Webex** ein, wählen Sie im Ergebnisbereich **Cisco Webex** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Cisco Webex in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Cisco Webex mithilfe einer Testbenutzerin namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Cisco Webex eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Cisco Webex müssen die folgenden Bausteine ausgeführt werden:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Cisco Webex](#configure-cisco-webex-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Cisco Webex-Testbenutzers](#create-cisco-webex-test-user)**, um ein Pendant zu Britta Simon in Cisco Webex zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei Cisco Webex die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Cisco Webex** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Cisco Webex](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** die URL wie folgt ein: `https://web.ciscospark.com/#/signin`

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://idbroker.webex.com/<Org Id>`.

    > [!NOTE]
    > Dieser Bezeichnerwert ist nicht der tatsächliche Wert. Aktualisieren Sie den Wert mit dem tatsächlichen Bezeichner. Falls Sie über Metadaten des Dienstanbieters verfügen, laden Sie sie im Abschnitt **Grundlegende SAML-Konfiguration** hoch. Der Wert **Bezeichner (Entitäts-ID)** wird daraufhin automatisch aufgefüllt.

5. Die Cisco Webex-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. Klicken Sie auf das Symbol **Bearbeiten**, um die Attribute hinzuzufügen.

    ![image](common/edit-attribute.png)

6. Darüber hinaus wird von der Cisco Webex-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden. Führen Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die folgenden Schritte aus, um das SAML-Tokenattribut wie in der folgenden Tabelle gezeigt hinzuzufügen:
    
    | NAME |  Quellattribut|
    | ---------------|--------- |
    | uid | user.userprincipalname |

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    f. Klicken Sie auf **OK**.

    g. Klicken Sie auf **Speichern**.

7. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

8. Kopieren Sie im Abschnitt **Cisco Webex einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-cisco-webex-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Cisco Webex

1. Melden Sie sich beim [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) mit vollständigen Administratorberechtigungen an.

2. Wählen **Settings** (Einstellungen) aus, und klicken Sie im Bereich **Authentication** (Authentifizierung) auf **Modify** (Ändern).

    ![Configure single sign-on](./media/cisco-spark-tutorial/tutorial_cisco_spark_10.png)
  
3. Wählen Sie **Integrate a 3rd-party identity provider. (Advanced)** (Identitätsanbieter von Drittanbieter integrieren (Erweitert)) aus, und wechseln Sie zum nächsten Bildschirm.

4. Laden Sie auf der Seite **Import Idp Metadata** (IdP-Metadaten importieren) die Azure AD-Metadatendatei hoch, indem Sie diese entweder per Drag & Drop auf die Seite ziehen oder sie mithilfe der Option zum Durchsuchen auswählen. Wählen Sie anschließend **Require certificate signed by a certificate authority in Metadata (more secure)** (Signatur einer Zertifizierungsstelle in den Metadaten erzwingen (sicherer)) aus, und klicken Sie auf **Weiter**.

    ![Configure single sign-on](./media/cisco-spark-tutorial/tutorial_cisco_spark_11.png)

5. Wählen Sie **Test SSO Connection** (SSO-Verbindung testen) aus, und authentifizieren Sie sich, wenn eine neue Browserregisterkarte geöffnet wird, durch Ihre Anmeldung bei Azure AD.

6. Wechseln Sie zurück zur Browserregisterkarte **Cisco Cloud Collaboration Management**. Wenn der Test erfolgreich war, wählen Sie **This test was successful. Enable Single Sign-On option** (Der Test war erfolgreich. Single Sign-On aktivieren), und klicken Sie auf **Weiter**.

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Cisco Webex gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**, **Alle Anwendungen** und dann **Cisco Webex** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Cisco Webex**aus.

    ![Cisco Webex-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-cisco-webex-test-user"></a>Erstellen eines Cisco Webex-Testbenutzers

In diesem Abschnitt erstellen Sie in Cisco Webex einen Benutzer mit dem Namen Britta Simon. In diesem Abschnitt erstellen Sie in Cisco Webex einen Benutzer mit dem Namen Britta Simon.

1. Wechseln Sie zum [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/), und verwenden Sie dabei vollständige Administratorberechtigungen.

2. Klicken Sie auf **Users** und dann auf **Manage Users**.
   
    ![Configure single sign-on](./media/cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. Wählen Sie im Fenster **Manage User** (Benutzer verwalten) die Option **Manually add or modify users** (Benutzer manuell hinzufügen oder ändern) aus, und klicken Sie auf **Weiter**.

4. Wählen Sie **Names and Email address** (Namen und E-Mail-Adressen) aus. Füllen Sie das Textfeld anschließend wie folgt aus:

    ![Configure single sign-on](./media/cisco-spark-tutorial/tutorial_cisco_spark_13.png) 

    a. Geben Sie im Textfeld **Vorname** den Vornamen des Benutzers ein (beispielsweise **Britta**).

    b. Geben Sie im Textfeld **Nachname** den Nachnamen des Benutzers ein (beispielsweise **Simon**).

    c. Geben Sie im Textfeld **Email address** (E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein, z. B. **brittasimon\@contoso.com**.

5. Klicken Sie auf das Pluszeichen, um Britta Simon hinzuzufügen. Klicken Sie auf **Weiter**.

6. Klicken Sie im Fenster **Add Services for Users** (Dienste für Benutzer hinzufügen) auf **Speichern** und dann auf **Fertig stellen**.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Cisco Webex“ klicken, sollten Sie automatisch bei der Cisco Webex-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurieren der Benutzerbereitstellung](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-spark-provisioning-tutorial) 
