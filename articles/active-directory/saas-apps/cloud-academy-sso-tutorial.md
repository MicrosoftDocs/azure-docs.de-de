---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Cloud Academy - SSO'
description: In diesem Tutorial erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Cloud Academy - SSO konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: jeedes
ms.openlocfilehash: 822e28402d0b7829b835ad03a3b3cf7d05c3d343
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180998"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-cloud-academy---sso"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Cloud Academy - SSO

In diesem Tutorial erfahren Sie, wie Sie Cloud Academy - SSO in Azure Active Directory (Azure AD) integrieren. Die Integration von Cloud Academy - SSO in Azure AD ermöglicht Folgendes:

* Steuern Sie mithilfe von Azure AD, wer auf Cloud Academy - SSO zugreifen kann.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Cloud Academy - SSO anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Worum handelt es sich beim einmaligen Anmelden (Single Sign-On, SSO)?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Abonnement für Cloud Academy - SSO, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="tutorial-description"></a>Beschreibung des Tutorials

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

Cloud Academy - SSO unterstützt SP-initiiertes einmaliges Anmelden.

Nach dem Konfigurieren von Cloud Academy - SSO können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Sitzungssteuerungen basieren auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.

## <a name="add-cloud-academy---sso-from-the-gallery"></a>Hinzufügen von Cloud Academy - SSO aus dem Katalog

Zum Konfigurieren der Integration von Cloud Academy - SSO in Azure AD müssen Sie Cloud Academy - SSO aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen:

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Bereich **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Cloud Academy - SSO** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Cloud Academy - SSO** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-cloud-academy---sso"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Cloud Academy - SSO

Sie konfigurieren und testen das einmalige Anmelden von Azure AD mit Cloud Academy - SSO mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Cloud Academy - SSO eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Cloud Academy - SSO diese allgemeinen Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD zu testen.
    1. **[Gewähren der Zugriffsberechtigungen für den Testbenutzer](#grant-access-to-the-test-user)** , um dem Benutzer die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Cloud Academy - SSO](#configure-single-sign-on-for-cloud-academy)** auf der Anwendungsseite
    1. **[Erstellen eines Testbenutzers für Cloud Academy - SSO](#create-a-cloud-academy-test-user)** als Pendant zur Azure AD-Darstellung des Benutzers
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert.

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Cloud Academy - SSO** im Abschnitt **Verwalten** die Option **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol für **Grundlegende SAML-Konfiguration** aus, um die Einstellungen zu bearbeiten:

   ![Screenshot: Stiftsymbol zum Bearbeiten der grundlegenden SAML-Konfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** im Feld **Anmelde-URL** die URL `https://cloudacademy.com/login/enterprise/` ein.

1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die Kopierschaltfläche aus, um die **App-Verbundmetadaten-URL** zu kopieren. Speichern Sie die URL.

    ![Screenshot: Schaltfläche „Kopieren“ für die App-Verbundmetadaten-URL](common/copy-metadataurl.png)

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Cloud Academy - SSO gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Cloud Academy - SSO** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus:

   ![Screenshot: Option „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie die Schaltfläche **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus:

    ![Screenshot: Schaltfläche „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste **Benutzer** den Eintrag **B. Simon** aus, und klicken Sie dann am unteren Bildschirmrand auf die Schaltfläche **Auswählen**.
1. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Klicken Sie am unteren Bildschirmrand auf die Schaltfläche **Auswählen**.
1. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

## <a name="configure-single-sign-on-for-cloud-academy"></a>Konfigurieren des einmaligen Anmeldens für Cloud Academy

1. Melden Sie sich in einem anderen Browserfenster bei der Unternehmenswebsite von Cloud Academy - SSO als Administrator an.

1. Wählen Sie den Namen Ihres Unternehmens und anschließend im angezeigten Menü **Settings & Integrations** (Einstellungen und Integrationen) aus:

    ![Screenshot: Option „Settings & Integrations“ (Einstellungen und Integrationen)](./media/cloud-academy-sso-tutorial/config-1.PNG)

1. Wählen Sie auf der Seite **Settings & Integrations** (Einstellungen und Integrationen) auf der Registerkarte **Integrations** (Integrationen) die Karte **SSO** aus:

    ![Screenshot: Karte „SSO“ auf der Registerkarte „Integrations“ (Integrationen)](./media/cloud-academy-sso-tutorial/config-2.PNG)

1. Führen Sie auf dieser Seite die folgenden Schritte aus:

    ![Screenshot: Seite „Integrations“ (Integrationen) > SSO](./media/cloud-academy-sso-tutorial/config-3.PNG)

    a. Geben Sie in das Feld **Entity ID URL** (Entitäts-ID-URL) den Wert der Entitäts-ID ein, den Sie aus dem Azure-Portal kopiert haben.

    b. Fügen Sie im Feld **SSO URL** (SSO-URL) den Wert der Anmelde-URL ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Öffnen Sie das aus dem Azure-Portal heruntergeladene Base64-Zertifikat in Editor. Fügen Sie den Inhalt in das Feld **Certificate** (Zertifikat) ein.

    d. Übernehmen Sie im Feld **Name ID Format** (Format der Namens-ID) den Standardwert `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`.

1. Wählen Sie **Speichern** aus.

    > [!NOTE]
    > Weitere Informationen zum Konfigurieren von Cloud Academy - SSO finden Sie im Artikel zum [Einrichten des einmaligen Anmeldens](https://support.cloudacademy.com/hc/articles/360043908452-Setting-Up-Single-Sign-On).

### <a name="create-a-cloud-academy-test-user"></a>Erstellen eines Cloud Academy-Testbenutzers

1. Melden Sie sich bei Cloud Academy - SSO an.

1. Wählen Sie den Namen Ihres Unternehmens und anschließend im angezeigten Menü **Members** (Mitglieder) aus:

    ![Screenshot: Option „Members“ (Mitglieder)](./media/cloud-academy-sso-tutorial/create-user.PNG)

1. Wählen Sie **Invite Members** (Mitglieder einladen) und dann **Invite a Single Member** (Einzelnes Mitglied einladen) aus:

    ![Screenshot: Option „Invite a single Member“ (Einzelnes Mitglied einladen)](./media/cloud-academy-sso-tutorial/create-user-1.PNG)

1. Geben Sie Werte in die erforderlichen Felder ein, und wählen Sie dann **Invite** (Einladen) aus:

    ![Screenshot: Dialogfeld zum Einladen eines Mitglieds](./media/cloud-academy-sso-tutorial/create-user-2.PNG)

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

Testen Sie die Azure AD-Konfiguration für einmaliges Anmelden (SSO) über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich die Kachel „Cloud Academy - SSO“ auswählen, sollten Sie automatisch bei der Instanz von Cloud Academy - SSO angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)

- [Cloud Academy - SSO mit Azure AD ausprobieren](https://aad.portal.azure.com/)

- [Was ist Sitzungssteuerung in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Schützen von Apps mit der App-Steuerung für bedingten Zugriff von Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)