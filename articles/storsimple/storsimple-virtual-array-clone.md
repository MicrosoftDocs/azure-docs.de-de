---
title: Klonen einer StorSimple Virtual Array-Sicherung | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Sicherung klonen und eine Datei aus Ihrem StorSimple Virtual Array wiederherstellen können.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: af6e979c-55e3-477c-b53e-a76a697f80c9
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: b6cc89cb082cd0ed32abd88e3a6683c60a27ba90
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023039"
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>Klonen aus einer Sicherung des StorSimple Virtual Array

## <a name="overview"></a>Übersicht

In diesem Artikel wird Schritt für Schritt beschrieben, wie Sie das Klonen aus einem Sicherungssatz Ihrer Freigaben oder Volumes auf dem Microsoft Azure StorSimple Virtual Array durchführen. Die geklonte Sicherung wird verwendet, um eine gelöschte oder verlorene Datei wiederherzustellen. Dieser Artikel enthält auch ausführliche Schritte zur Wiederherstellung auf Elementebene auf Ihrem StorSimple Virtual Array, das als Dateiserver konfiguriert ist.

## <a name="clone-shares-from-a-backup-set"></a>Klonen von Freigaben aus einem Sicherungssatz

**Bevor Sie versuchen, die Freigaben zu klonen, sollten Sie sich vergewissern, dass auf dem Gerät ausreichend Speicherplatz zum Durchführen dieses Vorgangs vorhanden ist.** Führen Sie im [Azure-Portal](https://portal.azure.com/) die folgenden Schritte aus, um aus einer Sicherung zu klonen.

#### <a name="to-clone-a-share"></a>So klonen Sie eine Freigabe

1. Navigieren Sie zum Blatt **Geräte**. Wählen Sie Ihr Gerät aus, und klicken Sie darauf. Klicken Sie anschließend auf **Freigaben**. Wählen Sie die Freigabe aus, die Sie klonen möchten, und klicken Sie mit der rechten Maustaste auf die Freigabe, um das Kontextmenü aufzurufen. Wählen Sie **Klonen** aus.
   
   ![Klonen einer Sicherung](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. Klicken Sie auf dem Blatt **Klonen** auf **Backup > Auswahl** und führen Sie folgendes durch: 
   
   a.    Filtern Sie eine Sicherung auf diesem Gerät basierend auf dem Zeitbereich. Sie können zwischen **Letzte 7 Tage**, **Letzte 30 Tage** und **Letztes Jahr** wählen.
   
   b.    Wählen Sie in der Liste, in der die gefilterten Sicherungen angezeigt werden, eine Sicherung aus, aus der Sie klonen möchten.
   
   c.    Klicken Sie auf **OK**.
   
   ![Klonen einer Sicherung 2](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. Klicken Sie auf dem Blatt **Klonen** auf **Zieleinstellungen**, und führen Sie folgendes durch:
   
   a.    Geben Sie einen Freigabenamen an. Der Freigabename kann 3-127 Zeichen lang sein.
   
   b.    Geben Sie optional eine Beschreibung für die geklonte Freigabe an.
   
   c.    Der Typ der Freigabe, die Sie wiederherstellen möchten, kann nicht geändert werden. Eine mehrstufige Freigabe wird als mehrstufig geklont, und eine lokale Freigabe als lokale Freigabe.
   
   d.    Die Kapazität ist auf die Größe der Freigabe, aus der Sie klonen, begrenzt.
   
   e.    Weisen Sie die Administratoren für diese Freigabe zu. Sie können die Freigabeeigenschaften mit dem Datei-Explorer ändern, nachdem das Klonen abgeschlossen ist.
   
   f.    Klicken Sie auf **OK**.
   
   ![Klonen einer Sicherung 3](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. Klicken Sie auf **Klonen**, um einen Klonauftrag zu starten. Nachdem der Auftrag abgeschlossen ist beginnt der Klonvorgang, und Sie erhalten eine Benachrichtigung. Gehen Sie zum Überwachen des Fortschritts des Klonvorgangs zum Blatt **Aufträge**, und klicken Sie auf den Auftrag, um die Auftragsdetails anzuzeigen.
5. Nachdem der Klon erfolgreich erstellt wurde, navigieren Sie auf Ihrem Gerät zurück zum Blatt **Freigaben**.
6. Sie können nun die neue geklonte Freigabe in der Liste mit den Freigaben auf Ihrem Gerät anzeigen. Eine mehrstufige Freigabe wird auch als mehrstufig geklont, und eine lokale Freigabe als lokale Freigabe.
   
   ![Klonen einer Sicherung 4](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>Klonen von Volumes aus einem Sicherungssatz

Um aus einer Sicherung im Azure-Portal zu klonen, müssen Sie ähnliche Schritte ausführen wie beim Klonen von Freigaben. Beim Klonvorgang wird die Sicherung auf ein neues Volume desselben virtuellen Geräts geklont. Das Klonen auf ein anderes Gerät ist nicht möglich.

#### <a name="to-clone-a-volume"></a>So klonen Sie ein Volume

1. Navigieren Sie zum Blatt **Geräte**. Wählen Sie Ihr Gerät aus, und klicken Sie darauf. Klicken Sie anschließend auf **Volumes**. Wählen Sie das Volume aus, das Sie klonen möchten, und klicken Sie mit der rechten Maustaste auf das Volume, um das Kontextmenü aufzurufen. Wählen Sie **Klonen** aus.
   
   ![Volume klonen](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. Klicken Sie auf dem Blatt **Klonen** auf **Sicherung** und führen Sie folgendes durch: 
   
   a.    Filtern Sie eine Sicherung auf diesem Gerät basierend auf dem Zeitbereich. Sie können zwischen **Letzte 7 Tage**, **Letzte 30 Tage** und **Letztes Jahr** wählen. 
   
   b.    Wählen Sie in der Liste, in der die gefilterten Sicherungen angezeigt werden, eine Sicherung aus, aus der Sie klonen möchten.
   
   c.    Klicken Sie auf **OK**.
   
   ![Klonen eines Volumes 2](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. Klicken Sie auf dem Blatt **Klonen** auf **Zielvolumeinstellungen**, und führen Sie folgendes durch:
   
   a. Der Gerätename wird automatisch aufgefüllt.
   
   b. Geben Sie einen Volumenamen für das **geklonte Volume** an. Der Volumename kann 3 bis 127 Zeichen lang sein.
   
   c. Der Volumetyp wird automatisch auf das ursprüngliche Volume festgelegt. Ein mehrstufiges Volume wird als mehrstufig geklont, und eine lokales Volume als lokales Volume.
   
   d. Klicken Sie für **Verbundene Hosts** auf **Auswahl**.
   
   ![Klonen eines Volumes 3](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. Wählen Sie auf dem Blatt **Verbundene Hosts** aus einem vorhandenen ACR aus oder fügen Sie einen neuen ACR hinzu. Sie müssen einen ACR-Namen und einen Host-IQN angeben, um einen neuen ACR hinzuzufügen. Klicken Sie auf **Auswählen**.
   
   ![Klonen eines Volumes 4](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. Klicken Sie auf **Klonen**, um einen Klonauftrag zu starten.
   
   ![Klonen eines Volumes 5](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. Nachdem der Klonauftrag erstellt wurde, wird das Klonen gestartet. Sobald der Klon erstellt wurde, wird er auf dem Blatt „Volumes“ auf Ihrem Gerät angezeigt. Beachten Sie, dass ein mehrstufiges Volume als mehrstufiges Volume geklont wird, und ein lokales Volume als lokales Volume.
   
   ![Klonen eines Volumes 6](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. Sobald das Volume in der Liste der Volumes online angezeigt wird, ist das Volume für die Verwendung verfügbar. Aktualisieren Sie auf dem iSCSI-Initiatorhost die Liste der Ziele im Fenster mit den iSCSI-Initiatoreigenschaften. Ein neues Ziel, das den Namen des geklonten Volumes enthält, sollte in der Statusspalte als „inaktiv“ angezeigt werden.
8. Wählen Sie das Ziel aus, und klicken Sie auf **Verbinden**. Wenn der Initiator mit dem Ziel verbunden ist, sollte sich der Status in **Verbunden** ändern.
9. Im Fenster **Datenträgerverwaltung** werden die bereitgestellten Volumes wie in der folgenden Abbildung dargestellt angezeigt. Klicken Sie mit der rechten Maustaste auf das ermittelte Volume (klicken Sie auf den Datenträgernamen), und klicken Sie dann auf **Online**.

> [!IMPORTANT]
> Wenn Sie versuchen, ein Volume oder eine Freigabe aus einer Sicherung zu klonen, und der Klonauftrag fehlschlägt, wird möglicherweise dennoch ein Zielvolume bzw. eine -freigabe im Portal erstellt. Es ist wichtig, dass Sie dieses Zielvolume bzw. die -freigabe im Portal löschen, um zukünftige Probleme mit diesem Element zu minimieren.
> 
> 

## <a name="item-level-recovery-ilr"></a>Wiederherstellung auf Elementebene

Mit dieser Version wird die Wiederherstellung auf Elementebene (Item-Level Recovery, ILR) auf einem StorSimple Virtual Array eingeführt, das als Dateiserver konfiguriert ist. Mit diesem Feature können Sie die Dateien und Ordner einer Cloudsicherung für alle Freigaben des StorSimple-Geräts präzise wiederherstellen. Sie können gelöschte Dateien aus kürzlich erfolgten Sicherungen per Self-Service wiederherstellen.

Jede Freigabe verfügt über einen Ordner *.backups* , der die letzten Sicherungen enthält. Sie können zur gewünschten Sicherung navigieren, relevante Dateien und Ordner aus der Sicherung kopieren, und diese dann wiederherstellen. Durch diese Funktion ist es nicht mehr erforderlich, sich wegen der Wiederherstellung von Dateien aus Sicherungen an den Administrator zu wenden.

1. Wenn Sie die Wiederherstellung auf Elementebene durchführen, können Sie die Sicherungen per Datei-Explorer anzeigen. Klicken Sie auf die jeweilige Freigabe, für die Sie sich die Sicherungen ansehen möchten. Sie sehen den Ordner *.backups* , der in der Freigabe erstellt wurde und alle Sicherungen enthält. Erweitern Sie den Ordner *.backups* , um die Sicherungen anzuzeigen. Im Ordner wird die Explosionsansicht der gesamten Sicherungshierarchie angezeigt. Diese Ansicht wird bei Bedarf erstellt, was normalerweise nur wenige Sekunden dauert.
   
   Auf diese Weise werden die letzten fünf Sicherungen angezeigt, und können zur Wiederherstellung auf Elementebene verwendet werden. Zu den fünf letzten Sicherungen gehören sowohl die standardmäßig geplanten als auch die manuellen Sicherungen.
   
   * **Geplante Sicherungen** weisen die Bezeichnung &lt;Gerätename&gt;DailySchedule-YYYYMMDD-HHMMSS-UTC auf.
   * **Manuelle Sicherungen** weisen die Bezeichnung „Ad-hoc-YYYYMMDD-HHMMSS-UTC“ auf.
     
     ![Screenshot des Datei-Explorers mit Anzeige des Ordners „.backups“. Innerhalb dieses Ordners wird der Ordner für manuelle Sicherungen ausgewählt.](./media/storsimple-virtual-array-clone/image14.png)

2. Identifizieren Sie die Sicherung mit der letzten Version der gelöschten Datei. Der Ordnername enthält zwar in allen vorigen Fällen einen UTC-Zeitstempel, aber der Zeitpunkt der Ordnererstellung ist die eigentliche Geräteuhrzeit, zu der die Sicherung gestartet wurde. Verwenden Sie den Ordnerzeitstempel, um die Sicherungen zu finden und zu identifizieren.

3. Suchen Sie nach dem Ordner oder der Datei, den bzw. die Sie in der im vorherigen Schritt identifizierten Sicherung wiederherstellen möchten. Beachten Sie, dass Sie nur die Dateien oder Ordner anzeigen können, für die Sie Berechtigungen besitzen. Wenn Sie nicht auf bestimmte Dateien oder Ordner zugreifen können, wenden Sie sich an einen Freigabeadministrator. Der Administrator kann den Datei-Explorer verwenden, um Dateifreigaben zu bearbeiten, und Ihnen den Zugriff auf die bestimmte Datei oder den bestimmten Ordner ermöglichen. Es ist eine empfohlene bewährte Methode, dass es sich beim Freigabeadministrator um eine Benutzergruppe handeln sollte, nicht um einen einzelnen Benutzer.

4. Kopieren Sie die Datei bzw. den Ordner auf die entsprechende Freigabe auf dem StorSimple-Dateiserver.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr darüber, wie Sie das [StorSimple Virtual Array mit der lokalen Webbenutzeroberfläche verwalten](storsimple-ova-web-ui-admin.md).

