---
title: Verwenden der Übersicht über Geräte im StorSimple-Geräte-Manager-Dienst
description: Beschreibt die Übersicht über den StorSimple-Geräte-Manager-Dienst und erläutert, wie diese verwendet wird, um Speichermetriken und verbundene Initiatoren anzuzeigen sowie nach der Seriennummer und dem IQN zu suchen.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 34bbf4d723e46663efe77560245db74d9dea1fc4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017150"
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Verwenden der Übersicht über Geräte im StorSimple-Geräte-Manager-Dienst

## <a name="overview"></a>Übersicht
Das Blatt mit der Übersicht über StorSimple-Geräte zeigt die Informationen zu einem bestimmten StorSimple-Gerät. Dadurch unterscheidet es sich deutlich vom Blatt mit der Dienstübersicht, das Ihnen Informationen zu allen Geräten bietet, die in Ihrer Microsoft Azure StorSimple-Lösung enthalten sind.

Das Geräteübersichtsblatt bietet eine zusammenfassende Darstellung eines Geräts der StorSimple 8000-Serie, das bei einem bestimmten StorSimple-Geräte-Manager registriert ist. Dabei werden Geräteprobleme markiert, die die Aufmerksamkeit des Systemadministrators erfordern. Dieses Tutorial bietet eine Einführung zum Geräteübersichtsblatt, erläutert seinen Inhalt und seine Funktion und beschreibt die Aufgaben, die Sie auf diesem Blatt ausführen können.

Auf dem Geräteübersichtsblatt werden die folgenden Informationen angezeigt:

![Blatt mit der Geräteübersicht](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Befehlsleiste für die Verwaltung

Auf dem Blatt mit der Geräteübersicht sehen Sie die Optionen zum Verwalten Ihres StorSimple-Geräts. Im oberen Bereich und auf der linken Seite des Blatts finden Sie die Verwaltungsbefehle. Verwenden Sie diese Optionen, um Freigaben oder Volumes hinzuzufügen oder um Ihr Gerät zu aktualisieren bzw. ein Failover des Geräts zu ermöglichen.

![Befehlsleiste für die Verwaltung](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Essentials

Der Bereich „Zusammenfassung“ erfasst einige der wichtigen Eigenschaften wie z.B. Status, Modell, Ziel-IQN und Softwareversion. 

![Gerätezusammenfassung](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Überwachung

* Die Kachel **Warnungen** bietet eine Momentaufnahme aller aktiven Warnungen für Ihr Gerät, gruppiert nach dem jeweiligen Schweregrad.

    ![Kachel „Warnung“](./media/storsimple-8000-device-dashboard/device-summary4.png)

    Klicken Sie auf die Kachel, um das Blatt **Warnungen** zu öffnen, und klicken Sie anschließend auf einzelne Warnungen, um weitere Details zur jeweiligen Warnung anzuzeigen, darunter auch empfohlene Aktionen. Sie können die Warnung auch löschen, falls das Problem behoben wurde.

    ![Klicken Sie auf „Warnungsregeln“](./media/storsimple-8000-device-dashboard/device-summary10.png)

* Die Kachel **Status und die Integrität** bietet Einblicke in die Integrität der Hardwarekomponenten eines Geräts, einschließlich Gerätestatus. Der Gerätestatus kann „Offline“, „Online“, „Deaktiviert“ oder „Zur Einrichtung bereit“ sein.

    ![Kachel „Status und Integrität“](./media/storsimple-8000-device-dashboard/device-summary5.png)

* Die Kachel **Volumes** bietet einen nach Status gruppierten Überblick über die Anzahl der Volumes auf Ihrem Gerät.

    ![Kachel „Volumes“](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Klicken Sie auf die Kachel, um das Listenblatt **Volumes** zu öffnen. Klicken Sie anschließend auf ein einzelnes Volume, um seine Eigenschaften anzuzeigen oder zu ändern.
    
    ![Klicken Sie auf die Kachel „Volumes“](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    Weitere Informationen finden Sie unter [Verwalten von Volumes](storsimple-8000-manage-volumes-u2.md).

* Im Diagramm zur **Nutzung** können Sie sehen, wie viel primären Speicher auf Ihrem Gerät und wie viel Cloudspeicher in den letzten 7 Tagen (Standardzeitraum) belegt wurde.

     ![Kachel „Nutzung“](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Zur Auswahl einer anderen Zeitskala verwenden Sie die Option **Bearbeiten** rechts oben im Diagramm.

     ![Bearbeiten des Nutzungsdiagramms](./media/storsimple-8000-device-dashboard/device-summary12.png)

     In diesem Diagramm können Sie Metriken für den gesamten primären Speicher (die Menge der Daten, die von Hosts in das Gerät geschrieben wurden) sowie für den gesamten Cloudspeicher sehen, der von Ihrem Gerät in einem Zeitraum beansprucht wurde.
  
     In diesem Zusammenhang bezieht sich *primärer Speicher* auf die Gesamtmenge der Daten, die vom Host geschrieben werden. Dieser Speicher kann nach Volumetyp untergliedert werden: Der *primäre mehrstufige Speicher* enthält lokal gespeicherte Daten sowie auch Daten, die in die Cloud ausgelagert wurden. Der *primäre lokale Speicher* enthält nur lokal gespeicherte Daten. *Cloudspeicher* ist demgegenüber ein Maß für die Gesamtmenge der Daten, die in der Cloud gespeichert sind. Dies umfasst mehrstufige Daten und Sicherungen. In der Cloud gespeicherte Daten sind dedupliziert und komprimiert, der Primärspeicher dagegen zeigt die Menge an Speicherplatz an, der belegt wird, bevor die Daten dedupliziert und komprimiert werden. (Sie können diese beiden Zahlen vergleichen, um einen Eindruck von der Komprimierungsrate zu erhalten.) Sowohl für primären als auch für Cloudspeicher basieren die angezeigten Mengen auf der Nachverfolgungshäufigkeit, die Sie konfigurieren. Wenn Sie beispielsweise eine Häufigkeit von einer Woche wählen, werden im Diagramm Daten für jeden Tag der vorherigen Woche angezeigt.

     Um die Menge des über einen Zeitraum beanspruchten Cloudspeichers anzuzeigen, aktivieren Sie die Option **VERWENDETER CLOUDSPEICHER**. Um die gesamte Speichermenge anzuzeigen, die vom Host geschrieben wurde, aktivieren Sie die Optionen **VERWENDETER PRIMÄRER MEHRSTUFIGER SPEICHER** und **VERWENDETER PRIMÄRER LOKALER SPEICHER**. 
     Weitere Informationen finden Sie unter [Überwachen Ihres StorSimple-Geräts mithilfe des StorSimple-Geräte-Manager-Diensts](./storsimple-8000-monitor-device.md).


* Die Kachel **Kapazität** zeigt den primären Speicher, der bereitgestellt wurde und auf dem Gerät verbleibt, im Verhältnis zum verfügbaren Gesamtspeicher des Geräts. **Bereitgestellt** bezieht sich auf die Menge an Speicherplatz, die vorbereitet wird und für die Nutzung reserviert ist. **Verbleibend** bezieht sich auf die verbleibenden Kapazität, die über dieses Gerät bereitgestellt werden kann. 

    ![Kachel für Nutzung (2)](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Klicken Sie auf diese Kachel, um anzuzeigen, wie die Kapazität für mehrstufige und lokale Volumes bereitgestellt wird. **Verbleibende mehrstufige Kapazität** bezeichnet die verfügbare Kapazität, einschließlich Cloud, die bereitgestellt werden kann. **Verbleibende lokale Kapazität** ist die verbleibende Kapazität auf den Datenträgern, die an dieses Gerät angeschlossen sind.

    ![Klicken Sie auf das Nutzungsdiagramm](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über das [Blatt „StorSimple-Dienstzusammenfassung“](storsimple-8000-service-dashboard.md).
* Erfahren Sie mehr [Verwalten von StorSimple-Geräten mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-8000-manager-service-administration.md).