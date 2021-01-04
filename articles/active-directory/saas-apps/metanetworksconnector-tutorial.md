---
title: 'Tutorial: Azure Active Directory-Integration mit Meta Networks Connector | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden für Azure Active Directory und Meta Networks Connector konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: b14a75dba2860c9dee58e40673d3299fdde277e7
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516865"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Tutorial: Azure Active Directory-Integration mit Meta Networks Connector

In diesem Tutorial erfahren Sie, wie Sie Meta Networks Connector in Azure Active Directory (Azure AD) integrieren.
Die Integration von Meta Networks Connector in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Meta Networks Connector hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei Learning Seat LMS anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Meta Networks Connector konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Meta Networks Connector-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Meta Networks Connector unterstützt **SP** - und **IDP** -initiiertes einmaliges Anmelden.
 
* Meta Networks Connector unterstützt die **Just-in-Time** -Bereitstellung.

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Hinzufügen von Meta Networks Connector aus dem Katalog

Zum Konfigurieren der Integration von Meta Networks Connector in Azure AD müssen Sie Meta Networks Connector aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

**Um Meta Networks Connector aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory** .

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie in das Suchfeld **Meta Networks Connector** ein, wählen Sie **Meta Networks Connector** im Ergebnisbereich aus, und klicken Sie dann auf **Hinzufügen** , um die Anwendung hinzuzufügen.

     ![Meta Networks Connector in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Meta Networks Connector mithilfe einer Testbenutzerin namens **Britta Simon** .
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Meta Networks Connector eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Meta Networks Connector zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Meta Networks Connector](#configure-meta-networks-connector-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Meta Networks Connector-Testbenutzers](#create-meta-networks-connector-test-user)** , um eine Entsprechung von Britta Simon in Meta Networks Connector zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei Meta Networks Connector die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Meta Networks Connector** die Option **Einmaliges Anmelden** .

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP** -initiierten Modus konfigurieren möchten:

    ![Screenshot: Seite „Grundlegende SAML-Konfiguration“ zum Eingeben des Bezeichners und einer Antwort-URL sowie zum Klicken auf „Speichern“](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`

5. Klicken Sie auf **Zusätzliche URLs festlegen** , und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    ![Screenshot: Option „Zusätzliche URLs festlegen“ zum Eingeben einer Anmelde-URL](common/both-advanced-urls.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`

    b. Geben Sie im Textfeld **Relayzustand** eine URL nach folgendem Muster ein: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächlichen Werte für Bezeichner, Antwort-URL und Anmelde-URL. Darauf wird später im Tutorial eingegangen.

6. Die Meta Networks Connector-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. Klicken Sie auf das Symbol **Bearbeiten** , um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![Screenshot: Benutzerattribute mit ausgewähltem Bearbeitungssymbol](common/edit-attribute.png)
    
7. Darüber hinaus wird von der Meta Networks Connector-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden. Führen Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die folgenden Schritte aus, um das SAML-Tokenattribut wie in der folgenden Tabelle gezeigt hinzuzufügen:
    
    | Name | Quellattribut | Namespace|
    | ---------------| --------------- | -------- |
    | firstname | user.givenname | |
    | lastname | user.surname | |
    | emailaddress| user.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | name | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | phone | user.telephonenumber | |

    a. Klicken Sie auf **Neuen Anspruch hinzufügen** , um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![Screenshot: Benutzeransprüche mit Option zum Hinzufügen eines neuen Anspruchs](common/new-save-attribute.png)

    ![Screenshot: Dialogfeld „Benutzeransprüche verwalten“ zum Eingeben der hier beschriebenen Werte](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    f. Klicken Sie auf **OK** .

    g. Klicken Sie auf **Speichern** .

8. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

9. Kopieren Sie im Abschnitt **Meta Networks Connector einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-meta-networks-connector-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Meta Networks Connector

1. Öffnen Sie eine neue Registerkarte in Ihrem Browser, und melden Sie sich mit Ihrem Meta Networks Connector-Administratorkonto an.
    
    > [!NOTE]
    > Meta Networks Connector ist ein sicheres System. Vor dem Zugriff auf das Portal müssen Sie daher dafür sorgen, dass Ihre öffentliche IP-Adresse der entsprechenden Liste zugelassener IP-Adressen hinzugefügt wird. Klicken Sie zum Abrufen Ihrer öffentlichen IP-Adresse auf [diesen](https://whatismyipaddress.com/) Link. Senden Sie Ihre IP-Adresse an das [Supportteam für den Meta Networks Connector-Client](mailto:support@metanetworks.com), damit sie der Liste zugelassener IP-Adressen hinzugefügt werden kann.
    
2. Klicken Sie auf **Verwaltung** und dann auf **Einstellungen** .
    
    ![Screenshot, auf dem die Option „Settings“ (Einstellungen) im Menü „Administration“ (Verwaltung) ausgewählt ist](./media/metanetworksconnector-tutorial/configure3.png)
    
3. Stellen Sie sicher, dass **Log Internet Traffic** (Internetdatenverkehr protokollieren) und **Force VPN MFA** (VPN-MFA erzwingen) deaktiviert sind.
    
    ![Screenshot: Deaktivieren dieser Einstellungen](./media/metanetworksconnector-tutorial/configure1.png)
    
4. Klicken Sie auf **Verwaltung** und dann auf **SAML** .
    
    ![Screenshot, auf dem „SAML“ im Menü „Administration“ (Verwaltung) ausgewählt ist](./media/metanetworksconnector-tutorial/configure4.png)
    
5. Führen Sie auf der Registerkarte **DETAILS** die folgenden Schritte aus:
    
    ![Screenshot: Seite „DETAILS“, auf der Sie die beschriebenen Werte eingeben können](./media/metanetworksconnector-tutorial/configure2.png)
    
    a. Kopieren Sie den Wert für **SSO-URL** , und fügen Sie ihn im Abschnitt **Domäne und URLs für Meta Networks Connector** ins Textfeld **Anmelde-URL** ein.
    
    b. Kopieren Sie den Wert für **Empfänger-URL** , und fügen Sie ihn im Abschnitt **Domäne und URLs für Meta Networks Connector** ins Textfeld **Antwort-URL** ein.
    
    c. Kopieren Sie den Wert für **Audience URI (SP Entity ID)** (Zielgruppen-URI (SP-Identitäts-ID)), und fügen Sie ihn im Abschnitt **Domäne und URLs für Meta Networks Connector** ins Textfeld **Bezeichner (Entitäts-ID)** ein.
    
    d. Aktivieren Sie die SAML.
    
6. Führen Sie auf der Registerkarte **ALLGEMEIN** die folgenden Schritte aus:

    ![Screenshot: Seite „GENERAL“ (ALLGEMEIN), auf der Sie die beschriebenen Werte eingeben können](./media/metanetworksconnector-tutorial/configure5.png)

    a. Fügen Sie den Wert der **Anmelde-URL** , den Sie aus dem Azure-Portal kopiert haben, in **URL für einmaliges Anmelden des Identitätsanbieters** ein.

    b. Fügen Sie in **Identity Provider Issuer** (Aussteller des Identitätsanbieters) den Wert von **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Öffnen Sie das aus dem Azure-Portal heruntergeladene Zertifikat im Editor, und fügen Sie es in das Textfeld **X 509 Certificate** (X.509-Zertifikat) ein.

    d. Aktivieren Sie die **Just-in-Time-Bereitstellung** .

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** , **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** Folgendes ein: **brittasimon\@ihreunternehmensdomäne.erweiterung** .  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt gewähren Sie Britta Simon Zugriff auf Meta Networks Connector, damit sie das einmalige Anmelden von Azure verwenden kann.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen** , **Alle Anwendungen** und **Meta Networks Connector** .

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Meta Networks Connector** aus.

    ![Der Meta Networks Connector-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen** .

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen** .

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

### <a name="create-meta-networks-connector-test-user"></a>Erstellen eines Meta Networks Connector-Testbenutzers

In diesem Abschnitt wird eine Benutzerin mit dem Namen „Britta Simon“ in Meta Networks Connector erstellt. Meta Networks Connector unterstützt die Just-in-Time-Bereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Falls in Meta Networks Connector nicht bereits ein Benutzer vorhanden ist, wird ein neuer erstellt, wenn Sie versuchen, auf Meta Networks Connector zuzugreifen.

>[!Note]
>Wenn Sie einen Benutzer manuell erstellen müssen, setzen Sie sich mit dem [Supportteam für den Meta Networks Connector-Client](mailto:support@metanetworks.com) in Verbindung.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Meta Networks Connector“ klicken, sollten Sie automatisch bei Ihrer Meta Networks Connector-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)