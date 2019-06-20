---
title: Ein- und Ausschalten von StorSimple-Geräten der Serie 8000 | Microsoft-Dokumentation
description: 'Dieses Thema erläutert Folgendes: Einschalten eines neuen StorSimple-Geräts oder ein Geräts, das heruntergefahren werden musste oder dessen Stromversorgung unterbrochen war, sowie Ausschalten eines ausgeführten Geräts.'
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 8e9c6e6c-965c-4a81-81bd-e1c523a14c82
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 29a45c0d32e35b5d321670bf25334a2976b93e56
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64693680"
---
# <a name="turn-on-or-turn-off-your-storsimple-8000-series-device"></a>Ein- und Ausschalten von Geräten der StorSimple 8000-Serie

## <a name="overview"></a>Übersicht
Im Rahmen des normalen Systembetriebs ist es nicht erforderlich, ein Microsoft Azure StorSimple-Gerät herunterzufahren. Möglicherweise müssen Sie jedoch ein neues Gerät oder ein Gerät, das heruntergefahren werden musste, einschalten. Im Allgemeinen müssen Sie Geräte herunterfahren, wenn Sie fehlerhafte Hardware austauschen, eine Einheit physisch verschieben oder ein Gerät außer Betrieb nehmen müssen. Dieses Tutorial beschreibt das erforderliche Verfahren zum Einschalten und Herunterfahren von StorSimple-Geräten in verschiedenen Szenarien.

## <a name="turn-on-a-new-device"></a>Einschalten eines neuen Geräts
Die Schritte zum erstmaligen Einschalten eines StorSimple-Geräts unterscheiden sich, je nachdem, ob es sich um ein 8100- oder ein 8600-Modell handelt. Das Modell 8100 verfügt über ein einziges primäres Gehäuse, während das Modell 8600 zwei Gehäuse aufweist: ein primäres und ein EBOD-Gehäuse. In den folgenden Abschnitten werden die detaillierten Schritte für beide Modelle beschrieben.

* [Neues Gerät nur mit primärem Gehäuse](#new-device-with-primary-enclosure-only)
* [Neues Gerät mit EBOD-Gehäuse](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Neues Gerät nur mit primärem Gehäuse
Das StorSimple-Modell 8100 besitzt nur ein Gehäuse. Das Gerät enthält redundante Stromversorgungs- und Kühleinheiten (Power and Cooling Modules, PCMs). Beide PCMs müssen installiert und mit unterschiedlichen Stromquellen verbunden sein, um Hochverfügbarkeit sicherzustellen.

Führen Sie die folgenden Schritte aus, um das Gerät für die Stromversorgung zu verkabeln.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

> [!NOTE]
> Vollständige Anweisungen zur Einrichtung und Verkabelung des Geräts finden Sie unter [Installieren des StorSimple 8100-Geräts](storsimple-8100-hardware-installation.md). Stellen Sie sicher, dass die Anweisungen genau befolgt werden.
> 
> 

### <a name="new-device-with-ebod-enclosure"></a>Neues Gerät mit EBOD-Gehäuse
Das StorSimple-Modell 8600 weist ein primäres und ein EBOD-Gehäuse auf. Daher müssen die Einheiten für SAS-Konnektivität (Serial Attached SCSI) und Stromversorgung miteinander verbunden werden.

Wenn Sie dieses Gerät zum ersten Mal einrichten, führen Sie zuerst die Schritte für die SAS-Verkabelung und danach die Schritte für die Stromverkabelung aus.

[!INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

> [!NOTE]
> Vollständige Anweisungen zur Einrichtung und Verkabelung des Geräts finden Sie unter [Installieren des StorSimple 8600-Geräts](storsimple-8600-hardware-installation.md). Stellen Sie sicher, dass die Anweisungen genau befolgt werden.

## <a name="turn-on-a-device-after-shutdown"></a>Einschalten eines Geräts nach dem Herunterfahren
Die Schritte zum Einschalten eines StorSimple-Geräts nach dem Herunterfahren unterscheiden sich, je nachdem, ob es sich um ein 8100- oder ein 8600-Modell handelt. Das Modell 8100 verfügt über ein einziges primäres Gehäuse, während das Modell 8600 zwei Gehäuse aufweist: ein primäres und ein EBOD-Gehäuse.

* [Gerät nur mit primärem Gehäuse](#device-with-primary-enclosure-only)
* [Gerät mit EBOD-Gehäuse](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Gerät nur mit primärem Gehäuse
Verwenden Sie das folgende Verfahren, um ein StorSimple-Gerät mit primärem Gehäuse (ohne EBOD-Gehäuse) nach dem Herunterfahren einzuschalten.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>So aktivieren Sie ein Gerät nur mit primärem Gehäuse
1. Stellen Sie sicher, dass sich die Netzschalter beider PCMs in Position OFF befinden. Wenn dies nicht der Fall ist, bringen Sie die Schalter in die Position OFF, und warten Sie, bis die LEDs erloschen sind.
2. Schalten Sie das Gerät ein, indem Sie die Netzschalter beider PCMs in die Position ON bringen. Das Gerät sollte sich einschalten.
3. Prüfen Sie Folgendes, um sicherzustellen, dass das Gerät vollständig eingeschaltet ist:
   
   1. Die LEDs für OK leuchten an beiden PCMs grün.
   2. Die Status-LEDs an beiden Controllern leuchten grün.
   3. Die blaue LED an einem der Controller blinkt, was darauf hinweist, dass der Controller aktiv ist.
      
      Wenn eine dieser Bedingungen nicht erfüllt ist, funktioniert das Gerät nicht fehlerfrei. [Kontaktieren Sie den Microsoft-Support](storsimple-8000-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>Gerät mit EBOD-Gehäuse
Verwenden Sie das folgende Verfahren, um ein StorSimple-Gerät mit primärem und EBOD-Gehäuse nach dem Herunterfahren einzuschalten. Führen Sie jeden Schritt in der richtigen Reihenfolge und genau wie beschrieben aus. Andernfalls könnten Datenverluste die Folge sein.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>So aktivieren Sie ein Gerät mit primärem und EBOD-Gehäuse
1. Stellen Sie sicher, dass das EBOD-Gehäuse mit dem primären Gehäuse verbunden ist. Weitere Informationen finden Sie unter [Installieren des StorSimple 8600-Geräts](storsimple-8600-hardware-installation.md).
2. Stellen Sie sicher, dass sich die Netzschalter der PCMs sowohl am EBOD- als auch am primären Gehäuse in Position OFF befinden. Wenn dies nicht der Fall ist, bringen Sie die Schalter in die Position OFF, und warten Sie, bis die LEDs erloschen sind.
3. Schalten Sie zuerst das EBOD-Gehäuse ein, indem Sie die Netzschalter beider PCMs in Position ON bringen. Die PCM-LEDs sollten grün leuchten. Eine grüne EBOD-Controller-LED an dieser Einheit weist darauf hin, dass das EBOD-Gehäuse eingeschaltet ist.
4. Schalten Sie das primäre Gehäuse ein, indem Sie die Netzschalter beider PCMs in Position ON bringen. Das gesamte System sollte jetzt eingeschaltet sein.
5. Überprüfen Sie, ob die SAS-LEDs grün leuchten, um sicherzustellen, dass die Verbindung zwischen EBOD- und primärem Gehäuse gut ist.

## <a name="turn-on-a-device-after-a-power-loss"></a>Einschalten eines Geräts nach einem Stromausfall
Ein Ausfall oder eine Unterbrechung der Stromversorgung kann dazu führen, dass ein StorSimple-Gerät heruntergefahren wird. Ein solcher Stromausfall kann in einer oder beiden Stromversorgungen auftreten. Die Wiederherstellungsschritte unterscheiden sich, je nachdem, ob es sich bei dem Gerät um ein 8100- oder ein 8600-Modell handelt. Das Modell 8100 verfügt über ein einziges primäres Gehäuse, während das Modell 8600 zwei Gehäuse aufweist: ein primäres und ein EBOD-Gehäuse. In diesem Abschnitt werden die Wiederherstellungsverfahren für jedes Szenario besprochen.

* [Gerät nur mit primärem Gehäuse](#8100)
* [Gerät mit EBOD-Gehäuse](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Gerät nur mit primärem Gehäuse <a name="8100">
Wenn eine der beiden Stromversorgungen unterbrochen wird, kann das System den normalen Betrieb fortsetzen. Um die Hochverfügbarkeit des Geräts sicherzustellen, sollten Sie jedoch die Stromversorgung möglichst bald wiederherstellen.

Wenn beide Stromversorgungen ausfallen oder unterbrochen werden, fährt das System ordnungsgemäß und kontrolliert herunter. Wenn die Stromversorgung wiederhergestellt ist, schaltet sich das System automatisch ein.

### <a name="device-with-ebod-enclosure-a-name8600"></a>Gerät mit EBOD-Gehäuse <a name="8600">
#### <a name="power-loss-on-one-power-supply"></a>Stromausfall in einer der Stromversorgungen
Wenn eine der Stromversorgungen im primären oder im EBOD-Gehäuse unterbrochen wird, kann das System den normalen Betrieb fortsetzen. Um die Hochverfügbarkeit des Geräts sicherzustellen, stellen Sie die Stromversorgung möglichst bald wieder her.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Stromausfall in beiden Stromversorgungen im primären und im EBOD-Gehäuse
Wenn beide Stromversorgungen ausfallen oder unterbrochen werden, fährt das EBOD-Gehäuse sofort und das primäre Gehäuse ordnungsgemäß und kontrolliert herunter. Wenn die Stromversorgung wiederhergestellt ist, startet das Gerät automatisch.

Wenn die Stromversorgung manuell ausgeschaltet wurde, führen Sie die folgenden Schritte aus, um das System wieder mit Strom zu versorgen.

1. Schalten Sie das EBOD-Gehäuse ein.
2. Wenn das EBOD-Gehäuse eingeschaltet ist, schalten Sie das primäre Gehäuse ein.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Stromausfall in beiden Stromversorgungen im EBOD-Gehäuse
Wenn Sie die Verkabelung installieren, müssen Sie sicherstellen, dass das EBOD-Gehäuse niemals nur an eine separate Stromverteilungseinheit angeschlossen ist. Wenn das EBOD- und das primäre Gehäuse gleichzeitig ausfallen, wird das System wiederhergestellt.

Wenn nur das EBOD an beiden Stromversorgungen ausfällt, wird das System nicht automatisch wiederhergestellt. Führen Sie die folgenden Schritte aus, um das System einzuschalten und in einem fehlerfreien Zustand wiederherzustellen:

1. Wenn das primäre Gehäuse eingeschaltet ist, schalten Sie beide PCMs aus.
2. Warten Sie einige Minuten, bis das System vollständig heruntergefahren ist.
3. Schalten Sie das EBOD-Gehäuse ein.
4. Wenn das EBOD-Gehäuse eingeschaltet ist, schalten Sie das primäre Gehäuse ein.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Einschalten eines Geräts nach Unterbrechung der Verbindung von primärem Gehäuse und EBOD
Wenn die Verbindung zwischen dem Standbycontroller und dem entsprechenden EBOD-Controller unterbrochen wird, arbeitet das Gerät weiter. Wenn die Verbindung zwischen dem aktiven Systemcontroller und dem entsprechenden EBOD-Controller unterbrochen wird, sollte ein Failover auftreten, und das Gerät sollte weiterhin normal funktionieren.

Wenn beide SAS-Kabel (Serial Attached SCSI) entfernt werden oder die Verbindung zwischen dem EBOD- und dem primären Gehäuse unterbrochen wird, stellt das Gerät den Betrieb ein. Führen Sie in diesem Fall die folgenden Schritte aus:

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>So schalten Sie ein Gerät nach unterbrochener Verbindung ein
1. Greifen Sie auf die Rückseite des Geräts zu.
2. Wenn das SAS-Kabel zwischen dem EBOD- und dem primären Gehäuse beschädigt ist, sind alle SAS-Leitungs-LEDs am EBOD-Gehäuse erloschen.
3. Fahren Sie beide PCMs am EBOD- und am primären Gehäuse herunter.
4. Warten Sie, bis alle LEDs auf der Rückseite beider Gehäuse erloschen sind.
5. Stecken Sie die SAS-Kabel wieder ein, und stellen Sie sicher, dass eine gute Verbindung zwischen dem EBOD- und dem primären Gehäuse besteht.
6. Schalten Sie zuerst das EBOD-Gehäuse ein, indem Sie beide PCM-Schalter in Position ON bringen.
7. Stellen Sie sicher, dass das EBOD-Gehäuse eingeschaltet ist, indem Sie überprüfen, ob die grüne LED leuchtet.
8. Schalten Sie das primäre Gehäuse ein.
9. Stellen Sie sicher, dass das primäre Gehäuse eingeschaltet ist, indem Sie überprüfen, ob die grüne Controller-LED leuchtet.
10. Stellen Sie sicher, dass eine gute Verbindung zwischen dem EBOD- und dem primären Gehäuse besteht, indem Sie überprüfen, ob alle SAS-Leitungs-LEDs (vier pro EBOD-Controller) leuchten.

> [!IMPORTANT]
> Wenn Sie das System einschalten und die SAS-Kabel beschädigt sind oder keine gute Verbindung zwischen dem EBOD- und dem primären Gehäuse besteht, wechselt das System in den Wiederherstellungsmodus. [Kontaktieren Sie den Microsoft-Support](storsimple-8000-contact-microsoft-support.md) , falls dies eintritt.


## <a name="turn-off-a-running-device"></a>Ausschalten eines ausgeführten Geräts
Ein ausgeführtes StorSimple-Gerät muss möglicherweise heruntergefahren werden, wenn es verschoben oder außer Betrieb genommen werden soll oder wenn eine Komponente des Geräts fehlerhaft ist und ausgetauscht werden muss. Die Schritte unterscheiden sich, je nachdem, ob es sich bei dem StorSimple-Gerät um ein 8100- oder ein 8600-Modell handelt. Das Modell 8100 verfügt über ein einziges primäres Gehäuse, während das Modell 8600 zwei Gehäuse aufweist: ein primäres und ein EBOD-Gehäuse. In diesem Abschnitt werden die Schritte zum Herunterfahren eines ausgeführten Geräts beschrieben.

* [Gerät mit primärem Gehäuse](#8100a)
* [Gerät mit EBOD-Gehäuse](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Gerät mit primärem Gehäuse <a name="8100a">
Ordnungsgemäß und kontrolliert herunterfahren können Sie das Gerät über das Azure-Portal oder Windows PowerShell für StorSimple. 

> [!IMPORTANT]
> Schalten Sie ein ausgeführtes Gerät nicht über den Netzschalter auf der Geräterückseite aus.
> 
> Bevor Sie das Gerät herunterfahren, vergewissern Sie sich, dass alle Gerätekomponenten fehlerfrei funktionieren. Navigieren Sie im Azure-Portal zu **Geräte** > **Überwachung** > **Hardwareintegrität**, und vergewissern Sie sich, dass der Status aller Komponenten in Grün angezeigt wird. Dies ist nur der Fall, wenn das System einwandfrei funktioniert. Wenn das System heruntergefahren wird, um eine fehlerhafte Komponente auszutauschen, wird für die jeweilige Komponente unter **Hardwarestatus**der Fehlerstatus (rot) oder der heruntergestufte Status (gelb) angezeigt.
> 
> 

Nachdem Sie auf Windows PowerShell für StorSimple oder das Azure-Portal zugegriffen haben, führen Sie die Schritte in [Herunterfahren eines StorSimple-Geräts](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) aus. 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>Gerät mit EBOD-Gehäuse <a name="8600a">
> [!IMPORTANT]
> Bevor Sie das primäre und das EBOD-Gehäuse herunterfahren, stellen Sie sicher, dass sich alle Gerätekomponenten in fehlerfreiem Zustand befinden. Navigieren Sie im Azure-Portal zu **Geräte** > **Überwachung** > **Hardwareintegrität**, und überprüfen Sie, ob alle Komponenten fehlerfrei funktionieren.


#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>So fahren Sie ein ausgeführtes Gerät mit EBOD-Gehäuse herunter
1. Führen Sie alle unter [Herunterfahren eines StorSimple-Geräts](storsimple-8000-manage-device-controller.md#shut-down-a-storsimple-device) aufgeführten Schritte für das primäre Gehäuse aus.
2. Nachdem das primäre Gehäuse heruntergefahren wurde, fahren Sie das EBOD-Gehäuse herunter, indem Sie beide PCM-Schalter ausschalten.
3. Stellen Sie sicher, dass das EBOD-Gehäuse heruntergefahren ist, indem Sie sich vergewissern, dass alle LEDs auf der Rückseite des EBOD-Gehäuses erloschen sind.

> [!NOTE]
> Die SAS-Verbindungskabel zwischen dem EBOD- und dem primären Gehäuse dürfen erst entfernt werden, nachdem das System heruntergefahren wurde.

## <a name="next-steps"></a>Nächste Schritte
[Kontaktieren Sie den Microsoft-Support](storsimple-8000-contact-microsoft-support.md) , wenn beim Einschalten oder Herunterfahren eines StorSimple-Geräts Probleme auftreten.

