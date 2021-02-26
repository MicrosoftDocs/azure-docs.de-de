---
title: 'Tutorial: Azure Active Directory-Integration in Zoho One| Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden für Azure Active Directory und Zoho One konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: 225d6b13c882566a6b71c5809d67955a27561ed6
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99821057"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Tutorial: Azure Active Directory-Integration in Zoho One

In diesem Tutorial erfahren Sie, wie Sie Zoho One in Azure Active Directory (Azure AD) integrieren. Die Integration von Zoho One in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Zoho One hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Zoho One anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in Zoho One konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Zoho One-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Zoho One unterstützt **SP**- und **IDP**-initiiertes einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-zoho-one-from-the-gallery"></a>Hinzufügen von Zoho One aus dem Katalog

Zum Konfigurieren der Integration von Zoho One in Azure AD müssen Sie Zoho One aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Zoho One** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Zoho One** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-zoho-one"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Zoho One

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Zoho One mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Zoho One eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Zoho One die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Zoho One](#configure-zoho-one-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Zoho One-Testbenutzers](#create-zoho-one-test-user)** , um eine Entsprechung von B. Simon in Zoho One zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Zoho One** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** die URL `one.zoho.com` ein.

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > Der vorherige Wert für die **Antwort-URL** entspricht nicht dem tatsächlichen Wert. Den Wert `<saml-identifier>` erhalten Sie in Schritt 4 des Abschnitts **Konfigurieren des einmaligen Anmeldens für Zoho One**. Dies wird weiter unten in diesem Tutorial beschrieben.

    c. Klicken Sie auf **Zusätzliche URLs festlegen**.

    d. Geben Sie im Textfeld **Vermittlungsstatus** die URL ein: `https://one.zoho.com`

5. Wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten, führen Sie die folgenden Schritte durch:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > Der vorherige Wert für die **Anmelde-URL** entspricht nicht dem tatsächlichen Wert. Sie müssen den Wert durch die tatsächliche Anmelde-URL im Abschnitt **Konfigurieren des einmaligen Anmeldens für Zoho One** ersetzen. Dies wird weiter unten in diesem Tutorial beschrieben. 

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

7. Kopieren Sie im Abschnitt **Zoho One einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Zoho One gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Zoho One** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="configure-zoho-one-sso"></a>Konfigurieren des einmaligen Anmeldens für Zoho One

1. Melden Sie sich in einem anderen Webbrowserfenster auf der Zoho One-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf der Registerkarte **Organisation** (Organisation) unter **SAML Authentication** (SAML-Authentifizierung) auf **Setup** (Einrichten).

    ![Organisation in Zoho One](./media/zoho-one-tutorial/set-up.png)

3. Führen Sie auf der Popupseite die folgenden Schritte aus:

    ![Anmelden und Abmelden in Zoho One](./media/zoho-one-tutorial/save.png)

    a. Fügen Sie in das Textfeld **Sign-in URL** (Anmelde-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    b. Fügen Sie in das Textfeld **Sign-out URL** (Abmelde-URL) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Klicken Sie auf **Browse** (Durchsuchen), um das **Zertifikat (Base64)** hochzuladen, das Sie zuvor aus dem Azure-Portal heruntergeladen haben.

    d. Klicken Sie auf **Speichern**.

4. Nachdem Sie die eingerichtete SAML-Authentifizierung gespeichert haben, kopieren Sie den **SAML-Identifier**-Wert und fügen die **Antwort-URL** anstelle von `<saml-identifier>` an (beispielsweise `https://accounts.zoho.com/samlresponse/one.zoho.com`). Anschließend fügen Sie den erzeugten Wert im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Antwort-URL** ein.

    ![SAML in Zoho One](./media/zoho-one-tutorial/saml-identifier.png)

5. Öffnen Sie die Registerkarte **Domains** (Domänen), und klicken Sie auf **Add Domain** (Domäne hinzufügen).

    ![Domäne in Zoho One](./media/zoho-one-tutorial/add-domain.png)

6. Führen Sie auf der Seite **Add Domain** (Domäne hinzufügen) die folgenden Schritte aus:

    ![Hinzufügen einer Domäne in Zoho One](./media/zoho-one-tutorial/add-domain-name.png)

    a. Geben Sie im Textfeld **Domain Name** (Domänennamen) eine Domäne ein, z. B. „contoso.com“.

    b. Klicken Sie auf **Hinzufügen**.

    >[!Note]
    >Überprüfen Sie Ihre Domäne nach dem Hinzufügen mithilfe [dieser Schritte](https://www.zoho.com/one/help/admin-guide/domain-verification.html). Nachdem die Domäne überprüft wurde, geben Sie Ihren Domänennamen im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** unter **Anmelde-URL** ein.

### <a name="create-zoho-one-test-user"></a>Erstellen eines Zoho One-Testbenutzers

Damit sich Azure AD-Benutzer bei Zoho One anmelden können, müssen sie in Zoho One bereitgestellt werden. Im Fall von Zoho One muss die Bereitstellung manuell ausgeführt werden.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Zoho One als Sicherheitsadministrator an.

2. Klicken Sie auf der Registerkarte **Users** (Benutzer) auf das **Benutzerlogo**.

    ![Benutzer in Zoho One](./media/zoho-one-tutorial/user.png)

3. Führen Sie auf der Seite **Benutzer hinzufügen** die folgenden Schritte aus:

    ![Hinzufügen eines Benutzers in Zoho One](./media/zoho-one-tutorial/add-user.png)
    
    a. Geben Sie im Textfeld **Name** (Name) den Namen des Benutzers ein, z.B. **Britta Simon**.
    
    b. Geben Sie im Textfeld **Email Address** (E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein, z.B. brittasimon@contoso.com.

    >[!Note]
    >Wählen Sie aus der Domänenliste Ihre überprüfte Domäne aus.

    c. Klicken Sie auf **Hinzufügen**.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Zoho One weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die Zoho One-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Zoho One-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Zoho One“ in „Meine Apps“ geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Zoho One-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Zoho One können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.
