---
title: Verwalten von Azure Cache for Redis | Microsoft-Dokumentation
description: Informationen zum Ausführen von Verwaltungsaufgaben wie Neustarten und Planen von Updates für Azure Cache for Redis
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 8c915ae6-5322-4046-9938-8f7832403000
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 07/05/2017
ms.author: yegu
ms.openlocfilehash: 81ef669b62c822e10d8bf5c45e58dd769c5dbeb9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60233023"
---
# <a name="how-to-administer-azure-cache-for-redis"></a>Verwalten von Azure Cache for Redis
In diesem Thema erfahren Sie, wie Verwaltungsaufgaben wie das [Neustarten](#reboot) und [Planen von Updates](#schedule-updates) für Ihre Azure Cache for Redis-Instanzen erfolgen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reboot"></a>Reboot
Auf dem Blatt **Neustart** können Sie einen oder mehrere Knoten Ihres Caches neu starten. Mit dieser Neustartfunktion können Sie Ihre Anwendung bei einem Ausfall eines Cacheknotens auf Resilienz testen.

![Reboot](./media/cache-administration/redis-cache-administration-reboot.png)

Wählen Sie die Knoten, die neu gestartet werden sollen, und klicken Sie auf **Neustart**.

![Reboot](./media/cache-administration/redis-cache-reboot.png)

Wenn Sie über einen Premium-Cache mit aktiviertem Clustering verfügen, können Sie die Shards des Caches auswählen, die neu gestartet werden sollen.

![Reboot](./media/cache-administration/redis-cache-reboot-cluster.png)

Zum Neustarten eines oder mehrerer Knoten Ihres Caches wählen Sie die gewünschten Knoten aus und klicken auf **Neustart**. Wenn Sie über einen Premium-Cache mit aktiviertem Clustering verfügen, wählen Sie die gewünschten Shards aus, die neu gestartet werden sollen, und klicken Sie dann auf **Neu starten**. Nach einigen Minuten werden die ausgewählten Knoten neu gestartet, die nur wenige Minuten später wieder online sind.

Die Auswirkungen auf Clientanwendungen hängen von den Knoten ab, die Sie neu starten.

* **Master** : Wenn der Masterknoten neu gestartet wird, führt Azure Cache for Redis ein Failover auf den Replikatknoten durch, der zum Master hochgestuft wird. Während dieses Failovers gibt es möglicherweise ein kurzes Intervall, in dem keine Verbindungen mit dem Cache aufgebaut werden können.
* **Untergeordneter Knoten** : Wenn der Slaveknoten neu gestartet wird, hat dies meist keine Auswirkungen auf Cacheclients.
* **Primär- und Sekundärgerät** : Wenn beide Cacheknoten neu gestartet werden, gehen alle Daten im Cache verloren, und es können erst wieder Verbindungen mit dem Cache hergestellt werden, wenn der primäre Knoten wieder online ist. Wenn Sie [Datenpersistenz](cache-how-to-premium-persistence.md) konfiguriert haben, wird die letzte Sicherung wiederhergestellt, sobald der Cache wieder online ist. Allerdings gehen alle Cacheschreibvorgänge verloren, die nach der letzten Sicherung durchgeführt wurden.
* **Knoten eines Premium-Caches mit aktiviertem Clustering:** Wenn Sie einen oder mehrere Knoten eines Premium-Caches mit aktiviertem Clustering neu starten, entspricht das Verhalten für die ausgewählten Knoten dem Neustart des oder der entsprechenden Knoten eines nicht gruppierten Caches.

> [!IMPORTANT]
> Ein Neustart ist nun für alle Tarife verfügbar.
> 
> 

## <a name="reboot-faq"></a>Häufig gestellte Fragen zum Neustart
* [Welche Knoten sollte ich neu starten, um meine Anwendung zu testen?](#which-node-should-i-reboot-to-test-my-application)
* [Kann ich den Cache neu starten, um Clientverbindungen zu löschen?](#can-i-reboot-the-cache-to-clear-client-connections)
* [Gehen beim Neustart Daten aus dem Cache verloren?](#will-i-lose-data-from-my-cache-if-i-do-a-reboot)
* [Kann ich meinen Cache mithilfe von PowerShell, CLI oder anderen Verwaltungstools neu starten?](#can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools)
* [Welche Tarife bieten die Neustartfunktionalität?](#what-pricing-tiers-can-use-the-reboot-functionality)

### <a name="which-node-should-i-reboot-to-test-my-application"></a>Welche Knoten sollte ich neu starten, um meine Anwendung zu testen?
Um die Stabilität Ihrer Anwendung bei Ausfall des primären Knotens Ihres Caches zu testen, starten Sie den **Master** -Knoten neu. Um die Stabilität Ihrer Anwendung bei Ausfall des sekundären Knotens Ihres Caches zu testen, starten Sie den **untergeordneten** -Knoten neu. Um die Stabilität Ihrer Anwendung bei Ausfall Ihres gesamten Caches zu testen, starten Sie den **beide** Knoten neu.

### <a name="can-i-reboot-the-cache-to-clear-client-connections"></a>Kann ich den Cache neu starten, um Clientverbindungen zu löschen?
Ja, wenn Sie den Cache neu starten, werden alle Clientverbindungen gelöscht. Ein Neustart kann sinnvoll sein, wenn alle Clientverbindungen aufgrund eines Logikfehlers oder eines Fehlers in der Clientanwendung belegt sind. Jeder Tarif weist verschiedene [Grenzwerte für Clientverbindungen](cache-configure.md#default-redis-server-configuration) für die verschiedenen Größen auf. Sobald diese Grenzwerte erreicht sind, werden keine weiteren Clientverbindungen akzeptiert. Das Neustarten des Caches bietet eine Möglichkeit, alle Clientverbindungen zu löschen.

> [!IMPORTANT]
> Wenn Sie Ihren Cache neu starten, um Clientverbindungen zu löschen, stellt StackExchange.Redis automatisch wieder eine Verbindung her, sobald der Redis-Knoten wieder online ist. Wenn das zugrunde liegende Problem nicht behoben wird, kann es weiterhin passieren, dass die Clientverbindungen aufgebraucht werden.
> 
> 

### <a name="will-i-lose-data-from-my-cache-if-i-do-a-reboot"></a>Gehen beim Neustart Daten aus dem Cache verloren?
Wenn Sie sowohl den **Master**- als auch den **untergeordneten** Knoten neu starten, gehen möglicherweise alle Daten im Cache (oder im jeweiligen Shard, wenn Sie einen Premium-Cache mit aktiviertem Clustering nutzen) verloren, doch dies ist auch nicht sicher. Wenn Sie [Datenpersistenz](cache-how-to-premium-persistence.md) konfiguriert haben, wird die letzte Sicherung wiederhergestellt, sobald der Cache wieder online ist. Allerdings gehen alle Cacheschreibvorgänge verloren, die nach der Sicherung durchgeführt wurden.

Wenn Sie nur einen der Knoten neu starten, gehen Daten in der Regel nicht verloren, möglicherweise aber doch. Wenn z.B. der Masterknoten neu gestartet und ein Cacheschreibvorgang ausgeführt wird, gehen die Daten des Cacheschreibvorgangs verloren. Ein weiteres Szenario für Datenverlust ist der Fall, in dem Sie einen Knoten neu starten und der andere Knoten aufgrund eines Fehlers gleichzeitig ausfällt. Weitere Informationen zu den möglichen Ursachen für den Datenverlust finden Sie unter [Was ist mit meinen Daten in Redis passiert?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md).

### <a name="can-i-reboot-my-cache-using-powershell-cli-or-other-management-tools"></a>Kann ich meinen Cache mithilfe von PowerShell, CLI oder anderen Verwaltungstools neu starten?
Ja. Anweisungen für PowerShell finden Sie unter [So starten Sie einen Azure Cache for Redis neu](cache-howto-manage-redis-cache-powershell.md#to-reboot-an-azure-cache-for-redis).

### <a name="what-pricing-tiers-can-use-the-reboot-functionality"></a>Welche Tarife bieten die Neustartfunktionalität?
Ein Neustart ist für alle Tarife verfügbar.

## <a name="schedule-updates"></a>Planen von Updates
Auf dem Blatt **Updates planen** können Sie ein Wartungsfenster für Ihren Cache im Premium-Tarif festlegen. Wenn das Wartungsfenster angegeben ist, erfolgen alle Redis-Serverupdates in diesem Zeitfenster. 

> [!NOTE] 
> Das Wartungsfenster bezieht sich nur auf Redis-Serverupdates und nicht auf Azure-Updates oder Updates des Betriebssystems der virtuellen Computer, die den Cache hosten.
> 
> 

![Planen von Updates](./media/cache-administration/redis-schedule-updates.png)

Aktivieren Sie zum Angeben eines Wartungsfensters die Kontrollkästchen der gewünschten Tage, geben Sie jeweils die Startzeit des Wartungsfensters an, und klicken Sie auf **OK**. Beachten Sie, dass die Zeit im Wartungsfenster als UTC angegeben ist. 

Das Standardwartungsfenster für Updates beträgt fünf Stunden (Mindestdauer). Der Wert kann nicht über das Azure-Portal konfiguriert werden. Sie können ihn jedoch in PowerShell mithilfe des `MaintenanceWindow`-Parameters des Cmdlets [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry) konfigurieren. Weitere Informationen finden Sie unter „Kann ich geplante Updates mit PowerShell, der CLI oder anderen Verwaltungstools verwalten?“.


## <a name="schedule-updates-faq"></a>Häufig gestellte Fragen zum Planen von Updates
* [Wann erfolgen Updates, wenn nicht das Feature zum Planen von Updates verwendet wird?](#when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature)
* [Welche Art von Updates erfolgen während des geplanten Wartungsfensters?](#what-type-of-updates-are-made-during-the-scheduled-maintenance-window)
* [Kann ich geplante Updates mit PowerShell, der CLI oder anderen Verwaltungstools verwalten?](#can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools)
* [Welche Tarife bieten die Funktionalität zum Planen von Updates?](#what-pricing-tiers-can-use-the-schedule-updates-functionality)

### <a name="when-do-updates-occur-if-i-dont-use-the-schedule-updates-feature"></a>Wann erfolgen Updates, wenn nicht das Feature zum Planen von Updates verwendet wird?
Wenn Sie kein Wartungsfenster angeben, können Updates jederzeit erfolgen.

### <a name="what-type-of-updates-are-made-during-the-scheduled-maintenance-window"></a>Welche Art von Updates erfolgen während des geplanten Wartungsfensters?
Nur Updates des Redis-Servers erfolgen während des geplanten Wartungsfensters. Das Wartungsfenster gilt nicht für Azure-Updates oder Updates des Betriebssystems virtueller Computer.

### <a name="can-i-managed-scheduled-updates-using-powershell-cli-or-other-management-tools"></a>Kann ich geplante Updates mit Powershell, der CLI oder anderen Verwaltungstools verwalten?
Ja, Sie können Ihre geplanten Updates mit den folgenden PowerShell-Cmdlets verwalten:

* [Get-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/get-azrediscachepatchschedule)
* [New-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/new-azrediscachepatchschedule)
* [New-AzRedisCacheScheduleEntry](/powershell/module/az.rediscache/new-azrediscachescheduleentry)
* [Remove-AzRedisCachePatchSchedule](/powershell/module/az.rediscache/remove-azrediscachepatchschedule)

### <a name="what-pricing-tiers-can-use-the-schedule-updates-functionality"></a>Welche Tarife bieten die Funktionalität zum Planen von Updates?
Das Feature **Zeitplanaktualisierungen** ist nur im Premium-Tarif verfügbar.

## <a name="next-steps"></a>Nächste Schritte
* Entdecken Sie weitere Features des [Azure Cache for Redis-Premium-Tarifs](cache-premium-tier-intro.md).

