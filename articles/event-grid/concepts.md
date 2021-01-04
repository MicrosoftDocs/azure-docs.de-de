---
title: Azure Event Grid – Begriffe
description: In diesem Artikel werden Azure Event Grid und die zugehörigen Begriffe beschrieben. Zudem werden verschiedene Schlüsselkomponenten von Event Grid definiert.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 6cfb8b3aaf16a0080b9864ce5198b8a7232e8bc8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075108"
---
# <a name="concepts-in-azure-event-grid"></a>Begriffe in Azure Event Grid

Dieser Artikel beschreibt die zentralen Begriffe in Azure Event Grid.

## <a name="events"></a>Events

Ein Ereignis ist die kleinste Informationsmenge, die einen Vorgang im System umfassend beschreibt. Jedes Ereignis enthält allgemeine Informationen wie Quelle des Ereignisses, Zeitpunkt, an dem das Ereignis aufgetreten ist, und den eindeutigen Bezeichner. Jedes Ereignis enthält auch spezielle Informationen, die nur für den jeweiligen Ereignistyp relevant sind. Beispielsweise enthält ein Ereignis zu einer neuen Datei, die in Azure Storage erstellt wird, Details über die Datei, z.B. den Wert von `lastTimeModified`. Alternativ dazu enthält ein Event Hubs-Ereignis die URL der Erfassungsdatei. 

Ein Ereignis mit einer Größe von bis zu 64 KB wird von der Vereinbarung zum Servicelevel (SLA) für die allgemeine Verfügbarkeit (GA) abgedeckt. Die Unterstützung für ein Ereignis von einer Größe bis zu 1 MB ist derzeit in der Vorschauversion verfügbar. Ereignisse, die größer als 64 KB sind, werden in Schritten von 64 KB in Rechnung gestellt. 


Die gesendeten Ereigniseigenschaften finden Sie unter [Azure Event Grid-Ereignisschema](event-schema.md).

## <a name="publishers"></a>Herausgeber

Ein Herausgeber ist der Benutzer oder die Organisation, die Ereignisse an Event Grid sendet. Microsoft veröffentlicht Ereignisse für mehrere Azure-Dienste. Sie haben die Möglichkeit, Ereignisse über Ihre eigene Anwendung zu veröffentlichen. Organisationen, die Dienst außerhalb von Azure hosten, können Ereignisse über Event Grid veröffentlichen.

## <a name="event-sources"></a>Ereignisquellen

Eine Ereignisquelle ist die Quelle, in der das Ereignis auftritt. Jede Ereignisquelle ist mit mindestens einem Ereignistyp verknüpft. Azure Storage ist z.B. die Ereignisquelle für durch Blobs erstellte Ereignisse. IoT Hub ist die Ereignisquelle für Ereignisse, die von Geräten erstellt wurden. Ihre Anwendung ist die Ereignisquelle für benutzerdefinierte Ereignisse, die Sie definieren. Ereignisquellen sind dafür zuständig, Ereignisse an Event Grid zu senden.

Informationen zum Implementieren der unterstützten Event Grid-Quellen finden Sie unter [Ereignisquellen in Azure Event Grid](overview.md#event-sources).

## <a name="topics"></a>Themen

Das Event Grid-Thema stellt einen Endpunkt bereit, an den die Ereignisquelle Ereignisse sendet. Der Herausgeber erstellt das Thema und legt fest, ob eine Ereignisquelle ein Thema oder mehrere Themen benötigt. Ein Event Grid-Thema wird für eine Sammlung ähnlicher Ereignisse verwendet. Um auf bestimmte Arten von Ereignissen zu reagieren, legen Abonnenten fest, welche Themen sie abonnieren.

**Systemthemen** sind integrierte Themen, die von Azure-Diensten bereitgestellt werden, z. B. von Azure Storage, Azure Event Hubs und Azure Service Bus. Sie können Systemthemen in Ihrem Azure-Abonnement erstellen und sie abonnieren. Weitere Informationen finden Sie in der [Übersicht über Systemthemen](system-topics.md). 

**Benutzerdefinierte Themen** sind Anwendungs- und Drittanbieterthemen. Wenn Sie ein benutzerdefiniertes Thema erstellen oder Zugriff darauf erhalten, wird das benutzerdefinierte Thema in Ihrem Abonnement angezeigt. Weitere Informationen finden Sie unter [Benutzerdefinierte Themen](custom-topics.md). Beim Entwerfen Ihrer Anwendung können Sie flexibel entscheiden, wie viele Themen erstellt werden sollen. Erstellen Sie für große Lösungen ein benutzerdefiniertes Thema für jede Kategorie von verwandten Ereignissen. Denken Sie beispielsweise an eine Anwendung, die Ereignisse im Zusammenhang mit der Änderung von Benutzerkonten und der Verarbeitung von Bestellungen sendet. Es ist unwahrscheinlich, dass ein Ereignishandler beide Ereigniskategorien benötigt. Erstellen Sie zwei benutzerdefinierte Themen, und lassen Sie Ereignishandler das jeweils relevante Thema abonnieren. Für kleine Lösungen empfiehlt es sich ggf., alle Ereignisse an ein Thema zu senden. Ereignisabonnenten können nach den gewünschten Ereignistypen filtern.

Es gibt eine weitere Art von Thema: das **Partnerthema**. Mit dem Feature [Partnerereignisse](partner-events-overview.md) kann ein SaaS-Drittanbieter Ereignisse aus seinen Diensten veröffentlichen, um Sie für Consumer verfügbar zu machen, die diese Ereignisse abonnieren können. Der SaaS-Anbieter stellt einen Thementyp bereit (ein **Partnerthema** ), der von Abonnenten verwendet wird, um Ereignisse zu verarbeiten. Außerdem bietet dieser Typ ein sauberes Pub-Sub-Modell, indem es die Belange und den Besitz von Ressourcen trennt, die von Ereignisherausgebern und -abonnenten verwendet werden.

## <a name="event-subscriptions"></a>Ereignisabonnements

Ein Abonnement informiert Event Grid über die Ereignisse, die Sie zu einem Thema erhalten möchten. Wenn Sie das Abonnement erstellen, stellen Sie einen Endpunkt für den Umgang mit dem Ereignis bereit. Sie können die Ereignisse filtern, die an den Endpunkt gesendet werden. Sie können nach Ereignistyp oder Betreffmuster filtern. Weitere Informationen finden Sie unter [Event Grid – Abonnementschema](subscription-creation-schema.md).

Beispiele für das Erstellen von Abonnements finden Sie unter:

* [Azure CLI-Beispiele für Event Grid](cli-samples.md)
* [Azure PowerShell-Beispiele für Event Grid](powershell-samples.md)
* [Azure Resource Manager-Vorlagen für Event Grid](template-samples.md)

Weitere Informationen zum Abfragen Ihrer aktuellen Event Grid-Abonnements finden Sie unter [Abfragen von Event Grid-Abonnements](query-event-subscriptions.md).

## <a name="event-subscription-expiration"></a>Ereignisabonnementablauf
Das Ereignisabonnement läuft nach diesem Datum automatisch ab. Legen Sie ein Ablaufdatum für Ereignisabonnements fest, die nur für einen begrenzten Zeitraum erforderlich sind und um deren Bereinigung Sie sich nicht kümmern möchten. Beispielsweise könnten Sie beim Erstellen eines Ereignisabonnements zum Testen eines Szenarios ein Ablaufdatum festlegen. 

Ein Beispiel für das Festlegen eines Ablaufdatums finden Sie unter [Abonnieren mit erweiterten Filtern](how-to-filter-events.md#subscribe-with-advanced-filters).

## <a name="event-handlers"></a>Ereignishandler

In Bezug auf Event Grid ist ein Ereignishandler das Ziel, an den das Ereignis gesendet wird. Der Handler ergreift zur Verarbeitung des Ereignisses weitere Maßnahmen. Event Grid unterstützt verschiedene Handlertypen. Sie können einen unterstützten Azure-Dienst oder einen eigenen Webhook als Handler verwenden. Je nach Handlertyp übermittelt Event Grid die Ereignisse auf unterschiedliche Art und Weise. Bei HTTP-Webhookereignishandlern wird das Ereignis solange wiederholt, bis der Handler einen Statuscode von `200 – OK` zurückgibt. Bei Azure Storage Queue werden die Ereignisse solange wiederholt, bis der Warteschlangendienst den Nachrichtenpush in der Warteschlange verarbeitet.

Informationen zum Implementieren der unterstützten Event Grid-Handler finden Sie unter [Ereignishandler in Azure Event Grid](event-handlers.md).

## <a name="security"></a>Sicherheit

Event Grid ermöglicht ein sicheres Abonnieren und Veröffentlichen von Themen. Für das Abonnieren sind entsprechende Berechtigungen für die Ressource oder das Event Grid-Thema erforderlich. Für das Veröffentlichen ist ein SAS-Token oder eine Schlüsselauthentifizierung für das Thema erforderlich. Weitere Informationen finden Sie unter [Event Grid – Sicherheit und Authentifizierung](security-authentication.md).

## <a name="event-delivery"></a>Ereignisbereitstellung

Wenn Event Grid nicht bestätigen kann, dass ein Ereignis beim Endpunkt des Abonnenten eingegangen ist, wird das Ereignis erneut übermittelt. Weitere Informationen finden Sie unter [Event Grid – Nachrichtenübermittlung und -wiederholung](delivery-and-retry.md).

## <a name="batching"></a>Batching

Wenn Sie ein benutzerdefiniertes Thema verwenden, müssen die Ereignisse immer in einem Array veröffentlicht werden. Dies kann für Szenarien mit geringem Durchsatz ein Batch mit nur einem Element sein. Für Anwendungsfälle mit hohem Volumen wird aber empfohlen, pro Veröffentlichung mehrere Ereignisse zu Batches zusammenzufassen, um eine höhere Effizienz zu erzielen. Batches können eine Größe von bis zu 1 MB haben. Für die einzelnen Ereignisse sollte trotzdem eine Größe von 64 KB (Allgemeine Verfügbarkeit) bzw. 1 MB (Vorschauversion) nicht überschritten werden.

> [!NOTE]
> Ein Ereignis mit einer Größe von bis zu 64 KB wird von der Vereinbarung zum Servicelevel (SLA) für die allgemeine Verfügbarkeit (GA) abgedeckt. Die Unterstützung für ein Ereignis von einer Größe bis zu 1 MB ist derzeit in der Vorschauversion verfügbar. Ereignisse, die größer als 64 KB sind, werden in Schritten von 64 KB in Rechnung gestellt. 

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung in Event Grid finden Sie unter [Informationen zu Event Grid](overview.md).
* Um sich schnell mit der Verwendung von Event Grid vertraut zu machen, lesen Sie [Erstellen und Weiterleiten benutzerdefinierter Ereignisse mit Azure Event Grid](custom-event-quickstart.md).
