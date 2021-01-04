---
title: Bekannte Probleme bei Azure Kinect und Problembehandlung
description: Informieren Sie sich über einige der bekannten Probleme und Tipps zur Problembehandlung bei der Verwendung des Sensor SDK mit Azure Kinect DK.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Problembehandlung, Update, Bug, Kinect, Feedback, Wiederherstellung, Protokollierung, Tipps
ms.openlocfilehash: 9711968de061956a945fca183444dd6ebde4ca9c
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94356381"
---
# <a name="azure-kinect-known-issues-and-troubleshooting"></a>Bekannte Probleme bei Azure Kinect und Problembehandlung

Diese Seite enthält bekannte Probleme und Tipps zur Problembehandlung bei der Verwendung des Sensor SDK mit Azure Kinect DK. Weitere Informationen zu hardwarespezifischen Produktproblemen finden Sie außerdem auf den [Produktsupportseiten](./index.yml).

## <a name="known-issues"></a>Bekannte Probleme

- Kompatibilitätsprobleme mit ASMedia-USB-Hostcontrollern (z. B. mit ASM1142-Chipsatz)
  - In manchen Fällen kann die Blockierung durch Verwenden des Microsoft USB-Treibers behoben werden
  - Viele PCs weisen außerdem alternative Hostcontroller auf, und das Ändern des USB3-Anschlusses hilft möglicherweise weiter.

Weitere Probleme im Zusammenhang mit dem Sensor SDK finden Sie in den [GitHub Issues](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/issues)

## <a name="collecting-logs"></a>Sammeln von Protokollen

Die Protokollierung für die K4A.dll wird durch Umgebungsvariablen aktiviert. Standardmäßig werden Protokolle an stdout gesendet, und es werden nur Fehler und kritische Meldungen generiert. Diese Einstellungen können geändert werden, sodass die Protokollierung in einer Datei erfolgt. Die Ausführlichkeit lässt sich ebenfalls nach Bedarf anpassen. Unten finden Sie ein Beispiel für Windows zum Aktivieren der Protokollierung in einer Datei mit dem Namen „k4a.log“, in der Warnungen und höherstufige Meldungen erfasst werden.

1. `set K4A_ENABLE_LOG_TO_A_FILE=k4a.log`
2. `set K4A_LOG_LEVEL=w`
3. Führen Sie ein Szenario an einer Eingabeaufforderung aus (starten Sie beispielsweise den Viewer)
4. Navigieren Sie zu „k4a.log“, und geben Sie die Datei frei.

Weitere Informationen finden Sie im Codeausschnitt aus der Headerdatei unten:

```console
/**
* environment variables
* K4A_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4A_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4A_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

Die Protokollierung für die K4ABT.dll des Body Tracking SDK ist ähnlich, abgesehen davon, dass die Benutzer einen anderen Satz von Umgebungsvariablen ändern sollten:

```console
/**
* environment variables
* K4ABT_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4ABT_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4ABT_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

## <a name="device-doesnt-enumerate-in-device-manager"></a>Das Gerät wird im Geräte-Manager nicht aufgelistet

- Überprüfen Sie die Status-LED auf der Geräterückseite. Wenn sie bernsteinfarben blinkt, liegt ein Verbindungsproblem vor, und das Gerät erhält nicht genügend Leistung. Sie sollten das Netzkabel in das mitgelieferte Netzteil einstecken. Das Netzkabel weist zwar eine USB-Typ A-Verbindung auf, das Gerät benötigt aber mehr Leistung, als über einen PC-USB-Anschluss bereitgestellt werden kann. Verbinden Sie es also nicht mit einem PC-Anschluss oder einem USB-Hub.
- Vergewissern Sie sich, dass ein Netzkabel angeschlossen ist und Sie zur Datenübertragung einen USB3-Anschluss verwenden.
- Versuchen Sie, den USB3-Anschluss für die Datenbankverbindung zu ändern (Es empfiehlt sich, einen nahe am Motherboard liegenden USB-Anschluss zu verwenden, beispielsweise auf der PC-Rückseite).
- Überprüfen Sie Ihr Kabel, beschädigte oder minderwertige Kabel können zu einer unzuverlässigen Auflistung führen (das Gerät „blinkt“ fortgesetzt im Geräte-Manager).
- Wenn Sie das Gerät mit einem Laptop verbunden haben, der auf Akku läuft, verringert dies möglicherweise die Leistung des Anschlusses.
- Starten Sie den Host-PC neu.
- Wenn das Problem weiterhin besteht, liegt möglicherweise ein Kompatibilitätsproblem vor.
- Wenn während eines Firmwareupdates ein Fehler aufgetreten ist und sich das Gerät nicht selbst wiederhergestellt hat, führen Sie ein [Zurücksetzen auf die Werkseinstellungen](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk) durch.

## <a name="azure-kinect-viewer-fails-to-open"></a>Azure Kinect Viewer wird nicht geöffnet

- Überprüfen Sie zunächst, ob Ihr Gerät im Windows Geräte-Manager aufgelistet ist.

    ![Azure Kinect-Kameras im Windows Geräte-Manager](./media/resources/viewer-fails.png)

- Überprüfen Sie, ob eine andere Ihrer Anwendungen das Gerät verwendet (z. B. Windows Kamera-Anwendung). Es kann jederzeit nur jeweils eine Anwendung auf das Gerät zugreifen.
- Überprüfen Sie das Protokoll „k4aviewer.err“ auf Fehlermeldungen.
- Öffnen Sie die Windows Kamera-Anwendung, und überprüfen Sie deren Funktion.
- Schalten Sie das Gerät aus und wieder ein, und warten Sie, bis die Streaming-LED erloschen ist, bevor Sie das Gerät verwenden.
- Starten Sie den Host-PC neu.
- Vergewissern Sie sich, dass Sie auf dem PC die neuesten Grafiktreiber verwenden.
- Wenn Sie ein selbst erstelltes SDK verwenden, probieren Sie aus, ob die offiziell veröffentlichte Version das Problem behebt.
- Wenn das Problem fortbesteht, [sammeln Sie Protokolle](troubleshooting.md#collecting-logs), und reichen Sie Feedback ein.

## <a name="cannot-find-microphone"></a>Das Mikrofon wird nicht gefunden

- Überprüfen Sie zunächst, ob das Mikrofonarray im Geräte-Manager aufgeführt ist.
- Wenn ein Gerät aufgelistet wird und ansonsten unter Windows ordnungsgemäß funktioniert, kann das Problem vorliegen, dass Windows der Tiefenkamera nach einem Firmwareupdate eine andere Container-ID zugewiesen hat.
- Sie können versuchen, sie im Geräte-Manager zurückzusetzen, indem Sie mit der rechten Maustaste auf „Azure Kinect-Mikrofonarray“ klicken und „Gerät deinstallieren“ auswählen. Sobald dies erfolgt ist, trennen Sie den Sensor, und schließen Sie ihn wieder an.

    ![Azure Kinect-Mikrofonarray](./media/resources/mic-not-found.png)

- Starten Sie dann Azure Kinect Viewer erneut, und versuchen Sie es noch einmal.

## <a name="device-firmware-update-issues"></a>Updateprobleme der Gerätefirmware

- Wenn nach einem Update nicht die richtige Versionsnummer gemeldet wird, müssen Sie das Gerät möglicherweise aus- und wieder einschalten.
- Wenn bei einem Firmwareupdate eine Unterbrechung eintritt, kann es in einen fehlerhaften Zustand gelangen und wird dann nicht aufgelistet. Trennen Sie das Gerät, schließen Sie es erneut an, und warten Sie 60 Sekunden lang, um zu überprüfen, ob es sich wiederherstellen kann.
Ist dies nicht der Fall, führen Sie das [Zurücksetzen auf die Werkseinstellungen](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk) durch

## <a name="image-quality-issues"></a>Probleme bei der Bildqualität

- Starten Sie [Azure Kinect Viewer](azure-kinect-viewer.md), und prüfen Sie, ob die Platzierung des Geräts Interferenzen verursacht oder ob der Sensor blockiert oder das Objektiv verschmutzt ist.
- Probieren Sie verschiedene Betriebsmodi aus, um weiter einzugrenzen, ob das Problem nur in einem bestimmten Modus auftritt.
- Um Probleme bei der Bildqualität mit dem Team zu teilen, bieten sich Ihnen diese Möglichkeiten:

1) Erstellen Sie einen Screenshot von einem angehaltenen Bild in [Azure Kinect Viewer](azure-kinect-viewer.md) oder
2) Erstellen Sie eine Aufzeichnung mithilfe des [Azure Kinect-Rekorders](azure-kinect-recorder.md), beispielsweise `k4arecorder.exe -l 5 -r 5 output.mkv`

## <a name="inconsistent-or-unexpected-device-timestamps"></a>Inkonsistente oder unerwartete Zeitstempel von Geräten

Das Aufrufen von ```k4a_device_set_color_control``` kann vorübergehend Änderungen der Gerätezeit bewirken, die sich erst nach mehreren Aufzeichnungen stabilisieren. Vermeiden Sie es, die API in der Bilderfassungsschleife aufzurufen, um ein Zurücksetzen der internen Zeitberechnung für jedes neue Bild zu vermeiden. Rufen Sie stattdessen die API vor dem Starten der Kamera auf oder nur dann, wenn Sie den Wert innerhalb der Bilderfassungsschleife ändern müssen. Vermeiden Sie insbesondere das Aufrufen von ```k4a_device_set_color_control(K4A_COLOR_CONTROL_AUTO_EXPOSURE_PRIORITY)```.

## <a name="usb3-host-controller-compatibility"></a>Kompatibilität des USB3-Hostcontrollers

Wenn das Gerät im Geräte-Manager nicht aufgelistet wird, liegt dies möglicherweise daran, dass es an einen nicht unterstützten USB3-Controller angeschlossen ist. 

Für das Azure Kinect DK unter **Windows** stellen **Intel, Texas Instruments (TI)** und **Renesas** die einzigen *unterstützten Hostcontroller* dar. Das Azure Kinect SDK auf Windows-Plattformen baut auf einer einheitlichen Container-ID auf, und es muss USB 2.0- und 3.0-Geräte abdecken, damit das SDK die Geräte für Tiefe, Farbe und Audio finden kann, die sich physisch im gleichen Gerät befinden. Unter Linux werden die meisten Hostcontroller unterstützt, da diese Plattform weniger die Container-ID als vielmehr Geräteseriennummern verwendet. 

Das Thema der USB-Hostcontroller wird sogar noch komplizierter, wenn auf einem PC mehrere Hostcontroller installiert sind. Bei gemischter Verwendung von Hostcontrollern können für Benutzer Probleme auftreten, bei denen manche Anschlüsse ordnungsgemäß und andere überhaupt nicht funktionieren. Je nachdem, wie die Anschlüsse mit dem Gehäuse verbunden sind, können bei allen frontseitigen Anschlüssen Probleme mit Azure Kinect auftreten.

**Windows:** Um zu ermitteln, welchen Hostcontroller Sie verwenden, müssen Sie den Geräte-Manager öffnen

1. Ansicht > Geräte nach Typ 
2. Wählen Sie bei verbundenem Azure Kinect „Kameras“> „Azure Kinect 4K Camera“ aus
3. Ansicht > Geräte nach Verbindung

![Problembehandlung für USB-Anschlüsse](./media/resources/usb-troubleshooting.png)

Um besser zu verstehen, welcher USB-Anschluss auf Ihrem PC verbunden ist, wiederholen Sie diese Schritte für jeden USB-Anschluss, während Sie Azure Kinect DK mit verschiedenen USB-Anschlüssen auf dem PC verbinden.

## <a name="depth-camera-auto-powers-down"></a>Die Tiefenkamera schaltet sich automatisch aus

Der Laser, der von der Tiefenkamera zum Berechnen der Tiefendaten von Bildern verwendet wird, hat nur eine begrenzte Lebensdauer. Um die Lebensdauer der Laser zu maximieren, erkennt die Tiefenkamera, wenn keine Tiefeninformationen verbraucht werden. Die Tiefenkamera fährt herunter, wenn das Gerät mehrere Minuten lang streamt, der Host-PC die Daten aber nicht liest. Dies wirkt sich auch auf die Synchronisierung mehrerer Geräte aus, bei der untergeordnete Geräte in einem Status gestartet werden, bei dem ein Streaming von der Tiefenkamera erfolgt und Tiefeninformationen vom Mastergerät aktiv für das Synchronisieren von Aufzeichnungen verwendet werden. Um dieses Problem in Aufzeichnungsszenarien mit mehreren Geräten zu vermeiden, achten Sie darauf, dass das Mastergerät maximal eine Minute nach dem Starten des ersten untergeordneten Geräts gestartet wird. 

## <a name="using-body-tracking-sdk-with-unreal"></a>Verwenden des Body Tracking SDK mit Unreal

Um das Body Tracking SDK mit Unreal zu verwenden, achten Sie darauf, `<SDK Installation Path>\tools` zur Umgebungsvariable `PATH` hinzuzufügen und `dnn_model_2_0.onnx` und `cudnn64_7.dll` nach `Program Files/Epic Games/UE_4.23/Engine/Binaries/Win64` zu kopieren.

## <a name="next-steps"></a>Nächste Schritte

[Weitere Supportinformationen](support.md)