---
title: Funktionsweise von Azure NetApp Files-Momentaufnahmen | Microsoft-Dokumentation
description: Erfahren Sie, wie Azure NetApp Files-Momentaufnahmen funktionieren, welche Möglichkeiten es zum Erstellen von Momentaufnahmen gibt, wie Momentaufnahmen wiederhergestellt werden und wie Sie Momentaufnahmen in regionsübergreifenden Replikationseinstellungen verwenden.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: b-juche
ms.openlocfilehash: 526ef0af08833954aef4136716930cec0df40eea
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625246"
---
# <a name="how-azure-netapp-files-snapshots-work"></a>Funktionsweise von Azure NetApp Files-Momentaufnahmen

In diesem Artikel wird die Funktionsweise von Azure NetApp Files-Momentaufnahmen erläutert. Die Momentaufnahmetechnologie von Azure NetApp Files bietet Stabilität, Skalierbarkeit und eine schnellere Wiederherstellbarkeit, ohne dass sich dies auf die Leistung auswirkt. Die Technologie hinter Azure NetApp Files-Momentaufnahmen bildet die Grundlage für Lösungen zum Schutz von Daten, einschließlich des Wiederherstellens einzelner Dateien, des Wiederherstellens und Klonens von Volumes sowie des Replizierens über Regionen hinweg. 

Die Schritte zum Verwenden von Volumemomentaufnahmen finden Sie unter [Verwalten von Momentaufnahmen mithilfe von Azure NetApp Files](azure-netapp-files-manage-snapshots.md). Überlegungen zur Verwaltung von Momentaufnahmen bei der regionsübergreifenden Replikation finden Sie unter [Regionsübergreifende Replikation: Anforderungen und Überlegungen](cross-region-replication-requirements-considerations.md).

## <a name="what-volume-snapshots-are"></a>Definition von Volumemomentaufnahmen  

Eine Azure NetApp Files-Momentaufnahme ist ein Image eines Dateisystems (Volumes) zu einem bestimmten Zeitpunkt. Sie kann als ideale Onlinesicherung dienen. Sie können mit einer Momentaufnahme ein [neues Volume erstellen](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume), eine [Datei wiederherstellen](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client) oder ein [Volume wiederherstellen](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert). Bestimmte Anwendungsdaten, die auf Azure NetApp Files-Volumes gespeichert sind, erfordern möglicherweise zusätzliche Schritte, um die Anwendungskonsistenz sicherzustellen.  

Der geringe Mehraufwand für die Momentaufnahmen wird durch die einzigartigen Funktionen der zugrunde liegenden Volumevirtualisierungstechnologie ermöglicht, die Teil von Azure NetApp Files ist. Wie bei einer Datenbank nutzt diese Schicht Zeiger auf die eigentlichen Datenblöcke auf dem Datenträger. Anders als bei einer Datenbank werden vorhandene Blöcke jedoch nicht erneut generiert. Stattdessen werden die aktualisierten Daten in einen neuen Block geschrieben, und anschließend wird der Zeiger geändert. Eine Azure NetApp Files-Momentaufnahme passt also nur die Blockzeiger an. Dadurch entsteht eine „fixierte“, schreibgeschützte Ansicht eines Volumes, über die Anwendungen ohne spezielle Programmierung auf ältere Versionen von Dateien und Verzeichnishierarchien zugreifen können. Es werden keine Datenblöcke kopiert. Aus diesem Grund sind Momentaufnahmen beim Erstellen so effizient. Ihre Generierung erfolgt unabhängig von der Volumegröße nahezu unmittelbar. Darüber hinaus sind Momentaufnahmen auch beim Speicherplatz effizient. Sie beanspruchen selbst keinen Speicherplatz, da nur die Deltablöcke zwischen den Momentaufnahmen und dem aktiven Volume beibehalten werden. 

Die folgenden Diagramme veranschaulichen die Konzepte:  

![Diagramme der wichtigsten Konzepte von Momentaufnahmen](../media/azure-netapp-files/snapshot-concepts.png)

In den Diagrammen wird in Abbildung 1a eine Momentaufnahme erstellt. In Abbildung 1b werden geänderte Daten in einen *neuen Block* geschrieben, und der Zeiger wird aktualisiert. Der Momentaufnahmezeiger verweist aber immer noch auf den *zuvor geschriebenen Block*, sodass Sie eine aktuelle und eine historische Ansicht der Daten erhalten. In Abbildung 1c wird eine weitere Momentaufnahme erstellt. Damit haben Sie Zugriff auf drei Generationen der Daten (die Livedaten, Momentaufnahme 2 und Momentaufnahme 1, in der Reihenfolge ihres Alters), ohne den Volumespeicherplatz in Anspruch zu nehmen, der für drei vollständige Kopien nötig wäre. 

Für eine Momentaufnahme wird nur eine Kopie der Volumemetadaten (*I-Knoten-Tabelle*) erstellt. Die Erstellung dauert nur wenige Sekunden, und zwar unabhängig von der Volumegröße, der genutzten Kapazität und dem Grad an Aktivität auf dem Volume. Daher nimmt das Erstellen einer Momentaufnahme eines 100-TiB-Volumes die gleiche (annähernd keine) Zeit in Anspruch wie eine Momentaufnahme eines 100-GiB-Volumes. Nachdem eine Momentaufnahme erstellt wurde, spiegeln sich Änderungen an den Datendateien wie üblich in der aktiven Version der Dateien wider.

Gleichzeitig bleiben die Datenblöcke, auf die von einer Momentaufnahme verwiesen wird, stabil und unverändert. Aufgrund dieser „Umleitung beim Schreiben“ von Azure NetApp Files-Volumes entsteht bei der Erstellung einer Momentaufnahme kein Mehraufwand an Leistung, und es wird selbst kein Speicherplatz beansprucht. Sie können im Lauf der Zeit bis zu 255 Momentaufnahmen pro Volume speichern, auf die jeweils als schreibgeschützte und Onlineversion der Daten zugegriffen werden kann. Sie belegen aber nur so viel Kapazität, wie Blöcke zwischen den einzelnen Momentaufnahmen geändert wurden. Geänderte Blöcke werden im aktiven Volume gespeichert. Blöcke, auf die in Momentaufnahmen verwiesen wird, werden (schreibgeschützt) auf dem Volume aufbewahrt und nur dann erneut verwendet, wenn alle Zeiger im aktiven Volume und in den Momentaufnahmen gelöscht wurden. Daher steigt die Volumeauslastung im Lauf der Zeit durch neue Datenblöcke oder (geänderte) Datenblöcke, die in Momentaufnahmen aufbewahrt werden.

 Im folgenden Diagramm werden die Momentaufnahmen eines Volumes und der verwendete Speicherplatz im Lauf der Zeit gezeigt: 

![Diagramm der Momentaufnahmen eines Volumes und der verwendete Speicherplatz im Zeitverlauf](../media/azure-netapp-files/snapshots-used-space-over-time.png)

Da in einer Volumemomentaufnahme nur die Blockänderungen seit der letzten Momentaufnahme aufgezeichnet werden, bietet sie die folgenden wichtigen Vorteile:

* Momentaufnahmen sind ***speichereffizient***.   
    Momentaufnahmen verbrauchen nur minimalen Speicherplatz, da nicht die Datenblöcke des gesamten Volumes kopiert werden. Zwei nacheinander erstellte Momentaufnahmen unterscheiden sich nur in den Blöcken, die im Zeitraum zwischen den beiden hinzugefügt oder geändert wurden. Durch dieses blockbezogene, inkrementelle Verhalten wird die verbrauchte Speicherkapazität reduziert. Viele alternative Implementierungen für Momentaufnahmen verbrauchen Speichervolumes, die dem aktiven Dateisystem entsprechen, und erhöhen so die Speicherkapazitätsanforderungen. Abhängig von der täglichen Änderungsrate auf *Blockebene* von Anwendungen verbrauchen Azure NetApp Files-Momentaufnahmen mehr oder weniger Kapazität, jedoch nur für die geänderten Daten. Der durchschnittliche Verbrauch liegt bei täglichen Momentaufnahmen auf vielen Anwendungsvolumes bei 1–5 % der genutzten Volumekapazität bzw. bei SAP HANA-Datenbankvolumes bei 20–30 %. [Überwachen Sie den Volume- und Momentaufnahmeverbrauch](azure-netapp-files-metrics.md#volumes) auf den Kapazitätsverbrauch von Momentaufnahmen im Verhältnis zur Anzahl der erstellten und verwalteten Momentaufnahmen.   

* Momentaufnahmen können ***schnell erstellt, repliziert, wiederhergestellt oder geklont*** werden.   
    Es dauert nur wenige Sekunden, eine Momentaufnahme zu erstellen, zu replizieren, wiederherzustellen oder zu klonen. Außerdem ist diese Zeit unabhängig von der Volumegröße und dem Grad an Aktivität. Sie können Volumemomentaufnahmen [bei Bedarf](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume) erstellen. Sie können auch [Momentaufnahmerichtlinien](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) verwenden, um anzugeben, wann Azure NetApp Files automatisch eine Momentaufnahme erstellen soll und wie viele Momentaufnahmen für ein Volume aufbewahrt werden sollen.  Anwendungskonsistenz erreichen Sie durch die Orchestrierung von Momentaufnahmen mit der Anwendungsschicht, beispielsweise durch die Verwendung des Tools [AzAcSnap](azacsnap-introduction.md) für SAP HANA.

* Momentaufnahmen haben keine Auswirkung auf die ***Leistung*** des Volumes.   
    Da die zugrunde liegende Technologie eine „Umleitung beim Schreiben“ nutzt, hat das Speichern oder Beibehalten von Azure NetApp Files-Momentaufnahmen keinerlei Auswirkungen auf die Leistung – selbst bei einer hohen Datenaktivität. Auch das Löschen einer Momentaufnahme hat in den meisten Fällen nur geringe oder gar keine Auswirkungen auf die Leistung. 

* Momentaufnahmen bieten ***Skalierbarkeit***, da sie häufig erstellt und meistens beibehalten werden können.   
    Azure NetApp Files-Volumes unterstützen bis zu 255 Momentaufnahmen. Die Möglichkeit, eine große Anzahl von häufig erstellten Momentaufnahmen mit geringer Auswirkung zu speichern, erhöht die Wahrscheinlichkeit, dass die gewünschte Version der Daten erfolgreich wiederhergestellt werden kann.

* Momentaufnahmen bieten ***Sichtbarkeit für Benutzer** _ und ermöglichen das _*_Wiederherstellen von Dateien_**.   
Die hohe Leistung, Skalierbarkeit und Stabilität der Azure NetApp Files-Momentaufnahmetechnologie macht sie zur idealen Option für die Onlinesicherung zur benutzergesteuerten Wiederherstellung. Momentaufnahmen können Benutzern für das Wiederherstellen von Dateien, Verzeichnissen oder Volumes verfügbar gemacht werden. Mit zusätzlichen Lösungen können Sie Sicherungen in den Offlinespeicher kopieren oder zum Zweck der Aufbewahrung oder Notfallwiederherstellung [zwischen Regionen replizieren](cross-region-replication-introduction.md).

## <a name="ways-to-create-snapshots"></a>Optionen zum Erstellen von Momentaufnahmen   

Sie können verschiedene Methoden zum Erstellen und Verwalten von Momentaufnahmen verwenden:

* Manuell (bedarfsgesteuert) mit:   
    * [Azure-Portal](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume), [REST-API](/rest/api/netapp/snapshots), [Azure-Befehlszeilenschnittstelle](/cli/azure/netappfiles/snapshot) oder [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshot)-Tools
    * Skripts (siehe [Beispiele](azure-netapp-files-solution-architectures.md#sap-tech-community-and-blog-posts))

* Automatisiert mit:
    * Momentaufnahmerichtlinien mithilfe von [Azure-Portal](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies), [REST-API](/rest/api/netapp/snapshotpolicies), [Azure-Befehlszeilenschnittstelle](/cli/azure/netappfiles/snapshot/policy) oder [PowerShell](/powershell/module/az.netappfiles/new-aznetappfilessnapshotpolicy)-Tools
    * Tool für anwendungskonsistente Momentaufnahmen wie [AzAcSnap](azacsnap-introduction.md)

## <a name="how-volumes-and-snapshots-are-replicated-cross-region-for-dr"></a>Regionsübergreifendes Replizieren von Volumes und Momentaufnahmen für die Notfallwiederherstellung  

Azure NetApp Files unterstützt die [regionsübergreifende Replikation](cross-region-replication-introduction.md) für die Notfallwiederherstellung. Für die regionsübergreifende Replikation nutzt Azure NetApp Files die SnapMirror-Technologie. Dabei werden nur die geänderten Blöcke in einem komprimierten, effizienten Format über das Netzwerk gesendet. Nachdem eine regionsübergreifende Replikation zwischen Volumes initiiert wurde, wird nur einmal der gesamte Volumeinhalt (d. h. die tatsächlich gespeicherten Datenblöcke) übertragen. Dieser Vorgang wird als *Baselinetransfer* bezeichnet. Nach der ersten Übertragung werden nur noch geänderte Blöcke (die in Momentaufnahmen aufgezeichnet werden) übertragen. Das Ergebnis ist ein asynchrones 1:1-Replikat des Quellvolumes einschließlich aller Momentaufnahmen. Dieses Verhalten folgt einem vollständigen und dauerhaft inkrementellen Replikationsmechanismus. Diese Technologie minimiert die Menge an Daten, die regionsübergreifend repliziert werden muss, wodurch Datenübertragungskosten eingespart werden. Außerdem verkürzt sie die Replikationszeit. Sie können ein kleineres Recovery Point Objective (RPO) erreichen, da mehr Momentaufnahmen erstellt und mit geringeren Datenübertragungen häufiger übertragen werden können. Außerdem müssen keine hostbasierten Replikationsmechanismen mehr angewandt werden, sodass auch die Kosten für VMs und Softwarelizenzen wegfallen.

Das folgende Diagramm zeigt den Datenverkehr für Momentaufnahmen in regionsübergreifenden Replikationsszenarien: 

![Diagramm des Datenverkehrs für Momentaufnahmen in regionsübergreifenden Replikationsszenarien](../media/azure-netapp-files/snapshot-traffic-cross-region-replication.png)

## <a name="ways-to-restore-data-from-snapshots"></a>Verfahren zum Wiederherstellen von Daten aus Momentaufnahmen  

Die Azure NetApp Files-Momentaufnahmetechnologie steigert die Häufigkeit und Zuverlässigkeit von Sicherungen erheblich. Gleichzeitig verursacht sie nur einen minimalen Leistungsmehraufwand und kann sicher auf einem aktiven Volume erstellt werden. Azure NetApp Files-Momentaufnahmen ermöglichen nahezu sofortige, sichere und optional vom Benutzer verwaltete Wiederherstellungen. In diesem Abschnitt werden verschiedene Methoden beschrieben, mit denen Daten aus Azure NetApp Files-Momentaufnahmen abgerufen oder wiederhergestellt werden können.

### <a name="restoring-files-or-directories-from-snapshots"></a>Wiederherstellen von Dateien oder Verzeichnissen aus Momentaufnahmen 

Wenn die [Sichtbarkeit des Momentaufnahmepfads](azure-netapp-files-manage-snapshots.md#edit-the-hide-snapshot-path-option) nicht auf `hidden` festgelegt ist, können Benutzer direkt auf Momentaufnahmen zugreifen, um Daten nach einem versehentlichen Löschen, Beschädigen oder Ändern wiederherzustellen. Die Sicherheit von Dateien und Verzeichnissen wird in der Momentaufnahme beibehalten. Außerdem sind Momentaufnahmen immer schreibgeschützt. Daher ist die Wiederherstellung sicher und einfach. 

Das folgende Diagramm zeigt den Zugriff auf Dateien oder Verzeichnisse in einer Momentaufnahme: 

![Diagramm des Zugriffs auf Dateien oder Verzeichnisse in einer Momentaufnahme](../media/azure-netapp-files/snapshot-file-directory-access.png)

Im Diagramm verwendet „Snapshot 1“ nur die Deltablöcke zwischen dem aktiven Volume und dem Zeitpunkt der Erstellung der Momentaufnahme. Wenn Sie jedoch auf die Volumemomentaufnahme über ihren Pfad zugreifen, werden die Daten so dargestellt, *als ob* es sich um die vollständige Volumekapazität zum Zeitpunkt der Erstellung der Momentaufnahme handelt. Durch den Zugriff auf die Momentaufnahmeordner können Benutzer Daten selbst wiederherstellen, indem sie Dateien und Verzeichnisse aus der gewünschten Momentaufnahme kopieren.

Ähnlich kann bei einer regionsübergreifenden Replikation zur Datenwiederherstellung in der Notfallwiederherstellungsregion schreibgeschützt auf Momentaufnahmen auf Zielvolumes zugegriffen werden.  

Das folgende Diagramm zeigt den Zugriff auf Momentaufnahmen in Szenarien mit regionsübergreifender Replikation: 

![Diagramm des Zugriffs auf Momentaufnahmen in Szenarien mit regionsübergreifender Replikation](../media/azure-netapp-files/snapshot-access-cross-region-replication.png)

Informationen zum Wiederherstellen einzelner Dateien oder Verzeichnisse aus Momentaufnahmen finden Sie unter [Wiederherstellen einer Datei aus einer Momentaufnahme mithilfe eines Clients](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client).

### <a name="restoring-cloning-a-snapshot-to-a-new-volume"></a>Wiederherstellen (Klonen) einer Momentaufnahme auf einem neuen Volume

Sie können Azure NetApp Files-Momentaufnahmen auf einem separaten, unabhängigen Volume wiederherstellen. Dieser Vorgang erfolgt unabhängig von der Volumegröße und der verbrauchten Kapazität nahezu unmittelbar. Auf das neu erstellte Volume kann fast sofort zugegriffen werden, während das eigentlichen Volume und die Datenblöcke aus der Momentaufnahme kopiert werden. Abhängig von der Volumegröße und der Kapazität kann dieser Vorgang eine beträchtliche Zeit in Anspruch nehmen, während der das übergeordnete Volume und die Momentaufnahme nicht gelöscht werden können. Auf das Volume kann jedoch bereits nach der anfänglichen Erstellung zugegriffen werden, während der Kopiervorgang im Hintergrund ausgeführt wird. Diese Funktion ermöglicht das schnelle Erstellen von Volumes für die Datenwiederherstellung sowie das Klonen von Volumes für Tests und die Entwicklung. Aufgrund der Art des Datenkopiervorgangs verdoppelt sich der Verbrauch des Speicherkapazitätspools nach Abschluss der Wiederherstellung, und das neue Volume hat die gesamte aktive Kapazität der ursprünglichen Momentaufnahme. Nachdem dieser Vorgang abgeschlossen wurde, ist das Volume unabhängig und wird vom ursprünglichen Volume getrennt. Die Quellvolumes und Momentaufnahmen können unabhängig vom neuen Volume verwaltet oder entfernt werden.

Das folgende Diagramm zeigt ein neues Volume, das durch Wiederherstellen einer Momentaufnahme (Klonen) erstellt wurde:   

![Diagramm eines neuen, durch Wiederherstellen einer Momentaufnahme erstellten Volumes](../media/azure-netapp-files/snapshot-restore-clone-new-volume.png)

Die gleichen Vorgänge können auch mit replizierten Momentaufnahmen für ein Notfallwiederherstellungs-Volume ausgeführt werden. Jede Momentaufnahme kann auf einem neuen Volume wiederhergestellt werden, selbst wenn die regionsübergreifende Replikation aktiv ist oder fortgesetzt wird. Diese Funktion ermöglicht eine unterbrechungsfreie Erstellung von Test- und Entwicklungsumgebungen in einer Notfallwiederherstellungsregion. So können die Daten genutzt werden, während die replizierten Volumes andernfalls nur für die Notfallwiederherstellung verwendet werden. Dieser Anwendungsfall ermöglicht die Isolation von Tests und der Entwicklung von der Produktion und beseitigt damit potenzielle Auswirkungen auf Produktionsumgebungen.  

Das folgende Diagramm zeigt die Volumewiederherstellung (Klonen) mithilfe der Volumemomentaufnahme am Notfallwiederherstellungsziel, während eine regionsübergreifende Replikation stattfindet:  

![Diagramm der Volumewiederherstellung mithilfe der Volumemomentaufnahme am Notfallwiederherstellungsziel](../media/azure-netapp-files/snapshot-restore-clone-target-volume.png)

Weitere Informationen zu Wiederherstellungsvorgängen für Volumes finden Sie unter [Wiederherstellen einer Momentaufnahme auf einem neuen Volume](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume).

### <a name="restoring-reverting-a-snapshot-in-place"></a>Direktes Wiederherstellen (Zurücksetzen) einer Momentaufnahme

Da ein neues Volume Speicherkapazität beansprucht, ist das Erstellen eines neuen Volumes aus einer Momentaufnahme in einigen Fällen möglicherweise nicht erforderlich oder nicht angemessen. Zur schnellen Wiederherstellung nach Datenbeschädigungen (z. B. bei Datenbankbeschädigungen oder Ransomwareangriffen) ist es möglicherweise besser, eine Momentaufnahme innerhalb des Volumes selbst wiederherzustellen. Dieser Vorgang kann mithilfe der Wiederherstellungsfunktion von Azure NetApp Files durchgeführt werden. Mit dieser Funktion können Sie schnell ein Volume wieder in den Zustand zurückversetzen, in dem es sich befand, als eine bestimmte Momentaufnahme erstellt wurde. In den meisten Fällen erfolgt das Wiederherstellen eines Volumes wesentlich schneller als das Wiederherstellen einzelner Dateien aus einer Momentaufnahme in das aktive Dateisystem. Dies gilt insbesondere bei großen Volumes mit vielen TiB an Daten. 

Das Wiederherstellen einer Volumemomentaufnahme erfolgt nahezu unmittelbar und dauert selbst bei den größten Volumes nur wenige Sekunden. Die Metadaten des aktiven Volumes (*I-Knoten-Table*) werden durch die Metadaten der Momentaufnahme zum Zeitpunkt der Erstellung der Momentaufnahme ersetzt. Dadurch wird das Volume auf diesen Zeitpunkt zurückgesetzt. Für diese Wiederherstellung müssen keine Datenblöcke kopiert werden. Daher ist dieses Verfahren hinsichtlich des Speicherplatzes wesentlich effizienter als die Wiederherstellung einer Momentaufnahme auf einem neuen Volume. 

Das folgende Diagramm zeigt ein Volume, das auf eine ältere Momentaufnahme wiederhergestellt wird:  

![Diagramm der Wiederherstellung eines Volumes auf eine ältere Momentaufnahme](../media/azure-netapp-files/snapshot-volume-revert.png)

> [!IMPORTANT]
> Aktive Dateisystemdaten und Momentaufnahmen, die nach Erstellung der ausgewählten Momentaufnahme geschrieben bzw. erstellt wurden, gehen verloren. Der Vorgang zum Wiederherstellen der Momentaufnahme ersetzt alle Daten im Zielvolume durch die Daten in der ausgewählten Momentaufnahme. Bei der Auswahl der Momentaufnahme sollten Sie auf die Inhalte und das Erstellungsdatum achten. Sie können den Vorgang zum Wiederherstellen der Momentaufnahme nicht zurücksetzen.  

Weitere Informationen zur Verwendung dieses Features finden Sie unter [Wiederherstellen eines Volumes mit „Momentaufnahme wiederherstellen“](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert).

## <a name="how-snapshots-are-deleted"></a>Löschen von Momentaufnahmen 

Momentaufnahmen belegen Speicherkapazität. Daher werden sie in der Regel nicht unbegrenzt aufbewahrt. Für den Schutz von Daten, die Aufbewahrung und die Wiederherstellbarkeit wird in der Regel eine bestimmte Anzahl von Momentaufnahmen (zu unterschiedlichen Zeitpunkten) für eine bestimmte Dauer online gehalten. Diese Dauer richtet sich nach den Anforderungen an RPO, RTO und Beibehaltung in der Vereinbarung zum Servicelevel (SLA). Ältere Momentaufnahmen müssen jedoch häufig nicht im Speicherdienst aufbewahrt werden und können gelöscht werden, um Speicherplatz freizugeben. Alle Momentaufnahmen können jederzeit von einem Administrator gelöscht werden (nicht notwendigerweise in der Reihenfolge ihrer Erstellung). 

> [!IMPORTANT]
> Das Löschen von Momentaufnahmen kann nicht rückgängig gemacht werden. Sie sollten für den Schutz von Daten und die Aufbewahrung Offlinekopien des Volumes speichern. 

Beim Löschen einer Momentaufnahme werden alle Zeiger aus dieser Momentaufnahme auf vorhandene Datenblöcke entfernt. Wenn auf einen Datenblock keine Zeiger mehr verweisen (im aktiven Volume oder in anderen Momentaufnahmen im Volume), wird der Datenblock an den freien Speicherplatz auf dem Volume übertragen, der dann wieder verwendet werden kann. Daher wird durch das Entfernen von Momentaufnahmen in der Regel mehr Kapazität in einem Volume freigegeben als durch das Löschen von Daten aus dem aktiven Volume, da Datenblöcke häufig in zuvor erstellten Momentaufnahmen erfasst werden. 

Das folgende Diagramm zeigt die Auswirkung auf den Speicherverbrauch durch das Löschen von „Snapshot 3“ auf einem Volume:  

![Diagramm der Auswirkungen des Löschens einer Momentaufnahme auf den Speicherverbrauch](../media/azure-netapp-files/snapshot-delete-storage-consumption.png)

Sie sollten den [Verbrauch durch Volumes und Momentaufnahmen überwachen](azure-netapp-files-metrics.md#volumes) und analysieren, um besser zu verstehen, wie Anwendung, aktives Volume und Momentaufnahmeverbrauch zusammenhängen. 

Weitere Informationen zum Verwalten der Löschung von Momentaufnahmen finden Sie unter [Löschen von Momentaufnahmen](azure-netapp-files-manage-snapshots.md#delete-snapshots). Weitere Informationen zum Automatisieren dieses Vorgangs finden Sie unter [Verwalten von Momentaufnahmerichtlinien](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von Momentaufnahmen mithilfe von Azure NetApp Files](azure-netapp-files-manage-snapshots.md)
* [Überwachen von Volume- und Momentaufnahmemetriken](azure-netapp-files-metrics.md#volumes)
* [Problembehandlung für Momentaufnahmenrichtlinien](troubleshoot-snapshot-policies.md)
* [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Video „Azure NetApp Files Snapshots 101“](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Azure NetApp Files-Momentaufnahmen: Übersicht](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)



