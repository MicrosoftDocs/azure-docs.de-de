---
title: Verwenden von Azure Files mit Linux | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie unter Linux eine Azure-Dateifreigabe über SMB einbinden. Lesen Sie die Liste der Voraussetzungen. Überprüfen Sie die Sicherheitsüberlegungen zu SMB auf Linux-Clients.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c271107b85e4903153c29b58aadadd37fb051b76
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022563"
---
# <a name="use-azure-files-with-linux"></a>Verwenden von Azure Files mit Linux
[Azure Files](storage-files-introduction.md) ist das benutzerfreundliche Clouddateisystem von Microsoft. Azure-Dateifreigaben können mithilfe des [SMB-Kernelclients](https://wiki.samba.org/index.php/LinuxCIFS) in Linux-Distributionen eingebunden werden. Dieser Artikel veranschaulicht zwei Möglichkeiten zum Einbinden einer Azure-Dateifreigabe: bedarfsgesteuert mit dem Befehl `mount` oder beim Start durch Erstellen eines Eintrags in `/etc/fstab`.

Es wird empfohlen, SMB 3.0 zu verwenden, um eine Azure-Dateifreigabe in Linux einzubinden. Standardmäßig müssen für Azure Files Daten während der Übertragung verschlüsselt werden. Dies wird nur von SMB 3.0 unterstützt. Azure Files unterstützt auch die Version 2.1 von SMB, mit der allerdings keine Verschlüsselung während der Übertragung möglich ist. Deshalb können Sie aus Sicherheitsgründen mit SMB 2.1 keine Azure-Dateifreigaben aus anderen Regionen oder lokal einbinden. Sofern für Ihre Anwendung nicht unbedingt SMB 2.1 erforderlich ist, gibt es wenige Gründe, die für die Verwendung dieser Version sprechen, da die beliebtesten und neusten Linux-Distributionen SMB 3.0 unterstützen:  

| Linux-Verteilung | SMB 2.1 <br>(Einbindungen auf VMs innerhalb derselben Azure-Region) | SMB 3.0 <br>(Einbindungen aus einer lokalen Region und regionsübergreifend) |
| --- | :---: | :---: |
| Ubuntu | 14.04+ | 16.04 und höher |
| Red Hat Enterprise Linux (RHEL) | 7 und höher | 7.5 und höher |
| CentOS | 7 und höher |  7.5 und höher |
| Debian | 8 und höher | 10+ |
| openSUSE | 13.2 und höher | 42.3+ |
| SUSE Linux Enterprise Server | 12+ | 12 SP2+ |

Wenn Sie eine Linux-Distribution verwenden, die nicht in der obigen Tabelle aufgeführt ist, können Sie anhand Ihrer Linux-Kernelversion überprüfen, ob Ihre Linux-Distribution SMB 3.0 mit Verschlüsselung unterstützt. SMB 3.0 mit Verschlüsselung wurde der Linux-Kernelversion 4.11 hinzugefügt. Über den Befehl `uname` wird die Version des verwendeten Linux-Kernels zurückgegeben:

```bash
uname -r
```

## <a name="prerequisites"></a>Voraussetzungen
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**Vergewissern Sie sich, dass das cifs-utils-Paket installiert ist.**  
    Das Paket „cifs-utils“ kann mithilfe des Paket-Managers für die Linux-Distribution Ihrer Wahl installiert werden. 

    Verwenden Sie bei auf **Ubuntu** und **Debian** basierenden Distributionen den Paket-Manager `apt`:

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    Verwenden Sie unter **Fedora**, **Red Hat Enterprise Linux 8 und höher** sowie **CentOS 8 und höher** den Paket-Manager `dnf`:

    ```bash
    sudo dnf install cifs-utils
    ```

    Verwenden Sie unter älteren Versionen von **Red Hat Enterprise Linux** und **CentOS** den Paket-Manager `yum`:

    ```bash
    sudo yum install cifs-utils 
    ```

    Verwenden Sie unter **OpenSUSE** den Paket-Manager `zypper`:

    ```bash
    sudo zypper install cifs-utils
    ```

    Verwenden Sie bei anderen Distributionen den entsprechenden Paket-Manager, oder [kompilieren Sie den Quellcode](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).

* **Die neueste Version der Azure-Befehlszeilenschnittstelle (CLI).** Informationen zum Installieren der Azure CLI finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) im Abschnitt zu Ihrem Betriebssystem. Wenn Sie lieber mit dem Azure PowerShell-Modul in PowerShell 6 und höher arbeiten möchten, können Sie dies tun, die folgenden Anweisungen beziehen sich jedoch auf die Azure CLI.

* **Prüfen Sie, ob Port 445 geöffnet ist**: SMB kommuniziert über den TCP-Port 445. Vergewissern Sie sich, dass der TCP-Port 445 des Clientcomputers nicht durch die Firewall blockiert wird.  Ersetzen Sie `<your-resource-group>` und `<your-storage-account>`, und führen Sie dann das folgende Skript aus:
    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $smbPath | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    Wenn erfolgreich eine Verbindung hergestellt werden konnte, wird in etwa die folgende Ausgabe angezeigt:

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    Wenn Sie Port 445 nicht in Ihrem Unternehmensnetzwerk öffnen können oder der Internetdienstanbieter dies nicht zulässt, können Sie eine VPN-Verbindung oder ExpressRoute verwenden, um Port 445 zu umgehen. Weitere Informationen finden Sie unter [Überlegungen zum Netzwerk für den direkten Zugriff auf Azure-Dateifreigaben](storage-files-networking-overview.md).

## <a name="mounting-azure-file-share"></a>Einbinden von Azure-Dateifreigaben
Wenn Sie eine Azure-Dateifreigabe mit Ihrer Linux-Distribution verwenden möchten, müssen Sie ein Verzeichnis erstellen, das als Bereitstellungspunkt für die Azure-Dateifreigabe fungiert. Der Bereitstellungspunkt kann zwar an einem beliebigen Ort auf Ihrem Linux-System erstellt werden, üblicherweise erfolgt die Erstellung jedoch unter „/mnt“. Nach dem Bereitstellungspunkt können Sie den Befehl `mount` verwenden, um auf die Azure-Dateifreigabe zuzugreifen.

Falls gewünscht, können Sie dieselbe Azure-Dateifreigabe in mehrere Bereitstellungspunkte einbinden.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Bedarfsgesteuertes Einbinden der Azure-Dateifreigabe mit `mount`
1. **Erstellen Sie einen Ordner für den Bereitstellungspunkt**: Ersetzen Sie `<your-resource-group>`, `<your-storage-account>` und `<your-file-share>` durch die entsprechenden Informationen für Ihre Umgebung:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Verwenden Sie den Einbindungsbefehl, um die Azure-Dateifreigabe einzubinden.** Im folgenden Beispiel gilt für die lokalen Berechtigungen für Linux-Dateien und -Ordner der Standardwert 0755. Dadurch erhalten Besitzer Lese-, Schreib- und Ausführungsberechtigungen (basierend auf dem Linux-Besitzer der Datei bzw. des Verzeichnisses), Benutzer in Besitzergruppen erhalten Lese- und Ausführungsberechtigungen und alle anderen Benutzer des Systems erhalten Lese- und Ausführungsberechtigungen. Sie können die Einbindungsoptionen `uid` und `gid` verwenden, um die Benutzer-ID und die Gruppen-ID für die Einbindung festzulegen. Sie können auch `dir_mode` und `file_mode` verwenden, um nach Belieben benutzerdefinierte Berechtigungen festzulegen. Weitere Informationen zum Festlegen von Berechtigungen finden Sie unter [UNIX numeric notation](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) (Numerische UNIX-Notation) auf Wikipedia. 

    ```bash
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')

    sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
    ```

    > [!Note]  
    > Über die obigen Einbindungsbefehle erfolgt die Einbindung in SMB 3.0. Wenn Ihre Linux-Distribution SMB 3.0 mit Verschlüsselung nicht unterstützt oder wenn sie nur SMB 2.1 unterstützt, können Sie nur über eine Azure-VM, die sich in derselben Region wie das Speicherkonto befindet, eine Einbindung vornehmen. Wenn Sie Ihre Azure-Dateifreigabe in eine Linux-Distribution einbinden möchten, die SMB 3.0 mit Verschlüsselung nicht unterstützt, müssen Sie die [Verschlüsselung während der Übertragung für das Speicherkonto deaktivieren](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Wenn Sie die Azure-Dateifreigabe nicht mehr benötigen, können Sie `sudo umount $mntPath` verwenden, um die Einbindung der Freigabe aufzuheben.

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>Erstellen eines permanenten Bereitstellungspunkts für die Azure-Dateifreigabe mit `/etc/fstab`
1. **Erstellen Sie einen Ordner für den Bereitstellungspunkt**: Ein Ordner für einen Bereitstellungspunkt kann zwar an einem beliebigen Ort im Dateisystem erstellt werden, üblicherweise erfolgt die Erstellung jedoch unter „/mnt“. Mit dem folgenden Befehl wird beispielsweise ein neues Verzeichnis erstellt. Ersetzen Sie `<your-resource-group>`,`<your-storage-account>` und `<your-file-share>` durch die entsprechenden Informationen für Ihre Umgebung:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Erstellen Sie eine Datei mit Anmeldeinformationen, um den Benutzernamen (den Namen des Speicherkontos) und das Kennwort (den Schlüssel des Speicherkontos) für die Dateifreigabe zu speichern.** 

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
        sudo mkdir "/etc/smbcredentials"
    fi

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')
    
    smbCredentialFile="/etc/smbcredentials/$storageAccountName.cred"
    if [ ! -f $smbCredentialFile ]; then
        echo "username=$storageAccountName" | sudo tee $smbCredentialFile > /dev/null
        echo "password=$storageAccountKey" | sudo tee -a $smbCredentialFile > /dev/null
    else 
        echo "The credential file $smbCredentialFile already exists, and was not modified."
    fi
    ```

1. **Ändern Sie die Berechtigungen in der Datei mit den Anmeldeinformationen so, dass nur „root“ die Kennwortdatei lesen oder ändern kann.** Da der Speicherkontoschlüssel eigentlich ein Superadministratorkennwort für das Speicherkonto ist, ist es wichtig, die Berechtigungen für die Datei so festzulegen, dass nur „root“ darauf zugreifen kann, damit Benutzer mit geringeren Rechten den Speicherkontoschlüssel nicht abrufen können.   

    ```bash
    sudo chmod 600 $smbCredentialFile
    ```

1. **Fügen Sie mit dem folgenden Befehl die folgende Zeile an `/etc/fstab` an**: Im folgenden Beispiel gilt für die lokalen Berechtigungen für Linux-Dateien und -Ordner der Standardwert 0755. Dadurch erhalten Besitzer Lese-, Schreib- und Ausführungsberechtigungen (basierend auf dem Linux-Besitzer der Datei bzw. des Verzeichnisses), Benutzer in Besitzergruppen erhalten Lese- und Ausführungsberechtigungen und alle anderen Benutzer des Systems erhalten Lese- und Ausführungsberechtigungen. Sie können die Einbindungsoptionen `uid` und `gid` verwenden, um die Benutzer-ID und die Gruppen-ID für die Einbindung festzulegen. Sie können auch `dir_mode` und `file_mode` verwenden, um nach Belieben benutzerdefinierte Berechtigungen festzulegen. Weitere Informationen zum Festlegen von Berechtigungen finden Sie unter [UNIX numeric notation](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) (Numerische UNIX-Notation) auf Wikipedia.

    ```bash
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    if [ -z "$(grep $smbPath\ $mntPath /etc/fstab)" ]; then
        echo "$smbPath $mntPath cifs nofail,vers=3.0,credentials=$smbCredentialFile,serverino" | sudo tee -a /etc/fstab > /dev/null
    else
        echo "/etc/fstab was not modified to avoid conflicting entries as this Azure file share was already present. You may want to double check /etc/fstab to ensure the configuration is as desired."
    fi

    sudo mount -a
    ```
    
    > [!Note]  
    > Über die obigen Einbindungsbefehle erfolgt die Einbindung in SMB 3.0. Wenn Ihre Linux-Distribution SMB 3.0 mit Verschlüsselung nicht unterstützt oder wenn sie nur SMB 2.1 unterstützt, können Sie nur über eine Azure-VM, die sich in derselben Region wie das Speicherkonto befindet, eine Einbindung vornehmen. Wenn Sie Ihre Azure-Dateifreigabe in eine Linux-Distribution einbinden möchten, die SMB 3.0 mit Verschlüsselung nicht unterstützt, müssen Sie die [Verschlüsselung während der Übertragung für das Speicherkonto deaktivieren](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="using-autofs-to-automatically-mount-the-azure-file-shares"></a>Verwenden von autofs zum automatischen Einbinden der Azure-Dateifreigaben

1. **Stellen Sie sicher, dass das autofs-Paket installiert ist.**  

    Das Paket „autofs“ kann mithilfe des Paket-Managers für die Linux-Distribution Ihrer Wahl installiert werden. 

    Verwenden Sie bei auf **Ubuntu** und **Debian** basierenden Distributionen den Paket-Manager `apt`:
    ```bash
    sudo apt update
    sudo apt install autofs
    ```
    Verwenden Sie unter **Fedora**, **Red Hat Enterprise Linux 8 und höher** sowie **CentOS 8 und höher** den Paket-Manager `dnf`:
    ```bash
    sudo dnf install autofs
    ```
    Verwenden Sie unter älteren Versionen von **Red Hat Enterprise Linux** und **CentOS** den Paket-Manager `yum`:
    ```bash
    sudo yum install autofs 
    ```
    Verwenden Sie unter **OpenSUSE** den Paket-Manager `zypper`:
    ```bash
    sudo zypper install autofs
    ```
2. **Erstellen Sie einen Bereitstellungspunkt für die Freigabe:**
   ```bash
    sudo mkdir /fileshares
    ```
3. **Erstellen Sie eine benutzerdefinierte autofs-Konfigurationsdatei:**
    ```bash
    sudo vi /etc/auto.fileshares
    ```
4. **Fügen Sie die folgenden Einträge zu /etc/auto.fileshares hinzu:**
   ```bash
   echo "$fileShareName -fstype=cifs,credentials=$smbCredentialFile :$smbPath"" > /etc/auto.fileshares
   ```
5. **Fügen Sie den folgenden Eintrag zu /etc/auto.master hinzu:**
   ```bash
   /fileshares /etc/auto.fileshares --timeout=60
   ```
6. **Starten Sie autofs neu:**
    ```bash
    sudo systemctl restart autofs
    ```
7.  **Greifen Sie auf den für die Freigabe vorgesehenen Ordner zu:**
    ```bash
    cd /fileshares/$filesharename
    ```
## <a name="securing-linux"></a>Sichern von Linux
Zum Einbinden einer Azure-Dateifreigabe unter Linux muss Port 445 zugänglich sein. Viele Organisationen blockieren Port 445 aufgrund von mit SMB 1 verbundenen Sicherheitsrisiken. SMB 1, auch bekannt als CIFS (Common Internet File System) ist ein Legacydateisystemprotokoll, das in vielen Linux-Distributionen enthalten ist. SMB 1 ist ein veraltetes, ineffizientes und vor allem unsicheres Protokoll. Azure Files unterstützt SMB 1 nicht und ab der Linux-Kernelversion 4.18 ist es möglich, SMB 1 unter Linux zu deaktivieren. Es wird [dringend empfohlen](https://aka.ms/stopusingsmb1), SMB 1 auf Ihren Linux-Clients zu deaktivieren, bevor Sie SMB-Dateifreigaben in der Produktion verwenden.

Ab der Linux-Kernelversion 4.18 stellt das SMB-Kernelmodul (`cifs` genannt, weil es sich um eine Legacyversion handelt) einen neuen Modulparameter (häufig in verschiedenen Dokumentationen als *parm*, hier aber als `disable_legacy_dialects` bezeichnet) zur Verfügung. Obwohl dies mit der Linux-Kernelversion 4.18 eingeführt wurde, haben einige Anbieter diese Änderung auf ältere von ihnen unterstützte Kernelversionen zurückportiert. In der folgenden Tabelle wird die Verfügbarkeit dieses Modulparameters für allgemeine Linux-Distributionen ausführlich erläutert.

| Distribution | SMB 1 deaktivierbar |
|--------------|-------------------|
| Ubuntu 14.04–16.04 | Nein |
| Ubuntu 18.04 | Ja |
| Ubuntu 19.04 und höher | Ja |
| Debian 8–9 | Nein |
| Debian 10 und höher | Ja |
| Fedora 29 und höher | Ja |
| CentOS 7 | Nein | 
| CentOS 8 und höher | Ja |
| Red Hat Enterprise Linux 6.x–7.x | Nein |
| Red Hat Enterprise Linux 8 und höher | Ja |
| openSUSE Leap 15.0 | Nein |
| openSUSE Leap 15.1 und höher | Ja |
| openSUSE Tumbleweed | Ja |
| SUSE Linux Enterprise 11.x–12.x | Nein |
| SUSE Linux Enterprise 15 | Nein |
| SUSE Linux Enterprise 15.1 | Nein |

Mithilfe des folgenden Befehls können Sie überprüfen, ob Ihre Linux-Distribution den Modulparameter `disable_legacy_dialects` unterstützt.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

Es sollte die folgende Meldung ausgegeben werden:

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

Bevor Sie SMB 1 deaktivieren, müssen Sie sich vergewissern, dass das SMB-Modul zum aktuellen Zeitpunkt nicht auf Ihrem System geladen ist. Wenn Sie eine SMB-Freigabe eingebunden haben, wird dies automatisch überprüft. Ansonsten können Sie den folgenden Befehl verwenden, der keine Ausgabe zurückgeben sollte, wenn SMB nicht geladen ist:

```bash
lsmod | grep cifs
```

Wenn Sie das Modul entladen möchten, entfernen Sie zuerst alle SMB-Freigaben (wie oben beschrieben über den Befehl `umount`). Mithilfe des folgenden Befehls können Sie alle eingebunden SMB-Freigaben auf Ihrem System identifizieren:

```bash
mount | grep cifs
```

Nachdem Sie alle SMB-Dateifreigaben entfernt haben, ist es sicher, das Modul zu entladen. Hierfür können Sie den Befehl `modprobe` verwenden:

```bash
sudo modprobe -r cifs
```

Sie können das Modul mithilfe des Befehls `modprobe` manuell laden, wenn SMB 1 entladen ist:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Zum Schluss können Sie überprüfen, ob das SMB-Modul mit dem Parameter geladen wurde, indem Sie sich die geladenen Parameter in `/sys/module/cifs/parameters` ansehen:

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Wenn Sie SMB 1 für Ubuntu- und Debian-basierte Distributionen dauerhaft aktivieren möchten, müssen Sie eine neue Datei mit dem Namen `/etc/modprobe.d/local.conf` und der entsprechenden Einstellung erstellen (wenn Sie noch nicht über benutzerdefinierte Optionen für andere Module verfügen). Dafür können Sie den folgenden Befehl verwenden:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

Wenn Sie das SMB-Modul laden, können Sie überprüfen, ob der Vorgang erfolgreich war:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Azure Files finden Sie unter diesen Links:

* [Planung für eine Azure Files-Bereitstellung](storage-files-planning.md)
* [Häufig gestellte Fragen](./storage-files-faq.md)
* [Problembehandlung](storage-troubleshoot-linux-file-connection-problems.md)