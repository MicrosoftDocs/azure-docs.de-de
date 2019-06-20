---
title: Erstellen einer Azure Load Balancer-Regel für einen Cluster
description: Konfigurieren Sie eine Azure Load Balancer-Instanz zum Öffnen von Ports für Ihren Azure Service Fabric-Cluster.
services: service-fabric
documentationcenter: na
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: aljo
ms.openlocfilehash: d95d2802398a61b948ff6c59fb3eab0e1ddddbc5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66147462"
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Öffnen von Port für einen Service Fabric-Cluster

Der mit Ihren Azure Service Fabric-Cluster bereitgestellte Lastenausgleich leitet Datenverkehr an Ihre auf einem Knoten ausgeführte App weiter. Wenn Sie Ihre Anwendung für die Verwendung eines anderen Ports ändern, müssen Sie diesen Port in Azure Load Balancer verfügbar machen (oder einen anderen Port weiterleiten).

Als Sie Ihren Service Fabric-Cluster in Azure bereitgestellt haben, wurde automatisch ein Lastenausgleich erstellt. Wenn Sie über keinen Lastenausgleich verfügen, finden Sie weitere Informationen unter [Konfigurieren eines internetseitigen Lastenausgleichs](../load-balancer/load-balancer-get-started-internet-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-service-fabric"></a>Konfigurieren von Service Fabric

Die Konfigurationsdatei für die Service Fabric-Anwendung **ServiceManifest.xml** definiert die Endpunkte, die Ihre Anwendung erwartet. Nachdem die Konfigurationsdatei mit der Definition eines Endpunkts aktualisiert wurde, muss der Lastenausgleich aktualisiert werden, um diesen Port (oder einen anderen) verfügbar zu machen. Weitere Informationen zum Erstellen des Service Fabric-Endpunkts finden Sie unter [Einrichten eines Endpunkts](service-fabric-service-manifest-resources.md).

## <a name="create-a-load-balancer-rule"></a>Erstellen einer Load Balancer-Regel

Eine Lastenausgleichsregel öffnet einen internetseitigen Port und leitet Datenverkehr an den von Ihrer Anwendung verwendeten Port des internen Knotens weiter. Wenn Sie über keinen Lastenausgleich verfügen, finden Sie weitere Informationen unter [Konfigurieren eines internetseitigen Lastenausgleichs](../load-balancer/load-balancer-get-started-internet-portal.md).

Um eine Lastenausgleichsregel zu erstellen, müssen Sie die folgenden Informationen sammeln:

- Name des Lastenausgleichs
- Ressourcengruppe des Lastenausgleichs und Service Fabric-Cluster
- Externer Port
- Interner Port

## <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle
Es ist nur ein einziger Befehl erforderlich, um mit der **Azure-Befehlszeilenschnittstelle** eine Lastenausgleichsregel zu erstellen. Sie müssen lediglich den Namen des Lastenausgleichs und die Ressourcengruppe kennen, um eine neue Regel zu erstellen.

>[!NOTE]
>Wenn Sie den Namen des Lastenausgleichs ermitteln müssen, verwenden Sie diesen Befehl, um schnell eine Liste aller Lastenausgleichsmodule und der zugehörigen Ressourcengruppen abzurufen.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

Der Azure CLI-Befehl verfügt über einige Parameter, die in der folgenden Tabelle beschrieben werden:

| Parameter | BESCHREIBUNG |
| --------- | ----------- |
| `--backend-port`  | Port, an dem die Service Fabric-Anwendung lauscht |
| `--frontend-port` | Port, den der Lastenausgleich für externe Verbindungen verfügbar macht |
| `-lb-name` | Name des Lastenausgleichs, der geändert werden soll |
| `-g`       | Ressourcengruppe, in der sich der Lastenausgleich und der Service Fabric-Cluster befinden |
| `-n`       | Gewünschter Name der Regel |


>[!NOTE]
>Weitere Informationen zum Erstellen eines Lastenausgleichs mit der Azure-Befehlszeilenschnittstelle finden Sie unter [Erstellen eines Lastenausgleichs mit der Azure-Befehlszeilenschnittstelle](../load-balancer/load-balancer-get-started-internet-arm-cli.md).

## <a name="powershell"></a>PowerShell

PowerShell ist etwas komplizierter als die Azure-Befehlszeilenschnittstelle. Führen Sie diese grundlegenden Schritte zum Erstellen einer Regel aus:

1. Rufen Sie den Lastenausgleich von Azure ab.
2. Erstellen Sie eine Regel.
3. Fügen Sie dem Lastenausgleich eine neue Regel hinzu.
4. Aktualisieren Sie den Lastenausgleich.

>[!NOTE]
>Wenn Sie den Namen des Lastenausgleichs ermitteln müssen, verwenden Sie diesen Befehl, um schnell eine Liste aller Lastenausgleichsmodule und der zugehörigen Ressourcengruppen abzurufen.
>
>`Get-AzLoadBalancer | Select Name, ResourceGroupName`

```powershell
# Get the load balancer
$lb = Get-AzLoadBalancer -Name LB-svcfab3 -ResourceGroupName svcfab_cli

# Create the rule based on information from the load balancer.
$lbrule = New-AzLoadBalancerRuleConfig -Name my-app-rule7 -Protocol Tcp -FrontendPort 39990 -BackendPort 40009 `
                                            -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
                                            -BackendAddressPool  $lb.BackendAddressPools[0] `
                                            -Probe $lb.Probes[0]

# Add the rule to the load balancer
$lb.LoadBalancingRules.Add($lbrule)

# Update the load balancer on Azure
$lb | Set-AzLoadBalancer
```

Im `New-AzLoadBalancerRuleConfig`-Befehl gibt `-FrontendPort` den Port an, den der Lastenausgleich für externe Verbindungen verfügbar macht, und `-BackendPort` gibt den Port an, an dem die Service Fabric-App lauscht.

>[!NOTE]
>Weitere Informationen zum Erstellen eines Lastenausgleichs mit PowerShell finden Sie unter [Erstellen eines Lastenausgleichs mit PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu [Netzwerkmustern für Service Fabric](service-fabric-patterns-networking.md).rvice-fabric-patterns-networking.md).