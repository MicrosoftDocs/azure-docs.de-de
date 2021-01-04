---
title: 'Tutorial: Azure Active Directory-Integration mit N2F | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und N2F konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: b260e51321e14a6ea1d1ee75f88ca7564b83d492
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516748"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Tutorial: Azure Active Directory-Integration mit N2F

In diesem Tutorial erfahren Sie, wie Sie N2F für Spesenberichte in Azure Active Directory (Azure AD) integrieren.
Das Integrieren von N2F in Azure AD bietet Ihnen die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf die N2F-Spesenberichte hat.
* Sie können Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei N2F - Expense reports anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit N2F zu konfigurieren, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein N2F - Expense reports-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* N2F - Expense reports unterstützt **SP** - und **IDP** -initiiertes einmaliges Anmelden.

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Hinzufügen von N2F aus dem Katalog

Um die Integration von N2F in Azure AD zu konfigurieren, müssen Sie N2F aus dem Katalog Ihrer Liste von verwalteten SaaS-Anwendungen hinzufügen.

**So fügen Sie N2F aus dem Katalog hinzu**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory** .

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **N2F** ein, wählen Sie im Ergebnisbereich **N2F** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

     ![N2F in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit N2F - Expense reports mithilfe der Testbenutzerin **Britta Simon** .
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in N2F - Expense reports eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei N2F müssen Sie die folgenden Aufgaben ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für N2F - Expense reports](#configure-n2f---expense-reports-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines N2F - Expense reports-Testbenutzers](#create-n2f---expense-reports-test-user)** , um ein Pendant zu Britta Simon in N2F - Expense reports zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit N2F - Expense reports die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **N2F - Expense reports** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Wenn Sie die Anwendung im **IDP** -initiierten Modus konfigurieren möchten, müssen Sie im Abschnitt **Grundlegende SAML-Konfiguration** keine Schritte ausführen, da die App bereits vorab in Azure integriert wurde.

    ![Screenshot: Seite „SAML-basierte Anmeldung“ mit dem Abschnitt „Grundlegende SAML-Konfiguration“](common/preintegrated.png)

5. Klicken Sie auf **Zusätzliche URLs festlegen** , und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    ![Screenshot: Seite „Integrationen“, auf der Sie einmaliges Anmelden für Azure AD hinzufügen können](common/metadata-upload-additional-signon.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL ein: `https://www.n2f.com/app/`.

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

7. Kopieren Sie im Abschnitt **myPolicies einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-n2f---expense-reports-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für N2F - Expense reports

1. Melden Sie sich in einem anderen Webbrowserfenster auf der N2F-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf **Settings** (Einstellungen), und wählen Sie aus der Dropdownliste **Advanced Settings** (Erweiterte Einstellungen) aus.

    ![Screenshot: Auswahl von „Advanced Settings“ (Erweiterte Einstellungen)](./media/n2f-expensereports-tutorial/configure1.png)

3. Wählen Sie die Registerkarte **Account settings** (Kontoeinstellungen) aus.

    ![Screenshot: Auswahl von „Account Settings“ (Kontoeinstellungen)](./media/n2f-expensereports-tutorial/configure2.png)

4. Wählen Sie **Authentication** (Authentifizierung) und dann die Registerkarte **+ Add an authentication method** (Authentifizierungsmethode hinzufügen) aus.

    ![Screenshot: Authentifizierungsoption in den Kontoeinstellungen, unter der Sie eine Authentifizierungsmethode hinzufügen können](./media/n2f-expensereports-tutorial/configure3.png)

5. Wählen Sie als Authentifizierungsmethode **SAML Microsoft Office 365** (Microsoft Office 365-SAML) aus.

    ![Screenshot: Authentifizierungsmethode mit Auswahl von „SAML Microsoft Office 365“ (Microsoft Office 365-SAML)](./media/n2f-expensereports-tutorial/configure4.png)

6. Führen Sie im Abschnitt **Authentication method** (Authentifizierungsmethode) die folgenden Schritte aus:

    ![Screenshot: Option „Authentication method“ (Authentifizierungsmethode), unter der Sie die beschriebenen Werte eingeben können](./media/n2f-expensereports-tutorial/configure5.png)

    a. Fügen Sie in das Textfeld **Entity ID** (Entitäts-ID) den Wert für den **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    b. Fügen Sie im Textfeld **Metadata URL** (Metadaten-URL) den Wert der **Verbundmetadaten-URL der App** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Klicken Sie auf **Speichern** .

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

In diesem Abschnitt ermöglichen Sie der Benutzerin Britta Simon das einmalige Anmelden von Azure, indem Sie ihr Zugriff auf N2F gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen** , **Alle Anwendungen** und **N2F - Expense reports** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **N2F** aus.

    ![N2F-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen** .

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen** .

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

### <a name="create-n2f---expense-reports-test-user"></a>Erstellen eines N2F - Expense reports-Testbenutzers

Azure AD-Benutzer müssen in N2F bereitgestellt werden, um sich bei N2F anmelden zu können. Im Fall von N2F muss die Bereitstellung manuell erfolgen.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich auf der N2F-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf **Settings** (Einstellungen), und wählen Sie aus der Dropdownliste **Advanced Settings** (Erweiterte Einstellungen) aus.

    ![Screenshot: Auswahl von „Advanced Settings“ (Erweiterte Einstellungen)](./media/n2f-expensereports-tutorial/configure1.png)

3. Wählen Sie links im Navigationsbereich die Registerkarte **Users** (Benutzer) aus.

    ![Screenshot: Auswahl von „Users“ (Benutzer)](./media/n2f-expensereports-tutorial/user1.png)

4. Wählen Sie die Registerkarte **+ New user** (Neuer Benutzer) aus.

    ![Screenshot: Option „New user“ (Neuer Benutzer)](./media/n2f-expensereports-tutorial/user2.png)

5. Führen Sie im Abschnitt **User** (Benutzer) die folgenden Schritte aus:

    ![Screenshot: Abschnitt, in dem Sie die beschriebenen Werte eingeben können](./media/n2f-expensereports-tutorial/user3.png)

    a. Geben Sie im Textfeld **Email address** (E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein, z. B. **brittasimon\@contoso.com** .

    b. Geben Sie im Textfeld **Vorname** den Vornamen des Benutzers ein (z.B. **Britta** ).

    c. Geben Sie im Textfeld **Name** den Namen des Benutzers ein, z.B. **BrittaSimon** .

    d. Wählen Sie **Role, Direct manager (N+1)** (Rolle, Direkter Vorgesetzter (N+1)) und **Division** (Abteilung) gemäß Ihren Organisationsanforderungen aus.

    e. Klicken Sie auf **Validate and send invitation** (Bestätigen und Einladung senden).

    > [!NOTE]
    > Wenn beim Hinzufügen des Benutzers Probleme auftreten, wenden Sie sich an das [N2F-Supportteam](mailto:support@n2f.com).

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „N2F - Expense reports“ klicken, sollten Sie automatisch bei der N2F - Expense reports-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)