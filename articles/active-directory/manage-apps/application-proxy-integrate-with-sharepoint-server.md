---
title: Aktivieren des Remotezugriffs auf SharePoint – Azure AD-Anwendungsproxy
description: Behandelt die Grundlagen zur Integration eines lokalen SharePoint-Servers mit Azure AD-Anwendungsproxy.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/02/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c318c539b1c09761ed81e7602808e415fdaf8b80
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658178"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Aktivieren des Remotezugriffs auf SharePoint per Azure AD-Anwendungsproxy

In dieser Schritt-für-Schritt-Anleitung wird beschrieben, wie Sie eine lokale SharePoint-Farm mit dem Anwendungsproxy von Azure Active Directory (Azure AD) integrieren.

## <a name="prerequisites"></a>Voraussetzungen

Um die Konfiguration vornehmen zu können, benötigen Sie die folgenden Ressourcen:
- Eine SharePoint 2013-Farm (oder neuer).
- Einen Azure AD-Mandanten mit einem Plan, der den Anwendungsproxy enthält. Weitere Informationen zu Azure AD-Tarifen und Preisen finden Sie [hier](https://azure.microsoft.com/pricing/details/active-directory/).
- Eine [benutzerdefinierte überprüfte Domäne](../fundamentals/add-custom-domain.md) im Azure AD-Mandanten.
- Eine mit Azure AD Connect synchronisierte lokale Active Directory-Instanz, über die sich Benutzer [bei Azure anmelden](../hybrid/plan-connect-user-signin.md) können.
- Einen Anwendungsproxyconnector, der auf einem Computer in der Unternehmensdomäne installiert ist und ausgeführt wird.

Für die Konfiguration von SharePoint mit dem Anwendungsproxy sind zwei URLs erforderlich:
- Eine externe URL, die für Endbenutzer sichtbar und in Azure AD festgelegt ist. Diese URL kann eine benutzerdefinierte Domäne enthalten. Weitere Informationen zur Verwendung benutzerdefinierter Domänen im Azure AD-Anwendungsproxy finden Sie [hier](application-proxy-configure-custom-domain.md).
- Eine interne URL, die nur innerhalb der Unternehmensdomäne bekannt ist und nie direkt verwendet wird.

> [!IMPORTANT]
> Halten Sie sich an die folgenden Empfehlungen für die interne URL, um sicherzustellen, dass die Links ordnungsgemäß zugeordnet werden:
> - Verwenden Sie HTTPS.
> - Verwenden Sie keine benutzerdefinierten Ports.
> - Erstellen Sie im Domain Name System (DNS) des Unternehmens keinen Alias (CName), sondern einen Host (A), der auf den SharePoint-WFE (oder Lastenausgleich) verweist.

In diesem Artikel werden folgende Werte verwendet:
- Interne URL: `https://sharepoint`
- Externe URL: `https://spsites-demo1984.msappproxy.net/`
- Anwendungspoolkonto für die SharePoint-Webanwendung: `Contoso\spapppool`

## <a name="step-1-configure-an-application-in-azure-ad-that-uses-application-proxy"></a>Schritt 1: Konfigurieren einer Anwendung in Azure AD, die den Anwendungsproxy verwendet

In diesem Schritt wird in Ihrem Azure Active Directory-Mandanten eine Anwendung erstellt, die den Anwendungsproxy verwendet. Sie legen die externe URL fest und geben die interne URL an. Beide werden später in SharePoint verwendet.

1. Erstellen Sie die App mit den folgenden Einstellungen gemäß der Beschreibung. Eine ausführliche Anleitung finden Sie unter [Veröffentlichen von Anwendungen mit Azure AD-Anwendungsproxy](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   * **Interne URL:** Interne SharePoint-URL, die später in SharePoint festgelegt wird (Beispiel: `https://sharepoint`).
   * **Vorauthentifizierung:** Azure Active Directory
   * **Übersetzen von URLs in Headern**: Nein
   * **URLs übersetzen in Anwendungstext:** Nein

   ![Veröffentlichen von SharePoint als Anwendung](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

1. Nachdem Ihre App veröffentlicht wurde, konfigurieren Sie die Einstellungen für einmaliges Anmelden:

   1. Wählen Sie auf der Anwendungsseite im Portal **Einmaliges Anmelden** aus.
   1. Legen Sie den **Modus für einmaliges Anmelden** auf **Integrierte Windows-Authentifizierung** fest.
   1. Legen Sie **Interner Anwendungs-SPN** auf den zuvor festgelegten Wert fest. In diesem Beispiel lautet der Wert `HTTP/sharepoint`.
   1. Wählen Sie unter **Delegierte Identität für Anmeldung** die am besten geeignete Option für Ihre Active Directory-Gesamtstrukturkonfiguration aus. Wenn Ihre Gesamtstruktur also beispielsweise eine einzelne Active Directory-Domäne enthält, wählen Sie **Name des lokalen SAM-Kontos** aus (wie im folgenden Screenshot zu sehen). Sollten sich Ihre Benutzer allerdings nicht in der gleichen Domäne befinden wie SharePoint und die Anwendungsproxy-Connectorserver, wählen Sie **Lokaler Benutzerprinzipalname** aus (nicht im Screenshot dargestellt).

   ![Konfigurieren der integrierten Windows-Authentifizierung für einmaliges Anmelden](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

1. Zum Abschluss der Anwendungseinrichtung wechseln Sie zum Abschnitt **Benutzer und Gruppen**. Weisen Sie dort Benutzer zu, die Zugriff auf diese Anwendung erhalten. 

## <a name="step-2-configure-the-sharepoint-web-application"></a>Schritt 2: Konfigurieren der SharePoint-Webanwendung

Die SharePoint-Webanwendung muss mit Kerberos und den entsprechenden alternativen Zugriffszuordnungen konfiguriert werden, damit sie ordnungsgemäß mit dem Azure AD-Anwendungsproxy funktioniert. Es gibt zwei mögliche Optionen:

- Erstellen Sie eine neue Webanwendung, und verwenden Sie nur die Standardzone. Dies ist die bevorzugte Option für die optimale Verwendung von SharePoint. (Die Links in den von SharePoint generierten E-Mail-Benachrichtigungen verweisen beispielsweise immer auf die Standardzone.)
- Erweitern Sie eine vorhandene Webanwendung, um Kerberos für eine standardfremde Zone zu konfigurieren.

> [!IMPORTANT]
> Das Anwendungspoolkonto der SharePoint-Webanwendung muss unabhängig von der verwendeten Zone ein Domänenkonto sein, damit Kerberos ordnungsgemäß funktioniert.

### <a name="provision-the-sharepoint-web-application"></a>Bereitstellen der SharePoint-Webanwendung

- Gehen Sie wie folgt vor, wenn Sie eine neue Webanwendung erstellen und nur die Standardzone verwenden (bevorzugte Option):

    1. Starten Sie die **SharePoint-Verwaltungsshell**, und führen Sie das folgende Skript aus:

       ```powershell
       # This script creates a web application and configures the Default zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment. Note that the managed account must exist and it must be a domain account
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       $applicationPoolManagedAccount = "Contoso\spapppool"
            
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = New-SPWebApplication -Name "SharePoint - AAD Proxy" -Port 443 -SecureSocketsLayer -URL $externalUrl -ApplicationPool "SharePoint - AAD Proxy" -ApplicationPoolAccount (Get-SPManagedAccount $applicationPoolManagedAccount) -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Default -Internal
       ```

    2. Öffnen Sie die Website der **SharePoint-Zentraladministration**.
    1. Wählen Sie unter **Systemeinstellungen** die Option **Alternative Zugriffszuordnungen konfigurieren**. Das Feld **Alternative Zugriffszuordnungssammlung** wird geöffnet.
    1. Filtern Sie die Anzeige mit der neuen Webanwendung, und vergewissern Sie sich, dass die Anzeige in etwa wie folgt aussieht:

       ![Alternative Zugriffszuordnungen der Webanwendung](./media/application-proxy-integrate-with-sharepoint-server/new-webapp-aam.png)

- Gehen Sie wie folgt vor, wenn Sie eine vorhandene Webanwendung auf eine neue Zone erweitern (falls Sie die Standardzone nicht verwenden können):

    1. Starten Sie die SharePoint-Verwaltungsshell, und führen Sie das folgende Skript aus:

       ```powershell
       # This script extends an existing web application to Internet zone with the internal/external URL needed to work with Azure AD Application Proxy
       # Edit variables below to fit your environment
       $webAppUrl = "http://spsites/"
       $internalUrl = "https://sharepoint"
       $externalUrl = "https://spsites-demo1984.msappproxy.net/"
       
       $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
       $wa = Get-SPWebApplication $webAppUrl
       New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -Identity $wa -SecureSocketsLayer -Zone Extranet -Url $externalUrl -AuthenticationProvider $winAp
       New-SPAlternateURL -Url $internalUrl -WebApplication $wa -Zone Extranet -Internal
       ```

    2. Öffnen Sie die Website der **SharePoint-Zentraladministration**.
    1. Wählen Sie unter **Systemeinstellungen** die Option **Alternative Zugriffszuordnungen konfigurieren**. Das Feld **Alternative Zugriffszuordnungssammlung** wird geöffnet.
    1. Filtern Sie die Anzeige mit der erweiterten Webanwendung, und vergewissern Sie sich, dass die Anzeige in etwa wie folgt aussieht:

        ![Alternative Zugriffszuordnungen der erweiterten Anwendung](./media/application-proxy-integrate-with-sharepoint-server/extend-webapp-aam.png)

### <a name="make-sure-the-sharepoint-web-application-is-running-under-a-domain-account"></a>Sicherstellen, dass die SharePoint-Webanwendung unter einem Domänenkonto ausgeführt wird

Identifizieren Sie das Konto, unter dem der Anwendungspool der SharePoint-Webanwendung ausgeführt wird, und vergewissern Sie sich, dass es sich dabei um ein Domänenkonto handelt:

1. Öffnen Sie die Website der **SharePoint-Zentraladministration**.
1. Navigieren Sie zu **Sicherheit**, und wählen Sie die Option **Dienstkonten konfigurieren**.
1. Wählen Sie **Webanwendungspool – YourWebApplicationName** aus.

   ![Optionen für das Konfigurieren eines Dienstkontos](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

1. Vergewissern Sie sich, dass im Feld **Wählen Sie ein Konto für diese Komponente aus.** ein Domänenkonto angezeigt wird, und merken Sie sich dieses Konto für den nächsten Schritt.

### <a name="make-sure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Sicherstellen, dass für die IIS-Website der Zone „Extranet“ ein HTTPS-Zertifikat konfiguriert ist

Da die interne URL das HTTPS-Protokoll (`https://SharePoint/`) verwendet, muss auf der IIS-Website (Internet Information Services, Internetinformationsdienste) ein Zertifikat festgelegt werden.

1. Öffnen Sie die Windows PowerShell-Konsole.
1. Führen Sie das folgende Skript aus, um ein selbstsigniertes Zertifikat zu generieren und es dem Computerzertifikatspeicher „MY“ hinzuzufügen:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!IMPORTANT]
   > Selbstsignierte Zertifikate sind nur für Testzwecke geeignet. In Produktionsumgebungen wird dringend empfohlen, stattdessen Zertifikate einer Zertifizierungsstelle zu verwenden.

1. Öffnen Sie die Konsole „Internetinformationsdienste-Manager“.
1. Erweitern Sie den Server in der Strukturansicht, erweitern Sie **Websites**, wählen Sie die Website **SharePoint – AAD-Proxy** aus, und wählen Sie anschließend **Bindungen** aus.
1. Wählen Sie **HTTPS-Bindung** und anschließend **Bearbeiten** aus.
1. Wählen Sie im TLS/SSL-Zertifikatfeld die Zertifikatoption **SharePoint** und anschließend **OK** aus.

Nun können Sie extern über den Azure AD-Anwendungsproxy auf die SharePoint-Website zugreifen.

## <a name="step-3-configure-kerberos-constrained-delegation"></a>Schritt 3: Konfigurieren der eingeschränkten Kerberos-Delegierung

Benutzer authentifizieren sich zunächst bei Azure AD und dann bei SharePoint, wobei diese Authentifizierung mithilfe von Kerberos über den Azure AD-Proxyconnector erfolgt. Damit der Connector ein Kerberos-Token im Namen des Azure AD-Benutzers abrufen kann, muss die eingeschränkte Kerberos-Delegierung (Kerberos Constrained Delegation, KCD) mit Protokollübergang konfiguriert werden. Weitere Informationen zu KCD finden Sie in der [Übersicht über die eingeschränkte Kerberos-Delegierung](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)).

### <a name="set-the-spn-for-the-sharepoint-service-account"></a>Festlegen des SPN für das SharePoint-Dienstkonto

Da in diesem Artikel die interne URL `https://sharepoint` verwendet wird, lautet der Dienstprinzipalname (Service Principal Name, SPN) `HTTP/sharepoint`. Diese Werte müssen durch Werte für Ihre Umgebung ersetzt werden.
Um den SPN `HTTP/sharepoint` für das SharePoint-Anwendungspoolkonto `Contoso\spapppool` zu registrieren, führen Sie den folgenden Befehl als Administrator der Domäne an einer Eingabeaufforderung aus:

`setspn -S HTTP/sharepoint Contoso\spapppool`

Der Befehl `Setspn` sucht vor dem Hinzufügen nach dem SPN. Ist der SPN bereits vorhanden, wird der Fehler **Doppelter SPN-Wert** angezeigt. In diesem Fall sollte der vorhandene SPN ggf. entfernt werden, wenn er nicht unter dem korrekten Anwendungspoolkonto festgelegt ist. Sie können überprüfen, ob der SPN erfolgreich hinzugefügt wurde, indem Sie den Befehl `Setspn` mit der Option „-L“ ausführen. Weitere Informationen zu diesem Befehl finden Sie unter [Setspn](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc731241(v=ws.11)).

### <a name="make-sure-the-connector-is-trusted-for-delegation-to-the-spn-that-was-added-to-the-sharepoint-application-pool-account"></a>Sicherstellen, dass dem Connector bei Delegierungen an den SPN, der dem SharePoint-Anwendungspoolkonto hinzugefügt wurde, vertraut wird

Konfigurieren Sie KCD so, dass der Azure AD-Anwendungsproxydienst Benutzeridentitäten an das SharePoint-Anwendungspoolkonto delegieren kann. Sie konfigurieren KCD, indem Sie den Anwendungsproxyconnector so einrichten, dass er Kerberos-Tickets für Benutzer abruft, die in Azure AD authentifiziert wurden. Anschließend übergibt dieser Server den Kontext an die Zielanwendung (in diesem Fall an SharePoint).

Führen Sie zum Konfigurieren von KCD für jeden Connectorcomputer die folgenden Schritte aus:

1. Melden Sie sich bei einem Domänencontroller als Domänenadministrator an, und öffnen Sie dann „Active Directory-Benutzer und -Computer“.
1. Suchen Sie nach dem Computer, auf dem der Azure AD-Proxyconnector ausgeführt wird. In diesem Beispiel ist das der SharePoint-Server.
1. Doppelklicken Sie auf den Computer, und wählen Sie anschließend die Registerkarte **Delegierung** aus.
1. Stellen Sie sicher, dass die Delegierungsoptionen auf **Computer bei Delegierungen angegebener Dienste vertrauen** festgelegt sind. Wählen Sie dann die Option **Beliebiges Authentifizierungsprotokoll verwenden** aus.
1. Wählen Sie die Schaltfläche **Hinzufügen** und anschließend **Benutzer oder Computer** aus, und suchen Sie nach dem SharePoint-Anwendungspoolkonto. Beispiel: `Contoso\spapppool`.
1. Wählen Sie in der Liste der SPNs denjenigen aus, den Sie zuvor für das Dienstkonto erstellt haben.
1. Wählen Sie **OK** und anschließend erneut **OK** aus, um Ihre Änderungen zu speichern.
  
   ![Delegierungseinstellungen](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

Nun können Sie sich unter Verwendung der externen URL bei SharePoint anmelden und bei Azure authentifizieren.

## <a name="troubleshoot-sign-in-errors"></a>Beheben von Anmeldefehlern

Sollte die Anmeldung bei der Website nicht funktionieren, finden Sie weitere Informationen zum Problem in den Connectorprotokollen: Öffnen Sie die Ereignisanzeige auf dem Computer, auf dem der Connector ausgeführt wird, navigieren Sie zu **Anwendungs- und Dienstprotokolle** > **Microsoft** > **AadApplicationProxy** > **Connector**, und überprüfen Sie das Protokoll **Admin**.

## <a name="next-steps"></a>Nächste Schritte

* [Arbeiten mit benutzerdefinierten Domänen im Azure AD-Anwendungsproxy](application-proxy-configure-custom-domain.md)
* [Grundlegendes zu Azure AD-Anwendungsproxyconnectors](application-proxy-connectors.md)