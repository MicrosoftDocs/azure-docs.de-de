---
title: Includedatei
description: Includedatei
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 8eff9da82fdfa5749fd1c2bc04652d5c8ce8dfd2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518059"
---
![Screenshot zur f i o-Ausgabe zeigt „r=22,8k“ hervorgehoben an.](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

Standard_D8s_v3 kann insgesamt 28.600 IOPS erreichen. Mithilfe der Metriken wollen wir untersuchen, was vor sich geht und unseren Speicher-E/A-Engpass identifizieren. Wählen Sie im linken Bereich **Metriken** aus:

![Screenshot mit hervorgehobener Option „Metriken“ im linken Bereich.](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

Sehen wir uns zunächst die Metrik **VM Cached IOPS Consumed Percentage** (Verbrauchte von der VM zwischengespeicherte IOPS in Prozent) an:

![Der Screenshot zeigt „VM Cached IOPS Consumed Percentage“ (Verbrauchte von der VM zwischengespeicherte IOPS in Prozent) an.](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

Diese Metrik teilt uns mit, dass 61 % der 16.000 IOPS, die in der VM für zwischengespeicherte IOPS zugeteilt sind, verwendet werden. Dieser Prozentsatz bedeutet, dass der Engpass bei der Speicher-E/A nicht bei den zwischengespeicherten Datenträgern liegt, da er die 100 % nicht erreicht. Sehen wir uns nun die Metrik **VM Uncached IOPS Consumed Percentage** (Verbrauchte von der VM nicht zwischengespeicherte IOPS in Prozent) an:

![Der Screenshot zeigt „VM Uncached IOPS Consumed Percentage“ (Verbrauchte von der VM nicht zwischengespeicherte IOPS in Prozent) an.](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

Diese Metrik liegt bei 100 %. Sie gibt an, dass alle 12.800 IOPS, die der VM für nicht zwischengespeicherte IOPS zugeteilt sind, verwendet werden. Eine Möglichkeit, dieses Problem zu beheben, besteht darin, unsere VM so zu vergrößern, dass sie die zusätzliche E/A verarbeiten kann. Aber bevor wir das erledigen, sollten wir uns den angefügten Datenträger ansehen, um herauszufinden, wie viele IOPS sie erfahren. Prüfen Sie den Betriebssystemdatenträger, indem Sie die Metrik **Beanspruchte Betriebssystem-Datenträger-IOPS in Prozent** betrachten:

![Der Screenshot zeigt „Beanspruchte Betriebssystem-Datenträger-IOPS in Prozent“ an.](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

Diese Metrik gibt an, dass annähernd 90 % von den für diesen P30-Betriebssystem-Datenträger bereitgestellten 5.000 IOPS verwendet werden. Dieser Prozentsatz bedeutet, dass kein Engpass auf dem Betriebssystemdatenträger vorliegt. Prüfen wir nun die an die VM angefügten Datenträger in Form der Metrik **Beanspruchte Datenträger-IOPS in Prozent** :

![Der Screenshot zeigt „Beanspruchte Datenträger-IOPS in Prozent“ an.](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

Diese Metrik teilt uns mit, dass der durchschnittlich verbrauchte Prozentsatz an IOPS über alle Datenträger bei ungefähr 42 % liegt. Dieser Prozentsatz wird auf der Grundlage der von den Datenträgern verwendeten IOPS berechnet, die nicht aus dem Hostcache bedient werden. Sehen wir uns diese Metrik genauer an, indem wir *Aufteilung* auf diese Metriken anwenden und nach dem LUN-Wert aufteilen:

![Der Screenshot zeigt „Beanspruchte Datenträger-IOPS in Prozent“ mit Aufteilung an.](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

Diese Metrik informiert uns, dass die an LUN 3 und 2 angefügten Datenträger ungefähr 85 % ihrer bereitgestellten IOPS verbrauchen. Dieses Diagramm zeigt, wie die E/A aus dem Blickwinkel der VM- und Datenträgerarchitektur aussieht:

![Diagramm für das Beispiel „Speicher-E/A-Metriken“.](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
