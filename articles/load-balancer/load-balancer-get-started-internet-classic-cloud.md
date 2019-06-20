---
title: Erstellen eines Lastenausgleichs für den Internetzugriff für Azure-Clouddienste
titlesuffix: Azure Load Balancer
description: Erfahren Sie, wie Sie für Clouddienste einen Lastenausgleich mit Internetzugriff in einem klassischen Bereitstellungsmodell erstellen.
services: load-balancer
documentationcenter: na
author: genlin
manager: cshepard
tags: azure-service-management
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: 66c978a7eb151ce9df939a11e2e3c0016c8e7c9e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60532513"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-for-cloud-services"></a>Erste Schritte zum Erstellen eines Load Balancers mit Internetzugriff für Clouddienste

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Azure-Befehlszeilenschnittstelle](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure Cloud Services](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Bevor Sie mit Azure-Ressourcen arbeiten, müssen Sie sich darüber im Klaren sein, dass Azure derzeit über zwei Bereitstellungsmodelle verfügt: Azure Resource Manager und klassische Bereitstellung. Stellen Sie sicher, dass Sie die [Bereitstellungsmodelle und -tools](../azure-classic-rm.md) verstanden haben, bevor Sie mit Azure-Ressouren arbeiten. Zum Anzeigen der Dokumentation für verschiedene Tools klicken Sie auf die Registerkarten oben in diesem Artikel. Dieser Artikel gilt für das klassische Bereitstellungsmodell. Informationen zum Erstellen eines Load Balancers mit Internetzugriff unter Verwendung des Azure-Ressourcen-Managers finden Sie [hier](load-balancer-get-started-internet-arm-ps.md).

Clouddienste werden automatisch mit einem Lastenausgleich konfiguriert und können über das Dienstmodell angepasst werden.

## <a name="create-a-load-balancer-using-the-service-definition-file"></a>Erstellen eines Load Balancers mit der Dienstdefinitionsdatei

Sie können das Azure SDK für .NET 2.5 verwenden, um Ihren Clouddienst zu aktualisieren. Endpunkteinstellungen für Clouddienste werden in der [Dienstdefinitionsdatei](https://msdn.microsoft.com/library/azure/gg557553.aspx) (.csdef) vorgenommen.

Das folgende Beispiel zeigt, wie eine "servicedefinition.csdef"-Datei für eine Cloudbereitstellung konfiguriert wird:

Wenn Sie den von einer Cloudbereitstellung generierten Codeabschnitt für die CSDEF-Datei überprüfen, sehen Sie, dass der externe Endpunkt für die Verwendung von HTTP an den Ports 10000, 10001 und 10002 konfiguriert wurde.

```xml
<ServiceDefinition name="Tenant">
    <WorkerRole name="FERole" vmsize="Small">
        <Endpoints>
            <InputEndpoint name="FE_External_Http" protocol="http" port="10000" />
            <InputEndpoint name="FE_External_Tcp"  protocol="tcp"  port="10001" />
            <InputEndpoint name="FE_External_Udp"  protocol="udp"  port="10002" />

            <InputEndpoint name="HTTP_Probe" protocol="http" port="80" loadBalancerProbe="MyProbe" />

            <InstanceInputEndpoint name="InstanceEP" protocol="tcp" localPort="80">
                <AllocatePublicPortFrom>
                    <FixedPortRange min="10110" max="10120"  />
                </AllocatePublicPortFrom>
            </InstanceInputEndpoint>
            <InternalEndpoint name="FE_InternalEP_Tcp" protocol="tcp" />
        </Endpoints>
    </WorkerRole>
</ServiceDefinition>
```

## <a name="check-load-balancer-health-status-for-cloud-services"></a>Überprüfen des Integritätsstatus des Lastenausgleichs für Clouddienste

Im Folgenden finden Sie ein Beispiel für das Erstellen eines Integritätstests:

```xml
<LoadBalancerProbes>
    <LoadBalancerProbe name="MyProbe" protocol="http" path="Probe.aspx" intervalInSeconds="5" timeoutInSeconds="100" />
</LoadBalancerProbes>
```

Der Lastenausgleich verbindet die Informationen des Endpunkts und die Informationen des Tests, um eine URL in Form von `http://{DIP of VM}:80/Probe.aspx` zur Abfrage der Integrität des Diensts zu erstellen.

Der Dienst erkennt regelmäßige Tests derselben IP-Adresse. Dies ist die Integritätstestanforderung vom Host des Knotens, auf dem der virtuelle Computer ausgeführt wird. Der Dienst muss mit einem HTTP-Statuscode 200 für den Lastenausgleich antworten, sodass davon ausgegangen wird, dass der Dienst fehlerfrei reagiert. Bei allen anderen HTTP-Statuscodes (z. B. 503) wird der virtuelle Computer direkt aus der Rotation genommen.

Die Testdefinition steuert auch die Häufigkeit des Tests. In unserem Fall testet der Lastenausgleich den Endpunkt alle 5 Sekunden. Wenn nach 10 Sekunden (zwei Testintervallen) keine positive Antwort empfangen wurde, wird von einem Ausfall ausgegangen und der virtuelle Computer aus der Rotation genommen. Dementsprechend wird der Dienst wieder in die Rotation aufgenommen, wenn eine positive Antwort empfangen wird. Wenn der Dienst zwischen fehlerfrei und fehlerhaft schwankt, kann der Lastenausgleich die erneute Einführung des Diensts in die Rotation verzögern, bis er in einer Reihe von Tests als fehlerfrei erkannt wurde.

Überprüfen Sie für weitere Informationen das Dienstdefinitionsschema für das [LoadBalancerProbe-Schema](https://msdn.microsoft.com/library/azure/jj151530.aspx) .

## <a name="next-steps"></a>Nächste Schritte

[Erste Schritte zum Konfigurieren des internen Lastenausgleichs](load-balancer-get-started-ilb-arm-ps.md)

[Konfigurieren eines Lastenausgleichs-Verteilungsmodus](load-balancer-distribution-mode.md)

[Konfigurieren von TCP-Leerlauftimeout-Einstellungen für den Lastenausgleich](load-balancer-tcp-idle-timeout.md)

