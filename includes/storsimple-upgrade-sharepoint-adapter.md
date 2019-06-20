---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: b2dec95e0258933b50d4437f1cb317639b62883d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178134"
---
### <a name="upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-storsomple-adapter-for-sharepoint"></a>Upgraden von SharePoint 2010 auf SharePoint 2013 und Installieren des StorSimple-Adapters für SharePoint
> [!IMPORTANT]
> Alle Dateien, die zuvor per RBS an einen externen Speicher verschoben wurden, sind erst wieder verfügbar, wenn das Upgrade abgeschlossen und das RBS-Feature wieder aktiviert ist. Führen Sie Aktualisierungen oder Neuinstallationen während eines geplanten Wartungsfensters durch, um die Auswirkungen für Benutzer gering zu halten.
> 
> 

#### <a name="to-upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-adapter"></a>So führen Sie ein Upgrade von SharePoint 2010 auf SharePoint 2013 mit anschließender Adapterinstallation aus
1. Notieren Sie sich in der SharePoint 2010-Farm den BLOB-Speicherpfad für die externalisierten BLOBs und die Inhaltsdatenbanken, für die RBS aktiviert ist. 
2. Installieren und konfigurieren Sie die neue SharePoint 2013-Farm. 
3. Verschieben Sie Datenbanken, Anwendungen und Websitesammlungen aus der SharePoint 2010-Farm in die neue SharePoint 2013-Farm. Eine Anleitung finden Sie in der [Übersicht über die Verfahren beim Upgrade auf SharePoint 2013](https://technet.microsoft.com/library/cc262483.aspx).
4. Installieren Sie in der neuen Farm den StorSimple-Adapter für SharePoint. Die Vorgehensweise wird unter [Installieren des StorSimple-Adapters für SharePoint](#install-the-storsimple-adapter-for-sharepoint) beschrieben.
5. Aktivieren Sie RBS unter Verwendung der Informationen, die Sie in Schritt 1 notiert haben, für die gleichen Inhaltsdatenbanken, und geben Sie den BLOB-Speicherpfad an, der auch in der SharePoint 2010-Installation verwendet wurde. Die Vorgehensweise finden Sie unter [Konfigurieren von RBS](#configure-rbs) . Nach Abschluss dieses Schritts kann von der neuen Farm aus auf zuvor externalisierte Dateien zugegriffen werden. 

### <a name="upgrade-the-storsimple-adapter-for-sharepoint"></a>Upgraden des StorSimple-Adapters für SharePoint
> [!IMPORTANT]
> Dieses Upgrade sollte aus folgenden Gründen in einem geplanten Wartungsfenster ausgeführt werden:
> 
> * Zuvor externalisierte Inhalte sind erst nach der Neuinstallation des Adapters wieder verfügbar.
> * Alle Inhalte, die nach der Deinstallation der vorherigen Version des StorSimple-Adapters für SharePoint und vor der Installation der neuen Version an die Website hochgeladen werden, werden in der Inhaltsdatenbank gespeichert. Diese Inhalte müssen nach dem Installieren des neuen Adapters auf das StorSimple-Gerät verschoben werden. Sie können das Microsoft PowerShell-Cmdlet `RBS Migrate()` aus SharePoint für das Migrieren der Inhalte verwenden. Weitere Informationen finden Sie unter [Migrieren von Inhalten in den und aus dem Remote-BLOB-Speicher (Remote BLOB Storage, RBS) (SharePoint Foundation 2010)](https://technet.microsoft.com/library/ff628255.aspx). 
> 
> 

#### <a name="to-upgrade-the-storsimple-adapter-for-sharepoint"></a>So führen Sie ein Upgrade des StorSimple-Adapters für SharePoint aus
1. Deinstallieren Sie die vorherige Version des StorSimple-Adapters für SharePoint.
   
   > [!NOTE]
   > Dadurch wird RBS automatisch für die Inhaltsdatenbanken deaktiviert. Bereits vorhandene BLOBs bleiben allerdings auf dem StorSimple-Gerät erhalten. Da RBS deaktiviert ist und die BLOBs nicht wieder in die Inhaltsdatenbanken migriert wurden, sind Anforderungen für diese BLOBs nicht erfolgreich. 
   > 
   > 
2. Installieren Sie den StorSimple-Adapter für SharePoint. Der neue Adapter erkennt automatisch die Inhaltsdatenbanken, die zuvor mit RBS verwendet wurden, und verwendet die vorherigen Einstellungen.

