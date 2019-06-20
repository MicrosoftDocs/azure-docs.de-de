---
title: 'Konfigurieren des Peerings für eine Verbindung – ExpressRoute: Azure CLI | Microsoft-Dokumentation'
description: Dieser Artikel unterstützt Sie bei der Erstellung und Bereitstellung des privaten, öffentlichen und Microsoft-Peerings einer ExpressRoute-Verbindung. Außerdem wird veranschaulicht, wie Sie den Status überprüfen, Updates durchführen oder Peerings für die Verbindung löschen.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 3f27e10c47b84b6f037c0d9422e9fba40e0315f7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64717036"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit-using-cli"></a>Erstellen und Ändern des Peerings für eine ExpressRoute-Verbindung mithilfe von CLI

In diesem Artikel erfahren Sie, wie Sie die Routingkonfiguration bzw. das Peering einer ExpressRoute-Verbindung mithilfe der CLI im Resource Manager-Bereitstellungsmodell erstellen und verwalten. Sie können außerdem den Status prüfen, ein Update durchführen oder Peerings für eine ExpressRoute-Verbindung löschen bzw. deren Bereitstellung aufheben. Wenn Sie eine andere Methode für die Arbeit mit Ihrer Verbindung verwenden möchten, wählen Sie einen Artikel aus der folgenden Liste aus:

> [!div class="op_single_selector"]
> * [Azure-Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure-Befehlszeilenschnittstelle](howto-routing-cli.md)
> * [Video – Privates Peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video – Öffentliches Peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video – Microsoft-Peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassisch)](expressroute-howto-routing-classic.md)
> 

## <a name="configuration-prerequisites"></a>Konfigurationsvoraussetzungen

* Installieren Sie als Vorbereitung die aktuelle Version der CLI-Befehle (2.0 oder höher). Informationen zur Installation der CLI-Befehle finden Sie unter [Installieren von Azure CLI](/cli/azure/install-azure-cli).
* Stellen Sie sicher, dass Sie vor Beginn der Konfiguration die Seiten [Voraussetzungen](expressroute-prerequisites.md), [Routinganforderungen](expressroute-routing.md) und [Workflows](expressroute-workflows.md) gelesen haben.
* Sie benötigen eine aktive ExpressRoute-Verbindung. Führen Sie die Schritte zum [Erstellen einer ExpressRoute-Verbindung](howto-circuit-cli.md) aus, und lassen Sie sie vom Konnektivitätsanbieter aktivieren, bevor Sie fortfahren. Die ExpressRoute-Verbindung muss sich im Zustand „Provisioned“ und „Enabled“ befinden, damit Sie die Befehle in diesem Artikel ausführen können.

Diese Anweisungen gelten nur für Verbindungen, die über Service Provider erstellt wurden, von denen Layer 2-Konnektivitätsdienste angeboten werden. Wenn Sie einen Dienstanbieter nutzen, der verwaltete Layer 3-Dienste anbietet (meist ein IP-VPN, z.B. MPLS), übernimmt Ihr Konnektivitätsanbieter die Konfiguration und Verwaltung des Routings für Sie.

Sie können ein, zwei oder alle drei Peerings (Azure privat, Azure öffentlich und Microsoft) für eine ExpressRoute-Verbindung konfigurieren. Sie können Peerings in beliebiger Reihenfolge konfigurieren. Sie müssen jedoch sicherstellen, dass Sie die Konfiguration jedes Peerings einzeln nacheinander durchführen. Weitere Informationen zu Routingdomänen und Peerings finden Sie unter [ExpressRoute-Routingdomänen](expressroute-circuit-peerings.md).

## <a name="msft"></a>Microsoft-Peering

Dieser Abschnitt unterstützt Sie beim Erstellen, Abrufen, Aktualisieren und Löschen der Microsoft-Peeringkonfiguration für eine ExpressRoute-Verbindung.

> [!IMPORTANT]
> Beim Microsoft-Peering von ExpressRoute-Verbindungen, die vor dem 1. August 2017 konfiguriert wurden, werden alle Dienstpräfixe über das Microsoft-Peering angekündigt, auch wenn keine Routenfilter definiert sind. Beim Microsoft-Peering von ExpressRoute-Verbindungen, die am oder nach dem 1. August 2017 konfiguriert wurden, werden Präfixe erst angekündigt, wenn der Verbindung ein Routenfilter hinzugefügt wurde. Weitere Informationen finden Sie unter [Konfigurieren eines Routenfilters für das Microsoft-Peering](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>So erstellen Sie Microsoft-Peering

1. Installieren Sie die neueste Version der Azure CLI. Verwenden Sie die neueste Version der Azure-Befehlszeilenschnittstelle (CLI).* Bevor Sie mit der Konfiguration beginnen, überprüfen Sie die [Voraussetzungen](expressroute-prerequisites.md) und [Workflows](expressroute-workflows.md).

   ```azurecli-interactive
   az login
   ```

   Wählen Sie das Abonnement aus, für das eine ExpressRoute-Verbindung erstellt werden soll.

   ```azurecli-interactive
   az account set --subscription "<subscription ID>"
   ```
2. Erstellen Sie eine ExpressRoute-Verbindung. Führen Sie die Schritte zum Erstellen einer [ExpressRoute-Verbindung](howto-circuit-cli.md) aus, und lassen Sie sie vom Konnektivitätsanbieter bereitstellen. Wenn Ihr Konnektivitätsanbieter verwaltete Layer 3-Dienste im Angebot hat, können Sie bei ihm die Aktivierung des Microsoft-Peerings anfordern. In diesem Fall müssen Sie die Anweisungen in den nächsten Abschnitten nicht befolgen. Falls Ihr Konnektivitätsanbieter das Routing jedoch nicht für Sie verwaltet, setzen Sie Ihre Konfiguration nach dem Erstellen der Verbindung mit den nachfolgenden Schritten fort. 

3. Überprüfen Sie, ob die ExpressRoute-Verbindung bereitgestellt und auch aktiviert wurde. Nehmen Sie das folgende Beispiel:

   ```azurecli-interactive
   az network express-route list
   ```

   Die Antwort ähnelt dem folgenden Beispiel:

   ```azurecli
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. Konfigurieren Sie das Microsoft-Peering für die Verbindung. Stellen Sie vorab sicher, dass die folgenden Informationen vorliegen:

   * Ein /30-Subnetz für die primäre Verknüpfung. Dies muss ein gültiges öffentliches IPv4-Präfix sein, das sich in Ihrem Besitz befindet und über eine RIR/IRR-Registrierung verfügt.
   * Ein /30-Subnetz für die sekundäre Verknüpfung. Dies muss ein gültiges öffentliches IPv4-Präfix sein, das sich in Ihrem Besitz befindet und über eine RIR/IRR-Registrierung verfügt.
   * Eine gültige VLAN-ID zum Einrichten dieses Peerings. Stellen Sie sicher, dass kein anderes Peering der Verbindung die gleiche VLAN-ID verwendet.
   * AS-Nummer für Peering. Sie können sowohl AS-Nummern mit 2 Byte als auch mit 4 Byte verwenden.
   * Angekündigte Präfixe: Sie müssen eine Liste mit allen Präfixen bereitstellen, die Sie über die BGP-Sitzung ankündigen möchten. Nur öffentliche IP-Adresspräfixe werden akzeptiert. Wenn Sie planen, einen Satz mit Präfixen zu senden, können Sie eine durch Komma getrennte Liste senden. Diese Präfixe müssen über eine RIR/IRR-Registrierung für Sie verfügen.
   * **Optional:** Kunden-ASN: Wenn Sie Präfixe ankündigen, die nicht für die Peering-AS-Nummer registriert sind, können Sie die AS-Nummer angeben, unter der sie registriert sind.
   * Routing-Registrierungsname: Sie können den RIR/IRR-Wert angeben, unter dem die AS-Nummer und die Präfixe registriert sind.
   * **Optional** – Einen MD5-Hash, wenn Sie sich für dessen Einsatz entscheiden.

   Führen Sie das folgende Beispiel aus, um das Microsoft-Peering für Ihre Verbindung zu konfigurieren:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 123.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 123.0.0.4/30 --vlan-id 300 --peering-type MicrosoftPeering --advertised-public-prefixes 123.1.0.0/24
   ```

### <a name="getmsft"></a>So zeigen Sie die Details zum Microsoft-Peering an:

Sie können Konfigurationsdetails anhand des folgenden Beispiels abrufen:

```azurecli-interactive
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzureMicrosoftPeering
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```azurecli
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzureMicrosoftPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": {
    "advertisedPublicPrefixes": [
        ""
      ],
     "advertisedPublicPrefixesState": "",
     "customerASN": ,
     "routingRegistryName": ""
  }
  "name": "AzureMicrosoftPeering",
  "peerAsn": ,
  "peeringType": "AzureMicrosoftPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="updatemsft"></a>So aktualisieren Sie die Konfiguration des Microsoft-Peerings:

Sie können einen beliebigen Teil der Konfiguration aktualisieren. Die angekündigten Präfixe der Verbindung werden im folgenden Beispiel von 123.1.0.0/24 in 124.1.0.0/24 aktualisiert:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroup --peering-type MicrosoftPeering --advertised-public-prefixes 124.1.0.0/24
```

### <a name="addIPv6msft"></a>So fügen Sie IPv6-Microsoft-Peeringeinstellungen zu einer vorhandenen IPv4-Konfiguration hinzu

```azurecli-interactive
az network express-route peering update -g ExpressRouteResourceGroup --circuit-name MyCircuit --peering-type MicrosoftPeering --ip-version ipv6 --primary-peer-subnet 2002:db00::/126 --secondary-peer-subnet 2003:db00::/126 --advertised-public-prefixes 2002:db00::/126
```

### <a name="deletemsft"></a>So löschen Sie das Microsoft-Peering:

Sie können Ihre Peeringkonfiguration entfernen, indem Sie das folgende Beispiel ausführen:

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name MicrosoftPeering
```

## <a name="private"></a>Privates Azure-Peering:

Dieser Abschnitt unterstützt Sie beim Erstellen, Abrufen, Aktualisieren und Löschen der privaten Azure-Peeringkonfiguration für eine ExpressRoute-Verbindung.

### <a name="to-create-azure-private-peering"></a>So erstellen Sie ein privates Azure-Peering

1. Installieren Sie die neueste Version der Azure CLI. Sie müssen die neueste Version der Azure-Befehlszeilenschnittstelle (CLI) verwenden.* Bevor Sie mit der Konfiguration beginnen, überprüfen Sie die [Voraussetzungen](expressroute-prerequisites.md) und [Workflows](expressroute-workflows.md).

   ```azurecli-interactive
   az login
   ```

   Wählen Sie das Abonnement aus, mit dem die ExpressRoute-Verbindung erstellt werden soll.

   ```azurecli-interactive
   az account set --subscription "<subscription ID>"
   ```
2. Erstellen Sie eine ExpressRoute-Verbindung. Führen Sie die Schritte zum Erstellen einer [ExpressRoute-Verbindung](howto-circuit-cli.md) aus, und lassen Sie sie vom Konnektivitätsanbieter bereitstellen. Wenn Ihr Konnektivitätsanbieter verwaltete Layer 3-Dienste im Angebot hat, können Sie bei ihm die Aktivierung des privaten Azure-Peerings anfordern. In diesem Fall müssen Sie die Anweisungen in den nächsten Abschnitten nicht befolgen. Falls Ihr Konnektivitätsanbieter das Routing jedoch nicht für Sie verwaltet, setzen Sie Ihre Konfiguration nach dem Erstellen der Verbindung mit den nachfolgenden Schritten fort.

3. Überprüfen Sie, ob die ExpressRoute-Verbindung bereitgestellt und auch aktiviert wurde. Nehmen Sie das folgende Beispiel:

   ```azurecli-interactive
   az network express-route show --resource-group ExpressRouteResourceGroup --name MyCircuit
   ```

   Die Antwort ähnelt dem folgenden Beispiel:

   ```azurecli
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
   "bandwidthInMbps": 200,
   "peeringLocation": "Silicon Valley",
   "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. Konfigurieren Sie das private Azure-Peering für die Verbindung. Stellen Sie sicher, dass Sie über die folgenden Elemente verfügen, bevor Sie mit den nächsten Schritten fortfahren:

   * Ein /30-Subnetz für die primäre Verknüpfung. Dieses Subnetz darf nicht Teil eines Adressraums sein, der für virtuelle Netzwerke reserviert ist.
   * Ein /30-Subnetz für die sekundäre Verknüpfung. Dieses Subnetz darf nicht Teil eines Adressraums sein, der für virtuelle Netzwerke reserviert ist.
   * Eine gültige VLAN-ID zum Einrichten dieses Peerings. Stellen Sie sicher, dass kein anderes Peering der Verbindung die gleiche VLAN-ID verwendet.
   * AS-Nummer für Peering. Sie können sowohl AS-Nummern mit 2 Byte als auch mit 4 Byte verwenden. Sie können eine private AS-Nummer für dieses Peering verwenden. Achten Sie darauf, dass Sie nicht 65515 verwenden.
   * **Optional** – Einen MD5-Hash, wenn Sie sich für dessen Einsatz entscheiden.

   Verwenden Sie das folgende Beispiel, um das private Azure-Peering für Ihre Verbindung zu konfigurieren:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering
   ```

   Wenn Sie sich für den Einsatz eines MD5-Hash entscheiden, verwenden Sie das folgende Beispiel:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 10.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 10.0.0.4/30 --vlan-id 200 --peering-type AzurePrivatePeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Stellen Sie sicher, dass Sie Ihre AS-Nummer als Peering-ASN angeben, nicht als Kunden-ASN.
   > 
   > 

### <a name="getprivate"></a>So zeigen Sie Details zum privaten Azure-Peering an:

Sie können Konfigurationsdetails anhand des folgenden Beispiels abrufen:

```azurecli-interactive
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```azurecli
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePrivatePeering",
  "ipv6PeeringConfig": null,
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePrivatePeering",
  "peerAsn": 7671,
  "peeringType": "AzurePrivatePeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="updateprivate"></a>So aktualisieren Sie die Konfiguration für privates Azure-Peering:

Sie können einen beliebigen Teil der Konfiguration anhand des folgenden Beispiels aktualisieren. In diesem Beispiel wird die VLAN-ID der Verbindung von 100 in 500 geändert.

```azurecli-interactive
az network express-route peering update --vlan-id 500 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

### <a name="deleteprivate"></a>So löschen Sie ein privates Azure-Peering:

Sie können Ihre Peeringkonfiguration entfernen, indem Sie das folgende Beispiel ausführen:

> [!WARNING]
> Sie müssen sicherstellen, dass alle virtuellen Netzwerke und ExpressRoute Global Reach-Verbindungen entfernt wurden, bevor Sie dieses Beispiel ausführen. 
> 
> 

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePrivatePeering
```

## <a name="public"></a>Öffentliches Azure-Peering:

Dieser Abschnitt unterstützt Sie beim Erstellen, Abrufen, Aktualisieren und Löschen der öffentlichen Azure-Peeringkonfiguration für eine ExpressRoute-Verbindung.

> [!Note]
> Öffentliches Azure-Peering gilt für neue Leitungen als veraltet. Weitere Informationen finden Sie unter [ExpressRoute-Peering](expressroute-circuit-peerings.md).
>

### <a name="to-create-azure-public-peering"></a>So erstellen Sie ein öffentliches Azure-Peering

1. Installieren Sie die neueste Version der Azure CLI. Sie müssen die neueste Version der Azure-Befehlszeilenschnittstelle (CLI) verwenden.* Bevor Sie mit der Konfiguration beginnen, überprüfen Sie die [Voraussetzungen](expressroute-prerequisites.md) und [Workflows](expressroute-workflows.md).

   ```azurecli-interactive
   az login
   ```

   Wählen Sie das Abonnement aus, für das eine ExpressRoute-Verbindung erstellt werden soll.

   ```azurecli-interactive
   az account set --subscription "<subscription ID>"
   ```
2. Erstellen Sie eine ExpressRoute-Verbindung.  Führen Sie die Schritte zum Erstellen einer [ExpressRoute-Verbindung](howto-circuit-cli.md) aus, und lassen Sie sie vom Konnektivitätsanbieter bereitstellen. Wenn Ihr Konnektivitätsanbieter verwaltete Layer 3-Dienste im Angebot hat, können Sie bei ihm die Aktivierung des öffentlichen Azure-Peerings anfordern. In diesem Fall müssen Sie die Anweisungen in den nächsten Abschnitten nicht befolgen. Falls Ihr Konnektivitätsanbieter das Routing jedoch nicht für Sie verwaltet, setzen Sie Ihre Konfiguration nach dem Erstellen der Verbindung mit den nachfolgenden Schritten fort.

3. Überprüfen Sie, ob die ExpressRoute-Verbindung bereitgestellt und auch aktiviert wurde. Nehmen Sie das folgende Beispiel:

   ```azurecli-interactive
   az network express-route list
   ```

   Die Antwort ähnelt dem folgenden Beispiel:

   ```azurecli
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

4. Konfigurieren Sie das öffentliche Azure-Peering für die Verbindung. Stellen Sie sicher, dass die folgenden Informationen vorliegen, bevor Sie fortfahren:

   * Ein /30-Subnetz für die primäre Verknüpfung. Dies muss ein gültiges öffentliches IPv4-Präfix sein.
   * Ein /30-Subnetz für die sekundäre Verknüpfung. Dies muss ein gültiges öffentliches IPv4-Präfix sein.
   * Eine gültige VLAN-ID zum Einrichten dieses Peerings. Stellen Sie sicher, dass kein anderes Peering der Verbindung die gleiche VLAN-ID verwendet.
   * AS-Nummer für Peering. Sie können sowohl AS-Nummern mit 2 Byte als auch mit 4 Byte verwenden.
   * **Optional** – Einen MD5-Hash, wenn Sie sich für dessen Einsatz entscheiden.

   Führen Sie das folgende Beispiel aus, um das öffentliche Azure-Peering für Ihre Verbindung zu konfigurieren:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   Wenn Sie sich für den Einsatz eines MD5-Hash entscheiden, verwenden Sie das folgende Beispiel:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > Stellen Sie sicher, dass Sie Ihre AS-Nummer als Peering-ASN angeben, nicht als Kunden-ASN.

### <a name="getpublic"></a>So zeigen Sie Details zum öffentlichen Azure-Peering an:

Sie können Konfigurationsdetails anhand des folgenden Beispiels abrufen:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```azurecli
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePublicPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePublicPeering",
  "peerAsn": 7671,
  "peeringType": "AzurePublicPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="updatepublic"></a>Aktualisieren der Konfiguration für öffentliches Azure-Peering:

Sie können einen beliebigen Teil der Konfiguration anhand des folgenden Beispiels aktualisieren. In diesem Beispiel wird die VLAN-ID der Verbindung von 200 in 600 geändert.

```azurecli-interactive
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="deletepublic"></a>So löschen Sie ein öffentliches Azure-Peering:

Sie können Ihre Peeringkonfiguration entfernen, indem Sie das folgende Beispiel ausführen:

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="next-steps"></a>Nächste Schritte

[Verknüpfen Sie als Nächstes ein VNET mit einer ExpressRoute-Verbindung](howto-linkvnet-cli.md).

* Weitere Informationen zu ExpressRoute-Workflows finden Sie unter [ExpressRoute-Workflows](expressroute-workflows.md).
* Weitere Informationen zum Verbindungspeering finden Sie unter [ExpressRoute-Verbindungen und Routingdomänen](expressroute-circuit-peerings.md).
* Weitere Informationen zur Arbeit mit virtuellen Netzwerken finden Sie unter [Virtuelle Netzwerke im Überblick](../virtual-network/virtual-networks-overview.md).
