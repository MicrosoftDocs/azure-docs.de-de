---
title: Konfiguration der unverankerten IP von Azure Load Balancer
description: Übersicht über die unverankerte IP von Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: 01cca2f2233ed5cdfb3003bb44c40f481bcf9bda
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94699405"
---
# <a name="azure-load-balancer-floating-ip-configuration"></a>Konfiguration der unverankerten IP von Azure Load Balancer

Load Balancer verfügt über mehrere Funktionen für UDP- und TCP-Anwendungen.

## <a name="floating-ip"></a>Unverankerte IP

In einigen Anwendungsszenarien ist es eine Priorität bzw. Anforderung, den gleichen Port für mehrere Anwendungsinstanzen auf einer einzelnen VM im Back-End-Pool zu verwenden. Gängige Beispiele für die Portwiederverwendung sind das Clustering für Hochverfügbarkeit, virtuelle Netzwerkgeräte und die Bereitstellung mehrerer TLS-Endpunkte ohne erneute Verschlüsselung. Wenn Sie den Back-End-Port in mehreren Regeln wiederverwenden möchten, müssen Sie in der Regeldefinition Floating IP aktivieren.

**Floating IP** ist die in Azure verwendete Benennung für die Komponente „Direct Server Return (DSR)“. DSR besteht aus zwei Teilen:

- Datenflusstopologie
- Zuordnungsschema für IP-Adressen

Auf Plattformebene wird Azure Load Balancer immer in einer DSR-Datenflusstopologie betrieben, unabhängig davon, ob Floating IP aktiviert ist. Dies bedeutet, dass der ausgehende Teil eines Datenflusses immer ordnungsgemäß so umgeschrieben wird, dass er direkt wieder an den Ursprung übermittelt wird.
Ohne Floating IP wird von Azure ein herkömmliches Zuordnungsschema für IP-Adressen für den Lastenausgleich (IP-Adresse für VM-Instanzen) verfügbar gemacht, um die Nutzung zu vereinfachen. Durch die Aktivierung von Floating IP wird die Zuordnung der IP-Adressen in die Front-End-IP-Adresse des Lastenausgleichsmoduls geändert, um für zusätzliche Flexibilität zu sorgen. [Hier](load-balancer-multivip-overview.md)erhalten Sie weitere Informationen.

## <a name="limitations"></a><a name = "limitations"></a>Einschränkungen

- Unverankerte IP wird für sekundäre IP-Konfigurationen in Szenarien mit Lastenausgleich derzeit nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu den ersten Schritten mit einer Load Balancer-Instanz finden Sie unter [Schnellstart: Erstellen einer Load Balancer Standard-Instanz für den Lastenausgleich virtueller Computer im Azure-Portal](quickstart-load-balancer-standard-public-portal.md).
- Informieren Sie sich über [Azure Load Balancer – Ausgehende Verbindungen](load-balancer-outbound-connections.md).
- Weitere Informationen zu [Azure Load Balancer](load-balancer-overview.md).
- Informationen zu [Integritätstests](load-balancer-custom-probe-overview.md)
- Weitere Informationen zu [Diagnosen für Standard Load Balancer](load-balancer-standard-diagnostics.md).
- Weitere Informationen zu [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md).