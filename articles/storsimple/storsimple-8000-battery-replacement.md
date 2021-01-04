---
title: Austauschen von Akkus bei Geräten der Microsoft Azure StorSimple 8000-Serie
description: Beschreibt das Entfernen, Ersetzen und Warten des Notfallakkus auf dem StorSimple-Gerät.
author: alkohli
ms.service: storsimple
ms.topic: troubleshooting
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 17a6a07d117557cdee23634bd211a14cf90d49e3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023770"
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Ersetzen des Notfallakkus auf dem StorSimple-Gerät

## <a name="overview"></a>Übersicht
Die Stromversorgungs- und Kühleinheit (PCM) im primären Gehäuse des Microsoft Azure StorSimple-Geräts verfügt über einen zusätzlichen Akku. Dieser Akku stellt die Stromversorgung bereit, damit das StorSimple-Gerät Daten speichern kann, wenn ein Stromausfall im primären Gehäuse auftritt. Dieser Akku wird als *Notfallakku* bezeichnet. Der Notfallakku ist nur für das primäre Gehäuse Ihres StorSimple-Geräts vorhanden (das EBOD-Gehäuse enthält keinen Notfallakku).

In diesem Tutorial werden folgende Punkte erläutert:

* Entfernen des Notfallakkus
* Installieren eines neuen Notfallakkus
* Warten des Notfallakkus

> [!IMPORTANT]
> Vor dem Entfernen und Austauschen eines Notfallakkus überprüfen Sie die Sicherheitsinformationen in der [Einführung in den Austausch von StorSimple-Hardwarekomponenten](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-backup-battery-module"></a>Entfernen des Notfallakkus
Der Notfallakku für Ihr StorSimple-Gerät ist eine vor Ort austauschbare Komponente. Vor der Installation im PCM sollten Sie den Notfallakku in seiner ursprünglichen Verpackung aufbewahren. Führen Sie die folgenden Schritte aus, um den Notfallakku zu entfernen.

#### <a name="to-remove-the-backup-battery-module"></a>So entfernen Sie den Notfallakku
1. Wechseln Sie im Azure-Portal zum Blatt des StorSimple-Geräte-Manager-Diensts. Wechseln Sie zu **Geräte**, und wählen Sie dann Ihr Gerät in der Liste der Geräte aus. Navigieren Sie zu **Überwachung** > **Hardwareintegrität**. Überprüfen Sie den Status des Akkus unter **Gemeinsam genutzte Komponenten**.
2. Stellen Sie fest, in welchem PCM der Akkufehler aufgetreten ist. Abbildung 1 zeigt die Rückseite des StorSimple-Geräts.
   
    ![Rückwand des Geräts – Primäre Gehäusemodule](./media/storsimple-battery-replacement/IC740994.png)
   
    **Abbildung 1:** Rückseite des Hauptgeräts mit PCM und Controllermodulen
   
   | Bezeichnung | BESCHREIBUNG |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Controller 0 |
   | 4 |Controller 1 |
   
    Wie unter Nummer 3 in Abbildung 2 dargestellt, sollte die Überwachungs-LED an PCM 0, die für einen **Akkufehler** steht, leuchten.
   
    ![Rückwand des Geräts – PCM-Überwachungs-LEDs](./media/storsimple-battery-replacement/IC740992.png)
   
    **Abbildung 2:** Rückseite des PCM mit Überwachungs-LEDs
   
   | Bezeichnung | BESCHREIBUNG |
   |:--- |:--- |
   | 1 |Stromausfall (Wechselstrom) |
   | 2 |Lüfterausfall |
   | 3 |Batterieausfall |
   | 4 |PCM OK |
   | 5 |Stromausfall (Gleichstrom) |
   | 6 |Batterie betriebsbereit |
3. Um das PCM mit dem fehlerhaften Akku zu entfernen, führen Sie die Schritte unter [Entfernen eines PCM](storsimple-8000-power-cooling-module-replacement.md#remove-a-pcm)aus.
4. Heben Sie bei entferntem PCM das Akkumodul nach oben, und drehen Sie den Griff, wie in der folgenden Abbildung dargestellt, um den Akku zu entfernen.
   
    ![Entfernen des Akkus aus dem PCM](./media/storsimple-battery-replacement/IC741019.png)
   
    **Abbildung 3:** Entfernen des Akkus aus dem PCM
5. Platzieren Sie das Modul in der Verpackung der vor Ort austauschbaren Einheit.
6. Geben Sie die defekte Einheit zur ordnungsgemäßen Wartung und Verarbeitung an Microsoft zurück.

## <a name="install-a-new-backup-battery-module"></a>Installieren eines neuen Notfallakkus
Führen Sie die folgenden Schritte aus, um den Notfallakku im PCM im primären Gehäuse des StorSimple-Geräts zu installieren.

#### <a name="to-install-the-battery-module"></a>So installieren Sie den Notfallakku
1. Setzen Sie den Notfallakku in der richtigen Ausrichtung im PCM ein.
2. Drücken Sie den Notfallakku am Griff so weit, bis er auf der Verbindung sitzt.
3. Ersetzen Sie das PCM im primären Gehäuse anhand der Richtlinien unter [Ersetzen einer Stromversorgungs- und Kühleinheit im StorSimple-Gerät](storsimple-8000-power-cooling-module-replacement.md).
4. Wechseln Sie nach dem Austausch zu Ihrem Gerät und dann im Azure-Portal zu **Überwachung** > **Hardwareintegrität**. Überprüfen Sie den Status des Akkus, um sicherzustellen, dass er richtig eingesetzt wurde. Ein grüner Status gibt an, dass der Akku fehlerfrei ist.

## <a name="maintain-the-backup-battery-module"></a>Warten des Notfallakkus
Im StorSimple-Gerät sorgt der Notfallakku für die Stromzufuhr des Controllers bei einem Stromausfall. Dadurch kann das StorSimple-Gerät vor dem kontrollierten Herunterfahren wichtige Daten speichern. Mit zwei vollständig geladenen Akkus in den PCMs kann das System zwei aufeinanderfolgende Stromausfälle verkraften.

Im Azure-Portal wird auf dem Blatt **Überwachung** unter **Hardwareintegrität** angezeigt, ob der Akku fehlerhaft ist oder sich seine Lebensdauer dem Ende zuneigt. Der Akkustatus wird durch **Akku in PCM 0** oder **Akku in PCM 1** unter **Gemeinsam genutzte Komponenten** angezeigt. Auf diesem Blatt wird der Status **HERUNTERGESTUFT** angezeigt, wenn sich die Lebensdauer dem Ende zuneigt, und zum Ende der Lebensdauer **FEHLERHAFT** angezeigt.

> [!NOTE]
> Der Akku meldet möglicherweise **FEHLGESCHLAGEN** , wenn er lediglich aufgeladen werden muss.


Wenn der Status **HERUNTERGESTUFT** angezeigt wird, empfehlen wir die folgende Vorgehensweise:

* Im System ist möglicherweise kürzlich ein Stromausfall aufgetreten, oder die Akkus werden derzeit eventuell einer Routinewartung unterzogen. Beobachten Sie das System für 12 Stunden, bevor Sie fortfahren.
  
  * Wenn der Status nach 12 Stunden ununterbrochener Stromversorgung bei aktivem Controller und PCMs immer noch **HERUNTERGESTUFT** ist, muss der Akku ausgetauscht werden. Wenden Sie sich zum Erhalt eines neuen Notfallakkus an [den Microsoft Support](storsimple-8000-contact-microsoft-support.md) .
  * Wenn der Status nach 12 Stunden "OK" lautet, ist der Akku betriebsbereit, und es war nur ein Wartungsladezyklus erforderlich.
* Wenn kein Stromausfall stattgefunden hat, das PCM eingeschaltet und mit dem Stromnetz verbunden ist, muss der Akku ausgetauscht werden. [Contact Microsoft Support](storsimple-8000-contact-microsoft-support.md) .

> [!IMPORTANT]
> Entsorgen Sie den defekten Akku den geltenden Vorschriften Ihres Landes entsprechend.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Austauschen von StorSimple-Hardwarekomponenten](storsimple-8000-hardware-component-replacement.md).

