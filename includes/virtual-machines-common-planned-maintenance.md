---
title: include file
description: include file
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 4/30/2019
ms.author: shants
ms.custom: include file
ms.openlocfilehash: c2931fa410cf92755a5df5b7129dcf93de900930
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178071"
---
Azure aktualisiert seine Plattform regelmäßig, um die Zuverlässigkeit, Leistung und Sicherheit der Hostinfrastruktur für virtuelle Computer zu verbessern. Der Zweck dieser Updates reicht von Patches für Softwarekomponenten in der Hostumgebung über Upgrades für Netzwerkkomponenten bis hin zur Außerbetriebsetzung von Hardware. 

Updates wirken sich kaum auf die gehosteten virtuellen Computer aus. Wenn Updates Auswirkungen haben, wählt Azure das Verfahren mit den geringsten Auswirkungen für Updates aus:

- Wenn ein Update ohne Neustart möglich ist, wird die VM angehalten, während der Host aktualisiert wird, oder sie wird in Echtzeit zu einem bereits aktualisierten Host migriert.

- Wenn die Wartung einen Neustart erfordert, werden Sie über die geplante Wartung informiert. Azure räumt Ihnen außerdem ein Zeitfenster ein, in dem Sie die Wartung zu einem Zeitpunkt Ihrer Wahl selbst starten können. Das Zeitfenster für die selbstständige Wartung umfasst in der Regel 30 Tage, sofern die Wartung nicht dringend ist. Azure setzt sich für Technologien ein, um die Zahl der Fälle zu reduzieren, in denen VMs aus Gründen der planmäßigen Plattformwartung neu gestartet werden müssen. 

Auf dieser Seite erfahren Sie, wie Azure die beiden Wartungsarten durchführt. Weitere Informationen zu ungeplanten Ereignissen (Ausfällen) finden Sie unter  [Verwalten der Verfügbarkeit virtueller Windows-Computer in Azure](../articles/virtual-machines/windows/manage-availability.md) bzw. im entsprechenden Artikel für [Linux](../articles/virtual-machines/linux/manage-availability.md).

In einer VM können Sie [mithilfe von Scheduled Events für Windows](../articles/virtual-machines/windows/scheduled-events.md) oder [Linux](../articles/virtual-machines/linux/scheduled-events.md) Benachrichtigungen zu bevorstehender Wartung erhalten.

Anweisungen zum Verwalten der geplanten Wartung finden Sie unter [Behandlung von Benachrichtigungen der geplanten Wartung für Linux](../articles/virtual-machines/linux/maintenance-notifications.md) oder im entsprechenden Artikel für [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="maintenance-that-doesnt-require-a-reboot"></a>Keinen Neustart erfordernde Wartung

Wie bereits erwähnt, wirken sich die meisten Plattformupdates nicht auf Kunden-VMs aus. Ist es nicht möglich, ein Update ohne Auswirkungen durchzuführen, wählt Azure den Updatemechanismus aus, der die geringsten Auswirkungen auf die Kunden-VMs hat. 

Bei der Wartung mit den geringsten Auswirkungen wird der virtuelle Computer für weniger als 10 Sekunden angehalten. In bestimmten Fällen verwendet Azure Wartungsmechanismen, die den Arbeitsspeicherinhalt beibehalten. Diese Mechanismen halten den virtuellen Computer bis zu 30 Sekunden lang an und behalten den Inhalt des RAM-Speichers bei. Anschließend wird der Betrieb des virtuellen Computers fortgesetzt, und seine Uhr wird automatisch synchronisiert. 

Wartung mit Speicherbeibehaltung funktioniert für mehr als 90% der virtuellen Azure-Computer. Ausnahmen sind lediglich die G-, M-, N- und H-Serie. Azure verwendet in zunehmendem Maß Technologien zur Livemigration und bessere Mechanismen zur Erhaltung des Arbeitsspeichers, um die Anhaltedauer zu verringern.  

Diese Wartungsvorgänge, die keinen Neustart erfordern, werden jeweils auf eine Fehlerdomäne angewendet. Sie werden beendet, wenn sie Integritätswarnsignale erhalten. 

Diese Typen von Updates können sich auf einige Anwendungen auswirken. Wenn der virtuelle Computer live zu einem anderen Host migriert wird, ist einige Minuten vor dem Anhalten der VM bei einigen empfindlichen Workloads unter Umständen eine geringfügige Leistungsbeeinträchtigung feststellbar. Versuchen Sie, [Scheduled Events für Windows](../articles/virtual-machines/windows/scheduled-events.md) oder [Linux](../articles/virtual-machines/linux/scheduled-events.md) für solche Anwendungen zu verwenden, um die VM-Wartung vorzubereiten und Auswirkungen der Azure-Wartung zu reduzieren. Azure arbeitet an Funktionen zur Wartungssteuerung für diese empfindlichen Anwendungen. 

### <a name="live-migration"></a>Livemigration

Die Livemigration erfordert keinen Neustart und behält den Arbeitsspeicherinhalt virtueller Computer bei. Sie bewirkt eine Pause oder ein Einfrieren; dies dauert in der Regel höchstens 5 Sekunden. Mit Ausnahme der Serien G, M, N und H sind alle IaaS-VMs (Infrastructure-as-a-Service) für die Livemigration geeignet. Geeignete virtuelle Computer machen mehr als 90 Prozent der IaaS-VMs aus, die in der Azure-Flotte bereitgestellt werden. 

Die Azure-Plattform startet die Livemigration in den folgenden Szenarien:
- Geplante Wartung
- Hardwarefehler
- Zuordnungsoptimierungen

Einige Szenarien mit geplanter Wartung nutzen die Livemigration, und Sie können Scheduled Events verwenden, um im Voraus zu wissen, wann Livemigrationsvorgänge gestartet werden.

Per Livemigration können auch VMs verschoben werden, wenn Azure Machine Learning-Algorithmen einen Hardwarefehler vorhersagen, oder wenn Sie VM-Zuordnungen optimieren möchten. Weitere Informationen zur Vorhersagemodellierung, die Instanzen von beeinträchtigter Hardware erkennt, finden Sie unter [Improving Azure Virtual Machine resiliency with predictive ML and live migration](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure) (Verbessern der Resilienz von virtuellen Azure-Computern mit Machine Learning zur Vorhersage und Livemigration). Livemigrationsbenachrichtigungen werden im Azure-Portal in den Monitor- und Service Health-Protokollen sowie in Scheduled Events angezeigt, wenn Sie diese Dienste verwenden.

## <a name="maintenance-that-requires-a-reboot"></a>Einen Neustart erfordernde Wartung

Im seltenen Fall, dass virtuelle Computer für eine geplante Wartung neu gestartet werden müssen, werden Sie darüber im Voraus informiert. Die geplante Wartung hat zwei Phasen: die Self-Service-Phase und eine Phase für die geplante Wartung.

Während der *Self-Service-Phase*, die in der Regel vier Wochen dauert, starten Sie die Wartung auf Ihren virtuellen Computern. Im Rahmen des Self-Service können Sie die einzelnen virtuellen Computer abfragen, um ihren Status zu ermitteln und die Ergebnisse Ihrer letzten Wartungsanforderung zu prüfen.

Wenn Sie die Self-Service-Wartung starten, wird Ihre VM auf einem bereits aktualisierten Knoten neu bereitgestellt. Aufgrund des Neustarts des virtuellen Computers geht der temporäre Datenträger verloren, und die der virtuellen Netzwerkschnittstelle zugeordneten dynamischen IP-Adressen werden aktualisiert.

Falls im Rahmen der Self-Service-Wartung ein Fehler auftritt, wird der Vorgang beendet, der virtuelle Computer wird nicht aktualisiert, und Sie erhalten die Möglichkeit, die Self-Service-Wartung erneut durchzuführen. 

Wenn die Self-Service-Phase endet, beginnt die *Phase der geplanten Wartung*. Während dieser Phase können Sie zwar weiterhin die Wartungsphase abfragen, die Wartung aber nicht mehr selbst starten.

Weitere Informationen zum Verwalten der Wartung, die einen Neustart erfordert, finden Sie unter [Behandlung von Benachrichtigungen der geplanten Wartung für Linux](../articles/virtual-machines/linux/maintenance-notifications.md) oder im entsprechenden Artikel für [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Überlegungen zur Verfügbarkeit während einer geplanten Wartung 

Wenn Sie bis zur Phase für die geplante Wartung warten möchten, sollten Sie einige Dinge berücksichtigen, um eine möglichst hohe Verfügbarkeit Ihrer virtuellen Computer zu gewährleisten. 

#### <a name="paired-regions"></a>Regionspaare

Jede Azure-Region ist mit einer anderen Region innerhalb desselben geografischen Gebiets gekoppelt. Zusammen bilden sie ein Regionspaar. Während der Phase der geplanten Wartung aktualisiert Azure nur die virtuellen Computer in einer einzelnen Region eines Regionspaars. Wenn z.B. die VMs in der Region „USA, Norden-Mitte“ aktualisiert werden, aktualisiert Azure nicht gleichzeitig die VMs in der Region „USA, Süden-Mitte“. Andere Regionen wie Nordeuropa können jedoch gleichzeitig mit USA (Ost) gewartet werden. Sie sollten mit der Funktionsweise von Regionspaaren vertraut sein, um Ihre virtuellen Computer besser auf Regionen verteilen zu können. Weitere Informationen finden Sie unter [Geschäftskontinuität und Notfallwiederherstellung: Azure-Regionspaare](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Verfügbarkeitsgruppen und Skalierungsgruppen

Wenn Sie eine Workload auf virtuellen Azure-Computern bereitstellen, können Sie die virtuellen Computer in einer *Verfügbarkeitsgruppe* erstellen, um die Hochverfügbarkeit der Anwendung zu erreichen. Mit Verfügbarkeitsgruppen können Sie sicherstellen, dass während eines Ausfalls oder Wartungsereignissen, die einen Neustart erfordern, mindestens ein virtueller Computer verfügbar ist.

Innerhalb einer Verfügbarkeitsgruppe werden einzelne virtuelle Computer auf bis zu 20 Updatedomänen (UDs) verteilt. Während einer geplanten Wartung wird jeweils nur eine Updatedomäne aktualisiert. Updatedomänen (UDs) werden nicht unbedingt sequenziell aktualisiert. 

VM-*Skalierungsgruppen* sind eine Azure-Computeressource, mit der Sie eine Gruppe identischer virtueller Computer als Einzelressource bereitstellen und verwalten können. Die Skalierungsgruppe wird automatisch Updatedomänen übergreifend bereitgestellt – genau wie virtuelle Computer in einer Verfügbarkeitsgruppe. Genau wie bei Verfügbarkeitsgruppen wird auch bei Skalierungsgruppen während einer geplanten Wartung immer nur jeweils eine UD aktualisiert.

Weitere Informationen zur Einrichtung Ihrer VMs für hohe Verfügbarkeit finden Sie unter  [Verwalten der Verfügbarkeit virtueller Windows-Computer in Azure](../articles/virtual-machines/windows/manage-availability.md) bzw. im entsprechenden Artikel für [Linux](../articles/virtual-machines/linux/manage-availability.md).
