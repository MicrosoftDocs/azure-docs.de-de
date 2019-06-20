---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 720288aff462b0590bb9da509096a9305b9b6cc7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178126"
---
#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>So installieren Sie Wartungsmodus-Updates über Windows PowerShell für StorSimple
1. Wenn Sie dies noch nicht getan haben, greifen Sie auf die serielle Konsole des Geräts zu, und wählen Sie Option 1, d.h. die **Anmeldung mit Vollzugriff**. 
2. Geben Sie das Kennwort ein. Das Standardkennwort lautet **Password1**.
3. Geben Sie an der Eingabeaufforderung Folgendes ein:
   
     `Get-HcsUpdateAvailability` 
4. Sie werden benachrichtigt, wenn Updates verfügbar sind und ob diese Updates mit oder ohne Unterbrechungen installiert werden können. Um Updates anzuwenden, durch die der Betrieb unterbrochen wird, müssen Sie das Gerät in den Wartungsmodus versetzen. Anweisungen dazu finden Sie unter [Schritt 2: Wechseln in den Wartungsmodus](../articles/storsimple/storsimple-update-device.md#step2).
5. Wenn sich Ihr Gerät im Wartungsmodus befindet, geben Sie an der Eingabeaufforderung folgenden Befehl ein: `Start-HcsUpdate`
6. Sie werden aufgefordert, diesen Schritt zu bestätigen. Nachdem Sie die Updates bestätigt haben, werden sie auf dem Controller installiert, auf den Sie gerade zugreifen. Nachdem die Updates installiert wurden, wird der Controller neu gestartet. 
7. Überwachen Sie den Status der Aktualisierungen. Geben Sie Folgendes ein:
   
    `Get-HcsUpdateStatus`
   
    Wenn `RunInProgress` `True` lautet, ist das Update noch nicht abgeschlossen. Wenn `RunInProgress``False` lautet, ist das Update abgeschlossen.  
8. Wurde der aktuelle Controller nach der Installation der Aktualisierung neu gestartet, stellen Sie eine Verbindung zu dem anderen Controller her und führen Sie die Schritte 1 bis 6 durch.
9. Nachdem beide Controller aktualisiert wurden, beenden Sie den Wartungsmodus. Anweisungen dazu finden Sie unter [Schritt 4: Beenden des Wartungsmodus](../articles/storsimple/storsimple-update-device.md#step4).

