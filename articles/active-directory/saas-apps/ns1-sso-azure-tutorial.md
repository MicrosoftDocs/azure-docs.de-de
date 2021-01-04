---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit NS1 SSO for Azure | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und NS1 SSO for Azure konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.openlocfilehash: a7fad0af26d15bc77a52d55309c594937ba3be13
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518618"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ns1-sso-for-azure"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit NS1 SSO for Azure

In diesem Tutorial erfahren Sie, wie Sie NS1 SSO for Azure in Azure Active Directory (Azure AD) integrieren. Die Integration von NS1 SSO for Azure in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf NS1 SSO for Azure hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei NS1 SSO for Azure anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps (Software as a Service) mit Azure AD finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein NS1 SSO for Azure-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* NS1 SSO for Azure unterstützt SP- und IDP-initiiertes einmaliges Anmelden.
* Nach dem Konfigurieren von NS1 SSO for Azure können Sie die Sitzungssteuerung erzwingen. Diese schützt in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.


## <a name="add-ns1-sso-for-azure-from-the-gallery"></a>Hinzufügen von NS1 SSO for Azure aus dem Katalog

Zum Konfigurieren der Integration von NS1 SSO for Azure in Azure AD müssen Sie NS1 SSO for Azure aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **NS1 SSO for Azure** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **NS1 SSO for Azure** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ns1-sso-for-azure"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für NS1 SSO for Azure

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit NS1 SSO for Azure mithilfe eines Testbenutzers mit dem Namen **B. Simon** . Damit einmaliges Anmelden funktioniert, richten Sie eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in NS1 SSO for Azure ein.

Nachfolgend sind die allgemeinen Schritte zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit NS1 SSO for Azure aufgeführt:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen

    a. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.

    b. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für NS1 SSO for Azure](#configure-ns1-sso-for-azure-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren

    a. **[Erstellen eines NS1 SSO for Azure-Testbenutzers](#create-an-ns1-sso-for-azure-test-user)** , um eine Entsprechung von B. Simon in NS1 SSO for Azure zu erhalten Diese Entsprechung ist mit ihrer Azure AD-Darstellung verknüpft.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **NS1 SSO for Azure** zum Abschnitt **Verwalten** . Wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol für **Grundlegende SAML-Konfiguration** aus, um die Einstellungen zu bearbeiten.

   ![Screenshot: Seite „Einmaliges Anmelden (SSO) mit SAML einrichten“ mit hervorgehobenem Stiftsymbol](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP** -initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** die folgende URL ein: `https://api.nsone.net/saml/metadata`.

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://api.nsone.net/saml/sso/<ssoid>`.

1. Wählen Sie **Zusätzliche URLs festlegen** aus, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten** Modus konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** die folgende URL ein: `https://my.nsone.net/#/login/sso`.

    > [!NOTE]
    > Der Wert der Antwort-URL entspricht nicht dem tatsächlichen Wert. Aktualisieren Sie den Wert für die Antwort-URL mit der richtigen Antwort-URL. Den Wert erhalten Sie vom [Supportteam für den NS1 SSO for Azure-Client](mailto:techops@nsone.net). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die NS1 SSO for Azure-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Sie können die Werte dieser Attribute im Abschnitt **Benutzerattribute und Ansprüche** auf der Anwendungsintegrationsseite verwalten. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol aus, um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![Screenshot: Abschnitt „Benutzerattribute und Ansprüche“ mit hervorgehobenem Stiftsymbol](./media/ns1-sso-for-azure-tutorial/attribute-edit-option.png)

1. Wählen Sie den Attributnamen aus, um den Anspruch zu bearbeiten.

    ![Screenshot: Abschnitt „Benutzerattribute und Ansprüche“ mit hervorgehobenem Attributnamen](./media/ns1-sso-for-azure-tutorial/attribute-claim-edit.png)

1. Wählen Sie **Transformation** aus.

    ![Screenshot: Abschnitt „Anspruch verwalten“ mit hervorgehobener Option „Transformation“](./media/ns1-sso-for-azure-tutorial/prefix-edit.png)

1. Führen Sie im Abschnitt **Transformation verwalten** die folgenden Schritte aus:

    ![Screenshot: Abschnitt „Transformation verwalten“ mit verschiedenen hervorgehobenen Feldern](./media/ns1-sso-for-azure-tutorial/prefix-added.png)

    1. Wählen Sie unter **Transformation** die Option **ExactMailPrefix()** aus.

    1. Wählen Sie unter **Parameter 1** die Option **user.userprincipalname** aus.

    1. Wählen Sie **Hinzufügen** .

    1. Wählen Sie **Speichern** aus.

1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die Kopierschaltfläche aus. Dadurch wird die **App-Verbundmetadaten-URL** kopiert und auf Ihrem Computer gespeichert.

    ![Screenshot: „SAML-Signaturzertifikat“ mit hervorgehobener Kopierschaltfläche](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer namens B.Simon.

1. Wählen Sie im Azure-Portal im linken Bereich **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:

   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.
   1. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf NS1 SSO for Azure gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **NS1 SSO for Azure** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.

   ![Screenshot: Abschnitt „Verwalten“ mit hervorgehobener Option „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Klicken Sie auf **Benutzer hinzufügen** . Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Screenshot: Seite „Benutzer und Gruppen“ mit hervorgehobener Option zum Hinzufügen eines Benutzers](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ die Option **B.Simon** aus. Wählen Sie dann unten auf dem Bildschirm die Schaltfläche **Auswählen** aus.
1. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Wählen Sie dann unten auf dem Bildschirm die Schaltfläche **Auswählen** aus.
1. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

## <a name="configure-ns1-sso-for-azure-sso"></a>Konfigurieren des einmaligen Anmeldens für NS1 SSO for Azure

Zum Konfigurieren des einmaligen Anmeldens aufseiten von „NS1 SSO for Azure“ müssen Sie die App-Verbundmetadaten-URL an das [Supportteam von NS1 SSO for Azure](mailto:techops@nsone.net) senden. Das Team konfiguriert diese Einstellung, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-an-ns1-sso-for-azure-test-user"></a>Erstellen eines NS1 SSO for Azure-Testbenutzers

In diesem Abschnitt erstellen Sie in NS1 SSO for Azure einen Benutzer namens B. Simon. Wenden Sie sich an das Supportteam von NS1 SSO for Azure, um die Benutzer auf der NS1 SSO for Azure-Plattform hinzuzufügen. Sie können einmaliges Anmelden erst verwenden, wenn Sie Benutzer erstellt und aktiviert haben.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich die Kachel „NS1 SSO for Azure“ auswählen, sollten Sie automatisch bei der NS1 SSO for Azure-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Tutorials zur Integration von SaaS-Anwendungen in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [NS1 SSO for Azure mit Azure AD ausprobieren](https://aad.portal.azure.com/)

- [Was ist Sitzungssteuerung in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)