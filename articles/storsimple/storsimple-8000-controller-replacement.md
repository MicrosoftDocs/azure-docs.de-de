---
title: Austauschen von Gerätecontrollern für Geräte der StorSimple 8000-Serie | Microsoft-Dokumentation
description: In diesem Thema wird erläutert, wie Sie ein oder beide Controllermodule in einem Gerät der StorSimple 8000-Serie austauschen oder entfernen.
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
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: dd2f6fcc9b2f5d716566e91e89487969613d1005
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61482869"
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Ersetzen des Controllermoduls auf dem StorSimple-Gerät
## <a name="overview"></a>Übersicht
In diesem Tutorial wird erläutert, wie Sie ein oder beide Controllermodule in einem StorSimple-Gerät austauschen oder entfernen. Darüber hinaus wird die zugrunde liegende Logik für die Austauschszenarios bei Einzel- oder Dualcontrollern erläutert.

> [!NOTE]
> Vor der Durchführung des Controlleraustauschs empfehlen wir, die Controllerfirmware auf die neueste Version zu aktualisieren.
> 
> Um Schäden an Ihrem StorSimple-Gerät zu verhindern, werfen Sie den Controller nicht aus, bis die LEDs einen der folgenden Status anzeigen:
> 
> * Alle Leuchten sind AUS.
> * LED 3, ![grünes Häkchensymbol](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png) und ![rotes Kreuzsymbol](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) blinken, und LED 0 und LED 7 **LEUCHTEN**.


In der folgenden Tabelle werden die unterstützten Controlleraustauschszenarios dargestellt.

| Fall | Austauschszenario | Anwendbare Verfahren |
|:--- |:--- |:--- |
| 1 |Ein Domänencontroller ist in einem fehlerhaften Zustand, der andere Controller funktioniert fehlerfrei und ist aktiv. |[Austauschen eines einzelnen Controllers](#replace-a-single-controller). Es werden sowohl die [Logik hinter dem Austauschen eines einzelnen Controllers](#single-controller-replacement-logic) als auch die [Schritte für den Austausch](#single-controller-replacement-steps) beschrieben. |
| 2 |Beide Controller sind fehlerhaft und müssen ausgetauscht werden. Das Gehäuse sowie die Laufwerke und Laufwerkgehäuse sind fehlerfrei. |[Austauschen zweier Controller](#replace-both-controllers). Es werden sowohl die [Logik hinter dem Austauschen zweier Controller](#dual-controller-replacement-logic) als auch die [Schritte für den Austausch](#dual-controller-replacement-steps) beschrieben. |
| 3 |Die Controller aus dem gleichen Gerät oder aus verschiedenen Geräten werden ausgetauscht. Das Gehäuse, die Laufwerke und die Laufwerkgehäuse sind fehlerfrei. |Es wird eine Warnung bezüglich des falschen Steckplatzes angezeigt. |
| 4 |Ein Domänencontroller ist nicht vorhanden, und der andere Controller ist fehlerhaft. |[Austauschen zweier Controller](#replace-both-controllers). Es werden sowohl die [Logik hinter dem Austauschen zweier Controller](#dual-controller-replacement-logic) als auch die [Schritte für den Austausch](#dual-controller-replacement-steps) beschrieben. |
| 5 |Ein oder beide Domänencontroller sind ausgefallen. Auf das Gerät kann nicht über die serielle Konsole oder Windows PowerShell-Remoteverbindung zugegriffen werden. |[Wenden Sie sich an den Microsoft Support](storsimple-8000-contact-microsoft-support.md) , um Anweisungen zum manuellen Controlleraustausch zu erhalten. |
| 6 |Die Controller haben unterschiedliche Buildversionen. Folgendes kommt als Ursache infrage:<ul><li>Die Controller haben unterschiedliche Softwareversionen.</li><li>Die Controller haben unterschiedliche Firmwareversionen.</li></ul> |Wenn sich die Controller-Softwareversionen unterscheiden, erkennt dies die Austauschlogik und aktualisiert die Software auf dem Ersatzcontroller.<br><br>Wenn sich die Firmwareversionen der Controller unterscheiden und die alte Firmwareversion **nicht** automatisch aktualisiert werden kann, wird im Azure-Portal eine Warnung angezeigt. Sie sollten nach Updates suchen und die Firmwareupdates installieren.</br></br>Wenn sich die Controller-Firmwareversionen unterscheiden und die alte Firmwareversion automatisch aktualisiert werden kann, erkennt dies die Ersatzcontrollerlogik, und nach dem Start des Controllers wird die Firmware automatisch aktualisiert. |

Sie müssen ein Controllermodul entfernen, wenn es einen Fehler verursacht hat. Ein oder beide Controllermodule können Fehler aufweisen. Dies kann den Austausch eines einzelnen oder beider Controller zur Folge haben. Beschreibungen der Austauschvorgehensweisen und der jeweils zugehörigen Logik finden Sie in den folgenden Abschnitten:

* [Austauschen eines einzelnen Controllers](#replace-a-single-controller)
* [Austauschen beider Controller](#replace-both-controllers)
* [Entfernen eines Controllers](#remove-a-controller)
* [Einsetzen eines Controllers](#insert-a-controller)
* [Identifizieren des aktiven Controllers im Gerät](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> Überprüfen Sie vor dem Entfernen und Austauschen eines Controllers die Sicherheitsinformationen unter [Austauschen von StorSimple-Hardwarekomponenten](storsimple-8000-hardware-component-replacement.md).
> 
> 

## <a name="replace-a-single-controller"></a>Austauschen eines einzelnen Controllers
Wenn einer der beiden Controller auf dem Microsoft Azure StorSimple-Gerät fehlerhaft ist, nicht funktioniert oder fehlt, müssen Sie einen einzelnen Controller ersetzen.

### <a name="single-controller-replacement-logic"></a>Logik beim Austauschen eines einzelnen Controllers
Beim Austausch eines einzelnen Controllers sollten Sie zuerst den fehlgeschlagenen Controller entfernen. (Der verbleibende Controller im Gerät ist der aktive Controller.) Wenn Sie den Ersatzcontroller einsetzen, werden folgende Aktionen ausgeführt:

1. Der Ersatzcontroller beginnt sofort mit der Kommunikation mit dem StorSimple-Gerät.
2. Eine Momentaufnahme der virtuellen Festplatte (VHD) für den aktiven Controller wird auf den Ersatzcontroller kopiert.
3. Die Momentaufnahme wird so angepasst, dass der Ersatzcontroller beim Starten von dieser VHD als Standbycontroller erkannt wird.
4. Wenn die Änderungen abgeschlossen sind, startet der Ersatzcontroller als Standbycontroller.
5. Wenn beide Controller ausgeführt werden, wird der Cluster online geschaltet.

### <a name="single-controller-replacement-steps"></a>Schritte zum Austauschen eines einzelnen Controllers
Führen Sie folgende Schritte aus, wenn einer der Controller in Ihrem Microsoft Azure StorSimple-Gerät einen Fehler verursacht. (Der andere Controller muss aktiv sein und ausgeführt werden. Wenn beide Controller ausfallen oder Fehlfunktionen aufweisen, führen Sie die [Schritte zum Austauschen zweier Controller](#dual-controller-replacement-steps)aus.)

> [!NOTE]
> Der Neustart des Controllers bis zum vollständigen Abschluss des Austauschverfahrens kann 30 bis 45 Minuten dauern. Die Zeit für die gesamte Prozedur beträgt einschließlich der Installation der Kabel ungefähr 2 Stunden.


#### <a name="to-remove-a-single-failed-controller-module"></a>So entfernen Sie ein einzelnes fehlerhaftes Controllermodul
1. Wechseln Sie im Azure-Portal zum StorSimple-Geräte-Manager-Dienst, und klicken Sie auf **Geräte** und dann auf den Namen des Geräts, das Sie überwachen möchten.
2. Wechseln Sie zu **Überwachung > Hardwareintegrität**. Der Status von Controller 0 oder von Controller 1 wird rot dargestellt. Dies zeigt einen Fehler an.
   
   > [!NOTE]
   > Der fehlerhafte Controller beim Austausch eines einzelnen Controllers ist immer der Standbycontroller.
   
3. Verwenden Sie Abbildung 1 und die folgende Tabelle zum Feststellen des fehlerhaften Controllermoduls.
   
    ![Rückwand des Geräts – Module für primäre Gehäuse](./media/storsimple-controller-replacement/IC740994.png)
   
    **Abbildung 1:** Rückseite des StorSimple-Geräts
   
   | Bezeichnung | BESCHREIBUNG |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Controller 0 |
   | 4 |Controller 1 |
4. Entfernen Sie alle verbundenen Netzwerkkabel von den Datenanschlüssen am fehlerhaften Controller. Wenn Sie ein 8600-Modell verwenden, entfernen Sie auch die SAS-Kabel, die den Controller mit dem EBOD-Controller verbinden.
5. Führen Sie die Schritte unter [Entfernen eines Controllers](#remove-a-controller) aus, um den fehlerhaften Controller zu entfernen.
6. Installieren Sie das Ersatzteil im selben Steckplatz, aus dem der fehlerhafte Controller entfernt wurde. Dadurch wird die Logik zum Austauschen eines einzelnen Controllers ausgelöst. Weitere Informationen finden Sie unter [Logik beim Austauschen eines einzelnen Controllers](#single-controller-replacement-logic).
7. Während die Logik zum Austauschen einzelner Controller im Hintergrund ausgeführt wird, schließen Sie die Kabel wieder an. Achten Sie darauf, alle Kabel genau auf die gleiche Weise zu verbinden wie vor dem Austausch.
8. Überprüfen Sie nach dem Neustart des Controllers den **Controllerstatus** und **Clusterstatus** im Azure-Portal. Stellen Sie sicher, dass sich der Controller wieder in einwandfreiem Zustand und im Standbymodus befindet.

> [!NOTE]
> Wenn Sie das Gerät über die serielle Konsole überwachen, können mehrere Neustarts durchgeführt werden, während der Controller aus dem Austauschverfahren wiederhergestellt wird. Wenn das Menü der seriellen Konsole angezeigt wird, wissen Sie, dass der Austausch abgeschlossen ist. Wenn das Menü nicht innerhalb von zwei Stunden nach Beginn des Controlleraustauschs angezeigt wird, [wenden Sie sich an den Microsoft Support Service](storsimple-8000-contact-microsoft-support.md).
>
> Ab Update 4 können Sie auch das Cmdlet `Get-HCSControllerReplacementStatus` in der Windows PowerShell-Schnittstelle des Geräts verwenden, um den Status des Austauschvorgangs der Controller zu überwachen.
> 

## <a name="replace-both-controllers"></a>Austauschen beider Controller
Wenn beide Controller auf dem Microsoft Azure StorSimple-Gerät fehlerhaft sind, nicht funktionieren oder fehlen, müssen Sie beide Controller ersetzen. 

### <a name="dual-controller-replacement-logic"></a>Logik beim Austauschen zweier Controller
Beim Austausch zweier Controller entfernen Sie zuerst beide fehlerhaften Controller und ersetzen diese dann. Wenn die zwei Ersatzcontroller eingesetzt wurden, werden die folgenden Aktionen ausgeführt:

1. Der Ersatzcontroller in Steckplatz 0 überprüft Folgendes:
   
   1. Wird die aktuelle Version von Firmware und Software verwendet?
   2. Ist er Teil des Clusters?
   3. Wird der Peercontroller ausgeführt, und befindet er sich in einem Cluster?
      
      Wenn keine dieser Bedingungen erfüllt ist, sucht der Controller nach der letzten täglichen Sicherung (auf Laufwerk S: unter **nonDOMstorage** ). Der Controller kopiert die aktuellste Momentaufnahme der virtuellen Festplatte aus der Sicherung.
2. Der Controller in Steckplatz 0 verwendet die Momentaufnahme für ein eigenes Image.
3. In der Zwischenzeit wartet der Controller in Steckplatz 1 darauf, dass Controller 0 die Imageerstellung abschließt und startet.
4. Nach dem Start von Controller 0 erkennt Controller 1 den von Controller 0 erstellten Cluster, wodurch die Logik zum Austausch eines einzelnen Controllers ausgelöst wird. Weitere Informationen finden Sie unter [Logik beim Austauschen eines einzelnen Controllers](#single-controller-replacement-logic).
5. Anschließend werden beide Controller ausgeführt, und der Cluster wird online geschaltet.

> [!IMPORTANT]
> Nach dem Austausch von zwei Controllern ist es sehr wichtig, dass Sie eine manuelle Sicherung des Geräts erstellen, sobald das StorSimple-Gerät konfiguriert wurde. Tägliche Gerätekonfigurationssicherungen werden erst nach 24 Stunden ausgelöst. Wenden Sie sich an den [Microsoft Support](storsimple-8000-contact-microsoft-support.md) , um eine manuelle Sicherung Ihres Geräts vorzunehmen.


### <a name="dual-controller-replacement-steps"></a>Schritte zum Austauschen zweier Controller
Dieser Workflow ist erforderlich, wenn beide Controller in Ihrem Microsoft Azure StorSimple-Gerät fehlerhaft sind. Dies könnte in einem Rechenzentrum passieren, in dem das Kühlsystem nicht mehr funktioniert, sodass innerhalb kurzer Zeit beide Controller fehlschlagen. Je nachdem, ob das StorSimple-Gerät ein- oder ausgeschaltet ist und ob Modell 8600 oder Modell 8100 verwendet wird, sind unterschiedliche Schritte erforderlich.

> [!IMPORTANT]
> Der Neustart des Controllers bis zum vollständigen Abschluss des Austauschverfahrens für zwei Controller kann 45 Minuten bis 1 Stunde dauern. Die Zeit für die gesamte Prozedur beträgt einschließlich der Installation der Kabel ungefähr 2,5 Stunden.

#### <a name="to-replace-both-controller-modules"></a>So tauschen Sie beide Controllermodule aus
1. Wenn das Gerät ausgeschaltet ist, überspringen Sie diesen Schritt und fahren mit dem nächsten Schritt fort. Wenn das Gerät eingeschaltet ist, schalten Sie es aus.
   
   1. Bei Verwendung eines Modells 8600 deaktivieren Sie zunächst das primäre Gehäuse und dann das EBOD-Gehäuse.
   2. Warten Sie, bis das Gerät vollständig heruntergefahren wurde. Alle LEDs an der Rückseite des Geräts sind deaktiviert.
2. Entfernen Sie alle Netzwerkkabel, die mit den Datenanschlüssen verbunden sind. Wenn Sie ein Modell 8600 verwenden, entfernen Sie auch die SAS-Kabel, die das primäre Gehäuse mit dem EBOD-Gehäuse verbinden.
3. Entfernen Sie beide Controller aus dem StorSimple-Gerät. Weitere Informationen finden Sie unter [Entfernen eines Controllers](#remove-a-controller).
4. Tauschen Sie zuerst Controller 0 aus, und setzen Sie dann Controller 1 ein. Weitere Informationen finden Sie unter [Einsetzen eines Controllers](#insert-a-controller). Dadurch wird die Logik zum Austauschen zweier Controller ausgelöst. Weitere Informationen finden Sie unter [Logik beim Austauschen zweier Controller](#dual-controller-replacement-logic).
5. Während die Logik zum Austauschen im Hintergrund ausgeführt wird, schließen Sie die Kabel wieder an. Achten Sie darauf, alle Kabel genau auf die gleiche Weise zu verbinden wie vor dem Austausch. Ausführliche Anleitungen für Ihr Modell finden Sie im Abschnitt zur Geräteverkabelung unter [Installieren des StorSimple 8100-Geräts](storsimple-8100-hardware-installation.md) oder [Installieren des StorSimple 8600-Geräts](storsimple-8600-hardware-installation.md).
6. Schalten Sie das StorSimple-Geräte ein. Wenn Sie ein Modell 8600 verwenden:
   
   1. Stellen Sie sicher, dass das EBOD-Gehäuse zuerst eingeschaltet wird.
   2. Warten Sie, bis das EBOD-Gehäuse ausgeführt wird.
   3. Schalten Sie das primäre Gehäuse ein.
   4. Nachdem der erste Controller neu gestartet wurde und sich in einem ordnungsgemäßen Zustand befindet, wird das System ausgeführt.
      
      > [!NOTE]
      > Wenn Sie das Gerät über die serielle Konsole überwachen, können mehrere Neustarts durchgeführt werden, während der Controller aus dem Austauschverfahren wiederhergestellt wird. Wenn das Menü der seriellen Konsole angezeigt wird, wissen Sie, dass der Austausch abgeschlossen ist. Wenn das Menü nicht innerhalb von 2,5 Stunden nach Beginn des Controlleraustauschs angezeigt wird, [wenden Sie sich an den Microsoft Support Service](storsimple-8000-contact-microsoft-support.md).
     
## <a name="remove-a-controller"></a>Entfernen eines Controllers
Verwenden Sie das folgende Verfahren, um ein fehlerhaftes Controllermodul aus Ihrem StorSimple-Gerät zu entfernen.

> [!NOTE]
> Die folgenden Abbildungen gelten für Controller 0. Für Controller 1 werden diese Schritte in umgekehrter Reihenfolge ausgeführt.


#### <a name="to-remove-a-controller-module"></a>So entfernen Sie ein Controllermodul
1. Greifen Sie den Modulriegel mit Daumen und Zeigefinger.
2. Drücken Sie Daumen und Zeigefinger vorsichtig zusammen, um den Controllerriegel zu lösen.
   
    ![Lösen des Controllerriegels](./media/storsimple-controller-replacement/IC741047.png)
   
    **Abbildung 2:** Lösen des Controllerriegels
3. Verwenden Sie den Riegel als Griff, um den Controller aus dem Gehäuse zu ziehen.
   
    ![Ausführen des Controllers aus dem Gehäuse](./media/storsimple-controller-replacement/IC741048.png)
   
    **Abbildung 3:** Entfernen des Controllers aus dem Gehäuse

## <a name="insert-a-controller"></a>Einsetzen eines Controllers
Gehen Sie folgendermaßen vor, um ein neues Controllermodul zu installieren, nachdem Sie ein fehlerhaftes Modul aus Ihrem StorSimple-Gerät entfernt haben.

#### <a name="to-install-a-controller-module"></a>So installieren Sie ein Controllermodul
1. Überprüfen Sie, ob Schäden an den Schnittstellenanschlüssen vorliegen. Installieren Sie das Modul nicht, wenn einer der Stecker beschädigt oder verbogen ist.
2. Schieben Sie das Controllermodul in das Gehäuse, während der Riegel vollständig geöffnet ist.
   
    ![Einsetzen des Controllers in das Gehäuse](./media/storsimple-controller-replacement/IC741053.png)
   
    **Abbildung 4:** Einsetzen des Controllers in das Gehäuse
3. Beginnen Sie bei eingesetztem Controllermodul mit dem Schließen des Riegels, und schieben Sie das Controllermodul weiter in das Gehäuse. Der Riegel führt den Controller in die richtige Position.
   
    ![Schließen des Controllerriegels](./media/storsimple-controller-replacement/IC741054.png)
   
    **Abbildung 5:** Schließen des Controllerriegels
4. Wenn der Riegel einrastet, ist der Vorgang abgeschlossen. Die LED **OK** sollte jetzt leuchten.
   
   > [!NOTE]
   > Es kann bis zu 5 Minuten dauern, bis Controller und LED aktiviert werden.
  
5. Um sicherzustellen, dass der Austausch erfolgreich war, navigieren Sie im Azure-Portal zu **Überwachung** > **Hardwareintegrität**, und stellen Sie sicher, dass Controller 0 und Controller 1 fehlerfrei sind (Status wird grün angezeigt).

## <a name="identify-the-active-controller-on-your-device"></a>Identifizieren des aktiven Controllers im Gerät
Es gibt viele Situationen, wie beispielsweise die erstmalige Registrierung des Geräts oder der Austausch eines Controllers, in denen es erforderlich ist, den aktiven Controller auf einem StorSimple-Gerät festzustellen. Der aktive Controller verarbeitet alle Datenträgerfirmware- und Netzwerkvorgänge. Zum Identifizieren des aktiven Controllers können Sie eine der folgenden Methoden verwenden:

* [Verwenden des Azure-Portals zum Identifizieren des aktiven Controllers](#use-the-azure-portal-to-identify-the-active-controller)
* [Verwenden von Windows PowerShell für StorSimple zum Identifizieren des aktiven Controllers](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [Überprüfen des physischen Geräts zum Identifizieren des aktiven Controllers](#check-the-physical-device-to-identify-the-active-controller)

Jedes dieser Verfahren wird im Folgenden beschrieben.

### <a name="use-the-azure-portal-to-identify-the-active-controller"></a>Verwenden des Azure-Portals zum Identifizieren des aktiven Controllers
Navigieren Sie im Azure-Portal zu Ihrem Gerät und dann zu **Überwachung** > **Hardwareintegrität**, und scrollen Sie zum Abschnitt **Controller**. Hier können Sie überprüfen, welcher Domänencontroller aktiv ist.

![Identifizieren des aktiven Controllers im Azure-Portal](./media/storsimple-controller-replacement/IC752072.png)

**Abbildung 6** Der aktive Controller im Azure-Portal

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Verwenden von Windows PowerShell für StorSimple zum Identifizieren des aktiven Controllers
Wenn Sie auf Ihr Gerät über die serielle Konsole zugreifen, wird ein Meldungsbanner angezeigt. Das Meldungsbanner enthält die grundlegenden Informationen, wie z. B. Modell, Name, installierte Softwareversion und Status des Controllers, auf den Sie zugreifen. Die folgende Abbildung zeigt ein Beispiel für ein Meldungsbanner:

![Meldungsbanner in der seriellen Konsole](./media/storsimple-controller-replacement/IC741098.png)

**Abbildung 7:** Meldungsbanner mit aktivem Controller 0

Über das Meldungsbanner können Sie feststellen, ob der verbundene Controller aktiv oder passiv ist.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Überprüfen des physischen Geräts zum Identifizieren des aktiven Controllers
Suchen Sie zum Identifizieren des aktiven Controllers in Ihrem Gerät die blaue LED oberhalb des DATA 5-Anschlusses auf der Rückseite des primären Gehäuses.

Wenn diese LED blinkt, ist der Controller aktiv, und der andere Controller befindet sich im Standbymodus. Verwenden Sie das folgende Diagramm und die Tabelle als Hilfsmittel.

![Gehäuserückwand des primären Geräts mit Datenanschlüssen](./media/storsimple-controller-replacement/IC741055.png)

**Abbildung 8:** Rückseite des primären Gehäuses mit Datenanschlüssen und Überwachungs-LEDs

| Bezeichnung | BESCHREIBUNG |
|:--- |:--- |
| 1-6 |Netzwerkanschlüsse DATA 0-5 |
| 7 |Blaue LED |

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Austauschen von StorSimple-Hardwarekomponenten](storsimple-8000-hardware-component-replacement.md).

