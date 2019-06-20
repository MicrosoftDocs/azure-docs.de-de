---
title: Tutorial – Sichern von virtuellen Linux-Computern im Azure-Portal | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie das Azure-Portal zum Schützen Ihrer virtuellen Linux-Computer mit Azure Backup verwenden.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 99ec8ea6a5b08323021a168e8ec47b976e1ae7f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60739389"
---
# <a name="tutorial-back-up-and-restore-files-for-linux-virtual-machines-in-azure"></a>Tutorial: Sichern und Wiederherstellen von Dateien für virtuelle Linux-Computer in Azure

Sie können Ihre Daten schützen, indem Sie in regelmäßigen Abständen Sicherungen erstellen. Azure Backup erstellt Wiederherstellungspunkte, die in geografisch redundanten Recovery-Tresoren gespeichert werden. Wenn Sie eine Wiederherstellung von einem Wiederherstellungspunkt durchführen, können Sie den gesamten virtuellen Computer oder bestimmte Dateien wiederherstellen. In diesem Artikel wird erläutert, wie eine einzelne Datei von einem virtuellen Linux-Computer mit nginx wiederhergestellt wird. Wenn Sie noch nicht über einen virtuellen Computer verfügen, können Sie diesen mit dem [Linux-Schnellstart](quick-create-cli.md) erstellen. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer Sicherung eines virtuellen Computers
> * Planen einer täglichen Sicherung
> * Wiederherstellen einer Datei aus einer Sicherung

## <a name="backup-overview"></a>Übersicht über Azure Backup

Wenn der Azure Backup-Dienst eine Sicherung initiiert, löst er die Sicherungserweiterung zum Erstellen einer Momentaufnahme aus. Der Azure Backup-Dienst verwendet unter Linux die Erweiterung _VMSnapshotLinux_. Die Erweiterung wird während der ersten VM-Sicherung installiert, wenn die VM ausgeführt wird. Wenn die VM nicht ausgeführt wird, erstellt der Backup-Dienst eine Momentaufnahme des zugrunde liegenden Speichers (da keine Schreibvorgänge der Anwendung erfolgen, während die VM beendet ist).

Standardmäßig erstellt Azure Backup eine dateisystemkonsistente Sicherung für Linux-VMs, der Dienst kann aber auch für [anwendungskonsistente Sicherungen mithilfe des Pre-Skript- und Post-Skript-Frameworks](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent) konfiguriert werden. Nachdem der Azure Backup-Dienst die Momentaufnahme erstellt hat, werden die Daten in den Sicherungstresor übertragen. Um die Effizienz zu maximieren, werden vom Dienst nur diejenigen Datenblöcke bestimmt und übertragen, die seit der vorherigen Sicherung geändert wurden.

Wenn die Datenübertragung abgeschlossen ist, wird die Momentaufnahme entfernt und ein Wiederherstellungspunkt erstellt.


## <a name="create-a-backup"></a>Erstellen einer Sicherung
Erstellen Sie eine geplante tägliche Sicherung in einem Recovery Services-Tresor:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie im Menü auf der linken Seite **Virtuelle Computer** aus. 
3. Wählen Sie in der Liste eine zu sichernde VM aus.
4. Klicken Sie auf dem Blatt des virtuellen Computers im Abschnitt **Einstellungen** auf **Sicherung**. Das Blatt **Sicherung aktivieren** wird geöffnet.
5. Wenn kein **Recovery Services-Tresor** vorhanden ist, können Sie auf **Neu erstellen** klicken und den Namen für den neuen Tresor angeben. Ein neuer Tresor wird in derselben Ressourcengruppe und an demselben Standort wie der virtuelle Computer erstellt.
6. Klicken Sie auf **Sicherungsrichtlinie**. In diesem Beispiel behalten Sie die Standardwerte bei. Klicken Sie auf **OK**.
7. Klicken Sie auf dem Blatt **Sicherung aktivieren** auf **Sicherung aktivieren**. Dadurch wird eine tägliche Sicherung basierend auf dem Standardzeitplan erstellt.
10. Um einen erste Wiederherstellungspunkt zu erstellen, klicken Sie auf dem Blatt **Sicherung** auf **Jetzt sichern**.
11. Klicken Sie auf dem Blatt **Jetzt sichern** auf das Kalendersymbol, verwenden Sie das Kalendersteuerelement zum Auswählen des letzten Aufbewahrungstags des Wiederherstellungspunkts, und klicken Sie auf **Sichern**.
12. Auf dem Blatt **Sicherung** für Ihren virtuellen Computer wird die Anzahl der Wiederherstellungspunkte angezeigt, die abgeschlossen sind.

    ![Wiederherstellungspunkte](./media/tutorial-backup-vms/backup-complete.png)

Die erste Sicherung dauert ca. 20 Minuten. Fahren Sie nach dem Abschluss der Sicherung mit dem nächsten Teil dieses Tutorials fort.

## <a name="restore-a-file"></a>Wiederherstellen einer Datei

Wenn Sie eine Datei versehentlich löschen oder daran Änderungen vornehmen, können Sie diese Datei über die Dateiwiederherstellung aus dem Sicherungstresor wiederherstellen. Die Dateiwiederherstellung verwendet ein Skript, das auf dem virtuellen Computer ausgeführt wird, um den Wiederherstellungspunkt als lokales Laufwerk einzubinden. Diese Laufwerke bleiben 12 Stunden eingebunden, damit Sie Dateien aus dem Wiederherstellungspunkt kopieren und auf dem virtuellen Computer wiederherstellen können.  

In diesem Beispiel wird beschrieben, wie Sie die nginx-Standardwebseite „/var/www/html/index.nginx-debian.html“ wiederherstellen. Die öffentliche IP-Adresse der VM in diesem Beispiel ist *13.69.75.209*. Sie können die IP-Adresse des virtuellen Computers wie folgt suchen:

 ```bash 
 az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
 ```

 
1. Öffnen Sie auf dem lokalen Computer einen Browser, und geben Sie die öffentliche IP-Adresse Ihres virtuellen Computers ein, um die nginx-Standardwebseite anzuzeigen.

    ![nginx-Standardwebseite](./media/tutorial-backup-vms/nginx-working.png)

1. Stellen Sie eine SSH-Verbindung mit Ihrem virtuellen Computer her.

    ```bash
    ssh 13.69.75.209
    ```
2. Löschen Sie „/var/www/html/index.nginx-debian.html“.

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```
    
4. Aktualisieren Sie den Browser auf dem lokalen Computer durch Drücken von STRG+F5, um zu zeigen, dass diese nginx-Standardseite nicht mehr angezeigt wird.

    ![nginx-Standardwebseite](./media/tutorial-backup-vms/nginx-broken.png)
    
1. Melden Sie sich auf dem lokalen Computer beim [Azure-Portal](https://portal.azure.com/) an.
6. Wählen Sie im Menü auf der linken Seite **Virtuelle Computer** aus. 
7. Wählen Sie den virtuellen Computer in der Liste aus.
8. Klicken Sie auf dem Blatt des virtuellen Computers im Abschnitt **Einstellungen** auf **Sicherung**. Das Blatt **Sicherung** wird geöffnet. 
9. Wählen Sie im Menü oben auf dem Blatt die Option **Dateiwiederherstellung** aus. Das Blatt **Dateiwiederherstellung** wird geöffnet.
10. Wählen Sie in **Schritt 1: Auswählen eines Wiederherstellungspunkts** in der Dropdownliste einen Wiederherstellungspunkt aus.
11. Wählen Sie in **Schritt 2: Herunterladen des Skripts zum Suchen und Wiederherstellen von Dateien** die Schaltfläche **Ausführbare Datei herunterladen** aus. Speichern Sie die heruntergeladene Datei auf dem lokalen Computer.
7. Klicken Sie auf **Skript herunterladen**, um die Skriptdatei lokal herunterzuladen.
8. Öffnen Sie eine Bash-Eingabeaufforderung, und geben Sie Folgendes ein. Ersetzen Sie dabei *Linux_myVM_05-05-2017.sh* durch den richtigen Pfad und Dateinamen für das Skript, das Sie heruntergeladen haben, *Azureuser* durch den Benutzernamen für den virtuellen Computer und *13.69.75.209* durch die öffentliche IP-Adresse für Ihren virtuellen Computer.
    
    ```bash
    scp Linux_myVM_05-05-2017.sh azureuser@13.69.75.209:
    ```
    
9. Öffnen Sie auf dem lokalen Computer eine SSH-Verbindung mit dem virtuellen Computer.

    ```bash
    ssh 13.69.75.209
    ```
    
10. Fügen Sie der Skriptdatei auf dem virtuellen Computer Ausführungsberechtigungen hinzu.

    ```bash
    chmod +x Linux_myVM_05-05-2017.sh
    ```
    
11. Führen Sie auf dem virtuellen Computer das Skript zum Einbinden des Wiederherstellungspunkts als Dateisystem aus.

    ```bash
    ./Linux_myVM_05-05-2017.sh
    ```
    
12. Die Ausgabe des Skripts enthält den Pfad zum Bereitstellungspunkt. Die Ausgabe sieht in etwa wie folgt aus:

    ```bash
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
                          
    Connecting to recovery point using ISCSI service...
    
    Connection succeeded!
    
    Please wait while we attach volumes of the recovery point to this machine...
                         
    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath 

    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170505191055/Volume1

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

12. Kopieren Sie auf Ihrem virtuellen Computer die nginx Standardwebseite aus dem Bereitstellungspunkt an die Stelle, an der die Datei gelöscht haben.

    ```bash
    sudo cp ~/myVM-20170505191055/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```
    
17. Öffnen Sie auf dem lokalen Computer die Browserregisterkarte, auf der Sie mit der IP-Adresse des virtuellen Computers verbunden sind, der die nginx-Standardseite anzeigt. Drücken Sie STRG+F5, um die Browserseite zu aktualisieren. Die Standardseite sollte wieder in Betrieb sein.

    ![nginx-Standardwebseite](./media/tutorial-backup-vms/nginx-working.png)

18. Wechseln Sie auf dem lokalen Computer zurück zur Browserregisterkarte für das Azure-Portal, und klicken Sie in **Schritt 3:  Aufheben der Bereitstellung der Datenträger nach der Wiederherstellung** auf die Schaltfläche **Bereitstellung der Datenträger aufheben**. Wenn Sie vergessen, diesen Schritt durchzuführen, wird die Verbindung mit dem Bereitstellungspunkt nach 12 Stunden automatisch getrennt. Nach diesen 12 Stunden müssen Sie ein neues Skript zum Erstellen eines neuen Bereitstellungspunkts herunterladen.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer Sicherung eines virtuellen Computers
> * Planen einer täglichen Sicherung
> * Wiederherstellen einer Datei aus einer Sicherung

Im nächsten Tutorial erhalten Sie Informationen zur Überwachung virtueller Computer.

> [!div class="nextstepaction"]
> [Virtual machine governance with Azure PowerShell](tutorial-govern-resources.md) (Steuern von virtuellen Computern mit Azure PowerShell)

