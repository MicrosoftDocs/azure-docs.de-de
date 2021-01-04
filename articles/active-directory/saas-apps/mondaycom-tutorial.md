---
title: 'Tutorial: Integration von monday.com in das einmalige Anmelden (Single Sign-On, SSO) von Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und monday.com konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.openlocfilehash: 30cc4e66f73f0fe7f4c11f84f24312caf4f8a483
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516761"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mondaycom"></a>Tutorial: Integration von monday.com in das einmalige Anmelden (Single Sign-On, SSO) von Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie monday.com in Azure Active Directory (Azure AD) integrieren. Bei der Integration von monday.com in Azure AD haben Sie folgende Möglichkeiten:

* Steuern Sie in Azure AD, wer Zugriff auf monday.com hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei monday.com anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* monday.com-Abonnement, für das einmaliges Anmelden (SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* monday.com unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.
* monday.com unterstützt die **Just-in-Time** -Benutzerbereitstellung.

## <a name="adding-mondaycom-from-the-gallery"></a>Hinzufügen von monday.com aus dem Katalog

Zum Konfigurieren der Integration von monday.com in Azure AD müssen Sie monday.com aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **monday.com** in das Suchfeld ein.
1. Wählen Sie **monday.com** im Ergebnisbereich aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on-for-mondaycom"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für monday.com

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit monday.com mithilfe eines Testbenutzers mit dem Namen **B. Simon** . Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in monday.com eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit monday.com müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für monday.com](#configure-mondaycom-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines monday.com-Testbenutzers](#create-mondaycom-test-user)** , um eine Entsprechung von B. Simon in monday.com zu erhalten, die mit seiner Darstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **monday.com** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie über eine **Metadatendatei des Dienstanbieters** verfügen und die Anwendung im **IDP** -initiierten Modus konfigurieren möchten:

    a. Klicken Sie auf **Metadatendatei hochladen** .

    ![Metadatendatei hochladen](common/upload-metadata.png)

    b. Klicken Sie auf das **Ordnerlogo** , wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen** .

    ![Metadatendatei auswählen](common/browse-upload-metadata.png)

    c. Nach dem erfolgreichen Upload der Metadatendatei werden die Werte unter **Bezeichner** und **Antwort-URL** im Abschnitt „Grundlegende SAML-Konfiguration“ automatisch eingefügt.

    ![Screenshot der grundlegenden S A M L-Konfiguration, in der Sie I D und Antwort-U R L eingeben und „Speichern“ auswählen](common/idp-intiated.png)

    > [!Note]
    > Wenn die Werte **Bezeichner** und **Antwort-URL** nicht automatisch ausgefüllt werden, geben Sie sie manuell ein. Die Werte für **Bezeichner** und **Antwort-URL** sind identisch und haben folgendes Format: `https://<your-domain>.monday.com/saml/saml_callback`

1. Klicken Sie auf **Zusätzliche URLs festlegen** , und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    ![Screenshot: Option „Zusätzliche URLs festlegen“ zum Eingeben einer Anmelde-URL](common/metadata-upload-additional-signon.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<YOUR_DOMAIN>.monday.com`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Wenden Sie sich an das [Supportteam für den monday.com-Client](https://monday.com/contact-us/), um diesen Wert zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die monday.com-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![Screenshot: „User Attributes & Claims“ (Benutzerattribute und -ansprüche) mit Standardwerten wie „user.givenname“ als Vorname und „User.mail“ als E-Mail-Adresse](common/default-attributes.png)

1. Darüber hinaus wird von der monday.com-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.

    | Name | Quellattribut |
    |--|--|
    | Email | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **monday.com einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf monday.com gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **monday.com** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen** .
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen** .
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

## <a name="configure-mondaycom-sso"></a>Konfigurieren des einmaligen Anmeldens für monday.com

1. Wenn Sie die Konfiguration in monday.com automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

1. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **monday.com einrichten** , um zur monday.com-Anwendung weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei monday.com anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 6.

    ![Einrichtungskonfiguration](common/setup-sso.png)

1. Wenn Sie monday.com manuell einrichten möchten, öffnen Sie ein neues Webbrowserfenster, melden Sie sich als Administrator bei monday.com an, und führen Sie die folgenden Schritte aus:

1. Navigieren Sie zu **Profile** (Profil) in der rechten oberen Ecke, und klicken Sie auf **Admin** (Administrator).

    ![Screenshot: Auswahl des Profils „Admin“ (Administrator)](./media/mondaycom-tutorial/configuration01.png)

1. Wählen Sie **Security** (Sicherheit) aus, und klicken Sie neben „SAML“ auf **Open** (Öffnen).

    ![Screenshot: Registerkarte „Security“ (Sicherheit) mit der Option „Open“ (Öffnen) neben „SAML“](./media/mondaycom-tutorial/configuration02.png)

1. Geben Sie die nachfolgend gezeigten Details an.

    ![Screenshot: SAML-Anbieter, unter dem Sie Informationen Ihres Identitätsanbieters eingeben können](./media/mondaycom-tutorial/configuration03.png)

    > [!NOTE]
    > Weitere Informationen finden Sie in [diesem Artikel](https://support.monday.com/hc/articles/360000460605-SAML-Single-Sign-on?abcb=34642).

### <a name="create-mondaycom-test-user"></a>Erstellen eines monday.com-Testbenutzers

In diesem Abschnitt wird in monday.com ein Benutzer mit dem Namen B. Simon erstellt. monday.com unterstützt die Just-in-Time-Bereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Wenn in monday.com noch kein Benutzer vorhanden ist, wird beim Zugriff auf monday.com ein neuer Benutzer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „monday.com“ klicken, sollten Sie automatisch bei Ihrer monday.com-Anwendung angemeldet werden. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [Ausprobieren von monday.com mit Azure AD](https://aad.portal.azure.com/)