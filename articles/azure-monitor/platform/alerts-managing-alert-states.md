---
title: Verwalten von Warnungen und Status intelligenter Gruppen
description: Verwalten des Status der Warnung und der intelligenten Gruppeninstanzen
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.date: 09/24/2018
ms.openlocfilehash: fa1701c59f099f5222ab6454cc67b9dc20d1e7e7
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186676"
---
# <a name="manage-alert-and-smart-group-states"></a>Verwalten von Warnungen und Status intelligenter Gruppen

Warnungen in Azure Monitor verfügen jetzt über einen [Warnungsstatus und eine Überwachungsbedingung](./alerts-overview.md), und ähnlich verfügen intelligente Gruppen über einen [Status für intelligente Gruppen](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json). Statusänderungen werden jetzt im Verlauf erfasst, der der jeweiligen Warnung oder intelligenten Gruppe zugeordnet ist. Dieser Artikel führt Sie durch den Prozess der Statusänderung, sowohl für Warnungen als auch für intelligente Gruppen.

## <a name="change-the-state-of-an-alert"></a>Ändern des Status einer Warnung

1. Sie können den Status einer Warnung auf verschiedene Weise ändern: 
    * Aktivieren Sie auf der Seite „Alle Warnungen“ das Kontrollkästchen neben den Warnungen, deren Status Sie ändern möchten, und klicken Sie auf „Status ändern“.   
    ![Screenshot der Seite „Alle Warnungen“ mit ausgewählter Option „Status ändern“](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * Auf der Seite „Warnungsdetails“ für eine bestimmte Warnungsinstanz können Sie auf „Status ändern“ klicken.   
    ![Screenshot der Seite „Warnungsdetails“ mit ausgewählter Option „Warnungsstatus ändern“](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * Auf der Seite „Warnungsdetails“ für eine bestimmte Warnungsinstanz können Sie im Bereich „Intelligente Gruppe“ das Kontrollkästchen neben den gewünschten Warnungen aktivieren.    
    ![Screenshot der Seite „Warnungsdetails“ für die Heartbeat-Warnung, bei der einige Instanzen mit Häkchen versehen sind](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * Auf der Seite zu den Details der intelligenten Gruppe können Sie in der Liste der Mitgliedswarnungen das Kontrollkästchen neben den Warnungen aktivieren, deren Status Sie ändern möchten, und auf „Status ändern“ klicken, um den Status zu ändern.   
    ![Screenshot der Seite zu den Details der intelligenten Gruppe, auf der Warnungen zum Ändern des Status ausgewählt werden können](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. Wenn Sie auf „Status ändern“ klicken, wird ein Popup geöffnet, in dem Sie den Status auswählen (Neu/Bestätigt/Geschlossen) und bei Bedarf einen Kommentar eingeben können.   
![Screenshot des Dialogfelds zum Ändern des Warnungsstatus unter den Details](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. Nach diesem Vorgang wird die Statusänderung im Verlauf der jeweiligen Warnung aufgezeichnet. Dies kann angezeigt werden, indem die entsprechende Detailseite geöffnet und der Verlaufsbereich überprüft wird.    
![Screenshot des Verlaufs von Statusänderungen](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Ändern des Status einer intelligenten Gruppe
1. Sie können den Status einer intelligenten Gruppe auf verschiedene Weise ändern:
    1. Aktivieren Sie auf der Listenseite „Intelligente Gruppe“ das Kontrollkästchen neben den intelligenten Gruppen, deren Status Sie ändern möchten, und klicken Sie auf „Status ändern“.  
    ![Screenshot der Seite „Status ändern“ für intelligente Gruppen](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. Auf der Seite mit den Details zur intelligenten Gruppe können Sie auf „Status ändern“ klicken.        
    ![Screenshot der Seite mit den Details zur intelligenten Gruppe mit ausgewählter Option „Status der intelligenten Gruppe ändern“](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. Wenn Sie auf „Status ändern“ klicken, wird ein Popup geöffnet, in dem Sie den Status auswählen (Neu/Bestätigt/Geschlossen) und bei Bedarf einen Kommentar eingeben können. 
![Screenshot des Dialogfelds „Status ändern“ für die intelligente Gruppe](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  Durch das Ändern des Zustands einer intelligenten Gruppe wird nicht der Zustand der einzelnen Mitgliedswarnungen geändert.

1. Nach diesem Vorgang wird die Statusänderung im Verlauf der jeweiligen intelligenten Gruppe aufgezeichnet. Dies kann angezeigt werden, indem die entsprechende Detailseite geöffnet und der Verlaufsbereich überprüft wird.     
![Screenshot des Änderungsverlaufs für die intelligente Gruppe](./media/alerts-managing-alert-states/state-sg-history.jpg)