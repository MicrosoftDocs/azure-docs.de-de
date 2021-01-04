---
title: 'Tutorial: Azure Active Directory-Integration mit Keeper Password Manager & Digital Vault | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Keeper Password Manager & Digital Vault konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/13/2020
ms.author: jeedes
ms.openlocfilehash: b70c50e7c2900f884dd4d91c6650205bc626326e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96178040"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Tutorial: Azure Active Directory-Integration mit Keeper Password Manager & Digital Vault

In diesem Tutorial erfahren Sie, wie Sie Keeper Password Manager & Digital Vault in Azure Active Directory (Azure AD) integrieren.
Diese Integration bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Keeper Password Manager & Digital Vault hat.
* Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Keeper Password Manager & Digital Vault anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten an einem zentralen Ort verwalten: im Azure-Portal.


## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Keeper Password Manager & Digital Vault konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über keine Azure AD-Umgebung verfügen, können Sie [eine einmonatige Testversion](https://azure.microsoft.com/pricing/free-trial/) erhalten.
* Ein Keeper Password Manager & Digital Vault-Abonnement, für das einmaliges Anmelden (SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Keeper Password Manager & Digital Vault unterstützt SP-initiiertes einmaliges Anmelden.

* Keeper Password Manager & Digital Vault unterstützt die Just-In-Time-Benutzerbereitstellung.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Hinzufügen von Keeper Password Manager & Digital Vault aus dem Katalog

Zum Konfigurieren der Integration von Keeper Password Manager & Digital Vault in Azure AD müssen Sie Keeper Password Manager & Digital Vault über den Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Bereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie unter **Aus Katalog hinzufügen** den Suchbegriff **Keeper Password Manager & Digital Vault** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Keeper Password Manager & Digital Vault** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager--digital-vault"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Keeper Password Manager & Digital Vault

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Keeper Password Manager & Digital Vault mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Keeper Password Manager & Digital Vault eine Linkbeziehung eingerichtet werden.

Führen Sie das Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Keeper Password Manager & Digital Vault wie folgt durch:

1. [Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso), um Ihren Benutzern die Verwendung dieses Features zu ermöglichen

    * [Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user), um das einmalige Anmelden mit Azure AD mit der Testbenutzerin Britta Simon zu testen.
    * [Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user), um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.

1. [Konfigurieren des einmaligen Anmeldens für Keeper Password Manager & Digital Vault](#configure-keeper-password-manager--digital-vault-sso), um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    * [Erstellen eines Keeper Password Manager & Digital Vault-Testbenutzers](#create-a-keeper-password-manager--digital-vault-test-user), um eine Entsprechung von Britta Simon in Keeper Password Manager & Digital Vault zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
1. [Testen des einmaligen Anmeldens](#test-sso), um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Keeper Password Manager & Digital Vault** zum Abschnitt **Verwalten**. Wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol für **Grundlegende SAML-Konfiguration** aus, um die Einstellungen zu bearbeiten.

   ![Screenshot: „Einmaliges Anmelden (SSO) mit SAML einrichten“ mit hervorgehobenem Stiftsymbol](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie unter **Anmelde-URL** eine URL mit dem folgenden Muster ein:
    * Für Cloud-SSO: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Für SSO in der lokalen Umgebung: `https://<KEEPER_FQDN>/sso-connect/saml/login`

    b. Geben Sie unter **Bezeichner (Entitäts-ID)** eine URL mit dem folgenden Muster ein:
    * Für Cloud-SSO: `https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * Für SSO in der lokalen Umgebung: `https://<KEEPER_FQDN>/sso-connect`

    c. Geben Sie unter **Antwort-URL** eine URL mit dem folgenden Muster ein:
    * Für Cloud-SSO: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Für SSO in der lokalen Umgebung: `https://<KEEPER_FQDN>/sso-connect/saml/sso`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL, den tatsächlichen Bezeichner und die tatsächliche Antwort-URL. Wenden Sie sich an das [Supportteam für Keeper Password Manager & Digital Vault](https://keepersecurity.com/contact.html), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die Keeper Password Manager & Digital Vault-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![Screenshot: „Benutzerattribute und Ansprüche“](common/default-attributes.png)

1. Darüber hinaus wird von der Keeper Password Manager & Digital Vault-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden. Diese sind in der folgenden Tabelle angegeben. Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überarbeiten.

    | Name | Quellattribut|
    | ------------| --------- |
    | First | user.givenname |
    | Letzter | user.surname |
    | Email | user.mail |

5. Navigieren Sie unter **Einmaliges Anmelden (SSO) mit SAML einrichten** zum Abschnitt **SAML-Signaturzertifikat**, und wählen Sie die Option **Herunterladen** aus. Die **Verbundmetadaten-XML-Datei** wird mit den entsprechenden Optionen für Ihre Anforderungen heruntergeladen und auf Ihrem Computer gespeichert.

    ![Screenshot: „SAML-Signaturzertifikat“ mit hervorgehobener Option „Herunterladen“](common/metadataxml.png)

6. Kopieren Sie unter **Keeper Password Manager & Digital Vault einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Screenshot: „Keeper Password Manager & Digital Vault einrichten“ mit hervorgehobenen URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen `B.Simon`.

1. Wählen Sie im Azure-Portal im linken Bereich **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie unter **Name**`B.Simon` ein.  
   1. Geben Sie unter **Benutzername** einen Benutzernamen im Format `username@companydomain.extension` ein. Beispiel: `B.Simon@contoso.com`.
   1. Wählen Sie **Kennwort anzeigen** aus, und notieren Sie sich den angezeigten Wert.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Keeper Password Manager & Digital Vault gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Liste der Anwendungen **Keeper Password Manager & Digital Vault**.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Klicken Sie auf **Benutzer hinzufügen**. Wählen Sie unter **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie unter **Benutzer und Gruppen** in der Liste mit den Benutzern die Option **B. Simon** aus. Wählen Sie anschließend am unteren Bildschirmrand **Auswählen** aus.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie in der Liste **Rolle auswählen** auswählen. Wird für diese App keine Rolle eingerichtet, wird die Rolle **Standardzugriff** ausgewählt.
1. Wählen Sie unter **Zuweisung hinzufügen** die Option **Zuweisen** aus.


## <a name="configure-keeper-password-manager--digital-vault-sso"></a>Konfigurieren des einmaligen Anmeldens für Keeper Password Manager & Digital Vault

Informationen zum Konfigurieren des einmaligen Anmeldens für die App finden Sie in den Richtlinien im [Keeper-Supporthandbuch](https://docs.keeper.io/sso-connect-guide/).

### <a name="create-a-keeper-password-manager--digital-vault-test-user"></a>Erstellen eines Keeper Password Manager & Digital Vault-Testbenutzers

Damit sich Azure AD-Benutzer bei Keeper Password Manager & Digital Vault anmelden können, müssen Sie sie bereitstellen. Die Anwendung unterstützt die Just-In-Time-Benutzerbereitstellung. Nach der Authentifizierung werden Benutzer in der Anwendung automatisch erstellt. Sie können sich an den [Support von Keeper](https://keepersecurity.com/contact.html) wenden, wenn Sie Benutzer manuell einrichten möchten.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Wählen Sie im Azure-Portal die Option **Diese Anwendung testen** aus. Sie werden an die Anmelde-URL für Keeper Password Manager & Digital Vault umgeleitet, damit Sie die Anmeldung initiieren können. 

* Sie können direkt zur Anmelde-URL für die Anwendung navigieren und die Anmeldung dort initiieren.

* Sie können den Microsoft-Zugriffsbereich verwenden. Wenn Sie im Zugriffsbereich die Kachel **Keeper Password Manager & Digital Vault** auswählen, werden Sie an die Anmelde-URL für die Anwendung umgeleitet. Weitere Informationen zum Zugriffsbereich finden Sie unter [Anmelden beim Portal „Meine Apps“ und Starten von Apps über dieses](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Keeper Password Manager & Digital Vault konfiguriert haben, können Sie die Sitzungssteuerung erzwingen. Diese schützt in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten. Die Sitzungssteuerung basiert auf bedingtem Zugriff. Weitere Informationen finden Sie im Artikel zum [Erzwingen der Sitzungssteuerung mit Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).