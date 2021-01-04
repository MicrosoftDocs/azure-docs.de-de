---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 069d7af9cee0bee673c8f0b32659ce362fe4dd70
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027399"
---
### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>So ändern Sie die Angabe für „gatewayIpAddress“ für ein Gateway des lokalen Netzwerks

Wenn für das VPN-Gerät, mit dem Sie eine Verbindung herstellen möchten, die öffentliche IP-Adresse geändert wurde, müssen Sie das Gateway des lokalen Netzwerks entsprechend anpassen. Die Gateway-IP-Adresse kann geändert werden, ohne dass eine vorhandene VPN-Gatewayverbindung (sofern vorhanden) entfernt werden muss. Ersetzen Sie zum Ändern der Gateway-IP-Adresse die Werte „Site2“ und „TestRG1“ durch eigene Werte. Verwenden Sie dazu den Befehl [az network local-gateway update](/cli/azure/network/local-gateway).

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

Überprüfen Sie, ob die IP-Adresse in der Ausgabe korrekt ist:

```
"gatewayIpAddress": "23.99.222.170",
```