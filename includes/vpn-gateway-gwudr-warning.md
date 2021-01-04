---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: cf9d4c3fd96df83361e7d9aa89ba702d37265ec6
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95558047"
---
Benutzerdefinierte Routen mit einem 0.0.0.0/0-Ziel und NSGs im Gatewaysubnetz **werden nicht unterstützt**. Die Erstellung von Gateways mit dieser Konfiguration wird blockiert. Gateways benötigen für die ordnungsgemäße Funktion Zugriff auf die Verwaltungscontroller. [BGP-Routenverteilung](../articles/virtual-network/virtual-networks-udr-overview.md#border-gateway-protocol) sollte für das Gatewaysubnetz auf „Aktiviert“ festgelegt werden, um die Verfügbarkeit des Gateways zu gewährleisten. Ist diese Option deaktiviert, funktioniert das Gateway nicht.