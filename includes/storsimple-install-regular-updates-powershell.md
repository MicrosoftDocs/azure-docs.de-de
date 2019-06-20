---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: dc50f94ae9b207961a71480c2fc172e88db79cf4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178124"
---
#### <a name="to-install-regular-updates-via-windows-powershell-for-storsimple"></a>So installieren Sie regelmäßige Updates über Windows PowerShell für StorSimple
1. Öffnen Sie die serielle Konsole des Geräts, und wählen Sie Option 1, **Anmeldung mit Vollzugriff**, aus. Geben Sie das Kennwort ein. Das Standardkennwort lautet *Password1*. 
2. Geben Sie an der Eingabeaufforderung Folgendes ein:
   
     `Get-HcsUpdateAvailability`
   
    Sie werden benachrichtigt, wenn Updates verfügbar sind und ob diese Updates mit oder ohne Unterbrechungen installiert werden können.
3. Geben Sie an der Eingabeaufforderung Folgendes ein:
   
     `Start-HcsUpdate`
   
    Der Updatevorgang wird gestartet.

> [!IMPORTANT]
> * Dieser Befehl gilt nur für regelmäßige Updates. Dieser Befehl muss nur auf einem Controller ausgeführt werden, es werden jedoch beide Controller aktualisiert. 
> * Möglicherweise werden Sie während des Updatevorgangs ein Failover des Controllers bemerken. Dieses Failover wirkt sich jedoch nicht auf die Systemverfügbarkeit oder den Betrieb aus.
> 
> 

