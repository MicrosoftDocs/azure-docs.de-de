---
title: 'Tutorial: Azure Active Directory-Integration mit MOVEit Transfer – Azure AD-Integration| Microsoft-Dokumentation'
description: Informationen zur Konfiguration des einmaligen Anmeldens zwischen Azure Active Directory und MOVEit Transfer – Azure AD-Integration
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
ms.openlocfilehash: 31bcf1592178ebe3290403c3d3abb3f7a3a7cd27
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99429909"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Tutorial: Azure Active Directory-Integration mit MOVEit Transfer – Azure AD-Integration

In diesem Tutorial erfahren Sie, wie Sie MOVEit Transfer – Azure AD-Integration in Azure Active Directory (Azure AD) integrieren. Die Integration von MOVEit Transfer – Azure AD-Integration in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf MOVEit Transfer – Azure AD-Integration hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihrem Azure AD-Konto automatisch bei MOVEit Transfer – Azure AD-Integration anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Abonnement für MOVEit Transfer – Azure AD-Integration, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* MOVEit Transfer – Azure AD-Integration unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="add-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Hinzufügen von MOVEit Transfer – Azure AD-Integration aus dem Katalog

Zum Konfigurieren der Integration von MOVEit Transfer – Azure AD-Integration in Azure AD müssen Sie MOVEit Transfer – Azure AD-Integration über den Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **MOVEit Transfer – Azure AD-Integration** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **MOVEit Transfer – Azure AD-Integration** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-moveit-transfer---azure-ad-integration"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für MOVEit Transfer – Azure AD-Integration

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit MOVEit Transfer – Azure AD-Integration unter Verwendung eines Testbenutzers namens **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in MOVEit Transfer – Azure AD-Integration eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit MOVEit Transfer – Azure AD-Integration zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für MOVEit Transfer – Azure AD-Integration](#configure-moveit-transfer---azure-ad-integration-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Testbenutzers für MOVEit Transfer – Azure AD-Integration](#create-moveit-transfer---azure-ad-integration-test-user)** , um in MOVEit Transfer – Azure AD-Integration eine Entsprechung von B. Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **MOVEit Transfer – Azure AD-Integration** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie über eine **Dienstanbieter-Metadatendatei** verfügen:

    a. Klicken Sie auf **Metadatendatei hochladen**.

    ![Metadatendatei hochladen](common/upload-metadata.png)

    b. Klicken Sie auf das **Ordnerlogo**, wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen**.

    ![Metadatendatei auswählen](common/browse-upload-metadata.png)

    c. Nach dem erfolgreichen Upload der Metadatendatei werden die Werte unter **Bezeichner** und **Antwort-URL** im Abschnitt **Grundlegende SAML-Konfiguration** automatisch eingefügt:

    ![SSO-Informationen zur Domäne und zu den URLs für MOVEit Transfer – Azure AD-Integration](common/sp-identifier-reply.png)

    Geben Sie im Textfeld **Anmelde-URL** die URL ein: `https://contoso.com`.

    > [!NOTE]
    > Der Wert der **Anmelde-URL** entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Wenden Sie sich an das [Kundensupportteam von MOVEit Transfer – Azure AD-Integration](https://community.ipswitch.com/s/support), um den Wert zu erhalten. Sie können die **Dienstanbieter-Metadatendatei** über die **Metadaten-URL des Dienstanbieters** herunterladen. Dies ist weiter unten im Abschnitt **Konfigurieren des einmaligen Anmeldens für MOVEit Transfer – Azure AD-Integration** beschrieben. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **MOVEit Transfer – Azure AD-Integration einrichten** je nach Bedarf die entsprechenden URLs.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf MOVEit Transfer – Azure AD-Integration gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Option **MOVEit Transfer – Azure AD-Integration** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="configure-moveit-transfer---azure-ad-integration-sso"></a>Konfigurieren des einmaligen Anmeldens für MOVEit Transfer – Azure AD-Integration

1. Melden Sie sich bei Ihrem MOVEit Transfer-Mandanten als Administrator an.

2. Klicken Sie im linken Navigationsbereich auf **Einstellungen**.

    ![Abschnitt „Einstellungen“ auf App-Seite](./media/moveittransfer-tutorial/settings.png)

3. Klicken Sie auf den Link **Einmaliges Anmelden** (unter **Sicherheitsrichtlinien > Benutzerauthentifizierung**).

    ![App-seitige Sicherheitsrichtlinien](./media/moveittransfer-tutorial/sso.png)

4. Klicken Sie auf den Metadaten-URL-Link, um das Metadatendokument herunterzuladen.

    ![Metadaten-URL des Dienstanbieters](./media/moveittransfer-tutorial/metadata.png)
    
   * Stellen Sie sicher, dass der Wert von **entityID** mit dem Wert von **Bezeichner** im Abschnitt **Grundlegende SAML-Konfiguration** übereinstimmt.
   * Stellen Sie sicher, dass die **AssertionConsumerService**-Speicherort-URL mit der **ANTWORT-URL** im Abschnitt **Grundlegende SAML-Konfiguration** übereinstimmt.
    
     ![Einmaliges Anmelden auf App-Seite konfigurieren](./media/moveittransfer-tutorial/xml.png)

5. Klicken Sie auf die Schaltfläche **Add Identity Provider** (Identitätsanbieter hinzufügen), um einen neuen Verbundidentitätsanbieter hinzuzufügen.

    ![Identitätsanbieter hinzufügen](./media/moveittransfer-tutorial/idp.png)

6. Klicken Sie auf **Durchsuchen...**, um die Metadatendatei auszuwählen, die Sie aus dem Azure-Portal heruntergeladen haben, und klicken Sie anschließend auf **Add Identity Provider** (Identitätsanbieter hinzufügen), um die heruntergeladene Datei hochzuladen.

    ![SAML-Identitätsanbieter](./media/moveittransfer-tutorial/saml.png)

7. Wählen Sie auf der Seite **Edit Federated Identity Provider Settings...** (Einstellungen für Verbundidentitätsanbieter bearbeiten...) unter **Aktiviert** die Option **Ja** aus, und klicken Sie anschließend auf **Speichern**.

    ![Einstellungen für den Verbundidentitätsanbieter](./media/moveittransfer-tutorial/save.png)

8. Führen Sie auf der Seite **Edit Federated Identity Provider User Settings** (Benutzereinstellungen für Verbundidentitätsanbieter) die folgenden Aktionen aus:
    
    ![Bearbeiten der Einstellungen für den Verbundidentitätsanbieter](./media/moveittransfer-tutorial/attributes.png)
    
    a. Wählen Sie unter **Anmeldename** die Option **SAML NameID** (SAML-Namens-ID) aus.
    
    b. Wählen Sie als **vollständiger Name****Andere** aus, und fügen Sie im Textfeld **Attributname** den Wert `http://schemas.microsoft.com/identity/claims/displayname` ein.
    
    c. Wählen Sie als **E-Mail****Andere** aus, und fügen Sie im Textfeld **Attributname** den Wert `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` ein.
    
    d. Wählen Sie unter **Auto-create account on signon** (Konto bei der Anmeldung automatisch erstellen) die Option **Ja** aus.
    
    e. Klicken Sie auf die Schaltfläche **Save** .

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Erstellen eines Testbenutzers für MOVEit Transfer – Azure AD-Integration

In diesem Abschnitt wird in MOVEit Transfer – Azure AD-Integration ein Benutzer namens Britta Simon erstellt. MOVEit Transfer – Azure AD-Integration unterstützt die von Ihnen aktivierte Just-in-Time-Bereitstellung. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist noch kein Benutzer vorhanden, wird beim Aufrufen von MOVEit Transfer – Azure AD-Integration ein neuer Benutzer erstellt.

>[!NOTE]
>Setzen Sie sich mit dem [Supportteam von MOVEit Transfer– Azure AD-Integrations-Client](https://community.ipswitch.com/s/support) in Verbindung, wenn Sie einen Benutzer manuell erstellen müssen.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für MOVEit Transfer – Azure AD-Integration umgeleitet, wo Sie den Anmeldeablauf initiieren können. 

* Navigieren Sie direkt zur Anmelde-URL für MOVEit Transfer – Azure AD-Integration, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „MOVEit Transfer – Azure AD-Integration“ klicken, sollten Sie automatisch bei der Instanz von MOVEit Transfer – Azure AD-Integration angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von MOVEit Transfer – Azure AD-Integration können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.