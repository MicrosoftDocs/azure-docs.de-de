---
title: 'Tutorial: Azure Active Directory-Integration mit Replicon | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Replicon konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: dada52dcf66db2f0d0e40f3dbf6d1a16560c2754
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515026"
---
# <a name="tutorial-integrate-replicon-with-azure-active-directory"></a>Tutorial: Integrieren von Replicon in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Replicon in Azure Active Directory (Azure AD) integrieren. Bei der Integration von Replicon in Azure AD haben Sie folgende Möglichkeiten:

* Sie können in Azure AD steuern, wer Zugriff auf Replicon hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Replicon anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie kein Abonnement besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige kostenlose Testversion erhalten.
* Replicon-Abonnement, für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung. Replicon unterstützt **SP** -initiiertes einmaliges Anmelden.

## <a name="adding-replicon-from-the-gallery"></a>Hinzufügen von Replicon aus dem Katalog

Zum Konfigurieren der Integration von Replicon in Azure AD müssen Sie Replicon über den Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Replicon** in das Suchfeld ein.
1. Wählen Sie **Replicon** im Ergebnisbereich aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Replicon mithilfe eines Testbenutzers mit dem Namen **B. Simon** . Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Replicon eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Replicon müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Replicon](#configure-replicon-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
5. **[Erstellen eines Replicon-Testbenutzers](#create-replicon-test-user)** , um ein Pendant von B. Simon in Replicon zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Replicon** den Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie auf der Seite **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    1. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://global.replicon.com/!/saml2/<client name>/sp-sso/post`

    1. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein: `https://global.replicon.com/!/saml2/<client name>`.

    1. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://global.replicon.com/!/saml2/<client name>/sso/post`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL, den tatsächlichen Bezeichner und die tatsächliche Antwort-URL. Wenden Sie sich an das [Clientsupportteam von Replicon](https://www.replicon.com/customerzone/contact-support), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Klicken Sie auf das Bearbeitungssymbol (Stift) für **SAML-Signaturzertifikat** , um die Einstellungen zu bearbeiten.

    ![Signaturalgorithmus](common/signing-algorithm.png)

    1. Wählen Sie unter **Signaturoption** die Option **SAML-Assertion signieren** aus.

    1. Wählen Sie unter **Signaturalgorithmus** den Eintrag **SHA-256** aus.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML** , und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

   ![Downloadlink für das Zertifikat](common/metadataxml.png)

### <a name="configure-replicon-sso"></a>Konfigurieren des einmaligen Anmeldens mit Replicon

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Replicon-Unternehmenswebsite als Administrator an.

2. Um SAML 2.0 zu konfigurieren, führen Sie die folgenden Schritte aus:

    ![SAML-Authentifizierung aktivieren](./media/replicon-tutorial/ic777805.png "Klicken Sie auf „SAML-Authentifizierung aktivieren“.")

    a. Zum Anzeigen des Dialogfelds **EnableSAML Authentication2** fügen Sie nach dem Unternehmensschlüssel Folgendes an Ihre URL an: `/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * Das ist das Schema der vollständigen URL: `https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Klicken Sie auf **+** , um den Abschnitt **v20Configuration** zu erweitern.

   c. Klicken Sie auf **+** , um den Abschnitt **metaDataConfiguration** zu erweitern.

   d. Wählen Sie **SHA256** für xmlSignatureAlgorithm aus.

   e. Klicken Sie auf **Datei auswählen** , um die Metadaten-XML-Datei Ihres Identitätsanbieters auszuwählen. Klicken Sie dann auf **Senden** .

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `BrittaSimon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert aus dem Feld **Kennwort** .
   1. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Replicon gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Replicon** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen** .
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen** .
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

### <a name="create-replicon-test-user"></a>Erstellen eines Replicon-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens B. Simon in Replicon.

**Wenn Sie einen Benutzer manuell erstellen möchten, führen Sie die folgenden Schritte aus:**

1. Melden Sie sich in einem Webbrowserfenster bei der Replicon-Unternehmenswebsite als Administrator an.

2. Wechseln Sie zu **Verwaltung \> Benutzer** .

    ![Benutzer](./media/replicon-tutorial/ic777806.png "Benutzer")

3. Klicken Sie auf **+Benutzer** .

    ![Benutzer hinzufügen](./media/replicon-tutorial/ic777807.png "Benutzer hinzufügen")

4. Führen Sie im Abschnitt **Benutzerprofil** die folgenden Schritte aus:

    ![Benutzerprofil](./media/replicon-tutorial/ic777808.png "Benutzerprofil")

    a. Geben Sie in das Textfeld **Anmeldename** die E-Mail-Adresse des Azure AD-Benutzers ein, den Sie bereitstellen möchten, etwa `B.Simon@contoso.com`.

    > [!NOTE]
    > „Anmeldename“ muss der E-Mail-Adresse des Benutzers in Azure AD entsprechen.

    b. Als **Authentifizierungstyp** wählen Sie **SSO** aus.

    c. Legen „Authentifizierung-ID“ auf den gleichen Wert wie „Anmeldename“ fest (die E-Mail-Adresse des Benutzers in Azure AD).

    d. Geben Sie in das Textfeld **Abteilung** die Abteilung des Benutzers ein.

    e. Wählen Sie als **Mitarbeitertyp** die Option **Administrator** aus.

    f. Klicken Sie auf **Benutzerprofil speichern** .

> [!NOTE]
> Sie können Azure AD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Replicon-Benutzerkonten oder mithilfe der von Replicon bereitgestellten APIs erstellen.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens

Wenn Sie im Zugriffsbereich auf die Kachel „Replicon“ klicken, sollten Sie automatisch bei der Replicon-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)