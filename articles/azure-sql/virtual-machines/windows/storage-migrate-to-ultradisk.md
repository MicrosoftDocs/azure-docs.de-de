---
title: Migrieren eines Protokolldatenträgers zu einem Ultra-Datenträger
description: Hier erfahren Sie, wie Sie Ihren Protokolldatenträger für SQL Server auf einem virtuellen Azure-Computer (Azure-VM) zu einem Azure Ultra-Datenträger migrieren, um von hoher Leistung und geringer Wartezeit zu profitieren.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 42d7760d25f6ab591c19889eb2159711d6de1b07
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356751"
---
# <a name="migrate-log-disk-to-ultra-disk"></a>Migrieren eines Protokolldatenträgers zu einem Ultra-Datenträger
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure Ultra-Datenträger bieten hohen Durchsatz, eine hohe IOPS-Rate sowie Datenträgerspeicher mit konsistent geringer Wartezeit für SQL Server auf einem virtuellen Azure-Computer (Azure-VM). 

In diesem Artikel erfahren Sie, wie Sie Ihren Protokolldatenträger zu SSD Ultra migrieren, um die Leistungsvorteile von Ultra-Disks zu nutzen. 

## <a name="back-up-database"></a>Sichern der Datenbank

Führen Sie eine [vollständige Sicherung](backup-restore.md) Ihrer Datenbank durch. 

## <a name="attach-disk"></a>Anfügen des Datenträgers

Fügen Sie den SSD Ultra-Datenträger an Ihren virtuellen Computer an, nachdem Sie die Ultra-Datenträgerkompatibilität für den virtuellen Computer aktiviert haben. 

Ultra-Datenträger werden für eine Teilmenge von VM-Größen und Regionen unterstützt. Vergewissern Sie sich vor den nächsten Schritten, dass Ultra-Datenträger für die Region, Zone und Größe Ihres virtuellen Computers unterstützt werden. Zum [Ermitteln der VM-Größe und Regionsverfügbarkeit](../../../virtual-machines/disks-enable-ultra-ssd.md#determine-vm-size-and-region-availability) können Sie die Azure-Befehlszeilenschnittstelle oder PowerShell verwenden. 

### <a name="enable-compatibility"></a>Aktivieren der Kompatibilität

Gehen Sie zum Aktivieren der Kompatibilität wie folgt vor:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrem virtuellen Computer. 
1. Beenden Sie den virtuellen Computer, und heben Sie die Zuordnung auf. 
1. Wählen Sie unter **Einstellungen** die Option **Datenträger** und anschließend **Zusätzliche Einstellungen** aus. 

   :::image type="content" source="media/storage-migrate-to-ultradisk/additional-disks-settings-azure-portal.png" alt-text="Auswählen zusätzlicher Einstellungen für Datenträger unter „Einstellungen“ im Azure-Portal":::

1. Wählen Sie für **Ultra-Datenträgerkompatibilität aktivieren** die Option **Ja** aus. 

   :::image type="content" source="../../../virtual-machines/media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png" alt-text="Screenshot mit der Option „Ja“.":::

1. Wählen Sie **Speichern** aus. 



### <a name="attach-disk"></a>Anfügen des Datenträgers

Verwenden Sie das Azure-Portal, um einen Ultra-Datenträger an Ihren virtuellen Computer anzufügen. Ausführliche Informationen finden Sie unter [Anfügen eines Ultra-Datenträgers](../../../virtual-machines/disks-enable-ultra-ssd.md#attach-an-ultra-disk).

Starten Sie Ihren virtuellen Computer über das Azure-Portal, nachdem Sie den Datenträger hinzugefügt haben. 



## <a name="format-disk"></a>Formatieren des Datenträgers

Stellen Sie eine Verbindung mit Ihrem virtuellen Computer her, und formatieren Sie Ihren Ultra-Datenträger.  

Gehen Sie zum Formatieren Ihres Ultra-Datenträgers wie folgt vor:

1. Stellen Sie eine RDP-Verbindung (Remotedesktopprotokoll) mit Ihrem virtuellen Computer her.
1. Verwenden Sie die [Datenträgerverwaltung](/windows-server/storage/disk-management/overview-of-disk-management), um den neu angefügten Ultra-Datenträger zu formatieren und zu partitionieren. 


## <a name="use-disk-for-log"></a>Verwenden des Datenträgers für das Protokoll

Konfigurieren Sie SQL Server für die Verwendung des neuen Protokolllaufwerks. Hierzu können Sie Transact-SQL (T-SQL) oder SQL Server Management Studio (SSMS) verwenden. Das als SQL Server-Dienstkonto verwendete Konto muss über Vollzugriff auf den neuen Protokolldatei-Speicherort verfügen. 

### <a name="configure-permissions"></a>Konfigurieren von Berechtigungen

1. Überprüfen Sie das von SQL Server verwendete Dienstkonto. Hierzu können Sie den SQL Server-Konfigurations-Manager oder „Services.msc“ verwenden.
1. Navigieren Sie zu Ihrem neuen Datenträger. 
1. Erstellen Sie einen oder mehrere Ordner für Ihre Protokolldatei. 
1. Klicken Sie mit der rechten Maustaste auf den Ordner, und wählen Sie **Eigenschaften** aus.
1. Erteilen Sie dem SQL Server-Dienstkonto auf der Registerkarte **Sicherheit** Vollzugriff. 
1. Wählen Sie **OK** aus, um Ihre Einstellungen zu speichern. 
1. Wiederholen Sie diese Vorgehensweise für jeden Ordner auf der Stammebene, der SQL-Daten enthalten soll. 

### <a name="use-new-log-drive"></a>Verwenden des neuen Protokolllaufwerks 

Verwenden Sie nach Erteilung der Berechtigung entweder Transact-SQL (T-SQL) oder SQL Server Management Studio (SSMS), um die Datenbank zu trennen und vorhandene Protokolldateien an den neuen Speicherort zu verschieben.

   > [!CAUTION]
   > Das Trennen der Datenbank bewirkt, dass die Datenbank offline geschaltet wird, Verbindungen geschlossen werden und ein Rollback für alle derzeit aktiven Transaktionen ausgeführt wird. Lassen Sie bei diesem Schritt Vorsicht walten, und führen Sie ihn während eines Wartungsfensters mit Downtime durch. 



# <a name="transact-sql-t-sql"></a>[Transact-SQL (T-SQL)](#tab/tsql)

Verwenden Sie T-SQL, um die vorhandenen Dateien an einen neuen Speicherort zu verschieben:

1. Stellen Sie in SQL Server Management Studio eine Verbindung mit Ihrer Datenbank her, und öffnen Sie ein Fenster vom Typ **Neue Abfrage**. 
1. Rufen Sie die vorhandenen Dateien und Speicherorte ab:

   ```sql
   USE AdventureWorks
   GO

   sp_helpfile
   GO
   ```

1. Trennen Sie die Datenbank: 

   ```sql
   USE master
   GO

   sp_detach_db 'AdventureWorks'
   GO
   ```

1. Verschieben Sie die Protokolldatei mithilfe des Datei-Explorers an den neuen Speicherort auf dem Ultra-Datenträger. 

1. Fügen Sie die Datenbank unter Angabe der neuen Dateispeicherorte an: 

   ```sql
    sp_attach_db 'AdventureWorks'
   'E:\Fixed_FG\AdventureWorks.mdf',
   'E:\Fixed_FG\AdventureWorks_2.ndf',
   'F:\New_Log\AdventureWorks_log.ldf'
   GO
   ```

Die Datenbank wird daraufhin mit dem Protokoll am neuen Speicherort online geschaltet. 



# <a name="sql-server-management-studio-ssms"></a>[SQL Server Management Studio (SSMS)](#tab/ssms)

Verwenden Sie SSMS, um die vorhandenen Dateien an einen neuen Speicherort zu verschieben:

1. Stellen Sie in SQL Server Management Studio (SSMS) eine Verbindung mit Ihrer Datenbank her. 
1. Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie **Eigenschaften** > **Dateien** aus. 
1. Notieren Sie sich den Pfad der vorhandenen Dateien. 
1. Wählen Sie **OK** aus, um das Dialogfeld zu schließen. 
1. Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie **Aufgaben** > **Trennen** aus. 
1. Befolgen Sie die Anweisungen des Assistenten, um die Datenbank zu trennen. 
1. Verschieben Sie die Protokolldatei mithilfe des Datei-Explorers manuell an den neuen Speicherort.
1. Fügen Sie die Datenbank in SQL Server Management Studio an:
   1. Klicken Sie im **Objekt-Explorer** mit der rechten Maustaste auf **Datenbanken**, und wählen Sie **Datenbank anfügen** aus. 
   1. Fügen Sie im Dialogfeld die einzelnen Dateien (einschließlich der Protokolldatei) ihrem neuen Speicherort hinzu. 
   1. Wählen Sie **OK** aus, um die Datenbank anzufügen. 

Die Datenbank wird daraufhin mit dem Protokoll am neuen Speicherort online geschaltet.

---


## <a name="next-steps"></a>Nächste Schritte

Unter [Leistungsrichtlinien für SQL Server auf Azure Virtual Machines](performance-guidelines-best-practices.md) finden Sie zusätzliche Einstellungen zur Verbesserung der Leistung. 

Eine Übersicht über SQL Server in Azure Virtual Machines finden Sie in den folgenden Artikeln:

- [Was ist SQL Server auf virtuellen Azure-Computern? (Windows)](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Übersicht über SQL Server auf virtuellen Azure-Computern (Linux)](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
