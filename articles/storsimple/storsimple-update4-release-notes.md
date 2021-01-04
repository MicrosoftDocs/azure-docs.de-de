---
title: Versionshinweise zu Update 4 der StorSimple 8000-Serie | Microsoft-Dokumentation
description: Beschreibt die neuen Features sowie Probleme und Problemumgehungen für Update 4 der StorSimple 8000-Serie.
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
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: 060a52b61dcd43fe0d05b2a8ef594c0ab9f2cc9e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954051"
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>Versionshinweise zu Update 4 der StorSimple 8000-Serie

## <a name="overview"></a>Übersicht

Die folgenden Versionshinweise beschreiben die neuen Features und weisen auf wichtige offene Punkte bei Update 4 der StorSimple 8000-Serie hin. Es enthält auch eine Liste der StorSimple-Software-Updates, die in dieser Version enthalten sind. 

Update 4 kann auf alle StorSimple-Geräte angewendet werden, auf denen die GA-Version oder Update 0.1 bis Update 3.1 ausgeführt wird. Update 4 ist die Geräteversion 6.3.9600.17820 zugeordnet.

Lesen Sie vor der Bereitstellung des Updates in Ihrer StorSimple-Lösung die Informationen in den Versionsanmerkungen sorgfältig durch.

> [!IMPORTANT]
> * Update 4 beinhaltet Updates für Gerätesoftware, USM-Firmware, LSI-Treiber und -Firmware, Datenträgerfirmware, Storport und Spaceport, Sicherheit und andere Betriebssysteme. Es dauert ungefähr 4 Stunden, dieses Update zu installieren. Ein Firmwareupdate auf dem Datenträger ist ein Update, das zu einer Unterbrechung und zu einer Ausfallzeit Ihres Geräts führt. Wir empfehlen Ihnen die Anwendung von Update 4, um Ihr Gerät auf dem neuesten Stand zu halten. 
> * Bei neuen Versionen werden Updates möglicherweise nicht sofort angezeigt, da diese in mehreren Phasen bereitgestellt werden. Warten Sie einige Tage, und suchen Sie dann erneut nach Updates, da diese bald verfügbar werden.

## <a name="whats-new-in-update-4"></a>Neuerungen in Update 4

Die folgenden wichtigen Verbesserungen und Fehlerbehebungen wurden in Update 4 vorgenommen.

* **Intelligentere automatisierte Algorithmen zur Freigabe von Speicherplatz:** In Update 4 wurden automatisierte Algorithmen zur Freigabe von Speicherplatz verbessert, um die Zyklen zur Freigabe von Speicherplatz auf Grundlage des erwarteten freigegebenen und in der Cloud verfügbaren Speicherplatzes anzupassen. 

* **Leistungsverbesserungen bei lokalen Volumes:** In Update 4 wurde die Leistung lokaler Volumes in Szenarien mit hoher Datenerfassung (Daten vergleichbar mit Volumegröße) verbessert.

* **Heatmap-basierte Wiederherstellung:** In früheren Versionen wurden nach einer Notfallwiederherstellung die Daten aus der Cloud basierend auf den Zugriffsmustern wiederhergestellt, was zu einer niedrigen Leistung führte. 

    In Update 4 wurde ein neues Feature implementiert, das häufig aufgerufene Daten erfasst, um eine Heatmap der Gerätenutzung vor einer Notfallwiederherstellung zu erstellen. (Die am häufigsten verwendeten Datenblöcke weisen eine hohe Temperatur auf, wohingegen weniger genutzte Blöcke eine geringere Temperatur aufweisen.) Nach der Notfallwiederherstellung verwendet StorSimple die Heatmap, um die Daten aus der Cloud automatisch wiederherzustellen und zu aktivieren. 

    Alle Wiederherstellungsvorgänge sind jetzt Heatmap-basierte Wiederherstellungsvorgänge. Weitere Informationen zum Abfragen und Stornieren Heatmap-basierter Wiederherstellungs- und Aktivierungsaufträge finden Sie unter [HCS-Cmdlets in Windows PowerShell](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps).

* **StorSimple-Diagnosetool:** In Update 4 wird ein StorSimple-Diagnosetool veröffentlicht, das eine einfache Diagnose und Behebung von Problemen im Zusammenhang mit der Integrität von System, Netzwerk, Leistung und Hardwarekomponenten ermöglicht. Dieses Tool wird über Windows PowerShell für StorSimple ausgeführt. Weitere Informationen finden Sie unter [Beheben von Problemen mit dem StorSimple-Diagnosetool](storsimple-8000-diagnostics.md).

* **UI-basiertes StorSimple-Migrationstool:** Vor dieser Version mussten die Benutzer aufgrund der Datenmigration der 5000-7000-Serie einen Teil des Migrationsworkflows mithilfe der Azure PowerShell-Schnittstelle ausführen. In dieser Version steht ein einfach zu bedienendes benutzeroberflächenbasiertes StorSimple-Migrationstool zur Unterstützung bereit, um denselben Workflow zu erleichtern. Dieses Tool lässt auch die Konsolidierung der Wiederherstellungsbuckets zu. 

* **Änderungen im Zusammenhang mit FIPS:** Ab dieser Version ist FIPS standardmäßig auf allen Geräten der StorSimple 8000-Serie für Microsoft Azure Government- und Azure Public Cloud-Konten aktiviert.

* **Aktualisieren von Änderungen:** In dieser Version wurden Fehler im Zusammenhang mit Aktualisierungsfehlern behoben.

* **Warnung bei Datenträgerfehlern:** Eine neue Warnung, die den Benutzer auf bevorstehende Datenträgerfehler hinweist, wurde in dieser Version hinzugefügt. Wenn diese Warnung auftritt, wenden Sie sich an den Microsoft-Support, damit ein Ersatzdatenträger bereitgestellt wird. Weitere Informationen finden Sie unter [Hardwarewarnungen auf dem StorSimple-Gerät](storsimple-8000-manage-alerts.md#hardware-alerts).

* **Änderungen am Controlleraustausch:** Ein Cmdlet, das dem Benutzer das Abfragen des Status des Controlleraustauschvorgangs ermöglicht, wurde in dieser Version hinzugefügt. Weitere Informationen finden Sie unter [HCS-Cmdlets in Windows PowerShell](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps).


## <a name="issues-fixed-in-update-4"></a>Behobene Probleme in Update 4

Die folgende Tabelle enthält eine Zusammenfassung der Probleme, die in Update 4 behoben wurden.    

| Nein | Funktion | Problem | Gilt für das physische Gerät | Gilt für das virtuelle Gerät |
| --- | --- | --- | --- | --- |
| 1 |Failover |In der vorherigen Version bestand nach dem Failover ein Problem im Zusammenhang mit der Bereinigung am Kundenstandort. Dieses Problem wurde in dieser Version behoben. |Ja |Ja |
| 2 |Lokale Volumes |In der vorherigen Version bestand ein Problem im Zusammenhang mit dem Erstellen von lokalen Volumes, das zu Fehlern beim Erstellen von Volumes führte. Dieses Problem gibt es in dieser Version nicht mehr. |Ja |Nein |
| 3 |Supportpaket |In der vorherigen Version bestanden Probleme im Zusammenhang mit dem Supportpaket, die zur Ausnahme „System.OutOfMemory“ oder anderen Fehlern führten, die wiederum Fehler beim Erstellen von Supportpaketen bedingten. Diese Fehler wurden in dieser Version behoben. |Ja |Ja |
| 4 |Überwachung |In der vorherigen Version bestand ein Problem im Zusammenhang mit der Überwachung von Diagrammen für lokale Volumes, deren Verbrauch in „EB“ gezeigt wurde. Dieser Fehler wurde in dieser Version behoben. |Ja |Ja |
| 5 |Migration |In der vorherigen Version bestanden mehrere Probleme im Zusammenhang mit der Zuverlässigkeit der Migration von Geräten der 5000-7000-Serie zu Geräten der 8000-Serie. Diese Probleme wurden in dieser Version behoben. |Ja |Ja |
| 6 |Aktualisieren |Wenn in vorherigen Releases ein Fehler beim Update aufgetreten ist, sind die Controller in den Wiederherstellungsmodus gewechselt. Daher konnte der Benutzer nicht mit dem Update fortfahren und musste sich an den Microsoft-Support wenden. <br> Dieses Verhalten wurde in diesem Release geändert. Wenn beim Benutzer ein Fehler beim Update auftritt, wenn beide Controller die gleiche Version ausführen (Update 4), wechseln die Controller nicht in den Wiederherstellungsmodus. Wenn beim Benutzer dieser Fehler auftritt, empfehlen wir eine Weile zu warten und das Update dann zu wiederholen. Die Wiederholung ist möglicherweise erfolgreich. Wenn das nicht erfolgreich ist sollte sich der Benutzer an den Microsoft-Support wenden. |Ja |Ja |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Bekannte Probleme in Update 4 aus vorherigen Versionen

Es gibt keine neuen bekannten Probleme in Update 4. Eine Liste der Probleme, die aus vorherigen Versionen in Update 4 übertragen wurden, finden Sie unter [Versionshinweise zu Update 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Updates des SAS-Controllers (Serial Attached SCSI) und der Firmware in Update 4

Diese Version enthält SAS-Controller, LSI-Treiber und Firmwareupdates. Weitere Informationen zum Installieren dieser Updates finden Sie auf Ihrem StorSimple-Gerät unter [Installieren von Update 4](./storsimple-8000-install-update-4.md).

## <a name="virtual-device-updates-in-update-4"></a>Updates von virtuellen Geräten in Update 4

Dieses Update gilt nicht für die StorSimple Cloud Appliance (auch bekannt als das virtuelle Gerät). Es müssen neue virtuelle Geräte erstellt werden. 

## <a name="next-step"></a>Nächster Schritt

Weitere Informationen zum [Installieren von Update 4](./storsimple-8000-install-update-4.md) auf Ihrem StorSimple-Gerät.