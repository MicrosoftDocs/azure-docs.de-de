---
title: include file
description: include file
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 07/02/2018
ms.author: shants
ms.custom: include file
ms.openlocfilehash: 50a215175d7305834a64b7e0cfbc153431b10b7c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178070"
---
## <a name="view-vms-scheduled-for-maintenance-in-the-portal"></a>Anzeigen der virtuellen Computer mit geplanter Wartung im Portal

Nachdem eine geplante Wartung angesetzt wurde, können Sie die Liste der virtuellen Computer anzeigen, die von der anstehenden geplanten Wartung betroffen sind. 

Sie können im Azure-Portal nach VMs suchen, für die eine Wartung geplant ist.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Klicken Sie im linken Navigationsbereich auf **Virtuelle Computer**.

3. Klicken Sie im Bereich mit den virtuellen Computern auf die Schaltfläche **Spalten**, um die Liste mit den verfügbaren Spalten zu öffnen.

4. Wählen Sie die folgenden Spalten aus, und fügen Sie sie hinzu:

   **Wartung:** Zeigt den Wartungsstatus für den virtuellen Computer an. Mögliche Werte:
      
      | Wert | BESCHREIBUNG |
      |-------|-------------|
      | Jetzt starten | Der virtuelle Computer befindet sich im Zeitfenster für die Self-Service-Wartung, in dem Sie die Wartung selbst initiieren können. Informationen zum Starten der Wartung für Ihren virtuellen Computer finden Sie weiter unten. | 
      | Geplant | Die Wartung für den virtuellen Computer ist geplant und kann nicht von Ihnen initiiert werden. Informationen zum Wartungszeitfenster erhalten Sie, indem Sie in dieser Ansicht „Wartung – geplantes Fenster“ auswählen oder auf den virtuellen Computer klicken. | 
      | Bereits aktualisiert | Ihr virtueller Computer wurde bereits aktualisiert, und zu diesem Zeitpunkt ist keine weitere Aktion erforderlich. | 
      | Versuchen Sie es später noch mal. | Sie haben erfolglos versucht, die Wartung zu initiieren. Die Option für die Self-Service-Wartung kann zu einem späteren Zeitpunkt verwendet werden. | 
      | Jetzt erneut versuchen | Sie können eine zuvor nicht erfolgreiche selbst initiierte Wartung wiederholen. | 
      | - | Ihre VM ist nicht Teil einer geplanten Wartung. |
      

   **Wartung – Self-Service-Fenster:** Zeigt das Zeitfenster an, in dem Sie die Wartung für Ihre virtuellen Computer selbst starten können.
   
   **Wartung – geplantes Fenster:** Zeigt das Zeitfenster an, in dem Azure auf Ihrem virtuellen Computer die Wartung abschließt. 



## <a name="notification-and-alerts-in-the-portal"></a>Benachrichtigungen und Warnungen im Portal

Azure sendet eine E-Mail an die Gruppe der Abonnementbesitzer und -mitbesitzer, um sie über den Zeitplan für eine geplante Wartung zu informieren. Sie können Azure-Aktivitätsprotokollwarnungen erstellen, um weitere Empfänger und Kanäle hinzuzufügen. Weitere Informationen finden Sie unter [Erstellen von Aktivitätsprotokollwarnungen zu Dienstbenachrichtigungen](../articles/azure-monitor/platform/alerts-activity-log-service-notifications.md).

Stellen Sie sicher, dass Sie den **Ereignistyp** als **Planmäßige Wartung** und **Dienste** als **VM-Skalierungsgruppe** und/oder **VM** einstellen.
    
    
## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Starten der Wartung für Ihren virtuellen Computer über das Portal

In der Ansicht mit den VM-Details können Sie auch weitere wartungsbezogene Details anzeigen.  
Am oberen Rand der VM-Detailansicht wird ein neues Benachrichtigungsmenüband angezeigt, wenn Ihr virtueller Computer von einer Welle für eine geplante Wartung betroffen ist. Darüber hinaus wird eine neue Option zum Starten der Wartung hinzugefügt (sofern möglich). 


Klicken Sie auf die Wartungsbenachrichtigung, um die Wartungsseite mit weiteren Details zur geplanten Wartung anzuzeigen. Von dort aus können Sie die Wartung für Ihren virtuellen Computer **starten**.

Nach dem Start der Wartung wird Ihr virtueller Computer gewartet, und der Wartungsstatus wird innerhalb weniger Minuten mit dem entsprechenden Ergebnis aktualisiert.

Wenn Sie das Self-Service-Zeitfenster verpasst haben, wird das Zeitfenster, in dem Ihre VM von Azure gewartet wird, weiterhin angezeigt. 
