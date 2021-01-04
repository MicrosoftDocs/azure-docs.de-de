---
title: 'Tutorial: Wiederherstellen von Dateien auf einem virtuellen Computer mit Azure Backup'
description: Informationen zum Ausführen von Wiederherstellungen auf Dateiebene auf einer Azure-VM mit Sicherungs- und Wiederherstellungsdiensten.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d977919b806be32b84001a9b91dc9e396fbd63ce
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96557908"
---
# <a name="restore-files-to-a-virtual-machine-in-azure"></a>Wiederherstellen von Dateien auf einem virtuellen Computer in Azure

Azure Backup erstellt Wiederherstellungspunkte, die in geografisch redundanten Recovery-Tresoren gespeichert werden. Wenn Sie eine Wiederherstellung von einem Wiederherstellungspunkt durchführen, können Sie den gesamten virtuellen Computer oder einzelne Dateien wiederherstellen. In diesem Artikel wird erläutert, wie Sie einzelne Dateien wiederherstellen. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Auflisten und Auswählen von Wiederherstellungspunkten
> * Herstellen einer Verbindung eines Wiederherstellungspunkts mit einer VM
> * Wiederherstellen von Dateien aus einem Wiederherstellungspunkt

## <a name="prerequisites"></a>Voraussetzungen

Dieses Tutorial erfordert eine Linux-VM, die mit Azure Backup geschützt wurde. Um eine versehentliche Dateilöschung und den Wiederherstellungsprozess zu simulieren, löschen Sie eine Seite von einem Webserver. Wenn Sie eine Linux-VM benötigen, die einen Webserver ausführt und mit Azure Backup geschützt wurde, lesen Sie [Back up a virtual machine in Azure with the CLI](quick-backup-vm-cli.md) (Sichern eines virtuellen Computers in Azure mit der CLI).

Vorbereiten der Umgebung:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Für diesen Artikel ist mindestens Version 2.0.18 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="backup-overview"></a>Übersicht über Azure Backup

Wenn Azure eine Sicherung initiiert, erstellt die Sicherungserweiterung auf dem virtuellen Computer eine Zeitpunkt-Momentaufnahme. Die Sicherungserweiterung wird auf dem virtuellen Computer installiert, wenn die erste Sicherung angefordert wird. Azure Backup kann auch eine Momentaufnahme des zugrunde liegenden Speichers erstellen, falls die VM zum Zeitpunkt der Sicherung nicht ausgeführt wird.

Standardmäßig führt Azure Backup eine konsistente Sicherung des Dateisystems durch. Nachdem Azure Backup die Momentaufnahme erstellt hat, werden die Daten in den Recovery Services-Tresor übertragen. Um die Effizienz zu maximieren, bestimmt und überträgt Azure Backup nur diejenigen Datenblöcke, die seit der vorherigen Sicherung geändert wurden.

Wenn die Datenübertragung abgeschlossen ist, wird die Momentaufnahme entfernt und ein Wiederherstellungspunkt erstellt.

## <a name="delete-a-file-from-a-vm"></a>Löschen einer Datei von einem virtuellen Computer

Wenn Sie versehentlich eine Datei löschen oder Änderungen an ihr vornehmen, können Sie einzelne Dateien von einem Wiederherstellungspunkt aus wiederherstellen. Dieser Prozess ermöglicht Ihnen, die in einem Wiederherstellungspunkt gesicherten Dateien zu durchsuchen, um nur die benötigten Dateien wiederherzustellen. In diesem Beispiel wird eine Datei von einem Webserver gelöscht, um den Prozess der Wiederherstellung auf Dateiebene zu veranschaulichen.

1. Um die Verbindung mit Ihrer VM herzustellen, rufen Sie die IP-Adresse Ihres virtuellen Computers mit dem Befehl [az vm show](/cli/azure/vm#az-vm-show) ab:

     ```azurecli-interactive
     az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
     ```

2. Um zu bestätigen, dass die Website derzeit funktioniert, rufen Sie in einem Webbrowser die öffentliche IP-Adresse Ihres virtuellen Computers auf. Lassen Sie das Webbrowserfenster geöffnet.

    ![NGINX-Standardwebseite](./media/tutorial-restore-files/nginx-working.png)

3. Stellen Sie die Verbindung mit Ihrem virtuellen Computer mit SSH her. Ersetzen Sie *publicIpAddress* mit der öffentlichen IP-Adresse, die Sie in einem vorherigen Befehl erhalten haben:

    ```bash
    ssh publicIpAddress
    ```

4. Löschen Sie die Standardseite unter */var/www/html/index.nginx-debian.html* wie folgt vom Webserver:

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```

5. Aktualisieren Sie in Ihrem Webbrowser die Webseite. Die Website lädt die Seite nicht mehr, wie im folgenden Beispiel gezeigt:

    ![NGINX-Website lädt die Standardseite nicht mehr](./media/tutorial-restore-files/nginx-broken.png)

6. Schließen Sie die SSH-Sitzung zu Ihrem virtuellen Computer wie folgt:

    ```bash
    exit
    ```

## <a name="generate-file-recovery-script"></a>Generieren des Skripts zur Dateiwiederherstellung

Um Ihre Dateien wiederherzustellen, bietet Azure Backup ein Skript zur Ausführung auf Ihrem virtuellen Computer, das die Verbindung zu Ihrem Wiederherstellungspunkt als lokales Laufwerk herstellt. Sie können dieses lokale Laufwerk durchsuchen, Dateien auf der VM selbst wiederherstellen und dann die Verbindung zum Wiederherstellungspunkt trennen. Azure Backup setzt die Sicherung Ihrer Daten auf Basis der zugewiesenen Zeitplan- und Aufbewahrungsrichtlinie fort.

1. Verwenden Sie zum Auflisten der Wiederherstellungspunkte für Ihren virtuellen Computer [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list). In diesem Beispiel wird der letzte Wiederherstellungspunkt für den virtuellen Computer mit dem Namen *myVM* ausgewählt, der in *myRecoveryServicesVault* geschützt wird:

    ```azurecli-interactive
    az backup recoverypoint list \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --query [0].name \
        --output tsv
    ```

2. Um das Skript abzurufen, das eine Verbindung des Wiederherstellungspunkts mit Ihrer VM herstellt bzw. ihn dort einbindet, verwenden Sie [az backup restore files mount-rp](/cli/azure/backup/restore/files#az-backup-restore-files-mount-rp). Im folgenden Beispiel wird das Skript für den virtuellen Computer mit dem Namen *myVM* abgerufen, der in *myRecoveryServicesVault* geschützt wird.

    Ersetzen Sie *myRecoveryPointName* mit dem Namen des Wiederherstellungspunkts, den Sie im vorherigen Befehl abgerufen haben:

    ```azurecli-interactive
    az backup restore files mount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

    Das Skript wird heruntergeladen, und ein Kennwort wird wie im folgenden Beispiel angezeigt:

    ```output
    File downloaded: myVM_we_1571974050985163527.sh. Use password c068a041ce12465
    ```

3. Um das Skript auf Ihren virtuellen Computer zu übertragen, verwenden Sie Secure Copy (SCP). Geben Sie den Namen des heruntergeladenen Skripts an, und ersetzen Sie *publicIpAddress* mit der öffentlichen IP-Adresse Ihres virtuellen Computers. Stellen Sie sicher, dass Sie das nachfolgende `:` am Ende des SCP-Befehls wie folgt einschließen:

    ```bash
    scp myVM_we_1571974050985163527.sh 52.174.241.110:
    ```

## <a name="restore-file-to-your-vm"></a>Wiederherstellen der Datei auf Ihrem virtuellen Computer

Wenn das Wiederherstellungsskript auf Ihren virtuellen Computer kopiert worden ist, können Sie eine Verbindung mit dem Wiederherstellungspunkt herstellen und Dateien wiederherstellen.

>[!NOTE]
> [Hier](backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script) können Sie überprüfen, ob Sie das Skript auf Ihrem virtuellen Computer ausführen können, bevor Sie den Vorgang fortsetzen.

1. Stellen Sie die Verbindung mit Ihrem virtuellen Computer mit SSH her. Ersetzen Sie *publicIpAddress* wie folgt durch die öffentliche IP-Adresse Ihres virtuellen Computers:

    ```bash
    ssh publicIpAddress
    ```

2. Damit Ihr Skript ordnungsgemäß ausgeführt werden kann, fügen Sie mit **chmod** Ausführungsberechtigungen hinzu. Geben Sie den Namen Ihres eigenen Skripts ein:

    ```bash
    chmod +x myVM_we_1571974050985163527.sh
    ```

3. Um den Wiederherstellungspunkt einzubinden, führen Sie das Skript aus. Geben Sie den Namen Ihres eigenen Skripts ein:

    ```bash
    ./myVM_we_1571974050985163527.sh
    ```

    Während das Skript ausgeführt wird, werden Sie aufgefordert, ein Kennwort für den Zugriff auf den Wiederherstellungspunkt einzugeben. Geben Sie das Kennwort ein, das in der Ausgabe des vorherigen [az backup restore files mount-rp](/cli/azure/backup/restore/files#az-backup-restore-files-mount-rp)-Befehls angezeigt wurde, der das Wiederherstellungsskript generiert hat.

    Die Ausgabe des Skripts enthält den Pfad zum Wiederherstellungspunkt. Die folgende Beispielausgabe zeigt, dass der Wiederherstellungspunkt unter */home/azureuser/myVM-20170919213536/Volume1* eingebunden wird:

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : c068a041ce12465

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170919213536/Volume1

    ************ Open File Explorer to browse for files. ************
    ```

4. Kopieren Sie mit **cp** die NGINX-Standardwebseite aus dem eingebundenen Wiederherstellungspunkt zurück an den ursprünglichen Dateispeicherort. Ersetzen Sie den Bereitstellungspunkt */home/azureuser/myVM-20170919213536/Volume1* mit Ihrem eigenen Standort:

    ```bash
    sudo cp /home/azureuser/myVM-20170919213536/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```

5. Aktualisieren Sie in Ihrem Webbrowser die Webseite. Die Website wird jetzt wieder richtig geladen, wie im folgenden Beispiel gezeigt:

    ![NGINX-Website wird nun ordnungsgemäß geladen.](./media/tutorial-restore-files/nginx-restored.png)

6. Schließen Sie die SSH-Sitzung zu Ihrem virtuellen Computer wie folgt:

    ```bash
    exit
    ```

7. Heben Sie die Einbindung des Wiederherstellungspunkts auf Ihrem virtuellen Computer mit [az backup restore files unmount-rp](/cli/azure/backup/restore/files#az-backup-restore-files-unmount-rp) auf. Im folgenden Beispiel wird die Einbindung des Wiederherstellungspunkt auf dem virtuellen Computer mit dem Namen *myVM* in *myRecoveryServicesVault* aufgehoben.

    Ersetzen Sie *myRecoveryPointName* mit dem Namen Ihres Wiederherstellungspunkts, den Sie in den vorherigen Befehlen abgerufen haben:

    ```azurecli-interactive
    az backup restore files unmount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die Verbindung eines Wiederherstellungspunkts mit einem virtuellen Computer hergestellt und Dateien für einen Webserver wiederhergestellt. Sie haben Folgendes gelernt:

> [!div class="checklist"]
>
> * Auflisten und Auswählen von Wiederherstellungspunkten
> * Herstellen einer Verbindung eines Wiederherstellungspunkts mit einer VM
> * Wiederherstellen von Dateien aus einem Wiederherstellungspunkt

Fahren Sie mit dem nächsten Tutorial fort, um Informationen zum Sichern von Windows Server in Azure zu erhalten.

> [!div class="nextstepaction"]
> [Sichern von Windows Server in Azure](tutorial-backup-windows-server-to-azure.md)
