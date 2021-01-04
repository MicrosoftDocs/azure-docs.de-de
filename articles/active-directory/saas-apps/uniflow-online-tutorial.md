---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit uniFLOW Online | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und uniFLOW Online konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/02/2019
ms.author: jeedes
ms.openlocfilehash: fc1317ffce7c958f092ad01feff473e123581439
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521763"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit uniFLOW Online

In diesem Tutorial erfahren Sie, wie Sie uniFLOW Online in Azure Active Directory (Azure AD) integrieren. Die Integration von uniFLOW Online in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf uniFLOW Online hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten bei uniFLOW Online anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* uniFLOW Online-Mandant

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* uniFLOW Online unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="adding-uniflow-online-from-the-gallery"></a>Hinzufügen von uniFLOW Online aus dem Katalog

Zum Konfigurieren der Integration von uniFLOW Online in Azure AD müssen Sie uniFLOW Online aus dem Katalog zu Ihrer Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **uniFLOW Online** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **uniFLOW Online** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on-for-uniflow-online"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für uniFLOW Online

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit uniFLOW Online mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in uniFLOW Online eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit uniFLOW Online die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
   1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
   1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für uniFLOW Online](#configure-uniflow-online-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * **[Anmelden bei uniFLOW Online mithilfe des erstellten Testbenutzers](#sign-in-to-uniflow-online-using-the-created-test-user)** , um die Benutzeranmeldung auf der Anwendungsseite zu testen

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **uniFLOW Online** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein:

    - `https://<tenant_domain_name>.eu.uniflowonline.com`
    - `https://<tenant_domain_name>.us.uniflowonline.com`
    - `https://<tenant_domain_name>.sg.uniflowonline.com`
    - `https://<tenant_domain_name>.jp.uniflowonline.com`
    - `https://<tenant_domain_name>.au.uniflowonline.com`

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein:

    - `https://<tenant_domain_name>.eu.uniflowonline.com`
    - `https://<tenant_domain_name>.us.uniflowonline.com`
    - `https://<tenant_domain_name>.sg.uniflowonline.com`
    - `https://<tenant_domain_name>.jp.uniflowonline.com`
    - `https://<tenant_domain_name>.au.uniflowonline.com`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Diese Werte erhalten Sie vom [Supportteam für den uniFLOW Online-Client](mailto:support@nt-ware.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal oder die im uniFLOW Online-Mandanten angezeigte Antwort-URL ansehen.

1. Die uniFLOW Online-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Darüber hinaus wird von der uniFLOW Online-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.

    | Name |  Quellattribut|
    | -----------| --------------- |
    | displayname | user.displayname |
    | nickname | user.onpremisessamaccountname |

   > [!NOTE]
   > Das Attribut `user.onpremisessamaccountname` enthält nur dann einen Wert, wenn Ihre Azure AD-Benutzer aus einer lokalen Windows Active Directory-Instanz synchronisiert werden.

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf uniFLOW Online gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **uniFLOW Online** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

   ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

> [!NOTE]
> Wenn Sie ohne manuelle Zuweisung allen Benutzern den Zugriff auf die Anwendung ermöglichen möchten, navigieren Sie zum Abschnitt **Verwalten** , und wählen Sie **Eigenschaften** aus. Ändern Sie dann den Parameter **Benutzerzuweisung erforderlich** in **NEIN**.

## <a name="configure-uniflow-online-sso"></a>Konfigurieren des einmaligen Anmeldens für uniFLOW Online

1. Melden Sie sich in einem anderen Webbrowserfenster bei der uniFLOW Online-Website als Administrator an.

1. Wählen Sie im linken Navigationsbereich die Registerkarte **User** (Benutzer) aus.

    ![Screenshot: uniFLOW Online-Website, auf der „User“ (Benutzer) ausgewählt ist](./media/uniflow-online-tutorial/configure1.png)

1. Klicken Sie auf **Identitätsanbieter**.

    ![Screenshot, auf dem „Identity Provider“ (Identitätsanbieter) ausgewählt ist](./media/uniflow-online-tutorial/configure2.png)

1. Klicken Sie auf **Add identity provider** (Identitätsanbieter hinzufügen).

    ![Screenshot, auf dem „Add identity provider“ (Identitätsanbieter hinzufügen) ausgewählt ist](./media/uniflow-online-tutorial/configure3.png)

1. Führen Sie im Abschnitt **ADD IDENTITY PROVIDER** (IDENTITÄTSANBIETER HINZUFÜGEN) die folgenden Schritte aus:

    ![Screenshot: Abschnitt „ADD IDENTITY PROVIDER“ (IDENTITÄTSANBIETER HINZUFÜGEN), in dem Sie die beschriebenen Werte eingeben können](./media/uniflow-online-tutorial/configure4.png)

    a. Geben Sie den Anzeigenamen ein, etwa *AzureAD SSO*.

    b. Wählen Sie unter **Provider type** (Anbietertyp) im Dropdownmenü die Option **WS-Fed** aus.

    c. Wählen Sie unter **WS-Fed type** (WS-Fed-Typ) im Dropdownmenü die Option **Azure Active Directory** aus.

    d. Klicken Sie auf **Speichern**.

1. Führen Sie auf der Registerkarte **General** (Allgemein) die folgenden Schritte aus:

    ![Screenshot: Registerkarte „General“ (Allgemein), auf der Sie die beschriebenen Werte eingeben können](./media/uniflow-online-tutorial/configure5.png)

    a. Geben Sie den Anzeigenamen ein, etwa *AzureAD SSO*.

    b. Wählen Sie unter **ADFS Federation Metadata** (ADFS-Verbundmetadaten) die Option **From URL** (Über URL) aus.

    c. Fügen Sie im Textfeld **Federation Metadata URL** (Verbundmetadaten-URL) den Wert der **App-Verbundmetadaten-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Aktivieren Sie für **Identity provider** (Identitätsanbieter) das Kontrollkästchen **Enabled** (Aktiviert).

    e. Aktivieren Sie für **Automatic user registration** (Automatische Benutzerregistrierung) das Kontrollkästchen **Activated** (Aktiviert).

    f. Klicken Sie auf **Speichern**.

### <a name="sign-in-to-uniflow-online-using-the-created-test-user"></a>Anmelden bei uniFLOW Online mithilfe des erstellten Testbenutzers

1. Rufen Sie in einem anderen Webbrowserfenster die uniFLOW Online-URL für Ihren Mandanten auf.

1. Wählen Sie den zuvor erstellten Identitätsanbieter aus, um sich über Ihre Azure AD-Instanz anzumelden.

1. Melden Sie sich unter Verwendung des Testbenutzers an.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [uniFLOW Online mit Azure AD ausprobieren](https://aad.portal.azure.com/)