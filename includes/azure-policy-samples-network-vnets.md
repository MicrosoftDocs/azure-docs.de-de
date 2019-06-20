---
title: include file
description: include file
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: f93e22012a4855257f5372c1fc1dbc05ad29a6cd
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178238"
---
### <a name="virtual-networks"></a>Virtuelle Netzwerke

|  |  |
|---------|---------|
| [Zulässige Application Gateway-SKUs](../articles/governance/policy/samples/allowed-app-gateway-sku.md) | Verlangt, dass Application Gateways eine zulässige SKU verwenden. Sie geben ein Array von zulässigen SKUs an. |
| [Zulässige VNET-Gateway-SKUs](../articles/governance/policy/samples/allowed-vnet-gateway-sku.md) | Erfordert, dass Gateways für virtuelle Netzwerke eine zulässige SKU verwenden. Sie geben ein Array von zulässigen SKUs an. |
| [Zulässige Load Balancer-SKUs](../articles/governance/policy/samples/allowed-load-balancer-skus.md) | Erfordert, dass Lastenausgleichsmodule für virtuelle Netzwerke eine zulässige SKU verwenden. Sie geben ein Array von zulässigen SKUs an. |
| [Kein Netzwerkpeering zu einem ExpressRoute-Netzwerk](../articles/governance/policy/samples/no-peering-express-route-network.md) | Verhindert, dass ein Netzwerkpeering einem Netzwerk in einer angegebenen Ressourcengruppe zugeordnet wird. Verwenden Sie diese Richtlinie, um eine Verbindung zu zentraler verwalteter Netzwerkinfrastruktur zu vermeiden. Sie geben den Namen der Ressourcengruppe an, um eine Zuordnung zu verhindern. |
| [Keine benutzerdefinierten Routentabellen](../articles/governance/policy/samples/no-user-defined-route-table.md)  | Verhindert, dass virtuelle Netzwerke mit einer benutzerdefinierten Routentabelle bereitgestellt werden. |
| [Netzwerksicherheitsgruppe X auf jedem Subnetz](../articles/governance/policy/samples/nsg-on-subnet.md) | Verlangt, dass eine bestimmte Netzwerksicherheitsgruppe für jedes virtuelle Subnetz verwendet wird. Sie geben die ID der zu verwendenden Netzwerksicherheitsgruppe an. |
| [Verwenden eines zulässigen Subnetzes für VM-Netzwerkschnittstellen](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | Verlangt, dass Netzwerkschnittstellen ein zulässiges Subnetz verwenden. Sie geben die ID des zulässigen Subnetzes an. |
| [Verwenden eines zulässigen virtuellen Netzwerks für VM-Netzwerkschnittstellen](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | Verlangt, dass Netzwerkschnittstellen ein zulässiges virtuelles Netzwerk verwenden. Sie geben die ID des zulässigen virtuellen Netzwerks an. |