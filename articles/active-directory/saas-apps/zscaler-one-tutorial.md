---
title: 'Tutorial: Azure Active Directory-Integration mit Zscaler One | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Zscaler One konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: 3a9279189e3767b035256726a1da53b9edc8392f
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608985"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-one"></a>Tutorial: Azure Active Directory-Integration in Zscaler One

In diesem Tutorial erfahren Sie, wie Sie Zscaler One in Azure Active Directory (Azure AD) integrieren.
Die Integration von Zscaler One in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Zscaler One hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Zscaler One anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in Zscaler One konfigurieren zu können, ist Folgendes erforderlich:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Zscaler One-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Zscaler One unterstützt **SP-initiiertes** einmaliges Anmelden.

* Zscaler One unterstützt die **Just-in-Time**-Benutzerbereitstellung.

## <a name="adding-zscaler-one-from-the-gallery"></a>Hinzufügen von Zscaler One aus dem Katalog

Zum Konfigurieren der Integration von Zscaler One in Azure AD müssen Sie Zscaler One aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Zscaler One aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld den Namen **Zscaler One** ein, wählen Sie im Ergebnisbereich den Eintrag **Zscaler One** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Zscaler One in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Zscaler One mithilfe eines Testbenutzers namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Zscaler One eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Zscaler One müssen die folgenden Schritte ausgeführt werden:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Zscaler One](#configure-zscaler-one-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Zscaler One-Testbenutzers](#create-zscaler-one-test-user)** , um ein Pendant von Britta Simon in Zscaler One zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in Zscaler One die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Zscaler One** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Zscaler One](common/sp-signonurl.png)

    Geben Sie im Textfeld **Anmelde-URL** die von Ihren Benutzern für die Anmeldung bei Ihrer Zscaler One-Anwendung verwendete URL ein.

    > [!NOTE]
    > Sie ersetzen diesen Wert durch die tatsächliche Anmelde-URL. Den Wert erhalten Sie vom [Supportteam für den Zscaler One-Client](https://www.zscaler.com/company/contact). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Ihre Zscaler One-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. Klicken Sie auf das Symbol **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![Screenshot: Benutzerattribute mit ausgewähltem Bearbeitungssymbol](common/edit-attribute.png)

6. Darüber hinaus wird von der Zscaler One-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden. Führen Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die folgenden Schritte aus, um das SAML-Tokenattribut wie in der folgenden Tabelle gezeigt hinzuzufügen:
    
    | Name | Quellattribut |
    | ---------| ------------ |
    | memberOf | user.assignedroles |

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![Screenshot: Benutzeransprüche mit Option zum Hinzufügen eines neuen Anspruchs](common/new-save-attribute.png)

    ![Screenshot des Dialogfelds „Benutzeransprüche verwalten“, in dem Sie die hier beschriebenen Werte eingeben können](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.
    
    f. Klicken Sie auf **Speichern**.

    > [!NOTE]
    > Klicken Sie [hier](../develop/active-directory-enterprise-app-role-management.md), um herauszufinden, wie Sie die Rolle in Azure AD konfigurieren.

7. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

8. Kopieren Sie im Abschnitt **Zscaler One einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-zscaler-one-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Zscaler One

1. Wenn Sie die Konfiguration in Zscaler One automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Zscaler One einrichten**, um zur Zscaler One-Anwendung weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Zscaler One anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 6.

    ![Einrichten des einmaligen Anmeldens](common/setup-sso.png)

3. Wenn Sie Zscaler One manuell einrichten möchten, öffnen Sie ein neues Webbrowserfenster, melden Sie sich bei der Zscaler One-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

4. Navigieren Sie zu **Verwaltung > Authentifizierung > Authentifizierungseinstellungen**, und führen Sie die folgenden Schritte aus:
   
    ![Screenshot der Zscaler One-Website mit den beschriebenen Schritten](./media/zscaler-one-tutorial/ic800206.png "Verwaltung")

    a. Wählen Sie **SAML** als Authentifizierungstyp aus.

    b. Klicken Sie auf **Configure SAML**.

5. Führen Sie im Fenster **Edit SAML** (SAML bearbeiten) die folgenden Schritte aus, und klicken Sie auf „Speichern“.  
            
    ![Benutzer & Authentifizierung verwalten](./media/zscaler-one-tutorial/ic800208.png "Benutzer & Authentifizierung verwalten")
    
    a. Fügen Sie in das Textfeld **SAML Portal URL** (SAML-Portal-URL) die **Anmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.

    b. Geben Sie im Textfeld **Login Name Attribute** (Anmeldenamensattribut) die Zeichenfolge **NameID** ein.

    c. Klicken Sie auf **Upload** (Hochladen), um das Azure-SAML-Signaturzertifikat hochzuladen, das Sie aus dem Azure-Portal unter **Public SSL Certificate** (Öffentliches SSL-Zertifikat) heruntergeladen haben.

    d. Betätigen Sie den Umschalter **Automatische SAML-Bereitstellung aktivieren**.

    e. Geben Sie im Textfeld **User Display Name Attribute** (Benutzeranzeigenamens-Attribut) die Zeichenfolge **displayName** ein, wenn Sie die automatische SAML-Bereitstellung für displayName-Attribute aktivieren möchten.

    f. Geben Sie im Textfeld **Group Name Attribute** (Gruppennamensattribut) die Zeichenfolge **memberOf** ein, wenn Sie die automatische SAML-Bereitstellung für memberOf-Attribute aktivieren möchten.

    g. Geben Sie unter **Department Name Attribute** (Abteilungsnamensattribut) die Zeichenfolge **department** ein, wenn Sie die automatische SAML-Bereitstellung für Abteilungsattribute aktivieren möchten.

    h. Klicken Sie auf **Speichern**.

6. Führen Sie auf der Dialogseite **Benutzerauthentifizierung konfigurieren** die folgenden Schritte aus:

    ![Screenshot des Dialogfelds „Benutzerauthentifizierung konfigurieren“ mit ausgewählter Option „Aktivieren“](./media/zscaler-one-tutorial/ic800207.png)

    a. Bewegen Sie den Mauszeiger unten links auf das Menü **Aktivierung**.

    b. Klicken Sie auf **Aktivieren**.

## <a name="configuring-proxy-settings"></a>Konfigurieren von Proxyeinstellungen
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>So konfigurieren Sie die Proxyeinstellungen in Internet Explorer:

1. Starten Sie **Internet Explorer**.

2. Wählen Sie im Menü **Extras** die Option **Internetoptionen**, um das Dialogfeld **Internetoptionen** zu öffnen.   

    ![Internetoptionen](./media/zscaler-one-tutorial/ic769492.png "Internetoptionen")

3. Klicken Sie auf die Registerkarte **Verbindungen** .   

    ![Verbindungen](./media/zscaler-one-tutorial/ic769493.png "Verbindungen")

4. Klicken Sie zum Öffnen des Dialogfelds **LAN-Einstellungen** auf **LAN-Einstellungen**.

5. Führen Sie im Abschnitt "Proxyserver" die folgenden Schritte aus:   
   
    ![Proxyserver](./media/zscaler-one-tutorial/ic769494.png "Proxyserver")

    a. Wählen Sie **Proxyserver für LAN verwenden** aus.

    b. Geben Sie im Textfeld „Adresse“ die Adresse **gateway.Zscaler One.net** ein.

    c. Geben Sie im Textfeld „Port“ **80** ein.

    d. Wählen Sie **Proxyserver für lokale Adressen umgehen**.

    e. Klicken Sie zum Schließen des Dialogfelds **Einstellungen für lokales Netzwerk** auf **OK**.

7. Klicken Sie zum Schließen des Dialogfelds **Internetoptionen** auf **OK**.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** den Namen brittasimon@yourcompanydomain.extension ein. Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Zscaler One gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Zscaler One** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Liste der Anwendungen **Zscaler One** aus.

    ![Zscaler One-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste die Benutzerin **Britta Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.

    ![Screenshot des Dialogfelds „Benutzer und Gruppen“ zum Auswählen eines Benutzers](./media/zscaler-one-tutorial/tutorial_zscalerone_users.png)

6. Wählen Sie im Dialogfeld **Rolle auswählen** die geeignete Rolle in der Liste aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.

    ![Screenshot des Dialogfelds „Rolle auswählen“ zum Auswählen einer Benutzerrolle](./media/zscaler-one-tutorial/tutorial_zscalerone_roles.png)

7. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Schaltfläche **Zuweisen** aus.

    ![Screenshot des Dialogfelds „Zuweisung hinzufügen“ zum Auswählen der Schaltfläche „Zuweisen“](./media/zscaler-one-tutorial/tutorial_zscalerone_assign.png)

### <a name="create-zscaler-one-test-user"></a>Erstellen eines Zscaler One-Testbenutzers

In diesem Abschnitt wird in Zscaler One ein Benutzer namens Britta Simon erstellt. Zscaler One unterstützt die Just-in-Time-Benutzerbereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in Zscaler One vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

>[!Note]
>Wenn Sie einen Benutzer manuell erstellen müssen, wenden Sie sich an das [Supportteam von Zscaler One](https://www.zscaler.com/company/contact).

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Zscaler One“ klicken, sollten Sie automatisch bei der Zscaler One-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)