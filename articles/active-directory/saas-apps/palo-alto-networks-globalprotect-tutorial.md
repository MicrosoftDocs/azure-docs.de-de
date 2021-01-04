---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory-Integration mit Palo Alto Networks – GlobalProtect | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Palo Alto Networks - GlobalProtect konfigurieren.
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
ms.openlocfilehash: a0b76c21ee76731f78ecd7f482472908fe93a9ee
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92513329"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-palo-alto-networks---globalprotect"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory-Integration mit Palo Alto Networks – GlobalProtect

In diesem Tutorial erfahren Sie, wie Sie Palo Alto Networks – GlobalProtect in Azure Active Directory (Azure AD) integrieren. Die Integration von Palo Alto Networks – GlobalProtect in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Palo Alto Networks – GlobalProtect hat.
* Ermöglichen Sie es Benutzern, sich mit ihren Azure AD-Konten automatisch bei Palo Alto Networks – GlobalProtect anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Abonnement für Palo Alto Networks – GlobalProtect, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Palo Alto Networks – GlobalProtect unterstützt **SP** -initiiertes einmaliges Anmelden.
* Palo Alto Networks – GlobalProtect unterstützt die **Just-in-Time** -Benutzerbereitstellung.

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>Hinzufügen von Palo Alto Networks - GlobalProtect aus dem Katalog

Zum Konfigurieren der Integration von Palo Alto Networks - GlobalProtect in Azure AD müssen Sie Palo Alto Networks - GlobalProtect aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Palo Alto Networks - GlobalProtect** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Palo Alto Networks - GlobalProtect** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-palo-alto-networks---globalprotect"></a>Konfigurieren und Testen des einmaligen Anmeldens (SSO) von Azure AD für Palo Alto Networks – GlobalProtect

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Palo Alto Networks – GlobalProtect mithilfe eines Testbenutzers mit dem Namen **B. Simon** . Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Palo Alto Networks – GlobalProtect eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens (SSO) von Azure AD bei Palo Alto Networks – GlobalProtect die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Palo Alto Networks – GlobalProtect](#configure-palo-alto-networks---globalprotect-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Palo Alto Networks – GlobalProtect-Testbenutzers](#create-palo-alto-networks---globalprotect-test-user)** , um eine Entsprechung von B. Simon in Palo Alto Networks – GlobalProtect zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Palo Alto Networks – GlobalProtect** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<Customer Firewall URL>`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<Customer Firewall URL>/SAML20/SP`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Supportteam für den Palo Alto Networks - GlobalProtect-Client](https://support.paloaltonetworks.com/support), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML** , und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Palo Alto Networks – GlobalProtect einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Palo Alto Networks – GlobalProtect gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Palo Alto Networks - GlobalProtect** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen** .
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

## <a name="configure-palo-alto-networks---globalprotect-sso"></a>Konfigurieren des einmaligen Anmeldens für Palo Alto Networks – GlobalProtect

1. Öffnen Sie Palo Alto Networks - GlobalProtect als Administrator in einem anderen Browserfenster.

2. Klicken Sie auf **Device** (Gerät).

    ![Konfigurieren des einmaligen Anmeldens für Palo Alto (1)](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Klicken Sie in der linken Navigationsleiste auf **SAML-Identitätsanbieter** und anschließend auf „Importieren“, um die Metadatendatei zu importieren.

    ![Konfigurieren des einmaligen Anmeldens für Palo Alto (2)](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Führen Sie im Fenster zum Importieren die folgenden Aktionen aus:

    ![Konfigurieren des einmaligen Anmeldens für Palo Alto (3)](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. Geben Sie im Textfeld **Profile Name** (Profilname) einen Namen ein, etwa „Azure AD Global Protect“.

    b. Klicken Sie unter **Identity Provider Metadata** (Metadaten des Identitätsanbieters) auf **Browse** (Durchsuchen), und wählen Sie die Datei „metadata.xml“ aus, die Sie aus dem Azure-Portal heruntergeladen haben.

    c. Klicken Sie auf **OK**

### <a name="create-palo-alto-networks---globalprotect-test-user"></a>Erstellen eines Palo Alto Networks – GlobalProtect-Testbenutzers

In diesem Abschnitt wird in Palo Alto Networks – GlobalProtect ein Benutzer namens B. Simon erstellt. Palo Alto Networks – GlobalProtect unterstützt die Just-in-Time-Benutzerbereitstellung. Dies ist auch die Standardeinstellung. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Falls ein Benutzer nicht bereits in Palo Alto Networks – GlobalProtect vorhanden ist, wird nach der Authentifizierung ein neuer Benutzer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

1. Klicken Sie im Azure-Portal auf **Diese Anwendung testen** . Dadurch werden Sie zur Anmelde-URL für Palo Alto Networks – GlobalProtect weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

2. Rufen Sie direkt die Anmelde-URL für Palo Alto Networks – GlobalProtect auf, und initiieren Sie den Anmeldeflow.

3. Sie können den Microsoft-Zugriffsbereich verwenden. Wenn Sie im Zugriffsbereich auf die Kachel „Palo Alto Networks – GlobalProtect“ klicken, sollten Sie automatisch bei der Palo Alto Networks – GlobalProtect-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Palo Alto Networks – GlobalProtect können Sie eine Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.