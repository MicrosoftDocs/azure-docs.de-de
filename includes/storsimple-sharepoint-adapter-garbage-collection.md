---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 0b5d9deacdd4266da30f17c95b6e575a652d2f76
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155869"
---
Dieses Verfahren umfasst Folgendes:

1. [Vorbereiten der Ausführung von „Maintainer.exe“](#to-prepare-to-run-the-maintainer)
2. [Vorbereiten der Inhaltsdatenbank und des Papierkorbs für die sofortige Löschung verwaister BLOBs](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs)
3. [Ausführen von „Maintainer.exe“](#to-run-the-maintainer)
4. [Wiederherstellen der Einstellungen von Inhaltsdatenbank und Papierkorb](#to-revert-the-content-database-and-recycle-bin-settings)

#### <a name="to-prepare-to-run-the-maintainer"></a>So bereiten Sie die Ausführung von Maintainer vor
1. Öffnen Sie auf dem Front-End-Webserver die SharePoint 2013-Verwaltungsshell als Administrator.
2. Navigieren Sie zum folgenden Ordner: *Startlaufwerk*:\Programme\Microsoft SQL Remote Blob Storage 10.50\Maintainer\..
3. Benennen Sie **Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config** in **web.config** um.
4. Entschlüsseln Sie die Datei „web.config“ mithilfe von `aspnet_regiis -pdf connectionStrings` .
5. Fügen Sie der entschlüsselten Datei „web.config“ unter dem Knoten `connectionStrings` die Verbindungszeichenfolge für Ihre SQL Server-Instanz und den Namen der Inhaltsdatenbank hinzu. Siehe folgendes Beispiel.
   
    `<add name=”RBSMaintainerConnectionWSSContent” connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. Verwenden Sie `aspnet_regiis –pef connectionStrings` , um die Datei „web.config“ erneut zu verschlüsseln. 
7. Benennen Sie die Datei „Web.config“ in „Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config“ um. 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>So bereiten Sie die Inhaltsdatenbank und den Papierkorb für die sofortige Löschung verwaister BLOBs vor
1. Führen Sie auf dem Computer mit SQL Server in SQL Management Studio die folgenden Updateabfragen für die Zielinhaltsdatenbank aus: 
   
       `use WSS_Content`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`
2. Bearbeiten Sie auf dem Front-End-Webserver unter **Zentraladministration** die **Allgemeinen Webanwendungseinstellungen** für die gewünschte Inhaltsdatenbank, um den Papierkorb vorübergehend zu deaktivieren. Im Zuge dieser Aktion wird auch der Papierkorb für alle dazugehörigen Websitesammlungen geleert. Klicken Sie auf **Zentraladministration** -> **Anwendungsverwaltung** -> **Webanwendungen (Webanwendungen verwalten)** -> **SharePoint - 80** -> **Allgemeine Anwendungseinstellungen**. Legen Sie den **Status des Papierkorbs** auf **AUS** fest.
   
    ![Allgemeinen Webanwendungseinstellungen](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>So führen Sie Maintainer aus
* Verwenden Sie auf dem Front-End-Webserver in der SharePoint 2013-Verwaltungsshell Folgendes, um Maintainer auszuführen:
  
      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > Für StorSimple wird derzeit nur der `GarbageCollection`-Vorgang unterstützt. Beachten Sie außerdem, dass bei den für „Microsoft.Data.SqlRemoteBlobs.Maintainer.exe“ ausgegebenen Parametern die Groß-/Kleinschreibung beachtet werden muss. 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>So stellen Sie die Einstellungen von Inhaltsdatenbank und Papierkorb wieder her
1. Führen Sie auf dem Computer mit SQL Server in SQL Management Studio die folgenden Updateabfragen für die Zielinhaltsdatenbank aus:
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. Bearbeiten Sie auf dem Front-End-Webserver unter **Zentraladministration** die **Allgemeinen Webanwendungseinstellungen** für die gewünschte Inhaltsdatenbank, um den Papierkorb wieder zu aktivieren. Klicken Sie auf **Zentraladministration** -> **Anwendungsverwaltung** -> **Webanwendungen (Webanwendungen verwalten)** -> **SharePoint - 80** -> **Allgemeine Anwendungseinstellungen**. Legen Sie den „Status des Papierkorbs“ auf **EIN**fest.

