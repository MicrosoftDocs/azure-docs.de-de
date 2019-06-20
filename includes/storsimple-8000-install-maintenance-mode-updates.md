---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 560c9c177bfa693580979101e5b9343fcff7fe40
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178287"
---
### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Installieren von Wartungsmodus-Updates über Windows PowerShell für StorSimple

Wenn Sie Wartungsmodus-Updates auf einem StorSimple-Gerät anwenden, werden alle E/A-Anforderungen angehalten. Dienste wie NVRAM (Non-Volatile Random Access Memory) oder der Clusterdienst werden beendet. Beide Controller werden neu gestartet, wenn Sie diesen Modus starten oder beenden. Wenn Sie diesen Modus beenden, werden alle Dienste fortgesetzt und funktionieren fehlerfrei. (Dies kann einige Minuten dauern.)

> [!IMPORTANT]
> * Vergewissern Sie sich im Azure-Portal vor dem Starten des Wartungsmodus, dass beide Gerätecontroller fehlerfrei funktionieren. Wenn der Controller nicht fehlerfrei funktioniert, wenden Sie sich für die nächsten Schritte [an den Microsoft-Support](../articles/storsimple/storsimple-8000-contact-microsoft-support.md).
> * Im Wartungsmodus müssen Sie zunächst einen und dann den anderen Controller aktualisieren.

1. Verwenden Sie PuTTY, um eine Verbindung mit der seriellen Konsole herzustellen. Ausführliche Anweisungen dazu finden Sie unter [Verwenden von PuTTy zum Herstellen einer Verbindung mit der seriellen Konsole](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). Drücken Sie bei der Eingabeaufforderung die **Eingabetaste**. Wählen Sie Option 1 aus: **Anmeldung mit Vollzugriff**.

2. Um den Controller in den Wartungsmodus zu versetzen, geben Sie Folgendes ein:
    
    `Enter-HcsMaintenanceMode`

    Beide Controller werden im Wartungsmodus neu gestartet.

3. Installieren Sie die Wartungsmodus-Updates. Geben Sie Folgendes ein:

    `Start-HcsUpdate`

    Sie werden aufgefordert, diesen Schritt zu bestätigen. Nachdem Sie die Updates bestätigt haben, werden sie auf dem Controller installiert, auf den Sie gerade zugreifen. Nachdem die Updates installiert wurden, wird der Controller neu gestartet.

4. Überwachen Sie den Status der Aktualisierungen. Melden Sie sich beim Peercontroller an, da der aktuelle Controller aktualisiert wird und keine anderen Befehle verarbeiten kann. Geben Sie Folgendes ein:

    `Get-HcsUpdateStatus`

    Wenn `RunInProgress` `True` lautet, ist das Update noch nicht abgeschlossen. Wenn `RunInProgress``False` lautet, ist das Update abgeschlossen.

5. Nachdem die Firmwareupdates für Datenträger erfolgreich installiert wurden und der aktualisierte Controller neu gestartet wurde, überprüfen Sie die Firmwareversion des Datenträgers. Geben Sie auf dem aktualisierten Controller Folgendes ein:

    `Get-HcsFirmwareVersion`
   
    Die erwarteten Datenträger-Firmwareversionen lauten: `XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`

6. Beenden Sie den Wartungsmodus. Geben Sie den folgenden Befehl für jeden Gerätecontroller ein:

    `Exit-HcsMaintenanceMode`

    Beim Beenden des Wartungsmodus werden die Controller neu gestartet.

7. Kehren Sie zum Azure-Portal zurück. Es kann bis zu 24 Stunden dauern, bis die Installation der Wartungsmodus-Updates im Portal angezeigt wird.