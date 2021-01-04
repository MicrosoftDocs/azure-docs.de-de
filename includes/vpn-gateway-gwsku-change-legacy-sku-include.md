---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c232e1ce183c6935d625b5bc9987a4981865ae4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010781"
---
Wenn Sie mit dem Resource Manager-Bereitstellungsmodell arbeiten, können Sie zur neuen Gateway-SKU wechseln. Wenn Sie von einer älteren Gateway-SKU zu einer neuen SKU wechseln, löschen Sie das vorhandene VPN-Gateway und erstellen ein neues VPN-Gateway.

Workflow:

1. Entfernen Sie alle Verbindungen mit dem Gateway des virtuellen Netzwerks.
2. Löschen Sie das alte VPN Gateway.
3. Erstellen Sie das neue VPN Gateway.
4. Aktualisieren Sie Ihre lokalen VPN-Geräte mit der IP-Adresse des neuen VPN Gateways (für Standort-zu-Standort-Verbindungen).
5. Aktualisieren Sie den Gateway-IP-Adresswert für alle lokalen VNet-zu-VNet-Netzwerkgateways, die eine Verbindung mit diesem Gateway herstellen.
6. Laden Sie neue Client-VPN-Konfigurationspakete für P2S-Clients herunter, die über dieses VPN Gateway eine Verbindung mit dem virtuellen Netzwerk herstellen.
7. Stellen Sie die Verbindungen mit dem Gateway des virtuellen Netzwerks erneut her.

Überlegungen:

* Für den Wechsel zu den neuen SKUs muss sich das VPN-Gateway im Resource Manager-Bereitstellungsmodell befinden.
* Wenn Sie ein klassisches VPN-Gateway haben, müssen Sie weiterhin die älteren SKUs für dieses Gateway verwenden. Sie können zwischen den Größen der älteren SKUs wechseln. Sie können nicht zu den neuen SKUs wechseln.
* Während des Wechsels von einer älteren SKU zu einer neuen SKU ist die Konnektivität unterbrochen.
* Wenn Sie zu einer neuen Gateway-SKU wechseln, ändert sich die öffentliche IP-Adresse für Ihr VPN-Gateway. Dies geschieht auch, wenn Sie das gleiche öffentliche IP-Adressobjekt angeben, das Sie bisher verwendet haben.