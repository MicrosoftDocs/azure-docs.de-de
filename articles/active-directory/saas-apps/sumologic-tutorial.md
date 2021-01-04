---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit SumoLogic | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SumoLogic konfigurieren.
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
ms.openlocfilehash: 2dcc52688cabebaa6eb813e3240150ea8774e716
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521893"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sumologic"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit SumoLogic

In diesem Tutorial erfahren Sie, wie Sie SumoLogic in Azure Active Directory (Azure AD) integrieren. Die Integration von SumoLogic in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf SumoLogic hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei SumoLogic anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein SumoLogic-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SumoLogic unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="adding-sumologic-from-the-gallery"></a>Hinzufügen von SumoLogic aus dem Katalog

Zum Konfigurieren der Integration von SumoLogic in Azure AD müssen Sie SumoLogic aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **SumoLogic** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **SumoLogic** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sumologic"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für SumoLogic

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit SumoLogic mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SumoLogic eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit SumoLogic die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für SumoLogic](#configure-sumologic-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * **[Erstellen eines SumoLogic-Testbenutzers](#create-sumologic-test-user)** , um eine Entsprechung von B. Simon in SumoLogic zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **SumoLogic** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein:

    - `https://service.sumologic.com`
    - `https://<tenantname>.us2.sumologic.com`
    - `https://<tenantname>.us4.sumologic.com`
    - `https://<tenantname>.eu.sumologic.com`
    - `https://<tenantname>.jp.sumologic.com`
    - `https://<tenantname>.de.sumologic.com`
    - `https://<tenantname>.ca.sumologic.com`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein:

    - `https://service.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.us2.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.us4.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.eu.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.jp.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.de.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.ca.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.au.sumologic.com/sumo/saml/consume/<tenantname>`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit dem eigentlichen Bezeichner und der Antwort-URL. Wenden Sie sich an das [Clientsupportteam von SumoLogic](https://www.sumologic.com/contact-us/), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Darüber hinaus wird von der SumoLogic-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.

    |  Name | Quellattribut |
    | ---------------| --------------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Rollen | user.assignedroles |

    > [!NOTE]
    > Klicken Sie [hier](../develop/active-directory-enterprise-app-role-management.md), um herauszufinden, wie Sie die **Rolle** in Azure AD konfigurieren.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **SumoLogic einrichten** die entsprechenden URLs basierend auf Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf SumoLogic gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **SumoLogic** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-sumologic-sso"></a>Konfigurieren des einmaligen Anmeldens für SumoLogic

1. Melden Sie sich in einem anderen Webbrowserfenster auf der SumoLogic-Unternehmenswebsite als Administrator an.

1. Navigieren Sie zu **Verwalten \> Sicherheit**.

    ![Verwalten](./media/sumologic-tutorial/ic778556.png "Verwalten")

1. Klicken Sie auf **SAML**.

    ![Globale Sicherheitseinstellungen](./media/sumologic-tutorial/ic778557.png "Globale Sicherheitseinstellungen")

1. Wählen Sie aus der Liste **Eine Konfiguration auswählen oder eine neue erstellen** die Option **Azure AD** aus, und klicken Sie dann auf **Konfigurieren**.

    ![Screenshot: Dialogfeld zum Konfigurieren von SAML 2.0, in dem Sie „Azure AD“ auswählen können](./media/sumologic-tutorial/ic778558.png "SAML 2.0 konfigurieren")

1. Führen Sie auf der Seite **SAML 2.0 konfigurieren** die folgenden Schritte aus:

    ![Screenshot: Dialogfeld „Configure SAML 2.0“ (SAML 2.0 konfigurieren), in dem Sie die beschriebenen Werte eingeben können](./media/sumologic-tutorial/ic778559.png "SAML 2.0 konfigurieren")

    a. Geben Sie in das Textfeld **Konfigurationsnamen** die Option **Azure AD** ein.

    b. Wählen Sie **Debug-Modus** aus.

    c. Fügen Sie in das Textfeld **Issuer** (Aussteller) den Wert des **Azure AD-Bezeichners** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Fügen Sie in das Textfeld **Authn Request URL** (Authentifizierungsanforderungs-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    e. Öffnen Sie das Base-64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie anschließend das gesamte Zertifikat in das Textfeld **X.509-Zertifikat** ein.

    f. Wählen Sie als **E-Mail-Attribut** die Option **SAML-Betreff verwenden**.  

    g. Wählen Sie **SP-initiierte Anmeldungskonfiguration**.

    h. Geben Sie im Textfeld **Anmeldepfad** den Wert **Azure** ein, und klicken Sie auf **Speichern**.

### <a name="create-sumologic-test-user"></a>Erstellen eines SumoLogic-Testbenutzers

Damit sich Azure AD-Benutzer bei SumoLogic anmelden können, müssen sie in SumoLogic bereitgestellt werden. Im Fall von SumoLogic ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrem **SumoLogic** -Mandanten an.

1. Navigieren Sie zu **Benutzer \> verwalten**.

    ![Screenshot: Auswahl von „Users“ (Benutzer) im Menü „Manage“ (Verwalten)](./media/sumologic-tutorial/ic778561.png "Benutzer")

1. Klicken Sie auf **Hinzufügen**.

    ![Screenshot: Schaltfläche „Add“ (Hinzufügen) für „Users“ (Benutzer)](./media/sumologic-tutorial/ic778562.png "Benutzer")

1. Führen Sie im Dialogfeld **Neuer Benutzer** die folgenden Schritte aus:

    ![Neuer Benutzer](./media/sumologic-tutorial/ic778563.png "Neuer Benutzer")

    a. Geben Sie in die Textfelder für **Vorname** , **Nachname** und **E-Mail-Adresse** die entsprechenden Informationen des Azure AD-Benutzerkontos ein, das Sie bereitstellen möchten.
  
    b. Wählen Sie eine Rolle aus.
  
    c. Wählen Sie als **Status** **Aktiv** aus.
  
    d. Klicken Sie auf **Speichern**.

> [!NOTE]
> Sie können Azure AD-Benutzerkonten auch mit anderen Tools zum Erstellen von SumoLogic-Benutzerkonten oder mit den APIs von SumoLogic bereitstellen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „SumoLogic“ klicken, sollten Sie automatisch bei der SumoLogic-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [SumoLogic mit Azure AD ausprobieren](https://aad.portal.azure.com/)