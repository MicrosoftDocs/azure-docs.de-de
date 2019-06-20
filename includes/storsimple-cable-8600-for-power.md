---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9b9922602218280d58331a755ed0dfed7df96f40
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178127"
---
#### <a name="to-cable-your-device-for-power"></a>So verkabeln Sie das Gerät für die Stromversorgung
> [!NOTE]
> Zu beiden Gehäusen des StorSimple-Geräts gehören redundante Stromversorgungs- und Kühleinheiten (Power and Cooling Modules, PCMs). Für jedes Gehäuse müssen die PCMs installiert und mit unterschiedlichen Stromquellen verbunden sein, um Hochverfügbarkeit sicherzustellen.
> 
> 

1. Stellen Sie sicher, dass sich die Netzschalter aller PCMs in ausgeschalteter Position befinden.
2. Schließen Sie an beide PCMs im primären Gehäuse die Netzkabel an. Die Netzkabel sind im folgenden Verkabelungsdiagramm rot dargestellt.
3. Stellen Sie sicher, dass die beiden PCMs des primären Gehäuses separate Stromquellen verwenden.
4. Schließen Sie die Netzkabel über die Rack-PDUs an die Stromversorgung an, wie im Verkabelungsdiagramm dargestellt.
5. Wiederholen Sie die Schritte 2 bis 4 für das EBOD-Gehäuse.
6. Schalten Sie das EBOD-Gehäuse ein, indem Sie die Netzschalter der PCMs in die Position ON bringen.
7. Stellen Sie sicher, dass das EBOD-Gehäuse eingeschaltet ist, indem Sie überprüfen, ob die grünen LEDs an der Rückseite des EBOD-Controllers leuchten.
8. Schalten Sie das primäre Gehäuse ein, indem Sie die PCM-Schalter in die Position ON bringen.
9. Stellen Sie sicher, dass das System eingeschaltet ist, indem Sie überprüfen, ob die Controller-LEDs leuchten.
10. Vergewissern Sie sich, dass die Verbindung zwischen dem EBOD-Controller und dem Gerätecontroller aktiv ist, indem Sie überprüfen, ob die vier LEDs neben dem SAS-Anschluss am EBOD-Controller grün leuchten.
    
    > [!IMPORTANT]
    > Damit die Hochverfügbarkeit des Systems sichergestellt ist, empfiehlt es sich dringend, sich strikt an das im folgenden Diagramm dargestellte Stromverkabelungsschema zu halten.
    > 
    > 
    
    ![Stromverkabelung des 4 HE-Geräts](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **Stromverkabelung**
    
    | Bezeichnung | BESCHREIBUNG |
    |:--- |:--- |
    | 1 |Primäres Gehäuse |
    | 2 |PCM 0 |
    | 3 |PCM 1 |
    | 4 |Controller 0 |
    | 5 |Controller 1 |
    | 6 |EBOD-Controller 0 |
    | 7 |EBOD-Controller 1 |
    | 8 |EBOD-Gehäuse |
    | 9 |PDUs |

