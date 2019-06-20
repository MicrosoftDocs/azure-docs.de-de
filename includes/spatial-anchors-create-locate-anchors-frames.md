---
ms.openlocfilehash: 10eb16dc1757255c77ed8967dc132f42660e070f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177794"
---
## <a name="provide-frames-to-the-session"></a>Bereitstellen von Rahmen für die Sitzung

Bei einer Sitzung mit Raumankern wird die Umgebung zugeordnet, in der sich der Benutzer gerade befindet. Auf diese Weise lässt sich ermitteln, wo sich Anker befinden. Mobile Plattformen (iOS und Android) erfordern einen nativen Aufruf des Kamerafeeds, um Rahmen aus der AR-Bibliothek Ihrer Plattform abzurufen. Im Gegensatz dazu scannt HoloLens die Umgebung ständig, sodass kein gesonderter Aufruf wie bei mobilen Plattformen erforderlich ist.