---
title: Konfigurieren von Azure Analysis Services für VNET-Datenquellen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Azure Analysis Services-Server so konfigurieren, dass er ein Gateway für Datenquellen im Azure Virtual Network (VNET) verwendet.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c3a0ed0f2c9a52d10d206075d229cd62cd3909db
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2021
ms.locfileid: "108145751"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Verwenden eines Gateways für Datenquellen in einer Azure Virtual Network-Instanz (VNET)

In diesem Artikel wird die Servereigenschaft **AlwaysUseGateway** von Azure Analysis Services beschrieben, die verwendet werden soll, wenn sich Datenquellen in einer [Azure Virtual Network-Instanz (VNET)](../virtual-network/virtual-networks-overview.md) befinden.

## <a name="server-access-to-vnet-data-sources"></a>Serverzugriff auf VNET-Datenquellen

Beim Zugriff auf Ihre Datenquellen über ein VNET muss Ihr Azure Analysis Services-Server mit diesen Datenquellen in Ihrer eigenen Umgebung eine Verbindung herstellen, als ob sie lokal wären. Sie können die Servereigenschaft **AlwaysUseGateway** so konfigurieren, dass der Server angewiesen wird, auf alle Datenquellen über ein [lokales Gateway](analysis-services-gateway.md) zuzugreifen. 

Azure SQL Managed Instance-Datenquellen werden in einem Azure-VNET mit einer privaten IP-Adresse ausgeführt. Wenn für die-Instanz der öffentliche Endpunkt aktiviert ist, ist kein Gateway erforderlich. Wenn der öffentliche Endpunkt nicht aktiviert ist, ist ein lokales Datengateway erforderlich, und die AlwaysUseGateway-Eigenschaft muss auf TRUE festgelegt werden.

> [!NOTE]
> Diese Eigenschaft ist nur wirksam, wenn ein [lokales Datengateway](analysis-services-gateway.md) installiert und konfiguriert ist. Das Gateway kann sich im VNET befinden.

## <a name="configure-alwaysusegateway-property"></a>Konfigurieren der AlwaysUseGateway-Eigenschaft

1. Klicken Sie unter „SSMS“ > „Server“ > **Eigenschaften** > **Allgemein** auf **Show Advanced (All) Properties** (Erweiterte (alle Eigenschaften) anzeigen).
2. Legen Sie **ASPaaS\AlwaysUseGateway** auf **true** fest.

    ![AlwaysUseGateway-Eigenschaft](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Weitere Informationen
[Herstellen einer Verbindung mit lokalen Datenquellen mit dem lokalen Azure-Datengateway](analysis-services-gateway.md)   
[Installieren und Konfigurieren eines lokalen Datengateways](analysis-services-gateway-install.md)   
[Was ist Azure Virtual Network?](../virtual-network/virtual-networks-overview.md)   

