---
title: Verwalten von Warnungen und Status intelligenter Gruppen
description: Verwalten des Status der Warnung und der intelligenten Gruppeninstanzen
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: 2aa521b0552b60e5a875a5f46ab9887c6e5b6e3e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60919660"
---
# <a name="manage-alert-and-smart-group-states"></a>Verwalten von Warnungen und Status intelligenter Gruppen
Warnungen in Azure Monitor verfügen jetzt über einen [Warnungsstatus und eine Überwachungsbedingung](https://aka.ms/azure-alerts-overview), und ähnlich verfügen intelligente Gruppen über einen [Status für intelligente Gruppen](https://aka.ms/smart-groups). Statusänderungen werden jetzt im Verlauf erfasst, der der jeweiligen Warnung oder intelligenten Gruppe zugeordnet ist. Dieser Artikel führt Sie durch den Prozess der Statusänderung, sowohl für Warnungen als auch für intelligente Gruppen.

## <a name="change-the-state-of-an-alert"></a>Ändern des Status einer Warnung
1. Sie können den Status einer Warnung auf verschiedene Weise ändern: 
    * Aktivieren Sie auf der Seite „Alle Warnungen“ das Kontrollkästchen neben den Warnungen, deren Status Sie ändern möchten, und klicken Sie auf „Status ändern“.   
    ![Überwachung](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * Auf der Seite „Warnungsdetails“ für eine bestimmte Warnungsinstanz können Sie auf „Status ändern“ klicken.   
    ![Überwachung](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * Auf der Seite „Warnungsdetails“ für eine bestimmte Warnungsinstanz können Sie im Bereich „Intelligente Gruppe“ das Kontrollkästchen neben den gewünschten Warnungen aktivieren.    
    ![Überwachung](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * Auf der Seite zu den Details der intelligenten Gruppe können Sie in der Liste der Mitgliedswarnungen das Kontrollkästchen neben den Warnungen aktivieren, deren Status Sie ändern möchten, und auf „Status ändern“ klicken, um den Status zu ändern.   
    ![Überwachung](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. Wenn Sie auf „Status ändern“ klicken, wird ein Popup geöffnet, in dem Sie den Status auswählen (Neu/Bestätigt/Geschlossen) und bei Bedarf einen Kommentar eingeben können.   
![Überwachung](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. Nach diesem Vorgang wird die Statusänderung im Verlauf der jeweiligen Warnung aufgezeichnet. Dies kann angezeigt werden, indem die entsprechende Detailseite geöffnet und der Verlaufsbereich überprüft wird.    
![Überwachung](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Ändern des Status einer intelligenten Gruppe
1. Sie können den Status einer intelligenten Gruppe auf verschiedene Weise ändern:
    1. Aktivieren Sie auf der Listenseite „Intelligente Gruppe“ das Kontrollkästchen neben den intelligenten Gruppen, deren Status Sie ändern möchten, und klicken Sie auf „Status ändern“.  
    ![Überwachung](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. Auf der Seite mit den Details zur intelligenten Gruppe können Sie auf „Status ändern“ klicken.        
    ![Überwachung](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. Wenn Sie auf „Status ändern“ klicken, wird ein Popup geöffnet, in dem Sie den Status auswählen (Neu/Bestätigt/Geschlossen) und bei Bedarf einen Kommentar eingeben können. 
![Überwachung](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  Durch das Ändern des Zustands einer intelligenten Gruppe wird nicht der Zustand der einzelnen Mitgliedswarnungen geändert.

1. Nach diesem Vorgang wird die Statusänderung im Verlauf der jeweiligen intelligenten Gruppe aufgezeichnet. Dies kann angezeigt werden, indem die entsprechende Detailseite geöffnet und der Verlaufsbereich überprüft wird.     
![Überwachung](./media/alerts-managing-alert-states/state-sg-history.jpg)

