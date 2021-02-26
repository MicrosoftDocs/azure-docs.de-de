---
title: 'Azure ExpressRoute: Konfigurieren von ExpressRoute Global Reach: Befehlszeilenschnittstelle (CLI)'
description: Erfahren Sie, wie Sie ExpressRoute-Leitungen miteinander verbinden können, um ein privates Netzwerk zwischen Ihren lokalen Netzwerken aufzubauen und Global Reach mithilfe der Azure CLI zu aktivieren.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/07/2021
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 55f1e4c030dc0e7c8d0011dcff0cc1c9f8fec06e
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260230"
---
# <a name="configure-expressroute-global-reach-by-using-the-azure-cli"></a>Konfigurieren von ExpressRoute Global Reach unter Verwendung der Azure CLI

Dieser Artikel unterstützt Sie bei der Konfiguration von Azure ExpressRoute Global Reach unter Verwendung der Azure CLI. Weitere Informationen finden Sie unter [ExpressRoute Global Reach](expressroute-global-reach.md).
 
Führen Sie vor Beginn der Konfiguration die folgenden Anforderungen Schritte aus:

* Installieren Sie die aktuelle Version der Azure-Befehlszeilenschnittstelle (CLI). Informationen finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli) sowie unter [Erste Schritte mit der Azure CLI](/cli/azure/get-started-with-azure-cli).
* Machen Sie sich mit den [Workflows](expressroute-workflows.md) zur Bereitstellung von ExpressRoute-Leitungen vertraut.
* Vergewissern Sie sich, dass Ihre ExpressRoute-Leitungen den Status „Bereitgestellt“ haben.
* Stellen Sie sicher, dass für Ihre ExpressRoute-Leitungen das private Azure-Peering konfiguriert ist.  

### <a name="sign-in-to-your-azure-account"></a>Anmelden bei Ihrem Azure-Konto

Um mit der Konfiguration zu beginnen, melden Sie sich zunächst bei Ihrem Azure-Konto an. Der folgende Befehl öffnet Ihren Standardbrowser und fordert Sie zur Eingabe der Anmeldeinformationen für Ihr Azure-Konto auf:  

```azurecli
az login
```

Überprüfen Sie die Abonnements für das Konto, wenn Sie über mehrere Azure-Abonnements verfügen:

```azurecli
az account list
```

Geben Sie das Abonnement an, das Sie verwenden möchten:

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Bestimmen der zu konfigurierenden ExpressRoute-Leitungen

Sie können ExpressRoute Global Reach zwischen zwei beliebigen ExpressRoute-Leitungen aktivieren. Die Leitungen müssen sich in unterstützten Ländern bzw. Regionen befinden und an unterschiedlichen Peeringstandorten erstellt worden sein. Falls sich beide Leitungen unter Ihrem Abonnement befinden, können Sie eine der Leitungen für die Ausführung der Konfiguration auswählen. Wenn sich die beiden Leitungen aber in unterschiedlichen Azure-Abonnements befinden, müssen Sie über eine der Leitungen einen Autorisierungsschlüssel erstellen. Mit dem über die erste Leitung generierten Autorisierungsschlüssel können Sie Global Reach für die zweite Leitung aktivieren.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Aktivieren der Konnektivität zwischen Ihren lokalen Netzwerken

Beachten Sie bei der Ausführung des Befehls zur Aktivierung der Konnektivität die folgenden Anforderungen an Parameterwerte:

* Die Peerleitung *peer-circuit* muss die vollständige Ressourcen-ID sein. Beispiel:

  > /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering

* *address-prefix* muss ein IPv4-Subnetz vom Typ „/29“ sein (zum Beispiel: 10.0.0.0/29). Wir verwenden IP-Adressen in diesem Subnetz, um eine Verbindung zwischen den beiden ExpressRoute-Leitungen herzustellen. Sie können keine Adressen dieses Subnetzes in Ihren virtuellen Azure-Netzwerken oder lokalen Netzwerken verwenden.

Führen Sie den folgenden CLI-Befehl aus, um zwei ExpressRoute-Leitungen zu verbinden:

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

Die CLI-Ausgabe sieht wie folgt aus:

```output
{
  "addressPrefix": "<__.__.__.__/29>",
  "authorizationKey": null,
  "circuitConnectionStatus": "Connected",
  "etag": "W/\"48d682f9-c232-4151-a09f-fab7cb56369a\"",
  "expressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<ResourceGroupName>"
  },
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering/connections/<ConnectionName>",
  "name": "<ConnectionName>",
  "peerExpressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<Circuit2ResourceGroupName>"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "<ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/peerings/connections"
}
```

Wenn der beschriebene Vorgang abgeschlossen ist, verfügen Sie dank der beiden ExpressRoute-Leitungen auf beiden Seiten über Konnektivität zwischen Ihren lokalen Netzwerken.

## <a name="enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions"></a>Aktivieren der Konnektivität zwischen ExpressRoute-Leitungen in verschiedenen Azure-Abonnements

Wenn sich die beiden Leitungen nicht im gleichen Azure-Abonnement befinden, benötigen Sie eine Autorisierung. In der folgenden Konfiguration generieren Sie die Autorisierung aus dem Abonnement von Leitung 2. Anschließend übergeben Sie den Autorisierungsschlüssel an Leitung 1.

1. Generieren Sie einen Autorisierungsschlüssel:

   ```azurecli
   az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
   ```

   Die CLI-Ausgabe sieht wie folgt aus:

   ```output
   {
     "authorizationKey": "<authorizationKey>",
     "authorizationUseStatus": "Available",
     "etag": "W/\"cfd15a2f-43a1-4361-9403-6a0be00746ed\"",
     "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/authorizations/<AuthorizationName>",
     "name": "<AuthorizationName>",
     "provisioningState": "Succeeded",
     "resourceGroup": "<Circuit2ResourceGroupName>",
     "type": "Microsoft.Network/expressRouteCircuits/authorizations"
   }
   ```

1. Notieren Sie sich sowohl die Ressourcen-ID als auch den Autorisierungsschlüssel für Leitung 2.

1. Führen Sie den folgenden Befehl für Leitung 1 aus, und übergeben Sie die Ressourcen-ID und den Autorisierungsschlüssel von Leitung 2:

   ```azurecli
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

Wenn der beschriebene Vorgang abgeschlossen ist, verfügen Sie dank der beiden ExpressRoute-Leitungen auf beiden Seiten über Konnektivität zwischen Ihren lokalen Netzwerken.

## <a name="get-and-verify-the-configuration"></a>Abrufen und Überprüfen der Konfiguration

Verwenden Sie den folgenden Befehl, um die Konfiguration für die Leitung zu überprüfen, für die die Konfiguration vorgenommen wurde (Leitung 1 im vorstehenden Beispiel):

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

In der CLI-Ausgabe wird *CircuitConnectionStatus* angezeigt. Er gibt über den Status „Connected“ (Verbunden) oder „Disconnected“ (Nicht verbunden) an, ob Konnektivität zwischen den beiden Leitungen besteht. 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Deaktivieren der Konnektivität zwischen Ihren lokalen Netzwerken

Führen Sie den folgenden Befehl zum Deaktivieren der Konnektivität für die Leitung aus, für die die Konfiguration vorgenommen wurde (Leitung 1 im obigen Beispiel).

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Verwenden Sie den Befehl ```show```, um den Status zu überprüfen.

Nach Abschluss dieses Vorgangs besteht zwischen Ihren lokalen Netzwerken keine Verbindung mehr über Ihre ExpressRoute-Leitungen.

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zu ExpressRoute Global Reach](expressroute-global-reach.md)
* [Überprüfen der ExpressRoute-Konnektivität](expressroute-troubleshooting-expressroute-overview.md)
* [Verknüpfen einer ExpressRoute-Verbindung mit einem virtuellen Netzwerk](expressroute-howto-linkvnet-arm.md)
