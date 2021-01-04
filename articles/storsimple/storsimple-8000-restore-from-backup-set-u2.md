---
title: Wiederherstellen eines Volumes aus einer Sicherung auf einem StorSimple-Gerät der Serie 8000 | Microsoft-Dokumentation
description: Erläutert, wie Sie den Sicherungskatalog des StorSimple-Geräte-Manager-Diensts zum Wiederherstellen eines StorSimple-Volumes aus einem Sicherungssatz verwenden.
services: storsimple
documentationcenter: NA
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.date: 07/15/2020
ms.author: alkohli
ms.openlocfilehash: ff70df225b5ed27960c96889d409d7005f0fbcc4
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94950728"
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Wiederherstellen eines StorSimple-Volumes aus einem Sicherungssatz

## <a name="overview"></a>Übersicht

In diesem Tutorial wird der Wiederherstellungsvorgang auf einem StorSimple-Gerät der Serie 8000 mit einem vorhandenen Sicherungssatz beschrieben. Verwenden Sie das Blatt **Sicherungskatalog** zum Wiederherstellen eines Volumes aus einer lokalen oder einer Cloudsicherung. Auf dem Blatt **Sicherungskatalog** werden alle Sicherungssätze angezeigt, die mithilfe manueller oder automatisierter Sicherungen erstellt wurden. Das Volume wird durch die Wiederherstellung aus dem Sicherungssatz sofort online geschaltet, während die Daten im Hintergrund heruntergeladen werden.

## <a name="before-you-restore"></a>Vor der Wiederherstellung

Bevor Sie eine Wiederherstellung starten, überprüfen Sie die folgenden Einschränkungen:

* **Das Volume muss offline geschaltet werden** . Schalten Sie das Volume sowohl auf dem Host als auch auf dem Gerät offline, bevor Sie die Wiederherstellung initiieren. Sie können das Volume auf dem Host online schalten, sobald das Volume auf dem Gerät online ist. (Sie müssen nicht warten, bis die Wiederherstellung abgeschlossen ist.) Anleitungen hierzu finden Sie unter [Offlineschalten von Volumes](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline).

* **Volumetyp nach der Wiederherstellung** : Gelöschte Volumes werden basierend auf dem Typ in der Momentaufnahme wiederhergestellt; das heißt, lokale Volumes werden als lokale Volumes wiederhergestellt, und mehrstufige Volumes werden als mehrstufige Volumes wiederhergestellt.

    Bei vorhandenen Volumes überschreibt der aktuelle Verwendungstyp des Volumes den Typ, der in der Momentaufnahme gespeichert ist. Wenn Sie beispielsweise ein Volume aus einer Momentaufnahme wiederherstellen, die erstellt wurde, als das Volume mehrstufig war, und der Volumetyp jetzt (aufgrund eines Konvertierungsvorgangs) lokal lautet, wird das Volume als lokales Volume wiederhergestellt. In gleicher Weise gilt: Wenn ein vorhandenes lokales Volume erweitert wurde und anschließend aus einer älteren Momentaufnahme, bei der das Volume kleiner war, wiederhergestellt wird, behält das wiederhergestellte Volume die aktuelle, erweiterte Größe bei.

    Sie können ein Volume nicht von einem mehrstufigen Volume in ein lokales Volume oder von einem lokalen Volume in ein mehrstufiges Volume konvertieren, während das Volume wiederhergestellt wird. Warten Sie, bis die Wiederherstellung abgeschlossen ist. Anschließend können Sie das Volume in einen anderen Typ konvertieren. Informationen zum Konvertieren eines Volumes finden Sie unter [Ändern des Volumetyps](storsimple-8000-manage-volumes-u2.md#change-the-volume-type). 

* **Die Größe des Volumes spiegelt sich im wiederhergestellten Volume wider:** Dies ist ein wichtiger Aspekt, wenn Sie ein gelöschtes lokales Volume wiederherstellen (weil lokale Volumes vollständig bereitgestellt werden). Stellen Sie sicher, dass Sie über genügend Speicherplatz verfügen, bevor Sie versuchen, ein gelöschtes lokales Volume wiederherzustellen.

* **Ein Volume kann während der Wiederherstellung nicht erweitert werden.** Warten Sie, bis die Wiederherstellung abgeschlossen ist, bevor Sie versuchen, das Volume zu erweitern. Informationen zum Erweitern eines Datenträgers finden Sie unter [Ändern eines Volumes](storsimple-8000-manage-volumes-u2.md#modify-a-volume).

* **Sie können eine Sicherung ausführen, während Sie ein lokales Volume wiederherstellen.** Vorgehensweisen finden Sie unter [Verwalten von Sicherungsrichtlinien mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-8000-manage-backup-policies-u2.md).

* **Sie können einen Wiederherstellungsvorgang abbrechen.** Wenn Sie den Wiederherstellungsauftrag abbrechen, wird das Volume in den Zustand zurückversetzt, in dem es sich vor dem Start des Wiederherstellungsvorgangs befand. Verfahren hierzu finden Sie unter [Abbrechen eines Auftrags](storsimple-8000-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Funktionsweise der Wiederherstellung

Für Geräte, auf denen Update 4 oder höher ausgeführt wird, wird eine Heatmap-basierte Wiederherstellung implementiert. Wenn die Hostanforderungen für den Zugriff auf Daten das Gerät erreichen, werden diese Anforderungen erfasst, und eine Heatmap wird erstellt. Eine hohe Anforderungsrate führt zu Datenblöcken mit höheren Heat-Werten, eine niedrigere Anforderungsrate dagegen zu Datenblöcken mit niedrigeren Heat-Werten. Sie müssen mindestens zweimal auf die Daten zugreifen, damit sie als _hot_ markiert werden. Eine Datei, die geändert wird, wird auch als _hot_ markiert. Nach dem Initiieren der Wiederherstellung erfolgt die proaktive Aktualisierung der Daten basierend auf der Heatmap. Für Versionen vor Update 4 wurden die Daten während der Wiederherstellung nur basierend auf dem Zugriff heruntergeladen.

Die folgenden Einschränkungen gelten für Wiederherstellungen auf Basis von Heatmaps:

* Die Heatmap-Nachverfolgung ist nur für mehrstufige Volumes aktiviert. Lokale Volumes werden nicht unterstützt.

* Beim Klonen eines Volumes auf einem anderen Gerät wird die Heatmap-basierte Wiederherstellung nicht unterstützt. 

* Wenn bei einer direkten Wiederherstellung auf dem Gerät eine lokale Momentaufnahme für das wiederherzustellende Volume vorhanden ist, wird keine Aktivierung durchgeführt (da die Daten bereits lokal verfügbar sind). 

* Standardmäßig werden bei der Wiederherstellung die Aktivierungsaufträge initiiert, mit denen Daten basierend auf der Heatmap proaktiv aktiviert werden. 

In Update 4 können Windows PowerShell-Cmdlets zum Abfragen der ausgeführten Aktivierungsaufträge, zum Abbrechen eines Aktivierungsauftrags und zum Abrufen des Status des Aktivierungsvorgangs verwendet werden.

* `Get-HcsRehydrationJob`: Mit diesem Cmdlet wird der Status des Aktivierungsauftrags abgerufen. Für ein Volume wird jeweils ein Aktivierungsauftrag ausgelöst.

* `Set-HcsRehydrationJob`: Mit diesem Cmdlet können Sie den Aktivierungsauftrag unterbrechen, beenden und wiederaufnehmen, wenn die Aktivierung ausgeführt wird.

Weitere Informationen zu den Cmdlets für die Aktivierung finden Sie in der [Cmdlet-Referenz für Windows PowerShell für StorSimple](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps).

Bei der automatischen Aktivierung wird normalerweise eine höhere vorübergehende Leseleistung erwartet. Die tatsächlichen Optimierungen hängen von verschiedenen Faktoren wie dem Zugriffsmuster, der Datenänderungsrate und dem Datentyp ab. 

Sie können einen Aktivierungsauftrag mit dem PowerShell-Cmdlet abbrechen. Wenn Sie Aktivierungsaufträge für alle zukünftigen Wiederherstellungen dauerhaft deaktivieren möchten, [wenden Sie sich an den Microsoft-Support](storsimple-8000-contact-microsoft-support.md).

## <a name="how-to-use-the-backup-catalog"></a>So verwenden Sie den Sicherungskatalog

Das Blatt **Sicherungskatalog** bietet eine Abfrage, mit der Sie die Auswahl der Sicherungssätze einschränken können. Sie können die abgerufenen Sicherungssätze anhand der folgenden Parameter filtern:

* **Zeitbereich**: der Datums- und Uhrzeitbereich, in dem die Sicherung erstellt wurde
* **Gerät** : das Gerät, auf dem der Sicherungssatz erstellt wurde.
* **Sicherungsrichtlinie** oder **Volume**: die Sicherungsrichtlinie oder das Volume, der oder dem dieser Sicherungssatz zugeordnet ist.

Die gefilterten Sicherungssätze werden dann basierend auf den folgenden Attributen in Tabellenform angezeigt:

* **Name** – der Name der Sicherungsrichtlinie oder des Volumes, der oder dem dieser Sicherungssatz zugeordnet ist.
* **Typ** : Sicherungssätze können lokale Momentaufnahmen oder Cloudmomentaufnahmen sein. Eine lokale Momentaufnahme ist eine Sicherung aller Volumedaten, die auf dem lokalen Gerät gespeichert ist, während die Sicherung von Volumedaten in der Cloud als Cloudmomentaufnahme bezeichnet wird. Lokale Momentaufnahmen bieten schnelleren Zugriff, während Cloudmomentaufnahmen für Datenstabilität ausgewählt werden.
* **Größe** : die tatsächliche Größe des Sicherungssatzes.
* **Erstellt am** – das Datum und die Uhrzeit der Erstellung der Sicherungen. 
* **Volumes**: die Anzahl der Volumes, die dem Sicherungssatz zugeordnet sind
* **Initiiert**: Die Sicherungen können automatisch nach einem Zeitplan oder manuell durch einen Benutzer initiiert werden. (Sie können eine Sicherungsrichtlinie verwenden, um Sicherungen zu planen. Es ist aber auch möglich, mithilfe der Option **Sicherung erstellen** eine interaktive, bedarfsgesteuerte Sicherung durchzuführen.)

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>So stellen Sie Ihr StorSimple-Volume aus einer Sicherung wieder her

Sie können Ihr StorSimple-Volume auf dem Blatt **Sicherungskatalog** aus einer bestimmten Sicherung wiederherstellen. Beachten Sie dabei, dass das Volume durch die Wiederherstellung auf den Zustand zum Zeitpunkt der Sicherung zurückgesetzt wird. Alle Daten, die nach dem Sicherungsvorgang hinzugefügt wurden, gehen verloren.

> [!WARNING]
> Beim Wiederherstellen aus einer Sicherung werden die vorhandenen Volumes durch die Sicherung ersetzt. Dadurch können Daten verloren gehen, die nach dem Erstellen der Sicherung geschrieben wurden.


### <a name="to-restore-your-volume"></a>So stellen Sie Ihr Volume wieder her
1. Navigieren Sie zum StorSimple-Geräte-Manager-Dienst, und klicken Sie dann auf **Sicherungskatalog**.

2. Wählen Sie wie folgt einen Sicherungssatz aus:
   
   1. Geben Sie den Zeitraum an.
   2. Wählen Sie das entsprechende Gerät aus.
   3. Wählen Sie in der Dropdownliste das Volume oder die Sicherungsrichtlinie für die gewünschte Sicherung aus.
   4. Klicken Sie auf **Anwenden**, um diese Abfrage durchzuführen.

      Die dem ausgewählten Volume oder der Sicherungsrichtlinie zugeordneten Sicherungen sollten in der Liste der Sicherungssätze angezeigt werden.
   
      ![Liste der Sicherungssätze](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Erweitern Sie einen Sicherungssatz, um die zugehörigen Volumes anzuzeigen. Diese Volumes müssen auf dem Host und dem Gerät offline geschaltet werden, bevor sie wiederhergestellt werden können. Greifen Sie auf dem Blatt **Volumes** auf Ihr Gerät zu, und führen Sie dann die Schritte in [Offlineschalten eines Volumes](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) durch, um diese offline zu schalten.
   
   > [!IMPORTANT]
   > Vergewissern Sie sich, dass die Volumes auf dem Host offline sind, bevor Sie diese auf dem Gerät offline schalten. Wenn Sie die Volumes auf dem Host nicht offline schalten, kann es zur Beschädigung von Daten kommen.
   
4. Navigieren Sie zurück zur Registerkarte **Sicherungskatalog** , und wählen Sie einen Sicherungssatz aus. Klicken Sie mit der rechten Maustaste, und wählen Sie im Kontextmenü **Wiederherstellen** aus.

    ![Liste der Sicherungssätze 2](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. Sie werden aufgefordert, diesen Schritt zu bestätigen. Überprüfen Sie die Wiederherstellungsinformationen, und aktivieren Sie dann das Kontrollkästchen zur Bestätigung.
   
    ![Bestätigungsseite](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7. Klicken Sie auf **Wiederherstellen**. Damit initiieren Sie einen Wiederherstellungsauftrag, den Sie auf der Seite **Aufträge** anzeigen können.

   ![Bestätigungsseite 2](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. Überprüfen Sie, nachdem die Wiederherstellung abgeschlossen ist, ob die Inhalte der Volumes durch die aus der Sicherung ersetzt wurden.


## <a name="if-the-restore-fails"></a>Bei einem Wiederherstellungsfehler

Sie erhalten eine Warnung, wenn der Wiederherstellungsvorgang aus irgendeinem Grund nicht durchgeführt werden kann. Aktualisieren Sie in diesem Fall die Sicherungsliste, um zu überprüfen, ob die Sicherung noch gültig ist. Wenn die Sicherung gültig ist und die Wiederherstellung aus der Cloud erfolgt, wird der Fehler möglicherweise durch Verbindungsprobleme verursacht.

Um den Wiederherstellungsvorgang erfolgreich abzuschließen, schalten Sie das Volume auf dem Host offline, und wiederholen Sie den Wiederherstellungsvorgang. Beachten Sie, dass Änderungen an den Volumedaten, die während des Wiederherstellungsvorgangs  ausgeführt wurden, verloren gehen.

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie, wie Sie [StorSimple-Volumes verwalten](storsimple-8000-manage-volumes-u2.md).
* Erfahren Sie, wie Sie [Ihr StorSimple-Gerät mithilfe des StorSimple-Geräte-Manager-Diensts verwalten](storsimple-8000-manager-service-administration.md).