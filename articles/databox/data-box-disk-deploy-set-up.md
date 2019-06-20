---
title: Tutorial zum Entpacken, Verbinden und Entsperren von Azure Data Box Disk | Microsoft-Dokumentation
description: Verwenden Sie dieses Tutorial, um zu erfahren, wie Sie Ihren Azure Data Box-Datenträger einrichten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 10/31/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 34d62df989da80c84bad92a90fc2253c416a4924
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939645"
---
# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>Tutorial: Entpacken, Verbinden und Entsperren von Azure Data Box Disk

In diesem Tutorial wird beschrieben, wie Sie Ihren Azure Data Box-Datenträger entpacken, verbinden und entsperren.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Entpacken Ihres Data Box-Datenträgers
> * Herstellen der Verbindung mit Datenträgern und Abrufen des Hauptschlüssels
> * Entsperren von Datenträgern auf Windows-Client
> * Entsperren von Datenträgern auf Linux-Client

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie beginnen:

1. Sie haben die Schritte unter [Tutorial: Bestellen von Azure Data Box Disk](data-box-disk-deploy-ordered.md).
2. Sie haben Ihre Datenträger erhalten, und der Auftragsstatus im Portal wurde aktualisiert und in **Übermittelt** geändert.
3. Sie verfügen über einen Clientcomputer, auf dem Sie das Tool zum Entsperren von Data Box Disk installieren können. Ihr Client-Computer muss folgende Voraussetzungen erfüllen:
    - Es muss ein [unterstütztes Betriebssystem](data-box-disk-system-requirements.md#supported-operating-systems-for-clients) ausgeführt werden.
    - Wenn es sich um einen Windows-Client handelt, muss weitere [erforderliche Software](data-box-disk-system-requirements.md#other-required-software-for-windows-clients) darauf installiert sein.  

## <a name="unpack-your-disks"></a>Auspacken der Datenträger

 Führen Sie die folgenden Schritte aus, um Ihre Datenträger auszupacken.

1. Die Data Box-Datenträger werden in einem kleinen Versandpaket gesendet. Öffnen Sie das Paket, und nehmen Sie den Inhalt heraus. Überprüfen Sie, ob das Paket ein bis fünf SSD-Datenträger (Solid-State Disks) und ein USB-Verbindungskabel pro Datenträger enthält. Überprüfen Sie das Paket auf Zeichen einer Manipulation oder andere erkennbare Beschädigungen. 

    ![Versandpaket für Data Box-Datenträger](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. Öffnen Sie das Versandpaket nicht, falls es manipuliert wurde oder stark beschädigt ist. Wenden Sie sich an den Microsoft-Support, um Hilfe bei der Ermittlung zu erhalten, ob sich die Datenträger in einem einwandfreien Zustand befinden oder ob eine Ersatzsendung erforderlich ist.
3. Prüfen Sie, ob das Paket über eine durchsichtige Kunststoffhülle mit einem Versandetikett für die Rücksendung verfügt (unter dem Etikett für die Zusendung an Sie). Falls dieses Etikett nicht vorhanden oder beschädigt ist, können Sie im Azure-Portal jederzeit ein neues Etikett herunterladen und ausdrucken. 

    ![Versandetikett für Data Box-Datenträger](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. Heben Sie das Paket und die Schaumteile für den Schutz des Inhalts für die Rücksendung der Datenträger auf.

## <a name="connect-to-disks-and-get-the-passkey"></a>Herstellen der Verbindung mit Datenträgern und Abrufen des Hauptschlüssels 

1. Verwenden Sie das beigefügte Kabel, um den Datenträger an einen Clientcomputer anzuschließen, auf dem ein unterstütztes Betriebssystem ausgeführt wird (siehe „Voraussetzungen“). 

    ![Anschließen des Data Box-Datenträgers](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)    
    
2. Navigieren Sie im Azure-Portal zu **Allgemein > Gerätedetails**. Verwenden Sie das Symbol „Kopieren“, um den Hauptschlüssel zu kopieren. Dieser Hauptschlüssel wird zum Entsperren der Datenträger verwendet.

    ![Hauptschlüssel zum Entsperren von Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-get-passkey.png) 

Je nachdem, ob Sie mit einem Windows- oder Linux-Client verbunden sind, unterscheiden sich die Schritte zum Entsperren der Datenträger.

## <a name="unlock-disks-on-windows-client"></a>Entsperren von Datenträgern auf Windows-Client

Führen Sie die folgenden Schritte aus, um Ihre Datenträger anzuschließen und zu entsperren.
     
1. Navigieren Sie im Azure-Portal zu **Allgemein > Gerätedetails**. 
2. Laden Sie das entsprechende Data Box Disk-Toolset für den Windows-Client herunter. Dieses Toolset enthält drei Tools: Data Box Disk-Tool zum Entsperren, Data Box Disk-Überprüfungstool und Data Box Disk-Tool zum Aufteilen/Kopieren. 

    In diesem Verfahren verwenden Sie nur das Tool zum Entsperren von Data Box-Datenträgern. Die anderen beiden Tools werden später verwendet.

    > [!div class="nextstepaction"]
    > [Herunterladen des Data Box Disk-Toolsets für Windows](https://aka.ms/databoxdisktoolswin)         

3. Extrahieren Sie das Toolset auf demselben Computer, den Sie zum Kopieren der Daten verwenden möchten. 
4. Öffnen Sie ein Eingabeaufforderungsfenster, oder führen Sie Windows PowerShell auf demselben Computer als Administrator aus.
5. (Optional) Um zu überprüfen, ob der zum Entsperren des Datenträgers verwendete Computer die Anforderungen des Betriebssystems erfüllt, können Sie den Befehl für die Systemüberprüfung ausführen. Nachfolgend sehen Sie eine Beispielausgabe. 

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

6. Führen Sie `DataBoxDiskUnlock.exe` aus, und geben Sie den Hauptschlüssel ein, den Sie in [Herstellen der Verbindung mit Datenträgern und Abrufen des Hauptschlüssels](#connect-to-disks-and-get-the-passkey) abgerufen haben. Der Laufwerkbuchstabe, der dem Datenträger zugewiesen ist, wird angezeigt. Nachfolgend sehen Sie eine Beispielausgabe.

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkey :
    testpasskey1
    
    Following volumes are unlocked and verified.
    Volume drive letters: D:
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

7. Wiederholen Sie die Schritte zum Entsperren bei jedem erneuten Anschließen des Datenträgers. Verwenden Sie den Befehl `help`, falls Sie Hilfe zum Tool zum Entsperren von Data Box Disk benötigen.   

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKey:<passkey_from_Azure_portal>
    
    Example: DataBoxUnlock /PassKey:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help
    
    /PassKey:        Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  
8. Nachdem der Datenträger entsperrt wurde, können Sie den Inhalt des Datenträgers anzeigen.    

    ![Inhalt des Data Box-Datenträgers](media/data-box-disk-deploy-set-up/data-box-disk-content.png) 

## <a name="unlock-disks-on-linux-client"></a>Entsperren von Datenträgern auf Linux-Client

1. Navigieren Sie im Azure-Portal zu **Allgemein > Gerätedetails**. 
2. Laden Sie das entsprechende Data Box Disk-Toolset für den Linux-Client herunter.  

    > [!div class="nextstepaction"]
    > [Herunterladen des Data Box Disk-Toolsets für Linux](https://aka.ms/databoxdisktoolslinux) 

3. Öffnen Sie ein Terminal auf Ihrem Linux-Client. Navigieren Sie zum Ordner, in den Sie die Software herunterladen haben. Ändern Sie die Dateiberechtigungen, sodass Sie diese Dateien ausführen können. Geben Sie folgenden Befehl ein: 

    `chmod +x DataBoxDiskUnlock_x86_64` 
    
    `chmod +x DataBoxDiskUnlock_Prep.sh` 
 
    Nachfolgend sehen Sie eine Beispielausgabe. Nach der chmod-Befehl ausgeführt wurde, können Sie durch Ausführen des `ls`-Befehls überprüfen, ob die Dateiberechtigungen geändert wurden. 
 
    ```
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_x86_64  
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_Prep.sh   
        [user@localhost Downloads]$ ls -l  
        -rwxrwxr-x. 1 user user 1152664 Aug 10 17:26 DataBoxDiskUnlock_x86_64  
        -rwxrwxr-x. 1 user user 795 Aug 5 23:26 DataBoxDiskUnlock_Prep.sh
    ```
4. Führen Sie das Skript so aus, dass es alle für die Software zum Entsperren von Data Box Disk erforderlichen Binärdateien installiert. Verwenden Sie `sudo` zum Ausführen des Befehls als Root-Benutzer. Sobald die Binärdateien erfolgreich installiert sind, wird im Terminal ein entsprechender Hinweis angezeigt.

    `sudo ./DataBoxDiskUnlock_Prep.sh`

    Das Skript überprüft zuerst, ob auf Ihrem Client-Computer ein unterstütztes Betriebssystem ausgeführt wird. Nachfolgend sehen Sie eine Beispielausgabe. 
 
    ```
    [user@localhost Documents]$ sudo ./DataBoxDiskUnlock_Prep.sh 
        OS = CentOS Version = 6.9 
        Release = CentOS release 6.9 (Final) 
        Architecture = x64 
    
        The script will install the following packages and dependencies. 
        epel-release 
        dislocker 
        ntfs-3g 
        fuse-dislocker 
        Do you wish to continue? y|n :|
    ```
    
 
5. Geben Sie `y` ein, um die Installation fortzusetzen. Das Skript installiert folgende Pakete: 
   - **epel-release**: Repository, das die folgenden drei Pakete enthält. 
   - **„dislocker“ und „fuse-dislocker“** : Dieses Dienstprogramm dient zur Entschlüsselung von BitLocker-verschlüsselten Datenträgern. 
   - **ntfs-3g**: Dieses Paket dient zur Bereitstellung von NTFS-Volumes. 
 
     Sobald die Pakete erfolgreich installiert wurden, wird im Terminal eine entsprechende Benachrichtigung angezeigt.     
     ```
     Dependency Installed: compat-readline5.x86 64 0:5.2-17.I.el6 dislocker-libs.x86 64 0:0.7.1-8.el6 mbedtls.x86 64 0:2.7.4-l.el6        ruby.x86 64 0:1.8.7.374-5.el6 
     ruby-libs.x86 64 0:1.8.7.374-5.el6 
     Complete! 
     Loaded plugins: fastestmirror, refresh-packagekit, security 
     Setting up Remove Process 
     Resolving Dependencies 
     --> Running transaction check 
     ---> Package epel-release.noarch 0:6-8 will be erased --> Finished Dependency Resolution 
     Dependencies Resolved 
     Package        Architecture        Version        Repository        Size 
     Removing:  epel-release        noarch         6-8        @extras        22 k 
     Transaction Summary                                 
     Remove        1 Package(s) 
     Installed size: 22 k 
     Downloading Packages: 
     Running rpmcheckdebug 
     Running Transaction Test 
     Transaction Test Succeeded 
     Running Transaction 
     Erasing : epel-release-6-8.noarch 
     Verifying : epel-release-6-8.noarch 
     Removed: 
     epel-release.noarch 0:6-8 
     Complete! 
     Dislocker is installed by the script. 
     OpenSSL is already installed.
     ```

6. Führen Sie das Tool zum Entsperren von Data Box Disk aus. Geben Sie den Hauptschlüssel aus dem Azure-Portal ein, den Sie in [Herstellen der Verbindung mit Datenträgern und Abrufen des Hauptschlüssels](#connect-to-disks-and-get-the-passkey) abgerufen haben. Geben Sie optional eine Liste mit BitLocker verschlüsselten Volumes an, die entsperrt werden sollen. Die Hauptschlüssel und die Volumeliste müssen in einfachen Anführungszeichen angegeben werden. 

    Geben Sie folgenden Befehl ein.
 
    `sudo ./DataBoxDiskUnlock_x86_64 /PassKey:’<Your passkey from Azure portal>’          

    Die Beispielausgabe wird unten gezeigt. 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Passkey:’qwerqwerqwer’  
    
    START: Mon Aug 13 14:25:49 2018 
    Volumes: /dev/sdbl 
    Passkey: qwerqwerqwer 
    
    Volumes for data copy : 
    /dev/sdbl: /mnt/DataBoxDisk/mountVoll/ 
    END: Mon Aug 13 14:26:02 2018
    ```
    Der Bereitstellungspunkt für das Volume, auf das Sie Ihre Daten kopieren können, wird angezeigt.

7. Wiederholen Sie die Schritte zum Entsperren bei jedem erneuten Anschließen des Datenträgers. Verwenden Sie den Befehl `help`, falls Sie Hilfe zum Tool zum Entsperren von Data Box Disk benötigen. 
    
    `sudo ./DataBoxDiskUnlock_x86_64 /Help` 

    Die Beispielausgabe wird unten gezeigt. 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Help  
    START: Mon Aug 13 14:29:20 2018 
    USAGE: 
    sudo DataBoxDiskUnlock /PassKey:’<passkey from Azure_portal>’ 
    
    Example: sudo DataBoxDiskUnlock /PassKey:’passkey’ 
    Example: sudo DataBoxDiskUnlock /PassKey:’passkey’ /Volumes:’/dev/sdbl’ 
    Example: sudo DataBoxDiskUnlock /Help Example: sudo DataBoxDiskUnlock /Clean 
    
    /PassKey: This option takes a passkey as input and unlocks all of your disks. 
    Get the passkey from your Data Box Disk order in Azure portal. 
    /Volumes: This option is used to input a list of BitLocker encrypted volumes. 
    /Help: This option provides help on the tool usage and examples. 
    /Unmount: This option unmounts all the volumes mounted by this tool. 
   
    END: Mon Aug 13 14:29:20 2018 [user@localhost Downloads]$
    ```
    
8. Nachdem der Datenträger entsperrt wurde, können Sie zum Bereitstellungspunkt wechseln und den Inhalt des Datenträgers anzeigen. Nun können Sie die Daten in *BlockBlob*- oder *PageBlob*-Ordner kopieren. 

    ![Inhalt des Data Box-Datenträgers](media/data-box-disk-deploy-set-up/data-box-disk-content-linux.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Informationen zu Azure Data Box-Datenträgern erhalten, z.B.:

> [!div class="checklist"]
> * Entpacken Ihres Data Box-Datenträgers
> * Herstellen der Verbindung mit Datenträgern und Abrufen des Hauptschlüssels
> * Entsperren von Datenträgern auf Windows-Client
> * Entsperren von Datenträgern auf Linux-Client


Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Daten auf Ihren Data Box-Datenträger kopieren.

> [!div class="nextstepaction"]
> [Kopieren von Daten auf Ihren Data Box-Datenträger](./data-box-disk-deploy-copy-data.md)

