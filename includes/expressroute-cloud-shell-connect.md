---
title: Includedatei
description: Includedatei
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1aca39a7ff162aa3c42fdb3ca5999c71091ec02e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66119395"
---
 Wenn Sie Azure Cloud Shell verwenden, melden Sie sich nach dem Klicken auf „Ausprobieren“ automatisch bei Ihrem Azure-Konto an. Öffnen Sie zum lokalen Anmelden Ihre PowerShell-Konsole mit erhöhten Rechten, und führen Sie das Cmdlet aus, um eine Verbindung herzustellen.

```azurepowershell
Connect-AzAccount
```

Falls Sie über mehrere Abonnements verfügen, rufen Sie eine Liste Ihrer Azure-Abonnements ab.

```azurepowershell-interactive
Get-AzSubscription
```

Geben Sie das Abonnement an, das Sie verwenden möchten.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```