---
title: 'Tutorial: Azure Active Directory-Integration mit Way We Do | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Way We Do konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.openlocfilehash: 0205617198ec8e57cb891d2415d2b5413a945f1b
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608934"
---
# <a name="tutorial-integrate-way-we-do-with-azure-active-directory"></a>Tutorial: Integrieren von Way We Do in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Way We Do in Azure Active Directory (Azure AD) integrieren. Bei der Integration von Way We Do in Azure AD haben Sie folgende Möglichkeiten:

* Sie können in Azure AD steuern, wer Zugriff auf Way We Do hat.
* Ermöglichen Sie Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Way We Do anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie kein Abonnement besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige kostenlose Testversion erhalten.
* Ein Way We Do-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Way We Do unterstützt **SP-initiiertes** einmaliges Anmelden.
* Way We Do unterstützt die **Just-in-Time**-Benutzerbereitstellung.

## <a name="adding-way-we-do-from-the-gallery"></a>Hinzufügen von Way We Do aus dem Katalog

Zum Konfigurieren der Integration von Way We Do in Azure AD müssen Sie Way We Do aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Way We Do** in das Suchfeld ein.
1. Wählen Sie **Way We Do** im Ergebnisbereich aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Way We Do mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Way We Do eingerichtet werden.

Sie müssen die folgenden Aufgaben ausführen, um das einmalige Anmelden von Azure AD bei Way We Do zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Way We Do](#configure-way-we-do-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Way We Do-Testbenutzers](#create-way-we-do-test-user)** , um eine Entsprechung von Britta Simon in Way We Do zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Way We Do** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie auf der Seite **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.waywedo.com`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Clientsupportteam von Way We Do](mailto:support@waywedo.com), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Suchen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** den Eintrag **Zertifikat (Rohdaten)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

   ![Downloadlink für das Zertifikat](common/certificateraw.png)

1. Kopieren Sie im Abschnitt **Way We Do einrichten** die entsprechende(n) URL(s) gemäß Ihren Anforderungen.

   ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="configure-way-we-do-sso"></a>Konfigurieren des einmaligen Anmeldens für Way We Do

1. Wenn Sie die Konfiguration in Way We Do automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

1. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Way We Do einrichten**, um zur Anwendung Way We Do weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Way We Do anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 6.

    ![Einrichtungskonfiguration](common/setup-sso.png)

1. Wenn Sie Way We Do manuell einrichten möchten, öffnen Sie ein neues Webbrowserfenster, melden Sie sich bei der Way We Do-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

1. Klicken Sie in der oberen rechten Ecke einer beliebigen Seite von Way We Do auf das **Personensymbol** und dann im Dropdownmenü auf **Account (Konto)** .

    ![Way We Do-Konto](./media/waywedo-tutorial/tutorial_waywedo_account.png)

1. Klicken Sie auf das **Menüsymbol**, um das Push-Navigationsmenü zu öffnen, und dann auf **Single Sign On (Einmaliges Anmelden)** .

    ![Way We Do: Einmaliges Anmelden](./media/waywedo-tutorial/tutorial_waywedo_single.png)

1. Führen Sie auf der Seite **Single sign-on setup (Setup für einmaliges Anmelden)** die folgenden Schritte aus:

    ![Way We Do: Speichern](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    1. Klicken Sie auf die Schaltfläche **Turn on single sign-on (Einmaliges Anmelden aktivieren)**, um zu **Ja** zu wechseln und damit das einmalige Anmelden zu aktivieren.

    1. Geben Sie im Textfeld **Single sign-on name (Name für einmaliges Anmelden)** Ihren Namen ein.

    1. Fügen Sie in das Textfeld **Entity ID** (Entitäts-ID) den Wert von **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    1. Fügen Sie in das Textfeld **SAML SSO URL** (SSO-Anmelde-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    1. Laden Sie das Zertifikat hoch, indem Sie auf die **Auswahlschaltfläche** neben **Certificate (Zertifikat)** klicken.

    1. **Optionale Einstellungen** -

        * „Enable Passwords“ (Kennwörter aktivieren): Wenn diese Option deaktiviert ist, ist das reguläre Kennwort für Way We Do nicht nutzbar, sodass Benutzer nur einmaliges Anmelden verwenden können.

        * „Enable Auto-provisioning“ (Automatische Bereitstellung aktivieren): Wenn diese Option aktiviert ist, wird die zum Anmelden verwendete E-Mail-Adresse automatisch mit der Liste der Benutzer in Way We Do verglichen. Wenn die E-Mail-Adresse nicht mit einem aktiven Benutzer in Way We Do übereinstimmt, wird automatisch ein neues Benutzerkonto für die Person hinzugefügt, die sich anmeldet, und alle fehlenden Informationen werden angefordert.

          > [!NOTE]
          > Über einmaliges Anmelden hinzugefügte Benutzer werden als allgemeine Benutzer hinzugefügt, und ihnen wird keine Rolle im System zugewiesen. Ein Administrator kann ihre Sicherheitsrolle als Editor oder Administrator ändern und auch eine oder mehrere Organigrammrollen zuweisen.

    1. Klicken Sie auf **Save (Speichern)**, um Ihre Einstellungen beizubehalten.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihm Zugriff auf Way We Do gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Way We Do** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-way-we-do-test-user"></a>Erstellen eines Way We Do-Testbenutzers

In diesem Abschnitt wird in Way We Do ein Benutzer mit dem Namen Britta Simon erstellt. Way We Do unterstützt die Just-in-Time-Benutzerbereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in Way We Do vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

> [!Note]
> Wenn Sie einen Benutzer manuell erstellen müssen, setzen Sie sich mit dem [Way We Do-Clientsupportteam](mailto:support@waywedo.com) in Verbindung.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens

Wenn Sie im Zugriffsbereich die Kachel „Way We Do“ auswählen, sollten Sie automatisch bei der Way We Do-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)