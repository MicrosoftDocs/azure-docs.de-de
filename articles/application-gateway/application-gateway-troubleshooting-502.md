---
title: Behandeln von Fehlern aufgrund eines ungültigen Gateways – Azure Application Gateway
description: 'Hier erfahren Sie, wie Sie folgenden Application Gateway-Serverfehler behandeln: 502: Webserver hat als Gateway oder Proxyserver eine ungültige Antwort erhalten.'
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: d44f9109540c3899ab50bd5c4c02afa19045bafb
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182936"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Behandeln von Fehlern aufgrund eines ungültigen Gateways in Application Gateway

Erfahren Sie mehr zur Problembehandlung bei Fehlern aufgrund eines ungültigen Gateways (502) in Application Gateway.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Übersicht

Nach der Konfiguration eines Anwendungsgateways ist einer der möglichen Fehler, die eventuell angezeigt werden, „Serverfehler: 502 - Webserver hat als Gateway oder Proxyserver eine ungültige Antwort erhalten.“ auftritt, Dieser Fehler kann folgende Hauptursachen haben:

* Eine NSG, eine benutzerdefinierte Route (UDR, user-defined route) oder ein benutzerdefiniertes DNS blockiert den Zugriff auf Back-End-Poolmitglieder.
* Virtuelle Back-End-Computer oder Instanzen der VM-Skalierungsgruppe reagieren nicht auf die standardmäßige Integritätsüberprüfung.
* Benutzerdefinierte Integritätsüberprüfungen sind ungültig oder nicht korrekt konfiguriert.
* Der [Back-End-Pool von Azure Application Gateway ist nicht konfiguriert oder leer](#empty-backendaddresspool).
* Die virtuellen Computer oder Instanzen in der [VM-Skalierungsgruppe befinden sich nicht in einem fehlerfreien Zustand](#unhealthy-instances-in-backendaddresspool).
* Bei der [Anforderung tritt ein Timeout auf, oder es liegen Verbindungsprobleme](#request-time-out) bei Benutzeranforderungen vor.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Netzwerksicherheitsgruppe, benutzerdefinierte Route oder benutzerdefiniertes DNS-Problem

### <a name="cause"></a>Ursache

Wenn der Zugriff auf das Back-End wegen einer NSG, benutzerdefinierten Route oder einem benutzerdefinierten DNS blockiert ist, können Application Gateway-Instanzen den Back-End-Pool nicht erreichen. Dies verursacht Überprüfungsfehler, die zu Fehlern vom Typ 502 führen.

Die NSG/UDR könnte entweder im Application Gateway-Subnetz oder im Subnetz vorhanden sein, in dem die Anwendungs-VMs bereitgestellt sind.

Auf ähnliche Weise könnte auch das Vorhandensein eines benutzerdefinierten DNS im VNet auch Probleme verursachen. Ein für Back-End-Poolmitglieder verwendeter FQDN wird möglicherweise nicht ordnungsgemäß vom benutzerkonfigurierten DNS-Server für das VNet aufgelöst.

### <a name="solution"></a>Lösung

Überprüfen Sie die NSG-, UDR- und DNS-Konfiguration, indem Sie die folgenden Schritte durchlaufen:

* Überprüfen Sie NSGs, die dem Application Gateway-Subnetz zugeordnet sind. Stellen Sie sicher, dass die Kommunikation mit dem Back-End nicht blockiert ist.
* Überprüfen Sie die UDR, die dem Application Gateway-Subnetz zugeordnet ist. Stellen Sie sicher, dass die UDR keinen Datenverkehr vom Back-End-Subnetz wegleitet. Überprüfen Sie z. B. das Routing zu virtuellen Geräten oder Standardrouten, die dem Application Gateway-Subnetz über ExpressRoute/VPN angekündigt werden.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Überprüfen Sie die effektiven NSGs und Routen mit dem virtuellen Back-End-Computer.

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Überprüfen Sie das Vorhandensein eines benutzerdefinierten DNS im VNet. DNS kann überprüft werden, indem Sie einen Blick auf die Details der VNet-Eigenschaften in der Ausgabe werfen.

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Falls vorhanden, stellen Sie sicher, dass der DNS-Server den FQDN von Back-End-Poolmitgliedern ordnungsgemäß auflösen kann.

## <a name="problems-with-default-health-probe"></a>Probleme mit der standardmäßigen Integritätsüberprüfung

### <a name="cause"></a>Ursache

502-Fehler sind auch oftmals darauf zurückzuführen, dass virtuelle Back-End-Computer für die standardmäßige Integritätsüberprüfung nicht erreichbar sind.

Beim Bereitstellen einer Application Gateway-Instanz wird unter Verwendung von Eigenschaften der Back-End-HTTP-Einstellung automatisch für jeden Back-End-Adresspool eine standardmäßige Integritätsüberprüfung konfiguriert. Zum Festlegen dieser Überprüfung ist keinerlei Benutzereingabe erforderlich. Wenn eine Regel für den Lastenausgleich konfiguriert wird, erfolgt eine Zuordnung zwischen einer Back-End-HTTP-Einstellung und dem Back-End-Adresspool. Für diese Zuordnungen wird jeweils eine standardmäßige Integritätsüberprüfung konfiguriert, und Application Gateway stellt über den im BackendHttpSetting-Element angegebenen Port in regelmäßigen Abständen eine Verbindung mit den einzelnen Instanzen im Back-End-Adresspool her, um die Integrität zu überprüfen. 

Die folgende Tabelle enthält die Werte der standardmäßigen Integritätsüberprüfung:

| Überprüfungseigenschaft | Wert | BESCHREIBUNG |
| --- | --- | --- |
| Überprüfungs-URL |`http://127.0.0.1/` |URL-Pfad |
| Intervall |30 |Überprüfungsintervall in Sekunden |
| Zeitüberschreitung |30 |Zeitüberschreitung der Überprüfung in Sekunden |
| Fehlerhafter Schwellenwert |3 |Anzahl der Wiederholungsversuche der Überprüfung Der Back-End-Server wird als außer Betrieb markiert, nachdem die Anzahl der aufeinanderfolgenden fehlgeschlagenen Überprüfungen den fehlerhaften Schwellenwert erreicht. |

### <a name="solution"></a>Lösung

* Stellen Sie sicher, dass die Standardwebsite konfiguriert ist und an 127.0.0.1 lauscht.
* Falls im BackendHttpSetting-Element nicht der Port 80 angegeben ist, muss die Standardwebsite so konfiguriert werden, dass sie am angegebenen Port lauscht.
* Der Aufruf von `http://127.0.0.1:port` sollte den HTTP-Ergebniscode 200 zurückgeben. Dieser muss innerhalb des Timeoutzeitraums von 30 Sekunden zurückgegeben werden.
* Vergewissern Sie sich, dass der konfigurierte Port geöffnet ist und dass eingehender oder ausgehender Datenverkehr am konfigurierten Port nicht durch Firewallregeln oder Azure-Netzwerksicherheitsgruppen blockiert wird.
* Stellen Sie bei Verwendung klassischer virtueller Azure-Computer sowie bei Verwendung des Clouddiensts mit FQDN oder öffentlicher IP-Adresse außerdem sicher, dass der entsprechende [Endpunkt](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints?toc=%2fazure%2fapplication-gateway%2ftoc.json) geöffnet ist.
* Falls der virtuelle Computer über Azure Resource Manager konfiguriert wurde und sich außerhalb des virtuellen Netzwerks befindet, in dem Application Gateway bereitgestellt ist, muss für den Zugriff auf den gewünschten Port eine [Netzwerksicherheitsgruppe](../virtual-network/network-security-groups-overview.md) konfiguriert werden.

## <a name="problems-with-custom-health-probe"></a>Probleme mit einer benutzerdefinierten Integritätsüberprüfung

### <a name="cause"></a>Ursache

Benutzerdefinierte Integritätsüberprüfungen sorgen für zusätzliche Flexibilität. Bei Verwendung von benutzerdefinierten Überprüfungen können Sie das Überprüfungsintervall, die URL und den zu überprüfenden Pfad konfigurieren und festlegen, wie viele fehlerhafte Antworten akzeptiert werden, bevor die Back-End-Pool-Instanz als fehlerhaft gekennzeichnet wird.

Folgende Zusatzeigenschaften werden hinzugefügt:

| Überprüfungseigenschaft | BESCHREIBUNG |
| --- | --- |
| Name |Name der Überprüfung. Dieser Name wird verwendet, um in den Back-End-HTTP-Einstellungen auf die Überprüfung zu verweisen. |
| Protocol |Das zum Senden der Überprüfung verwendete Protokoll. Für die Überprüfung wird das in den HTTP-Einstellungen des Back-Ends festgelegte Protokoll verwendet. |
| Host |Hostname zum Senden der Überprüfung Nur relevant, wenn in Application Gateway mehrere Standorte konfiguriert sind. Entspricht nicht dem VM-Hostnamen. |
| `Path` |Relativer Pfad der Überprüfung. Der gültige Pfad beginnt mit „/“. Der Test wird an \<protocol\>://\<host\>:\<port\>\<path\> gesendet. |
| Intervall |Überprüfungsintervall in Sekunden Dies ist das Zeitintervall zwischen zwei aufeinanderfolgenden Überprüfungen. |
| Zeitüberschreitung |Zeitüberschreitung der Überprüfung in Sekunden. Die Überprüfung wird als fehlerhaft markiert, wenn innerhalb des Zeitraums für die Zeitüberschreitung keine gültige Antwort empfangen wird. |
| Fehlerhafter Schwellenwert |Anzahl der Wiederholungsversuche der Überprüfung Der Back-End-Server wird als außer Betrieb markiert, nachdem die Anzahl der aufeinanderfolgenden fehlgeschlagenen Überprüfungen den fehlerhaften Schwellenwert erreicht. |

### <a name="solution"></a>Lösung

Vergewissern Sie sich anhand der Tabelle weiter oben, dass die benutzerdefinierte Integritätsüberprüfung ordnungsgemäß konfiguriert ist. Stellen Sie zusätzlich zu den oben aufgeführten Problembehandlungsschritten auch Folgendes sicher:

* Stellen Sie anhand der [Anleitung](application-gateway-create-probe-ps.md)sicher, dass die Überprüfung ordnungsgemäß angegeben ist.
* Wenn Application Gateway für einen einzelnen Standort konfiguriert ist, muss der Hostname standardmäßig als `127.0.0.1` angegeben werden, sofern in der benutzerdefinierten Überprüfung nichts anderes konfiguriert ist.
* Vergewissern Sie sich, dass ein Aufruf von „http://\<host\>:\<port\>\<path\>“ den HTTP-Ergebniscode 200 zurückgibt.
* Stellen Sie sicher, dass „Intervall“, „Zeitüberschreitung“ und „Fehlerhafter Schwellenwert“ innerhalb des zulässigen Bereichs liegen.
* Stellen Sie bei Verwendung einer HTTPS-Überprüfung sicher, dass der Back-End-Server kein SNI erfordert, indem Sie ein Fallback-Zertifikat auf dem Back-End-Server selbst erstellen.

## <a name="request-time-out"></a>Anforderungstimeout

### <a name="cause"></a>Ursache

Wenn eine Benutzeranforderung empfangen wird, wendet Application Gateway die konfigurierten Regeln auf die Anforderung an und leitet sie an eine Back-End-Poolinstanz weiter. Danach wartet Application Gateway eine bestimmte Zeit auf eine Antwort der Back-End-Instanz. Dieses Intervall ist standardmäßig auf **20** Sekunden festgelegt. Wenn Application Gateway innerhalb dieses Intervalls keine Antwort von der Back-End-Anwendung erhält, wird dem Benutzer der Fehler 502 angezeigt.

### <a name="solution"></a>Lösung

Mit Application Gateway können Sie diese Einstellung über das BackendHttpSetting-Element konfigurieren und auf verschiedene Pools anwenden. Bei unterschiedlichen Back-End-Pools können unterschiedliche Back-End-HTTP-Einstellungen und unterschiedliche Anforderungstimeouts konfiguriert sein.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>Leerer Back-End-Adresspool

### <a name="cause"></a>Ursache

Falls im Back-End-Adresspool für Application Gateway keine virtuellen Computer konfiguriert sind oder keine VM-Skalierungsgruppe konfiguriert ist, können Kundenanforderungen nicht weitergeleitet werden, und die Fehlermeldung „Ungültiges Gateway“ wird gesendet.

### <a name="solution"></a>Lösung

Vergewissern Sie sich, dass der Back-End-Adresspool nicht leer ist. Hierzu können Sie entweder PowerShell, die Befehlszeilenschnittstelle oder das Portal verwenden.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

Die Ausgabe des vorherigen Cmdlets muss nicht leere Back-End-Adresspools enthalten. Im folgenden Beispiel werden zwei zurückgegebene Pools gezeigt, die mit einem FQDN oder einer IP-Adresse für virtuelle Back-End-Computer konfiguriert sind. Der Bereitstellungszustand des Back-End-Adresspools muss „Succeeded“ lauten.

BackendAddressPoolsText:

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>Fehlerhafte Instanzen im Back-End-Adresspool

### <a name="cause"></a>Ursache

Wenn alle Instanzen des Back-End-Adresspools fehlerhaft sind, steht Application Gateway kein Back-End zur Weiterleitung von Benutzeranforderungen zur Verfügung. Dies kann auch der Fall sein, wenn zwar fehlerfreie Back-End-Instanzen vorhanden sind, diese aber nicht über die erforderliche Anwendung verfügen.

### <a name="solution"></a>Lösung

Stellen Sie sicher, dass die Instanzen fehlerfrei sind und die Anwendung ordnungsgemäß konfiguriert ist. Prüfen Sie, ob die Back-End-Instanzen auf ein Ping von einem anderen virtuellen Computer im gleichen virtuellen Netzwerk reagieren. Vergewissern Sie sich bei einer Konfiguration mit öffentlichem Endpunkt, dass eine an die Webanwendung gerichtete Browseranforderung verarbeitet werden kann.

## <a name="next-steps"></a>Nächste Schritte

Sollte sich das Problem mit den oben genannten Schritten nicht beheben lassen, erstellen Sie ein [Supportticket](https://azure.microsoft.com/support/options/).