---
title: Verwalten von Momentaufnahmen mithilfe von Azure NetApp Files | Microsoft-Dokumentation
description: In diesem Artikel wird das Erstellen, Verwalten und Verwenden von Momentaufnahmen mithilfe von Azure NetApp Files beschrieben.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/18/2020
ms.author: b-juche
ms.openlocfilehash: 03b7941385517fe694f0743194655a1b6a1c0e1e
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2020
ms.locfileid: "95253557"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Verwalten von Momentaufnahmen mithilfe von Azure NetApp Files

Azure NetApp Files unterstützt das Erstellen bedarfsgesteuerter Momentaufnahmen und das Verwenden von Momentaufnahmenrichtlinien zum Planen der automatischen Erstellung von Momentaufnahmen. Sie können auch eine Momentaufnahme auf einem neuen Volume wiederherstellen, eine einzelne Datei mithilfe eines Clients wiederherstellen oder ein vorhandenes Volume mithilfe einer Momentaufnahme wiederherstellen.

> [!NOTE] 
> Überlegungen zur Verwaltung von Momentaufnahmen bei der regionsübergreifenden Replikation finden Sie unter [Regionsübergreifende Replikation: Anforderungen und Überlegungen](cross-region-replication-requirements-considerations.md).

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Erstellen einer Momentaufnahme bei Bedarf für ein Volume

Sie können bei Bedarf Volumemomentaufnahmen erstellen. 

1.  Wechseln Sie zu dem Volume, für das Sie eine Momentaufnahme erstellen möchten. Klicken Sie auf **Momentaufnahmen**.

    ![Zu Momentaufnahmen navigieren](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Klicken Sie auf **+ Momentaufnahme hinzufügen**, um feine Momentaufnahme bei Bedarf für ein Volume zu erstellen.

    ![Momentaufnahme hinzufügen](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  Geben Sie im Fenster „Neue Momentaufnahme“ der neu erstellten Momentaufnahme einen Namen an.   

    ![Neue Momentaufnahme](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Klicken Sie auf **OK**. 

## <a name="manage-snapshot-policies"></a>Verwalten von Momentaufnahmenrichtlinien

Mithilfe von Momentaufnahmenrichtlinien können Sie planen, dass automatisch Volumemomentaufnahmen erstellt werden. Sie können eine Momentaufnahmenrichtlinie nach Bedarf auch ändern oder sogar löschen, wenn Sie sie nicht mehr benötigen.  

### <a name="register-the-feature"></a>Registrieren der Funktion

Die Funktion **Momentaufnahmenrichtlinie** steht derzeit als Vorschau zur Verfügung. Wenn Sie diese Funktion zum ersten Mal verwenden, müssen Sie sie zuerst registrieren. 

1. Registrieren Sie die Funktion: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

2. Überprüfen Sie den Status der Funktionsregistrierung: 

    > [!NOTE]
    > Der **RegistrationState** kann für bis zu 60 Minuten den Status `Registering` aufweisen, bevor der Wechsel in `Registered` erfolgt. Warten Sie, bis der Status **Registriert** lautet, bevor Sie fortfahren.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```
Sie können auch die [Azure CLI-Befehle](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` und `az feature show` verwenden, um das Feature zu registrieren und den Registrierungsstatus anzuzeigen. 

### <a name="create-a-snapshot-policy"></a>Erstellen einer Momentaufnahmenrichtlinie 

Eine Momentaufnahmenrichtlinie ermöglicht es Ihnen, die Häufigkeit der Erstellung von Momentaufnahmen in stündlichen, täglichen, wöchentlichen oder monatlichen Zyklen anzugeben. Außerdem müssen Sie die maximale Anzahl von Momentaufnahmen angeben, die für das Volume beibehalten werden soll.  

1.  Klicken Sie in der Ansicht für das NetApp-Konto auf **Momentaufnahmenrichtlinie**.

    ![Momentaufnahmenrichtliniennavigation](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  Legen Sie die Option „Richtlinienstatus“ im Fenster „Momentaufnahmenrichtlinie“ auf **Aktiviert** fest. 

3.  Klicken Sie auf die Registerkarte **Stündlich**, **Täglich**, **Wöchentlich** oder **Monatlich**, um Richtlinien für stündliche, tägliche, wöchentliche oder monatliche Momentaufnahmen zu erstellen. Bestimmen Sie die **Anzahl von Momentaufnahmen, die beibehalten werden sollen**.  

    Informationen zur maximalen Anzahl von Momentaufnahmen, die für ein Volume zulässig sind, finden Sie unter [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md). 

    Das folgende Beispiel zeigt die Konfiguration der Richtlinie für stündliche Momentaufnahmen. 

    ![Richtlinie für stündliche Momentaufnahme](../media/azure-netapp-files/snapshot-policy-hourly.png)

    Das folgende Beispiel zeigt die Konfiguration der Richtlinie für tägliche Momentaufnahmen.

    ![Richtlinie für tägliche Momentaufnahme](../media/azure-netapp-files/snapshot-policy-daily.png)

    Das folgende Beispiel zeigt die Konfiguration der Richtlinie für wöchentliche Momentaufnahmen.

    ![Richtlinie für wöchentliche Momentaufnahme](../media/azure-netapp-files/snapshot-policy-weekly.png)

    Das folgende Beispiel zeigt die Konfiguration der Richtlinie für monatliche Momentaufnahmen.

    ![Richtlinie für monatliche Momentaufnahme](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  Klicken Sie auf **Speichern**.  

Wiederholen Sie Schritt 3, wenn Sie zusätzliche Momentaufnahmenrichtlinien erstellen müssen.
Die von Ihnen erstellten Richtlinien werden auf der Seite „Momentaufnahmenrichtlinie“ angezeigt.

Wenn Sie möchten, dass die Richtlinie von einem Volume verwendet wird, müssen Sie [die Richtlinie auf das Volume anwenden](azure-netapp-files-manage-snapshots.md#apply-a-snapshot-policy-to-a-volume). 

### <a name="apply-a-snapshot-policy-to-a-volume"></a>Anwenden einer Momentaufnahmenrichtlinie auf ein Volume

Wenn Sie möchten, dass ein Volume eine von Ihnen erstellte Momentaufnahmenrichtlinie verwendet, müssen Sie die Richtlinie auf das Volume anwenden. 

Bei der regionsübergreifenden Replikation können Sie eine Momentaufnahmenrichtlinie nicht auf ein Zielvolume anwenden.  

1.  Navigieren Sie zur Seite **Volumes**, klicken Sie mit der rechten Maustaste auf das Volume, auf das Sie eine Momentaufnahmenrichtlinie anwenden möchten, und klicken Sie dann auf **Bearbeiten**.

    ![Rechtsklicken auf Volumes im Menü](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  Wählen Sie unter **Momentaufnahmenrichtlinie** im Fenster „Bearbeiten“ eine Richtlinie aus, die für das Volume verwendet werden soll.  Klicken Sie auf **OK**, um die Richtlinie anzuwenden.  

    ![Bearbeiten einer Momentaufnahmenrichtlinie](../media/azure-netapp-files/snapshot-policy-edit.png) 

### <a name="modify-a-snapshot-policy"></a>Ändern einer Momentaufnahmenrichtlinie 

Sie können eine vorhandene Momentaufnahmenrichtlinie ändern, um den Richtlinienstatus, die Häufigkeit der Momentaufnahmen (stündlich, täglich, wöchentlich oder monatlich) oder die Anzahl von Momentaufnahmen zu ändern, die aufbewahrt werden sollen.  
 
1.  Klicken Sie in der Ansicht für das NetApp-Konto auf **Momentaufnahmenrichtlinie**.

2.  Klicken Sie mit der rechten Maustaste auf die Momentaufnahmenrichtlinie, die Sie ändern möchten, und wählen Sie dann **Bearbeiten** aus.

    ![Rechtsklicken für Menü zum Bearbeiten der Momentaufnahmenrichtlinie](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Nehmen Sie die Änderungen im Fenster „Momentaufnahmenrichtlinie“ vor, das angezeigt wird, und klicken Sie dann auf **Speichern**. 

### <a name="delete-a-snapshot-policy"></a>Löschen einer Momentaufnahmenrichtlinie 

Sie können eine Momentaufnahmenrichtlinie löschen, die Sie nicht mehr beibehalten möchten.   

1.  Klicken Sie in der Ansicht für das NetApp-Konto auf **Momentaufnahmenrichtlinie**.

2.  Klicken Sie mit der rechten Maustaste auf die Momentaufnahmenrichtlinie, die Sie ändern möchten, und wählen Sie dann **Löschen** aus.

    ![Rechtsklicken für Menü zum Bearbeiten der Momentaufnahmenrichtlinie](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  Klicken Sie auf **Ja**, um zu bestätigen, dass Sie die Momentaufnahmenrichtlinie löschen möchten.   

    ![Bestätigung zum Löschen der Momentaufnahmenrichtlinie](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="edit-the-hide-snapshot-path-option"></a>Bearbeiten der Option „Momentaufnahmepfad ausblenden“
Die Option „Momentaufnahmepfad ausblenden“ steuert, ob der Momentaufnahmepfad eines Volumes sichtbar ist. Während der Erstellung eines [NFS](azure-netapp-files-create-volumes.md#create-an-nfs-volume)- oder [SMB](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume)-Volumes können Sie angeben, ob der Momentaufnahmepfad ausgeblendet werden soll. Sie können die Option „Momentaufnahmepfad ausblenden“ anschließend nach Bedarf bearbeiten.  

> [!NOTE]
> Für ein [Zielvolume](cross-region-replication-create-peering.md#create-the-data-replication-volume-the-destination-volume) bei der regionsübergreifenden Replikation ist die Einstellung „Momentaufnahmepfad ausblenden“ standardmäßig aktiviert, und die Einstellung kann nicht geändert werden. 

1. Wählen Sie das Volume aus, um die Optionseinstellung „Momentaufnahmepfad ausblenden“ eines Volumes anzuzeigen. Das Feld **Momentaufnahmepfad ausblenden** zeigt an, ob die Option aktiviert ist.   
    ![Screenshot, der das Feld „Momentaufnahmepfad ausblenden“ veranschaulicht.](../media/azure-netapp-files/hide-snapshot-path-field.png) 
2. Um die Option „Momentaufnahmepfad ausblenden“ zu bearbeiten, klicken Sie auf der Volumeseite auf **Bearbeiten**, und ändern Sie die Option **Momentaufnahmepfad ausblenden** nach Bedarf.   
    ![Screenshot, der die Option „Volumemomentaufnahme bearbeiten“ veranschaulicht.](../media/azure-netapp-files/volume-edit-snapshot-options.png) 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Wiederherstellen einer Momentaufnahme auf einem neuen Volume

Derzeit können Sie eine Momentaufnahme nur auf einem neuen Volume wiederherstellen. 
1. Klicken Sie im Bereich „Volume“ auf **Momentaufnahmen**, um die Liste der Momentaufnahmen anzuzeigen. 
2. Klicken Sie mit der rechten Maustaste auf die Momentaufnahme, die Sie wiederherstellen möchten, und klicken Sie dann im Menü auf die Option **Auf neuem Volume wiederherstellen**.  

    ![Wiederherstellen von Momentaufnahme auf neuem Volume](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. Geben Sie im Fenster „Volume erstellen“ Informationen für das neue Volume an:  
    * **Name**   
        Geben Sie den Namen für das Volume an, das Sie erstellen möchten.  
        
        Der Name muss innerhalb einer Ressourcengruppe eindeutig sein. Er muss mindestens drei Zeichen lang sein.  Er darf beliebige alphanumerische Zeichen enthalten.

    * **Kontingent**  
        Geben Sie die Menge an logischem Speicherplatz an, den Sie dem Volume zuordnen möchten.  

    ![Wiederherstellen auf neuem Volume](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. Klicken Sie auf **Überprüfen und erstellen**.  Klicken Sie auf **Erstellen**.   
    Das neue Volume verwendet das gleiche Protokoll wie die Momentaufnahme.   
    Das neue Volume, auf dem die Momentaufnahme wiederhergestellt wurde, wird auf dem Blatt „Volumes“ angezeigt.

## <a name="restore-a-file-from-a-snapshot-using-a-client"></a>Wiederherstellen einer Datei aus einer Momentaufnahme mithilfe eines Clients

Wenn Sie nicht [die gesamte Momentaufnahme auf einem Volume wiederherstellen](#restore-a-snapshot-to-a-new-volume) möchten, haben Sie die Möglichkeit, eine Datei aus einer Momentaufnahme wiederherzustellen, und zwar mithilfe eines Clients, der das Volume eingebunden hat.  

Das eingebundene Volume enthält ein Momentaufnahmeverzeichnis namens `.snapshot` (in NFS-Clients) oder `~snapshot` (bei SMB-Clients), auf das der Client Zugriff hat. Das Momentaufnahmeverzeichnis enthält Unterverzeichnisse, die den Momentaufnahmen des Volumes entsprechen. Jedes Unterverzeichnis enthält die Dateien der Momentaufnahme. Wenn Sie versehentlich eine Datei löschen oder überschreiben, können Sie die Datei im übergeordneten Lese-/Schreibverzeichnis wiederherstellen, indem Sie die Datei aus einem Unterverzeichnis der Momentaufnahme in das Lese-/Schreibverzeichnis kopieren. 

Wenn das Momentaufnahmeverzeichnis nicht angezeigt wird, ist es möglicherweise ausgeblendet, weil die Option „Momentaufnahmepfad ausblenden“ aktuell aktiviert ist. Sie können [die Option „Momentaufnahmepfad ausblenden“ bearbeiten](#edit-the-hide-snapshot-path-option), um sie deaktivieren.  

### <a name="restore-a-file-by-using-a-linux-nfs-client"></a>Wiederherstellen einer Datei mithilfe eines Linux-NFS-Clients 

1. Führen Sie den Linux-Befehl `ls` aus, um die Datei aufzulisten, die Sie aus dem Verzeichnis `.snapshot` wiederherstellen möchten. 

    Beispiel:

    `$ ls my.txt`   
    `ls: my.txt: No such file or directory`   

    `$ ls .snapshot`   
    `daily.2020-05-14_0013/              hourly.2020-05-15_1106/`   
    `daily.2020-05-15_0012/              hourly.2020-05-15_1206/`   
    `hourly.2020-05-15_1006/             hourly.2020-05-15_1306/`   

    `$ ls .snapshot/hourly.2020-05-15_1306/my.txt`   
    `my.txt`

2. Führen Sie den Befehl `cp` aus, um die Datei in das übergeordnete Verzeichnis zu kopieren.  

    Beispiel: 

    `$ cp .snapshot/hourly.2020-05-15_1306/my.txt .`   

    `$ ls my.txt`   
    `my.txt`   

### <a name="restore-a-file-by-using-a-windows-client"></a>Wiederherstellen einer Datei mithilfe eines Windows-Clients 

1. Wenn das Verzeichnis `~snapshot` des Volumes ausgeblendet ist, [blenden Sie ausgeblendete Elemente](https://support.microsoft.com/help/4028316/windows-view-hidden-files-and-folders-in-windows-10) im übergeordneten Verzeichnis ein, um `~snapshot` anzuzeigen.

    ![Ausgeblendete Elemente anzeigen](../media/azure-netapp-files/snapshot-show-hidden.png) 

2. Navigieren Sie zum Unterverzeichnis innerhalb von `~snapshot`, um die Datei zu finden, die Sie wiederherstellen möchten.  Klicken Sie mit der rechten Maustaste auf die Datei. Wählen Sie **Kopieren** aus.  

    ![Kopieren der wiederherzustellenden Datei](../media/azure-netapp-files/snapshot-copy-file-restore.png) 

3. Kehren Sie zum übergeordneten Verzeichnis zurück. Klicken Sie mit der rechten Maustaste in das übergeordnete Verzeichnis, und wählen Sie `Paste` aus, um die Datei in das Verzeichnis einzufügen.

    ![Einfügen der wiederherzustellenden Datei](../media/azure-netapp-files/snapshot-paste-file-restore.png) 

4. Sie können auch mit der rechten Maustaste auf das übergeordnete Verzeichnis klicken, **Eigenschaften** auswählen, auf die Registerkarte **Vorherige Versionen** klicken, um die Liste der Momentaufnahmen anzuzeigen, und **Wiederherstellen** auswählen, um eine Datei wiederherzustellen.  

    ![Eigenschaften vorheriger Versionen](../media/azure-netapp-files/snapshot-properties-previous-version.png) 

## <a name="revert-a-volume-using-snapshot-revert"></a>Wiederherstellen eines Volumes mit „Momentaufnahme wiederherstellen“

Mit der Funktion zum Wiederherstellen von Momentaufnahmen können Sie schnell ein Volume wieder in den Zustand versetzen, in dem es sich befand, als eine bestimmte Momentaufnahme erstellt wurde. In den meisten Fällen ist das Wiederherstellen eines Volumes wesentlich schneller als das Wiederherstellen einzelner Dateien aus einer Momentaufnahme in das aktive Dateisystem. Im Vergleich zur Wiederherstellung einer Momentaufnahme auf einem neuen Volume ist es auch wesentliche effizienter hinsichtlich des Speicherplatzes. 

Die Option „Volume wiederherstellen“ finden Sie im Menü „Momentaufnahmen“ eines Volumes. Nachdem Sie eine Momentaufnahme für die Wiederherstellung ausgewählt haben, setzt Azure NetApp Files das Volume auf die Daten und Zeitstempel zurück, die es bei der Erstellung der ausgewählten Momentaufnahme enthielt. 

> [!IMPORTANT]
> Aktive Dateisystemdaten und Momentaufnahmen, die nach Erstellung der ausgewählten Momentaufnahme erstellt wurden, gehen verloren. Der Vorgang zum Wiederherstellen der Momentaufnahme ersetzt *alle* Daten im Zielvolume durch die Daten in der ausgewählten Momentaufnahme. Bei der Auswahl der Momentaufnahme sollten Sie auf die Inhalte und das Erstellungsdatum achten. Sie können den Vorgang zum Wiederherstellen der Momentaufnahme nicht zurücksetzen.

1. Rufen Sie das Menü **Momentaufnahmen** eines Volumes auf.  Klicken Sie mit der rechten Maustaste auf die Momentaufnahme, die Sie für die Wiederherstellung verwenden möchten. Klicken Sie auf **Volume wiederherstellen**. 

    ![Screenshot: Kontextmenü einer Momentaufnahme](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. Geben Sie im Fenster „Volume auf Momentaufnahme zurücksetzen“ den Namen des Volumes ein, und klicken Sie dann auf **Wiederherstellen**.   

    Das Volume wird nun anhand des Zeitpunkts der ausgewählten Momentaufnahme wiederhergestellt.

    ![Screenshot: Fenster „Volume auf Momentaufnahme zurücksetzen“](../media/azure-netapp-files/snapshot-revert-volume.png) 

## <a name="delete-snapshots"></a>Löschen von Momentaufnahmen  

Sie können Momentaufnahmen löschen, die Sie nicht mehr beibehalten müssen. 

1. Rufen Sie das Menü **Momentaufnahmen** eines Volumes auf. Klicken Sie mit der rechten Maustaste auf die zu löschende Momentaufnahme. Klicken Sie auf **Löschen**.

    ![Screenshot: Kontextmenü einer Momentaufnahme](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. Bestätigen Sie im Fenster „Momentaufnahme löschen“, dass Sie die Momentaufnahme löschen möchten, indem Sie auf **Ja** klicken. 

    ![Screenshot: Bestätigung zum Löschen der Momentaufnahme](../media/azure-netapp-files/snapshot-confirm-delete.png)  

## <a name="next-steps"></a>Nächste Schritte

* [Problembehandlung für Momentaufnahmenrichtlinien](troubleshoot-snapshot-policies.md)
* [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Video „Azure NetApp Files Snapshots 101“](https://www.youtube.com/watch?v=uxbTXhtXCkw&feature=youtu.be)
