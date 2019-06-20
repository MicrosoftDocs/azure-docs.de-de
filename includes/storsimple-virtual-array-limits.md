---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 658fd9178495f14274c85eab2129c9dcd3be7693
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178122"
---
| **Begrenzungsbezeichner** | **Begrenzung** | **Kommentare** |
| --- | --- | --- |
| Gesamtkapazität (einschließlich Cloud) |Bis zu 64 TB pro virtuellem Gerät |Sie können ein Failover eines vollständigen virtuellen StorSimple-Arrays in ein anderes leeres Array durchführen. Wenn Sie versuchen, die Wiederherstellung auf demselben Gerät durchzuführen, sollten Sie sich vergewissern, dass auf dem Gerät ausreichend Speicherplatz zum Durchführen dieses Vorgangs vorhanden ist. Nachdem 32 TB überschritten wurden, können Sie keine Wiederherstellung auf dasselbe Gerät mehr durchführen. |
| Maximale Anzahl von Anmeldeinformationen für das Speicherkonto pro Gerät |1 | |
| Maximale Anzahl von Volumes/Freigaben |16 | |
| Mindestgröße einer mehrstufigen Freigabe |500 GB | |
| Mindestgröße eines mehrstufigen Volumes |500 GB | |
| Maximale Größe einer mehrstufigen Freigabe |20 TB | |
| Maximale Größe eines mehrstufigen Volumes |5 TB | |
| Mindestgröße einer lokalen Freigabe |50 GB | |
| Mindestgröße eines lokalen Volumes |50 GB | |
| Maximale Größe einer lokalen Freigabe |2 TB | |
| Maximale Größe eines lokalen Volumes |200 GB | |
| Maximale Anzahl von iSCSI-Verbindungen von Initiatoren |512 | |
| Maximale Anzahl von Zugriffssteuerungsdatensätzen pro Gerät |64 | |
| Maximale Anzahl von Sicherungen, die vom virtuellen Gerät im Ordner *.backups* für den Dateiserver gespeichert werden |5 |Dazu zählen die zuletzt geplanten (von der Standardsicherungsrichtlinie generierten) und manuelle Sicherungen. |
| Maximale Anzahl von geplanten Sicherungen, die vom Gerät gespeichert werden |55 |30 tägliche Sicherungen<br>12 monatliche Sicherungen<br>13 jährliche Sicherungen |
| Maximale Anzahl von manuellen Sicherungen, die vom Gerät gespeichert werden |45 | |
| Maximale Anzahl von Dateien pro Freigabe für einen Dateiserver |1 Mio. |Beim Ausführen einer Gerätewiederherstellung verhalten sich die Wiederherstellungszeiten proportional zur Anzahl von Dateien auf allen Freigaben des Geräts. |
| Maximale Anzahl von Dateien pro Volume für einen iSCSI-Server |1 Mio. | |
| Maximale Anzahl von Dateien pro Virtual Array |4 Millionen | |
| Wiederherstellungszeit für Wiederherstellen |Schnelle Wiederherstellung |Die Wiederherstellung basiert auf dem Wärmebild und ist abhängig von der Volumegröße.<br>Sicherungsvorgänge können stattfinden, während ein Wiederherstellungsvorgang ausgeführt wird. |

