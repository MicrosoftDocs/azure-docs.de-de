---
title: 'Tutorial: Azure Active Directory-Integration mit JIRA SAML SSO by Microsoft (V5.2) | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden für Azure Active Directory und JIRA SAML SSO by Microsoft (Version 5.2) konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d0c00408-f9b8-4a79-bccc-c346a7331845
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 003666d5bb3c309e501bcf76a15beb47340f9150
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64708735"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Tutorial: Azure Active Directory-Integration mit JIRA SAML SSO by Microsoft (V5.2)

In diesem Tutorial erfahren Sie, wie Sie JIRA SAML SSO by Microsoft (Version 5.2) in Azure Active Directory (Azure AD) integrieren.
Die Integration von JIRA SAML SSO by Microsoft (Version 5.2) in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf JIRA SAML SSO by Microsoft (Version 5.2) hat.
* Sie können es Ihren Benutzern ermöglichen, dass sie mit ihren Azure AD-Konten automatisch bei JIRA SAML SSO by Microsoft (V5.2) angemeldet werden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="description"></a>BESCHREIBUNG

Verwenden Sie Ihr Microsoft Azure Active Directory-Konto mit einem Atlassian JIRA-Server, um einmaliges Anmelden zu ermöglichen. Auf diese Weise können sich alle Benutzer in Ihrer Organisation mit den Azure AD-Anmeldeinformationen bei der JIRA-Anwendung anmelden. Dieses Plug-In nutzt SAML 2.0 für den Verbund.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in JIRA SAML SSO by Microsoft (Version 5.2) konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- JIRA Core und Software 5.2 muss unter einer 64-Bit-Version von Windows installiert und konfiguriert werden.
- JIRA-Server mit HTTPS-Aktivierung
- Beachten Sie, dass die unterstützten Versionen für das JIRA-Plug-In weiter unten aufgeführt sind.
- Der JIRA-Server sollte über das Internet erreichbar sein, insbesondere zur Authentifizierung über die Azure AD-Anmeldeseite. Außerdem sollte er in der Lage sein, das Token von Azure AD zu erhalten.
- In JIRA eingerichtete Administratoranmeldeinformationen
- WebSudo-Deaktivierung in JIRA
- Erstellter Testbenutzer in der JIRA-Serveranwendung

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung mit JIRA zu verwenden. Testen Sie die Integration als Erstes in der Entwicklungs- oder Stagingumgebung der Anwendung, und verwenden Sie erst danach die Produktionsumgebung.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie über keine Azure AD-Testumgebung verfügen, können Sie hier eine einmonatige Testversion anfordern: [Kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-jira"></a>Unterstützte JIRA-Versionen

* JIRA Core und Software: 5,2
* JIRA unterstützt auch 6.0 bis 7.12. Weitere Informationen finden Sie unter [JIRA SAML SSO by Microsoft](jiramicrosoft-tutorial.md).

> [!NOTE]
> Beachten Sie, dass unser JIRA-Plug-In auch unter Ubuntu Version 16.04 funktioniert.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* JIRA SAML SSO by Microsoft (V5.2) unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>Hinzufügen von JIRA SAML SSO by Microsoft (V5.2) aus dem Katalog

Zum Konfigurieren der Integration von JIRA SAML SSO by Microsoft (Version 5.2) in Azure AD müssen Sie JIRA SAML SSO by Microsoft (Version 5.2) aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**So fügen Sie JIRA SAML SSO by Microsoft (Version 5.2) aus dem Katalog hinzu**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **JIRA SAML SSO by Microsoft (V5.2)** ein, wählen Sie im Ergebnisbereich **JIRA SAML SSO by Microsoft (V5.2)** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![JIRA SAML SSO by Microsoft (V5.2) in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit JIRA SAML SSO by Microsoft (V5.2) anhand eines Testbenutzers namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in JIRA SAML SSO by Microsoft (V5.2) eine Linkbeziehung eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit JIRA SAML SSO by Microsoft (Version 5.2) zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für JIRA SAML SSO by Microsoft (V5.2)](#configure-jira-saml-sso-by-microsoft-v52-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines JIRA SAML SSO by Microsoft (V5.2)-Testbenutzers](#create-jira-saml-sso-by-microsoft-v52-test-user)** , um eine Entsprechung für Britta Simon in JIRA SAML SSO by Microsoft (V5.2) zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie z um Konfigurieren des einmaligen Anmeldens von Azure AD mit JIRA SAML SSO by Microsoft (V5.2) die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **JIRA SAML SSO by Microsoft (V5.2)** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für JIRA SAML SSO by Microsoft (V5.2)](common/sp-identifier-reply.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein: `https://<domain:port>/`.

    c. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch den tatsächlichen Bezeichner, die Antwort-URL und die Anmelde-URL. Der Port ist optional, sofern es sich um eine benannte URL handelt. Diese Werte werden während der Konfiguration des JIRA-Plug-Ins empfangen, die später im Tutorial beschrieben wird.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

### <a name="configure-jira-saml-sso-by-microsoft-v52-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für JIRA SAML SSO by Microsoft (V5.2)

1. Melden Sie sich in einem anderen Webbrowserfenster bei Ihrer JIRA-Instanz als Administrator an.

2. Fahren Sie mit dem Mauszeiger über das Zahnrad, und klicken Sie auf die **Add-Ons**.

    ![Configure single sign-on](./media/jira52microsoft-tutorial/addon1.png)

3. Klicken Sie im Registerkartenabschnitt „Add-Ons“ auf **Add-Ons verwalten**.

    ![Configure single sign-on](./media/jira52microsoft-tutorial/addon7.png)

4. Laden Sie das Plug-In aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56521) herunter. Laden Sie das von Microsoft bereitgestellte Plug-In manuell mithilfe des Menüs **Add-On hochladen** hoch. Das Herunterladen von Plug-Ins wird unter [Microsoft-Servicevertrag](https://www.microsoft.com/servicesagreement/) beschrieben.

    ![Configure single sign-on](./media/jira52microsoft-tutorial/addon12.png)

5. Sobald das Plug-In installiert ist, wird es im Abschnitt **User-installed add-ons** (Vom Benutzer installierte Add-Ons) angezeigt. Klicken Sie auf **Konfigurieren**, um das neue Plug-In zu konfigurieren.

    ![Configure single sign-on](./media/jira52microsoft-tutorial/addon13.png)

6. Führen Sie auf der Konfigurationsseite die folgenden Schritte aus:

    ![Configure single sign-on](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > Stellen Sie sicher, dass der App nur ein Zertifikat zugeordnet wird, um einen Fehler bei der Auflösung der Metadaten zu vermeiden. Bei mehreren Zertifikaten erhält der Administrator nach dem Auflösen der Metadaten eine Fehlermeldung.

    a. Fügen Sie im Textfeld **Metadaten-URL** den Wert für die **Verbundmetadaten-URL der App** ein, den Sie aus dem Azure-Portal kopiert haben, und klicken Sie auf die Schaltfläche **Auflösen**. Die IdP-Metadaten-URL wird gelesen, und alle Felder werden aufgefüllt.

    b. Kopieren Sie die Werte von **Bezeichner, Antwort-URL und Anmelde-URL**. Fügen Sie sie anschließend in die Textfelder **Bezeichner, Antwort-URL und Anmelde-URL** bzw. im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ein.

    c. Geben Sie in **Login Button Name** (Name der Anmeldeschaltfläche) den Schaltflächennamen ein, der auf dem Anmeldebildschirm für Ihre Benutzer angezeigt werden soll.

    d. Wählen Sie unter **SAML User ID Locations** (Speicherorte der SAML-Benutzer-ID) entweder die Option **User ID is in the NameIdentifier element of the Subject statement** (Benutzer-ID ist im NameIdentifier-Element der Subject-Anweisung enthalten) oder **User ID is in an Attribute element** (Benutzer-ID ist in einem Attribute-Element enthalten) aus.  Diese ID muss die JIRA-Benutzer-ID sein. Wenn die Benutzer-ID nicht übereinstimmt, ist eine Anmeldung nicht möglich.

    > [!Note]
    > Der Standardspeicherort der SAML-Benutzer-ID ist „Name Identifier“. Sie können dies in ein Attribut ändern und den entsprechenden Attributnamen eingeben.

    e. Wenn Sie die Option **User ID is in an Attribute element** (Benutzer-ID ist in einem Attribute-Element enthalten) auswählen, geben Sie im Textfeld **Attributname** den Namen des Attributs ein, in dem die Benutzer-ID erwartet wird. 

    f. Wenn Sie die Verbunddomäne von Azure AD (z.B. AD FS usw.) verwenden, klicken Sie auf die Option **Startbereichserkennung aktivieren**, und konfigurieren Sie den **Domänennamen**.

    g. Geben Sie in **Domänenname** den Domänennamen für den Fall einer auf AD FS basierenden Anmeldung ein.

    h. Setzen Sie ein Häkchen bei **Einmaliges Abmelden aktivieren**, wenn ein Benutzer beim Abmelden von JIRA bei Azure AD abgemeldet werden soll. 

    i. Klicken Sie auf die Schaltfläche **Save**, um die Änderungen zu speichern.

    > [!NOTE]
    > Weitere Informationen zur Installation und Problembehandlung finden Sie im [MS JIRA SSO Connector Admin Guide](../ms-confluence-jira-plugin-adminguide.md) (Administratorhandbuch zum MS JIRA SSO-Connector), das auch die [FAQs](../ms-confluence-jira-plugin-faq.md) zur weiteren Unterstützung enthält.

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

In diesem Abschnitt gewähren Sie Britta Simon Zugriff auf JIRA SAML SSO by Microsoft (Version 5.2), damit sie das einmalige Anmelden von Azure verwenden kann.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **JIRA SAML SSO by Microsoft (V5.2)** .

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **JIRA SAML SSO by Microsoft (V5.2)** aus.

    ![JIRA SAML SSO by Microsoft (V5.2)-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-jira-saml-sso-by-microsoft-v52-test-user"></a>Erstellen eines JIRA SAML SSO by Microsoft (V5.2)-Testbenutzers

Damit sich Azure AD-Benutzer bei einem lokalen JIRA-Server anmelden können, müssen sie auf einem lokalen JIRA-Server bereitgestellt werden.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrem lokalen JIRA-Server als Administrator an.

2. Fahren Sie mit dem Mauszeiger über das Zahnrad, und klicken Sie auf **Benutzerverwaltung**.

    ![Mitarbeiter hinzufügen](./media/jira52microsoft-tutorial/user1.png)

3. Sie werden zur Seite „Administratorzugriff“ umgeleitet, um Ihr **Kennwort** einzugeben. Klicken Sie anschließend auf die Schaltfläche **Bestätigen**.

    ![Mitarbeiter hinzufügen](./media/jira52microsoft-tutorial/user2.png)

4. Klicken Sie im Registerkartenabschnitt **Benutzerverwaltung** auf **Benutzer erstellen**.

    ![Mitarbeiter hinzufügen](./media/jira52microsoft-tutorial/user3.png) 

5. Führen Sie auf der Dialogfeldseite **Neuen Benutzer erstellen** die folgenden Schritte durch:

    ![Mitarbeiter hinzufügen](./media/jira52microsoft-tutorial/user4.png)

    a. Geben Sie im Textfeld **E-Mail-Adresse** die E-Mail-Adresse des Benutzers, z.B. Brittasimon@contoso.com, ein.

    b. Geben Sie im Textfeld **Vollständiger Name** den vollständigen Namen des Benutzers, z.B. „Britta Simon“, ein.

    c. Geben Sie im Textfeld **Benutzername** die E-Mail-Adresse des Benutzers, z.B. Brittasimon@contoso.com, ein.

    d. Geben Sie im Textfeld **Kennwort** das Kennwort des Benutzers ein.

    e. Klicken Sie auf **Benutzer erstellen**.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „JIRA SAML SSO by Microsoft (V5.2)“ klicken, sollten Sie automatisch bei der JIRA SAML SSO by Microsoft (V5.2)-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
