---
title: Hochverfügbarkeit und Zuverlässigkeit – Azure Scheduler
description: Erfahren Sie etwas über die Hochverfügbarkeit und Zuverlässigkeit bei Azure Scheduler.
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 5ec78e60-a9b9-405a-91a8-f010f3872d50
ms.topic: article
ms.date: 08/16/2016
ms.openlocfilehash: 50ab6cfefe4a7df9d671e7fd1287aa16b803f260
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64702893"
---
# <a name="high-availability-and-reliability-for-azure-scheduler"></a>Hochverfügbarkeit und Zuverlässigkeit für Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersetzt Azure Scheduler, der eingestellt wird. Zum Planen von Aufträgen sollten Sie stattdessen [Azure Logic Apps ausprobieren](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

Azure Scheduler bietet sowohl [Hochverfügbarkeit](https://docs.microsoft.com/azure/architecture/guide/pillars#availability) als auch Zuverlässigkeit für Ihre Aufträge. Weitere Informationen finden Sie unter [SLA für Scheduler](https://azure.microsoft.com/support/legal/sla/scheduler).

## <a name="high-availability"></a>Hochverfügbarkeit

Azure Scheduler ist [hochverfügbar] und bietet sowohl eine georedundante Dienstbereitstellung als auch eine georegionale Auftragsreplikation.

### <a name="geo-redundant-service-deployment"></a>Georedundante Dienstbereitstellung

Azure Scheduler ist im Azure-Portal in fast [jeder von Azure unterstützten geografischen Region](https://azure.microsoft.com/global-infrastructure/regions/#services) verfügbar. Wenn also ein Azure-Rechenzentrum in einer gehosteten Region nicht verfügbar ist, können Sie Azure Scheduler weiterhin verwenden, da die Failoverfunktionen Scheduler aus einem anderen Rechenzentrum verfügbar machen.

### <a name="geo-regional-job-replication"></a>Georegionale Auftragsreplikation

Ihre eigenen Aufträge in Azure Scheduler werden über Azure-Regionen repliziert. Bei einem Ausfall in einer Region wird so mittels Failover für Azure Scheduler sichergestellt, dass der Auftrag in einem anderen Rechenzentrum in der gekoppelten geografischen Region ausgeführt wird.

Wenn Sie also beispielsweise einen Auftrag in der Region „USA, Süden-Mitte“ erstellt haben, repliziert Azure Scheduler diesen Auftrag automatisch in die Region „USA, Norden-Mitte“. Wenn in „USA, Süden-Mitte“ ein Fehler auftritt, führt Azure Scheduler den Auftrag in „USA, Norden-Mitte“ aus. 

![Georegionale Auftragsreplikation](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png)

Azure Scheduler stellt auch sicher, dass Ihre Daten innerhalb der gleichen, aber größeren geografischen Region verbleiben, falls in Azure ein Fehler auftritt. Daher müssen Sie Ihre Aufträge nicht duplizieren, wenn Sie nur Hochverfügbarkeit benötigen. Azure Scheduler bietet automatisch Hochverfügbarkeit für Ihre Aufträge.

## <a name="reliability"></a>Zuverlässigkeit

Azure Scheduler bietet eine garantierte Hochverfügbarkeit, aber mit einem anderen Ansatz für Aufträge, die von Benutzern erstellt werden. Nehmen Sie beispielsweise an, dass Ihr Auftrag einen HTTP-Endpunkt aufruft, der nicht verfügbar ist. Azure Scheduler versucht trotzdem, den Auftrag erfolgreich auszuführen, indem Ihnen alternative Methoden für die Fehlerbehebung bereitgestellt werden: 

* Richten Sie Wiederholungsrichtlinien ein.
* Richten Sie alternative Endpunkte ein.

<a name="retry-policies"></a>

### <a name="retry-policies"></a>Wiederholungsrichtlinien

Mit Azure Scheduler können Sie Wiederholungsrichtlinien einrichten. Nach einer nicht erfolgreichen Auftragsausführung wiederholt Scheduler die Ausführung standardmäßig viermal im Abstand von jeweils 30 Sekunden. Sie können diese Wiederholungsrichtlinie umfangreicher gestalten, indem Sie z.B. 10 Wiederholungen in Intervallen von 30 Sekunden festlegen, oder eine weniger aggressive Wiederholungsrichtlinie anwenden, bei der z.B. zwei Wiederholungen in täglichen Intervallen durchgeführt werden.

Angenommen, Sie erstellen einen wöchentlichen Auftrag, der einen HTTP-Endpunkt aufruft. Wenn der HTTP-Endpunkt für einige Stunden nicht verfügbar ist, während Ihr Auftrag ausgeführt wird, möchten Sie nicht eine Woche warten, bis der Auftrag erneut ausgeführt wird. Dies würde allerdings geschehen, da die Standard-Wiederholungsrichtlinie in diesem Fall nicht funktioniert. Daher empfiehlt es sich, die Standard-Wiederholungsrichtlinie zu ändern, sodass Wiederholungen z.B. alle drei Stunden anstelle von 30 Sekunden erfolgen. 

Weitere Informationen zum Einrichten einer Wiederholungsrichtlinie finden Sie unter [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoints"></a>Alternative Endpunkte

Wenn Ihr Azure Scheduler-Auftrag einen Endpunkt, der nicht erreichbar ist, aufruft, greift Scheduler selbst nach der Wiederholungsrichtlinie auf einen anderen Endpunkt zurück, der solche Fehler behandeln kann. Wenn Sie einen solchen Endpunkt einrichten, ruft Scheduler also diesen Endpunkt auf. Dies macht Ihre eigenen Aufträge auch im Fall von Ausfällen hochverfügbar.

Dieses Diagramm zeigt beispielsweise, wie Scheduler die Wiederholungsrichtlinie bei einem Aufruf eines Webdiensts in New York befolgt. Führen die Wiederholungen nicht zu einem Erfolg, sucht Scheduler nach einem alternativen Endpunkt. Ist ein Endpunkt vorhanden, beginnt Scheduler damit, Anforderungen an den alternativen Endpunkt zu senden. Die gleiche Wiederholungsrichtlinie gilt sowohl für die ursprüngliche Aktion als auch für die alternative Aktion.

![Scheduler-Verhalten mit Wiederholungsrichtlinie und alternativem Endpunkt](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png)

Der Aktionstyp der alternativen Aktion kann sich von dem für die ursprüngliche Aktion unterscheiden. Obwohl beispielsweise die ursprüngliche Aktion einen HTTP-Endpunkt aufruft, kann die alternative Aktion Fehler mithilfe einer Storage-Warteschlange, Service Bus-Warteschlange oder Service Bus-Themenaktion protokollieren.

Weitere Informationen zum Einrichten eines alternativen Endpunkts finden Sie unter [errorAction](scheduler-concepts-terms.md#error-action).

## <a name="see-also"></a>Weitere Informationen

* [Was ist der Azure Scheduler?](scheduler-intro.md)
* [Konzepte, Terminologie und Entitätshierarchie](scheduler-concepts-terms.md)
* [Erstellen komplexer Zeitpläne und erweiterter Serien](scheduler-advanced-complexity.md)
* [Grenzwerte, Kontingente, Standardwerte und Fehlercodes](scheduler-limits-defaults-errors.md)
