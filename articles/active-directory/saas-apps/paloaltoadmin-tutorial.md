---
title: 'Tutorial: Azure Active Directory-Integration mit Palo Alto Networks - Admin UI | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Palo Alto Networks - Admin UI konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f45b0779693433f1b1fad3c5bfd4323eacb9a63b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65870225"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Tutorial: Azure Active Directory-Integration mit Palo Alto Networks - Admin UI

In diesem Tutorial erfahren Sie, wie Sie Palo Alto Networks - Admin UI in Azure Active Directory (Azure AD) integrieren.
Die Integration von Palo Alto Networks - Admin UI in Azure AD bietet Ihnen folgende Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Palo Alto Networks - Admin UI hat.
* Sie können es Benutzern ermöglichen, dass sie mit ihren Azure AD-Konten automatisch bei Palo Alto Networks – Admin UI angemeldet werden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Palo Alto Networks - Admin UI konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein Abonnement für Palo Alto Networks - Admin UI, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Palo Alto Networks - Admin UI unterstützt **SP**-initiiertes einmaliges Anmelden.
* Palo Alto Networks – Admin UI unterstützt die **Just-in-Time**-Benutzerbereitstellung.

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Hinzufügen von Palo Alto Networks - Admin UI aus dem Katalog

Zum Konfigurieren der Integration von Palo Alto Networks - Admin UI in Azure AD müssen Sie Palo Alto Networks - Admin UI aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

**Führen Sie zum Hinzufügen von Palo Alto Networks - Admin UI aus dem Katalog die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Palo Alto Networks - Admin UI** ein, wählen Sie im Ergebnisbereich **Palo Alto Networks - Admin UI** aus, und klicken Sie auf **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Palo Alto Networks - Admin UI in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Palo Alto Networks – Admin UI anhand einer Testbenutzerin mit dem Namen **Britta Simon**.
Für einmaliges Anmelden muss zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Palo Alto Networks - Admin UI eine Linkbeziehung eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Palo Alto Networks - Admin UI müssen die folgenden Schritte ausgeführt werden:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Palo Alto Networks - Admin UI](#configure-palo-alto-networks---admin-ui-single-sign-on)**, um die SSO-Einstellungen auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Palo Alto Networks - Admin UI-Testbenutzers](#create-palo-alto-networks---admin-ui-test-user)**, um ein Pendant von Britta Simon in Palo Alto Networks - Admin UI zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei Palo Alto Networks - Admin UI die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Palo Alto Networks – Admin UI** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Palo Alto Networks - Admin UI](common/sp-identifier-reply.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<Customer Firewall FQDN>/php/login.php`

    b. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein: `https://<Customer Firewall FQDN>:443/SAML20/SP`.

    c. Geben Sie im Textfeld **Antwort-URL** die Assertionsverbraucherdienst-URL (Assertion Consumer Service, ACS) im folgenden Format ein: `https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL, den tatsächlichen Bezeichner und die tatsächliche Antwort-URL. Wenden Sie sich an das [Supportteam für den Palo Alto Networks - Admin UI-Client](https://support.paloaltonetworks.com/support), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Die Palo Alto Networks - Admin UI-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Sie können die Werte dieser Attribute im Abschnitt **Benutzerattribute** auf der Anwendungsintegrationsseite verwalten. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![image](common/edit-attribute.png)

   > [!NOTE]
   > Bei den Attributwerten handelt es sich nur um Beispiele. Sie müssen die entsprechenden Werte für *username* und *adminrole* zuordnen. Es ist ein weiteres optionales Attribut *accessdomain* verfügbar, mit dem der Administratorzugriff auf bestimmte virtuelle Systeme in der Firewall beschränkt wird.
   >

6. Konfigurieren Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** das SAML-Tokenattribut wie in der obigen Abbildung gezeigt, und führen Sie die folgenden Schritte aus:

    | NAME |  Quellattribut|
    | --- | --- |
    | username | user.userprincipalname |
    | adminrole | customadmin |
    | | |

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    f. Klicken Sie auf **OK**.

    g. Klicken Sie auf **Speichern**.

    > [!NOTE]
    > Weitere Informationen zu den Attributen finden Sie in den folgenden Artikeln:
    > * [Administrative role profile for Admin UI (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile) (Administratorrollenprofil für Admin UI – adminrole)
    > * [Device access domain for Admin UI (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain) (Gerätezugriffsdomäne für Admin UI – accessdomain)

7. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

8. Kopieren Sie im Abschnitt **Palo Alto Networks - Admin UI einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-palo-alto-networks---admin-ui-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Palo Alto Networks - Admin UI

1. Öffnen Sie als Administrator in einem neuen Fenster die Administratoroberfläche für die Palo Alto Networks-Firewall.

2. Klicken Sie auf die Registerkarte **Device** (Gerät).

    ![Registerkarte „Device“ (Gerät)](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Wählen Sie im linken Bereich **SAML Identity Provider** (SAML-Identitätsanbieter) aus, und klicken Sie anschließend auf **Import** (Importieren), um die Metadatendatei zu importieren.

    ![Schaltfläche zum Importieren der Metadatendatei](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Führen Sie im Fenster **SAML Identity Provider Server Profile Import** (Import des SAML-Identitätsanbieter-Serverprofils) die folgenden Schritte aus:

    ![Fenster „SAML Identity Provider Server Profile Import“ (Import des SAML-Identitätsanbieter-Serverprofils)](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. Geben Sie im Feld **Profile Name** (Profilname) einen Namen ein (z. B. **AzureAD Admin UI**).
    
    b. Klicken Sie unter **Identity Provider Metadata** (Identitätsanbieter-Metadaten) auf **Browse** (Durchsuchen), und wählen Sie die Datei „metadata.xml“ aus, die Sie zuvor aus dem Azure-Portal heruntergeladen haben.
    
    c. Deaktivieren Sie das Kontrollkästchen **Validate Identity Provider Certificate** (Zertifikat des Identitätsanbieters überprüfen).
    
    d. Klicken Sie auf **OK**.
    
    e. Committen Sie die Konfigurationen der Firewall, indem Sie auf die Schaltfläche **Commit** (Committen) klicken.

5. Wählen Sie im linken Bereich **SAML Identity Provider** (SAML-Identitätsanbieter) und anschließend das SAML-Identitätsanbieterprofil (z. B. **AzureAD Admin UI**) aus, das Sie im vorherigen Schritt erstellt haben.

    ![SAML-Identitätsanbieterprofil](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

6. Führen Sie im Fenster **SAML Identity Provider Server Profile** (SAML-Identitätsanbieter-Serverprofil) die folgenden Schritte aus:

    ![Fenster „SAML Identity Provider Server Profile“ (SAML-Identitätsanbieter-Serverprofil)](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. Ersetzen Sie im Feld **Identity Provider SLO URL** (SLO-URL des Identitätsanbieters) die zuvor importierte SLO-URL durch die folgende URL: `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`
  
    b. Klicken Sie auf **OK**.

7. Klicken Sie auf der Administratoroberfläche für die Palo Alto Networks-Firewall auf **Device** (Gerät) und dann auf **Admin Roles** (Administratorrollen).

8. Wählen Sie die Schaltfläche **Hinzufügen** aus.

9. Geben Sie im Fenster **Admin Role Profile** (Administratorrollenprofil) einen Namen für die Administratorrolle in das Feld **Name** ein (z. B. **fwadmin**). Der Administratorrollenname sollte mit dem vom Identitätsanbieter gesendeten Attributnamen für die SAML-Administratorrolle identisch sein. Der Name und Wert der Administratorrolle wurden im Abschnitt **Benutzerattribute** im Azure-Portal erstellt.

    ![Konfigurieren der Administratorrolle für Palo Alto Networks](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
10. Klicken Sie auf der Administratoroberfläche der Firewall auf **Device** (Gerät) und dann auf **Authentication Profile** (Authentifizierungsprofil).

11. Wählen Sie die Schaltfläche **Hinzufügen** aus.

12. Führen Sie im Fenster **Authentication Profile** (Authentifizierungsprofil) die folgenden Schritte aus: 

    ![Fenster „Authentication Profile“ (Authentifizierungsprofil)](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. Geben Sie im Feld **Name** einen Namen ein (z. B. **AzureSAML_Admin_AuthProfile**).

    b. Wählen Sie in der Dropdownliste **Type** (Typ) den Eintrag **SAML** aus. 

    c. Wählen Sie in der Dropdownliste **IdP Server Profile** (IdP-Serverprofil) das entsprechende SAML-Identitätsanbieter-Serverprofil aus (z. B. **AzureAD Admin UI**).

    c. Aktivieren Sie das Kontrollkästchen **Enable Single Logout** (Einmaliges Abmelden aktivieren).

    d. Geben Sie im Feld **Admin Role Attribute** (Administratorrollenattribut) den Attributnamen ein (z. B. **adminrole**).

    e. Klicken Sie auf die Registerkarte **Advanced** (Erweitert) und dann unter **Allow List** (Liste „Zulassen) auf **Add** (Hinzufügen).

    ![Schaltfläche „Add“ (Hinzufügen) auf der Registerkarte „Advanced“ (Erweitert)](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)

    f. Aktivieren Sie das Kontrollkästchen **All** (Alle), oder wählen Sie die Benutzer und Gruppen aus, die sich mit diesem Profil authentifizieren können.  
    Bei der Authentifizierung eines Benutzers gleicht die Firewall den zugewiesenen Benutzernamen bzw. die zugewiesene Gruppe mit den Einträgen in der Liste ab. Wenn Sie keine Einträge hinzufügen, können sich keine Benutzer authentifizieren.

    g. Klicken Sie auf **OK**.

13. Um Administratoren die Verwendung von SAML-SSO mit Azure zu ermöglichen, klicken Sie auf **Device** > **Setup** (Gerät > Einrichten). Klicken Sie im Bereich **Setup** (Einrichten) auf die Registerkarte **Management** (Verwaltung) und dann unter **Authentication Settings** (Authentifizierungseinstellungen) auf die Schaltfläche mit dem Zahnradsymbol (**Einstellungen**).

    ![Schaltfläche „Settings“ (Einstellungen)](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

14. Wählen Sie das SAML-Authentifizierungsprofil aus, das Sie im Fenster „Authentifizierungsprofil“ erstellt haben (z.B. **AzureSAML_Admin_AuthProfile**).

    ![Feld „Authentication Profile“ (Authentifizierungsprofil)](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

15. Klicken Sie auf **OK**.

16. Klicken Sie zum Committen der Konfiguration auf **Commit** (Committen).

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** Folgendes ein: **brittasimon\@ihreunternehmensdomäne.erweiterung**.  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie Zugriff auf Palo Alto Networks - Admin UI gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **Palo Alto Networks - Admin UI**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Palo Alto Networks - Admin UI** aus.

    ![Der Link für Palo Alto Networks - Admin UI in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Erstellen eines Palo Alto Networks - Admin UI-Testbenutzers

Palo Alto Networks – Admin UI unterstützt die Just-in-Time-Benutzerbereitstellung. Wenn ein Benutzer nicht bereits vorhanden ist, wird er nach der erfolgreichen Authentifizierung automatisch im System erstellt. Zum Erstellen des Benutzers ist keine Aktion Ihrerseits erforderlich.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Palo Alto Networks - Admin UI“ klicken, sollten Sie automatisch bei der Palo Alto Networks - Admin UI-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
