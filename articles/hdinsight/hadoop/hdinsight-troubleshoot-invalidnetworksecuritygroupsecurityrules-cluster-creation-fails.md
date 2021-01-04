---
title: InvalidNetworkSecurityGroupSecurityRules-Fehler – Azure HDInsight
description: 'Fehler bei der Clustererstellung. Fehlercode: InvalidNetworkSecurityGroupSecurityRules'
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: b1423dc965a3169a5f615ccc371849cc177be244
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289106"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>Szenario: InvalidNetworkSecurityGroupSecurityRules: Fehler bei der Clustererstellung in Azure HDInsight

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Interaktion mit Azure HDInsight-Clustern beschrieben.

## <a name="issue"></a>Problem

Sie erhalten den Fehlercode `InvalidNetworkSecurityGroupSecurityRules` mit einer Beschreibung wie: „Die Sicherheitsregeln in der Netzwerksicherheitsgruppe, die für das Subnetz konfiguriert ist, lässt die erforderliche ein- bzw. ausgehende Konnektivität nicht zu“.

## <a name="cause"></a>Ursache

Es liegt vermutlich ein Problem mit den Regeln für [Netzwerksicherheitsgruppen](../../virtual-network/virtual-network-vnet-plan-design-arm.md) für eingehenden Datenverkehr vor, die für Ihren Cluster konfiguriert wurden.

## <a name="resolution"></a>Lösung

Navigieren Sie zum Azure-Portal, und ermitteln Sie die NSG des Subnetzes, in dem der Cluster bereitgestellt wird. Stellen Sie im Abschnitt **Eingangssicherheitsregeln** sicher, dass die Regeln den Zugriff in eingehender Richtung auf Port 443 für die [hier](../control-network-traffic.md) genannten IP-Adressen zulassen.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]