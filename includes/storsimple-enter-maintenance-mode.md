---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d35f0ef783a2c48f8211657bc8829635c19495aa
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178118"
---
#### <a name="to-enter-maintenance-mode"></a>So wechseln Sie in den Wartungsmodus
1. Wählen Sie im Menü der seriellen Konsole Option 1 aus, d.h. die **Anmeldung mit Vollzugriff**.
2. Geben Sie das Kennwort ein. Das Standardkennwort lautet **Password1**.
3. Geben Sie an der Eingabeaufforderung Folgendes ein:
   
     `Enter-HcsMaintenanceMode`
4. Es wird eine Warnmeldung angezeigt, die Sie darüber informiert, dass durch den Wartungsmodus alle E/A-Anforderungen unterbrochen werden und die Verbindung mit dem klassischen Azure-Portal getrennt wird. Sie werden aufgefordert, dies zu bestätigen. Geben Sie **J** ein, um in den Wartungsmodus zu wechseln.
   
    Beide Controller werden neu gestartet. Wenn der Neustart abgeschlossen ist, wird eine andere Meldung angezeigt, die besagt, dass das Gerät in den Wartungsmodus versetzt wird.

