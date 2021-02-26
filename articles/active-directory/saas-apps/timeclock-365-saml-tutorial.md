---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Timeclock 365 SAML | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Timeclock 365 SAML konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/28/2021
ms.author: jeedes
ms.openlocfilehash: d0c8364cc85cfce900021272d17456527919122b
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050803"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timeclock-365-saml"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Timeclock 365 SAML

In diesem Tutorial erfahren Sie, wie Sie Timeclock 365 SAML in Azure Active Directory (Azure AD) integrieren. Die Integration von Timeclock 365 SAML in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Timeclock 365 SAML hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Timeclock 365 SAML anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Timeclock 365 SAML-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Timeclock 365 SAML unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="adding-timeclock-365-saml-from-the-gallery"></a>Hinzufügen von Timeclock 365 SAML aus dem Katalog

Zum Konfigurieren der Integration von Timeclock 365 SAML in Azure AD müssen Sie Timeclock 365 SAML aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Timeclock 365 SAML** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Timeclock 365 SAML** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-timeclock-365-saml"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Timeclock 365 SAML

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Timeclock 365 SAML mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Timeclock 365 SAML eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Timeclock 365 SAML die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Timeclock 365 SAML](#configure-timeclock-365-saml-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Timeclock 365 SAML-Testbenutzers](#create-timeclock-365-saml-test-user)** , um eine Entsprechung von B. Simon in Timeclock 365 SAML zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Timeclock 365 SAML** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    Geben Sie im Textfeld **Anmelde-URL** die URL ein: `https://live.timeclock365.com/login`.


1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Timeclock 365 SAML gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **Timeclock 365 SAML** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-timeclock-365-saml-sso"></a>Konfigurieren des einmaligen Anmeldens für Timeclock 365 SAML

1. Wenn Sie die Konfiguration in Timeclock 365 SAML automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Timeclock 365 SAML einrichten**, um zur Anwendung Timeclock 365 SAML weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Timeclock 365 SAML anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 und 4.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie Timeclock 365 SAML manuell einrichten möchten, melden Sie sich in einem anderen Webbrowserfenster als Administrator bei der Timeclock 365 SAML-Unternehmenswebsite an.

1. Führen Sie die folgenden Schritte aus:

    ![Timeclock-Konfiguration](./media/timeclock-365-saml-tutorial/saml-configuration.png)

    a. Wechseln Sie zur Registerkarte **Settings > Company profile > Settings** (Einstellungen > Unternehmensprofil > Einstellungen).

    b. Fügen Sie unter **IDP metadata path** (IDP-Metadatenpfad) die **App-Verbundmetadaten-URL** ein, die Sie aus dem Azure-Portal kopiert haben.

    c. Klicken Sie dann auf **Aktualisieren**.

### <a name="create-timeclock-365-saml-test-user"></a>Erstellen eines Timeclock 365 SAM-Testbenutzers

1. Öffnen Sie eine neue Registerkarte in Ihrem Browser, und melden Sie sich bei Ihrer Timeclock 365 SAML-Unternehmenswebsite als Administrator an.

1. Navigieren Sie zu **Users > Add new user** (Benutzer > Neuen Benutzer hinzufügen).

    ![Erstellen eines Testbenutzers 1 ](./media/timeclock-365-saml-tutorial/add-user-1.png)

1. Geben Sie alle erforderlichen Informationen auf der Seite **User information** (Benutzerinformationen) ein, und klicken Sie auf **Save** (Speichern).

    ![Erstellen eines Testbenutzers 2 ](./media/timeclock-365-saml-tutorial/add-user-2.png)

1. Klicken Sie auf die Schaltfläche **Create** (Erstellen), um den Testbenutzer zu erstellen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Timeclock 365 SAML weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die Timeclock 365 SAML-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“auf die Kachel „Timeclock 365 SAML“ klicken, werden Sie zur Anmelde-URL für Timeclock 365 SAML weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Timeclock 365 SAML können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.
