---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Workware | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Workware konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: jeedes
ms.openlocfilehash: f4996943fd95252be45f7a270d143f14e7f2d48a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181270"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workware"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Workware

In diesem Tutorial erfahren Sie, wie Sie Workware in Azure Active Directory (Azure AD) integrieren. Die Integration von Workware in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Workware hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Workware anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Workware-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Workware unterstützt **IDP-initiiertes** einmaliges Anmelden.

## <a name="adding-workware-from-the-gallery"></a>Hinzufügen von Workware aus dem Katalog

Zum Konfigurieren der Integration von Workware in Azure AD müssen Sie Workware aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Workware** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Workware** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-workware"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Workware

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Workware mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Workware eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Workware die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Workware](#configure-workware-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * **[Erstellen eines Workware-Testbenutzers](#create-workware-test-user)** , um ein Pendant von B. Simon in Workware zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Workware** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `<WORKWARE_URL>/WW/AuthServices`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `<WORKWARE_URL>/WW/AuthServices/Acs`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit dem eigentlichen Bezeichner und der Antwort-URL. Diese Werte erhalten Sie vom [Supportteam für den Workware-Client](mailto:support@activeops.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Workware einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Workware gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Workware** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-workware-sso"></a>Konfigurieren des einmaligen Anmeldens für Workware

Damit Sie das SSO-Feature in Workware nutzen können, muss das folgende Setup abgeschlossen werden:

#### <a name="enable-sso-permissions-for-workware-system-administrators"></a>Aktivieren von SSO-Berechtigungen für Workware-Systemadministratoren

* Damit Workware-Systemadministratoren die SSO-Authentifizierung einrichten können, muss die Berechtigung zur SSO-Authentifizierung für Workware-Systemadministratoren aktiviert werden (auf dem Bildschirm „Permissions for selected Role“ (Berechtigungen für ausgewählte Rolle) unter **Administration > System configuration permissions category in the System Settings** (Verwaltung > Kategorie für Systemkonfigurationsberechtigungen in den Systemeinstellungen)).

    ![Berechtigung zur SSO-Authentifizierung](./media/workware-tutorial/permission.png)

#### <a name="set-up-sso-authentication-in-workware"></a>Einrichten der SSO-Authentifizierung in Workware

1. Navigieren Sie zur Seite **System Settings** (Systemeinstellungen), und klicken Sie auf **SSO Authentication** (SSO-Authentifizierung).

1. Klicken Sie im Abschnitt **SSO Authentication** (SSO-Authentifizierung) auf die Schaltfläche **Add SSO Authentication** (SSO-Authentifizierung hinzufügen), und führen Sie die folgenden Schritte aus: 

    ![SSO Authentication (SSO-Authentifizierung)](./media/workware-tutorial/authentication.png)

    1. Geben Sie im Feld **External Identity Provider** (Externer Identitätsanbieter) den Namen des IDP ein.
    1. Wählen Sie im Feld **Authentication Type** (Authentifizierungstyp) den Eintrag **SAML 2.0** aus.
    1. Fügen Sie in das Textfeld **Identity Provider SignIn URL** (Anmelde-URL des Identitätsanbieters) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.
    1. Geben Sie im Textfeld **Identity Provider Issuer URL** (Aussteller-URL des Identitätsanbieters) den Wert von **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.
    1. Fügen Sie in das Textfeld **Identity Provider Logout URL** (Abmelde-URL des Identitätsanbieters) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.
    1. Klicken Sie auf **Enable** (Aktivieren).
    1. Laden Sie unter  **Identity Provider Certificate** (Identitätsanbieterzertifikat) das aus dem Azure-Portal heruntergeladene **Zertifikat** hoch.
    1. Klicken Sie unten auf der Seite auf **Speichern**.


### <a name="create-workware-test-user"></a>Erstellen eines Workware-Testbenutzers

1. Melden Sie sich bei der Workware-Website als Administrator an.

1. Wählen Sie **Admin > Create / View > User Accounts > Add New** (Verwaltung > Erstellen/Anzeigen > Benutzerkonten > Neu hinzufügen) aus.

1. Führen Sie auf der folgenden Seite die folgenden Schritte aus:

    ![test user](./media/workware-tutorial/create-user.png)

    a. Geben Sie im Feld **Name** einen gültigen Namen ein.

    b. Wählen Sie unter **Authentication Type** (Authentifizierungstyp) den Eintrag **SSO** aus.

    c. Füllen Sie die erforderlichen Felder aus, und klicken Sie auf **Save** (Speichern).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der Workware-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können den Microsoft-Zugriffsbereich verwenden. Wenn Sie im Zugriffsbereich auf die Kachel „Workware“ klicken, sollten Sie automatisch bei der Workware-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Workware können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.