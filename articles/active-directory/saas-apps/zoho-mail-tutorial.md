---
title: 'Tutorial: Azure Active Directory-Integration mit Zoho | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Zoho konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: jeedes
ms.openlocfilehash: 50e54b8518ecdbb4ef9f9a55ba50510da6becedc
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820909"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Tutorial: Azure Active Directory-Integration mit Zoho

In diesem Tutorial erfahren Sie, wie Sie Zoho in Azure Active Directory (Azure AD) integrieren. Die Integration von Zoho in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Zoho hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Zoho anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in Zoho One konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Zoho-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Zoho unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="add-zoho-from-the-gallery"></a>Hinzufügen von Zoho aus dem Katalog

Zum Konfigurieren der Integration von Zoho in Azure AD müssen Sie Zoho aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Zoho** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Zoho** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-zoho"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Zoho

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Zoho mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Zoho eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Zoho die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Zoho](#configure-zoho-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Zoho-Testbenutzers](#create-zoho-test-user)** , um eine Entsprechung von B. Simon in Zoho zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Zoho** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<company name>.zohomail.com`

    > [!NOTE]
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Wenden Sie sich an das [Supportteam für den Zoho-Client](https://www.zoho.com/mail/contact.html), um den Wert zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Zoho einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Zoho gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **Zoho** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="configure-zoho-sso"></a>Konfigurieren des einmaligen Anmeldens für Zoho

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Zoho Mail-Unternehmenswebsite als Administrator an.

2. Wechseln Sie zur **Systemsteuerung**.
   
    ![Systemsteuerung](./media/zoho-mail-tutorial/control-panel.png "Systemsteuerung")

3. Klicken Sie auf die Registerkarte **SAML-Authentifizierung** .
   
    ![SAML Authentication (SAML-Authentifizierung)](./media/zoho-mail-tutorial/saml-authentication.png "SAML-Authentifizierung")

4. Führen Sie im Abschnitt für die **SAML-Authentifizierungsdetails** die folgenden Schritte aus:
   
    ![SAML Authentication Details (SAML-Authentifizierungsdetails)](./media/zoho-mail-tutorial/details.png "SAML-Authentifizierungsdetails")
   
    a. Fügen Sie in das Textfeld **Login URL** (Anmelde-URL) die **Anmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.
   
    b. Fügen Sie in das Textfeld **Logout URL** (Abmelde-URL) die **Abmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.
   
    c. Fügen Sie im Textfeld **Change Password URL** den Wert der **URL für Kennwortänderung** ein, den Sie aus dem Azure-Portal kopiert haben.
       
    d. Öffnen Sie in Editor das Base64-codierte Zertifikat, das Sie aus dem Azure-Portal heruntergeladen haben, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **PublicKey** ein.
   
    e. Wählen Sie als **Algorithmus** den Wert **RSA** aus.
   
    f. Klicken Sie auf **OK**.

### <a name="create-zoho-test-user"></a>Erstellen eines Zoho-Testbenutzers

Damit sich Azure AD-Benutzer bei Zoho Mail anmelden können, müssen sie in Zoho Mail bereitgestellt werden. Im Fall von Zoho Mail ist die Bereitstellung eine manuelle Aufgabe.

> [!NOTE]
> Sie können Azure AD-Benutzerkonten auch mit anderen Tools zum Erstellen von Zoho Mail-Benutzerkonten oder mit den APIs von Zoho Mail bereitstellen.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:

1. Melden Sie sich bei der **Zoho Mail** -Unternehmenswebsite als Administrator an.

1. Wechseln Sie zu **Systemsteuerung \> E-Mails und Dokumente**.

1. Wechseln Sie zu **Benutzerdetails \> Benutzer hinzufügen**.
   
    ![Screenshot: Zoho Mail-Website, auf der „User Details“ (Benutzerdetails) und „Add User“ (Benutzer hinzufügen) ausgewählt sind](./media/zoho-mail-tutorial/add-user-1.png "Benutzer hinzufügen")

1. Führen Sie im Dialogfeld **Benutzer hinzufügen** die folgenden Schritte aus:
   
    ![Screenshot: Dialogfeld „Benutzer hinzufügen“, in dem Sie die beschriebenen Werte eingeben können](./media/zoho-mail-tutorial/add-user-2.png "Benutzer hinzufügen")
   
    a. Geben Sie im Textfeld **Vorname** den Vornamen des Benutzers ein, z.B. **Britta**.

    b. Geben Sie im Textfeld **Nachname** den Nachnamen des Benutzers ein, z.B. **Simon**.

    c. Geben Sie im Textfeld **Email ID** (E-Mail-ID) die E-Mail-ID des Benutzers ein, z. B. **brittasimon\@contoso.com**.

    d. Geben Sie im Textfeld **Kennwort** das Kennwort des Benutzers ein.
   
    e. Klicken Sie auf **OK**.  
      
    > [!NOTE]
    > Der Besitzer des Azure Active Directory-Kontos erhält eine E-Mail mit einem Link zur Bestätigung des Kontos, bevor es aktiv wird.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Zoho weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Navigieren Sie direkt zur Anmelde-URL für Zoho, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „Zoho“ klicken, sollten Sie automatisch bei der Zoho-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Zoho können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.
