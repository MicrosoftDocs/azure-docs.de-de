---
title: 'Tutorial: Azure Active Directory-Integration mit AirWatch | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und AirWatch konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e20a298c34a7b2723963396f8ccaafaa9472e19a
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65899204"
---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Tutorial: Azure Active Directory-Integration mit AirWatch

In diesem Tutorial erfahren Sie, wie Sie AirWatch in Azure Active Directory (Azure AD) integrieren.
Die Integration von AirWatch in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf AirWatch hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei AirWatch anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit AirWatch konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein AirWatch-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* AirWatch unterstützt das **SP-initiierte** einmalige Anmelden.

## <a name="adding-airwatch-from-the-gallery"></a>Hinzufügen von AirWatch aus dem Katalog

Zum Konfigurieren der Integration von AirWatch in Azure AD müssen Sie AirWatch aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um AirWatch aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **AirWatch** ein, wählen Sie im Ergebnisbereich **AirWatch** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![AirWatch in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit AirWatch mithilfe einer Testbenutzerin namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in AirWatch eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei AirWatch müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für AirWatch](#configure-airwatch-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Erstellen eines AirWatch-Testbenutzers](#create-airwatch-test-user)**, um eine Entsprechung von Britta Simon in AirWatch zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
5. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit AirWatch die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **AirWatch** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für AirWatch](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** folgenden Wert ein: `AirWatch`.

    > [!NOTE]
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Wenden Sie sich an das [Supportteam von AirWatch](https://www.air-watch.com/company/contact-us/), um diesen Wert zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Die AirWatch-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Sie können die Werte dieser Attribute im Abschnitt **Benutzerattribute** auf der Anwendungsintegrationsseite verwalten. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![image](common/edit-attribute.png)

6. Bearbeiten Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die Ansprüche mithilfe des Symbols zum **Bearbeiten**, oder fügen Sie die Ansprüche über **Neuen Anspruch hinzufügen** hinzu, um das SAML-Tokenattribut wie in der obigen Abbildung gezeigt zu konfigurieren. Führen Sie dann die folgenden Schritte aus:

    | NAME |  Quellattribut|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

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

8. Kopieren Sie im Abschnitt **AirWatch einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-airwatch-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für AirWatch

1. Melden Sie sich in einem anderen Webbrowserfenster bei der AirWatch-Unternehmenswebsite als Administrator an.

2. Klicken Sie im linken Navigationsbereich auf **Konten**, und klicken Sie dann auf **Administratoren**.

   ![Administratoren](./media/airwatch-tutorial/ic791920.png "Administratoren")

3. Erweitern Sie das Menü **Einstellungen**, und klicken Sie dann auf **Verzeichnisdienste**.

   ![Einstellungen](./media/airwatch-tutorial/ic791921.png "Einstellungen")

4. Klicken Sie auf die Registerkarte **Benutzer**, geben Sie im Textfeld **Basis-DN** Ihren Domänennamen ein, und klicken Sie dann auf **Speichern**.

   ![Benutzer](./media/airwatch-tutorial/ic791922.png "Benutzer")

5. Klicken Sie auf die Registerkarte **Server** .

   ![Server](./media/airwatch-tutorial/ic791923.png "Server")

6. Führen Sie die folgenden Schritte aus:

    ![Hochladen](./media/airwatch-tutorial/ic791924.png "Hochladen")   

    a. Wählen Sie unter **Verzeichnistyp** die Option **Keiner** aus.

    b. Aktivieren Sie **Use SAML For Authentication**.

    c. Klicken Sie auf **Upload**, um das heruntergeladene Zertifikat hochzuladen.

7. Führen Sie im Abschnitt **Request** die folgenden Schritte aus:

    ![Anforderung](./media/airwatch-tutorial/ic791925.png "Anforderung")  

    a. Wählen Sie als **Anforderungsbindungstyp** die Option **POST** aus.

    b. Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für Airwatch** den Wert für **Anmelde-URL**, und fügen Sie ihn in das Textfeld **SSO-URL des Identitätsanbieters** ein.

    c. Wählen Sie als **NameID-Format** die Option **E-Mail-Adresse** aus.

    d. Klicken Sie auf **Speichern**.

8. Klicken Sie erneut auf die Registerkarte **User** .

    ![Benutzer](./media/airwatch-tutorial/ic791926.png "Benutzer")

9. Führen Sie im Abschnitt **Attribute** die folgenden Schritte aus:

    ![Attribut](./media/airwatch-tutorial/ic791927.png "Attribut")

    a. Geben Sie im Textfeld **Objektbezeichner** Folgendes ein: `http://schemas.microsoft.com/identity/claims/objectidentifier`.

    b. Geben Sie im Textfeld **Benutzername** Folgendes ein: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    c. Geben Sie im Textfeld **Anzeigename** Folgendes ein: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    d. Geben Sie im Textfeld **Vorname** Folgendes ein: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. Geben Sie im Textfeld **Nachname** Folgendes ein: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. Geben Sie in das Textfeld **E-Mail** die Adresse `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` ein.

    g. Klicken Sie auf **Speichern**.

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf AirWatch gewähren.

1. Wählen Sie im Azure-Portal die Option **Unternehmensanwendungen** aus, und wählen Sie dann **Alle Anwendungen** und **AirWatch** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **AirWatch** aus.

    ![AirWatch-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-airwatch-test-user"></a>Erstellen eines AirWatch-Testbenutzers

Damit sich Azure AD-Benutzer bei AirWatch anmelden können, müssen sie in AirWatch bereitgestellt werden. Im Fall von AirWatch ist die Bereitstellung eine manuelle Aufgabe.

**Um die Benutzerbereitstellung zu konfigurieren, führen Sie die folgenden Schritte durch:**

1. Melden Sie sich bei der **AirWatch** -Unternehmenswebsite als Administrator an.

2. Klicken Sie im Navigationsbereich links auf **Konten**, und klicken Sie dann auf **Benutzer**.
  
   ![Benutzer](./media/airwatch-tutorial/ic791929.png "Benutzer")

3. Klicken Sie im Menü **Benutzer** auf **Listenansicht**, und klicken Sie dann auf **Hinzufügen \> Benutzer hinzufügen**.
  
   ![Benutzer hinzufügen](./media/airwatch-tutorial/ic791930.png "Benutzer hinzufügen")

4. Führen Sie im Dialogfeld **Add / Edit User** die folgenden Schritte aus:

   ![Benutzer hinzufügen](./media/airwatch-tutorial/ic791931.png "Benutzer hinzufügen")

   a. Geben Sie in die Textfelder **Benutzername**, **Kennwort**, **Kennwort bestätigen**, **Vorname**, **Nachname** und **E-Mail-Adresse** die Informationen eines gültigen Azure Active Directory-Kontos ein, das Sie bereitstellen möchten.

   b. Klicken Sie auf **Speichern**.

> [!NOTE]
> Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von AirWatch-Benutzerkonten oder mithilfe der von AirWatch bereitgestellten APIs erstellen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „AirWatch“ klicken, sollten Sie automatisch bei der AirWatch-Anwendung angemeldet werden, für die Sie SSO eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
