---
title: 'Tutorial: Azure Active Directory-Integration in SilkRoad Life Suite | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und der SilkRoad Life Suite konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 89d086ce136885e203e300f04bdbf3ade0affeb3
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516051"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Tutorial: Azure Active Directory-Integration in die SilkRoad Life Suite

In diesem Tutorial erfahren Sie, wie Sie SilkRoad Life Suite in Azure Active Directory (Azure AD) integrieren.
Die Integration der SilkRoad Life Suite in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer auf die SilkRoad Life Suite zugreifen kann.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei SilkRoad Life Suite anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in die SilkRoad Life Suite konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* SilkRoad Life Suite-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SilkRoad Life Suite unterstützt das **SP-initiierte** einmalige Anmelden.

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Hinzufügen der SilkRoad Life Suite aus dem Katalog

Zum Konfigurieren der Integration der SilkRoad Life Suite in Azure AD müssen Sie die SilkRoad Life Suite aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um die SilkRoad Life Suite aus dem Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory** .

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **SilkRoad Life Suite** ein, wählen Sie im Ergebnisbereich **SilkRoad Life Suite** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

    ![SilkRoad Life Suite in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden mit Azure AD bei SilkRoad Life Suite mithilfe einer Testbenutzerin namens **Britta Simon** .
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SilkRoad Life Suite eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei der SilkRoad Life Suite müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für SilkRoad Life Suite](#configure-silkroad-life-suite-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines SilkRoad Life Suite-Testbenutzers](#create-silkroad-life-suite-test-user)** , um eine Entsprechung von Britta Simon in der SilkRoad Life Suite zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei der SilkRoad Life Suite die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **SilkRoad Life Suite** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie über eine **Dienstanbieter-Metadatendatei** verfügen:

    > [!NOTE]
    > Sie erhalten die **Dienstanbieter-Metadatendatei** , die weiter unten in diesem Tutorial erläutert wird.

    a. Klicken Sie auf **Metadatendatei hochladen** .

    ![Screenshot: Abschnitt „Grundlegende SAML-Konfiguration“ mit dem Link „Metadatendatei hochladen“](common/upload-metadata.png)

    b. Klicken Sie auf das **Ordnerlogo** , wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen** .

    ![Screenshot: Dialogfeld, in dem Sie eine Datei auswählen und hochladen können](common/browse-upload-metadata.png)

    c. Nach dem erfolgreichen Upload der Metadatendatei werden die Werte unter **Bezeichner** und **Antwort-URL** im Abschnitt „Grundlegende SAML-Konfiguration“ automatisch eingefügt.

    ![Screenshot: Seite „Grundlegende SAML-Konfiguration“ zum Eingeben des Bezeichners und einer Antwort-URL sowie zum Klicken auf „Speichern“](common/sp-identifier-reply.png)

    > [!Note]
    > Falls die Werte **Bezeichner** und **Antwort-URL** nicht automatisch aufgefüllt werden, geben Sie die erforderlichen Werte manuell ein.

    d. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<subdomain>.silkroad-eng.com/Authentication/`

5. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie nicht über eine **Dienstanbieter-Metadatendatei** verfügen:

    ![SSO-Informationen zur Domäne und den URLs für SilkRoad Life Suite](common/sp-identifier-reply.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<subdomain>.silkroad-eng.com/Authentication/`

    b. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein:

    - `https://<subdomain>.silkroad-eng.com/Authentication/SP`
    - `https://<subdomain>.silkroad.com/Authentication/SP`

    c. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein:

    - `https://<subdomain>.silkroad-eng.com/Authentication/`
    - `https://<subdomain>.silkroad.com/Authentication/`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL, den tatsächlichen Bezeichner und die tatsächliche Antwort-URL. Wenden Sie sich an das [Clientsupportteam von SilkRoad Life Suite](https://www.silkroad.com/locations/), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML** -Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

7. Kopieren Sie im Abschnitt **SilkRoad Life Suite einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-silkroad-life-suite-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für SilkRoad Life Suite

1. Melden Sie sich bei Ihrer SilkRoad Life Suite-Unternehmenswebsite als Administrator an.

    > [!NOTE]
    > Um Zugriff auf die Authentifizierungsanwendung der Anwendung SilkRoad Life Suite zu erhalten und den Verbund mit Microsoft Azure AD zu konfigurieren, wenden Sie sich an den SilkRoad-Support oder Ihren Vertriebsmitarbeiter für SilkRoad-Dienste.

1. Wechseln Sie zu **Dienstanbieter** , und klicken Sie dann auf **Verbunddetails** .

    ![Screenshot: Ausgewählte Option „Verbunddetails“ unter „Dienstanbieter“](./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png)

1. Klicken Sie auf **Download Federation Metadata** (Verbundmetadaten herunterladen), und speichern Sie die Metadatendatei dann auf Ihrem Computer. Verwenden Sie die heruntergeladenen Verbundmetadaten als **Dienstanbieter-Metadatendatei** im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal.

    ![Screenshot: Link „Download Federation Metadata“ (Verbundmetadaten herunterladen)](./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png)

1. Klicken Sie in der Anwendung **SilkRoad** auf **Authentication Sources** (Authentifizierungsquellen).

    ![Screenshot: Ausgewählte Option „Authentication Sources“ (Authentifizierungsquellen)](./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png) 

1. Klicken Sie auf **Add Authentication Source** (Authentifizierungsquelle hinzufügen).

    ![Screenshot: Link „Add Authentication Source“ (Authentifizierungsquelle hinzufügen)](./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png)

1. Führen Sie im Abschnitt **Add Authentication Source** (Authentifizierungsquelle hinzufügen) die folgenden Schritte aus:

    ![Screenshot: „Add Authentication Source“ (Authentifizierungsquelle hinzufügen) mit ausgewählter Schaltfläche „Create Identity Provider using File Data“ (Identitätsanbieter mithilfe von Dateidaten erstellen)](./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png)
  
    a. Klicken Sie unter **Option 2 - Metadata File** (Option 2 – Metadatendatei) auf **Browse (Durchsuchen)** , um die aus dem Azure-Portal heruntergeladene Metadatendatei hochzuladen.
  
    b. Klicken Sie auf **Create Identity Provider using File Data** (Identitätsanbieter mithilfe von Dateidaten erstellen).

1. Klicken Sie im Abschnitt **Authentication Sources (Authentifizierungsquellen)** auf **Edit (Bearbeiten)** .

    ![Screenshot: „Authentication Sources“ (Authentifizierungsquellen) mit ausgewählter Bearbeitungsoption](./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png)

1. Führen Sie im Dialogfeld **Edit Authentication Source** (Authentifizierungsquelle bearbeiten) die folgenden Schritte aus:

    ![Screenshot: Dialogfeld „Edit Authentication Source“ (Authentifizierungsquelle bearbeiten), in dem Sie die beschriebenen Werte eingeben können](./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png)

    a. Wählen Sie für **Enabled (Aktiviert)** die Option **Yes (Ja)** aus.

    b. Fügen Sie in das Textfeld **EntityId** den Wert für **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Geben Sie im Textfeld **IdP Description** (IdP-Beschreibung) eine Beschreibung für die Konfiguration ein (etwa *Azure AD SSO* ).

    d. Laden Sie im Textfeld **Metadata file**  (Metadatendatei), die **Metadatendatei** hoch, die Sie aus dem Azure-Portal heruntergeladen haben.
  
    e. Geben Sie im Textfeld **IdP Name** (IdP-Name) einen für Ihre Konfiguration spezifischen Namen ein (etwa *Azure SP* ).
  
    f. Fügen Sie in das Textfeld **Abmeldedienst-URL** den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    g. Fügen Sie in das Textfeld **Anmeldedienst-URL** den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    h. Klicken Sie auf **Speichern** .

1. Deaktivieren Sie alle anderen Authentifizierungsquellen.

    ![Screenshot: „Authentication Sources“ (Authentifizierungsquellen) mit der Möglichkeit zum Deaktivieren anderer Quellen ](./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** , **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** den Namen `brittasimon@yourcompanydomain.extension` ein.  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf SilkRoad Life Suite erteilen.

1. Wählen Sie im Azure-Portal die Option **Unternehmensanwendungen** aus, und wählen Sie dann **Alle Anwendungen** und **SilkRoad Life Suite** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **SilkRoad Life Suite** aus.

    ![SilkRoad Life Suite-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen** .

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen** .

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

### <a name="create-silkroad-life-suite-test-user"></a>Erstellen eines SilkRoad Life Suite-Testbenutzers

In diesem Abschnitt erstellen Sie in SilkRoad Life Suite einen Benutzer namens Britta Simon. Arbeiten Sie mit dem [Clientsupportteam von SilkRoad Life Suite](https://www.silkroad.com/locations/) zusammen, um der SilkRoad Life Suite-Plattform Benutzer hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „SilkRoad Life Suite“ klicken, sollten Sie automatisch bei der SilkRoad Life Suite-Anwendung angemeldet werden, für die Sie SSO eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)