---
title: Übertragen von Daten mit Azure Data Box Gateway | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Freigaben für Ihre Azure Data Box Gateway-Instanz hinzufügen und eine Verbindung damit herstellen. Anschließend kann Ihr Data Box Gateway-Gerät Daten an Azure übertragen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: aaa75988e1177590a0ff6f4ca2e00f92de68789f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96580363"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway"></a>Tutorial: Übertragen von Daten mit Azure Data Box Gateway


## <a name="introduction"></a>Einführung

In diesem Artikel erfahren Sie, wie Sie auf Ihrem Data Box Gateway-Gerät Freigaben hinzufügen und eine Verbindung mit diesen Freigaben herstellen. Nachdem die Freigaben hinzugefügt wurden, kann das Gerät Daten an Azure übertragen.

Dieser Vorgang kann bis zu zehn Minuten dauern.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Hinzufügen einer Freigabe
> * Herstellen einer Verbindung mit einer Freigabe

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie Ihrem Data Box Gateway Freigaben hinzufügen:

- Sie haben ein virtuelles Gerät bereitgestellt und wie unter [Tutorial: Bereitstellen von Azure Data Box Gateway in Hyper-V](data-box-gateway-deploy-provision-hyperv.md) bzw. [Tutorial: Bereitstellen von Azure Data Box Gateway in VMware](data-box-gateway-deploy-provision-vmware.md) beschrieben eine Verbindung mit dem Gerät hergestellt.

- Sie haben das virtuelle Gerät gemäß der Anleitung unter [Tutorial: Verbinden, Einrichten und Aktivieren von Azure Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md) aktiviert.

- Das Gerät ist für die Erstellung von Freigaben und die Übertragung von Daten bereit.

## <a name="add-a-share"></a>Hinzufügen einer Freigabe

Gehen Sie wie folgt vor, um eine Freigabe zu erstellen:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrer Data Box Gateway-Ressource und anschließend zu **Übersicht**. Ihr Gerät sollte online sein. Wählen Sie auf der Gerätebefehlsleiste die Option **+ Freigabe hinzufügen** aus.
   
   ![Hinzufügen einer Freigabe](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. Gehen Sie unter **Freigabe hinzufügen** wie folgt vor:

    1. Geben Sie einen eindeutigen Namen für die Freigabe an. Freigabenamen dürfen nur Kleinbuchstaben, Zahlen und Bindestriche enthalten. Der Name der Freigabe muss drei bis 63 Zeichen lang sein und mit einem Buchstaben oder einer Zahl beginnen. Vor und nach jedem Bindestrich muss ein Zeichen stehen, das kein Bindestrich ist.
    
    2. Wählen Sie einen **Typ** für die Freigabe aus. Die verfügbaren Typen sind „SMB“ und „NFS“ („SMB“ ist die Standardeinstellung). „SMB“ ist die Standardeinstellung für Windows-Clients, und „NFS“ wird für Linux-Clients verwendet. Die angezeigten Optionen für SMB- und NFS-Dateifreigaben unterscheiden sich geringfügig.

    3. Geben Sie das gewünschte Speicherkonto für die Freigabe an. Wenn ein Container noch nicht vorhanden ist, wird er im Speicherkonto mit dem neu erstellten Freigabenamen angelegt. Wenn er bereits vorhanden ist, wird der vorhandene Container verwendet.
       > [!IMPORTANT]
       > Stellen Sie sicher, dass für das von Ihnen genutzte Azure Storage-Konto keine Unveränderlichkeitsrichtlinien festgelegt sind, falls Sie es mit einem Data Box Gateway-Gerät verwenden. Weitere Informationen finden Sie unter [Festlegen und Verwalten von Unveränderlichkeitsrichtlinien für Blobspeicher](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage).
    
    4. Wählen Sie für **Speicherdienst** die Option „Blockblob“, „Seitenblob“ oder „Dateien“ aus. Der ausgewählte Diensttyp hängt von dem Format ab, in dem die Daten in Azure gespeichert werden sollen. In diesem Fall sollen die Daten beispielsweise als Blobblöcke in Azure gespeichert werden. Daher wählen wir „Blockblob“ aus. Wenn Sie „Seitenblob“ auswählen, müssen Sie sicherstellen, dass Ihre Daten ganzzahlige Vielfache von 512 Bytes sind. VHDX-Daten sind beispielsweise immer ganzzahlige Vielfache von 512 Bytes.
   
    5. Dieser Schritt hängt davon ab, ob Sie eine SMB- oder NFS-Freigabe erstellen.
     
    - **SMB-Freigabe:** Wählen Sie unter **Alle lokalen Benutzer mit Berechtigungen** die Option **Neu erstellen** oder **Vorhandene verwenden**. Wenn Sie einen neuen lokalen Benutzer erstellen, geben Sie einen Wert für **Benutzername** und **Kennwort** ein, und **bestätigen Sie dann das Kennwort**. Dadurch werden die Berechtigungen dem lokalen Benutzer zugewiesen. Die Änderung der Berechtigungen auf Freigabeebene wird derzeit nicht unterstützt.
    
        ![Hinzufügen einer SMB-Freigabe](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Wenn Sie für diese Freigabedaten das Kontrollkästchen **Nur Lesevorgänge zulassen** aktivieren, können Sie Benutzer angeben, die nur über Lesezugriff verfügen.
        
    - **NFS-Freigabe:** Geben Sie die IP-Adressen der zulässigen Clients an, die auf die Freigabe zugreifen können.

        ![Hinzufügen einer NFS-Freigabe](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. Wählen Sie **Erstellen** aus, um die Freigabe zu erstellen.
    
    Sie werden benachrichtigt, wenn die Freigabe erstellt wird. Nachdem die Freigabe mit den angegebenen Einstellungen erstellt wurde, wird die Kachel **Freigaben** aktualisiert, und die neue Freigabe wird angezeigt.
    
    ![Kachel „Freigaben“ nach der Aktualisierung](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Herstellen einer Verbindung mit der Freigabe

Nun können Sie eine Verbindung mit den Freigaben herstellen, die Sie im vorherigen Schritt erstellt haben. Die auszuführenden Schritte sind ggf. abhängig von der verwendeten Freigabe (SMB oder NFS).

### <a name="connect-to-an-smb-share"></a>Herstellen einer Verbindung mit einer SMB-Freigabe

Stellen Sie über Ihren Windows Server-Client, der mit Ihrem Data Box Gateway-Gerät verbunden ist, eine Verbindung mit einer SMB-Freigabe her, indem Sie die folgenden Befehle eingeben:


1. Geben Sie in einem Befehlsfenster den folgenden Befehl ein:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Geben Sie das Kennwort für die Freigabe ein, wenn Sie dazu aufgefordert werden. Hier sehen Sie eine Beispielausgabe des Befehls.

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2017 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60'  
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. Drücken Sie WINDOWS-TASTE+R. 
3. Geben Sie im Fenster **Ausführen** die IP-Adresse des Geräts (`\\<device IP address>`) an, und wählen Sie anschließend **OK** aus. Der Datei-Explorer wird geöffnet. Die von Ihnen erstellten Freigaben sollten jetzt als Ordner angezeigt werden. Doppelklicken Sie im Datei-Explorer auf eine Freigabe (einen Ordner), um den Inhalt anzuzeigen.
 
    ![Herstellen einer Verbindung mit einer SMB-Freigabe](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    Die Daten werden bei ihrer Generierung in diese Freigaben geschrieben, und das Gerät pusht die Daten in die Cloud.

### <a name="connect-to-an-nfs-share"></a>Herstellen einer Verbindung mit einer NFS-Freigabe

Führen Sie auf dem mit Ihrem Gerät verbundenen Linux-Client die folgenden Schritte aus:

1. Stellen Sie sicher, dass der NFSv4-Client auf dem Client installiert ist. Verwenden Sie zum Installieren des NFS-Clients den folgenden Befehl:

   `sudo apt-get install nfs-common`

    Weitere Informationen finden Sie unter [Install NFSv4 client](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client) (Installieren des NFSv4-Clients).

2. Führen Sie nach der Installation des NFS-Clients den folgenden Befehl aus, um die NFS-Freigabe einzubinden, die Sie auf Ihrem Data Box Gateway-Gerät erstellt haben:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    Vergewissern Sie sich vor dem Einrichten der Bereitstellungen, dass die Verzeichnisse, die als Bereitstellungspunkte auf dem lokalen Computer fungieren, bereits erstellt wurden und keine Dateien oder Unterordner enthalten.

    Das folgende Beispiel zeigt, wie Sie über NFS eine Verbindung mit einer Freigabe auf dem Gateway-Gerät herstellen. Die IP-Adresse des virtuellen Geräts lautet `10.10.10.60`, die Freigabe `mylinuxshare2` wird auf der Ubuntu-VM eingebunden, und der Bereitstellungspunkt ist `/home/databoxubuntuhost/gateway`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> Für dieses Release gelten folgende Einschränkungen:
> - Das Umbenennen einer Datei, die in den Freigaben erstellt wurde, wird nicht unterstützt.
> - Durch das Löschen einer Datei aus einer Freigabe wird der Eintrag im Speicherkonto nicht gelöscht.
> - Wenn Sie Daten mithilfe von `rsync` kopieren, wird die Option `rsync -a` nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie sich mit den folgenden Themen zu Data Box Gateway befasst:

> [!div class="checklist"]
> * Hinzufügen einer Freigabe
> * Herstellen einer Verbindung mit einer Freigabe


Im nächsten Tutorial erfahren Sie, wie Sie Ihr Data Box Gateway verwalten.

> [!div class="nextstepaction"]
> [Verwenden der lokalen Webbenutzeroberfläche zum Verwalten eines Data Box Gateway](https://aka.ms/dbg-docs)