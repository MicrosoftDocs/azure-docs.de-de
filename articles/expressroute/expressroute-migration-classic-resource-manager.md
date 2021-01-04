---
title: 'Azure ExpressRoute: Migrieren von klassischen VNETs und Resource Manager'
description: Auf dieser Seite wird beschrieben, wie über ExpressRoute zugeordnete virtuelle Netzwerke zu Resource Manager migriert werden, nachdem die Verbindung verschoben wurde.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/06/2020
ms.author: duau
ms.openlocfilehash: 57c439cf8ac52d93d231d6ff33f72a5a942dec6a
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351604"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Migrieren von über ExpressRoute zugeordneten virtuellen Netzwerken vom klassischen Modell zu Resource Manager

In diesem Artikel wird erläutert, wie über ExpressRoute zugeordnete virtuelle Netzwerke vom klassischen Bereitstellungsmodell zum Azure Resource Manager-Bereitstellungsmodell migriert werden, nachdem die ExpressRoute-Verbindung verschoben wurde. 

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Stellen Sie sicher, dass Sie die neuesten Versionen der Azure PowerShell-Module besitzen. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/). Informationen zur Installation des PowerShell-Dienstverwaltungsmoduls (das für das klassische Bereitstellungsmodell benötigt wird) finden Sie unter [Installieren des Azure PowerShell-Dienstverwaltungsmoduls](/powershell/azure/servicemanagement/install-azure-ps).
* Stellen Sie sicher, dass Sie vor Beginn der Konfiguration die Seiten [Voraussetzungen](expressroute-prerequisites.md), [Routinganforderungen](expressroute-routing.md) und [Workflows](expressroute-workflows.md) gelesen haben.
* Lesen Sie die unter [Verschieben von ExpressRoute-Verbindungen vom klassischen zum Resource Manager-Bereitstellungsmodell](expressroute-move.md) bereitgestellten Informationen. Stellen Sie sicher, dass Sie die Grenzwerte und Einschränkungen verstehen.
* Vergewissern Sie sich, dass die Verbindung im klassischen Bereitstellungsmodell voll funktionsfähig ist.
* Stellen Sie sicher, dass Sie über eine Ressourcengruppe verfügen, die im Resource Manager-Bereitstellungsmodell erstellt wurde.
* Lesen Sie die folgende Dokumentation zur Ressourcenmigration:

    * [Plattformgestützte Migration von IaaS-Ressourcen aus dem klassischen Bereitstellungsmodell zu Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)
    * [Ausführliche technische Informationen zur plattformgestützten Migration vom klassischen Bereitstellungsmodell zu Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-deep-dive.md)
    * [Häufig gestellte Fragen: Plattformgestützte Migration von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-faq.md)
    * [Überprüfen der häufigsten Fehler bei der Migration und Gegenmaßnahmen](../virtual-machines/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Unterstützte und nicht unterstützte Szenarien

* Eine ExpressRoute-Verbindung kann ohne Downtime von der klassischen Umgebung zur Resource Manager-Umgebung verschoben werden. Sie können jede ExpressRoute-Verbindung ohne Downtime von der klassischen Umgebung auf die Resource Manager-Umgebung umstellen. Befolgen Sie die Anweisungen zum [Verschieben von ExpressRoute-Verbindungen vom klassischen Bereitstellungsmodell zum Resource Manager-Bereitstellungsmodell mithilfe von PowerShell](expressroute-howto-move-arm.md). Dies ist eine Voraussetzung für das Verschieben von Ressourcen, die mit dem virtuellen Netzwerk verbunden sind.
* Virtuelle Netzwerke, Gateways und zugeordnete Bereitstellungen innerhalb des virtuellen Netzwerks, die einer ExpressRoute-Verbindung im selben Abonnement angefügt sind, können ohne Downtime zur Resource Manager-Umgebung migriert werden. Sie können die weiter unten beschriebenen Schritte ausführen, um Ressourcen wie virtuelle Netzwerke, Gateways und im virtuellen Netzwerk bereitgestellte virtuelle Computer zu migrieren. Sie müssen sicherstellen, dass die virtuellen Netzwerke ordnungsgemäß konfiguriert sind, bevor sie migriert werden. 
* Bei virtuellen Netzwerken, Gateways und zugeordneten Bereitstellungen innerhalb des virtuellen Netzwerks, die nicht im selben Abonnement wie die ExpressRoute-Verbindung enthalten sind, ist eine gewisse Downtime zum Abschließen der Migration erforderlich. Im letzten Abschnitt des Dokuments werden die Schritte zum Migrieren von Ressourcen beschrieben.
* Ein virtuelles Netzwerk mit einem ExpressRoute-Gateway und einem VPN-Gateway kann nicht migriert werden.
* Die abonnementübergreifende Migration einer ExpressRoute-Verbindung wird nicht unterstützt. Weitere Informationen finden Sie im Abschnitt zur [Unterstützung des Verschiebevorgangs für Microsoft.Network](../azure-resource-manager/management/move-support-resources.md#microsoftnetwork).

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Umstellen einer ExpressRoute-Verbindung vom klassischen Modell auf das Resource Manager-Modell
Sie müssen eine ExpressRoute-Verbindung von der klassischen Umgebung in die Resource Manager-Umgebung verschieben, bevor Sie Ressourcen migrieren, die mit der ExpressRoute-Verbindung verknüpft sind. Informationen zum Ausführen dieser Aufgabe finden Sie in den folgenden Artikeln:

* Lesen Sie die unter [Verschieben von ExpressRoute-Verbindungen vom klassischen zum Resource Manager-Bereitstellungsmodell](expressroute-move.md) bereitgestellten Informationen.
* [Verschieben von ExpressRoute-Verbindungen vom klassischen zum Resource Manager-Bereitstellungsmodell mithilfe von PowerShell](expressroute-howto-move-arm.md)
* Verwenden Sie das Azure-Service-Verwaltungsportal. Sie können dem Workflow zum [Erstellen einer neuen ExpressRoute-Verbindung](expressroute-howto-circuit-portal-resource-manager.md) folgen und die Importoption auswählen. 

Bei diesem Vorgang kommt es zu keinerlei Downtime. Sie können während der Migration weiterhin Daten zwischen Ihrem Standort und Microsoft übertragen.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Migrieren von virtuellen Netzwerken, Gateways und zugehörigen Bereitstellungen

Die Schritte, die Sie für die Migration ausführen, hängen davon ab, ob sich Ihre Ressourcen in demselben Abonnement, in verschiedenen Abonnements oder sowohl in demselben als auch in verschiedenen Abonnements befinden.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Migrieren von virtuellen Netzwerken, Gateways und zugeordneten Bereitstellungen im selben Abonnement wie die ExpressRoute-Verbindung
In diesem Abschnitt werden die Schritte beschrieben, die zum Migrieren eines virtuellen Netzwerks, eines Gateways und zugeordneter Bereitstellungen im selben Abonnement wie die ExpressRoute-Verbindung ausgeführt werden müssen. Bei dieser Migration kommt es zu keinerlei Downtime. Während des Migrationsvorgangs können alle Ressourcen weiterhin verwendet werden. Die Verwaltungsebene ist während der Ausführung der Migration gesperrt. 

1. Stellen Sie sicher, dass die ExpressRoute-Verbindung von der klassischen Umgebung auf die Resource Manager-Umgebung umgestellt wurde.
2. Stellen Sie sicher, dass das virtuelle Netzwerk ordnungsgemäß für die Migration vorbereitet wurde.
3. Registrieren Sie Ihr Abonnement für die Ressourcenmigration. Um Ihr Abonnement für die Ressourcenmigration zu registrieren, verwenden Sie den folgenden PowerShell-Codeausschnitt:

   ```powershell 
   Select-AzSubscription -SubscriptionName <Your Subscription Name>
   Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   ```
4. Überprüfen, Vorbereiten und Migrieren: Verwenden Sie den folgenden PowerShell-Codeausschnitt zum Verschieben des virtuellen Netzwerks:

   ```powershell
   Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
   ```

   Sie können die Migration auch abbrechen, indem Sie das folgende PowerShell-Cmdlet ausführen:

   ```powershell
   Move-AzureVirtualNetwork -Abort $vnetName
   ```

## <a name="next-steps"></a>Nächste Schritte
* [Plattformgestützte Migration von IaaS-Ressourcen aus dem klassischen Bereitstellungsmodell zu Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)
* [Ausführliche technische Informationen zur plattformgestützten Migration vom klassischen Bereitstellungsmodell zu Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-deep-dive.md)
* [Häufig gestellte Fragen: Plattformgestützte Migration von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-faq.md)
* [Überprüfen der häufigsten Fehler bei der Migration und Gegenmaßnahmen](../virtual-machines/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)