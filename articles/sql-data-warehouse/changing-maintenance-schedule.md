---
title: Azure-Wartungszeitpläne (Vorschau) | Microsoft-Dokumentation
description: Wartungszeitpläne ermöglichen es Kunden, die notwendigen geplanten Wartungsereignisse in ihre Planungen einzubeziehen, mit denen der Azure SQL Data Warehouse-Dienst neue Features, Upgrades und Patches einführt.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 04/03/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 4bc1867f0dd18a101b2e764970de4e1904b960e0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60680419"
---
# <a name="change-a-maintenance-schedule"></a>Ändern des Wartungszeitplans 

## <a name="portal"></a>Portal
Ein Wartungszeitplan kann jederzeit aktualisiert oder geändert werden. Falls die ausgewählte Instanz gerade einen aktiven Wartungszyklus durchläuft, werden die Einstellungen gespeichert. Sie werden dann im nächsten identifizierten Wartungszeitraum angewendet. Weitere Informationen zum Überwachen Ihrer Data Warehouse-Daten während eines aktiven Verwaltungsereignisses finden Sie [hier](https://docs.microsoft.com/azure/service-health/resource-health-overview). 

Zur Verwendung von Wartungszeitplänen müssen Sie zwei Wartungsfenster innerhalb eines Zeitraums von sieben Tagen auswählen. Jedes Wartungsfenster kann zwischen drei und acht Stunden lang sein. Innerhalb eines Wartungsfensters kann die Wartung zu einem beliebigen Zeitpunkt erfolgen. Außerhalb eines Zeitfensters ist dagegen eine vorherige Benachrichtigung erforderlich. Darüber hinaus geht kurzzeitig die Konnektivität verloren, während der Dienst neuen Code in Ihrem Datawarehouse bereitstellt. 

## <a name="identifying-the-primary-and-secondary-windows"></a>Angeben des primären und sekundären Fensters

Primäre und sekundäre Fenster müssen jeweils separate Tagesbereiche besitzen. Ein Beispiel wäre etwa ein primäres Fenster von Dienstag bis Donnerstag und ein sekundäres Fenster von Samstag bis Sonntag.

Führen Sie die folgenden Schritte aus, um den Wartungszeitplan für Ihr Data Warehouse zu ändern:
1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2.  Wählen Sie das Data Warehouse aus, das Sie aktualisieren möchten. Die Seite wird auf dem Blatt „Übersicht“ geöffnet. 
3.  Klicken Sie auf dem Übersichtsblatt auf den Link **Maintenance Schedule (preview) summary** (Wartungszeitplan (Vorschauversion) – Zusammenfassung), um die Seite mit den Wartungszeitplaneinstellungen zu öffnen. Alternativ können Sie auch im Ressourcenmenü auf der linken Seite auf **Wartungszeitplan** klicken.  

    ![Optionen auf dem Blatt „Übersicht“](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. Geben Sie mithilfe der Optionen im oberen Seitenbereich die bevorzugte Zeitspanne (in Tagen) für Ihr primäres Wartungsfenster an. Dadurch bestimmen Sie, ob Ihr primäres Fenster Werktage oder Wochenendtage abdeckt. Die Dropdownwerte werden auf der Grundlage Ihrer Auswahl aktualisiert. Während der Vorschauphase unterstützen einige Regionen ggf. noch nicht alle verfügbaren Optionen für **Tag**.

   ![Blatt „Wartungseinstellungen“](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. Wählen Sie über die Dropdown-Listenfelder Ihr bevorzugtes primäres und sekundäres Wartungsfenster aus:
   - **Tag**: Der bevorzugte Tag, an dem die Wartungsarbeiten innerhalb des ausgewählten Zeitfensters durchgeführt werden sollen.
   - **Startzeit**: Die bevorzugte Startzeit für das Wartungsfenster.
   - **Zeitfenster**: Die bevorzugte Dauer des Zeitfensters.

   Der Bereich **Zusammenfassung des Zeitplans** am unteren Rand des Blatts wird anhand der ausgewählten Werte aktualisiert. 
  
6. Wählen Sie **Speichern** aus. Eine Meldung bestätigt, dass Ihr neuer Zeitplan jetzt aktiv ist. 

   Wenn Sie einen Zeitplan in einer Region speichern, in der keine Wartungszeitpläne unterstützt werden, wird die folgende Meldung angezeigt. Ihre Einstellungen werden gespeichert und angewendet, sobald das Feature auch in Ihrer Region verfügbar ist.    

   ![Meldung zur regionalen Verfügbarkeit](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>Nächste Schritte
- [Weitere Informationen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) zu Webhookaktionen für Protokollwarnungsregeln
- [Weitere Informationen](https://docs.microsoft.com/azure/service-health/service-health-overview) zu Azure Service Health


