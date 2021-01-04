---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: 27da28073b01c6bc43868172d6c989d9518d8f53
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95556504"
---
## <a name="start-your-powershell-session"></a>Starten der PowerShell-Sitzung
 

Führen Sie das Cmdlet [**Connect-AzAccount**](/powershell/module/Az.Accounts/Connect-AzAccount) aus. Daraufhin wird eine Anmeldeseite angezeigt, auf der Sie Ihre Anmeldeinformationen eingeben müssen. Verwenden Sie die gleichen Anmeldeinformationen wie für die Anmeldung beim Azure-Portal.

```powershell
Connect-AzAccount
```

Wenn Sie über mehrere Abonnements verfügen, wählen Sie mit dem Cmdlet [**Set-AzContext**](/powershell/module/az.accounts/set-azcontext) das Abonnement aus, das für die PowerShell-Sitzung verwendet werden soll. Führen Sie das Cmdlet [**Get-AzContext**](/powershell/module/az.accounts/get-azcontext) aus, um das Abonnement anzuzeigen, das für die aktuelle PowerShell-Sitzung verwendet wird. Um alle Ihre Abonnements anzuzeigen, führen Sie [**Get-AzSubscription**](/powershell/module/az.accounts/get-azsubscription) aus.

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```