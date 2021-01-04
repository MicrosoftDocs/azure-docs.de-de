---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit FortiGate SSL VPN | Microsoft-Dokumentation'
description: Lernen Sie die Schritte kennen, die Sie ausführen müssen, um FortiGate SSL VPN in Azure Active Directory (Azure AD) zu integrieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 021550598452516d45ae67c1139c2f891629a875
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296572"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit FortiGate SSL VPN

In diesem Tutorial erfahren Sie, wie Sie FortiGate SSL VPN in Azure Active Directory (Azure AD) integrieren. Die Integration von FortiGate SSL VPN in Azure AD ermöglicht Folgendes:

* Steuern Sie mithilfe von Azure AD, wer auf FortiGate SSL VPN zugreifen kann.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei FortiGate SSL VPN anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* FortiGate SSL VPN-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="tutorial-description"></a>Beschreibung des Tutorials

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

FortiGate SSL VPN unterstützt SP-initiiertes einmaliges Anmelden.

Nach dem Konfigurieren von FortiGate SSL VPN können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.

## <a name="add-fortigate-ssl-vpn-from-the-gallery"></a>Hinzufügen von FortiGate SSL VPN aus dem Katalog

Zum Konfigurieren der Integration von FortiGate SSL VPN in Azure AD müssen Sie FortiGate SSL VPN aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen:

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Bereich **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **FortiGate SSL VPN** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **FortiGate SSL VPN** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für FortiGate SSL VPN

Sie konfigurieren und testen das einmalige Anmelden von Azure AD mit FortiGate SSL VPN mithilfe eines Testbenutzers mit dem Namen B. Simon. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in FortiGate SSL VPN eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit FortiGate SSL VPN diese allgemeinen Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um das Feature für Ihre Benutzer zu aktivieren
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD zu testen.
    1. **[Gewähren des Zugriffs für den Testbenutzer](#grant-access-to-the-test-user)** , um dem Benutzer die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen
1. **[Konfigurieren des einmaligen Anmeldens für FortiGate SSL VPN](#configure-fortigate-ssl-vpn-sso)** auf der Anwendungsseite
    1. **Erstellen eines FortiGate SSL VPN-Testbenutzers** als Pendant zur Azure AD-Darstellung des Benutzers
1. **[Testen des einmaligen Anmeldens](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **FortiGate SSL VPN** im Abschnitt **Verwalten** die Option **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol für **Grundlegende SAML-Konfiguration** aus, um die Einstellungen zu bearbeiten:

   ![Screenshot: Stiftsymbol zum Bearbeiten der grundlegenden SAML-Konfiguration](common/edit-urls.png)

1. Geben Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die folgenden Werte ein:

    a. Geben Sie im Feld **Anmelde-URL** eine URL im folgenden Format ein: `https://<FQDN>/remote/login`.

    b. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein: `https://<FQDN>/remote/saml/metadata`.

    c. Geben Sie im Feld **Antwort-URL** eine URL im folgenden Format ein: `https://<FQDN>/remote/saml/login`.

    d. Geben Sie im Feld **Abmelde-URL** eine URL im folgenden Format ein: `https://<FQDN>/remote/saml/logout`.

    > [!NOTE]
    > Diese Werte sind nur Beispiele. Sie müssen die tatsächlichen Werte für **Anmelde-URL**, **Bezeichner**, **Antwort-URL** und **Abmelde-URL** verwenden. Unterstützung erhalten Sie bei Bedarf vom [Fortinet-Support](https://support.fortinet.com). Sie können sich auch die Beispielmuster in der Fortinet-Dokumentation und den Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die FortiGate SSL VPN-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![Screenshot: Standardattribute](common/default-attributes.png)

1. Die beiden zusätzlichen Ansprüche, die für FortiGate SSL VPN erforderlich sind, sind in der folgenden Tabelle aufgeführt. Die Namen dieser Ansprüche müssen mit den Namen übereinstimmen, die im Abschnitt **Ausführen der FortiGate-Befehlszeilenkonfiguration**  dieses Tutorials verwendet werden. 

   | Name |  Quellattribut|
   | ------------ | --------- |
   | username | user.userprincipalname |
   | Gruppe | user.groups |
   
   So erstellen Sie diese zusätzlichen Ansprüche:
   
   1. Wählen Sie neben **Benutzerattribute & Ansprüche** die Option **Bearbeiten** aus.
   1. Wählen Sie **Neuen Anspruch hinzufügen** aus.
   1. Geben Sie für **Name** die Zeichenfolge **username** ein.
   1. Wählen Sie für **Quellattribut** die Option **user.userprincipalname** aus.
   1. Wählen Sie **Speichern** aus.
   1. Wählen Sie **Gruppenanspruch hinzufügen** aus.
   1. Wählen Sie **Alle Gruppen** aus.
   1. Aktivieren Sie das Kontrollkästchen **Name des Gruppenanspruchs anpassen**.
   1. Geben Sie für **Name** die Zeichenfolge **group** ein.
   1. Wählen Sie **Speichern** aus.   

1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** neben **Zertifikat (Base64)** den Link **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer:

    ![Screenshot: Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **FortiGate SSL VPN einrichten** die entsprechenden URLs gemäß Ihren Anforderungen:

    ![Screenshot: Konfigurations-URLs](common/copy-configuration-urls.png)

#### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Azure-Portals den Dienst **Azure Active Directory** aus. Wählen Sie **Benutzer** und dann **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie in den **Benutzereigenschaften** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** den Namen **B.Simon** ein.  
   1. Geben Sie im Feld **Benutzername** Folgendes ein: \<username>@\<companydomain>.\<extension>. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.
   1. Klicken Sie auf **Erstellen**.

#### <a name="grant-access-to-the-test-user"></a>Gewähren von Zugriff für den Testbenutzer

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf FortiGate SSL VPN gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **FortiGate SSL VPN** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus:

   ![Screenshot: Option „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus:

    ![Screenshot: Schaltfläche „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste **Benutzer** den Eintrag **B. Simon** aus, und klicken Sie dann am unteren Bildschirmrand auf die Schaltfläche **Auswählen**.
1. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Klicken Sie am unteren Bildschirmrand auf die Schaltfläche **Auswählen**.
1. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

#### <a name="create-a-security-group-for-the-test-user"></a>Erstellen einer Sicherheitsgruppe für den Testbenutzer

In diesem Abschnitt erstellen Sie in Azure Active Directory eine Sicherheitsgruppe für den Testbenutzer. FortiGate verwendet diese Sicherheitsgruppe, um dem Benutzer Netzwerkzugriff über das VPN zu gewähren.

1. Wählen Sie im linken Bereich des Azure-Portals den Dienst **Azure Active Directory** aus. Wählen Sie dann **Gruppen** aus.
1. Wählen Sie am oberen Bildschirmrand die Option **Neue Gruppe** aus.
1. Führen Sie in den Eigenschaften für **Neue Gruppe** die folgenden Schritte aus:
   1. Wählen Sie in der Liste **Gruppentyp** die Option **Sicherheit**.
   1. Geben Sie in das Feld **Gruppenname** den Namen **FortiGateAccess** ein.
   1. Geben Sie in das Feld **Gruppenbeschreibung** die Beschreibung **Group for granting FortiGate VPN access** (Gruppe zum Gewähren des FortiGate-VPN-Zugriffs) ein.
   1. Wählen Sie für die Einstellungen **Azure AD-Rollen können der Gruppe zugewiesen werden (Vorschau)** die Option **Nein** aus.
   1. Wählen Sie im Feld **Mitgliedschaftstyp** die Option **Zugewiesen** aus.
   1. Wählen Sie unter **Mitglieder** die Option **Keine Mitglieder ausgewählt** aus.
   1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste **Benutzer** den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
   1. Klicken Sie auf **Erstellen**.
1. Wechseln Sie in Azure Active Directory zurück zum Abschnitt **Gruppen**, navigieren Sie zur Gruppe **FortiGate-Zugriff**, und notieren Sie sich den Wert für **Objekt-ID**. Sie benötigen die Information später.

### <a name="configure-fortigate-ssl-vpn-sso"></a>Konfigurieren des einmaligen Anmeldens für FortiGate SSL VPN

#### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>Hochladen des Base64-SAML-Zertifikats für die FortiGate-Appliance

Nach Abschluss der SAML-Konfiguration der FortiGate-App in Ihrem Mandanten haben Sie das Base64-codierte SAML-Zertifikat heruntergeladen. Sie müssen dieses Zertifikat in die FortiGate-Appliance hochladen:

1. Melden Sie sich beim Verwaltungsportal Ihrer FortiGate-Appliance an.
1. Wählen Sie im linken Bereich **System** aus.
1. Wählen Sie unter **System** die Option **Certificates** (Zertifikate) aus.
1. Wählen Sie **Import** > **Remote Certificate** (Importieren > Remotezertifikat) aus.
1. Navigieren Sie zu dem Zertifikat, das bei der FortiGate-App-Bereitstellung im Azure-Mandanten heruntergeladen wurde, wählen Sie es aus, und wählen Sie dann **OK** aus.

Nachdem das Zertifikat hochgeladen wurde, notieren Sie sich den Namen, der unter **System** > **Certificates** > **Remote Certificate** (System > Zertifikate > Remotezertifikat) angezeigt wird. Der Standardname lautet „REMOTE_Cert_ *N*“. *N* ist dabei eine ganze Zahl.

#### <a name="complete-fortigate-command-line-configuration"></a>Abschließen der Befehlszeilenkonfiguration für FortiGate

Für die folgenden Schritte müssen Sie die Azure-Abmelde-URL konfigurieren. Diese URL enthält ein Fragezeichen (?). Sie müssen bestimmte Schritte ausführen, damit dieses Zeichen erfolgreich übermittelt werden kann. Sie können diese Schritte nicht über die CLI-Konsole von FortiGate ausführen. Richten Sie stattdessen mithilfe eines Tools wie PuTTY eine SSH-Sitzung mit der FortiGate-Appliance ein. Wenn es sich bei Ihrer FortiGate-Appliance um eine Azure-VM handelt, können Sie die folgenden Schritte über die serielle Konsole für Azure-VMs ausführen.

Für diese Schritte benötigen Sie die zuvor notierten Werte:

- Entitäts-ID
- Antwort-URL
- Abmelde-URL
- Azure-Anmelde-URL
- Azure AD-Bezeichner
- Azure-Abmelde-URL
- Name des Base64-SAML-Zertifikats (REMOTE_Cert_ *N*)

1. Richten Sie eine SSH-Sitzung mit der FortiGate-Appliance ein, und melden Sie sich mit einem FortiGate-Administratorkonto an.
1. Führen Sie diese Befehle aus:

   ```console
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert <Base64 SAML Certificate Name>
    set user-name username
    set group-name group
    end

   ```

   > [!NOTE]
   > Die Azure-Abmelde-URL enthält das Zeichen `?`. Sie müssen eine spezielle Schlüsselsequenz eingeben, um diese URL ordnungsgemäß an die serielle Konsole von FortiGate zu übermitteln. Die URL ist in der Regel `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`.
   >
   > Geben Sie die Azure-Abmelde-URL folgendermaßen an der seriellen Konsole ein: `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation`.
   > 
   > Fügen Sie dann die restliche URL per STRG+V ein, um die Zeile zu vervollständigen: `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`.

#### <a name="configure-fortigate-for-group-matching"></a>Konfigurieren von FortiGate für den Gruppenabgleich

In diesem Abschnitt konfigurieren Sie FortiGate so, dass die Objekt-ID der Sicherheitsgruppe erkannt wird, die den Testbenutzer enthält. Mit dieser Konfiguration kann FortiGate Entscheidungen basierend auf der Gruppenmitgliedschaft treffen.

Für diese Schritte benötigen Sie die Objekt-ID der zuvor in diesem Tutorial erstellten Sicherheitsgruppe „FortiGateAccess“.

1. Richten Sie eine SSH-Sitzung mit der FortiGate-Appliance ein, und melden Sie sich mit einem FortiGate-Administratorkonto an.
1. Führen Sie diese Befehle aus:

   ```
    config user group
    edit FortiGateAccess
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <Object Id>
    next
    end
    next
    end
   ```

#### <a name="create-a-fortigate-vpn-portals-and-firewall-policy"></a>Erstellen von VPN-Portalen und einer Firewallrichtlinie in FortiGate

In diesem Abschnitt konfigurieren Sie VPN-Portale und eine Firewallrichtlinie in FortiGate, um Zugriff auf die weiter oben in diesem Tutorial erstellte Sicherheitsgruppe „FortiGateAccess“ zu gewähren.

Wenden Sie sich an das [FortiGate-Supportteam](mailto:tac_amer@fortinet.com), um die VPN-Portale und Firewallrichtlinie zur FortiGate-VPN-Plattform hinzuzufügen. Sie müssen diesen Schritt ausführen, bevor Sie einmaliges Anmelden verwenden.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich die Kachel „FortiGate SSL VPN“ auswählen, sollten Sie automatisch bei der FortiGate SSL VPN-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

Für ein optimales Benutzererlebnis empfehlen Microsoft und FortiGate die Verwendung des Fortinet-VPN-Clients: FortiClient.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)

- [FortiGate SSL VPN mit Azure AD ausprobieren](https://aad.portal.azure.com/)

- [Was ist Sitzungssteuerung in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
