---
title: Verwalten von Azure CDN mit PowerShell | Microsoft Docs
description: Erfahren Sie, wie Sie Azure PowerShell-Cmdlets zum Verwalten von Azure CDN verwenden.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: fb6f57a5-6e26-4847-8fd9-b51fb05a79eb
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: magattus
ms.openlocfilehash: 0ad3d1693e2dbf1c4f5329ec23265ea1b3469e2e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66125452"
---
# <a name="manage-azure-cdn-with-powershell"></a>Verwalten von Azure CDN mit PowerShell
PowerShell ermöglicht eine äußerst flexible Methode zur Verwaltung Ihrer Azure CDN-Profile und -Endpunkte.  Sie können PowerShell interaktiv oder durch Schreiben von Skripts verwenden, um Verwaltungsaufgaben zu automatisieren.  In diesem Tutorial werden einige allgemeine Aufgaben vorgestellt, die Sie mit PowerShell zum Verwalten Ihrer Azure CDN-Profile und -Endpunkte ausführen können.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Wenn Sie PowerShell zur Verwaltung Ihrer Azure CDN-Profile und -Endpunkte verwenden möchten, muss das Azure PowerShell-Modul installiert sein.  Informationen zum Installieren von Azure PowerShell und zum Herstellen einer Verbindung mit Azure mithilfe des `Connect-AzAccount` -Cmdlets finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> Melden Sie sich mit `Connect-AzAccount` an, um Azure PowerShell-Cmdlets ausführen zu können.
> 
> 

## <a name="listing-the-azure-cdn-cmdlets"></a>Auflisten der Azure CDN-Cmdlets
Mithilfe des `Get-Command` -Cmdlets können Sie alle Azure CDN-Cmdlets auflisten.

```text
PS C:\> Get-Command -Module Az.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-AzCdnCustomDomain                         2.0.0      Az.Cdn
Cmdlet          Get-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          Get-AzCdnEndpointNameAvailability             2.0.0      Az.Cdn
Cmdlet          Get-AzCdnOrigin                               2.0.0      Az.Cdn
Cmdlet          Get-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Get-AzCdnProfileSsoUrl                        2.0.0      Az.Cdn
Cmdlet          New-AzCdnCustomDomain                         2.0.0      Az.Cdn
Cmdlet          New-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          New-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Publish-AzCdnEndpointContent                  2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnCustomDomain                      2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnEndpoint                          2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnProfile                           2.0.0      Az.Cdn
Cmdlet          Set-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          Set-AzCdnOrigin                               2.0.0      Az.Cdn
Cmdlet          Set-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Start-AzCdnEndpoint                           2.0.0      Az.Cdn
Cmdlet          Stop-AzCdnEndpoint                            2.0.0      Az.Cdn
Cmdlet          Test-AzCdnCustomDomain                        2.0.0      Az.Cdn
Cmdlet          Unpublish-AzCdnEndpointContent                2.0.0      Az.Cdn
```

## <a name="getting-help"></a>Hilfe
Mit dem `Get-Help` -Cmdlet können Sie Hilfe zu allen diesen Cmdlets aufrufen.  `Get-Help` zeigen Sie Nutzungsinformationen und die Syntax und optional auch Beispiele an.

```text
PS C:\> Get-Help Get-AzCdnProfile

NAME
    Get-AzCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzCdnProfile -examples".
    For more information, type: "get-help Get-AzCdnProfile -detailed".
    For technical information, type: "get-help Get-AzCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>Auflisten vorhandener Azure CDN-Profile
Das `Get-AzCdnProfile` -Cmdlet ohne Parameter ruft alle vorhandenen CDN-Profile ab.

```powershell
Get-AzCdnProfile
```

Diese Ausgabe kann zur Enumeration an Cmdlets übergeben werden.

```powershell
# Output the name of all profiles on this subscription.
Get-AzCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzCdnProfile | Where-Object { $_.Sku.Name -eq "Standard_Verizon" }
```

Sie können auch ein einzelnes Profil zurückgeben, indem Sie den Profilnamen und die Ressourcengruppe angeben.

```powershell
Get-AzCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

> [!TIP]
> Es ist möglich, mehrere CDN-Profile mit demselben Namen zu verwenden, solange sich die Profile in verschiedenen Ressourcengruppen befinden.  Durch Auslassen des Parameters `ResourceGroupName` werden alle Profile mit dem gleichen Namen zurückgegeben.
> 
> 

## <a name="listing-existing-cdn-endpoints"></a>Auflisten vorhandener CDN-Endpunkte
`Get-AzCdnEndpoint` kann einen einzelnen Endpunkt oder alle Endpunkte in einem Profil abrufen.  

```powershell
# Get a single endpoint.
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzCdnProfile | Get-AzCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzCdnProfile | Get-AzCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>Erstellen von CDN-Profilen und -Endpunkten
`New-AzCdnProfile` und `New-AzCdnEndpoint` werden zur Erstellung von CDN-Profilen und -Endpunkten verwendet. Die folgenden SKUs werden unterstützt:
- Standard_Verizon
- Premium_Verizon
- Custom_Verizon
- Standard_Akamai
- Standard_Microsoft
- Standard_ChinaCdn

```powershell
# Create a new profile
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US"

# Create a new endpoint
New-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US" | New-AzCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>Überprüfen der Verfügbarkeit von Endpunktnamen
`Get-AzCdnEndpointNameAvailability` gibt ein Objekt zurück, das angibt, ob ein Endpunktname verfügbar ist.

```powershell
# Retrieve availability
$availability = Get-AzCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>Hinzufügen einer benutzerdefinierten Domäne
`New-AzCdnCustomDomain` fügt einem vorhandenen Endpunkt den Namen einer benutzerdefinierten Domäne hinzu.

> [!IMPORTANT]
> CNAME muss mit dem DNS-Anbieter eingerichtet werden. Die Vorgehensweise ist unter [Zuordnen einer benutzerdefinierten Domäne zu einem CDN-Endpunkt (Content Delivery Network, Netzwerk für die Inhaltsübermittlung)](cdn-map-content-to-custom-domain.md) beschrieben.  Sie können die Zuordnung mithilfe von `Test-AzCdnCustomDomain` testen, bevor Sie den Endpunkt ändern.
> 
> 

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>Ändern eines Endpunkts
`Set-AzCdnEndpoint` ändert einen vorhandenen Endpunkt.

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>Löschen/Vorabladen von CDN-Assets
`Unpublish-AzCdnEndpointContent` löscht zwischengespeicherte Assets, und `Publish-AzCdnEndpointContent` lädt Assets vorab auf unterstützten Endpunkten.

```powershell
# Purge some assets.
Unpublish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzCdnProfile | Get-AzCdnEndpoint | Unpublish-AzCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>Starten/Beenden von CDN-Endpunkten
`Start-AzCdnEndpoint` und `Stop-AzCdnEndpoint` können zum Starten und Beenden von einzelnen Endpunkten oder Gruppen mit Endpunkten verwendet werden.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Stop-AzCdnEndpoint

# Start all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Start-AzCdnEndpoint
```

## <a name="deleting-cdn-resources"></a>Löschen von CDN-Ressourcen
`Remove-AzCdnProfile` und `Remove-AzCdnEndpoint` können zum Entfernen von Profilen und Endpunkten verwendet werden.

```powershell
# Remove a single endpoint
Remove-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzCdnEndpoint | Remove-AzCdnEndpoint -Force

# Remove a single profile
Remove-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie, wie Sie Azure CDN mit [.NET](cdn-app-dev-net.md) oder [Node.js](cdn-app-dev-node.md) automatisieren.

Informationen zu CDN-Features finden Sie unter [Übersicht über das Azure Content Delivery Network (CDN)](cdn-overview.md).

