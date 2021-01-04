---
title: 'Tutorial: Azure Active Directory-Integration mit ScreenSteps | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und ScreenSteps konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 3816239798290318404980ded388b726d8134395
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999789"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Tutorial: Azure Active Directory-Integration mit ScreenSteps

In diesem Tutorial erfahren Sie, wie Sie ScreenSteps in Azure Active Directory (Azure AD) integrieren.
Die Integration von ScreenSteps in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf ScreenSteps hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei ScreenSteps anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit ScreenSteps konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein ScreenSteps-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* ScreenSteps unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="adding-screensteps-from-the-gallery"></a>Hinzufügen von ScreenSteps aus dem Katalog

Zum Konfigurieren der Integration von ScreenSteps in Azure AD müssen Sie ScreenSteps über den Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um ScreenSteps aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **ScreenSteps** ein, wählen Sie im Ergebnisbereich **ScreenSteps** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![ScreenSteps in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei ScreenSteps mithilfe einer Testbenutzerin namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in ScreenSteps eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei ScreenSteps müssen die folgenden Schritte ausgeführt werden:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für ScreenSteps](#configure-screensteps-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines ScreenSteps-Testbenutzers](#create-screensteps-test-user)** , um eine Entsprechung von Britta Simon in ScreenSteps zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei ScreenSteps die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **ScreenSteps** **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für ScreenSteps](common/sp-signonurl.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<tenantname>.ScreenSteps.com`

    > [!NOTE]
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Ersetzen Sie den Wert durch die tatsächliche Anmelde-URL. Dies wird später in diesem Tutorial beschrieben.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **ScreenSteps einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-screensteps-single-sign-on"></a>Konfigurieren von einmaligem Anmelden für ScreenSteps

1. Melden Sie sich in einem anderen Webbrowserfenster bei der ScreenSteps-Unternehmenswebsite als Administrator an.

1. Klicken Sie auf **Kontoeinstellungen**.

    ![Kontoverwaltung](./media/screensteps-tutorial/ic778523.png "Kontenverwaltung")

1. Klicken Sie auf **Einmaliges Anmelden**.

    ![Screenshot mit ausgewählter Option „Einmaliges Anmelden“](./media/screensteps-tutorial/ic778524.png "Remoteauthentifizierung")

1. Klicken Sie auf **Create Single Sign-on Endpoint** (Endpunkt für einmaliges Anmelden erstellen).

    ![Remoteauthentifizierung](./media/screensteps-tutorial/ic778525.png "Remoteauthentifizierung")

1. Führen Sie im Abschnitt **Create Single Sign-on Endpoint** (Endpunkt für einmaliges Anmelden erstellen) die folgenden Schritte aus:

    ![Authentifizierungsendpunkt erstellen](./media/screensteps-tutorial/ic778526.png "Authentication Endpoint erstellen")

    a. Geben Sie in das Textfeld **Titel** einen Titel ein.

    b. Wählen Sie in der Liste **Modus** die Option **SAML** aus.

    c. Klicken Sie auf **Erstellen**.

1. **Bearbeiten** Sie den neuen Endpunkt.

    ![Endpunkt bearbeiten](./media/screensteps-tutorial/ic778528.png "Endpunkt bearbeiten")

1. Führen Sie im Abschnitt **Edit Single Sign-on Endpoint** (Endpunkt für einmaliges Anmelden bearbeiten) die folgenden Schritte aus:

    ![Endpunkt für Remoteauthentifizierung](./media/screensteps-tutorial/ic778527.png "Remote Authentication Endpoint")

    a. Klicken Sie auf **Upload new SAML Certificate file** (Neue SAML-Zertifikatsdatei hochladen), und laden Sie dann das Zertifikat hoch, das Sie aus dem Azure-Portal heruntergeladen haben.

    b. Fügen Sie in das Textfeld **URL für Remoteanmeldung** den Wert für die **Anmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie den Wert der **Abmelde-URL**, den Sie aus dem Azure-Portal kopiert haben, in das Textfeld **Abmelde-URL** ein.

    d. Wählen Sie eine **Gruppe** zum Zuweisen von Benutzern bei deren Bereitstellung aus.

    e. Klicken Sie auf **Aktualisieren**.

    f. Kopieren Sie die **SAML-Consumer-URL** in die Zwischenablage, und fügen Sie sie in das Textfeld **Anmelde-URL** im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ein.

    g. Wechseln Sie zurück zu **Edit Single Sign-on Endpoint** (Endpunkt für einmaliges Anmelden bearbeiten).

    h. Klicken Sie auf die Schaltfläche **Make default for account** (Als Standard für Konto festlegen), um diesen Endpunkt für alle Benutzer zu verwenden, die sich bei ScreenSteps anmelden. Alternativ können Sie auf die Schaltfläche **Add to Site** (Zu Standort hinzufügen) klicken, um diesen Endpunkt für bestimmte Websites in **ScreenSteps** zu verwenden.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** **brittasimon@yourcompanydomain.extension** ein.  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf ScreenSteps gewähren.

1. Wählen Sie im Azure-Portal die Option **Unternehmensanwendungen** aus, und wählen Sie dann **Alle Anwendungen** und **ScreenSteps** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **ScreenSteps** aus.

    ![ScreenSteps-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-screensteps-test-user"></a>Erstellen eines ScreenSteps-Testbenutzers

In diesem Abschnitt erstellen Sie in ScreenSteps einen Benutzer mit dem Namen Britta Simon. Wenden Sie sich an das [Clientsupportteam von ScreenSteps](https://www.screensteps.com/contact), um die Benutzer in der ScreenSteps-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „ScreenSteps“ klicken, sollten Sie automatisch bei Ihrer ScreenSteps-Anwendung angemeldet werden. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)