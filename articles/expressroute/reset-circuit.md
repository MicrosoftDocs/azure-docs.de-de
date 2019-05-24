---
title: 'Zurücksetzen einer fehlerhaften Verbindung: ExpressRoute: PowerShell: Azure | Microsoft-Dokumentation'
description: In diesem Artikel wird beschrieben, wie Sie eine ExpressRoute-Verbindung zurücksetzen, die sich in einem fehlerhaften Zustand befindet.
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: article
ms.date: 11/28/2018
ms.author: anzaman
ms.custom: seodec18
ms.openlocfilehash: f6fd8e5f0e5730e55fa5bbe13f74be796dd6a4cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66115836"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Zurücksetzen einer fehlerhaften ExpressRoute-Verbindung

Wenn ein Vorgang über eine ExpressRoute-Verbindung nicht erfolgreich abgeschlossen wird, gelangt die Verbindung möglicherweise in einen Fehlerstatus. In diesem Artikel erfahren Sie, wie Sie eine fehlerhafte Azure ExpressRoute-Verbindung zurücksetzen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reset-a-circuit"></a>Zurücksetzen einer Verbindung

1. Installieren Sie die aktuelle Version der PowerShell-Cmdlets für Azure Resource Manager. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-az-ps).

2. Öffnen Sie die PowerShell-Konsole mit erhöhten Rechten, und stellen Sie eine Verbindung mit Ihrem Konto her. Verwenden Sie das folgende Beispiel, um eine Verbindung herzustellen:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
3. Überprüfen Sie die Abonnements für das Konto, wenn Sie über mehrere Azure-Abonnements verfügen.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
4. Geben Sie das Abonnement an, das Sie verwenden möchten.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
5. Führen Sie die folgenden Befehle aus, um eine Verbindung zurückzusetzen, die sich in einem Fehlerzustand befindet:

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

Die Verbindung sollte jetzt fehlerfrei sein. Öffnen Sie ein Supportticket beim [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls die Verbindung weiterhin fehlerhaft ist.

## <a name="next-steps"></a>Nächste Schritte

Öffnen Sie ein Supportticket beim [Microsoft-Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , wenn weiterhin Probleme auftreten.
