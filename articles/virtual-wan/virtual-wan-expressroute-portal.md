---
title: Verwenden von Azure Virtual WAN zum Erstellen von ExpressRoute-Verbindungen mit Azure und lokalen Umgebungen | Microsoft-Dokumentation
description: In diesem Tutorial wird beschrieben, wie Sie Azure Virtual WAN verwenden, um ExpressRoute-Verbindungen mit Azure und lokalen Umgebungen zu erstellen.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 04/02/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: d43cdec9e906087c98759a478ebc0c86beaa1354
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693273"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan-preview"></a>Tutorial: Erstellen einer ExpressRoute-Zuordnung per Azure Virtual WAN (Vorschauversion)

In diesem Tutorial wird veranschaulicht, wie Sie mit Virtual WAN eine Verbindung mit Ihren Ressourcen in Azure herstellen, indem Sie eine ExpressRoute-Verbindung und -Zuordnung verwenden. Weitere Informationen zu Virtual WAN finden Sie auf der Seite mit der [Übersicht über Virtual WAN](virtual-wan-about.md).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines vWAN
> * Erstellen eines Hubs
> * Suchen und Zuordnen einer Verbindung mit dem Hub
> * Zuordnen der Verbindung mit einem oder mehreren Hubs
> * Verbinden eines VNET mit einem Hub
> * Anzeigen Ihrer Virtual WAN-Instanz
> * Anzeigen der Ressourcenintegrität
> * Überwachen einer Verbindung

> [!IMPORTANT]
> Diese öffentliche Vorschauversion wird ohne Servicelevelvereinbarung bereitgestellt und sollte nicht für Produktionsworkloads verwendet werden. Unter Umständen werden bestimmte Features nicht unterstützt, verfügen über eingeschränkte Funktionen und sind nicht an allen Azure-Standorten verfügbar. Weitere Informationen finden Sie unter [Ergänzende Nutzungsbedingungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="register"></a>Registrieren dieses Features

Bevor Sie Virtual WAN konfigurieren können, müssen Sie Ihr Abonnement zunächst für die Vorschauversion registrieren. Andernfalls können Sie Virtual WAN im Portal nicht verwenden. Senden Sie eine E-Mail mit Ihrer Abonnement-ID an **azurevirtualwan\@microsoft.com**, um sich zu registrieren. Sie erhalten eine E-Mail zurück, nachdem Ihr Abonnement registriert wurde.

**Hinweise zur Vorschauversion:**

Die ExpressRoute-Leitung muss in einem Land/einer Region aktiviert werden, das/die [ExpressRoute Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#where-is-expressroute-global-reach-supported) unterstützt.
Zum Herstellen einer Verbindung mit dem Virtual WAN-Hub muss eine ExpressRoute Premium-Leitung verwendet werden. 

## <a name="vnet"></a>1. Erstellen eines virtuellen Netzwerks

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="openvwan"></a>2. Erstellen eines virtuellen WAN

Navigieren Sie in einem Browser zum [Azure-Portal (Vorschauversion)](https://aka.ms/azurevirtualwanpreviewfeatures), und melden Sie sich mit Ihrem Azure-Konto an.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

### <a name="getting-started-page"></a>Seite „Erste Schritte“

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-gettingstarted-include.md)]

## <a name="hub"></a>3. Erstellen eines Hubs

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="hub"></a>4. Suchen und Zuordnen einer Verbindung mit dem Hub

1. Wählen Sie Ihr vWAN und unter **Virtual WAN Architecture** (Virtual WAN-Architektur) dann die Option **ExpressRoute-Leitungen** aus.
1. Klicken Sie für Ihr Abonnement bzw. Ihre Abonnements auf **Select ExpressRoute circuit** (ExpressRoute-Leitung auswählen), wenn sich die ExpressRoute-Leitung unter demselben Abonnement wie Ihr vWAN befindet. 
1. Wählen Sie über das Pulldownmenü die ExpressRoute-Leitung, die Sie dem Hub zuordnen möchten.
1. Falls sich die ExpressRoute-Leitung nicht unter demselben Abonnement befindet oder Sie einen [Autorisierungsschlüssel und eine Peer-ID](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md) erhalten haben, wählen Sie die Option **Find a circuit redeeming an authorization key** (Leitung durch Einlösen eines Autorisierungsschlüssels finden).
1. Geben Sie die folgenden Details ein:
1. **Autorisierungsschlüssel**: Wird wie oben beschrieben vom Leitungsbesitzer generiert.
1. **Peerleitungs-URI**: Der Leitungs-URI, der vom Leitungsbesitzer bereitgestellt wird und als eindeutiger Bezeichner für die Leitung verwendet wird.
1. **Routinggewichtung** - [Routinggewichtung](../expressroute/expressroute-optimize-routing.md) ermöglicht Ihnen die Bevorzugung bestimmter Pfade, wenn mehrere Leitungen unterschiedlicher Peeringstandorte mit demselben Hub verbunden sind.
1. Klicken Sie auf **Find circuit** (Leitung suchen), und wählen Sie die Leitung aus, falls sie gefunden wird.
1. Wählen Sie über die Dropdownliste mindestens einen Hub aus, und klicken Sie auf **Speichern**.

## <a name="vnet"></a>5. Verbinden Ihres VNET mit einem Hub

In diesem Schritt erstellen Sie die Peeringverbindung zwischen Ihrem Hub und einem VNET. Wiederholen Sie diese Schritte für jedes VNET, mit dem Sie eine Verbindung herstellen möchten.

1. Klicken Sie auf der Seite für Ihr virtuelles WAN auf **Virtual network connection** (VNET-Verbindung).
2. Klicken Sie auf der Seite für die VNET-Verbindung auf **+Add connection** (+Verbindung hinzufügen).
3. Füllen Sie auf der Seite **Add connection** (Verbindung hinzufügen) die folgenden Felder aus:

    * **Verbindungsname**: Dies ist der Name Ihrer Verbindung.
    * **Hubs**: Wählen Sie den Hub aus, den Sie dieser Verbindung zuordnen möchten.
    * **Abonnement**: Überprüfen Sie das Abonnement.
    * **Virtuelles Netzwerk**: Wählen Sie das virtuelle Netzwerk aus, das Sie mit diesem Hub verbinden möchten. Für das virtuelle Netzwerk kann nicht bereits ein Gateway für virtuelle Netzwerke vorhanden sein.


## <a name="viewwan"></a>6. Anzeigen Ihrer Virtual WAN-Instanz

1. Navigieren Sie zum virtuellen WAN.
2. Auf der Seite „Übersicht“ steht jeder Punkt auf der Karte für einen Hub. Bewegen Sie den Mauszeiger jeweils auf einen dieser Punkte, um die Zusammenfassung zur Integrität des Hubs anzuzeigen.
3. Im Abschnitt mit den Hubs und Verbindungen können Sie den Hubstatus, die Site, die Region, den VPN-Verbindungsstatus und die Anzahl von ein- und ausgehenden Byte anzeigen.

## <a name="viewhealth"></a>7. Anzeigen der Ressourcenintegrität

1. Navigieren Sie zu Ihrem WAN.
2. Klicken Sie auf der Seite für Ihr WAN im Abschnitt **Support + Problembehandlung** auf **Integrität**, und zeigen Sie Ihre Ressource an.

## <a name="connectmon"></a>8. Überwachen einer Verbindung

Erstellen Sie eine Verbindung, um die Kommunikation zwischen einer Azure-VM und einem Remotestandort zu überwachen. Weitere Informationen zum Einrichten einer Verbindungsüberwachung finden Sie unter [Überwachen der Netzwerkkommunikation](~/articles/network-watcher/connection-monitor.md). Das Quellfeld ist die VM-IP in Azure, und die Ziel-IP ist die Site-IP.

## <a name="cleanup"></a>9. Bereinigen von Ressourcen

Wenn Sie diese Ressourcen nicht mehr benötigen, können Sie den Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) verwenden, um die Ressourcengruppe und alle darin enthaltenen Ressourcen zu entfernen. Ersetzen Sie „myResourceGroup“ durch den Namen Ihrer Ressourcengruppe, und führen Sie den folgenden PowerShell-Befehl aus:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines vWAN
> * Erstellen eines Hubs
> * Suchen und Zuordnen einer Verbindung mit dem Hub
> * Zuordnen der Verbindung mit einem oder mehreren Hubs
> * Verbinden eines VNET mit einem Hub
> * Anzeigen Ihrer Virtual WAN-Instanz
> * Anzeigen der Ressourcenintegrität
> * Überwachen einer Verbindung

Weitere Informationen zu Virtual WAN finden Sie auf der Seite mit der [Übersicht über Virtual WAN](virtual-wan-about.md).
