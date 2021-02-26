---
title: 'Tutorial: Azure Active Directory-Integration mit Everbridge | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Everbridge konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 37a59d6da6fdc844b0b3647c029d716429b50ef6
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430836"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Tutorial: Azure Active Directory-Integration mit Everbridge

In diesem Tutorial erfahren Sie, wie Sie Everbridge in Azure Active Directory (Azure AD) integrieren.
Bei der Integration von Everbridge in Azure AD haben Sie folgende Möglichkeiten:

* Steuern Sie in Azure AD, wer Zugriff auf Everbridge hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Everbridge anzumelden (einmaliges Anmelden; Single Sign-On, SSO). Diese Zugriffssteuerung wird als einmaliges Anmelden (Single Sign-On, SSO) bezeichnet.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Everbridge konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Everbridge-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Everbridge unterstützt IDP-initiiertes einmaliges Anmelden.

## <a name="add-everbridge-from-the-gallery"></a>Hinzufügen von Everbridge über den Katalog

Zum Konfigurieren der Integration von Everbridge in Azure AD müssen Sie Everbridge über den Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Everbridge** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Everbridge** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-everbridge"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Everbridge

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Everbridge mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Everbridge eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Everbridge die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Everbridge](#configure-everbridge-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Everbridge-Testbenutzers](#create-everbridge-test-user)** , um ein Pendant von B. Simon in Everbridge zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Everbridge** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

    >[!NOTE]
    >Konfigurieren Sie die Anwendung sowohl im Azure-Portal als auch im Everbridge-Portal entweder als Manager-Portal *oder* als Mitgliederportal.

4. Um die Anwendung **Everbridge** als **Everbridge-Manager-Portal** zu konfigurieren, führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Everbridge](common/idp-intiated.png)

    a. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein:
    `https://sso.everbridge.net/<API_Name>`

    b. Geben Sie im Feld **Antwort-URL** eine URL im folgenden Format ein:
    `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit dem tatsächlichen Bezeichner und der Antwort-URL. Diese Werte erhalten Sie vom [Everbridge-Supportteam](mailto:support@everbridge.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Um die Anwendung **Everbridge** als **Everbridge-Mitgliederportal** zu konfigurieren, führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

  * Wenn Sie die Anwendung im SP-initiierten Modus konfigurieren möchten, führen Sie die folgenden Schritte aus:

     ![SSO-Informationen zur Domäne und zu den URLs für Everbridge für den IDP-initiierten Modus](common/idp-intiated.png)

    a. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein: `https://sso.everbridge.net/<API_Name>/<Organization_ID>`.

    b. Geben Sie im Feld **Antwort-URL** eine URL im folgenden Format ein: `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`.

   * Wenn Sie die Anwendung im SP-initiierten Modus konfigurieren möchten, wählen Sie **Zusätzliche URLs festlegen** aus, und führen Sie den folgenden Schritt aus:

     ![SSO-Informationen zur Domäne und zu den URLs für Everbridge für den SP-initiierten Modus](common/both-signonurl.png)

     a. Geben Sie im Feld **Anmelde-URL** eine URL im folgenden Format ein: `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`.

     > [!NOTE]
     > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächlichen Werte für Bezeichner, Antwort-URL und Anmelde-URL. Diese Werte erhalten Sie vom [Everbridge-Supportteam](mailto:support@everbridge.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

6. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die Option **Herunterladen** aus, um die **Verbundmetadaten-XML** herunterzuladen. Speichern Sie das Zertifikat auf Ihrem Computer.

    ![Downloadlink für Zertifikat](common/metadataxml.png)

7. Kopieren Sie im Abschnitt **Everbridge einrichten** die URLs gemäß Ihren Anforderungen:

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Everbridge gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **Everbridge** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="configure-everbridge-sso"></a>Konfigurieren des einmaligen Anmeldens für Everbridge

Führen Sie die folgenden Schritte aus, um SSO in **Everbridge** als **Everbridge -Manager-Portalanwendung** zu konfigurieren.
 
1. Melden Sie sich in einem anderen Webbrowserfenster als Administrator bei Everbridge an.

1. Wählen Sie im oberen Menü die Registerkarte **Settings** (Einstellungen) aus. Wählen Sie unter **Security** (Sicherheit) die Option **Single Sign-On** (Einmaliges Anmelden) aus.
   
     ![Einmaliges Anmelden konfigurieren](./media/everbridge-tutorial/sso.png)
   
     a. Geben Sie im Feld **Name** den Namen des Identitätsanbieters ein. Ein Beispiel dafür ist der Name Ihres Unternehmens.
   
     b. Geben Sie im Feld **API Name** (API-Name) den Namen der API ein.
   
     c. Wählen Sie **Choose File** (Datei auswählen) aus, um die Metadatendatei hochzuladen, die Sie aus dem Azure-Portal heruntergeladen haben.
   
     d. Wählen Sie für **SAML Identity Location** (SAML-Identitätsstandort) die Option **Identity is in the NameIdentifier element of the Subject statement** (Identität befindet sich im NameIdentifier-Element der Ausstellererklärung).
   
     e. Fügen Sie in das Feld **Identity Provider Login URL** (Anmelde-URL des Identitätsanbieters) die **Anmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.
   
     f. Wählen Sie für **Service Provider initiated Request Binding** (Vom Dienstanbieter initiierte Anforderungsbindung) die Einstellung **HTTP Redirect** (HTTP-Umleitung) aus.

     g. Wählen Sie **Speichern** aus.

### <a name="configure-everbridge-as-everbridge-member-portal-sso"></a>Konfigurieren von Everbridge als Everbridge-Mitgliederportal mit einmaligem Anmelden

Um einmaliges Anmelden für **Everbridge** als **Everbridge-Mitgliederportal** zu konfigurieren, senden Sie die heruntergeladene **Verbundmetadaten-XML-Datei** an das [Everbridge-Supportteam](mailto:support@everbridge.com). Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-everbridge-test-user"></a>Erstellen eines Everbridge-Testbenutzers

In diesem Abschnitt erstellen Sie in Everbridge den Testbenutzer Britta Simon. Wenn Sie Benutzer auf der Everbridge-Plattform hinzufügen möchten, wenden Sie sich an das [Supportteam von Everbridge](mailto:support@everbridge.com). Benutzer müssen in Everbridge erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können. 

### <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der Everbridge-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Everbridge“ klicken, sollten Sie automatisch bei der Everbridge-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Everbridge können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.