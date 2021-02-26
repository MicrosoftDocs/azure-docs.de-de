---
title: 'Tutorial: Azure Active Directory-Integration mit Asana | Microsoft Docs'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Asana konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: a46396d25519decdd45764acbea485bdd0571782
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99428842"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Tutorial: Azure Active Directory-Integration mit Asana

In diesem Tutorial erfahren Sie, wie Sie Asana in Azure Active Directory (Azure AD) integrieren. Die Integration von Asana in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Asana hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Asana anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Asana-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Asana unterstützt **SP**-initiiertes einmaliges Anmelden.

* Asana unterstützt die [**automatisierte** Benutzerbereitstellung](asana-provisioning-tutorial.md).

## <a name="add-asana-from-the-gallery"></a>Hinzufügen von Asana aus dem Katalog

Zum Konfigurieren der Integration von Asana in Azure AD müssen Sie Asana über den Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Asana** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Asana** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-asana"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Asana

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Asana mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Asana eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Asana die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Asana](#configure-asana-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Asana-Testbenutzers](#create-asana-test-user)** , um in Asana ein Pendant von B. Simon zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Asana** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Asana](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** die URL ein: `https://app.asana.com/`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** die folgende URL ein: `https://app.asana.com/`.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Asana einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Asana gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **Asana** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="configure-asana-sso"></a>Konfigurieren des einmaligen Anmeldens für Asana

1. Melden Sie sich in einem anderen Browserfenster bei Ihrer Asana-Anwendung als Administrator an. Greifen Sie zum Konfigurieren von SSO in Asana auf die Arbeitsbereichseinstellungen zu, indem Sie in der rechten oben Bildschirmecke auf den Namen des Arbeitsbereichs klicken. Klicken Sie dann auf **Einstellungen für \<your workspace name\>** .

    ![Asana-SSO-Einstellungen](./media/asana-tutorial/settings.png)

2. Klicken Sie im Fenster **Organisationseinstellungen** auf **Verwaltung**. Klicken Sie auf **Members must log in via SAML** (Mitglieder müssen sich über SAML anmelden), um die SSO-Konfiguration zu aktivieren. Führen Sie anschließend die folgenden Schritte aus:

    ![Konfigurieren der Organisationseinstellungen für einmaliges Anmelden](./media/asana-tutorial/save.png)  

    a. Fügen Sie im Textfeld **URL der Anmeldeseite** **Anmelde-URL** ein.

    b. Klicken Sie mit der rechten Maustaste auf das Zertifikat, das Sie aus dem Azure-Portal heruntergeladen haben, und öffnen Sie die Zertifikatsdatei mit Editor oder Ihrem bevorzugten Texteditor. Kopieren Sie den Inhalt zwischen den Überschriften für Beginn und Ende des Zertifikats, und fügen Sie ihn in das Textfeld **X.509-Zertifikat** ein.

3. Klicken Sie auf **Speichern**. Weitere Informationen finden Sie im [Asana-Handbuch zum Einrichten von SSO](https://asana.com/guide/help/premium/authentication#gl-saml) .

### <a name="create-asana-test-user"></a>Erstellen eines Asana-Testbenutzers

In diesem Abschnitt wird in Asana eine Benutzerin namens Britta Simon erstellt. Asana unterstützt die automatische Benutzerbereitstellung, die standardmäßig aktiviert ist. Weitere Details zur Konfiguration der automatischen Benutzerbereitstellung finden Sie [hier](asana-provisioning-tutorial.md).

**Wenn Sie einen Benutzer manuell erstellen möchten, führen Sie die folgenden Schritte aus:**

In diesem Abschnitt erstellen Sie in Asana einen Benutzer namens Britta Simon.

1. Navigieren Sie in **Asana** im linken Bereich zum Abschnitt **Teams**. Klicken Sie auf die Pluszeichen-Schaltfläche.

    ![Erstellen eines Azure AD-Testbenutzers](./media/asana-tutorial/teams.png)

2. Geben Sie die E-Mail-Adresse des Benutzers (etwa **britta.simon\@contoso.com**) in das Textfeld ein, und wählen **Invite** (Einladen) aus.

3. Klicken Sie auf **Send Invite**(Einladung senden). Eine E-Mail wird an das E-Mail-Konto des neuen Benutzers gesendet. Der Benutzer muss das Konto erstellen und bestätigen.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Asana weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Navigieren Sie direkt zur Anmelde-URL für Asana, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“auf die Kachel „Asana“ klicken, werden Sie zur Anmelde-URL für Asana weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Asana können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.
