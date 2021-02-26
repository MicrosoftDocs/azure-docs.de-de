---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Tableau Server | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden für Azure Active Directory und Tableau Server konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 7ee22121fd023f3942eed64bd605bf87b961d893
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99429830"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Tableau Server

In diesem Tutorial erfahren Sie, wie Sie Tableau Server in Azure Active Directory (Azure AD) integrieren. Die Integration von Tableau Server in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Tableau Server hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Tableau Server anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.


## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Tableau Server-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Tableau Server unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="add-tableau-server-from-the-gallery"></a>Hinzufügen von Tableau Server aus dem Katalog

Zum Konfigurieren der Integration von Tableau Server in Azure AD müssen Sie Tableau Server aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Tableau Server** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Tableau Server** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-tableau-server"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Tableau Server

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Tableau Server mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Tableau Server eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Tableau Server die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Tableau Server](#configure-tableau-server-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Tableau Server-Testbenutzers](#create-tableau-server-test-user)** , um eine Entsprechung von B. Simon in Tableau Server zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Tableau Server** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://azure.<domain name>.link`

    b. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein: `https://azure.<domain name>.link`.

    c. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Die vorangehenden Werte sind keine echten Werte. Aktualisieren Sie die Werte mit der tatsächlichen Anmelde-URL, dem Bezeichner und der Antwort-URL von der Seite „Tableau Server Configuration“ (Tableau Server-Konfiguration). Darauf wird später im Tutorial eingegangen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Tableau Server einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Tableau Server gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Tableau Server**.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-tableau-server-sso"></a>Konfigurieren des einmaligen Anmeldens für Tableau Server

1. Zum Konfigurieren des einmaligen Anmeldens für Ihre Anwendung müssen Sie sich als Administrator bei Ihrem Tableau Server-Mandanten anmelden.

2. Wählen Sie auf der Registerkarte **CONFIGURATION (KONFIGURATION)** **User Identity & Access (Benutzeridentität und Zugriff)** aus, und wählen Sie dann die Registerkarte **Authentication Method (Authentifizierungsmethode)** aus.

    ![Screenshot, auf dem unter „User Identity & Access“ (Benutzeridentität und Zugriff) die Option „Authentication“ (Authentifizierung) ausgewählt ist](./media/tableauserver-tutorial/auth.png)

3. Führen Sie auf der Seite **CONFIGURATION (KONFIGURATION)** die folgenden Schritte aus:

    ![Screenshot der Seite „Configuration“ (Konfiguration), auf der Sie die beschriebenen Werte eingeben können.](./media/tableauserver-tutorial/config.png)

    a. Wählen Sie als **Authentication Method (Authentifizierungsmethode)** die Option „SAML“ aus.

    b. Aktivieren Sie das Kontrollkästchen **Enable SAML Authentication for the server (SAML-Authentifizierung für den Server aktivieren)** .

    c. „Tableau Server return URL“: Die URL, auf die Tableau Server-Benutzer zugreifen, z. B. `http://tableau_server`. Ein Verwenden von `http://localhost` ist nicht zu empfehlen. Die Verwendung einer URL mit einem nachstehenden Schrägstrich (z.B. `http://tableau_server/`) wird nicht unterstützt. Kopieren Sie den Wert im Feld **Tableau Server return URL** (Tableau Server-Rückgabe-URL), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** ins Textfeld **Anmelde-URL** ein.

    d. „SAML entity ID“: Die Entitäts-ID zur eindeutigen Identifizierung Ihrer Tableau Server-Installation durch den IdP. Sie können in dieses Feld erneut Ihre Tableau Server-URL eingeben, es muss jedoch nicht die Tableau Server-URL verwendet werden. Kopieren Sie den Wert im Feld **SAML entity ID** (SAML-Entitäts-ID), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** ins Textfeld **Bezeichner** ein.

    e. Klicken Sie auf **Download XML Metadata File (XML-Metadatendatei herunterladen)** , und öffnen Sie die Datei im Text-Editor. Suchen Sie nach „Assertion Consumer Service URL“ mit HTTP Post und Index 0, und kopieren Sie die URL. Fügen Sie sie im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** ins Textfeld **Antwort-URL** ein.

    f. Suchen Sie nach der Datei mit Ihren Verbundmetadaten, die Sie aus dem Azure-Portal heruntergeladen haben, und laden Sie sie in die **SAML Idp metadata file** hoch.

    g. Geben Sie die Namen für die Attribute ein, die der IdP verwendet, um Benutzernamen, Anzeigenamen und E-Mail-Adressen aufzunehmen.

    h. Klicken Sie auf **Speichern**.

    > [!NOTE]
    > Der Kunde muss eine PEM-codierte x.509-Zertifikatdatei mit der Erweiterung CRT und eine Private RSA- oder DSA-Schlüsseldatei mit der Erweiterung KEY als Zertifikatschlüsseldatei hochladen. Weitere Informationen zur Zertifikatdatei und zur Zertifikatschlüsseldatei finden Sie in [diesem](https://help.tableau.com/current/server/en-us/saml_requ.htm) Dokument. Wenn Sie Hilfe bei der Konfiguration von SAML für Tableau Server benötigen, finden Sie weitere Informationen im Artikel [Konfigurieren von serverweitem SAML](https://help.tableau.com/current/server/en-us/config_saml.htm).

### <a name="create-tableau-server-test-user"></a>Erstellen eines Tableau Server-Testbenutzers

In diesem Abschnitt wird in Tableau Server ein Benutzer namens B. Simon erstellt. Sie müssen alle Benutzer in Tableau Server bereitstellen.

Der username-Wert des Benutzers muss dem Wert entsprechen, den Sie im benutzerdefinierten Attribut **username** in Azure AD konfiguriert haben. Bei ordnungsgemäßer Zuordnung sollte die Integration funktionieren: Konfigurieren des einmaligen Anmeldens in Azure AD.

> [!NOTE]
> Wenn Sie einen Benutzer manuell erstellen müssen, wenden Sie sich an den Tableau Server-Administrator in Ihrer Organisation.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Tableau Server weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die Tableau Server-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „Tableau Server“ klicken, werden Sie zur Anmelde-URL von Tableau Server weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Tableau Server können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.
