---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: c6666f4417cde9e0f77cc965ded1d6bdb5dced34
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178049"
---
## <a name="start-your-powershell-session"></a>Starten der PowerShell-Sitzung
 

Führen Sie das Cmdlet [**Connect-AzAccount**](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount) aus. Daraufhin wird eine Anmeldeseite angezeigt, auf der Sie Ihre Anmeldeinformationen eingeben müssen. Verwenden Sie die gleichen Anmeldeinformationen wie für die Anmeldung beim Azure-Portal.

```powershell
Connect-AzAccount
```

Wenn Sie über mehrere Abonnements verfügen, wählen Sie mit dem Cmdlet [**Set-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) das Abonnement aus, das für die PowerShell-Sitzung verwendet werden soll. Führen Sie das Cmdlet [**Get-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext) aus, um das Abonnement anzuzeigen, das für die aktuelle PowerShell-Sitzung verwendet wird. Um alle Ihre Abonnements anzuzeigen, führen Sie [**Get-AzSubscription**](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription) aus.

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```

