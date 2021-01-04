---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 923620c1c9719d857cc848507a4c5507f528d34e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95564010"
---
### <a name="to-view-local-network-gateways"></a>So zeigen Sie Gateways des lokalen Netzwerks an

Verwenden Sie zum Anzeigen einer Liste der Gateways des lokalen Netzwerks den Befehl [az network local-gateway list](/cli/azure/network/local-gateway).

```azurecli
az network local-gateway list --resource-group TestRG1
```

[!INCLUDE [modify-prefix](vpn-gateway-modify-ip-prefix-cli-include.md)]

[!INCLUDE [modify-gateway-IP](vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

### <a name="to-verify-the-shared-key-values"></a>So überprüfen Sie die Werte für den gemeinsam verwendeten Schlüssel

Vergewissern Sie sich, dass der Wert des gemeinsam verwendeten Schlüssels dem Wert entspricht, den Sie für die Konfiguration Ihres VPN-Geräts verwendet haben. Wenn nicht, sollten Sie entweder die Verbindung erneut ausführen, indem Sie den Wert des Geräts verwenden, oder das Gerät mit dem zurückgegebenen Wert aktualisieren. Die Werte müssen übereinstimmen. Verwenden Sie zum Anzeigen des gemeinsam verwendeten Schlüssels den Befehl [az network vpn-connection-list](/cli/azure/network/vpn-connection).

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 --resource-group TestRG1
```
### <a name="to-view-the-vpn-gateway-public-ip-address"></a>So zeigen Sie die öffentliche IP-Adresse des VPN-Gateways an

Verwenden Sie den Befehl [az network public-ip list](/cli/azure/network/public-ip), um die öffentliche IP-Adresse des Gateways des virtuellen Netzwerks zu ermitteln. Zur besseren Lesbarkeit wird die Ausgabe für dieses Beispiel formatiert, damit die Liste mit den öffentlichen IP-Adressen in Tabellenform angezeigt wird.

```azurecli
az network public-ip list --resource-group TestRG1 --output table
```