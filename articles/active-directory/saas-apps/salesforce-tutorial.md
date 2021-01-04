---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Salesforce | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Salesforce konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 625058b131ef5cb6180873806185a3202a766118
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675559"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Salesforce

In diesem Tutorial erfahren Sie, wie Sie Salesforce in Azure Active Directory (Azure AD) integrieren. Die Integration von Salesforce in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Salesforce hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Salesforce anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Salesforce-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Salesforce unterstützt **SP** -initiiertes einmaliges Anmelden.

* Salesforce unterstützt die [**automatisierte** Benutzerbereitstellung und Bereitstellungsaufhebung](salesforce-provisioning-tutorial.md) (empfohlen).

* Salesforce unterstützt die **Just-in-Time** -Benutzerbereitstellung.

* Die mobile Salesforce-Anwendungen kann nun mit Azure AD konfiguriert werden, um SSO zu ermöglichen. In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

## <a name="adding-salesforce-from-the-gallery"></a>Hinzufügen von Salesforce aus dem Katalog

Zum Konfigurieren der Integration von Salesforce in Azure AD müssen Sie Salesforce aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Salesforce** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Salesforce** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-salesforce"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Salesforce

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Salesforce mithilfe eines Testbenutzers mit dem Namen **B. Simon** . Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Salesforce eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Salesforce die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Salesforce](#configure-salesforce-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * **[Erstellen eines Salesforce-Testbenutzers](#create-salesforce-test-user)** , um ein Pendant von B. Simon in Salesforce zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Salesforce** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Anmelde-URL** den Wert im folgenden Format ein:

    Enterprise-Konto: `https://<subdomain>.my.salesforce.com`

    Developer-Konto: `https://<subdomain>-dev-ed.my.salesforce.com`
    
    b. Geben Sie im Textfeld **Antwort-URL** den Wert nach folgendem Muster ein:

    Enterprise-Konto: `https://<subdomain>.my.salesforce.com`

    Developer-Konto: `https://<subdomain>-dev-ed.my.salesforce.com`

    c. Geben Sie im Textfeld **Bezeichner** den Wert in folgendem Format ein:

    Enterprise-Konto: `https://<subdomain>.my.salesforce.com`

    Developer-Konto: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Kundensupportteam von Salesforce](https://help.salesforce.com/support), um diese Werte zu erhalten.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML** , und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Salesforce einrichten** die entsprechenden URLs basierend auf Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Salesforce gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Liste der Anwendungen **Salesforce** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen** .
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

## <a name="configure-salesforce-sso"></a>Konfigurieren des einmaligen Anmeldens für Salesforce

1. Wenn Sie die Konfiguration in Salesforce automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

1. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Salesforce einrichten** , um zur Salesforce-SSO-Anwendung weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Salesforce anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 13.

    ![Einrichtungskonfiguration](common/setup-sso.png)

1. Wenn Sie Salesforce manuell einrichten möchten, melden Sie sich in einem neuen Webbrowserfenster bei der Salesforce-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

1. Klicken Sie in der oberen rechten Ecke der Seite unter dem **Einstellungssymbol** auf **Setup** .

    ![Symbol zum Konfigurieren der Einstellungen für einmaliges Anmelden](./media/salesforce-tutorial/configure1.png)

1. Scrollen Sie im Navigationsbereich nach unten bis zu den **SETTINGS** (EINSTELLUNGEN), und klicken Sie auf **Identity** (Identität), um den zugehörigen Bereich zu erweitern. Klicken Sie dann auf **Einstellungen für einmaliges Anmelden** .

    ![Konfigurieren der Einstellungen für einmaliges Anmelden](./media/salesforce-tutorial/sf-admin-sso.png)

1. Klicken Sie auf der Seite **Einstellungen für einmaliges Anmelden** auf die Schaltfläche **Bearbeiten** .

    ![Konfigurieren des einmaligen Anmeldens: Bearbeiten](./media/salesforce-tutorial/sf-admin-sso-edit.png)

    > [!NOTE]
    > Wenn Sie keine Einstellungen für die einmalige Anmeldung für Ihr Salesforce-Konto aktivieren können, müssen Sie sich an das [Kundensupportteam von Salesforce](https://help.salesforce.com/support) wenden.

1. Wählen Sie **SAML aktiviert** , und klicken Sie dann auf **Speichern** .

    ![Konfigurieren des einmaligen Anmeldens: SAML aktiviert](./media/salesforce-tutorial/sf-enable-saml.png)

1. Klicken Sie auf **Neu aus Metadatendatei** , um Ihre SAML-Einstellungen für einmaliges Anmelden zu konfigurieren.

    ![Konfigurieren des einmaligen Anmeldens: Neu aus Metadatendatei](./media/salesforce-tutorial/sf-admin-sso-new.png)

1. Klicken Sie auf **Datei auswählen** , um die Metadaten-XML-Datei hochzuladen, die Sie aus dem Azure-Portal heruntergeladen haben, und klicken Sie dann auf **Erstellen** .

    ![Konfigurieren des einmaligen Anmeldens: Datei auswählen](./media/salesforce-tutorial/xmlchoose.png)

1. Auf der Seite **SAML-Einstellungen für einmaliges Anmelden** werden die Felder automatisch aufgefüllt. Wählen Sie **Benutzerbereitstellung aktiviert** aus, und klicken Sie dann auf **Speichern** .

    ![Konfigurieren des einmaligen Anmeldens: Benutzerbereitstellung aktiviert](./media/salesforce-tutorial/salesforcexml.png)

1. Klicken Sie in Salesforce im linken Navigationsbereich auf **Company Settings** (Unternehmenseinstellungen), um den zugehörigen Abschnitt zu erweitern, und klicken Sie dann auf **My Domain** (Meine Domäne).

    ![Konfigurieren des einmaligen Anmeldens: My Domain (Meine Domäne)](./media/salesforce-tutorial/sf-my-domain.png)

1. Führen Sie einen Bildlauf nach unten zum Abschnitt **Authentifizierungskonfiguration** aus, und klicken Sie auf die Schaltfläche **Bearbeiten** .

    ![Konfigurieren des einmaligen Anmeldens: Authentifizierungskonfiguration](./media/salesforce-tutorial/sf-edit-auth-config.png)

1. Wählen Sie im Abschnitt **Konfiguration der Authentifizierung** die Option **AzureSSO** als **Authentifizierungsdienst** Ihrer SAML-SSO-Konfiguration, und klicken Sie dann auf **Speichern** .

    ![Konfigurieren des einmaligen Anmeldens: Authentifizierungsdienst](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Wenn mehrere Authentifizierungsdienste aktiviert sind, werden Benutzer aufgefordert, einen Authentifizierungsdienst zur Anmeldung auszuwählen, wenn das einmalige Anmelden bei der Salesforce-Umgebung initiiert wird. Wenn Sie dies nicht möchten, sollten Sie **alle anderen Authentifizierungsdienste deaktiviert lassen** .

### <a name="create-salesforce-test-user"></a>Erstellen eines Salesforce-Testbenutzers

In diesem Abschnitt wird ein Benutzer mit dem Namen B. Simon in Salesforce erstellt. Salesforce unterstützt die Just-in-Time-Bereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Falls ein Benutzer nicht bereits in Salesforce vorhanden ist, wird beim Versuch, auf Salesforce zuzugreifen, ein neuer Benutzer erstellt. Außerdem unterstützt Salesforce die automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](salesforce-provisioning-tutorial.md).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

1. Klicken Sie im Azure-Portal auf **Diese Anwendung testen** . Dadurch werden Sie zur Anmelde-URL für Salesforce weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

2. Rufen Sie direkt die Salesforce-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

3. Sie können den Microsoft-Zugriffsbereich verwenden. Wenn Sie im Zugriffsbereich auf die Kachel „Salesforce“ klicken, sollten Sie automatisch bei der Salesforce-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="test-sso-for-salesforce-mobile"></a>Testen des einmaligen Anmeldens für Salesforce (mobil)

1. Öffnen Sie die mobile Salesforce-Anwendung. Klicken Sie auf der Anmeldeseite auf **Use Custom Domain** (Benutzerdefinierte Domäne verwenden).

    ![Mobile Salesforce-App: Use Custom Domain (Benutzerdefinierte Domäne verwenden)](media/salesforce-tutorial/mobile-app1.png)

1. Geben Sie in das Textfeld **Custom Domain** (Benutzerdefinierte Domäne) Ihren registrierten benutzerdefinierten Domänenamen ein, und klicken Sie auf **Continue** (Weiter).

    ![Mobile Salesforce-App: Custom Domain (Benutzerdefinierte Domäne)](media/salesforce-tutorial/mobile-app2.png)

1. Geben Sie Ihre Azure AD-Anmeldeinformationen ein, um sich bei der Salesforce-Anwendung anzumelden, und klicken Sie auf **Next** (Weiter).

    ![Mobile Salesforce-App: Azure AD-Anmeldeinformationen](media/salesforce-tutorial/mobile-app3.png)

1. Klicken Sie auf der nachfolgend gezeigten Seite **Allow Access** (Zugriff zulassen) auf **Allow** (Zulassen), um den Zugriff auf die Salesforce-Anwendung zuzulassen.

    ![Mobile Salesforce-App: Allow Access (Zugriff zulassen)](media/salesforce-tutorial/mobile-app4.png)

1. Nach erfolgreicher Anmeldung wird die Startseite der Anwendung angezeigt:

    ![Startseite der mobilen Salesforce-App](media/salesforce-tutorial/mobile-app5.png) ![Mobile Salesforce-App](media/salesforce-tutorial/mobile-app6.png)

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Salesforce können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-aad) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.