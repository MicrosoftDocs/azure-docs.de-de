---
title: 'Tutorial: Integration des einmaligen Anmeldens von Azure AD mit Trend Micro Web Security (TMWS)'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Trend Micro Web Security (TMWS) konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/21/2020
ms.author: jeedes
ms.openlocfilehash: b76c41787d7a35fb3024fa18c0122bc966243bbc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008394"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-security-tmws"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Trend Micro Web Security (TMWS)

In diesem Tutorial erfahren Sie, wie Sie Trend Micro Web Security (TMWS) in Azure Active Directory (Azure AD) integrieren. Die Integration von TMWS in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf TMWS hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei TMWS anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein TMWS-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* TMWS unterstützt SP-initiiertes einmaliges Anmelden.
* Nach dem Konfigurieren von TMWS können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. Informationen zum Erzwingen der Sitzungssteuerung mithilfe von Microsoft Cloud App Security finden Sie unter [Integrieren und Bereitstellen der App-Steuerung für bedingten Zugriff für beliebige Apps](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-tmws-from-the-gallery"></a>Hinzufügen von TMWS über den Katalog

Um die Integration von TMWS in Azure AD zu konfigurieren, müssen Sie TMWS über den Katalog Ihrer Liste mit verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Bereich den Dienst **Azure Active Directory** aus.
1. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Trend Micro Web Security (TMWS)** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Trend Micro Web Security (TMWS)** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-tmws"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für TMWS

Sie konfigurieren und testen das einmalige Anmelden von Azure AD mit TMWS unter Verwendung eines Testbenutzers namens B. Simon. Damit einmaliges Anmelden funktioniert, muss eine Verknüpfung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in TMWS eingerichtet werden.

Führen Sie die folgenden grundlegenden Schritte aus, um das einmalige Anmelden von Azure AD mit TMWS zu konfigurieren und zu testen:

1. [Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso), um das Feature für Ihre Benutzer zu aktivieren
    1. [Erstellen eines Azure AD-Benutzers](#create-an-azure-ad-test-user), um das einmalige Anmelden von Azure AD zu testen
    1. [Gewähren des Zugriffs auf TMWS für den Azure AD-Testbenutzer](#grant-the-azure-ad-test-user-access-to-tmws)
    1. [Konfigurieren der Einstellungen für die Benutzer- und Gruppensynchronisierung in Azure AD](#configure-user-and-group-synchronization-settings-in-azure-ad)
1. [Konfigurieren des einmaligen Anmeldens für TMWS](#configure-tmws-sso) auf der Anwendungsseite
1. [Testen des einmaligen Anmeldens](#test-sso), um die Konfiguration zu überprüfen

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Trend Micro Web Security (TMWS)** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol für **Grundlegende SAML-Konfiguration** aus, um die Einstellungen zu bearbeiten:

   ![Bearbeiten der Einstellungen für die grundlegende SAML-Konfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** Werte in die folgenden Felder ein:

    a. Geben Sie im Feld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein:

    `https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. Geben Sie im Feld **Antwort-URL** die folgende URL ein:

    `https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > Der Bezeichnerwert im vorherigen Schritt ist nicht der Wert, den Sie eingeben sollten. Sie müssen den tatsächlichen Bezeichner verwenden. Diesen Wert können Sie auf der Seite **Authentifizierungsmethode** für Azure AD im Abschnitt **Service Provider Settings for the Azure Admin Portal** (Dienstanbietereinstellungen für das Azure-Verwaltungsportal) unter **Verwaltung > Verzeichnisdienste** abrufen.

1. TMWS erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Standardattribute:

    ![Standardattribute](common/default-attributes.png)

1. Von TMWS wird erwartet, dass in der SAML-Antwort zusätzlich zu den Attributen auf dem vorherigen Screenshot noch zwei weitere Attribute zurückgegeben werden. Diese Attribute sind in der folgenden Tabelle angegeben. Die Attribute werden vorab aufgefüllt, Sie können sie jedoch gemäß Ihren Anforderungen ändern.
    
    | Name | Quellattribut|
    | --------------- | --------- |
    | sAMAccountName | user.onpremisessamaccountname |
    | uPN | user.userprincipalname |

1. Suchen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** nach **Zertifikat (Base64)** . Wählen Sie neben diesem Zertifikat den Link **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer:

    ![Downloadlink für Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Trend Micro Web Security (TMWS) einrichten** die entsprechenden URLs gemäß Ihren Anforderungen:

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Azure-Portals den Dienst **Azure Active Directory** aus. Wählen Sie **Benutzer** und dann **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** Folgendes ein: `B.Simon`.  
   1. Geben Sie im Feld **Benutzername** den Namen **_Benutzername_@* IhreUnternehmensdomäne *.* Erweiterung*** ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.
   1. Klicken Sie auf **Erstellen**.

### <a name="grant-the-azure-ad-test-user-access-to-tmws"></a>Gewähren des Zugriffs auf TMWS für den Azure AD-Testbenutzer

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf TMWS gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Trend Micro Web Security (TMWS)** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus:

   ![Benutzer und Gruppen auswählen](common/users-groups-blade.png)

1. Wählen Sie die Schaltfläche **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Auswählen von „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste **Benutzer** den Eintrag **B. Simon** aus, und klicken Sie dann am unteren Bildschirmrand auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Konfigurieren der Einstellungen für die Benutzer- und Gruppensynchronisierung in Azure AD

1. Wählen Sie im linken Bereich **Azure Active Directory** aus.

1. Wählen Sie unter **Verwalten** die Option **App-Registrierungen** und dann unter **Alle Anwendungen** Ihre neue Unternehmensanwendung aus.

1. Wählen Sie unter **Verwalten** die Option **Zertifikate und Geheimnisse** aus.

1. Wählen Sie im Bereich **Geheime Clientschlüssel** die Option **Neuer geheimer Clientschlüssel** aus.

1. Fügen Sie auf dem Bildschirm **Geheimen Clientschlüssel hinzufügen** optional eine Beschreibung hinzu, und wählen Sie einen Ablaufzeitraum für den geheimen Clientschlüssel und anschließend **Hinzufügen** aus. Der neue geheime Clientschlüssel wird im Bereich **Geheime Clientschlüssel** angezeigt.

1. Notieren Sie sich den Wert des geheimen Clientschlüssels. Sie geben ihn später in TMWS ein.

1. Wählen Sie unter **Verwalten** die Option **API-Berechtigungen**. 

1. Wählen Sie im Fenster **API-Berechtigungen** die Option **Berechtigung hinzufügen** aus.

1. Wählen Sie im Fenster **API-Berechtigungen anfordern** auf der Registerkarte **Microsoft-APIs** die Option **Microsoft Graph** und dann **Anwendungsberechtigungen** aus.

1. Suchen Sie nach den folgenden Berechtigungen, und fügen Sie sie hinzu: 

    * Group.Read.All
    * User.Read.All

1. Wählen Sie **Berechtigungen hinzufügen** aus. Eine Meldung mit der Bestätigung wird angezeigt, dass die Einstellungen gespeichert wurden. Die neuen Berechtigungen werden im Fenster **API-Berechtigungen** angezeigt.

1. Wählen Sie im Bereich **Einwilligung erteilen** die Option **Administratorzustimmung für *Ihr Administratorkonto* erteilen (Standardverzeichnis)** und dann **Ja** aus. Eine Meldung mit der Bestätigung wird angezeigt, dass die Administratoreinwilligung für die angeforderten Berechtigungen erteilt wurde.

1. Wählen Sie **Übersicht**. 

1. Notieren Sie die **Anwendungs-ID (Client)** und die **Verzeichnis-ID (Mandant)** , die im Bereich auf der rechten Seite angezeigt werden. Sie geben diese Informationen später in TMWS ein. Sie können auch unter **Azure Active Directory > Verwalten** die Option **Namen der benutzerdefinierten Domänen** auswählen und den im rechten Bereich angezeigten Domänennamen notieren.

## <a name="configure-tmws-sso"></a>Konfigurieren des einmaligen Anmeldens für TMWS

Führen Sie die folgenden Schritte aus, um einmaliges Anmelden für TMWS auf der Anwendungsseite zu konfigurieren:

1. Melden Sie sich bei der TMWS-Verwaltungskonsole an, und navigieren Sie zu **Administration** > **USERS & AUTHENTICATION** > **Directory Services** (Verwaltung > BENUTZER & AUTHENTIFIZIERUNG > Verwaltungsdienste).

1. Klicken Sie **hier** im oberen Bildschirmbereich.

1. Wählen Sie auf der Seite **Authentication Method** (Authentifizierungsmethode) die Option **Azure AD** aus.

1. Wählen Sie **On** (Ein) oder **Off** (Aus) aus, um zu konfigurieren, ob das Aufrufen von Websites über TMWS für die Azure AD-Benutzer Ihrer Organisation möglich sein soll, wenn ihre Daten nicht mit TMWS synchronisiert werden.

    > [!NOTE]
    > Benutzer, die nicht aus Azure AD synchronisiert werden, können nur über bekannte TMWS-Gateways oder den dedizierten Port für Ihre Organisation authentifiziert werden.

1. Führen Sie im Abschnitt **Identity Provider Settings** (Identitätsanbietereinstellungen) die folgenden Schritte aus:

    a. Fügen Sie im Feld **Service URL** (Dienst-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    b. Geben Sie im Feld **Logon name attribute** (Anmeldenamensattribut) den **Benutzeranspruchsnamen** mit dem Quellattribut **user.onpremisessamaccountname** aus dem Azure-Portal ein.

    c. Verwenden Sie im Feld **Public SSL certificate** (Öffentliches SSL-Zertifikat) das heruntergeladene **Zertifikat (Base64)** aus dem Azure-Portal.

1. Führen Sie im Abschnitt **Synchronization Settings** (Synchronisierungseinstellungen) die folgenden Schritte aus:

    a. Geben Sie in das Feld **Tenant** (Mandant) den Wert für **Verzeichnis-ID (Mandant)** oder **Benutzerdefinierter Domänenname** aus dem Azure-Portal ein.

    b. Geben Sie in das Feld **Application ID** (Anwendungs-ID) den Wert für **Anwendungs-ID (Client)** aus dem Azure-Portal ein.

    c. Geben Sie in das Feld **Client secret** (Geheimer Clientschlüssel) den Wert für **Geheimer Clientschlüssel** aus dem Azure-Portal ein.

    d. Wählen Sie **Synchronization schedule** (Synchronisierungszeitplan) aus, um die Synchronisierung mit Azure AD manuell oder gemäß einem Zeitplan auszuführen. Wenn Sie **Manuell** auswählen, müssen Sie bei Änderungen an den Informationen der Active Directory-Benutzer zurück zur Seite **Directory Services** (Verzeichnisdienste) navigieren und die manuelle Synchronisierung ausführen, damit die Informationen in TMWS auf dem neuesten Stand bleiben.

    e. Wählen Sie **Test Connection** (Verbindung testen) aus, um zu überprüfen, ob eine Verbindung mit dem Azure AD-Dienst hergestellt werden kann.
    
    f. Wählen Sie **Speichern** aus.
 
 > [!NOTE]
 > Weitere Informationen zum Konfigurieren von TMWS mit Azure AD finden Sie unter [Konfigurieren von Azure AD-Einstellungen in TMWS](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

Nachdem Sie den Azure AD-Dienst konfiguriert und Azure AD als Methode für die Benutzerauthentifizierung angegeben haben, können Sie sich zum Überprüfen des Setups beim TMWS-Proxyserver anmelden. Nachdem bei der Azure AD-Anmeldung Ihr Konto überprüft wurde, können Sie im Internet surfen.

> [!NOTE]
> Das Testen des einmaligen Anmeldens über das Azure AD-Portal unter **Übersicht** > **Einmaliges Anmelden** > **Einmaliges Anmelden (SSO) mit SAML einrichten** > **Test of your new enterprise application** (Neue Unternehmensanwendung testen) wird von TMWS nicht unterstützt.

1. Löschen Sie alle Cookies im Browser, und starten Sie den Browser neu. 

1. Navigieren Sie im Browser zum TMWS-Proxyserver. Weitere Informationen finden Sie unter [Weiterleiten von Datenverkehr mit PAC-Dateien](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B).

1. Rufen Sie eine beliebige Website im Internet auf. TMWS leitet Sie an das Captive Portal von TMWS weiter.

1. Geben Sie ein Active Directory-Konto (Format: *domain*\\*sAMAccountName* oder *sAMAccountName*@*domain*), eine E-Mail-Adresse oder einen UPN an, und wählen Sie dann **Log On** (Anmelden) aus. TMWS leitet Sie zum Azure AD-Anmeldefenster weiter.

1. Geben Sie im Azure AD-Anmeldefenster die Anmeldeinformationen für das Azure AD-Konto ein. Sie sollten jetzt bei TMWS angemeldet werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)

- [Trend Micro Web Security mit Azure AD ausprobieren](https://aad.portal.azure.com/)

- [Was ist Sitzungssteuerung in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Schützen von Apps mit der App-Steuerung für bedingten Zugriff von Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)