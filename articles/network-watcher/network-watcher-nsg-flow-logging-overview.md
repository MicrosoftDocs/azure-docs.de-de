---
title: Einführung in die Datenflussprotokollierung für Netzwerksicherheitsgruppen mit Azure Network Watcher | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie das NSG-Datenflussprotokolle-Feature von Azure Network Watcher verwendet wird.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 47d91341-16f1-45ac-85a5-e5a640f5d59e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 1ec7fd4116aa848a9c431df386997cb23f405f1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64925417"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Einführung in die Datenflussprotokollierung für Netzwerksicherheitsgruppen

Datenflussprotokolle für Netzwerksicherheitsgruppen (NSG) sind ein Network Watcher-Feature, mit dem Sie Informationen zu ein- und ausgehendem IP-Datenverkehr über eine NSG anzeigen können. Die Flowprotokolle sind im JSON-Format geschrieben und zeigen aus- und eingehende Datenflows pro Regel, die Netzwerkschnittstelle, auf die sich der Datenflow bezieht, 5-Tupel-Informationen über den Datenflow (Quell-/Ziel-IP-Adresse, Quell-/Zielport, Protokoll) und Informationen zu zugelassenem oder verweigertem Datenverkehr an. In Version 2 sind darüber hinaus Durchsatzinformationen (Bytes und Pakete) angegeben.


![Übersicht über Datenflussprotokolle](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

Da sich Datenflussprotokolle auf NSGs beziehen, werden sie nicht wie andere Protokolle angezeigt. Datenflussprotokolle werden nur innerhalb eines Speicherkontos gespeichert. Der Protokollpfad entspricht dabei dem folgenden Beispiel:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
Sie können Datenflussprotokolle analysieren und so mithilfe von [Datenverkehrsanalysen](traffic-analytics.md) Einblicke in Ihren Netzwerkdatenverkehr gewinnen.

Für Datenflussprotokolle gelten die gleichen Aufbewahrungsrichtlinien wie für andere Protokolle. Sie können Protokollaufbewahrungs-Richtlinien von einem Tag bis zu 2.147.483.647 Tagen festlegen. Wenn keine Aufbewahrungsrichtlinie festgelegt wurde, werden die Protokolle unbegrenzt aufbewahrt.

> [!NOTE] 
> Wenn Sie die Aufbewahrungsrichtlinienfunktion mit der NSG-Datenflussprotokollierung verwenden, kann dies zu einem hohen Volumen von Speichervorgängen und den damit verbundenen Kosten führen. Wenn Sie die Aufbewahrungsrichtlinienfunktion nicht benötigen, empfehlen wir, dass Sie diesen Wert auf 0 festlegen.


## <a name="log-file"></a>Protokolldatei

Datenflussprotokolle enthalten die folgenden Eigenschaften:

* **time:** Zeitpunkt, zu dem das Ereignis ausgelöst wurde
* **systemId:** ID der Netzwerksicherheitsgruppen-Ressource
* **category**: Die Kategorie des Ereignisses. Die Kategorie ist immer **NetworkSecurityGroupFlowEvent**.
* **resourceid:** Ressourcen-ID der Netzwerksicherheitsgruppe
* **operationName:** immer NetworkSecurityGroupFlowEvents
* **properties:** Sammlung der Eigenschaften des Datenflusses
    * **Version:** Versionsnummer des Ereignisschemas für das Datenflussprotokoll
    * **flows:** Sammlung von Datenflüssen. Diese Eigenschaft verfügt über mehrere Einträge für verschiedene Regeln.
        * **rule:** Regel, für die die Datenflüsse aufgeführt werden
            * **flows:** Sammlung von Datenflüssen
                * **mac:** MAC-Adresse der Netzwerkkarte für den virtuellen Computer, auf dem der Datenfluss erfasst wurde
                * **flowTuples:** Zeichenfolge, die mehrere Eigenschaften für das Datenflusstupel in einem Format mit Trennzeichen enthält
                    * **Zeitstempel:** Wert des Zeitstempels für den Zeitpunkt, zu dem der Datenfluss auftrat, im UNIX EPOCHE-Format
                    * **Quell-IP:** Quell-IP-Adresse
                    * **Ziel-IP:** Ziel-IP-Adresse
                    * **Quellport:** Quellport
                    * **Zielport:** Zielport
                    * **Protokoll:** Protokoll des Datenflusses. Gültige Werte sind **T** für TCP und **U** für UDP.
                    * **Datenfluss:** Richtung des Datenflusses. Gültige Werte sind **I** für eingehende (inbound) und **O** für ausgehende (outbound) Nachrichten.
                    * **Entscheidung zum Datenverkehr:** Gibt an, ob Datenverkehr zugelassen oder verweigert wurde. Gültige Werte sind **A** für zugelassen (allowed) und **D** für verweigert (denied).
                    * **Flowstatus (nur Version 2):** Erfasst den Flowstatus. Mögliche Statusangaben: **B**: („Begin“/Anfang): Erstellung eines Flows. Statistiken werden nicht bereitgestellt. **C**: („Continue“/Fortsetzung): Ein laufender Flow wird weiter fortgesetzt. Statistiken werden in Intervallen von 5 Minuten bereitgestellt. **E**: („End“/Beendigung): Beendigung eines Flows. Statistiken werden bereitgestellt.
                    * **Pakete – Quelle zu Ziel – nur Version 2:** Die Gesamtanzahl von TCP- oder UDP-Paketen, die seit dem letzten Update von der Quelle zum Ziel gesendet wurden
                    * **Gesendete Bytes – Quelle zu Ziel – nur Version 2:** Die Gesamtanzahl von TCP- oder UDP-Paketbytes, die seit dem letzten Update von der Quelle zum Ziel gesendet wurden Paketbytes enthalten den Paketheader und die Nutzlast.
                    * **Pakete – Ziel zu Quelle – nur Version 2:** Die Gesamtanzahl von TCP- oder UDP-Paketen, die seit dem letzten Update vom Ziel zur Quelle gesendet wurden
                    * **Gesendete Bytes – Ziel zu Quelle – nur Version 2:** Die Gesamtanzahl von TCP- oder UDP-Paketbytes, die seit dem letzten Update vom Ziel zur Quelle gesendet wurden Paketbytes enthalten den Paketheader und die Nutzlast.

## <a name="nsg-flow-logs-version-2"></a>Version 2 der NSG-Flowprotokolle

In Version 2 der Protokollierung wird der Flowzustand eingeführt. Sie können konfigurieren, welche Version von Flowprotokollen Sie erhalten. Wie Sie Datenflussprotokolle aktivieren, erfahren Sie unter [Tutorial: Verwalten von Datenflussprotokollen für Netzwerksicherheitsgruppen über das Azure-Portal](network-watcher-nsg-flow-logging-portal.md).

Der Flowzustand *B* wird aufgezeichnet, wenn ein Flow initiiert wird. Die Flowzustände *C* und *E* markieren die Fortsetzung bzw. die Beendung eines Flows. Die Zustände *C* und *E* enthalten Informationen zur Bandbreite des Datenverkehrs.

Für die Flowzustände *C* (Fortsetzung) und *E* (Beendung) wird die Anzahl von Bytes und Paketen vom Zeitpunkt des vorherigen Flowtupeldatensatzes an aggregiert. Entsprechend der vorherigen Beispielkonversation ist die Gesamtanzahl der übertragenen Pakete 1021+52+8005+47 = 9125. Die Gesamtanzahl der übertragenen Bytes ist 588096+29952+4610880+27072 = 5256000.

**Beispiel**: Flowtupel von einer TCP-Unterhaltung zwischen 185.170.185.105:35370 und 10.2.0.4:23:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

Für die Flowzustände *C* (Fortsetzung) und *E* (Beendung) wird die Anzahl von Bytes und Paketen vom Zeitpunkt des vorherigen Flowtupeldatensatzes an aggregiert. Entsprechend der vorherigen Beispielkonversation ist die Gesamtanzahl der übertragenen Pakete 1021+52+8005+47 = 9125. Die Gesamtanzahl der übertragenen Bytes ist 588096+29952+4610880+27072 = 5256000.

Der folgende Text ist ein Beispiel für ein Datenflussprotokoll. Wie Sie sehen können, sind mehrere Datensätze vorhanden, die der im vorherigen Abschnitt beschriebenen Eigenschaftenliste entsprechen.

## <a name="nsg-flow-logging-considerations"></a>Überlegungen zur NSG-Datenflussprotokollierung

**Aktivieren der NSG-Datenflussprotokollierung für alle Netzwerksicherheitsgruppen (NSGs), die einer Ressource angefügt sind**: Datenflussprotokollierung in Azure wird für die einzelne NSG-Ressource konfiguriert. Ein Flow wird nur einer einzigen NSG-Regel zugeordnet. In Szenarien, in denen mehrere NSGs verwendet werden, empfehlen wir, dass die NSG-Datenflussprotokollierung für alle auf das Subnetz oder die Netzwerkschnittstelle einer Ressource angewendeten Netzwerksicherheitsgruppen aktiviert wird, um sicherzustellen, dass der gesamte Datenverkehr aufgezeichnet wird. Weitere Informationen zu Netzwerksicherheitsgruppen finden Sie unter [Gewusst wie: Bewertung von Datenverkehr](../virtual-network/security-overview.md#how-traffic-is-evaluated). 

**Kosten der Datenflussprotokollierung**: Die NSG-Datenflussprotokollierung wird über die Menge der erzeugten Protokolle abgerechnet. Hohe Datenverkehrsvolumen können zu großen Datenflussprotokollvolumen und den damit verbundenen Kosten führen. Preise für NSG-Datenflussprotokolle enthalten nicht die zugrunde liegenden Kosten der Speicherung. Wenn Sie die Aufbewahrungsrichtlinienfunktion mit der NSG-Datenflussprotokollierung verwenden, kann dies zu einem hohen Volumen von Speichervorgängen und den damit verbundenen Kosten führen. Wenn Sie die Aufbewahrungsrichtlinienfunktion nicht benötigen, empfehlen wir, dass Sie diesen Wert auf 0 festlegen. Zusätzliche Details finden Sie unter [Network Watcher – Preise](https://azure.microsoft.com/pricing/details/network-watcher/) und [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

**Aus Internet-IP-Adressen protokollierte eingehende Datenflüsse an virtuelle Computer ohne öffentliche IP-Adressen**: Für virtuelle Computer, denen keine öffentliche IP-Adresse über eine öffentliche IP-Adresse zugewiesen wurde, die der Netzwerkkarte als öffentliche IP-Adresse auf Instanzebene zugeordnet ist, oder die zu einem Basis-Back-End-Pool für Lastenausgleich gehören, werden [standardmäßiges SNAT](../load-balancer/load-balancer-outbound-connections.md#defaultsnat) und eine IP-Adresse verwendet, die von Azure zugewiesen wurde, um ausgehende Verbindung zu unterstützen. Daher sehen Sie möglicherweise Datenflussprotokolleinträge für Datenflüsse von Internet-IP-Adressen, wenn der jeweilige Datenfluss für einen Port im Bereich der Ports bestimmt war, die für SNAT zugewiesen sind. Obwohl Azure diese Datenflüsse zu dem virtuellen Computer nicht zulässt, wird der Versuch protokolliert und konzeptbedingt im NSG-Datenflussprotokoll von Network Watcher aufgeführt. Es empfiehlt sich, unerwünschten eingehenden Internet-Datenverkehr explizit mit NSG zu blockieren.

## <a name="sample-log-records"></a>Beispielprotokolleinträge

Der folgende Text ist ein Beispiel für ein Datenflussprotokoll. Wie Sie sehen können, sind mehrere Datensätze vorhanden, die der im vorherigen Abschnitt beschriebenen Eigenschaftenliste entsprechen.


> [!NOTE]
> Die Werte in der **flowTuples*-Eigenschaft sind eine durch Trennzeichen getrennte Liste.
 
### <a name="version-1-nsg-flow-log-format-sample"></a>Beispiel für das Format eines NSG-Flowprotokolls der Version 1
```json
{
    "records": [
        {
            "time": "2017-02-16T22:00:32.8950000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A",
                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A",
                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A",
                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2017-02-16T22:01:32.8960000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",
                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",
                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
    "records":
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        ,
        ...
```
### <a name="version-2-nsg-flow-log-format-sample"></a>Beispiel für das Format eines NSG-Flowprotokolls der Version 2
```json
 {
    "records": [
        {
            "time": "2018-11-13T12:00:35.3899262Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                                    "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                                    "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "DefaultRule_AllowInternetOutBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                                    "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                                    "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                                    "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2018-11-13T12:01:35.3918317Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110437,125.64.94.197,10.5.16.4,59752,18264,T,I,D,B,,,,",
                                    "1542110475,80.211.72.221,10.5.16.4,37433,8088,T,I,D,B,,,,",
                                    "1542110487,46.101.199.124,10.5.16.4,60577,8088,T,I,D,B,,,,",
                                    "1542110490,176.119.4.30,10.5.16.4,57067,52801,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        ...
```

## <a name="next-steps"></a>Nächste Schritte

- Wie Sie Datenflussprotokolle aktivieren, erfahren Sie unter [Tutorial: Verwalten von Datenflussprotokollen für Netzwerksicherheitsgruppen über das Azure-Portal](network-watcher-nsg-flow-logging-portal.md).
- Unter [Lesen von NSG-Datenflussprotokollen](network-watcher-read-nsg-flow-logs.md) erfahren Sie, wie die Datenflussprotokolle gelesen werden.
- Unter [Azure Monitor-Protokolle für Netzwerksicherheitsgruppen (NSGs)](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) finden Sie weitere Informationen über die NSG-Protokollierung.
- Wie Sie bestimmen, ob Datenverkehr zu oder von einem virtuellen Computer zugelassen oder verweigert ist, erfahren Sie unter [Schnellstart: Diagnostizieren eines VM-Netzwerkdatenverkehr-Filterproblems](diagnose-vm-network-traffic-filtering-problem.md).
