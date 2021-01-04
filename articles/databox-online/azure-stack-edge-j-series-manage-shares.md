---
title: Azure Stack Edge Pro-GPU-Freigabeverwaltung | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie im Azure-Portal Freigaben für Ihr Azure Stack Edge Pro-GPU-Gerät verwalten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 413a93a145ae063a3aab4066ed62365e154d744a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454112"
---
# <a name="use-azure-portal-to-manage-shares-on-your-azure-stack-edge-pro"></a>Verwalten von Freigaben für Ihr Azure Stack Edge Pro-Gerät im Azure-Portal

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

In diesem Artikel wird beschrieben, wie Sie Freigaben für Ihr Azure Stack Edge Pro-Gerät verwalten. Sie können das Azure Stack Edge Pro-Gerät im Azure-Portal oder über die lokale Webbenutzeroberfläche verwalten. Verwenden Sie das Azure-Portal, um Freigaben hinzuzufügen, zu löschen oder zu aktualisieren oder um Speicherschlüssel für das Speicherkonto zu synchronisieren, das den Freigaben zugeordnet ist.

## <a name="about-shares"></a>Informationen zu Freigaben

Wenn Sie Daten an Azure übertragen möchten, müssen Sie Freigaben für Ihr Azure Stack Edge Pro-Gerät erstellen. Bei den Freigaben, die Sie auf dem Azure Stack Edge Pro-Gerät hinzufügen, kann es sich um lokale Freigaben oder Freigaben handeln, die Daten per Pushvorgang in die Cloud übertragen.

 - **Lokale Freigaben**: Verwenden Sie diese Freigaben, wenn die Daten lokal auf dem Gerät verarbeitet werden sollen.
 - **Freigaben**: Verwenden Sie diese Freigaben, wenn die Gerätedaten automatisch in Ihr Speicherkonto in der Cloud gepusht werden sollen. Alle Cloudfunktionen, z. B. **Aktualisieren** und **Speicherkontoschlüssel synchronisieren**, werden auf die Freigaben angewendet.


## <a name="add-a-share"></a>Hinzufügen einer Freigabe

Gehen Sie im Azure-Portal wie folgt vor, um eine Freigabe zu erstellen:

1. Wechseln Sie im Azure-Portal zu Ihrer Azure Stack Edge-Ressource und dann zu **Gateway > Freigaben**. Wählen Sie in der Befehlsleiste die Option **+ Freigabe hinzufügen**.

    ![Auswählen von „Freigabe hinzufügen“](media/azure-stack-edge-j-series-manage-shares/add-share-1.png)

2. Geben Sie unter **Freigabe hinzufügen** die Freigabeeinstellungen an. Geben Sie einen eindeutigen Namen für die Freigabe an.
    
    Freigabenamen dürfen nur Zahlen, Kleinbuchstaben und Bindestriche enthalten. Der Name der Freigabe muss 3 bis 63 Zeichen lang sein und mit einem Buchstaben oder einer Zahl beginnen. Vor und nach jedem Bindestrich muss ein Zeichen stehen, das kein Bindestrich ist.

3. Wählen Sie einen **Typ** für die Freigabe aus. Zur Auswahl stehen **SMB** und **NFS** (Standardeinstellung: SMB). „SMB“ ist die Standardeinstellung für Windows-Clients, und „NFS“ wird für Linux-Clients verwendet. Die angezeigten Optionen für SMB- und NFS-Dateifreigaben unterscheiden sich geringfügig.

4. Geben Sie ein **Speicherkonto** an, in dem sich die Freigabe befindet. Im Speicherkonto wird ein Container mit dem Freigabenamen erstellt, sofern er noch nicht vorhanden ist. Wenn er bereits vorhanden ist, wird der vorhandene Container verwendet.

5. Wählen Sie in der Dropdownliste **Speicherdienst** die Option „Blockblob“, „Seitenblob“ oder „Dateien“ aus. Der ausgewählte Diensttyp hängt von dem Format ab, in dem die Daten in Azure gespeichert werden sollen. In diesem Fall sollen die Daten beispielsweise als Blockblobs in Azure gespeichert werden. Daher wählen wir **Blockblob** aus. Wenn Sie **Seitenblob** auswählen, müssen Sie sicherstellen, dass Ihre Daten ganzzahlige Vielfache von 512 Byte sind. Verwenden Sie **Seitenblobs** für VHDs oder VHDX, die immer an 512 Byte ausgerichtet sind.

6. Dieser Schritt hängt davon ab, ob Sie eine SMB- oder NFS-Freigabe erstellen.
    - **Wenn Sie eine SMB-Freigabe erstellen:** Wählen Sie im Feld **Lokaler Benutzer (alle Berechtigungen)** die Option **Neu erstellen** oder **Vorhandene verwenden** aus. Wenn Sie einen neuen lokalen Benutzer erstellen, füllen Sie die Felder **Benutzername** und **Kennwort** aus, und bestätigen Sie anschließend das Kennwort. Dadurch werden die Berechtigungen dem lokalen Benutzer zugewiesen. Nachdem Sie hier die Berechtigungen zugewiesen haben, können Sie den Datei-Explorer verwenden, um diese Berechtigungen zu ändern.

        ![Hinzufügen einer SMB-Freigabe](media/azure-stack-edge-j-series-manage-shares/add-smb-share.png)

        Wenn Sie für diese Freigabedaten das Kontrollkästchen „Allow only read operations“ (Nur Lesevorgänge zulassen) aktivieren, können Sie Benutzer angeben, die nur über Lesezugriff verfügen.
    - **Wenn Sie eine NFS-Freigabe erstellen:** Geben Sie die **IP-Adressen der zulässigen Clients** an, die auf die Freigabe zugreifen können.

        ![Hinzufügen einer NFS-Freigabe](media/azure-stack-edge-j-series-manage-shares/add-nfs-share.png)

7. Verwenden Sie den lokalen Bereitstellungspunkt, um von Edgecomputingmodulen aus leicht auf die Freigaben zugreifen zu können. Wählen Sie **Freigabe mit Edgecomputing verwenden** aus, damit die Freigabe nach ihrer Erstellung automatisch bereitgestellt wird. Wenn diese Option ausgewählt ist, kann das Edgemodul Compute auch mit dem lokalen Bereitstellungspunkt verwenden.

8. Klicken Sie auf **Erstellen**, um die Freigabe zu erstellen. Sie werden benachrichtigt, wenn die Freigabe erstellt wird. Nachdem die Freigabe mit den angegebenen Einstellungen erstellt wurde, wird das Blatt **Freigaben** aktualisiert und zeigt die neue Freigabe.

## <a name="add-a-local-share"></a>Hinzufügen einer lokalen Freigabe

1. Wechseln Sie im Azure-Portal zu Ihrer Azure Stack Edge-Ressource und dann zu **Gateway > Freigaben**. Wählen Sie in der Befehlsleiste die Option **+ Freigabe hinzufügen**.

    ![Auswählen von „Freigabe hinzufügen“ 2](media/azure-stack-edge-j-series-manage-shares/add-local-share-1.png)

2. Geben Sie unter **Freigabe hinzufügen** die Freigabeeinstellungen an. Geben Sie einen eindeutigen Namen für die Freigabe an.
    
    Freigabenamen dürfen nur Zahlen, Klein- und Großbuchstaben sowie Bindestriche enthalten. Der Name der Freigabe muss 3 bis 63 Zeichen lang sein und mit einem Buchstaben oder einer Zahl beginnen. Vor und nach jedem Bindestrich muss ein Zeichen stehen, das kein Bindestrich ist.

3. Wählen Sie einen **Typ** für die Freigabe aus. Zur Auswahl stehen **SMB** und **NFS** (Standardeinstellung: SMB). „SMB“ ist die Standardeinstellung für Windows-Clients, und „NFS“ wird für Linux-Clients verwendet. Die angezeigten Optionen für SMB- und NFS-Dateifreigaben unterscheiden sich geringfügig.

   > [!IMPORTANT]
   > Stellen Sie sicher, dass für das von Ihnen verwendete Azure Storage-Konto keine Unveränderlichkeitsrichtlinien festgelegt sind, wenn Sie es mit einem Azure Stack Edge Pro- oder Data Box Gateway-Gerät verwenden. Weitere Informationen finden Sie unter [Festlegen und Verwalten von Unveränderlichkeitsrichtlinien für Blobspeicher](../storage/blobs/storage-blob-immutability-policies-manage.md).

4. Verwenden Sie den lokalen Bereitstellungspunkt, um von Edgecomputingmodulen aus leicht auf die Freigaben zugreifen zu können. Wählen Sie **Freigabe mit Edgecomputing verwenden**, damit das Edgemodul die Computeressource mit dem lokalen Bereitstellungspunkt nutzen kann.

5. Wählen Sie **Als lokale Edge-Freigabe konfigurieren**. Die Daten lokaler Dateifreigaben verbleiben auf dem lokalen Gerät. Sie können diese Daten lokal verarbeiten.

6. Wählen Sie im Feld **Lokaler Benutzer (alle Berechtigungen)** die Option **Neu erstellen** oder **Vorhandene verwenden**.

7. Klicken Sie auf **Erstellen**. 

    ![Erstellen einer lokalen Freigabe](media/azure-stack-edge-j-series-manage-shares/add-local-share-2.png)

    Es wird eine Benachrichtigung angezeigt, dass die Freigabe erstellt wird. Nachdem die Freigabe mit den angegebenen Einstellungen erstellt wurde, wird das Blatt **Freigaben** aktualisiert und zeigt die neue Freigabe.

    ![Anzeigen von Updates auf dem Blatt „Freigaben“](media/azure-stack-edge-j-series-manage-shares/add-local-share-3.png)
    
    Wählen Sie die Freigabe aus, um den lokalen Bereitstellungspunkt für die Edgecomputingmodule dieser Freigabe anzuzeigen.

    ![Anzeigen von Details zur lokalen Freigabe](media/azure-stack-edge-j-series-manage-shares/add-local-share-4.png)

## <a name="mount-a-share"></a>Einbinden einer Dateifreigabe

Wenn Sie eine Freigabe erstellt haben, bevor Sie Compute für Ihr Azure Stack Edge Pro-Gerät konfiguriert haben, müssen Sie die Freigabe einbinden. Gehen Sie wie folgt vor, um eine Freigabe einzubinden.


1. Wechseln Sie im Azure-Portal zu Ihrer Azure Stack Edge-Ressource und dann zu **Gateway > Freigaben**. Wählen Sie in der Liste mit den Freigaben die Freigabe aus, die Sie einbinden möchten. Für die ausgewählte Freigabe wird in der Spalte **Für Compute verwendet** der Status **Deaktiviert** angezeigt.

    ![Auswählen der Freigabe](media/azure-stack-edge-j-series-manage-shares/mount-share-1.png)

2. Wählen Sie **Einbinden** aus.

    ![Auswählen von „Einbinden“](media/azure-stack-edge-j-series-manage-shares/mount-share-2.png)

3. Wählen Sie **Ja** aus, wenn Sie zur Bestätigung aufgefordert werden. Dadurch wird die Freigabe eingebunden.

    ![Überprüfen der Einbindung](media/azure-stack-edge-j-series-manage-shares/mount-share-3.png)

4. Navigieren Sie nach erfolgter Einbindung der Freigabe zur Liste mit den Freigaben. In der Spalte **Für Compute verwendet** wird der Freigabestatus **Aktiviert** angezeigt.

    ![Freigabe eingebunden](media/azure-stack-edge-j-series-manage-shares/mount-share-4.png)

5. Wählen Sie die Freigabe erneut aus, um den lokalen Bereitstellungspunkt für die Freigabe anzuzeigen. Das Edgecomputingmodul verwendet diesen lokalen Bereitstellungspunkt für die Freigabe.

    ![Lokaler Bereitstellungspunkt für die Freigabe](media/azure-stack-edge-j-series-manage-shares/mount-share-5.png)

## <a name="unmount-a-share"></a>Aufheben der Einbindung einer Freigabe

Gehen Sie im Azure-Portal wie folgt vor, um die Einbindung einer Freigabe aufzuheben:

1. Wechseln Sie im Azure-Portal zu Ihrer Azure Stack Edge-Ressource und dann zu **Gateway > Freigaben**.

    ![Auswählen von Freigabe 2](media/azure-stack-edge-j-series-manage-shares/unmount-share-1.png)

2. Wählen Sie in der Liste mit den Freigaben die Freigabe aus, deren Einbindung Sie aufheben möchten. Achten Sie dabei darauf, dass die Freigabe, deren Einbindung Sie aufheben möchten, nicht von einem anderen Modul verwendet wird. Andernfalls treten Probleme mit dem entsprechenden Modul auf. Wählen Sie **Bereitstellung aufheben** aus.

    ![Auswählen von „Bereitstellung aufheben“](media/azure-stack-edge-j-series-manage-shares/unmount-share-2.png)

3. Wählen Sie **Ja** aus, wenn Sie zur Bestätigung aufgefordert werden. Dadurch wird die Einbindung der Freigabe aufgehoben.

    ![Überprüfen der Einbindungsaufhebung](media/azure-stack-edge-j-series-manage-shares/unmount-share-3.png)

4. Navigieren Sie nach erfolgter Aufhebung der Einbindung der Freigabe zur Liste mit den Freigaben. In der Spalte **Für Compute verwendet** wird der Freigabestatus **Deaktiviert** angezeigt.

    ![Einbindung der Freigabe aufgehoben](media/azure-stack-edge-j-series-manage-shares/unmount-share-4.png)

## <a name="delete-a-share"></a>Löschen einer Freigabe

Gehen Sie im Azure-Portal wie folgt vor, um eine Freigabe zu löschen:

1. Klicken Sie in der Liste mit den Freigaben auf die Freigabe, die Sie löschen möchten.

    ![Auswählen der Freigabe 3](media/azure-stack-edge-j-series-manage-shares/delete-share-1.png)

2. Klicken Sie auf **Löschen**.

    ![Auf „Löschen“ klicken](media/azure-stack-edge-j-series-manage-shares/delete-share-2.png)

3. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**.

    ![Bestätigen des Löschens](media/azure-stack-edge-j-series-manage-shares/delete-share-3.png)

Die Liste mit den Freigaben wird nach dem Löschen entsprechend aktualisiert.

## <a name="refresh-shares"></a>Aktualisieren von Freigaben

Mithilfe des Aktualisierungsfeatures können Sie den Inhalt einer Freigabe aktualisieren. Wenn Sie eine Freigabe aktualisieren, wird eine Suche nach allen Azure-Objekten mit Blobs und Dateien initiiert, die der Cloud seit der letzten Aktualisierung hinzugefügt wurden. Diese zusätzlichen Dateien werden dann heruntergeladen, um den Inhalt der Freigabe auf dem Gerät zu aktualisieren.

> [!IMPORTANT]
> - Lokale Freigaben können nicht aktualisiert werden.
> - Berechtigungen und Zugriffssteuerungslisten (ACLs) werden über einen Aktualisierungsvorgang hinaus nicht beibehalten. 

Gehen Sie im Azure-Portal wie folgt vor, um eine Freigabe zu aktualisieren:

1.  Navigieren Sie im Azure-Portal zu **Freigaben**. Klicken Sie auf die Freigabe, die Sie aktualisieren möchten.

    ![Auswählen der Freigabe 4](media/azure-stack-edge-j-series-manage-shares/refresh-share-1.png)

2.  Klicken Sie auf **Aktualisieren**. 

    ![Klicken auf „Aktualisieren“](media/azure-stack-edge-j-series-manage-shares/refresh-share-2.png)
 
3.  Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**. Daraufhin wird ein Auftrag gestartet, um den Inhalt der lokalen Freigabe zu aktualisieren.

    ![Bestätigen der Aktualisierung](media/azure-stack-edge-j-series-manage-shares/refresh-share-3.png)
 
4.   Während der Ausführung des Aktualisierungsvorgangs ist die Aktualisierungsoption im Kontextmenü ausgegraut. Klicken Sie auf die Auftragsbenachrichtigung, um den Status des Aktualisierungsauftrags anzuzeigen.

5.   Die Dauer des Aktualisierungsvorgangs hängt davon ab, wie viele Dateien sich im Azure-Container und auf dem Gerät befinden. Nach erfolgreichem Abschluss der Aktualisierung wird der Zeitstempel der Freigabe aktualisiert. Der Zeitstempel wird auch aktualisiert, wenn der Vorgang nur teilweise erfolgreich war. Die Fehlerprotokolle zur Aktualisierung werden ebenfalls aktualisiert.

![Aktualisierter Zeitstempel](media/azure-stack-edge-j-series-manage-shares/refresh-share-4.png)
 
Im Falle eines Fehlers wird eine Warnung ausgelöst. Die Warnung enthält Informationen zur Ursache sowie Empfehlungen für die Problembehebung. Darüber hinaus enthält die Warnung einen Link zu einer Datei mit einer vollständigen Zusammenfassung der Fehler für die Dateien, die nicht aktualisiert oder gelöscht werden konnten.

## <a name="sync-pinned-files"></a>Synchronisieren angehefteter Dateien 

Führen Sie die folgenden Schritte im Azure-Portal aus, um angeheftete Dateien automatisch zu synchronisieren:
 
1. Wählen Sie ein vorhandenes Azure-Speicherkonto aus. 

2. Wechseln Sie zu **Container** und wählen Sie **+ Container** aus, um einen Container zu erstellen. Benennen Sie diesen Container als *newcontainer*. Legen Sie die **Öffentliche Zugriffsebene** auf „Container“ fest.

    ![Automatisches Synchronisieren für angeheftete Dateien 1](media/azure-stack-edge-j-series-manage-shares/image-1.png)

3. Wählen Sie den Containernamen aus, und legen Sie die folgenden Metadaten fest:  

    - Name = "Pinned" 
    - Value = "True" 

    ![Automatisches Synchronisieren für angeheftete Dateien 2](media/azure-stack-edge-j-series-manage-shares/image-2.png)
 
4. Erstellen Sie eine neue Freigabe auf Ihrem Gerät. Ordnen Sie sie dem angehefteten Container zu, indem Sie die Option „Vorhandener Container“ auswählen. Markieren Sie die Freigabe als schreibgeschützt. Erstellen Sie einen neuen Benutzer, und geben Sie den Benutzernamen und ein entsprechendes Kennwort für diese Freigabe an.  

    ![Automatisches Synchronisieren für angeheftete Dateien 3](media/azure-stack-edge-j-series-manage-shares/image-3.png)
 
5. Navigieren Sie im Azure-Portal zum Container, den Sie erstellt haben. Laden Sie die anzuheftende Datei in „newcontainer“ hoch, in dem der Metadatensatz angeheftet werden soll. 

6. Wählen Sie **Daten aktualisieren** im Azure-Portal für das Gerät aus, um die Richtlinie für das Anheften für diesen bestimmten Azure Storage-Container herunterzuladen.  

    ![Automatisches Synchronisieren für angeheftete Dateien 4](media/azure-stack-edge-j-series-manage-shares/image-4.png)
 
7. Greifen Sie auf die neue Freigabe zu, die auf dem Gerät erstellt wurde. Die Datei, die in das Speicherkonto hochgeladen wurde, wird jetzt in die lokale Freigabe heruntergeladen. 

    Jedes Mal, wenn das Gerät getrennt oder wieder angeschlossen wird, löst es eine Aktualisierung aus. Bei der Aktualisierung werden nur die Dateien abgerufen, die sich geändert haben. 


## <a name="sync-storage-keys"></a>Synchronisieren von Speicherschlüsseln

Speicherkontoschlüssel müssen nach der Rotation synchronisiert werden. Die Synchronisierung stellt sicher, dass das Gerät über die neuesten Schlüssel für Ihr Speicherkonto verfügt.

Gehen Sie im Azure-Portal wie folgt vor, um Ihren Speicherzugriffsschlüssel zu synchronisieren:

1. Navigieren Sie in Ihrer Ressource zu **Übersicht**. Klicken Sie in der Liste mit den Freigaben auf eine Freigabe, die dem Speicherkonto zugeordnet ist, das Sie synchronisieren möchten.

    ![Auswählen einer Freigabe mit relevantem Speicherkonto](media/azure-stack-edge-j-series-manage-shares/sync-storage-key-1.png)

2. Klicken Sie auf **Speicherkontoschlüssel synchronisieren**. Klicken Sie auf **Ja**, wenn Sie aufgefordert werden, den Vorgang zu bestätigen.

     ![Wählen von „Speicherkontoschlüssel synchronisieren“](media/azure-stack-edge-j-series-manage-shares/sync-storage-key-2.png)

3. Schließen Sie das Dialogfeld, wenn die Synchronisierung abgeschlossen ist.

>[!NOTE]
> Dieser Vorgang muss nur einmal pro Speicherkonto ausgeführt werden. Er muss nicht für alle Freigaben wiederholt werden, die dem gleichen Speicherkonto zugeordnet sind.


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Benutzer über das Azure-Portal verwalten](azure-stack-edge-j-series-manage-users.md).