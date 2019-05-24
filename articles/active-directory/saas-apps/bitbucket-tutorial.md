---
title: 'Tutorial: Azure Active Directory-Integration mit SAML SSO for Bitbucket by resolution GmbH | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SAML SSO for Bitbucket by resolution GmbH konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: fc947df1-f24e-43ae-9a34-518293583d69
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: eaa00690cab2cf8bc1ef7c1c3ab279a9f59fcabf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65858606"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Tutorial: Azure Active Directory-Integration mit SAML SSO for Bitbucket by resolution GmbH

In diesem Tutorial erfahren Sie, wie Sie SAML SSO for Bitbucket by resolution GmbH in Azure Active Directory (Azure AD) integrieren.
Die Integration von SAML SSO for Bitbucket by resolution GmbH in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf SAML SSO for Bitbucket by resolution GmbH hat.
* Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei SAML SSO for Bitbucket by resolution GmbH anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit SAML SSO for Bitbucket by resolution GmbH konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein Abonnement, für das einmaliges Anmelden bei SAML SSO for Bitbucket by resolution GmbH aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SAML SSO for Bitbucket by resolution GmbH unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.
* SAML SSO for Bitbucket by resolution GmbH unterstützt die **Just-in-Time**-Benutzerbereitstellung.


## <a name="adding-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>Hinzufügen von SAML SSO for Bitbucket by resolution GmbH aus dem Katalog

Um die Integration von SAML SSO for Bitbucket by resolution GmbH in Azure AD zu konfigurieren, müssen Sie SAML SSO for Bitbucket by resolution GmbH aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um SAML SSO for Bitbucket by resolution GmbH aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **SAML SSO for Bitbucket by resolution GmbH** ein, wählen Sie im Ergebnisbereich **SAML SSO for Bitbucket by resolution GmbH** aus, und klicken Sie dann auf **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![SAML SSO for Bitbucket by resolution GmbH in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit SAML SSO for Bitbucket by resolution GmbH mithilfe eines Testbenutzers namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SAML SSO for Bitbucket by resolution GmbH eine Linkbeziehung eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei SAML SSO for Bitbucket by resolution GmbH müssen die folgenden Schritte ausgeführt werden:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für SAML SSO for Bitbucket by resolution GmbH](#configure-saml-sso-for-bitbucket-by-resolution-gmbh-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Testbenutzers für SAML SSO for Bitbucket by resolution GmbH](#create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)**, um eine Entsprechung von Britta Simon in SAML SSO for Bitbucket by resolution GmbH zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit SAML SSO for Bitbucket by resolution GmbH die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **SAML SSO for Bitbucket by resolution GmbH** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten Modus** konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für SAML SSO for Bitbucket by resolution GmbH](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für SAML SSO for Bitbucket by resolution GmbH](common/metadata-upload-additional-signon.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Diese Werte erhalten Sie vom [Supportteam für den SAML SSO for Bitbucket by resolution GmbH-Client](https://marketplace.atlassian.com/apps/1217045/saml-single-sign-on-sso-bitbucket?hosting=server&tab=support). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

### <a name="configure-saml-sso-for-bitbucket-by-resolution-gmbh-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für SAML SSO for Bitbucket by resolution GmbH

1. Melden Sie sich bei der Unternehmenswebsite von SAML SSO for Bitbucket by resolution GmbH als Administrator an.

2. Klicken Sie rechts auf der Hauptsymbolleiste auf **Settings** (Einstellungen).

3. Wechseln Sie zum Abschnitt „ACCOUNTS“ (Konten), und klicken Sie auf der Menüleiste auf **SAML SingleSignOn**.

    ![SAML SingleSignOn](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

4. Klicken Sie auf der Seite **SAML SingleSignOn Plugin Configuration** (Konfiguration des SAML-SingleSignOn-Plug-Ins) auf **Add IdP** (IdP hinzufügen). 

    ![Add IdP (IdP hinzufügen)](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

5. Führen Sie auf der Seite **Choose your SAML Identity Provider** (SAML-Identitätsanbieter auswählen) die folgenden Schritte aus:

    ![Identitätsanbieter](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. Wählen Sie **AZURE AD** als **IdP Type** (IdP-Typ) aus.

    b. Geben Sie im Textfeld **Name** den Namen ein.

    c. Geben Sie im Textfeld **Description** (Beschreibung) die Beschreibung ein.

    d. Klicken Sie auf **Weiter**.

6. Klicken Sie auf der Seite **Identity provider configuration** (Identitätsanbieterkonfiguration) auf die Schaltfläche **Next** (Weiter).

    ![Identitätsanbieterkonfiguration](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

7.  Klicken Sie auf der Seite **Import SAML IdP Metadata** (SAML-IdP-Metadaten importieren) auf **Load File** (Datei laden), um die **METADATA-XML**-Datei hochzuladen, die Sie im Azure-Portal heruntergeladen haben.

    ![IPD-Metadaten](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)
    
8. Klicken Sie auf **Weiter**.

9. Klicken Sie auf **Save settings** (Einstellungen speichern).

    ![Save settings (Einstellungen speichern)](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf SAML SSO for Bitbucket by resolution GmbH gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **SAML SSO for Bitbucket by resolution GmbH** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Geben Sie in der Anwendungsliste **SAML SSO for Bitbucket by resolution GmbH** ein, und wählen Sie den Eintrag aus.

    ![SAML SSO for Bitbucket by resolution GmbH-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>Erstellen eines Testbenutzers für SAML SSO for Bitbucket by resolution GmbH

Ziel dieses Abschnitts ist das Erstellen einer Benutzerin namens Britta Simon in SAML SSO for Bitbucket by resolution GmbH. SAML SSO for Bitbucket by resolution GmbH unterstützt die Just-in-Time-Bereitstellung. Zudem können Benutzer auch manuell erstellt werden. Wenden Sie sich nach Bedarf an das [Supportteam für den SAML SSO for Bitbucket by resolution GmbH-Client](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support).

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „SAML SSO for Bitbucket by resolution GmbH“ klicken, sollten Sie automatisch bei Ihrer SAML SSO for Bitbucket by resolution GmbH-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

