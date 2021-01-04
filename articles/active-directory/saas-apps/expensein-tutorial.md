---
title: 'Tutorial: Azure Active Directory-Integration mit ExpenseIn | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und ExpenseIn konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/17/2020
ms.author: jeedes
ms.openlocfilehash: eb7062b745ae6eb97dc2cd4ba2051cc221031630
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96179162"
---
# <a name="tutorial-integrate-expensein-with-azure-active-directory"></a>Tutorial: Integrieren von ExpenseIn in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie ExpenseIn in Azure Active Directory (Azure AD) integrieren. Die Integration von ExpenseIn in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf ExpenseIn hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei ExpenseIn anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* ExpenseIn-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung. 
* ExpenseIn unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.
* Nach dem Konfigurieren von ExpenseIn können Sie eine Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.


## <a name="adding-expensein-from-the-gallery"></a>Hinzufügen von ExpenseIn aus dem Katalog

Zum Konfigurieren der Integration von ExpenseIn in Azure AD müssen Sie ExpenseIn aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **ExpenseIn** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **ExpenseIn** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-expensein"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für ExpenseIn

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit ExpenseIn mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in ExpenseIn eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit ExpenseIn müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für ExpenseIn](#configure-expensein-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines ExpenseIn-Testbenutzers](#create-expensein-test-user)** , um in ExpenseIn ein Pendant von B. Simon zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **ExpenseIn** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Im Abschnitt **Grundlegende SAML-Konfiguration** muss der Benutzer keine Schritte ausführen, weil die App bereits in Azure integriert ist.

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL ein: `https://app.expensein.com/saml`.

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren. Klicken Sie anschließend auf **Herunterladen**, um das **Zertifikat (Base64)** herunterzuladen und auf Ihrem Computer zu speichern.

   ![Downloadlink für das Zertifikat](./media/expensein-tutorial/copy-metdataurl-certificate.png)

1. Kopieren Sie im Abschnitt **ExpenseIn einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf ExpenseIn gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **ExpenseIn** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.


## <a name="configure-expensein-sso"></a>Konfigurieren des einmaligen Anmeldens für ExpenseIn

1. Wenn Sie die Konfiguration in ExpenseIn automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

1. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **ExpenseIn einrichten**, um zur Anwendung ExpenseIn weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei ExpenseIn anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 5.

    ![Einrichtungskonfiguration](common/setup-sso.png)

1. Wenn Sie ExpenseIn manuell einrichten möchten, melden Sie sich bei der ExpenseIn-Unternehmenswebsite als Administrator an.

1. Klicken Sie oben auf der Seite auf **Admin** (Administrator), navigieren Sie zu **Single Sign-On** (Einmaliges Anmelden), und klicken Sie auf **Add provider** (Anbieter hinzufügen).

     ![Screenshot: Registerkarte „Admin“ (Administrator) und Seite „Single Sign-On – Providers“ (Einmaliges Anmelden – Anbieter) mit der ausgewählten Option „Add Provider“ (Anbieter hinzufügen)](./media/expenseIn-tutorial/config01.png)

1. Führen Sie im Popupfenster **New Identity Provider** (Neuer Identitätsanbieter) die folgenden Schritte aus:

    ![Screenshot: Popupelement „Edit Identity Provider“ (Identitätsanbieter bearbeiten) mit eingegebenen Werten](./media/expenseIn-tutorial/config02.png)

    a. Geben Sie im Textfeld **Provider Name** (Anbietername) den Namen ein, z. B. „Azure“.

    b. Wählen Sie unter **Allow Provider Intitiated Sign-On** (Vom Anbieter initiierte Anmeldung zulassen) die Option **Yes** (Ja) aus.

    c. Fügen Sie in das Textfeld **Target Url** (Ziel-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Fügen Sie im Textfeld **Aussteller** den Wert von **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    e. Öffnen Sie das Zertifikat (Base64) im Editor, kopieren Sie den Inhalt, und fügen Sie ihn in das Textfeld **Certificate** (Zertifikat) ein.

    f. Klicken Sie auf **Erstellen**.

### <a name="create-expensein-test-user"></a>Erstellen eines ExpenseIn-Testbenutzers

Damit sich Azure AD-Benutzer bei ExpenseIn anmelden können, müssen sie in ExpenseIn bereitgestellt werden. Im Fall von ExpenseIn muss die Bereitstellung manuell ausgeführt werden.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei ExpenseIn als Administrator an.

2. Klicken Sie oben auf der Seite auf **Admin** (Administrator), navigieren Sie zu **Users** (Benutzer), und klicken Sie auf **New User** (Neuer Benutzer).

     ![Screenshot: Registerkarte „Admin“ (Administrator) und Seite „Manage Users“ (Benutzer verwalten) mit ausgewählter Option „New User“ (Neuer Benutzer)](./media/expenseIn-tutorial/config03.png)

3. Führen Sie auf der Registerkarte **Details** die folgenden Schritte aus:

    ![ExpenseIn-Konfiguration](./media/expenseIn-tutorial/config04.png)

    a. Geben Sie im Textfeld **First name** (Vorname) den Vornamen des Benutzers ein, z. B. **B**.

    b. Geben Sie im Textfeld **Last Name** (Nachname) den Nachnamen des Benutzers ein, z.B. **Simon**.

    c. Geben Sie im Textfeld **Email** (E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein, z.B. `B.Simon@contoso.com`.

    d. Klicken Sie auf **Erstellen**.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

Wenn Sie im Zugriffsbereich die Kachel „ExpenseIn“ auswählen, sollten Sie automatisch bei der ExpenseIn-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)

- [Ausprobieren von ExpenseIn mit Azure AD](https://aad.portal.azure.com/)

- [Was ist Sitzungssteuerung in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Schützen von ExpenseIn mit erweiterter Sichtbarkeit und komplexeren Kontrollen](/cloud-app-security/proxy-intro-aad)