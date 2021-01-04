---
title: Verwalten von Bandbreitenzeitplänen in Azure Data Box Gateway | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie über das Azure-Portal Bandbreitenzeitpläne auf Ihrer Azure Data Box Gateway-Ressource verwalten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 55676128f482c8fb4dbdeab0523e4556f3957af7
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581008"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-gateway"></a>Verwalten von Bandbreitenzeitplänen auf Ihrer Azure Data Box Gateway-Ressource über das Azure-Portal  

In diesem Artikel erfahren Sie, wie Sie Benutzer auf Ihrer Azure Data Box Gateway-Ressource verwalten. Mithilfe von Bandbreitenzeitplänen können Sie die Nutzung der Netzwerkbandbreite durch mehrere tageszeitbasierte Zeitpläne konfigurieren. Diese Zeitpläne können auf die Upload- und Downloadvorgänge zwischen Ihrem Gerät und der Cloud angewendet werden.

Sie können die Bandbreitenzeitpläne für Data Box Gateway über das Azure-Portal hinzufügen, ändern oder löschen.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
>
> * Hinzufügen eines Zeitplans
> * Ändern eines Zeitplans
> * Löschen eines Zeitplans

## <a name="add-a-schedule"></a>Hinzufügen eines Zeitplans

Gehen Sie im Azure-Portal wie folgt vor, um einen Benutzer hinzuzufügen:

1. Navigieren Sie im Azure-Portal für Ihre Data Box Gateway-Ressource zu **Bandbreite**.
2. Klicken Sie im rechten Bereich auf **+ Zeitplan hinzufügen**.

    ![Klicken auf „Benutzer hinzufügen“](media/data-box-gateway-manage-bandwidth-schedules/add-schedule-1.png)

3. Gehen Sie unter **Zeitplan hinzufügen** wie folgt vor: 

   1. Geben Sie den **Starttag**, den **Endtag**, die **Startzeit** und die **Endzeit** des Zeitplans an. 
   2. Wenn der Zeitplan für den gesamten Tag gilt, können Sie die Option **Ganztägig** aktivieren. 
   3. **Bandbreitenrate** ist die Bandbreite in Megabit pro Sekunde (MBit/s), die Ihr Gerät bei Cloudvorgängen (Uploads und Downloads) verwendet. Geben Sie eine Zahl zwischen 1 und 1000 in dieses Feld ein. 
   4. Aktivieren Sie die Option **Unbegrenzt**, wenn Sie Uploads und Downloads nicht drosseln möchten. 
   5. Klicken Sie auf **Hinzufügen**.

      ![Klicken auf „Benutzer 2 hinzufügen“](media/data-box-gateway-manage-bandwidth-schedules/add-schedule-2.png)

3. Ein Zeitplan mit den angegebenen Parametern wird erstellt. Dieser Zeitplan wird dann im Portal in der Liste mit den Bandbreitenzeitplänen angezeigt.


## <a name="edit-schedule"></a>Bearbeiten des Zeitplans

Führen Sie die folgenden Schritte aus, um einen Bandbreitenzeitplan zu bearbeiten: 

1. Navigieren Sie im Azure-Portal zu Ihrer Data Box Gateway-Ressource und anschließend zu „Bandbreite“. 
2. Klicken Sie in der Liste mit den Bandbreitenzeitplänen auf einen Zeitplan, den Sie ändern möchten.

    ![Ändern eines Benutzers](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-1.png)

3. Nehmen Sie die gewünschten Änderungen vor, und speichern Sie sie.

    ![Ändern von Benutzer 2](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-2.png)

4. Nach der Änderung des Zeitplans wird die Liste mit den Zeitplänen aktualisiert, um den geänderten Zeitplan widerzuspiegeln.

    ![Ändern von Benutzer 3](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Löschen eines Zeitplans

Führen Sie die folgenden Schritte aus, um einen mit Ihrem Data Box Gateway-Gerät verknüpften Bandbreitenzeitplan zu löschen:

1. Navigieren Sie im Azure-Portal zu Ihrer Data Box Gateway-Ressource und anschließend zu **Bandbreite**.  

2. Wählen Sie in der Liste mit den Bandbreitenzeitplänen einen Zeitplan aus, den Sie löschen möchten. Klicken Sie mit der rechten Maustaste, um das Kontextmenü zu öffnen, und klicken Sie anschließend auf **Löschen**. 

   ![Löschen eines Benutzers](media/data-box-gateway-manage-bandwidth-schedules/delete-schedule-1.png)

3.  Nach der Löschung des Zeitplans wird die Liste mit den Zeitplänen aktualisiert.



## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Bandbreite verwalten](data-box-gateway-manage-bandwidth-schedules.md). 
