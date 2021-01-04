---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Workplace by Facebook | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Workplace by Facebook konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2020
ms.author: jeedes
ms.openlocfilehash: f20849e4085adcca909d990a2ae9fb7b9751220b
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520097"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workplace-by-facebook"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Workplace by Facebook

In diesem Tutorial erfahren Sie, wie Sie Workplace by Facebook in Azure Active Directory (Azure AD) integrieren. Die Integration von Workplace by Facebook in Azure AD bietet folgende Vorteile:

* Steuern Sie in Azure AD, wer Zugriff auf Workplace by Facebook hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Workplace by Facebook anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Workplace by Facebook-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

> [!NOTE]
> Bei Facebook gibt es zwei Produkte: Workplace Standard (kostenlos) und Workplace Premium (kostenpflichtig). Alle Workplace Premium-Mandanten können SCIM- und SSO-Integration ohne weitere Auswirkungen auf die Kosten oder die erforderlichen Lizenzen konfigurieren. SSO und SCIM sind in Workplace Standard-Instanzen nicht verfügbar.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Workplace by Facebook unterstützt **SP** -initiiertes einmaliges Anmelden.
* Workplace by Facebook unterstützt die **Just-in-Time-Bereitstellung** .
* Workplace by Facebook unterstützt die **[automatische Benutzerbereitstellung](workplacebyfacebook-provisioning-tutorial.md)** .
* Mobile Workplace by Facebook-Anwendungen können nun mit Azure AD konfiguriert werden, um SSO zu ermöglichen. In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.
* Nach dem Konfigurieren von Workplace by Facebook können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-aad) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Hinzufügen von Workplace by Facebook aus dem Katalog

Zum Konfigurieren der Integration von Workplace by Facebook in Azure AD müssen Sie Workplace by Facebook aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Workplace by Facebook** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Workplace by Facebook** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-workplace-by-facebook"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Workplace by Facebook

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Workplace by Facebook mithilfe eines Testbenutzers mit dem Namen **B.Simon** . Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Workplace by Facebook eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Workplace by Facebook müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Workplace by Facebook](#configure-workplace-by-facebook-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * **[Erstellen eines Workplace by Facebook-Testbenutzers](#create-workplace-by-facebook-test-user)** , um eine Entsprechung von B. Simon in Workplace by Facebook zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
3. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Workplace by Facebook** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<instancename>.facebook.com`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://www.facebook.com/company/<instanceID>`.

    c. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://www.facebook.com/company/<instanceID>`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL, den tatsächlichen Bezeichner und die tatsächliche Antwort-URL. Die richtigen Werte für die Workplace-Community finden Sie auf der Authentifizierungsseite des Workplace-Unternehmensdashboards. Dies wird weiter unten im Tutorial erläutert.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Workplace by Facebook einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert aus dem Feld **Kennwort** .
   1. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Workplace by Facebook gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Workplace by Facebook** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen** .
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen** .
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

## <a name="configure-workplace-by-facebook-sso"></a>Konfigurieren des einmaligen Anmeldens für Workplace by Facebook

1. Wenn Sie die Konfiguration in Workplace by Facebook automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

1. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Workplace by Facebook einrichten** , um zur Anwendung Workplace by Facebook weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Workplace by Facebook anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 5.

    ![Einrichtungskonfiguration](common/setup-sso.png)

1. Wenn Sie Workplace by Facebook manuell einrichten möchten, melden Sie sich in einem neuen Webbrowserfenster bei der Workplace by Facebook-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

    > [!NOTE]
    > Im Rahmen des SAML-Authentifizierungsprozesses werden von Workplace ggf. Abfragezeichenfolgen mit einer Größe von bis zu 2,5 KB genutzt, um Parameter an Azure AD zu übergeben.

1. Navigieren Sie im linken Navigationsbereich zur Registerkarte **Security** > **Authentication** (Sicherheit > Authentifizierung).

    ![Administratorbereich](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

    a. Aktivieren Sie die Option **Single-sign on (SSO)** (Einmaliges Anmelden (SSO)).
    
    b. Klicken Sie auf **+Add new SSO Provider** (+Neuen SSO-Anbieter hinzufügen).
    > [!NOTE]
    > Aktivieren Sie außerdem das Kontrollkästchen für das Kennwort. Administratoren benötigen diese Anmeldeoption möglicherweise während der Ausführung des Zertifikatrollovers, um zu verhindern, dass sie ausgesperrt werden.

1. Wählen Sie unter **Authentication** (Authentifizierung) die Registerkarte **Single-Sign On (SSO)** (Einmaliges Anmelden (SSO)) aus, und führen Sie die folgenden Schritte aus:

    ![Registerkarte „Authentication“ (Authentifizierung)](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. Geben Sie unter **Name of the SSO Provider** (Name des SSO-Anbieters) den Namen der SSO-Instanz ein, etwa „Azureadsso“.

    b. Fügen Sie in das Textfeld **SAML-URL** den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie in das Textfeld **SAML Issuer URL** (SAML-Aussteller-URL) den Wert für **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Öffnen Sie im Editor das **Base64-codierte Zertifikat** , das Sie aus dem Azure-Portal heruntergeladen haben, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **SAML-Zertifikat** ein.

    e. Kopieren Sie die **Zielgruppen-URL** für Ihre Instanz, und fügen Sie sie im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Bezeichner (Entitäts-ID)** ein.

    f. Kopieren Sie die **Empfänger-URL** für Ihre Instanz, und fügen Sie sie im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Anmelde-URL** ein.

    g. Kopieren Sie den Wert unter **ACS (Assertion Consumer Service) URL** (ACS-URL (Assertion Consumer Service, Assertionsverbraucherdienst)) für Ihre Instanz, und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** ins Textfeld **Antwort-URL** ein.

    h. Scrollen Sie im Abschnitt nach unten, und klicken Sie auf die Schaltfläche **Test SSO** (SSO testen). Ein Popupfenster mit der Azure AD-Anmeldeseite wird angezeigt. Geben Sie Ihre Anmeldeinformationen ein, wie Sie es von der Authentifizierung kennen.

    **Problembehandlung:** Stellen Sie sicher, das die E-Mail-Adresse, die von Azure AD zurückgegeben wird, mit dem Workplace-Konto übereinstimmt, mit dem Sie angemeldet sind.

    i. Nachdem der Test erfolgreich abgeschlossen wurde, können Sie an das Ende der Seite scrollen und auf die Schaltfläche **Speichern** klicken.

    j. Allen Benutzern von Workplace wird jetzt die Azure AD-Anmeldeseite für die Authentifizierung angezeigt.

1. **SAML Logout Redirect (optional)**  - (SAML-Abmeldeumleitung (optional))

    Optional können Sie angeben, dass Sie eine SAML-Abmelde-URL konfigurieren möchten, mit der auf die Azure AD-Abmeldeseite verwiesen werden kann. Wenn diese Einstellung aktiviert und konfiguriert wird, werden Benutzer nicht mehr auf die Workplace-Abmeldeseite verwiesen. Stattdessen werden Benutzer an die URL umgeleitet, die unter der Einstellung für die SAML-Abmeldeumleitung hinzugefügt wurde.

### <a name="configuring-reauthentication-frequency"></a>Konfigurieren der Häufigkeit der erneuten Authentifizierung

Sie können Workplace so konfigurieren, dass die Aufforderung für die SAML-Überprüfung jeden Tag, alle drei Tage, einmal pro Woche, einmal alle zwei Wochen, einmal im Monat oder nie angezeigt wird.

> [!NOTE]
> Die Mindesteinstellung für die SAML-Überprüfung von mobilen Anwendungen ist auf einmal pro Woche festgelegt.

Sie können auch eine SAML-Zurücksetzung für alle Benutzer mithilfe der folgenden Schaltfläche erzwingen: SAML-Authentifizierung jetzt für alle Benutzer erzwingen.

### <a name="create-workplace-by-facebook-test-user"></a>Erstellen eines Workplace by Facebook-Testbenutzers

In diesem Abschnitt wird in Workplace by Facebook ein Benutzer mit dem Namen B. Simon erstellt. Workplace by Facebook unterstützt die Just-in-Time-Bereitstellung, die standardmäßig aktiviert ist.

Für Sie steht in diesem Abschnitt keine Aktion an. Falls ein Benutzer in Workplace by Facebook nicht vorhanden ist, wird ein neuer Benutzer erstellt, wenn Sie versuchen, auf Workplace by Facebook zuzugreifen.

>[!Note]
>Wenden Sie sich an das [Supportteam für den Workplace by Facebook-Client](https://www.workplace.com/help/work/), wenn Sie einen Benutzer manuell erstellen müssen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Workplace by Facebook“ klicken, sollten Sie automatisch bei der Workplace by Facebook-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>Testen des einmaligen Anmeldens für Workplace by Facebook (mobil)

1. Öffnen Sie die mobile Workplace by Facebook-Anwendung. Klicken Sie auf der Anmeldeseite auf **LOG IN** (ANMELDEN).

    ![Anmeldung](./media/workplacebyfacebook-tutorial/test05.png)

2. Geben Sie Ihre geschäftliche E-Mail-Adresse ein, und klicken Sie auf **CONTINUE** (WEITER).

    ![E-Mail](./media/workplacebyfacebook-tutorial/test02.png)

3. Klicken Sie auf **JUST ONCE** (NUR EINMAL).

    ![Einmal](./media/workplacebyfacebook-tutorial/test04.png)

4. Klicken Sie auf **Zulassen** .

    ![Zulassen](./media/workplacebyfacebook-tutorial/test03.png)

5. Nach erfolgreicher Anmeldung wird die Startseite der Anwendung angezeigt:    

    ![Homepage](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [Konfigurieren der Benutzerbereitstellung](workplacebyfacebook-provisioning-tutorial.md)

- [Workplace by Facebook mit Azure AD ausprobieren](https://aad.portal.azure.com)

- [Was ist Sitzungssteuerung in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)