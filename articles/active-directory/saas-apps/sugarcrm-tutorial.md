---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Sugar CRM | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Sugar CRM konfigurieren.
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
ms.openlocfilehash: 9cd91bf40354a40129d20a6ed0381801fece4ba4
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063136"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sugar-crm"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Sugar CRM

In diesem Tutorial erfahren Sie, wie Sie Sugar CRM in Azure Active Directory (Azure AD) integrieren. Bei der Integration von Sugar CRM in Azure AD haben Sie folgende Möglichkeiten:

* Steuern Sie in Azure AD, wer Zugriff auf Sugar CRM hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Sugar CRM anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Sugar CRM-Abonnement, für das einmaliges Anmelden (SSO) aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Sugar CRM unterstützt **SP**-initiiertes einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-sugar-crm-from-the-gallery"></a>Hinzufügen von Sugar CRM aus dem Katalog

Zum Konfigurieren der Integration von Sugar CRM in Azure AD müssen Sie Sugar CRM aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Sugar CRM** in das Suchfeld ein.
1. Wählen Sie **Sugar CRM** im Ergebnisbereich aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-sugar-crm"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Sugar CRM

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Sugar CRM mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Sugar CRM eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Sugar CRM die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Sugar CRM](#configure-sugar-crm-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Sugar CRM-Testbenutzers](#create-sugar-crm-test-user)** , um eine Entsprechung von B. Simon in Sugar CRM zu erhalten, die mit der Darstellung des Benutzers in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Sugar CRM** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein:

    - `https://<companyname>.sugarondemand.com`
    - `https://<companyname>.trial.sugarcrm`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein:

    - `https://<companyname>.sugarondemand.com/<companyname>`
    - `https://<companyname>.trial.sugarcrm.com/<companyname>`
    - `https://<companyname>.trial.sugarcrm.eu/<companyname>`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und Antwort-URL. Wenden Sie sich an das [Supportteam von Sugar CRM](https://support.sugarcrm.com/), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Sugar CRM einrichten** die entsprechende(n) URL(s) gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Sugar CRM gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Sugar CRM** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-sugar-crm-sso"></a>Konfigurieren des einmaligen Anmeldens für Sugar CRM

1. Melden Sie sich in einem anderen Webbrowserfenster an der Sugar CRM-Unternehmenswebsite als Administrator an.

1. Wechseln Sie zu **Administrator**.

    ![Administrator](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. Klicken Sie unter **Administration** auf **Kennwortverwaltung**.

    ![Screenshot: Abschnitt „Administration“ (Verwaltung), in dem Sie „Password Management“ (Kennwortverwaltung) auswählen können](./media/sugarcrm-tutorial/ic795889.png "Verwaltung")

1. Wählen Sie die Option **SAML-Authentifizierung aktivieren**.

    ![Screenshot: Option zum Auswählen der SAML-Authentifizierung](./media/sugarcrm-tutorial/ic795890.png "Verwaltung")

1. Führen Sie im Abschnitt für die **SAML-Authentifizierung** die folgenden Schritte aus:

    ![SAML Authentication (SAML-Authentifizierung)](./media/sugarcrm-tutorial/save.png "SAML-Authentifizierung")  

    a. Fügen Sie in das Textfeld **Anmelde-URL** den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.
  
    b. Fügen Sie in das Textfeld **SLO URL** (SLO-URL) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.
  
    c. Öffnen Sie das Base-64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie anschließend das gesamte Zertifikat in das Textfeld **X.509-Zertifikat** ein.
  
    d. Klicken Sie auf **Speichern**.

### <a name="create-sugar-crm-test-user"></a>Erstellen eines Sugar CRM-Testbenutzers

Damit sich Azure AD-Benutzer bei Sugar CRM anmelden können, müssen sie in Sugar CRM bereitgestellt werden. Im Fall von Sugar CRM ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich an der **Sugar CRM**-Unternehmenswebsite als Administrator an.

1. Wechseln Sie zu **Administrator**.

    ![Administrator](./media/sugarcrm-tutorial/ic795888.png "Admin")

1. Klicken Sie im Abschnitt **Verwaltung** auf **Benutzerverwaltung**.

    ![Screenshot: Abschnitt „Administration“ (Verwaltung), in dem Sie „User Management“ (Benutzerverwaltung) auswählen können](./media/sugarcrm-tutorial/ic795893.png "Verwaltung")

1. Klicken Sie auf **Benutzer \> Neuen Benutzer** erstellen.

    ![Create New User (Neuen Benutzer erstellen)](./media/sugarcrm-tutorial/ic795894.png "Neuen Benutzer erstellen")

1. Führen Sie auf der Registerkarte **Benutzerprofil** die folgenden Schritte aus:

    ![Screenshot: Registerkarte „User Profile“ (Benutzerprofil), auf der Sie die beschriebenen Werte eingeben können](./media/sugarcrm-tutorial/ic795895.png "Neuer Benutzer")

    * Geben Sie die entsprechenden Informationen in die Textfelder **Benutzername**, **Nachname** und **E-Mail-Adresse** eines gültigen Azure Active Directory-Kontos ein, das Sie bereitstellen möchten.
  
1. Wählen Sie als **Status** **Aktiv** aus.

1. Führen Sie auf der Registerkarte „Kennwort“ die folgenden Schritte aus:

    ![Screenshot: Registerkarte „Password“ (Kennwort), auf der Sie die beschriebenen Werte eingeben können](./media/sugarcrm-tutorial/ic795896.png "Neuer Benutzer")

    a. Geben Sie das Kennwort in das entsprechende Textfeld ein.

    b. Klicken Sie auf **Speichern**.

> [!NOTE]
> Sie können Azure AD-Benutzerkonten auch mit anderen Tools zum Erstellen von Sugar CRM-Benutzerkonten oder mit den APIs von Sugar CRM bereitstellen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Sugar CRM weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die Sugar CRM-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“auf die Kachel „Sugar CRM“ klicken, werden Sie zur Anmelde-URL für Sugar CRM weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Sugar CRM können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.
