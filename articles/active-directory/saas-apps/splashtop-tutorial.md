---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Splashtop | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Splashtop konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: b6dda20487caf6fe3ba49578cfdc0b65434a8dfa
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520556"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-splashtop"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Splashtop

In diesem Tutorial erfahren Sie, wie Sie Splashtop in Azure Active Directory (Azure AD) integrieren. Die Integration von Splashtop in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf Splashtop haben soll.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei Splashtop anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* SSO-fähiges Splashtop-Abonnement

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Splashtop unterstützt **SP-initiiertes** einmaliges Anmelden.

* Nach dem Konfigurieren von Splashtop können Sie Sitzungssteuerungen erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützen. Sitzungssteuerungen basieren auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.

## <a name="adding-splashtop-from-the-gallery"></a>Hinzufügen von Splashtop aus dem Katalog

Um die Integration von Splashtop in Azure AD zu konfigurieren, müssen Sie Splashtop über den Katalog Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Splashtop** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Splashtop** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-single-sign-on-for-splashtop"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Splashtop

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Splashtop mithilfe eines Testbenutzers namens **B.Simon** . Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Splashtop eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Splashtop zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Splashtop](#configure-splashtop-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    * **[Erstellen eines Splashtop-Testbenutzers](#create-splashtop-test-user)** , um in Splashtop eine Entsprechung von B.Simon zu erhalten, die mit der Benutzerdarstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Splashtop** den Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    Geben Sie im Textfeld **Anmelde-URL** die URL ein: `https://my.splashtop.com/login/sso`.

1. Die Splashtop-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute, wobei **nameidentifier** hier **user.userprincipalname** zugeordnet wird. Die TicketManager-Anwendung erwartet, dass **nameidentifier** der Wert **user.mail** zugeordnet ist. Sie müssen die Attributzuordnung daher bearbeiten, indem Sie auf das Symbol **Bearbeiten** klicken und die Zuordnung entsprechend ändern.

    ![Screenshot: Benutzerattribute mit ausgewähltem Bearbeitungssymbol](common/edit-attribute.png)

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Splashtop einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B.Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie Zugriff auf Splashtop gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Splashtop** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen** .
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen** .
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

## <a name="configure-splashtop-sso"></a>Konfigurieren des einmaligen Anmeldens für Splashtop

In diesem Abschnitt müssen Sie über das [Splashtop-Webportal](https://my.splashtop.com/login) eine neue SSO-Methode beantragen.
1. Navigieren Sie im Splashtop-Webportal zu **Account info** / **Team** (Kontoinformationen > Team), und scrollen Sie nach unten zum Abschnitt **Single Sign On** (Einmaliges Anmelden). Klicken Sie anschließend auf **Apply for new SSO method** (Neue SSO-Methode beantragen).

    ![Screenshot: Seite „Single Sign-On“ (Einmaliges Anmelden) mit der Option „Apply for new SSO method“ (Neue SSO-Methode beantragen)](media/splashtop-tutorial/apply-for-new-SSO-method.png)

1. Geben Sie im Antragsfenster unter **SSO name** (SSO-Name) einen SSO-Namen an (beispielsweise „New Azure“). Wählen Sie **Azure** als IDP-Typ aus, und geben unter **Login URL** (Anmelde-URL) und **Azure AD Identifier** (Azure AD-Bezeichner) die Werte ein, die Sie im Azure-Portal aus der Splashtop-Anwendung kopiert haben.

    ![Screenshot: Seite „Apply for new SSO method“ (Neue SSO-Methode beantragen) mit Eingabe eines Namens und anderer Informationen](media/splashtop-tutorial/azure-sso-1.png)

1. Klicken Sie zum Angeben der Zertifikatinformationen mit der rechten Maustaste auf die Zertifikatdatei, die Sie im Portal aus der Splashtop-Anwendung heruntergeladen haben, bearbeiten Sie sie mit Editor, kopieren Sie den Inhalt, und fügen Sie ihn in das Feld **Download Certificate (Base64)** (Downloadzertifikat (Base64)) ein.

    ![Screenshot: Auswahl einer Zertifikatsdatei und Öffnen im Editor](media/splashtop-tutorial/cert-1.png)
    ![Screenshot: Inhalt der Zertifikatsdatei](media/splashtop-tutorial/cert-2.png)
    ![Screenshot: Textfeld für „Zertifikat herunterladen“](media/splashtop-tutorial/azure-sso-2.png)

1. Das ist alles! Klicken Sie auf **Save** (Speichern). Das SSO-Überprüfungsteam von Splashtop setzt sich mit Ihnen hinsichtlich der Überprüfungsinformationen in Verbindung und aktiviert dann die SSO-Methode.

### <a name="create-splashtop-test-user"></a>Erstellen eines Splashtop-Testbenutzers

1. Aktivieren Sie nach der Aktivierung der neu erstellten SSO-Methode im Abschnitt **Einmaliges Anmelden** das entsprechende Kontrollkästchen, um die Methode zu aktivieren.

    ![Screenshot: Seite „Einmaliges Anmelden“, auf der Sie die neue Methode aktivieren können](media/splashtop-tutorial/enable.png)

1. Laden Sie den Testbenutzer (beispielsweise `B.Simon@contoso.com`) mit der neu erstellten SSO-Methode zu Ihrem Splashtop-Team ein.

    ![Screenshot: Seite „Benutzer einladen“, auf der Sie Ihre neue Methode auswählen können](media/splashtop-tutorial/invite.png)

1. Sie können auch ein vorhandenes Splashtop-Konto in ein SSO-Konto ändern. Eine entsprechende Anleitung finden Sie [hier](https://support-splashtopbusiness.splashtop.com/hc/en-us/articles/360038685691-How-to-associate-SSO-method-to-existing-team-admin-member-).

1. Das ist alles! Sie können sich nun mit dem SSO-Konto beim Splashtop-Webportal oder bei der Splashtop Business-App anmelden.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Splashtop“ klicken, sollten Sie automatisch bei der Splashtop-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [Ausprobieren von Splashtop mit Azure AD](https://aad.portal.azure.com/)

- [Was ist Sitzungssteuerung in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Schützen von Apps mit der App-Steuerung für bedingten Zugriff von Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)