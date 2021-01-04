---
title: 'Tutorial: Azure Active Directory-Integration mit Mozy Enterprise | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Mozy Enterprise konfigurieren.
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
ms.openlocfilehash: 97294bdd088f50a46786c3568f4230706f750c4c
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520930"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Tutorial: Azure Active Directory-Integration mit Mozy Enterprise

In diesem Tutorial erfahren Sie, wie Sie Mozy Enterprise in Azure Active Directory (Azure AD) integrieren.
Die Integration von Mozy Enterprise in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Mozy Enterprise hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Mozy Enterprise anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Mozy Enterprise konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein Mozy Enterprise-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Mozy Enterprise unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Hinzufügen von Mozy Enterprise aus dem Katalog

Zum Konfigurieren der Integration von Mozy Enterprise in Azure AD müssen Sie Mozy Enterprise aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Mozy Enterprise aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory** .

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Mozy Enterprise** ein, wählen Sie im Ergebnisbereich **Mozy Enterprise** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

     ![Mozy Enterprise in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Mozy Enterprise basierend auf einem Testbenutzer mit dem Namen **Britta Simon** .
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Mozy Enterprise eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Mozy Enterprise müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Mozy Enterprise](#configure-mozy-enterprise-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Mozy Enterprise-Testbenutzers](#create-mozy-enterprise-test-user)** , um eine Entsprechung von Britta Simon in Mozy Enterprise zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Mozy Enterprise die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Mozy Enterprise** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Mozy Enterprise](common/sp-signonurl.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE]
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Den Wert erhalten Sie vom [Supportteam für den Mozy Enterprise-Client](https://support.mozy.com/). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Mozy Enterprise einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-mozy-enterprise-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Mozy Enterprise

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Mozy Enterprise-Unternehmenswebsite als Administrator an.

2. Klicken Sie im Abschnitt **Konfiguration** auf **Authentication Policy** (Authentifizierungsrichtlinie).
   
    ![Screenshot: Auswahl von „Authentication Policy“ (Authentifizierungsrichtlinie) unter „Configuration“ (Konfiguration)](./media/mozy-enterprise-tutorial/ic777314.png "Authentifizierungsrichtlinie")

3. Führen Sie im Abschnitt **Authentifizierungsrichtlinie** die folgenden Schritte aus:
   
    ![Screenshot: Abschnitt „Authentication Policy“ (Authentifizierungsrichtlinie), in dem Sie die beschriebenen Werte eingeben können](./media/mozy-enterprise-tutorial/ic777315.png "Authentifizierungsrichtlinie")
   
    a. Wählen Sie für **Directory Service** (Verzeichnisdienst) die Option **Anbieter** aus.
   
    b. Wählen Sie **LDAP-Push verwenden** aus.
   
    c. Klicken Sie auf die Registerkarte **SAML-Authentifizierung** .
   
    d. Fügen Sie in das Textfeld **Authentication URL** (Authentifizierungs-URL) die **Anmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.
   
    e. Fügen Sie den **Azure AD-Bezeichner** , den Sie aus dem Azure-Portal kopiert haben, in das Textfeld **SAML Endpoint** (SAML-Endpunkt) ein.
   
    f. Öffnen Sie das heruntergeladene Base64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie anschließend das gesamte Zertifikat in das Textfeld **SAML-Zertifikat** ein.
   
    g. Wählen Sie **SSO für Administratoren zur Anmeldung mit den Netzwerkanmeldeinformationen aktivieren** aus.
   
    h. Klicken Sie auf **Änderungen speichern** .

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Mozy Enterprise gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Mozy Enterprise** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Mozy Enterprise** aus.

    ![Mozy Enterprise-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen** .

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen** .

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

### <a name="create-mozy-enterprise-test-user"></a>Erstellen eines Mozy Enterprise-Testbenutzers

Damit sich Azure AD-Benutzer bei Mozy Enterprise anmelden können, müssen sie in Mozy Enterprise bereitgestellt werden. Im Fall von Mozy Enterprise ist die Bereitstellung eine manuelle Aufgabe.

>[!NOTE]
>Sie können Azure AD-Benutzerkonten auch mit anderen Tools zum Erstellen von Mozy Enterprise-Benutzerkonten oder mit den APIs von Mozy Enterprise bereitstellen.

**Führen Sie zum Bereitstellen von Benutzerkonten die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrem **Mozy Enterprise** -Mandanten an.

2. Klicken Sie auf **Benutzer** und dann auf **Add New User** (Neuen Benutzer hinzufügen).
   
    ![Benutzer](./media/mozy-enterprise-tutorial/ic777317.png "Benutzer")
   
    >[!NOTE]
    >Die Option **Add New User** wird nur angezeigt, wenn **Mozy** unter **Authentication Policy** als Anbieter ausgewählt ist. Wenn die SAML-Authentifizierung konfiguriert ist, werden die Benutzer automatisch bei ihrer ersten Anmeldung mittels SSO hinzugefügt.
    
3. Führen Sie im Dialogfeld „Neuer Benutzer“ die folgenden Schritte aus:
   
    ![Add Users (Benutzer hinzufügen)](./media/mozy-enterprise-tutorial/ic777318.png "Benutzer hinzufügen")
   
    a. Wählen Sie in der Liste **Gruppe auswählen** eine Gruppe aus.
   
    b. Wählen Sie in der Liste **Benutzertyp** einen Typ aus.
   
    c. Geben Sie im Textfeld **Benutzername** den Namen des Azure AD-Benutzers ein.
   
    d. Geben Sie im Textfeld **E-Mail** die E-Mail-Adresse des Azure AD-Benutzers ein.
   
    e. Wählen Sie **E-Mail mit Anweisungen an Benutzer senden** aus.
   
    f. Klicken Sie auf **Benutzer hinzufügen** .

     >[!NOTE]
     > Nach dem Erstellen des Benutzers erhält der Azure AD-Benutzer eine E-Mail mit einem Link zur Bestätigung des Kontos, bevor es aktiv wird.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Mozy Enterprise“ klicken, sollten Sie automatisch bei der Mozy Enterprise-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)