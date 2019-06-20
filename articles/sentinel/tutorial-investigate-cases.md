---
title: Untersuchen von Fällen mit Azure Sentinel Preview | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Fälle mit Azure Sentinel untersucht werden.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a493cd67-dc70-4163-81b8-04a9bc0232ac
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: d1da180d3b30b57ca2b69985bf3d0261b8d70c39
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65205521"
---
# <a name="tutorial-investigate-cases-with-azure-sentinel-preview"></a>Tutorial: Untersuchen von Fällen mit Azure Sentinel Preview

> [!IMPORTANT]
> Azure Sentinel befindet sich momentan in der Public Preview.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dieses Tutorial unterstützt Sie bei der Gefahrenerkennung mit Azure Sentinel.

Nachdem Sie [Ihre Datenquellen mit Azure Sentinel verbunden](quickstart-onboard.md) haben, möchten Sie gewarnt werden, wenn etwas Verdächtiges geschieht. Damit Sie dies tun können, ermöglicht Azure Sentinel es Ihnen, erweiterte Warnungsregeln zu erstellen, die Fälle generieren, die Sie zuweisen und verwenden können, um Anomalien und Bedrohungen für Ihre Umgebung gründlich untersuchen zu können. 

> [!div class="checklist"]
> * Erstellen von Fällen
> * Untersuchen von Fällen
> * Reagieren auf Bedrohungen

## <a name="investigate-cases"></a>Untersuchen von Fällen

Ein Fall kann mehrere Warnungen enthalten. Es ist eine Aggregation aller relevanten Beweise für eine bestimmte Untersuchung. Ein Fall wird auf Grundlage von Warnungen erstellt, die Sie auf der Seite **Analyse** angegeben haben. Die Eigenschaften, die zu den Warnungen gehören, z. B. Schweregrad und Status, werden auf Fallebene festgelegt. Nachdem Azure Sentinel die Informationen dazu hat, welche Bedrohungsarten Sie suchen und wie Sie diese finden, können Sie erkannte Bedrohungen überwachen, indem Sie Fälle untersuchen. 

1. Wählen Sie **Cases** (Fälle) aus. Auf der Seite **Cases** (Fälle) werden Sie darüber informiert, wie viele Fälle vorhanden sind, wie viele offen sind, für wie viele Sie **In progress** (In Arbeit) festgelegt haben, und wie viele geschlossen wurden. Für jeden Fall wird angezeigt, wann er aufgetreten ist, sowie der Fallstatus. Beurteilen Sie mithilfe des Schweregrads, welche Fälle zuerst bearbeitet werden. Klicken Sie auf der Seite **Cases** (Fälle) auf die Registerkarte **Warnungen**, um alle zu einem Fall gehörenden Warnungen anzeigen zu lassen. Entitäten, die Sie früher als Teil des Falls zugeordnet haben, können Sie sich in der Registerkarte **Entitäten** ansehen.  Sie können die Fälle nach Bedarf filtern, z. B. nach Status oder Schweregrad. Wenn Sie sich die Registerkarte **Cases** (Fälle) ansehen, werden Ihnen offene Fälle angezeigt, die Warnungen enthalten, die von Ihren auf der Seite **Analyse** festgelegten Erkennungsregeln ausgelöst wurden. Im oberen Bereich werden aktive Fälle angezeigt, neue Fälle sowie Fälle, die sich in Arbeit befinden. Sie können auch eine Übersicht über alle Fälle nach Schweregrad geordnet anzeigen lassen.

   ![Warnungsdashboard](./media/tutorial-investigate-cases/cases.png)

2. Klicken Sie auf einen bestimmten Fall, um mit der Untersuchung zu beginnen. Rechts sehen Sie detaillierte Informationen zum Fall, einschließlich seines Schweregrads und einer Zusammenfassung der Anzahl an Entitäten, die auf Grundalge Ihrer Zuordnung im Fall involviert sind. Jeder Fall hat eine eindeutige ID. Der Schweregrad eines Falls wird gemäß der zum Fall gehörenden Warnungen mit dem höchsten Schweregrad bestimmt.  

1. Wenn Sie sich mehr Details zu den Warnungen und Entitäten des Falls ansehen möchten, klicken Sie auf der Fallseite auf **View full details** (Vollständige Details anzeigen), und prüfen Sie die relevanten Registerkarten, in denen die Fallinformationen zusammengefasst sind.  Die vollständige Fallansicht fasst alle Beweise in der Warnung zusammen, die zugeordneten Warnungen und Entitäten.

1. Prüfen Sie in der Registerkarte **Warnungen** die Warnung selbst, z. B. wann sie ausgelöst wurde, und um wie viel sie den von Ihnen festgelegten Schwellenwert überschreitet. Sie können sich alle relevanten Informationen zur Warnung ansehen: die Abfrage, die die Warnung ausgelöst hat, die Anzahl der Ergebnisse, die pro Abfrage zurückgegeben wurde, und die Möglichkeit, Playbooks für die Warnungen auszuführen. Wenn Sie noch detaillierte Informationen zum Fall erhalten möchten, klicken Sie auf die Zahl der Treffer. Dadurch wird die Abfrage geöffnet, die die Ergebnisse generiert hat, und die Ergebnisse, die die Warnung in Log Analytics ausgelöst haben.

3. In der Registerkarte **Entitäten** können Sie sich alle Entitäten ansehen, die Sie als Teil der Warnungsregeldefinition zugeordnet haben. 

4. Wenn Sie einen Fall aktiv untersuchen, empfiehlt es sich, den Fallstatus auf **In Arbeit** festzulegen, bis Sie ihn schließen. Sie können den Fall auch schließen. Hierbei zeigt der Status **closed resolved** (geschlossen, aufgelöst) an, dass ein Vorfall bearbeitet wurde, während der Status **closed dismissed** (geschlossen, verworfen) anzeigt, dass Fälle nicht bearbeitet werden müssen. Sie müssen Erklärungen abgeben, warum genau Sie einen Fall schließen.

5. Fälle können einem bestimmten Benutzer zugewiesen werden. Jedem Fall können Sie einen Besitzer zuweisen, indem Sie das Feld **Besitzer** festlegen. Alle Fälle sind zu Beginn nicht zugewiesen. Sie können die Fälle nach Ihrem Namen filtern, um alle Fälle anzeigen zu lassen, deren Besitzer Sie sind. 

5. Klicken Sie auf **Untersuchen**, um die Untersuchungszuordnung und das Ausmaß der Sicherheitsverletzung mit Schritten zur Abwehr anzuzeigen. 



## <a name="respond-to-threats"></a>Reagieren auf Bedrohungen

In Azure Sentinel stehen Ihnen zwei Hauptoptionen zur Verfügung, um mithilfe von Playbooks auf Bedrohungen zu reagieren. Sie können festlegen, dass ein Playbook automatisch ausgeführt wird, wenn eine Warnung ausgelöst wird, oder Sie können ein Playbook manuell als Reaktion auf eine Warnung ausführen.

- Bei der Konfiguration des Playbooks können Sie festlegen, dass es automatisch ausgeführt wird, wenn eine Warnung ausgelöst wird. 

- Innerhalb der Warnung können Sie ein Playbook manuell ausführen, indem Sie auf **Playbooks anzeigen** klicken und dann ein Playbook auswählen, das ausgeführt werden soll.




## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie eine Einführung erhalten, wie Fälle mithilfe von Azure Sentinel untersucht werden. Fahren Sie mit dem [Tutorial: Einrichten automatisierter Reaktionen auf Bedrohungen in der Vorschauversion von Azure Sentinel](tutorial-respond-threats-playbook.md) fort.
> [!div class="nextstepaction"]
> [Reagieren Sie auf Bedrohungen](tutorial-respond-threats-playbook.md), um Ihre Maßnahmen zur Reaktion auf Bedrohungen zu automatisieren.

