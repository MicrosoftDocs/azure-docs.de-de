---
title: Was ist der StorSimple Snapshot Manager? | Microsoft-Dokumentation
description: Beschreibt den StorSimple Snapshot Manager, seine Architektur und seine Features.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6094c31e-e2d9-4592-8a15-76bdcf60a754
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2214f085c30419cefb3f6f84139d5592873173f7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017983"
---
# <a name="an-introduction-to-storsimple-snapshot-manager"></a>Einführung in StorSimple Snapshot Manager

## <a name="overview"></a>Übersicht
StorSimple Snapshot Manager ist ein Microsoft Management Console (MMC)-Snap-In, das den Datenschutz und die Sicherungsverwaltung in einer Microsoft Azure StorSimple-Umgebung vereinfacht. Mit dem StorSimple Snapshot Manager können Sie Microsoft Azure StorSimple-Daten im Rechenzentrum und in der Cloud als eine einzelne integrierte Speicherlösung verwalten und somit die Sicherungsprozesse vereinfachen und die Kosten senken.

Diese Übersicht bietet eine Einführung in StorSimple Snapshot Manager, eine Beschreibung der Features und eine Erläuterung seiner Rolle in Microsoft Azure StorSimple. 

Eine Übersicht über das gesamte Microsoft Azure StorSimple-System, einschließlich StorSimple-Gerät, StorSimple Manager-Dienst, StorSimple Snapshot Manager und StorSimple-Adapter für SharePoint, finden Sie unter [StorSimple 8000-Serie: eine Hybridcloud-Speicherlösung](storsimple-overview.md). 

> [!NOTE]
> * StorSimple Snapshot Manager eignet sich nicht zur Verwaltung von Microsoft Azure StorSimple Virtual Arrays (auch bekannt als lokale virtuelle StorSimple-Geräte).
> * Wenn Sie StorSimple Update 2 auf Ihrem StorSimple-Gerät installieren möchten, laden Sie unbedingt die aktuelle Version von StorSimple Snapshot Manager herunter, und installieren Sie sie **vor der Installation von StorSimple Update 2**. Die neueste Version von StorSimple Snapshot Manager ist abwärtskompatibel und kann mit allen veröffentlichten Versionen von Microsoft Azure StorSimple verwendet werden. Wenn Sie die vorherige Version von StorSimple Snapshot Manager verwenden, müssen Sie sie aktualisieren (die vorherige Version muss vor der Installation der neuen Version nicht deinstalliert werden).
> 
> 

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>Zweck und Architektur des StorSimple Snapshot Managers
Der StorSimple Snapshot Manager stellt eine zentrale Verwaltungskonsole bereit, die Sie zum Erstellen konsistenter zeitpunktbezogener Sicherungskopien von lokalen und Clouddaten verwenden können. Sie können die Konsole z. B. für Folgendes verwenden:

* Konfigurieren, Sichern und Löschen von Volumes
* Konfigurieren von Volumegruppen, um sicherzustellen, dass gesicherte Daten anwendungskonsistent sind
* Verwalten von Sicherungsrichtlinien, damit die Daten nach einem vordefinierten Zeitplan gesichert werden.
* Erstellen lokaler und cloudbasierter Momentaufnahmen, die in der Cloud gespeichert und für die Notfallwiederherstellung verwendet werden können.

StorSimple Snapshot Manager ruft die Liste der Anwendungen ab, die für den VSS-Anbieter auf dem Host registriert sind. Damit anwendungskonsistente Sicherungen erstellt werden, überprüft der Manager anschließend die von einer Anwendung verwendeten Volumes und schlägt zu konfigurierende Volumegruppen vor. Der StorSimple Snapshot Manager verwendet diese Volumegruppen zum Erstellen von anwendungskonsistenten Sicherungskopien. (Anwendungskonsistenz ist gegeben, wenn alle zugehörigen Dateien und Datenbanken synchron sind und den tatsächlichen Zustand der Anwendung zu einem bestimmten Zeitpunkt darstellen.) 

StorSimple Snapshot Manager-Sicherungen sind inkrementelle Momentaufnahmen, die nur die Änderungen seit der letzten Sicherung erfassen. Deshalb verbrauchen die Sicherungen weniger Speicherplatz und können schnell erstellt und wiederhergestellt werden. Der StorSimple Snapshot Manager verwendet den Windows-Volumeschattenkopie-Dienst (Volume Shadow Copy Service, VSS), um sicherzustellen, dass in den Momentaufnahmen anwendungskonsistente Daten erfasst werden. (Weitere Informationen finden Sie im Abschnitt "Integration in den Windows-Volumeschattenkopie-Dienst".) Sie können mit dem StorSimple Snapshot Manager Sicherungszeitpläne erstellen oder sofortige Sicherungen anlegen. Wenn Sie anschließend Daten aus einer Sicherung wiederherstellen müssen, ermöglicht der StorSimple Snapshot Manager die Auswahl aus in einem Katalog mit lokalen oder Cloudmomentaufnahmen. Azure StorSimple stellt nur die erforderlichen Daten wieder her, wenn sie benötigt werden – dies verhindert Verzögerungen bei der Datenverfügbarkeit.)

![StorSimple Snapshot Manager-Architektur](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**StorSimple Snapshot Manager-Architektur** 

## <a name="support-for-multiple-volume-types"></a>Unterstützung für mehrere Volumetypen
Sie können den StorSimple Snapshot Manager zum Konfigurieren und Sichern der folgenden Volumetypen verwenden: 

* **Basisvolumes** : Ein Basisvolume ist eine einzelne Partition auf einem Basisdatenträger. 
* **Einfache Volumes:** Ein einfaches Volume ist ein dynamisches Volume, das den Speicherplatz von einem einzelnen dynamischen Datenträger enthält. Ein einfaches Volume besteht aus einem einzelnen Bereich auf einem Datenträger oder aus mehreren Bereichen auf demselben Datenträger, die miteinander verknüpft sind. (Sie können einfache Volumes nur auf dynamischen Datenträgern erstellen.) Einfache Volumes bieten keine Fehlertoleranz.
* **Dynamische Volumes** : Ein dynamisches Volume ist ein Volume, das auf einem dynamischen Datenträger erstellt wird. Dynamische Datenträger verwenden eine Datenbank zum Nachverfolgen von Informationen zu den Volumes auf den dynamischen Datenträgern in einem Computer. 
* **Dynamische Volumes mit Spiegelung** : Dynamische Volumes mit Spiegelung basieren auf der RAID-1-Architektur. Bei RAID 1 werden identische Daten auf zwei oder mehr Datenträger geschrieben, wodurch ein gespiegelter Satz entsteht. Eine Leseanforderung kann dann von einem beliebigen Datenträger behandelt werden, der die angeforderten Daten enthält.
* **Freigegebene Clustervolumes** : Bei freigegebenen Clustervolumes (CSV) können mehrere Knoten in einem Failovercluster gleichzeitig auf demselben Datenträger lesen oder schreiben. Failover von einem Knoten auf einen anderen Knoten können sehr schnell durchgeführt werden, ohne dass eine Änderung am Besitz für das Laufwerk erforderlich ist oder dass ein Volume bereitgestellt, seine Bereitstellung aufgehoben oder ein Volume entfernt werden muss. 

> [!IMPORTANT]
> Mischen Sie keine freigegebene Clustervolumes mit nicht freigegebenen Clustervolumes in derselben Momentaufnahme. Das Vermischen von freigegebenen Clustervolumes mit nicht freigegebenen Clustervolumes in einer Momentaufnahme wird nicht unterstützt. 
> 
> 

Sie können mit dem StorSimple Snapshot Manager komplette Volumegruppen oder einzelne Volumes klonen und einzelne Dateien wiederherstellen.

* [Volumes und Volumegruppen](#volumes-and-volume-groups) 
* [Sicherungstypen und -richtlinien](#backup-types-and-backup-policies) 

Weitere Informationen über Features des StorSimple Snapshot Managers und deren Verwendung finden Sie unter [Benutzeroberfläche des StorSimple Snapshot Managers](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Volumes und Volumegruppen
Mit dem StorSimple Snapshot Manager können Sie Volumes erstellen und dann in Volumegruppen konfigurieren. 

Der StorSimple Snapshot Manager verwendet Volumegruppen zum Erstellen von anwendungskonsistenten Sicherungskopien. Anwendungskonsistenz ist gegeben, wenn alle zugehörigen Dateien und Datenbanken synchron sind und den tatsächlichen Zustand einer Anwendung zu einem bestimmten Zeitpunkt darstellen. Volumegruppen (auch als *Konsistenzgruppen* bezeichnet) bilden die Basis von Sicherungs- oder Wiederherstellungsaufträgen.

Volumegruppen sind nicht dasselbe wie Volumecontainer. Ein Volumecontainer enthält ein oder mehrere Volumes, die gemeinsame Einstellungen für das Cloudspeicherkonto und andere Attribute wie die Verschlüsselung und die Bandbreitenauslastung haben. Ein Volumecontainer kann bis zu 256 nach Bedarf bereitgestellte StorSimple-Volumes enthalten. Weitere Informationen zu Volumecontainern finden Sie unter [Verwalten von Volumecontainern](./storsimple-8000-manage-volume-containers.md). Volumegruppen sind Sammlungen von Volumes, die Sie für Sicherungsvorgänge konfigurieren. Wenn Sie zwei Volumes, die zu verschiedenen Volumecontainern gehören, in einer einzelnen Volumegruppe platzieren und dann eine Sicherungsrichtlinie für diese Volumegruppe erstellen, wird jedes Volume mithilfe des zugehörigen Speicherkontos im entsprechenden Volumecontainer gesichert.

> [!NOTE]
> Alle Volumes in einer Volumegruppe müssen von einem Cloud-Service Provider stammen.
> 
> 

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integration in den Windows-Volumeschattenkopie-Dienst
Der StorSimple Snapshot Manager verwendet den Windows-Volumeschattenkopie-Dienst (Volume Shadow Copy Service, VSS), um anwendungskonsistente Daten zu erfassen. VSS gewährleistet die Anwendungskonsistenz über die Kommunikation mit VSS-fähigen Anwendungen zur Koordination der Erstellung inkrementeller Momentaufnahmen. VSS stellt sicher, dass die Anwendungen vorübergehend inaktiv oder untätig sind, wenn die Momentaufnahmen erstellt werden. 

Die StorSimple Snapshot Manager-Implementierung von VSS arbeitet mit SQL Server und generischen NTFS-Volumes. Der Prozess sieht folgendermaßen aus: 

1. Ein Anforderer, i. d. R. eine Datenverwaltungs- und Datenschutzlösung (z. B. StorSimple Snapshot Manager) oder eine Sicherungsanwendung, ruft VSS auf und fordert den Dienst zum Sammeln von Informationen von der Writer-Software in der Zielanwendung auf.
2. VSS ruft eine Beschreibung der Daten von der Writer-Komponente ab. Der Writer gibt die Beschreibung der zu sichernden Daten zurück. 
3. VSS signalisiert dem Writer, die Anwendung auf die Sicherung vorzubereiten. Der Writer bereitet die Daten durch den Abschluss offener Transaktionen, die Aktualisierung der Transaktionsprotokolle usw. für die Sicherung vor und benachrichtigt anschließend VSS.
4. VSS weist den Writer an, die Datenspeicher für die Anwendung vorübergehend zu beenden, und sicherzustellen, dass keine Daten auf das Volume geschrieben werden, während die Schattenkopie erstellt wird. Durch diesen Schritt, der nicht länger als 60 Sekunden dauert, wird die Konsistenz der Daten sichergestellt.
5. VSS weist den Anbieter an, die Schattenkopie zu erstellen. Anbieter (software- oder hardwarebasiert) verwalten die zurzeit ausgeführten Volumes und erstellen von diesen bei Bedarf Schattenkopien. Der Anbieter erstellt die Schattenkopie und benachrichtigt VSS nach Abschluss des Vorgangs.
6. VSS weist den Writer an, die Anwendung zu benachrichtigen, dass die E/A-Vorgänge fortgesetzt werden können, und zu bestätigen, dass die E/A-Vorgänge während der Erstellung der Schattenkopien erfolgreich angehalten wurden. 
7. Wenn der Kopiervorgang erfolgreich war, gibt VSS den Speicherort der Kopie an den Anforderer zurück. 
8. Wenn während der Erstellung der Schattenkopie Daten geschrieben wurden, ist die Sicherung inkonsistent. VSS löscht die Schattenkopie in diesem Fall und benachrichtigt den Anforderer. Der Anforderer kann entweder den Sicherungsprozess automatisch wiederholen oder den Administrator benachrichtigen, den Vorgang zu einem späteren Zeitpunkt zu wiederholen.

Der Vorgang wird in der folgenden Abbildung veranschaulicht.

![VSS-Prozess](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Ablauf des Windows-Volumeschattenkopie-Diensts** 

## <a name="backup-types-and-backup-policies"></a>Sicherungstypen und -richtlinien
Mit StorSimple Snapshot Manager können Sie Daten sichern und lokal und in der Cloud speichern. Sie können StorSimple Snapshot Manager verwenden, um Daten sofort zu sichern, oder Sie können eine Sicherungsrichtlinie verwenden, um einen Zeitplan für das automatische Erstellen von Sicherungen zu erstellen. Mit Sicherungsrichtlinien können außerdem angeben, wie viele Snapshots beibehalten werden. 

### <a name="backup-types"></a>Sicherungstypen
Sie können mit StorSimple Snapshot Manager die folgenden Typen von Sicherungen erstellen:

* **Lokale Momentaufnahmen:** Lokale Momentaufnahmen sind Zeitpunktkopien der Volumedaten, die auf dem StorSimple-Gerät gespeichert sind. Diese Art der Sicherung kann i. d. R. schnell erstellt und wiederhergestellt werden. Sie können eine lokale Momentaufnahme wie eine lokale Sicherungskopie verwenden.
* **Cloudmomentaufnahmen:** Cloudmomentaufnahmen sind Zeitpunktkopien der Volumedaten, die in der Cloud gespeichert werden. Eine Cloudmomentaufnahme entspricht einer Momentaufnahme, die auf einem anderen, externen Speichersystem repliziert wird. Cloudmomentaufnahmen sind besonders nützlich für Notfallwiederherstellungszenarios.

### <a name="on-demand-and-scheduled-backups"></a>Bedarfsgesteuerte und geplante Sicherungen
Sie können mit dem StorSimple Snapshot Manager sofort eine einmalige Sicherung initiieren, oder Sie können eine Sicherungsrichtlinie verwenden, um wiederholte Sicherungsvorgänge zu planen.

Eine Sicherungsrichtlinie ist ein Satz von automatisierten Regeln, mit denen Sie regelmäßige Sicherungen planen können. Eine Sicherungsrichtlinie ermöglicht es, die Häufigkeit und Parameter zum Erstellen von Momentaufnahmen einer bestimmten Volumegruppe festzulegen. Sie können Richtlinien verwenden, um Start-und Ablaufdaten, Uhrzeiten, Häufigkeiten und Aufbewahrungsanforderungen für lokale und Cloudmomentaufnahmen anzugeben. Eine Richtlinie wird sofort nach ihrer Definition angewendet. 

Sie können mit dem StorSimple Snapshot Manager bei Bedarf jederzeit Sicherungsrichtlinien konfigurieren oder neu konfigurieren. 

Sie konfigurieren die folgenden Informationen für jede von Ihnen erstellte Sicherungsrichtlinie:

* **Name** : der eindeutige Name der ausgewählten Sicherungsrichtlinie.
* **Typ** : der Typ der Sicherungsrichtlinie – lokale Momentaufnahme oder Cloudmomentaufnahme.
* **Volumegruppe** : die Volumegruppe, der die ausgewählte Sicherungsrichtlinie zugewiesen wird.
* **Aufbewahrung:** die Anzahl von beizubehaltenden Sicherungskopien. Wenn Sie die Option **Alle** aktivieren, werden alle Sicherungskopien aufbewahrt, bis die maximale Anzahl von Sicherungskopien pro Volume erreicht ist. Zu diesem Zeitpunkt schlägt die Richtlinie fehl und generiert eine Fehlermeldung. Sie können auch eine Anzahl von Sicherungen angeben, die beibehalten werden sollen (zwischen 1 und 64).
* **Datum** : das Datum, an dem die Sicherungsrichtlinie erstellt wurde.

Informationen zum Konfigurieren von Sicherungsrichtlinien finden Sie unter [Erstellen und Verwalten von Sicherungsrichtlinien mit dem StorSimple Snapshot Manager](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Überwachen und Verwalten von Sicherungsaufträgen
Sie können StorSimple Snapshot Manager zum Überwachen und Verwalten bevorstehender, geplanter und abgeschlossener Sicherungsaufträge verwenden. Darüber hinaus stellt StorSimple Snapshot Manager einen Katalog von bis zu 64 abgeschlossenen Sicherungen bereit. Sie können in diesem Katalog nach Volumes oder einzelnen Dateien suchen und diese wiederherstellen. 

Informationen zum Überwachen von Sicherungsaufträgen finden Sie unter [Anzeigen und Verwalten von Sicherungsaufträgen mit dem StorSimple Snapshot Manager](storsimple-snapshot-manager-manage-backup-jobs.md).

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum [Verwenden von StorSimple Snapshot Manager zum Verwalten der StorSimple-Lösung](storsimple-snapshot-manager-admin.md).
* [StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220)herunterladen.