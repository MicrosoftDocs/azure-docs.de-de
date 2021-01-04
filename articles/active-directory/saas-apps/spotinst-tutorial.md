---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Spotinst | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Spotinst konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: d9c9ac87e655cf9178d58f04b502f7e781d1fef7
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181406"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-spotinst"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Spotinst

In diesem Tutorial erfahren Sie, wie Sie Spotinst in Azure Active Directory (Azure AD) integrieren. Die Integration von Spotinst in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Spotinst hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Spotinst anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Spotinst-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Spotinst unterstützt **SP- und IDP**-initiiertes einmaliges Anmelden.

## <a name="adding-spotinst-from-the-gallery"></a>Hinzufügen von Spotinst aus dem Katalog

Zum Konfigurieren der Integration von Spotinst in Azure AD müssen Sie Keylight aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Spotinst** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Spotinst** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on-for-spotinst"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Spotinst

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Spotinst mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Spotinst eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Spotinst die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Spotinst](#configure-spotinst-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * **[Erstellen eines Spotinst-Testbenutzers](#create-spotinst-test-user)** , um eine Entsprechung von B. Simon in Spotinst zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Spotinst** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im IDP-initiierten Modus konfigurieren möchten:

   1. Stellen Sie sicher, dass die **Antwort-URL** auf https://console.spotinst.com/auth/saml festgelegt ist.
   1. Geben Sie unter **Relayzustand** die Spotinst-Organisations-ID an, die Sie auch auf der Registerkarte **SSO** überprüfen können.
   1. Das Feld **Anmelde-URL** muss leer sein.

1. Klicken Sie auf **Speichern**.

1. Spotinst erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Darüber hinaus wird von der Spotinst-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.

    | Name | Quellattribut|
    | -----| --------------- |
    | Email | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Spotinst einrichten** die entsprechenden URLs basierend auf Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Spotinst gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **Spotinst** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-spotinst-sso"></a>Konfigurieren des einmaligen Anmeldens für Spotinst

1. Melden Sie sich in einem anderen Webbrowserfenster als Sicherheitsadministrator bei Spotinst an.

2. Klicken Sie auf das **Benutzersymbol** in der oberen rechten Ecke und dann auf **Einstellungen**.

    ![Screenshot, in dem die Option „Einstellungen“ über das Benutzersymbol ausgewählt ist.](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

3. Klicken Sie auf die Registerkarte **SICHERHEIT** am oberen Rand, wählen Sie **Identitätsanbieter**, und führen Sie die folgenden Schritte aus:

    ![Spotinst-Sicherheit](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Kopieren Sie den Wert für den **Relayzustand** Ihrer Instanz, und fügen Sie ihn im Azure-Portal unter **Grundlegende SAML-Konfiguration** in das Textfeld **Relayzustand** ein.

    b. Klicken Sie dann zum Hochladen der XML-Metadatendatei, die Sie aus dem Azure-Portal heruntergeladen haben, auf **DURCHSUCHEN**.

    c. Klicken Sie auf **SPEICHERN**.

### <a name="create-spotinst-test-user"></a>Erstellen eines Spotinst-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in Spotinst.

1. Wenn Sie die Anwendung im **SP**-initiierten Modus konfiguriert haben, führen Sie die folgenden Schritte aus:

   a. Melden Sie sich in einem anderen Webbrowserfenster als Sicherheitsadministrator bei Spotinst an.

   b. Klicken Sie auf das **Benutzersymbol** in der oberen rechten Ecke und dann auf **Einstellungen**.

    ![Screenshot, in dem über das Benutzersymbol die Option „Einstellungen“ ausgewählt ist.](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Klicken Sie auf **Benutzer**, und wählen Sie **BENUTZER HINZUFÜGEN**.

    ![Screenshot, in dem über „Benutzer“ die Option „BENUTZER HINZUFÜGEN“ ausgewählt ist.](./media/spotinst-tutorial/adduser1.png)

    d. Führen Sie im Abschnitt „Benutzer hinzufügen“ die folgenden Schritte aus:

    ![Screenshot des Abschnitts „Benutzer hinzufügen“, in dem Sie die beschriebenen Werte eingeben können.](./media/spotinst-tutorial/adduser2.png)

    * Geben Sie im Textfeld **Vollständiger Name** den vollständigen Namen des Benutzers ein, z.B. **BrittaSimon**.

    * Geben Sie im Textfeld **Email** (E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein, z. B. `brittasimon\@contoso.com`.

    * Wählen Sie Ihre organisationsspezifischen Details für die **Organisationsrolle, Kontorolle und Konten**.

2. Wenn Sie die Anwendung im **IDP**-initiierten Modus konfiguriert haben, finden Sie in diesem Abschnitt kein Aktionselement. Spotinst unterstützt die Just-In-Time-Bereitstellung, die standardmäßig aktiviert ist. Wenn noch kein Benutzer vorhanden ist, wird beim Zugreifen auf Spotinst ein neuer Benutzer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Spotinst“ klicken, sollten Sie automatisch bei der Spotinst-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [Spotinst mit Azure AD ausprobieren](https://aad.portal.azure.com/)