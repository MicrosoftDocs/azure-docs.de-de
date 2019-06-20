---
title: 'Löschen eines Gateways für virtuelle Netzwerke über das Portal Azure-Portal: Resource Manager | Microsoft-Dokumentation'
description: Löschen Sie ein Gateway des virtuellen Netzwerks über das Azure-Portal im Resource Manager-Bereitstellungsmodell.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.date: 10/23/2018
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: 387b4e982772f22453876e1ea8b9e7c4039601c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60845687"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Löschen eines Gateways für virtuelle Netzwerke über das Portal

> [!div class="op_single_selector"]
> * [Azure-Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klassisch)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Dieser Artikel enthält Anweisungen zum Löschen von Azure-VPN-Gateways, die mit dem Resource Manager-Bereitstellungsmodell bereitgestellt wurden. Sie können zwischen ein paar unterschiedlichen Ansätzen wählen, wenn Sie ein Gateway des virtuellen Netzwerks für eine VPN-Gatewaykonfiguration löschen möchten.

- Wenn Sie alles löschen und von Neuem anfangen möchten, wie im Fall einer Testumgebung, können Sie die Ressourcengruppe löschen. Wenn Sie eine Ressourcengruppe löschen, werden alle Ressourcen innerhalb der Gruppe gelöscht. Diese Vorgehensweise wird nur empfohlen, wenn Sie keine der Ressourcen aus der Ressourcengruppe beibehalten möchten. Sie können mit diesem Ansatz nicht selektiv nur ein paar Ressourcen löschen.

- Wenn Sie einige der Ressourcen aus der Ressourcengruppe beibehalten möchten, ist das Löschen eines Gateways des virtuellen Netzwerks etwas komplizierter. Bevor Sie das Gateway des virtuellen Netzwerks löschen können, müssen Sie zuerst alle Ressourcen löschen, die von dem Gateway abhängig sind. Welche Schritte Sie ausführen, hängt vom Typ der Verbindungen ab, die Sie erstellt haben, und den abhängigen Ressourcen für jede Verbindung.

> [!IMPORTANT]
> Die folgenden Anweisungen beschreiben das Löschen von Azure-VPN-Gateways, die mit dem Resource Manager-Bereitstellungsmodell bereitgestellt wurden. Um ein VPN-Gateway zu löschen, das mit dem klassischen Bereitstellungsmodell bereitgestellt wurde, verwenden Sie Azure PowerShell wie [hier](vpn-gateway-delete-vnet-gateway-classic-powershell.md) beschrieben.


## <a name="delete-a-vpn-gateway"></a>Löschen eines VPN-Gateways

Um ein Gateway für virtuelle Netzwerke zu löschen, müssen Sie zunächst jede Ressource löschen, die zu dem Gateway für virtuelle Netzwerke gehört. Ressourcen müssen aufgrund von Abhängigkeiten in einer bestimmten Reihenfolge gelöscht werden.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

Zu diesem Zeitpunkt wird das virtuelle Netzwerkgateway gelöscht. In den nächsten Schritten können Sie Ressourcen löschen, die nicht mehr verwendet werden.

### <a name="to-delete-the-local-network-gateway"></a>Löschen des lokalen Netzwerkgateways

1. Suchen Sie in **Alle Ressourcen** nach den lokalen Netzwerkgateways, die den einzelnen Verbindungen zugeordnet wurden.
2. Klicken Sie auf dem Blatt **Übersicht** für das lokale Netzwerkgateway auf **Löschen**.

### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Löschen der öffentlichen IP-Adressressource für das Gateway

1. Suchen Sie in **Alle Ressourcen** nach der öffentlichen IP-Adressressource, die dem Gateway zugeordnet wurde. Wenn das Gateway des virtuellen Netzwerks aktiv/aktiv war, sehen Sie zwei öffentliche IP-Adressen. 
2. Klicken Sie auf der Seite **Übersicht** für die öffentliche IP-Adresse auf **Löschen** und zum Bestätigen auf **Ja**.

### <a name="to-delete-the-gateway-subnet"></a>Löschen des Gatewaysubnetzes

1. Suchen Sie in **Alle Ressourcen** nach dem virtuellen Netzwerk. 
2. Klicken Sie auf dem Blatt **Subnetze** auf das **GatewaySubnet**, und klicken Sie anschließend auf **Löschen**. 
3. Klicken Sie auf **Ja**, um zu bestätigen, dass Sie das Gatewaysubnetz löschen möchten.

## <a name="deleterg"></a>Löschen eines VPN-Gateways durch Löschen der Ressourcengruppe

Wenn Sie Ihre bestehenden Ressourcen in der Ressourcengruppe nicht behalten müssen und neu beginnen möchten, können Sie eine gesamte Ressourcengruppe löschen. Sie lernen hier eine schnelle Möglichkeit kennen, alles zu entfernen. Die folgenden Schritte gelten für das Resource Manager-Bereitstellungsmodell.

1. Suchen Sie in **Alle Ressourcen** nach der Ressourcengruppe, und klicken Sie darauf, um das Blatt zu öffnen.
2. Klicken Sie auf **Löschen**. Auf dem Blatt „Löschen“ werden die betroffenen Ressourcen angezeigt. Vergewissern Sie sich, dass alle diese Ressourcen gelöscht werden sollen. Führen Sie andernfalls die Schritte unter „Löschen eines VPN Gateway“ am Anfang dieses Artikels aus.
3. Geben Sie zum Fortfahren den Namen der zu löschenden Ressourcengruppe ein, und klicken Sie auf **Löschen**.
