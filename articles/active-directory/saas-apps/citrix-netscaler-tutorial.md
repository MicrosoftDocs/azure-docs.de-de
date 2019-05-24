---
title: 'Tutorial: Azure Active Directory-Integration mit Citrix Netscaler | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Citrix Netscaler konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af501bd0-8ff5-468f-9b06-21e607ae25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 6d434295a6a46ee5b7089608cbf788ff91589fb7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65863395"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-netscaler"></a>Tutorial: Azure Active Directory-Integration mit Citrix Netscaler

In diesem Tutorial erfahren Sie, wie Sie Citrix Netscaler in Azure Active Directory (Azure AD) integrieren.
Die Integration von Citrix Netscaler in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Citrix Netscaler hat.
* Sie können es Benutzern ermöglichen, dass sie mit ihren Azure AD-Konten automatisch bei Citrix Netscaler angemeldet werden (einmaliges Anmelden).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Citrix Netscaler konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Citrix Netscaler-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Citrix Netscaler unterstützt **SP**-initiiertes einmaliges Anmelden.

* Citrix Netscaler unterstützt die **Just-In-Time**-Benutzerbereitstellung.

## <a name="adding-citrix-netscaler-from-the-gallery"></a>Hinzufügen von Citrix Netscaler aus dem Katalog

Zum Konfigurieren der Integration von Citrix Netscaler in Azure AD müssen Sie Citrix Netscaler aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

**Um Citrix Netscaler aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Citrix Netscaler** ein, wählen Sie im Ergebnisbereich **Citrix Netscaler** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Citrix Netscaler in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Citrix Netscaler mithilfe eines Testbenutzers namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Citrix Netscaler eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Citrix Netscaler müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Citrix Netscaler](#configure-citrix-netscaler-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Citrix Netscaler-Testbenutzers](#create-citrix-netscaler-test-user)**, um eine Entsprechung von Britta Simon in Citrix Netscaler zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in Citrix Netscaler die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Citrix Netscaler** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Citrix Netscaler](common/sp-identifier-reply.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`.
    
    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<<Your FQDN>>`.

    c. Geben Sie im Textfeld **Antwort-URL (Assertionsverbraucherdienst-URL)** eine URL in folgendem Format ein: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`.
    
    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Clientsupportteam von Citrix Netscaler](https://www.citrix.com/contact/technical-support.html), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

    > [!NOTE]
    > Damit einmaliges Anmelden auch funktioniert, muss von öffentlichen Sites auf diese URLs zugegriffen werden können. Sie müssen die Firewall oder andere Sicherheitseinstellungen auf der Seite von Netscaler so aktivieren, dass Azure AD das Token in der konfigurierten ACS-URL bereitstellen kann.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **Citrix Netscaler einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-citrix-netscaler-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Citrix Netscaler

1. Melden Sie sich in einem anderen Webbrowserfenster als Administrator bei Ihrem Citrix Netscaler-Mandanten an.

2. Stellen Sie sicher, dass die **NetScaler-Firmwareversion = NS12.1: Build 48.13.nc** lautet.

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure01.png)

3. Führen Sie auf der Seite **VPN Virtual Server** (Virtueller VPN-Server) die folgenden Schritte aus:

     ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure02.png)

    a. Legen Sie die Gatewayeinstellungen **ICA Only** (Nur ICA) auf **true** fest.
    
    b. Legen Sie **Enable Authentification** (Authentifizierung aktivieren) auf **true** fest.
    
    c. **DTLS** ist optional.
    
    d. Stellen Sie sicher, dass **SSLv3** **Deaktiviert** ist.

4. Eine benutzerdefinierte Gruppe **SSL Ciphers** wird erstellt, um wie unten gezeigt auf https://www.ssllabs.com A+ zu erreichen:

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure03.png)

5. Führen Sie auf der Seite **Configure Authentication SAML Server** (SAML-Authentifizierungsserver konfigurieren) die folgenden Schritte aus:

      ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure04.png)

    a. Geben Sie in das Textfeld **Name** den Namen Ihres Servers ein.

    b. Fügen Sie in das Textfeld **Redirect URL** (Umleitungs-URL) den Wert von **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie in das Textfeld **Single Logout URL** (URL für einmaliges Abmelden) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Klicken Sie in **IDP Certificate Name** (IDP-Zertifikatname) auf das **„+“**-Zeichen, um das Zertifikat hinzuzufügen, das Sie aus dem Azure-Portal heruntergeladen haben. Nachdem es hochgeladen wurde, wählen Sie das Zertifikat aus der Dropdownliste aus.

    e. Folgende weitere Felder müssen auf dieser Seite festgelegt werden

      ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure24.png)

    f. Wählen Sie **Requested Authentication Context** (Angeforderter Authentifizierungskontext) als **Exact** (Genau) aus.

    g. Wählen Sie **Signature Algorithm** (Signaturalgorithmus) als **RSA-SHA256** aus.

    h. Wählen Sie **Digest Method** (Digestmethode) als **SHA256** aus.

    i. Aktivieren Sie **Enforce Username** (Benutzernamen erzwingen).

    j. Klicken Sie auf **OK**

6. Führen Sie zum Konfigurieren des **Session Profile** (Sitzungsprofil) die folgenden Schritte aus:

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure06.png)

    a. Geben Sie in das Textfeld **Name** den Namen Ihres Sitzungsprofils ein.

    b. Auf der Registerkarte **Client Experience** (Clientumgebung) nehmen Sie die Änderungen, wie im folgenden Screenshot gezeigt, vor.

    c. Nehmen Sie, wie unten dargestellt, weitere Änderungen auf der Registerkarte **General** (Allgemein) vor, und klicken Sie auf **OK**.

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure07.png)

    d. Auf der Registerkarte **Published Applications** (Veröffentlichte Anwendungen) nehmen Sie die Änderungen, wie im folgenden Screenshot gezeigt, vor, und klicken Sie auf **OK**.

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure08.png)

    e. Auf der Registerkarte **Security** (Sicherheit) nehmen Sie die Änderungen, wie im folgenden Screenshot gezeigt, vor, und klicken Sie auf **OK**.

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure09.png)

7. Sorgen Sie dafür, dass die ICA-Verbindungen eine Verbindung am Sitzungszuverlässigkeitsport **2598** herstellen, wie im folgenden Screenshot gezeigt.

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure10.png)

8. Fügen Sie im Abschnitt **SAML** die **Server** hinzu, wie im folgenden Screenshot gezeigt.

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure11.png)

9. Fügen Sie im Abschnitt **SAML** die **Policies** (Richtlinien) hinzu, wie im folgenden Screenshot dargestellt.

     ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure12.png)

10. Wechseln Sie auf der Seite **Global Settings** (Globale Einstellungen) zum Abschnitt **Clientless Access** (Clientloser Zugriff).

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure13.png)

11. Führen Sie auf der Registerkarte **Configuration** (Konfiguration) die folgenden Schritte aus:

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure14.png)

    a. Wählen Sie **Domänen zulassen** (Allow Domains) aus.

    b. Wählen Sie im Textfeld **Domain Name** (Domänennamen) die Domäne aus.

    c. Klicken Sie auf **OK**.

12. Legen Sie die **StoreFront**-Einstellungen auf dem **Receiver for Web Sites** (Empfänger für Websites) fest, wie im folgenden Screenshot gezeigt:

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure15.png)

13. Führen Sie im Popup **Manage Authentication Methods – Corp** (Authentifizierungsmethode verwalten – Unternehmen) die folgenden Schritte aus:

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure16.png)

    a. Wählen Sie **Benutzername und Kennwort** aus.

    b. Wählen Sie **Pass-Through-from NetScaler Gateway** aus.

    c. Klicken Sie auf **OK**.

14. Führen Sie im Popup **Configure Trusted Domains** (Vertrauenswürdige Domänen konfigurieren) die folgenden Schritte aus:

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure17.png)

    a. Wählen Sie **Trusted Domains only** (Nur vertrauenswürdige Domänen) aus.

    b. Klicken Sie auf **Add** (Hinzufügen), um Ihre Domäne im Textfeld **Trusted Domains** (Vertrauenswürdige Domänen) hinzuzufügen.

    c. Wählen Sie die Standarddomäne aus Ihrer Liste **Default Domain** (Standarddomäne) aus.

    d. Wählen Sie **Show domains list in logon page** (Domänenliste auf Anmeldeseite anzeigen) aus.

    e. Klicken Sie auf **OK**.

15. Führen Sie im Popup **Manage NetScaler Gateways** (NetScaler Gateways verwalten) die folgenden Schritte aus:

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure18.png)

    a. Klicken Sie auf **Add** (Hinzufügen), um Ihre NetScaler-Gateways im Textfeld **NetScaler Gateways** hinzuzufügen.

    b. Klicken Sie auf **Schließen**.

16. Führen Sie auf der Registerkarte **StoreFront General Settings** (Allgemeine StoreFront-Einstellungen) die folgenden Schritte aus:

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure19.png)

    a. Geben Sie in das Textfeld **Display name** (Anzeigename) Ihren NetScaler Gateway-Namen ein.

    b. Geben Sie in das Textfeld **NetScaler Gateway URL** Ihre NetScaler-Gateway-URL ein.

    c. Wählen Sie **Usage or Role** (Nutzung oder Rolle) als **Authentication and HDX Routing** (Authentifizierung und HDX-Routing) aus.

    d. Klicken Sie auf **OK**.

17. Führen Sie auf der Registerkarte **StoreFront Secure Ticket Authority** (Sichere StoreFront-Ticketautorität) die folgenden Schritte aus:

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure20.png)

    a. Klicken Sie auf die Schaltfläche **Add** (Hinzufügen), um Ihre **Secure Ticket Authority URLs** (Sichere StoreFront-Ticketautoritäts-URLs) in dem Textfeld hinzuzufügen.

    b. Wählen Sie **Enable session reliability** (Sitzungszuverlässigkeit aktivieren) aus.

    c. Klicken Sie auf **OK**.

18. Führen Sie auf der Registerkarte **StoreFront Authentication Settings** (StoreFront-Authentifizierungseinstellungen) die folgenden Schritte aus:

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure21.png)

    a. Wählen Sie Ihre **Version** aus.

    b. Wählen Sie den **Logon type** (Anmeldetyp) als **Domain** (Domäne) aus.

    c. Geben Sie Ihre **Callback URL** (Rückruf-URL) ein.

    d. Klicken Sie auf **OK**.

19. Führen Sie auf der Registerkarte **StoreFront Deploy Citrix Receiver** (StoreFront: Citrix Receiver bereitstellen) die folgenden Schritte aus:

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure22.png)

    a. Wählen Sie **Deployment option** Bereitstellungsoption als **Use Receiver for HTML5 if local Receiver is unavailable** (Empfänger für HTML5 verwenden, falls der lokale Empfänger nicht verfügbar ist) aus.

    b. Klicken Sie auf **OK**.

20. Führen Sie im Popup **Manage Beacons** (Beacons verwalten) die folgenden Schritte aus:

    ![Configure single sign-on](./media/citrix-netscaler-tutorial/configure23.png)

    a. Wählen Sie das **Internal Beacon** (Interner Beacon) als **Use the service URL** (Dienst-URL verwenden) aus.

    b. Klicken Sie auf **Add** (Hinzufügen), um Ihre URLs im Textfeld **External Beacons** (Externe Beacons) hinzuzufügen.

    c. Klicken Sie auf **OK**.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** **brittasimon@yourcompanydomain.extension** ein.  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Citrix Netscaler gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **Citrix Netscaler**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Liste der Anwendungen **Citrix Netscaler** aus.

    ![Citrix Netscaler-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-citrix-netscaler-test-user"></a>Erstellen eines Citrix Netscaler-Testbenutzers

In diesem Abschnitt wird ein Benutzer mit dem Namen „Britta Simon“ in Citrix Netscaler erstellt. Citrix Netscaler unterstützt die Just-In-Time-Benutzerbereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in Citrix Netscaler vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

>[!NOTE]
>Wenn Sie einen Benutzer manuell erstellen müssen, setzen Sie sich mit dem [Supportteam für den Citrix Netscaler-Client](https://www.citrix.com/contact/technical-support.html) in Verbindung.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Citrix Netscaler“ klicken, sollten Sie automatisch bei der Citrix Netscaler-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

