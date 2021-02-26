---
title: 'Tutorial: Azure Active Directory-Integration in Adobe Sign | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Adobe Sign konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: f8566386e73ab361f3078d5e6d27f92c51856afa
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259451"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Tutorial: Azure Active Directory-Integration in Adobe Sign

In diesem Tutorial erfahren Sie, wie Sie Adobe Sign in Azure Active Directory (Azure AD) integrieren. Die Integration von Adobe Sign in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Adobe Sign hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Adobe Sign anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:
 
* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Adobe Sign-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Adobe Sign unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="add-adobe-sign-from-the-gallery"></a>Hinzufügen von Adobe Sign aus dem Katalog

Zum Konfigurieren der Integration von Azure AD in Adobe Sign müssen Sie Adobe Sign aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Adobe Sign** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Adobe Sign** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-adobe-sign"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Adobe Sign

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Adobe Sign anhand eines Testbenutzers namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Adobe Sign eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Adobe Sign die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Konfigurieren des einmaligen Anmeldens für Adobe Sign](#configure-adobe-sign-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Adobe Sign-Testbenutzers](#create-adobe-sign-test-user)** , um eine Entsprechung von Britta Simon in Adobe Sign zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Adobe Sign die folgenden Schritte aus:

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Adobe Sign** auf **Einmaliges Anmelden**.

1. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<companyname>.echosign.com/`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<companyname>.echosign.com`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Supportteam von Adobe Sign](https://helpx.adobe.com/in/contact/support.html), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Adobe Sign einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Adobe Sign gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Adobe Sign** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="configure-adobe-sign-sso"></a>Konfigurieren des einmaligen Anmeldens für Adobe Sign

1. Wenden Sie sich vor der Konfiguration an das [Supportteam für den Adobe Sign-Client](https://helpx.adobe.com/in/contact/support.html), um Ihre Domäne zur Adobe Sign-Zulassungsliste hinzufügen zu lassen. So fügen Sie die Domäne hinzu:

    a. Das [Adobe Sign-Supportteam](https://helpx.adobe.com/in/contact/support.html) sendet Ihnen ein zufällig generiertes Token. Für Ihre Domäne sieht das Token wie folgt aus: **adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Veröffentlichen Sie das Überprüfungstoken in einem DNS-Texteintrag, und benachrichtigen Sie das [Adobe Sign-Supportteam](https://helpx.adobe.com/in/contact/support.html).

    > [!NOTE]
    > Die Bearbeitung kann ein paar Tage oder länger dauern. Beachten Sie, dass Verzögerungen bei der DNS-Verteilung bedeuten, dass ein in DNS veröffentlichter Wert möglicherweise mindestens eine Stunde nicht angezeigt wird. Ihr IT-Administrator weiß, wie dieses Token in einem DNS-Texteintrag veröffentlicht wird.

    c. Wenn Sie das [Adobe Sign-Supportteam](https://helpx.adobe.com/in/contact/support.html) nach der Tokenveröffentlichung über das Supportticket benachrichtigen, überprüft das Team die Domäne und fügt sie Ihrem Konto hinzu.

    d. So veröffentlichen Sie generell ein Token in einem DNS-Eintrag

    * Melden Sie sich in Ihrem Domänenkonto an.
    * Suchen Sie die Seite zum Aktualisieren des DNS-Eintrags. Diese Seite heißt möglicherweise „DNS-Verwaltung“, „Namensserververwaltung“, „Erweiterte Einstellungen“ usw.
    * Suchen Sie die TXT-Einträge für Ihre Domäne.
    * Fügen Sie einen TXT-Eintrag mit dem vollständigen Tokenwert hinzu, den Adobe bereitgestellt hat.
    * Speichern Sie die Änderungen.

1. Melden Sie sich in einem anderen Webbrowserfenster auf der Adobe Sign-Unternehmenswebsite als Administrator an.

1. Rufen Sie im SAML-Menü **Account Settings** (Kontoeinstellungen)  > **SAML Settings** (SAML-Einstellungen) auf.

    ![Screenshot der Adobe Sign-SAML-Einstellungsseite](./media/adobe-echosign-tutorial/settings.png "Konto")

1. Führen Sie im Abschnitt für die **SAML-Einstellungen** die folgenden Schritte aus:

    ![Screenshot, in dem die S A M L-Einstellungen mit „S A M L Mandatory“ hervorgehoben sind](./media/adobe-echosign-tutorial/saml1.png "SAML-Einstellungen")

   ![Screenshot der SAML-Einstellungen](./media/adobe-echosign-tutorial/saml.png "SAML-Einstellungen")

   a. Wählen Sie als **SAML Mode** (SAML-Modus) **SAML Mandatory** (SAML erforderlich) aus.

   b. Aktivieren Sie **Allow Echosign Account Administrators to log in using their Echosign Credentials** (EchoSign-Kontoadministratoren dürfen sich mit ihren EchoSign-Anmeldeinformationen anmelden).

   c. Wählen Sie unter **User Creation** (Benutzererstellung) **Automatically add users authenticated through SAML** (SAML-authentifizierte Benutzer automatisch hinzufügen) aus.

   d. Fügen Sie den **Azure AD-Bezeichner**, den Sie aus dem Azure-Portal kopiert haben, in das Textfeld **Idp Entity ID** (IdP-Entitäts-ID) ein.

   e. Fügen Sie in das Textfeld **Idp Login URL** (IdP-Anmelde-URL) die **Anmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.

   f. Fügen Sie in das Textfeld **Idp Logout URL** (IdP-Abmelde-URL) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

   g. Öffnen Sie die heruntergeladene **Zertifikat (Base64)** -Datei im Editor. Kopieren Sie den Inhalt in die Zwischenablage, und fügen Sie ihn in das Textfeld **IdP Certificate** (IdP-Zertifikat) ein.

   h. Klicken Sie auf **Save changes** (Änderungen speichern).

### <a name="create-adobe-sign-test-user"></a>Erstellen eines Adobe Sign-Testbenutzers

Damit sich Azure AD-Benutzer bei Adobe Sign anmelden können, müssen sie in Adobe Sign bereitgestellt werden. Diese Aufgabe muss manuell ausgeführt werden.

>[!NOTE]
>Sie können Azure AD-Benutzerkonten auch mit anderen Tools zum Erstellen von Adobe Sign-Benutzerkonten oder mit den von Adobe Sign bereitgestellten APIs erstellen. 

1. Melden Sie sich auf der **Adobe Sign**-Unternehmenswebsite als Administrator an.

2. Wählen Sie oben im Menü **Account** (Konto) aus. Wählen Sie im linken Bereich **Users & Groups** (Benutzer & Gruppen)  > **Create a new user** (Neuen Benutzer erstellen) aus.

    ![Screenshot der Adobe Sign-Unternehmenswebsite mit Hervorhebung von „Account“ (Konto), „Users & Groups“ (Benutzer & Gruppen) und „Create a new user“ (Neuen Benutzer erstellen)](./media/adobe-echosign-tutorial/account.png "Konto")

3. Führen Sie im Abschnitt **Create New User** die folgenden Schritte aus:

    ![Screenshot des Abschnitts „Create New User“ (Neuen Benutzer erstellen)](./media/adobe-echosign-tutorial/user.png "Benutzer erstellen")

    a. Geben Sie in die Textfelder **Email Address** (E-Mail-Adresse), **First Name** (Vorname) und **Last Name** (Nachname) die entsprechenden Informationen eines gültigen Azure AD-Kontos ein, das Sie bereitstellen möchten.

    b. Klicken Sie auf **Create User** (Benutzer erstellen).

>[!NOTE]
>Der Besitzer des Azure Active Directory-Kontos erhält eine E-Mail mit einem Bestätigungslink für das Konto, bevor es aktiv wird. 

### <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Adobe Sign weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die Adobe Sign-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Adobe Sign“ klicken, sollten Sie automatisch bei der Adobe Sign-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Adobe Sign können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.
