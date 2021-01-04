---
title: 'Tutorial: Kopieren von VHDs auf verwaltete Datenträger'
titleSuffix: Azure Data Box
description: Hier erfahren Sie, wie Sie Daten von VHDs aus lokalen VM-Workloads auf Ihre Azure Data Box kopieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/20/2019
ms.author: alkohli
ms.openlocfilehash: 1394cf6511a65a0e406e51229953e8666d4d4d8d
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337677"
---
# <a name="tutorial-use-data-box-to-import-data-as-managed-disks-in-azure"></a>Tutorial: Verwenden von Data Box zum Importieren von Daten als verwaltete Datenträger in Azure

In diesem Tutorial erfahren Sie, wie Sie die Azure Data Box verwenden, um Ihre lokalen VHDs zu verwalteten Datenträgern in Azure zu migrieren. Die VHDs lokaler virtueller Computer werden in Form von Seitenblobs auf die Data Box kopiert und als verwaltete Datenträger in Azure hochgeladen. Diese verwalteten Datenträger können dann an virtuelle Azure-Computer angefügt werden.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Überprüfen der Voraussetzungen
> * Herstellen einer Verbindung mit der Data Box
> * Kopieren von Daten auf die Data Box

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie beginnen:

1. Sie haben die Schritte unter [Tutorial: Einrichten der Azure Data Box](data-box-deploy-set-up.md) ausgeführt.
2. Sie haben Ihre Data Box erhalten, und die Bestellung wird im Portal mit dem Status **Übermittelt** angezeigt.
3. Sie verfügen über eine Verbindung mit einem Hochgeschwindigkeitsnetzwerk. Mindestens eine 10-GbE-Verbindung wird dringend empfohlen. Sollte keine 10-GbE-Verbindung verfügbar sein, verwenden Sie eine 1-GbE-Datenverbindung. (Dadurch verlangsamen sich allerdings die Kopiervorgänge.)
4. Sie haben sich mit Folgendem vertraut gemacht:

    - Unterstützte Größen für verwaltete Datenträger und [Größenbeschränkungen für das Azure-Objekt](data-box-limits.md#azure-object-size-limits)
    - [Einführung in verwaltete Azure-Datenträger](../virtual-machines/managed-disks-overview.md) 

5. Sie haben eine Kopie der Quelldaten aufbewahrt, bis Sie bestätigt haben, dass Ihre Daten von Data Box in Azure Storage übertragen wurden.

## <a name="connect-to-data-box"></a>Herstellen einer Verbindung mit der Data Box

Die Data Box erstellt auf der Grundlage der angegebenen Ressourcengruppen jeweils eine Freigabe für die zugeordneten Ressourcengruppen. Wenn bei der Bestellung also beispielsweise `mydbmdrg1` und `mydbmdrg2` erstellt wurden, werden folgende Freigaben erstellt:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

Innerhalb der einzelnen Freigaben werden jeweils die folgenden drei Ordner erstellt, die Containern in Ihrem Speicherkonto entsprechen:

- SSD Premium
- HDD Standard
- SSD Standard

Die folgende Tabelle enthält die UNC-Pfade der Freigaben auf Ihrer Data Box:
 
|        Verbindungsprotokoll           |             UNC-Pfad der Freigabe                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<PremiumSSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<StandardHDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<StandardSSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<PremiumSSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<StandardHDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<StandardSSD>/file3.vhd` |

Die Schritte für die Verbindungsherstellung hängen davon ab, ob Sie SMB oder NFS verwenden, um die Verbindung mit den Data Box-Freigaben herzustellen.

> [!NOTE]
> Die Verbindungsherstellung über REST wird für dieses Feature nicht unterstützt.

### <a name="connect-to-data-box-via-smb"></a>Herstellen einer Verbindung mit der Data Box über SMB

Wenn Sie einen Windows Server-Hostcomputer verwenden, führen Sie die folgenden Schritte aus, um eine Verbindung mit der Data Box herzustellen.

1. Zunächst müssen Sie sich authentifizieren und eine Sitzung starten. Navigieren Sie zu **Verbindung herstellen und Daten kopieren**. Klicken Sie auf **Anmeldeinformationen abrufen**, um die Anmeldeinformationen für den Zugriff auf die mit Ihrer Ressourcengruppe verknüpften Freigaben abzurufen. Die Anmeldeinformationen für den Zugriff können auch über die **Gerätedetails** im Azure-Portal abgerufen werden.

    > [!NOTE]
    > Sie sind für alle Freigaben für verwaltete Datenträger identisch.

    ![Option „Verbindung herstellen und Daten kopieren“, Abrufen der Anmeldeinformationen für Freigaben](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. Kopieren Sie im Dialogfeld **Auf Freigabe zugreifen und Daten kopieren** die Werte für **Benutzername** und **Kennwort** für die Freigabe. Klicken Sie auf **OK**.
    
    ![Option „Verbindung herstellen und Daten kopieren“, Kopieren der Anmeldeinformationen für Freigaben](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Öffnen Sie ein Befehlsfenster, um über Ihren Hostcomputer auf die Freigaben zuzugreifen, die mit Ihrer Ressource (*mydbmdrg1* im folgenden Beispiel) verknüpft sind. Geben Sie an der Eingabeaufforderung Folgendes ein:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Die UNC-Freigabepfade lauten in diesem Beispiel wie folgt:

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. Geben Sie das Kennwort für die Freigabe ein, wenn Sie dazu aufgefordert werden. Das folgende Beispiel zeigt das Herstellen einer Verbindung mit einer Freigabe über den obigen Befehl.

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for 'mdisk' to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

5. Drücken Sie WINDOWS-TASTE+R. Geben Sie im Fenster **Ausführen** die `\\<device IP address>\<ShareName>` an. Klicken Sie auf **OK**, um den Datei-Explorer zu öffnen.
    
    ![Herstellen einer Verbindung mit der Freigabe über den Datei-Explorer](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    In jeder Freigabe sollten nun die folgenden vorab erstellten Ordner vorhanden sein.
    
    ![Herstellen einer Verbindung mit der Freigabe über den Datei-Explorer, Ordner für eine Freigabe](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-via-nfs"></a>Herstellen einer Verbindung mit der Data Box über NFS

Wenn Sie einen Linux-Hostcomputer verwenden, führen Sie die folgenden Schritte aus, um Data Box zum Zulassen des Zugriffs auf NFS-Clients zu konfigurieren.

1. Geben Sie die IP-Adressen der zulässigen Clients an, die auf die Freigabe zugreifen können. Wechseln Sie auf der lokalen Webbenutzeroberfläche zur Seite **Verbindung herstellen und Daten kopieren**. Klicken Sie unter **NFS-Einstellungen** auf **NFS-Clientzugriff**.

    ![Konfigurieren des NFS-Clientzugriffs](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Geben Sie die IP-Adresse des NFS-Clients an, und klicken Sie auf **Hinzufügen**. Sie können den Zugriff für mehrere NFS-Clients konfigurieren, indem Sie diesen Schritt wiederholen. Klicken Sie auf **OK**.

    ![Konfigurieren der IP-Adresse des NFS-Clients](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Stellen Sie sicher, dass auf dem Linux-Hostcomputer eine [unterstützte Version](data-box-system-requirements.md) des NFS-Clients installiert ist. Verwenden Sie die jeweilige Version für Ihre Linux-Distribution.

3. Führen Sie nach der Installation des NFS-Clients den folgenden Befehl aus, um die NFS-Freigabe auf Ihrem Data Box-Gerät einzubinden:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    Das folgende Beispiel zeigt, wie Sie über NFS eine Verbindung mit einer Data Box-Freigabe herstellen. Die IP-Adresse des Data Box-Geräts lautet `169.254.250.200`, die Freigabe `mydbmdrg1_MDisk` wird auf der Ubuntu-VM eingebunden, und der Bereitstellungspunkt ist `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box"></a>Kopieren von Daten auf die Data Box

Nach der Verbindungsherstellung mit dem Datenserver müssen im nächsten Schritt Ihre Daten kopiert werden. Die VHD-Datei wird als Seitenblob in das Stagingspeicherkonto kopiert. Das Seitenblob wird anschließend in einen verwalteten Datenträger konvertiert und in eine Ressourcengruppe verschoben.

Beachten Sie Folgendes, bevor Sie mit dem Kopieren der Daten beginnen:

- Kopieren Sie die VHDs immer in einen der vorab erstellten Ordner. Wenn Sie die VHDs an einen Ort außerhalb dieser Ordner oder in einen selbst erstellten Ordner kopieren, werden sie nicht als verwaltete Datenträger, sondern als Seitenblobs in das Azure Storage-Konto hochgeladen.
- Zur Erstellung von verwalteten Datenträgern können nur feste VHDs hochgeladen werden. VHDX-Dateien oder differenzierende VHDs werden nicht unterstützt.
- Der Name eines verwalteten Datenträgers muss in einer Ressourcengruppe über alle vorab erstellten Ordner hinweg eindeutig sein. Die VHDs, die in die vorab erstellten Ordner hochgeladen werden, müssen also jeweils einen eindeutigen Namen aufweisen. Achten Sie darauf, dass der Name nicht mit dem Namen eines bereits vorhandenen verwalteten Datenträgers in einer Ressourcengruppe identisch ist.
- Informationen zu Grenzwerten für verwaltete Datenträger finden Sie unter [Größenbeschränkungen für das Azure-Objekt](data-box-limits.md#azure-object-size-limits).

Sie können Folgendes verwenden (abhängig davon, ob Sie die Verbindung über SMB oder über NFS herstellen):

- [Kopieren von Daten über SMB](data-box-deploy-copy-data.md#copy-data-to-data-box)
- [Kopieren von Daten über NFS](data-box-deploy-copy-data-via-nfs.md#copy-data-to-data-box)

Warten Sie, bis die Kopieraufträge abgeschlossen sind. Vergewissern Sie sich, dass die Kopieraufträge ohne Fehler abgeschlossen wurden, bevor Sie mit dem nächsten Schritt fortfahren.

![Keine Fehler auf der Seite **Verbindung herstellen und Daten kopieren**](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

Sollte während des Kopiervorgangs ein Fehler aufgetreten sein, laden Sie die Protokolle von der Seite **Verbindung herstellen und Daten kopieren** herunter.

- Falls Sie eine Datei kopiert haben, die keinem ganzzahligen Vielfachen von 512 Bytes entspricht, wird die Datei nicht als Seitenblob in Ihr Stagingspeicherkonto hochgeladen. Die Protokolle enthalten dann einen Fehler. Entfernen Sie die Datei, und kopieren Sie eine Datei, die einem ganzzahligen Vielfachen von 512 Bytes entspricht.

- Wenn Sie eine VHDX-Datei (nicht unterstützt) mit einem langen Namen kopiert haben, wird ein Fehler protokolliert.

    ![Fehler in den Protokollen der Seite **Verbindung herstellen und Daten kopieren**](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    Beheben Sie die Fehler, bevor Sie mit dem nächsten Schritt fortfahren.

Um die Datenintegrität zu gewährleisten, wird inline eine Prüfsumme berechnet, während die Daten kopiert werden. Überprüfen Sie nach Abschluss des Kopiervorgangs den belegten Speicherplatz und den freien Speicherplatz auf Ihrem Gerät.
    
![Überprüfen des freien und belegten Speicherplatzes im Dashboard](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

Nach Abschluss des Kopierauftrags können Sie zu **Für Versand vorbereiten** wechseln.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Informationen zu Azure Data Box-Themen erhalten, darunter die folgenden:

> [!div class="checklist"]
> * Überprüfen der Voraussetzungen
> * Herstellen einer Verbindung mit der Data Box
> * Kopieren von Daten auf die Data Box


Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Ihre Data Box zurück an Microsoft senden.

> [!div class="nextstepaction"]
> [Zurücksenden der Azure Data Box an Microsoft](./data-box-deploy-picked-up.md)