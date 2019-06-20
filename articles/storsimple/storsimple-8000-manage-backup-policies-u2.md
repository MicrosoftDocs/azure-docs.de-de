---
title: Verwalten von Sicherungsrichtlinien auf Geräten der StorSimple 8000-Serie | Microsoft-Dokumentation
description: Beschreibt, wie Sie den StorSimple-Geräte-Manager-Dienst zum Erstellen und Verwalten von manuellen Sicherungen, Sicherungszeitplänen und zur Sicherungsaufbewahrung auf Geräten der StorSimple 8000-Serie verwenden können.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/05/2017
ms.author: alkohli
ms.openlocfilehash: 607379f8645226a031646376df9ca18f4d3164bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60818823"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>Verwenden des StorSimple-Geräte-Manager-Diensts im Azure-Portal zum Verwalten von Sicherungsrichtlinien


## <a name="overview"></a>Übersicht

In diesem Tutorial wird erläutert, wie Sie das Blatt **Sicherungsrichtlinien** des StorSimple-Geräte-Manager-Diensts verwenden, um Sicherungsprozesse und die Sicherungsaufbewahrung für Ihre StorSimple-Volumes zu steuern. Außerdem wird beschrieben, wie eine manuelle Sicherung durchgeführt wird.

Wenn Sie ein Volume sichern, können Sie eine lokale Momentaufnahme oder eine Cloudmomentaufnahme erstellen. Wenn Sie ein lokal fixiertes Volume sichern, wird empfohlen, dass Sie eine Cloudmomentaufnahme festlegen. Die Aufzeichnung einer großen Anzahl lokaler Momentaufnahmen von einem lokalen Volume in Verbindung mit einem Dataset mit zahlreichen Änderungen führt zu einer Situation, in der der lokale Speicherplatz schnell ausgeschöpft sein kann. Wir empfehlen bei der Aufzeichnung lokaler Momentaufnahmen, dass Sie weniger tägliche Momentaufnahmen erstellen, um den aktuellen Status zu sichern, diese für einen Tag beibehalten und dann löschen.

Wenn Sie eine Cloudmomentaufnahme eines lokal fixierten Volumes erstellen, kopieren Sie nur die geänderten Daten in die Cloud, wo sie dedupliziert und komprimiert werden.

## <a name="the-backup-policy-blade"></a>Das Blatt „Sicherungsrichtlinie“

Auf dem Blatt **Sicherungsrichtlinien** für Ihr StorSimple-Gerät können Sie Sicherungsrichtlinien verwalten sowie lokale und Cloudmomentaufnahmen zeitlich planen. Sicherungsrichtlinien werden zum Konfigurieren von Sicherungszeitplänen und der Sicherungsaufbewahrung für eine Sammlung von Volumes verwendet. Mithilfe von Sicherungsrichtlinien können Sie eine Momentaufnahme mehrerer Volumes gleichzeitig erstellen. Dies bedeutet, dass es sich bei durch Sicherungsrichtlinien erstellten Sicherungen um ausfallsichere Kopien handelt.

Mithilfe der Tabelle mit den Sicherungsrichtlinien können Sie außerdem die vorhandenen Sicherungsrichtlinien anhand von einem oder mehreren der folgenden Felder filtern:

* **Richtlinienname** – Der der Richtlinie zugeordnete Name. Die folgenden verschiedenen Richtlinientypen sind verfügbar:

  * Geplante Richtlinien, die explizit vom Benutzer erstellt werden.
  * Importierte Richtlinien, die ursprünglich im StorSimple-Momentaufnahme-Manager erstellt wurden. Diese weisen ein Tag auf, das den StorSimple-Momentaufnahme-Manager-Host beschreibt, von dem die Richtlinien importiert wurden.

  > [!NOTE]
  > Automatische oder standardmäßige Sicherungsrichtlinien werden nicht mehr zum Zeitpunkt der Volumeerstellung aktiviert.

* **Letzte erfolgreiche Sicherung** – Das Datum und die Uhrzeit der letzten erfolgreichen Sicherung, die mit dieser Richtlinie erstellt wurde.

* **Nächste Sicherung** – Das Datum und die Uhrzeit der nächsten geplanten Sicherung, die von dieser Richtlinie initiiert wird.

* **Volumes** – Die Volumes, die der Richtlinie zugeordnet sind. Alle Volumes, die einer Sicherungsrichtlinie zugeordnet sind, werden beim Erstellen von Sicherungen gruppiert.

* **Zeitpläne** – Die Anzahl der Zeitpläne, die der Sicherungsrichtlinie zugeordnet sind.

Für Sicherungsrichtlinien können die folgenden allgemeinen Vorgänge ausgeführt werden:

* Sicherungsrichtlinie hinzufügen
* Hinzufügen oder Ändern eines Zeitplans
* Hinzufügen oder Entfernen eines Volumes
* Löschen einer Sicherungsrichtlinie
* Erstellen einer manuellen Sicherung

## <a name="add-a-backup-policy"></a>Sicherungsrichtlinie hinzufügen

Fügen Sie eine Sicherungsrichtlinie zum automatischen Planen Ihrer Sicherungen hinzu. Wenn Sie ein Volume erstmals erstellen, ist diesem keine standardmäßige Sicherungsrichtlinie zugeordnet. Zum Schutz von Daten auf dem Volume müssen Sie eine Sicherungsrichtlinie hinzufügen und zuweisen.

Führen Sie die folgenden Schritte im Azure-Portal aus, um eine Sicherungsrichtlinie für Ihr StorSimple-Gerät hinzuzufügen. Nachdem Sie die Richtlinie hinzugefügt haben, können Sie einen Zeitplan definieren (siehe [Hinzufügen oder Ändern eines Zeitplans](#add-or-modify-a-schedule).

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>Hinzufügen oder Ändern eines Zeitplans

Sie können einen Zeitplan hinzufügen oder ändern, der an eine vorhandene Sicherungsrichtlinie auf Ihrem StorSimple-Gerät angefügt wird. Führen Sie die folgenden Schritte im Azure-Portal aus, um einen Zeitplan hinzuzufügen oder zu ändern.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>Hinzufügen oder Entfernen eines Volumes

Sie können ein Volume hinzufügen oder entfernen, das einer Sicherungsrichtlinie auf Ihrem StorSimple-Gerät zugewiesen ist. Führen Sie die folgenden Schritte im Azure-Portal aus, um ein Volume hinzuzufügen oder zu entfernen.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>Löschen einer Sicherungsrichtlinie

Führen Sie die folgenden Schritte im Azure-Portal aus, um eine Sicherungsrichtlinie für Ihr StorSimple-Gerät zu löschen.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Erstellen einer manuellen Sicherung

Führen Sie die folgenden Schritte im Azure-Portal aus, um bei Bedarf eine (manuelle) Sicherung für ein einzelnes Volume zu erstellen.

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr [Verwalten von StorSimple-Geräten mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-8000-manager-service-administration.md).

