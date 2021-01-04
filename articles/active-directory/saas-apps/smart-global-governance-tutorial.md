---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit Smart Global Governance'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Smart Global Governance konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: jeedes
ms.openlocfilehash: 5a2169db47ca5a6adcddbcc9558161370b896a62
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997068"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-smart-global-governance"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Smart Global Governance

In diesem Tutorial erfahren Sie, wie Sie Smart Global Governance in Azure Active Directory (Azure AD) integrieren. Die Integration von Smart Global Governance in Azure AD ermöglicht Folgendes:

* Verwenden Sie Azure AD, um festzulegen, wer auf Smart Global Governance zugreifen kann.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Smart Global Governance anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Smart Global Governance-Abonnement,das für einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist.

## <a name="tutorial-description"></a>Beschreibung des Tutorials

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

Smart Global Governance unterstützt SP- und IDP-initiiertes einmaliges Anmelden.

Nach dem Konfigurieren von Smart Global Governance können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Sitzungssteuerungen basieren auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.

## <a name="add-smart-global-governance-from-the-gallery"></a>Hinzufügen von Smart Global Governance aus dem Katalog

Zum Konfigurieren der Integration von Smart Global Governance in Azure AD müssen Sie Smart Global Governance aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Bereich **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Smart Global Governance** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Smart Global Governance** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-smart-global-governance"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Smart Global Governance

Konfigurieren und testen Sie einmaliges Anmelden von Azure AD mit Smart Global Governance mithilfe eines Testbenutzers mit dem Namen B.Simon. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Smart Global Governance eingerichtet werden.

Führen Sie diese Schritte aus, um einmaliges Anmelden von Azure AD mit Smart Global Governance zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD zu testen.
    1. **[Gewähren der Zugriffsberechtigungen für den Testbenutzer](#grant-access-to-the-test-user)** , um dem Benutzer die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Smart Global Governance](#configure-smart-global-governance-sso)** auf Anwendungsseite.
    1. **[Erstellen eines Smart Global Governance-Testbenutzers](#create-a-smart-global-governance-test-user)** als Pendant zur Azure AD-Darstellung des Benutzers.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert.

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Smart Global Governance** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol für **Grundlegende SAML-Konfiguration** aus, um die Einstellungen zu bearbeiten:

   ![Schaltfläche mit dem Bleistift für die grundlegende SAML-Konfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im IDP-initiierten Modus konfigurieren möchten.

    a. Geben Sie im Feld **Bezeichner** eine der folgenden URLs ein:

    - `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/metadata`
    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/metadata`

    b. Geben Sie im Feld **Antwort-URL** eine der folgenden URLs ein:

    - `https://eu-fr-south.console.smartglobalprivacy.com/platform/authentication-saml2/acs`
    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo/authentication-saml2/acs`

1. Wenn Sie die Anwendung im SP-initiierten Modus konfigurieren möchten, klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus.

   - Geben Sie im Feld **Anmelde-URL** eine der folgenden URLs ein:

    - `https://eu-fr-south.console.smartglobalprivacy.com/dpo`
    - `https://eu-fr-south.console.smartglobalprivacy.com/platform`

1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** den Link **Download** für **Zertifikat (Rohdaten)** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer:

    ![Downloadlink für Zertifikat](common/certificateraw.png)

1. Kopieren Sie im Abschnitt **Smart Global Governance einrichten** die entsprechenden URLs gemäß Ihren Anforderungen:

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Azure-Portals den Dienst **Azure Active Directory** aus. Wählen Sie **Benutzer** und dann **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie in den **Benutzereigenschaften** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** den Namen **B.Simon** ein.  
   1. Geben Sie im Feld **Benutzername** Folgendes ein: \<username>@\<companydomain>.\<extension>. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.
   1. Klicken Sie auf **Erstellen**.

### <a name="grant-access-to-the-test-user"></a>Gewähren von Zugriff für den Testbenutzer

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Smart Global Governance gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Smart Global Governance** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus:

   ![Benutzer und Gruppen auswählen](common/users-groups-blade.png)

1. Wählen Sie die Schaltfläche **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus:

    ![Auswählen von „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste **Benutzer** den Eintrag **B. Simon** aus, und klicken Sie dann am unteren Bildschirmrand auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

## <a name="configure-smart-global-governance-sso"></a>Konfigurieren des einmaligen Anmeldens für Smart Global Governance

Zum Konfigurieren des einmaligen Anmeldens aufseiten von Smart Global Governance müssen Sie das heruntergeladene Rohzertifikat und die entsprechenden aus dem Azure-Portal kopierten URLs an das [Supportteam von Smart Global Governance](mailto:support.tech@smartglobal.com) senden. Die SAML-SSO-Verbindung wird so konfiguriert, dass Sie auf beiden Seiten richtig ist.

### <a name="create-a-smart-global-governance-test-user"></a>Erstellen eines Smart Global Governance-Testbenutzers

Wenden Sie sich an das [Smart Global Governance-Supportteam](mailto:support.tech@smartglobal.com), um einen Benutzer namens B.Simon in Smart Global Governance hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden (SSO) über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich die Kachel „Smart Global Governance“ auswählen, sollten Sie automatisch bei der Smart Global Governance-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)

- [Smart Global Governance mit Azure AD ausprobieren](https://aad.portal.azure.com/)

- [Was ist Sitzungssteuerung in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Schützen von Apps mit der App-Steuerung für bedingten Zugriff von Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)