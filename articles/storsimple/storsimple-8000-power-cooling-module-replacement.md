---
title: Austauschen eines PCM in einem StorSimple-Gerät der Serie 8000 | Microsoft-Dokumentation
description: In diesem Thema wird erläutert, wie Sie ein oder beide Module für Stromversorgung und Kühlung (PCM) in einem StorSimple-Gerät austauschen oder ausbauen.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 42561570e24aec5edd33248ef1738e53175e480e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60632464"
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Austauschen eines Moduls für Stromversorgung und Kühlung eines StorSimple-Geräts
## <a name="overview"></a>Übersicht
Ein Modul für Stromversorgung und Kühlung (Power and Cooling Module, PCM) in einem Microsoft Azure StorSimple-Gerät umfasst eine Stromversorgung sowie Lüfter, die über das primäre und das EBOD-Gehäuse gesteuert werden. Es gibt nur ein PCM-Modell, das für das jeweilige Gehäuse zertifiziert ist. Das primäre Gehäuse ist für ein PCM mit 764 W und das EBOD-Gehäuse für ein PCM mit 580 W zertifiziert. Obwohl die PCMs für das primäre Gerät und das EBOD-Gerät unterschiedlich sind, ist die Vorgehensweise zum Austauschen gleich.

In diesem Tutorial werden folgende Punkte erläutert:

* Ausbauen eines PCM
* Einbauen eines Austausch-PCM

> [!IMPORTANT]
> Überprüfen Sie vor dem Ausbauen und Austauschen eines PCM die Sicherheitsinformationen unter [Austauschen von StorSimple-Hardwarekomponenten](storsimple-8000-hardware-component-replacement.md).


## <a name="before-you-replace-a-pcm"></a>Bevor Sie ein PCM austauschen
Achten Sie auf die folgenden wichtigen Punkte, bevor Sie ein PCM austauschen:

* Ist die Stromversorgung des PCM ausgefallen, belassen Sie das fehlerhafte Modul installiert, trennen aber das Netzkabel. Der Lüfter erhält nun Strom vom Gehäuse und bietet weiterhin eine ordnungsgemäße Kühlung. Wenn der Lüfter ausfällt, muss das PCM sofort ausgetauscht werden.
* Vor dem Ausbau des PCM unterbrechen Sie die Stromversorgung des PCM, indem Sie den Hauptschalter (sofern vorhanden) ausschalten oder das Netzkabel trennen. Dies ermöglicht eine Warnung an Ihr System, dass ein Ausschaltvorgang bevorsteht.
* Vergewissern Sie sich vor dem Austausch des fehlerhaften PCM, dass das andere PCM funktioniert, sodass der Systembetrieb aufrechterhalten wird. Ein fehlerhaftes PCM muss schnellstmöglich gegen ein voll funktionsfähiges PCM ausgetauscht werden.
* Der Austausch eines PCM dauert nur einige Minuten, muss aber, damit eine Überhitzung vermieden wird, binnen 10 Minuten ab Ausbau des ausgefallenen PCM abgeschlossen sein.
* Beachten Sie, dass die ab Werk gelieferten 764-W-Austausch-PCMs keinen Notfallakku enthalten. Sie müssen den Akku aus dem fehlerhaften PCM entfernen und in das Ersatzmodul einsetzen, bevor Sie den Austausch durchführen. Weitere Informationen finden Sie unter [Entfernen und Einsetzen eines Notfallakkus](storsimple-8000-battery-replacement.md).

## <a name="remove-a-pcm"></a>Ausbauen eines PCM
Gehen Sie entsprechend der folgenden Anleitung vor, wenn Sie ein Modul für Stromversorgung und Kühlung (Power and Cooling Module, PCM) aus einem Microsoft Azure StorSimple-Gerät ausbauen möchten.

> [!NOTE]
> Bevor Sie das jeweilige PCM ausbauen, sollten Sie sich vergewissern, dass Sie ein passendes Austauschmodul haben (764 W für das primäre Gehäuse oder 580 W für das EBOD-Gehäuse).

#### <a name="to-remove-a-pcm"></a>So bauen Sie ein PCM aus
1. Klicken Sie im klassischen Azure-Portal auf **Einstellungen > Überwachen > Hardwareintegrität**. Prüfen Sie den Status der PCM-Komponenten unter **Freigegebene Komponenten**, um zu ermitteln, welches PCM ausgefallen ist:
   
   * Ist eine Stromversorgung in PCM 0 ausgefallen, leuchtet die Status-LED für **Stromversorgung in PCM 0** rot.
   * Ist eine Stromversorgung in PCM 1 ausgefallen, leuchtet die Status-LED für **Stromversorgung in PCM 1** rot.
   * Ist ein Lüfter in PCM 1 ausgefallen, leuchtet die Status-LED von **Kühlung 0 für PCM 0** oder **Kühlung 1 für PCM 0** rot.
2. Suchen Sie auf der Rückseite des primären Gehäuses nach dem ausgefallenen PCM. Ist das Gerät ein Modell 8600, ermitteln Sie das primäre Gehäuse anhand der Systemidentifikationsnummer, die in der LED-Anzeige auf dem vorderen Bedienfeld angezeigt wird. Die auf dem primären Gehäuse angezeigte Standardgerätekennung lautet **00**, während die auf dem EBOD-Gehäuse angezeigte Standardgerätekennung **01** lautet. In der folgenden Abbildung und Tabelle wird das vordere Bedienfeld der LED-Anzeige erläutert.
   
    ![Gerätekennung auf dem Bedienfeld auf der Vorderseite](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **Abbildung 1** Vorderseite des Geräts  
   
   | Bezeichnung | BESCHREIBUNG |
   |:--- |:--- |
   | 1 |Taste zum Stummschalten |
   | 2 |Systemstromversorgung |
   | 3 |Modulfehler |
   | 4 |Logischer Fehler |
   | 5 |Anzeige der Gerätekennung |
3. Das ausgefallene PCM kann auch über die Überwachungs-LEDs auf der Rückseite des primären Gehäuses ermittelt werden. Sehen Sie sich die folgende Abbildung und Tabelle an, um zu verstehen, wie Sie anhand der LEDs das ausgefallene PCM finden. Beispiel: Wenn die LED für einen **Lüfterausfall** leuchtet, ist der Lüfter ausgefallen. Leuchtet die LED, die einen **Stromausfall** anzeigt, ist die Stromversorgung ausgefallen. 
   
    ![Rückwand des Geräts – PCM-Überwachungs-LEDs](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **Abbildung 2** Rückseite eines PCM mit Indikator-LEDs
   
   | Bezeichnung | BESCHREIBUNG |
   |:--- |:--- |
   | 1 |Stromausfall (Wechselstrom) |
   | 2 |Lüfterausfall |
   | 3 |Batterieausfall |
   | 4 |PCM OK |
   | 5 |Stromausfall (Gleichstrom) |
   | 6 |Batterie betriebsbereit |
4. Ermitteln Sie anhand der folgenden Abbildung der Rückseite des StorSimple-Geräts das ausgefallene PCM. PCM 0 befindet sich auf der linken und PCM 1 auf der rechten Seite. In der folgenden Tabelle sind die Module erläutert.
   
     ![Rückwand des Geräts – Module für primäre Gehäuse](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **Abbildung 3** Rückseite des Geräts mit Steckmodulen 
   
   | Bezeichnung | BESCHREIBUNG |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Controller 0 |
   | 4 |Controller 1 |
5. Schalten Sie das ausgefallene PCM aus, und trennen Sie das Netzkabel. Das PCM kann jetzt ausgebaut werden.
6. Fassen Sie den Riegel und die Seite des PCM-Bügels zwischen Ihrem Daumen und Zeigefinger, und drücken Sie beide zusammen, um den Bügel zu öffnen.
   
    ![Öffnen des PCM-Bügels](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **Abbildung 4** Öffnen des PCM-Bügels
7. Greifen Sie den Bügel, und entnehmen Sie das PCM.
   
    ![Ausbauen des PCM des Geräts](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **Abbildung 5** Entnehmen des PCM

## <a name="install-a-replacement-pcm"></a>Einbauen eines Austausch-PCM
Folgen Sie diesen Anweisungen, um ein PCM in Ihrem StorSimple-Gerät einzubauen. Stellen Sie sicher, dass Sie den Notfallakku eingesetzt haben, bevor Sie das Austausch-PCM einbauen (gilt nur für PCMs mit 764 W). Weitere Informationen finden Sie unter [Entfernen und Einsetzen eines Notfallakkus](storsimple-8000-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>So bauen Sie ein PCM ein
1. Vergewissern Sie sich, dass Sie das richtige Austausch-PCM für dieses Gehäuse haben. Im primären Gehäuse wird ein PCM mit 764 W und im EBOD-Gehäuse ein PCM mit 580 W verwendet. Versuchen Sie nicht, ein PCM mit 580 W im primären Gehäuse und ein PCM mit 764 W im EBOD-Gehäuse zu verwenden. In der folgenden Abbildung wird gezeigt, wo diese Information auf dem Typenschild zu finden ist, das auf dem PCM angebracht ist.
   
    ![Typenschild des PCM des Geräts](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **Abbildung 6** PCM-Typenschild
2. Überprüfen Sie das Gehäuse auf Beschädigungen, wobei Sie besonderes auf die Anschlüsse achten sollten. 
   
   > [!NOTE]
   > **Bauen Sie das Modul nicht ein, wenn einer der Anschlussstifte verbogen ist.**
   > 
   > 
3. Schieben Sie das Modul bei entriegeltem PCM-Bügel in das Gehäuse.
   
    ![Einbauen des PCM des Geräts](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **Abbildung 7** Einbauen des PCM
4. Verriegeln Sie den PCM-Bügel. Sie sollten ein Klicken hören, wenn der Bügelverschluss einrastet.
   
   > [!NOTE]
   > Wenn Sie prüfen möchten, ob die Anschlussstifte Kontakt haben, ziehen Sie vorsichtig am Bügel, ohne den Verschluss zu öffnen. Wenn das PCM herausgleitet, ist davon auszugehen, dass der Verschluss verriegelt wurde, bevor die Anschlüsse Kontakt hatten.
   
5. Verbinden Sie die Netzkabel mit der Stromquelle und dem PCM.
6. Sichern Sie die Zugentlastungspäckchen.
7. Schalten Sie das PCM ein.
8. Vergewissern Sie sich, dass der Austausch erfolgreich war: Navigieren Sie im Azure-Portal Ihres StorSimple-Geräte-Manager-Diensts zu Ihrem Gerät und dann zu **Einstellung > Überwachung > Hardwareintegrität**. Unter **Freigegebene Komponenten** muss die Statusanzeige des PCM grün leuchten.
   
   > [!NOTE]
   > Es kann einige Minuten dauern, bis das Austausch-PCM vollständig initialisiert ist.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Austauschen von StorSimple-Hardwarekomponenten](storsimple-8000-hardware-component-replacement.md).

