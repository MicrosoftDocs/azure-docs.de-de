---
title: Installieren von Update 5 auf einem Gerät der StorSimple 8000-Serie | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Update 5 für die StorSimple 8000-Serie auf Ihrem StorSimple-Gerät der 8000-Serie installieren.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: d86e77ef0148c0fac3dfa31153364de153b094ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62126748"
---
# <a name="install-update-5-on-your-storsimple-device"></a>Installieren von Update 5 auf Ihrem StorSimple-Gerät

## <a name="overview"></a>Übersicht

In diesem Tutorial erfahren Sie, wie Sie Update 5 über das Azure-Portal und mithilfe der Hotfixmethode auf einem StorSimple-Gerät mit einer älteren Softwareversion installieren. Der Hotfixmethode wird für die Installation von Update 5 auf einem Gerät verwendet, auf dem Versionen vor Update 3 ausgeführt werden. Die Hotfixmethode wird auch verwendet, wenn ein Gateway auf einer anderen Netzwerkschnittstelle als DATA 0 des StorSimple-Geräts konfiguriert ist und Sie versuchen, das Update von einer Softwareversion durchzuführen, die noch nicht Update 1 enthält.

Update 5 umfasst Updates für Gerätesoftware, Storport und Spaceport sowie Sicherheitsupdates für das Betriebssystem, allgemeine Betriebssystemupdates und Updates für Datenträgerfirmware.  Die Updates für Gerätesoftware, Spaceport, Storport, Sicherheit und andere Betriebssystemupdates sind unterbrechungsfreie Updates. Die unterbrechungsfreien und regulären Updates können über das Azure-Portal oder die Hotfixmethode angewendet werden. Die Updates für die Datenträgerfirmware führen zu einer Betriebsunterbrechung und werden unter Verwendung der Hotfixmethode über die Windows PowerShell-Schnittstelle des Geräts angewendet, wenn sich das Gerät im Wartungsmodus befindet.

> [!IMPORTANT]
> * Update 5 ist ein obligatorisches Update und sollte sofort installiert werden. Weitere Informationen finden Sie in den [Versionshinweisen zu Update 5](storsimple-update5-release-notes.md).
> * Vor der Installation wird ein Satz manueller und automatischer Vorabprüfungen durchgeführt, mit denen die Geräteintegrität in Bezug auf Hardwarestatus und Netzwerkkonnektivität ermittelt wird. Diese Vorabprüfungen werden nur ausgeführt, wenn Sie die Updates aus dem Azure-Portal ausführen.
> * Bei der Aktualisierung eines Geräts, auf dem Versionen vor Update 3 ausgeführt werden, wird ausdrücklich empfohlen, Updates mithilfe der Hotfixmethode zu installieren. Wenn Probleme auftreten, [ log a support ticket](storsimple-8000-contact-microsoft-support.md) (erstellen Sie ein Supportticket).
> * Wir empfehlen, die Software und anderen regulären Updates über das Azure-Portal zu installieren. Sie sollten nur dann die Windows PowerShell-Schnittstelle des Geräts (zum Installieren der Updates) verwenden, wenn die Vorabprüfung für das Gateway im Portal fehlschlägt. Abhängig von der Version, von der aus Sie aktualisieren, dauert die Installation des Updates mindestens vier Stunden. Wartungsmodusupdates müssen über die Windows PowerShell-Schnittstelle des Geräts ausgeführt werden. Da Updates im Wartungsmodus den Betrieb unterbrechen, führen sie zu einer Ausfallzeit für Ihr Gerät.
> * Vergewissern Sie sich bei Verwendung des optionalen StorSimple Snapshot Managers, dass Sie die Snapshot Manager-Version auf Update 5 aktualisiert haben, bevor Sie das Gerät aktualisieren.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-portal"></a>Installieren von Update 5 über das Azure-Portal
Führen Sie die folgenden Schritte aus, um Ihr Gerät auf [Update 5](storsimple-update5-release-notes.md) zu aktualisieren.

> [!NOTE]
> Microsoft ruft zusätzliche Diagnoseinformationen vom Gerät ab. Dadurch können wir besser Informationen auf dem Gerät sammeln und Probleme diagnostizieren, wenn unser Betriebsteam Geräte mit Problemen ermittelt.

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update5-via-portal.md)]

Vergewissern Sie sich, dass auf Ihrem Gerät **Update 5 der StorSimple 8000-Serie (6.3.9600.17845)** ausgeführt wird. Das **Datum der letzten Aktualisierung** sollte sich ändern.

Sie werden auch feststellen, dass Wartungsmodus-Updates verfügbar sind. (Diese Meldung wird nach der Installation des Updates u.U. noch bis zu 24 Stunden angezeigt.) Die Schritte zum Installieren von Updates im Wartungsmodus werden im nächsten Abschnitt beschrieben.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## <a name="install-update-5-as-a-hotfix"></a>Installieren von Update 5 als Hotfix

Die Softwareversionen, die mithilfe dieser Methode aktualisiert werden können, sind:

* Update 0.1, 0.2, 0.3
* Update 1, 1.1, 1.2
* Update 2, 2.1, 2.2
* Update 3, 3.1
* Update 4

> [!NOTE] 
> Wenn Sie ein Update von Update 3 und höher durchführen, wird empfohlen, Update 5 über das Azure-Portal zu installieren. Verwenden Sie dieses Verfahren beim Aktualisieren eines Geräts, auf dem Versionen vor Update 3 ausgeführt werden. Sie können auch wie folgt vorgehen, falls beim Installieren über das Azure-Portal ein Fehler bei der Gatewayprüfung auftritt. Die Überprüfung ist nicht erfolgreich, wenn Sie ein Gateway einer DATA 0-fremden Netzwerkschnittstelle zugewiesen haben und auf Ihrem Gerät eine Softwareversion vor Update 1 ausgeführt wird.

Das Hotfixverfahren umfasst die folgenden drei Schritte:

1. Laden Sie die Hotfixes aus dem Microsoft Update-Katalog herunter.
2. Installieren und überprüfen Sie die Hotfixes für den normalen Modus.
3. Installieren und überprüfen Sie den Hotfix für den Wartungsmodus.

#### <a name="download-updates-for-your-device"></a>Herunterladen von Updates für Ihr Gerät

Sie müssen die folgenden Hotfixes in der vorgeschriebenen Reihenfolge und in die vorgeschlagenen Ordner herunterladen und installieren:

| Reihenfolge | KB | BESCHREIBUNG | Updatetyp | Installationszeit |Installationsordner|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Softwareupdate<br> Laden Sie _HcsSoftwareUpdate.exe_ und _CisMSDAgent.exe_ herunter. |Regulär <br></br>Unterbrechungsfrei |ca. 25 Min. |FirstOrderUpdate|

Wenn Sie das Update über ein Gerät mit Update 4 durchführen, müssen Sie lediglich die kumulativen Betriebssystemupdates als zweitrangige Updates installieren.

| Reihenfolge | KB | BESCHREIBUNG | Updatetyp | Installationszeit |Installationsordner|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |Paket mit kumulativen Betriebssystemupdates <br> Laden Sie die Windows Server 2012 R2-Version herunter. |Regulär <br></br>Unterbrechungsfrei |- |SecondOrderUpdate|

Wenn Sie das Update über ein Gerät mit Update 3 oder einer älteren Version durchführen, installieren Sie zusätzlich zu den kumulativen Updates Folgendes:

| Reihenfolge | KB | BESCHREIBUNG | Updatetyp | Installationszeit |Installationsordner|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |Updates von LSI-Treiber und -Firmware <br> Update der USM-Firmware (Version 3.38) |Regulär <br></br>Unterbrechungsfrei |ca. 3 Stunden <br> (enthält 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |Sicherheitsupdatepaket für das Betriebssystem <br> Laden Sie die Windows Server 2012 R2-Version herunter. |Regulär <br></br>Unterbrechungsfrei |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |Paket mit Betriebssystemupdates <br> Laden Sie die Windows Server 2012 R2-Version herunter. |Regulär <br></br>Unterbrechungsfrei |- |SecondOrderUpdate|


Sie müssen zusätzlich zu den in den vorhergehenden Tabellen enthaltenen Updates ggf. auch Updates der Firmware von Datenträgern installieren. Sie können überprüfen, ob Sie Updates für die Datenträgerfirmware benötigen, indem Sie das Cmdlet `Get-HcsFirmwareVersion` ausführen. Wenn Sie die Firmwareversionen `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107` ausführen, müssen Sie diese Updates nicht installieren.

| Reihenfolge | KB | BESCHREIBUNG | Updatetyp | Installationszeit | Installationsordner|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Datenträgerfirmware |Wartung <br></br>Mit Unterbrechung |~ 30 Min. | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Wenn Sie von Update 4 aktualisieren, dauert die gesamte Installation knapp vier Stunden.
> * Stellen Sie vor dem Verwenden dieses Verfahrens zum Anwenden des Updates sicher, dass beide Gerätecontroller online und alle Hardwarekomponenten fehlerfrei sind.

Führen Sie die folgenden Schritte aus, um diese Datei herunterzuladen und die Hotfixes zu installieren.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter [StorSimple 8000 Series Update 5 release notes](storsimple-update5-release-notes.md) (Versionshinweise zu Update 5 für die StorSimple 8000-Serie).

