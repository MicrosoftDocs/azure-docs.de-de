---
title: 'Tutorial: Azure Active Directory-Integration mit SAP Cloud Platform | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SAP Cloud Platform konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.openlocfilehash: 2d87be085331c174b58e2a475e18efa54a78cfea
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895162"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Tutorial: Azure Active Directory-Integration mit SAP Cloud Platform

In diesem Tutorial erfahren Sie, wie Sie SAP Cloud Platform in Azure Active Directory (Azure AD) integrieren.
Die Integration von SAP Cloud Platform in Azure AD hat folgende Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf SAP Cloud Platform haben soll.
* Sie können Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei SAP Cloud Platform anzumelden (Single Sign-On, SSO; einmaliges Anmelden).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren der Azure AD-Integration mit SAP Cloud Platform benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* SAP Cloud Platform-Abonnement, für das einmaliges Anmelden aktiviert ist

Nach Abschluss dieses Tutorials können sich die SAP Cloud Platform zugewiesenen Azure AD-Benutzer wie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md) beschrieben mittels einmaliger Anmeldung bei der Anwendung anmelden.

>[!IMPORTANT]
>Sie müssen eine eigene Anwendung bereitstellen oder eine Anwendung unter Ihrem SAP Cloud Platform-Konto abonnieren, um das einmalige Anmelden testen zu können. In diesem Tutorial wird eine Anwendung auf dem Konto bereitgestellt.
> 

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SAP Cloud Platform unterstützt **SP** -initiiertes einmaliges Anmelden.

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Hinzufügen von SAP Cloud Platform über den Katalog

Zum Konfigurieren der Integration von SAP Cloud Platform in Azure AD müssen Sie SAP Cloud Platform aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um SAP Cloud Platform über den Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **SAP Cloud Platform** ein, wählen Sie im Ergebnisbereich **SAP Cloud Platform** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

     ![SAP Cloud Platform in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei SAP Cloud Platform mithilfe eines Testbenutzers namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SAP Cloud Platform eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei SAP Cloud Platform müssen die folgenden Schritte ausgeführt werden:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für SAP Cloud Platform](#configure-sap-cloud-platform-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines SAP Cloud Platform-Testbenutzers](#create-sap-cloud-platform-test-user)** , um ein Pendant von Britta Simon in SAP Cloud Platform zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei SAP Cloud Platform die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **SAP Cloud Platform** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für SAP Cloud Platform](common/sp-identifier-reply.png)

    a. Geben Sie im Textfeld **Anmelde-URL** die URL ein, die von den Benutzern zur Anmeldung bei der **SAP Cloud Platform** -Anwendung verwendet wird. Dies ist die kontospezifische URL einer geschützten Ressource in der SAP Cloud Platform-Anwendung. Die URL basiert auf dem folgenden Muster: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
    >[!NOTE]
    >Dies ist die URL in Ihrer SAP Cloud Platform-Anwendung, die die Authentifizierung des Benutzers erfordert.
    > 

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`

    b. Geben Sie im Textfeld **Bezeichner** die URL von SAP Cloud Platform in einem der folgenden Formate an: 

    - `https://hanatrial.ondemand.com/<instancename>`
    - `https://hana.ondemand.com/<instancename>`
    - `https://us1.hana.ondemand.com/<instancename>`
    - `https://ap1.hana.ondemand.com/<instancename>`

    c. Geben Sie im Textfeld **Antwort-URL** eine URL nach folgendem Muster ein:

    - `https://<subdomain>.hanatrial.ondemand.com/<instancename>`
    - `https://<subdomain>.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>`
    - `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>`

    > [!NOTE] 
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL, den tatsächlichen Bezeichner und die tatsächliche Antwort-URL. Anmelde-URL und Bezeichner erhalten Sie vom [Supportteam für den SAP Cloud Plattform-Client](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html). Die Antwort-URL finden Sie im Abschnitt für die Verwaltung der Vertrauensstellung. Dies wird weiter unten im Tutorial erläutert.
    > 
4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML** -Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

### <a name="configure-sap-cloud-platform-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für SAP Cloud Platform

1. Melden Sie sich in einem anderen Webbrowserfenster unter `https://account.<landscape host>.ondemand.com/cockpit` (Beispiel: https://account.hanatrial.ondemand.com/cockpit) ) beim SAP Cloud Platform-Cockpit an.

2. Klicken Sie auf die Registerkarte **Vertrauen** .
   
    ![Trust (Vertrauen)](./media/sap-hana-cloud-platform-tutorial/ic790800.png "Vertrauen")

3. Führen Sie im Abschnitt für die Verwaltung der Vertrauensstellung unter **Local Service Provider** (Lokaler Dienstanbieter) die folgenden Schritte aus:

    ![Screenshot: Abschnitt für die Verwaltung der Vertrauensstellung, in dem die Registerkarte „Local Service Provider“ (Lokaler Dienstanbieter) ausgewählt ist und alle Textfelder hervorgehoben sind](./media/sap-hana-cloud-platform-tutorial/ic793931.png "Verwaltung von Vertrauensstellungen")
   
    a. Klicken Sie auf **Bearbeiten**.

    b. Wählen Sie als **Konfigurationstyp** die Option **Benutzerdefiniert**.

    c. Belassen Sie als **Name des lokalen Dienstanbieters** den Standardwert. Kopieren Sie diesen Wert, und fügen Sie ihn in der Azure AD-Konfiguration für SAP Cloud Plattform in das Feld **Bezeichner** ein.

    d. Klicken Sie zum Generieren eines **Signaturschlüssels** und eines **Signaturzertifikat** -Schlüsselpaars auf **Schlüsselpaar generieren**.

    e. Wählen Sie als **Prinzipalweitergabe** die Option **Deaktiviert**.

    f. Wählen Sie unter **Zwangsauthentifizierung** die Option **Deaktiviert**.

    g. Klicken Sie auf **Speichern**.

4. Führen Sie nach dem Speichern der Einstellungen für **Local Service Provider** (Lokaler Dienstanbieter) die folgenden Schritte aus, um die Antwort-URL zu erhalten:
   
    ![Abrufen von Metadaten](./media/sap-hana-cloud-platform-tutorial/ic793930.png "Metadaten abrufen")

    a. Klicken Sie zum Herunterladen der SAP Cloud Platform-Metadatendatei auf **Metadaten abrufen**.

    b. Öffnen Sie die heruntergeladene SAP Cloud Platform-Metadatendatei (XML-Datei), und navigieren Sie zum Tag **Ns3:AssertionConsumerService**.
 
    c. Kopieren Sie den Wert des Attributs **Speicherort** , und fügen Sie ihn in der Azure AD-Konfiguration für SAP Cloud Plattform in das Textfeld **Antwort-URL** ein.

5. Klicken Sie auf die Registerkarte **Vertrauenswürdiger Identitätsanbieter** , und klicken Sie dann auf **Vertrauenswürdigen Identitätsanbieter hinzufügen**.
   
    ![Screenshot: Seite für die Verwaltung der Vertrauensstellung, auf der die Registerkarte „Trusted Identity Provider“ (Vertrauenswürdiger Identitätsanbieter) ausgewählt ist](./media/sap-hana-cloud-platform-tutorial/ic790802.png "Verwaltung von Vertrauensstellungen")
   
    >[!NOTE]
    >Um die Liste der vertrauenswürdigen Identitätsanbieter verwalten zu können, müssen Sie den Konfigurationstyp „Benutzerdefinierte“ im Abschnitt „Lokale Dienstanbieter“ ausgewählt haben. Beim Standard-Konfigurationstyp haben Sie ein nicht-bearbeitbares und implizites Vertrauen gegenüber dem SAP ID-Dienst. Bei der Option „Keine“ haben Sie keine Vertrauenseinstellungen.
    > 
    > 

6. Klicken Sie auf die Registerkarte **Allgemein** und dann auf **Durchsuchen** , um die heruntergeladene Metadatendatei hochzuladen.
    
    ![Verwaltung von Vertrauensstellungen](./media/sap-hana-cloud-platform-tutorial/ic793932.png "Verwaltung von Vertrauensstellungen")
    
    >[!NOTE]
    >Nach dem Hochladen der Metadatendatei werden die Werte für **URL für einmaliges Anmelden** , **URL für einmaliges Abmelden** und **Signaturzertifikat** automatisch ausgefüllt.
    > 
     
7. Klicken Sie auf die Registerkarte **Attribute** .

8. Führen Sie auf der Registerkarte **Attribute** die folgenden Schritte aus:
    
    ![Attribute](./media/sap-hana-cloud-platform-tutorial/ic790804.png "Attributes") 

    a. Klicken Sie auf **Assertion-Attribut hinzufügen** , und für Sie dann die folgenden Assertion-basierten Attribute hinzu:
       
    | Assertion-Attribut | Prinzipal-Attribut |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |firstname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |lastname |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |email |
   
     >[!NOTE]
     >Die Konfiguration der Attribute hängt davon ab, wie die Anwendung(en) für SPC entwickelt wurden – also welche Attribute sie in der SAML-Antwort erwarten und unter welchem Namen (Prinzipal-Attribut) sie auf dieses Attribut im Code zugreifen.
     > 
    
    b. Das **Standard-Attribut** im Screenshot dient nur der Veranschaulichung. Es ist nicht erforderlich, damit das Szenario funktioniert.  
 
    c. Die Namen und Werte für das im Screenshot gezeigte **Prinzipal Attribut** ist abhängig davon, wie die Anwendung entwickelt wird. Es ist möglich, dass die Anwendung andere Zuordnungen erfordert.

### <a name="assertion-based-groups"></a>Assertion-Gruppen

Als optionalen Schritt können Sie Assertion-Gruppen für Ihren Azure Active Directory-Identitätsanbieter konfigurieren.

Mithilfe von Gruppen für SAP Cloud Platform können Sie einzelne oder mehrere Benutzer dynamisch einzelnen oder mehreren Rollen in Ihren SAP Cloud Platform-Anwendungen zuweisen – definiert durch Werte von Attributen in der SAML 2.0-Assertion. 

Beispiel: Wenn die Assertion das Attribut „ *contract=temporary* “ enthält, kann es sein, dass Sie alle betroffenen Benutzer der Gruppe „ *TEMPORÄR* “ hinzufügen möchten. Die Gruppe „ *TEMPORÄR* “ enthält unter Umständen Rollen aus mindestens einer Anwendung, die unter Ihrem SAP Cloud Platform-Konto bereitgestellt wurde.
 
Verwenden Sie Assertion-basierte Gruppen, wenn Sie Anwendungsrollen in Ihrem SAP Cloud Platform-Konto gleichzeitig viele Benutzer zuweisen möchten. Wenn Sie nur einen einzelnen Benutzer oder eine geringe Anzahl von Benutzern bestimmten Rollen zuweisen möchten, empfiehlt es sich, diese direkt im SAP Cloud Platform-Cockpit auf der Registerkarte **Autorisierungen** zuzuweisen.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** , **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** Folgendes ein: **brittasimon\@ihreunternehmensdomäne.erweiterung**.  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf SAP Cloud Platform gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **SAP Cloud Platform** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Geben Sie in der Anwendungsliste **SAP Cloud Platform** ein, und wählen Sie den Eintrag aus.

    ![Der Link „SAP Cloud Platform“ in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-sap-cloud-platform-test-user"></a>Erstellen eines SAP Cloud Platform-Testbenutzers

Um Azure AD-Benutzern die Anmeldung bei SAP Cloud Platform zu ermöglichen, müssen Sie ihnen Rollen in SAP Cloud Platform zuweisen.

**Führen Sie die folgenden Schritte aus, um einem Benutzer eine Rolle zuzuweisen:**

1. Melden Sie sich bei Ihrem **SAP Cloud Platform** -Cockpit an.

2. Führen Sie Folgendes aus:
   
    ![Autorisierungen](./media/sap-hana-cloud-platform-tutorial/ic790805.png "Authorizations")
   
    a. Klicken Sie auf **Autorisierung**.

    b. Klicken Sie auf die Registerkarte **Benutzer** .

    c. Geben Sie im Textfeld **Benutzer** die E-Mail-Adresse des Benutzers ein.

    d. Klicken Sie auf **Zuweisen** , um den Benutzer einer Rolle zuzuweisen.

    e. Klicken Sie auf **Speichern**.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „SAP Cloud Platform“ klicken, sollten Sie automatisch bei der SAP Cloud Platform-Anwendung angemeldet werden, für die Sie SSO eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)