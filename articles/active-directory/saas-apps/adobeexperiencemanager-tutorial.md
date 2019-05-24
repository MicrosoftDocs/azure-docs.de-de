---
title: 'Tutorial: Azure Active Directory-Integration mit Adobe Experience Manager | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Adobe Experience Manager konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb066ce346d56b9b879a5e3cdcf30e7da77d94fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65859370"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Tutorial: Azure Active Directory-Integration mit Adobe Experience Manager

In diesem Tutorial erfahren Sie, wie Sie Adobe Experience Manager in Azure Active Directory (Azure AD) integrieren.
Die Integration von Adobe Experience Manager in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Adobe Experience Manager hat.
* Sie können es Ihren Benutzern ermöglichen, dass sie mit ihren Azure AD-Konten automatisch bei Adobe Experience Manager angemeldet werden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Adobe Experience Manager konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Adobe Experience Manager-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Adobe Experience Manager unterstützt **SP- und IDP**-initiiertes einmaliges Anmelden.

* Adobe Experience Manager unterstützt die **Just-in-Time**-Benutzerbereitstellung.

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Hinzufügen von Adobe Experience Manager aus dem Katalog

Zum Konfigurieren der Integration von Adobe Experience Manager in Azure AD müssen Sie Adobe Experience Manager aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um Adobe Experience Manager aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Adobe Experience Manager** ein, wählen Sie im Ergebnisbereich **Adobe Experience Manager** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Adobe Experience Manager in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei [Anwendungsname] mithilfe einer Testbenutzerin namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in [Anwendungsname] eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei [Anwendungsname] müssen Sie die folgenden Schritte ausgeführt werden:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Adobe Experience Manager](#configure-adobe-experience-manager-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Adobe Experience Manager-Testbenutzers](#create-adobe-experience-manager-test-user)**, um eine Entsprechung von Britta Simon in Adobe Experience Manager zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit [Anwendungsname] zu konfigurieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Adobe Experience Manager** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für Adobe Experience Manager](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** einen eindeutigen Wert ein, den Sie auch auf dem AEM-Server definieren.

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > Der Wert der Antwort-URL entspricht nicht dem tatsächlichen Wert. Aktualisieren Sie den Wert für die Antwort-URL mit der richtigen Antwort-URL. Nehmen Sie Verbindung mit dem [Clientsupportteam für Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) auf, um diesen Wert zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für Adobe Experience Manager](common/metadata-upload-additional-signon.png)

    Geben Sie im Textfeld **Anmelde-URL** die URL des Adobe Experience Manager-Servers ein.

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

7. Kopieren Sie im Abschnitt **Adobe Experience Manager einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-adobe-experience-manager-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Adobe Experience Manager

1. Öffnen Sie das Verwaltungsportal für **Adobe Experience Manager** in einem neuen Browserfenster.

2. Wählen Sie **Settings** (Einstellungen) > **Security** (Sicherheit) > **Users** (Benutzer) aus.

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

3. Wählen Sie **Administrator** oder einen anderen relevanten Benutzer aus.

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

4. Klicken Sie auf **Account settings** > **Manage TrustStore** (Kontoeinstellungen > TrustStore verwalten).

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

5. Klicken Sie unter **Add Certificate from CER file** (Zertifikat aus CER-Datei hinzufügen) auf **Select Certificate File** (Zertifikatdatei auswählen). Suchen Sie die Zertifikatsdatei, die Sie bereits aus dem Azure-Portal heruntergeladen haben, und wählen Sie sie aus.

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

6. Das Zertifikat wird dem TrustStore hinzugefügt. Notieren Sie den Alias des Zertifikats.

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

7. Wählen Sie auf der Seite **Users** (Benutzer) die Option **authentication-service** aus.

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

8. Wählen Sie **Account Settings** (Kontoeinstellungen) > **Create/Manage KeyStore** (KeyStore erstellen/verwalten) aus. Erstellen Sie den KeyStore durch Angeben eines Kennworts.

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

9. Wechseln Sie zurück zum Verwaltungsbildschirm. Klicken Sie dann auf **Settings** > **Operations** > **Web Console** (Einstellungen > Vorgänge > Webkonsole).

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Dadurch wird die Konfigurationsseite geöffnet.

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

10. Suchen Sie **Adobe Granite SAML 2.0 Authentication Handler**. Klicken Sie dann auf das Symbol zum **Hinzufügen**.

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

11. Führen Sie auf dieser Seite die folgenden Aktionen aus:

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. Geben Sie im Feld **Path** (Pfad) **/** ein.

    b. Fügen Sie im Feld **IDP URL** (IdP-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Geben Sie im Feld **IDP Certificate Alias** (Alias des IdP-Zertifikats) den Wert von **Certificate Alias** (Zertifikatalias) ein, den Sie in TrustStore hinzugefügt haben.

    d. Geben Sie im Feld **Security Provided Entity ID** (Sicherheits-Entitäts-ID) den eindeutigen **Azure AD-Bezeichner** ein, den Sie im Azure-Portal konfiguriert haben.

    e. Geben Sie im Feld **Assertion Consumer Service URL** (Assertionsverbraucherdienst-URL) den Wert der **Antwort-URL** ein, den Sie im Azure-Portal konfiguriert haben.

    f. Geben Sie im Feld **Password of Key Store** (Kennwort des Schlüsselspeichers) das **Kennwort** ein, das Sie in KeyStore festgelegt haben.

    g. Geben Sie im Feld **User Attribute ID** (Benutzerattribut-ID) die **Namens-ID** oder eine andere relevante Benutzer-ID ein.

    h. Aktivieren Sie **Autocreate CRX Users** (CRX-Benutzer automatisch erstellen).

    i. Geben Sie im Feld **Logout URL** (Abmelde-URL) den Wert der eindeutigen **Abmelde-URL** ein, den Sie im Azure-Portal erhalten haben.

    j. Wählen Sie **Speichern** aus.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** Folgendes ein: **brittasimon\@ihreunternehmensdomäne.erweiterung**.  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon das einmalige Anmelden von Azure, indem Sie ihr Zugriff auf Adobe Experience Manager gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **Adobe Experience Manager**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Adobe Experience Manager** aus.

    ![Adobe Experience Manager-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-adobe-experience-manager-test-user"></a>Erstellen eines Adobe Experience Manager-Testbenutzers

In diesem Abschnitt erstellen Sie in Adobe Experience Manager eine Benutzerin namens Britta Simon. Wenn Sie die Option **Autocreate CRX Users** (CRX-Benutzer automatisch erstellen) aktiviert haben, werden nach der erfolgreichen Authentifizierung automatisch Benutzer erstellt.

Wenn Sie Benutzer manuell erstellen möchten, können Sie sich an das [Adobe Experience Manager-Supportteam](https://helpx.adobe.com/support/experience-manager.html) wenden, um die Benutzer auf der Adobe Experience Manager-Plattform hinzufügen zu lassen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Adobe Experience Manager“ klicken, sollten Sie automatisch bei Ihrer Adobe Experience Manager-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
