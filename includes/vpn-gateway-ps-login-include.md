---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 853bd32cf3ea97571929d54fb7d3ba04bde16b81
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177960"
---
Bevor Sie mit dieser Konfiguration beginnen, müssen Sie sich bei Ihrem Azure-Konto anmelden. Sie werden vom Cmdlet zur Eingabe der Anmeldeinformationen für Ihr Azure-Konto aufgefordert. Nach der Anmeldung werden Ihre Kontoeinstellungen heruntergeladen, damit sie Azure PowerShell zur Verfügung stehen. Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell mit Resource Manager](../articles/powershell-azure-resource-manager.md).

Öffnen Sie zum Anmelden Ihre PowerShell-Konsole mit erhöhten Rechten, und stellen Sie eine Verbindung mit Ihrem Konto her. Verwenden Sie das folgende Beispiel, um eine Verbindung herzustellen:

```powershell
Connect-AzAccount
```

Überprüfen Sie die Abonnements für das Konto, wenn Sie über mehrere Azure-Abonnements verfügen.

```powershell
Get-AzSubscription
```

Geben Sie das Abonnement an, das Sie verwenden möchten.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```
