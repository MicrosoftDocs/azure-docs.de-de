---
title: 'Mandantenübergreifendes Verbinden von VNETs mit einem Hub: PowerShell'
titleSuffix: Azure Virtual WAN
description: Dieser Artikel unterstützt Sie beim Herstellen der Verbindung mandantenübergreifender VNETs mit einem virtuellen Hub mithilfe von PowerShell.
services: virtual-wan
author: wtnlee
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/28/2020
ms.author: wellee
ms.openlocfilehash: c49a85c71c9b877be7e143f5caf27dc307fe0c12
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381268"
---
# <a name="connect-cross-tenant-vnets-to-a-virtual-wan-hub"></a>Mandantenübergreifendes Verbinden von VNETs mit einem Virtual WAN-Hub

Dieser Artikel unterstützt Sie bei der Verwendung von Virtual WAN, um die Verbindung eines VNET mit einem virtuellen Hub in einem anderen Mandanten herzustellen. Diese Architektur ist nützlich, wenn Sie über Clientworkloads verfügen, die mit dem gleichen Netzwerk verbunden sein müssen, sich jedoch auf unterschiedlichen Mandanten befinden. So können Sie z. B. wie im folgenden Diagramm gezeigt eine Verbindung zwischen einem Nicht-Contoso-VNET (dem Remotemandanten) und einem virtuellen Contoso-Hub (dem übergeordneten Mandanten) herstellen.

:::image type="content" source="./media/cross-tenant-vnet/connectivity.png" alt-text="Einrichten der Routingkonfiguration" :::

In diesem Artikel werden folgende Vorgehensweisen behandelt:

* Hinzufügen eines weiteren Mandanten als Mitwirkender in Ihrem Azure-Abonnement.
* Verbinden eines mandantenübergreifenden VNET mit einem virtuellen Hub.

Die Schritte für diese Konfiguration werden mithilfe einer Kombination aus Azure-Portal und PowerShell ausgeführt. Die Funktion selbst ist jedoch nur in PowerShell und der CLI verfügbar.

## <a name="before-you-begin"></a>Vorbereitungen

### <a name="prerequisites"></a>Voraussetzungen

Damit Sie die Schritte in diesem Artikel ausführen können, müssen Sie die folgende Konfiguration bereits in Ihrer Umgebung eingerichtet haben:

* Ein virtuelles WAN und ein virtueller Hub in ihrem übergeordneten Abonnement.
* Ein virtuelles Netzwerk, das in einem anderen (Remote-) Mandanten in einem Abonnement konfiguriert ist.
* Stellen Sie sicher, dass sich der VNET-Adressraum im Remotemandanten nicht mit einem anderen Adressraum in anderen VNETs überschneidet, die bereits mit dem übergeordneten virtuellen Hub verbunden sind.

### <a name="working-with-azure-powershell"></a>Arbeiten mit Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="assign-permissions"></a><a name="rights"></a>Zuweisen von Berechtigungen

Damit das übergeordnete Abonnement mit dem virtuellen Hub die virtuellen Netzwerke im Remotemandanten ändern und auf diese zugreifen kann, müssen Sie dem übergeordneten Abonnement aus dem Remotemandantenabonnement **Mitwirkender**-Berechtigungen zuweisen.

1. Fügen Sie dem übergeordneten Konto (dem mit dem virtuellen WAN-Hub) die **Mitwirkender**-Rollenzuweisung hinzu. Sie können zum Zuweisen dieser Rolle entweder PowerShell oder das Azure-Portal verwenden. Weitere Informationen finden Sie in den folgenden Artikeln zum **Hinzufügen oder Entfernen von Rollenzuweisungen**:

   * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
   * [Portal](../role-based-access-control/role-assignments-portal.md)

1. Fügen Sie als nächstes das Remotemandantenabonnement und das übergeordnete Mandantenabonnement der aktuellen PowerShell-Sitzung hinzu. Führen Sie den folgenden Befehl aus. Wenn Sie beim übergeordneten Element angemeldet sind, müssen Sie nur den Befehl für den Remotemandanten ausführen.

   ```azurepowershell-interactive
   Add-AzAccount -SubscriptionId "xxxxx-b34a-4df9-9451-4402dcaecc5b"
   ```

1. Überprüfen Sie, ob die Rollenzuweisung erfolgreich ist, indem Sie sich mit den Anmeldeinformationen des übergeordneten Elements bei Azure PowerShell anmelden und den folgenden Befehl ausführen:

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. Wenn die Berechtigungen erfolgreich an das übergeordnete Element weitergegeben und der Sitzung hinzugefügt wurden, wird das im Besitz des Remotemandanten befindliche Abonnement in der Ausgabe des Befehls angezeigt.

## <a name="connect-vnet-to-hub"></a><a name="connect"></a>Verbinden eines VNET mit einem Hub

In den folgenden Schritten wechseln Sie zwischen dem Kontext der beiden Abonnements, wenn Sie das virtuelle Netzwerk mit dem virtuellen Hub verknüpfen. Ersetzen Sie die Beispielwerte Ihrer Umgebung entsprechend.

1. Stellen Sie sicher, dass Sie sich im Kontext des Remotekontos befinden, indem Sie den folgenden Befehl ausführen:

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[remote ID]"
   ```

1. Erstellen Sie eine lokale Variable zum Speichern der Metadaten des virtuellen Netzwerks, das Sie mit dem Hub verbinden möchten.

   ```azurepowershell-interactive
   $remote = Get-AzVirtualNetwork -Name "[vnet name]" -ResourceGroupName "[resource group name]"
   ```

1. Wechseln Sie zurück zum übergeordneten Konto.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[parent ID]"
   ```

1. Stellen Sie die Verbindung des VNET mit dem Hub her.

   ```azurepowershell-interactive
   New-AzVirtualHubVnetConnection -ResourceGroupName "[parent resource group name]" -VirtualHubName "[virtual hub name]" -Name "[name of connection]" -RemoteVirtualNetwork $[local variable name]
   ```

1. Sie können die neue Verbindung entweder in PowerShell oder im Azure-Portal anzeigen.

   * **PowerShell:** Die Metadaten aus der neu hergestellten Verbindung werden in der PowerShell-Konsole angezeigt, wenn die Verbindung erfolgreich hergestellt wurde.
   * **Azure-Portal:** Navigieren Sie zum virtuellen Hub, **Konnektivität -> Virtuelle Netzwerkverbindungen**. Sie können den Zeiger auf die Verbindung anzeigen. Um die tatsächliche Ressource anzuzeigen, benötigen Sie die richtigen Berechtigungen.
   
## <a name="troubleshooting"></a><a name="troubleshoot"></a>Problembehandlung

* Vergewissern Sie sich, dass die Metadaten in $remote (aus dem vorherigen [Abschnitt](#connect)) mit den Informationen aus dem Azure-Portal übereinstimmen.
* Sie können Berechtigungen mit IAM-Einstellungen der Remotemandanten-Ressourcengruppe oder Azure PowerShell-Befehlen (Get-AzSubscription) verwenden.
* Stellen Sie sicher, dass die Namen von Ressourcengruppen oder anderen umgebungsspezifische Variablen in Anführungszeichen eingeschlossen werden (z. B. „VirtualHub1“ oder „VirtualNetwork1“).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Virtual WAN finden Sie unter:

* [Häufig gestellte Fragen](virtual-wan-faq.md) zu Virtual WAN
