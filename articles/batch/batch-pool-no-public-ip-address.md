---
title: Erstellen eines Azure Batch-Pools ohne öffentliche IP-Adressen
description: Erfahren Sie mehr zum Erstellen eines Pools ohne öffentliche IP-Adressen.
author: pkshultz
ms.topic: how-to
ms.date: 10/08/2020
ms.author: peshultz
ms.custom: references_regions
ms.openlocfilehash: 09a5632f969117e69e68bbe0df2bfbab9a8a102b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842134"
---
# <a name="create-an-azure-batch-pool-without-public-ip-addresses"></a>Erstellen eines Azure Batch-Pools ohne öffentliche IP-Adressen

Wenn Sie einen Azure Batch-Pool erstellen, können Sie den Konfigurationspool virtueller Computer ohne eine öffentliche IP-Adresse konfigurieren. In diesem Artikel wird erklärt, wie Sie einen Azure Batch-Pool ohne öffentliche IP-Adressen einrichten.

## <a name="why-use-a-pool-without-public-ip-addresses"></a>Gründe für einen Pool ohne öffentliche IP-Adressen

Standardmäßig wird allen Computeknoten in einem Azure Batch-Konfigurationspool für virtuelle Computer eine öffentliche IP-Adressen zugewiesen. Diese Adresse wird vom Batch-Dienst zur Planung von Aufgaben und Kommunikation mit Computeknoten verwendet, einschließlich des ausgehenden Zugriffs auf das Internet.

Um den Zugriff auf diese Knoten einzuschränken und die Auffindbarkeit dieser Knoten im Internet zu verringern, können Sie den Pool ohne öffentliche IP-Adressen bereitstellen.

> [!IMPORTANT]
> Die Unterstützung von Pools ohne öffentliche IP-Adressen in Azure Batch befindet sich derzeit für die folgenden Regionen in der öffentlichen Vorschau: „Frankreich, Mitte“, „Asien, Osten“, „USA, Westen-Mitte“, „USA, Süden-Mitte“, „USA, Westen 2“, „USA, Osten“, „Europa, Norden“, „USA, Osten 2“, „USA, Mitte“, „Europa, Westen“, „USA, Norden-Mitte“, „USA, Westen“, „Australien, Osten“, „Japan, Osten“, „Japan, Westen“.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

- **Authentifizierung**. Um einen Pool ohne öffentliche IP-Adressen innerhalb eines [virtuellen Netzwerks](./batch-virtual-network.md) zu verwenden, muss die Batch-Client-API die Azure Active Directory-Authentifizierung (AAD) verwenden. Die Azure Batch-Unterstützung für Azure AD ist unter [Authentifizieren von Lösungen des Azure Batch-Diensts mit Active Directory](batch-aad-auth.md) dokumentiert. Wenn Sie Ihren Pool nicht innerhalb eines virtuellen Netzwerks erstellen, kann entweder die Azure AD-Authentifizierung oder die schlüsselbasierte Authentifizierung verwendet werden.

- **Ein Azure VNET** Wenn Sie Ihren Pool in einem [virtuellen Netzwerk](batch-virtual-network.md) erstellen, befolgen Sie diese Anforderungen und Konfigurationen. Um ein VNET mit mindestens einem Subnetz vorzubereiten, können Sie das Azure-Portal, Azure PowerShell, die Azure-Befehlszeilenschnittstelle (CLI) oder andere Methoden verwenden.
  - Das VNET muss sich im gleichen Abonnement und in der gleichen Region befinden wie das für die Poolerstellung verwendete Batch-Konto.
  - Das für den Pool angegebene Subnetz muss über ausreichend nicht zugewiesene IP-Adressen verfügen, um die Anzahl virtueller Computer aufnehmen zu können, die für den Pool geplant sind, d. h. die Summe der `targetDedicatedNodes`- und `targetLowPriorityNodes`-Eigenschaften des Pools. Wenn das Subnetz nicht über ausreichend nicht zugewiesene IP-Adressen verfügt, belegt der Pool teilweise die Computeknoten und es tritt ein Anpassungsfehler auf.
  - Sie müssen die Netzwerkrichtlinien für den Private Link-Dienst und Endpunkte deaktivieren. Dies kann mithilfe der Azure CLI erfolgen: ```az network vnet subnet update --vnet-name <vnetname> -n <subnetname> --resouce-group <resourcegroup> --disable-private-endpoint-network-policies --disable-private-link-service-network-policies```

> [!IMPORTANT]
> Dabei ordnet Azure Batch pro 100 dedizierten Knoten oder Knoten mit niedriger Priorität jeweils einen Private Link-Dienst und einen Lastenausgleich zu. Diese Ressourcen werden durch die [Ressourcenkontingente](../azure-resource-manager/management/azure-subscription-service-limits.md) des Abonnements beschränkt. Bei umfangreichen Pools muss ggf. für eine oder mehrere der Ressourcen [eine Kontingenterhöhung angefordert](batch-quota-limit.md#increase-a-quota) werden. Darüber hinaus dürfen keine Ressourcensperren auf von Azure Batch erstellte Ressourcen angewendet werden. Ansonsten wird möglicherweise die Bereinigung von Ressourcen infolge der vom Benutzer ausgelösten Aktionen (etwa Löschen eines Pools oder Verkleinern auf 0) verhindert.

## <a name="current-limitations"></a>Aktuelle Einschränkungen

1. Pools ohne öffentliche IP-Adressen müssen die Konfiguration virtueller Computer und nicht die Konfiguration von Clouddiensten verwenden.
1. Eine [benutzerdefinierte Endpunktkonfiguration](pool-endpoint-configuration.md) auf Computeknoten von Azure Batch funktioniert nicht mit Pools ohne öffentliche IP-Adressen.
1. Da es keine öffentlichen IP-Adressen gibt, können Sie [Ihre eigenen angegebenen öffentlichen IP-Adressen](create-pool-public-ip.md) bei dieser Art von Pool nicht verwenden.

## <a name="create-a-pool-without-public-ip-addresses-in-the-azure-portal"></a>Erstellen eines Pools ohne öffentliche IP-Adressen im Azure-Portal

1. Navigieren Sie im Azure-Portal zu Ihrem Batch-Konto.
1. Wählen Sie links im Fenster **Einstellungen** die Option **Pools** aus.
1. Wählen Sie im Fenster **Pools** die Option **Hinzufügen** aus.
1. Wählen Sie im Fenster **Pool hinzufügen** die Option, die Sie verwenden möchten, in der Dropdownliste **Imagetyp** aus.
1. Wählen Sie für Ihr Image die richtige Angabe für **Herausgeber/Angebot/SKU**.
1. Geben Sie die übrigen erforderlichen Einstellungen, einschließlich **Knotengröße**, **Ziel für dedizierte Knoten** und **Knoten mit niedriger Priorität**, sowie alle gewünschten optionalen Einstellungen an.
1. Wählen Sie optional ein virtuelles Netzwerk und Subnetz aus, das Sie verwenden möchten. Dieses virtuelle Netzwerk muss sich in derselben Ressourcengruppe befinden wie der Pool, den Sie anlegen.
1. Wählen Sie unter **Typ der IP-Adressbereitstellung** die Option **NoPublicIPAddresses** aus.

![Screenshot des Bildschirms „Pool hinzufügen“ mit Auswahl von NoPublicIPAddresses.](./media/batch-pool-no-public-ip-address/create-pool-without-public-ip-address.png)

## <a name="use-the-batch-rest-api-to-create-a-pool-without-public-ip-addresses"></a>Erstellen eines Pools ohne öffentliche IP-Adressen mit der REST-API von Azure Batch

Das folgende Beispiel zeigt, wie die [REST-API von Azure Batch](/rest/api/batchservice/pool/add) eingesetzt wird, um einen Pool zu erstellen, der öffentliche IP-Adressen verwendet.

### <a name="rest-api-uri"></a>REST-API-URI

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

### <a name="request-body"></a>Anforderungstext

```json
"pool": {
     "id": "pool2",
     "vmSize": "standard_a1",
     "virtualMachineConfiguration": {
          "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.040-LTS"
          },
     "nodeAgentSKUId": "batch.node.ubuntu 16.04"
     }
     "networkConfiguration": {
          "subnetId": "/subscriptions/<your_subscription_id>/resourceGroups/<your_resource_group>/providers/Microsoft.Network/virtualNetworks/<your_vnet_name>/subnets/<your_subnet_name>",
          "publicIPAddressConfiguration": {
               "provision": "NoPublicIPAddresses"
          }
     },
     "resizeTimeout": "PT15M",
     "targetDedicatedNodes": 5,
     "targetLowPriorityNodes": 0,
     "taskSlotsPerNode": 3,
     "taskSchedulingPolicy": {
          "nodeFillType": "spread"
     },
     "enableAutoScale": false,
     "enableInterNodeCommunication": true,
     "metadata": [
    {
      "name": "myproperty",
      "value": "myvalue"
    }
       ]
}
```

## <a name="outbound-access-to-the-internet"></a>Ausgehender Zugriff auf das Internet

In einem Pool ohne öffentliche IP-Adressen können Ihre virtuellen Computer nicht auf das öffentliche Internet zugreifen, es sei denn, Sie konfigurieren Ihre Netzwerkeinrichtung entsprechend, z. B. mit [NAT für virtuelle Netzwerke](../virtual-network/nat-overview.md). Beachten Sie, dass NAT den virtuellen Computern im virtuellen Netzwerk nur ausgehenden Zugriff auf das Internet erlaubt. In Azure Batch erstellte Computeknoten sind nicht öffentlich zugänglich, da ihnen keine öffentlichen IP-Adressen zugeordnet sind.

Eine weitere Möglichkeit, ausgehende Konnektivität bereitzustellen, bietet eine benutzerdefinierte Route. Damit können Sie Datenverkehr zu einem Proxycomputer leiten, der Zugriff auf das öffentliche Internet hat.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Erstellen von Pools in einem virtuellen Netzwerk](batch-virtual-network.md).
- Erfahren Sie mehr zum [Verwenden privater Endpunkte mit Batch-Konten](private-connectivity.md).
