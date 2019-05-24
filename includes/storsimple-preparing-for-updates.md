---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4e262c9e5bb88e77bc9c09853c06f4cdb41eedaa
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66149642"
---
## <a name="preparing-for-updates"></a>Vorbereitung auf Updates
Sie müssen die folgenden Schritte ausführen, bevor Sie das Update überprüfen und anwenden können:

1. Erstellen Sie eine Cloudmomentaufnahme der Gerätedaten.
2. Stellen Sie sicher, dass die festen Controller-IP-Adressen routingfähig sind und eine Verbindung mit dem Internet herstellen können. Diese festen IPs werden zum Anwenden von Updates auf Ihr Gerät verwendet. Sie können die Konnektivität testen, indem Sie von der Windows PowerShell-Schnittstelle des Geräts aus auf jedem Controller das folgende Cmdlet ausführen:
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network>`
   
    **Beispielausgabe für die Testverbindung, wenn feste IP-Adressen eine Verbindung mit dem Internet herstellen können**

        Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com

        Source      Destination     IPV4Address      IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200

        Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

        Source      Destination       IPV4Address    IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200

Nachdem Sie diese manuellen Vorabprüfungen erfolgreich abgeschlossen haben, können Sie mit dem Überprüfen und Installieren der Updates beginnen.

