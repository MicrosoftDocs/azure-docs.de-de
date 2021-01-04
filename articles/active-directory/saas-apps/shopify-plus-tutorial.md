---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Shopify Plus | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Shopify Plus konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/18/2020
ms.author: jeedes
ms.openlocfilehash: d7a5d160fa8a0209478fe81d2880e8956828350d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993498"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-shopify-plus"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Shopify Plus

In diesem Tutorial erfahren Sie, wie Sie Shopify Plus in Azure Active Directory (Azure AD) integrieren. Die Integration von Shopify Plus in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Shopify Plus haben soll.
* Ermöglichen Sie Ihren Benutzern, sich mit ihrem Azure AD-Konto automatisch bei Shopify Plus anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Shopify Plus-Abonnement, für das einmaliges Anmelden (SSO) aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Shopify Plus unterstützt **SP- und IdP**-initiiertes einmaliges Anmelden

* Nach dem Konfigurieren von Shopify Plus können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.

## <a name="adding-shopify-plus-from-the-gallery"></a>Hinzufügen von Shopify Plus aus dem Katalog

Zum Konfigurieren der Integration von Shopify Plus in Azure AD müssen Sie Shopify Plus aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Shopify Plus** im Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Shopify Plus** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-single-sign-on-for-shopify-plus"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Shopify Plus

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Shopify Plus mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Shopify Plus eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Shopify Plus die folgenden Bausteine aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Shopify Plus](#configure-shopify-plus-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Shopify Plus-Testbenutzers](#create-shopify-plus-test-user)** , um in Shopify Plus eine Entsprechung von B. Simon zu erhalten, die mit der Benutzerdarstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Shopify Plus** den Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://accounts.shopify.com/saml/consume/organization/<ORGANIZATION_ID>`.

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** die URL ein: `https://shopify.plus/login`.

    > [!NOTE]
    > Der Wert der Antwort-URL entspricht nicht dem tatsächlichen Wert. Aktualisieren Sie den Wert mit der richtigen Antwort-URL. Den Wert erhalten Sie vom [Clientsupportteam für Shopify Plus](mailto:plus-user-management@shopify.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die Shopify Plus-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Darüber hinaus erwartet die Shopify Plus-Anwendung, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.

    | Name | Quellattribut|
    | ---- | --------------- |
    | email | user.mail |

1. Ändern Sie das Format der Namens-ID (**Name ID**) in **Permanent**. Wählen Sie die Option **Eindeutiger Benutzerbezeichner (Namens-ID)** und anschließend das Format **Namensbezeichner** aus. Wählen Sie für diese Option den Wert **Permanent** aus. Speichern Sie die Änderungen.
1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die Kopierschaltfläche aus, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Shopify Plus gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Shopify Plus** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-shopify-plus-sso"></a>Konfigurieren des einmaligen Anmeldens für Shopify Plus

Die vollständigen Schritte finden Sie in der [Shopify-Dokumentation zum Einrichten von SAML-Integrationen](https://help.shopify.com/en/manual/shopify-plus/saml).

Kopieren Sie zum Konfigurieren des einmaligen Anmeldens aufseiten von **Shopify Plus** die **App-Verbundmetadaten-URL** aus Azure Active Directory. Melden Sie sich anschließend als [Organisationsadministrator](https://shopify.plus) an, und navigieren Sie zu **Users** > **Security** („Benutzer“ > „Sicherheit“). Wählen Sie **Set up configuration** (Konfiguration einrichten) aus, und fügen Sie anschließend im Abschnitt **Identity provider metadata URL** (Metadaten-URL des Identitätsanbieters) Ihre App-Verbundmetadaten-URL ein. Wählen Sie **Add** (Hinzufügen) aus, um den Schritt abzuschließen.

### <a name="create-shopify-plus-test-user"></a>Erstellen eines Shopify Plus-Testbenutzers

In diesem Abschnitt erstellen Sie in Shopify Plus einen Benutzer namens B. Simon. Kehren Sie zum Abschnitt **Users** (Benutzer) zurück, und fügen Sie einen Benutzer hinzu, indem Sie dessen E-Mail-Adresse und Berechtigungen eingeben. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

### <a name="enforce-saml-authentication"></a>Erzwingen der SAML-Authentifizierung

> [!NOTE]
> Es empfiehlt sich, die Integration vor der allgemeinen Einführung zunächst mit einzelnen Benutzern zu testen.

Vorgehensweise für einzelne Benutzer:
1. Navigieren Sie in Shopify Plus zur Seite eines einzelnen Benutzers mit einer E-Mail-Domäne, die von Azure AD verwaltet wird und in Shopify Plus überprüft wurde.
1. Wählen Sie im Abschnitt für die SAML-Authentifizierung die Option **Edit** (Bearbeiten) aus, wählen Sie **Required** (Erforderlich) aus, und wählen Sie anschließend **Save** (Speichern) aus.
1. Vergewissern Sie sich, dass sich dieser Benutzer erfolgreich über den IdP-initiierten und den SP-initiierten Flow anmelden kann.

Vorgehensweise für alle Benutzer unter einer E-Mail-Domäne:
1. Kehren Sie zur Seite **Security** (Sicherheit) zurück.
1. Wählen Sie **Required** (Erforderlich) für Ihre SAML-Authentifizierungseinstellung aus. Dadurch wird SAML in Shopify Plus für alle Benutzer mit dieser E-Mail-Domäne erzwungen.
1. Wählen Sie **Save** (Speichern) aus.

> [!IMPORTANT]
> Die Aktivierung von SAML für alle Benutzer unter einer E-Mail-Domäne wirkt sich auf alle Benutzer aus, die diese Anwendung verwenden. Die Benutzer können sich nicht über ihre reguläre Anmeldeseite anmelden. Sie können nur über Azure Active Directory auf die App zugreifen. Shopify bietet keine alternative Anmelde-URL, über die sich Benutzer mit Ihrem normalen Benutzernamen und Kennwort anmelden können. Wenden Sie sich zur Deaktivierung von SAML bei Bedarf an den Support von Shopify.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Shopify Plus“ klicken, sollten Sie automatisch bei Ihrer Shopify Plus-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [Testen von Shopify Plus mit Azure AD](https://aad.portal.azure.com/)

- [Was ist Sitzungssteuerung in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Schützen von Apps mit der App-Steuerung für bedingten Zugriff von Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)