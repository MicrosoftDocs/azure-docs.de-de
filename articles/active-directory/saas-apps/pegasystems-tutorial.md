---
title: 'Tutorial: Azure Active Directory-Integration mit Pega Systems | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Pega Systems konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 4cd767736d6349199f4c82b00cb0b35db36cdb44
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430137"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Tutorial: Azure Active Directory-Integration mit Pega Systems

In diesem Tutorial erfahren Sie, wie Sie Pega Systems in Azure Active Directory (Azure AD) integrieren. Die Integration von Pega Systems in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Pega Systems hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Pega Systems anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Pega Systems-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Pega Systems unterstützt SP- und IDP-initiiertes einmaliges Anmelden.

## <a name="add-pega-systems-from-the-gallery"></a>Hinzufügen von Pega Systems aus dem Katalog

Um die Integration von Pega Systems in Azure AD zu konfigurieren, müssen Sie Pega Systems aus dem Katalog zu Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Pega Systems** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Pega Systems** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-pega-systems"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Pega Systems

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Pega Systems mithilfe eines Testbenutzers namens **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Pega Systems eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Pega Systems die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Pega Systems](#configure-pega-systems-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Pega Systems-Testbenutzers](#create-pega-systems-test-user)** , um ein Pendant von B. Simon in Pega Systems zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Pega Systems** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Dialogfeld **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im IDP-initiierten Modus konfigurieren möchten.

    ![Dialogfeld „Grundlegende SAML-Konfiguration“](common/idp-intiated.png)

    1. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. Geben Sie im Feld **Antwort-URL** eine URL im folgenden Format ein:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Wenn Sie die Anwendung im SP-initiierten Modus konfigurieren möchten, klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie die folgenden Schritte aus.

    ![SSO-Informationen zur Domäne und zu den URLs für Pega Systems](common/both-advanced-urls.png)

    1. Geben Sie im Feld **Anmelde-URL** den Wert der Anmelde-URL ein.

    1. Geben Sie im Feld **Relayzustand** eine URL im folgenden Format ein: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Die hier angegebenen Werte sind Platzhalter. Sie müssen die tatsächlichen Werte für Bezeichner, Antwort-URL, Anmelde-URL und Relayzustand-URL verwenden. Sie können die Werte für den Bezeichner und die Antwort-URL aus einer Pega-Anwendung abrufen, wie es weiter unten in diesem Tutorial beschrieben ist. Wenden Sie sich an das [Supportteam von Pega Systems](https://www.pega.com/contact-us), um den Wert für den Relayzustand zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

6. Für die Pega Systems-Anwendung müssen die SAML-Assertionen in einem bestimmten Format vorliegen. Um sie im richtigen Format zu erhalten, müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Standardattribute. Wählen Sie das Symbol **Bearbeiten** aus, um das Dialogfeld **Benutzerattribute** zu öffnen:

    ![Benutzerattribute](common/edit-attribute.png)

7. Zusätzlich zu den im vorherigen Screenshot gezeigten Attributen erfordert die Pega Systems-Anwendung einige weitere Attribute, die in der SAML-Antwort zurückgegeben werden müssen. Führen Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die folgenden Schritte aus, um diese SAML-Tokenattribute hinzuzufügen:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Diese Werte sind spezifisch für Ihre Organisation. Geben Sie die entsprechenden Werte an.

    1. Wählen Sie **Neuen Anspruch hinzufügen** aus, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen:

    ![„Neuen Anspruch hinzufügen“ auswählen](common/new-save-attribute.png)

    ![Dialogfeld „Benutzeransprüche verwalten“](common/new-attribute-details.png)

    1. Geben Sie im Feld **Name** den für die Zeile angezeigten Attributnamen ein.

    1. Lassen Sie das Feld **Namespace** leer.

    1. Wählen Sie für **Quelle** die Option **Attribut** aus.

    1. Wählen Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert aus.

    1. Klicken Sie auf **OK**.

    1. Wählen Sie **Speichern** aus.

8. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** den Link **Herunterladen** neben **Verbundmetadaten-XML** gemäß Ihren Anforderungen aus, und speichern Sie das Zertifikat auf Ihrem Computer:

    ![Downloadlink für Zertifikat](common/metadataxml.png)

9. Kopieren Sie im Abschnitt **Pega Systems einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Pega Systems gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **Pega Systems** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="configure-pega-systems-sso"></a>Konfigurieren des einmaligen Anmeldens für Pega Systems

1. Zum Konfigurieren des einmaligen Anmeldens auf **Pega Systems**-Seite melden Sie sich in einem anderen Browserfenster mit einem Administratorkonto beim Pega-Portal an.

2. Wählen Sie **Create** (Erstellen) > **SysAdmin** (Systemadministrator) > **Authentication Service** (Authentifizierungsdienst) aus:

    ![Authentifizierungsdienst auswählen](./media/pegasystems-tutorial/admin.png)
    
3. Führen Sie im Bildschirm **Create Authentication Service** (Authentifizierungsdienst erstellen) die folgenden Schritte aus.

    ![Bildschirm zum Erstellen des Authentifizierungsdiensts](./media/pegasystems-tutorial/admin1.png)

    1. Wählen Sie in der Liste **Type** (Typ) den Eintrag **SAML 2.0** aus.

    1. Geben Sie in das Feld **Name** den gewünschten Namen ein (z.B. **Azure AD SSO**).

    1. Geben Sie in das Feld **Short description** (Kurzbeschreibung) eine Beschreibung ein.  

    1. Wählen Sie **Create and open** (Erstellen und öffnen) aus.
    
4. Wählen Sie im Abschnitt **Identity Provider (IdP) information** (Informationen zum Identitätsanbieter) die Option **Import IdP metadata** (IDP-Metadaten importieren) aus, und navigieren Sie zu der Metadatendatei, die Sie aus dem Azure-Portal heruntergeladen haben. Klicken Sie auf **Submit** (Senden), um die Metadaten zu laden:

    ![Abschnitt mit Informationen zum Identitätsanbieter](./media/pegasystems-tutorial/admin2.png)
    
    Durch den Importvorgang werden die IDP-Daten wie hier gezeigt eingetragen:

    ![Importierte IDP-Daten](./media/pegasystems-tutorial/idp.png)
    
6. Führen Sie im Abschnitt **Service Provider (SP) settings** (Einstellungen für den Dienstanbieter) die folgenden Schritte aus.

    ![Einstellungen für den Dienstanbieter](./media/pegasystems-tutorial/sp.png)

    1. Kopieren Sie den Wert im Feld **Entity Identification** (Entitätsidentifizierung), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Feld **Bezeichner** ein.

    1. Kopieren Sie den Wert im Feld **Assertion Consumer Service (ACS) location** (ACS-Speicherort), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Feld **Antwort-URL** ein.

    1. Aktivieren Sie **Disable request signing**.

7. Wählen Sie **Speichern** aus.

### <a name="create-pega-systems-test-user"></a>Erstellen eines Pega Systems-Testbenutzers

Als Nächstes müssen Sie in Pega Systems einen Benutzer mit dem Namen Britta Simon erstellen. Wenden Sie sich an das [Supportteam von Pega Systems](https://www.pega.com/contact-us), um Benutzer zu erstellen.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Pega Systems weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die Pega Systems-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Pega Systems-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Pega Systems“ in „Meine Apps“ geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Pega Systems-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Pega Systems können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.