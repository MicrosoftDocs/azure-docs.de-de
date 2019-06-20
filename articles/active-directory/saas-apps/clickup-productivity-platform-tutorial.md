---
title: 'Tutorial: Azure Active Directory-Integration mit ClickUp Productivity Platform | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und ClickUp Productivity Platform konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06853edd-e8da-4ad2-a4e6-5555d592cee5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: 2a14b251df0bedbcd27ab4e9bf1abe9a27ad50d6
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65950920"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Tutorial: Azure Active Directory-Integration mit ClickUp Productivity Platform

In diesem Tutorial erfahren Sie, wie Sie ClickUp Productivity Platform in Azure Active Directory (Azure AD) integrieren.
Die Integration von ClickUp Productivity Platform in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf ClickUp Productivity Platform hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei ClickUp Productivity Platform anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit ClickUp Productivity Platform konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* ClickUp Productivity Platform-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* ClickUp Productivity Platform unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="adding-clickup-productivity-platform-from-the-gallery"></a>Hinzufügen von ClickUp Productivity Platform aus dem Katalog

Zum Konfigurieren der Integration von ClickUp Productivity Platform in Azure AD müssen Sie ClickUp Productivity Platform aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um ClickUp Productivity Platform aus dem Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **ClickUp Productivity Platform** ein, wählen Sie im Ergebnisbereich **ClickUp Productivity Platform**, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![ClickUp Productivity Platform in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit ClickUp Productivity Platform basierend auf einer Testbenutzerin mit dem Namen **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in ClickUp Productivity Platform eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei ClickUp Productivity Platform müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für ClickUp Productivity Platform](#configure-clickup-productivity-platform-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines ClickUp Productivity Platform-Testbenutzers](#create-clickup-productivity-platform-test-user)** , um eine Entsprechung von Britta Simon in ClickUp Productivity Platform zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit ClickUp Productivity Platform die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **ClickUp Productivity Platform** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für ClickUp Productivity Platform](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** die URL wie folgt ein: `https://app.clickup.com/login/sso`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://api.clickup.com/v1/team/<team_id>/microsoft`.

    > [!NOTE]
    > Der ID-Wert ist nicht der tatsächliche Wert. Ersetzen Sie den Wert durch den tatsächlichen Bezeichner. Dies wird später in diesem Tutorial beschrieben.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

### <a name="configure-clickup-productivity-platform-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für ClickUp Productivity Platform

1. Melden Sie sich in einem anderen Webbrowserfenster an Ihrem ClickUp Productivity Platform-Mandanten als Administrator an.

2. Klicken Sie auf das **Benutzerprofil**, und wählen Sie die Option **Einstellungen**.

    ![ClickUp Productivity-Konfiguration](./media/clickup-productivity-platform-tutorial/configure1.png)

3. Wählen Sie als Anbieter für einmaliges Anmelden die Option **Microsoft**.

    ![ClickUp Productivity-Konfiguration](./media/clickup-productivity-platform-tutorial/configure2.png)

4. Führen Sie auf der Seite **Einmaliges Anmelden konfigurieren für ClickUp Productivity Platform** die folgenden Schritte aus:

    ![ClickUp Productivity-Konfiguration](./media/clickup-productivity-platform-tutorial/configure3.png)

    a. Klicken Sie auf **Kopieren**, um den Wert im Feld „Entity ID“ (Entitäts-ID) zu kopieren, und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Bezeichner (Entitäts-ID)** ein.
    
    b. Fügen Sie im Textfeld **Azure Federation Metadata URL** (Azure-Verbundmetadaten-URL) den Wert der App-Verbundmetadaten-URL ein, den Sie aus dem Azure-Portal kopiert haben, und klicken Sie auf **Speichern**.

5. Klicken Sie zum Abschließen des Setups auf **Authenticate With Microsoft to complete setup** (Zum Beenden des Setups bei Microsoft authentifizieren), und führen Sie die Authentifizierung mit dem Microsoft-Konto durch.

    ![ClickUp Productivity-Konfiguration](./media/clickup-productivity-platform-tutorial/configure4.png)

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf ClickUp Productivity Platform gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **ClickUp Productivity Platform**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste den Eintrag **ClickUp Productivity Platform** aus.

    ![ClickUp Productivity Platform-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-clickup-productivity-platform-test-user"></a>Erstellen eines ClickUp Productivity Platform-Testbenutzers

1. Melden Sie sich in einem anderen Webbrowserfenster an Ihrem ClickUp Productivity Platform-Mandanten als Administrator an.

2. Klicken Sie auf das **Benutzerprofil**, und wählen Sie die Option **Benutzer**.

    ![ClickUp Productivity-Konfiguration](./media/clickup-productivity-platform-tutorial/user1.png)

3. Geben Sie die E-Mail-Adresse des Benutzers im Textfeld ein, und klicken Sie auf **Einladen**.

    ![ClickUp Productivity-Konfiguration](./media/clickup-productivity-platform-tutorial/user2.png)

    > [!NOTE]
    > Der Benutzer erhält die Benachrichtigung und muss die Einladung annehmen, um das Konto aktivieren zu können.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „ClickUp Productivity Platform“ klicken, sollten Sie automatisch bei der ClickUp Productivity Platform-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

