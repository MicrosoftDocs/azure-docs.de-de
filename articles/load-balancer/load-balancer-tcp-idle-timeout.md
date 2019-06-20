---
title: Konfigurieren des TCP-Leerlauftimeouts in Azure für den Load Balancer
titlesuffix: Azure Load Balancer
description: Konfigurieren des TCP-Leerlauftimeouts für den Load Balancer
services: load-balancer
documentationcenter: na
author: kumudd
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 0c57eec4d739da13d98099a6b2f01fbf0ad0051c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60734598"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Konfigurieren der TCP-Leerlauftimeout-Einstellungen für Azure Load Balancer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

In der Standardkonfiguration ist der Azure Load Balancer auf ein Leerlauftimeout von 4 Minuten eingestellt. Wenn die Dauer einer Inaktivitätsperiode den Timeoutwert überschreitet, gibt es keine Garantie dafür, dass die TCP- oder HTTP-Sitzung zwischen dem Client und Ihrem Clouddienst aufrechterhalten wird.

Sobald die Verbindung geschlossen wird, wird in der Clientanwendung möglicherweise die folgende Fehlermeldung angezeigt: „Die zugrunde liegende Verbindung wurde geschlossen: Eine Verbindung, deren Aufrechterhaltung erwartet wurde, wurde vom Server geschlossen.“

Eine gängige Methode zur Aufrechterhaltung von Verbindungen ist TCP-Keep-Alive. Dadurch bleibt die Verbindung länger aktiv. Weitere Informationen finden Sie in [diesen .NET-Beispielen](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Bei aktivierter Keep-Alive-Funktion werden während inaktiver Phasen Pakete über die Verbindung gesendet. Diese Keep-Alive-Pakete sorgen dafür, dass der Wert für das Leerlauftimeout niemals erreicht und die Verbindung über einen langen Zeitraum aufrechterhalten wird.

Diese Einstellung funktioniert nur für eingehende Verbindungen. Um den Verlust der Verbindung zu vermeiden, müssen Sie entweder für den TCP-Keep-Alive ein Intervall konfigurieren, das kürzer ist als das Leerlauftimeout, oder umgekehrt den Leerlauftimeout-Wert verlängern. Für solche Szenarien haben wir Unterstützung für konfigurierbare Leerlauftimeouts hinzugefügt. Sie können nun eine Dauer von 4 bis 30 Minuten festlegen.

TCP-Keep-Alive eignet sich für Szenarien, in denen die Akkulaufzeit kein limitierender Faktor ist. Bei mobilen Anwendungen ist die Verwendung dieser Funktion hingegen nicht empfehlenswert. TCP-Keep-Alive kann bei mobilen Anwendungen zu einer schnelleren Entladung des Geräteakkus führen.

![TCP-Timeout](./media/load-balancer-tcp-idle-timeout/image1.png)

In den folgenden Abschnitten wird das Ändern von Leerlauftimeout-Einstellungen von virtuellen Computern und Clouddiensten beschrieben.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Festlegen des TCP-Timeouts für Ihre öffentliche IP auf Instanzebene auf 15 Minuten

```powershell
Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15
```

`IdleTimeoutInMinutes` ist optional. Wenn dieser Wert nicht festgelegt ist, beträgt das Standardtimeout vier Minuten. Der zulässige Timeoutbereich beträgt 4 bis 30 Minuten.

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>Festlegen des Leerlauftimeouts beim Erstellen eines Azure-Endpunkts auf einem virtuellen Computer

Verwenden Sie Folgendes, um die Timeouteinstellung für einen Endpunkt zu ändern:

```powershell
Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
```

Verwenden Sie den folgenden Befehl, um Ihre Leerlauftimeout-Konfiguration abzurufen:

    PS C:\> Get-AzureVM -ServiceName "MyService" -Name "MyVM" | Get-AzureEndpoint
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

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>Festlegen des TCP-Timeouts für einen Endpunktsatz mit Lastenausgleich

Wenn Endpunkte Bestandteil eines Endpunktsatzes mit Lastenausgleich sind, muss das TCP-Timeout für den Endpunktsatz mit Lastenausgleich festgelegt werden. Beispiel:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
```

## <a name="change-timeout-settings-for-cloud-services"></a>Ändern von Timeouteinstellungen für Clouddienste

Sie können das Azure SDK verwenden, um Ihren Clouddienst zu aktualisieren. Endpunkteinstellungen für Clouddienste nehmen Sie in der CSDEF-Datei vor. Das Aktualisieren des TCP-Timeouts für die Bereitstellung eines Clouddiensts erfordert ein Upgrade der Bereitstellung. Ausnahme: Das TCP-Timeout wird nur für eine öffentliche IP festgelegt. Einstellungen für die öffentliche IP sind in der CSCFG-Datei gespeichert, und Sie können sie während des Updates und Upgrades der Bereitstellung aktualisieren.

In der CSDEF-Datei müssen die Einstellungen für einen Endpunkt folgendermaßen geändert werden:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
    </Endpoints>
</WorkerRole>
```

Die Änderungen in der CSCFG-Datei für die Timeouteinstellung für öffentliche IP-Adressen lauten:

```xml
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

## <a name="rest-api-example"></a>REST-API-Beispiel

Sie können das TCP-Leerlauftimeout mithilfe der Dienstverwaltungs-API konfigurieren. Vergewissern Sie sich, dass der `x-ms-version`-Header mindestens auf Version `2014-06-01` festgelegt ist. Aktualisieren Sie die Konfiguration der angegebenen Eingabeendpunkte mit Lastenausgleich auf allen virtuellen Computern in einer Bereitstellung.

### <a name="request"></a>Anforderung

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

### <a name="response"></a>response

```xml
<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
    <InputEndpoint>
    <LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
    <LocalPort>local-port-number</LocalPort>
    <Port>external-port-number</Port>
    <LoadBalancerProbe>
        <Path>path-of-probe</Path>
        <Port>port-assigned-to-probe</Port>
        <Protocol>probe-protocol</Protocol>
        <IntervalInSeconds>interval-of-probe</IntervalInSeconds>
        <TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
    </LoadBalancerProbe>
    <LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
    <Protocol>endpoint-protocol</Protocol>
    <IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
    <EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
    <EndpointACL>
        <Rules>
        <Rule>
            <Order>priority-of-the-rule</Order>
            <Action>permit-rule</Action>
            <RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
            <Description>description-of-the-rule</Description>
        </Rule>
        </Rules>
    </EndpointACL>
    </InputEndpoint>
</LoadBalancedEndpointList>
```

## <a name="next-steps"></a>Nächste Schritte

[Interner Lastenausgleich (Übersicht)](load-balancer-internal-overview.md)

[Erste Schritte zum Konfigurieren eines Lastenausgleichs für Internetverbindungen](load-balancer-get-started-internet-arm-ps.md)

[Konfigurieren eines Lastenausgleichs-Verteilungsmodus](load-balancer-distribution-mode.md)
