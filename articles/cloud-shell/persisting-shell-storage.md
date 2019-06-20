---
title: Beibehalten von Dateien für Bash in Azure Cloud Shell | Microsoft-Dokumentation
description: Exemplarische Vorgehensweise für Bash zum Beibehalten von Dateien in Azure Cloud Shell.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: damaerte
ms.openlocfilehash: 0aa00af543a3d21db9b8ad0ed808a8bff0b534e1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60200182"
---
[!INCLUDE [PersistingStorage-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-cloud-shell-storage-works"></a>Funktionsweise von Cloud Shell-Speicher 
Cloud Shell nutzt die beiden folgenden Methoden zum Beibehalten von Dateien: 
* Es wird ein Datenträgerimage Ihres Verzeichnisses `$Home` erstellt, um alle Inhalte im Verzeichnis beizubehalten. Das Datenträgerimage wird in der von Ihnen angegebenen Dateifreigabe als `acc_<User>.img` unter `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img` gespeichert, und die Änderungen werden automatisch synchronisiert. 
* Ihre angegebene Dateifreigabe wird zur direkten Interaktion mit der Freigabe als `clouddrive` in Ihrem Verzeichnis `$Home` eingebunden. `/Home/<User>/clouddrive` wird `fileshare.storage.windows.net/fileshare` zugeordnet.
 
> [!NOTE]
> Alle Dateien in Ihrem Verzeichnis `$Home`, z.B. SSH-Schlüssel, werden dauerhaft in Ihrem Benutzerdatenträgerimage beibehalten, das in der eingebundenen Dateifreigabe gespeichert ist. Nutzen Sie beim dauerhaften Speichern von Informationen in Ihrem Verzeichnis `$Home` und auf der eingebundenen Dateifreigabe die bewährten Methoden.

## <a name="bash-specific-commands"></a>Bash-spezifische Befehle

### <a name="use-the-clouddrive-command"></a>Verwenden des Befehls `clouddrive`
Mit Bash in Cloud Shell können Sie den Befehl `clouddrive` ausführen, mit dem Sie die in Cloud Shell bereitgestellte Dateifreigabe manuell aktivieren können.
![Ausführen des Befehls „clouddrive“](media/persisting-shell-storage/clouddrive-h.png)

### <a name="mount-a-new-clouddrive"></a>Einbinden eines neuen clouddrive-Verzeichnisses

#### <a name="prerequisites-for-manual-mounting"></a>Voraussetzungen für die manuelle Bereitstellung
Sie können die Dateifreigabe aktualisieren, die Cloud Shell zugeordnet ist, indem Sie den Befehl `clouddrive mount` verwenden.

Wenn Sie eine vorhandene Dateifreigabe einbinden, müssen sich die Speicherkonten in der ausgewählten Cloud Shell-Region befinden. Sie rufen den Standort durch Ausführen von `env` in Bash und Überprüfen der `ACC_LOCATION` ab.

#### <a name="the-clouddrive-mount-command"></a>Befehl `clouddrive mount`

> [!NOTE]
> Wenn Sie eine neue Dateifreigabe einbinden, wird für Ihr `$Home`-Verzeichnis ein neues Benutzerimage erstellt. Ihre vorheriges `$Home`-Image wird in Ihrer vorherigen Dateifreigabe gespeichert.

Führen Sie den Befehl `clouddrive mount` mit den folgenden Parametern aus:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Führen Sie `clouddrive mount -h` wie hier gezeigt aus, um weitere Details anzuzeigen:

![Ausführen des Befehls „clouddrive mount“](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Aufheben der Einbindung von clouddrive
Sie können die Bereitstellung einer Dateifreigabe in Cloud Shell jederzeit aufheben. Cloud Shell erfordert jedoch eine eingebundene Dateifreigabe, daher werden Sie aufgefordert, in der nächsten Sitzung eine andere Dateifreigabe zu erstellen und einzubinden.

1. Führen Sie `clouddrive unmount`aus.
2. Lesen und bestätigen Sie die Eingabeaufforderungen.

Ihre Dateifreigabe ist weiterhin vorhanden, sofern Sie sie nicht manuell löschen. Cloud Shell sucht in nachfolgenden Sitzungen nicht mehr nach dieser Dateifreigabe. Führen Sie `clouddrive unmount -h` wie hier gezeigt aus, um weitere Details anzuzeigen:

![Ausführen des Befehls „clouddrive unmount“](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Durch Ausführung dieses Befehls werden keine Ressourcen gelöscht. Wenn Sie aber eine Ressourcengruppe, ein Speicherkonto oder eine Dateifreigabe mit Zuordnung zu Cloud Shell manuell löschen, werden dadurch sowohl Ihr Datenträgerimage im Verzeichnis `$Home` als auch alle Dateien in Ihrer Dateifreigabe gelöscht. Diese Aktion kann nicht rückgängig gemacht werden.

### <a name="list-clouddrive"></a>Auflisten von `clouddrive`
Führen Sie den `df`-Befehl aus, um zu ermitteln, welche Dateifreigabe als `clouddrive` bereitgestellt wird. 

Der Dateipfad zu „clouddrive“ zeigt den Namen Ihres Speicherkontos und die Dateifreigabe in der URL. Zum Beispiel, `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```
## <a name="powershell-specific-commands"></a>PowerShell-spezifische Befehle

### <a name="list-clouddrive-azure-file-shares"></a>Auflisten von `clouddrive`-Azure-Dateifreigaben
Das Cmdlet `Get-CloudDrive` ruft Informationen zu den derzeit von `clouddrive` in Cloud Shell eingebundenen Azure-Dateifreigaben ab. <br>
![Get-CloudDrive ausführen](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Aufheben der Bereitstellung von `clouddrive`
Sie können die Einbindung einer Azure-Dateifreigabe in Cloud Shell jederzeit aufheben. Wenn die Azure-Dateifreigabe entfernt wurde, werden Sie in der nächsten Sitzung aufgefordert, eine neue Azure-Dateifreigabe zu erstellen und einzubinden.

Das Cmdlet `Dismount-CloudDrive` hebt die Einbindung einer Azure-Dateifreigabe für das aktuelle Speicherkonto auf. Durch das Aufheben der Einbindung von `clouddrive` wird die aktuelle Sitzung beendet. Der Benutzer wird in der nächsten Sitzung aufgefordert, eine neue Azure-Dateifreigabe zu erstellen und einzubinden.
![Dismount-CloudDrive ausführen](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Hinweis: Wenn Sie eine Funktion in einer Datei definieren und diese über die PowerShell-Cmdlets aufrufen müssen, muss der Punktoperator enthalten sein. Beispiel: . .\MeineFunktionen.ps1

## <a name="next-steps"></a>Nächste Schritte
[Bash in Cloud Shell – Schnellstart](quickstart.md) <br>
[Schnellstart für PowerShell in Cloud Shell](quickstart-powershell.md) <br>
[Einführung in Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-introduction) <br>
[Weitere Informationen zu Speichertags](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
