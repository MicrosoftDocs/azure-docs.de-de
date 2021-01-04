---
title: 'Tutorial: Azure Active Directory-Integration von Mimecast Personal Portal | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Mimecast Personal Portal konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2020
ms.author: jeedes
ms.openlocfilehash: ad7b5b97149d38b64d75f5a02cd0aa776893e832
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92522562"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mimecast-personal-portal"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Mimecast Personal Portal

In diesem Tutorial erfahren Sie, wie Sie Mimecast Personal Portal in Azure Active Directory (Azure AD) integrieren. Die Integration von Mimecast Personal Portal in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf Mimecast Personal Portal hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Mimecast Personal Portal anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Mimecast Personal Portal-Abonnement, für das das einmalige Anmelden (SSO) aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Mimecast Personal Portal unterstützt **SP- und IDP** -initiiertes einmaliges Anmelden.
* Nach dem Konfigurieren von Mimecast Personal Portal können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Hinzufügen von Mimecast Personal Portal aus dem Katalog

Zum Konfigurieren der Integration von Mimecast Personal Portal in Azure AD müssen Sie Mimecast Personal Portal aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Mimecast Personal Portal** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Mimecast Personal Portal** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on-for-mimecast-personal-portal"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Mimecast Personal Portal

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Mimecast Personal Portal mithilfe einer Testbenutzerin mit dem Namen **B. Simon** . Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Mimecast Personal Portal eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Mimecast Personal Portal die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Mimecast Personal Portal](#configure-mimecast-personal-portal-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Mimecast Personal Portal-Testbenutzers](#create-mimecast-personal-portal-test-user)** , um eine Entsprechung von B.Simon in Mimecast Personal Portal zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Mimecast Personal Portal** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im IDP-initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Identifier** (Bezeichner) eine URL nach folgendem Muster ein:

    | Region  |  Wert | 
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | USA   | `https://us-api.mimecast.com/sso/<accountcode>`|
    | Südafrika    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Australien       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Offshore        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    > [!NOTE]
    > Sie finden den `accountcode`-Wert in Mimecast Personal Portal unter **Konto** > **Einstellungen** > **Kontocode** . Fügen Sie den `accountcode` an den Bezeichner an.

    b. Geben Sie im Textfeld **Antwort-URL** eine URL folgendermaßen ein:

    | Region  |  Wert |
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | USA   | `https://us-api.mimecast.com/login/saml`|
    | Südafrika    | `https://za-api.mimecast.com/login/saml`|
    | Australien       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

1. Wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL wie die Folgende ein:

    | Region  |  Wert |
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | USA   | `https://us-api.mimecast.com/login/saml`|
    | Südafrika    | `https://za-api.mimecast.com/login/saml`|
    | Australien       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

1. Klicken Sie auf **Speichern** .

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

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

In diesem Abschnitt ermöglichen Sie B.Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Mimecast Personal Portal gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Mimecast Personal Portal** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen** .
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen** .
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

## <a name="configure-mimecast-personal-portal-sso"></a>Konfigurieren des einmaligen Anmeldens für Mimecast Personal Portal

1. Melden Sie sich in einem anderen Webbrowserfenster bei Mimecast Administration Console an.

1. Navigieren Sie zu **Verwaltung** > **Dienste** > **Anwendungen** .

    ![Screenshot: Fenster „Mimecast“, in dem die Option „Applications“ (Anwendungen) ausgewählt ist](./media/mimecast-personal-portal-tutorial/services.png)

1. Klicken Sie auf die Registerkarte **Authentifizierungsprofile** .
    
    ![Screenshot: Registerkarte „Applications“ (Anwendungen), auf der „Authentication Profiles“ (Authentifizierungsprofile) ausgewählt ist](./media/mimecast-personal-portal-tutorial/authentication-profiles.png)

1. Klicken Sie auf die Registerkarte **Neues Authentifizierungsprofil** .

    ![Screenshot, auf dem „New Authentication Profile“ (Neues Authentifizierungsprofil) ausgewählt ist](./media/mimecast-personal-portal-tutorial/new-authenticatio-profile.png)

1. Geben Sie im Textfeld **Beschreibung** eine gültige Beschreibung ein, und wählen Sie **SAML-Authentifizierung für Mimecast Personal erzwingen** aus.

    ![Screenshot, auf dem „New Authentication Profile“ (Neues Authentifizierungsprofil) ausgewählt ist](./media/mimecast-personal-portal-tutorial/selecting-personal-portal.png)

1. Führen Sie auf der Seite **SAML-Konfiguration für Mimecast Personal** die folgenden Schritte aus:

    ![Screenshot, auf dem gezeigt wird, wo „Enforce SAML Authentication for Administration Console“ (SAML-Authentifizierung für Verwaltungskonsole erzwingen) ausgewählt wird](./media/mimecast-personal-portal-tutorial/sso-settings.png)

    a. Wählen Sie als **Anbieter** in der Dropdownliste **Azure Active Directory** aus.

    b. Fügen Sie im Textfeld **Metadaten-URL** den Wert der **Verbundmetadaten-URL der App** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Klicken Sie auf **Importieren** . Nachdem Sie die Metadaten-URL importiert haben, werden die Felder automatisch aufgefüllt, sodass keine Aktion für diese Felder ausgeführt werden muss.

    d. Deaktivieren Sie die Kontrollkästchen **Kennwortgeschützten Kontext verwenden** und **Integrierten Authentifizierungskontext verwenden** .

    e. Klicken Sie auf **Speichern** .

### <a name="create-mimecast-personal-portal-test-user"></a>Erstellen eines Mimecast Personal Portal-Testbenutzers

1. Melden Sie sich in einem anderen Webbrowserfenster bei Mimecast Administration Console an.

1. Navigieren Sie zu **Verwaltung** > **Verzeichnisse** > **Interne Verzeichnisse** .

    ![Screenshot: SAML-Konfiguration für Mimecast Personal Portal, wo Sie die beschriebenen Werte eingeben können](./media/mimecast-personal-portal-tutorial/internal-directories.png)

1. Wählen Sie Ihre Domäne aus, wenn sie unten angegeben ist, und erstellen Sie andernfalls eine neue Domäne, indem Sie auf **Neue Domäne** klicken.

    ![Screenshot: Fenster „Mimecast“, in dem die Option „Internal Directories“ (Interne Verzeichnisse) ausgewählt ist](./media/mimecast-personal-portal-tutorial/domain-name.png)

1. Klicken Sie auf die Registerkarte **Neue Adresse** .

    ![Screenshot: Ausgewählte Domäne](./media/mimecast-personal-portal-tutorial/new-address.png)

1. Geben Sie auf der folgenden Seite die erforderlichen Benutzerinformationen an:

    ![Screenshot: Seite, auf der Sie die beschriebenen Werte eingeben können](./media/mimecast-personal-portal-tutorial/user-information.png)

    a. Geben Sie im Textfeld **E-Mail-Adresse** die E-Mail-Adresse des Benutzers ein, z. B. `B.Simon@yourdomainname.com`.

    b. Geben Sie im Textfeld **Globaler Name** den **Vollständigen Namen** des Benutzers ein.

    c. Geben Sie in den Textfeldern **Kennwort** und **Kennwort bestätigen** das Kennwort des Benutzers ein.

    d. Aktivieren Sie das Kontrollkästchen **Änderung bei Anmeldung erzwingen** .

    e. Klicken Sie auf **Speichern** .

    f. Um dem Benutzer Rollen zuzuweisen, klicken Sie auf **Rollenbearbeitung** , und weisen Sie dem Benutzer die erforderliche Rolle gemäß den Anforderungen Ihrer Organisation zu.

    ![Screenshot: Adresseinstellungen, in denen Sie auf „Role Edit“ (Rollenbearbeitung) klicken können](./media/mimecast-personal-portal-tutorial/assign-role.png)


## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Mimecast Personal Portal“ klicken, sollten Sie automatisch bei Ihrer Mimecast Personal Portal-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [Testen von Mimecast Personal Portal mit Azure AD](https://aad.portal.azure.com/)

- [Was ist Sitzungssteuerung in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Schützen von Mimecast Personal Portal mit erweiterter Sichtbarkeit und komplexeren Kontrollen](/cloud-app-security/proxy-intro-aad)