---
title: Includedatei
description: Includedatei
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: eb2555cdc163ee0f88149248b9e7d83a51d34a1b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150264"
---
1. Wählen Sie im [Azure-Portal](http://portal.azure.com) das virtuelle Resource Manager-Netzwerk aus, für das Sie ein virtuelles Netzwerkgateway erstellen möchten.

2. Klicken Sie auf der Seite Ihres virtuellen Netzwerks im Abschnitt **Einstellungen** auf **Subnetze**, um die Seite **Subnetze** zu erweitern.

3. Klicken Sie auf der Seite **Subnetze** auf **Gatewaysubnetz**, um die Seite **Subnetz hinzufügen** zu öffnen.

   ![Hinzufügen des Gatewaysubnetzes](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "Hinzufügen des Gatewaysubnetzes")

4. Als **Name** für Ihr Subnetz wird automatisch der Wert *GatewaySubnet* eingefügt. Dieser Wert ist erforderlich, damit Azure das Subnetz als Gatewaysubnetz erkennt. Passen Sie die automatisch ausgefüllten Werte für **Adressbereich** gemäß Ihren Konfigurationsanforderungen an, und wählen Sie dann **OK** aus, um das Subnetz zu erstellen.

   ![Hinzufügen des Subnetzes](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "Hinzufügen des Subnetzes")