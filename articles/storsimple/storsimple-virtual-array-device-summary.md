---
title: StorSimple Virtual Array – Gerätezusammenfassungsblatt | Microsoft-Dokumentation
description: Beschreibt das Geräteübersichtsblatt für den StorSimple-Geräte-Manager und erläutert, wie Sie die Integrität Ihres StorSimple Virtual Array mithilfe dieses Blatts überwachen können.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: a13c1ea7-6428-4234-84a6-0ebf51670a85
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: manuaery
ms.openlocfilehash: 1ab72788c768568366f2627055015c74028330b5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005876"
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Verwenden des Geräteübersichtsblatts für den StorSimple-Geräte-Manager, der mit dem StorSimple Virtual Array verbunden ist

## <a name="overview"></a>Übersicht

Das Geräteübersichtsblatt für den StorSimple-Geräte-Manager bietet eine zusammenfassende Darstellung eines StorSimple Virtual Array, der bei einem bestimmten StorSimple-Geräte-Manager registriert ist. Dabei werden Geräteprobleme markiert, die die Aufmerksamkeit des Systemadministrators erfordern. Dieses Tutorial bietet eine Einführung zum Geräteübersichtsblatt, erläutert seinen Inhalt und seine Funktion und beschreibt die Aufgaben, die Sie auf diesem Blatt ausführen können.

Auf dem Geräteübersichtsblatt werden die folgenden Informationen angezeigt:

![Gerätedashboard](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Verwaltung

Auf dem Blatt mit der Geräteübersicht sehen Sie die Optionen zum Verwalten Ihres StorSimple-Geräts. Im oberen Bereich und auf der linken Seite des Blatts finden Sie die Verwaltungsbefehle. Verwenden Sie diese Optionen, um Freigaben oder Volumes hinzuzufügen, oder um Ihr virtuelles Array zu aktualisieren bzw. ein Failover des Geräts zu ermöglichen.

Der Bereich „Essentials“ (Zusammenfassung) erfasst einige der wichtigen Eigenschaften wie z.B. Status, Modell, Softwareversion sowie einen Link zur **Web-UI** des Arrays. Wenn Sie sich in einem internen Netzwerk befinden, können Sie die [lokale Web-UI](storsimple-ova-web-ui-admin.md) direkt starten, um Ihren virtuellen Array zu verwalten.

![Gerätezusammenfassung](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>StorSimple-Geräteübersicht

* Die Kachel **Warnungen** bietet eine Momentaufnahme aller aktiven Warnungen für Ihr virtuelles Array, gruppiert nach dem jeweiligen Schweregrad. Klicken Sie auf die Kachel, um das Blatt **Warnungen** zu öffnen, und klicken Sie anschließend auf einzelne Warnungen, um weitere Details zur jeweiligen Warnung anzuzeigen, darunter auch empfohlene Aktionen. Sie können die Warnung auch löschen, falls das Problem behoben wurde.

* Die Kachel **Kapazität** zeigt den primären Speicher, der bereitgestellt wurde und auf dem virtuellen Gerät verbleibt, im Verhältnis zum verfügbaren Gesamtspeicher des Geräts. **Bereitgestellt** bezieht sich auf die Menge an Speicherplatz, die vorbereitet wird und für die Nutzung reserviert ist. **Verbleibend** bezieht sich auf die verbleibenden Kapazität, die über dieses Gerät bereitgestellt werden kann. **Remaining Tiered** (Verbleibend (Mehrstufig)) bezeichnet die verfügbare Kapazität, einschließlich der Cloud, die bereitgestellt werden kann. **Remaining Local** (Verbleibend (lokal)) ist die verbleibende Kapazität auf den Datenträgern, die an das virtuelle Array angefügt sind.

* Im Diagramm **Verwendung** können Sie sehen, wie viel primären Speicher auf Ihrem virtuellen Array und wie viel Cloud-Speicher in den letzten 7 Tagen (standardmäßiger Zeitraum) verbraucht wurde. Verwenden Sie die Option **Bearbeiten** in der oberen rechten Ecke des Diagramms, um eine andere Zeitskala auszuwählen.

* Die Kachel **Freigaben** oder **Volumes** bietet einen nach Status gruppierten Überblick über die Anzahl der Freigaben oder Volumes auf Ihrem Gerät. Klicken Sie auf die Kachel, um das Listenblatt **Freigaben** oder **Volumes** zu öffnen, und klicken Sie anschließend auf eine einzelne Freigabe oder ein einzelnes Volume, um seine Eigenschaften anzuzeigen oder zu ändern. Weitere Informationen finden Sie unter [Use the StorSimple Device Manager service to manage shares on the StorSimple Virtual Array (Verwenden des StorSimple-Geräte-Manager-Diensts zum Verwalten von Freigaben auf dem StorSimple Virtual Array)](storsimple-virtual-array-manage-shares.md) oder [Use StorSimple Device Manager service to manage volumes on the StorSimple Virtual Array (Verwenden des StorSimple-Geräte-Manager-Diensts zum Verwalten von Volumes auf dem StorSimple Virtual Array)](storsimple-virtual-array-manage-volumes.md).

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel werden folgende Themen erläutert:
- [Verwalten von Freigaben auf StorSimple Virtual Array (in englischer Sprache)](storsimple-virtual-array-manage-shares.md)
    
- [Verwalten von Volumes auf StorSimple Virtual Array (in englischer Sprache)](storsimple-virtual-array-manage-volumes.md)

