---
title: Einführung in die Datenflussprotokollierung für Netzwerksicherheitsgruppen
titleSuffix: Azure Network Watcher
description: In diesem Artikel wird erläutert, wie das NSG-Datenflussprotokolle-Feature von Azure Network Watcher verwendet wird.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: b6f66813ea23f6c9d4b47a3733d0c72c683d0676
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493983"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Einführung in die Datenflussprotokollierung für Netzwerksicherheitsgruppen

## <a name="introduction"></a>Einführung

[NSG-Datenflussprotokolle (Netzwerksicherheitsgruppe)](../virtual-network/network-security-groups-overview.md#security-rules) sind ein Feature von Azure Network Watcher, mit dem Sie Informationen zum IP-Datenverkehr protokollieren können, der eine NSG durchläuft. Flussdaten werden an Azure Storage-Konten gesendet. Dort können Sie darauf zugreifen und die Daten in beliebige Visualisierungstools, SIEM- Systeme oder IDS Ihrer Wahl exportieren.

![Übersicht über Datenflussprotokolle](./media/network-watcher-nsg-flow-logging-overview/homepage.jpg)

## <a name="why-use-flow-logs"></a>Gründe für die Verwendung von Datenflussprotokollen

Es ist wichtig, dass Sie Ihr eigenes Netzwerk kennen, überwachen und verwalten, um die Sicherheit, Compliance und Leistung zu gewährleisten. Eine genaue Kenntnis Ihrer eigenen Umgebung ist von großer Bedeutung für deren Schutz und Optimierung. Häufig müssen Sie den aktuellen Status des Netzwerks kennen und wissen, wer womit verbunden ist, von wo die Verbindung erstellt wird, welche Ports zum Internet geöffnet sind, welches Netzwerkverhalten zu erwarten ist oder ob unregelmäßiges Netzwerkverhalten oder plötzliche Anstiege beim Datenverkehr auftreten.

Datenflussprotokolle sind die Quelle der Wahrheit für alle Netzwerkaktivitäten in ihrer Cloudumgebung. Ganz gleich, ob Sie als aufstrebendes Startup-Unternehmen Ihre Ressourcen optimieren oder als großes Unternehmen Eindringversuche entdecken möchten: Flussprotokolle sind die beste Lösung. Sie können sie zur Optimierung des Netzwerkdatenflusses, zur Überwachung des Durchsatzes, zur Überprüfung der Compliance, zur Erkennung von Eindringversuchen und für vieles mehr verwenden.

## <a name="common-use-cases"></a>Gängige Anwendungsfälle

**Netzwerküberwachung**: Erkennen Sie unbekannten oder unerwünschten Datenverkehr. Überwachen Sie die Datenverkehrsebenen und die Bandbreitennutzung. Filtern Sie Datenflussprotokolle nach IP-Adresse und Port, um das Anwendungsverhalten zu verstehen. Exportieren Sie Datenfluss Protokolle in Analyse- und Visualisierungstools Ihrer Wahl, um Überwachungsdashboards einzurichten.

**Nutzungsüberwachung und -optimierung:** Identifizieren Sie die wichtigsten Datenflüsse („Top Talkers“) in Ihrem Netzwerk. Kombinieren Sie Datenflüsse mit GeoIP-Daten, um regionsübergreifenden Datenverkehr zu identifizieren. Verstehen Sie das Wachstum des Datenverkehrs, um Kapazitätsprognosen abgeben zu können. Verwenden Sie Daten, um übermäßig restriktive Datenverkehrsregeln zu entfernen.

**Compliance**: Verwenden Sie Datenflussdaten zum Überprüfen der Netzwerkisolation und -compliance mit Unternehmenszugriffsregeln.

**Netzwerkforensik und Sicherheitsanalyse**: Analysieren Sie Netzwerkdatenflüsse von kompromittierten IP-Adressen und Netzwerkschnittstellen. Exportieren Sie Datenflussprotokolle in ein beliebiges SIEM- oder IDS-Tool Ihrer Wahl.

## <a name="how-logging-works"></a>Funktionsweise der Protokollierung

**Schlüsseleigenschaften**

- Datenflussprotokolle arbeiten auf [Ebene 4](https://en.wikipedia.org/wiki/OSI_model#Layer_4:_Transport_Layer) und zeichnen alle IP-Datenflüsse auf, die eine NSG durchlaufen.
- Protokolle werden über die Azure-Plattform erfasst und wirken sich nicht auf Kundenressourcen oder die Netzwerkleistung aus.
- Protokolle werden im JSON-Format geschrieben und zeigen eingehende und ausgehende Datenflüsse auf NSG-Regelbasis ab.
- Jeder Protokolldatensatz enthält die Netzwerkschnittstelle (NIC), auf die sich der Datenfluss bezieht, 5-Tupel-Informationen, die Datenverkehrsentscheidung und (nur Version 2) Durchsatzinformationen. Ausführliche Informationen finden Sie weiter unten unter _Protokollformat_.
- Datenflussprotokolle verfügen über eine Aufbewahrungsfunktion, die das automatische Löschen der Protokolle bis zu einem Jahr nach ihrer Erstellung ermöglicht. 

> [!NOTE]
> Aufbewahrung ist nur verfügbar, wenn Sie [GPv2-Speicherkonten (Speicherkonten vom Typ „Allgemein v2“](../storage/common/storage-account-overview.md#types-of-storage-accounts)) verwenden. 

**Wichtige Konzepte**

- Softwaredefinierte Netzwerke werden um virtuelle Netzwerke (VNETs) und Subnetze herum organisiert. Die Sicherheit dieser VNETs und Subnetze kann über eine Netzwerksicherheitsgruppe (NSG) verwaltet werden.
- Eine Netzwerksicherheitsgruppe (NSG) enthält eine Liste mit _Sicherheitsregeln_, mit denen Netzwerkdatenverkehr in verbundenen Ressourcen zugelassen oder verweigert wird. NSGs können Subnetzen, einzelnen VMs oder einzelnen Netzwerkschnittstellen (NICs) zugeordnet werden, die mit virtuellen Computern (Resource Manager) verbunden sind. Weitere Informationen finden Sie unter [Übersicht über Netzwerksicherheit](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Alle Datenverkehrsflüsse in Ihrem Netzwerk werden mithilfe der Regeln in der entsprechenden NSG ausgewertet.
- Das Ergebnis dieser Auswertungen sind NSG-Datenflussprotokolle. Datenflussprotokolle werden über die Azure-Plattform erfasst und erfordern keine Änderung an den Kundenressourcen.
- Hinweis: Es gibt zwei Arten von Regeln: beendende und nicht beendende, mit jeweils unterschiedlichem Protokollierungsverhalten.
- - Verweigerungsregeln von Netzwerksicherheitsgruppen sind beendende Regeln. Die Netzwerksicherheitsgruppe, die Datenverkehr ablehnt, protokolliert dies in Flussprotokollen, und die Verarbeitung wird in diesem Fall beendet, wenn eine NSG den Datenverkehr abgelehnt hat. 
- - NSG-Zulassungsregeln sind nicht beendende Regeln, was bedeutet, dass die Verarbeitung mit der nächsten NSG fortgesetzt wird, selbst wenn eine NSG den Datenverkehr zulässt. Die letzte NSG, die Datenverkehr zulässt, protokolliert den Datenverkehr in Flussprotokollen.
- NSG-Datenflussprotokolle werden in Speicherkonten geschrieben, von wo aus auf sie zugegriffen werden kann.
- Sie können Datenflussprotokolle mithilfe von Tools wie TA, Splunk, Grafana, Stealthwatch usw. exportieren, verarbeiten, analysieren und visualisieren.

## <a name="log-format"></a>Protokollformat

Datenflussprotokolle enthalten die folgenden Eigenschaften:

* **time:** Zeitpunkt, zu dem das Ereignis ausgelöst wurde
* **systemId**: System-ID der Netzwerksicherheitsgruppe.
* **category**: Die Kategorie des Ereignisses. Die Kategorie ist immer **NetworkSecurityGroupFlowEvent**.
* **resourceid**: Ressourcen-ID der Netzwerksicherheitsgruppe
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


**Version 2 der NSG-Datenflussprotokolle (im Gegensatz zu Version 1)** 

In Version 2 der Protokolle wird das Konzept des Flowzustands eingeführt. Sie können konfigurieren, welche Version von Flowprotokollen Sie erhalten.

Der Flowzustand _B_ wird aufgezeichnet, wenn ein Flow initiiert wird. Die Flowzustände _C_ und _E_ markieren die Fortsetzung bzw. die Beendung eines Flows. Die Zustände _C_ und _E_ enthalten Informationen zur Bandbreite des Datenverkehrs.

### <a name="sample-log-records"></a>Beispielprotokolleinträge

Der folgende Text ist ein Beispiel für ein Datenflussprotokoll. Wie Sie sehen können, sind mehrere Datensätze vorhanden, die der im vorherigen Abschnitt beschriebenen Eigenschaftenliste entsprechen.

> [!NOTE]
> Die Werte in der Eigenschaft *flowTuples* sind eine durch Trennzeichen getrennte Liste.
 
**Beispiel für das Format eines NSG-Flowprotokolls der Version 1**
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
        
        
```
**Beispiel für das Format eines NSG-Flowprotokolls der Version 2**
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
        }
        
```
**Erläuterungen zum Protokolltupel**

![Datenflussprotokolltupel](./media/network-watcher-nsg-flow-logging-overview/tuple.png)

**Beispiel für Bandbreitenberechnung**

Flowtupel von einer TCP-Unterhaltung zwischen 185.170.185.105:35370 und 10.2.0.4:23:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

Für die Flowzustände _C_ (Fortsetzung) und _E_ (Beendung) wird die Anzahl von Bytes und Paketen vom Zeitpunkt des vorherigen Flowtupeldatensatzes an aggregiert. Entsprechend der vorherigen Beispielkonversation ist die Gesamtanzahl der übertragenen Pakete 1021+52+8005+47 = 9125. Die Gesamtanzahl der übertragenen Bytes ist 588096+29952+4610880+27072 = 5256000.


## <a name="enabling-nsg-flow-logs"></a>Aktivieren von NSG-Flussprotokollen

Verwenden Sie den entsprechenden Link unten, um Anleitungen zum Aktivieren von Datenflussprotokollen zu erhalten.

- [Azure portal](./network-watcher-nsg-flow-logging-portal.md)
- [PowerShell](./network-watcher-nsg-flow-logging-powershell.md)
- [BEFEHLSZEILENSCHNITTSTELLE (CLI)](./network-watcher-nsg-flow-logging-cli.md)
- [REST](./network-watcher-nsg-flow-logging-rest.md)
- [Azure Resource Manager](./network-watcher-nsg-flow-logging-azure-resource-manager.md)

## <a name="updating-parameters"></a>Aktualisieren von Parametern

**Azure portal**

Navigieren Sie im Azure-Portal zum Abschnitt „NSG-Datenflussprotokolle“ in Network Watcher. Klicken Sie dann auf den Namen der NSG. Dadurch wird der Bereich "Einstellungen" für das Datenflussprotokoll geöffnet. Ändern Sie die gewünschten Parameter, und klicken Sie auf **Speichern**, um die Änderungen bereitzustellen.

**PS/CLI/REST/ARM**

Zum Aktualisieren von Parametern über Befehlszeilentools verwenden Sie denselben Befehl, der zum Aktivieren von Datenflussprotokollen verwendet wurde (siehe oben), jedoch mit aktualisierten Parametern, die Sie ändern möchten.

## <a name="working-with-flow-logs"></a>Arbeiten mit Datenflussprotokollen

*Lesen und Exportieren von Datenflussprotokollen*

- [Herunterladen aus dem &amp; Anzeigen von Datenflussprotokollen im Portal](./network-watcher-nsg-flow-logging-portal.md#download-flow-log)
- [Lesen von Datenflussprotokollen mithilfe von PowerShell-Funktionen](./network-watcher-read-nsg-flow-logs.md)
- [Exportieren von NSG-Datenflussprotokollen in Splunk](https://www.splunk.com/en_us/blog/tips-and-tricks/splunking-microsoft-azure-network-watcher-data.html)

Da sich Datenflussprotokolle auf NSGs beziehen, werden sie nicht wie andere Protokolle angezeigt. Datenflussprotokolle werden nur innerhalb eines Speicherkontos gespeichert. Der Protokollpfad entspricht dabei dem folgenden Beispiel:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

*Visualisieren von Datenflussprotokollen*

- [Azure Traffic Analytics](./traffic-analytics.md) ist ein nativer Azure-Dienst zum Verarbeiten von Datenflussprotokollen, zum Extrahieren von Erkenntnissen und Visualisieren von Datenflussprotokollen. 
- [[Tutorial] Visualisieren von NSG-Datenflussprotokollen mit Power BI](./network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [[Tutorial] Visualisieren von NSG-Datenflussprotokollen mit Elastic Stack](./network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [[Tutorial] Verwalten und Analysieren von NSG-Datenflussprotokollen mit Grafana](./network-watcher-nsg-grafana.md)
- [[Tutorial] Verwalten und Analysieren von NSG-Datenflussprotokollen mit Graylog](./network-watcher-analyze-nsg-flow-logs-graylog.md)


## <a name="nsg-flow-logging-considerations"></a>Überlegungen zur NSG-Flowprotokollierung

**Überlegungen zum Speicherkonto:** 

- Standort: Das verwendete Speicherkonto muss sich in derselben Region wie die Netzwerksicherheitsgruppe befinden.
- Rotation von selbstverwalteten Schlüsseln: Wenn Sie die Zugriffsschlüssel für Ihr Speicherkonto ändern oder rotieren, funktionieren die NSG-Datenflussprotokolle nicht mehr. Um dieses Problem zu beheben, müssen Sie die NSG-Datenflussprotokolle deaktivieren und anschließend wieder aktivieren.

**Kosten der Datenflussprotokollierung**: Die NSG-Datenflussprotokollierung wird über die Menge der erzeugten Protokolle abgerechnet. Hohe Datenverkehrsvolumen können zu großen Datenflussprotokollvolumen und den damit verbundenen Kosten führen. Preise für NSG-Datenflussprotokolle enthalten nicht die zugrunde liegenden Kosten der Speicherung. Die Verwendung der Aufbewahrungsrichtlinienfunktion mit NSG-Datenflussprotokollierung bedeutet, dass separate Speicherkosten für längere Zeiträume anfallen. Wenn Sie die Aufbewahrungsrichtlinienfunktion nicht benötigen, empfehlen wir, dass Sie diesen Wert auf 0 festlegen. Weitere Informationen und zusätzliche Details finden Sie unter [Network Watcher – Preise](https://azure.microsoft.com/pricing/details/network-watcher/) und [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

**Probleme mit benutzerdefinierten TCP-Eingangsregeln:** [Netzwerksicherheitsgruppen (NSGs)](../virtual-network/network-security-groups-overview.md) werden als eine [zustandsbehaftete Firewall](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true) implementiert. Aufgrund der aktuellen Plattformeinschränkungen werden benutzerdefinierte Regeln, die sich auf eingehende TCP-Flows auswirken, jedoch in einer zustandslosen Weise implementiert. Aus diesem Grund führen die von den benutzerdefinierten Eingangsregeln betroffenen Flows zu keiner Beendigung. Darüber hinaus wird die Byte- und Paketanzahl für diese Flows nicht aufgezeichnet. Entsprechend kann die Anzahl der Bytes und Pakete, die in den NSG-Flussprotokollen (und der Datenverkehrsanalyse) gemeldet wird, von den tatsächlichen Werten abweichen. Ein Flag, das diese Probleme behebt und abonniert werden kann, soll spätestens im Dezember 2020 zur Verfügung stehen. In der Zwischenzeit können Kunden mit schwerwiegenden Problemen, die infolge dieses Verhaltens auftreten, die Aktivierung des Flags über den Support anfordern. Stellen Sie dazu unter „Network Watcher“ > „NSG-Flowprotokolle“ eine Supportanfrage.  

**Aus Internet-IP-Adressen protokollierte eingehende Datenflüsse an virtuelle Computer ohne öffentliche IP-Adressen**: Für virtuelle Computer, denen keine öffentliche IP-Adresse über eine öffentliche IP-Adresse zugewiesen wurde, die der Netzwerkkarte als öffentliche IP-Adresse auf Instanzebene zugeordnet ist, oder die zu einem Basis-Back-End-Pool für Lastenausgleich gehören, werden [standardmäßiges SNAT](../load-balancer/load-balancer-outbound-connections.md) und eine IP-Adresse verwendet, die von Azure zugewiesen wurde, um ausgehende Verbindung zu unterstützen. Daher sehen Sie möglicherweise Datenflussprotokolleinträge für Datenflüsse von Internet-IP-Adressen, wenn der jeweilige Datenfluss für einen Port im Bereich der Ports bestimmt war, die für SNAT zugewiesen sind. Obwohl Azure diese Datenflüsse zu dem virtuellen Computer nicht zulässt, wird der Versuch protokolliert und konzeptbedingt im NSG-Datenflussprotokoll von Network Watcher aufgeführt. Es empfiehlt sich, unerwünschten eingehenden Internet-Datenverkehr explizit mit NSG zu blockieren.

**Inkompatible Dienste**: Aufgrund der derzeitigen Plattformeinschränkungen wird eine kleine Anzahl von Azure-Diensten nicht von NSG-Datenflussprotokollen unterstützt. Die aktuelle Liste der inkompatiblen Dienste lautet:
- [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
- [Logik-Apps](https://azure.microsoft.com/services/logic-apps/) 

## <a name="best-practices"></a>Bewährte Methoden

**Aktivieren für kritische VNETs/Subnetze**: Datenflussprotokolle sollten für alle kritischen VNETs/Subnetze in Ihrem Abonnement als bewährte Methode für Überwachbarkeit und Sicherheit aktiviert werden. 

**Aktivieren der NSG-Datenflussprotokollierung für alle Netzwerksicherheitsgruppen (NSGs), die einer Ressource angefügt sind**: Datenflussprotokollierung in Azure wird für die einzelne NSG-Ressource konfiguriert. Ein Flow wird nur einer einzigen NSG-Regel zugeordnet. In Szenarien, in denen mehrere NSGs verwendet werden, empfehlen wir, die NSG-Datenflussprotokollierung für alle auf das Subnetz oder die Netzwerkschnittstelle einer Ressource angewendeten Netzwerksicherheitsgruppen zu aktivieren, um sicherzustellen, dass der gesamte Datenverkehr aufgezeichnet wird. Weitere Informationen zu Netzwerksicherheitsgruppen finden Sie unter [Auswertung von Datenverkehr](../virtual-network/network-security-group-how-it-works.md).

**Speicherbereitstellung**: Speicher sollte in Abstimmung mit dem erwarteten Datenflussprotokoll-Volumen bereitgestellt werden.

## <a name="troubleshooting-common-issues"></a>Behandeln allgemeiner Probleme

**Ich konnte die NSG-Flussprotokolle nicht aktivieren.**

- **Microsoft.Insights**-Ressourcenanbieter ist nicht registriert

Wenn Sie den Fehler _AuthorizationFailed_ oder _GatewayAuthenticationFailed_ erhalten, haben Sie unter Umständen den Microsoft Insights-Ressourcenanbieter für Ihr Abonnement nicht aktiviert. [Befolgen Sie die Anleitung](./network-watcher-nsg-flow-logging-portal.md#register-insights-provider), um den Microsoft Insights-Anbieter zu aktivieren.

**Ich habe die NSG-Flussprotokolle aktiviert, aber in meinem Speicherkonto werden keine Daten angezeigt.**

- **Einrichtungszeit**

Es kann bis zu fünf Minuten dauern, bis NSG-Flussprotokolle in Ihrem Speicherkonto angezeigt werden (bei ordnungsgemäßer Konfiguration). Die Datei „PT1H.json“ wird angezeigt. Darauf kann wie [hier beschrieben](./network-watcher-nsg-flow-logging-portal.md#download-flow-log) zugegriffen werden.

- **Kein Datenverkehr in Ihren NSGs**

In einigen Fällen werden keine Protokolle angezeigt, weil Ihre VMs nicht aktiv sind, oder der Datenverkehr zu Ihren NSGs wird aufgrund von Upstream-Filtern auf einem App Gateway oder anderen Geräten blockiert.

**Ich möchte NSG-Flussprotokolle automatisieren**

Die Unterstützung der Automatisierung über ARM-Vorlagen ist derzeit für NSG-Datenflussprotokolle nicht verfügbar. Weitere Informationen finden Sie in der [Featureankündigung](https://azure.microsoft.com/updates/arm-template-support-for-nsg-flow-logs/).

## <a name="faq"></a>Häufig gestellte Fragen

**Welche Aufgabe haben NSG-Datenflussprotokolle?**

Azure-Netzwerkressourcen können mithilfe von [Netzwerksicherheitsgruppen (NSGs)](../virtual-network/network-security-groups-overview.md) kombiniert und verwaltet werden. NSG-Flussprotokolle ermöglichen Ihnen, Flussinformationen mit 5 Tupeln zum gesamten Datenverkehr durch Ihre NSGs zu protokollieren. Die unformatierten Flowprotokolle werden in ein Azure Storage-Konto geschrieben, in dem Sie nach Bedarf weiterverarbeitet, analysiert, abgefragt oder exportiert werden können.

**Wirkt sich die Verwendung von Datenflussprotokollen auf die Netzwerklatenz oder -leistung aus?**

Die Daten von Datenflussprotokollen werden außerhalb des Pfads Ihres Netzwerkdatenverkehrs erfasst und wirken sich daher nicht auf den Netzwerkdurchsatz oder die -latenz aus. Sie können Datenflussprotokolle erstellen oder löschen, ohne dass sich dies auf die Netzwerkleistung auswirkt.

**Wie verwende ich NSG-Flussprotokolle mit einem Speicherkonto hinter einer Firewall?**

Wenn Sie ein Speicherkonto hinter einer Firewall verwenden möchten, müssen Sie eine Ausnahme für vertrauenswürdige Microsoft-Dienste für den Zugriff auf Ihr Speicherkonto bereitstellen:

- Navigieren Sie zum Speicherkonto, indem Sie im Portal oder auf der Seite [Speicherkonten](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts) den Namen des Speicherkontos in das Feld für die globale Suche eingeben.
- Wählen Sie im Abschnitt **EINSTELLUNGEN** die Option **Firewalls und virtuelle Netzwerke** aus.
- Wählen Sie unter **Zugriff erlauben von** die Option **Ausgewählte Netzwerke** aus. Aktivieren Sie anschließend unter **Ausnahmen** das Kontrollkästchen neben  ****Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben****
- Falls die Option bereits ausgewählt ist, ist keine Änderung erforderlich.
- Suchen Sie auf der Seite [NSG-Flussprotokolle: Übersicht](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) nach ihrer Ziel-NSG, und aktivieren Sie NSG-Flussprotokolle mit dem oben ausgewählten Speicherkonto.

Sie können nach einigen Minuten die Speicherprotokolle überprüfen, um zu ermitteln, ob ein aktualisierter Zeitstempel vorhanden ist oder eine neue JSON-Datei erstellt wurde.

**Wie verwende ich NSG-Flussprotokolle mit einem Speicherkonto hinter einem Dienstendpunkt?**

NSG-Flussprotokolle sind ohne weitere Konfiguration mit Dienstendpunkten kompatibel. Weitere Informationen finden Sie im [Tutorial zum Aktivieren von Dienstendpunkten](../virtual-network/tutorial-restrict-network-access-to-resources.md#enable-a-service-endpoint) in Ihrem virtuellen Netzwerk.

**Worin besteht der Unterschied zwischen den Datenflussprotokollen der Version 1 und 2?**

In Flowprotokolle der Version 2 wurde des Konzept des _Flowzustands_ eingeführt, gemäß dem Informationen zu Bytes und Paketen gespeichert werden. [hier](#log-format)

## <a name="pricing"></a>Preise

NSG-Datenflussprotokolle werden pro GB an erfassten Protokollen abgerechnet und verfügen über einen Free-Tarif von 5 GB/Monat pro Abonnement. Die aktuellen Preise in Ihrer Region finden Sie auf der Seite [Network Watcher-Preise](https://azure.microsoft.com/pricing/details/network-watcher/).

Die Speicherung von Protokollen wird separat abgerechnet. Weitere Informationen zu den relevanten Preisen finden Sie auf der Seite [Azure Storage-Blockblobpreise](https://azure.microsoft.com/pricing/details/storage/blobs/).
