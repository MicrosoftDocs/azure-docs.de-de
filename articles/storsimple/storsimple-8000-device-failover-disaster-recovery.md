---
title: Ausführen eines Failovers und einer Notfallwiederherstellung für Geräte der StorSimple 8000-Serie
description: Erfahren Sie, wie Sie ein Failover des StorSimple-Geräts auf sich selbst, auf ein anderes physisches Gerät oder auf ein Cloudgerät durchführen.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: dffa059b18e159d04b5e3bb8555dabf801ede692
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017116"
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>Ausführen eines Failovers und einer Notfallwiederherstellung für Geräte der StorSimple 8000-Serie

## <a name="overview"></a>Übersicht

In diesem Artikel werden die Funktionen für das Gerätefailover der StorSimple 8000-Serie und die Wiederherstellung von StorSimple-Geräten nach einem Notfall behandelt. StorSimple verwendet das Gerätefailover, um die Daten auf einem Quellgerät im Rechenzentrum auf ein anderes Zielgerät zu migrieren. Die Anleitung in diesem Artikel gilt für physische Geräte der StorSimple 8000-Serie und Cloudgeräte, auf denen Softwareversionen mit Update 3 und höher ausgeführt werden.

StorSimple verwendet das Blatt **Geräte**, um die Funktion für das Gerätefailover bei einem Notfall zu starten. Auf diesem Blatt sind alle StorSimple-Geräte aufgeführt, die mit Ihrem StorSimple-Geräte-Manager-Dienst verbunden sind.

![Blatt „Geräte“](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>Notfallwiederherstellung und Gerätefailover

Bei einer Notfallwiederherstellung funktioniert das primäre Gerät nicht mehr. StorSimple verwendet das primäre Gerät als _Quelle_ und verschiebt die zugehörigen Clouddaten auf ein anderes _Zielgerät_. Dieser Vorgang wird als *Failover* bezeichnet. Die folgende Abbildung veranschaulicht den Failovervorgang.

![Was geschieht beim Failover eines Geräts?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

Das Zielgerät für ein Failover kann ein physisches Gerät oder sogar ein Cloudgerät sein. Das Zielgerät kann sich am gleichen oder einem anderen geografischen Ort als das Quellgerät befinden.

Während des Failovers können Sie Volumecontainer für die Migration auswählen. StorSimple ändert dann den Besitzer dieser Volumecontainer vom Quellgerät in das Zielgerät. Sobald der Besitzer der Volumecontainer geändert wurde, werden diese von StorSimple vom Quellgerät gelöscht. Nach dem Löschvorgang kann für das Zielgerät ein Failback erfolgen. Beim _Failback_ wird der Besitz wieder an das ursprüngliche Quellgerät übertragen.

### <a name="cloud-snapshot-used-during-device-failover"></a>Während des Gerätefailovers verwendete Cloudmomentaufnahme

Nach einer Notfallwiederherstellung werden die Daten mithilfe der letzten Cloudsicherung auf dem Zielgerät wiederhergestellt. Weitere Informationen zu Cloudmomentaufnahmen und zum Erstellen einer manuellen Sicherung finden Sie unter [Verwalten eines StorSimple-Geräts mit dem StorSimple-Geräte-Manager-Dienst](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup).

Auf einem Gerät der StorSimple 8000-Serie sind mit Sicherungen Sicherungsrichtlinien verknüpft. Wenn mehrere Sicherungsrichtlinien für das gleiche Volume vorhanden sind, wählt StorSimple die Sicherungsrichtlinie mit der größten Anzahl von Volumes. StorSimple verwendet dann die gemäß der ausgewählten Sicherungsrichtlinie letzte Sicherung zum Wiederherstellen der Daten auf dem Zielgerät.

Angenommen, es gibt zwei Sicherungsrichtlinien, *pefaultPol* und *customPol*:

* *defaultPol*: Ein Volume (*vol1*) wird täglich ab 22:30 Uhr ausgeführt.
* *customPol*: Vier Volumes (*vol1*, *vol2*, *vol3* und *vol4*) werden täglich ab 22:00 Uhr ausgeführt.

In diesem Fall wird *customPol* von StorSimple verwendet, da die Richtlinie mehr Volumes umfasst und Absturzkonsistenz Priorität hat. Die Daten werden auf der Grundlage der neuesten Sicherung für diese Richtlinie wiederhergestellt. Weitere Informationen zum Erstellen und Verwalten von Sicherungsrichtlinien finden Sie unter [Verwalten von Sicherungsrichtlinien mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-8000-manage-backup-policies-u2.md).

## <a name="common-considerations-for-device-failover"></a>Allgemeine Überlegungen zum Gerätefailover

Lesen Sie die folgenden Informationen, bevor Sie ein Failover für ein Gerät ausführen:

* Bevor das Failover eines Geräts gestartet wird, müssen alle Volumes innerhalb der Volumecontainer offline sein. Bei einem ungeplanten Failover werden StorSimple-Volumes automatisch offline geschaltet. Doch wenn Sie ein geplantes Failover (zum Testen der Notfallwiederherstellung) ausführen, müssen Sie alle Volumes offline schalten.
* Nur die Volumecontainer, die über eine zugeordnete Cloudmomentaufnahme verfügen, sind für die Notfallwiederherstellung aufgeführt. Es muss mindestens einen Volumecontainer mit einer zugeordneten Cloudmomentaufnahme zum Wiederherstellen von Daten geben.
* Wenn es Cloudmomentaufnahmen gibt, die sich über mehrere Volumecontainer erstrecken, führt StorSimple für diese Volumecontainer ein Failover als Gruppe durch. In seltenen Fall, bei dem es lokale Momentaufnahmen gibt, die sich über mehrere Volumecontainer erstrecken, was für zugeordnete Cloudmomentaufnahmen nicht zutrifft, führt StorSimple für die lokalen Momentaufnahmen ein Failover durch, doch die lokalen Daten gehen nach der Notfallwiederherstellung verloren.
* Die verfügbaren Zielgeräte für die Notfallwiederherstellung sind Geräte, die über ausreichenden Speicherplatz zur Aufnahme der ausgewählten Volumecontainer verfügen. Geräte, die nicht über ausreichend Speicherplatz verfügen, sind nicht als Zielgeräte aufgeführt.
* Nach einer Notfallwiederherstellung kann für (eine bestimmte Dauer) die Datenzugriffsleistung erheblich beeinträchtigt sein, da das Gerät auf die Daten in der Cloud zugreifen und sie lokal speichern muss.

#### <a name="device-failover-across-software-versions"></a>Gerätefailover in allen Softwareversionen

Der StorSimple-Geräte-Manager-Dienst verfügt in einer Bereitstellung möglicherweise über mehrere physische und Cloudgeräte, auf denen unterschiedliche Softwareversionen ausgeführt werden.

Bestimmen Sie anhand der folgenden Tabelle, ob ein Failover oder Failback zu einem anderen Gerät mit anderer Softwareversion möglich ist und wie sich die Volumetypen während der Notfallwiederherstellung verhalten.

#### <a name="failover-and-failback-across-software-versions"></a>Failover und Failback in allen Softwareversionen

| Failover/Failback von | Physisches Gerät | Cloudgerät |
| --- | --- | --- |
| Update 3 zu Update 4 |Für mehrstufige Volumes erfolgt das Failover entsprechend den Stufen. <br></br>Für lokale Volumes erfolgt das Failover gemäß der lokalen Konfiguration. <br></br> Wenn Sie nach einem Failover auf einem Gerät mit Update 4 eine Momentaufnahme erstellen, wird die [auf einer Heatmap basierende Nachverfolgung](storsimple-update4-release-notes.md#whats-new-in-update-4) aktiviert. |Für lokale Volumes erfolgt das Failover gemäß den Stufen. |
| Update 4 zu Update 3 |Für mehrstufige Volumes erfolgt das Failover entsprechend den Stufen. <br></br>Für lokale Volumes erfolgt das Failover gemäß der lokalen Konfiguration. <br></br> Für eine Wiederherstellung verwendete Sicherungen behalten die Metadaten von Heatmaps bei. <br></br>Die auf einer Heatmap basierende Nachverfolgung ist in Update 3 nach einem Failback nicht verfügbar. |Für lokale Volumes erfolgt das Failover gemäß den Stufen. |


## <a name="device-failover-scenarios"></a>Szenario für das Gerätefailover

Bei einem Notfall können Sie als Failoverziel für Ihr StorSimple-Gerät Folgendes auswählen:

* [Ein physisches Gerät](storsimple-8000-device-failover-physical-device.md)
* [Das Gerät selbst](storsimple-8000-device-failover-same-device.md)
* [Ein Cloudgerät](storsimple-8000-device-failover-cloud-appliance.md)

Die vorherigen Artikel bieten detaillierte Schritte für jeden der oben genannten Failoverfälle.


## <a name="failback"></a>Failback

Für Update 3 oder höher unterstützt StorSimple auch das Failback. Failback ist die Umkehrung des Failovers. Das Ziel wird zur Quelle, und das ursprüngliche Quellgerät während des Failovers wird zum Zielgerät. 

Während des Failbacks synchronisiert StorSimple die Daten erneut zurück an den primären Standort, hält E/A-Vorgänge und die Anwendungsaktivität an und leitet die Umstellung zurück auf den ursprünglichen Speicherort ein.

Nach Abschluss des Failovers führt StorSimple die folgenden Aktionen aus:

* StorSimple bereinigt die Volumecontainer, für die ein Failover durchgeführt wurde, vom Quellgerät.
* Auf dem Quellgerät wird von StorSimple ein Hintergrundauftrag pro Volumecontainer (mit Failover) eingeleitet. Wenn Sie versuchen, ein Failback auszuführen, während der Auftrag ausgeführt wird, erhalten Sie eine entsprechende Benachrichtigung. Warten Sie, bis der Auftrag abgeschlossen ist, bevor Sie das Failback starten.
* Wie lange das Löschen der Volumecontainer dauert, richtet sich nach verschiedenen Faktoren, wie z.B. der Datenmenge, dem Alter der Daten, der Anzahl von Sicherungen und der für den Vorgang verfügbaren Netzwerkbandbreite.

Wenn Sie Testfailover oder Testfailbacks planen, wird empfohlen, Volumecontainer mit weniger Daten (GBs) zu testen. Zumeist können Sie das Failback 24 Stunden nach Abschluss des Failovers starten.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

Q. **Was geschieht, wenn die Notfallwiederherstellung misslingt oder nur teilweise erfolgreich war?**

A. Wenn die Notfallwiederherstellung misslingt, sollten Sie sie wiederholen. Der zweite Failoverauftrag für ein Gerät erkennt den Status des ersten Auftrags und fährt an der entsprechenden Stelle fort.

Q. **Kann ich ein Gerät löschen, während das Gerätefailover erfolgt?**

A. Ein Gerät kann nicht gelöscht werden, während eine Notfallwiederherstellung ausgeführt wird. Sie können Ihr Gerät erst nach erfolgter Notfallwiederherstellung löschen. Sie können den Status des Failoverauftrags eines Geräts auf dem Blatt **Aufträge** überwachen.

Q. **Wann wird die Garbage Collection auf dem Quellgerät gestartet, damit die lokalen Daten auf dem Quellgerät gelöscht werden?**

A. Die automatische Speicherbereinigung wird auf dem Quellgerät erst aktiviert, nachdem das Gerät vollständig bereinigt wurde. Die Bereinigung enthält bereinigte Objekte, für die ein Failover vom Quellgerät erfolgt ist, z.B. Volumes, Sicherungsobjekte (keine Daten), Volumecontainer und Richtlinien.

Q. **Was geschieht, wenn der den Volumecontainern zugeordnete Löschauftrag auf dem Quellgerät misslingt?**

A.  Wenn der Löschauftrag misslingt, können Sie die Volumecontainer manuell löschen. Wählen Sie auf dem Blatt **Geräte** Ihr Quellgerät aus, und klicken Sie auf **Volumecontainer**. Wählen Sie die Volumecontainer aus, für die Sie ein Failover ausgeführt haben, und klicken Sie unten auf dem Blatt auf **Löschen**. Sobald Sie alle Volumecontainer mit erfolgtem Failover vom Quellgerät gelöscht haben, können Sie das Failback starten. Weitere Informationen finden Sie unter [Löschen eines Volumecontainers](storsimple-8000-manage-volume-containers.md#delete-a-volume-container).

## <a name="business-continuity-disaster-recovery-bcdr"></a>Business Continuity Disaster Recovery (BCDR)

Ein Business Continuity Disaster Recovery (BCDR)-Szenario liegt vor, wenn das gesamte Azure-Rechenzentrum nicht mehr funktioniert. Dieses Szenario kann sich auf den StorSimple-Geräte-Manager-Dienst und die zugehörigen StorSimple-Geräte auswirken.

Wenn ein StorSimple-Gerät unmittelbar vor einem Notfall registriert wurden, muss dieses Gerät möglicherweise auf die Werkseinstellungen zurückgesetzt werden. Nach dem Notfall wird das StorSimple-Gerät im Azure-Portal als offline angezeigt. Dieses Gerät muss aus dem Portal gelöscht werden. Setzen Sie das Gerät auf die Werkseinstellungen zurück, und registrieren Sie es erneut beim Dienst.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie bereit sind, ein Gerätefailover auszuführen, wählen Sie eines der folgenden Szenarien, um ausführliche Anweisungen zu erhalten:

- [Failover auf ein anderes physisches Gerät](storsimple-8000-device-failover-physical-device.md)
- [Failover auf dasselbe Gerät](storsimple-8000-device-failover-same-device.md)
- [Failover auf StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md)

Wenn für Ihr Gerät ein Failover erfolgt ist, wählen Sie eine der folgenden Optionen:

* [Deaktivieren und Löschen eines StorSimple-Geräts](storsimple-8000-deactivate-and-delete-device.md)
* [Verwalten Ihres StorSimple-Geräts mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-8000-manager-service-administration.md)

