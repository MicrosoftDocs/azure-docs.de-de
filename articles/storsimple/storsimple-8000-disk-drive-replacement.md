---
title: Austauschen eines Festplattenlaufwerks in einem Gerät der StorSimple 8000-Serie | Microsoft-Dokumentation
description: Erläutert, wie ein Laufwerk in einem primären StorSimple- oder EBOD-Gehäuse ausgetauscht wird.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 8/25/2017
ms.author: alkohli
ms.openlocfilehash: a5a7a31c174271f963bb005f10b8376a59133d36
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014872"
---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>Austauschen eines Festplattenlaufwerks in einem Gerät der StorSimple 8000-Serie

## <a name="overview"></a>Übersicht
In diesem Tutorial wird erläutert, wie Sie ein schlecht funktionierendes oder fehlerhaftes Festplattenlaufwerk eines Microsoft Azure StorSimple-Geräts austauschen. Führen Sie zum Austauschen eines Festplattenlaufwerks die folgenden Schritte aus:

* Entriegeln der Zugangssperre
* Ausbauen des Laufwerks
* Einbauen des Austauschlaufwerks

> [!IMPORTANT]
> Überprüfen Sie vor dem Ausbauen und Austauschen eines Laufwerks die Sicherheitsinformationen unter [Austauschen von StorSimple-Hardwarekomponenten](storsimple-8000-hardware-component-replacement.md).
 

## <a name="disengage-the-antitamper-lock"></a>Entriegeln der Zugangssperre
In dieser Vorgehensweise wird erläutert, wie die Zugangssperren eines StorSimple-Geräts beim Austausch eines Laufwerks entriegelt und wieder verriegelt werden. Die Zugangssperren befinden sich in den Laufwerkstragegriffen und sind durch eine kleine Öffnung im Riegelteil des jeweiligen Griffs zugänglich. Laufwerke werden mit den Sperren in der verriegelten Position ausgeliefert.

#### <a name="to-unlock-the-antitamper-lock"></a>So entriegeln Sie eine Zugangssperre
1. Führen Sie den Sperrschlüssel (ein "manipulationssicherer" T10-Schraubendreher, der von Microsoft mitgeliefert wurde), vorsichtig in die Öffnung im Griff und in die zugehörige Vertiefung ein. 
   
   Ist die Zugangssperre aktiviert, ist in der Öffnung das rote Kennzeichen zu sehen.
  
    ![Verriegeltes Laufwerk](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **Abbildung 1** Verriegelte Zugangssperre
   
   | Bezeichnung | BESCHREIBUNG |
   |:--- |:--- |
   | 1 |Öffnung mit Kennzeichen |
   | 2 |Zugangssperre |
2. Drehen Sie den Schlüssel gegen den Uhrzeigersinn, bis das rote Kennzeichen nicht mehr in der Öffnung über dem Schlüssel zu sehen ist.
3. Entfernen Sie den Schlüssel.
   
    ![Entriegeltes Laufwerk](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **Abbildung 2** Entriegeltes Laufwerk
4. Das Laufwerk kann nun ausgebaut werden.

Führen Sie die Schritte in umgekehrter Reihenfolge aus, um die Zugangssperre zu verriegeln.

## <a name="remove-the-disk-drive"></a>Ausbauen des Laufwerks
Ihr StorSimple-Gerät unterstützt eine RAID-10-ähnliche Speicherplatzkonfiguration. Dies bedeutet, dass das Gerät mit einem einzelnen fehlerhaften SSD- (Solid State Drive) oder HDD-Laufwerk (Hard Disk Drive) normal funktionieren kann.

> [!IMPORTANT]
> * Falls es im System mehrere fehlerhafte Datenträger gibt, dürfen Sie immer nur jeweils eine SSD oder HDD aus dem System ausbauen. Andernfalls kann es zu Datenverlusten kommen.
> * Stellen Sie sicher, dass Sie eine Austausch-SSD in einem Einschubfach platzieren, das zuvor eine SSD enthalten hat. Auf ähnliche Weise setzen Sie eine Austauschfestplatte in ein Einschubfach ein, das zuvor eine HDD enthalten hat.
> * Im Azure-Portal sind Einschubfächer von 0 bis 11 nummeriert. Wenn das Portal also anzeigt, dass ein Datenträger in Einschubfach 2 ausgefallen ist, finden Sie diesen Datenträger im Gerät im dritten Einschubfach von links oben.
> 
> 

Laufwerke können bei laufendem Betrieb des Systems ausgebaut und ausgetauscht werden.

#### <a name="to-remove-a-drive"></a>So bauen Sie ein Laufwerk aus
1. Wechseln Sie zur Identifizierung des fehlerhaften Datenträgers im Azure-Portal zu **Einstellungen > Hardwareintegrität** für Ihr Gerät. Weil ein Datenträger im primären Gehäuse und/oder in einem EBOD-Gehäuse ausfallen kann (wenn Sie ein Modell des Typs 8600 verwenden), sehen Sie sich die Status der Datenträger unter **Gemeinsam genutzte Komponenten** und **Gemeinsam genutzte EBOD-Komponenten** an. Ein fehlerhafter Datenträger in einem der Gehäuse wird mit rotem Status angezeigt.
2. Suchen Sie auf der Vorderseite des primären Gehäuses oder des EBOD-Gehäuses nach den Laufwerken. 
3. Wenn der Datenträger nicht verriegelt ist, fahren Sie mit dem nächsten Schritt fort. Wenn der Datenträger verriegelt ist, entriegeln Sie ihn mithilfe des Verfahrens unter [Entriegeln der Zugangssperre](#disengage-the-antitamper-lock).
4. Drücken Sie den schwarzen Riegel am Laufwerksträgermodul, und ziehen Sie den Laufwerksträgergriff heraus und von der Vorderseite des Gehäuses fort.
   
    ![Freigeben des Laufwerkgriffs](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **Abbildung 3** Entriegeln des Laufwerkgriffs
5. Wenn der Laufwerksträgergriff vollständig geöffnet ist, ziehen Sie den Laufwerksträger aus dem Gehäuse heraus. 
   
    ![Herausziehen des Datenträgers aus dem Laufwerk](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **Abbildung 4** Herausziehen des Laufwerks

## <a name="install-the-replacement-disk-drive"></a>Einbauen des Austauschlaufwerks
Nachdem ein Laufwerk in einem StorSimple-Gerät ausgefallen ist und Sie es entfernt haben, gehen Sie wie folgt vor, um es durch ein neues Laufwerk zu ersetzen.

#### <a name="to-insert-a-drive"></a>So bauen Sie ein Laufwerk ein
1. Öffnen Sie den Laufwerksträgergriff vollständig, wie dies in der folgenden Abbildung zu sehen ist.
   
    ![Laufwerk mit ausgefahrenem Griff](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **Abbildung 5** Laufwerksträger mit geöffnetem Griff
2. Schieben Sie den Laufwerksträger vollständig in das Gehäuse ein.
   
    ![Einschieben des Datenträgers in den Laufwerksträger](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **Abbildung 6**  Einschieben des Laufwerkträgers in das Gehäuse
3. Nachdem der Laufwerksträger eingeschoben wurde, schließen Sie den Laufwerksträgergriff, indem Sie den Laufwerksträger weiter in das Gehäuse drücken, bis der Laufwerksträgergriff in der verriegelten Position einrastet.
4. Sichern Sie den Trägergriff mit dem Sperrschlüssel, der von Microsoft mitgeliefert wurde (manipulationssicherer Torx-Schraubendreher), in seiner Position, indem Sie die Sperrschraube eine Vierteldrehung im Uhrzeigersinn drehen.
5. Prüfen Sie, ob der Austausch erfolgreich war und das Laufwerk betriebsbereit ist. Wechseln Sie zum Azure-Portal, und navigieren Sie zu **Geräteeinstellungen** > **Hardwareintegrität**. Unter **Gemeinsam genutzte Komponenten** oder **Gemeinsam genutzte EBOD-Komponenten** muss der Laufwerksstatus grün angezeigt werden, was heißt, dass das Laufwerk betriebsbereit ist.

   
   > [!NOTE]
   > Es kann mehrere Stunden dauern, bis für ein Laufwerk nach einem Austausch der Status "Grün" angezeigt wird.
  
## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Austauschen von StorSimple-Hardwarekomponenten](storsimple-8000-hardware-component-replacement.md).

