---
title: Wiederherstellen aus der Sicherung – Azure App Service
description: Hier erfahren Sie, wie Sie Ihre App auf der Grundlage einer Momentaufnahme wiederherstellen.
services: app-service
documentationcenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.topic: article
ms.date: 04/04/2018
ms.author: aelnably;nicking
ms.custom: seodec18
ms.openlocfilehash: 8d4290f1411749e2d8d3d27fbd792ceeeea47ef7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60851332"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Wiederherstellen einer App in Azure auf der Grundlage einer Momentaufnahme
In diesem Artikel erfahren Sie, wie Sie eine App in [Azure App Service](../app-service/overview.md) auf der Grundlage einer Momentaufnahme wiederherstellen. Mithilfe der Momentaufnahmen Ihrer App können Sie die App in einem vorherigen Zustand wiederherstellen. Die Sicherung von Momentaufnahmen muss nicht aktiviert werden, da die Plattform automatisch eine Momentaufnahme aller Apps speichert, um die Wiederherstellung von Daten zu ermöglichen.

Momentaufnahmen sind inkrementelle Schattenkopien und bieten im Vergleich zu regulären [Sicherungen](manage-backup.md) mehrere Vorteile:
- Keine Dateikopierfehler durch Dateisperren
- Keine Beschränkung der Speichergröße
- Keine Konfiguration erforderlich

Momentaufnahmebasierte Wiederherstellungen für Apps stehen ab dem Tarif **Premium** zur Verfügung. Informationen zum zentralen Hochskalieren der App finden Sie unter [Zentrales Hochskalieren einer App in Azure](web-sites-scale.md).

## <a name="limitations"></a>Einschränkungen

- Dieses Feature befindet sich derzeit in der Vorschauphase.
- Als Wiederherstellungsziel kann nur die gleiche App oder ein Slot verwendet werden, der zu dieser App gehört.
- Die Ziel-App oder der Zielslot wird während der Wiederherstellung beendet.
- Für die Wiederherstellung von Plattformdaten speichert App Service Momentaufnahmen für einen Zeitraum von drei Monaten.
- Es können nur Momentaufnahmen der letzten 30 Tage wiederhergestellt werden.
- App Services, die in einer App Service-Umgebung ausgeführt werden, unterstützen keine Momentaufnahmen.
 

## <a name="restore-an-app-from-a-snapshot"></a>Wiederherstellen einer App auf der Grundlage einer Momentaufnahme

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf der Seite **Einstellungen** Ihrer App auf **Sicherungen**, um die Seite **Sicherungen** anzuzeigen. Klicken Sie anschließend im Abschnitt **Momentaufnahme (Vorschau)** auf **Wiederherstellen**.
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. Wählen Sie auf der Seite **Wiederherstellen** die Momentaufnahme aus, die Sie wiederherstellen möchten.
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. Geben Sie unter **Wiederherstellungsziel** das Ziel für die App-Wiederherstellung an.
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Bei Verwendung von **Überschreiben** werden alle im aktuellen Dateisystem Ihrer App vorhandenen Daten gelöscht und überschrieben. Vergewissern Sie sich vor dem Klicken auf **OK**, dass Sie diesen Schritt wirklich ausführen möchten.
   > 
   > 
      
   > [!Note]
   > Aufgrund aktueller technischer Einschränkungen sind nur App-Wiederherstellungen in der gleichen Skalierungseinheit möglich. Diese Einschränkung wird in einer späteren Version aufgehoben.
   > 
   > 
   
    Sie können **Vorhandene App** auswählen, um einen Slot als Wiederherstellungsziel zu verwenden. Wenn Sie diese Option verwenden möchten, müssen Sie in Ihrer App bereits einen Slot erstellt haben.

4. Sie können auswählen, dass Ihre Websitekonfiguration wiederhergestellt werden soll.
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. Klicken Sie auf **OK**.
