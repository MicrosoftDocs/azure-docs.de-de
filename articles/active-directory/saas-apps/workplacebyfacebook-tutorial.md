---
title: 'Tutorial: Azure Active Directory-Integration mit Workplace by Facebook | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Workplace by Facebook konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27173c8beeecf2be43e80f59df8907952734c06b
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65905220"
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Tutorial: Azure Active Directory-Integration mit Workplace by Facebook

In diesem Tutorial erfahren Sie, wie Sie Workplace by Facebook in Azure Active Directory (Azure AD) integrieren.
Die Integration von Workplace by Facebook in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD den Zugriff auf Workplace by Facebook steuern.
* Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Workplace by Facebook anzumelden (einmaliges Anmelden).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für das Konfigurieren der Azure AD-Integration mit Workplace by Facebook benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Workplace by Facebook-Abonnement mit aktiviertem einmaligem Anmelden

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/)eine einmonatige Testversion anfordern.

> [!NOTE]
> Bei Facebook gibt es zwei Produkte: Workplace Standard (kostenlos) und Workplace Premium (kostenpflichtig). Alle Workplace Premium-Mandanten können SCIM- und SSO-Integration ohne weitere Auswirkungen auf die Kosten oder die erforderlichen Lizenzen konfigurieren. SSO und SCIM sind in Workplace Standard-Instanzen nicht verfügbar.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Workplace by Facebook unterstützt **SP**-initiiertes einmaliges Anmelden.
* Workplace by Facebook unterstützt die **Just-in-Time-Bereitstellung**.
* Workplace by Facebook unterstützt die **[automatische Benutzerbereitstellung](workplacebyfacebook-provisioning-tutorial.md)**.

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Hinzufügen von Workplace by Facebook aus dem Katalog

Zum Konfigurieren der Integration von Workplace by Facebook in Azure AD müssen Sie Workplace by Facebook aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Workplace by Facebook aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Workplace by Facebook** ein, wählen Sie im Ergebnisbereich **Workplace by Facebook** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Workplace by Facebook in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Workplace by Facebook mithilfe eines Testbenutzers namens **Britta Simon**.
Damit das einmalige Anmelden funktioniert, muss zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Workplace by Facebook eine Linkbeziehung eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Workplace by Facebook müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Workplace by Facebook](#configure-workplace-by-facebook-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Workplace by Facebook-Testbenutzers](#create-workplace-by-facebook-test-user)**, um eine Entsprechung von Britta Simon in Workplace by Facebook zu erhalten, die mit der Darstellung des Benutzers in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei Workplace by Facebook die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Workplace by Facebook** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zu Workplace by Facebook-Domäne und -URLs](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<instancename>.facebook.com`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://www.facebook.com/company/<instanceID>`.

    > [!NOTE] 
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Die richtigen Werte für die Workplace-Community finden Sie auf der Authentifizierungsseite des Workplace-Unternehmensdashboards.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Workplace by Facebook einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-workplace-by-facebook-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Workplace by Facebook

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Workplace by Facebook-Unternehmenswebsite als Administrator an.
  
    > [!NOTE]
    > Im Rahmen des SAML-Authentifizierungsprozesses werden von Workplace ggf. Abfragezeichenfolgen mit einer Größe von bis zu 2,5 KB genutzt, um Parameter an Azure AD zu übergeben.

2. Wechseln Sie im **Admin Panel** (Administratorbereich) zur Registerkarte **Security** (Sicherheit).

    ![Administratorbereich](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

3. Wählen Sie unter **Authentication** (Authentifizierung) die Registerkarte **Single-Sign On (SSO)** (Einmaliges Anmelden (SSO)) aus, und führen Sie die folgenden Schritte aus:

    ![Registerkarte „Authentication“ (Authentifizierung)](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. Fügen Sie in das Textfeld **SAML-URL** den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    b. Fügen Sie in das Textfeld **SAML Issuer URI** (SAML-Aussteller-URI) den Wert von **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie in **SAML-Abmeldeumleitung** (optional) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Öffnen Sie im Editor das **Base64-codierte Zertifikat**, das Sie aus dem Azure-Portal heruntergeladen haben, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **SAML-Zertifikat** ein.

    e. Kopieren Sie die **Zielgruppen-URL** für Ihre Instanz, und fügen Sie sie im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Bezeichner (Entitäts-ID)** ein.

    f. Kopieren Sie die **Empfänger-URL** für Ihre Instanz, und fügen Sie sie im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Anmelde-URL** ein.

    g. Scrollen Sie im Abschnitt nach unten, und klicken Sie auf die Schaltfläche **Test SSO** (SSO testen). Ein Popupfenster mit der Azure AD-Anmeldeseite wird angezeigt. Geben Sie Ihre Anmeldeinformationen ein, wie Sie es von der Authentifizierung kennen.

    **Problembehandlung:** Stellen Sie sicher, das die E-Mail-Adresse, die von Azure AD zurückgegeben wird, mit dem Workplace-Konto übereinstimmt, mit dem Sie angemeldet sind.

    h. Nachdem der Test erfolgreich abgeschlossen wurde, können Sie an das Ende der Seite scrollen und auf die Schaltfläche **Speichern** klicken.

    i. Allen Benutzern von Workplace wird jetzt die Azure AD-Anmeldeseite für die Authentifizierung angezeigt.

4. **SAML Logout Redirect (optional)** - (SAML-Abmeldeumleitung (optional))

    Optional können Sie angeben, dass Sie eine SAML-Abmelde-URL konfigurieren möchten, mit der auf die Azure AD-Abmeldeseite verwiesen werden kann. Wenn diese Einstellung aktiviert und konfiguriert wird, werden Benutzer nicht mehr auf die Workplace-Abmeldeseite verwiesen. Stattdessen werden Benutzer an die URL umgeleitet, die unter der Einstellung für die SAML-Abmeldeumleitung hinzugefügt wurde.

### <a name="configuring-reauthentication-frequency"></a>Konfigurieren der Häufigkeit der erneuten Authentifizierung

Sie können Workplace so konfigurieren, dass die Aufforderung für die SAML-Überprüfung jeden Tag, alle drei Tage, einmal pro Woche, einmal alle zwei Wochen, einmal im Monat oder nie angezeigt wird.

> [!NOTE]
> Die Mindesteinstellung für die SAML-Überprüfung von mobilen Anwendungen ist auf einmal pro Woche festgelegt.

Sie können auch eine SAML-Zurücksetzung für alle Benutzer mithilfe der folgenden Schaltfläche erzwingen: SAML-Authentifizierung jetzt für alle Benutzer erzwingen.

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie Zugriff auf Workplace by Facebook gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**, **Alle Anwendungen** und dann **Workplace by Facebook** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Geben Sie in der Anwendungsliste **Workplace by Facebook** ein und wählen Sie es aus.

    ![Der Link von Workplace by Facebook in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-workplace-by-facebook-test-user"></a>Erstellen eines Workplace by Facebook-Testbenutzers

In diesem Abschnitt wird ein Benutzer mit dem Namen Britta Simon in Workplace by Facebook erstellt. Workplace by Facebook unterstützt die Just-in-Time-Bereitstellung, die standardmäßig aktiviert ist.

Für Sie steht in diesem Abschnitt keine Aktion an. Falls ein Benutzer in Workplace by Facebook nicht vorhanden ist, wird ein neuer Benutzer erstellt, wenn Sie versuchen, auf Workplace by Facebook zuzugreifen.

>[!Note]
>Wenden Sie sich an das [Supportteam für den Workplace by Facebook-Client](https://workplace.fb.com/faq/), wenn Sie einen Benutzer manuell erstellen müssen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Workplace by Facebook“ klicken, sollten Sie automatisch bei der Workplace by Facebook-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurieren der Benutzerbereitstellung](workplacebyfacebook-provisioning-tutorial.md)
