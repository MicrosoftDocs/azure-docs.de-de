---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Slack | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Slack konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: jeedes
ms.openlocfilehash: 1cdce2108130b64533bc6f14a4e9084a15678d2c
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515881"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Slack

In diesem Tutorial erfahren Sie, wie Sie Slack in Azure Active Directory (Azure AD) integrieren. Bei der Integration von Slack in Azure AD haben Sie folgende Möglichkeiten:

* Sie können in Azure AD steuern, wer Zugriff auf Slack hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Slack anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Slack-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

> [!NOTE]
> Wenn Sie in einem Mandanten mehr als eine Slack-Instanz integrieren müssen, kann der Bezeichner für jede Anwendung eine Variable sein.

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Slack unterstützt **SP** -initiiertes einmaliges Anmelden.
* Slack unterstützt die **Just-in-Time** -Benutzerbereitstellung.
* Slack unterstützt die [**automatisierte** Benutzerbereitstellung](./slack-provisioning-tutorial.md).
* Nach dem Konfigurieren von Slack können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-aad) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="adding-slack-from-the-gallery"></a>Hinzufügen von Slack aus dem Katalog

Um die Integration von Slack in Azure AD zu konfigurieren, müssen Sie Slack über den Katalog Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Slack** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Slack** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-slack"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Slack

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Slack mithilfe eines Testbenutzers mit dem Namen **B. Simon** . Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Slack eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Slack müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Slack](#configure-slack-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * **[Erstellen eines Slack-Testbenutzers](#create-slack-test-user)** , um ein Pendant von B. Simon in Slack zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Slack** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<DOMAIN NAME>.slack.com/sso/saml/start`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** die folgende URL ein: `https://slack.com`.
    
    c. Geben Sie unter **Antwort-URL** die URL in einem der folgenden Formate ein:
    
    | Antwort-URL|
    |----------|
    | `https://<DOMAIN NAME>.slack.com/sso/saml` |
    | `https://<DOMAIN NAME>.enterprise.slack.com/sso/saml` |

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Die Werte müssen durch die tatsächliche Anmelde-URL und die tatsächliche Antwort-URL ersetzt werden. Wenden Sie sich an das [Clientsupportteam von Slack](https://slack.com/help/contact), um den Wert zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

    > [!NOTE]
    > Der Wert für **Bezeichner (Entitäts-ID)** kann eine Variable sein, wenn Sie mehr als eine Slack-Instanz in den Mandanten integrieren müssen. Verwenden Sie das Muster `https://<DOMAIN NAME>.slack.com`. In diesem Szenario müssen Sie außerdem die Kopplung mit einer anderen Einstellung in Slack durchführen, indem Sie denselben Wert verwenden.

1. Die Slack-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/edit-attribute.png)

1. Darüber hinaus wird in der Slack-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen. Außerdem müssen Sie das `email`-Attribut hinzufügen. Wenn der Benutzer keine E-Mail-Adresse hat, ordnen Sie **emailaddress** zu **user.userprincipalname** und **email** zu **user.userprincipalname** zu.

    | Name | Quellattribut |
    | -----|---------|
    | emailaddress | user.userprincipalname |
    | email | user.userprincipalname |

   > [!NOTE]
   > Um die Konfiguration des Dienstanbieters einzurichten, müssen Sie auf der Seite mit der SAML-Konfiguration auf **Erweitern** neben **Erweiterte Optionen** klicken. Geben Sie im Feld **Aussteller des Dienstanbieters** die Arbeitsbereichs-URL ein. Der Standardwert ist „slack.com“. 

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Slack einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Slack gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Option **Slack** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen** .
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen** .
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

## <a name="configure-slack-sso"></a>Konfigurieren des einmaligen Anmeldens für Slack

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Slack-Unternehmenswebsite als Administrator an.

2. Navigieren Sie zu **Microsoft Azure AD** und dann zu **Teameinstellungen** .

     ![Konfigurieren des einmaligen Anmeldens in Microsoft Azure AD](./media/slack-tutorial/tutorial-slack-team-settings.png)

3. Klicken Sie im Abschnitt **Teameinstellungen** auf die Registerkarte **Authentifizierung** , und klicken Sie dann auf **Einstellungen ändern** .

    ![Konfigurieren des einmaligen Anmeldens in den Teameinstellungen](./media/slack-tutorial/tutorial-slack-authentication.png)

4. Führen Sie im Dialogfeld **SAML-Authentifizierungseinstellungen** die folgenden Schritte aus:

    ![Konfigurieren des einmaligen Anmeldens in den SAML-Authentifizierungseinstellungen](./media/slack-tutorial/tutorial-slack-save-authentication.png)

    a.  Fügen Sie in das Textfeld **SAML 2.0-Endpunkt (HTTP)** den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    b.  Fügen Sie in das Textfeld **Aussteller des Identitätsanbieters** den Wert vom **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    c.  Öffnen Sie die heruntergeladene Zertifikatsdatei in Editor, kopieren Sie den Inhalt in die Zwischenablage, und fügen Sie ihn anschließend im Textfeld **Öffentliches Zertifikat** ein.

    d. Konfigurieren Sie die oben genannten drei Einstellungen gemäß den Anforderungen Ihres Slack-Teams. Weitere Informationen zu den Einstellungen finden Sie hier im **SSO-Konfigurationshandbuch für Slack** . `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    ![Konfigurieren des einmaligen Anmeldens aufseiten der App](./media/slack-tutorial/tutorial-slack-expand.png)

    e. Klicken Sie auf **Erweitern** , und geben Sie `https://slack.com` im Textfeld **Aussteller des Dienstanbieters** ein.

    f.  Klicken Sie auf **Konfiguration speichern** .
    
    > [!NOTE]
    > Wenn Sie mehr als eine Slack-Instanz in Azure AD integrieren müssen, legen Sie `https://<DOMAIN NAME>.slack.com` auf **Aussteller des Dienstanbieters** fest, damit eine Kopplung mit der Einstellung **Bezeichner** in der Azure-Anwendung erfolgen kann.

### <a name="create-slack-test-user"></a>Erstellen eines Slack-Testbenutzers

In diesem Abschnitt wird in Slack ein Benutzer namens B.Simon erstellt. Slack unterstützt die Just-in-Time-Bereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Wenn noch kein Benutzer vorhanden ist, wird beim Zugreifen auf Slack ein neuer Benutzer erstellt. Außerdem unterstützt Slack die automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](slack-provisioning-tutorial.md).

> [!NOTE]
> Setzen Sie sich mit dem [Supportteam von Slack](https://slack.com/help/contact) in Verbindung, wenn Sie einen Benutzer manuell erstellen müssen.

> [!NOTE]
> Azure AD Connect ist das Synchronisierungstool, das lokale Active Directory-Identitäten mit Azure AD synchronisieren kann, woraufhin diese synchronisierten Benutzer ebenfalls die Anwendungen wie alle anderen Cloudbenutzer verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Slack“ klicken, sollten Sie automatisch bei Ihrer Slack-Anwendung angemeldet werden. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [Slack mit Azure AD ausprobieren](https://aad.portal.azure.com/)

- [Was ist Sitzungssteuerung in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)