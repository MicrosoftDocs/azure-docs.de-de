---
title: Verwenden des Dashboards für ein Classroom-Lab in Azure Lab Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie das Dashboard für ein Classroom-Lab in Azure Lab Services verwenden.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a91e1649d913956219ddcf192ab315a245b32eae
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434599"
---
# <a name="dashboard-for-labs"></a>Dashboard für Labs
Dieser Artikel beschreibt die Dashboardansicht eines Classroom-Labs in Azure Lab Services. 

![Bildschirmaufnahme der Dashboardansicht eines Classroom-Labs in Azure Lab Services](./media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>Kachel „Kosten und Abrechnung“
Diese Kachel bietet die folgenden Details zur Kostenschätzung:

| Einstellung | value | 
| ------- | ----- | 
| Kontingentstunden | Die maximale Anzahl von Stunden, die ein Benutzer die VM außerhalb der geplanten Stunden verwenden kann. |
| Geplante Stunden | Die Stunden, die auf der Grundlage des im Lab festgelegten Zeitplans anfallen. Dieser Wert ist nur verfügbar, wenn für alle Zeitplanereignisse Von-bis-Datum festgelegt ist. |
| Stunden/Benutzer | Die Summe der Kontingentstunden und geplanten Stunden. |
| Maximale Anzahl Benutzer | Die maximale Anzahl von Benutzern im Lab basierend auf allen zu beanspruchenden virtuellen Computern. |
| Stunden x Benutzer | Stunden/Benutzer multipliziert mit der Anzahl der Benutzer. |
| Angepasstes Kontingent | Die Summe der Kontingentstunden, die bestimmten Benutzern hinzugefügt werden. |
| Gesamtstunden * USD/Stunde | Die Kosten pro Stunde basierend auf der ausgewählten VM-Größe. Dieser Wert basiert auf dem regulären Preis für nutzungsbasierte Bezahlung. |
| Geschätzte Gesamtkosten | Dies ist der maximale Preis für dieses Lab basierend auf den aktuellen Einstellungen. |

## <a name="template-tile"></a>Kachel „Vorlage“
Auf dieser Kachel werden die folgenden Informationen angezeigt:

- Das Datum, an dem die Vorlage erstellt wurde. 
- Das Datum, an dem die Vorlage zuletzt veröffentlicht wurde. 

Enthält auch einen Link, um zur Seite **Vorlage** zu navigieren, auf der Sie die [Vorlagen-VM für die Klasse verwalten](how-to-create-manage-template.md) können. 

## <a name="virtual-machine-pool-tile"></a>Kachel „Pool des virtuellen Computers“

Auf dieser Kachel werden die folgenden Informationen angezeigt:

- Anzahl der virtuellen Computer, die Studenten zugewiesen werden (Benutzer)
- Die Anzahl der virtuellen Computer, die noch keinen Studenten zugewiesen werden.

Enthält auch einen Link, um zur Seite **Pool des virtuellen Computers** zu navigieren, auf der Sie den [Pool der virtuellen Computer](how-to-set-virtual-machine-passwords.md) im Lab verwalten können. 

## <a name="users-tile"></a>Kachel „Benutzer“

Auf dieser Kachel werden die folgenden Informationen angezeigt:

- Anzahl der Benutzer, die bei der Klasse registriert sind
- Die Anzahl der Benutzer, die dem Lab hinzugefügt, aber nicht bei der Klasse registriert sind. 

Enthält auch einen Link, um zur Seite **Benutzer** zu navigieren, auf der Sie die [Benutzer für das Lab verwalten](how-to-configure-student-usage.md) können. 

## <a name="schedules-tile"></a>Kachel „Zeitpläne“
Auf der Kachel werden die aktuellen geplanten Ereignisse für das Lab angezeigt. Enthält außerdem ist ein Link, um zur Seite **Zeitplan** zu navigieren, auf dem Sie [Zeitpläne erstellen und verwalten](how-to-create-schedules.md) können. Die Kachel zeigt Details für nur zwei geplante Ereignisse und die Anzahl der verbleibenden geplanten Ereignisse für das Lab an. 

![Geplante Ereignisse](./media/use-dashboard/scheduled-events.png)

