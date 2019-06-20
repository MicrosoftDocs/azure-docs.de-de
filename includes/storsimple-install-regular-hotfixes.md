---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8cc5dbb907c342b766cebe6da36cf580ddac5e2c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178123"
---
#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>So installieren Sie regelmäßige Hotfixes über Windows PowerShell für StorSimple
1. Stellen Sie eine Verbindung mit der seriellen Gerätekonsole her. Weitere Informationen finden Sie unter [Schritt 1: Herstellen einer Verbindung mit der seriellen Konsole](../articles/storsimple/storsimple-update-device.md#step1).
2. Wählen Sie im Menü der seriellen Konsole Option 1 aus, d.h. die **Anmeldung mit Vollzugriff**. Geben Sie das Kennwort ein. Das Standardkennwort lautet **Password1**.
3. Geben Sie an der Eingabeaufforderung Folgendes ein:
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > Dieser Befehl gilt nur für regelmäßige Hotfixes. Dieser Befehl muss nur auf einem Controller ausgeführt werden, es werden jedoch beide Controller aktualisiert.
    > Möglicherweise werden Sie während des Updatevorgangs ein Failover des Controllers bemerken. Dieses Failover wirkt sich jedoch nicht auf die Systemverfügbarkeit oder den Betrieb aus.

4. Geben Sie bei entsprechender Aufforderung den Pfad zur Netzwerkfreigabe mit den Hotfix-Dateien ein.
5. Sie werden aufgefordert, diesen Schritt zu bestätigen. Geben Sie **J** ein, um mit der Installation des Hotfixes fortzufahren.

