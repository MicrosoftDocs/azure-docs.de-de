---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9132cf438cab518e20e6c2ddfdb7d0928753bd19
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177938"
---
1. Navigieren Sie im Portal zum virtuellen Netzwerk, für das Sie ein Gateway des virtuellen Netzwerks erstellen möchten.
2. Klicken Sie auf der VNET-Seite im Abschnitt **Einstellungen** auf **Subnetze**, um die Seite „Subnetze“ zu erweitern.
3. Klicken Sie auf der Seite **Subnetze** oben auf **+Gatewaysubnetz**, um die Seite **Subnetz hinzufügen** zu öffnen.

   ![Hinzufügen des Gatewaysubnetzes](./media/vpn-gateway-add-gateway-subnet-portal-include/gateway-subnet.png "Hinzufügen des Gatewaysubnetzes")
  
4. Als **Name** für Ihr Subnetz wird automatisch der Wert „GatewaySubnet“ eingefügt. Der Wert „GatewaySubnet“ ist erforderlich, damit Azure das Subnetz als Gatewaysubnetz erkennt. Passen Sie die automatisch ausgefüllten Werte für **Adressbereich** an Ihre Konfigurationsanforderungen an.

   ![Hinzufügen des Gatewaysubnetzes](./media/vpn-gateway-add-gateway-subnet-portal-include/add-gateway-subnet.png "Hinzufügen des Gatewaysubnetzes")
  
5. Klicken Sie unten auf der Seite auf **OK**, um das Subnetz zu erstellen.
