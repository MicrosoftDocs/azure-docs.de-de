---
title: 'Tutorial: Azure Active Directory-Integration mit Periscope Data | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Periscope Data konfigurieren.
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
ms.openlocfilehash: deac4dcde51049d744706b7479f5b5f3bbb4d9c0
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520855"
---
# <a name="tutorial-azure-active-directory-integration-with-periscope-data"></a>Tutorial: Azure Active Directory-Integration mit Periscope Data

In diesem Tutorial erfahren Sie, wie Sie Periscope Data in Azure Active Directory (Azure AD) integrieren.
Die Integration von Periscope Data in Azure AD bietet Ihnen folgende Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Periscope Data hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Periscope Data anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in Periscope Data konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Periscope Data-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Periscope Data unterstützt **SP** -initiiertes einmaliges Anmelden.

## <a name="adding-periscope-data-from-the-gallery"></a>Hinzufügen von Periscope Data aus dem Katalog

Zum Konfigurieren der Integration von Periscope Data in Azure AD müssen Sie Periscope Data aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Periscope Data aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory** .

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Periscope Data** ein, wählen Sie im Ergebnisbereich **Periscope Data** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

     ![Periscope Data in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Periscope Data mithilfe einer Testbenutzerin namens **Britta Simon** .
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Periscope Data eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Periscope Data müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Periscope Data](#configure-periscope-data-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Periscope Data-Testbenutzers](#create-periscope-data-test-user)** , um eine Entsprechung von Britta Simon in Periscope Data zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei Periscope Data die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Periscope Data** die Option **Einmaliges Anmelden** .

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Periscope Data](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine der URLs ein:
    
    ```https
    https://app.periscopedata.com/
    https://app.periscopedata.com/app/<SITENAME>
    ```

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://app.periscopedata.com/<SITENAME>/sso`.

    > [!NOTE]
    > Der Wert der Anmelde-URL entspricht nicht dem tatsächlichen Wert. Aktualisieren Sie die Werte mit der tatsächlichen Anmelde-URL. Wenden Sie sich an das [Kundensupportteam von Periscope Data](mailto:support@periscopedata.com), um diesen Wert zu erhalten. Den Bezeichnerwert erhalten Sie, indem Sie die Schritte im Abschnitt **Konfigurieren des einmaligen Anmeldens für Periscope Data** weiter unten ausführen. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

### <a name="configure-periscope-data-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Periscope Data

1. Melden Sie sich in einem anderen Webbrowserfenster als Administrator bei Periscope Data an.

2. Öffnen Sie das Zahnradmenü unten links und dann das Menü **Abrechnung** > **Sicherheit** , und führen Sie die folgenden Schritte aus. Nur Administratoren haben Zugriff auf diese Einstellungen.

    ![Screenshot: Dialogfeld „Sicherheit“ mit ausgewählten Einstellungen](./media/periscope-data-tutorial/configure01.png)

    a. Kopieren Sie die **App-Verbundmetadaten-URL** aus „Schritt 5 **SAML-Signaturzertifikat** “, und öffnen Sie sie in einem Browser. Ein XML-Dokument wird geöffnet.

    b. Wählen Sie im Textfeld **Einmaliges Anmelden** die Option **Azure Active Directory** .

    c. Suchen Sie nach dem Tag **SingleSignOnService** , und fügen Sie den Wert **Standort** in das Textfeld **SSO-URL** ein.

    d. Suchen Sie nach dem Tag **SingleLogoutService** , und fügen Sie den Wert **Standort** in das Textfeld **SLO-URL** ein.

    e. Kopieren Sie den Wert von **Bezeichner** für Ihre Instanz, und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Bezeichner (Entitäts-ID)** ein.

    f. Kopieren Sie für das erste Tag der XML-Datei den Wert von **entityID** , und fügen Sie ihn in das Textfeld **Aussteller** ein.

    g. Suchen nach dem Tag **IDPSSODescriptor** mit dem SAML-Protokoll. Suchen Sie in diesem Abschnitt nach dem Tag **KeyDescriptor** mit **use=signing** . Kopieren Sie den Wert von **X509Certificate** , und fügen Sie ihn in das Textfeld **Zertifikat** ein.

    h. Für Sites mit mehreren Bereichen kann der Standardbereich in der Dropdownliste **Standardbereich** ausgewählt werden. Dies ist der Bereich, dem neue Benutzer hinzugefügt werden, wenn sie sich zum ersten Mal an Periscope Data anmelden und über das einmalige Anmelden von Active Directory bereitgestellt werden.

    i. Klicken Sie abschließend auf **Speichern** , und **bestätigen** Sie die Änderung der SSO-Einstellungen, indem Sie **Logout** (Abmelden) eingeben.

    ![Screenshot: Dialogfeld „Update SSO Configuration“ (SSO-Konfiguration aktualisieren) mit Eingabe von „Logout“ (Abmelden) im Textfeld und Auswahl der Schaltfläche „Confirm“ (Bestätigen)](./media/periscope-data-tutorial/configure02.png)

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Periscope Data gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen** , **Alle Anwendungen** und **Periscope Data** .

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Periscope Data** aus.

    ![Periscope Data-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen** .

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen** .

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

### <a name="create-periscope-data-test-user"></a>Erstellen eines Periscope Data-Testbenutzers

Um für Azure AD-Benutzer das Anmelden an Periscope Data zu ermöglichen, müssen diese in Periscope Data bereitgestellt werden. In Periscope Data ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich an Periscope Data als Administrator an.

2. Klicken Sie unten links im Menü auf **Einstellungen** , und navigieren Sie zu **Berechtigungen** .

    ![Screenshot: Menü „Einstellungen“ mit Auswahl von „Berechtigungen“](./media/periscope-data-tutorial/configure03.png)

3. Klicken Sie auf **BENUTZER HINZUFÜGEN** , und führen Sie die folgenden Schritte aus:

      ![Informationen zur Konfiguration von Periscope Data](./media/periscope-data-tutorial/configure04.png)

    a. Geben Sie im Textfeld **First Name** (Vorname) den Vornamen des Benutzers ein, z.B. **Britta** .

    b. Geben Sie im Textfeld **Last Name** (Nachname) den Nachnamen des Benutzers ein, z.B. **Simon** .

    c. Geben Sie im Textfeld **Email** (E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein, z. B. **brittasimon\@contoso.com** .

    d. Klicken Sie auf **HINZUFÜGEN** .

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Periscope Data“ klicken, sollten Sie automatisch bei der Periscope Data-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)