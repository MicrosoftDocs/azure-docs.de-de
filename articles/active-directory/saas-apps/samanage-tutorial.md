---
title: 'Tutorial: Azure Active Directory-Integration mit SolarWinds Service Desk (vormals Samanage) | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und SolarWinds Service Desk (vormals Samanage) konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: ab720430af0341f3a42d9f4d4dc19b9469872211
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675590"
---
# <a name="tutorial-azure-active-directory-integration-with-solarwinds-service-desk-previously-samanage"></a>Tutorial: Azure Active Directory-Integration mit SolarWinds Service Desk (vormals Samanage)

In diesem Tutorial erfahren Sie, wie Sie SolarWinds in Azure Active Directory (Azure AD) integrieren.
Die Integration von SolarWinds in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf SolarWinds hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei SolarWinds anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren der Azure AD-Integration mit SolarWinds Service Desk (vormals Samanage) benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Samanage-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SolarWinds unterstützt **SP** -initiiertes einmaliges Anmelden.

## <a name="adding-solarwinds-from-the-gallery"></a>Hinzufügen von SolarWinds aus dem Katalog

Zum Konfigurieren der Integration von SolarWinds in Azure AD müssen Sie SolarWinds aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um SolarWinds aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich das Symbol **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **SolarWinds** ein, wählen Sie im Ergebnisbereich **SolarWinds** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

     ![SolarWinds in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit SolarWinds mithilfe eines Testbenutzers namens **Britta Simon** .
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SolarWinds eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit SolarWinds die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für SolarWinds Service Desk](#configure-solarwinds-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines SolarWinds Service Desk-Testbenutzers](#create-solarwinds-test-user)** , um in SolarWinds Service Desk eine Entsprechung von Britta Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit SolarWinds zu konfigurieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **SolarWinds** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Samanage](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<Company Name>.samanage.com/saml_login/<Company Name>`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<Company Name>.samanage.com`.

    > [!NOTE] 
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Dies wird später in diesem Tutorial beschrieben. Wenden Sie sich an das [Supportteam für den Samanage-Client](https://www.samanage.com/support), wenn Sie weitere Details benötigen. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **SolarWinds einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

<a name="configure-solarwinds-single-sign-on"></a>

### <a name="configure-solarwinds-service-desk-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für SolarWinds Service Desk

1. Melden Sie sich in einem anderen Webbrowserfenster bei der SolarWinds-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf **Dashboard** , und wählen Sie im linken Navigationsbereich **Setup** aus.
   
    ![Dashboard](./media/samanage-tutorial/tutorial_samanage_001.png "Dashboard")

3. Klicken Sie auf **Einmaliges Anmelden** .
   
    ![Einmaliges Anmelden](./media/samanage-tutorial/tutorial_samanage_002.png "Single Sign-On")

4. Führen Sie im Abschnitt **Login using SAML** (Mit SAML anmelden) die folgenden Schritte aus:
   
    ![Login using SAML (Mit SAML anmelden)](./media/samanage-tutorial/tutorial_samanage_003.png "Login using SAML")
 
    a. Klicken Sie auf **Enable Single Sign-On with SAML** (Einmaliges Anmelden mit SAML aktivieren).  
 
    b. Fügen Sie in das Textfeld **URL des Identitätsanbieters** den Wert des **Azure AD-Bezeichners** ein, den Sie aus dem Azure-Portal kopiert haben.    
 
    c. Vergewissern Sie sich, dass die **Anmelde-URL** mit der **Anmelde-URL** im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal übereinstimmt.
 
    d. Fügen Sie in das Textfeld **Abmelde-URL** den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.
 
    e. Geben Sie im Textfeld **SAML Issuer** (SAML-Aussteller) den in Ihrem Identitätsanbieter festgelegten App-ID-URI an.
 
    f. Öffnen Sie das aus dem Azure-Portal heruntergeladene Base-64-codierte Zertifikat in Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Paste your Identity Provider x.509 Certificate below** (X.509-Zertifikat des Identitätsanbieters einfügen) ein.
 
    g. Klicken Sie auf **Create users if they do not exist in SolarWinds** (Benutzer erstellen, wenn sie nicht in SolarWinds vorhanden sind).
 
    h. Klicken Sie auf **Aktualisieren** .

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens bei Azure, indem Sie ihr Zugriff auf SolarWinds gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **SolarWinds** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **SolarWinds** aus.

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen** .

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen** .

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

### <a name="create-solarwinds-test-user"></a>Erstellen eines SolarWinds-Testbenutzers

Damit sich Azure AD-Benutzer bei SolarWinds anmelden können, müssen sie in SolarWinds bereitgestellt werden.  
Im Fall von SolarWinds ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei der SolarWinds-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf **Dashboard** , und wählen Sie im linken Navigationsbereich die Option **Setup** aus.
   
    ![Einrichtung](./media/samanage-tutorial/tutorial_samanage_001.png "Setup")

3. Klicken Sie auf die Registerkarte **Benutzer**
   
    ![Benutzer](./media/samanage-tutorial/tutorial_samanage_006.png "Benutzer")

4. Klicken Sie auf **Neuer Benutzer** .
   
    ![Neuer Benutzer](./media/samanage-tutorial/tutorial_samanage_007.png "Neuer Benutzer")

5. Geben Sie **Name** und **E-Mail-Adresse** eines vorhandenen Azure Active Directory-Kontos ein, das Sie bereitstellen möchten, und klicken Sie auf **Benutzer erstellen** .
   
    ![Benutzer erstellen](./media/samanage-tutorial/tutorial_samanage_008.png "Benutzer erstellen")
   
   >[!NOTE]
   >Der Besitzer des Azure Active Directory-Kontos erhält eine E-Mail und folgt einem Link zur Bestätigung des Kontos, bevor es aktiv wird. Sie können Azure Active Directory-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von SolarWinds-Benutzerkonten oder mithilfe der von SolarWinds bereitgestellten APIs erstellen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „SolarWinds“ klicken, sollten Sie automatisch bei der SolarWinds-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)