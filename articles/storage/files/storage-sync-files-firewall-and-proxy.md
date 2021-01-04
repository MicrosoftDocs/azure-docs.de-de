---
title: Lokale Firewall- und -Proxyeinstellungen der Azure-Dateisynchronisierung | Microsoft-Dokumentation
description: Hier finden Sie grundlegende Informationen zu lokalen Proxy- und Firewalleinstellungen der Azure-Dateisynchronisierung. Machen Sie sich mit den Konfigurationsdetails für Ports, Netzwerke und spezielle Verbindungen mit Azure vertraut.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/30/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cffa6b1200b7236b3c0a3e48b50c58275cf4c57b
ms.sourcegitcommit: 5ae2f32951474ae9e46c0d46f104eda95f7c5a06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95316619"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>Proxy- und Firewalleinstellungen der Azure-Dateisynchronisierung
Die Azure-Dateisynchronisierung verbindet Ihre lokalen Server mit Azure Files, wodurch Synchronisierung für mehrere Standorte und Cloudtiering-Funktionalität ermöglicht werden. Daher muss ein lokaler Server eine Verbindung mit dem Internet haben. Ein IT-Administrator muss den besten Weg festlegen, auf dem der Server zu den Azure-Clouddiensten gelangt.

Dieser Artikel gibt einen Einblick in die speziellen Anforderungen und Optionen, die verfügbar sind, um Ihren Server erfolgreich und sicher mit der Azure-Dateisynchronisierung zu verbinden.

Wir empfehlen Ihnen, vor dem Lesen dieses Leitfadens den Artikel [Azure-Dateisynchronisierung – Überlegungen zum Netzwerkbetrieb](storage-sync-files-networking-overview.md) zu lesen.

## <a name="overview"></a>Übersicht
Die Azure-Dateisynchronisierung fungiert als Orchestrierungsdienst zwischen Ihrem Windows-Server, Ihrer Azure-Dateifreigabe und mehreren anderen Azure-Diensten, um Daten so zu synchronisieren, wie dies in Ihrer Synchronisierungsgruppe beschrieben ist. Damit die Azure-Dateisynchronisierung korrekt funktioniert, müssen Sie Ihre Server so konfigurieren, dass sie mit den folgenden Azure-Diensten kommunizieren:

- Azure Storage
- Azure-Dateisynchronisierung
- Azure Resource Manager
- Authentifizierungsdienste

> [!Note]  
> Der Azure-Dateisynchronisierungs-Agent unter Windows Server initiiert alle Anforderungen an die Clouddienste, was dazu führt, dass aus Sicht einer Firewall nur ausgehender Datenverkehr berücksichtigt werden muss. <br /> Keiner der Azure-Dienste initiiert eine Verbindung mit dem Azure-Dateisynchronisierungs-Agent.

## <a name="ports"></a>Ports
Die Azure-Dateisynchronisierung bewegt Dateidaten und Metadaten ausschließlich über HTTPS und erfordert, dass Port 443 für ausgehenden Datenverkehr geöffnet ist.
Daher wird der gesamte Datenverkehr verschlüsselt.

## <a name="networks-and-special-connections-to-azure"></a>Netzwerke und spezielle Verbindungen mit Azure
Der Azure-Dateisynchronisierungs-Agent hat keine Anforderungen hinsichtlich spezieller Kanäle wie [ExpressRoute](../../expressroute/expressroute-introduction.md) usw. zu Azure.

Die Azure-Dateisynchronisierung nutzt alle verfügbaren Mittel, die Zugriff in Azure ermöglichen, passt sich automatisch an verschiedene Netzwerkeigenschaften wie Bandbreite, Wartezeit an und bietet Administratorsteuerung zur Feinabstimmung. Derzeit sind nicht alle Funktionen verfügbar. Wenn Sie ein bestimmtes Verhalten konfigurieren möchten, können Sie uns dies über [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670) mitteilen.

## <a name="proxy"></a>Proxy
Die Azure-Dateisynchronisierung unterstützt App-spezifische und computerweite Proxyeinstellungen.

**App-spezifische Proxyeinstellungen** ermöglichen es, einen Proxy speziell für den Datenverkehr der Azure-Dateisynchronisierung zu konfigurieren. App-spezifische Proxyeinstellungen werden ab Version 4.0.1.0 des Agents unterstützt und können während der Installation des Agents oder mit dem PowerShell-Cmdlet Set-StorageSyncProxyConfiguration konfiguriert werden.

PowerShell-Befehle zum Konfigurieren von App-spezifischen Proxyeinstellungen:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
Die **computerweiten Proxyeinstellungen** sind für den Azure-Dateisynchronisierungs-Agent transparent, da der gesamte Datenverkehr des Servers über den Proxy geleitet wird.

Um computerweite Proxyeinstellungen zu konfigurieren, führen Sie in die folgenden Schritte aus: 

1. Konfigurieren von Proxyeinstellungen für .NET-Anwendungen 

   - Bearbeiten Sie diese beiden Dateien:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Fügen Sie den Abschnitt <system.net> in den machine.config-Dateien (unter dem Abschnitt <system.serviceModel>) hinzu.  Ändern Sie 127.0.01:8888 in die IP-Adresse und den Port des Proxyservers. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. Festlegen der WinHTTP-Proxyeinstellungen 

   - Führen Sie den folgenden Befehl in einer Eingabeaufforderung mit erhöhten Rechten oder in PowerShell aus, um die vorhandene Proxyeinstellung anzuzeigen:   

     netsh winhttp show proxy

   - Führen den folgenden Befehl in einer Eingabeaufforderung mit erhöhten Rechten oder in PowerShell aus, um die Proxyeinstellung festzulegen (ändern Sie 127.0.01:8888 in die IP-Adresse und den Port für den Proxyserver):  

     netsh winhttp set proxy 127.0.0.1:8888

3. Starten Sie den Storage-Synchronisierungs-Agent-Dienst neu, indem Sie den folgenden Befehl in einer Eingabeaufforderung mit erhöhten Rechten oder in PowerShell ausführen: 

      net stop filesyncsvc

      Hinweis: Der Storage-Synchronisierungs-Agent-Dienst (filesyncsvc) wird automatisch gestartet, nachdem er beendet wurde.

## <a name="firewall"></a>Firewall
Wie in einem vorhergehenden Abschnitt erwähnt, muss Port 443 für ausgehenden Datenverkehr geöffnet sein. Entsprechend den Richtlinien in Ihrem Rechenzentrum, Ihrer Niederlassung oder Ihrer Region kann eine weitere Beschränkung des Datenverkehrs über diesen Port auf bestimmte Domänen erwünscht oder erforderlich sein.

In der folgenden Tabelle sind die für eine Kommunikation erforderlichen Domänen beschrieben:

| Dienst | Öffentlicher Cloudendpunkt | Azure Government-Endpunkt | Verwendung |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | `https://management.azure.com` | https://management.usgovcloudapi.net | Jeder Benutzeraufruf (etwa PowerShell) geht an/über diese URL, dazu gehört auch der erstmalige Serverregistrierungsaufruf. |
| **Azure Active Directory** | https://login.windows.net<br>`https://login.microsoftonline.com` | https://login.microsoftonline.us | Azure Resource Manager-Aufrufe müssen von einem authentifizierten Benutzer vorgenommen werden. Um erfolgreich zu sein, wird diese URL für die Benutzerauthentifizierung verwendet. |
| **Azure Active Directory** | https://graph.microsoft.com/ | https://graph.microsoft.com/ | Im Rahmen der Bereitstellung der Azure-Dateisynchronisierung wird ein Dienstprinzipal in Azure Active Directory des Abonnements erstellt. Diese URL wird dazu verwendet. Dieser Prinzipal wird dazu verwendet, einen minimalen Satz von Berechtigungen an den Azure-Dateisynchronisierungsdienst zu delegieren. Der Benutzer, der die erste Einrichtung der Azure-Dateisynchronisierung ausführt, muss ein authentifizierter Benutzer mit Abonnementbesitzerberechtigungen sein. |
| **Azure Active Directory** | https://secure.aadcdn.microsoftonline-p.com | Verwenden Sie die öffentliche Endpunkt-URL. | Auf diese URL wird von der Active Directory-Authentifizierungsbibliothek zugegriffen, die von der Benutzeroberfläche für die Registrierung beim Azure-Dateisynchronisierungsserver zum Anmelden des Administrators verwendet wird. |
| **Azure Storage (in englischer Sprache)** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | Beim Herunterladen einer Datei auf dem Server wird diese Datenverschiebung effizienter ausgeführt, wenn eine direkte Verbindung zwischen dem Server und der Azure-Dateifreigabe im Speicherkonto besteht. Der Server hat einen SAS-Schlüssel, der nur gezielten Dateifreigabezugriff zulässt. |
| **Azure-Dateisynchronisierung** | &ast;.one.microsoft.com<br>&ast;.afs.azure.net | &ast;.afs.azure.us | Nach der erstmaligen Serverregistrierung erhält der Server eine regionale URL für die Azure-Dateisynchronisierungs-Dienstinstanz in dieser Region. Der Server kann über die URL direkt und effizient mit der Instanz kommunizieren, die seine Synchronisierung verwaltet. |
| **Microsoft PKI** | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | Sobald der Agent für die Azure-Dateisynchronisierung installiert ist, werden über die PKI-URL Zwischenzertifikate heruntergeladen, die für die Kommunikation mit dem Azure-Dateisynchronisierungsdienst und der Azure-Dateifreigabe erforderlich sind. Mithilfe der OCSP-URL wird der Status eines Zertifikats überprüft. |
| **Microsoft Update** | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;.dl.delivery.mp.microsoft.com<br>&ast;.emdl.ws.microsoft.com | &ast;.update.microsoft.com<br>&ast;.download.windowsupdate.com<br>&ast;.dl.delivery.mp.microsoft.com<br>&ast;.emdl.ws.microsoft.com | Nachdem der Azure-Dateisynchronisierungs-Agent installiert wurde, werden Updates für diesen über die Microsoft Update-URLs heruntergeladen. |

> [!Important]
> Wenn Datenverkehr über „&ast;.afs.azure.net“ zugelassen wird, ist er nur für den Synchronisierungsdienst möglich. Es gibt keine anderen Microsoft-Dienste, die diese Domäne verwenden.
> Wenn Datenverkehr über „&ast;.one.microsoft.com“ zugelassen wird, kann Datenverkehr vom Server nicht nur an den Synchronisierungsdienst, sondern auch an weitere Stellen gesendet werden. Es gibt viele weitere Microsoft-Dienste unter Unterdomänen.

Wenn „&ast;.afs.azure.net“ oder „&ast;.one.microsoft.com“ zu ausgedehnt ist, können Sie die Kommunikation des Servers begrenzen, indem Sie eine Kommunikation nur für explizite regionale Instanzen des Azure Files Sync-Diensts zulassen. Welche Instanz(en) ausgewählt werden muss/müssen, hängt von der Region des Speichersynchronisierungsdienst ab, für den Sie den Server bereitgestellt und registriert haben. Diese Region heißt in der folgenden Tabelle „Primäre Endpunkt-URL“.

Für Business Continuity und Disaster Recovery (BCDR) haben Sie Ihre Azure-Dateifreigaben möglicherweise in einem georedundanten (GRS) Speicherkonto angegeben. Wenn das der Fall ist, werden Ihre Azure-Dateifreigaben im Falle eines dauerhaften regionalen Ausfalls auf die gekoppelte Region übertragen. Die Azure-Dateisynchronisierung verwendet die gleichen regionalen Kombinationen als Speicher. Wenn Sie also GRS-Speicherkonten verwenden, müssen Sie zusätzliche URLs aktivieren, damit Ihr Server mit der gekoppelten Region für die Azure-Dateisynchronisierung kommunizieren kann. In der folgenden Tabelle wird dies als „gekoppelte Region“ bezeichnet. Darüber hinaus muss eine Traffic Manager-Profil-URL aktiviert werden. Dadurch wird sichergestellt, dass der Netzwerkverkehr im Falle eines Failovers nahtlos in die gekoppelte Region umgeleitet werden kann. Die URL ist in der folgenden Tabelle als „Ermittlungs-URL“ bezeichnet.

| Cloud  | Region | URL des primären Endpunkts | Regionspaar | Ermittlungs-URL |
|--------|--------|----------------------|---------------|---------------|
| Öffentlich |Australien (Osten) | https:\//australiaeast01.afs.azure.net<br>https:\//kailani-aue.one.microsoft.com | Australien, Südosten | https:\//tm-australiaeast01.afs.azure.net<br>https:\//tm-kailani-aue.one.microsoft.com |
| Öffentlich |Australien, Südosten | https:\//australiasoutheast01.afs.azure.net<br>https:\//kailani-aus.one.microsoft.com | Australien (Osten) | https:\//tm-australiasoutheast01.afs.azure.net<br>https:\//tm-kailani-aus.one.microsoft.com |
| Öffentlich | Brasilien Süd | https:\//brazilsouth01.afs.azure.net | USA Süd Mitte | https:\//tm-brazilsouth01.afs.azure.net |
| Öffentlich | Kanada, Mitte | https:\//canadacentral01.afs.azure.net<br>https:\//kailani-cac.one.microsoft.com | Kanada, Osten | https:\//tm-canadacentral01.afs.azure.net<br>https:\//tm-kailani-cac.one.microsoft.com |
| Öffentlich | Kanada, Osten | https:\//canadaeast01.afs.azure.net<br>https:\//kailani-cae.one.microsoft.com | Kanada, Mitte | https:\//tm-canadaeast01.afs.azure.net<br>https:\//tm-kailani.cae.one.microsoft.com |
| Öffentlich | Indien, Mitte | https:\//centralindia01.afs.azure.net<br>https:\//kailani-cin.one.microsoft.com | Indien (Süden) | https:\//tm-centralindia01.afs.azure.net<br>https:\//tm-kailani-cin.one.microsoft.com |
| Öffentlich | USA (Mitte) | https:\//centralus01.afs.azure.net<br>https:\//kailani-cus.one.microsoft.com | USA (Ost) 2 | https:\//tm-centralus01.afs.azure.net<br>https:\//tm-kailani-cus.one.microsoft.com |
| Öffentlich | Asien, Osten | https:\//eastasia01.afs.azure.net<br>https:\//kailani11.one.microsoft.com | Asien, Südosten | https:\//tm-eastasia01.afs.azure.net<br>https:\//tm-kailani11.one.microsoft.com |
| Öffentlich | East US | https:\//eastus01.afs.azure.net<br>https:\//kailani1.one.microsoft.com | USA (Westen) | https:\//tm-eastus01.afs.azure.net<br>https:\//tm-kailani1.one.microsoft.com |
| Öffentlich | USA (Ost) 2 | https:\//eastus201.afs.azure.net<br>https:\//kailani-ess.one.microsoft.com | USA (Mitte) | https:\//tm-eastus201.afs.azure.net<br>https:\//tm-kailani-ess.one.microsoft.com |
| Öffentlich | Deutschland, Norden | https:\//germanynorth01.afs.azure.net | Deutschland, Westen-Mitte | https:\//tm-germanywestcentral01.afs.azure.net |
| Öffentlich | Deutschland, Westen-Mitte | https:\//germanywestcentral01.afs.azure.net | Deutschland, Norden | https:\//tm-germanynorth01.afs.azure.net |
| Öffentlich | Japan, Osten | https:\//japaneast01.afs.azure.net | Japan, Westen | https:\//tm-japaneast01.afs.azure.net |
| Öffentlich | Japan, Westen | https:\//japanwest01.afs.azure.net | Japan, Osten | https:\//tm-japanwest01.afs.azure.net |
| Öffentlich | Korea, Mitte | https:\//koreacentral01.afs.azure.net/ | Korea, Süden | https:\//tm-koreacentral01.afs.azure.net/ |
| Öffentlich | Korea, Süden | https:\//koreasouth01.afs.azure.net/ | Korea, Mitte | https:\//tm-koreasouth01.afs.azure.net/ |
| Öffentlich | USA Nord Mitte | https:\//northcentralus01.afs.azure.net | USA Süd Mitte | https:\//tm-northcentralus01.afs.azure.net |
| Öffentlich | Nordeuropa | https:\//northeurope01.afs.azure.net<br>https:\//kailani7.one.microsoft.com | Europa, Westen | https:\//tm-northeurope01.afs.azure.net<br>https:\//tm-kailani7.one.microsoft.com |
| Öffentlich | USA Süd Mitte | https:\//southcentralus01.afs.azure.net | USA Nord Mitte | https:\//tm-southcentralus01.afs.azure.net |
| Öffentlich | Indien (Süden) | https:\//southindia01.afs.azure.net<br>https:\//kailani-sin.one.microsoft.com | Indien, Mitte | https:\//tm-southindia01.afs.azure.net<br>https:\//tm-kailani-sin.one.microsoft.com |
| Öffentlich | Asien, Südosten | https:\//southeastasia01.afs.azure.net<br>https:\//kailani10.one.microsoft.com | Asien, Osten | https:\//tm-southeastasia01.afs.azure.net<br>https:\//tm-kailani10.one.microsoft.com |
| Öffentlich | UK, Süden | https:\//uksouth01.afs.azure.net<br>https:\//kailani-uks.one.microsoft.com | UK, Westen | https:\//tm-uksouth01.afs.azure.net<br>https:\//tm-kailani-uks.one.microsoft.com |
| Öffentlich | UK, Westen | https:\//ukwest01.afs.azure.net<br>https:\//kailani-ukw.one.microsoft.com | UK, Süden | https:\//tm-ukwest01.afs.azure.net<br>https:\//tm-kailani-ukw.one.microsoft.com |
| Öffentlich | USA, Westen-Mitte | https:\//westcentralus01.afs.azure.net | USA, Westen 2 | https:\//tm-westcentralus01.afs.azure.net |
| Öffentlich | Europa, Westen | https:\//westeurope01.afs.azure.net<br>https:\//kailani6.one.microsoft.com | Nordeuropa | https:\//tm-westeurope01.afs.azure.net<br>https:\//tm-kailani6.one.microsoft.com |
| Öffentlich | USA (Westen) | https:\//westus01.afs.azure.net<br>https:\//kailani.one.microsoft.com | East US | https:\//tm-westus01.afs.azure.net<br>https:\//tm-kailani.one.microsoft.com |
| Öffentlich | USA, Westen 2 | https:\//westus201.afs.azure.net | USA, Westen-Mitte | https:\//tm-westus201.afs.azure.net |
| Behörden | US Gov Arizona | https:\//usgovarizona01.afs.azure.us | US Gov Texas | https:\//tm-usgovarizona01.afs.azure.us |
| Behörden | US Gov Texas | https:\//usgovtexas01.afs.azure.us | US Gov Arizona | https:\//tm-usgovtexas01.afs.azure.us |

- Wenn Sie lokal redundanten (LRS) oder zonenredundante (ZRS) Speicherkonten verwenden, müssen Sie nur die URL aktivieren, die unter "Primäre Endpunkt-URL" aufgeführt ist.

- Wenn Sie georedundante (GRS) Speicherkonten verwenden, aktivieren Sie die drei URLs.

**Beispiel:** Sie stellen einen Speichersynchronisierungsdienst in `"West US"` bereit und registrieren damit Ihren Server. Die URLs, mit denen der Server in diesem Fall kommunizieren kann, sind:

> - https:\//westus01.afs.azure.net (primärer Endpunkt: USA, Westen)
> - https:\//eastus01.afs.azure.net (gekoppelte Failoverregion: USA, Osten)
> - https:\//tm-westus01.afs.azure.net (Ermittlungs-URL der primären Region)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Zulassungsliste für Azure-Dateisynchronisierungs-IP-Adressen
Die Azure-Dateisynchronisierung unterstützt die Verwendung von [Diensttags](../../virtual-network/service-tags-overview.md), die eine Gruppe von IP-Adresspräfixen für einen bestimmten Azure-Dienst darstellen. Sie können Diensttags verwenden, um Firewallregeln zu erstellen, die die Kommunikation mit dem Azure-Dateisynchronisierungsdienst ermöglichen. Das Diensttag für die Azure-Dateisynchronisierung ist `StorageSyncService`.

Wenn Sie die Azure-Dateisynchronisierung in Azure verwenden, können Sie den Namen des Diensttags direkt in der Netzwerksicherheitsgruppe verwenden, um Datenverkehr zuzulassen. Weitere Informationen zur Vorgehensweise finden Sie unter [Netzwerksicherheitsgruppen](../../virtual-network/network-security-groups-overview.md).

Wenn Sie die Azure-Dateisynchronisierung lokal verwenden, können Sie die Diensttag-API verwenden, um bestimmte IP-Adressbereiche für die Zulassungsliste Ihrer Firewall abzurufen. Es gibt zwei Methoden zum Abrufen dieser Informationen:

- Die aktuelle Liste der IP-Adressbereiche für alle Azure-Dienste, die Diensttags unterstützen, wird wöchentlich im Microsoft Download Center in Form eines JSON-Dokuments veröffentlicht. Es gibt für jede Azure-Cloud ein eigenes JSON-Dokument mit den IP-Adressbereichen, die für diese Cloud relevant sind:
    - [Azure öffentlich](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure China](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Deutschland](https://www.microsoft.com/download/details.aspx?id=57064)
- Die Diensttagermittlungs-API (Vorschauversion) ermöglicht das programmgesteuerte Abrufen der aktuellen Liste von Diensttags. In der Vorschauversion gibt die Diensttagermittlungs-API eventuell weniger aktuelle Informationen zurück, als in den im Microsoft Download Center veröffentlichten JSON-Dokumenten enthalten sind. Sie können je nach Ihren Automatisierungsvorlieben auch die API-Benutzeroberfläche verwenden:
    - [REST-API](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure-Befehlszeilenschnittstelle](/cli/azure/network#az-network-list-service-tags)

Da die Diensttagermittlungs-API nicht so häufig aktualisiert wird wie die im Microsoft Download Center veröffentlichten JSON-Dokumente, sollten Sie für die Aktualisierung der Zulassungsliste Ihrer Firewall das JSON-Dokument verwenden. Gehen Sie dazu folgendermaßen vor:

```PowerShell
# The specific region to get the IP address ranges for. Replace westus2 with the desired region code 
# from Get-AzLocation.
$region = "westus2"

# The service tag for Azure File Sync. Do not change unless you're adapting this
# script for another service.
$serviceTag = "StorageSyncService"

# Download date is the string matching the JSON document on the Download Center. 
$possibleDownloadDates = 0..7 | `
    ForEach-Object { [System.DateTime]::Now.AddDays($_ * -1).ToString("yyyyMMdd") }

# Verify the provided region
$validRegions = Get-AzLocation | `
    Where-Object { $_.Providers -contains "Microsoft.StorageSync" } | `
    Select-Object -ExpandProperty Location

if ($validRegions -notcontains $region) {
    Write-Error `
            -Message "The specified region $region is not available. Either Azure File Sync is not deployed there or the region does not exist." `
            -ErrorAction Stop
}

# Get the Azure cloud. This should automatically based on the context of 
# your Az PowerShell login, however if you manually need to populate, you can find
# the correct values using Get-AzEnvironment.
$azureCloud = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty Name

# Build the download URI
$downloadUris = @()
switch($azureCloud) {
    "AzureCloud" { 
        $downloadUris = $possibleDownloadDates | ForEach-Object {  
            "https://download.microsoft.com/download/7/1/D/71D86715-5596-4529-9B13-DA13A5DE5B63/ServiceTags_Public_$_.json"
        }
    }

    "AzureUSGovernment" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/6/4/D/64DB03BF-895B-4173-A8B1-BA4AD5D4DF22/ServiceTags_AzureGovernment_$_.json"
        }
    }

    "AzureChinaCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/9/D/0/9D03B7E2-4B80-4BF3-9B91-DA8C7D3EE9F9/ServiceTags_China_$_.json"
        }
    }

    "AzureGermanCloud" {
        $downloadUris = $possibleDownloadDates | ForEach-Object { 
            "https://download.microsoft.com/download/0/7/6/076274AB-4B0B-4246-A422-4BAF1E03F974/ServiceTags_AzureGermany_$_.json"
        }
    }

    default {
        Write-Error -Message "Unrecognized Azure Cloud: $_" -ErrorAction Stop
    }
}

# Find most recent file
$found = $false 
foreach($downloadUri in $downloadUris) {
    try { $response = Invoke-WebRequest -Uri $downloadUri -UseBasicParsing } catch { }
    if ($response.StatusCode -eq 200) {
        $found = $true
        break
    }
}

if ($found) {
    # Get the raw JSON 
    $content = [System.Text.Encoding]::UTF8.GetString($response.Content)

    # Parse the JSON
    $serviceTags = ConvertFrom-Json -InputObject $content -Depth 100

    # Get the specific $ipAddressRanges
    $ipAddressRanges = $serviceTags | `
        Select-Object -ExpandProperty values | `
        Where-Object { $_.id -eq "$serviceTag.$region" } | `
        Select-Object -ExpandProperty properties | `
        Select-Object -ExpandProperty addressPrefixes
} else {
    # If the file cannot be found, that means there hasn't been an update in
    # more than a week. Please verify the download URIs are still accurate
    # by checking https://docs.microsoft.com/azure/virtual-network/service-tags-overview
    Write-Verbose -Message "JSON service tag file not found."
    return
}
```

Anschließend können Sie die IP-Adressbereiche in `$ipAddressRanges` verwenden, um Ihre Firewall zu aktualisieren. Auf der Website Ihrer Firewall-/Netzwerkappliance finden Sie Informationen zum Aktualisieren der Firewall.

## <a name="test-network-connectivity-to-service-endpoints"></a>Testen der Netzwerkkonnektivität mit Dienstendpunkten
Nach der Registrierung eines Servers beim Azure-Dateisynchronisierungsdienst können das Cmdlet „Test-StorageSyncNetworkConnectivity“ und die Datei „ServerRegistration.exe“ dazu verwendet werden, die Kommunikation mit allen für diesen Server spezifischen Endpunkten (URLs) zu testen. Dieses Cmdlet kann Sie bei der Problembehandlung unterstützen, wenn unvollständige Kommunikation den Server daran hindert, vollständig mit der Azure-Dateisynchronisierung zu arbeiten. Es kann außerdem zum Optimieren der Proxy- und Firewallkonfigurationen verwendet werden.

Installieren Sie zum Testen der Netzwerkkonnektivität mindestens Version  9.1 des Azure-Dateisynchronisierungs-Agents, und führen Sie die folgenden PowerShell-Befehle aus:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Zusammenfassung und Risikobegrenzung
Die Listen weiter oben in diesem Dokument enthalten die URLs, mit denen die Azure-Dateisynchronisierung derzeit kommuniziert. Firewalls müssen in der Lage sein, ausgehenden Datenverkehr zu diesen Domänen zuzulassen. Microsoft ist bestrebt, diese Liste auf dem neuesten Stand zu halten.

Ein Einrichten von domäneneinschränkenden Firewallregeln kann eine Maßnahme sein, die Sicherheit zu verbessern. Wenn Sie diese Firewallkonfigurationen verwenden, müssen Sie bedenken, dass URLs hinzugefügt und im Verlauf der Zeit vielleicht geändert werden. Lesen Sie diesen Artikel in regelmäßigen Abständen.

## <a name="next-steps"></a>Nächste Schritte
- [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](storage-sync-files-planning.md)
- [Bereitstellen der Azure-Dateisynchronisierung](storage-sync-files-deployment-guide.md)
- [Überwachen der Azure-Dateisynchronisierung](storage-sync-files-monitoring.md)
