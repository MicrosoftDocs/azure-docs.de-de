---
title: 'Tutorial: Integration des einmaligen Anmeldens von Azure Active Directory mit Looker Analytics Platform | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Looker Analytics Platform konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/10/2020
ms.author: jeedes
ms.openlocfilehash: dbb6f6d278256730e77677e78f452615fe4b611e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180739"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-looker-analytics-platform"></a>Tutorial: Integration des einmaligen Anmeldens von Azure Active Directory mit Looker Analytics Platform

In diesem Tutorial erfahren Sie, wie Sie Looker Analytics Platform in Azure Active Directory (Azure AD) integrieren. Die Integration von Looker Analytics Platform in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Looker Analytics Platform hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Looker Analytics Platform anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Looker Analytics Platform-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Looker Analytics Platform unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.
* Looker Analytics Platform unterstützt die **Just-In-Time**-Benutzerbereitstellung.


## <a name="adding-looker-analytics-platform-from-the-gallery"></a>Hinzufügen von Looker Analytics Platform aus dem Katalog

Zum Konfigurieren der Integration von Looker Analytics Platform in Azure AD müssen Sie Looker Analytics Platform aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Looker Analytics Platform** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Looker Analytics Platform** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-looker-analytics-platform"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Looker Analytics Platform

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Looker Analytics Platform mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Looker Analytics Platform eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Looker Analytics Platform die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Looker Analytics Platform](#configure-looker-analytics-platform-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Looker Analytics Platform-Testbenutzers](#create-looker-analytics-platform-test-user)** , um ein Pendant von B. Simon in Looker Analytics Platform zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Looker Analytics Platform** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `<SPN>_looker`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.looker.com/samlcallback`

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.looker.com`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Diese Werte erhalten Sie vom [Kundensupportteam von Looker Analytics Platform](mailto:support@looker.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Looker Analytics Platform einrichten** die entsprechenden URLs basierend auf Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Looker Analytics Platform gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Looker Analytics Platform** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-looker-analytics-platform-sso"></a>Konfigurieren des einmaligen Anmeldens für Looker Analytics Platform

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Looker Analytics Platform-Website als Administrator an.

1. Wechseln Sie zu **Admin** -> **Authentication** -> **SAML** (Verwaltung > Authentifizierung > SAML).

    ![Screenshot der SAML-Option](./media/looker-analytics-platform-tutorial/admin.png)

1. Fügen Sie die **Verbundmetadaten**, die Sie aus dem Azure-Portal kopiert haben, in das Textfeld **IDP Metadata** (IDP-Metadaten) ein, und klicken Sie auf **Load** (Laden).

    ![Screenshot zum Hochladen von Metadaten](./media/looker-analytics-platform-tutorial/metadata.png)

1. Führen Sie im Abschnitt **User Attribute Settings** (Benutzerattributeinstellungen) die folgenden Schritte aus.

    ![Screenshot der Benutzerattributeinstellungen](./media/looker-analytics-platform-tutorial/user-attribute-settings.png)

    a. Fügen Sie im Feld „Email Attr“ (Email-Attribut) den folgenden Wert hinzu: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    b. Fügen Sie im Feld „FName Attr“ (FName-Attribut) den folgenden Wert hinzu: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    c. Fügen Sie im Feld „LName Attr“ (LName-Attribut) den folgenden Wert hinzu: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    d. Klicken Sie unter **Test User Authentication** (Benutzerauthentifizierung testen) auf **Test SAML Authentication** (SAML-Authentifizierung testen). Wenn eine Seite mit der Meldung „Server Response Successfully Validated“ (Serverantwort erfolgreich überprüft) geladen wird, haben Sie die Instanz erfolgreich für die SAML-Integration eingerichtet.
    
    e. Aktivieren Sie unter **Save and Apply Settings** (Einstellungen speichern und anwenden) das Kontrollkästchen **I have confirmed the configuration above and want to enable applying it globally** (Ich habe die obige Konfiguration überprüft und möchte sie global anwenden).

    f. Klicken Sie auf die Schaltfläche **Update settings** (Einstellungen aktualisieren).

### <a name="create-looker-analytics-platform-test-user"></a>Erstellen eines Looker Analytics Platform-Testbenutzers

In diesem Abschnitt wird in Looker Analytics Platform ein Benutzer mit dem Namen Britta Simon erstellt. Looker Analytics Platform unterstützt die Just-in-Time-Benutzerbereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in Looker Analytics Platform vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Looker Analytics Platform weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die Looker Analytics Platform-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Looker Analytics Platform-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Zugriffsbereich verwenden, um die Anwendung in einem beliebigen Modus zu testen. Wenn Sie im Zugriffsbereich auf die Kachel „Looker Analytics Platform“ klicken, geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet, und wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Looker Analytics Platform-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Looker Analytics Platform können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.