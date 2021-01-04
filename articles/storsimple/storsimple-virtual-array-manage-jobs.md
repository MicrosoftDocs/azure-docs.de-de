---
title: Anzeigen und Verwalten von StorSimple Virtual Array-Aufträgen | Microsoft Docs
description: Beschreibt die Seite „Aufträge“ des StorSimple-Geräte-Manager-Diensts und wie Sie damit kürzlich durchgeführte und aktuelle Aufträge für das StorSimple Virtual Array nachverfolgen.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 31879821-b599-4609-a7f4-d4b0f658a933
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/11/2016
ms.author: alkohli
ms.openlocfilehash: d806d8a04dc1bd8547808d20c77bfec310f7dd06
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95992997"
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>Verwenden des StorSimple-Geräte-Manager-Diensts zum Anzeigen von Aufträgen für das StorSimple Virtual Array
## <a name="overview"></a>Übersicht
Das Blatt **Aufträge** enthält ein zentrales Portal zum Anzeigen und Verwalten von auf virtuellen Arrays gestarteten Aufträgen, die über eine Verbindung mit Ihrem StorSimple-Geräte-Manager-Dienst verfügen. Sie können aktive, abgeschlossene und fehlgeschlagene Aufträge für mehrere virtuelle Geräte anzeigen. Die jeweiligen Ergebnisse werden in einem Tabellenformat angezeigt.

![Blatt „Aufträge“](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

Sie können die Aufträge, an denen Sie interessiert sind, schnell finden, indem Sie nach folgenden Feldern filtern:

* **Zeitbereich** – Aufträge können entsprechend einem Datums- und Zeitbereich gefiltert werden.
* **Geräte** – Aufträge werden für ein bestimmtes Gerät ausgelöst, das mit Ihrem Dienst verbunden ist. Die gefilterten Aufträge werden dann basierend auf den folgenden Attributen in Tabellenform angezeigt:
  
  * **Name** – Der Auftragsname kann **Alle**, **Backup**, **Klonen**, **Failover**, **Updates herunterladen** oder **Updates installieren** lauten.
  * **Status** – Der Auftragsstatus kann **Alle**, **In Bearbeitung**, **Erfolgreich**, **Fehler** oder **Abgebrochen** lauten.
  * **Entität** – Die Aufträge können einem Volume, einer Freigabe oder einem Gerät zugeordnet sein.
  * **Gerät** – Der Name des Geräts, für den der Auftrag gestartet wurde.
  * **Gestartet am** – der Zeitpunkt, zu dem der Auftrag gestartet wurde.
  * **Dauer** – wie lange der Auftrag ausgeführt wurde
* **Status** – Sie können nach allen, aktiven, abgeschlossenen oder fehlgeschlagenen Aufträgen suchen.
* **Auftragstyp** – Der Auftragstyp kann „Alle“, „Backup“, „Wiederherstellung“, „Failover“, „Updates herunterladen“ oder „Updates installieren“ lauten.

Die Liste der Aufträge wird alle 30 Sekunden aktualisiert.

## <a name="view-job-details"></a>Anzeigen von Auftragsdetails
Führen Sie die folgenden Schritte aus, um die Details eines Auftrags anzuzeigen.

#### <a name="to-view-job-details"></a>So zeigen Sie Auftragsdetails an
1. Zeigen Sie auf dem Blatt **Aufträge** die für Sie interessanten Aufträge an, indem Sie eine Abfrage mit entsprechenden Filtern ausführen. Sie können nach abgeschlossenen oder aktiven Aufträgen suchen.
2. Wählen Sie in der tabellarischen Auftragsliste einen Auftrag aus.
   
    ![Blatt „Auftrag“](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. Klicken Sie unten auf der Seite auf **Details**.
4. Im Dialogfeld **Details** können Sie Status, Details und Zeitstatistiken anzeigen. Die folgende Abbildung zeigt ein Beispiel für das Dialogfeld **Auftragsdetails sichern** .
   
    ![Auftragsdetails](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Auftragsfehler, wenn der virtuelle Computer im Hypervisor angehalten wird
Wenn ein Auftrag im StorSimple Virtual Array ausgeführt wird und das Gerät (bereitgestellter virtueller Computer im Hypervisor) für mehr als 15 Minuten angehalten wurde, schlägt der Auftrag fehl. Dies ist darauf zurückzuführen, dass die Zeit Ihres virtuellen StorSimple-Arrays nicht mehr synchron mit der Microsoft Azure-Zeit ist. 

Die folgende Fehlermeldung wird angezeigt: „Your device time is out of sync with the Microsoft Azure time by more than 15 minutes. Ensure that the hypervisor and the device times are synchronized with an NTP server. Verify that there are no connectivity issues. To troubleshoot connectivity issues, run diagnostic tests from the local web UI of your virtual device.“ (Ihr Gerät ist seit über 15 Minuten nicht mehr mit der Microsoft Azure-Zeit synchronisiert. Stellen Sie sicher, dass der Hypervisor und die Gerätezeit mit dem NTP-Server synchronisiert sind. Sie können Verbindungsfehler beheben, indem Sie Diagnosetests von der lokalen Webbenutzeroberfläche Ihres virtuellen Geräts ausführen.)

Diese Fehler betreffen Backup-, Wiederherstellungs-, Aktualisierungs- und Failover-Aufträge. Wenn Ihr virtueller Computer in Hyper-V bereitgestellt wird, synchronisiert der Computer die Zeit letztendlich mit dem Hypervisor. Sobald dies geschieht, können Sie den Auftrag neu starten.

## <a name="next-steps"></a>Nächste Schritte
[Erfahren Sie, wie Sie Ihr StorSimple Virtual Array über die lokale Webbenutzeroberfläche verwalten](storsimple-ova-web-ui-admin.md).

