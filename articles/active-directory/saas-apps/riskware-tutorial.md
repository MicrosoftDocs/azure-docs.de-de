---
title: 'Tutorial: Azure Active Directory-Integration in Riskware | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden für Azure Active Directory und Riskware konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: e33664501483280978c67b100cd56a714ef63a30
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520760"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Tutorial: Azure Active Directory-Integration in Riskware

In diesem Tutorial erfahren Sie, wie Sie Riskware in Azure Active Directory (Azure AD) integrieren.
Die Integration von Riskware in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Riskware hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Riskware anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in Riskware konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Riskware-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Riskware unterstützt **SP** -initiiertes einmaliges Anmelden.

## <a name="adding-riskware-from-the-gallery"></a>Hinzufügen von Riskware aus dem Katalog

Zum Konfigurieren der Integration von Riskware in Azure AD müssen Sie Riskware aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**So fügen Sie Riskware aus dem Katalog hinzu**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory** .

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Riskware** ein, wählen Sie im Ergebnisbereich **Riskware** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

    ![Riskware in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Riskware mithilfe einer Testbenutzerin namens **Britta Simon** .
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Riskware eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Riskware zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Riskware](#configure-riskware-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Riskware-Testbenutzers](#create-riskware-test-user)** , um eine Entsprechung von Britta Simon in Riskware zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD bei Riskware zu konfigurieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Riskware** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Riskware](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein:
    
    | Environment| URL-Muster|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PROD| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** die URL ein:
    
    | Environment| URL-Muster|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | PROD| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > Der Wert der Anmelde-URL entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Wenden Sie sich an das [Supportteam von Riskware](mailto:support@pansoftware.com.au), um diesen Wert zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML** -Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **Riskware einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-riskware-single-sign-on"></a>Konfigurieren des einmaliges Anmeldens für Riskware

1. Melden Sie sich in einem anderen Webbrowserfenster auf der Riskware-Unternehmenswebsite als Administrator an.

1. Klicken Sie oben rechts auf **Maintenance** (Wartung), um die Wartungsseite zu öffnen.

    ![Wartung bei Riskware-Konfiguration](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Klicken Sie auf der Wartungsseite auf **Authentication** (Authentifizierung).

    ![Authentifizierung bei Riskware-Konfiguration](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. Führen Sie auf der Seite **Authentication Configuration** (Authentifizierungskonfiguration) die folgenden Schritte aus:

    ![Authentifizierung und Wartung bei Riskware-Konfiguration](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Legen Sie für die Authentifizierung **Type** (Typ) als **SAML** (SAML) fest.

    b. Geben Sie im Textfeld **Code** (Code) den Code ein, z.B. „AZURE_UAT“.

    c. Geben Sie im Textfeld **Description** (Beschreibung) eine Beschreibung ein, z.B. „AZURE Configuration for SSO“ (AZURE-Konfiguration für SSO).

    d. Fügen Sie im Textfeld **Single Sign On Page** (Seite für einmaliges Anmelden) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    e. Fügen Sie in das Textfeld **Sign out Page** (Abmeldeseite) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    f. Geben Sie im Textfeld **Post Form Field** (Formularfeld für POST) den Feldnamen in der SAML-Antwort ein, z.B. „SAMLResponse“.

    g. Geben Sie im Textfeld **XML Identity Tag Name** (Tagname der XML-Identität) das Attribut ein, das den eindeutigen Bezeichner in der SAML-Antwort enthält, z.B. „NameID“.

    h. Öffnen Sie im Editor die **Metadaten-XML** -Datei, die Sie im Azure-Portal heruntergeladen haben, kopieren Sie das Zertifikat der Metadatendatei, und fügen Sie es in das Textfeld **Certificate** (Zertifikat) ein.

    i. Fügen Sie in das Textfeld **Consumer URL** (Consumer-URL) den Wert der **Antwort-URL** ein, den Sie vom Supportteam erhalten.

    j. Fügen Sie in das Textfeld **Issuer** (Aussteller) den Wert des **Bezeichners** ein, den Sie vom Supportteam erhalten.

    > [!Note]
    > Wenden Sie sich an das [Supportteam von Riskware](mailto:support@pansoftware.com.au), um diese Werte zu erhalten.

    k. Aktivieren Sie das Kontrollkästchen **Use POST** (POST verwenden).

    l. Aktivieren Sie das Kontrollkästchen **Use SAML Request** (SAML-Anforderung verwenden).

    m. Klicken Sie auf **Speichern** .

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** , **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** den Namen `brittasimon@yourcompanydomain.extension` ein.  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt gewähren Sie Britta Simon Zugriff auf Riskware, damit sie das einmalige Anmelden von Azure verwenden kann.

1. Wählen Sie im Azure-Portal die Option **Unternehmensanwendungen** aus, und wählen Sie dann **Alle Anwendungen** und **Riskware** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Riskware** aus.

    ![Riskware-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen** .

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen** .

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

### <a name="create-riskware-test-user"></a>Erstellen eines Riskware-Testbenutzers

Damit sich Azure AD-Benutzer bei Riskware anmelden können, müssen sie in Riskware bereitgestellt werden. Im Fall von Riskware muss die Bereitstellung manuell ausgeführt werden.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Riskware als Sicherheitsadministrator an.

1. Klicken Sie oben rechts auf **Maintenance** (Wartung), um die Wartungsseite zu öffnen. 

    ![Wartung bei Riskware-Konfiguration](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Klicken Sie auf der Wartungsseite auf **People** (Personen).

    ![Personen bei Riskware-Konfiguration](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Wählen Sie die Registerkarte **Details** aus, und führen Sie die folgenden Schritte aus:

    ![Riskware-Konfigurationsdetails](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Wählen Sie **Person Type** (Personentyp) aus, z.B. „Employee“ (Mitarbeiter).

    b. Geben Sie im Textfeld **First Name** (Vorname) den Vornamen des Benutzers ein, z.B. **Britta** .

    c. Geben Sie im Textfeld **Surname** (Nachname) den Nachnamen des Benutzers ein, z.B. **Simon** .

1. Führen Sie im Abschnitt **Sicherheit** die folgenden Schritte aus:

    ![Sicherheit bei Riskware-Konfiguration](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. Wählen Sie im Abschnitt **Authentication** (Authentifizierung) den von Ihnen eingerichteten Modus **Authentication** (Authentifizierung) aus, z.B. „AZURE Configuration for SSO“ (AZURE-Konfiguration für SSO).

    b. Geben Sie im Abschnitt **Logon Details** (Anmeldeinformationen) im Textfeld **User ID** (Benutzer-ID) die E-Mail-Adresse des Benutzers ein, z. B. `brittasimon@contoso.com`.

    c. Geben Sie im Textfeld **Password** (Kennwort) das Kennwort des Benutzers ein.

1. Führen Sie auf der Registerkarte **Organisation** die folgenden Schritte aus:

    ![Organisation bei Riskware-Konfiguration](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Wählen Sie als Option die Organisation **Level1** aus.

    b. Geben Sie im Abschnitt **Person's Primary Workplace** (Primärer Arbeitsplatz der Person) im Textfeld **Location** (Ort) Ihren Standort ein.

    c. Wählen Sie im Abschnitt **Employee** (Mitarbeiter) den **Employee Status** (Mitarbeiterstatus) aus, z.B. „Casual“ (Gelegentlich).

    d. Klicken Sie auf **Speichern** .

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Riskware“ klicken, sollten Sie automatisch bei Ihrer Riskware-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)