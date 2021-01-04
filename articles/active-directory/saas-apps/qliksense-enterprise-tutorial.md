---
title: 'Tutorial: Azure Active Directory-Integration mit Qlik Sense Enterprise | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Qlik Sense Enterprise konfigurieren.
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
ms.openlocfilehash: b55e66eaf4bda06369711e389629b6a20765271d
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92522205"
---
# <a name="tutorial-integrate-qlik-sense-enterprise-with-azure-active-directory"></a>Tutorial: Integrieren von Qlik Sense Enterprise in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Qlik Sense Enterprise in Azure Active Directory (Azure AD) integrieren. Die Integration von Qlik Sense Enterprise in Azure Active Directory ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Qlik Sense Enterprise hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Qlik Sense Enterprise anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie kein Abonnement besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige kostenlose Testversion erhalten.
* Qlik Sense Enterprise-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung. 
* Qlik Sense Enterprise unterstützt **SP-initiiertes** einmaliges Anmelden.
* Qlik Sense Enterprise unterstützt die **Just-In-Time-Bereitstellung** .

* Nach dem Konfigurieren von Qlik Sense Enterprise können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-aad) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Hinzufügen von Qlik Sense Enterprise aus dem Katalog

Zum Konfigurieren der Integration von Qlik Sense Enterprise in Azure AD müssen Sie Qlik Sense Enterprise aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Qlik Sense Enterprise** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Qlik Sense Enterprise** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Qlik Sense Enterprise mithilfe eines Testbenutzers mit dem Namen **Britta Simon** . Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Qlik Sense Enterprise eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Qlik Sense Enterprise müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Konfigurieren des einmaligen Anmeldens für Qlik Sense Enterprise](#configure-qlik-sense-enterprise-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * **[Erstellen eines Qlik Sense Enterprise-Testbenutzers](#create-qlik-sense-enterprise-test-user)** , um eine Entsprechung von Britta Simon in Qlik Sense Enterprise zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Qlik Sense Enterprise** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie auf der Seite **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/hub`.

    b. Geben Sie im Textfeld **Bezeichner** eine URL in einem der folgenden Formate ein:

    ```http
    https://<Fully Qualified Domain Name>.qlikpoc.com
    https://<Fully Qualified Domain Name>.qliksense.com
    ```

    c. Geben Sie im Textfeld **Antwort-URL** eine URL nach folgendem Muster ein:

    `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/samlauthn/`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit der tatsächlichen Anmelde-URL, dem Bezeichner und der Antwort-URL. Diese werden später in diesem Tutorial erläutert. Wenden Sie sich alternativ an das [Clientsupportteam von Qlik Sense Enterprise](https://www.qlik.com/us/services/support), um diese Werte zu erhalten. Der Standardport für die URLs ist 443, Sie können ihn jedoch gemäß den Anforderungen Ihrer Organisation anpassen.

1. Suchen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** in den verfügbaren Optionen die Ihre Anforderungen entsprechende **Verbundmetadaten-XML** , und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal eine Testbenutzerin mit dem Namen Britta Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `Britta Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `BrittaSimon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert aus dem Feld **Kennwort** .
   1. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Qlik Sense Enterprise gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Qlik Sense Enterprise** .
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste den Eintrag **Britta Simon** aus, und klicken Sie anschließend im unteren Bildschirmbereich auf die Schaltfläche **Auswählen** .
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen** .
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

## <a name="configure-qlik-sense-enterprise-sso"></a>Konfigurieren des einmaligen Anmeldens für Qlik Sense Enterprise

1. Bereiten Sie die XML-Datei mit den Verbundmetadaten für das Hochladen auf den Qlik Sense-Server vor.

    > [!NOTE]
    > Vor dem Hochladen der IdP-Metadaten auf den Qlik Sense-Server muss die Datei bearbeitet werden, um Informationen zu entfernen und so den richtigen Ablauf zwischen Azure AD und dem Qlik Sense-Server sicherzustellen.

    ![Screenshot: Visual Studio Code-Fenster mit der Verbundmetadaten-XML-Datei][qs24]

    a. Öffnen Sie die aus dem Azure-Portal heruntergeladene Datei „FederationMetaData.xml“ in einem Text-Editor.

    b. Suchen Sie nach dem Wert **RoleDescriptor** .  Es sind vier Einträge vorhanden (jeweils zwei Paare mit öffnenden und schließenden Elementtags).

    c. Löschen Sie die RoleDescriptor-Tags und alle Informationen dazwischen aus der Datei.

    d. Speichern Sie die Datei, und bewahren Sie sie auf, um sie zu einem späteren Zeitpunkt in diesem Dokument verwenden zu können.

2. Navigieren Sie als Benutzer mit Rechten zum Erstellen von Konfigurationen für virtuelle Proxys zur Qlik Sense Qlik Management Console (QMC).

3. Klicken Sie in der QMC auf das Menüelement **Virtual Proxy** .

    ![Screenshot: Auswahl von „Virtual Proxies“ (Virtuelle Proxys) unter „CONFIGURE SYSTEM“ (SYSTEM KONFIGURIEREN)][qs6]

4. Klicken Sie am unteren Bildschirmrand auf die Schaltfläche **Create new** (Neu erstellen).

    ![Screenshot: Option „Create new“ (Neu erstellen)][qs7]

5. Der Bearbeitungsbildschirm für den virtuellen Proxy wird angezeigt.  Auf der rechten Seite des Bildschirms befindet sich ein Menü zum Anzeigen von Konfigurationsoptionen.

    ![Screenshot: Auswahl von „Identification“ (Identifikation) in den Eigenschaften][qs9]

6. Geben Sie bei aktivierter Menüoption „Identification“ die identifizierenden Informationen für die Azure-Konfiguration des virtuellen Proxys ein.

    ![Screenshot: Abschnitt zum Bearbeiten der Identifikation des virtuellen Proxys, in dem Sie die beschriebenen Werte eingeben können][qs8]  

    a. Das Feld **Description** (Beschreibung) enthält einen Anzeigenamen für die Konfiguration des virtuellen Proxys.  Geben Sie einen Wert als Beschreibung ein.

    b. Mit dem Feld **Prefix** wird der Endpunkt des virtuellen Proxys für die Verbindung mit Qlik Sense per einmaligem Anmelden von Azure AD angegeben.  Geben Sie einen eindeutigen Präfixnamen für diesen virtuellen Proxy ein.

    c. **Session inactivity timeout (minutes)** (Timeout bei Sitzungsinaktivität (Minuten)) ist das Timeout für Verbindungen über diesen virtuellen Proxy.

    d. Unter **Session cookie header name** (Headername für Sitzungscookie) wird der Cookiename zum Speichern des Sitzungsbezeichners für die Qlik Sense-Sitzung angegeben, die einem Benutzer nach der erfolgreichen Authentifizierung zugeordnet wird.  Dieser Name muss eindeutig sein.

7. Klicken Sie auf die Menüoption „Authentication“, um die entsprechenden Informationen anzuzeigen.  Der Bildschirm „Authentication“ wird angezeigt.

    ![Screenshot: Abschnitt „Edit virtual proxy“ > „Authentication“, in dem Sie die beschriebenen Werte eingeben können][qs10]

    a. Mit dem Dropdownmenü **Anonymous access mode** wird festgelegt, ob anonyme Benutzer über den virtuellen Proxy auf Qlik Sense zugreifen können.  Die Standardoption ist „No anonymous user“.

    b. Mit dem Dropdownmenü **Authentication method** (Authentifizierungsmethode) wird festgelegt, welches Authentifizierungsschema vom virtuellen Proxy verwendet wird.  Wählen Sie in der Dropdownliste die Option „SAML“ aus.  Daraufhin werden weitere Optionen angezeigt.

    c. Geben Sie im Feld **SAML host URI** den Hostnamen ein, den Benutzer eingeben müssen, um über diesen virtuellen SAML-Proxy auf Qlik Sense zuzugreifen.  Der Hostname ist der URI des Qlik Sense-Servers.

    d. Geben Sie im Feld **SAML entity ID** den gleichen Wert wie im Feld „SAML host URI“ ein.

    e. Unter **SAML IdP metadata** (SAML-IdP-Metadaten) wird die Datei gewählt, die Sie zuvor im Abschnitt **Edit Federation Metadata from Azure AD Configuration** (Verbundmetadaten der Azure AD-Konfiguration bearbeiten) bearbeitet haben.  **Vor dem Hochladen der IdP-Metadaten muss die Datei bearbeitet werden** , um Informationen zu entfernen und so den richtigen Ablauf zwischen Azure AD und dem Qlik Sense-Server sicherzustellen.  **Verwenden Sie die obige Anleitung, falls Sie die Datei noch bearbeiten müssen.**  Wenn die Datei bearbeitet wurde, können Sie auf die Schaltfläche zum Durchsuchen klicken und die bearbeitete Metadatendatei auswählen, um sie für die Konfiguration des virtuellen Proxys hochzuladen.

    f. Geben Sie den Attributnamen oder den Schemaverweis für das SAML-Attribut ein, das für die **UserID** steht, die von Azure AD an den Qlik Sense-Server gesendet wird.  Informationen zum Schemaverweis sind auf den Azure-App-Bildschirmen zur Post-Konfiguration verfügbar.  Geben Sie zum Verwenden des Namensattributs `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` ein.

    g. Geben Sie den Wert für das Benutzerverzeichnis ( **user directory** ) ein, das Benutzern zugeordnet wird, wenn diese die Authentifizierung gegenüber dem Qlik Sense-Server über Azure AD durchführen.  Hartcodierte Werte müssen in **eckige Klammern []** gesetzt werden.  Geben Sie den Namen des Attributs in diesem Textfeld **ohne** eckige Klammern ein, um ein Attribut zu verwenden, das in der Azure AD-SAML-Assertion gesendet wird.

    h. Unter **SAML signing algorithm** wird die Service Provider-Zertifikatsignatur (hier: Qlik Sense-Server) für die Konfiguration des virtuellen Proxys festgelegt.  Ändern Sie den SAML-Signaturalgorithmus in **SHA-256** , wenn für den Qlik Sense-Server ein vertrauenswürdiges Zertifikat verwendet wird, das per Microsoft Enhanced RSA und AES Cryptographic Provider generiert wurde.

    i. Im Abschnitt „SAML attribute mapping“ können weitere Attribute, z.B. Gruppen, zur Verwendung in Sicherheitsregeln an Qlik Sense gesendet werden.

8. Klicken Sie auf die Menüoption **LOAD BALANCING** , um die entsprechenden Informationen anzuzeigen.  Der Bildschirm „Load Balancing“ wird angezeigt.

    ![Screenshot: Option „LOAD BALANCING“ im Bearbeitungsfenster für den virtuellen Proxy, unter der Sie „Add new server node“ (Neuen Serverknoten hinzufügen) auswählen können][qs11]

9. Klicken Sie auf die Schaltfläche **Add new server node** (Neuen Serverknoten hinzufügen), wählen Sie den Engine-Knoten oder Knoten aus, an die von Qlik Sense zu Lastenausgleichszwecken Sitzungen gesendet werden, und klicken Sie auf die Schaltfläche **Add** (Hinzufügen).

    ![Screenshot: Schaltfläche zum Hinzufügen von Servern im Dialogfeld zum Hinzufügen von Serverknoten für den Lastenausgleich][qs12]

10. Klicken Sie auf die Menüoption „Advanced“, um die entsprechenden Informationen anzuzeigen. Der Bildschirm „Advanced“ wird angezeigt.

    ![Screenshot: Bildschirm „Advanced“ für die Bearbeitung des virtuellen Proxys][qs13]

    In der Liste zugelassener Hosts sind die Hostnamen aufgeführt, die akzeptiert werden, wenn eine Verbindung mit dem Qlik Sense-Server hergestellt wird.  **Geben Sie den Hostnamen ein, der von Benutzern angegeben werden muss, wenn eine Verbindung mit dem Qlik Sense-Server hergestellt werden soll.** Der Hostname ist der gleiche Wert wie der SAML-Host-URI ohne „https://“.

11. Klicken Sie auf die Schaltfläche **Übernehmen** .

    ![Screenshot: Schaltfläche „Übernehmen“][qs14]

12. Klicken Sie auf „OK“, um die Warnmeldung mit dem Hinweis zu akzeptieren, dass mit dem virtuellen Proxy verknüpfte Proxys neu gestartet werden.

    ![Screenshot: Bestätigungsmeldung für die Anwendung von Änderungen auf den virtuellen Proxy][qs15]

13. Rechts auf dem Bildschirm wird das Menü „Associated items“ angezeigt.  Klicken Sie auf die Menüoption **Proxies** .

    ![Screenshot: Auswahl von „Proxies“ unter „Associated items“][qs16]

14. Der Proxybildschirm wird angezeigt.  Klicken Sie unten auf die Schaltfläche **Link** , um einen Proxy mit dem virtuellen Proxy zu verknüpfen.

    ![Screenshot: Schaltfläche „Link“][qs17]

15. Wählen Sie den Proxyknoten aus, der diese Verbindung mit dem virtuellen Proxy unterstützen soll, und klicken Sie auf die Schaltfläche **Link** .  Nach dem Verknüpfen wird der Proxy in der Liste mit den zugeordneten Proxys aufgeführt.

    ![Screenshot: Auswahl von Proxydiensten][qs18]
  
    ![Screenshot: Zugeordnete Proxys im Dialogfeld mit den zugeordneten Elementen für virtuelle Proxys][qs19]

16. Nach ungefähr fünf bis zehn Sekunden wird die Meldung „Refresh QMC“ angezeigt.  Klicken Sie auf die Schaltfläche **Refresh QMC** (QMC aktualisieren).

    ![Screenshot: Meldung mit dem Hinweis, dass Ihre Sitzung beendet wurde][qs20]

17. Klicken Sie nach der QMC-Aktualisierung auf das Menüelement **Virtual proxies** . Der Eintrag mit dem neuen virtuellen SAML-Proxy wird in der Tabelle auf dem Bildschirm angezeigt.  Klicken Sie einmal auf den Eintrag für den virtuellen Proxy.

    ![Screenshot: Einzelner Eintrag unter „Virtual proxies“][qs51]

18. Unten auf dem Bildschirm wird die Schaltfläche „Download SP metadata“ aktiviert.  Klicken Sie auf die Schaltfläche **Download SP metadata** (SP-Metadaten herunterladen), um die Metadaten in einer Datei zu speichern.

    ![Screenshot: Schaltfläche „Download SP metadata“ (SP-Metadaten herunterladen)][qs52]

19. Öffnen Sie die SP-Metadatendatei.  Sehen Sie sich die Einträge **entityID** und **AssertionConsumerService** an.  Diese Werte entsprechen dem **Bezeichner** , der **Anmelde-URL** und der **Antwort-URL** in der Azure AD-Anwendungskonfiguration. Fügen Sie diese Werte in der Azure AD-Anwendungskonfiguration im Abschnitt **Domäne und URLs für Qlik Sense Enterprise** . Sofern sie nicht übereinstimmen, ersetzen Sie sie auch im Konfigurations-Assistenten der Azure AD-App.

    ![Screenshot: Nur-Text-Editor mit „EntityDescriptor“ und Hervorhebung von „entityID“ und „AssertionConsumerService“][qs53]

### <a name="create-qlik-sense-enterprise-test-user"></a>Erstellen eines Qlik Sense Enterprise-Testbenutzers

Qlik Sense Enterprise unterstützt die **Just-In-Time-Bereitstellung** . Benutzer werden dem Repository „USERS“ (BENUTZER) von Qlik Sense Enterprise automatisch hinzugefügt, wenn sie das SSO-Feature nutzen. Darüber hinaus können Kunden die QMC verwenden und einen Benutzerverzeichnisconnector (User Directory Connector, UDC) erstellen, um Benutzer in Qlik Sense Enterprise aus dem LDAP ihrer Wahl (etwa Active Directory) vorab aufzufüllen.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens

Wenn Sie im Zugriffsbereich die Kachel „Qlik Sense Enterprise“ auswählen, sollten Sie automatisch bei der Qlik Sense Enterprise-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)

- [Was ist Sitzungssteuerung in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[qs6]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png