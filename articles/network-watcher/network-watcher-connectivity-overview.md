---
title: Einführung in die Problembehandlung für Verbindungen in Azure Network Watcher | Microsoft-Dokumentation
description: Diese Seite enthält eine Übersicht über die Network Watcher-Funktion zur Problembehandlung für Verbindungen.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: damendo
ms.openlocfilehash: 047da0224cdbdb534ad9080faee4fd48da3840c5
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492453"
---
# <a name="introduction-to-connection-troubleshoot-in-azure-network-watcher"></a>Einführung in die Problembehandlung für Verbindungen in Azure Network Watcher

Das Feature zur Problembehandlung von Verbindungen von Network Watcher bietet die Möglichkeit, eine direkte TCP-Verbindung von einem virtuellen Computer mit einem anderen virtuellen Computer (VM), einem vollqualifizierten Domänennamen (FQDN), einem URI oder einer IPv4-Adresse zu überprüfen. Netzwerkszenarios sind komplex: Sie werden mithilfe von Netzwerksicherheitsgruppen, Firewalls, benutzerdefinierten Routen und Ressourcen, die von Azure bereitgestellt werden, implementiert. Komplexe Konfigurationen stellen eine große Herausforderung bei der Behandlung von Konnektivitätsproblemen dar. Network Watcher trägt dazu bei, die zum Suchen und Erkennen von Konnektivitätsproblemen benötigte Zeit zu verkürzen. Die zurückgegebenen Ergebnisse können Einblicke in die Frage geben, ob ein Konnektivitätsproblem auf ein Problem mit einer Plattform oder einer Benutzerkonfiguration zurückzuführen ist. Die Konnektivität kann mit [PowerShell](network-watcher-connectivity-powershell.md), [Azure CLI](network-watcher-connectivity-cli.md) und [REST-API](network-watcher-connectivity-rest.md) überprüft werden.

> [!IMPORTANT]
> Für die Problembehandlung für Verbindungen muss auf dem virtuellen Computer, auf dem Sie die Problembehandlung ausführen, die VM-Erweiterung `AzureNetworkWatcherExtension` installiert sein. Informationen zur Installation der Erweiterung finden Sie für einen virtuellen Windows-Computer unter [VM-Erweiterung für den Network Watcher-Agent für Windows](../virtual-machines/extensions/network-watcher-windows.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) und für einen virtuellen Linux-Computer unter [VM-Erweiterung für den Network Watcher-Agent für Linux](../virtual-machines/extensions/network-watcher-linux.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Die Erweiterung ist nicht auf dem Zielendpunkt erforderlich.

## <a name="response"></a>Antwort

Die folgende Tabelle zeigt die Eigenschaften, die nach Ausführung der Problembehandlung für Verbindungen zurückgegeben werden:

|Eigenschaft  |BESCHREIBUNG  |
|---------|---------|
|ConnectionStatus     | Status der Konnektivitätsprüfung. Mögliche Ergebnisse sind **Erreichbar** und **Nicht erreichbar**.        |
|AvgLatencyInMs     | Durchschnittliche Wartezeit während der Konnektivitätsprüfung in Millisekunden. (Wird nur angezeigt, wenn der Status „Erreichbar“ ist)        |
|MinLatencyInMs     | Minimale Wartezeit während der Konnektivitätsprüfung in Millisekunden. (Wird nur angezeigt, wenn der Status „Erreichbar“ ist)        |
|MaxLatencyInMs     | Maximale Wartezeit während der Konnektivitätsprüfung in Millisekunden. (Wird nur angezeigt, wenn der Status „Erreichbar“ ist)        |
|ProbesSent     | Anzahl der Tests, die während der Überprüfung gesendet werden. Höchstwert ist 100.        |
|ProbesFailed     | Anzahl der fehlerhaften Tests während der Überprüfung. Höchstwert ist 100.        |
|Hops     | Hop-by-Hop-Pfad von der Quelle zum Ziel.        |
|Hops[].Type     | Ressourcentyp. Mögliche Werte sind **Source**, **VirtualAppliance**, **VnetLocal** und **Internet**.        |
|Hops[].Id | Eindeutiger Bezeichner des Hops.|
|Hops[].Address | IP-Adresse des Hops.|
|Hops[].ResourceId | Ressourcen-ID des Hops, wenn der Hop eine Azure-Ressource ist. Wenn es sich um eine Internetressource handelt, lautet die Ressourcen-ID **Internet**. |
|Hops[].NextHopIds | Eindeutiger Bezeichner des nächsten Hops.|
|Hops[].Issues | Auflistung von Problemen, die während der Überprüfung auf diesem Hop aufgetreten sind. Wert ist leer, wenn keine Probleme aufgetreten sind.|
|Hops[].Issues[].Origin | Auf dem aktuellen Hop, wo das Problem aufgetreten ist. Mögliche Werte:<br/> **Eingehend**: Problem liegt auf der Verbindung vom vorherigen Hop zum aktuellen Hop.<br/>**Ausgehend**: Problem liegt auf der Verbindung vom aktuellen Hop zum nächsten Hop.<br/>**Lokal**: Problem befindet sich auf dem aktuellen Hop.|
|Hops[].Issues[].Severity | Schweregrad des gefundenen Problems. Mögliche Werte sind **Error** und **Warning**. |
|Hops[].Issues[].Type |Typ des gefundenen Problems. Mögliche Werte: <br/>**CPU**<br/>**Memory**<br/>**GuestFirewall**<br/>**DnsResolution**<br/>**NetworkSecurityRule**<br/>**UserDefinedRoute** |
|Hops[].Issues[].Context |Details zu dem gefundenen Problem.|
|Hops[].Issues[].Context[].key |Schlüssel des zurückgegebenen Schlüssel-Wert-Paars.|
|Hops[].Issues[].Context[].value |Wert des zurückgegebenen Schlüssel-Wert-Paars.|

Im Folgenden finden Sie ein Beispiel für ein Problem, das auf einem Hop gefunden wurde:

```json
"Issues": [
    {
        "Origin": "Outbound",
        "Severity": "Error",
        "Type": "NetworkSecurityRule",
        "Context": [
            {
                "key": "RuleName",
                "value": "UserRule_Port80"
            }
        ]
    }
]
```
## <a name="fault-types"></a>Fehlertypen

Die Problembehandlung für Verbindungen gibt Fehlertypen der Verbindung zurück. Die folgende Tabelle listet die aktuellen zurückgegebenen Fehlertypen auf:

|type  |BESCHREIBUNG  |
|---------|---------|
|CPU     | Hohe CPU-Auslastung.       |
|Arbeitsspeicher     | Hohe Speicherauslastung.       |
|GuestFirewall     | Datenverkehr wird aufgrund der Firewall-Konfiguration eines virtuellen Computers blockiert.        |
|DNSResolution     | Fehler bei DNS-Auflösung für Zieladresse.        |
|NetworkSecurityRule    | Datenverkehr wird durch NSG-Regel blockiert (Regel zurückgegeben).        |
|UserDefinedRoute|Datenverkehr wird aufgrund einer benutzerdefinierten oder Systemroute verworfen. |

### <a name="next-steps"></a>Nächste Schritte

Lesen Sie, wie Sie mit dem [Azure-Portal](network-watcher-connectivity-portal.md), [PowerShell](network-watcher-connectivity-powershell.md), der [Azure CLI](network-watcher-connectivity-cli.md) oder der [REST-API](network-watcher-connectivity-rest.md) eine Problembehandlung für Verbindungen ausführen.