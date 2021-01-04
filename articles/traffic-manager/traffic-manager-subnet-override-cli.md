---
title: Außerkraftsetzung für Azure Traffic Manager-Subnetz mithilfe der Azure-Befehlszeilenschnittstelle | Microsoft-Dokumentation
description: Dieser Artikel wird Ihnen helfen zu verstehen, wie die Außerkraftsetzung des Traffic Manager-Subnetzes verwendet werden kann, um die Routingmethode eines Traffic Manager-Profils außer Kraft zu setzen, um den Datenverkehr basierend auf der Endbenutzer-IP-Adresse über vordefinierte IP-Bereiche auf Endpunktzuordnungen zu leiten.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 984cdfb3e60717f7632ea1fdcd05a4681dd6a1fd
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517936"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Außerkraftsetzung für Traffic Manager-Subnetz mithilfe der Azure-Befehlszeilenschnittstelle

Die Außerkraftsetzung des Traffic Manager-Subnetzes ermöglicht es Ihnen, die Routingmethode eines Profils zu ändern.  Das Hinzufügen einer Außerkraftsetzung leitet den Datenverkehr basierend auf der IP-Adresse des Endbenutzers mit einem vordefinierten IP-Bereich an die Endpunktzuordnung. 

## <a name="how-subnet-override-works"></a>Funktionsweise der Außerkraftsetzung von Subnetzen

Wenn Außerkraftsetzungen von Subnetzen zu einem Traffic-Manager-Profil hinzugefügt werden, überprüft Traffic Manager zunächst, ob eine Außerkraftsetzung des Subnetzes für die IP-Adresse des Endbenutzers vorliegt. Wenn diese gefunden wird, erfolgt eine Weiterleitung der DNS-Abfrage des Benutzers an den entsprechenden Endpunkt.  Wenn keine Zuordnung gefunden wird, greift Traffic Manager auf die ursprüngliche Routingmethode des Profils zurück. 

Die IP-Adressbereiche können entweder als CIDR-Bereiche (z. B. 1.2.3.0/24) oder als Adressbereiche (z. B. 1.2.3.4-5.6.7.8) angegeben werden. Die jedem Endpunkt zugeordneten IP-Bereiche müssen für diesen Endpunkt eindeutig sein. Jede Überlappung von IP-Bereichen zwischen verschiedenen Endpunkten führt dazu, dass das Profil von Traffic Manager abgelehnt wird.

Es gibt zwei Arten von Routingprofilen, die eine Außerkraftsetzung von Subnetzen unterstützen:

* **Geografisch**: Wenn Traffic Manager eine Außerkraftsetzung des Subnetzes für die IP-Adresse der DNS-Abfrage findet, wird die Abfrage unabhängig von seiner Integrität an den Endpunkt weitergeleitet.
* **Leistung**: Wenn Traffic Manager eine Außerkraftsetzung des Subnetzes für die IP-Adresse der DNS-Abfrage findet, wird der Datenverkehr nur dann an den Endpunkt weitergeleitet, wenn er fehlerfrei ist.  Traffic Manager greift auf die Heuristik des Leistungsroutings zurück, wenn der Endpunkt für die Außerkraftsetzung des Subnetzes nicht fehlerfrei ist.

## <a name="create-a-traffic-manager-subnet-override"></a>Erstellen einer Außerkraftsetzung für ein Traffic Manager-Subnetz

Sie können die Azure-Befehlszeilenschnittstelle verwenden, um die Subnetze für die Außerkraftsetzung zum Traffic Manager-Endpunkt hinzuzufügen, um eine Außerkraftsetzung des Traffic Manager-Subnetzes zu erstellen.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.28 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Aktualisieren Sie den Traffic Manager-Endpunkt mit der Außerkraftsetzung des Subnetzes.
Verwenden Sie die Azure-Befehlszeilenschnittstelle, um Ihren Endpunkt mit [az network traffic-manager endpoint update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update) zu aktualisieren.

```azurecli-interactive
### Add a range of IPs ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 1.2.3.4-5.6.7.8 \
    --type AzureEndpoints

### Add a subnet ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 9.10.11.0:24 \
    --type AzureEndpoints
```

Sie können die IP-Adressbereiche entfernen, indem Sie [az network traffic-manager endpoint update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update) mit der Option **--remove** ausführen.

```azurecli-interactive
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints
```

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich ausführlicher über [Traffic Manager-Routingmethoden](traffic-manager-routing-methods.md).

Weitere Informationen zur [Routingmethode „Subnetz“ für Datenverkehr](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method)
