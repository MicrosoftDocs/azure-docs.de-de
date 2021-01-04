---
title: 'Tutorial: Azure Active Directory-Integration mit Sprinklr | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Sprinklr konfigurieren.
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
ms.openlocfilehash: e76386c8c276c6c8c07751474b3718f98af18f4b
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519451"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Tutorial: Azure Active Directory-Integration mit Sprinklr

In diesem Tutorial erfahren Sie, wie Sie Sprinklr in Azure Active Directory (Azure AD) integrieren.
Die Integration von Sprinklr in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Sprinklr haben soll.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Sprinklr anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für die Konfiguration der Azure AD-Integration mit Sprinklr benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein Sprinklr-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Sprinklr unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="adding-sprinklr-from-the-gallery"></a>Hinzufügen von Sprinklr über den Katalog

Zum Konfigurieren der Integration von Sprinklr in Azure AD müssen Sie Sprinklr über den Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Sprinklr über den Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory** .

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Sprinklr** ein, wählen Sie im Ergebnisbereich **Sprinklr** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

     ![Sprinklr in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Sprinklr mithilfe eines Testbenutzers namens **Britta Simon** .
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Sprinklr eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Sprinklr zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Sprinklr](#configure-sprinklr-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Sprinklr-Testbenutzers](#create-sprinklr-test-user)** , um eine Entsprechung von Britta Simon in Sprinklr zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Sprinklr zu konfigurieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Sprinklr** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Sprinklr](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<subdomain>.sprinklr.com`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<subdomain>.sprinklr.com`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an den [Sprinklr-Support](https://www.sprinklr.com/contact-us/), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Sprinklr einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-sprinklr-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Sprinklr

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Sprinklr-Unternehmenswebsite als Administrator an.

1. Wechseln Sie zu **Verwaltung \> Einstellungen** .

    ![Verwaltung](./media/sprinklr-tutorial/ic782907.png "Verwaltung")

1. Wechseln Sie im linken Fensterbereich zu **Partner verwalten \> Einmaliges Anmelden** .

    ![Partner verwalten](./media/sprinklr-tutorial/ic782908.png "Partner verwalten")

1. Klicken Sie auf **+Einmalige Anmeldungen hinzufügen** .

    ![Screenshot der Schaltfläche „Einmalige Anmeldungen hinzufügen“.](./media/sprinklr-tutorial/ic782909.png "Einmaliges Anmelden")

1. Führen Sie im Dialogfeld **Einmaliges Anmelden** die folgenden Schritte aus:

    ![Screenshot der Seite „Einmaliges Anmelden“, auf der Sie die beschriebenen Werte eingeben können.](./media/sprinklr-tutorial/ic782910.png "Einmaliges Anmelden")

    a. Geben Sie im Textfeld **Name** einen Namen für Ihre Konfiguration ein (z.B.: *WAADSSOTest* ).

    b. Wählen Sie **Aktiviert** .

    c. Wählen Sie **Neues SSO-Zertifikat verwenden** .

    d. Öffnen Sie das Base64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Zertifikat des Identitätsanbieters** ein.

    e. Fügen Sie in das Textfeld **Entity Id** (Entitäts-ID) den Wert für den **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    f. Fügen Sie in das Textfeld **Identity Provider Login URL** (Anmelde-URL des Identitätsanbieters) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    g. Fügen Sie in das Textfeld **Identity Provider Logout URL** (Abmelde-URL des Identitätsanbieters) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    h. Wählen Sie für **SAML-Benutzer-ID-Typ** die Option **Assertion enthält sprinklr.com-Benutzernamen des Benutzers** aus.

    i. Wählen Sie für **SAML-Benutzer-ID-Speicherort** die Option **Benutzer-ID ist das Namensbezeichnerelement der Ausstellererklärung** aus.

    j. Klicken Sie auf **Speichern** .

    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** , **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** **brittasimon@yourcompanydomain.extension** ein.  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Sprinklr gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Sprinklr** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Sprinklr** aus.

    ![Sprinklr-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen** .

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen** .

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

### <a name="create-sprinklr-test-user"></a>Erstellen eines Sprinklr-Testbenutzers

1. Melden Sie sich bei der Sprinklr-Unternehmenswebsite als Administrator an.

1. Wechseln Sie zu **Verwaltung \> Einstellungen** .

    ![Verwaltung](./media/sprinklr-tutorial/ic782907.png "Verwaltung")

1. Wechseln Sie im linken Fensterbereich zu **Client verwalten \> Benutzer** .

    ![Screenshot der Schaltfläche „Benutzer hinzufügen“ unter „Einstellungen“/„Benutzer“.](./media/sprinklr-tutorial/ic782914.png "Einstellungen")

1. Klicken Sie auf **Benutzer hinzufügen** .

    ![Screenshot des Dialogfelds „Benutzer bearbeiten“, in dem Sie die beschriebenen Werte eingeben können.](./media/sprinklr-tutorial/ic782915.png "Einstellungen")

1. Führen Sie im Dialogfeld **Benutzer bearbeiten** die folgenden Schritte aus:

    ![Benutzer bearbeiten](./media/sprinklr-tutorial/ic782916.png "Benutzer bearbeiten")

    a. Geben Sie in die Textfelder **E-Mail** , **Vorname** und **Nachname** die Informationen des Azure AD-Benutzerkontos ein, das Sie bereitstellen möchten.

    b. Wählen Sie **Kennwort deaktiviert** .

    c. Wählen Sie **Sprache** aus.

    d. Wählen Sie **Benutzertyp** aus.

    e. Klicken Sie auf **Aktualisieren** .

    > [!IMPORTANT]
    > **Kennwort deaktiviert** muss ausgewählt sein, um einem Benutzer die Anmeldung über einen Identitätsanbieter zu ermöglichen. 

1. Wechseln Sie zu **Rolle** , und führen Sie dann die folgenden Schritte aus:

    ![Partnerrollen](./media/sprinklr-tutorial/ic782917.png "Partnerrollen")

    a. Wählen Sie aus der Liste **Global** die Option **ALL_Permissions** aus.  

    b. Klicken Sie auf **Aktualisieren** .

> [!NOTE]
> Sie können Azure AD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Sprinklr-Benutzerkonten oder mithilfe der von Sprinklr bereitgestellten APIs erstellen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Sprinklr“ klicken, sollten Sie automatisch bei der Sprinklr-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)