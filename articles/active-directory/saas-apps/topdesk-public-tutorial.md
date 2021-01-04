---
title: 'Tutorial: Azure Active Directory-Integration mit TOPdesk – Public | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und TOPdesk - Public konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.openlocfilehash: 3688dc2db67c0c1343a74be4652cb52466ef762e
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520437"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Tutorial: Azure Active Directory-Integration mit TOPdesk – Public

In diesem Tutorial erfahren Sie, wie Sie TOPdesk - Public in Azure Active Directory (Azure AD) integrieren.
Die Integration von TOPdesk - Public in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf TOPdesk - Public hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei TOPdesk - Public anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in TOPdesk - Public konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* TOPdesk - Public-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* TOPdesk - Public unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="adding-topdesk---public-from-the-gallery"></a>Hinzufügen von TOPdesk - Public aus dem Katalog

Zum Konfigurieren der Integration von TOPdesk - Public in Azure AD müssen Sie TOPdesk - Public aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um TOPdesk - Public aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory** .

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **TOPdesk - Public** ein, wählen Sie im Ergebnisbereich **TOPdesk - Public** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

     ![TOPdesk - Public in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit TOPdesk - Public mithilfe eines Testbenutzers namens **Britta Simon** .
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in TOPdesk - Public eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei TOPdesk - Public müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für TOPdesk - Public](#configure-topdesk---public-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines TOPdesk - Public-Testbenutzers](#create-topdesk---public-test-user)** , um ein Pendant von Britta Simon in TOPdesk - Public zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit TOPdesk - Public die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **TOPdesk - Public** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4.  Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie über eine **Dienstanbieter-Metadatendatei** verfügen:

    >[!NOTE]
    >Die **Dienstanbieter-Metadatendatei** rufen Sie später im Tutorial im Abschnitt **Konfigurieren des einmaligen Anmeldens für TOPdesk - Public** ab.

    a. Klicken Sie auf **Metadatendatei hochladen** .
    
    ![Metadatendatei hochladen](common/upload-metadata.png)

    b. Klicken Sie auf das **Ordnerlogo** , wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen** .

    ![Metadatendatei auswählen](common/browse-upload-metadata.png)

    c. Nach dem erfolgreichen Upload der Metadatendatei werden die Werte unter **Bezeichner** und **Antwort-URL** im Abschnitt „Grundlegende SAML-Konfiguration“ automatisch eingefügt.

    ![SSO-Informationen zur Domäne und zu den URLs für TOPdesk - Public](common/sp-identifier-reply.png)

    d. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<companyname>.topdesk.net`

    e. Geben Sie im Textfeld **Identifier URL** (Bezeichner-URL) die TOPdesk-Metadaten-URL ein, die Sie aus der TOPdesk-Konfiguration abrufen können. Verwenden Sie das folgende Format: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`.
    
    f. Geben Sie im Textfeld **Antwort-URL** eine URL nach folgendem Muster ein: `https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > Werden die Werte für **Bezeichner** und **Antwort-URL** nicht automatisch aufgefüllt, müssen Sie sie manuell eingeben. Verwenden Sie für „Bezeichner“ das oben genannte Format. Den Wert für „Antwort-URL“ rufen Sie später im Tutorial im Abschnitt **Konfigurieren des einmaligen Anmeldens für TOPdesk - Public** ab. Der Wert für die **Anmelde-URL** entspricht nicht dem tatsächlichen Wert. Aktualisieren Sie den Wert mit der richtigen Anmelde-URL. Den Wert erhalten Sie vom [Supportteam für den TOPdesk - Public-Client](https://help.topdesk.com/saas/enterprise/user/). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML** -Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **TOPdesk - Public einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-topdesk---public-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für TOPdesk - Public

1. Melden Sie sich bei der **TOPdesk - Public** -Unternehmenswebsite als Administrator an.

2. Klicken Sie im Menü **TOPdesk** auf **Einstellungen** .
   
    ![Einstellungen](./media/topdesk-public-tutorial/ic790598.png "Einstellungen")

3. Klicken Sie auf **Anmeldeeinstellungen** .
   
    ![Login Settings (Anmeldeeinstellungen)](./media/topdesk-public-tutorial/ic790599.png "Anmeldeeinstellungen")

4. Erweitern Sie das Menü **Anmeldeeinstellungen** , und klicken Sie dann auf **Allgemein** .
   
    ![Allgemein](./media/topdesk-public-tutorial/ic790600.png "Allgemein")

5. Führen Sie im Abschnitt **Öffentlich** des Konfigurationsabschnitts **SAML-Anmeldung** die folgenden Schritte aus:
   
    ![Technische Einstellungen](./media/topdesk-public-tutorial/ic790601.png "Technische Einstellungen")
   
    a. Klicken Sie auf **Herunterladen** , um die  öffentliche Metadatendatei herunterzuladen und sie dann lokal auf Ihrem Computer zu speichern.
   
    b. Öffnen Sie die heruntergeladene Metadatendatei, und suchen Sie den Knoten **AssertionConsumerService** .

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Kopieren Sie den Wert **AssertionConsumerService** , und fügen Sie ihn im Abschnitt **Basic SAML Configuration** (Grundlegende SAML-Konfiguration) in das Textfeld **Reply URL** (Antwort-URL) ein.      
   
6. Zum Erstellen einer Zertifikatsdatei führen Sie die folgenden Schritte durch:
    
    ![Certificate](./media/topdesk-public-tutorial/ic790606.png "Zertifikat")
    
    a. Öffnen Sie die aus dem Azure-Portal heruntergeladene Metadatendatei.
    
    b. Erweitern Sie den Knoten **RoleDescriptor** , der über einen **xsi:type** von **fed:ApplicationServiceType** verfügt.
    
    c. Kopieren Sie den Wert des Knotens **X509Certificate** .
    
    d. Speichern Sie den kopierten **X509Certificate** -Wert lokal in einer Datei auf Ihrem Computer.

7. Klicken Sie im Abschnitt **Öffentlich** auf **Hinzufügen** .
    
    ![SAML Login (SAML-Anmeldung)](./media/topdesk-public-tutorial/ic790625.png "SAML-Anmeldung")

8. Führen Sie auf der Seite **SAML Configuration Assistant** die folgenden Schritte aus:
    
    ![SAML Configuration Assistant (SAML-Konfigurationsassistent)](./media/topdesk-public-tutorial/ic790608.png "SAML Configuration Assistant")
    
    a. Klicken Sie zum Hochladen der aus dem Azure-Portal heruntergeladenen Metadatendatei unter **Verbundmetadaten** auf **Durchsuchen** .

    b. Klicken Sie zum Hochladen Ihrer Zertifikatsdatei unter **Zertifikat (RSA)** auf **Durchsuchen** .

    c. Klicken Sie zum Hochladen der Logodatei, die Sie vom TOPdesk-Supportteam erhalten haben, unter **Logosymbol** auf **Durchsuchen** .

    d. Geben Sie `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` in das Textfeld **Benutzernamenattribut** ein.

    e. Geben Sie in das Textfeld **Anzeigenname** einen Namen für die Konfiguration ein.

    f. Klicken Sie auf **Speichern** .

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** , **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** den Namen brittasimon@yourcompanydomain.extension ein. Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf TOPdesk - Public gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **TOPdesk - Public** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste den Eintrag **TOPdesk - Public** aus.

    ![TOPdesk - Public-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen** .

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen** .

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

### <a name="create-topdesk---public-test-user"></a>Erstellen eines TOPdesk - Public-Testbenutzers

Damit sich Azure AD-Benutzer bei TOPdesk - Public anmelden können, müssen sie in TOPdesk - Public bereitgestellt werden. Im Fall von TOPdesk - Public ist die Bereitstellung eine manuelle Aufgabe.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>So konfigurieren Sie die Benutzerbereitstellung

1. Melden Sie sich bei Ihrem **TOPdesk - Public** -Unternehmenswebsite als Administrator an.

2. Klicken Sie im Menü oben auf **TOPdesk \> Neu \> Support-Dateien \> Person** .
   
    ![Person](./media/topdesk-public-tutorial/ic790628.png "Person")

3. Führen Sie im Dialogfeld „Neue Person“ die folgenden Schritte aus:
   
    ![Neue Person](./media/topdesk-public-tutorial/ic790629.png "Neue Person")
   
    a. Klicken Sie auf die Registerkarte „Allgemein“.

    b. Geben Sie im Textfeld **Nachname** den Nachnamen des Benutzers ein, z.B. „Simon“.
 
    c. Wählen Sie einen **Speicherort** für das Konto aus.
 
    d. Klicken Sie auf **Speichern** .

> [!NOTE]
> Sie können Azure AD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von TOPdesk - Public-Benutzerkonten oder mithilfe der von TOPdesk - Public bereitgestellten APIs erstellen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „TOPdesk - Public“ klicken, sollten Sie automatisch bei der TOPdesk - Public-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)