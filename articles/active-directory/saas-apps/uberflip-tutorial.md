---
title: 'Tutorial: Azure Active Directory-Integration in Uberflip | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Uberflip konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 6bf3eccec70619bf73397e315c6d851f753ec52f
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521831"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>Tutorial: Azure Active Directory-Integration in Uberflip

In diesem Tutorial erfahren Sie, wie Sie Uberflip in Azure Active Directory (Azure AD) integrieren.

Die Integration von Uberflip in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Uberflip hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Uberflip anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten an einem zentralen Ort verwalten: im Azure-Portal.

Details zur Integration von SaaS-Apps (Software-as-a-Service) in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in Uberflip zu konfigurieren, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Ein Uberflip-Abonnement, das für das einmaliges Anmelden aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

Uberflip unterstützt die folgenden Features:

* SP- und IDP-initiiertes einmaliges Anmelden (SSO).
* Just-In-Time-Benutzerbereitstellung.

## <a name="add-uberflip-from-the-azure-marketplace"></a>Hinzufügen von Uberflip über den Azure Marketplace

Zum Konfigurieren der Integration von Uberflip in Azure AD müssen Sie Uberflip aus dem Azure Marketplace zur Liste der verwalteten SaaS-Apps hinzufügen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Bereich **Azure Active Directory** aus.

   ![Die Option „Azure Active Directory“](common/select-azuread.png)

1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

   ![Bereich „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie oben im Fenster **+Neue Anwendung** aus, um eine neue Anwendung hinzuzufügen.

   ![Die Option „Neue Anwendung“](common/add-new-app.png)

1. Geben Sie im Suchfeld **Uberflip** ein. Wählen Sie in den Suchergebnissen **Uberflip** aus, und wählen Sie dann **Hinzufügen** aus, um die Anwendung hinzuzufügen.

   ![Uberflip in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Uberflip mithilfe eines Testbenutzers namens **B Simon**. Damit einmaliges Anmelden funktioniert, muss eine Verknüpfung zwischen einem Azure AD-Benutzer und einem entsprechenden Benutzer in Uberflip eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Uberflip müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Uberflip](#configure-uberflip-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Erstellen eines Uberflip-Testbenutzers](#create-an-uberflip-test-user)** , damit in Uberflip ein Benutzer namens B. Simon vorhanden ist, der mit dem Azure AD-Benutzer namens B. Simon verknüpft ist.
1. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Uberflip die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Uberflip** die Option **Einmaliges Anmelden** aus.

    ![Option „Einmaliges Anmelden konfigurieren“](common/select-sso.png)

1. Wählen Sie im Bereich **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

1. Wählen Sie im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** die Option **Bearbeiten** (Stiftsymbol), um den Bereich **Grundlegende SAML-Konfiguration** zu öffnen.

   ![Screenshot: „Grundlegende SAML-Konfiguration“ zum Eingeben einer Antwort-URL](common/edit-urls.png)

1. Führen Sie im Bereich **Grundlegende SAML-Konfiguration** einen der folgenden Schritte aus, je nachdem, welchen SSO-Modus Sie konfigurieren möchten:

   * Zum Konfigurieren der Anwendung im IDP-initiierten SSO-Modus geben Sie im Feld **Antwort-URL (Assertionsverbraucherdienst-URL)** eine URL im folgenden Muster ein:

     `https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>`

     ![SSO-Informationen zur Domäne und zu den URLs für Uberflip](common/both-replyurl.png)

     > [!NOTE]
     > Dieser Wert entspricht nicht dem tatsächlichen Wert. Aktualisieren Sie den Wert mit der richtigen Antwort-URL. Erfragen Sie die tatsächlichen Werte beim [Supportteam von Uberflip](mailto:support@uberflip.com). Sie können sich auch die Muster im Bereich **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

   * Wählen Sie zum Konfigurieren der Anwendung im SP-initiierten SSO-Modus die Option **Zusätzliche URLs festlegen** aus, und geben Sie im Feld **Anmelde-URL** diese URL ein:

     `https://app.uberflip.com/users/login`

     ![Screenshot: Option „Zusätzliche URLs festlegen“ zum Eingeben einer Anmelde-URL](common/both-signonurl.png)

1. Klicken Sie im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um die **Verbundmetadaten-XML** aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

   ![Downloadoption für Verbundmetadaten-XML](common/metadataxml.png)

1. Kopieren Sie im Bereich **Uberflip einrichten** die URL(s), die Sie benötigen:

   * **Anmelde-URL**
   * **Azure AD-Bezeichner**
   * **Abmelde-URL**

   ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="configure-uberflip-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Uberflip

Zum Konfigurieren des einmaligen Anmeldens aufseiten von Uberflip müssen Sie die heruntergeladene Verbundmetadaten-XML und die kopierten URLs aus dem Azure-Portal an das [Supportteam von Uberflip](mailto:support@uberflip.com) senden. Das Uberflip-Team sorgt dafür, dass die SAML-Verbindung für einmaliges Anmelden auf beiden Seiten ordnungsgemäß eingerichtet wird.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.

    ![Die Optionen „Benutzer“ und „Alle Benutzer“](common/users.png)

1. Wählen Sie oben im Bildschirm die Option **+ Neuer Benutzer** aus.

    ![Option „Neuer Benutzer“](common/new-user.png)

1. Führen Sie im Bereich **Benutzer** folgende Schritte aus:

    ![Der Bereich „Benutzer“](common/user-properties.png)

    1. Geben Sie in das Feld **Name** den Namen **BSimon** ein.
  
    1. Geben Sie in das Feld **Benutzername** den Namen **BSimon\@\<yourcompanydomain>.\<extension>** ein. Beispiel: **BSimon\@contoso.com**

    1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert aus dem Feld **Kennwort**.

    1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihm Zugriff auf Uberflip gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Uberflip** aus.

    ![Bereich für Unternehmensanwendungen](common/enterprise-applications.png)

1. Wählen Sie in der Anwendungsliste **Uberflip** aus.

    ![Uberflip in der Anwendungsliste](common/all-applications.png)

1. Wählen Sie im linken Bereich unter **VERWALTEN** die Option **Benutzer und Gruppen** aus.

    ![Option „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie die Schaltfläche **+ Benutzer hinzufügen** aus, und wählen Sie anschließend im Bereich **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Bereich **Benutzer und Gruppen** in der Liste **Benutzer** den Eintrag **B Simon** und anschließend unten im Bereich **Auswählen** aus.

1. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Bereich **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Wählen Sie unten im Bereich die Option **Auswählen**.

1. Wählen Sie im Bereich **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="create-an-uberflip-test-user"></a>Erstellen eines Uberflip-Testbenutzers

Ein Benutzer namens B. Simon wird jetzt in Uberflip erstellt. Sie müssen nichts tun, um diesen Benutzer erstellen. Uberflip unterstützt die Just-in-Time-Benutzerbereitstellung (standardmäßig aktiviert). Ist noch kein Benutzer namens B. Simon in Uberflip vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

> [!NOTE]
> Setzen Sie sich mit dem [Supportteam von Uberflip](mailto:support@uberflip.com) in Verbindung, wenn Sie einen Benutzer manuell erstellen müssen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Portals „Meine Apps“.

Bei der Auswahl von **Uberflip** im Portal „Meine Apps“ sollten Sie automatisch beim Uberflip-Abonnement angemeldet werden, für das Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Portal „Meine Apps“ finden Sie unter [Zugreifen auf und Verwenden von Apps im Portal „Meine Apps“](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Anwendungen in Azure Active Directory](./tutorial-list.md)

* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

* [Was ist bedingter Zugriff?](../conditional-access/overview.md)