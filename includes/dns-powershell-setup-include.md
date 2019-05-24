---
title: Includedatei für PowerShell für Azure DNS
description: Includedatei für PowerShell für Azure DNS
services: dns
author: subsarma
ms.service: dns
ms.topic: include file for PowerShell for Azure DNS
ms.date: 03/21/2018
ms.author: subsarma
ms.custom: include file for PowerShell for Azure DNS
ms.openlocfilehash: 682b3d3fee4ff1cdbed6e05a51fd8828b6eb155f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66117270"
---
## <a name="set-up-azure-powershell-for-azure-dns"></a>Einrichten des Azure PowerShell für Azure DNS

### <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [requires-azurerm](requires-azurerm.md)]

Vergewissern Sie sich vor Beginn der Konfiguration, dass Sie über Folgendes verfügen:

* Ein Azure-Abonnement. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) registrieren.
* Sie müssen die aktuelle Version der PowerShell-Cmdlets für Azure Resource Manager installieren. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azureps-cmdlets-docs).

Zur Verwendung von Private Zones (Public Preview) benötigen Sie zudem folgende PowerShell-Module und -Versionen: 
* AzureRM.Dns: mindestens [Version 4.1.0](https://www.powershellgallery.com/packages/AzureRM.Dns/4.1.0)
* AzureRM.Network: mindestens [Version 5.4.0](https://www.powershellgallery.com/packages/AzureRM.Network/5.4.0)

```powershell 
Find-Module -Name AzureRM.Dns 
``` 
 
```powershell 
Find-Module -Name AzureRM.Network 
``` 
 
In der Ausgabe der obigen Befehle muss die AzureRM.Dns-Version mindestens 4.1.0 und die AzureRM.Network-Version mindestens 5.4.0 sein.  

Sollten in Ihrem System ältere Versionen vorhanden sein, können Sie entweder die neueste Version von Azure PowerShell installieren oder die obigen Module aus dem PowerShell-Katalog herunterladen und installieren. Verwenden Sie dazu die obigen Links neben den Modulversionen. Anschließend können Sie sie mithilfe der weiter unten bereitgestellten Befehle installieren. Beide Module sind erforderlich und vollständig abwärtskompatibel. 

```powershell
Install-Module -Name AzureRM.Dns -Force
```

```powershell
Install-Module -Name AzureRM.Network -Force
```

### <a name="sign-in-to-your-azure-account"></a>Anmelden bei Ihrem Azure-Konto

Öffnen Sie die PowerShell-Konsole, und stellen Sie eine Verbindung mit Ihrem Konto her. Weitere Informationen finden Sie unter [Anmelden](/powershell/azure/install-az-ps?view=azps-1.4.0#sign-in).

```powershell
Connect-AzureRmAccount
```

### <a name="select-the-subscription"></a>Auswählen des Abonnements
 
Überprüfen Sie die Abonnements für das Konto.

```powershell
Get-AzureRmSubscription
```

Wählen Sie aus, welches Azure-Abonnement Sie verwenden möchten.

```powershell
Select-AzureRmSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Azure Resource Manager erfordert, dass alle Ressourcengruppen einen Speicherort angeben. Dieser wird als Standardspeicherort für Ressourcen in dieser Ressourcengruppe verwendet. Da alle DNS-Ressourcen global und nicht regional sind, hat die Auswahl des Speicherorts für die Ressourcengruppe jedoch keine Auswirkungen auf Azure DNS.

Dieser Schritt kann übersprungen werden, wenn Sie eine vorhandene Ressourcengruppe verwenden.

```powershell
New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"
```

### <a name="register-resource-provider"></a>Registrieren des Ressourcenanbieters

Der Azure DNS-Dienst wird vom Ressourcenanbieter "Microsoft.Network" verwaltet. Ihr Azure-Abonnement muss für die Verwendung dieses Ressourcenanbieters registriert werden, bevor Sie Azure DNS verwenden können. Dieser Schritt muss einmal für jedes Abonnement ausgeführt werden.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```
