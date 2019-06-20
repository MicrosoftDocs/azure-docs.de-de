---
title: Konfigurieren von Hochverfügbarkeitsports für Azure Load Balancer
titlesuffix: Azure Load Balancer
description: Erfahren Sie, wie Sie Hochverfügbarkeitsports zum Lastenausgleich des internen Datenverkehrs an allen Ports verwenden können.
services: load-balancer
documentationcenter: na
author: rdhillon
manager: narayan
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: kumud
ms.openlocfilehash: ec43b79109181457f8ef8e214e296969db5dcb26
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66122347"
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Konfigurieren von Hochverfügbarkeitsports für internen Lastenausgleich

Dieser Artikel umfasst eine Beispielbereitstellung von Hochverfügbarkeitsports für einen internen Lastenausgleich. Weitere Informationen zu Konfigurationen, die speziell für virtuelle Netzwerkgeräte (Network Virtual Appliances, NVAs) gelten, finden Sie auf den Websites der entsprechenden Anbieter.

>[!NOTE]
>Azure Load Balancer unterstützt zwei unterschiedliche Typen: Basic und Standard. In diesem Artikel wird der Load Balancer vom Typ „Standard“ beschrieben. Weitere Informationen zum Load Balancer vom Typ „Basic“ finden Sie unter [Übersicht über Azure Load Balancer](load-balancer-overview.md).

Die Abbildung zeigt die folgende Konfiguration des in diesem Artikel beschriebenen Bereitstellungsbeispiels:

- Die virtuellen Netzwerkgeräte werden im Back-End-Pool eines internen Lastenausgleichs hinter der Konfiguration der Hochverfügbarkeitsports bereitgestellt. 
- Die auf die DMZ-Subnetz angewendete benutzerdefinierte Route (UDR) leitet den gesamten Datenverkehr an die virtuellen Netzwerkgeräte weiter, indem der nächste Hop als virtuelle IP-Adresse des internen Lastenausgleichsmoduls durchgeführt wird. 
- Der interne Lastenausgleich verteilt den Datenverkehr entsprechend dem Lastenausgleichsalgorithmus auf eines der aktiven virtuellen Netzwerkgeräte.
- Das virtuelle Netzwerkgerät verarbeitet den Datenverkehr und leitet ihn an das ursprüngliche Ziel im Back-End-Subnetz weiter.
- Der Rückgabepfad kann die gleiche Route nehmen, wenn eine entsprechende UDR im Back-End-Subnetz konfiguriert ist. 

![Beispielbereitstellung für Hochverfügbarkeitsports](./media/load-balancer-configure-ha-ports/haports.png)

## <a name="configure-high-availability-ports"></a>Konfigurieren von Hochverfügbarkeitsports

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Richten Sie zur Konfiguration von Hochverfügbarkeitsports einen internen Lastenausgleich mit den virtuellen Netzwerkgeräten im Back-End-Pool ein. Richten Sie eine entsprechende Konfiguration des Integritätstests des Lastenausgleichs ein, um die Integrität der virtuellen Netzwerkgeräte und die Lastenausgleichsregel mit Hochverfügbarkeitsports zu ermitteln. Die auf den allgemeinen Lastenausgleich bezogene Konfiguration ist im Artikel [Erstellen eines internen Lastenausgleichs über das Azure-Portal](load-balancer-get-started-ilb-arm-portal.md) abgedeckt. Dieser Artikel hebt die Konfiguration von Hochverfügbarkeitsports hervor.

Die Konfiguration umfasst im Wesentlichen das Festlegen des Werts des Front-End- und Back-End-Ports auf **0**. Legen Sie als Protokollwert **Alle** fest. In diesem Artikel wird beschrieben, wie Sie Hochverfügbarkeitports mit dem Azure-Portal, mit PowerShell und der Azure CLI konfigurieren.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Konfigurieren einer Lastenausgleichsregel für Hochverfügbarkeitsports mithilfe des Azure-Portals

Aktivieren Sie zum Konfigurieren von Hochverfügbarkeitsports mithilfe des Azure-Portals das Kontrollkästchen **HA-Ports**. Wenn diese ausgewählt ist, wird die entsprechende Port- und Protokollkonfiguration automatisch ausgefüllt. 

![Konfiguration von Hochverfügbarkeitsports über das Azure-Portal](./media/load-balancer-configure-ha-ports/haports-portal.png)

### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Konfigurieren einer Lastenausgleichsregel für Hochverfügbarkeitsports mithilfe der Resource Manager-Vorlage

Sie können die Hochverfügbarkeitsports mit der API-Version 2017-08-01 für Microsoft.Network/loadBalancers in der Load Balancer-Ressource konfigurieren. Der folgende JSON-Codeausschnitt zeigt die Änderungen an der Konfiguration für das Lastenausgleichsmodul für Hochverfügbarkeitsports über die REST-API:

```json
    {
        "apiVersion": "2017-08-01",
        "type": "Microsoft.Network/loadBalancers",
        ...
        "sku":
        {
            "name": "Standard"
        },
        ...
        "properties": {
            "frontendIpConfigurations": [...],
            "backendAddressPools": [...],
            "probes": [...],
            "loadBalancingRules": [
             {
                "properties": {
                    ...
                    "protocol": "All",
                    "frontendPort": 0,
                    "backendPort": 0
                }
             }
            ],
       ...
       }
    }
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Konfigurieren einer Lastenausgleichsregel für Hochverfügbarkeitsports mithilfe von PowerShell

Verwenden Sie den folgenden Befehl, um die Lastenausgleichsregel für Hochverfügbarkeitsports während der Erstellung des internen Lastenausgleichs mithilfe von PowerShell zu erstellen:

```powershell
lbrule = New-AzLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli"></a>Konfigurieren einer Lastenausgleichsregel für Hochverfügbarkeitsports mithilfe der Azure CLI

Verwenden Sie in Schritt 4 von [Erstellen eines internen Lastenausgleichs mithilfe der Azure-Befehlszeilenschnittstelle](load-balancer-get-started-ilb-arm-cli.md) den folgenden Befehl, um die Lastenausgleichsregel für Hochverfügbarkeitsports zu erstellen:

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über [Hochverfügbarkeitsports](load-balancer-ha-ports-overview.md).