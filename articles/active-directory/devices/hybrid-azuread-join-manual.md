---
title: Manuelles Konfigurieren von in Azure Active Directory eingebundenen Hybridgeräten | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie in Azure Active Directory eingebundene Hybridgeräte manuell konfigurieren.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 651e7156faf8305edb0a1541e957dd2abf3a71b8
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365751"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-joined-devices-manually"></a>Tutorial: Manuelles Konfigurieren von in Azure Active Directory eingebundenen Hybridgeräten

Mit der Geräteverwaltung in Azure Active Directory (Azure AD) können Sie sicherstellen, dass Benutzer auf Ihre Ressourcen über Geräte zugreifen, die Ihren Standards für Sicherheit und Konformität entsprechen. Weitere Informationen finden Sie unter [Einführung in die Geräteverwaltung in Azure Active Directory](overview.md).

> [!TIP]
> Wenn die Verwendung von Azure AD Connect eine Option für Sie ist, sehen Sie sich die entsprechenden Tutorials für [verwaltete Domänen](hybrid-azuread-join-managed-domains.md) bzw. [Verbunddomänen](hybrid-azuread-join-federated-domains.md) an. Durch die Verwendung von Azure AD Connect können Sie die Konfiguration einer Azure AD-Hybrideinbindung erheblich vereinfachen.

Wenn Sie in einer lokalen Active Directory-Umgebung Ihre in die Domäne eingebundenen Geräte in Azure AD einbinden möchten, kann dies durch Konfigurieren von in Azure AD eingebundenen Hybridgeräten erfolgen. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Manuelles Konfigurieren der Azure AD-Hybrideinbindung
> * Konfigurieren eines Dienstverbindungspunkts
> * Einrichten der Ausstellung von Ansprüchen
> * Aktivieren von kompatiblen Windows-Geräten
> * Überprüfen der eingebundenen Geräte
> * Problembehandlung bei der Implementierung

## <a name="prerequisites"></a>Voraussetzungen

In diesem Tutorial wird vorausgesetzt, dass Sie mit Folgendem vertraut sind:

* [Einführung in die Geräteverwaltung in Azure Active Directory](./overview.md)
* [Planen der Implementierung von Azure Active Directory (Azure AD) Hybrid Join](hybrid-azuread-join-plan.md)
* [Steuern der Azure AD-Hybrideinbindung für Ihre Geräte](hybrid-azuread-join-control.md)

Vergewissern Sie sich, dass folgende Voraussetzungen erfüllt sind, bevor Sie in Ihrem Unternehmen mit der Aktivierung von in Azure AD eingebundenen Hybridgeräten beginnen:

* Sie verwenden eine aktuelle Version von Azure AD Connect.
* Azure AD Connect hat die Computerobjekte der Geräte für die Azure AD-Hybrideinbindung mit Azure AD synchronisiert. Wenn die Computerobjekte zu bestimmten Organisationseinheiten (OEs) gehören, müssen diese OEs ebenfalls für die Synchronisierung in Azure AD Connect konfiguriert werden.

Azure AD Connect:

* Pflegt die Zuordnung zwischen dem Computerkonto in Ihrer lokalen AD-Instanz und dem Geräteobjekt in Azure AD.
* Ermöglicht die Verwendung weiterer gerätebezogener Features (etwa Windows Hello for Business).

Stellen Sie sicher, dass Computer innerhalb Ihres Unternehmensnetzwerks zur Registrierung von Computern bei Azure AD auf die folgenden URLs zugreifen können:

* `https://enterpriseregistration.windows.net`
* `https://login.microsoftonline.com`
* `https://device.login.microsoftonline.com`
* Der STS Ihrer Organisation (für Verbunddomänen; muss in die lokalen Intraneteinstellungen des Benutzers eingeschlossen werden)

> [!WARNING]
> Wenn Ihre Organisation Proxyserver verwendet, die SSL-Datenverkehr für Szenarien wie die Verhinderung von Datenverlust oder Azure AD-Mandanteneinschränkungen abfangen, stellen Sie sicher, dass der Datenverkehr zu „https://device.login.microsoftonline.com“ von TLSI (TLS break and inspect) ausgeschlossen ist. Wird „https://device.login.microsoftonline.com“ nicht ausgeschlossen, kann dies zu Beeinträchtigungen bei der Clientzertifikatauthentifizierung führen und Probleme bei der Geräteregistrierung und beim gerätebasierten bedingten Zugriff verursachen.

Falls Ihre Organisation die Verwendung von nahtlosem einmaligem Anmelden beabsichtigt, muss die folgende URL für Computer in Ihrer Organisation erreichbar sein. Sie muss ebenfalls der lokalen Intranetzone des Benutzers hinzugefügt werden.

* `https://autologon.microsoftazuread-sso.com`

Darüber hinaus muss in der Intranetzone des Benutzers die folgende Einstellung aktiviert werden: „Statusleistenupdates über Skript zulassen“.

Wenn Ihre Organisation die verwaltete Einrichtung (ohne Verbund) über lokales Active Directory und nicht Active Directory Federation Services (AD FS) zum Herstellen eines Verbunds mit Azure AD verwendet, basiert die Azure AD-Hybrideinbindung unter Windows 10 auf den Computerobjekten in Active Directory, die mit Azure AD synchronisiert werden sollen. Stellen Sie sicher, dass alle Organisationseinheiten, die die Computerobjekte enthalten, die hybrid in Azure AD eingebunden werden müssen, in der Azure AD Connect-Synchronisierungskonfiguration für die Synchronisierung aktiviert sind.

Wenn Ihre Organisation für Windows 10-Geräte mit Version 1703 oder einer älteren Version Zugriff auf das Internet über einen ausgehenden Proxy benötigt, müssen Sie die automatische Ermittlung von Webproxys (Web Proxy Auto-Discovery, WPAD) implementieren, damit Windows 10-Computer bei Azure AD registriert werden können.

Ab Version 1803 von Windows 10 versucht das Gerät, die Azure AD-Hybrideinbindung unter Verwendung des synchronisierten Computers/Geräts durchzuführen, auch wenn eine Azure AD-Hybrideinbindung durch ein Gerät in einer Verbunddomäne mit AD FS nicht erfolgreich war (sofern Azure AD Connect für die Synchronisierung des Computer-/Geräteobjekts mit Azure AD konfiguriert ist).

Sie können das Skript zum [Testen der Geräteregistrierungskonnektivität](https://docs.microsoft.com/samples/azure-samples/testdeviceregconnectivity/testdeviceregconnectivity/) verwenden, um zu überprüfen, ob das Gerät unter dem Systemkonto auf die oben genannten Microsoft-Ressourcen zugreifen kann.

## <a name="verify-configuration-steps"></a>Überprüfen der Konfigurationsschritte

Sie können in Azure AD eingebundene Hybridgeräte für verschiedene Windows-Geräteplattformen konfigurieren. Dieses Thema enthält die erforderlichen Schritte für alle typischen Konfigurationsszenarien.  

Verwenden Sie die folgende Tabelle, um eine Übersicht über die Schritte zu erhalten, die für Ihr Szenario erforderlich sind:  

| Schritte | Aktuelle Windows-Geräte und Kennworthashsynchronisierung | Aktuelle Windows-Geräte und Verbund | Kompatible Windows-Geräte |
| :--- | :---: | :---: | :---: |
| Konfigurieren des Dienstverbindungspunkts | ![Prüfen][1] | ![Prüfen][1] | ![Prüfen][1] |
| Einrichten der Ausstellung von Ansprüchen |     | ![Prüfen][1] | ![Prüfen][1] |
| Aktivieren von Geräten, auf denen nicht Windows 10 ausgeführt wird |       |        | ![Prüfen][1] |
| Überprüfen der eingebundenen Geräte | ![Prüfen][1] | ![Prüfen][1] | ![Prüfen][1] |

## <a name="configure-a-service-connection-point"></a>Konfigurieren eines Dienstverbindungspunkts

Ihre Geräte verwenden im Rahmen der Registrierung ein SCP-Objekt (Service Connection Point, Dienstverbindungspunkt), um Informationen zum Azure AD-Mandanten zu ermitteln. In Ihrer lokalen Active Directory-Instanz muss sich das SCP-Objekt für die in Azure AD eingebundenen Hybridgeräte in der Partition für den Konfigurationsnamenskontext der Computergesamtstruktur befinden. Es gibt nur einen Konfigurationsnamenskontext pro Gesamtstruktur. In einer Active Directory-Konfiguration mit mehreren Gesamtstrukturen muss der Dienstverbindungspunkt in allen Gesamtstrukturen vorhanden sein, die in die Domäne eingebundene Computer enthalten.

Sie können das [**Get-ADRootDSE**](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee617246(v=technet.10))-Cmdlet verwenden, um den Konfigurationsnamenskontext aus Ihrer Gesamtstruktur abzurufen.  

Für eine Gesamtstruktur mit dem Active Directory-Domänennamen *fabrikam.com* lautet der Konfigurationsnamenskontext:

`CN=Configuration,DC=fabrikam,DC=com`

In Ihrer Gesamtstruktur befindet sich das SCP-Objekt für die automatische Registrierung von in die Domäne eingebundenen Geräten unter:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

Je nachdem, wie Sie Azure AD Connect bereitgestellt haben, wurde das SCP-Objekt unter Umständen bereits konfiguriert.
Mit dem folgenden Windows PowerShell-Skript können Sie das Vorhandensein des Objekts überprüfen und die Ermittlungswerte abrufen:

   ```PowerShell
   $scp = New-Object System.DirectoryServices.DirectoryEntry;

   $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

   $scp.Keywords;
   ```

Die Ausgabe von **$scp.Keywords** enthält die Azure AD-Mandanteninformationen. Hier sehen Sie ein Beispiel:

   ```
   azureADName:microsoft.com
   azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47
   ```

Wenn der Dienstverbindungsendpunkt nicht vorhanden ist, können Sie ihn erstellen, indem Sie das `Initialize-ADSyncDomainJoinedComputerSync`-Cmdlet auf Ihrem Azure AD Connect-Server ausführen. Für die Ausführung dieses Cmdlets sind die Anmeldeinformationen eines Unternehmensadministrators erforderlich.  

Für das Cmdlet gilt Folgendes:

* Erstellt den Dienstverbindungspunkt in der Active Directory-Gesamtstruktur, mit der Azure AD Connect verbunden ist.
* Erfordert, dass Sie den Parameter `AdConnectorAccount` angeben. Dieses Konto ist als das Active Directory-Connectorkonto in Azure AD Connect konfiguriert.


Mit dem folgenden Skript wird ein Beispiel für die Verwendung des Cmdlets veranschaulicht. In diesem Skript ist für `$aadAdminCred = Get-Credential` die Eingabe eines Benutzernamens erforderlich. Sie müssen den Benutzernamen im UPN-Format (Benutzerprinzipalname) eingeben: `user@example.com`.

   ```PowerShell
   Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

   $aadAdminCred = Get-Credential;

   Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;
   ```

Für das `Initialize-ADSyncDomainJoinedComputerSync`-Cmdlet gilt Folgendes:

* Es verwendet das Active Directory-PowerShell-Modul und die Tools von Azure Active Directory Domain Services (Azure AD DS). Diese Tools basieren auf Active Directory-Webdiensten, die auf einem Domänencontroller ausgeführt werden. Active Directory-Webdienste werden auf Domänencontrollern mit Windows Server 2008 R2 und höher unterstützt.
* Es wird nur von der MSOnline PowerShell-Modulversion 1.1.166.0 unterstützt. Dieses Modul können Sie [hier](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0) herunterladen.
* Wenn die AD DS-Tools nicht installiert sind, ist `Initialize-ADSyncDomainJoinedComputerSync` nicht erfolgreich. Die AD DS-Tools können über den Server-Manager unter **Features** > **Remoteserver-Verwaltungstools** > **Rollenverwaltungstools** installiert werden.

Verwenden Sie für Domänencontroller mit Windows Server 2008 oder älteren Versionen das folgende Skript, um den Dienstverbindungspunkt zu erstellen. Verwenden Sie bei einer Active Directory-Konfiguration mit mehreren Gesamtstrukturen das folgende Skript, um den Dienstverbindungsendpunkt in allen Gesamtstrukturen zu erstellen, in denen Computer vorhanden sind.

   ```PowerShell
   $verifiedDomain = "contoso.com" # Replace this with any of your verified domain names in Azure AD
   $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47" # Replace this with you tenant ID
   $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com" # Replace this with your Active Directory configuration naming context

   $de = New-Object System.DirectoryServices.DirectoryEntry
   $de.Path = "LDAP://CN=Services," + $configNC
   $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
   $deDRC.CommitChanges()

   $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
   $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
   $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

   $deSCP.CommitChanges()
   ```

Im obigen Skript ist `$verifiedDomain = "contoso.com"` ein Platzhalter. Ersetzen Sie ihn durch einen Ihrer überprüften Domänennamen in Azure AD. Sie müssen die Domäne besitzen, um sie verwenden zu können.

Weitere Informationen zu überprüften Domänennamen finden Sie unter [Hinzufügen eines benutzerdefinierten Domänennamens zu Azure Active Directory](../fundamentals/add-custom-domain.md).

Zum Abrufen einer Liste mit Ihren überprüften Unternehmensdomänen können Sie das Cmdlet [Get-AzureADDomain](/powershell/module/Azuread/Get-AzureADDomain) verwenden.

![Liste mit Unternehmensdomänen](./media/hybrid-azuread-join-manual/01.png)

## <a name="set-up-issuance-of-claims"></a>Einrichten der Ausstellung von Ansprüchen

In einer Azure AD-Verbundkonfiguration benötigen die Geräte für die Authentifizierung gegenüber Azure AD entweder AD FS oder einen lokalen Verbunddienst eines Microsoft-Partners. Die Geräte führen die Authentifizierung durch, um ein Zugriffstoken für die Registrierung beim Geräteregistrierungsdienst von Azure Active Directory (Azure DRS) zu erhalten.

Bei aktuellen Windows-Geräten erfolgt die Authentifizierung mittels integrierter Windows-Authentifizierung gegenüber einem aktiven WS-Trust-Endpunkt (entweder Version 1.3 oder 2005), der vom lokalen Verbunddienst gehostet wird.

Bei Verwendung von AD FS müssen Sie die folgenden WS-Trust-Endpunkte aktivieren:
- `/adfs/services/trust/2005/windowstransport`
- `/adfs/services/trust/13/windowstransport`
- `/adfs/services/trust/2005/usernamemixed`
- `/adfs/services/trust/13/usernamemixed`
- `/adfs/services/trust/2005/certificatemixed`
- `/adfs/services/trust/13/certificatemixed`

> [!WARNING]
> Die Endpunkte **adfs/services/trust/2005/windowstransport** und **adfs/services/trust/13/windowstransport** dürfen nur als Endpunkte mit Intranetzugriff aktiviert und NICHT als Endpunkte mit Extranetzugriff über den Webanwendungsproxy verfügbar gemacht werden. Weitere Informationen zum Deaktivieren von WS-Trust-Windows-Endpunkten finden Sie unter [Deaktivieren von WS-Trust-Windows-Endpunkten auf dem Proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Welche Endpunkte aktiviert sind, sehen Sie in der AD FS-Verwaltungskonsole unter **Dienst** > **Endpunkte**.

> [!NOTE]
>Falls Sie nicht AD FS als lokalen Verbunddienst nutzen, informieren Sie sich in der Anleitung Ihres jeweiligen Anbieters, ob WS-Trust 1.3- oder 2005-Endpunkte unterstützt und per MEX-Datei (Metadata Exchange) veröffentlicht werden.

Damit die Geräteregistrierung abgeschlossen werden kann, müssen in dem Token, das von Azure DRS empfangen wird, folgende Ansprüche enthalten sein. Azure DRS erstellt in Azure AD ein Geräteobjekt mit einigen dieser Informationen. Diese werden dann von Azure AD Connect verwendet, um das neu erstellte Geräteobjekt dem lokalen Computerkonto zuzuordnen.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Wenn Sie über mehr als einen verifizierten Domänennamen verfügen, müssen Sie für Computer den folgenden Anspruch angeben:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Falls Sie bereits einen ImmutableID-Anspruch ausstellen (z. B. mit einer `mS-DS-ConsistencyGuid` oder einem anderen Attribut als Quellwert für die ImmutableID), müssen Sie einen entsprechenden Anspruch für Computer bereitstellen:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

Die folgenden Abschnitte enthalten Informationen zu diesen Punkten:

* Werte, über die jeder Anspruch verfügen muss
* Definition in AD FS

Mithilfe der Definition können Sie überprüfen, ob die Werte vorhanden sind oder ob Sie sie erstellen müssen.

> [!NOTE]
> Wenn Sie als lokalen Verbundserver nicht AD FS nutzen, sollten Sie die Anleitung Ihres Anbieters befolgen, um die entsprechende Konfiguration zum Ausgeben dieser Ansprüche zu erstellen.

### <a name="issue-account-type-claim"></a>Ausstellen des Kontotypanspruchs

Der Anspruch `http://schemas.microsoft.com/ws/2012/01/accounttype` muss den Wert **DJ** enthalten, mit dem das Gerät als in die Domäne eingebundener Computer identifiziert wird. In AD FS können Sie eine Ausstellungstransformationsregel hinzufügen, die wie folgt aussieht:

   ```
   @RuleName = "Issue account type for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "DJ"
   );
   ```

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>Ausstellen der objectGUID des lokalen Computerkontos

Der Anspruch `http://schemas.microsoft.com/identity/claims/onpremobjectguid` muss den **objectGUID**-Wert des lokalen Computerkontos enthalten. In AD FS können Sie eine Ausstellungstransformationsregel hinzufügen, die wie folgt aussieht:

   ```
   @RuleName = "Issue object GUID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$", 
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"),
      query = ";objectguid;{0}",
      param = c2.Value
   );
   ```

### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>Ausstellen der objectSID des lokalen Computerkontos

Der Anspruch `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid` muss den **objectSid**-Wert des lokalen Computerkontos enthalten. In AD FS können Sie eine Ausstellungstransformationsregel hinzufügen, die wie folgt aussieht:

   ```
   @RuleName = "Issue objectSID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(claim = c2);
   ```

### <a name="issue-issuerid-for-the-computer-when-multiple-verified-domain-names-are-in-azure-ad"></a>Ausstellen der Aussteller-ID (issuerID) für den Computer, wenn in Azure AD mehrere überprüfte Domänennamen enthalten sind

Der Anspruch `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid` muss den URI (Uniform Resource Identifier) eines der überprüften Domänennamen enthalten, für die eine Verbindung mit dem lokalen Verbunddienst (AD FS oder Partner) hergestellt wird, der das Token ausstellt. In AD FS können Sie Ausstellungstransformationsregeln hinzufügen, die wie die unten angegebenen Beispiele aussehen (in dieser spezifischen Reihenfolge und im Anschluss an die vorhergehenden Regeln). Beachten Sie, dass eine Regel erforderlich ist, die dafür sorgt, dass die Regel explizit für Benutzer ausgestellt wird. In den folgenden Regeln wird eine erste Regel hinzugefügt, die den Benutzer identifiziert (im Gegensatz zur Computerauthentifizierung).

   ```
   @RuleName = "Issue account type with the value User when its not a computer"
   NOT EXISTS(
   [
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "DJ"
   ]
   )
   => add(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "User"
   );

   @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
   c1:[
      Type == "http://schemas.xmlsoap.org/claims/UPN"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "User"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = regexreplace(
      c1.Value,
      ".+@(?<domain>.+)",
      "http://${domain}/adfs/services/trust/"
      )
   );

   @RuleName = "Issue issuerID for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = "http://<verified-domain-name>/adfs/services/trust/"
   );
   ```

Im obigen Anspruch ist `<verified-domain-name>` ein Platzhalter. Ersetzen Sie ihn durch einen Ihrer überprüften Domänennamen in Azure AD. Verwenden Sie z. B. `Value = "http://contoso.com/adfs/services/trust/"`.

Weitere Informationen zu überprüften Domänennamen finden Sie unter [Hinzufügen eines benutzerdefinierten Domänennamens zu Azure Active Directory](../fundamentals/add-custom-domain.md).  

Zum Abrufen einer Liste mit Ihren überprüften Unternehmensdomänen können Sie das [Get-MsolDomain](/powershell/module/msonline/get-msoldomain)-Cmdlet verwenden.

![Liste mit Unternehmensdomänen](./media/hybrid-azuread-join-manual/01.png)

### <a name="issue-immutableid-for-the-computer-when-one-for-users-exists-for-example-using-ms-ds-consistencyguid-as-the-source-for-immutableid"></a>Ausstellen der unveränderlichen ID (ImmutableID) für den Computer, wenn eine für Benutzer vorhanden ist (beispielsweise, unter Verwendung einer ms-DS-ConsistencyGuid als Quelle für die ImmutableID)

Der Anspruch `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID` muss einen gültigen Wert für Computer enthalten. In AD FS können Sie wie folgt eine Ausstellungstransformationsregel erstellen:

   ```
   @RuleName = "Issue ImmutableID for computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"),
      query = ";objectguid;{0}",
      param = c2.Value
   );
   ```

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Hilfsskript zum Erstellen der AD FS-Ausstellungstransformationsregeln

Das folgende Skript unterstützt Sie beim Erstellen der oben beschriebenen Ausstellungstransformationsregeln.

   ```
   $multipleVerifiedDomainNames = $false
   $immutableIDAlreadyIssuedforUsers = $false
   $oneOfVerifiedDomainNames = 'example.com'   # Replace example.com with one of your verified domains

   $rule1 = '@RuleName = "Issue account type for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "DJ"
   );'

   $rule2 = '@RuleName = "Issue object GUID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"),
      query = ";objectguid;{0}",
      param = c2.Value
   );'

   $rule3 = '@RuleName = "Issue objectSID for domain-joined computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(claim = c2);'

   $rule4 = ''
   if ($multipleVerifiedDomainNames -eq $true) {
   $rule4 = '@RuleName = "Issue account type with the value User when it is not a computer"
   NOT EXISTS(
   [
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "DJ"
   ]
   )
   => add(
      Type = "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value = "User"
   );

   @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
   c1:[
      Type == "http://schemas.xmlsoap.org/claims/UPN"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2012/01/accounttype",
      Value == "User"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = regexreplace(
      c1.Value,
      ".+@(?<domain>.+)",
      "http://${domain}/adfs/services/trust/"
      )
   );

   @RuleName = "Issue issuerID for domain-joined computers"
   c:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid",
      Value = "http://' + $oneOfVerifiedDomainNames + '/adfs/services/trust/"
   );'
   }

   $rule5 = ''
   if ($immutableIDAlreadyIssuedforUsers -eq $true) {
   $rule5 = '@RuleName = "Issue ImmutableID for computers"
   c1:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid",
      Value =~ "-515$",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   &&
   c2:[
      Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname",
      Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
   ]
   => issue(
      store = "Active Directory",
      types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"),
      query = ";objectguid;{0}",
      param = c2.Value
   );'
   }

   $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

   $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5

   $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

   Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString
   ```

#### <a name="remarks"></a>Bemerkungen

* Mit diesem Skript werden die Regeln an die bereits vorhandenen Regeln angefügt. Führen Sie das Skript nicht zweimal aus. Andernfalls wird der Regelsatz doppelt hinzugefügt. Stellen Sie sicher, dass keine entsprechenden Regeln für diese Ansprüche vorhanden sind (unter den jeweiligen Bedingungen), bevor Sie das Skript erneut ausführen.
* Wenn Sie über mehrere verifizierte Domänennamen verfügen (wie im Azure AD-Portal oder über das Cmdlet **Get-MsolDomain** angegeben), legen Sie den Wert von **$multipleVerifiedDomainNames** im Skript auf **$true** fest. Entfernen Sie außerdem alle vorhandenen **issuerid**-Ansprüche, die unter Umständen von Azure AD Connect oder auf anderem Wege erstellt wurden. Hier sehen Sie ein Beispiel für diese Regel:

   ```
   c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
   => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 
   ```

Wenn Sie bereits einen **ImmutableID**-Anspruch für Benutzerkonten ausgestellt haben, legen Sie den Wert von **$immutableIDAlreadyIssuedforUsers** im Skript auf **$true** fest.

## <a name="enable-windows-down-level-devices"></a>Aktivieren von kompatiblen Windows-Geräten

Wenn es sich bei einigen Ihrer in die Domäne eingebundenen Geräte um kompatible Windows-Geräte handelt, gehen Sie wie folgt vor:

* Legen Sie eine Richtlinie in Azure AD fest, um Benutzern das Registrieren von Geräten zu ermöglichen.
* Konfigurieren Sie Ihren lokalen Verbunddienst für das Ausstellen von Ansprüchen, um die integrierte Windows-Authentifizierung (IWA) für die Geräteregistrierung zu unterstützen.
* Fügen Sie den Endpunkt für die Azure AD-Geräteauthentifizierung den lokalen Intranetzonen hinzu, um beim Authentifizieren des Geräts Zertifikataufforderungen zu vermeiden.
* Steuern Sie kompatible Windows-Geräte.

### <a name="set-a-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Festlegen einer Richtlinie in Azure AD, um Benutzern das Registrieren von Geräten zu ermöglichen

Wenn Sie kompatible Windows-Geräte registrieren möchten, vergewissern Sie sich, dass die Einstellung aktiviert ist, die es Benutzern ermöglicht, Geräte in Azure AD zu registrieren. Im Azure-Portal befindet sich diese Einstellung unter **Azure Active Directory** > **Benutzer und Gruppen** > **Geräteeinstellungen**.

Die folgende Richtlinie muss auf **Alle** festgelegt sein: **Benutzer dürfen ihre Geräte für Azure AD registrieren**.

![Die Schaltfläche „Alle“, die Benutzern die Geräteregistrierung ermöglicht.](./media/hybrid-azuread-join-manual/23.png)

### <a name="configure-the-on-premises-federation-service"></a>Konfigurieren des lokalen Verbunddiensts

Ihr lokaler Verbunddienst muss das Ausstellen der Ansprüche **authenticationmethod** und **wiaormultiauthn** unterstützen, wenn er eine an die vertrauende Seite von Azure AD gerichtete Authentifizierungsanforderung empfängt, die den Parameter „resource_params“ mit dem folgenden codierten Wert enthält:

   ```
   eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

   which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}
   ```

Wenn eine Anforderung dieser Art eingeht, muss der lokale Verbunddienst den Benutzer per integrierter Windows-Authentifizierung authentifizieren. Nach erfolgreicher Authentifizierung muss der Verbunddienst die beiden folgenden Ansprüche ausstellen:

   `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows` `http://schemas.microsoft.com/claims/wiaormultiauthn`

In AD FS müssen Sie eine Ausstellungstransformationsregel hinzufügen, die die Authentifizierungsmethode durchläuft. Diese Regel wird wie folgt hinzugefügt:

1. Navigieren Sie in der AD FS-Verwaltungskonsole zu **AD FS** > **Vertrauensstellungen** > **Vertrauensstellungen der vertrauenden Seite**.
1. Klicken Sie mit der rechten Maustaste auf das Vertrauensstellungsobjekt der vertrauenden Seite, „Microsoft Office 365 Identity Platform“, und wählen Sie dann **Anspruchsregeln bearbeiten** aus.
1. Wählen Sie auf der Registerkarte **Ausstellungstransformationsregeln** die Option **Regel hinzufügen** aus.
1. Wählen Sie in der Vorlagenliste **Anspruchsregel** die Option **Ansprüche mit benutzerdefinierter Regel senden** aus.
1. Wählen Sie **Weiter** aus.
1. Geben Sie **Anspruchsregel für Authentifizierungsmethode** in das Textfeld **Anspruchsregelname** ein.
1. Geben Sie im Feld **Anspruchsregel** die folgende Regel ein:

   `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

1. Geben Sie auf Ihrem Verbundserver den folgenden PowerShell-Befehl ein. Ersetzen Sie **\<RPObjectName\>** durch den Objektnamen der vertrauenden Seite für Ihr Azure AD-Vertrauensstellungsobjekt der vertrauenden Seite. Dieses Objekt trägt normalerweise den Namen **Microsoft Office 365 Identity Platform**.

   `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-endpoint-to-the-local-intranet-zones"></a>Hinzufügen des Endpunkts für die Azure AD-Geräteauthentifizierung zu den lokalen Intranetzonen

Um die Anzeige von Zertifikataufforderungen zu vermeiden, wenn Benutzer beim Registrieren von Geräten die Authentifizierung für Azure AD durchführen, können Sie per Push eine Richtlinie an Ihre in die Domäne eingebundenen Geräte übertragen, um die folgende URL den lokalen Intranetzonen in Internet Explorer hinzuzufügen:

`https://device.login.microsoftonline.com`

### <a name="control-windows-down-level-devices"></a>Steuern kompatibler Windows-Geräte

Zum Registrieren von kompatiblen Windows-Geräten müssen Sie ein Windows Installer-Paket (.msi) aus dem Download Center herunterladen und installieren. Weitere Informationen finden Sie unter [Steuern der Azure AD-Hybrideinbindung für Ihre Geräte](hybrid-azuread-join-control.md#controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices).

## <a name="verify-joined-devices"></a>Überprüfen der eingebundenen Geräte

Im Folgenden finden Sie drei Möglichkeiten, den Gerätezustand zu finden und zu überprüfen:

### <a name="locally-on-the-device"></a>Lokal auf dem Gerät

1. Öffnen Sie Windows PowerShell.
2. Geben Sie `dsregcmd /status` ein.
3. Überprüfen Sie, ob sowohl **AzureAdJoined-** als auch **DomainJoined** auf **YES** festgelegt sind.
4. Sie können die **DeviceId** verwenden und den Status des Diensts entweder ium Azure-Portal oder mithilfe der PowerShell vergleichen.

### <a name="using-the-azure-portal"></a>Verwenden des Azure-Portals

1. Wechseln Sie zur Geräteseite über einen [direkten Link](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices).
2. Informationen, wie Sie ein Gerät lokalisieren können, finden Sie unter [Verwalten von Geräteidentitäten mit dem Azure-Portal](./device-management-azure-portal.md#manage-devices).
3. Wenn in der Spalte **Registriert** der Wert **Ausstehend** angezeigt wird, wurde Azure AD Hybrid Join nicht abgeschlossen. In Verbundumgebungen kann es hierzu nur kommen, wenn die Registrierung fehlgeschlagen ist und AAD Connect für die Synchronisierung der Geräte konfiguriert ist.
4. Wenn die Spalte **Registriert** einen **Datum/Uhrzeit**-Wert enthält, wurde Azure AD Hybrid Join abgeschlossen.

### <a name="using-powershell"></a>PowerShell

Überprüfen Sie den Geräteregistrierungsstatus in Ihrem Azure-Mandanten mithilfe von **[Get-MsolDevice](/powershell/module/msonline/get-msoldevice)** . Dieses Cmdlet befindet sich im [Azure Active Directory PowerShell-Modul](/powershell/azure/active-directory/install-msonlinev1).

Bei Verwendung des Cmdlets **Get-MSolDevice** zur Überprüfung der Dienstdetails:

- Ein Objekt mit der **Geräte-ID**, die der ID auf dem Windows-Client entspricht, muss vorhanden sein.
- Der Wert für **DeviceTrustType** ist **Domänenbeitritt**. Diese Einstellung entspricht dem Status **Hybrid in Azure AD eingebunden** auf der Seite **Geräte** im Azure AD-Portal.
- Für Geräte, die für den bedingten Zugriff verwendet werden, hat **Aktiviert** den Wert **True** und **DeviceTrustLevel** den Wert **Verwaltet**.

1. Öffnen Sie Windows PowerShell als Administrator.
2. Geben Sie `Connect-MsolService` ein, um die Verbindung mit Ihrem Azure-Mandanten herzustellen.

#### <a name="count-all-hybrid-azure-ad-joined-devices-excluding-pending-state"></a>Zählen Sie alle in Azure AD Hybrid eingebundenen Geräte (ausgenommen der Zustand **Ausstehend**).

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="count-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Zählen Sie alle in Azure AD Hybrid eingebundenen Geräte mit dem Zustand **Ausstehend**.

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="list-all-hybrid-azure-ad-joined-devices"></a>Listen Sie alle in Hybrid Azure AD eingebundenen Geräte auf.

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Listen Sie alle in Azure AD Hybrid eingebundenen Geräte mit dem Zustand **Ausstehend** auf.

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-details-of-a-single-device"></a>Details einen einzelnen Geräts auflisten:

1. Geben Sie `get-msoldevice -deviceId <deviceId>` ein (Dies ist die lokal auf dem Gerät abgerufene **DeviceId**).
2. Vergewissern Sie sich, dass **Aktiviert** auf **True** festgelegt ist.

## <a name="troubleshoot-your-implementation"></a>Problembehandlung bei der Implementierung

Sollten bei der Azure AD-Hybrideinbindung für in Domänen eingebundene Windows-Geräte Probleme auftreten, finden Sie weitere Informationen unter:

- [Problembehandlung von Geräten mit dem Befehl „dsregcmd“](./troubleshoot-device-dsregcmd.md)
- [Beheben von Problemen mit Geräten mit Hybrid-Azure Active Directory-Einbindung](troubleshoot-hybrid-join-windows-current.md)
- [Beheben von Problemen mit Geräten mit Hybrideinbindung in Azure Active Directory](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Nächste Schritte

* [Einführung in die Geräteverwaltung in Azure Active Directory](overview.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-manual/12.png
