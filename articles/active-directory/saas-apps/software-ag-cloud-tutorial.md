---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Software AG Cloud | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Software AG Cloud konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/20/2020
ms.author: jeedes
ms.openlocfilehash: 4acc4c0cec530b8f83648042cd7a417992257543
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602018"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-software-ag-cloud"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Software AG Cloud

In diesem Tutorial erfahren Sie, wie Sie Software AG Cloud in Azure Active Directory (Azure AD) integrieren. Die Integration von Software AG Cloud in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Software AG Cloud hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Software AG Cloud anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Software AG Cloud-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Software AG Cloud unterstützt **SP**-initiiertes einmaliges Anmelden.
* Software AG Cloud unterstützt die **Just-In-Time**-Bereitstellung von Benutzern.

## <a name="adding-software-ag-cloud-from-the-gallery"></a>Hinzufügen von Software AG Cloud aus dem Katalog

Zum Konfigurieren der Integration von Software AG Cloud in Azure AD müssen Sie Software AG Cloud aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Software AG Cloud** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Software AG Cloud** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-software-ag-cloud"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Software AG Cloud

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Software AG Cloud mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Software AG Cloud eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Software AG Cloud die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Software AG Cloud](#configure-software-ag-cloud-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Software AG Cloud-Testbenutzers](#create-software-ag-cloud-test-user)** , um eine Entsprechung von B. Simon in Software AG Cloud zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Software AG Cloud** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://*.softwareag.cloud/auth/realms/TENANT-NAME/broker/IDENTITY-PROVIDER-NAME/endpoint`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://*.softwareag.cloud/auth/realms/TENANT-NAME`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Diese Werte erhalten Sie vom [Supportteam für den Software AG Cloud-Client](mailto:support@softwareag.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Software AG Cloud einrichten** die entsprechenden URLs für Ihre Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)
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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Software AG Cloud gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Software AG Cloud** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-software-ag-cloud-sso"></a>Konfigurieren des einmaligen Anmeldens für Software AG Cloud

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Konfigurieren des einmaligen Anmeldens für Software AG Cloud-Website als Administrator an.

1.  Klicken Sie auf **Administration** (Verwaltung).

    ![Konfigurieren der Software AG Cloud: Administration](./media/software-ag-cloud-tutorial/admin.png)

1. Navigieren Sie zu **Single-sign on > Add identity provider** (Einmaliges Anmelden > Identitätsanbieter hinzufügen).

    ![Konfigurieren von Software AG Cloud: Identitätsanbieter](./media/software-ag-cloud-tutorial/add-identity-provider.png)

1. Führen Sie auf der folgenden Seite die folgenden Schritte aus:

    ![Konfigurieren von Software AG Cloud: Folgeschritte](./media/software-ag-cloud-tutorial/saml-1.png)

    a. Geben Sie in das Textfeld **Identity provider display name** (Anzeigename des Identitätsanbieters) einen gültigen Namen ein, etwa `azure ad`.

    b. Geben Sie im Textfeld **Identity provider unique identifier for use in Software AG Cloud redirect URI** (Eindeutiger Bezeichner des Identitätsanbieters zur Verwendung im Software AG Cloud-Umleitungs-URI) einen eindeutigen Namen für den Identitätsanbieter ein. Das Feld für den **Software AG Cloud-Umleitungs-URI** wird aktualisiert und mit dem URI aufgefüllt. Kopieren Sie diesen URI, und verwenden Sie ihn, um die **Entitäts-ID** und andere Informationen im Azure-Portal gemäß den definierten Mustern zu konfigurieren.

    c. Importieren Sie unter **Identity provider configuration** (Identitätsanbieterkonfiguration) die **Verbundmetadaten-XML-Datei**, und klicken Sie auf **Weiter**.

    d. Navigieren Sie zur Seite **Conﬁguration** (Konfiguration), und füllen Sie die Felder entsprechend aus.

### <a name="create-software-ag-cloud-test-user"></a>Erstellen eines Software AG Cloud-Testbenutzers

In diesem Abschnitt wird in Software AG Cloud ein Benutzer mit dem Namen Britta Simon erstellt. Software AG Cloud unterstützt die Just-In-Time-Benutzerbereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in Software AG Cloud vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Wenn Microsoft Azure in Software AG Cloud als Anbieter konfiguriert ist, können Sie zu `www.softwareag.cloud` navigieren, auf die Anmeldeschaltfläche klicken und den Umgebungsnamen eingeben. Klicken Sie auf dem nächsten Bildschirm auf den Link „Mit <IDP NAME> anmelden“, und geben Sie die Anmeldeinformationen ein. Nachdem Sie sich authentifiziert haben, werden Sie angemeldet, und die Homepage von Software AG Cloud wird angezeigt.

* Rufen Sie direkt die Software AG Cloud-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Software AG Cloud“ klicken, werden Sie zur Anmelde-URL für Software AG Cloud umgeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Software AG Cloud können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.