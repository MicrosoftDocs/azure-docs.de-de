---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit ADP | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und ADP konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.openlocfilehash: 0d78f2c8da338a92ef88734371647a48ddb190c3
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591207"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit ADP

In diesem Tutorial erfahren Sie, wie Sie ADP in Azure Active Directory (Azure AD) integrieren. Die Integration von ADP in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf ADP hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei ADP anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* ADP-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* ADP unterstützt **IDP**-initiiertes einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="adding-adp-from-the-gallery"></a>Hinzufügen von ADP über den Katalog

Zum Konfigurieren der Integration von ADP in Azure AD müssen Sie ADP aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **ADP** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **ADP** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on-for-adp"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für ADP

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit ADP mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in ADP eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit ADP müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für ADP](#configure-adp-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines ADP-Testbenutzers](#create-adp-test-user)** , um in ADP eine Entsprechung von B. Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
3. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **ADP** auf die Registerkarte **Eigenschaften**, und führen Sie die folgenden Schritte aus: 

    ![Eigenschaften für das einmalige Anmelden](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Legen Sie den Wert im Feld **Aktiviert für die Benutzeranmeldung** auf **Ja** fest.

    b. Kopieren Sie die **URL für den Benutzerzugriff**. Diese muss im Abschnitt **Anmelde-URL konfigurieren** eingefügt werden, dies wird später in diesem Tutorial erläutert.

    c. Legen Sie das Feld **Benutzerzuweisung erforderlich** auf **Ja** fest.

    d. Legen Sie das Feld **Für Benutzer sichtbar** auf **Nein** fest.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **ADP** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL ein: `https://fed.adp.com`.

4. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **ADP einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf ADP gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Option **ADP** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-adp-sso"></a>Konfigurieren des einmaligen Anmeldens für ADP

Um das einmalige Anmelden für **ADP** zu konfigurieren, müssen Sie die heruntergeladene **Metadaten-XML** auf die [ADP-Website](https://adpfedsso.adp.com/public/login/index.fcc) hochladen.

> [!NOTE]  
> Dieser Vorgang kann einige Tage in Anspruch nehmen.

### <a name="configure-your-adp-services-for-federated-access"></a>Konfigurieren Ihrer ADP-Dienste für den Verbundzugriff

>[!Important]
> Mitarbeiter, die Verbundzugriff auf Ihre ADP-Dienste benötigen, müssen der ADP-Dienst-App zugewiesen und anschließend dem jeweiligen ADP-Dienst erneut zugewiesen werden.
Nach der Bestätigung durch Ihren ADP-Vertriebsbeauftragten konfigurieren Sie Ihre ADP-Dienste und weisen Benutzer zum Steuern des Benutzerzugriffs für die jeweiligen ADP-Dienste zu bzw. verwalten diese.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **ADP** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **ADP** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.
1. Klicken Sie im Azure-Portal auf Ihrer Anwendungsintegrationsseite für **ADP** auf die Registerkarte **Eigenschaften**, und führen Sie die folgenden Schritte aus:  

    ![Eigenschaften für SSO über Link](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    1. Legen Sie den Wert im Feld **Aktiviert für die Benutzeranmeldung** auf **Ja** fest.

    1. Legen Sie das Feld **Benutzerzuweisung erforderlich** auf **Ja** fest.

    1. Legen Sie das Feld **Für Benutzer sichtbar** auf **Ja** fest.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **ADP** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.

1. Wählen Sie im Dialogfeld **SSO-Methode auswählen** unter **Modus** die Option **Verknüpft**, um Ihre Anwendung mit **ADP** zu verknüpfen.

    ![SSO über Link](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

1. Navigieren Sie zum Abschnitt **Anmelde-URL konfigurieren**, und führen Sie die folgenden Schritte aus:

    ![Eigenschaften für einmaliges Anmelden](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    1. Fügen Sie die **URL für den Benutzerzugriff** ein, die Sie oben aus der Registerkarte **Eigenschaften** (aus der Haupt-ADP-App) kopiert haben.

    1. Nachfolgend werden die 5 Apps aufgeführt, die unterschiedliche **Relayzustand-URLs** unterstützen. Sie müssen die geeignete **Relayzustand-URL** für die jeweilige Anwendung manuell an die **URL für den Benutzerzugriff** anfügen.

        * **ADP Workforce Now**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

        * **ADP Workforce Now Enhanced Time**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`

        * **ADP Vantage HCM**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

        * **ADP Enterprise HR**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

        * **MyADP**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

1. **Speichern** Sie die Änderungen.

1. Beginnen Sie nach Bestätigung durch Ihren ADP-Vertriebsbeauftragten mit dem Test für einen oder zwei Benutzer.

    1. Weisen Sie der ADP-Dienst-App einige Benutzer zu, um den Verbundzugriff zu testen.

    1. Der Test ist erfolgreich, wenn die Benutzer auf die ADP-Dienst-App im Katalog und auf ihren ADP-Dienst zugreifen können.

1. Wenn der Test erfolgreich war, weisen Sie den ADP-Verbunddienst einzelnen Benutzern oder Benutzergruppen zu, und führen Sie ein Rollout für Ihre Mitarbeiter durch. Dies wird später in diesem Tutorial erläutert.

### <a name="create-adp-test-user"></a>Erstellen eines ADP-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens B. Simon in ADP. Wenden Sie sich an das [ADP-Supportteam](https://www.adp.com/contact-us/overview.aspx), um die Benutzer unter dem ADP-Konto hinzufügen zu lassen. 

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „ADP“ klicken, sollten Sie automatisch bei Ihrer ADP-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)