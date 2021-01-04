---
title: Verwalten von Gerätecontrollern für die StorSimple 8000-Serie | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie StorSimple-Gerätecontroller beenden, neu starten, herunterfahren oder zurücksetzen.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2017
ms.author: alkohli
ms.openlocfilehash: 090797549cc61aa27945114e5ef8b666226b66e2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94956992"
---
# <a name="manage-your-storsimple-device-controllers"></a>Verwalten von StorSimple-Gerätecontrollern

## <a name="overview"></a>Übersicht

In diesem Tutorial werden die verschiedenen Vorgänge beschrieben, die auf den Gerätecontrollern von StorSimple durchgeführt werden können. Die Controller in Ihrem StorSimple-Gerät sind redundante Controller (Peer-Controller) in einer Aktiv/Passiv-Konfiguration. Es ist jeweils nur ein Controller aktiv, der alle Datenträger- und Netzwerkvorgänge verarbeitet. Der andere Controller befindet sich im passiven Modus. Wenn der aktive Controller ausfällt, wird automatisch der passive Controller aktiviert.

Dieses Tutorial enthält eine Schritt-für-Schritt-Anleitung zum Verwalten der Gerätecontroller mit folgenden Verfahren:

* Das Blatt **Controller** für Ihr Gerät im StorSimple-Geräte-Manager-Dienst.
* Windows PowerShell für StorSimple

Es wird empfohlen, die Gerätecontroller mit dem StorSimple-Geräte-Manager-Dienst zu verwalten. Wenn eine Aktion nur mithilfe von Windows PowerShell für StorSimple durchgeführt werden kann, ist dies im Tutorial entsprechend angegeben.

In diesem Tutorial lernen Sie Folgendes:

* Neustarten oder Herunterfahren eines StorSimple-Gerätecontrollers
* Herunterfahren eines StorSimple-Geräts
* Zurücksetzen des StorSimple-Geräts auf die Herstellerstandards

## <a name="restart-or-shut-down-a-single-controller"></a>Neustarten oder Herunterfahren eines einzelnen Controllers
Während des normalen Betriebs ist das Neustarten oder Herunterfahren eines Controllers normalerweise nicht erforderlich. Das Herunterfahren eines einzelnen Gerätecontrollers muss nur in Fällen durchgeführt werden, in denen eine ausgefallene Komponente der Gerätehardware ausgetauscht werden muss. Der Neustart eines Controllers kann auch erforderlich sein, wenn die Leistung durch übermäßige Arbeitsspeicherauslastung oder einen fehlerhaften Controller beeinträchtigt ist. Unter Umständen müssen Sie einen Controller auch nach einem erfolgreichen Controlleraustausch neu starten, um den neuen Controller zu aktivieren und zu testen.

Das Neustarten eines Geräts hat keine schädlichen Auswirkungen auf verbundene Initiatoren, sofern der passive Controller verfügbar ist. Wenn kein passiver Controller verfügbar ist oder wenn er ausgeschaltet ist, kann das Neustarten des aktiven Controllers zu Störungen des Diensts und zu Ausfällen führen.

> [!IMPORTANT]
> * **Ein aktiver Controller sollte nie physisch entfernt werden, da dies zu einem Verlust der Redundanz führen und das Ausfallrisiko erhöhen würde.**
> * Das folgende Verfahren gilt nur für das physische StorSimple-Gerät. Informationen zum Starten, Beenden und Neustarten der StorSimple Cloud Appliance finden Sie unter [Arbeiten mit dem Cloudgerät](storsimple-8000-cloud-appliance-u2.md#work-with-the-storsimple-cloud-appliance).

Sie können einen einzelnen Gerätecontroller neu starten oder herunterfahren, indem Sie das Azure-Portal des StorSimple Manager-Diensts oder Windows PowerShell für StorSimple verwenden.

Führen Sie die nachstehenden Schritte aus, um Gerätecontroller über das Azure-Portal zu verwalten.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>So können Sie einen Controller im Azure-Portal neu starten oder herunterfahren
1. Wechseln Sie in Ihrem StorSimple-Geräte-Manager-Dienst zu **Geräte**. Wählen Sie in der Liste der Geräte Ihr Gerät aus. 

    ![Wählen Sie ein Gerät](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Wechseln Sie zu **Einstellungen > Controller**.
   
    ![Überprüfen der Integrität von StorSimple-Gerätecontrollern](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. Überprüfen Sie auf dem Blatt **Controller**, ob der Status beider Controller auf Ihrem Gerät **Fehlerfrei** lautet. Wählen Sie einen Controller aus, klicken Sie mit der rechten Maustaste, und wählen Sie dann **Neu starten** oder **Herunterfahren** aus.

    ![Wählen von „Neu starten“ oder „Herunterfahren“ eines StorSimple-Gerätecontrollers](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. Ein Auftrag wird erstellt, um den Controller neu zu starten oder herunterzufahren. Ggf. werden entsprechende Warnungen angezeigt. Um das Neustarten oder Herunterfahren zu überwachen, wechseln Sie zu **Dienst > Aktivitätsprotokolle**, und filtern Sie nach Parametern, die für Ihren Dienst spezifisch sind. Nachdem ein Controller heruntergefahren wurde, müssen Sie den An-/Aus-Schalter drücken, um den Controller einzuschalten.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>So starten Sie einen Controller in Windows PowerShell für StorSimple neu oder fahren ihn herunter
Führen Sie die folgenden Schritte aus, um einen einzelnen Controller auf dem StorSimple-Gerät über Windows PowerShell für StorSimple herunterzufahren oder neu zu starten.

1. Greifen Sie auf das Gerät zu, indem Sie die serielle Konsole oder eine Telnet-Sitzung auf einem Remotecomputer verwenden. Stellen Sie eine Verbindung mit Controller 0 oder Controller 1 her, indem Sie die Schritte unter [Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console) ausführen.
2. Wählen Sie im Menü der seriellen Konsole Option 1 aus, d.h. die **Anmeldung mit Vollzugriff**.
3. Notieren Sie sich in der Bannermeldung den Controller, mit dem Sie verbunden sind (Controller 0 oder Controller 1), und ob es sich um den aktiven oder den passiven Controller (Standby) handelt.
   
   * Geben Sie zum Herunterfahren eines einzelnen Controllers an der Eingabeaufforderung Folgendes ein:
     
       `Stop-HcsController`
     
       Der Controller, mit dem Sie verbunden sind, wird heruntergefahren. Wenn Sie den aktiven Controller beenden, erfolgt für das Gerät vor dem Herunterfahren ein Failover auf den passiven Controller.

   * Geben Sie an der Eingabeaufforderung Folgendes ein, um einen Controller neu zu starten:
     
       `Restart-HcsController`
     
       Der Controller, mit dem Sie verbunden sind, wird neu gestartet. Wenn Sie den aktiven Controller neu starten, erfolgt vor dem Neustart ein Failover auf den passiven Controller.

## <a name="shut-down-a-storsimple-device"></a>Herunterfahren eines StorSimple-Geräts

In diesem Abschnitt wird erklärt, wie Sie ein aktives oder ausgefallenes StorSimple-Gerät über einen Remotecomputer herunterfahren. Ein Gerät wird ausgeschaltet, nachdem beide Gerätecontroller heruntergefahren wurden. Ein Gerät wird heruntergefahren, wenn das Gerät physisch bewegt oder außer Betrieb genommen wird.

> [!IMPORTANT]
> Überprüfen Sie vor dem Herunterfahren des Geräts die Integrität der Gerätekomponenten. Navigieren Sie zu Ihrem Gerät, und klicken Sie dann auf **Einstellungen > Hardwareintegrität**. Stellen Sie auf dem Blatt **Status und Hardwareintegrität** sicher, dass der LED-Status aller Komponenten grün ist. Nur ein fehlerfreies Gerät verfügt über einen grünen Status. Wenn Ihr Gerät heruntergefahren wird, um eine fehlerhafte Komponente auszutauschen, wird für die jeweilige Komponente der Fehlerstatus (rot) oder der heruntergestufte Status (gelb) angezeigt.


#### <a name="to-shut-down-a-storsimple-device"></a>So fahren Sie ein StorSimple-Gerät herunter

1. Verwenden Sie das Verfahren [Neustarten oder Herunterfahren eines Controllers](#restart-or-shut-down-a-single-controller) , um den passiven Controller auf Ihrem Gerät zu identifizieren und herunterzufahren. Sie können diesen Vorgang im Azure-Portal oder in Windows PowerShell für StorSimple durchführen.
2. Wiederholen Sie den obigen Schritt, um den aktiven Controller herunterzufahren.
3. Sie müssen sich nun die Rückwandplatine des Geräts ansehen. Nachdem die beiden Controller vollständig heruntergefahren wurden, sollten die Status-LEDs für beide Controller rot blinken. Wenn Sie das Gerät nun vollständig ausschalten müssen, bewegen Sie die Schalter an beiden Modulen (Power and Cooling Modules, PCMs) in die Position AUS. Das Gerät sollte hierdurch ausgeschaltet werden.

## <a name="reset-the-device-to-factory-default-settings"></a>Zurücksetzen des Geräts auf die werkseitigen Standardeinstellungen

> [!IMPORTANT]
> Wenn Sie Ihr Gerät auf die werkseitigen Standardeinstellungen zurücksetzen möchten, wenden Sie sich an den Microsoft Support. Das folgende Verfahren sollte nur in Verbindung mit dem Microsoft Support verwendet werden.

Hier erfahren Sie, wie Sie mit Windows PowerShell für StorSimple Ihr Microsoft Azure StorSimple-Gerät auf die werkseitigen Standardeinstellungen zurücksetzen.
Durch Zurücksetzen eines Geräts werden standardmäßig alle Daten und Einstellungen aus dem gesamten Cluster entfernt.

Führen Sie die folgenden Schritte aus, um das Microsoft Azure StorSimple-Gerät auf die werkseitigen Standardeinstellungen zurückzusetzen:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>So setzen Sie das Gerät in Windows PowerShell für StorSimple auf die Standardeinstellungen zurück
1. Greifen Sie über die serielle Konsole auf das Gerät zu. Überprüfen Sie die Bannermeldung, um sicherzustellen, dass Sie mit dem **aktiven** Controller verbunden sind.
2. Wählen Sie im Menü der seriellen Konsole Option 1 aus, d.h. die **Anmeldung mit Vollzugriff**.
3. Geben Sie an der Eingabeaufforderung den folgenden Befehl ein, um den gesamten Cluster zurückzusetzen, sodass alle Daten, Metadaten und Controllereinstellungen entfernt werden:
   
    `Reset-HcsFactoryDefault`
   
    Um stattdessen einen einzelnen Controller zurückzusetzen, verwenden Sie das Cmdlet [Reset-HcsFactoryDefault](/previous-versions/windows/powershell-scripting/dn688132(v=wps.630)) mit dem Parameter `-scope`.)
   
    Das System wird mehrmals neu gestartet. Sie werden benachrichtigt, wenn das Zurücksetzen erfolgreich abgeschlossen wurde. Je nach Systemmodell kann es 45 bis 60 Minuten dauern, bis dieser Prozess für ein Gerät der Serie 8100 beendet ist. Für ein Gerät der Serie 8600 kann dieser Prozess 60 bis 90 Minuten dauern.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Fragen und Antworten zur Verwaltung von Gerätecontrollern
In diesem Abschnitt haben wir einige der häufig gestellten Fragen zur Verwaltung von StorSimple-Gerätecontrollern zusammengefasst.

**F.** Was passiert, wenn beide Controller auf meinem Gerät fehlerfrei und eingeschaltet sind und ich den aktiven Controller neu starte oder herunterfahre?

**A.** Wenn beide Controller auf Ihrem Gerät fehlerfrei und eingeschaltet sind, werden Sie zur Bestätigung aufgefordert. Sie können wie folgt vorgehen:

* **Aktiven Controller neu starten**: Sie werden benachrichtigt, dass der Neustart eines aktiven Controllers bewirkt, dass das Gerät ein Failover auf den passiven Controller durchführt. Der Controller wird neu gestartet.
* **Einen aktiven Controller herunterfahren**: Sie werden benachrichtigt, dass das Herunterfahren eines aktiven Controllers zu Ausfallzeiten führt. Außerdem müssen Sie den An-/Aus-Schalter am Gerät betätigen, um den Controller einzuschalten.

**F.** Was passiert, wenn der passive Controller auf meinem Gerät nicht verfügbar oder ausgeschaltet ist und ich den aktiven Controller neu starte oder herunterfahre?

**A.** Wenn der passive Controller auf dem Gerät nicht verfügbar oder ausgeschaltet ist, ist der Ablauf in den unten angegebenen Fällen wie folgt:

* **Den aktiven Controller neu starten**: Sie werden benachrichtigt, dass das Fortsetzen des Vorgangs zu einer vorübergehenden Störung des Diensts führt, und zur Bestätigung aufgefordert.
* **Einen aktiven Controller herunterfahren**: Sie werden benachrichtigt, dass das Fortsetzen des Betriebs zu Ausfallzeiten führt. Außerdem müssen Sie den An-/Aus-Schalter an einem oder beiden Controllern betätigen, um das Gerät einzuschalten. Sie werden aufgefordert, diesen Schritt zu bestätigen.

**F.** In welchen Fällen kann das Neustarten oder Herunterfahren des Controllers nicht fortgesetzt werden?

**A.** Beim Neustarten oder Herunterfahren eines Controllers kann in den folgenden Fällen ein Fehler auftreten:

* Es wird ein Update des Geräts durchgeführt.
* Es wird bereits ein Neustart des Controllers durchgeführt.
* Das Herunterfahren des Controllers wird bereits durchgeführt.

**F.** Wie kann ermittelt werden, ob ein Controller neu gestartet oder heruntergefahren wurde?

**A.** Sie können den Controllerstatus auf dem Blatt „Controller“ überprüfen. Der Status des Controllers gibt an, ob ein Controller gerade neu gestartet oder heruntergefahren wird. Außerdem enthält das Blatt **Warnungen** eine Warnung des Typs „Information“, wenn der Controller neu gestartet oder heruntergefahren wird. Die Vorgänge des Neustartens und Herunterfahrens von Controllern werden zudem in den Aktivitätsprotokollen aufgezeichnet. Weitere Informationen zu Aktivitätsprotokollen finden Sie unter [Anzeigen der Aktivitätsprotokolle](storsimple-8000-service-dashboard.md#view-the-activity-logs).

**F.** Wirkt sich ein Failover des Controllers auf die Ein- und Ausgabe aus?

**A.** Die TCP-Verbindungen zwischen Initiatoren und aktivem Controller werden bei einem Failover des Controllers zurückgesetzt, aber wiederhergestellt, wenn der passive Controller den Betrieb übernimmt. Im Verlauf dieses Vorgangs kann es zu einer vorübergehenden Pause (weniger als 30 Sekunden) bei der E/A-Aktivität zwischen Initiatoren und dem Gerät kommen.

**F.** Wie kann ich den Controller wieder in Betrieb nehmen, nachdem er heruntergefahren und entfernt wurde?

**A.** Wenn Sie einen Controller wieder in Betrieb nehmen möchten, müssen Sie ihn wie unter [Ersetzen des Controllermoduls auf dem StorSimple-Gerät](storsimple-8000-controller-replacement.md).

## <a name="next-steps"></a>Nächste Schritte
* [Wenden Sie sich an den Microsoft Support](storsimple-8000-contact-microsoft-support.md), falls ein Problem mit Ihren StorSimple-Gerätecontrollern auftritt, das Sie mit den Verfahren in diesem Tutorial nicht beheben können.
* Weitere Informationen zum Verwenden des StorSimple-Geräte-Manager-Diensts finden Sie unter [Verwalten Ihres StorSimple-Geräts mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-8000-manager-service-administration.md).