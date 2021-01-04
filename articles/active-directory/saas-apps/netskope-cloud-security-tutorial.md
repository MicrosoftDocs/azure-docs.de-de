---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Netskope Administrator Console | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Netskope Administrator Console konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeedes
ms.openlocfilehash: eda584f2cb531cbf526c43eab00831b218a14f93
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92514417"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-administrator-console"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Netskope Administrator Console

In diesem Tutorial erfahren Sie, wie Sie Netskope Administrator Console in Azure Active Directory (Azure AD) integrieren. Die Integration von Netskope Administrator Console in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Netskope Administrator Console hat.
* Ermöglichen Sie es ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Netskope Administrator Console anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Netskope Administrator Console-Abonnement, für das das einmalige Anmelden (SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Netskope Administrator Console unterstützt **SP- und IDP** -initiiertes SSO.

## <a name="adding-netskope-administrator-console-from-the-gallery"></a>Hinzufügen von Netskope Administrator Console aus dem Katalog

Um die Integration von Netskope Administrator Console in Azure AD zu konfigurieren, müssen Sie Netskope Administrator Console aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Netskope Administrator Console** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Netskope Administrator Console** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-administrator-console"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Netskope Administrator Console

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Netskope Administrator Console mithilfe eines Testbenutzers mit dem Namen **B. Simon** . Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Netskope Administrator Console eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Netskope Administrator Console die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Netskope Administrator Console](#configure-netskope-administrator-console-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    * **[Erstellen eines Netskope Administrator Console-Testbenutzers](#create-netskope-administrator-console-test-user)** , um ein Pendant von B. Simon in Netskope Administrator Console zu erhalten, das mit der Azure AD-Darstellung des Benutzers verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Netskope Administrator Console** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP** -initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `<OrgKey>`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<tenant_host_name>/saml/acs`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit dem eigentlichen Bezeichner und der Antwort-URL. Diese Werte werden später in diesem Tutorial erklärt.

1. Klicken Sie auf **Zusätzliche URLs festlegen** , und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<tenantname>.goskope.com`

    > [!NOTE]
    > Die Anmelde-URL ist lediglich ein Beispielwert. Aktualisieren Sie ihn mit der tatsächlichen Anmelde-URL. Den Wert für die Anmelde-URL erhalten Sie vom [Clientsupportteam für Netskope Administrator Console](mailto:support@netskope.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die Netskope Administrator Console-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Darüber hinaus wird von der Netskope Administrator Console-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.

    | Name |  Quellattribut|
    | ---------| --------- |
    | admin-role | user.assignedroles |

    > [!NOTE]
    > Klicken Sie [hier](../develop/active-directory-enterprise-app-role-management.md), um zu erfahren, wie Sie Rollen in Azure AD erstellen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Netskope Administrator Console einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon das einmalige Anmelden von Azure, indem Sie ihr Zugriff auf Netskope Administrator Console gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **Netskope Administrator Console** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen** .
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen** .
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

## <a name="configure-netskope-administrator-console-sso"></a>Konfigurieren des einmaligen Anmeldens (SSO) von Netskope Administrator Console

1. Öffnen Sie eine neue Registerkarte in Ihrem Browser, und melden Sie sich bei Ihrer Netskope Administrator Console-Unternehmenswebsite als Administrator an.

1. Klicken Sie im linken Navigationsbereich auf **Settings** (Einstellungen).

    ![Screenshot: Auswahl von „Settings“ (Einstellungen) im Navigationsbereich](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Klicken Sie auf die Registerkarte **Administration** .

    ![Screenshot: Auswahl von „Administration“ unter „Settings“ (Einstellungen)](./media/netskope-cloud-security-tutorial/config-administration.png)

1. Klicken Sie auf die Registerkarte **SSO** .

    ![Screenshot: Auswahl von „SSO“ unter „Administration“](./media/netskope-cloud-security-tutorial/config-sso.png)

1. Führen Sie im Abschnitt **Network Settings** (Netzwerkeinstellungen) die folgenden Schritte aus:
    
    ![Screenshot: Abschnitt „Network Settings“ (Netzwerkeinstellungen), in dem Sie die beschriebenen Werte eingeben können](./media/netskope-cloud-security-tutorial/config-pasteurls.png)

    a. Kopieren Sie den Wert im Feld **Assertion Consumer Service URL** , und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Antwort-URL** ein.

    b. Kopieren Sie den Wert von **Service Provider Entity ID** , und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Bezeichner** ein.

1. Klicken Sie im Abschnitt **SSO/SLO Settings** (SSO/SLO-Einstellungen) auf **EDIT SETTINGS** (EINSTELLUNGEN BEARBEITEN).

    ![Screenshot: Abschnitt „SSO/SLO Settings“ (SSO/SLO-Einstellungen), in dem Sie „EDIT SETTINGS“ (EINSTELLUNGEN BEARBEITEN) auswählen können](./media/netskope-cloud-security-tutorial/config-editsettings.png)

1. Führen Sie im Popupfenster **Settings** die folgenden Schritte aus:

    ![Screenshot: Dialogfeld „Settings“ (Einstellungen), in dem Sie die beschriebenen Werte eingeben können](./media/netskope-cloud-security-tutorial/configuration.png)

    a. Aktivieren Sie **Enable SSO** (SSO aktivieren) aus.

    b. Fügen Sie in das Textfeld **IDP URL** (IDP-URL) die **Anmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie in das Textfeld **IDP ENTITY ID** (IDP-ENTITÄTS-ID) den **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Öffnen Sie das heruntergeladene Base64-codierte Zertifikat in Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **IDP CERTIFICATE** ein.

    e. Aktivieren Sie **Enable SSO** (SSO aktivieren) aus.

    f. Fügen Sie in das Textfeld **IDP SLO URL** den Wert von **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    g. Klicken Sie auf **SUBMIT** (SENDEN).

### <a name="create-netskope-administrator-console-test-user"></a>Erstellen eines Netskope Administrator Console-Testbenutzers

1. Öffnen Sie eine neue Registerkarte in Ihrem Browser, und melden Sie sich bei Ihrer Netskope Administrator Console-Unternehmenswebsite als Administrator an.

1. Klicken Sie im linken Navigationsbereich auf **Settings** (Einstellungen).

    ![Screenshot: Auswahl von „Settings“ (Einstellungen)](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Klicken Sie auf die Registerkarte **Active Platform** (Aktive Plattform).

    ![Screenshot: Auswahl von „Active Platform“ (Aktive Plattform) unter „Settings“ (Einstellungen)](./media/netskope-cloud-security-tutorial/user1.png)

1. Klicken Sie auf die Registerkarte **Benutzer** .

    ![Screenshot: Auswahl von „Users“ (Benutzer) unter „Active Platform“ (Aktive Plattform)](./media/netskope-cloud-security-tutorial/add-user.png)

1. Klicken Sie auf **ADD USERS** (BENUTZER HINZUFÜGEN).

    ![Screenshot: Dialogfeld „Users“ (Benutzer), in dem Sie die Option „ADD USERS“ (BENUTZER HINZUFÜGEN) auswählen können](./media/netskope-cloud-security-tutorial/user-add.png)

1. Geben Sie die E-Mail-Adresse des Benutzers ein, den Sie hinzufügen möchten, und klicken Sie anschließend auf **ADD** (HINZUFÜGEN).

    ![Screenshot: Abschnitt „Add Users“ (Benutzer hinzufügen), in dem Sie eine Liste mit Benutzern eingeben können](./media/netskope-cloud-security-tutorial/add-user-popup.png)

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Netskope Administrator Console“ klicken, sollten Sie automatisch bei der Netskope Administrator Console-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [Testen von Netskope Administrator Console mit Azure AD](https://aad.portal.azure.com/)