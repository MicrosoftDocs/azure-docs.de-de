---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit SiteIntel | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und SiteIntel konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/02/2020
ms.author: jeedes
ms.openlocfilehash: 0c8224bf84a2235086d941df7d02ab6f458f3f16
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92509997"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-siteintel"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit SiteIntel

In diesem Tutorial erfahren Sie, wie Sie SiteIntel in Azure Active Directory (Azure AD) integrieren. Die Integration von SiteIntel in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf SiteIntel hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei SiteIntel anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps (Software as a Service) in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein SiteIntel-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SiteIntel unterstützt SP- und IdP-initiiertes einmaliges Anmelden.
* Nach dem Konfigurieren von SiteIntel können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.

## <a name="add-siteintel-from-the-gallery"></a>Hinzufügen von SiteIntel aus dem Katalog

Zum Konfigurieren der Integration von SiteIntel in Azure AD müssen Sie SiteIntel aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Bereich **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Feld **Aus Katalog hinzufügen** den Suchbegriff **SiteIntel** ein.
1. Wählen Sie in der Ergebnisliste **SiteIntel** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on-for-siteintel"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für SiteIntel

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit SiteIntel mithilfe eines Testbenutzers namens *B.Simon*. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SiteIntel eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit SiteIntel müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen  

    a. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Benutzer B. Simon zu testen  

    b. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen

1. **[Konfigurieren des einmaligen Anmeldens für SiteIntel](#configure-siteintel-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren

    * **[Erstellen eines SiteIntel-Testbenutzers](#create-a-siteintel-test-user)** , um ein Pendant von B. Simon in SiteIntel zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist

1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert.

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Führen Sie die folgenden Schritte aus, um einmaliges Anmelden von Azure AD im Azure-Portal zu aktivieren:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **SiteIntel** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Wählen Sie im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** neben **Grundlegende SAML-Konfiguration** das Symbol **Bearbeiten** (Stift) aus.

   ![Screenshot: Bereich „Einmaliges Anmelden (SSO) mit SAML einrichten“](common/edit-urls.png)

1. Gehen Sie im Abschnitt **Grundlegende SAML-Konfiguration** wie folgt vor, um die Anwendung im IdP-initiierten Modus zu konfigurieren:

    a. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein: `urn:amazon:cognito:sp:<REGION>_<USERPOOLID>`.

    b. Geben Sie im Feld **Antwort-URL** eine URL im folgenden Format ein: `https://<CLIENT>.auth.siteintel.com/saml2/idpresponse`.

    c. Geben Sie im Feld **Relayzustand** eine URL im folgenden Format ein: `https://<CLIENT>.siteintel.com`.

1. Wenn Sie die Anwendung im SP-initiierten Modus konfigurieren möchten, wählen Sie **Zusätzliche URLs festlegen** aus, und gehen Sie dann wie folgt vor:

   * Geben Sie im Feld **Anmelde-URL** eine URL im folgenden Format ein: `https://<CLIENT>.siteintel.com`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL, der Anmelde-URL und dem Relayzustand. Diese Werte erhalten Sie vom [Supportteam für den SiteIntel-Client](mailto:support@intalytics.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die Schaltfläche **Kopieren** aus, um die URL im Feld **App-Verbundmetadaten-URL** zu kopieren.

    ![Screenshot: Schaltfläche „Kopieren“ neben „App-Verbundmetadaten-URL“](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer namens B.Simon.

1. Wählen Sie im Azure-Portal im linken Bereich **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie im oberen Bereich die Option **Neuer Benutzer** aus.
1. Gehen Sie in den Eigenschaften für **Benutzer** wie folgt vor:

   a. Geben Sie im Feld **Name** den Namen **B.Simon** ein.  

   b. Geben Sie im Feld **Benutzername** den Benutzernamen im folgenden Format ein: `username@companydomain.extension` (z. B. `B.Simon@contoso.com`).

   c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert aus dem Feld **Kennwort**.

   d. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf SiteIntel gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Liste mit den **Anwendungen** den Eintrag **SiteIntel** aus.
1. Wählen Sie auf der Übersichtsseite der App im Abschnitt **Verwalten** die Option **Benutzer und Gruppen** aus.

   ![Screenshot: Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Bereich **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Screenshot: Schaltfläche „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Bereich **Benutzer und Gruppen** den Eintrag **B.Simon** und anschließend im unteren Bildschirmbereich die Schaltfläche **Auswählen** aus.
1. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Wählen Sie anschließend die Schaltfläche **Auswählen** aus.
1. Wählen Sie im Bereich **Zuweisung hinzufügen** die Schaltfläche **Zuweisen** aus.

## <a name="configure-siteintel-sso"></a>Konfigurieren des einmaligen Anmeldens für SiteIntel

Senden Sie zum Konfigurieren des einmaligen Anmeldens aufseiten von SiteIntel die URL, die Sie im Feld **App-Verbundmetadaten-URL** kopiert haben, an das [SiteIntel-Supportteam](mailto:support@intalytics.com). Es legt diesen Wert fest, um die SAML-SSO-Verbindung auf beiden Seiten richtig einzurichten.

### <a name="create-a-siteintel-test-user"></a>Erstellen eines SiteIntel-Testbenutzers

In diesem Abschnitt erstellen Sie in SiteIntel einen Benutzer namens *Britta Simon*. Wenden Sie sich zum Hinzufügen der Benutzer auf der SiteIntel-Plattform an das [SiteIntel-Supportteam](mailto:support@intalytics.com). Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich die Kachel **SiteIntel** auswählen, sollten Sie automatisch bei der SiteIntel-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)
- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)
- [SiteIntel mit Azure AD ausprobieren](https://aad.portal.azure.com/)
- [Was ist Sitzungssteuerung in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
- [Schützen von Apps mit der App-Steuerung für bedingten Zugriff von Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)