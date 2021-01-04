---
title: Konfigurieren des Azure Load Balancer-Verteilungsmodus
titleSuffix: Azure Load Balancer
description: In diesem Artikel werden die ersten Schritte zum Konfigurieren des Verteilungsmodus für Azure Load Balancer zur Unterstützung von Quell-IP-Affinität beschrieben.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 4e4fa9819343c90b5639fc485af8acb48968d62e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695655"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Konfigurieren des Verteilungsmodus für Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Modus der hashbasierten Verteilung

Der Standardverteilungsmodus für Azure Load Balancer ist ein Fünf-Tupel-Hash. 

Das Tupel besteht aus den folgenden Elementen:
* **Quell-IP**
* **Quellport**
* **Ziel-IP**
* **Zielport**
* **Protokolltyp**

Der Hash wird verwendet, um Datenverkehr den verfügbaren Servern zuzuordnen. Der Algorithmus stellt Bindung nur innerhalb einer Transportsitzung bereit. Pakete in der gleichen Sitzung werden an die gleiche Instanz der Rechenzentrums-IP hinter dem Endpunkt mit Lastenausgleich weitergeleitet. Wenn der Client eine neue Sitzung über die gleiche Quell-IP startet, wird der Quellport geändert, wodurch der Datenverkehr an einen anderen Rechenzentrumendpunkt weitergeleitet wird.

![Auf 5-Tupel-Hash basierender Verteilungsmodus](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Modus „Quell-IP-Affinität“

Der Lastenausgleich kann auch mithilfe des Verteilungsmodus „Quell-IP-Affinität“ konfiguriert werden. Dieser Verteilungsmodus ist auch als Sitzungsaffinität oder Client-IP-Affinität bekannt. Der Modus verwendet einen Zwei-Tupel-Hash (Quell-IP und Ziel-IP) oder Drei-Tupel-Hash (Quell-IP, Ziel-IP und Protokolltyp) zum Zuordnen des Datenverkehrs zu verfügbaren Servern. Mithilfe der Quell-IP-Affinität werden Verbindungen, die vom gleichen Clientcomputer gestartet werden, an den gleichen Rechenzentrumendpunkt geleitet.

Die folgende Abbildung veranschaulicht eine Konfiguration mit zwei Tupeln. Beachten Sie, wie der Zwei-Tupel-Hash durch den Lastenausgleich zum ersten virtuellen Computer (VM1) führt. VM1 wird dann durch VM2 und VM3 abgesichert.

![Verteilungsmodus „Zwei-Tupel-Sitzungsaffinität“](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Mit dem Modus „Quell-IP-Affinität“ wird das Problem der Inkompatibilität zwischen Azure Load Balancer und Remotedesktopgateway (RD-Gateway) gelöst. Mit diesem Modus können Sie eine RD-Gatewayfarm in einem einzelnen Clouddienst erstellen.

Ein weiterer Anwendungsfall ist das Hochladen von Medien. Der Datenupload erfolgt über UDP, während die Steuerungsebene über TCP realisiert wird:

* Ein Client startet eine TCP-Sitzung zur öffentlichen Adresse mit Lastenausgleich und wird zu einer bestimmten DIP-Adresse geleitet. Der Kanal bleibt zur Überwachung der Verbindungsintegrität aktiv.
* Es wird eine neue UDP-Sitzung vom gleichen Clientcomputer zum gleichen öffentlichen Endpunkt mit Lastenausgleich gestartet. Die Verbindung wird zum gleichen DIP-Endpunkt wie bei der vorherigen TCP-Verbindung geleitet. Das Hochladen von Medien kann mit hohem Durchsatz erfolgen, während gleichzeitig ein Steuerkanal über TCP betrieben wird.

> [!NOTE]
> Bei einer Änderung der Lastenausgleichsgruppe durch Entfernen oder Hinzufügen eines virtuellen Computers wird die Verteilung der Clientanforderungen neu berechnet. Sie können sich nicht darauf verlassen, dass neue Verbindungen von vorhandenen Clients beim gleichen Server landen. Darüber hinaus kann die Verwendung des Quell-IP-Affinitäts-Verteilungsmodus zu einer ungleichen Verteilung des Datenverkehrs führen. Clients, die hinter Proxys ausgeführt werden, können als eine einzige Clientanwendung betrachtet werden.

## <a name="configure-source-ip-affinity-settings"></a>Konfigurieren der Einstellungen für die Quell-IP-Affinität

### <a name="azure-portal"></a>Azure-Portal

Sie können die Konfiguration des Verteilungsmodus ändern, indem Sie die Lastenausgleichsregel im Portal ändern.

1. Melden Sie sich beim Azure-Portal an, und suchen Sie die Ressourcengruppe, die den Lastenausgleich enthält, den Sie ändern möchten, indem Sie auf **Ressourcengruppen** klicken.
2. Klicken Sie auf dem Bildschirm mit der Übersicht des Lastenausgleichs unter **Einstellungen** auf **Lastenausgleichsregeln**.
3. Klicken Sie auf dem Bildschirm „Lastenausgleichsregeln“ auf die Lastenausgleichsregel, für die Sie den Verteilungsmodus ändern möchten.
4. Unter der Regel wird der Verteilungsmodus geändert, indem Sie das Dropdownfeld **Sitzungspersistenz** ändern.  Die folgenden Optionen sind verfügbar:
    
    * **None (hash-based)** (Keine (Hash-basiert)): Gibt an, dass aufeinander folgende Anforderungen vom selben Client von jedem virtuellen Computer verarbeitet werden können.
    * **Client IP (source IP affinity 2-tuple)** (Client-IP (Quell-IP-Affinität 2-tupel)): Gibt an, dass aufeinanderfolgende Anforderungen von derselben Client-IP-Adresse vom selben virtuellen Computer verarbeitet werden.
    * **Client IP and protocol (source IP affinity 3-tuple)** (Client-IP und Protokoll (Quell-IP-Affinität 3-tupel)): Gibt an, dass aufeinanderfolgende Anforderungen von derselben Kombination aus Client-IP-Adresse und Protokoll vom selben virtuellen Computer verarbeitet werden.

5. Wählen Sie den Verteilungsmodus aus, und klicken Sie dann auf **Speichern**.

### <a name="azure-powershell"></a>Azure PowerShell

Verwenden Sie für mithilfe von Resource Manager bereitgestellte virtuelle Computer PowerShell, um die Verteilungseinstellungen für den Load Balancer in einer vorhandenen Lastenausgleichsregel zu ändern. Der folgende Befehl aktualisiert den Verteilungsmodus: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

Verwenden Sie für klassische virtuelle Computer Azure PowerShell, um die Verteilungseinstellungen zu ändern. Fügen Sie einem virtuellen Computer einen Azure-Endpunkt hinzu, und konfigurieren Sie den Verteilungsmodus für den Lastenausgleich:

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Legen Sie den Wert des `LoadBalancerDistribution`-Elements für den erforderlichen Umfang des Lastenausgleichs fest. Geben Sie „sourceIP“ für den Zwei-Tupel-Lastenausgleich (Quell-IP und Ziel-IP) an. Geben Sie „sourceIPProtocol“ für den Drei-Tupel-Lastenausgleich (Quell-IP, Ziel-IP und Protokolltyp) an. Geben Sie keinen Wert an, um das Standardverhalten, den Fünf-Tupel-Lastenausgleich, zu verwenden.

Abrufen einer Lastenausgleichs-Verteilungsmoduskonfiguration für einen Endpunkt mithilfe der folgenden Einstellungen:

```azurepowershell
PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
LBSetName : MyLoadBalancedSet
LocalPort : 80
Name : HTTP
Port : 80
Protocol : tcp
Vip : 65.52.xxx.xxx
ProbePath :
ProbePort : 80
ProbeProtocol : tcp
ProbeIntervalInSeconds : 15
ProbeTimeoutInSeconds : 31
EnableDirectServerReturn : False
Acl : {}
InternalLoadBalancerName :
IdleTimeoutInMinutes : 15
LoadBalancerDistribution : sourceIP
```

Wenn das Element `LoadBalancerDistribution` nicht vorhanden ist, verwendet Azure Load Balancer den standardmäßigen Fünf-Tupel-Algorithmus.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Konfigurieren des Verteilungsmodus für einen Endpunktsatz mit Lastenausgleich

Wenn Endpunkte Bestandteil eines Endpunktsatzes mit Lastenausgleich sind, muss der Verteilungsmodus für den Endpunktsatz mit Lastenausgleich konfiguriert werden:

```azurepowershell-interactive
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Konfigurieren des Verteilungsmodus für Cloud Services-Endpunkte

Verwenden Sie das Azure SDK für .NET 2.5, um Ihren Clouddienst zu aktualisieren. Die Endpunkteinstellungen für Cloud Services werden in der CSDEF-Datei vorgenommen. Um den Lastenausgleichs-Verteilungsmodus für eine Cloud Services-Bereitstellung zu aktualisieren, ist ein Upgrade der Bereitstellung erforderlich.

Nachfolgend sehen Sie ein Beispiel für in der CSDEF-Datei vorgenommene Änderungen für Endpunkteinstellungen:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
    </Endpoints>
</WorkerRole>
<NetworkConfiguration>
    <VirtualNetworkSite name="VNet"/>
    <AddressAssignments>
<InstanceAddress roleName="VMRolePersisted">
    <PublicIPs>
    <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
    </PublicIPs>
</InstanceAddress>
    </AddressAssignments>
</NetworkConfiguration>
```

## <a name="api-example"></a>API-Beispiel

Im folgenden Beispiel wird gezeigt, wie der Lastenausgleichs-Verteilungsmodus für einen bestimmten Satz mit Lastenausgleich in einer Bereitstellung neu konfiguriert wird. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Ändern des Verteilungsmodus für einen bereitgestellten Satz mit Lastenausgleich

Verwenden Sie das klassische Azure-Bereitstellungsmodell, um eine vorhandene Bereitstellungskonfiguration zu ändern. Fügen Sie den Header `x-ms-version` hinzu, und legen Sie den Wert auf Version 2014-09-01 oder höher fest.

#### <a name="request"></a>Anforderung

```http
POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
Content-Type: application/xml
```

```xml
<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
    <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
            <Port> port-assigned-to-probe </Port>
            <Protocol> probe-protocol </Protocol>
            <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
            <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
    </InputEndpoint>
</LoadBalancedEndpointList>
```

Legen Sie gemäß der vorherigen Beschreibung für den Wert des `LoadBalancerDistribution`-Elements „sourceIP“ für die Zwei-Tupel-Affinität oder „sourceIPProtocol“ für die Drei-Tupel-Affinität fest, oder geben Sie keinen Wert (keine Affinität) an (Fünf-Tupel-Affinität).

#### <a name="response"></a>Antwort

```http
HTTP/1.1 202 Accepted
Cache-Control: no-cache
Content-Length: 0
Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
x-ms-servedbyregion: ussouth2
x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
Date: Thu, 16 Oct 2014 22:49:21 GMT
```

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über Azure Load Balancer](load-balancer-overview.md)
* [Erste Schritte zum Konfigurieren eines Lastenausgleichs mit Internetzugriff](quickstart-load-balancer-standard-public-powershell.md)
* [Konfigurieren von TCP-Leerlauftimeout-Einstellungen für den Lastenausgleich](load-balancer-tcp-idle-timeout.md)