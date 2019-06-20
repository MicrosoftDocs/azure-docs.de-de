---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1aca39a7ff162aa3c42fdb3ca5999c71091ec02e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177824"
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