---
title: 'Tutorial: Azure Active Directory-Integration mit Recognize | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Recognize konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 7195a126938deabf542e6fa3e52b72c2efdddac2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515218"
---
# <a name="tutorial-azure-active-directory-integration-with-recognize"></a>Tutorial: Azure Active Directory-Integration mit Recognize

In diesem Tutorial erfahren Sie, wie Sie Recognize in Azure Active Directory (Azure AD) integrieren.
Die Integration von Recognize in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Recognize hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Recognize anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Recognize konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein Recognize-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Recognize unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="adding-recognize-from-the-gallery"></a>Hinzufügen von Recognize aus dem Katalog

Zum Konfigurieren der Integration von Workrite in Azure AD müssen Sie Recognize aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um Recognize aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory** .

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Recognize** ein, wählen Sie im Ergebnisbereich **Recognize** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

     ![Recognize in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Recognize mithilfe eines Testbenutzers namens **Britta Simon** .
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Recognize eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Recognize müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Recognize](#configure-recognize-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Recognize-Testbenutzers](#create-recognize-test-user)** , um ein Pendant von Britta Simon in Recognize zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Recognize die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Recognize** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie über eine **Dienstanbieter-Metadatendatei** verfügen:

    >[!NOTE]
    >Sie rufen die **Dienstanbieter-Metadatendatei** in diesem Tutorial im Abschnitt **Konfigurieren des einmaligen Anmeldens für Recognize** ab.

    a. Klicken Sie auf **Metadatendatei hochladen** .

    ![Metadatendatei hochladen](common/upload-metadata.png)

    b. Klicken Sie auf das **Ordnerlogo** , wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen** .

    ![Metadatendatei auswählen](common/browse-upload-metadata.png)

    c. Nach dem erfolgreichen Upload der Metadatendatei wird der Wert **Bezeichner** automatisch im Abschnitt „Grundlegende SAML-Konfiguration“ eingefügt:

    ![SSO-Informationen zur Domäne und zu den URLs für Recognize](common/sp-identifier.png)

     Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://recognizeapp.com/<your-domain>/saml/sso`.

    > [!Note]
    > Wird der Wert **Bezeichner** nicht automatisch aufgefüllt, können Sie ihn abrufen, indem Sie im Abschnitt mit den SSO-Einstellungen die Metadaten-URL des Dienstanbieters öffnen. Die Vorgehensweise ist weiter unten im Tutorial im Abschnitt **Konfigurieren des einmaligen Anmeldens für Recognize** erläutert. Der Wert der Anmelde-URL entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Den Wert erhalten Sie vom [Supportteam für den Recognize-Client](mailto:support@recognizeapp.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Recognize einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-recognize-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Recognize

1. Melden Sie sich in einem anderen Webbrowserfenster bei Ihrem Recognize-Mandanten als Administrator an.

2. Klicken Sie auf der rechten oberen Ecke auf **Menü** . Wechseln Sie zu **Company Admin** (Unternehmensadministrator).
   
    ![Screenshot: Auswahl von „Company“ (Unternehmen) im Menü „Settings“ (Einstellungen)](./media/recognize-tutorial/tutorial_recognize_000.png)

3. Klicken Sie im linken Navigationsbereich auf **Einstellungen** .
   
    ![Screenshot: Auswahl von „Einstellungen“ auf der Navigationsseite](./media/recognize-tutorial/tutorial_recognize_001.png)

4. Führen Sie im Abschnitt **SSO Settings** (SSO-Einstellungen) die folgenden Schritte aus:
   
    ![Screenshot: Abschnitt „SSO Settings“ (SSO-Einstellungen), in dem Sie die beschriebenen Werte eingeben können](./media/recognize-tutorial/tutorial_recognize_002.png)
    
    a. Wählen Sie für **Enable SSO** (SSO aktivieren) die Option **ON** (EIN) aus.

    b. Fügen Sie in das Textfeld **IDP Entity ID** (IDP-Entitäts-ID) den Wert für **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.
    
    c. Fügen Sie in das Textfeld **Sso target url** (SSO-Ziel-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.
    
    d. Fügen Sie in das Textfeld **Slo target url** (SLO-Ziel-URL) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben. 
    
    e. Öffnen Sie die heruntergeladene Datei **Zertifikat (Base64)** in Editor, kopieren Sie den Inhalt in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Zertifikat** ein.
    
    f. Klicken Sie auf die Schaltfläche **Save settings** (Einstellungen speichern). 

5. Kopieren Sie neben dem Abschnitt **SSO Settings** (SSO-Einstellungen) die URL unter **Service Provider Metadata url** (Metadaten-URL des Dienstanbieters).
   
    ![Screenshot: Abschnitt „Notes“ (Hinweise), in dem Sie die Metadaten des Dienstanbieters kopieren können](./media/recognize-tutorial/tutorial_recognize_003.png)

6. Öffnen Sie den **Metadaten-URL-Link** in einem leeren Browserfenster, um das Metadatendokument herunterzuladen. Kopieren Sie anschließend den Wert für „EntityDescriptor (entityID)“ in der Datei, und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Bezeichner** ein.
    
    ![Screenshot: Textfeld mit XML-Code im Nur-Text-Format, dem Sie die Entitäts-ID entnehmen können](./media/recognize-tutorial/tutorial_recognize_004.png)

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Recognize gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Recognize** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste den Eintrag **Recognize** aus.

    ![Recognize-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen** .

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen** .

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

### <a name="create-recognize-test-user"></a>Erstellen eines Recognize-Testbenutzers

Damit sich Azure AD-Benutzer bei Recognize anmelden können, müssen sie in Recognize bereitgestellt werden. Im Fall von Recognize muss die Bereitstellung manuell durchgeführt werden.

Diese App unterstützt keine SCIM-Bereitstellung. Sie verfügt aber über eine Benutzersynchronisierung, die Benutzer bereitstellt. 

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei der Recognize-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf der rechten oberen Ecke auf **Menü** . Wechseln Sie zu **Company Admin** (Unternehmensadministrator).

3. Klicken Sie im linken Navigationsbereich auf **Einstellungen** .

4. Führen Sie im Abschnitt **User Sync** (Benutzersynchronisierung) die folgenden Schritte aus.
   
    ![Neuer Benutzer](./media/recognize-tutorial/tutorial_recognize_005.png "Neuer Benutzer")
   
    a. Wählen Sie für **Sync Enabled** (Synchronisierung aktiviert) die Option **ON** (EIN) aus.
   
    b. Wählen Sie für **Choose sync provider** (Synchronisierungsanbieter auswählen) die Option **Microsoft/Office 365** aus.
   
    c. Klicken Sie auf **Run User Sync** (Benutzersynchronisierung ausführen).

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Recognize“ klicken, sollten Sie automatisch bei der Recognize-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)