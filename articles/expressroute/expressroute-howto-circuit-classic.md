---
title: 'Azure ExpressRoute: Ändern einer Verbindung: PowerShell: klassisch'
description: In diesem Artikel werden die Schritte beschrieben, die zum Überprüfen des Status, Aktualisieren oder Löschen und erneuten Bereitstellen Ihrer ExpressRoute-Verbindung mit klassischem Bereitstellungsmodell ausgeführt werden müssen.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/05/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: be45d49d3f445810c7ac6a38e3e12abe178a4bed
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018157"
---
# <a name="modify-an-expressroute-circuit-using-powershell-classic"></a>Ändern einer ExpressRoute-Verbindung mit PowerShell (klassisch)

> [!div class="op_single_selector"]
> * [Azure portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure-Befehlszeilenschnittstelle](howto-circuit-cli.md)
> * [Azure Resource Manager-Vorlage](expressroute-howto-circuit-resource-manager-template.md)
> * [Video – Azure-Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klassisch)](expressroute-howto-circuit-classic.md)
>

In diesem Artikel werden die Schritte beschrieben, die zum Überprüfen des Status, Aktualisieren oder Löschen und erneuten Bereitstellen Ihrer ExpressRoute-Verbindung mit klassischem Bereitstellungsmodell ausgeführt werden müssen. Dieser Artikel gilt für das klassische Bereitstellungsmodell.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Informationen zu Azure-Bereitstellungsmodellen**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="get-the-status-of-a-circuit"></a>Abrufen des Status einer Verbindung

Sie können diese Informationen jederzeit mithilfe des Cmdlets `Get-AzureCircuit` abrufen. Wenn Sie den Aufruf ohne Parameter durchführen, werden alle Verbindungen aufgelistet.

```powershell
Get-AzureDedicatedCircuit

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled

Bandwidth                        : 1000
CircuitName                      : MyAsiaCircuit
Location                         : Singapore
ServiceKey                       : #################################
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Sie können Informationen zu einer bestimmten ExpressRoute-Verbindung erhalten, indem Sie den Dienstschlüssel als Parameter an den Aufruf übergeben.

```powershell
Get-AzureDedicatedCircuit -ServiceKey "*********************************"

Bandwidth                        : 200
CircuitName                      : MyTestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Ausführliche Beschreibungen aller Parameter erhalten Sie, wenn Sie folgendes Beispiel ausführen:

```powershell
get-help get-azurededicatedcircuit -detailed
```

## <a name="modify-a-circuit"></a>Ändern einer Verbindung

Sie können bestimmte Eigenschaften einer ExpressRoute-Verbindung ändern, ohne die Konnektivität zu beeinträchtigen.

Sie können folgende Aufgaben ausführen, ohne Ausfallzeiten zu verursachen:

* Aktivieren oder deaktivieren Sie ein ExpressRoute Premium-Add-On für Ihre ExpressRoute-Verbindung.
* Erhöhen Sie die Bandbreite der ExpressRoute-Verbindung, sofern Kapazität am Port vorhanden ist. Ein Downgrade der Bandbreite einer Verbindung wird nicht unterstützt.
* Sie können den Abrechnungsplan von „Volumentarif“ zu „Datenflatrate“ ändern. Ein Ändern des Abrechnungsplans von „Datenflatrate“ in „Datentaktung“ (Volumentarif) wird nicht unterstützt.
* Sie können die Option *Klassische Vorgänge zulassen* aktivieren und deaktivieren.

Weitere Informationen zu Beschränkungen und Grenzwerten finden Sie unter [ExpressRoute – Häufig gestellte Fragen](expressroute-faqs.md) .

### <a name="enable-the-expressroute-premium-add-on"></a>Aktivieren des ExpressRoute Premium-Add-Ons

Für Ihre bereits vorhandene Verbindung können Sie das ExpressRoute Premium-Add-On mit dem folgenden PowerShell-Cmdlet aktivieren:

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Premium
Status                           : Enabled
```

Für Ihre Verbindung sind nun die Features des ExpressRoute Premium-Add-Ons aktiviert. Nachdem der Befehl erfolgreich ausgeführt wurde, beginnt die Abrechnung für das Premium-Add-On.

### <a name="disable-the-expressroute-premium-add-on"></a>Deaktivieren des ExpressRoute Premium-Add-Ons

> [!IMPORTANT]
> Bei diesem Vorgang kann ein Fehler auftreten, wenn Sie Ressourcen verwenden, die die zulässige Menge für die Standardverbindung überschreiten.
>
>

#### <a name="considerations"></a>Überlegungen

* Stellen Sie sicher, dass die Anzahl von virtuellen Netzwerken, die mit der Verbindung verknüpft sind, kleiner als zehn ist, bevor Sie ein Downgrade von Premium auf Standard durchführen. Andernfalls verursacht Ihre Updateanforderung einen Fehler, und Ihnen werden die Premium-Gebühren berechnet.
* Sie müssen die Verknüpfung für alle virtuellen Netzwerke in anderen geopolitischen Regionen aufheben. Andernfalls verursacht Ihre Updateanforderung einen Fehler, und Ihnen werden die Premium-Gebühren berechnet.
* Ihre Routentabelle muss für das private Peering weniger als 4.000 Routen aufweisen. Wenn Ihre Routentabelle mehr als 4.000 Routen umfasst, wird die BGP-Sitzung verworfen. Eine erneute Aktivierung ist erst dann wieder möglich, wenn die Anzahl der angekündigten Präfixe unter 4.000 fällt.

#### <a name="to-disable-the-premium-add-on"></a>So deaktivieren Sie das Premium-Add-On

Für Ihre bereits vorhandene Verbindung können Sie das ExpressRoute Premium-Add-On mit dem folgenden PowerShell-Cmdlet deaktivieren:

```powershell

Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

### <a name="update-the-expressroute-circuit-bandwidth"></a>Aktualisieren der Bandbreite für die ExpressRoute-Verbindung

Informationen zu unterstützten Bandbreitenoptionen für Ihren Anbieter finden Sie unter [ExpressRoute – Häufig gestellte Fragen](expressroute-faqs.md) . Sie können eine beliebige Größe auswählen, die größer als die vorhandene Verbindung ist, sofern der physische Port (für den die Verbindung erstellt wird) dies zulässt.

> [!IMPORTANT]
> Unter Umständen müssen Sie die ExpressRoute-Verbindung neu erstellen, wenn nicht ausreichend Kapazität am vorhandenen Port verfügbar ist. Die Verbindung kann nicht aktualisiert werden, wenn an dieser Stelle keine zusätzliche Kapazität verfügbar ist.
>
> Es ist nicht möglich, die Bandbreite einer ExpressRoute-Verbindung ohne Störungen zu reduzieren. Ein Downgrade der Bandbreite erfordert, dass Sie die Bereitstellung der ExpressRoute-Verbindung aufheben und dann eine neue ExpressRoute-Verbindung bereitstellen.
>
>

#### <a name="resize-a-circuit"></a>Ändern der Größe einer Verbindung

Sobald Sie sich für die benötigte Größe entschieden haben, können Sie den folgenden Befehl verwenden, um die Größe der Verbindung anzupassen.

```powershell
Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

Bandwidth                        : 1000
CircuitName                      : TestCircuit
Location                         : Silicon Valley
ServiceKey                       : *********************************
ServiceProviderName              : equinix
ServiceProviderProvisioningState : Provisioned
Sku                              : Standard
Status                           : Enabled
```

Nachdem die Größe für Ihre Verbindung auf Microsoft-Seite festgelegt wurde, müssen Sie sich an Ihren Konnektivitätsanbieter wenden, um die Konfigurationen gemäß dieser Änderung aktualisieren zu lassen. Ab diesem Punkt beginnt die Abrechnung für die aktualisierte Bandbreitenoption.

Wenn beim Erhöhen der Verbindungsbandbreite der folgende Fehler angezeigt wird, reicht die restliche Bandbreite auf dem physischen Port, auf dem die vorhandene Verbindung erstellt ist, nicht aus. Sie müssen diese Verbindung löschen und eine neue Verbindung mit der erforderlichen Größe erstellen.

```powershell
Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
update operation
At line:1 char:1
+ Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
  + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand
```

## <a name="deprovision-and-delete-a-circuit"></a>Aufheben der Bereitstellung und Löschen einer Verbindung

### <a name="considerations"></a>Überlegungen

* Sie müssen die Verknüpfung aller virtuellen Netzwerke mit der ExpressRoute-Verbindung aufheben, damit dieser Vorgang erfolgreich durchgeführt werden kann. Überprüfen Sie, ob noch virtuelle Netzwerke mit der Verbindung verknüpft sind, falls dieser Vorgang nicht erfolgreich ist.
* Wenn der Bereitstellungsstatus des ExpressRoute-Verbindungsdienstanbieters **Bereitstellung** oder **Bereitgestellt** lautet, arbeiten Sie mit Ihrem Dienstanbieter zusammen, um die Verbindungsbereitstellung auf Anbieterseite aufzuheben. Microsoft reserviert weiterhin Ressourcen für Sie und stellt Ihnen dies in Rechnung, bis der Dienstanbieter die Aufhebung der Verbindungsbereitstellung abgeschlossen hat und uns benachrichtigt.
* Wenn der Dienstanbieter die Bereitstellung der Verbindung aufgehoben hat (Bereitstellungsstatus des Dienstanbieters lautet **Nicht bereitgestellt**), können Sie die Verbindung löschen. Damit wird die Abrechnung für die Verbindung beendet.

#### <a name="delete-a-circuit"></a>Löschen einer Verbindung

Sie können die ExpressRoute-Verbindung löschen, indem Sie den folgenden Befehl ausführen:

```powershell
Remove-AzureDedicatedCircuit -ServiceKey "*********************************"
```
