---
title: 'Tutorial: Azure Active Directory-Integration von OpsGenie | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und OpsGenie konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: a71c29032730e0724213f9f5ef17f7336c188741
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516680"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-opsgenie"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit OpsGenie

In diesem Tutorial erfahren Sie, wie Sie OpsGenie in Azure Active Directory (Azure AD) integrieren. Die Integration von OpsGenie in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf OpsGenie hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei OpsGenie anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein OpsGenie-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* OpsGenie unterstützt **IDP-initiiertes** einmaliges Anmelden.
* Nach dem Konfigurieren von OpsGenie können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.

## <a name="adding-opsgenie-from-the-gallery"></a>Hinzufügen von OpsGenie aus dem Katalog

Zum Konfigurieren der Integration von OpsGenie in Azure AD müssen Sie OpsGenie aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **OpsGenie** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **OpsGenie** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on-for-opsgenie"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für OpsGenie

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit OpsGenie mithilfe eines Testbenutzers mit dem Namen **B. Simon** . Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in OpsGenie eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit OpsGenie die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für OpsGenie](#configure-opsgenie-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * **[Erstellen eines OpsGenie-Testbenutzers](#create-opsgenie-test-user)** , um eine Entsprechung von B. Simon in OpsGenie zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **OpsGenie** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://app.opsginie.com/auth/saml/<UNIQUEID>`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://app.opsginie.com/auth/saml?id=<UNIQUEID>`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch den tatsächlichen Bezeichner und die tatsächliche Antwort-URL. Dies wird später in diesem Tutorial beschrieben.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

1. Kopieren Sie im Abschnitt **OpsGenie einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert aus dem Feld **Kennwort** .
   1. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf OpsGenie gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **OpsGenie** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen** .
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen** .
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

## <a name="configure-opsgenie-sso"></a>Konfigurieren des einmaligen Anmeldens für OpsGenie

1. Öffnen Sie eine weitere Browserinstanz, und melden Sie sich bei OpsGenie als Administrator an.

2. Klicken Sie auf **Einstellungen** und dann auf die Registerkarte **Einmaliges Anmelden** .
   
    ![Einmaliges Anmelden bei OpsGenie](./media/opsgenie-tutorial/tutorial-opsgenie-06.png)

3. Um SSO (Single Sign-On, einmaliges Anmelden) zu aktivieren, wählen Sie **Enabled** aus.
   
    ![Screenshot: Aktiviertes Kontrollkästchen „Enabled“ (Aktiviert)](./media/opsgenie-tutorial/tutorial-opsgenie-07.png) 

4. Klicken Sie im Abschnitt **Anbieter** auf die Registerkarte **Azure Active Directory** .
   
    ![Screenshot: Abschnitt „Anbieter“ mit Auswahl der Registerkarte „Azure Active Directory“](./media/opsgenie-tutorial/tutorial-opsgenie-08.png) 

5. Führen Sie auf der Dialogfeldseite "Azure Active Directory" die folgenden Schritte aus:
   
    ![Screenshot: Abschnitt „Einmaliges Anmelden“ mit Umschalter für die Aktivierung des einmaligen Anmeldens und den Optionen „SAML 2.0-Endpunkt“ und „Metadaten-URL“](./media/opsgenie-tutorial/tutorial-opsgenie-09.png)
    
    a. Kopieren Sie den Wert im Feld **App ID URI** (App-ID-URI), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Bezeichner (Entitäts-ID)** ein.

    a. Kopieren Sie den Wert unter **Reply URL** (Antwort-URL), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Antwort-URL** ein.

    a. Fügen Sie im Textfeld **SAML 2.0 Endpoint** (SAML 2.0-Endpunkt) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.
    
    b. Fügen Sie im Textfeld **Metadaten-URL** den Wert für die **Verbundmetadaten-URL der App** ein, den Sie aus dem Azure-Portal kopiert haben.
    
    c. Schalten Sie zum Aktivieren des einmaligen Anmeldens die Umschaltfläche **Enable single sign-on** (Einmaliges Anmelden aktivieren) ein.

    d. Klicken Sie auf **Apply SSO settings** (SSO-Einstellungen anwenden).

### <a name="create-opsgenie-test-user"></a>Erstellen eines OpsGenie-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens B. Simon in OpsGenie. 

1. Melden Sie sich in einem Webbrowserfenster bei Ihrem OpsGenie-Mandanten als Administrator an.

2. Navigieren Sie zur Liste der Benutzer, indem Sie im linken Bereich auf **Users** (Benutzer) klicken.
   
    ![OpsGenie-Einstellungen](./media/opsgenie-tutorial/tutorial-opsgenie-10.png) 

3. Klicken Sie auf **Benutzer hinzufügen** .

4. Führen Sie im Dialogfeld **Add User** die folgenden Schritte aus:
   
    ![Screenshot: Dialogfeld „Benutzer hinzufügen“ mit Hervorhebung der Textfelder „E-Mail“ und „Vollständiger Name“ und Auswahl der Schaltfläche „Speichern“](./media/opsgenie-tutorial/tutorial-opsgenie-11.png)
   
    a. Geben Sie im Textfeld **Email** (E-Mail-Adresse) die E-Mail-Adresse von B. Simon aus Azure Active Directory ein.
   
    b. Geben Sie im Textfeld **Full Name** (Vollständiger Name) den Namen **B.Simon** ein.
   
    c. Klicken Sie auf **Speichern** . 

> [!NOTE]
> Daraufhin erhält B. Simon eine E-Mail mit Anweisungen zum Einrichten ihres Profils.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „OpsGenie“ klicken, sollten Sie automatisch bei der OpsGenie-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [OpsGenie mit Azure AD ausprobieren](https://aad.portal.azure.com/)

- [Was ist Sitzungssteuerung in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)