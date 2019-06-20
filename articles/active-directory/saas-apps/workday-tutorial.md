---
title: 'Tutorial: Azure Active Directory-Integration mit Workday | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Workday konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b9e4f5208eb1f6abb0d6fd786630c183a04ce50
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388871"
---
# <a name="tutorial-integrate-workday-with-azure-active-directory"></a>Tutorial: Integrieren von Workday in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Workday in Azure Active Directory (Azure AD) integrieren. Bei der Integration von Workday in Azure AD haben Sie folgende Möglichkeiten:

* Steuern Sie in Azure AD, wer Zugriff auf Workday hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Workday anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Workday-Abonnement, für das einmaliges Anmelden (SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung. Workday unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="adding-workday-from-the-gallery"></a>Hinzufügen von Workday aus dem Katalog

Zum Konfigurieren der Integration von Workday in Azure AD müssen Sie Workday aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Workday** in das Suchfeld ein.
1. Wählen Sie **Workday** im Ergebnisbereich aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Workday mithilfe eines Testbenutzers mit dem Namen **Britta Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Workday eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Workday müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
2. **[Konfigurieren von Workday](#configure-workday)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit der Testbenutzerin Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Workday-Testbenutzers](#create-workday-test-user)** , um ein Pendant von Britta Simon in Workday zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Workday** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie auf der Seite **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://www.workday.com`

    c. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und Antwort-URL. Ihre Antwort-URL muss eine Unterdomäne aufweisen (z.B. www, wd2, wd3, wd3-impl, wd5, wd5-impl).
    > Eine URL wie `http://www.myworkday.com` funktioniert, `http://myworkday.com` hingegen nicht. Wenden Sie sich an das [Supportteam für den Workday-Client](https://www.workday.com/en-us/partners-services/services/support.html), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

6. Die Workday-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. **nameidentifier** ist hier **user.userprincipalname** zugeordnet. Die Workday-Anwendung erwartet, dass **nameidentifier** die Werte für **user.mail**, **UPN** usw. zugeordnet sind. Sie müssen die Attributzuordnung daher bearbeiten, indem Sie auf das Symbol **Bearbeiten** klicken und die Attributzuordnung ändern.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Hier ist die Namens-ID mit Benutzerprinzipalname (user.userprincipalname) als Standard zugeordnet. Sie müssen die Namens-ID der tatsächlichen Benutzer-ID in Ihrem Workday-Konto (E-Mail-Adresse, UPN usw.) zuordnen, damit einmaliges Anmelden funktioniert.

1. Suchen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** den Eintrag **Zertifikat (Base64)** . Klicken Sie auf **Herunterladen**, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

   ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Klicken Sie zum Ändern der Optionen unter **Signieren** gemäß Ihren Anforderungen auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **SAML-Signaturzertifikat** zu öffnen.

    ![image](common/edit-certificate.png) 

    ![image](./media/workday-tutorial/signing-option.png)

    a. Wählen Sie **SAML-Antwort und -Assertion signieren** für **Signaturoption** aus.

    b. Klicken Sie unten auf der Seite auf **Speichern**.

1. Kopieren Sie im Abschnitt **Workday einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

   ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="configure-workday"></a>Konfigurieren von Workday

1. Melden Sie sich in einem anderen Webbrowserfenster auf der Workday-Unternehmenswebsite als Administrator an.

2. Geben Sie oben links auf der Startseite im **Suchfeld** den Namen **Mandanten-Setup bearbeiten – Sicherheit** ein.

    ![Mandantensicherheit bearbeiten](./media/workday-tutorial/IC782925.png "Mandantensicherheit bearbeiten")

3. Führen Sie im Abschnitt **Umleitungs-URLs** die folgenden Schritte aus:

    ![Umleitungs-URLs](./media/workday-tutorial/IC7829581.png "Umleitungs-URLs")

    a. Klicken Sie auf **Zeile hinzufügen**.

    b. Fügen Sie in die Textfelder **Login Redirect URL** (Umleitungs-URL für Anmeldung), **Timeout Redirect URL** (Umleitungs-URL für Timeout) and **Mobile Redirect URL** (Mobile Umleitungs-URL) die **Anmelde-URL** ein, die Sie im Azure-Portal im Abschnitt **Workday einrichten** kopiert haben.

    c. Fügen Sie ins Textfeld **Logout Redirect URL** (Umleitungs-URL für Abmeldung) die **Abmelde-URL** ein, die Sie im Azure-Portal im Abschnitt **Workday einrichten** kopiert haben.

    d. Wählen Sie im Textfeld **Used for Environments** (Für Umgebungen verwendet) den Umgebungsnamen aus.  

   > [!NOTE]
   > Der Wert des Umgebungsattributs wird an den Wert der Mandanten-URL gebunden:  
   > - Wenn der Domänenname der Workday-Mandanten-URL mit „impl“ beginnt (z. B. *https:\//impl.workday.com/\<tenant\>/login-saml2.flex*) muss das Attribut **Environment** (Umgebung) auf „Implementation“ (Implementierung) festgelegt werden.  
   > - Wenn der Domänenname anders beginnt, müssen Sie sich mit dem [Supportteam für den Workday-Client](https://www.workday.com/en-us/partners-services/services/support.html) in Verbindung setzen, um den passenden Wert für die **Umgebung** zu erhalten.

4. Führen Sie im Abschnitt **SAML-Setup** die folgenden Schritte aus:

    ![SAML-Setup](./media/workday-tutorial/IC782926.png "SAML-Setup")

    a.  Wählen Sie die Option **SAML-Authentifizierung aktivieren**.

    b.  Klicken Sie auf **Zeile hinzufügen**.

5. Führen Sie im Abschnitt **SAML-Identitätsanbieter** die folgenden Schritte aus:

    ![SAML-Identitätsanbieter](./media/workday-tutorial/IC7829271.png "SAML-Identitätsanbieter")

    a. Geben Sie im Textfeld **Name des Identitätsanbieters** einen Anbieternamen ein (z.B: *SPInitiatedSSO*).

    b. Kopieren Sie im Azure-Portal im Abschnitt **Workday einrichten** den **Azure AD-Bezeichner**, und fügen Sie sie in das Textfeld **Issuer** (Aussteller) ein.

    ![SAML-Identitätsanbieter](./media/workday-tutorial/IC7829272.png "SAML-Identitätsanbieter")

    c. Kopieren Sie im Azure-Portal im Abschnitt **Workday einrichten** die **Abmelde-URL**, und fügen Sie sie in das Textfeld **Logout Response URL** (Antwort-URL für Abmeldung) ein.

    d. Kopieren Sie im Azure-Portal im Abschnitt **Workday einrichten** die **Anmelde-URL**, und fügen Sie sie in das Textfeld **IdP SSO Service URL** (Dienst-URL des Identitätsanbieters für einmaliges Anmelden) ein.

    e. Wählen Sie im Textfeld **Used for Environments** (Für Umgebungen verwendet) den Umgebungsnamen aus.

    f. Klicken Sie auf **Identity Provider Public Key Certificate** (Öffentliches Schlüsselzertifikat des Identitätsanbieters), und klicken Sie dann auf **Erstellen**.

    ![Erstellen](./media/workday-tutorial/IC782928.png "Erstellen")

    g. Klicken Sie auf **Öffentlichen x509-Schlüssel erstellen**.

    ![Erstellen](./media/workday-tutorial/IC782929.png "Erstellen")

6. Führen Sie im Abschnitt **Öffentlichen x509-Schlüssel anzeigen** die folgenden Schritte aus:

    ![Öffentlichen x509-Schlüssel anzeigen](./media/workday-tutorial/IC782930.png "Öffentlichen x509-Schlüssel anzeigen")

    a. Geben Sie im Textfeld **Name** einen Namen für Ihr Zertifikat ein (z.B.: *PPE\_SP*).

    b. Geben Sie im Textfeld **Gültig ab** den „Gültig-ab“-Attributwert des Zertifikats ein.

    c.  Geben Sie im Textfeld **Gültig bis** den „Gültig-bis“-Attributwert des Zertifikats ein.

    > [!NOTE]
    > Sie können die „Gültig ab“- und „Gültig bis“-Daten per Doppelklick aus dem heruntergeladenen Zertifikat abrufen.  Die Datumsangaben sind in der Registerkarte **Details** aufgeführt.
    >
    >

    d.  Öffnen Sie das Base64-codierte Zertifikat in Editor, und kopieren Sie seinen Inhalt.

    e.  Fügen Sie in der Textbox **Zertifikat** den Inhalt der Zwischenablage ein.

    f.  Klicken Sie auf **OK**.

7. Führen Sie die folgenden Schritte aus:

    ![SSO-Konfiguration](./media/workday-tutorial/WorkdaySSOConfiguratio.png "SSO-Konfiguration")

    a.  Geben Sie in das Textfeld **Dienstanbieter-ID** Folgendes ein: **https://www.workday.com** .

    b. Wählen Sie **SP-initiierte Authentifizierungsanfrage nicht verkleinern**.

    c. Wählen Sie als **Authentication Request Signature Method** (Signaturmethode für Authentifizierungsanfragen) die Option **SHA256** aus.

    ![Signaturmethode für Authentifizierungsanfragen](./media/workday-tutorial/WorkdaySSOConfiguration.png "Signaturmethode für Authentifizierungsanfragen") 

    d. Klicken Sie auf **OK**.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Stellen Sie sicher, dass Sie das einmalige Anmelden ordnungsgemäß eingerichtet haben. Falls Sie das einmalige Anmelden mit der falschen Einrichtung aktivieren, können Sie die Anwendung möglicherweise nicht mit Ihren Anmeldeinformationen öffnen und werden gesperrt. In diesem Fall bietet Workday eine Sicherungs-URL für die Anmeldung, mit der Benutzer ihren normalen Benutzernamen mit Kennwort im folgenden Format verwenden können: [Ihre Workday-URL]/login.flex?redirect=n

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal eine Testbenutzerin mit dem Namen Britta Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `Britta Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `BrittaSimon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Workday gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Workday** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste den Eintrag **Britta Simon** aus, und klicken Sie anschließend im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-workday-test-user"></a>Erstellen eines Workday-Testbenutzers

In diesem Abschnitt erstellen Sie in Workday einen Benutzer namens Britta Simon. Arbeiten Sie mit dem [Supportteam für den Workday-Client](https://www.workday.com/en-us/partners-services/services/support.html) zusammen, um die Benutzer zur Workday-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens

Wenn Sie im Zugriffsbereich die Kachel „Workday“ auswählen, sollten Sie automatisch bei der Workday-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
