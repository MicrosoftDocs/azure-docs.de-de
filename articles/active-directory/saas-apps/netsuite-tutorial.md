---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit NetSuite | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und NetSuite konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/09/2020
ms.author: jeedes
ms.openlocfilehash: 9272114dd472717e59680b40c07d8435dea19682
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181967"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-sso-with-netsuite"></a>Tutorial: Integrieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit NetSuite

In diesem Tutorial erfahren Sie, wie Sie NetSuite in Azure Active Directory (Azure AD) integrieren. Die Integration von NetSuite in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf NetSuite hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei NetSuite anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein NetSuite-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung. 

NetSuite unterstützt Folgendes:

* IDP-initiiertes einmaliges Anmelden
* JIT-Benutzerbereitstellung (Just-In-Time)
* [Automatisierte Benutzerbereitstellung](NetSuite-provisioning-tutorial.md)

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-netsuite-from-the-gallery"></a>Hinzufügen von NetSuite aus dem Katalog

Fügen Sie zum Konfigurieren der Integration von NetSuite in Azure AD NetSuite aus dem Katalog wie folgt der Liste mit den verwalteten SaaS-Apps hinzu:

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Bereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **NetSuite** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **NetSuite** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für NetSuite

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit NetSuite mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in NetSuite eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit NetSuite die folgenden Schritte aus:

1. [Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso), um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
    * [Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user), um das einmalige Anmelden von Azure AD mit dem Benutzer B. Simon zu testen  
    * [Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user), um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen
1. [Konfigurieren des einmaligen Anmeldens für NetSuite](#configure-netsuite-sso), um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * [Erstellen eines NetSuite-Testbenutzers](#create-the-netsuite-test-user), um ein Pendant von B. Simon in NetSuite zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. [Testen Sie das einmalige Anmelden](#test-sso), um zu überprüfen, ob die Konfiguration funktioniert.

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Führen Sie die folgenden Schritte aus, um einmaliges Anmelden von Azure AD im Azure-Portal zu aktivieren:

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **NetSuite** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie im Bereich **SSO-Methode auswählen** die Methode **SAML** aus.
1. Wählen Sie im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** neben **Grundlegende SAML-Konfiguration** das Symbol **Bearbeiten** (Stift) aus.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** im Textfeld **Antwort-URL** eine URL in einem der folgenden Formate ein:

    ```https
    https://<Instance ID>.NetSuite.com/saml2/acs
    https://<Instance ID>.na1.NetSuite.com/saml2/acs
    https://<Instance ID>.na2.NetSuite.com/saml2/acs
    https://<Instance ID>.sandbox.NetSuite.com/saml2/acs
    https://<Instance ID>.na1.sandbox.NetSuite.com/saml2/acs
    https://<Instance ID>.na2.sandbox.NetSuite.com/saml2/acs
    ```

    * Den Wert **<`Instance ID`>** erhalten Sie im NetSuite-Konfigurationsabschnitt, wie weiter unten im Tutorial in Schritt 8 unter „NetSuite-Konfiguration“ erläutert. Dort finden Sie die exakte Domäne (in diesem Fall also beispielsweise „system.na0.netsuite.com“).

        ![Screenshot: Seite „SAML-Setup“, auf der Sie die Domäne abrufen können](./media/NetSuite-tutorial/domain-value.png)

        > [!NOTE]
        > Bei den Werten in den vorangehenden URLs handelt es sich um Beispielwerte. Ersetzen Sie sie durch die tatsächliche Antwort-URL. Den Wert erhalten Sie vom [Supportteam für den NetSuite-Client](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml). Sie können sich auch die Formate im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die NetSuite-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Darüber hinaus erwartet die NetSuite-Anwendung, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.

    | Name | Quellattribut |
    | ---------------| --------------- |
    | account  | `account id` |

    > [!NOTE]
    > Bei dem Wert des Kontoattributs handelt es sich nicht um einen echten Wert. Sie aktualisieren diesen Wert. Dies wird später in diesem Tutorial beschrieben.

1. Navigieren Sie auf der Seite „Einmaliges Anmelden (SSO) mit SAML einrichten“ im Abschnitt „SAML-Signaturzertifikat“ zu „Verbundmetadaten-XML“, und wählen Sie „Herunterladen“ aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **NetSuite einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer namens B.Simon.

1. Wählen Sie im Azure-Portal im linken Bereich **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.

1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

1. Führen Sie im Eigenschaftenbereich für **Benutzer** die folgenden Schritte aus:

   a. Geben Sie im Feld **Name** den Namen **B.Simon** ein.  
   b. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein (z. B. B.Simon@contoso.com).  
   c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.  
   d. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf NetSuite gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **NetSuite** aus.
1. Navigieren Sie im Übersichtsbereich zum Abschnitt **Verwalten**, und wählen Sie den Link **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Bereich **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Bereich **Benutzer und Gruppen** in der Dropdownliste **Benutzer** den Eintrag **B.Simon** und anschließend am unteren Bildschirmrand die Schaltfläche **Auswählen** aus.
1. Wenn Sie einen Rollenwert in der SAML-Assertion erwarten, gehen Sie wie folgt vor:

   a. Wählen Sie im Bereich **Rolle auswählen** in der Dropdownliste die entsprechende Rolle für den Benutzer aus.  
   b. Wählen Sie unten auf dem Bildschirm die Schaltfläche **Auswählen** aus.
1. Wählen Sie im Bereich **Zuweisung hinzufügen** die Schaltfläche **Zuweisen** aus.

## <a name="configure-netsuite-sso"></a>Konfigurieren des einmaligen Anmeldens für NetSuite

1. Öffnen Sie eine neue Registerkarte in Ihrem Browser, und melden Sie sich bei Ihrer NetSuite-Unternehmenswebsite als Administrator an.

2. Wählen Sie auf der oberen Navigationsleiste **Setup** und anschließend **Company** > **Enable Features** (Unternehmen > Features aktivieren) aus.

    ![Screenshot: Auswahl von „Enable Features“ (Features aktivieren) unter „Company“ (Unternehmen)](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Wählen Sie auf der Symbolleiste in der Mitte der Seite **SuiteCloud** aus.

    ![Screenshot: Auswahl von „SuiteCloud“](./media/NetSuite-tutorial/ns-suitecloud.png)

4. Aktivieren Sie unter **Manage Authentication** (Authentifizierung verwalten) das Kontrollkästchen **SAML Single Sign-on** (Einmaliges Anmelden für SAML), um die Option für SAML-SSO in NetSuite zu aktivieren.

    ![Screenshot: Option „Manage Authentication“ (Authentifizierung verwalten) mit Auswahl von „SAML Single Sign-on“ (Einmaliges Anmelden für SAML)](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Wählen Sie auf der oberen Navigationsleiste die Option **Setup** aus.

    ![Screenshot: Auswahl von „Setup“ in der NETSUITE-Navigationsleiste](./media/NetSuite-tutorial/ns-setup.png)

6. Wählen Sie in der Liste **Setup Tasks** (Setupaufgaben) die Option **Integration** aus.

    ![Screenshot: Auswahl von „Integration“ unter „Setup Tasks“ (Setupaufgaben)](./media/NetSuite-tutorial/ns-integration.png)

7. Wählen Sie unter **Manage Authentication** (Authentifizierung verwalten) die Option **SAML Single Sign-on** (Einmaliges Anmelden für SAML) aus.

    ![Screenshot: Auswahl von „SAML Single Sign-on“ (Einmaliges Anmelden für SAML) unter der Option „Integration“ von „Setup Tasks“ (Setupaufgaben)](./media/NetSuite-tutorial/ns-saml.png)

8. Führen Sie im Bereich **SAML Setup** (SAML-Setup) unter **NetSuite-Konfiguration** die folgenden Schritte aus:

    ![Screenshot: Bereich „SAML Setup“ (SAML-Setup), in dem Sie die beschriebenen Werte eingeben können](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Aktivieren Sie das Kontrollkästchen **Primary Authentication Method** (Primäre Authentifizierungsmethode).

    b. Wählen Sie unter **SAMLV2 Identity Provider Metadata** (SAMLV2-Identitätsanbietermetadaten) die Option **Upload IDP Metadata File** (IDP-Metadatendatei hochladen) und dann **Browse** (Durchsuchen) aus, um die aus dem Azure-Portal heruntergeladene Metadatendatei hochzuladen.

    c. Klicken Sie auf **Submit** (Senden).

9. Wählen Sie auf der oberen NetSuite-Navigationsleiste **Setup** und anschließend **Company** > **Company Information** (Unternehmensinformationen) aus.

    ![Screenshot: Auswahl von „Company Information“ (Unternehmensinformationen) unter „Company“ (Unternehmen)](./media/NetSuite-tutorial/ns-com.png)

    ![Screenshot: Bereich, in dem Sie die beschriebenen Werte eingeben können](./media/NetSuite-tutorial/ns-account-id.png)

    b. Kopieren Sie im Bereich **Company Information** (Unternehmensinformationen) in der rechten Spalte den Wert für **Account ID** (Konto-ID).

    c. Fügen Sie den Wert für **Account ID** (Konto-ID), den Sie im NetSuite-Konto kopiert haben, in das Feld **Attributwert** in Azure AD ein.

    ![Screenshot: Hinzufügen des Konto-ID-Werts](./media/netsuite-tutorial/attribute-value.png)

10. Bevor Benutzer die einmalige Anmeldung in NetSuite ausführen können, müssen ihnen zunächst die entsprechenden Berechtigungen in NetSuite zugewiesen werden. Gehen Sie zum Zuweisen dieser Berechtigungen wie folgt vor:

    a. Wählen Sie auf der oberen Navigationsleiste die Option **Setup** aus.

    ![Screenshot: Auswahl von „Setup“ in der NETSUITE-Navigationsleiste](./media/NetSuite-tutorial/ns-setup.png)

    b. Wählen Sie im linken Bereich **Users/Roles** (Benutzer/Rollen) und dann **Manage Roles** (Rollen verwalten) aus.

    ![Screenshot: Bereich „Manage Roles“ (Rollen verwalten), in dem Sie „New Role“ (Neue Rolle) auswählen können](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Wählen Sie **New Role** (Neue Rolle) aus.

    d. Geben Sie unter **Name** einen Namen für die neue Rolle ein.

    ![Screenshot: Setup-Manager, in dem Sie einen Namen für die Rolle eingeben können](./media/NetSuite-tutorial/ns-new-role.png)

    e. Wählen Sie **Speichern** aus.

    f. Wählen Sie auf der oberen Navigationsleiste die Option **Permissions** (Berechtigungen) aus. Wählen Sie anschließend **Setup** aus.

    ![Screenshot: Registerkarte „Setup“, auf der Sie die beschriebenen Werte eingeben können](./media/NetSuite-tutorial/ns-sso.png)

    g. Wählen Sie **SAML Single Sign-on** (Einmaliges Anmelden für SAML) und dann **Add** (Hinzufügen) aus.

    h. Wählen Sie **Speichern** aus.

    i. Wählen Sie auf der oberen Navigationsleiste **Setup** und anschließend **Setup Manager** (Setup-Manager) aus.

    ![Screenshot: Auswahl von „Setup“ in der NETSUITE-Navigationsleiste](./media/NetSuite-tutorial/ns-setup.png)

    j. Wählen Sie im linken Bereich **Users/Roles** (Benutzer/Rollen) und dann **Manage Users** (Benutzer verwalten) aus.

    ![Screenshot: Bereich „Manage Users“ (Benutzer verwalten), in dem Sie das Suite Demo Team auswählen können](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Wählen Sie einen Testbenutzer aus. Wählen Sie dann **Edit** (Bearbeiten) und die Registerkarte **Access** (Zugriff) aus.

    ![Screenshot: Bereich „Manage Users“ (Benutzer verwalten), in dem Sie „Edit“ (Bearbeiten) auswählen können](./media/NetSuite-tutorial/ns-edit-user.png)

    l. Weisen Sie im Bereich **Roles** (Rollen) die entsprechende Rolle zu, die Sie erstellt haben.

    ![Screenshot: Auswahl von „Administrator“ unter „Employee“ (Mitarbeiter)](./media/NetSuite-tutorial/ns-add-role.png)

    m. Wählen Sie **Speichern** aus.

### <a name="create-the-netsuite-test-user"></a>Erstellen eines NetSuite-Testbenutzers

In diesem Abschnitt wird in NetSuite ein Benutzer namens B. Simon erstellt. NetSuite unterstützt die Just-in-Time-Benutzerbereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in NetSuite vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

- Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der NetSuite-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

- Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „NetSuite“ klicken, sollten Sie automatisch bei der NetSuite-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von NetSuite können Sie Sitzungssteuerungen erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützen. Sitzungssteuerungen basieren auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-aad) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.