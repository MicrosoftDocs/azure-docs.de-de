---
title: 'Tutorial: Azure Active Directory-Integration mit SAML SSO for Confluence by resolution GmbH | Microsoft-Dokumentation'
description: In diesem Artikel erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SAML SSO for Confluence by resolution GmbH konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: jeedes
ms.openlocfilehash: 64e358ef6c20c72b1a6a406df1e49ca5a9763b1c
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100094294"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Tutorial: Azure Active Directory-Integration mit SAML SSO for Confluence by resolution GmbH

In diesem Tutorial erfahren Sie, wie Sie SAML SSO for Confluence by resolution GmbH in Azure Active Directory (Azure AD) integrieren. Die Integration von SAML SSO for Confluence by resolution GmbH in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf SAML SSO for Confluence by resolution GmbH hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei SAML SSO for Confluence by resolution GmbH anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Abonnement für SAML SSO for Confluence by resolution GmbH, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SAML SSO for Confluence by resolution GmbH unterstützt **SP- und IDP**-initiiertes einmaliges Anmelden.

## <a name="add-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>Hinzufügen von SAML SSO for Confluence by resolution GmbH aus dem Katalog

Um die Integration von SAML SSO for Confluence by resolution GmbH in Azure AD zu konfigurieren, müssen Sie SAML SSO for Confluence by resolution GmbH aus dem Katalog zu Ihrer Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **SAML SSO for Confluence by resolution GmbH** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **SAML SSO for Confluence by resolution GmbH** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-confluence-by-resolution-gmbh"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für SAML SSO for Confluence by resolution GmbH

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit SAML SSO for Confluence by resolution GmbH mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SAML SSO for Confluence by resolution GmbH eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit SAML SSO for Confluence by resolution GmbH zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für SAML SSO for Confluence by resolution GmbH](#configure-saml-sso-for-confluence-by-resolution-gmbh-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Testbenutzers für SAML SSO for Confluence by resolution GmbH](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)** , um ein Gegenstück für Britta Simon in SAML SSO for Confluence by resolution GmbH bereitzustellen, das mit der Azure AD-Darstellung eines Benutzers verknüpft ist.
6. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **SAML SSO for Confluence by resolution GmbH** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/samlsso`

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Diese Werte erhalten Sie vom [Supportteam für den SAML SSO for Confluence by resolution GmbH-Client](https://www.resolution.de/go/support). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)


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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf SAML SSO for Confluence by resolution GmbH gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **SAML SSO for Confluence by resolution GmbH** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.


## <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-sso"></a>Konfigurieren des einmaligen Anmeldens für SAML SSO for Confluence by resolution GmbH

1. Melden Sie sich in einem anderen Webbrowserfenster beim **SAML SSO for Confluence by resolution GmbH-Verwaltungsportal** als Administrator an.

2. Fahren Sie mit dem Mauszeiger über das Zahnrad, und klicken Sie auf die **Add-Ons**.
    
    ![Screenshot, in dem das Zahnradsymbol ausgewählt und im Dropdownmenü die Option „Add-Ons“ hervorgehoben ist.](./media/saml-sso-confluence-tutorial/add-on-1.png)

3. Sie werden zur Seite „Administratorzugriff“ umgeleitet. Geben Sie das Kennwort ein, und klicken Sie auf die Schaltfläche **Bestätigen**.

    ![Der Screenshot zeigt die Seite „Administratorzugriff“ mit ausgewählter Schaltfläche „Bestätigen“.](./media/saml-sso-confluence-tutorial/add-on-2.png)

4. Klicken Sie auf der Registerkarte **ATLASSIAN MARKETPLACE** auf **Nach neuen Add-Ons suchen**. 

    ![Der Screenshot zeigt die Registerkarte „ATTLASSIAN MARKETPLACE“ mit ausgewähltem „Nach neuen Add-Ons suchen“.](./media/saml-sso-confluence-tutorial/add-on.png)

5. Suchen Sie nach **Einmalige SAML-Anmeldung (SSO) für Confluence**, und klicken Sie auf die Schaltfläche **Installieren**, um das neue SAML-Plug-In zu installieren.

    ![Der Screenshot zeigt die Seite „Nach neuen Add-Ons suchen“ mit dem Suchbegriff „Einmalige SAML-Anmeldung (SSO) für Confluence“ im Suchfeld und der ausgewählten Schaltfläche „Installieren“.](./media/saml-sso-confluence-tutorial/add-on-7.png)

6. Die Installation des Plug-Ins wird gestartet. Klicken Sie auf **Schließen**.

    ![Der Screenshot zeigt das Dialogfeld „Installing“ (Wird installiert...).](./media/saml-sso-confluence-tutorial/add-on-8.png)

    ![Screenshot: Dialogfeld „Installed and ready to go!“ (Installiert und einsatzbereit!) mit der ausgewählten Aktion „Close“ (Schließen)](./media/saml-sso-confluence-tutorial/add-on-9.png)

7.  Klicken Sie auf **Manage**.

    ![Der Screenshot zeigt die App-Seite „Einmalige SAML-Anmeldung (SSO) für Confluence“ mit ausgewählter Schaltfläche „Manage“ (Verwalten).](./media/saml-sso-confluence-tutorial/add-on-10.png)
    
8. Klicken Sie auf **Konfigurieren**, um das neue Plug-In zu konfigurieren.

    ![Der Screenshot zeigt die Seite „Manage“ (Verwalten) mit ausgewählter Schaltfläche „Configure“ (Konfigurieren).](./media/saml-sso-confluence-tutorial/add-on-11.png)

9. Dieses neue Plug-In wird auch auf der Registerkarte **BENUTZER & SICHERHEIT** angezeigt.

    ![Der Screenshot zeigt die Seite „Benutzer & Sicherheit“ mit ausgewähltem „Einmalige SAML-Anmeldung“.](./media/saml-sso-confluence-tutorial/add-on-3.png)
    
10. Klicken Sie auf der Seite **Konfiguration des SAML-SSO-Plug-Ins** auf die Schaltfläche **Add new IdP** (Neuen IdP hinzufügen), um die Einstellungen des Identitätsanbieters zu konfigurieren.

    ![Der Screenshot zeigt die Seite „Konfiguration des SAML-SSO-Plug-Ins“ mit ausgewählter Schaltfläche „Add new IdP“.](./media/saml-sso-confluence-tutorial/add-on-4.png)

11. Führen Sie auf der Seite **Choose your SAML Identity Provider** (SAML-Identitätsanbieter auswählen) die folgenden Schritte aus:

    ![Der Screenshot zeigt die Seite „Choose your SAML Identity Provider“ mit den hervorgehobenen Textfeldern „IdP Type“ (IdP-Typ), „Name“ und „Description“ (Beschreibung).](./media/saml-sso-confluence-tutorial/add-on-5-a.png)
 
    a. Legen Sie als IdP-Typ die Option **Azure AD** fest.
    
    b. Fügen Sie den **Namen** des Identitätsanbieters (z.B. Azure AD) hinzu.
    
    c. Fügen Sie eine **Beschreibung** des Identitätsanbieters (z.B. Azure AD) hinzu.
    
    d. Klicken Sie auf **Weiter**.
    
12. Klicken Sie auf der Seite **Identitätsanbieterkonfiguration** auf die Schaltfläche **Weiter**.

    ![Der Screenshot zeigt die Seite „Identitätsanbieterkonfiguration“ mit ausgewählter Schaltfläche „Weiter“.](./media/saml-sso-confluence-tutorial/add-on-5-b.png)

13. Führen Sie auf der Seite **Import SAML IdP Metadata** (SAML-IdP-Metadaten importieren) die folgenden Schritte aus:

    ![Der Screenshot zeigt die Seite „Import SAML IdP Metadata“ mit den ausgewählten Schaltflächen „Importieren“, „Datei laden“ und „Weiter“.](./media/saml-sso-confluence-tutorial/add-on-5-c.png)

    a. Klicken Sie auf die Schaltfläche **Datei laden**, und wählen Sie die in Schritt 5 heruntergeladene Metadaten-XML-Datei aus.

    b. Klicken Sie auf die Schaltfläche **Importieren**.
    
    c. Warten Sie kurz, bis der Import erfolgreich ausgeführt wurde.
    
    d. Klicken Sie auf die Schaltfläche **Weiter**.
    
14. Klicken Sie auf der Seite **User ID attribute and transformation** (Benutzer-ID-Attribut und Transformation) auf die Schaltfläche **Weiter**.

    ![Der Screenshot zeigt die Seite „User ID attribute and transformation“ mit ausgewählter Schaltfläche „Weiter“.](./media/saml-sso-confluence-tutorial/add-on-5-d.png)
    
15. Klicken Sie auf der Seite **User creation and update** (Benutzererstellung und -aktualisierung) auf **Save & Next** (Speichern und weiter), um die Einstellungen zu speichern.   
    
    ![Der Screenshot zeigt die Seite „User creation and update“ mit ausgewählter Option „Save and Next“.](./media/saml-sso-confluence-tutorial/add-on-6-a.png)
    
16. Klicken Sie auf der Seite **Testen Ihrer Einstellungen** auf **Skip test & configure manually** (Test überspringen und manuell konfigurieren), um den Benutzertest vorerst zu überspringen. Dieser wird im nächsten Abschnitt durchgeführt und erfordert einige Einstellungen im Azure-Portal. 
    
    ![Der Screenshot zeigt die Seite „Testen Ihrer Einstellungen“ mit ausgewählter Schaltfläche „Skip test & configure manually“.](./media/saml-sso-confluence-tutorial/add-on-6-b.png)
    
17. Klicken Sie im angezeigten Dialogfeld **Das Überspringen des Tests bedeutet...** auf **OK**.
    
    ![Einmaliges Anmelden konfigurieren](./media/saml-sso-confluence-tutorial/add-on-6-c.png)


### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>Erstellen eines Testbenutzers für SAML SSO for Confluence by resolution GmbH

Um Azure AD-Benutzern die Anmeldung bei SAML SSO for Confluence by resolution GmbH zu ermöglichen, müssen diese in SAML SSO for Confluence by resolution GmbH bereitgestellt werden.  
Die Bereitstellung in SAML SSO for Confluence by resolution GmbH ist ein manueller Vorgang.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei der Unternehmenswebsite von SAML SSO for Confluence by resolution GmbH als Administrator an.

2. Fahren Sie mit dem Mauszeiger über das Zahnrad, und klicken Sie auf **Benutzerverwaltung**.

    ![Screenshot, in dem das Zahnradsymbol ausgewählt und im Menü die Option „Benutzerverwaltung“ hervorgehoben ist.](./media/saml-sso-confluence-tutorial/user-1.png) 

3. Klicken Sie im Abschnitt „Benutzer“ auf die Registerkarte **Benutzer hinzufügen**. Führen Sie auf der Dialogfeldseite **Benutzer hinzufügen** die folgenden Schritte aus:

    ![Mitarbeiter hinzufügen](./media/saml-sso-confluence-tutorial/user-2.png) 

    a. Geben Sie im Textfeld **Benutzername** die E-Mail-Adresse des Benutzers, z.B. von Britta Simon, ein.

    b. Geben Sie im Textfeld **Vollständiger Name** den vollständigen Namen des Benutzers, z.B. „Britta Simon“, ein.

    c. Geben Sie im Textfeld **E-Mail-Adresse** die E-Mail-Adresse des Benutzers, z.B. Brittasimon@contoso.com, ein.

    d. Geben Sie im Textfeld **Kennwort** das Kennwort von Britta Simon ein.

    e. Klicken Sie auf **Kennwort bestätigen**, um das Kennwort erneut einzugeben.
    
    f. Klicken Sie auf die Schaltfläche **Hinzufügen**.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für SAML SSO for Confluence by resolution GmbH weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die Anmelde-URL für SAML SSO for Confluence by resolution GmbH auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Instanz von SAML SSO for Confluence by resolution GmbH angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „SAML SSO for Confluence by resolution GmbH“ in „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Instanz von SAML SSO for Confluence by resolution GmbH angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von SAML SSO for Confluence by resolution GmbH können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.
