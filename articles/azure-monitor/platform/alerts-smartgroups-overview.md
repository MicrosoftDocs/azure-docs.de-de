---
title: Intelligente Gruppen
description: Intelligente Gruppen sind Aggregationen von Warnungen, die Ihnen helfen, Störungen bei Warnungen zu reduzieren.
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: e0bef0fc4f4b61add24c243af0dac64933ad5bab
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60346320"
---
# <a name="smart-groups"></a>Intelligente Gruppen
Eine häufige Herausforderung beim Umgang mit Warnungen ist das Durchsuchen der nicht relevanten Informationen, um die relevanten Warnungen zu ermitteln – intelligente Gruppen sind als Lösung für dieses Problem vorgesehen.  

Intelligente Gruppen werden automatisch mithilfe von Machine Learning-Algorithmen erstellt, um zusammengehörige Warnungen zu kombinieren, die ein einzelnes Problem darstellen.  Wenn eine Warnung erstellt wird, fügt der Algorithmus diese basierend auf Informationen wie bisherigen Mustern, Ähnlichkeit von Eigenschaften und Ähnlichkeit der Struktur einer neuen intelligenten Gruppe oder einer vorhandenen intelligenten Gruppe hinzu. Wenn z. B. „% CPU“ auf mehreren virtuellen Computern in einem Abonnement gleichzeitig Spitzenwerte erreicht, die zu vielen einzelnen Warnungen führen, und wenn solche Warnungen in der Vergangenheit immer wieder zusammen aufgetreten sind, werden diese Warnungen voraussichtlich in einer einzelnen intelligenten Gruppe zusammengefasst, was auf eine mögliche gemeinsame Ursache hindeutet. Das bedeutet, dass intelligente Gruppen für Personen, die Fehlerbehebungen für Warnungen durchführen, nicht nur die Möglichkeit haben, die nicht relevanten Informationen zu reduzieren, indem sie verwandte Warnungen als einzelne aggregierte Einheit verwalten, sondern sie auch zu möglichen gemeinsamen Ursachen für ihre Warnungen führen.

Derzeit berücksichtigt der Algorithmus nur Warnungen vom gleichen Überwachungsdienst innerhalb eines Abonnements. Intelligente Gruppen können aufgrund dieser Konsolidierung das Warnungsaufkommen um bis zu 99 % reduzieren. Der Grund für die Einbindung von Warnungen in eine Gruppe wird auf der Detailseite der intelligenten Gruppe angezeigt.

Sie können ähnlich wie bei Warnungen die Details der intelligenten Gruppen anzeigen und den Status festlegen. Jede Warnung ist nur Mitglied einer einzigen intelligenten Gruppe. 

## <a name="smart-group-state"></a>Zustand der intelligenten Gruppe
Der Zustand der intelligenten Gruppe ist ein ähnliches Konzept wie der Warnungszustand, mit dem Sie den Auflösungsprozess auf der Ebene einer intelligenten Gruppe verwalten können. Ähnlich wie beim Warnungszustand befindet sich eine intelligente Gruppe beim Erstellen im Zustand **Neu**, der entweder in **Bestätigt** oder **Geschlossen** geändert werden kann.

Die folgenden Zustände werden für intelligente Gruppen unterstützt.

| Zustand | BESCHREIBUNG |
|:---|:---|
| Neu | Das Problem wurde gerade erkannt und noch nicht überprüft. |
| Bestätigt | Ein Administrator hat die intelligente Gruppe überprüft und mit deren Bearbeitung begonnen. |
| Geschlossen | Das Problem wurde gelöst. Nachdem eine intelligente Gruppe geschlossen wurde, können Sie diese erneut öffnen, indem Sie den Zustand ändern. |

[Erfahren Sie mehr über das Ändern des Zustands Ihrer intelligenten Gruppen](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  Durch das Ändern des Zustands einer intelligenten Gruppe wird nicht der Zustand der einzelnen Mitgliedswarnungen geändert.

## <a name="smart-group-details-page"></a>Seite „Details der intelligenten Gruppe“

Die Detailseite für die intelligente Gruppe wird angezeigt, wenn Sie auf eine intelligente Gruppe klicken. Sie enthält Details zur intelligenten Gruppe (einschließlich der Gründe ihre Erstellung) und ermöglicht Ihnen das Ändern des Status.
 
![Details der intelligenten Gruppe](media/alerts-smartgroups-overview/smart-group-detail.png)


Die Detailseite für die intelligente Gruppe enthält die folgenden Abschnitte.

| `Section` | BESCHREIBUNG |
|:---|:---|
| Alerts | Listet die einzelnen Warnungen auf, die in der intelligenten Gruppe enthalten sind. Klicken Sie auf eine Warnung, um deren Detailseite zu öffnen. |
| Verlauf | Listet die einzelnen Aktionen, die von der intelligenten Gruppe ausgeführt wurden, und alle daran vorgenommenen Änderungen auf. Dies ist derzeit auf Statusänderungen und Änderungen der Warnungsmitgliedschaft beschränkt. |

## <a name="smart-group-taxonomy"></a>Taxonomie von intelligenten Gruppen

Der Name einer intelligenten Gruppe ist der Name der ersten Warnung. Sie können eine intelligente Gruppe weder erstellen noch umbenennen.

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten intelligenter Gruppen](https://aka.ms/managing-smart-groups)
- [Ändern des Zustands von Warnungen und intelligenten Gruppen](https://aka.ms/managing-alert-smart-group-states)


