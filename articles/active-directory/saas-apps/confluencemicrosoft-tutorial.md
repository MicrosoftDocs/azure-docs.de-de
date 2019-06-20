---
title: 'Tutorial: Azure Active Directory-Integration mit Confluence SAML SSO by Microsoft | Microsoft-Dokumentation'
description: In diesem Artikel erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Confluence SAML SSO by Microsoft konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9c422e93a6768b764873f21ad9eab6fad4a868e
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474140"
---
# <a name="tutorial-azure-active-directory-integration-with-confluence-saml-sso-by-microsoft"></a>Tutorial: Azure Active Directory-Integration mit Confluence SAML SSO by Microsoft

In diesem Tutorial erfahren Sie, wie Sie Confluence SAML SSO by Microsoft in Azure Active Directory (Azure AD) integrieren.
Die Integration von Confluence SAML SSO by Microsoft in Azure AD bietet Ihnen folgende Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Confluence SAML SSO by Microsoft hat.
* Sie können es Ihren Benutzern ermöglichen, dass sie mit ihren Azure AD-Konten automatisch bei Confluence SAML SSO by Microsoft angemeldet werden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="description"></a>Beschreibung:

Verwenden Sie Ihr Microsoft Azure Active Directory-Konto mit einem Atlassian Confluence-Server, um einmaliges Anmelden zu ermöglichen. Auf diese Weise können sich alle Benutzer in Ihrer Organisation mit den Azure AD-Anmeldeinformationen bei der Confluence-Anwendung anmelden. Dieses Plug-In nutzt SAML 2.0 für den Verbund.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Confluence SAML SSO by Microsoft konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Eine Confluence-Serveranwendung, die auf einem 64-Bit-Server unter Windows installiert ist (lokal oder in der Cloud-IaaS-Infrastruktur)
- Einen HTTPS-fähigen Confluence-Server
- Beachten Sie, dass die unterstützten Versionen für das Confluence-Plug-In weiter unten aufgeführt werden.
- Der Confluence-Server sollte über das Internet erreichbar sein, insbesondere zur Authentifizierung über die Azure AD-Anmeldeseite. Außerdem sollte er in der Lage sein, das Token von Azure AD zu erhalten.
- In Confluence eingerichtete Administratoranmeldeinformationen
- WebSudo in Confluence deaktiviert
- Einen in der Confluence-Serveranwendung erstellten Testbenutzer

> [!NOTE]
> Zum Testen der Schritte in diesem Tutorial wird empfohlen, keine Produktionsumgebung von Confluence zu verwenden. Testen Sie die Integration zuerst in der Entwicklungs- oder Stagingumgebung der Anwendung, und verwenden Sie erst danach die Produktionsumgebung.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie über keine Azure AD-Testumgebung verfügen, können Sie hier eine einmonatige Testversion anfordern: [Kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-confluence"></a>Unterstützte Confluence-Versionen

Zum aktuellen Zeitpunkt werden die folgenden Confluence-Versionen unterstützt:

- Confluence: 5.0 bis 5.10
- Confluence: 6.0.1
- Confluence: 6.1.1
- Confluence: 6.2.1
- Confluence: 6.3.4
- Confluence: 6.4.0
- Confluence: 6.5.0
- Confluence: 6.6.2
- Confluence: 6.7.0
- Confluence: 6.8.1
- Confluence: 6.9.0
- Confluence: 6.10.0
- Confluence: 6.11.0
- Confluence: 6.12.0
- Confluence: 6.15.3

> [!NOTE]
> Beachten Sie, dass unser Confluence-Plug-In auch unter Ubuntu Version 16.04 funktioniert.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Confluence SAML SSO by Microsoft unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Hinzufügen von Confluence SAML SSO by Microsoft aus dem Katalog

Zum Konfigurieren der Integration von Confluence SAML SSO by Microsoft in Azure AD müssen Sie Confluence SAML SSO by Microsoft aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Confluence SAML SSO by Microsoft aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Confluence SAML SSO by Microsoft** ein, wählen Sie im Ergebnisbereich **Confluence SAML SSO by Microsoft** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Confluence SAML SSO by Microsoft in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Confluence SAML SSO by Microsoft mithilfe einer Testbenutzerin namens **Britta Simon**.
Damit das einmalige Anmelden funktioniert, muss zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Confluence SAML SSO by Microsoft eine Linkbeziehung eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens mit Azure AD bei Confluence SAML SSO by Microsoft müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Confluence SAML SSO by Microsoft](#configure-confluence-saml-sso-by-microsoft-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Confluence SAML SSO by Microsoft-Testbenutzers](#create-confluence-saml-sso-by-microsoft-test-user)** , um in Confluence SAML SSO by Microsoft eine Entsprechung von Britta Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens mit Azure AD bei Confluence SAML SSO by Microsoft die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Confluence SAML SSO by Microsoft** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Confluence SAML SSO by Microsoft](common/sp-identifier-reply.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein: `https://<domain:port>/`.

    c. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch den tatsächlichen Bezeichner, die Antwort-URL und die Anmelde-URL. Der Port ist optional, sofern es sich um eine benannte URL handelt. Diese Werte werden während der Konfiguration des Confluence-Plug-Ins empfangen, die später im Tutorial beschrieben wird.

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

### <a name="configure-confluence-saml-sso-by-microsoft-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Confluence SAML SSO by Microsoft

1. Melden Sie sich in einem anderen Webbrowserfenster bei Ihrer Confluence-Instanz als Administrator an.

2. Fahren Sie mit dem Mauszeiger über das Zahnrad, und klicken Sie auf die **Add-Ons**.

    ![Configure single sign-on](./media/confluencemicrosoft-tutorial/addon1.png)

3. Laden Sie das Plug-In aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56503) herunter. Laden Sie das von Microsoft bereitgestellte Plug-In manuell mithilfe des Menüs **Add-On hochladen** hoch. Das Herunterladen von Plug-Ins wird unter [Microsoft-Servicevertrag](https://www.microsoft.com/servicesagreement/) beschrieben.

    ![Configure single sign-on](./media/confluencemicrosoft-tutorial/addon12.png)

4. Sobald das Plug-In installiert ist, wird es im Abschnitt **Add-Ons verwalten** unter **User Installed** (Vom Benutzer installiert) angezeigt. Klicken Sie auf **Konfigurieren**, um das neue Plug-In zu konfigurieren.

    ![Configure single sign-on](./media/confluencemicrosoft-tutorial/addon13.png)

5. Führen Sie auf der Konfigurationsseite die folgenden Schritte aus:

    ![Configure single sign-on](./media/confluencemicrosoft-tutorial/addon53.png)

    > [!TIP]
    > Stellen Sie sicher, dass der App nur ein Zertifikat zugeordnet wird, um einen Fehler bei der Auflösung der Metadaten zu vermeiden. Bei mehreren Zertifikaten erhält der Administrator nach dem Auflösen der Metadaten eine Fehlermeldung.

    1. Fügen Sie im Textfeld **Metadaten-URL** den Wert für die **Verbundmetadaten-URL der App** ein, den Sie aus dem Azure-Portal kopiert haben, und klicken Sie auf die Schaltfläche **Auflösen**. Die IdP-Metadaten-URL wird gelesen, und alle Felder werden aufgefüllt.

    1. Kopieren Sie die Werte von **Bezeichner, Antwort-URL und Anmelde-URL**. Fügen Sie sie anschließend in die Textfelder **Bezeichner, Antwort-URL und Anmelde-URL** bzw. im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ein.

    1. Geben Sie in **Login Button Name** (Name der Anmeldeschaltfläche) den Schaltflächennamen ein, der auf dem Anmeldebildschirm für Ihre Benutzer angezeigt werden soll.

    1. Wählen Sie unter **SAML User ID Locations** (Speicherorte der SAML-Benutzer-ID) entweder die Option **User ID is in the NameIdentifier element of the Subject statement** (Benutzer-ID ist im NameIdentifier-Element der Subject-Anweisung enthalten) oder **User ID is in an Attribute element** (Benutzer-ID ist in einem Attribute-Element enthalten) aus.  Diese ID muss die Confluence-Benutzer-ID sein. Wenn die Benutzer-ID nicht übereinstimmt, ist eine Anmeldung nicht möglich. 

       > [!Note]
       > Der Standardspeicherort der SAML-Benutzer-ID ist „Name Identifier“. Sie können dies in ein Attribut ändern und den entsprechenden Attributnamen eingeben.
    
    1. Wenn Sie die Option **User ID is in an Attribute element** (Benutzer-ID ist in einem Attribute-Element enthalten) auswählen, geben Sie im Textfeld **Attributname** den Namen des Attributs ein, in dem die Benutzer-ID erwartet wird. 

    1. Wenn Sie die Verbunddomäne von Azure AD (z.B. AD FS usw.) verwenden, klicken Sie auf die Option **Startbereichserkennung aktivieren**, und konfigurieren Sie den **Domänennamen**.
    
    1. Geben Sie in **Domänenname** den Domänennamen für den Fall einer auf AD FS basierenden Anmeldung ein.

    1. Setzen Sie ein Häkchen bei **Einmaliges Abmelden aktivieren**, wenn ein Benutzer beim Abmelden von Confluence bei Azure AD abgemeldet werden soll. 

    1. Aktivieren Sie das Kontrollkästchen **Force Azure Login** (Azure-Anmeldung erzwingen), wenn Sie für die Anmeldung ausschließlich Azure AD-Anmeldeinformationen verwenden möchten.
    
       > [!Note]
       > Fügen Sie den Abfrageparameter in der Browser-URL hinzu, um das Standardanmeldeformular für die Administratoranmeldung auf der Anmeldeseite zu aktivieren, wenn „Force Azure Login“ (Azure-Anmeldung erzwingen) aktiviert ist.
       > `https://<domain:port>/login.action?force_azure_login=false`
    
    1. Klicken Sie auf die Schaltfläche **Save**, um die Änderungen zu speichern.

       > [!NOTE]
       > Weitere Informationen zur Installation und Problembehandlung finden Sie im [Administratorhandbuch zum MS Confluence SSO-Connector](../ms-confluence-jira-plugin-adminguide.md). Es enthält außerdem [FAQs](../ms-confluence-jira-plugin-faq.md) zur weiteren Unterstützung.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** den Namen `brittasimon\@yourcompanydomain.extension` ein. Beispiel: BrittaSimon@contoso.com.

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Confluence SAML SSO by Microsoft gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **Confluence SAML SSO by Microsoft**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Confluence SAML SSO by Microsoft** aus.

    ![Confluence SAML SSO by Microsoft-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Erstellen eines Confluence SAML SSO by Microsoft-Testbenutzers

Um es Azure AD-Benutzern zu ermöglichen, sich auf dem lokalen Confluence-Server anzumelden, müssen sie in Confluence SAML SSO by Microsoft bereitgestellt werden. Bei Confluence SAML SSO by Microsoft ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrem lokalen Confluence-Server als Administrator an.

2. Fahren Sie mit dem Mauszeiger über das Zahnrad, und klicken Sie auf **Benutzerverwaltung**.

    ![Mitarbeiter hinzufügen](./media/confluencemicrosoft-tutorial/user1.png) 

3. Klicken Sie im Abschnitt „Benutzer“ auf die Registerkarte **Benutzer hinzufügen**. Führen Sie auf der Dialogfeldseite **Benutzer hinzufügen** die folgenden Schritte aus:

    ![Mitarbeiter hinzufügen](./media/confluencemicrosoft-tutorial/user2.png) 

    a. Geben Sie im Textfeld **Benutzername** die E-Mail-Adresse des Benutzers, z.B. von Britta Simon, ein.

    b. Geben Sie im Textfeld **Vollständiger Name** den vollständigen Namen des Benutzers, z.B. „Britta Simon“, ein.

    c. Geben Sie im Textfeld **E-Mail-Adresse** die E-Mail-Adresse des Benutzers, z.B. Brittasimon@contoso.com, ein.

    d. Geben Sie im Textfeld **Kennwort** das Kennwort von Britta Simon ein.

    e. Klicken Sie auf **Kennwort bestätigen**, um das Kennwort erneut einzugeben.

    f. Klicken Sie auf die Schaltfläche **Hinzufügen**.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Confluence SAML SSO by Microsoft“ klicken, sollten Sie automatisch bei Ihrer Confluence SAML SSO by Microsoft-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

