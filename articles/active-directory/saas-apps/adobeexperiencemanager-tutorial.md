---
title: 'Tutorial: Azure Active Directory-Integration mit Adobe Experience Manager | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Adobe Experience Manager konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2021
ms.author: jeedes
ms.openlocfilehash: 6cb490408cd66d5747156ef48ea9b4b2daa92abf
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100094650"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Tutorial: Azure Active Directory-Integration mit Adobe Experience Manager

In diesem Tutorial erfahren Sie, wie Sie Adobe Experience Manager in Azure Active Directory (Azure AD) integrieren. Die Integration von Adobe Experience Manager in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Adobe Experience Manager hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Adobe Experience Manager anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Adobe Experience Manager-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Adobe Experience Manager unterstützt **SP- und IDP**-initiiertes einmaliges Anmelden.

* Adobe Experience Manager unterstützt die **Just-in-Time**-Benutzerbereitstellung.

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Hinzufügen von Adobe Experience Manager aus dem Katalog

Zum Konfigurieren der Integration von Adobe Experience Manager in Azure AD müssen Sie Adobe Experience Manager aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Adobe Experience Manager** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Adobe Experience Manager** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-adobe-experience-manager"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Adobe Experience Manager

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Adobe Experience Manager mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Adobe Experience Manager eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Adobe Experience Manager zu konfigurieren:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Adobe Experience Manager](#configure-adobe-experience-manager-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Adobe Experience Manager-Testbenutzers](#create-adobe-experience-manager-test-user)** , um eine Entsprechung von Britta Simon in Adobe Experience Manager zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Adobe Experience Manager** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** einen eindeutigen Wert ein, den Sie auch auf dem AEM-Server definieren.

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > Der Wert der Antwort-URL entspricht nicht dem tatsächlichen Wert. Aktualisieren Sie den Wert für die Antwort-URL mit der richtigen Antwort-URL. Nehmen Sie Verbindung mit dem [Clientsupportteam für Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) auf, um diesen Wert zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** die URL des Adobe Experience Manager-Servers ein.

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

7. Kopieren Sie im Abschnitt **Adobe Experience Manager einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon das einmalige Anmelden von Azure, indem Sie ihr Zugriff auf Adobe Experience Manager gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Adobe Experience Manager** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-adobe-experience-manager-sso"></a>Konfigurieren des einmaligen Anmeldens für Adobe Experience Manager

1. Öffnen Sie das Verwaltungsportal für **Adobe Experience Manager** in einem neuen Browserfenster.

2. Wählen Sie **Settings** (Einstellungen) > **Security** (Sicherheit) > **Users** (Benutzer) aus.

    ![Screenshot mit der Kachel „Users“ im Adobe Experience Manager](./media/adobe-experience-manager-tutorial/user-1.png)

3. Wählen Sie **Administrator** oder einen anderen relevanten Benutzer aus.

    ![Screenshot, in dem der Benutzer „Administrator“ hervorgehoben ist](./media/adobe-experience-manager-tutorial/tutorial-admin-6.png)

4. Klicken Sie auf **Account settings** > **Manage TrustStore** (Kontoeinstellungen > TrustStore verwalten).

    ![Screenshot, in dem „Manage TrustStore“ unter „Account settings“ hervorgehoben ist](./media/adobe-experience-manager-tutorial/manage-trust.png)

5. Klicken Sie unter **Add Certificate from CER file** (Zertifikat aus CER-Datei hinzufügen) auf **Select Certificate File** (Zertifikatdatei auswählen). Suchen Sie die Zertifikatsdatei, die Sie bereits aus dem Azure-Portal heruntergeladen haben, und wählen Sie sie aus.

    ![Screenshot, in dem die Schaltfläche „Select Certificate File“ hervorgehoben ist](./media/adobe-experience-manager-tutorial/user-2.png)

6. Das Zertifikat wird dem TrustStore hinzugefügt. Notieren Sie den Alias des Zertifikats.

    ![Screenshot, der zeigt, dass das Zertifikat zu „TrustStore“ hinzugefügt wurde](./media/adobe-experience-manager-tutorial/tutorial-admin-7.png)

7. Wählen Sie auf der Seite **Users** (Benutzer) die Option **authentication-service** aus.

    ![Sreenshot, in dem „authentication-service“ auf dem Bildschirm hervorgehoben ist](./media/adobe-experience-manager-tutorial/tutorial-admin-8.png)

8. Wählen Sie **Account Settings** (Kontoeinstellungen) > **Create/Manage KeyStore** (KeyStore erstellen/verwalten) aus. Erstellen Sie den KeyStore durch Angeben eines Kennworts.

    ![Screenshot, in dem „Manage KeyStore“ hervorgehoben ist](./media/adobe-experience-manager-tutorial/tutorial-admin-9.png)

9. Wechseln Sie zurück zum Verwaltungsbildschirm. Klicken Sie dann auf **Settings** > **Operations** > **Web Console** (Einstellungen > Vorgänge > Webkonsole).

    ![Screenshot, in dem im Abschnitt „Settings“ unter „Operations“ die Option „Web Console“ hervorgehoben ist](./media/adobe-experience-manager-tutorial/tutorial-admin-1.png)

    Dadurch wird die Konfigurationsseite geöffnet.

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/adobe-experience-manager-tutorial/tutorial-admin-2.png)

10. Suchen Sie **Adobe Granite SAML 2.0 Authentication Handler**. Klicken Sie dann auf das Symbol zum **Hinzufügen**.

    ![Screenshot, in dem „Adobe Granite SAML 2.0 Authentication Handler“ hervorgehoben ist](./media/adobe-experience-manager-tutorial/tutorial-admin-3.png)

11. Führen Sie auf dieser Seite die folgenden Aktionen aus:

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/adobe-experience-manager-tutorial/tutorial-admin-4.png)

    a. Geben Sie im Feld **Path** (Pfad) **/** ein.

    b. Fügen Sie im Feld **IDP URL** (IdP-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Geben Sie im Feld **IDP Certificate Alias** (Alias des IdP-Zertifikats) den Wert von **Certificate Alias** (Zertifikatalias) ein, den Sie in TrustStore hinzugefügt haben.

    d. Geben Sie im Feld **Security Provided Entity ID** (Sicherheits-Entitäts-ID) den eindeutigen **Azure AD-Bezeichner** ein, den Sie im Azure-Portal konfiguriert haben.

    e. Geben Sie im Feld **Assertion Consumer Service URL** (Assertionsverbraucherdienst-URL) den Wert der **Antwort-URL** ein, den Sie im Azure-Portal konfiguriert haben.

    f. Geben Sie im Feld **Password of Key Store** (Kennwort des Schlüsselspeichers) das **Kennwort** ein, das Sie in KeyStore festgelegt haben.

    g. Geben Sie im Feld **User Attribute ID** (Benutzerattribut-ID) die **Namens-ID** oder eine andere relevante Benutzer-ID ein.

    h. Aktivieren Sie **Autocreate CRX Users** (CRX-Benutzer automatisch erstellen).

    i. Geben Sie im Feld **Logout URL** (Abmelde-URL) den Wert der eindeutigen **Abmelde-URL** ein, den Sie im Azure-Portal erhalten haben.

    j. Wählen Sie **Speichern** aus.

### <a name="create-adobe-experience-manager-test-user"></a>Erstellen eines Adobe Experience Manager-Testbenutzers

In diesem Abschnitt erstellen Sie in Adobe Experience Manager eine Benutzerin namens Britta Simon. Wenn Sie die Option **Autocreate CRX Users** (CRX-Benutzer automatisch erstellen) aktiviert haben, werden nach der erfolgreichen Authentifizierung automatisch Benutzer erstellt.

Wenn Sie Benutzer manuell erstellen möchten, wenden Sie sich an das [Adobe Experience Manager-Supportteam](https://helpx.adobe.com/support/experience-manager.html), um die Benutzer auf der Adobe Experience Manager-Plattform hinzuzufügen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Adobe Experience Manager weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die Adobe Experience Manager-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Adobe Experience Manager-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Adobe Experience Manager“ unter „Meine Apps“ geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Adobe Experience Manager-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Adobe Experience Manager können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.
