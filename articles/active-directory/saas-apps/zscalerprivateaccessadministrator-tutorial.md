---
title: 'Tutorial: Azure Active Directory-Integration mit Zscaler Private Access Administrator | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Zscaler Private Access Administrator konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: 803f5c25429b33e6d1af130196d38e125b410eb4
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890988"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Tutorial: Azure Active Directory-Integration mit Zscaler Private Access Administrator

In diesem Tutorial erfahren Sie, wie Sie Zscaler Private Access Administrator in Azure Active Directory (Azure AD) integrieren.
Die Integration von Zscaler Private Access Administrator in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Zscaler Private Access Administrator hat.
* Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Zscaler Private Access Administrator anzumelden (Single Sign-On, SSO; einmaliges Anmelden).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Zscaler Private Access Administrator konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Abonnement für Zscaler Private Access Administrator, für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Zscaler Private Access Administrator unterstützt **SP** - und **IDP** -initiiertes SSO.

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Hinzufügen von Zscaler Private Access Administrator über den Katalog

Zum Konfigurieren der Integration von Zscaler Private Access Administrator in Azure AD müssen Sie Zscaler Private Access Administrator aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Zscaler Private Access Administrator aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie in das Suchfeld **Zscaler Private Access Administrator** ein, wählen Sie im Ergebnisbereich **Zscaler Private Access Administrator** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

    ![Zscaler Private Access Administrator in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Zscaler Private Access Administrator mithilfe einer Testbenutzerin namens **Britta Simon**.
Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Zscaler Private Access Administrator muss eine Linkbeziehung eingerichtet werden, damit das einmalige Anmelden funktioniert.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Zscaler Private Access Administrator müssen die folgenden Schritte ausgeführt werden:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Zscaler Private Access Administrator](#configure-zscaler-private-access-administrator-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Testbenutzers für Zscaler Private Access Administrator](#create-zscaler-private-access-administrator-test-user)** , um eine Entsprechung von Britta Simon in Zscaler Private Access Administrator zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei Zscaler Private Access Administrator die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Zscaler Private** Access Administrator die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    ![Screenshot: Abschnitt „Grundlegende SAML-Konfiguration“, in dem Sie die beschriebenen Werte eingeben können](common/idp-relay.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<subdomain>.private.zscaler.com/auth/metadata`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Klicken Sie auf **Zusätzliche URLs festlegen**.

    d. Geben Sie im Textfeld **Relayzustand** eine URL ein: `idpadminsso`

5.  Wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten, führen Sie die folgenden Schritte durch:

    ![Screenshot: Option „Zusätzliche URLs festlegen“ zum Eingeben einer Anmelde-URL](common/both-signonurl.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<subdomain>.private.zscaler.com/auth/sso`   

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Wenden Sie sich an das [Kundensupportteam von Zscaler Private Access Administrator](https://help.zscaler.com/zpa-submit-ticket), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML** -Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

7. Kopieren Sie im Abschnitt für die **Einrichtung von Zscaler Private Access Administrator** die URLs, die Ihre Anforderungen erfüllen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-zscaler-private-access-administrator-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Zscaler Private Access Administrator

1. Melden Sie sich in einem anderen Webbrowserfenster bei Zscaler Private Access Administrator als Administrator an.

2. Klicken Sie im oberen Bereich auf **Verwaltung** und dann im Abschnitt **AUTHENTIFIZIERUNG** auf **IdP-Konfiguration**.

    ![Administrator für Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Klicken Sie in der Ecke oben rechts auf **Add IdP Configuration** (IdP-Konfiguration hinzufügen). 

    ![Hinzufügen der IdP-Konfiguration in Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

4. Führen Sie auf der Seite **Add IdP Configuration** (IdP-Konfiguration hinzufügen) die folgenden Schritte aus:
 
    ![Auswählen der IdP-Konfiguration in Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Klicken Sie auf **Select File** (Datei auswählen), um im Feld **IdP Metadata File Upload** (Upload der IdP-Metadatendatei) die heruntergeladene Metadatendatei von Azure AD hochzuladen.

    b. Die **IdP-Metadaten** werden in Azure AD gelesen, und alle Felder werden aufgefüllt, wie hier gezeigt:

    ![IdP-Konfiguration von Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Wählen Sie für **Single Sign On** (Einmaliges Anmelden) die Option **Administrator**.

    d. Wählen Sie im Feld **Domains** (Domänen) Ihre Domäne aus.
    
    e. Klicken Sie auf **Speichern**.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** , **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** den Namen `brittasimon@yourcompanydomain.extension` ein. Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Zscaler Private Access Administrator gewähren.

1. Wählen Sie im Azure-Portal die Option **Unternehmensanwendungen** und dann **Alle Anwendungen** und **Zscaler Private Access Administrator** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Liste der Anwendungen **Zscaler Private Access Administrator** aus.

    ![Der Link für Zscaler Private Access Administrator in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-zscaler-private-access-administrator-test-user"></a>Erstellen eines Zscaler Private Access Administrator-Testbenutzers

Um Azure AD-Benutzern die Anmeldung bei Zscaler Private Access Administrator zu ermöglichen, müssen diese in Zscaler Private Access Administrator bereitgestellt werden. Im Fall von Zscaler Private Access Administrator ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrer Zscaler Private Access Administrator-Unternehmenswebsite als Administrator an.

2. Klicken Sie im oberen Bereich auf **Verwaltung** und dann im Abschnitt **AUTHENTIFIZIERUNG** auf **IdP-Konfiguration**.

    ![Administrator für Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Klicken Sie links im Menü auf **Administrators** (Administratoren).

    ![Administrator für Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. Klicken Sie in der Ecke oben rechts auf **Add Administrator** (Administrator hinzufügen):

    ![Hinzufügen eines Administrators für Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. Führen Sie auf der Seite **Add Administrator** (Administrator hinzufügen) die folgenden Schritte aus:

    ![Benutzeradministrator in Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. Geben Sie im Textfeld **Username** (Benutzername) die E-Mail-Adresse des Benutzers ein, z. B. BrittaSimon@contoso.com.

    b. Geben Sie im Textfeld **Password** (Kennwort) das Kennwort des Benutzers ein.

    c. Geben Sie im Textfeld **Confirm Password** (Kennwort bestätigen) das Kennwort erneut ein.

    d. Wählen Sie unter **Role** (Rolle) die Option **Zscaler Private Access Administrator** aus.

    e. Geben Sie im Textfeld **Email** (E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein, z.B. BrittaSimon@contoso.com.

    f. Geben Sie im Textfeld **Phone** (Telefon) die Telefonnummer ein.

    g. Wählen Sie im Textfeld **Timezone** (Zeitzone) die Zeitzone aus.

    h. Klicken Sie auf **Speichern**.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel für Zscaler Private Access Administrator klicken, sollten Sie automatisch an der Zscaler Private Access Administrator-Instanz angemeldet werden, für die Sie das einmalige Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)