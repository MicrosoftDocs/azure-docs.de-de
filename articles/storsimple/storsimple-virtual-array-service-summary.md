---
title: StorSimple Virtual Array – Dienstzusammenfassungsblatt | Microsoft-Dokumentation
description: Beschreibt das Dienstübersichtsblatt für den StorSimple-Geräte-Manager und erläutert, wie Sie die Integrität Ihres StorSimple Virtual Array mithilfe dieses Blatt überwachen können.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 8a2b9a84-b0e6-48b9-b366-d16f004241a5
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 9c05bddaeb3c34400db1ec75c624ef00a85d9444
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720704"
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Verwenden der Dienstübersichtsblatts für den StorSimple-Geräte-Manager, der mit dem StorSimple Virtual Array verbunden ist
## <a name="overview"></a>Übersicht
Das Dienstübersichtsblatt des StorSimple-Geräte-Managers enthält eine Übersicht der StorSimple Virtual Arrays (auch als lokale virtuelle StorSimple-Geräte oder virtuelle Geräte bezeichnet), die mit dem Dienst verbunden sind. Die Geräte, die der Aufmerksamkeit eines Systemadministrators bedürfen, sind entsprechend gekennzeichnet. Dieses Tutorial bietet eine Einführung zum Dienstübersichtsblatt, erläutert seinen Inhalt und seine Funktion und beschreibt die Aufgaben, die Sie auf diesem Blatt ausführen können.

![Dienstdashboard](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Verwaltungsbefehle und Zusammenfassung
Auf dem StorSimple-Übersichtsblatt sehen Sie die Optionen, mit denen Sie sowohl Ihren StorSimple-Geräte-Manager-Dienst als auch die virtuellen Arrays verwalten können, die für diesen Dienst registriert sind. Im oberen Bereich und auf der linken Seite des Blatts finden Sie die Verwaltungsbefehle.

Verwenden Sie diese Optionen, um unterschiedliche Vorgänge auszuführen, wie z.B. das Hinzufügen von Freigaben oder Volumes oder das Überwachen verschiedener Aufträge, die auf den virtuellen Arrays ausgeführt werden.

Der Bereich „Essentials“ (Zusammenfassung) erfasst einige wichtige Eigenschaften wie die Ressourcengruppe, den Speicherort und das Abonnement, in dem Ihr StorSimple-Geräte-Manager erstellt wurde.

## <a name="storsimple-device-manager-service-summary"></a>Dienstübersicht für den StorSimple-Geräte-Manager
* Die Kachel **Warnungen** stellt eine Momentaufnahme aller aktiven Warnungen auf sämtlichen virtuellen Geräten bereit, gruppiert nach dem jeweiligen Schweregrad. Klicken Sie auf die Kachel, um das Blatt **Warnungen** zu öffnen. Dort können Sie auf einzelne Warnungen klicken, um weitere Details zur jeweiligen Warnung anzuzeigen, einschließlich empfohlener Aktionen. Sie können die Warnung auch löschen, falls das Problem behoben wurde.
* Die Kachel **Kapazität** zeigt den bereitgestellten primären Speicher, der auf allen virtuellen Geräten verbleibt, im Verhältnis zum Gesamtspeicher, der auf allen virtuellen Geräten zur Verfügung steht. **Bereitgestellt** bezieht sich auf die Menge an Speicherplatz, die vorbereitet wird und für die Nutzung reserviert ist. **Verbleibend** bezieht sich auf die verbleibenden Kapazität, die über alle virtuellen Geräte bereitgestellt werden kann. **Remaining Tiered** (Verbleibend (Mehrstufig)) bezeichnet die verfügbare Kapazität, einschließlich der Cloud, die bereitgestellt werden kann. **Remaining Local** (Verbleibend (lokal)) ist die verbleibende Kapazität auf den Datenträgern, die an die virtuellen Arrays angefügt sind.
* Im **Diagrammbereich** sehen Sie die relevanten Metriken für Ihre virtuellen Geräte. Sie können den primären Speicher anzeigen, der von allen virtuellen Geräten verwendet wird, sowie den Cloudspeicher, der von den virtuellen Geräten in den letzten sieben Tagen, dem standardmäßigen Zeitraum, verbraucht wurde. Verwenden Sie die Option **Bearbeiten** in der oberen rechten Ecke des Diagramms, um eine andere Zeitskala auszuwählen.
* Die Kachel **Geräte** bietet einen Überblick über die Anzahl der virtuellen Arrays in Ihrem StorSimple-Geräte-Manager, gruppiert nach Gerätestatus. Klicken Sie auf diese Kachel, um das Listenblatt **Geräte** zu öffnen, und klicken Sie anschließend auf ein einzelnes Gerät, um seine spezifische Geräteübersicht anzuzeigen. Sie können auch bestimmte Aktionen für ein Gerät über sein jeweiliges Geräteübersichtsblatt ausführen. Weitere Informationen zum Geräteübersichtsblatt finden Sie unter [Geräteübersichtsblatt](storsimple-virtual-array-device-summary.md).

## <a name="view-the-activity-logs"></a>Anzeigen der Aktivitätsprotokolle
Um die verschiedenen, in Ihrem StorSimple-Geräte-Manager ausgeführten Vorgänge anzuzeigen, klicken Sie auf den Link **Aktivitätsprotokolle** auf der linken Seite Ihres StorSimple-Dienstübersichtsblatts. Dadurch gelangen Sie zum Blatt **Aktivitätsprotokolle**, das eine Zusammenfassung der letzten ausgeführten Vorgänge enthält.

![Aktivitätsprotokolle](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie, wie Sie die lokale Webbenutzeroberfläche verwenden, um [Ihr StorSimple Virtual Array zu verwalten](storsimple-ova-web-ui-admin.md).

