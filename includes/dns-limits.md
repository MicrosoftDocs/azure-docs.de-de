---
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/05/2020
ms.author: rohink
ms.openlocfilehash: 7011b92485c56187021c9043ba84bc85e448a98f
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94329484"
---
**Öffentliche DNS-Zonen**

| Resource | Begrenzung |
| --- | --- |
| Öffentliche DNS-Zonen pro Abonnement |250 <sup>1</sup> |
| Datensatzgruppen pro öffentlicher DNS-Zone |10.000 <sup>1</sup> |
| Datensätze pro Datensatzgruppe in öffentlicher DNS-Zone |20 |
| Anzahl von Aliasdatensätzen für eine einzelne Azure-Ressource |20|

<sup>1</sup> Wenden Sie sich an den Azure-Support, falls Sie diese Grenzwerte erhöhen müssen.

**Private DNS-Zonen**

| Resource | Begrenzung |
| --- | --- |
| Private DNS-Zonen pro Abonnement |1000|
| Datensatzgruppen pro privater DNS-Zone |25000|
| Datensätze pro Datensatzgruppe für private DNS-Zonen |20|
| Virtuelle Netzwerkverbindungen pro privater DNS-Zone |1000|
| Virtuelle Netzwerkverbindungen pro privater DNS-Zone mit aktivierter automatischer Registrierung |100|
| Anzahl von privaten DNS-Zonen, mit denen ein virtuelles Netzwerk mit aktivierter automatischer Registrierung verknüpft werden kann |1|
| Anzahl von privaten DNS-Zonen, mit denen ein virtuelles Netzwerk verknüpft werden kann |1000|
| Anzahl von DNS-Abfragen, die ein virtueller Computer pro Sekunde an den Azure DNS-Resolver senden kann |1000 <sup>1</sup> |
| Maximale Anzahl von DNS-Abfragen in der Warteschlange (ausstehende Antwort) pro virtuellem Computer |200 <sup>1</sup> |

<sup>1</sup> Diese Grenzwerte gelten nicht auf der Ebene des virtuellen Netzwerks, sondern für jeden einzelnen virtuellen Computer. DNS-Abfragen, die über diese Grenzwerte hinausgehen, werden verworfen.
