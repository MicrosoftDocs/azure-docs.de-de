---
title: Ändern des StorSimple-Gerätemodus | Microsoft-Dokumentation
description: Beschreibt die StorSimple-Gerätemodi und erläutert, wie Sie den Gerätemodus mithilfe von Windows PowerShell für StorSimple ändern.
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
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: c7b0ea489c1d70ab86d677aad666ea6728fa76b4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017082"
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>Ändern des Gerätemodus auf Ihrem StorSimple-Gerät

Dieser Artikel enthält eine kurze Beschreibung der verschiedenen Modi, in denen StorSimple-Geräte ausgeführt werden können. Auf Ihrem StorSimple-Gerät stehen drei Modi zur Verfügung: der normale Modus, der Verwaltungsmodus und der Wiederherstellungsmodus.

Dieser Artikel enthält Folgendes:

* Informationen zu den StorSimple-Gerätemodi
* Informationen zur Ermittlung des derzeit aktiven Modus des StorSimple-Geräts
* Informationen zum Ändern des normalen Modus in den Wartungsmodus und *umgekehrt*

Die oben aufgeführten Verwaltungsaufgaben können nur über die Windows PowerShell-Schnittstelle Ihres StorSimple-Geräts ausgeführt werden.

## <a name="about-storsimple-device-modes"></a>Informationen zu den StorSimple-Gerätemodi

Ihr StorSimple-Gerät kann im normalen Modus, im Wartungsmodus oder im Wiederherstellungsmodus betrieben werden. Nachfolgend sind alle diese Modi kurz beschrieben.

### <a name="normal-mode"></a>Normaler Modus

Dies ist der normale Betriebsmodus für ein vollständig konfiguriertes StorSimple-Gerät. Standardmäßig sollte sich das Gerät im normalen Modus befinden.

### <a name="maintenance-mode"></a>Wartungsmodus

Manchmal muss das StorSimple-Gerät in den Wartungsmodus versetzt werden. In diesem Modus können Sie Wartungsarbeiten auf dem Gerät ausführen und Updates installieren, die den Betrieb unterbrechen, wie etwa Updates für Festplattenfirmware.

Sie können das System nur über Windows PowerShell für StorSimple in den Wartungsmodus versetzen. Alle E/A-Anforderungen werden in diesem Modus angehalten. Auch werden die Dienste wie der NVRAM- (Non-Volatile Random Access Memory) oder der Clusterdienst beendet. Beide Controller werden neu gestartet, wenn Sie diesen Modus starten oder beenden. Wenn Sie den Wartungsmodus beenden, werden alle Dienste fortgesetzt und sollten fehlerfrei funktionieren. Dies kann einige Minuten dauern.

> [!NOTE]
> **Der Wartungsmodus wird nur auf einem einwandfrei funktionierenden Gerät unterstützt. Auf einem Gerät, auf dem ein oder beide Controller nicht funktionieren, wird der Modus nicht unterstützt.**


### <a name="recovery-mode"></a>Wiederherstellungsmodus

Der Wiederherstellungsmodus kann als „Abgesicherter Modus von Windows mit Netzwerkunterstützung“ beschrieben werden. Beim Wiederherstellungsmodus ist das Supportteam von Microsoft involviert, das Diagnoseschritte für das System ausführt. Das Hauptziel des Wiederherstellungsmodus besteht im Abrufen der Systemprotokolle.

Wenn Ihr System in den Wiederherstellungsmodus wechselt, sollten Sie sich mit dem Microsoft-Support in Verbindung setzen, um Informationen zu den nächsten Schritten zu erhalten. Wenn Sie weitere Informationen benötigen, rufen Sie [Microsoft-Support kontaktieren](storsimple-8000-contact-microsoft-support.md)auf.

> [!NOTE]
> **Sie können das Gerät nicht in den Wiederherstellungsmodus versetzen. Wenn sich das Gerät in einem fehlerhaften Zustand befindet, versucht der Wiederherstellungsmodus das Gerät in einen Zustand zu versetzen, in dem Supportmitarbeiter von Microsoft das Gerät untersuchen können.**

## <a name="determine-storsimple-device-mode"></a>Ermitteln des StorSimple-Gerätemodus

#### <a name="to-determine-the-current-device-mode"></a>So ermitteln Sie den aktuellen Gerätemodus

1. Melden Sie sich bei der seriellen Gerätekonsole an, indem Sie die Schritte unter [Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)ausführen.
2. Sehen Sie sich die Bannermeldung im Menü der seriellen Konsole des Geräts an. Diese Meldung gibt explizit an, ob sich das Gerät im Wartungsmodus oder im Wiederherstellungsmodus befindet. Wenn die Meldung keine bestimmten Informationen zum Systemmodus enthält, befindet sich das Gerät im normalen Modus.

## <a name="change-the-storsimple-device-mode"></a>Ändern des StorSimple-Gerätemodus

Sie können das StorSimple-Gerät (vom normalen Modus) in den Wartungsmodus versetzen, um Wartungsaufgaben auszuführen oder Wartungsmodus-Updates zu installieren. Führen Sie die folgenden Schritte aus, um den Wartungsmodus zu starten oder zu beenden.

> [!IMPORTANT]
> Vergewissern Sie sich vor dem Starten des Wartungsmodus, dass beide Gerätecontroller fehlerfrei funktionieren, indem Sie im Azure-Portal für Ihr Gerät auf **Geräteeinstellungen > Hardwareintegrität** zugreifen. Falls einer oder beide Controller nicht fehlerfrei funktionieren, wenden Sie sich für die nächsten Schritte an den Microsoft-Support. Wenn Sie weitere Informationen benötigen, rufen Sie [Microsoft-Support kontaktieren](storsimple-8000-contact-microsoft-support.md)auf.
 

#### <a name="to-enter-maintenance-mode"></a>So wechseln Sie in den Wartungsmodus

1. Melden Sie sich bei der seriellen Gerätekonsole an, indem Sie die Schritte unter [Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)ausführen.
2. Wählen Sie im Menü der seriellen Konsole Option 1 aus, d.h. die **Anmeldung mit Vollzugriff**. Geben Sie das **Geräteadministratorkennwort** an, wenn Sie dazu aufgefordert werden. Das Standardkennwort lautet `Password1`.
3. Geben Sie an der Eingabeaufforderung Folgendes ein: 
   
    `Enter-HcsMaintenanceMode`
4. Es wird eine Warnmeldung angezeigt, die Sie informiert, dass durch den Wartungsmodus alle E/A-Anforderungen unterbrochen werden und die Verbindung mit dem Azure-Portal getrennt wird. Sie werden aufgefordert, dies zu bestätigen. Geben Sie **J** ein, um in den Wartungsmodus zu wechseln.
5. Beide Controller werden neu gestartet. Nach Abschluss des Neustarts wird in der seriellen Konsole in einem Banner angezeigt, dass sich das Gerät im Wartungsmodus befindet. Nachfolgend sehen Sie eine Beispielausgabe.

```
    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Controller0>Enter-HcsMaintenanceMode
    Checking device state...

    In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>

```

#### <a name="to-exit-maintenance-mode"></a>So beenden Sie den Wartungsmodus

1. Melden Sie sich bei der seriellen Gerätekonsole an. Überprüfen Sie anhand der Bannermeldung, ob sich Ihr Gerät im Wartungsmodus befindet.
2. Geben Sie an der Eingabeaufforderung Folgendes ein:
   
    `Exit-HcsMaintenanceMode`
3. Eine Warnmeldung und eine Bestätigungsmeldung werden angezeigt. Geben Sie **J** ein, um den Wartungsmodus zu beenden.
4. Beide Controller werden neu gestartet. Nach Abschluss des Neustarts wird in der seriellen Konsole in einem Banner angezeigt, dass sich das Gerät im normalen Modus befindet. Nachfolgend sehen Sie eine Beispielausgabe.

```
    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0
    ---------------------------------------------------------------

    Controller0>Exit-HcsMaintenanceMode
    Checking device state...

    Before exiting maintenance mode, ensure that any updates that are required on both controllers have been applied. Failure to install on each controller could result in data corruption. Exiting maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to exit maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Active
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>
```

## <a name="next-steps"></a>Nächste Schritte

Hier erfahren Sie, wie Sie auf Ihrem StorSimple-Gerät [Updates im normalen Modus und im Wartungsmodus installieren](storsimple-update-device.md) .

