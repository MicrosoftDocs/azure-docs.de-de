---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit FortiWeb Web Application Firewall | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und FortiWeb Web Application Firewall konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/24/2020
ms.author: jeedes
ms.openlocfilehash: b2da377ac6498940ed2fc260ad89494f13803ba5
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523242"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortiweb-web-application-firewall"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit FortiWeb Web Application Firewall

In diesem Tutorial erfahren Sie, wie Sie FortiWeb Web Application Firewall in Azure Active Directory (Azure AD) integrieren. Die Integration von FortiWeb Web Application Firewall in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf FortiWeb Web Application Firewall hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei FortiWeb Web Application Firewall anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* FortiWeb Web Application Firewall-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* FortiWeb Web Application Firewall unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="adding-fortiweb-web-application-firewall-from-the-gallery"></a>Hinzufügen von FortiWeb Web Application Firewall aus dem Katalog

Zum Konfigurieren der Integration von FortiWeb Web Application Firewall in Azure AD müssen Sie FortiWeb Web Application Firewall aus dem Katalog Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** im Suchfeld den Suchbegriff **FortiWeb Web Application Firewall** ein.
1. Wählen Sie im Ergebnisbereich den Eintrag **FortiWeb Web Application Firewall** aus, und fügen Sie die App dann hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-fortiweb-web-application-firewall"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für FortiWeb Web Application Firewall

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit FortiWeb Web Application Firewall mithilfe eines Testbenutzers namens **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in FortiWeb Web Application Firewall eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit FortiWeb Web Application Firewall zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für FortiWeb Web Application Firewall](#configure-fortiweb-web-application-firewall-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines FortiWeb Web Application Firewall-Testbenutzers](#create-fortiweb-web-application-firewall-test-user)** , um in FortiWeb Web Application Firewall eine Entsprechung von B. Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **FortiWeb Web Application Firewall** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

   a. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://www.<CUSTOMER_DOMAIN>.com`.

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://www.<CUSTOMER_DOMAIN>.com/<FORTIWEB_NAME>/saml.sso/SAML2/POST`

    c. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://www.<CUSTOMER_DOMAIN>.com`.

    d. Geben Sie im Textfeld **Abmelde-URL** eine URL im folgenden Format ein: `https://www.<CUSTOMER_DOMAIN>.info/<FORTIWEB_NAME>/saml.sso/SLO/POST`.
 
    > [!NOTE]
    > `<FORTIWEB_NAME>` ist ein Namensbezeichner, der später beim Bereitstellen der Konfiguration für FortiWeb verwendet wird.
    > Wenden Sie sich an das [Supportteam von FortiWeb Web Application Firewall](mailto:support@fortinet.com), um die tatsächlichen URL-Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)


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

In diesem Abschnitt ermöglichen Sie „B. Simon“ die Verwendung des einmaligen Anmeldens von Azure, indem Sie Zugriff auf FortiWeb Web Application Firewall gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **FortiWeb Web Application Firewall** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-fortiweb-web-application-firewall-sso"></a>Konfigurieren des einmaligen Anmeldens mit FortiWeb Web Application Firewall

1.  Navigieren Sie zu `https://<address>:8443`. Hierbei steht `<address>` für den vollqualifizierten Domänennamen oder die öffentliche IP-Adresse, der bzw. die dem virtuellen FortiWeb-Computer zugewiesen ist.

2.  Melden Sie sich mit den Administratoranmeldeinformationen an, die während der Bereitstellung des virtuellen FortiWeb-Computers angegeben wurden.

1. Führen Sie auf der folgenden Seite die folgenden Schritte aus:

    ![Seite „SAML-Server“](./media/fortiweb-web-application-firewall-tutorial/configure-sso.png)

    a.  Klicken Sie im linken Menü auf **Benutzer**.

    b.  Klicken Sie unter „Benutzer“ auf **Remoteserver**.

    c.  Klicken Sie auf **SAML-Server**.

    d.  Klicken Sie auf **Neue erstellen**.

    e.  Geben Sie im Feld **Name** den Wert für `<fwName>` an, der im Abschnitt für die Konfiguration von Azure AD verwendet wird.

    f.  Fügen Sie in das Textfeld **Entity ID** (Entitäts-ID) den Wert für den **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    g. Klicken Sie neben **Metadaten** auf **Datei auswählen**, und wählen Sie die **Verbundmetadaten-XML-Datei** aus, die Sie aus dem Azure-Portal heruntergeladen haben.

    h.  Klicken Sie auf **OK**.

### <a name="create-a-site-publishing-rule"></a>Erstellen einer Website-Veröffentlichungsregel

1.  Navigieren Sie zu `https://<address>:8443`. Hierbei steht `<address>` für den vollqualifizierten Domänennamen oder die öffentliche IP-Adresse, der bzw. die dem virtuellen FortiWeb-Computer zugewiesen ist.

1.  Melden Sie sich mit den Administratoranmeldeinformationen an, die während der Bereitstellung des virtuellen FortiWeb-Computers angegeben wurden.
1. Führen Sie auf der folgenden Seite die folgenden Schritte aus:

    ![Website-Veröffentlichungsregel](./media/fortiweb-web-application-firewall-tutorial/site-publish-rule.png)

    a.  Klicken Sie im linken Menü auf **Application Delivery** (Anwendungsbereitstellung).
    
    b.  Klicken Sie unter **Application Delivery** (Anwendungsbereitstellung) auf **Site Publish** (Websiteveröffentlichung).
    
    c.  Klicken Sie unter **Site Publish** (Websiteveröffentlichung) auf **Site Publish** (Websiteveröffentlichung).
    
    d.  Klicken Sie auf **Site Publish Rule** (Website-Veröffentlichungsregel).
    
    e.  Klicken Sie auf **Neue erstellen**.
    
    f.  Geben Sie einen Namen für die Website-Veröffentlichungsregel an.
    
    g.  Klicken Sie neben **Published Site Type** (Typ der veröffentlichten Website) auf **Regular Expression** (Regulärer Ausdruck).
    
    i.  Geben Sie neben **Published Site** (Veröffentlichte Website) eine Zeichenfolge an, die mit dem Hostheader der von Ihnen veröffentlichten Website übereinstimmt.
    
    j.  Geben Sie neben **Pfad** einen Schrägstrich (/) ein.
    
    k.  Wählen Sie neben **Client Authentication Method** (Clientauthentifizierungsmethode) die Option **SAML Authentication** (SAML-Authentifizierung) aus.
    
    l.  Wählen Sie in der Dropdownliste **SAML-Server** den zuvor erstellten SAML-Server aus.
    
    m.  Klicken Sie auf **OK**.


### <a name="create-a-site-publishing-policy"></a>Erstellen einer Website-Veröffentlichungsrichtlinie

1.  Navigieren Sie zu `https://<address>:8443`. Hierbei steht `<address>` für den vollqualifizierten Domänennamen oder die öffentliche IP-Adresse, der bzw. die dem virtuellen FortiWeb-Computer zugewiesen ist.

2.  Melden Sie sich mit den Administratoranmeldeinformationen an, die während der Bereitstellung des virtuellen FortiWeb-Computers angegeben wurden.

1. Führen Sie auf der folgenden Seite die folgenden Schritte aus:

    ![Website-Veröffentlichungsrichtlinie](./media/fortiweb-web-application-firewall-tutorial/site-publish-policy.png)

    a.  Klicken Sie im linken Menü auf **Application Delivery** (Anwendungsbereitstellung).

    b.  Klicken Sie unter **Application Delivery** (Anwendungsbereitstellung) auf **Site Publish** (Websiteveröffentlichung).

    c.  Klicken Sie unter **Site Publish** (Websiteveröffentlichung) auf **Site Publish** (Websiteveröffentlichung).

    d.  Klicken Sie auf **Site Publish Policy** (Website-Veröffentlichungsrichtlinie).

    e.  Klicken Sie auf **Neue erstellen**.

    f.  Geben Sie einen Namen für die Website-Veröffentlichungsrichtlinie an.

    g.  Klicken Sie auf **OK**.

    h.  Klicken Sie auf **Neue erstellen**.

    i.  Wählen Sie in der Dropdownliste **Regel** die zuvor erstellte Website-Veröffentlichungsregel aus.

    j.  Klicken Sie auf **OK**.

### <a name="create-and-assign-a-web-protection-profile"></a>Erstellen und Zuweisen eines Webschutzprofils

1.  Navigieren Sie zu `https://<address>:8443`. Hierbei steht `<address>` für den vollqualifizierten Domänennamen oder die öffentliche IP-Adresse, der bzw. die dem virtuellen FortiWeb-Computer zugewiesen ist.

2.  Melden Sie sich mit den Administratoranmeldeinformationen an, die während der Bereitstellung des virtuellen FortiWeb-Computers angegeben wurden.
3.  Klicken Sie im linken Menü auf **Richtlinie**.
4.  Klicken Sie unter **Richtlinie** auf **Web Protection Profile** (Webschutzprofil).
5.  Klicken Sie auf **Inline Standard Protection** (Inline-Standardschutz) und auf **Clone** (Klonen).
6.  Geben Sie einen Namen für das neue Webschutzprofil an, und klicken Sie auf **OK**.
7.  Wählen Sie das neue Webschutzprofil aus, und klicken Sie auf **Bearbeiten**.
8.  Wählen Sie neben **Site Publish** (Websiteveröffentlichung) die zuvor erstellte Website-Veröffentlichungsrichtlinie aus.
9.  Klicken Sie auf **OK**.
 
    ![Websiteveröffentlichung](./media/fortiweb-web-application-firewall-tutorial/web-protection.png)

10. Klicken Sie im linken Menü auf **Richtlinie**.
11. Klicken Sie unter **Richtlinie** auf **Serverrichtlinie**.
12. Wählen Sie die Serverrichtlinie zum Veröffentlichen der Website aus, für die Sie Azure Active Directory für die Authentifizierung verwenden möchten.
13. Klicken Sie auf **Bearbeiten**.
14. Wählen Sie in der Dropdownliste **Web Protection Profile** (Webschutzprofil) das zuvor erstellte Webschutzprofil aus.
15. Klicken Sie auf **OK**.
16. Versuchen Sie, auf die externe URL zuzugreifen, unter der die Website von FortiWeb veröffentlicht wird. Sie sollten zur Authentifizierung an Azure Active Directory umgeleitet werden.


### <a name="create-fortiweb-web-application-firewall-test-user"></a>Erstellen eines FortiWeb Web Application Firewall-Testbenutzers

In diesem Abschnitt erstellen Sie in FortiWeb Web Application Firewall eine Benutzerin mit dem Namen „Britta Simon“. Arbeiten Sie mit dem [Supportteam von FortiWeb Web Application Firewall](mailto:support@fortinet.com) zusammen, um die Benutzer der FortiWeb Web Application Firewall-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Sie werden zur Anmelde-URL für FortiWeb Web Application Firewall umgeleitet, um den Anmeldeablauf zu initiieren. 

* Navigieren Sie direkt zur Anmelde-URL für FortiWeb Web Application Firewall, und initiieren Sie den Anmeldeablauf.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „FortiWeb Web Application“ klicken, werden Sie zur Anmelde-URL für FortiWeb Web Application umgeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von FortiWeb Web Application Firewall können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.


