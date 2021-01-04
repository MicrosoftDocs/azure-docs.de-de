---
title: 'Tutorial: Integration von Azure Active Directory und RStudio Connect SAML Authentication | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und RStudio Connect SAML Authentication konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/21/2020
ms.author: jeedes
ms.openlocfilehash: 84e8c7fc1d2655ea0685ac79841a9c467bf766cf
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182390"
---
# <a name="tutorial-azure-active-directory-integration-with-rstudio-connect-saml-authentication"></a>Tutorial: Integration von Azure Active Directory und RStudio Connect SAML Authentication

In diesem Tutorial erfahren Sie, wie Sie RStudio Connect SAML Authentication in Azure Active Directory (Azure AD) integrieren.
Die Integration von RStudio Connect SAML Authentication in Azure AD hat folgende Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf RStudio Connect SAML Authentication hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei RStudio Connect SAML Authentication anzumelden (einmaliges Anmelden, Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit RStudio Connect SAML Authentication konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* RStudio Connect SAML Authentication. Wir bieten eine [kostenlose 45-tägige Evaluierungsversion](https://www.rstudio.com/products/connect/).

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* RStudio Connect SAML Authentication unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

* Für RStudio Connect SAML Authentication wird die **Just-In-Time**-Benutzerbereitstellung unterstützt.

## <a name="adding-rstudio-connect-saml-authentication-from-the-gallery"></a>Hinzufügen von RStudio Connect SAML Authentication aus dem Katalog

Um die Integration von RStudio Connect SAML Authentication in Azure AD zu konfigurieren, müssen Sie RStudio Connect SAML Authentication über den Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **RStudio Connect SAML Authentication** in das Suchfeld ein.
1. Wählen Sie **RStudio Connect SAML Authentication** im Ergebnisbereich aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-rstudio-connect-saml-authentication"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für RStudio Connect SAML Authentication

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit RStudio Connect SAML Authentication mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in RStudio Connect SAML Authentication eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD für RStudio Connect SAML Authentication zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
2. **[Konfigurieren des einmaligen Anmeldens für RStudio Connect SAML Authentication](#configure-rstudio-connect-saml-authentication-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    * **[Erstellen eines RStudio Connect SAML Authentication-Testbenutzers](#create-rstudio-connect-saml-authentication-test-user)** , um in RStudio Connect SAML Authentication eine Entsprechung von Britta Simon zu erhalten, die mit der Benutzerdarstellung in Azure AD verknüpft ist.
3. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **RStudio Connect SAML Authentication** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten, und ersetzen Sie dabei `<example.com>` durch die Serveradresse und den Port von RStudio Connect SAML Authentication:

    ![SSO-Informationen zur Domäne und zu den URLs für RStudio Connect SAML Authentication](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<example.com>/__login__/saml`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<example.com>/__login__/saml/acs`

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    ![Metadatenupload für RStudio Connect SAML Authentication](common/metadata-upload-additional-signon.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<example.com>/`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Diese Angaben werden durch die Serveradresse von RStudio Connect SAML Authentication (in den Beispielen oben `https://example.com`) bestimmt. Wenden Sie sich bei Problemen an das [Supportteam von RStudio Connect SAML Authentication](mailto:support@rstudio.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

6. Die RStudio Connect SAML Authentication-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. **nameidentifier** ist hier **user.userprincipalname** zugeordnet. Die RStudio Connect SAML Authentication-Anwendung erwartet, dass **nameidentifier** der Wert **user.mail** zugeordnet ist. Sie müssen die Attributzuordnung daher entsprechend ändern, indem Sie auf das Symbol **Bearbeiten** klicken.

    ![image](common/edit-attribute.png)

7. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf RStudio Connect SAML Authentication gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **RStudio Connect SAML Authentication** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-rstudio-connect-saml-authentication-sso"></a>Konfigurieren des einmaligen Anmeldens für RStudio Connect SAML Authentication

Um das einmalige Anmelden für **RStudio Connect SAML Authentication** zu konfigurieren, müssen Sie die **Verbundmetadaten-URL der App** und die **Serveradresse** verwenden, die oben bereits verwendet wurden. Dies erfolgt in der Konfigurationsdatei für RStudio Connect SAML Authentication unter `/etc/rstudio-connect.rstudio-connect.gcfg`.

Im Folgenden eine Beispielkonfigurationsdatei:

```
[Server]
SenderEmail =

; Important! The user-facing URL of your RStudio Connect SAML Authentication server.
Address = 

[Http]
Listen = :3939

[Authentication]
Provider = saml

[SAML]
Logging = true

; Important! The URL where your IdP hosts the SAML metadata or the path to a local copy of it placed in the RStudio Connect SAML Authentication server.
IdPMetaData = 

IdPAttributeProfile = azure
SSOInitiated = IdPAndSP
```

Speichern Sie die **Serveradresse** im `Server.Address`-Wert und die **Verbundmetadaten-URL der App** im `SAML.IdPMetaData`-Wert. Beachten Sie, dass in dieser Beispielkonfiguration eine unverschlüsselte HTTP-Verbindung verwendet wird, Azure AD jedoch die Verwendung einer verschlüsselten HTTPS-Verbindung erfordert. Sie können entweder einen vorgeschalteten [Reverseproxy](https://docs.rstudio.com/connect/admin/proxy/) für RStudio Connect SAML Authentication verwenden oder RStudio Connect SAML Authentication für die [direkte Verwendung von HTTPS](https://docs.rstudio.com/connect/admin/appendix/configuration/#HTTPS) konfigurieren. 

Falls Probleme bei der Konfiguration auftreten, können Sie das [Administratorhandbuch zu RStudio Connect SAML Authentication](https://docs.rstudio.com/connect/admin/authentication/saml/) lesen oder das [RStudio-Supportteam](mailto:support@rstudio.com) per E-Mail um Unterstützung bitten.

### <a name="create-rstudio-connect-saml-authentication-test-user"></a>Erstellen eines RStudio Connect SAML Authentication-Testbenutzers

In diesem Abschnitt wird in RStudio Connect SAML Authentication ein Benutzer namens Britta Simon erstellt. RStudio Connect SAML Authentication unterstützt die Just-In-Time-Benutzerbereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Falls ein Benutzer nicht bereits in RStudio Connect SAML Authentication vorhanden ist, wird beim Versuch, auf RStudio Connect SAML Authentication zuzugreifen, ein neuer Benutzer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Es wird eine Umleitung zur Anmelde-URL von RStudio Connect SAML Authentication durchgeführt, damit Sie den Anmeldeablauf initiieren können.  

* Navigieren Sie direkt zur Anmelde-URL von RStudio Connect SAML Authentication, und initiieren Sie den Anmeldeablauf auf dieser Seite.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Sie sollten automatisch bei der RStudio Connect SAML Authentication-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Zugriffsbereich verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „RStudio Connect SAML Authentication“ im Zugriffsbereich passiert Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeablaufs zur Anmeldeseite der Anwendung weitergeleitet, und wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der RStudio Connect SAML Authentication-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von RStudio Connect SAML Authentication können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.