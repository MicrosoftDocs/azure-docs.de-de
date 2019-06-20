---
title: Installieren von Update 4 auf einem Gerät der StorSimple 8000-Serie | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Update 4 für die StorSimple 8000-Serie auf Ihrem StorSimple-Gerät der 8000-Serie installieren.
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
ms.date: 08/02/2017
ms.author: alkohli
ms.openlocfilehash: 5b48cbd1020cfd51fe989a9be33197f2735f21f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60860377"
---
# <a name="install-update-4-on-your-storsimple-device"></a>Installieren von Update 4 auf Ihrem StorSimple-Gerät

## <a name="overview"></a>Übersicht

In diesem Tutorial wird beschrieben, wie Sie Update 4 auf einem StorSimple-Gerät mit einer früheren Softwareversion über das Azure-Portal und mithilfe der Hotfixmethode ausführen. Die Hotfixmethode wird verwendet, wenn ein Gateway auf einer anderen Netzwerkschnittstelle als DATA 0 des StorSimple-Geräts konfiguriert ist und Sie versuchen, das Update von einer Softwareversion durchzuführen, die noch nicht Update 1 enthält.

Update 4 umfasst Updates von Gerätesoftware, USM-Firmware, LSI-Treiber und -Firmware, Storport und Spaceport sowie Sicherheitsupdates für das Betriebssystem und verschiedene andere Betriebssystemupdates.  Die Updates für Gerätesoftware, USM-Firmware, Spaceport, Storport und anderen Betriebssystemupdates sorgen nicht für Betriebsunterbrechungen. Die unterbrechungsfreien und regulären Updates können über das Azure-Portal oder die Hotfixmethode angewendet werden. Die Updates für die Datenträgerfirmware führen zu einer Betriebsunterbrechung und können nur mittels Hotfixmethode über die Windows PowerShell-Schnittstelle des Geräts angewendet werden.

> [!IMPORTANT]
> * Vor der Installation wird ein Satz manueller und automatischer Vorabprüfungen durchgeführt, mit denen die Geräteintegrität in Bezug auf Hardwarestatus und Netzwerkkonnektivität ermittelt wird. Diese Vorabprüfungen werden nur ausgeführt, wenn Sie die Updates aus dem Azure-Portal ausführen.
> * Wir empfehlen, die Software und anderen regulären Updates über das Azure-Portal zu installieren. Sie sollten nur dann die Windows PowerShell-Schnittstelle des Geräts (zum Installieren der Updates) verwenden, wenn die Vorabprüfung für das Gateway im Portal fehlschlägt. Abhängig von der Version, von der aus Sie aktualisieren, dauert die Installation des Updates mindestens vier Stunden. Die Wartungsmodus-Updates müssen auch über die Windows PowerShell-Schnittstelle des Geräts installiert werden. Da Updates im Wartungsmodus den Betrieb unterbrechen, führen sie zu einer Ausfallzeit für Ihr Gerät.
> * Stellen Sie bei Ausführung des optionalen StorSimple Snapshot Managers sicher, dass Sie die Snapshot Manager-Version auf Update 4 aktualisiert haben, bevor Sie das Gerät aktualisieren.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-portal"></a>Installieren von Update 4 über das Azure-Portal
Führen Sie die folgenden Schritte aus, um Ihr Gerät auf [Update 4](storsimple-update4-release-notes.md) zu aktualisieren.

> [!NOTE]
> Microsoft ruft zusätzliche Diagnoseinformationen vom Gerät ab. Dadurch können wir besser Informationen auf dem Gerät sammeln und Probleme diagnostizieren, wenn unser Betriebsteam Geräte mit Problemen ermittelt. 

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update4-via-portal.md)]

Stellen Sie sicher, dass auf Ihrem Gerät **Update 4 der StorSimple 8000-Serie (6.3.9600.17820)** ausgeführt wird. Das **Datum der letzten Aktualisierung** hat sich ebenfalls geändert.

* Sie werden auch feststellen, dass Wartungsmodus-Updates verfügbar sind. (Diese Meldung wird nach der Installation des Updates u.U. noch bis zu 24 Stunden angezeigt.) Wartungsmodus-Updates unterbrechen die Verwendung, führen zu Ausfallzeiten des Geräts und können nur über die Windows PowerShell-Schnittstelle Ihres Geräts angewendet werden.

* Laden Sie die Wartungsmodus-Updates herunter, indem Sie mithilfe der unter [Herunterladen von Hotfixes](#to-download-hotfixes) angegebenen Schritte nach KB4011837 suchen und es herunterladen, um Datenträger-Firmwareupdates zu installieren. (Die anderen Updates müssten inzwischen bereits installiert sein.) Führen Sie die unter [Installieren und Überprüfen von Wartungsmodus-Hotfixes](#to-install-and-verify-maintenance-mode-hotfixes) angegebenen Schritte aus, um die Wartungsmodus-Updates zu installieren.

## <a name="install-update-4-as-a-hotfix"></a>Installieren von Update 4 als Hotfix
Die empfohlene Methode zum Installieren von Update 4 ist über das Azure-Portal.

Gehen Sie wie folgt vor, falls beim Installieren über das Azure-Portal ein Fehler bei der Gatewayprüfung auftritt. Die Überprüfung zeigt einen Fehler, weil Sie ein Gateway auf einer anderen Netzwerkschnittstelle als DATA 0 zugewiesen haben und auf Ihrem Gerät eine Softwareversion vor Update 1 ausgeführt wird.

Die Softwareversionen, die mithilfe dieser Methode aktualisiert werden können, sind:

* Update 0.1, 0.2, 0.3
* Update 1, 1.1, 1.2
* Update 2, 2.1, 2.2
* Update 3, 3.1


Das Hotfixverfahren umfasst die folgenden drei Schritte:

1. Laden Sie die Hotfixes aus dem Microsoft Update-Katalog herunter.
2. Installieren und überprüfen Sie die Hotfixes für den normalen Modus.
3. Installieren und überprüfen Sie den Hotfix für den Wartungsmodus.

#### <a name="download-updates-for-your-device"></a>Herunterladen von Updates für Ihr Gerät

Sie müssen die folgenden Hotfixes in der vorgeschriebenen Reihenfolge und in die vorgeschlagenen Ordner herunterladen und installieren:

| Reihenfolge | KB | BESCHREIBUNG | Updatetyp | Installationszeit |Installationsordner|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4011839 |Softwareupdate |Regulär <br></br>Unterbrechungsfrei |ca. 25 Min. |FirstOrderUpdate|
| 2A. |KB4011841 <br> KB4011842 |Updates von LSI-Treiber und -Firmware <br> Update der USM-Firmware (Version 3.38) |Regulär <br></br>Unterbrechungsfrei |ca. 3 Stunden <br> (enthält 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2B. |KB3139398, KB3108381 <br> KB3205400, KB3142030 <br> KB3197873, KB3197873 <br> KB3192392, KB3153704 <br> KB3174644, KB3139914  |Sicherheitsupdatepaket für das Betriebssystem <br> Herunterladen von Windows Server 2012 R2 |Regulär <br></br>Unterbrechungsfrei |- |SecondOrderUpdate|
| 2C. |KB3210083, KB3103616 <br> KB3146621, KB3121261 <br> KB3123538 |Paket mit Betriebssystemupdates <br> Herunterladen von Windows Server 2012 R2 |Regulär <br></br>Unterbrechungsfrei |- |SecondOrderUpdate|

Sie müssen zusätzlich zu den in den vorhergehenden Tabellen enthaltenen Updates ggf. auch Updates der Firmware von Datenträgern installieren. Sie können überprüfen, ob Sie Updates für die Datenträgerfirmware benötigen, indem Sie das Cmdlet `Get-HcsFirmwareVersion` ausführen. Wenn Sie die Firmwareversionen `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N002`, `0106` ausführen, müssen Sie diese Updates nicht installieren.

| Reihenfolge | KB | BESCHREIBUNG | Updatetyp | Installationszeit | Installationsordner|
| --- | --- | --- | --- | --- | --- |
| 3. |KB3121899 |Datenträgerfirmware |Wartung <br></br>Mit Unterbrechung |~ 30 Min. | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Dieses Verfahren muss nur einmal ausgeführt werden, um Update 4 anzuwenden. Über das Azure-Portal können Sie nachfolgende Updates installieren.
> * Wenn Sie von Update 3 oder 3.1 aus aktualisieren, dauert die gesamte Installation etwa vier Stunden.
> * Stellen Sie vor dem Verwenden dieses Verfahrens zum Anwenden des Updates sicher, dass beide Gerätecontroller online und alle Hardwarekomponenten fehlerfrei sind.

Führen Sie die folgenden Schritte aus, um diese Datei herunterzuladen und die Hotfixes zu installieren.

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter [Versionsanmerkungen zu Update 4](storsimple-update4-release-notes.md).

