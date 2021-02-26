---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Veracode | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Veracode konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: 220a575a252bb16cb5ac895a3d4c1ebedfd7c83e
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99431031"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Veracode

In diesem Tutorial erfahren Sie, wie Sie Veracode in Azure Active Directory (Azure AD) integrieren. Die Integration von Veracode in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Veracode hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Veracode anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Veracode-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung. Veracode unterstützt vom Identitätsanbieter initiiertes einmaliges Anmelden sowie die Just-In-Time-Benutzerbereitstellung.

## <a name="add-veracode-from-the-gallery"></a>Hinzufügen von Veracode aus dem Katalog

Fügen Sie zum Konfigurieren der Integration von Veracode in Azure AD Veracode aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzu.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff „Veracode“ in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Veracode** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-veracode"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Veracode

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Veracode mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Verknüpfung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Veracode eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Veracode die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Veracode](#configure-veracode-sso)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * **[Erstellen eines Veracode-Testbenutzers](#create-veracode-test-user)**, um eine Entsprechung von B. Simon in Veracode zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Veracode** zum Abschnitt **Verwalten**. Wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol für **Grundlegende SAML-Konfiguration** aus, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Im Abschnitt **SAML-Basiskonfiguration** ist die Anwendung vorkonfiguriert und die notwendigen URLs sind bereits mit Azure vorausgefüllt. Wählen Sie **Speichern** aus.

1. Suchen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** nach **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Screenshot: Abschnitt „SAML-Signaturzertifikat“ mit hervorgehobenem Downloadlink](common/certificatebase64.png)

1. Veracode erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![Screenshot: Abschnitt „Benutzerattribute und Ansprüche“](common/default-attributes.png)

1. Veracode erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden. Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überarbeiten.

    | Name | Quellattribut|
    | ---------------| --------------- |
    | firstname |User.givenname |
    | lastname |User.surname |
    | email |User.mail |

1. Kopieren Sie im Abschnitt **Veracode einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Screenshot: Abschnitt „Veracode einrichten“ mit hervorgehobenen Konfigurations-URLs](common/copy-configuration-urls.png)

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Veracode gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Veracode** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-veracode-sso"></a>Konfigurieren des einmaligen Anmeldens für Veracode

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Veracode-Unternehmenswebsite als Administrator an.

1. Wählen Sie im oberen Menü **Einstellungen** > **Administrator** aus.
   
    ![Screenshot: Veracode-Verwaltung mit hervorgehobenen Symbolen für Einstellungen und Administrator](./media/veracode-tutorial/admin.png "Verwaltung")

1. Wählen Sie die Registerkarte **SAML** aus.

1. Führen Sie im Abschnitt für die **SAML-Einstellungen der Organisation** die folgenden Schritte aus:

    ![Screenshot: Abschnitt „SAML-Einstellungen der Organisation“](./media/veracode-tutorial/saml.png "Verwaltung")

    a.  Fügen Sie unter **Issuer** (Aussteller) den Wert für den **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    b. Wählen Sie unter **Assertion Signing Certificate** (Assertionssignaturzertifikat) die Option **Choose File** (Datei auswählen) aus, um das heruntergeladene Zertifikat aus dem Azure-Portal hochzuladen.

    c. Aktivieren Sie unter **Self Registration** (Selbstregistrierung) die Option **Enable Self Registration** (Selbstregistrierung aktivieren).

1. Führen Sie im Abschnitt **Self Registration Settings** (Selbstregistrierungseinstellungen) die folgenden Schritte aus, und klicken Sie dann auf **Save** (Speichern):

    ![Screenshot: Abschnitt „Self Registration Settings“ (Selbstregistrierungseinstellungen) mit verschiedenen hervorgehobenen Optionen](./media/veracode-tutorial/save.png "Verwaltung")

    a. Wählen Sie für **New User Activation** (Aktivierung neuer Benutzer) die Option **No Activation Required** (Keine Aktivierung erforderlich) aus.

    b. Wählen Sie für **User Data Updates** (Benutzerdatenaktualisierungen) die Option **Preference Veracode User Data** (Veracode-Benutzerdaten bevorzugen) aus.

    c. Wählen Sie für die **SAML-Attributdetails** Folgendes aus:
      * **Benutzerrollen**
      * **Richtlinien-Administrator**
      * **Prüfer**
      * **Leiter der Sicherheitsabteilung**
      * **Executive**
      * **Absender**
      * **Creator**
      * **Alle Scantypen**
      * **Teammitgliedschaften**
      * **Standardteam**

### <a name="create-veracode-test-user"></a>Erstellen eines Veracode-Testbenutzers

In diesem Abschnitt wird in Veracode ein Benutzer namens B. Simon erstellt. Veracode unterstützt die Just-In-Time-Benutzerbereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist noch kein Benutzer in Veracode vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

> [!NOTE]
> Sie können Azure AD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Veracode-Benutzerkonten oder mithilfe der von Veracode bereitgestellten APIs erstellen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der Veracode-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Veracode“ klicken, sollten Sie automatisch bei der Veracode-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Veracode können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.
