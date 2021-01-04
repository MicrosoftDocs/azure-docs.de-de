---
title: 'Erstellen eines Verbindungsmonitors: ARM-Vorlage'
titleSuffix: Azure Network Watcher
description: Hier erfahren Sie, wie Sie einen Verbindungsmonitors mithilfe von ARMClient erstellen.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: vinigam
ms.openlocfilehash: c3b228d2652d5f7dcf7c6596ee5425b3f5f9a4d8
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544002"
---
# <a name="create-a-connection-monitor-using-the-arm-template"></a>Erstellen eines Verbindungsmonitors mithilfe einer ARM-Vorlage

Hier erfahren Sie, wie Sie mithilfe von ARMClient einen Verbindungsmonitor erstellen, um die Kommunikation zwischen Ihren Ressourcen zu überwachen. Er unterstützt Hybrid- und Azure-Cloudbereitstellungen.


## <a name="before-you-begin"></a>Voraussetzungen 

In Verbindungsmonitoren, die Sie im Feature „Verbindungsmonitor“ erstellen, können Sie sowohl lokale Computer als auch Azure-VMs als Quellen hinzufügen. Mit diesen Verbindungsmonitoren kann auch die Konnektivität mit Endpunkten überwacht werden. Die Endpunkte können sich in Azure oder einer beliebigen anderen URL oder IP-Adresse befinden.

Der Verbindungsmonitor umfasst die folgenden Entitäten:

* **Verbindungsmonitorressource**: Dies ist eine regionsspezifische Azure-Ressource. Alle folgenden Entitäten sind Eigenschaften einer Verbindungsmonitorressource.
* **Endpunkt**: Eine Quelle oder ein Ziel, die bzw. das an Konnektivitätsprüfungen beteiligt ist. Beispiele für Endpunkte umfassen Azure-VMs, lokale Agents, URLs und IP-Adressen.
* **Testkonfiguration**: Eine protokollspezifische Konfiguration für einen Test. Je nach ausgewähltem Protokoll können Sie den Port, Schwellenwerte, die Testhäufigkeit und weitere Parameter definieren.
* **Testgruppe**: Die Gruppe, die Quellendpunkte, Zielendpunkte und Testkonfigurationen enthält. Ein Verbindungsmonitor kann mehrere Testgruppen enthalten.
* **Test**: Die Kombination aus einem Quellendpunkt, einem Zielendpunkt und einer Testkonfiguration. Ein Test ist die differenzierteste Ebene, auf der Überwachungsdaten verfügbar sind. Die Überwachungsdaten umfassen den Prozentsatz von Überprüfungen mit Fehlern und die Roundtripzeit.

    ![Diagramm eines Verbindungsmonitors mit Definition der Beziehung zwischen Testgruppen und Tests](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-sample-arm-template"></a>Schritte zum Erstellen mit der ARM-Beispielvorlage

Verwenden Sie den folgenden Code, um einen Verbindungsmonitor mithilfe von ARMClient zu erstellen.

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "https://management.azure.com"

$SUB = "subscriptions/<subscription id 1>;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_<region>"

$body =

"{

location: '<region>',

properties: {

endpoints: [{

name: 'endpoint_workspace_machine',

type: 'MMAWorkspaceMachine',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

//Example 1: Choose a machine

address : '<non-Azure machine FQDN>'
}

//Example 2: Select IP from chosen machines

address : '<non-Azure machine FQDN>

"scope": {
      "include": [
            {
                  "address": "<IP belonging to machine chosen above>"  
        }
       ]
      }
   }    
   
name: 'endpoint_workspace_network',

type: 'MMAWorkspaceNetwork',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

 coverage level : 'high', //Optional
 
 //Include subnets. You can also exclude IPs from subnet to exclude from monitoring
 
 scope: {
      "include": [
            {
                  "address": "<subnet 1 mask>" // Eg: 10.10.1.0/28
            },
            {
                  "address": "<subnet 2 mask>" 
            }
      ],
      "exclude": [
            { 
            "address" : "<ip-from-included-subnets-that-should-be-excluded>"
        }
      ]
     }
},

//Use a Azure VM as an endpoint
{

name: 'endpoint_virtualmachine',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

//Use an Azure VNET or Subnet as an endpoint

 {

name: 'endpoint_vnet_subnet',

resourceId: '<resource id of VNET or subnet'
coverage level: 'high' //Optional

//Scope is optional.

  "scope": {
      "include": [
            {
                  "address": "<subnet 1 mask>" // Eg: 10.10.1.0/28 .This subnet should match with any existing subnet in vnet
            }
      ],
    "exclude": [
            {
                  "address": "<ip-from-included-subnets-that-should-be-excluded>" // If used with include, IP should be part of the subnet defined above. Without include, this could be any address within vnet range or any specific subnet range as a whole.
            }
      ]
  }
   },

//Endpoint as a URL
{

name: 'azure portal'

address: '<URL>'

   },

//Endpoint as an IP 
 {

    name: 'ip',

     address: '<IP>'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

   // Choose your protocol
   
    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: <threshold for checks failed %>,

     roundTripTimeMs: <threshold for RTT>

    }

   }, {

    name: 'https',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    httpConfiguration: {
    
     port: '<port of choice>'
  
    preferHTTPS: true // If port chosen is not 80 or 443
    
    method: 'GET', //Choose GET or POST
    
    path: '/', //Specify path for request
         
    requestHeaders: [
            {
              "name": "Content-Type",
              "value": "appication/json"
            }
          ],
          
    validStatusCodeRanges: [ "102", "200-202", "3xx" ], //Samples
          
    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, 
   {

    name: 'tcpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }

  ]

 }

} "
```

Dies ist der Bereitstellungsbefehl:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

## <a name="description-of-properties"></a>Beschreibung der Eigenschaften

* connectionMonitorName: Der Name der Verbindungsmonitorressource.

* SUB: Die Abonnement-ID des Abonnements, in dem Sie den Verbindungsmonitor erstellen möchten.

* NW: Die ID der Network Watcher-Ressource, in der der Verbindungsmonitor erstellt wird. 

* location: Die Region, in der der Verbindungsmonitor erstellt wird.

* Endpunkte
    * name: Der eindeutige Name für jeden Endpunkt.
    * resourceId: Bei Azure-Endpunkten bezieht sich die Ressourcen-ID auf die ID der Azure Resource Manager-Ressource für virtuelle Computer. Bei Azure-fremden Endpunkten bezieht sich die Ressourcen-ID auf die ID der Azure Resource Manager-Ressource für den Log Analytics-Arbeitsbereich, der mit Azure-fremden Agents verknüpft ist.
    * address: Dies trifft nur zu, wenn entweder keine Ressourcen-ID angegeben oder die Ressourcen-ID der Log Analytics-Arbeitsbereich ist. Bei Verwendung mit Log Analytics-Ressourcen-ID bezieht sich dies auf den FQDN des Agents, der für die Überwachung verwendet werden kann. Bei Verwendung ohne Ressourcen-ID kann dies die URL oder IP-Adresse eines beliebigen öffentlichen Endpunkts sein.
    * filter: Bei Azure-fremden Endpunkten verwenden Sie Filter, um Agents aus dem Log Analytics-Arbeitsbereich auszuwählen, die für die Überwachung in der Verbindungsmonitorressource verwendet werden. Wenn keine Filter festgelegt sind, können alle Agents, die zum Log Analytics-Arbeitsbereich gehören, für die Überwachung verwendet werden.
        * type: Legen Sie den Typ auf „Agent Address“ fest.
        * address: Legen Sie die Adresse als den FQDN Ihres lokalen Agents fest.

* Testgruppen
    * name: Geben Sie der Testgruppe einen Namen.
    * testConfigurations: Die Testkonfigurationen, die darauf basieren, welche Quellendpunkte mit Zielendpunkten verbunden sind.
    * sources: Treffen Sie eine Auswahl aus den oben erstellten Endpunkten. Auf Azure-basierten Quellendpunkten muss die Azure Network Watcher-Erweiterung installiert sein, und bei nicht auf Azure basierenden Quellendpunkten muss der Azure Log Analytics-Agent installiert sein. Informationen zum Installieren eines Agents für Ihre Quelle finden Sie unter [Installieren von Überwachungs-Agents](./connection-monitor-overview.md#install-monitoring-agents).
    * destinations: Treffen Sie eine Auswahl aus den oben erstellten Endpunkten. Sie können die Konnektivität mit Azure-VMs oder anderen Endpunkten (öffentliche IP-Adresse, URL oder FQDN) überwachen, indem Sie diese als Ziele festlegen. In einer einzelnen Testgruppe können Sie Azure-VMs, Office 365-URLs, Dynamics 365-URLs und benutzerdefinierte Endpunkte hinzufügen.
    * disable: Verwenden Sie dieses Feld, um die Überwachung für alle in der Testgruppe angegebenen Quellen und Ziele zu deaktivieren.

* Testkonfigurationen
    * name: Der Name der Testkonfiguration.
    * testFrequencySec: Geben Sie an, wie oft die Quellen die Ziele über das angegebene Protokoll und den angegebenen Port pingen. Sie können Intervalle von 30 Sekunden, 1 Minute, 5 Minuten, 15 Minuten oder 30 Minuten auswählen. Die Quellen testen die Konnektivität mit den Zielen auf Grundlage des von Ihnen ausgewählten Werts. Wenn Sie z. B. 30 Sekunden auswählen, überprüfen die Quellen die Konnektivität mit dem Ziel mindestens einmal alle 30 Sekunden.
    * protocol: Sie können TCP, ICMP, HTTP oder HTTPS auswählen. Je nach Protokoll können Sie einige protokollspezifische Konfigurationen vornehmen.
    
        * preferHTTPS: Geben Sie an, ob vorzugsweise HTTPS statt HTTP verwendet werden soll, wenn der verwendete Port weder 80 noch 443 ist.
        * port: Geben Sie den Zielport Ihrer Wahl an.
        * disableTraceRoute: Dies betrifft Testkonfigurationen mit dem TCP- oder ICMP-Protokoll. Dadurch werden von Quellen weder Topologie noch Hop-by-Hop-Roundtripzeit ermittelt.
        * method: Dies betrifft Testkonfigurationen mit dem HTTP-Protokoll. Wählen Sie die HTTP-Anforderungsmethode (GET oder POST) aus.
        * path: Geben Sie Pfadparameter an, die an die URL angefügt werden sollen.
        * validStatusCodes: Wählen Sie die anwendbaren Statuscodes aus. Wenn der Antwortcode nicht mit dieser Liste übereinstimmt, erhalten Sie eine Diagnosemeldung.
        * requestHeaders: Geben Sie benutzerdefinierte Zeichenfolgen für Anforderungsheader an, die an das Ziel übermittelt werden.
        
    * successThreshold: Sie können Schwellenwerte für die folgenden Netzwerkparameter festlegen:
        * checksFailedPercent: Legen Sie den Prozentsatz der Überprüfungen fest, die Fehler aufweisen können, wenn Quellen die Konnektivität mit Zielen anhand der von Ihnen angegebenen Kriterien überprüfen. Beim TCP- oder ICMP-Protokoll kann der Prozentsatz der Überprüfungen mit Fehlern dem Prozentsatz der Paketverluste gleichgesetzt werden. Beim HTTP-Protokoll stellt dieses Feld den Prozentsatz von HTTP-Anforderungen dar, die keine Antwort erhielen.
        * roundTripTimeMs: Legen Sie die Roundtripzeit in Millisekunden fest, die von Quellen zum Herstellen einer Verbindung mit dem Ziel über die Testkonfiguration benötigt werden darf.

## <a name="scale-limits"></a>Skalierungslimits

Für Verbindungsmonitore gelten die folgenden Skalierungslimits:

* Maximale Anzahl von Verbindungsmonitoren pro Abonnement und Region: 100
* Maximale Anzahl von Testgruppen pro Verbindungsmonitor: 20
* Maximale Anzahl von Quellen und Zielen pro Verbindungsmonitor: 100
* Maximale Anzahl von Testkonfigurationen pro Verbindungsmonitor: 20 über ARMClient

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [Überwachungsdaten analysieren und Warnungen festlegen](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts).
* Erfahren Sie, wie Sie [Probleme in Ihrem Netzwerk diagnostizieren](./connection-monitor-overview.md#diagnose-issues-in-your-network).