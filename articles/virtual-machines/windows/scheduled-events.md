---
title: Geplante Ereignisse für Windows-VMs in Azure | Microsoft-Dokumentation
description: Geplante Ereignisse mit dem Azure-Metadatendienst für Ihre virtuellen Windows-Computer.
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: ''
author: ericrad
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: e6a376803d8617e01ee279e40a33f6c1c3b748fd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65508199"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Instance Metadata Service: Scheduled Events für Windows-VMs

Geplante Ereignisse ist ein Azure-Metadatendienst, der Ihren Anwendungen Zeit zur Vorbereitung auf die Wartung virtueller Computer gibt. Er stellt Informationen zu geplanten Wartungsereignissen (z.B. Neustart) bereit, damit Ihre Anwendung sich darauf vorbereiten und Unterbrechungen begrenzen kann. Der Dienst steht für sämtliche Arten virtueller Azure-Computer zur Verfügung, einschließlich PaaS und IaaS unter Windows und Linux. 

Informationen zu geplanten Ereignissen unter Linux finden Sie unter [Geplante Ereignisse für virtuelle Linux-Computer ](../linux/scheduled-events.md).

> [!Note] 
> Scheduled Events ist in allen Azure-Regionen allgemein verfügbar. Informationen zu aktuellen Releases finden Sie unter [Version und regionale Verfügbarkeit](#version-and-region-availability).

## <a name="why-scheduled-events"></a>Warum geplante Ereignisse?

Viele Anwendungen können von der Vorbereitungszeit auf die Wartung virtueller Computer profitieren. Die Zeit kann genutzt werden, um anwendungsspezifische Aufgaben durchzuführen, die die Verfügbarkeit, Zuverlässigkeit und Wartungsfreundlichkeit verbessern. Dazu gehören u.a.: 

- Prüfpunkt und Wiederherstellung
- Verbindungsausgleich
- Primäres Replikatfailover 
- Entfernung aus dem Lastenausgleichspool
- Ereignisprotokollierung
- Ordnungsgemäßes Herunterfahren 

Mit Geplante Ereignisse kann Ihre Anwendung erkennen, wann eine Wartung erfolgt, und so Aufgaben auslösen, um die Auswirkungen zu beschränken. Mit geplanten Ereignissen erhält Ihr virtueller Computer ein Mindestmaß an Zeit, bevor die Wartungsaktivität ausgeführt wird. Details dazu finden Sie weiter unten im Abschnitt „Ereigniszeitplanung“.

Geplante Ereignisse umfasst Ereignisse in den folgenden Anwendungsfällen:
- [Von der Plattform ausgelöste Wartung](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates) (z. B. Neustart des virtuellen Computers, Livemigration oder Updates für den Host mit Speicherbeibehaltung)
- Heruntergestufte Hardware
- Benutzerinitiierte Wartung (z.B. Neustart oder erneute Bereitstellung eines virtuellen Computers durch den Benutzer)
- [Entfernung von VMs mit niedriger Priorität](https://azure.microsoft.com/blog/low-priority-scale-sets) in Skalierungsgruppen

## <a name="the-basics"></a>Die Grundlagen  

Der Azure-Metadatendienst macht Informationen zu ausgeführten virtuellen Computern mithilfe eines innerhalb einer VM zugänglichen REST-Endpunkts verfügbar. Die Informationen stehen über eine nicht routbare IP-Adresse bereit, die außerhalb der VM nicht verfügbar gemacht wird.

### <a name="endpoint-discovery"></a>Endpunktermittlung
Für virtuelle Computer in VNETs ist der Metadatendienst über eine statische, nicht routingfähige IP-Adresse (`169.254.169.254`) verfügbar. Der vollständige Endpunkt für die neueste Version von Scheduled Events ist wie folgt: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01`

Wenn der virtuelle Computer nicht innerhalb eines virtuellen Netzwerks erstellt wird (Standard für Clouddienste und klassische virtuelle Computer), ist zusätzliche Logik erforderlich, um die zu verwendende IP-Adresse zu ermitteln. In diesem Beispiel erfahren Sie, wie Sie [den Hostendpunkt ermitteln](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Version und regionale Verfügbarkeit
Der Dienst Geplante Ereignisse ist versionsspezifisch. Versionen sind obligatorisch, und die aktuelle Version ist `2017-11-01`.

| Version | Releasetyp | Regions | Versionsinformationen | 
| - | - | - | - |
| 2017-11-01 | Allgemeine Verfügbarkeit | Alle | <li> Unterstützung für die Entfernung von VMs mit niedriger Priorität hinzugefügt (EventType 'Preempt')<br> | 
| 2017-08-01 | Allgemeine Verfügbarkeit | Alle | <li> Ein vorangestellter Unterstrich wurde aus Ressourcennamen virtueller Iaas-Computer entfernt.<br><li>Der Metadatenheader wird als Voraussetzung für alle Anforderungen erzwungen. | 
| 2017-03-01 | Vorschau | Alle |<li>Erste Version

> [!NOTE] 
> In früheren Vorschauversionen von geplanten Ereignissen wird {latest} als „api-version“ unterstützt. Dieses Format wird nicht mehr unterstützt und wird zukünftig veraltet sein.

### <a name="enabling-and-disabling-scheduled-events"></a>Aktivieren und Deaktivieren von Scheduled Events
Scheduled Events wird für Ihren Dienst aktiviert, wenn Sie zum ersten Mal Ereignisse anfordern. Beim ersten Aufruf ist eine um bis zu zwei Minuten verzögerte Antwort zu erwarten. Sie sollten den Endpunkt regelmäßig abfragen, um anstehende Wartungsereignisse sowie den Status von gerade ausgeführten Wartungsaktivitäten zu ermitteln.

Scheduled Events wird für Ihren Dienst deaktiviert, wenn 24 Stunden lang keine Anforderung erfolgt.

### <a name="user-initiated-maintenance"></a>Vom Benutzer initiierte Wartung
Eine vom Benutzer über das Azure-Portal, die API, die Befehlszeilenschnittstelle oder PowerShell initiierte Wartung eines virtuellen Computers führt zu einem geplanten Ereignis. So können Sie die Logik zur Vorbereitung auf Wartungsmaßnahmen in Ihrer Anwendung testen und Ihre Anwendung auf die vom Benutzer initiierte Wartung vorbereiten.

Das Neustarten eines virtuellen Computers plant ein Ereignis vom Typ `Reboot`. Das erneute Bereitstellen eines virtuellen Computers plant ein Ereignis vom Typ `Redeploy`.

## <a name="using-the-api"></a>Verwenden der API

### <a name="headers"></a>Header
Beim Abfragen des Metadatendiensts müssen Sie den Header `Metadata:true` angeben, um sicherzustellen, dass die Anforderung nicht unbeabsichtigt umgeleitet wurde. Der Header `Metadata:true` ist für alle Anforderungen für Geplante Ereignisse erforderlich. Wird der Header nicht in die Anforderung eingefügt, erhalten Sie vom Metadatendienst die Antwort „Ungültige Anforderung“.

### <a name="query-for-events"></a>Abfragen von Ereignissen
Sie können geplante Ereignisse abfragen, indem Sie einfach folgenden Aufruf ausführen:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01 -H @{"Metadata"="true"}
```

Eine Antwort enthält ein Array geplanter Ereignisse. Ein leeres Array bedeutet, dass derzeit keine Ereignisse geplant sind.
Sofern geplante Ereignisse vorliegen, enthält die Antwort ein Array mit Ereignissen: 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
DocumentIncarnation ist ein ETag und bietet eine einfache Möglichkeit, um zu untersuchen, ob sich die Ereignisnutzlast seit der letzten Abfrage geändert hat.

### <a name="event-properties"></a>Ereigniseigenschaften
|Eigenschaft  |  BESCHREIBUNG |
| - | - |
| EventId | Global eindeutiger Bezeichner für dieses Ereignis <br><br> Beispiel: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Auswirkungen dieses Ereignisses <br><br> Werte: <br><ul><li> `Freeze`: Das Anhalten des virtuellen Computers für einige Sekunden ist geplant. Der Prozessor und die Netzwerkverbindung werden möglicherweise angehalten, es gibt jedoch keine Auswirkungen auf den Arbeitsspeicher oder geöffnete Dateien. <li>`Reboot`: Der Neustart der VM ist geplant (der flüchtige Arbeitsspeicher geht verloren). <li>`Redeploy`: Das Verschieben der VM auf einen anderen Knoten ist geplant (kurzlebige Datenträger gehen verloren). <li>`Preempt`: Virtueller Computer mit niedriger Priorität wird gelöscht (kurzlebige Datenträger gehen verloren).|
| ResourceType | Typ der Ressource, auf die sich dieses Ereignis auswirkt <br><br> Werte: <ul><li>`VirtualMachine`|
| Ressourcen| Liste von Ressourcen, auf die sich dieses Ereignis auswirkt. Diese Liste enthält garantiert Computer aus maximal einer [Updatedomäne](manage-availability.md), muss jedoch nicht alle Computer in dieser Domäne enthalten. <br><br> Beispiel: <br><ul><li> [„FrontEnd_IN_0“, „BackEnd_IN_0“] |
| Ereignisstatus | Status dieses Ereignisses <br><br> Werte: <ul><li>`Scheduled`: Dieses Ereignis erfolgt nach dem in der `NotBefore`-Eigenschaft angegebenen Zeitpunkt.<li>`Started`: Dieses Ereignis wurde gestartet.</ul> `Completed` oder ähnliche Statusangaben werden niemals bereitgestellt. Das Ereignis wird nicht mehr zurückgegeben, nachdem es abgeschlossen ist.
| NotBefore| Zeit, nach der dieses Ereignis gestartet werden kann <br><br> Beispiel: <br><ul><li> Mo., 19. September 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>Ereigniszeitplanung
Jedes Ereignis erfolgt dem Zeitplan nach, basierend auf dem Ereignistyp, eine Mindestzeitspanne in der Zukunft. Diese Zeit ist in der `NotBefore`-Eigenschaft eines Ereignisses angegeben. 

|EventType  | Mindestzeitspanne |
| - | - |
| Freeze| 15 Minuten |
| Reboot | 15 Minuten |
| Erneute Bereitstellung | 10 Minuten |
| Preempt | 30 Sekunden |

### <a name="event-scope"></a>Ereignisbereich     
Geplante Ereignisse werden übermittelt an:
 - Eigenständige virtuelle Computer
 - Alle virtuellen Computer in einem Clouddienst      
 - Alle virtuellen Computer in einer Verfügbarkeitsgruppe      
 - Alle virtuellen Computer in einer Skalierungsgruppen-Platzierungsgruppe         

Daher sollten Sie das Feld `Resources` in einem Ereignis überprüfen, um zu ermitteln, welche VMs betroffen sein werden. 

### <a name="starting-an-event"></a>Starten eines Ereignisses 

Sobald Sie von einem anstehenden Ereignis erfahren und Ihre Logik für ein ordnungsgemäßes Herunterfahren vervollständigt haben, können Sie ein ausstehendes Ereignis genehmigen, indem Sie einen Aufruf des Typs `POST` an den Metadatendienst mit der `EventId` ausführen. Dies zeigt Azure an, dass die minimale Benachrichtigungszeit verkürzt werden kann (wenn möglich). 

Im Folgenden finden Sie den in der `POST`-Anforderung erwarteten JSON-Code. Die Anforderung sollte eine Liste mit `StartRequests` enthalten. Jede `StartRequest` enthält die `EventId` für das Ereignis, das Sie beschleunigen möchten:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="powershell"></a>PowerShell
```
curl -H @{"Metadata"="true"} -Method POST -Body '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2017-11-01
```

> [!NOTE] 
> Durch das Bestätigen eines Ereignisses kann dieses für alle `Resources` im Ereignis fortgesetzt werden, nicht nur für den virtuellen Computer, der das Ereignis bestätigt. Daher sollten Sie möglicherweise einen Leiter zur Koordinierung der Bestätigung auswählen, beispielsweise einfach den ersten Computer im Feld `Resources`.


## <a name="powershell-sample"></a>PowerShell-Beispiel 

Im folgenden Beispiel fragt der Metadatenserver geplante Ereignisse ab und genehmigt jedes ausstehende Ereignis.

```powershell
# How to get scheduled events 
function Get-ScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function Approve-ScheduledEvent($eventId, $uri)
{
    # Create the Scheduled Events Approval Document
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests} 
    
    # Convert to JSON string
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

function Handle-ScheduledEvents($scheduledEvents)
{
    # Add logic for handling events here
}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events URI for a VNET-enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-11-01' -f $localHostIP 

# Get events
$scheduledEvents = Get-ScheduledEvents $scheduledEventURI

# Handle events however is best for your service
Handle-ScheduledEvents $scheduledEvents

# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
        Approve-ScheduledEvent $event.EventId $scheduledEventURI 
    }
}
``` 

## <a name="next-steps"></a>Nächste Schritte 

- Auf Azure Friday können Sie eine [Demo zu Scheduled Events](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) ansehen. 
- Sehen Sie sich die Codebeispiele für Scheduled Events im [Azure Instance Metadata Scheduled Events GitHub Repository](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm) (GitHub-Repository für Scheduled Events für Azure-Instanzmetadaten) an.
- Erfahren Sie mehr über die APIs im [Instanz-Metadatendienst](instance-metadata-service.md).
- Erfahren Sie mehr über [Geplante Wartung für virtuelle Windows-Computer in Azure](planned-maintenance.md).
