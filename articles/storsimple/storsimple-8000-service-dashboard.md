---
title: Verwenden von Geräten der StorSimple 8000-Serie – Zusammenfassung | Microsoft-Dokumentation
description: Beschreibt das StorSimple-Dienstübersichtsblatt und erläutert, wie Sie den Zustand Ihrer StorSimple-Lösung über dieses Dashboard verwalten.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: c174f6ce0fb3d40af953be205a7bfcca60fbfeec
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60633152"
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>Verwenden des Dienstübersichtsblatt für Geräte der StorSimple 8000-Serie

## <a name="overview"></a>Übersicht

Auf dem Übersichtsblatt Ihres StorSimple-Geräte-Manager-Diensts erhalten Sie einen Überblick über alle Geräte, die mit dem StorSimple-Geräte-Manager-Dienst verbunden sind. Geräte, die die Aufmerksamkeit des Systemadministrators erfordern, sind darauf markiert. Dieses Tutorial bietet eine Einführung zum Dienstübersichtsblatt, erläutert Inhalt und Funktion des Dashboard und beschreibt die Aufgaben, die Sie auf dieser Seite ausführen können.

![Dienstzusammenfassung](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>Befehle für Verwaltung

Auf dem StorSimple-Dienstübersichtsblatt sehen Sie die Optionen, mit denen Sie sowohl Ihren StorSimple-Geräte-Manager-Dienst als auch die Geräte der StorSimple 8000-Serie verwalten können, die für diesen Dienst registriert sind. Im oberen Bereich und auf der linken Seite des Blatts finden Sie die Verwaltungsbefehle.

![Befehlsleiste](./media/storsimple-8000-service-dashboard/service-summary2.png)

Verwenden Sie diese Optionen, um unterschiedliche Vorgänge auszuführen, wie z.B. das Hinzufügen von Freigaben oder Volumes oder das Überwachen verschiedener Aufträge, die auf den StorSimple-Geräten ausgeführt werden.


## <a name="essentials"></a>Zusammenfassung

Der Bereich „Essentials“ (Zusammenfassung) erfasst einige wichtige Eigenschaften wie die Ressourcengruppe, den Speicherort und das Abonnement, in dem Ihr StorSimple-Geräte-Manager erstellt wurde.

![Zusammenfassung](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>Dienstübersicht für den StorSimple-Geräte-Manager

* Die Kachel **Warnungen** stellt eine Momentaufnahme aller aktiven Warnungen auf sämtlichen Geräten bereit, gruppiert nach dem jeweiligen Schweregrad.

    ![Kachel „Warnungen“](./media/storsimple-8000-service-dashboard/service-summary4.png)

    Klicken Sie auf die Kachel, um das Blatt **Warnungen** zu öffnen. Dort können Sie auf einzelne Warnungen klicken, um weitere Details zur jeweiligen Warnung anzuzeigen, einschließlich empfohlener Aktionen. Sie können die Warnung auch löschen, falls das Problem behoben wurde.

    ![Auf Kachel „Warnungen“ klicken](./media/storsimple-8000-service-dashboard/service-summary8.png)

* Die Kachel **Kapazität** zeigt den bereitgestellten primären Speicher, der auf allen Geräten verbleibt, im Verhältnis zum Gesamtspeicher, der auf allen Geräten zur Verfügung steht. **Bereitgestellt** bezieht sich auf die Menge an Speicherplatz, die vorbereitet wird und für die Nutzung reserviert ist. **Verbleibend** bezieht sich auf die verbleibenden Kapazität, die über alle Geräte bereitgestellt werden kann.

    ![Kachel „Kapazität“](./media/storsimple-8000-service-dashboard/service-summary6.png)

    **Remaining Tiered** (Verbleibend (Mehrstufig)) bezeichnet die verfügbare Kapazität, einschließlich der Cloud, die bereitgestellt werden kann. **Remaining Local** (Verbleibend (lokal)) ist die verbleibende Kapazität auf den Datenträgern, die an die Geräte der StorSimple 8000-Serie angefügt sind.


* Im **Nutzungsdiagramm** sehen Sie die relevanten Metriken für Ihre Geräte. Sie können den primären Speicher anzeigen, der von allen Geräten verwendet wird, sowie den Cloudspeicher, der von den Geräten in den letzten sieben Tagen, dem standardmäßigen Zeitraum, verbraucht wurde. 

    ![Kachel „Nutzung“](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Zur Auswahl einer anderen Zeitskala verwenden Sie die Option **Bearbeiten** rechts oben im Diagramm.

     ![Auf Kachel „Nutzung“ klicken](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![Diagrammdaten exportieren](./media/storsimple-8000-service-dashboard/service-summary11.png)

* Die Kachel **Geräte** bietet einen Überblick über die Anzahl der Geräte der StorSimple 8000-Serie in Ihrem StorSimple-Geräte-Manager, gruppiert nach Gerätestatus. 

    ![Kachel „Geräte“](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Klicken Sie auf diese Kachel, um das Listenblatt **Geräte** zu öffnen, und klicken Sie anschließend auf ein einzelnes Gerät, um seine spezifische Geräteübersicht anzuzeigen. Sie können auch bestimmte Aktionen für ein Gerät über sein jeweiliges Geräteübersichtsblatt ausführen. Weitere Informationen zum Geräteübersichtsblatt finden Sie unter [Geräteübersichtsblatt](storsimple-8000-device-dashboard.md).

    ![Auf Kachel „Geräte“ klicken](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>Anzeigen der Aktivitätsprotokolle

Um die verschiedenen, in Ihrem StorSimple-Geräte-Manager ausgeführten Vorgänge anzuzeigen, klicken Sie auf den Link **Aktivitätsprotokolle** auf der linken Seite Ihres StorSimple-Dienstübersichtsblatts. Dadurch gelangen Sie zum Blatt **Aktivitätsprotokolle**, das eine Zusammenfassung der letzten ausgeführten Vorgänge enthält.

![Aktivitätsprotokolle](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen über das [Verwalten Ihres StorSimple-Geräts mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-8000-manager-service-administration.md)

