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
ms.openlocfilehash: b381c5ad8fd81cd9b7411e1f4679b3f5214e6de9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177932"
---
### <a name="noconnection"></a>So ändern Sie die IP-Adresspräfixe eines Gateways des lokalen Netzwerks: Keine Gatewayverbindung

Falls keine Gatewayverbindung besteht und Sie IP-Adresspräfixe hinzufügen oder entfernen möchten, verwenden Sie den gleichen Befehl wie für das Erstellen des Gateways des lokalen Netzwerks: [az network local-gateway create](https://docs.microsoft.com/cli/azure/network/local-gateway). Sie können diesen Befehl auch verwenden, um die Gateway-IP-Adresse für das VPN-Gerät zu aktualisieren. Verwenden Sie zum Überschreiben der aktuellen Einstellungen den Namen des Gateways des lokalen Netzwerks. Wenn Sie einen anderen Namen verwenden, wird nicht das bereits vorhandene Gateway des lokalen Netzwerks überschrieben, sondern ein neues erstellt.

Bei jeder Änderung muss die gesamte Liste mit Präfixen angegeben werden – nicht nur die Präfixe, die Sie ändern möchten. Geben Sie nur die Präfixe an, die Sie beibehalten möchten. Hier sind dies „10.0.0.0/24“ und „20.0.0.0/24“.

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

### <a name="withconnection"></a>So ändern Sie die IP-Adresspräfixe eines Gateways des lokalen Netzwerks: Vorhandene Gatewayverbindung

Falls eine Gatewayverbindung besteht und Sie IP-Adresspräfixe hinzufügen oder entfernen möchten, können Sie die Präfixe mit [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway) aktualisieren. Dies führt zu Ausfallzeiten bei Ihrer VPN-Verbindung. Beim Ändern von IP-Adresspräfixen müssen Sie das VPN-Gateway nicht löschen.

Bei jeder Änderung muss die gesamte Liste mit Präfixen angegeben werden – nicht nur die Präfixe, die Sie ändern möchten. In diesem Beispiel sind 10.0.0.0/24 und 20.0.0.0/24 bereits vorhanden. Wir fügen die Präfixe „30.0.0.0/24“ und „40.0.0.0/24“ hinzu, und geben alle vier Präfixe bei der Aktualisierung an.

```azurecli
az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 --name VNet1toSite2 -g TestRG1
```
