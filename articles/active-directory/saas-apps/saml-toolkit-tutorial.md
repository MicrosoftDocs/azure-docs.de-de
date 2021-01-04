---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Azure AD SAML Toolkit | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Azure AD SAML Toolkit konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 26618382223a87f779f95452000a39126f37efbb
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675435"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-azure-ad-saml-toolkit"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Azure AD SAML Toolkit

In diesem Tutorial erfahren Sie, wie Sie Azure AD SAML Toolkit in Azure Active Directory (Azure AD) integrieren. Die Integration von Azure AD SAML Toolkit in Azure AD ermöglicht Folgendes:

* In Azure AD steuern, wer Zugriff auf Azure AD SAML Toolkit hat.
* Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Azure AD SAML Toolkit anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Azure AD SAML Toolkit-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Azure AD SAML Toolkit unterstützt **SP-initiiertes** einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Hinzufügen von Azure AD-SAML-Toolkit aus dem Katalog

Zum Konfigurieren der Integration von Azure AD SAML Toolkit in Azure AD müssen Sie Azure AD SAML Toolkit aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Azure AD SAML Toolkit** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Azure AD SAML Toolkit** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-azure-ad-saml-toolkit"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Azure AD SAML Toolkit

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Azure AD SAML Toolkit mithilfe eines Testbenutzers mit dem Namen **B. Simon** . Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Azure AD SAML Toolkit eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Azure AD SAML Toolkit die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Azure AD SAML Toolkit](#configure-azure-ad-saml-toolkit-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * **[Erstellen eines Azure AD SAML Toolkit-Testbenutzers](#create-azure-ad-saml-toolkit-test-user)** , um ein Pendant von B. Simon in Azure AD SAML Toolkit zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Azure AD SAML Toolkit** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie auf der Seite **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Anmelde-URL** die URL ein: `https://samltoolkit.azurewebsites.net/`.

    b. Geben Sie im Textfeld **Antwort-URL** die folgende URL ein: `https://samltoolkit.azurewebsites.net/SAML/Consume`.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Rohdaten)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificateraw.png)

1. Kopieren Sie im Abschnitt **Azure AD SAML Toolkit einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert aus dem Feld **Kennwort** .
   1. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Azure AD SAML Toolkit gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Azure AD SAML Toolkit** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen** .
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

## <a name="configure-azure-ad-saml-toolkit-sso"></a>Konfigurieren des einmaligen Anmeldens für Azure AD SAML Toolkit

1. Öffnen Sie ein neues Webbrowserfenster, sofern Sie sich nicht auf der Azure AD SAML Toolkit-Website registriert haben. Registrieren Sie sich zunächst, indem Sie auf **Registrieren** klicken. Wenn Sie sich bereits registriert haben, melden Sie sich mit den registrierten Anmeldeinformationen bei Ihrer Azure AD SAML Toolkit-Unternehmenswebsite an.

    ![Azure AD SAML Toolkit-Registrierung](./media/saml-toolkit-tutorial/register.png)

1. Klicken Sie auf **SAML-Konfiguration** .

    ![SAML-Konfiguration für Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/saml-configure.png)

1. Klicken Sie auf **Erstellen** .

    ![Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/createsso.png)

1. Führen Sie auf der Seite **SAML SSO Configuration** die folgenden Schritte aus:

    ![Azure AD SAML Toolkit: Erstellen der SSO-Konfiguration](./media/saml-toolkit-tutorial/fill-details.png)

    1. Fügen Sie im Textfeld **Login URL** (Anmelde-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    1. Fügen Sie in das Textfeld **Azure AD Identifier** (Azure AD-Bezeichner) den Wert für den **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    1. Fügen Sie im Textfeld **Logout URL** (Abmelde-URL) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    1. Klicken Sie auf **Choose File** (Datei auswählen), um die **Zertifikatdatei (Rohdaten)** hochzuladen, die Sie aus dem Azure-Portal heruntergeladen haben.

    1. Klicken Sie auf **Erstellen** .

    1. Kopieren Sie die Werte für Anmelde-URL, Bezeichner und ACS-URL auf der Konfigurationsseite für einmaliges Anmelden für SAML Toolkit, und fügen Sie sie im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in die entsprechenden Textfelder ein.

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Erstellen des Testbenutzers für Azure AD SAML Toolkit

In diesem Abschnitt wird in Azure AD SAML Toolkit ein Benutzer namens B. Simon erstellt. Erstellen Sie einen Testbenutzer im Tool, indem Sie einen neuen Benutzer registrieren und alle Benutzerdetails angeben. 

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

1. Klicken Sie im Azure-Portal auf **Diese Anwendung testen** . Dadurch werden Sie zur Anmelde-URL für SAML Toolkit weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

2. Rufen Sie direkt die SAML Toolkit-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

3. Sie können den Microsoft-Zugriffsbereich verwenden. Wenn Sie im Zugriffsbereich auf die Kachel „SAML Toolkit“ klicken, sollten Sie automatisch bei der SAML Toolkit-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Azure AD SAML Toolkit können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-aad) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.