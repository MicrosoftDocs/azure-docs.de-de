---
title: Schützen von Azure Service Bus-Anwendungen vor Ausfällen und Notfällen | Microsoft Docs
description: Verfahren, die Anwendungen vor einem potenziellen Service Bus-Ausfall schützen können.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/14/2018
ms.author: aschhab
ms.openlocfilehash: 24611e265788cf046aa0733bc423917aaf305427
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60589747"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Bewährte Methoden zum Schützen von Anwendungen vor Service Bus-Ausfällen und Notfällen

Unternehmenswichtige Anwendungen müssen ohne Unterbrechung ausgeführt werden. Dies gilt auch bei ungeplanten Ausfällen oder in Notsituationen. In diesem Artikel werden Verfahren beschrieben, mit denen Sie Service Bus-Anwendungen vor einem potenziellen Dienstausfall oder Notfällen schützen können.

Ein Ausfall wird als vorübergehende Nichtverfügbarkeit von Azure Service Bus definiert. Der Ausfall kann nur einige Komponenten von Service Bus betreffen, z. B. einen Nachrichtenspeicher, oder auch das gesamte Rechenzentrum. Nachdem das Problem behoben wurde, ist Service Bus wieder verfügbar. In der Regel führt ein Ausfall nicht zum Verlust von Nachrichten oder anderen Daten. Ein Beispiel für den Ausfall einer Komponente ist die Nichtverfügbarkeit eines bestimmten Nachrichtenspeichers. Beispiele für den Ausfall eines gesamten Rechenzentrums sind ein Stromausfall im Rechenzentrum oder ein fehlerhafter Netzwerkswitch im Rechenzentrum. Ein Ausfall kann einige Minuten oder auch bis zu einigen Tagen dauern.

Ein Notfall wird als dauerhafter Verlust einer Service Bus-Skalierungseinheit oder eines Rechenzentrums definiert. Das Rechenzentrum kann danach wieder zur Verfügung stehen oder auch nicht. Häufig gehen bei einem Notfall einige oder alle Nachrichten oder anderen Daten verloren. Beispiele für Notfälle sind Feuer, Überflutung und Erdbeben.

## <a name="protecting-against-outages-and-disasters---service-bus-premium"></a>Schutz vor Ausfällen und Notfällen – Service Bus Premium
Die Konzepte der Hochverfügbarkeit und Notfallwiederherstellung sind direkt im Premium-Tarif von Azure Service Bus integriert, sowohl innerhalb derselben Region (über Verfügbarkeitszonen) als auch in verschiedenen Regionen (über die georedundante Notfallwiederherstellung).

### <a name="geo-disaster-recovery"></a>Georedundante Notfallwiederherstellung

Service Bus Premium unterstützt die georedundante Notfallwiederherstellung auf Namespaceebene. Weitere Informationen finden Sie unter [Georedundante Notfallwiederherstellung in Azure Service Bus](service-bus-geo-dr.md). Bei der Funktion zur Notfallwiederherstellung, die nur für [Premium SKU](service-bus-premium-messaging.md) verfügbar ist, wird die Notfallwiederherstellung von Metadaten implementiert. Diese basiert auf speziellen primären und sekundären Namespaces.

### <a name="availability-zones"></a>Verfügbarkeitszonen

Die Service Bus Premium-SKU unterstützt [Verfügbarkeitszonen](../availability-zones/az-overview.md), die fehlerisolierte Standorte innerhalb einer Azure-Region bieten.

> [!NOTE]
> Die Unterstützung für Verfügbarkeitszonen für Azure Service Bus Premium ist nur in [Azure-Regionen](../availability-zones/az-overview.md#services-support-by-region) verfügbar, in denen Verfügbarkeitszonen vorhanden sind.

Sie können Verfügbarkeitszonen nur für neue Namespaces über das Azure-Portal aktivieren. Service Bus bietet keine Unterstützung für die Migration vorhandener Namespaces. Sie können die Zonenredundanz nicht deaktivieren, wenn Sie sie für Ihren Namespace aktiviert haben.

![1][]


## <a name="protecting-against-outages-and-disasters---service-bus-standard"></a>Schutz vor Ausfällen und Notfällen – Service Bus Standard
Um bei Verwendung des Standard-Messaging-Tarifs Resilienz in Bezug auf Rechenzentrumausfälle zu erzielen, unterstützt Service Bus zwei Ansätze: *aktive* und *passive* Replikation. Bei beiden Ansätzen können Sie die Erstellung in beiden Namespaces durchführen, wenn eine Warteschlange oder ein Thema bei einem Rechenzentrumausfall verfügbar bleiben muss. Beide Entitäten können den gleichen Namen haben. Eine primäre Warteschlange kann beispielsweise unter **contosoPrimary.servicebus.windows.net/myQueue** erreichbar sein, während die sekundäre Warteschlange unter **contosoSecondary.servicebus.windows.net/myQueue** erreichbar ist.

>[!NOTE]
> Bei der **aktiven Replikation** und der **passiven Replikation** handelt es sich um allgemeine Lösungen und nicht um spezifische Funktionen von Service Bus. Die Replikationslogik (Senden an zwei verschiedene Namespaces) befindet sich in der Senderanwendung. Der Empfänger muss für die Erkennung von Duplikaten über benutzerdefinierte Logik verfügen.

Falls die Anwendung keine permanente Kommunikation vom Absender zum Empfänger erfordert, kann die Anwendung eine dauerhafte clientseitige Warteschlange implementieren, um Nachrichtenverlust zu verhindern und den Absender gegen vorübergehende Service Bus-Fehler abzuschirmen.

### <a name="active-replication"></a>Aktive Replikation
Bei der aktiven Replikation werden für jeden Vorgang Entitäten in beiden Namespaces verwendet. Von allen Clients, die eine Nachricht senden, werden jeweils zwei Exemplare derselben Nachricht gesendet. Die erste Kopie wird an die primäre Entität gesendet (z.B. **contosoPrimary.servicebus.windows.net/sales**), und die zweite Kopie der Nachricht wird an die sekundäre Entität gesendet (z.B. **contosoSecondary.servicebus.windows.net/sales**).

Ein Client empfängt Nachrichten aus beiden Warteschlangen. Der Empfänger verarbeitet die erste Kopie einer Nachricht, und die zweite Kopie wird unterdrückt. Damit doppelte Nachrichten unterdrückt werden können, muss der Absender jede Nachricht mit einem eindeutigen Bezeichner versehen. Beide Kopien der Nachricht müssen mit dem gleichen Bezeichner gekennzeichnet werden. Sie können die Eigenschaften [BrokeredMessage.MessageId][BrokeredMessage.MessageId] oder [BrokeredMessage.Label][BrokeredMessage.Label] oder auch eine benutzerdefinierte Eigenschaft zum Kennzeichnen der Nachricht verwenden. Der Empfänger muss eine Liste der Nachrichten vorhalten, die er bereits empfangen hat.

Im Beispiel für die [Georeplikation mit dem Standard-Tarif von Service Bus][Geo-replication with Service Bus Standard Tier] wird die aktive Replikation von Nachrichtenentitäten veranschaulicht.

> [!NOTE]
> Beim Ansatz mit der aktiven Replikation wird die Anzahl von Vorgängen verdoppelt. Daher kann dieser Ansatz zu höheren Kosten führen.
> 
> 

### <a name="passive-replication"></a>Passive Replikation
Wenn kein Fehler vorliegt, wird bei der passiven Replikation nur eine der beiden Nachrichtenentitäten verwendet. Ein Client sendet die Nachricht an die aktive Entität. Wenn der Vorgang auf der aktiven Entität mit einem Fehlercode fehlschlägt und angegeben wird, dass das Rechenzentrum, von dem die aktive Entität gehostet wird, ggf. nicht verfügbar ist, sendet der Client eine Kopie der Nachricht an die Backup-Entität. An diesem Punkt tauschen die aktive Entität und die Backup-Entität die Rollen: Der sendende Client sieht die alte aktive Entität als neue Backup-Entität und die alte Backup-Entität als neue aktive Entität an. Wenn beide Sendevorgänge fehlschlagen, bleiben die Rollen der beiden Entitäten unverändert, und es wird ein Fehler zurückgegeben.

Ein Client empfängt Nachrichten aus beiden Warteschlangen. Da die Möglichkeit besteht, dass der Empfänger zwei Kopien derselben Nachricht erhält, muss der Empfänger doppelte Nachrichten unterdrücken. Sie können Duplikate hierbei genauso unterdrücken, wie dies für die aktive Replikation beschrieben wurde.

Im Allgemeinen ist die passive Replikation wirtschaftlicher als die aktive Replikation, da in den meisten Fällen nur ein Vorgang ausgeführt wird. Latenz, Durchsatz und Kosten sind identisch mit dem Szenario ohne Replikation.

Bei Verwendung der passiven Replikation können Nachrichten in den folgenden Szenarien verloren gehen oder doppelt empfangen werden:

* **Nachrichtenverzögerung oder -verlust**: Angenommen, der Absender hat die Nachricht „m1“ an die primäre Warteschlange gesendet, und die Warteschlange ist dann nicht mehr verfügbar, bevor der Empfänger „m1“ erhält. Der Absender sendet die nachfolgende Nachricht „m2“ an die sekundäre Warteschlange. Wenn die primäre Warteschlange vorübergehend nicht verfügbar ist, erhält der Empfänger „m1“, wenn die Warteschlange wieder verfügbar ist. Bei einer Notfallsituation kommt „m1“ unter Umständen niemals beim Empfänger an.
* **Doppelter Empfang**: Angenommen, der Absender sendet eine Nachricht „m“ an die primäre Warteschlange. Service Bus verarbeitet „m“, aber es kann keine Antwort gesendet werden. Nach dem Timeout des Sendevorgangs sendet der Absender eine identische Kopie von „m“ an die sekundäre Warteschlange. Wenn der Empfänger die erste Kopie von „m“ empfangen kann, bevor die primäre Warteschlange nicht mehr verfügbar ist, erhält der Empfänger nahezu gleichzeitig beide Kopien von „m“. Falls der Empfänger die erste Kopie von „m“ nicht erhält, bevor die primäre Warteschlange nicht mehr verfügbar ist, erhält der Empfänger zuerst nur die zweite Kopie von „m“. Anschließend erhält er aber eine zweite Kopie von „m“, wenn die primäre Warteschlange verfügbar wird.

Im Beispiel für die [Georeplikation mit dem Standard-Tarif von Service Bus][Geo-replication with Service Bus Standard Tier] wird die passive Replikation von Nachrichtenentitäten veranschaulicht.

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Schutz von Relayendpunkten vor Rechenzentrumausfällen oder Notfällen
Die Georeplikation von Relayendpunkten ermöglicht die Verwendung eines Diensts, mit dem ein Relayendpunkt verfügbar gemacht wird, damit er bei Service Bus-Ausfällen zugänglich ist. Zum Erzielen der Georeplikation muss der Dienst zwei Relayendpunkte in unterschiedlichen Namespaces erstellen. Die Namespaces müssen sich in unterschiedlichen Rechenzentren befinden, und die beiden Endpunkte müssen unterschiedliche Namen haben. Ein primärer Endpunkt kann beispielsweise unter **contosoPrimary.servicebus.windows.net/myPrimaryService** erreichbar sein, während der sekundäre Endpunkt unter **contosoSecondary.servicebus.windows.net/mySecondaryService** erreichbar ist.

Der Dienst lauscht dann an beiden Endpunkten, und ein Client kann den Dienst über einen der beiden Endpunkte aufrufen. Eine Clientanwendung wählt ein Relayelement als primären Endpunkt und sendet dessen Anforderung an den aktiven Endpunkt. Wenn der Vorgang mit einem Fehlercode fehlschlägt, wird in diesem Fehler darauf hingewiesen, dass der Relayendpunkt nicht verfügbar ist. Die Anwendung öffnet einen Kanal zum Backup-Endpunkt und gibt die Anforderung neu heraus. An diesem Punkt tauschen der aktive Endpunkt und der Backup-Endpunkt die Rollen: Die Clientanwendung sieht den alten aktiven Endpunkt als neuen Backup-Endpunkt und den alten Backup-Endpunkt als neuen aktiven Endpunkt an. Wenn beide Sendevorgänge fehlschlagen, bleiben die Rollen der beiden Entitäten unverändert, und es wird ein Fehler zurückgegeben.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Notfallwiederherstellung finden Sie in diesen Artikeln:

* [Georedundante Notfallwiederherstellung in Azure Service Bus](service-bus-geo-dr.md)
* [Geschäftskontinuität in Azure SQL-Datenbank][Azure SQL Database Business Continuity]
* [Entwickeln robuster Anwendungen für Azure][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Geo-replication with Service Bus Standard Tier]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

[1]: ./media/service-bus-outages-disasters/az.png
