---
title: 'Tutorial: Azure Active Directory-Integration mit Saba TalentSpace | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Saba TalentSpace konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: 6da04bda9e9d1a00ff10cc94d3dcef557a3f2802
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99062952"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-talentspace"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Saba TalentSpace

In diesem Tutorial erfahren Sie, wie Sie Saba TalentSpace in Azure Active Directory (Azure AD) integrieren. Die Integration von Saba TalentSpace in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Saba TalentSpace hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Saba TalentSpace anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Saba TalentSpace-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Saba TalentSpace unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="add-saba-talentspace-from-the-gallery"></a>Hinzufügen von Saba TalentSpace aus dem Katalog

Zum Konfigurieren der Integration von Saba TalentSpace in Azure AD müssen Sie Saba TalentSpace aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Saba TalentSpace** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Saba TalentSpace** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-saba-talentspace"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Saba TalentSpace

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Saba TalentSpace mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Saba TalentSpace eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Saba TalentSpace die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Saba TalentSpace](#configure-saba-talentspace-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * **[Erstellen eines Saba TalentSpace-Testbenutzers](#create-saba-talentspace-test-user)** , um ein Pendant von B. Simon in Saba TalentSpace zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Saba TalentSpace** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Anmelde-URL** die URL im folgenden Format ein: `https://global.hgncloud.com/[companyname]/saml/login`

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://global.hgncloud.com/[companyname]/saml/metadata`.

    c. Geben Sie im Textfeld **Antwort-URL (Assertionsverbraucherdienst-URL)** eine URL im folgenden Format ein: `https://global.hgncloud.com/[companyname]/saml/SSO`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Diese Werte erhalten Sie vom [Supportteam für den Saba TalentSpace-Client](https://support.saba.com/). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Saba TalentSpace einrichten** die entsprechenden URLs basierend auf Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Saba TalentSpace gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Saba TalentSpace** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-saba-talentspace-sso"></a>Konfigurieren des einmaligen Anmeldens für Saba TalentSpace

1. Melden Sie sich in einem anderen Browserfenster bei der **Saba TalentSpace**-Anwendung als Administrator an.

2. Klicken Sie auf die Registerkarte **Options** .
  
    ![Screenshot: Startseite von „saba TalentSpace“ mit der ausgewählten Registerkarte „Optionen“](./media/halogen-software-tutorial/tutorial-halogen-12.png)

3. Klicken Sie im linken Navigationsbereich auf **SAML-Konfiguration**.
  
    ![Screenshot: Linker Navigationsbereich „User Interface“ (Benutzeroberfläche), in dem „SAML Configuration“ (SAML-Konfiguration) ausgewählt ist](./media/halogen-software-tutorial/tutorial-halogen-13.png)

4. Führen Sie auf der Seite **SAML-Konfiguration** die folgenden Schritte aus:

    ![Screenshot: Seite „SAML Configuration“ (SAML-Konfiguration), auf der Optionen unter „Einstellungen“ hervorgehoben sind](./media/halogen-software-tutorial/tutorial-halogen-14.png)

    a. Wählen Sie als **Eindeutigen Bezeichner** die Option **NameID** aus.

    b. Wählen Sie für **Eindeutiger Bezeichner ist zugeordnet** die Option **Benutzername** aus.
  
    c. Klicken Sie zum Hochladen der heruntergeladenen Metadatendatei auf **Durchsuchen**, um die Datei auszuwählen, und dann auf **Datei hochladen**.

    d. Klicken Sie zum Testen der Konfiguration auf **Durchführen des Tests**.

    > [!NOTE]
    > Sie müssen warten, bis die Meldung "*Der SAML-Test ist abgeschlossen. Schließen Sie dieses Fenster*" erscheint. Schließen Sie dann das geöffnete Browserfenster. Das Kontrollkästchen **SAML aktivieren** ist nur aktiviert, wenn der Test abgeschlossen wurde.

    e. Wählen Sie **SAML aktivieren**.

    f. Klicken Sie auf **Änderungen speichern**.

### <a name="create-saba-talentspace-test-user"></a>Erstellen eines Saba TalentSpace-Testbenutzers

In diesem Abschnitt wird in Saba TalentSpace ein Benutzer namens Britta Simon erstellt.

**Führen Sie die folgenden Schritte aus, um einen Benutzer namens Britta Simon in Saba TalentSpace zu erstellen:**

1. Melden Sie sich bei Ihrer **Saba TalentSpace**-Anwendung als Administrator an.

2. Klicken Sie auf die Registerkarte **Benutzercenter** und dann auf **Benutzer erstellen**.

    ![Screenshot: Ausgewählte Registerkarte „User Center“ (Benutzercenter) und ausgewählte Option „Create User“ (Benutzer erstellen)](./media/halogen-software-tutorial/tutorial-halogen-300.png)  

3. Führen Sie auf der Dialogfeldseite **Neuer Benutzer** die folgenden Schritte aus:

    ![Was ist Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-301.png)

    a. Geben Sie im Textfeld **First Name** (Vorname) den Vornamen des Benutzers ein, z. B. **B**.

    b. Geben Sie im Textfeld **Nachname** den Nachnamen des Benutzers wie folgt ein: **Simon**.

    c. Geben Sie im Textfeld **Username** (Benutzername) den Benutzernamen **B. Simon** wie im Azure-Portal ein.

    d. Geben Sie im Textfeld **Password** (Kennwort) ein Kennwort für B. Simon ein.

    e. Klicken Sie auf **Speichern**.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Saba TalentSpace weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die Saba TalentSpace-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Saba TalentSpace“ klicken, sollten Sie automatisch bei der Saba TalentSpace-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nächste Schritte

 Nach dem Konfigurieren von Saba TalentSpace können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.