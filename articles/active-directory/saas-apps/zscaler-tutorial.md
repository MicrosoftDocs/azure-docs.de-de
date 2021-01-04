---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Zscaler | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Zscaler konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.openlocfilehash: 438537efc9922e681c6dd253c2f41a44c5f2fb92
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608900"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Zscaler

In diesem Tutorial erfahren Sie, wie Sie Zscaler in Azure Active Directory (Azure AD) integrieren. Die Integration von Zscaler in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Zscaler hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Zscaler anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Zscaler-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Zscaler unterstützt **SP**-initiiertes einmaliges Anmelden.
* Zscaler unterstützt **Just-in-Time**-Benutzerbereitstellung.

## <a name="adding-zscaler-from-the-gallery"></a>Hinzufügen von Zscaler über den Katalog

Zum Konfigurieren der Integration von Zscaler in Azure AD müssen Sie Zscaler aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Zscaler** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Zscaler** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Zscaler

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Zscaler mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Zscaler eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Zscaler die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Zscaler](#configure-zscaler-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Zscaler-Testbenutzers](#create-zscaler-test-user)** , um ein Pendant von B. Simon in Zscaler zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Zscaler** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<companyname>.zscaler.net`

    > [!NOTE]
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Wenden Sie sich an das [Supportteam für den Zscaler-Client](https://www.zscaler.com/company/contact), um den Wert zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Ihre Zscaler-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. Klicken Sie auf das Symbol **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![Screenshot: Benutzerattribute mit ausgewähltem Bearbeitungssymbol](common/edit-attribute.png)

1. Darüber hinaus wird von der Zscaler-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden. Führen Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die folgenden Schritte aus, um das SAML-Tokenattribut wie in der folgenden Tabelle gezeigt hinzuzufügen:

    | Name | Quellattribut |
    | ---------| ------------ |
    | memberOf | user.assignedroles |

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    f. Klicken Sie auf **Speichern**.

    > [!NOTE]
    > Klicken Sie [hier](../develop/active-directory-enterprise-app-role-management.md), um herauszufinden, wie Sie die Rolle in Azure AD konfigurieren.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Zscaler einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Zscaler gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **Zscaler** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Liste der Anwendungen **Zscaler** aus.

    ![Zscaler-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste die Benutzerin **Britta Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.

    ![Screenshot des Dialogfelds „Benutzer und Gruppen“ zum Auswählen eines Benutzers](./media/zscaler-tutorial/tutorial_zscaler_users.png)

6. Wählen Sie im Dialogfeld **Rolle auswählen** die geeignete Rolle in der Liste aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.

    ![Screenshot des Dialogfelds „Rolle auswählen“ zum Auswählen einer Benutzerrolle](./media/zscaler-tutorial/tutorial_zscaler_roles.png)

7. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Schaltfläche **Zuweisen** aus.

    ![Screenshot des Dialogfelds „Zuweisung hinzufügen“ zum Auswählen der Schaltfläche „Zuweisen“](./media/zscaler-tutorial/tutorial_zscaler_assign.png)

## <a name="configure-zscaler-sso"></a>Konfigurieren des einmaligen Anmeldens für Zscaler

1. Wenn Sie die Konfiguration in Zscaler automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

1. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Zscaler einrichten**, um zur Zscaler-Anwendung weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Zscaler anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 6.

    ![Einrichten des einmaligen Anmeldens](common/setup-sso.png)

1. Wenn Sie Zscaler manuell einrichten möchten, öffnen Sie ein neues Webbrowserfenster, melden Sie sich bei der Zscaler-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

1. Navigieren Sie zu **Verwaltung > Authentifizierung > Authentifizierungseinstellungen**, und führen Sie die folgenden Schritte aus:

    ![Screenshot der Zscaler One-Website mit den beschriebenen Schritten](./media/zscaler-tutorial/ic800206.png "Verwaltung")

    a. Wählen Sie **SAML** als Authentifizierungstyp aus.

    b. Klicken Sie auf **Configure SAML**.

1. Führen Sie im Fenster **Edit SAML** (SAML bearbeiten) die folgenden Schritte aus, und klicken Sie auf „Speichern“.  

    ![Benutzer & Authentifizierung verwalten](./media/zscaler-tutorial/ic800208.png "Benutzer & Authentifizierung verwalten")

    a. Fügen Sie in das Textfeld **SAML Portal URL** (SAML-Portal-URL) die **Anmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.

    b. Geben Sie im Textfeld **Login Name Attribute** (Anmeldenamensattribut) die Zeichenfolge **NameID** ein.

    c. Klicken Sie auf **Upload** (Hochladen), um das Azure-SAML-Signaturzertifikat hochzuladen, das Sie aus dem Azure-Portal unter **Public SSL Certificate** (Öffentliches SSL-Zertifikat) heruntergeladen haben.

    d. Betätigen Sie den Umschalter **Automatische SAML-Bereitstellung aktivieren**.

    e. Geben Sie im Textfeld **User Display Name Attribute** (Benutzeranzeigenamens-Attribut) die Zeichenfolge **displayName** ein, wenn Sie die automatische SAML-Bereitstellung für displayName-Attribute aktivieren möchten.

    f. Geben Sie im Textfeld **Group Name Attribute** (Gruppennamensattribut) die Zeichenfolge **memberOf** ein, wenn Sie die automatische SAML-Bereitstellung für memberOf-Attribute aktivieren möchten.

    g. Geben Sie unter **Department Name Attribute** (Abteilungsnamensattribut) die Zeichenfolge **department** ein, wenn Sie die automatische SAML-Bereitstellung für Abteilungsattribute aktivieren möchten.

    h. Klicken Sie auf **Speichern**.

1. Führen Sie auf der Dialogseite **Benutzerauthentifizierung konfigurieren** die folgenden Schritte aus:

    ![Screenshot des Dialogfelds „Benutzerauthentifizierung konfigurieren“ mit ausgewählter Option „Aktivieren“](./media/zscaler-tutorial/ic800207.png)

    a. Bewegen Sie den Mauszeiger unten links auf das Menü **Aktivierung**.

    b. Klicken Sie auf **Aktivieren**.

## <a name="configuring-proxy-settings"></a>Konfigurieren von Proxyeinstellungen

### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>So konfigurieren Sie die Proxyeinstellungen in Internet Explorer:

1. Starten Sie **Internet Explorer**.

1. Wählen Sie im Menü **Extras** die Option **Internetoptionen**, um das Dialogfeld **Internetoptionen** zu öffnen.

    ![Internetoptionen](./media/zscaler-tutorial/ic769492.png "Internetoptionen")

1. Klicken Sie auf die Registerkarte **Verbindungen** .

    ![Verbindungen](./media/zscaler-tutorial/ic769493.png "Verbindungen")

1. Klicken Sie zum Öffnen des Dialogfelds **LAN-Einstellungen** auf **LAN-Einstellungen**.

1. Führen Sie im Abschnitt "Proxyserver" die folgenden Schritte aus:   

    ![Proxyserver](./media/zscaler-tutorial/ic769494.png "Proxyserver")

    a. Wählen Sie **Proxyserver für LAN verwenden** aus.

    b. Geben Sie in das Textfeld „Adresse“ die Adresse **gateway.zscaler.net** ein.

    c. Geben Sie im Textfeld „Port“ **80** ein.

    d. Wählen Sie **Proxyserver für lokale Adressen umgehen**.

    e. Klicken Sie zum Schließen des Dialogfelds **Einstellungen für lokales Netzwerk** auf **OK**.

1. Klicken Sie zum Schließen des Dialogfelds **Internetoptionen** auf **OK**.

### <a name="create-zscaler-test-user"></a>Erstellen eines Zscaler-Testbenutzers

In diesem Abschnitt wird in Zscaler eine Benutzerin namens Britta Simon erstellt. Zscaler unterstützt die Just-in-Time-Benutzerbereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in Zscaler vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

> [!Note]
> Wenn Sie einen Benutzer manuell erstellen müssen, wenden Sie sich an das [Supportteam von Zscaler](https://www.zscaler.com/company/contact).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Zscaler“ klicken, sollten Sie automatisch bei Ihrer Zscaler-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [Zscaler mit Azure AD ausprobieren](https://aad.portal.azure.com/)