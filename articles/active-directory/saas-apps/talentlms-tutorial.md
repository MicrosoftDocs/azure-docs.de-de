---
title: 'Tutorial: Azure Active Directory-Integration in TalentLMS | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und TalentLMS konfigurieren.
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
ms.openlocfilehash: 86ce076df076c990b8bdefa9695805d34674a03f
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063488"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Tutorial: Azure Active Directory-Integration in TalentLMS

In diesem Tutorial erfahren Sie, wie Sie TalentLMS in Azure Active Directory (Azure AD) integrieren. Die Integration von TalentLMS in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf TalentLMS hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei TalentLMS anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit TalentLMS konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein TalentLMS-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* TalentLMS unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="add-talentlms-from-the-gallery"></a>Hinzufügen von TalentLMS aus dem Katalog

Zum Konfigurieren der Integration von TalentLMS in Azure AD müssen Sie TalentLMS aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **TalentLMS** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **TalentLMS** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-talentlms"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für TalentLMS

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit TalentLMS mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in TalentLMS eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit TalentLMS die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für TalentLMS](#configure-talentlms-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines TalentLMS-Testbenutzers](#create-talentlms-test-user)** , um eine Entsprechung von B. Simon in TalentLMS zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **TalentLMS** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für TalentLMS](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<tenant-name>.TalentLMSapp.com`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `http://<tenant-name>.talentlms.com`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Supportteam für den TalentLMS-Client](https://www.talentlms.com/contact), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **SAML-Signaturzertifikat** zu öffnen.

    ![Bearbeiten des SAML-Signaturzertifikats](common/edit-certificate.png)

6. Kopieren Sie im Abschnitt **SAML-Signaturzertifikat** den **FINGERABDRUCK**, und speichern Sie ihn auf Ihrem Computer.

    ![Kopieren des Fingerabdruckwerts](common/copy-thumbprint.png)

7. Kopieren Sie im Abschnitt **TalentLMS einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf TalentLMS gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **TalentLMS** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="configure-talentlms-sso"></a>Konfigurieren des einmaligen Anmeldens für TalentLMS

1. Melden Sie sich in einem anderen Webbrowserfenster bei der TalentLMS-Unternehmenswebsite als Administrator an.

1. Klicken Sie im Abschnitt **Konto und Einstellungen** auf die Registerkarte **Benutzer**.

    ![Konto und Einstellungen](./media/talentlms-tutorial/IC777296.png "Konto und Einstellungen")

1. Klicken Sie auf **Single Sign-On (SSO)**,

1. Führen Sie im Abschnitt „Einmaliges Anmelden“ die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/talentlms-tutorial/saml.png "Einmaliges Anmelden")

    a. Wählen Sie in der Liste **SSO integration type** den Eintrag **SAML 2.0** aus.

    b. Fügen Sie in das Textfeld **Identitätsanbieter (IdP)** den Wert des **Azure AD-Bezeichners** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie in das Textfeld **Fingerabdruck des Zertifikats** den Wert für **Fingerabdruck** aus dem Azure-Portal ein.

    d.  Fügen Sie in das Textfeld **Remoteanmelde-URL** den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    e. Fügen Sie in das Textfeld **Remoteabmelde-URL** den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    f. Geben Sie Folgendes ein:

    * Geben Sie `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` in das Textfeld **TargetedID** ein.

    * Geben Sie `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` in das Textfeld **Vorname** ein.

    * Geben Sie `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` in das Textfeld **Nachname** ein.

    * Geben Sie `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` in das Textfeld **E-Mail** ein.

1. Klicken Sie auf **Speichern**.

### <a name="create-talentlms-test-user"></a>Erstellen eines TalentLMS-Testbenutzers

Damit sich Azure AD-Benutzer bei TalentLMS anmelden können, müssen sie in TalentLMS bereitgestellt werden. Im Fall von TalentLMS ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrem **TalentLMS**-Mandanten an.

1. Klicken Sie auf **Users**, und klicken Sie dann auf **Add User**.

1. Führen Sie auf der Seite **Add user** die folgenden Schritte aus:

    ![Benutzer hinzufügen](./media/talentlms-tutorial/IC777299.png "Benutzer hinzufügen")  

    a. Geben Sie im Textfeld **Vorname** den Vornamen des Benutzers ein (z.B. **Britta**).

    b. Geben Sie im Textfeld **Nachname** den Nachnamen des Benutzers ein (z.B. **Simon**).
 
    c. Geben Sie im Textfeld **E-Mail-Adresse** die E-Mail-Adresse des Benutzers ein, z.B. `brittasimon\@contoso.com`.

    d. Klicken Sie auf **Benutzer hinzufügen**.

> [!NOTE]
> Sie können Azure AD-Benutzerkonten auch mit anderen Tools zum Erstellen von TalentLMS-Benutzerkonten oder mit den APIs von TalentLMS bereitstellen.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für TalentLMS weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die TalentLMS-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“auf die Kachel „TalentLMS“ klicken, werden Sie zur Anmelde-URL für TalentLMS weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von TalentLMS können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.
