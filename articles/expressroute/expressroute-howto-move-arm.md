---
title: 'Umstellen von Verbindungen vom klassischen Modell auf das Resource Manager-Modell – ExpressRoute: PowerShell: Azure | Microsoft-Dokumentation'
description: Auf dieser Seite wird beschrieben, wie Sie mithilfe von PowerShell eine klassische Verbindung in das Resource Manager-Bereitstellungsmodell verschieben.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 11a84d4ced3232102d262352b84abe1f813e2406
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60365178"
---
# <a name="move-expressroute-circuits-from-classic-to-resource-manager-deployment-model-using-powershell"></a>Umstellen von ExpressRoute-Verbindungen vom klassischen auf das Resource Manager-Bereitstellungsmodell mithilfe von PowerShell

Damit Sie eine ExpressRoute-Verbindung sowohl für das klassische Bereitstellungsmodell als auch für das Resource Manager-Bereitstellungsmodell verwenden können, müssen Sie die Verbindung in das Resource Manager-Bereitstellungsmodell verschieben. In den folgenden Abschnitten wird erläutert, wie Sie Ihre Verbindung mithilfe von PowerShell umstellen können.

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Stellen Sie sicher, dass Sie das klassische und das Az Azure PowerShell-Modul lokal auf Ihrem Computer installiert haben. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview).
* Stellen Sie sicher, dass Sie vor Beginn der Konfiguration die Seiten [Voraussetzungen](expressroute-prerequisites.md), [Routinganforderungen](expressroute-routing.md) und [Workflows](expressroute-workflows.md) gelesen haben.
* Lesen Sie die unter [Verschieben von ExpressRoute-Verbindungen vom klassischen zum Resource Manager-Bereitstellungsmodell](expressroute-move.md) bereitgestellten Informationen. Stellen Sie sicher, dass Sie die Grenzwerte und Einschränkungen verstehen.
* Vergewissern Sie sich, dass die Verbindung im klassischen Bereitstellungsmodell voll funktionsfähig ist.
* Stellen Sie sicher, dass Sie über eine Ressourcengruppe verfügen, die im Resource Manager-Bereitstellungsmodell erstellt wurde.

## <a name="move-an-expressroute-circuit"></a>Verschieben einer ExpressRoute-Verbindung

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Schritt 1: Abrufen von Verbindungsdetails aus dem klassischen Bereitstellungsmodell

Melden Sie sich bei der klassischen Azure-Umgebung an, und rufen Sie den Dienstschlüssel ab.

1. Melden Sie sich beim Azure-Konto an.

   ```powershell
   Add-AzureAccount
   ```

2. Wählen Sie das entsprechende Azure-Abonnement.

   ```powershell
   Select-AzureSubscription "<Enter Subscription Name here>"
   ```

3. Importieren Sie die PowerShell-Module für Azure und ExpressRoute.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
   ```

4. Verwenden Sie das Cmdlet weiter unten, um die Dienstschlüssel für alle ExpressRoute-Verbindungen abzurufen. Kopieren Sie nach dem Abrufen der Schlüssel den **Dienstschlüssel** der Verbindung, die Sie in das Resource Manager-Bereitstellungsmodell verschieben möchten.

   ```powershell
   Get-AzureDedicatedCircuit
   ```

### <a name="step-2-sign-in-and-create-a-resource-group"></a>Schritt 2: Anmelden und Erstellen einer Ressourcengruppe

Melden Sie sich bei der Resource Manager-Umgebung an, und erstellen Sie eine neue Ressourcengruppe.

1. Melden Sie sich bei Ihrer Azure Resource Manager-Umgebung an.

   ```powershell
   Connect-AzAccount
   ```

2. Wählen Sie das entsprechende Azure-Abonnement.

   ```powershell
   Get-AzSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzSubscription
   ```

3. Ändern Sie den Codeausschnitt unten, um eine neue Ressourcengruppe zu erstellen, falls Sie noch keine besitzen.

   ```powershell
   New-AzResourceGroup -Name "DemoRG" -Location "West US"
   ```

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Schritt 3: Verschieben der ExpressRoute-Verbindung in das Resource Manager-Bereitstellungsmodell

Sie können Ihre ExpressRoute-Verbindung jetzt vom klassischen Bereitstellungsmodell in das Resource Manager-Bereitstellungsmodell verschieben. Bevor Sie fortfahren, lesen Sie die Informationen im Artikel [Verschieben von ExpressRoute-Verbindungen vom klassischen zum Resource Manager-Bereitstellungsmodell](expressroute-move.md).

Ändern Sie zum Verschieben den folgenden Codeausschnitt, und führen Sie ihn aus:

```powershell
Move-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"
```

Im klassischen Modus basiert eine ExpressRoute-Verbindung nicht auf einer Verknüpfung mit einer Region. In Resource Manager muss jedoch jede Ressource einer Azure-Region zugeordnet sein. Bei der im Cmdlet „Move-AzExpressRouteCircuit“ angegebenen Region kann es sich aus technischer Sicht um eine beliebige Region handeln. Für organisatorische Zwecke empfiehlt es sich, eine Region auszuwählen, die den Peeringstandort genau darstellt.

> [!NOTE]
> Nachdem der Verschiebevorgang abgeschlossen wurde, wird der neue Name (der im vorherigen Cmdlet aufgeführt wurde), zum Verweis auf die Ressource verwendet. Die Verbindung wird im Grunde umbenannt.
> 

## <a name="modify-circuit-access"></a>Ändern des Verbindungszugriffs

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>So aktivieren Sie eine ExpressRoute-Verbindung für beide Bereitstellungsmodelle

Nachdem Sie die klassische ExpressRoute-Verbindung in das Resource Manager-Bereitstellungsmodell verschoben haben, können Sie den Zugriff auf beide Bereitstellungsmodelle aktivieren. Führen Sie die folgenden Cmdlets aus, um den Zugriff auf beide Bereitstellungsmodelle zu aktivieren:

1. Rufen Sie die Verbindungsdetails ab.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Legen Sie „Klassische Vorgänge zulassen“ auf TRUE fest.

   ```powershell
   $ckt.AllowClassicOperations = $true
   ```

3. Aktualisieren Sie die Verbindung. Nachdem dieser Vorgang erfolgreich abgeschlossen wurde, können Sie die Verbindung im klassischen Bereitstellungsmodell anzeigen.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

4. Führen Sie das folgende Cmdlet aus, um die Details zur ExpressRoute-Verbindung abzurufen. Sie müssen in der Lage sein, den aufgeführten Dienstschlüssel anzuzeigen.

   ```powershell
   get-azurededicatedcircuit
   ```

5. Sie können jetzt Verknüpfungen mit der ExpressRoute-Verbindung mithilfe der Befehle des klassischen Bereitstellungsmodells für klassische VNets und mithilfe der Resource Manager-Befehle für Resource Manager-VNETs verwalten. In den folgenden Artikeln wird beschrieben, wie Sie Verknüpfungen mit der ExpressRoute-Verbindung verwalten können:

    * [Verknüpfen von virtuellen Netzwerken mit ExpressRoute-Verbindungen im Resource Manager-Bereitstellungsmodell](expressroute-howto-linkvnet-arm.md)
    * [Verknüpfen von virtuellen Netzwerken mit ExpressRoute-Verbindungen im klassischen Bereitstellungsmodell](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>So deaktivieren Sie die ExpressRoute-Verbindung für das klassische Bereitstellungsmodell

Führen Sie die folgenden Cmdlets aus, um den Zugriff auf das klassische Bereitstellungsmodell zu deaktivieren:

1. Rufen Sie die Details der ExpressRoute-Verbindung ab.

   ```powershell
   $ckt = Get-AzExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"
   ```

2. Legen Sie „Klassische Vorgänge zulassen“ auf FALSE fest.

   ```powershell
   $ckt.AllowClassicOperations = $false
   ```

3. Aktualisieren Sie die Verbindung. Nachdem dieser Vorgang erfolgreich abgeschlossen wurde, können Sie die Verbindung im klassischen Bereitstellungsmodell nicht mehr anzeigen.

   ```powershell
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen und Ändern des Routings für Ihre ExpressRoute-Verbindung](expressroute-howto-routing-arm.md)
* [Verknüpfen Ihres virtuelles Netzwerks mit Ihrer ExpressRoute-Verbindung](expressroute-howto-linkvnet-arm.md)
