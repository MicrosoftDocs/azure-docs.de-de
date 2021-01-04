---
title: Tutorial zum Übertragen von Daten an Freigaben mit Azure Stack Edge Pro | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie Ihrem Azure Stack Edge Pro-Gerät Freigaben hinzufügen und eine Verbindung damit herstellen, damit Azure Stack Edge Pro Daten in Azure übertragen kann.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 64fe65019a66013323b1c6d27055051badb47cee
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350312"
---
# <a name="tutorial-transfer-data-with-azure-stack-edge-pro"></a>Tutorial: Übertragen von Daten mit Azure Stack Edge Pro

In diesem Tutorial erfahren Sie, wie Sie auf Ihrem Azure Stack Edge Pro-Gerät Freigaben hinzufügen und eine Verbindung mit diesen Freigaben herstellen. Nachdem die Freigaben hinzugefügt wurden, kann Azure Stack Edge Pro Daten an Azure übertragen.

Dieser Vorgang kann bis zu zehn Minuten dauern.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Hinzufügen einer Freigabe
> * Herstellen einer Verbindung mit der Freigabe

 
## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich, dass Folgendes erfüllt ist, bevor Sie Ihrem Azure Stack Edge Pro-Gerät Freigaben hinzufügen:

- Sie haben Ihr physisches Gerät gemäß der Anleitung unter [Installieren von Azure Stack Edge Pro](azure-stack-edge-deploy-install.md) installiert.

- Das physische Gerät wurde aktiviert, wie unter [Tutorial: Verbinden, Einrichten und Aktivieren von Azure Stack Edge](azure-stack-edge-deploy-connect-setup-activate.md) beschrieben.


## <a name="add-a-share"></a>Hinzufügen einer Freigabe

Gehen Sie wie folgt vor, um eine Freigabe zu erstellen:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihre Azure Stack Edge-Ressource aus, und navigieren Sie anschließend zu **Übersicht**. Ihr Gerät sollte online sein.

   ![Gerät online](./media/azure-stack-edge-deploy-add-shares/device-online-1.png)

2. Wählen Sie auf der Gerätebefehlsleiste die Option **+ Freigabe hinzufügen** aus.

   ![Hinzufügen einer Freigabe](./media/azure-stack-edge-deploy-add-shares/select-add-share-1.png)

3. Gehen Sie im Bereich **Freigabe hinzufügen** wie folgt vor:

    a. Geben Sie im Feld **Name** einen eindeutigen Namen für die Freigabe an.  
    Der Freigabename darf nur Kleinbuchstaben, Ziffern und Bindestriche enthalten. Er muss aus 3 bis 63 Zeichen bestehen und mit einem Buchstaben oder einer Ziffer beginnen. Bindestriche müssen vorangestellt und von einem Buchstaben oder einer Ziffer gefolgt werden.
    
    b. Wählen Sie einen **Typ** für die Freigabe aus.  
    Zur Auswahl stehen **SMB** und **NFS** (Standardeinstellung: SMB). „SMB“ ist die Standardeinstellung für Windows-Clients, und „NFS“ wird für Linux-Clients verwendet.  
    Je nachdem, ob Sie sich für SMB- oder NFS-Freigaben entscheiden, variiert der Rest der Optionen geringfügig. 

    c. Geben Sie das gewünschte Speicherkonto für die Freigabe an. 

    > [!IMPORTANT]
    > Stellen Sie sicher, dass für das von Ihnen verwendete Azure Storage-Konto keine Unveränderlichkeitsrichtlinien festgelegt sind, wenn Sie es mit einem Azure Stack Edge Pro- oder Data Box Gateway-Gerät verwenden. Weitere Informationen finden Sie unter [Festlegen und Verwalten von Unveränderlichkeitsrichtlinien für Blobspeicher](../storage/blobs/storage-blob-immutability-policies-manage.md).
    
    d. Wählen Sie in der Dropdownliste **Speicherdienst** **Blockblob**, **Seitenblob** oder **Dateien** aus.  
    Der ausgewählte Diensttyp hängt von dem Format ab, in dem die Daten in Azure verwendet werden sollen. In diesem Beispiel sollen die Daten als Blobblöcke in Azure gespeichert werden. Daher wählen wir **Blockblob** aus. Bei Verwendung von **Seitenblob** müssen Ihre Daten ganzzahlige Vielfache von 512 Bytes sein. VHDX-Daten sind beispielsweise immer ganzzahlige Vielfache von 512 Bytes.

    e. Erstellen Sie einen neuen Blobcontainer, oder wählen Sie in der Dropdownliste einen vorhandenen Blobcontainer aus. Geben Sie bei Erstellung eines Blobcontainers einen Containernamen an. Wenn ein Container noch nicht vorhanden ist, wird er im Speicherkonto mit dem neu erstellten Freigabenamen angelegt.
   
    f. Je nachdem, ob Sie eine SMB-Freigabe oder eine NFS-Freigabe erstellt haben, führen Sie einen der folgenden Schritte aus: 
     
    - **SMB-Freigabe**: Wählen Sie unter **Alle lokalen Benutzer mit Berechtigungen** die Option **Neu erstellen** oder **Vorhandene verwenden**. Wenn Sie einen neuen lokalen Benutzer anlegen, geben Sie einen Benutzernamen und ein Kennwort ein, und bestätigen Sie dann das Kennwort. Dadurch werden die Berechtigungen dem lokalen Benutzer zugewiesen. Nachdem Sie hier die Berechtigungen zugewiesen haben, können Sie den Datei-Explorer verwenden, um diese zu ändern.

        Wenn Sie für diese Freigabedaten das Kontrollkästchen **Nur Lesevorgänge zulassen** aktivieren, können Sie Benutzer angeben, die nur über Lesezugriff verfügen.

        ![Hinzufügen einer SMB-Freigabe](./media/azure-stack-edge-deploy-add-shares/add-share-smb-1.png)
   
    - **NFS-Freigabe**: Geben Sie die IP-Adressen der zulässigen Clients ein, die auf die Freigabe zugreifen können.

        ![Hinzufügen einer NFS-Freigabe](./media/azure-stack-edge-deploy-add-shares/add-share-nfs-1.png)
   
4. Wählen Sie **Erstellen** aus, um die Freigabe zu erstellen.
    
    Sie werden benachrichtigt, wenn die Freigabe erstellt wird. Nachdem die Freigabe mit den angegebenen Einstellungen erstellt wurde, wird die Kachel **Freigaben** aktualisiert, und die neue Freigabe wird angezeigt.
    

## <a name="connect-to-the-share"></a>Herstellen einer Verbindung mit der Freigabe

Nun können Sie eine Verbindung mit den Freigaben herstellen, die Sie im vorherigen Schritt erstellt haben. Die auszuführenden Schritte sind ggf. abhängig von der verwendeten Freigabe (SMB oder NFS).

### <a name="connect-to-an-smb-share"></a>Herstellen einer Verbindung mit einer SMB-Freigabe

Stellen Sie auf dem Windows Server-Client, der mit Ihrem Azure Stack Edge Pro-Gerät verbunden ist, eine Verbindung mit einer SMB-Freigabe her, indem Sie die folgenden Befehle eingeben:


1. Geben Sie in einem Befehlsfenster den folgenden Befehl ein:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

2. Wenn Sie dazu aufgefordert werden, geben Sie das Kennwort für die Freigabe ein.  
   Hier sehen Sie eine Beispielausgabe des Befehls.

    ```powershell
    Microsoft Windows [Version 10.0.16299.192)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60':
    The command completed successfully.
    
    C: \Users\DataBoxEdgeUser>
    ```   


3. Wählen Sie auf der Tastatur „Windows + R“ aus.

4. Geben Sie im Fenster **Ausführen**`\\<device IP address>` an, und wählen Sie anschließend **OK** aus.  
   Der Datei-Explorer wird geöffnet. Die von Ihnen erstellten Freigaben sollten jetzt als Ordner angezeigt werden. Doppelklicken Sie im Datei-Explorer auf eine Freigabe (einen Ordner), um den Inhalt anzuzeigen.
 
    ![Herstellen einer Verbindung mit einer SMB-Freigabe](./media/azure-stack-edge-deploy-add-shares/connect-to-share2.png)

    Die Daten werden bei ihrer Generierung in diese Freigaben geschrieben, und das Gerät pusht die Daten in die Cloud.

### <a name="connect-to-an-nfs-share"></a>Herstellen einer Verbindung mit einer NFS-Freigabe

Führen Sie auf dem mit Ihrem Azure Stack Edge Pro-Gerät verbundenen Linux-Client die folgenden Schritte aus:

1. Stellen Sie sicher, dass der NFSv4-Client auf dem Client installiert ist. Verwenden Sie zum Installieren des NFS-Clients den folgenden Befehl:

   `sudo apt-get install nfs-common`

    Weitere Informationen finden Sie unter [Install NFSv4 client](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client) (Installieren des NFSv4-Clients).

2. Führen Sie nach der Installation des NFS-Clients den folgenden Befehl aus, um die NFS-Freigabe einzubinden, die Sie auf Ihrem Azure Stack Edge Pro-Gerät erstellt haben:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    > [!IMPORTANT]
    > Durch Verwendung der Option `sync` beim Einbinden von Freigaben werden die Übertragungsraten großer Dateien verbessert.
    > Vergewissern Sie sich vor dem Einbinden der Freigaben, dass die Verzeichnisse, die als Bereitstellungspunkte auf Ihrem lokalen Computer fungieren, bereits erstellt wurden. Diese Verzeichnisse dürfen keine Dateien oder Unterordner enthalten.

    Das folgende Beispiel zeigt, wie Sie über NFS eine Verbindung mit einer Freigabe auf Ihrem Azure Stack Edge Pro-Gerät herstellen. Die Geräte-IP lautet `10.10.10.60`. Die Freigabe `mylinuxshare2` wird in „ubuntuVM“ eingebunden. Der Bereitstellungspunkt der Freigabe ist `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> Für dieses Release gelten folgende Einschränkungen:
> - Das Umbenennen einer Datei nach ihrer Erstellung in der Freigabe wird nicht unterstützt. 
> - Durch das Löschen einer Datei aus einer Freigabe wird der Eintrag im Speicherkonto nicht gelöscht.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurden unter anderem folgende Azure Stack Edge Pro-Themen behandelt:

> [!div class="checklist"]
> * Hinzufügen einer Freigabe
> * Herstellen einer Verbindung mit einer Freigabe

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Ihre Daten mithilfe von Azure Stack Edge Pro transformieren:

> [!div class="nextstepaction"]
> [Transformieren von Daten mit Azure Stack Edge Pro](./azure-stack-edge-deploy-configure-compute.md)