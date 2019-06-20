---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/09/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 78dfd57fba6365f9c8937b30b5cf96b840749c68
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177961"
---
| **Hersteller** | **Gerätefamilie** | **Firmwareversion** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (Vorschau)|
|Cisco | ASA | ASA ( * ) RouteBased (IKEv2 – ohne BGP) für ASA unter 9.8 |
|Cisco | ASA | ASA RouteBased (IKEv2 – ohne BGP) für ASA 9.8 und höher |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Juniper | SRX | JunOS 12.x RouteBased BGP |
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased VTI|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased BGP|

> [!NOTE]
> ( * ) Erforderlich: NarrowAzureTrafficSelectors (Option „UsePolicyBasedTrafficSelectors“ aktivieren) und CustomAzurePolicies (IKE/IPsec)
>
