---
title: Verwalten der Verfügbarkeit von virtuellen Computern
description: Hier erfahren Sie, wie Sie mehrere virtuelle Computer verwenden, um Hochverfügbarkeit für die Anwendungen in Azure sicherzustellen.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cynthn
ms.openlocfilehash: 4dd15df0e745a5c6e3130233e693bbdb22655775
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500443"
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>Verwalten der Verfügbarkeit virtueller Linux-Computer

Erfahren Sie, wie Sie mehrere virtuelle Computer einrichten und verwalten können, um Hochverfügbarkeit für Ihre Linux-Anwendung in Azure sicherzustellen. 


## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Grundlegendes zu VM-Neustarts – Gegenüberstellung von Wartung und Ausfallzeit
Drei Szenarien können zu einer Beeinträchtigung virtueller Computer in Azure führen: eine ungeplante Hardwarewartung, eine unerwartete Ausfallzeit und eine geplante Wartung.

* **Ungeplante Hardwarewartung:** Tritt auf, wenn die Azure-Plattform den Ausfall einer Hardware- oder Plattformkomponente für einen physischen Computer prognostiziert. Daraufhin wird ein Ereignis für eine ungeplante Hardwarewartung initiiert, um die Auswirkungen auf virtuelle Computer, die die betroffene Hardware nutzen, möglichst gering zu halten. Die virtuellen Computer werden von Azure unter Verwendung von [Livemigrationstechnologie](./maintenance-and-updates.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json%252c%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json%253ftoc%253d%2fazure%2fvirtual-machines%2flinux%2ftoc.json) von der fehlerhaften Hardware zu einem fehlerfreien physischen Computer migriert. Bei der Livemigration wird der virtuelle Computer lediglich kurzzeitig angehalten. Arbeitsspeicher, geöffnete Dateien und bestehende Netzwerkverbindungen bleiben erhalten, die Leistung kann jedoch vor und/oder nach dem Ereignis beeinträchtigt sein. Falls die Livemigration nicht verwendet werden kann, kommt es bei dem virtuellen Computer wie unten beschrieben zu unerwarteter Ausfallzeit.


* **Unerwartete Ausfallzeiten** treten auf, wenn die Hardware oder die physische Infrastruktur für den virtuellen Computer unvorhergesehen ausfällt. Dies kann Ausfälle des lokalen Netzwerks, des lokalen Datenträgers oder andere Fehler auf Rackebene umfassen. Bei Erkennung solcher Probleme migriert die Azure-Plattform Ihren virtuellen Computer automatisch zu einem fehlerfreien physischen Computer im selben Rechenzentrum. Dieser Vorgang ist mit einer gewissen Ausfallzeit (Neustart) und in manchen Fällen mit dem Verlust des temporären Laufwerks verbunden. Die angefügten (Betriebssystem-)Datenträger bleiben in jedem Fall erhalten.

  Für virtuelle Computer kann es im unwahrscheinlichen Fall eines Ausfalls bzw. Notfalls, von dem ein gesamtes Datencenter oder sogar eine ganze Region betroffen ist, auch zu Ausfallzeiten kommen. Für diese Szenarien verfügt Azure über Schutzoptionen, z.B. [Verfügbarkeitszonen](../availability-zones/az-overview.md) und [Regionspaare](regions.md#region-pairs).

* **Geplante Wartungsereignisse:** Hierbei handelt es sich um regelmäßige Updates, die von Microsoft für die zugrunde liegende Azure-Plattform ausgeführt werden können, um die Verfügbarkeit, Leistung und Sicherheit der Plattforminfrastruktur, unter der die virtuellen Computer ausgeführt werden, zu verbessern. Die meisten dieser Updates werden ohne Auswirkungen auf die virtuellen Computer oder Clouddienste ausgeführt. (Weitere Informationen finden Sie unter [Keinen Neustart erfordernde Wartung](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)). Die Azure-Plattform versucht zwar, möglichst immer die Wartung mit direkter Migration und Beibehaltung der virtuellen Computer zu verwenden, in seltenen Fällen ist jedoch ein Neustart des virtuellen Computers erforderlich, um die erforderlichen Updates auf die zugrunde liegende Infrastruktur anzuwenden. In diesem Fall können Sie eine geplante Azure-Wartung mit erneuter Bereitstellung nach der Wartung durchführen, indem Sie die Wartung für die entsprechenden virtuellen Computer im geeigneten Zeitfenster initiieren. Weitere Informationen finden Sie unter [Geplante Wartung für virtuelle Windows-Computer](maintenance-and-updates.md).


Um die Downtime aufgrund eines oder mehrerer dieser Ereignisse zu verringern, sollten Sie die folgenden bewährten Methoden befolgen, um die Hochverfügbarkeit virtueller Computer zu gewährleisten:

* Verwenden von Verfügbarkeitszonen als Schutz vor Rechenzentrumsausfällen
* Konfigurieren mehrerer virtueller Computer in einer Verfügbarkeitsgruppe für höhere Redundanz
* Verwenden von verwalteten Datenträgern für virtuelle Computer in einer Verfügbarkeitsgruppe
* Verwenden von Scheduled Events für proaktive Antworten auf Ereignisse, die sich auf virtuelle Computer auswirken
* Konfigurieren einzelner Anwendungsebenen in separaten Verfügbarkeitsgruppen
* Kombinieren eines Lastenausgleichs mit Verfügbarkeitszonen oder -gruppen
* Verwenden von Verfügbarkeitszonen als Schutz vor Ausfällen auf Datencenterebene

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Verwenden von Verfügbarkeitszonen als Schutz vor Ausfällen auf Datencenterebene

Mit [Verfügbarkeitszonen](../availability-zones/az-overview.md) erhalten Sie mehr Kontrolle beim Aufrechterhalten der Verfügbarkeit von Anwendungen und Daten auf Ihren virtuellen Computern. Verfügbarkeitszonen sind eindeutige physische Standorte in einer Azure-Region. Jede Zone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig funktionieren. Zur Gewährleistung der Resilienz sind in allen aktivierten Regionen mindestens drei separate Zonen vorhanden. Die physische Trennung von Verfügbarkeitszonen innerhalb einer Region schützt Anwendungen und Daten vor Ausfällen von Rechenzentren. Zonenredundante Dienste replizieren Ihre Anwendungen und Daten zum Schutz vor einzelnen Fehlerquellen über Verfügbarkeitszonen hinweg.

Eine Verfügbarkeitszone in einer Azure-Region ist eine Kombination aus einer **Fehlerdomäne** und einer **Updatedomäne**. Wenn Sie z.B. drei oder mehr virtuelle Computer über drei Zonen verteilt in einer Azure-Region erstellen, werden Ihre virtuellen Computer effektiv auf drei Fehlerdomänen und drei Updatedomänen verteilt. Die Azure-Plattform erkennt diese Updatedomänen übergreifende Verteilung, um sicherzustellen, dass virtuelle Computer in unterschiedlichen Zonen nicht gleichzeitig aktualisiert werden.

Mit Verfügbarkeitszonen bietet Azure die branchenweit beste Betriebszeit-SLA von 99,99 % für VMs. Indem Sie Ihre Lösungen für die Verwendung von replizierten VMs in Zonen gestalten, können Sie Ihre Anwendungen und Daten vor dem Verlust eines Datencenters schützen. Wenn eine Zone kompromittiert ist, sind replizierte Apps und Daten sofort in einer anderen Zone verfügbar.

![Verfügbarkeitszonen](./media/virtual-machines-common-manage-availability/three-zones-per-region.png)

Informieren Sie sich über das Bereitstellen einer [Windows](./windows/create-powershell-availability-zone.md)- oder [Linux](./linux/create-cli-availability-zone.md)-VM in einer Verfügbarkeitszone.

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Konfigurieren mehrerer virtueller Computer in einer Verfügbarkeitsgruppe für höhere Redundanz
Verfügbarkeitsgruppen sind eine weitere Datencenterkonfiguration, um VM-Redundanz und -Verfügbarkeit bereitzustellen. Durch diese Konfiguration in einem Datencenter wird sichergestellt, dass während eines geplanten oder ungeplanten Wartungsereignisses mindestens ein virtueller Computer verfügbar ist und die von der Azure-SLA zugesicherte Verfügbarkeit von 99,95 Prozent eingehalten wird. Weitere Informationen finden Sie unter [SLA für Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Ein virtueller Computer mit einer einzelnen Instanz in einer selbst festgelegten Verfügbarkeit sollte für alle Datenträger für Betriebssysteme und Daten entweder SSD Premium oder Ultra Disk verwenden, um sich für die SLA für die Konnektivität von virtuellen Computern von mindestens 99,9 % zu qualifizieren. 
> 
> Für einen virtuellen Einzelinstanzcomputer mit SSD Standard gilt eine SLA von mindestens 99,5 Prozent, für einen virtuellen Einzelinstanzcomputer mit HDD Standard hingegen eine SLA von mindestens 95 Prozent.  Siehe [SLA für Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/)

Jeder virtuelle Computer in der Verfügbarkeitsgruppe wird einer **Updatedomäne** (UD) und einer **Fehlerdomäne** (FD) der zugrunde liegenden Azure-Plattform zugewiesen. Für eine bestimmte Verfügbarkeitsgruppe werden fünf nicht von Benutzern konfigurierbare Updatedomänen standardmäßig zugewiesen (Resource Manager-Bereitstellungen können dann vergrößert werden, um bis zu 20 Updatedomänen bereitzustellen), um die Gruppen mit virtuellen Computern und die zugehörigen physischen Hardwareelemente zu kennzeichnen, die gleichzeitig neu gestartet werden können. Wenn innerhalb einer Verfügbarkeitsgruppe mehr als fünf virtuelle Computer konfiguriert sind, wird der sechste virtuelle Computer in derselben Updatedomäne wie der erste virtuelle Computer gespeichert, der siebte in derselben Updatedomäne wie der zweite virtuelle Computer usw. Während einer geplanten Wartung werden die Updatedomänen unter Umständen nicht der Reihe nach neu gestartet, sondern es wird jeweils nur eine Updatedomäne neu gestartet. Bei einer neu gestarteten Updatedomäne wird 30 Minuten gewartet, bevor die Wartung für eine andere Updatedomäne initiiert wird.

Mit Fehlerdomänen wird die Gruppe der virtuellen Computer definiert, die eine Stromquelle und einen Netzwerkswitch gemeinsam nutzen. Standardmäßig sind die innerhalb Ihrer Verfügbarkeitsgruppe konfigurierten virtuellen Computer auf bis zu drei Fehlerdomänen bei Resource Manager-Bereitstellungen verteilt (zwei Fehlerdomänen bei klassischen Bereitstellungen). Auch wenn Verfügbarkeitsgruppen Ihre Anwendung nicht gänzlich vor Fehlern des Betriebssystems oder der Anwendung selbst schützen können, verringern sie doch die Auswirkungen von potenziellen Hardwarefehlern, Netzwerkausfällen oder Stromunterbrechungen.

<!--Image reference-->
   ![Schematische Darstellung der Konfiguration mit Updatedomäne und Fehlerdomäne](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Verwenden von verwalteten Datenträgern für virtuelle Computer in einer Verfügbarkeitsgruppe
Es wird empfohlen, dass Sie für [Linux](./linux/convert-unmanaged-to-managed-disks.md) und [Windows](./windows/convert-unmanaged-to-managed-disks.md) von nicht verwalteten zu verwalteten Datenträgern wechseln, wenn Sie derzeit virtuelle Computer mit nicht verwalteten Datenträgern verwenden.

[Managed Disks](./managed-disks-overview.md) ermöglicht eine bessere Zuverlässigkeit für Verfügbarkeitsgruppen, indem sichergestellt wird, dass die Datenträger virtueller Computer in einer Verfügbarkeitsgruppe ausreichend voneinander isoliert sind, um einzelne Fehlerquellen zu vermeiden. Dies wird automatisch durch Platzieren der Datenträger in verschiedenen Speicherfehlerdomänen (Speicherclustern) und Anpassen an die VM-Fehlerdomäne sichergestellt. Wenn eine Speicherfehlerdomäne aufgrund eines Hardware- oder Softwarefehlers ausfällt, tritt der Fehler nur bei der VM-Instanz mit Datenträgern in dieser Speicherfehlerdomäne auf.
![Fehlerdomänen für verwaltete Datenträger](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> Die Anzahl von Fehlerdomänen für verwaltete Verfügbarkeitsgruppen variieren je nach Region: zwei oder drei pro Region. Sie können die Fehlerdomäne für jede Region anzeigen, indem Sie die folgenden Skripts ausführen.

```azurepowershell-interactive
Get-AzComputeResourceSku | where{$_.ResourceType -eq 'availabilitySets' -and $_.Name -eq 'Aligned'}
```

```azurecli-interactive 
az vm list-skus --resource-type availabilitySets --query '[?name==`Aligned`].{Location:locationInfo[0].location, MaximumFaultDomainCount:capabilities[0].value}' -o Table
```

> [!NOTE]
> Unter bestimmten Umständen nutzen zwei virtuelle Computer in derselben Verfügbarkeitsgruppe möglicherweise dieselbe Fehlerdomäne. Sie können überprüfen, ob eine Fehlerdomäne gemeinsam genutzt wird, indem Sie zur betreffenden Verfügbarkeitsgruppe wechseln und sich die Spalte **Fehlerdomäne** ansehen. Es kann zur gemeinsamen Nutzung einer Fehlerdomäne kommen, wenn Sie bei der Bereitstellung der virtuellen Computer die folgenden Schritte ausgeführt haben:
> 1. Bereitstellen des ersten virtuellen Computers
> 1. Beenden des virtuellen Computers oder Aufheben seiner Zuordnung
> 1. Bereitstellen des zweiten virtuellen Computers
>
> Unter diesen Umständen wird der Betriebssystemdatenträger des zweiten virtuellen Computers möglicherweise in derselben Fehlerdomäne wie der des ersten virtuellen Computers erstellt, sodass sich die beiden virtuellen Computer in derselben Fehlerdomäne befinden. Um dieses Problem zu vermeiden, wird empfohlen, die virtuellen Computer zwischen den Bereitstellungen nicht zu beenden bzw. ihre Zuordnung nicht aufzuheben.

Gehen Sie wie folgt vor, wenn Sie planen, VMs mit nicht verwalteten Datenträgern zu verwenden: Halten Sie sich an die unten angegebenen bewährten Methoden für Storage-Konten, bei denen virtuelle Festplatten (VHDs) von VMs als [Seitenblobs](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) gespeichert werden.

1. **Alle Datenträger (Betriebssystem und Daten) müssen einem virtuellen Computer im selben Speicherkonto zugeordnet sein**
2. **Überprüfen Sie die [Grenzwerte](../storage/blobs/scalability-targets-premium-page-blobs.md) für die Anzahl von nicht verwalteten Datenträgern eines Azure Storage-Kontos**, bevor Sie einem Speicherkonto weitere VHDs hinzufügen.
3. **Verwenden Sie mehrere Speicherkonten für jeden virtuellen Computer in einer Verfügbarkeitsgruppe.** Geben Sie Storage-Konten mit mehreren VMs in derselben Verfügbarkeitsgruppe nicht für die gemeinsame Nutzung frei. Für VMs, die über verschiedene Verfügbarkeitsgruppen hinweg verteilt sind, können Speicherkonten freigegeben werden, solange die oben genannten bewährten Methoden befolgt werden. ![Fehlerdomänen nicht verwalteter Datenträger](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>Verwenden von Scheduled Events für proaktive Antworten auf Ereignisse, die sich auf virtuelle Computer auswirken

Wenn Sie [geplante Ereignisse](./linux/scheduled-events.md) abonnieren, wird Ihr virtueller Computer über geplante Wartungsereignisse unterrichtet, die Ihren virtuellen Computer beeinträchtigen können. Wenn geplante Ereignisse aktiviert sind, erhält Ihr virtueller Computer ein Mindestmaß an Zeit, bevor die Wartungsaktivität ausgeführt wird. Beispielsweise werden Host-BS-Updates, die Ihren virtuellen Computer beeinträchtigen könnten, als Ereignisse in die Warteschlange gestellt, die die Auswirkung sowie den Zeitpunkt angeben, zu dem die Wartung durchgeführt wird, wenn keine Aktion ausgeführt wird. Geplante Ereignisse werden auch in eine Warteschlange gestellt, wenn Azure den bevorstehenden Hardwarefehler erkennt, der sich auf Ihren virtuellen Computer auswirken könnte, sodass Sie entscheiden können, wann die Reparatur ausgeführt werden soll. Kunden können das Ereignis nutzen, um Aufgaben vor der Wartung auszuführen, z.B. Speichern des Status, Failover zum sekundären Standort usw. Nachdem Sie Ihre Logik zur ordnungsgemäßen Behandlung des Wartungsereignisses fertig gestellt haben, können Sie das ausstehende geplante Ereignis genehmigen, um der Plattform zu ermöglichen, die Wartung fortzusetzen.


## <a name="combine-a-load-balancer-with-availability-zones-or-sets"></a>Kombinieren eines Lastenausgleichs mit Verfügbarkeitszonen oder -gruppen
Kombinieren Sie den Azure-Lastenausgleich ([Azure Load Balancer](../load-balancer/load-balancer-overview.md)) mit einer Verfügbarkeitszone oder -gruppe, um höchste Anwendungsresilienz zu erzielen. Der Azure-Lastenausgleich verteilt den Datenverkehr auf mehrere virtuelle Computer. In die virtuellen Computer der Standardebene ist der Azure-Lastenausgleich bereits integriert. Der Azure Load Balancer ist aber nicht auf allen Ebenen des virtuellen Computers verfügbar. Weitere Informationen zum Lastenausgleich zwischen virtuellen Computern finden Sie unter **Lastenausgleich zwischen virtuellen Computern** für [Linux](linux/tutorial-load-balancer.md) oder [Windows](windows/tutorial-load-balancer.md).

Wenn der Lastenausgleich nicht für die gleichmäßige Verteilung des Datenverkehrs auf mehrere virtuelle Computer konfiguriert ist, wirkt sich ein geplantes Wartungsereignis schließlich auf den einzigen virtuellen Computer, der den Datenverkehr aufrechterhält, aus und führt zu einem Ausfall der Anwendungsebene. Werden dagegen mehrere virtuelle Computer derselben Ebene demselben Lastenausgleich und derselben Verfügbarkeitsgruppe zugeordnet, wird der Datenverkehr kontinuierlich von mindestens einer Instanz aufrechterhalten.

Ein Tutorial zum Lastenausgleich zwischen Verfügbarkeitszonen finden Sie unter [Tutorial: Verfügbarkeitszonenübergreifender Lastenausgleich für VMs mit einer Load Balancer Standard-Instanz im Azure-Portal](../load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal.md).


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Lastenausgleich zwischen virtuellen Computern finden Sie unter [Lastenausgleich für virtuelle Computer](../load-balancer/load-balancer-overview.md).
