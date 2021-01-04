---
author: msftradford
ms.author: parkerra
ms.date: 11/20/2020
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 41c0a6292bf7369a30fbb1e4bd474e70ffaedcb6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999740"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>Konfigurieren der Raumanker-Cloudsitzung

Als Nächstes kümmern wir uns um die Konfiguration der Raumanker-Cloudsitzung. In der ersten Zeile legen wir den Sensoranbieter für die Sitzung fest. Ab jetzt werden alle Anker, die wir während der Sitzung erstellen, einer Gruppe von Sensormesswerten zugeordnet. Als Nächstes instanziieren wir Ermittlungskriterien für Geräte in der Nähe und führen eine Initialisierung durch, um eine Anpassung an die Anwendungsanforderungen vorzunehmen. Abschließend weisen wir die Sitzung an, beim Ermitteln von Ankern Sensordaten zu nutzen, indem wir einen „Watcher“ für unsere Kriterien für Geräte in der Nähe erstellen.