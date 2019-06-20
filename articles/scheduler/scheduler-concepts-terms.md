---
title: Konzepte, Terminologie und Entitäten – Microsoft Azure Scheduler | Microsoft-Dokumentation
description: Informationen über die Konzepte, Terminologie und Entitätshierarchie (einschließlich Aufträge und Auftragssammlungen) in Microsoft Azure Scheduler
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 3ef16fab-d18a-48ba-8e56-3f3e0a1bcb92
ms.topic: conceptual
ms.date: 08/18/2016
ms.openlocfilehash: d701fba39685d781d1a4c2d8a6cf194ca7eb2908
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60530937"
---
# <a name="concepts-terminology-and-entities-in-azure-scheduler"></a>Konzepte, Terminologie und Entitäten in Microsoft Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersetzt Azure Scheduler, der eingestellt wird. Zum Planen von Aufträgen sollten Sie stattdessen [Azure Logic Apps ausprobieren](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

## <a name="entity-hierarchy"></a>Entitätshierarchie

Die folgenden Hauptentitäten oder Ressourcen werden von der Azure Scheduler-REST-API verfügbar gemacht und verwendet:

| Entität | BESCHREIBUNG |
|--------|-------------|
| **Job** | Definiert eine einzelne wiederkehrende Aktion mit einfachen oder komplexen Ausführungsstrategien. Beispiele für solche Aktionen sind HTTP-Anforderungen, Storage-Warteschlangenanforderungen, Service Bus-Warteschlangenanforderungen oder Service Bus-Themenanforderungen. | 
| **Auftragssammlung** | Enthält eine Gruppe von Aufträgen und dient zum Verwalten von Einstellungen, Kontingenten und Drosselungen für die Aufträge in der Sammlung. Als Besitzer eines Azure-Abonnements können Sie Auftragssammlungen erstellen und Aufträge basierend auf ihren Nutzungs- oder Anwendungsgrenzen zusammenfassen. Eine Auftragssammlung besitzt die folgenden Attribute: <p>- Beschränkt auf eine Region. <br>- Ermöglicht das Erzwingen von Kontingenten, sodass Sie die Nutzung für alle Aufträge in einer Sammlung einschränken können. <br>- Kontingente beinhalten „MaxJobs“ und „MaxRecurrence“. | 
| **Auftragsverlauf** | Beschreibt die Details für eine Auftragsausführung, beispielsweise den Status und beliebige Antwortdetails. |
||| 

## <a name="entity-management"></a>Entitätsverwaltung

Allgemein betrachtet macht die Scheduler-REST-API die folgenden Vorgänge zum Verwalten von Entitäten verfügbar.

### <a name="job-management"></a>Auftragsverwaltung

Unterstützt Vorgänge zum Erstellen und Bearbeiten von Aufträgen. Alle Aufträge müssen einer vorhandenen Auftragssammlung angehören. Eine implizite Erstellung ist folglich nicht möglich. Weitere Informationen finden Sie unter [Scheduler REST API – Jobs](https://docs.microsoft.com/rest/api/scheduler/jobs) (Scheduler-REST-API – Aufträge). Die URI-Adresse für diese Vorgänge lautet wie folgt:

```
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}
```

### <a name="job-collection-management"></a>Auftragssammlungsverwaltung

Unterstützt Vorgänge zum Erstellen und Bearbeiten von Aufträgen und Auftragssammlungen, denen Kontingente und gemeinsame Einstellungen zugeordnet sind. Kontingente geben beispielsweise die maximale Anzahl von Aufträgen und das kleinste Wiederholungsintervall an. Weitere Informationen finden Sie unter [Scheduler REST API – Job Collections](https://docs.microsoft.com/rest/api/scheduler/jobcollections) (Scheduler-REST-API – Auftragssammlungen). Die URI-Adresse für diese Vorgänge lautet wie folgt:

```
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}
```

### <a name="job-history-management"></a>Auftragsverlaufsverwaltung

Unterstützt den GET-Vorgang zum Abrufen des Auftragsausführungsverlaufs für 60 Tage. Dieser enthält unter anderem die verstrichene Zeit sowie die Ergebnisse der Auftragsausführung. Bietet Unterstützung für Abfragezeichenfolgenparameter zur Filterung auf der Grundlage des Zustands und Status. Weitere Informationen finden Sie unter [Scheduler REST API – Jobs – List Job History](https://docs.microsoft.com/rest/api/scheduler/jobs/listjobhistory) (Scheduler-REST-API – Aufträge – Auflisten des Auftragsverlaufs). Die URI-Adresse für diesen Vorgang lautet wie folgt:

```
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history
```

## <a name="job-types"></a>Auftragstypen

Azure Scheduler unterstützt mehrere Auftragstypen: 

* HTTP-Aufträge, einschließlich HTTPS-Aufträge, die SSL unterstützen, wenn Ihnen der Endpunkt für einen vorhandenen Dienst oder eine vorhandene Workload bekannt ist
* Storage-Warteschlangenaufträge für Workloads, die Storage-Warteschlangen verwenden (z. B. Veröffentlichung von Nachrichten in Storage-Warteschlangen)
* Service Bus-Warteschlangenaufträge für Workloads, die Service Bus-Warteschlangen verwenden
* Service Bus-Themenaufträge für Workloads, die Service Bus-Themen verwenden

## <a name="job-definition"></a>Auftragsdefinition

Allgemein betrachtet besteht ein Scheduler-Auftrag aus den folgenden grundlegenden Teilen:

* Die Aktion, die beim Auslösen des Auftragszeitgebers ausgeführt wird
* Optional: Die Zeit, zu der der Auftrag ausgeführt werden soll
* Optional: Die Angabe, wann und wie oft der Auftrag wiederholt werden soll
* Optional: Eine Fehleraktion, die bei einem Fehler der primären Aktion ausgeführt wird

Der Auftrag enthält auch vom System bereitgestellte Daten, beispielsweise die nächste geplante Ausführungszeit des Auftrags. Die Codedefinition des Auftrags ist ein Objekt im JSON-Format (JavaScript Object Notation), das folgende Elemente enthält:

| Element | Erforderlich | BESCHREIBUNG | 
|---------|----------|-------------| 
| [**startTime**](#start-time) | Nein | Die Startzeit für den Auftrag mit einem Zeitzonenoffset im [ISO 8601-Format](https://en.wikipedia.org/wiki/ISO_8601) | 
| [**action**](#action) | Ja | Die Details für die primäre Aktion (kann ein **errorAction**-Objekt enthalten) | 
| [**errorAction**](#error-action) | Nein | Die Details für die sekundäre Aktion, die ausgeführt wird, wenn bei der primären Aktion ein Fehler auftritt |
| [**recurrence**](#recurrence) | Nein | Die Details für einen Serienauftrag (z. B. Häufigkeit und Intervall) | 
| [**retryPolicy**](#retry-policy) | Nein | Die Details zur Wiederholungshäufigkeit für eine Aktion | 
| [**state**](#state) | Ja | Die Details für den aktuellen Zustand des Auftrags |
| [**status**](#status) | Ja | Die Details für den aktuellen Status des Auftrags (wird vom Dienst gesteuert) |
||||

Das folgende Beispiel zeigt eine umfassende Auftragsdefinition für eine HTTP-Aktion mit ausführlicheren Elementdetails, die an späterer Stelle beschrieben werden: 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z",
   "action": {
      "type": "Http",
      "request": {
         "uri": "http://contoso.com/some-method", 
         "method": "PUT",          
         "body": "Posting from a timer",
         "headers": {
            "Content-Type": "application/json"
         },
         "retryPolicy": { 
             "retryType": "None" 
         },
      },
      "errorAction": {
         "type": "Http",
         "request": {
            "uri": "http://contoso.com/notifyError",
            "method": "POST"
         }
      }
   },
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]
      },
      "count": 10,
      "endTime": "2012-11-04"
   },
   "state": "Disabled",
   "status": {
      "lastExecutionTime": "2007-03-01T13:00:00Z",
      "nextExecutionTime": "2007-03-01T14:00:00Z ",
      "executionCount": 3,
      "failureCount": 0,
      "faultedCount": 0
   }
}
```

<a name="start-time"></a>

## <a name="starttime"></a>startTime

Im **startTime**-Objekt können Sie die Startzeit und einen Zeitzonenoffset im [ISO 8601-Format](https://en.wikipedia.org/wiki/ISO_8601) angeben.

<a name="action"></a>

## <a name="action"></a>action

Ihr Scheduler-Auftrag führt eine primäre **Aktion** basierend auf dem angegebenen Zeitplan aus. Scheduler unterstützt HTTP-Aktionen, Storage-Warteschlangenaktionen, Service Bus-Warteschlangenaktionen und Service Bus-Themenaktionen. Wenn bei der primären **Aktion** ein Fehler auftritt, kann Scheduler eine sekundäre Fehleraktion ([**errorAction**](#erroraction)) zum Behandeln des Fehlers ausführen. Das **action**-Objekt beschreibt die folgenden Elemente:

* Den Diensttyp der Aktion
* Die Details der Aktion
* Eine alternative **errorAction**

Das vorherige Beispiel beschreibt eine HTTP-Aktion. Hier sehen Sie ein Beispiel für eine Storage-Warteschlangenaktion:

```json
"action": {
   "type": "storageQueue",
   "queueMessage": {
      "storageAccount": "myStorageAccount",  
      "queueName": "myqueue",                
      "sasToken": "TOKEN",                   
      "message": "My message body"
    }
}
```

Hier sehen Sie ein Beispiel für eine Service Bus-Warteschlangenaktion:

```json
"action": {
   "type": "serviceBusQueue",
   "serviceBusQueueMessage": {
      "queueName": "q1",  
      "namespace": "mySBNamespace",
      "transportType": "netMessaging", // Either netMessaging or AMQP
      "authentication": {  
         "sasKeyName": "QPolicy",
         "type": "sharedAccessKey"
      },
      "message": "Some message",  
      "brokeredMessageProperties": {},
      "customMessageProperties": {
         "appname": "FromScheduler"
      }
   }
},
```

Hier sehen Sie ein Beispiel für eine Service Bus-Themenaktion:

```json
"action": {
   "type": "serviceBusTopic",
   "serviceBusTopicMessage": {
      "topicPath": "t1",  
      "namespace": "mySBNamespace",
      "transportType": "netMessaging", // Either netMessaging or AMQP
      "authentication": {
         "sasKeyName": "QPolicy",
         "type": "sharedAccessKey"
      },
      "message": "Some message",
      "brokeredMessageProperties": {},
      "customMessageProperties": {
         "appname": "FromScheduler"
      }
   }
},
```

Weitere Informationen zu SAS-Token (Shared Access Signature) finden Sie unter [Autorisieren mit Shared Access Signatures (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

<a name="error-action"></a>

## <a name="erroraction"></a>errorAction

Wenn bei der primären **Aktion** Ihres Auftrags ein Fehler auftritt, kann Scheduler eine sekundäre Fehleraktion (**errorAction**) zum Behandeln des Fehlers ausführen. In der primären **Aktion** können Sie ein **errorAction**-Objekt angeben, damit Scheduler einen Endpunkt für die Fehlerbehandlung aufrufen oder eine Benutzerbenachrichtigung senden kann. 

Wenn beispielsweise ein Notfall am primären Endpunkt auftritt, können Sie mit **errorAction** einen sekundären Endpunkt aufrufen oder einen Endpunkt für die Fehlerbehandlung benachrichtigen. 

Wie bei der primären **Aktion** können Sie auch für die Fehleraktion eine einfache Logik oder eine auf anderen Aktionen beruhende komplexe Logik verwenden. 

<a name="recurrence"></a>

## <a name="recurrence"></a>recurrence

Ein Auftrag wird wiederholt, wenn die JSON-Definition des Auftrags das **recurrence**-Objekt enthält. Hier ein Beispiel:

```json
"recurrence": {
   "frequency": "Week",
   "interval": 1,
   "schedule": {
      "hours": [10, 22],
      "minutes": [0, 30],
      "weekDays": ["Monday", "Wednesday", "Friday"]
   },
   "count": 10,
   "endTime": "2012-11-04"
},
```

| Eigenschaft | Erforderlich | Value | BESCHREIBUNG | 
|----------|----------|-------|-------------| 
| **frequency** | Ja, wenn **recurrence** verwendet wird | Minute, Hour, Day, Week, Month, Year | Die Zeiteinheit zwischen Ausführungen. | 
| **interval** | Nein | 1 bis einschließlich 1.000 | Eine positive ganze Zahl, die die Anzahl von Zeiteinheiten zwischen den einzelnen Ausführungen basierend auf dem Wert von **frequency** bestimmt. | 
| **schedule** | Nein | Variabel | Die Details für komplexere und erweiterte Zeitpläne. Siehe **hours**, **minutes**, **weekDays**, **months** und **monthDays**. | 
| **hours** | Nein | 1 bis 24 | Ein Array mit den Stundenmarkierungen für die Ausführung des Auftrags. | 
| **minutes** | Nein | 0 bis 59 | Ein Array mit den Minutenmarkierungen für die Ausführung des Auftrags. | 
| **months** | Nein | 1 bis 12 | Ein Array mit den Monaten für die Ausführung des Auftrags. | 
| **monthDays** | Nein | Variabel | Ein Array mit den Tagen des Monats für die Ausführung des Auftrags. | 
| **weekDays** | Nein | Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday | Ein Array mit den Wochentagen für die Ausführung des Auftrags. | 
| **count** | Nein | <*none*> | Die Anzahl von Wiederholungen. In der Standardeinstellung wird ein Auftrag unendlich wiederholt. Es ist nicht möglich, **count** und **endTime** gleichzeitig zu verwenden, es wird jedoch die Regel berücksichtigt, die zuerst abgeschlossen wird. | 
| **endTime** | Nein | <*none*> | Das Datum und die Uhrzeit für die Beendigung der Wiederholung. In der Standardeinstellung wird ein Auftrag unendlich wiederholt. Es ist nicht möglich, **count** und **endTime** gleichzeitig zu verwenden, es wird jedoch die Regel berücksichtigt, die zuerst abgeschlossen wird. | 
||||

Weitere Informationen zu diesen Elementen finden Sie unter [Erstellen komplexer Zeitpläne und erweiterter Serien](../scheduler/scheduler-advanced-complexity.md).

<a name="retry-policy"></a>

## <a name="retrypolicy"></a>retryPolicy

Für den Fall, dass bei einem Scheduler-Auftrag ein Fehler auftritt, können Sie eine Wiederholungsrichtlinie einrichten, die bestimmt, ob und wie Scheduler die Aktion wiederholt. Standardmäßig führt Scheduler vier Wiederholungsversuche im Abstand von jeweils 30 Sekunden für einen Auftrag aus. Diese Richtlinie kann mehr oder weniger aggressiv gestaltet werden. Die folgende Richtlinie wiederholt eine Aktion beispielsweise zweimal pro Tag:

```json
"retryPolicy": { 
   "retryType": "Fixed",
   "retryInterval": "PT1D",
   "retryCount": 2
},
```

| Eigenschaft | Erforderlich | Value | BESCHREIBUNG | 
|----------|----------|-------|-------------| 
| **retryType** | Ja | **Fixed**, **None** | Bestimmt, ob Sie eine Wiederholungsrichtlinie angeben (**fixed**) oder nicht (**none**). | 
| **retryInterval** | Nein | PT30S | Gibt das Intervall und die Häufigkeit zwischen den Wiederholungsversuchen im [ISO 8601-Format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) an. Der Mindestwert ist 15 Sekunden und der Höchstwert 18 Monate. | 
| **retryCount** | Nein | 4 | Gibt die Anzahl von Wiederholungsversuchen an. Der Höchstwert ist 20. | 
||||

Weitere Informationen finden Sie unter [Hochverfügbarkeit und Zuverlässigkeit](../scheduler/scheduler-high-availability-reliability.md).

<a name="status"></a>

## <a name="state"></a>state

Der Zustand eines Auftrags ist entweder**Enabled**, **Disabled**, **Completed** oder **Faulted**. Hier sehen Sie ein Beispiel: 

`"state": "Disabled"`

Um den Zustand von Aufträgen in **Enabled** oder **Disabled** zu ändern, können Sie den PUT- oder PATCH-Vorgang für diese Aufträge verwenden.
Wenn ein Auftrag den Zustand **Completed** oder **Faulted** aufweist, ist es allerdings nicht möglich, den Zustand zu aktualisieren. Sie können jedoch den DELETE-Vorgang für den Auftrag ausführen. Scheduler löscht abgeschlossene und fehlerhafte Aufträge nach 60 Tagen. 

<a name="status"></a>

## <a name="status"></a>status

Nachdem ein Auftrag gestartet wurde, gibt Scheduler Informationen zum Auftragsstatus über das **status**-Objekt zurück. Dieses Objekt wird nur von Scheduler gesteuert. Das **status**-Objekt ist jedoch innerhalb des **job**-Objekts zugänglich. Der Status eines Auftrags umfasst die folgenden Informationen:

* Zeit für die vorherige Ausführung (sofern vorhanden)
* Zeit für die nächste geplante Ausführung für aktive Aufträge
* Anzahl von Auftragsausführungen
* Anzahl von fehlgeschlagenen Ausführungen (sofern vorhanden)
* Anzahl von Fehlern (sofern vorhanden)

Beispiel:

```json
"status": {
   "lastExecutionTime": "2007-03-01T13:00:00Z",
   "nextExecutionTime": "2007-03-01T14:00:00Z ",
   "executionCount": 3,
   "failureCount": 0,
   "faultedCount": 0
}
```

## <a name="see-also"></a>Weitere Informationen

* [Was ist der Azure Scheduler?](scheduler-intro.md)
* [Konzepte, Terminologie und Entitätshierarchie](scheduler-concepts-terms.md)
* [Erstellen komplexer Zeitpläne und erweiterter Serien](scheduler-advanced-complexity.md)
* [Grenzwerte, Kontingente, Standardwerte und Fehlercodes](scheduler-limits-defaults-errors.md)
* [Azure Scheduler-REST-API – Referenz](/rest/api/scheduler)
* [Azure Scheduler – PowerShell-Cmdlets-Referenz](scheduler-powershell-reference.md)
