---
title: Azure PowerShell-Skriptbeispiel – Öffnen eines Anwendungsports im Lastenausgleich | Microsoft-Dokumentation
description: Azure PowerShell-Skriptbeispiel – Öffnen eines Ports im Azure-Lastenausgleichsmodul für eine Service Fabric-Anwendung.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 05/18/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 942dd995651f4c672d50163e5c380b62155b2227
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66161939"
---
# <a name="open-an-application-port-in-the-azure-load-balancer"></a>Öffnen eines Anwendungsports im Azure-Lastenausgleichsmodul

Eine in Azure ausgeführte Service Fabric-Anwendung befindet sich hinter dem Azure-Lastenausgleichsmodul. Mit diesem Beispielskript wird ein Port in einem Azure-Lastenausgleichsmodul geöffnet, um die Kommunikation einer Service Fabric-Anwendung mit externen Clients zu ermöglichen. Passen Sie die Parameter nach Bedarf an. Wenn sich Ihr Cluster in einer Netzwerksicherheitsgruppe befindet, fügen Sie auch [eine Regel für eingehende Netzwerksicherheitsgruppen](service-fabric-powershell-add-nsg-rule.md) hinzu, um eingehenden Datenverkehr zuzulassen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Wenn Sie das Service Fabric-PowerShell-Modul benötigen, installieren Sie es zusammen mit dem [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Beispielskript

[!code-powershell[main](../../../powershell_scripts/service-fabric/open-port-in-load-balancer/open-port-in-load-balancer.ps1 "Open a port in the load balancer")]

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Ruft eine Azure-Ressource ab.  |
| [Get-AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer) | Ruft das Azure-Lastenausgleichsmodul ab. |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/add-azloadbalancerprobeconfig) | Fügt eine Testkonfiguration zu einem Lastenausgleichsmodul hinzu.|
| [Get-AzLoadBalancerProbeConfig](/powershell/module/az.network/get-azloadbalancerprobeconfig) | Erstellt eine Testkonfiguration für ein Lastenausgleichsmodul. |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/add-azloadbalancerruleconfig) | Fügt eine Regelkonfiguration zu einem Lastenausgleichsmodul hinzu. |
| [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer) | Legt den Zielzustand für ein Lastenausgleichsmodul fest. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Azure PowerShell-Modul finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/overview).

Zusätzliche PowerShell-Beispiele für Azure Service Fabric finden Sie unter [Azure PowerShell-Beispiele](../service-fabric-powershell-samples.md).
