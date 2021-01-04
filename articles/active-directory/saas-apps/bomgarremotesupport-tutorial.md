---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit BeyondTrust Remote Support | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und BeyondTrust Remote Support konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/12/2020
ms.author: jeedes
ms.openlocfilehash: c11d8aaa578006c7dbd96b457399df5b17fd9bd8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95914989"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyondtrust-remote-support"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit BeyondTrust Remote Support

In diesem Tutorial erfahren Sie, wie Sie BeyondTrust Remote Support in Azure Active Directory (Azure AD) integrieren. Die Integration von BeyondTrust Remote Support in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf BeyondTrust Remote Support hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei BeyondTrust Remote Support anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein BeyondTrust Remote Support-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* BeyondTrust Remote Support unterstützt **SP**-initiiertes einmaliges Anmelden.
* BeyondTrust Remote Support unterstützt die **Just-in-Time**-Benutzerbereitstellung.

## <a name="adding-beyondtrust-remote-support-from-the-gallery"></a>Hinzufügen von BeyondTrust Remote Support aus dem Katalog

Zum Konfigurieren der Integration von BeyondTrust Remote Support in Azure AD müssen Sie BeyondTrust Remote Support über den Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **BeyondTrust Remote Support** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **BeyondTrust Remote Support** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on-for-beyondtrust-remote-support"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für BeyondTrust Remote Support

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit BeyondTrust Remote Support mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in BeyondTrust Remote Support eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit BeyondTrust Remote Support die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für BeyondTrust Remote Support](#configure-beyondtrust-remote-support-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * **[Erstellen eines BeyondTrust Remote Support-Testbenutzers](#create-beyondtrust-remote-support-test-user)** , um eine Entsprechung von B. Simon in BeyondTrust Remote Support zu erhalten, die mit der Benutzerdarstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **BeyondTrust Remote Support** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<HOSTNAME>.bomgar.com/saml`

    b. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein: `https://<HOSTNAME>.bomgar.com`.

    c. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<HOSTNAME>.bomgar.com/saml/sso`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL, den tatsächlichen Bezeichner und die tatsächliche Antwort-URL. Diese Werte werden später in diesem Tutorial erklärt.

1. BeyondTrust Remote Support erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Darüber hinaus wird von BeyondTrust Remote Support erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.

    | Name |  Quellattribut|
    | ---------------| ----------|
    | Username | user.userprincipalname |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |
    | Gruppen | user.groups |

    > [!NOTE]
    > Wenn Sie Azure AD-Gruppen für die BeyondTrust Remote Support-Anwendung zuweisen, müssen Sie die Option „Im Anspruch zurückgegebene Gruppen“ von „None“ in „SecurityGroup“ ändern. Die Gruppen werden als Objekt-IDs in die Anwendung importiert. Die Objekt-ID der Azure AD-Gruppe finden Sie, indem Sie die Eigenschaften in der Azure Active Directory-Schnittstelle überprüfen. Dies ist erforderlich, um auf Azure AD-Gruppen zu verweisen und sie den richtigen Gruppenrichtlinien zuzuweisen.

1. Beim Festlegen der eindeutigen Benutzer-ID muss dieser Wert auf das folgende NameID-Format festgelegt werden: **Beständig**. Eine beständige ID ist erforderlich, damit der Benutzer richtig identifiziert und den richtigen Gruppenrichtlinien für die Berechtigungen zugeordnet werden kann. Klicken Sie auf das Bearbeitungssymbol, um das Dialogfeld **Benutzerattribute und Ansprüche** zu öffnen und den Wert der eindeutigen Benutzer-ID zu bearbeiten.

1. Klicken Sie im Bereich **Anspruch verwalten** auf **Namensbezeichnerformat auswählen**, legen Sie den Wert auf **Beständig** fest, und klicken Sie auf **Speichern**.

    ![Benutzerattribute und Ansprüche](./media/bomgarremotesupport-tutorial/attribute-unique-user-identifier.png)

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **BeyondTrust Remote Support einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie Zugriff auf BeyondTrust Remote Support gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Option **BeyondTrust Remote Support** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-beyondtrust-remote-support-sso"></a>Konfigurieren von SSO für BeyondTrust Remote Support

1. Melden Sie sich in einem anderen Webbrowserfenster bei BeyondTrust Remote Support als Administrator an.

1. Navigieren Sie zu **Users & Security** (Benutzer und Sicherheit)  > **Security Providers** (Sicherheitsanbieter).

1. Klicken Sie unter **SAML Providers** (SAML-Anbieter) auf das Symbol **Bearbeiten**.

    ![SAML-Anbieter: Bearbeitungssymbol](./media/bomgarremotesupport-tutorial/saml-providers.png)

1. Erweitern Sie den Abschnitt mit den **Dienstanbietereinstellungen**.

1. Klicken Sie auf **Download Service Provider Metadata** (Metadaten des Dienstanbieters herunterladen), oder kopieren Sie die Werte **Entity ID** (Entitäts-ID) und **ACS URL** (ACS-URL), und verwenden Sie sie im Abschnitt **Grundlegende SAML-Konfiguration** des Azure-Portals.

    ![Herunterladen der Metadaten des Dienstanbieters](./media/bomgarremotesupport-tutorial/service-provider-metadata.png)


1. Klicken Sie im Abschnitt mit den Identitätsanbietereinstellungen auf **Upload Identity Provider Metadata** (Metadaten des Identitätsanbieters hochladen), und suchen Sie die XML-Metadatendatei, die Sie aus dem Azure-Portal heruntergeladen haben.

1.  **Entity ID** (Entitäts-ID), **Single Sign-On Service URL** (Dienst-URL für einmaliges Anmelden)und **Server Certificate** (Serverzertifikat) werden automatisch hochgeladen, und **SSO URL Protocol Binding** (SSO-URL-Protokollbindung) muss in **HTTP POST** geändert werden.

    ![Screenshot: Abschnitt mit den Einstellungen des Identitätsanbieters, in dem diese Aktionen durchgeführt werden](./media/bomgarremotesupport-tutorial/identity-provider.png)

1. Klicken Sie auf **Speichern**.

### <a name="create-beyondtrust-remote-support-test-user"></a>Erstellen eines Testbenutzers für BeyondTrust Remote Support

Hier werden die Einstellungen für die Benutzerbereitstellung konfiguriert. Auf die in diesem Abschnitt verwendeten Werte wird vom Abschnitt **Benutzerattribute und Ansprüche** im Azure-Portal verwiesen. Wir haben diese als Standardwerte konfiguriert, die zum Zeitpunkt der Erstellung bereits importiert werden. Der Wert kann jedoch bei Bedarf angepasst werden.

![Screenshot: Einstellungen für die Benutzerbereitstellung, in denen Sie Benutzerwerte konfigurieren können](./media/bomgarremotesupport-tutorial/user-attribute.png)

> [!NOTE]
> Die Gruppen- und E-Mail-Attribute sind für diese Implementierung nicht erforderlich. Wenn Sie Azure AD-Gruppen verwenden und diese den BeyondTrust Remote Support-Gruppenrichtlinien für Berechtigungen zuweisen, muss über die Eigenschaften im Azure-Portal auf die Objekt-ID der Gruppe verwiesen werden, und sie muss im Abschnitt „Verfügbare Gruppen“ abgelegt werden. Nachdem dieser Vorgang abgeschlossen ist, ist die Objekt-ID/AD-Gruppe für die Zuweisung zu einer Gruppenrichtlinie für Berechtigungen verfügbar.

![Screenshot: IT-Abschnitt mit „Membership type“ (Mitgliedschaftstyp), „Source“ (Quelle), „Type“ (Typ) und „Object ID“ (Objekt-ID)](./media/bomgarremotesupport-tutorial/config-user2.png)

![Screenshot: Seite „Basic Settings“ (Grundeinstellungen) für eine Gruppenrichtlinie](./media/bomgarremotesupport-tutorial/group-policy.png)

> [!NOTE]
> Alternativ kann eine Standardgruppenrichtlinie für den SAML2-Sicherheitsanbieter festgelegt werden. Wenn Sie diese Option definieren, werden dadurch allen Benutzern, die sich über SAML authentifizieren, die in der Gruppenrichtlinie angegebenen Berechtigungen zugewiesen. Die Richtlinie „General Members“ (Allgemeine Mitglieder) ist in BeyondTrust Remote Support/Privileged Remote Access mit beschränkten Berechtigungen enthalten und kann verwendet werden, um die Authentifizierung zu testen und Benutzer den korrekten Richtlinien zuzuweisen. Benutzer werden über /login > „Users & Security“ (Benutzer und Sicherheit) erst dann in die SAML2-Benutzerliste eingetragen, wenn der erste erfolgreiche Authentifizierungsversuch erfolgt. Weitere Informationen zu Gruppenrichtlinien finden Sie unter den folgenden Links: `https://www.beyondtrust.com/docs/remote-support/getting-started/admin/group-policies.htm`

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für BeyondTrust Remote Support weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die BeyondTrust Remote Support-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „BeyondTrust Remote Support“ klicken, werden Sie zur Anmelde-URL für BeyondTrust Remote Support weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von BeyondTrust Remote Support können Sie Sitzungssteuerungen erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützen. Sitzungssteuerungen basieren auf bedingtem Zugriff. [Hier](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.
