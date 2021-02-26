---
title: Scheduled Events für Linux-VMs in Azure
description: Planen Sie Ereignisse mit dem Azure-Metadatendienst für Ihre virtuellen Linux-Computer.
author: EricRadzikowskiMSFT
ms.service: virtual-machines-linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2020
ms.author: ericrad
ms.reviewer: mimckitt
ms.openlocfilehash: 3bda1e2076e29fc1365bfc236adc9071db2564a1
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104738"
---
# <a name="azure-metadata-service-scheduled-events-for-linux-vms"></a>Instance Metadata Service: Scheduled Events für Linux-VMs

Scheduled Events ist ein Azure-Metadatendienst, der Ihren Anwendungen Zeit zur Vorbereitung auf die Wartung virtueller Computer gibt. Er stellt Informationen zu geplanten Wartungsereignissen (z.B. Neustart) bereit, damit Ihre Anwendung sich darauf vorbereiten und Unterbrechungen begrenzen kann. Der Dienst steht für alle Azure-VM-Typen zur Verfügung, einschließlich PaaS und IaaS unter Windows und Linux. 

Informationen zu geplanten Ereignissen unter Windows finden Sie unter [Geplante Ereignisse für virtuelle Windows-Computer](../windows/scheduled-events.md).

> [!Note] 
> Scheduled Events ist in allen Azure-Regionen allgemein verfügbar. Informationen zu aktuellen Releases finden Sie unter [Version und regionale Verfügbarkeit](#version-and-region-availability).

## <a name="why-use-scheduled-events"></a>Was spricht für die Verwendung geplanter Ereignisse?

Viele Anwendungen können von der Vorbereitungszeit auf die VM-Wartung profitieren. Die Zeit kann genutzt werden, um anwendungsspezifische Aufgaben durchzuführen, die die Verfügbarkeit, Zuverlässigkeit und Wartungsfreundlichkeit verbessern. Dazu gehören u.a.: 

- Prüfpunkt und Wiederherstellung
- Verbindungsausgleich
- Failover des primären Replikats
- Entfernung aus einem Lastenausgleichspool
- Ereignisprotokollierung
- Ordnungsgemäßes Herunterfahren

Mit dem Feature für geplante Ereignisse kann Ihre Anwendung erkennen, wann eine Wartung erfolgt, und so Aufgaben auslösen, um die Auswirkungen abzumildern.  

Geplante Ereignisse umfasst Ereignisse in den folgenden Anwendungsfällen:

- [Von der Plattform ausgelöste Wartung](../maintenance-and-updates.md?bc=/azure/virtual-machines/linux/breadcrumb/toc.json&toc=/azure/virtual-machines/linux/toc.json) (z. B. Neustart des virtuellen Computers, Livemigration oder Updates für den Host mit Speicherbeibehaltung)
- Der virtuelle Computer wird auf [heruntergestufter Hosthardware](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events) ausgeführt, deren baldiger Ausfall erwartet wird.
- Benutzerinitiierte Wartung (z.B. Neustart oder erneute Bereitstellung eines virtuellen Computers durch den Benutzer)
- Instanzentfernungen von [Spot-VM](../spot-vms.md) und [Spot-Skalierungsgruppen](../../virtual-machine-scale-sets/use-spot.md)

## <a name="the-basics"></a>Die Grundlagen  

  Der Metadatendienst macht Informationen zu ausgeführten virtuellen Computern mithilfe eines innerhalb einer VM zugänglichen REST-Endpunkts verfügbar. Die Informationen stehen über eine nicht routingfähige IP-Adresse bereit, die außerhalb der VM nicht verfügbar gemacht wird.

### <a name="scope"></a>`Scope`
Geplante Ereignisse werden übermittelt an:

- Eigenständige virtuelle Computer
- Alle VMs in einem Clouddienst
- Alle VMs in einer Verfügbarkeitsgruppe
- Alle VMs in einer Verfügbarkeitszone 
- Alle VMs in einer Gruppe für die Skalierungsgruppenplatzierung 

> [!NOTE]
> Für VMs in einer Verfügbarkeitszone gilt, dass geplante Ereignisse an einzelne VMs in der Zone gesendet werden.
> Haben Sie z. B. 100 VMs in einer Verfügbarkeitsgruppe, und wird eine dieser VMs aktualisiert, wird das geplante Ereignis an alle 100 gesendet. Haben Sie dagegen 100 einzelne VMs in einer Zone, wird das Ereignis nur an die VM gesendet, die betroffen ist.

Überprüfen Sie deshalb anhand des Felds `Resources` in einem Ereignis, welche VMs betroffen sein werden.

### <a name="endpoint-discovery"></a>Endpunktermittlung
Für virtuelle Computer in VNETs ist der Metadatendienst über eine statische, nicht routingfähige IP-Adresse (`169.254.169.254`) verfügbar. Der vollständige Endpunkt für die neueste Version von Scheduled Events ist wie folgt: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01`

Wenn der virtuelle Computer nicht innerhalb eines virtuellen Netzwerks erstellt wird (Standard für Clouddienste und klassische virtuelle Computer), ist zusätzliche Logik erforderlich, um die zu verwendende IP-Adresse zu ermitteln. In diesem Beispiel erfahren Sie, wie Sie [den Hostendpunkt ermitteln](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Version und regionale Verfügbarkeit
Das Feature für geplante Ereignisse ist versionsspezifisch. Die Versionen sind obligatorisch. Die aktuelle Version ist `2019-08-01`.

| Version | Releasetyp | Regions | Versionsinformationen | 
| - | - | - | - | 
| 2019-08-01 | Allgemeine Verfügbarkeit | All | <li> EventSource-Unterstützung hinzugefügt |
| 01.04.2019 | Allgemeine Verfügbarkeit | All | <li> Unterstützung der Ereignisbeschreibung hinzugefügt |
| 2019-01-01 | Allgemeine Verfügbarkeit | All | <li> Unterstützung für EventType „Terminate“ von VM-Skalierungsgruppen hinzugefügt |
| 2017-11-01 | Allgemeine Verfügbarkeit | All | <li> Unterstützung für die Entfernung von Spot-VMs hinzugefügt (EventType „Preempt“)<br> | 
| 2017-08-01 | Allgemeine Verfügbarkeit | All | <li> Ein vorangestellter Unterstrich wurde aus Ressourcennamen virtueller Iaas-Computer entfernt.<br><li>Der Metadatenheader wird als Voraussetzung für alle Anforderungen erzwungen. | 
| 2017-03-01 | Vorschau | All | <li>Erste Veröffentlichung |


> [!NOTE] 
> In früheren Vorschauversionen von geplanten Ereignissen wird {latest} als „api-version“ unterstützt. Dieses Format wird nicht mehr unterstützt und wird zukünftig veraltet sein.

### <a name="enabling-and-disabling-scheduled-events"></a>Aktivieren und Deaktivieren von Scheduled Events
Scheduled Events wird für Ihren Dienst aktiviert, wenn Sie zum ersten Mal Ereignisse anfordern. Beim ersten Aufruf ist eine um bis zu zwei Minuten verzögerte Antwort zu erwarten.

Scheduled Events wird für Ihren Dienst deaktiviert, wenn 24 Stunden lang keine Anforderung erfolgt.

### <a name="user-initiated-maintenance"></a>Vom Benutzer ausgelöste Wartung
Eine vom Benutzer über das Azure-Portal, die API, die CLI oder PowerShell initiierte Wartung eines virtuellen Computers führt zu einem geplanten Ereignis. Sie können anschließend die Logik zur Vorbereitung auf Wartungsmaßnahmen in Ihrer Anwendung testen und Ihre Anwendung auf die vom Benutzer initiierte Wartung vorbereiten.

Wenn Sie eine VM neu starten, wird ein Ereignis vom Typ `Reboot` geplant. Wenn Sie eine VM neu bereitstellen, wird ein Ereignis vom Typ `Redeploy` geplant.

## <a name="use-the-api"></a>Verwenden der API

### <a name="headers"></a>Header
Beim Abfragen des Metadatendiensts müssen Sie den Header `Metadata:true` angeben, um sicherzustellen, dass die Anforderung nicht unbeabsichtigt umgeleitet wurde. Der Header `Metadata:true` ist für alle Anforderungen für Geplante Ereignisse erforderlich. Wird der Header nicht in die Anforderung eingefügt, erhalten Sie vom Metadatendienst die Antwort „Ungültige Anforderung“.

### <a name="query-for-events"></a>Abfragen von Ereignissen
Sie können geplante Ereignisse abfragen, indem Sie den folgenden Aufruf ausführen:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Eine Antwort enthält ein Array geplanter Ereignisse. Ein leeres Array bedeutet, dass derzeit keine Ereignisse geplant sind.
Sofern geplante Ereignisse vorliegen, enthält die Antwort ein Array mit Ereignissen. 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt" | "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},       
            "Description": {eventDescription},
            "EventSource" : "Platform" | "User",
        }
    ]
}
```

### <a name="event-properties"></a>Ereigniseigenschaften
|Eigenschaft  |  BESCHREIBUNG |
| - | - |
| EventId | Global eindeutiger Bezeichner für dieses Ereignis <br><br> Beispiel: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Auswirkungen dieses Ereignisses <br><br> Werte: <br><ul><li> `Freeze`: Das Anhalten des virtuellen Computers für einige Sekunden ist geplant. Der Prozessor und die Netzwerkverbindung werden möglicherweise angehalten, es gibt jedoch keine Auswirkungen auf den Arbeitsspeicher oder geöffnete Dateien.<li>`Reboot`: Der Neustart der VM ist geplant (der flüchtige Arbeitsspeicher geht verloren). <li>`Redeploy`: Das Verschieben der VM auf einen anderen Knoten ist geplant (kurzlebige Datenträger gehen verloren). <li>`Preempt`: Spot-VM wird gelöscht (kurzlebige Datenträger gehen verloren). <li> `Terminate`: Das Löschen des virtuellen Computers ist geplant. |
| ResourceType | Typ der Ressource, auf die sich dieses Ereignis auswirkt. <br><br> Werte: <ul><li>`VirtualMachine`|
| Ressourcen| Liste der Ressourcen, auf die sich dieses Ereignis auswirkt. Die Liste enthält garantiert Computer aus maximal einer [Updatedomäne](../manage-availability.md), muss jedoch nicht alle Computer in dieser Domäne enthalten. <br><br> Beispiel: <br><ul><li> [„FrontEnd_IN_0“, „BackEnd_IN_0“] |
| EventStatus | Status dieses Ereignisses <br><br> Werte: <ul><li>`Scheduled`: Dieses Ereignis erfolgt nach dem in der `NotBefore`-Eigenschaft angegebenen Zeitpunkt.<li>`Started`: Dieses Ereignis wurde gestartet.</ul> `Completed` oder ein ähnlicher Status wird nie angegeben. Das Ergebnis wird nicht länger zurückgegeben, wenn es abgeschlossen wurde.
| NotBefore| Zeitpunkt, nach dem dieses Ereignis gestartet werden kann. <br><br> Beispiel: <br><ul><li> Mo., 19. September 2016 18:29:47 GMT  |
| BESCHREIBUNG | Beschreibung dieses Ereignisses. <br><br> Beispiel: <br><ul><li> Der Hostserver befindet sich im Wartungsmodus. |
| EventSource | Initiator des Ereignisses. <br><br> Beispiel: <br><ul><li> `Platform`: Dieses Ereignis wird durch die Plattform initiiert. <li>`User`: Dieses Ereignis wurde durch den Benutzer initiiert. |

### <a name="event-scheduling"></a>Ereigniszeitplanung
Jedes Ereignis wird, basierend auf dem Ereignistyp, mit einer minimalen Vorlaufzeit geplant. Diese Zeit ist in der `NotBefore`-Eigenschaft eines Ereignisses angegeben. 

|EventType  | Mindestzeitspanne |
| - | - |
| Freeze| 15 Minuten |
| Reboot | 15 Minuten |
| Erneute Bereitstellung | 10 Minuten |
| Preempt | 30 Sekunden |
| Terminate | [Vom Benutzer konfigurierbar](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications): 5 bis 15 Minuten |

> [!NOTE] 
> In einigen Fällen kann Azure den Hostausfall aufgrund von heruntergestufter Hardware vorhersagen und versucht, die Unterbrechung Ihres Diensts durch Planen einer Migration zu minimieren. Betroffene virtuelle Computer erhalten ein geplantes Ereignis mit einer `NotBefore`-Angabe, die in der Regel einige Tage in der Zukunft liegt. Der tatsächliche Zeitpunkt variiert je nach der vorhergesagten Risikobewertung des Ausfalls. Azure versucht, wenn möglich 7 Tage im Voraus zu benachrichtigen, aber der tatsächliche Zeitpunkt variiert und kann früher sein, wenn die Vorhersage lautet, dass eine hohe Wahrscheinlichkeit besteht, dass die Hardware bald ausfallen wird. Um das Risiko für Ihren Dienst zu verringern, falls vor der vom System initiierten Migration die Hardware ausfällt, empfehlen wir Ihnen, Ihren virtuellen Computer so bald wie möglich selbst erneut bereitzustellen.

### <a name="polling-frequency"></a>Abrufhäufigkeit

Sie können den Endpunkt beliebig häufig (oder selten) auf Updates abfragen. Je mehr Zeit zwischen den Anforderungen vergeht, desto mehr Zeit verlieren Sie möglicherweise für das Reagieren auf ein bevorstehendes Ereignis. Die meisten Ereignisse werden 5 bis 15 Minuten im Voraus angekündigt, manchmal bleibt jedoch nur ein Vorlauf von 30 Sekunden. Damit Sie so viel Zeit wie möglich haben, um Abhilfemaßnahmen zu ergreifen, empfiehlt es sich, den Dienst einmal pro Sekunde abzufragen.

### <a name="start-an-event"></a>Starten eines Ereignisses 

Nachdem Sie von einem anstehenden Ereignis erfahren und Ihre Logik für ein ordnungsgemäßes Herunterfahren vervollständigt haben, können Sie das ausstehende Ereignis genehmigen, indem Sie einen Aufruf des Typs `POST` an den Metadatendienst mit der `EventId` ausführen. Dieser Aufruf informiert Azure darüber, dass die minimale Benachrichtigungszeit verkürzt werden kann (falls möglich). 

Das folgende JSON-Beispiel wird im `POST`-Anforderungstext erwartet. Die Anforderung sollte eine Liste mit `StartRequests` enthalten. Jede `StartRequest` enthält die `EventId` für das Ereignis, das Sie beschleunigen möchten:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Bash-Beispiel
```
curl -H Metadata:true -X POST -d '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

> [!NOTE] 
> Durch das Bestätigen eines Ereignisses kann dieses für alle `Resources` im Ereignis fortgesetzt werden, nicht nur für die VM, die das Ereignis bestätigt. Daher können Sie einen Leiter zur Koordinierung der Bestätigung auswählen, beispielsweise einfach den ersten Computer im Feld `Resources`.

## <a name="python-sample"></a>Python-Beispiel 

Im folgenden Beispiel fragt der Metadatenserver geplante Ereignisse ab und genehmigt jedes ausstehende Ereignis:

```python
#!/usr/bin/python

import json
import socket
import urllib2

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01"
this_host = socket.gethostname()


def get_scheduled_events():
    req = urllib2.Request(metadata_url)
    req.add_header('Metadata', 'true')
    resp = urllib2.urlopen(req)
    data = json.loads(resp.read())
    return data


def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ", "_")
    description = evt['Description']
    eventSource = evt['EventSource']
        if this_host in resources:
            print("+ Scheduled Event. This host " + this_host +
                " is scheduled for " + eventtype + 
        " by " + eventSource + 
        " with description " + description +
        " not before " + notbefore)
            # Add logic for handling events here


def main():
    data = get_scheduled_events()
    handle_scheduled_events(data)


if __name__ == '__main__':
    main()
```

## <a name="next-steps"></a>Nächste Schritte 
- Auf Azure Friday können Sie eine [Demo zu Scheduled Events](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) ansehen. 
- Überprüfen Sie die Codebeispiele für Scheduled Events im [GitHub-Repository „Azure Instance Metadata Scheduled Events“](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm).
- Erfahren Sie mehr über die verfügbaren APIs im [Instanz-Metadatendienst](instance-metadata-service.md).
- Erfahren Sie mehr über [Geplante Wartung für virtuelle Linux-Computer in Azure](../maintenance-and-updates.md?bc=/azure/virtual-machines/linux/breadcrumb/toc.json&toc=/azure/virtual-machines/linux/toc.json).
