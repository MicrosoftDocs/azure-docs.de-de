---
title: include file
description: include file
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: be71b269e618d13a126e4005754b307e9c6517d7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178085"
---
## <a name="azure-backup"></a>Azure Backup

Verwenden Sie Azure Backup zum Sichern von Azure-VMs, auf denen Produktionsworkloads ausgeführt werden. Azure Backup unterstützt anwendungskonsistente Sicherungen für virtuelle Windows- und Linux-Computer. Azure Backup erstellt Wiederherstellungspunkte, die in geografisch redundanten Recovery-Tresoren gespeichert werden. Wenn Sie eine Wiederherstellung von einem Wiederherstellungspunkt durchführen, können Sie den gesamten virtuellen Computer oder nur bestimmte Dateien wiederherstellen. 

Eine einfache praktische Einführung in Azure Backup für Azure-VMs finden Sie im Tutorial zum Sichern virtueller Azure-Computer für [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) oder [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md).

Weitere Informationen dazu, wie Azure Backup funktioniert, finden Sie unter [Planen der Sicherungsinfrastruktur für virtuelle Computer in Azure](../articles/backup/backup-azure-vms-introduction.md).


## <a name="azure-site-recovery"></a>Azure Site Recovery

Mit Azure Site Recovery werden Ihre VMs vor einem Katastrophenszenario geschützt, bei dem eine ganze Region aufgrund einer Naturkatastrophe oder einer umfangreichen Dienstunterbrechung von einem Ausfall betroffen ist. Sie können Azure Site Recovery für Ihre virtuellen Computer konfigurieren, damit Sie Ihre Anwendung mit einem einfachen Mausklick innerhalb weniger Minuten wiederherstellen können. Sie können in eine Azure-Region Ihrer Wahl replizieren und sind nicht auf gekoppelte Regionen beschränkt. 

Sie können Übungen zur Notfallwiederherstellung mit bedarfsgesteuerten Testfailovern durchführen, ohne dass Ihre Produktionsworkloads oder die laufende Replikation beeinträchtigt wird. Erstellen Sie Wiederherstellungspläne, um das Failover und Failback der gesamten Anwendung zu orchestrieren, die auf mehreren VMs ausgeführt wird. Das Wiederherstellungsplan-Feature ist in Azure Automation-Runbooks integriert.

Als ersten Schritt können Sie [Ihre virtuellen Computer replizieren](https://aka.ms/a2a-getting-started). 

## <a name="managed-snapshots"></a>Verwaltete Momentaufnahmen 

In Entwicklungs- und Testumgebungen sind Momentaufnahmen eine schnelle und einfache Möglichkeit zum Sichern von VMs, für die Managed Disks verwendet werden. Eine verwaltete Momentaufnahme ist eine schreibgeschützte vollständige Kopie eines verwalteten Datenträgers. Momentaufnahmen existieren unabhängig vom Quelldatenträger und können zum Erstellen von neuen verwalteten Datenträgern verwendet werden, um eine VM neu zu erstellen. Die Abrechnung erfolgt basierend auf dem genutzten Teil des Datenträgers. Wenn Sie beispielsweise eine Momentaufnahme eines verwalteten Datenträgers mit einer bereitgestellten Kapazität von 64 GB und einer tatsächlichen Datengröße von 10 GB erstellen, wird die Momentaufnahme nur für die in Anspruch genommene Datengröße von 10 GB in Rechnung gestellt.  

Weitere Informationen zum Erstellen von Momentaufnahmen finden Sie unter:

* [Erstellen einer Kopie einer als verwalteter Datenträger gespeicherten virtuellen Festplatte mithilfe von Momentaufnahmen unter Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Erstellen einer Kopie einer als verwalteter Datenträger gespeicherten virtuellen Festplatte mithilfe von Momentaufnahmen unter Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)



## <a name="next-steps"></a>Nächste Schritte
Sie können Azure Backup ausprobieren, indem Sie das Tutorial „Sichern virtueller Windows-Computer in Azure“ für [Linux](../articles/virtual-machines/linux/tutorial-backup-vms.md) oder [Windows](../articles/virtual-machines/windows/tutorial-backup-vms.md) durcharbeiten.
