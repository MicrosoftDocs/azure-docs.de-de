---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 45b2ac73ffcb4b777572f9ad62d8aae0e8277e1c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95562051"
---
> [!NOTE]
> Wenn Sie den StorSimple-Adapter für die SharePoint-RBS-Konfiguration ändern möchten, müssen Sie mit einem Benutzerkonto angemeldet sein, das der Domänenadministratorgruppe angehört. Darüber hinaus müssen Sie die Konfigurationsseite in einem Browser öffnen, der auf dem gleichen Host ausgeführt wird wie die Zentraladministration.
> 
> 

#### <a name="to-configure-rbs"></a>So konfigurieren Sie RBS
1. Öffnen Sie die SharePoint-Seite „Zentraladministration“, und navigieren Sie zu **Systemeinstellungen**. 
2. Klicken Sie im Abschnitt **Azure StorSimple** auf **StorSimple-Adapter konfigurieren**.
   
    ![Konfigurieren des StorSimple-Adapters](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. Gehen Sie auf der Seite **StorSimple-Adapter konfigurieren** wie folgt vor:
   
   1. Vergewissern Sie sich, dass das Kontrollkästchen **Bearbeitungspfad aktivieren** aktiviert ist.
   2. Geben Sie im Textfeld den UNC-Pfad des BLOB-Speichers ein.
      
      > [!NOTE]
      > Das BLOB-Speichervolume muss auf einem auf dem StorSimple-Gerät konfigurierten iSCSI-Volume gehostet werden.

   3. Klicken Sie unter jeder Inhaltsdatenbank, die Sie für Remotespeicher konfigurieren möchten, auf die Schaltfläche **Aktivieren** .
      
      > [!NOTE]
      > Der BLOB-Speicher muss freigegeben und für Web-Front-End (WFE)-Server erreichbar sein, und das für die SharePoint-Serverfarm konfigurierte Benutzerkonto muss Zugriff auf die Freigabe haben.
      
      ![Aktivieren des RBS-Anbieters](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      Beim Aktivieren oder Deaktivieren von RBS erscheint die folgende Meldung:
      
      ![Konfigurieren des StorSimple-Adapters (aktivieren/deaktivieren)](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. Klicken Sie auf die Schaltfläche **Aktualisieren** , um die Konfiguration anzuwenden. Wenn Sie auf die Schaltfläche **Aktualisieren** klicken, wird der Status der RBS-Konfiguration auf allen WFE-Servern aktualisiert, und RBS wird in der gesamten Farm aktiviert. Die folgende Meldung wird angezeigt.
      
      ![Adapterkonfigurationsmeldung](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > Wenn Sie RBS für eine SharePoint-Farm konfigurieren, die mehr als 200 Datenbanken enthält, tritt auf der Webseite „SharePoint-Zentraladministration“ unter Umständen ein Timeout auf. Aktualisieren Sie die Seite, falls dies passiert. Dies wirkt sich nicht auf den Konfigurationsprozess aus.

4. Überprüfen Sie die Konfiguration:
   
   1. Melden Sie sich bei der SharePoint-Zentraladministrationswebsite an, und navigieren Sie zur Seite **StorSimple-Adapter konfigurieren**.
   2. Vergewissern Sie sich, dass die Konfigurationsdetails den eingegebenen Einstellungen entsprechen. 
5. Vergewissern Sie sich, dass RBS ordnungsgemäß funktioniert:
   
   1. Laden Sie ein Dokument an SharePoint hoch. 
   2. Navigieren Sie zum konfigurierten UNC-Pfad. Vergewissern Sie sich, dass die RBS-Verzeichnisstruktur erstellt wurde und das hochgeladene Objekt enthält.
6. (Optional) Mit dem in SharePoint enthaltenen Microsoft RBS-PowerShell-Cmdlet `Migrate()` können Sie vorhandene BLOB-Inhalte zum StorSimple-Gerät migrieren. Weitere Informationen finden Sie unter [Migrieren von Inhalten zu oder aus RBS in SharePoint 2013][6] bzw. [Migrieren von Inhalten in den und aus dem Remoteblobspeicher (SharePoint Foundation 2010)][7].
7. (Optional) In Testinstallationen können Sie sich wie folgt vergewissern, dass die BLOBs aus der Inhaltsdatenbank verschoben wurden: 
   
   1. Starten Sie SQL Management Studio.
   2. Führen Sie die Abfrage „ListBlobsInDB_2010.sql“ oder „ListBlobsInDB_2013.sql“ wie folgt aus:
      
      ```
      **ListBlobsInDB_2013.sql**
      
        USE WSS_Content
        GO
      
        SELECT DocStreams.DocId,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               DocStreams.RbsId,
               TimeLastModified
      
        FROM DocStreams
      
             INNER JOIN AllDocs ON DocStreams.DocId = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      
      **ListBlobsInDB_2010.sql**
      
        USE WSS_Content
        GO
      
        SELECT AllDocStreams.Id,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               RbsId,
               TimeLastModified
        FROM AllDocStreams
      
             INNER JOIN AllDocs ON AllDocStreams.Id = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      ```
      
      Wenn RBS ordnungsgemäß konfiguriert wurde, enthält die Spalte „SizeOfContentInDB“ für jedes hochgeladene Objekt, das erfolgreich mit RBS externalisiert wurde, einen NULL-Wert.
8. (Optional) Nachdem Sie RBS konfiguriert und alle BLOB-Inhalte auf das StorSimple-Gerät verschoben haben, können Sie die Inhaltsdatenbank auf das Gerät verschieben. Wenn Sie die Inhaltsdatenbank verschieben möchten, empfehlen wir, den Inhaltsdatenbankspeicher auf dem Gerät als primäres Volume zu konfigurieren. Wenden Sie dann die bewährten Methoden für die SQL Server-Migration an, um die Inhaltsdatenbank zum StorSimple-Gerät zu migrieren. 
   
   > [!NOTE]
   > Das Verschieben der Inhaltsdatenbank auf das Gerät wird nur bei der 8000er Serie von StorSimple unterstützt. (Bei der 5000er und 7000er Serie wird diese Funktion nicht unterstützt).
   
   Wenn Sie BLOBs und die Inhaltsdatenbank auf dem StorSimple-Gerät in separaten Volumes speichern, sollten Sie diese im gleichen Volumecontainer konfigurieren. Dadurch wird sichergestellt, dass sie gemeinsam gesichert werden.
   
   > [!WARNING]
   > Wenn Sie RBS nicht aktiviert haben, ist es nicht ratsam, die Inhaltsdatenbank auf das StorSimple-Gerät zu verschieben. Dies ist eine nicht getestete Konfiguration.
   
9. Fahren Sie mit dem nächsten Schritt fort: [Konfigurieren der automatischen Speicherbereinigung](#configure-garbage-collection).

[6]: /SharePoint/administration/migrate-content-into-or-out-of-rbs
[7]: /previous-versions/office/sharepoint-foundation-2010/ff628255(v=office.14)