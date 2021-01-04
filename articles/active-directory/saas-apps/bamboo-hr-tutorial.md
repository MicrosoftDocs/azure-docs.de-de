---
title: 'Tutorial: Azure Active Directory-Integration in BambooHR | Microsoft-Dokumentation'
description: In diesem Artikel erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und BambooHR konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/02/2020
ms.author: jeedes
ms.openlocfilehash: 3926e98cb55d1afd80caf4af5d67910b66c09cdd
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180335"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Tutorial: Azure Active Directory-Integration in BambooHR

In diesem Tutorial erfahren Sie, wie Sie Azure Active Directory (Azure AD) in BambooHR integrieren. Die Integration von BambooHR in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf BambooHR hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei BambooHR anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein BambooHR-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* BambooHR unterstützt **SP**-initiiertes einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.


## <a name="adding-bamboohr-from-the-gallery"></a>Hinzufügen von BambooHR aus dem Katalog

Zum Konfigurieren der Integration von Azure AD in BambooHR müssen Sie BambooHR aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **BambooHR** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **BambooHR** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-bamboohr"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für BambooHR

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit BambooHR mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in BambooHR eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit BambooHR die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
2. **[Konfigurieren des einmaligen Anmeldens für BambooHR](#configure-bamboohr-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * **[Erstellen eines BambooHR-Testbenutzers](#create-bamboohr-test-user)**, um eine Entsprechung von Britta Simon in BambooHR zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
3. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **BambooHR** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<company>.bamboohr.com`.

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein:

    | Antwort-URL |
    |-----------|
    | `https://<company>.bamboohr.com/saml/consume.php` |
    | `https://<company>.bamboohr.co.uk/saml/consume.php` |

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächlichen Werte für Anmelde-URL und Antwort-URL. Diese Werte erhalten Sie vom [Supportteam für den BambooHR-Client](https://www.bamboohr.com/contact.php). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **BambooHR einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf BambooHR gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **BambooHR** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-bamboohr-sso"></a>Konfigurieren des einmaligen Anmeldens für BambooHR

1. Melden Sie sich in einem neuen Fenster bei der BambooHR-Unternehmenswebsite als Administrator an.

2. Gehen Sie auf der Startseite wie folgt vor:
   
    ![Seite für einmaliges Anmelden mit BambooHR](./media/bamboo-hr-tutorial/ic796691.png "Single Sign-On")   

    a. Klicken Sie auf **Apps**.
   
    b. Wählen Sie im Bereich **Apps** die Option **Single Sign-On** (Einmaliges Anmelden).
   
    c. Wählen Sie **SAML Single Sign-On** (Einmaliges Anmelden für SAML).

3. Gehen Sie im Bereich **SAML Single Sign-On** (Einmaliges Anmelden für SAML) wie folgt vor:
   
    ![Bereich „SAML Single Sign-On“ (Einmaliges Anmelden für SAML)](./media/bamboo-hr-tutorial/IC796692.png "SAML Single Sign-On")
   
    a. Fügen Sie im Feld **SSO Login Url** (SSO-Anmelde-URL) die **Anmelde-URL** ein, die Sie in Schritt 6 aus dem Azure-Portal kopiert haben.
      
    b. Öffnen Sie im Editor das Base64-codierte Zertifikat, das Sie aus dem Azure-Portal heruntergeladen haben, kopieren Sie den Inhalt, und fügen Sie ihn anschließend in das Feld **X.509-Zertifikat** ein.
   
    c. Wählen Sie **Speichern** aus.

### <a name="create-bamboohr-test-user"></a>Erstellen eines BambooHR-Testbenutzers

Damit sich Azure AD-Benutzer bei BambooHR anmelden können, richten Sie diese wie folgt manuell in BambooHR ein:

1. Melden Sie sich bei Ihrer **BambooHR**-Website als Administrator an.

2. Wählen Sie oben in der Symbolleiste **Einstellungen**.
   
    ![Schaltfläche „Settings“ (Einstellungen)](./media/bamboo-hr-tutorial/IC796694.png "Einstellung")

3. Wählen Sie **Übersicht**.

4. Wählen Sie im linken Bereich **Security** (Sicherheit) > **Users** (Benutzer).

5. Geben Sie Benutzername, Kennwort und E-Mail-Adresse eines gültigen Azure AD-Kontos ein, das Sie einrichten möchten.

6. Wählen Sie **Speichern** aus.
        
>[!NOTE]
>Zum Einrichten von Azure AD-Benutzerkonten können Sie auch Tools oder APIs zum Erstellen von BambooHR-Benutzerkonten verwenden.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

1. Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für BambooHR weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

2. Rufen Sie direkt die BambooHR-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

3. Sie können den Microsoft-Zugriffsbereich verwenden. Wenn Sie im Zugriffsbereich auf die Kachel „BambooHR“ klicken, werden Sie zur Anmelde-URL für BambooHR umgeleitet. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von BambooHR können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.