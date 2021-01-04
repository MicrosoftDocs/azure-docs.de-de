---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Sonarqube | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Sonarqube konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: jeedes
ms.openlocfilehash: f756e738989775bf9c06b44a03f002c14f42e3ac
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182327"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sonarqube"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Sonarqube

In diesem Tutorial erfahren Sie, wie Sie Sonarqube in Azure Active Directory (Azure AD) integrieren. Die Integration von Sonarqube in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Sonarqube hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Sonarqube anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Sonarqube-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Sonarqube unterstützt **SP-initiiertes** einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="adding-sonarqube-from-the-gallery"></a>Hinzufügen von Sonarqube aus dem Katalog

Zum Konfigurieren der Integration von Sonarqube in Azure AD müssen Sie Sonarqube aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Sonarqube** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Sonarqube** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-sonarqube"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für SonarQube

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Sonarqube mithilfe eines Testbenutzers namens **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Sonarqube eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit SonarQube die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Sonarqube](#configure-sonarqube-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Sonarqube-Testbenutzers](#create-sonarqube-test-user)** , um ein Pendant von B. Simon in Sonarqube zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **SonarQube** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    Geben Sie im Textfeld **Anmelde-URL** eine URL ein:

    * **Für die Produktionsumgebung:**

        `https://servicessonar.corp.microsoft.com/`

    * **Für die Entwicklungsumgebung:**

        `https://servicescode-dev.westus.cloudapp.azure.com`

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Sonarqube einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Sonarqube gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Sonarqube** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.

1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-sonarqube-sso"></a>Konfigurieren des einmaligen Anmeldens für Sonarqube

1. Melden Sie sich in einem neuen Webbrowserfenster bei der Sonarqube-Unternehmenswebsite als Administrator an.

1. Klicken Sie auf **Administration > Configuration > Security** (Verwaltung > Konfiguration > Sicherheit), und navigieren Sie zum **SAML-Plug-In**, um die folgenden Schritte auszuführen.

1. Kopieren Sie die folgenden Details aus den IdP-Metadaten, und fügen Sie sie im SonarQube-Plug-In in die entsprechenden Textfelder ein.
    1. IdP-Entitäts-ID
    2. Anmelde-URL
    3. X.509-Zertifikat 
1. Speichern Sie alle Details.
    ![SAML-Plug-In: IDP](./media/sonarqube-tutorial/sso-idp-metadata.png)

1. Führen Sie auf der Seite **SAML** die folgenden Schritte aus:

    ![Sonarqube-Konfiguration](./media/sonarqube-tutorial/config01.png)

    a. Legen Sie die Option **Enabled** (Aktiviert) auf **yes** (ja) fest.

    b. Geben Sie im Textfeld **Application ID** (Anwendungs-ID) den Namen ein, etwa **sonarqube**.

    c. Geben Sie im Textfeld **Provider Name** (Anbietername) den Namen ein, etwa **SAML**.

    d. Fügen Sie im Textfeld **Provider ID** (Anbieter-ID) den Wert für **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    e. Fügen Sie in das Textfeld **SAML login url** (SAML-Anmelde-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    f. Öffnen Sie das Base64-codierte Zertifikat im Editor, kopieren Sie den Inhalt, und fügen Sie ihn in das Textfeld **Provider Certificate** (Anbieterzertifikat) ein.

    g. Geben Sie im Textfeld **SAML user login attribute** (Attribut der SAML-Benutzeranmeldung) den Wert `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` ein.

    h. Geben Sie im Textfeld **SAML user name attribute** (Attribut des SAML-Benutzernamens) den Wert `http://schemas.microsoft.com/identity/claims/displayname` ein.

    i. Geben Sie im Textfeld **SAML user email attribute** (Attribut der SAML-Benutzer-E-Mail) den Wert `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` ein.

    j. Klicken Sie auf **Speichern**.

### <a name="create-sonarqube-test-user"></a>Erstellen eines Sonarqube-Testbenutzers

In diesem Abschnitt erstellen Sie in Sonarqube einen Benutzer namens B. Simon. Wenden Sie sich an das [Supportteam für den Sonarqube-Client](https://www.sonarsource.com/support/), um die Benutzer auf der Sonarqube-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können. 

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

1. Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für SonarQube weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

2. Rufen Sie direkt die SonarQube-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

3. Sie können den Microsoft-Zugriffsbereich verwenden. Wenn Sie im Zugriffsbereich auf die Kachel „SonarQube“ klicken, werden Sie zur Anmelde-URL für SonarQube umgeleitet. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

* Nach dem Konfigurieren von SonarQube können Sie Sitzungssteuerungen erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützen. Sitzungssteuerungen basieren auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.