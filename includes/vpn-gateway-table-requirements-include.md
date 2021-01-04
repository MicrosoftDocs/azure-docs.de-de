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
ms.openlocfilehash: dc018b5d09c9b33c10cd2d54ac6572537e05ed25
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010794"
---
In der folgenden Tabelle sind die Anforderungen für PolicyBased- und RouteBased-VPN-Gateways aufgeführt. Diese Tabelle betrifft sowohl das Resource Manager-Bereitstellungsmodell als auch das klassische Bereitstellungsmodell. Im klassischen Modell sind PolicyBased-VPN-Gateways das Gleiche wie statische Gateways, und RouteBased-Gateways entsprechen dynamischen Gateways.

|  | **PolicyBased-VPN-Gateway, Basic** | **RouteBased-VPN-Gateway, Basic** | **RouteBased-VPN-Gateway, Standard** | **RouteBased-VPN-Gateway, High Performance** |
| --- | --- | --- | --- | --- |
| **Site-to-Site-Konnektivität (S2S)** |PolicyBased-VPN-Konfiguration |RouteBased-VPN-Konfiguration |RouteBased-VPN-Konfiguration |RouteBased-VPN-Konfiguration |
| **Punkt-zu-Standort-Konnektivität (P2S)** |Nicht unterstützt |Unterstützt (gemeinsame Verwendung mit S2S möglich) |Unterstützt (gemeinsame Verwendung mit S2S möglich) |Unterstützt (gemeinsame Verwendung mit S2S möglich) |
| **Authentifizierungsmethode** |Vorinstallierter Schlüssel |Vorinstallierter Schlüssel für S2S-Konnektivität, Zertifikate für P2S-Konnektivität |Vorinstallierter Schlüssel für S2S-Konnektivität, Zertifikate für P2S-Konnektivität |Vorinstallierter Schlüssel für S2S-Konnektivität, Zertifikate für P2S-Konnektivität |
| **Maximale Anzahl von S2S-Verbindungen** |1 |10 |10 |30 |
| **Maximale Anzahl von P2S-Verbindungen** |Nicht unterstützt |128 |128 |128 |
| **Aktive Routingunterstützung (BGP)** (*) |Nicht unterstützt |Nicht unterstützt |Unterstützt |Unterstützt |

  (*) Für das klassische Bereitstellungsmodell wird BGP nicht unterstützt.
