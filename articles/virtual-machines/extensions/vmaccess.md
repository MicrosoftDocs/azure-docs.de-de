---
title: Zurücksetzen des Zugriffs auf einen virtuellen Azure-Linux-Computer
description: Informationen zum Verwalten von Benutzern und Zurücksetzen des Zugriffs auf Linux-VMs mithilfe der VMAccess-Erweiterung und der Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2018
ms.author: akjosh
ms.openlocfilehash: e46f7f3d51d2841a2287c86f9e8dddd7460b1a5d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016436"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli"></a>Verwalten von Administratoren, SSH und Überprüfen oder Reparieren von Datenträgern auf Linux-VMs mit der VMAccess-Erweiterung und der Azure CLI
## <a name="overview"></a>Übersicht
Der Datenträger auf Ihrer Linux-VM zeigt Fehler an. Aus irgendeinem Grund haben Sie das Stammkennwort für Ihre Linux-VM zurückgesetzt oder Ihren privaten SSH-Schlüssel versehentlich gelöscht. Wenn dies früher zu Rechenzentrumszeiten geschah, mussten Sie dorthin fahren und den KVM öffnen, um an die Serverkonsole zu gelangen. Stellen Sie sich die Azure-VMAccess-Erweiterung als diesen KVM-Switch vor, mit dem Sie Zugriff auf die Konsole haben, um den Zugriff auf Linux zurückzusetzen oder Wartung auf Datenträgerebene durchzuführen.

In diesem Artikel erfahren Sie, wie Sie mithilfe der Azure VMAccess-Erweiterung einen Datenträger überprüfen oder reparieren, den Benutzerzugriff zurücksetzen, Administratorkonten verwalten oder die SSH-Konfiguration unter Linux aktualisieren, wenn die virtuellen Computer als virtuelle Azure Resource Manager ausgeführt werden. Wenn Sie klassisch bereitgestellte virtuelle Computer verwalten möchten, können Sie die Anweisungen in der [Dokumentation zu klassisch bereitgestellten virtuellen Computern](/previous-versions/azure/virtual-machines/linux/classic/reset-access-classic) befolgen. 
 
> [!NOTE]
> Wenn Sie die VMAccess-Erweiterung verwenden, um das Kennwort Ihres virtuellen Computers nach der Installation der AAD-Anmeldeerweiterung zurückzusetzen, müssen Sie die AAD-Anmeldeerweiterung erneut ausführen, um die AAD-Anmeldung für Ihren Computer erneut zu aktivieren.

## <a name="prerequisites"></a>Voraussetzungen
### <a name="operating-system"></a>Betriebssystem

Die VMAccess-Erweiterung kann für folgende Linux-Distributionen ausgeführt werden:

| Distribution | Version |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS und 12.04 LTS |
| Debian | Debian 7.9+, 8.2+ |
| Red Hat | RHEL 6.7+, 7.1+ |
| Oracle Linux | 6.4+, 7.0+ |
| Suse | 11 und 12 |
| openSUSE | openSUSE Leap 42.2+ |
| CentOS | CentOS 6.3+, 7.0+ |
| CoreOS | 494.4.0+ |

## <a name="ways-to-use-the-vmaccess-extension"></a>Verschiedene Verwendungsmöglichkeiten für die VMAccess-Erweiterung
Es gibt zwei Möglichkeiten, die VMAccess-Erweiterung auf Ihren virtuellen Linux-Computern zu verwenden:

* Verwenden Sie die Azure-Befehlszeilenschnittstelle und die erforderlichen Parameter.
* [Verwenden Sie JSON-Rohdatendateien, die von der VMAccess-Erweiterung verarbeitet](#use-json-files-and-the-vmaccess-extension) und dann verwendet werden können.

In den folgenden Beispielen werden Befehle vom Typ [az vm user](/cli/azure/vm/user) verwendet. Zum Ausführen dieser Schritte muss die neueste Version der [Azure CLI](/cli/azure/install-az-cli2) installiert sein, und Sie müssen mithilfe von [az login](/cli/azure/reference-index) bei einem Azure-Konto angemeldet sein.

## <a name="update-ssh-key"></a>Aktualisieren eines SSH-Schlüssels
Das folgende Beispiel aktualisiert den SSH-Schlüssel für den Benutzer `azureuser` auf dem virtuellen Computer `myVM`:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **HINWEIS:** Der `az vm user update`-Befehl fügt den Text des neuen öffentlichen Schlüssels der `~/.ssh/authorized_keys`-Datei für den Administratorbenutzer auf dem virtuellen Computer an. Vorhandene SSH-Schlüssel werden dadurch weder ersetzt noch entfernt. Vorherige, zur Bereitstellungszeit festgelegte Schlüssel oder nachfolgende Updates über die VMAccess-Erweiterung werden dadurch nicht entfernt.

## <a name="reset-password"></a>Kennwort zurücksetzen
Das folgende Beispiel setzt das Kennwort für den Benutzer `azureuser` auf dem virtuellen Computer `myVM` zurück:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>Neustarten von SSH
Im folgenden Beispiel wird auf einem virtuellen Computer namens `myVM` der SSH-Daemon neu gestartet und die SSH-Konfiguration auf Standardwerte zurückgesetzt:

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Erstellen eines Administrators/sudo-Benutzers
Im folgenden Beispiel wird ein Benutzer mit dem Namen `myNewUser` mit **sudo**-Berechtigungen erstellt. Das Konto verwendet einen SSH-Schlüssel für die Authentifizierung auf der VM mit dem Namen `myVM`. Diese Methode wurde dafür konzipiert, Ihnen Zugriff auf eine VM zu verschaffen für den Fall, dass die aktuellen Anmeldeinformationen verloren gehen oder Sie diese vergessen haben. Als bewährte Methode sollten Konten mit **sudo**-Berechtigungen beschränkt werden.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>Löschen eines Benutzers
Das folgende Beispiel löscht einen Benutzer namens `myNewUser` vom virtuellen Computer `myVM`:

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>Verwenden von JSON-Dateien und der VMAccess-Erweiterung
Die folgenden Beispiele verwenden JSON-Rohdatendateien. Verwenden Sie [az vm extension set](/cli/azure/vm/extension), um Ihre JSON-Dateien aufzurufen. Diese JSON-Dateien können auch aus Azure-Vorlagen heraus aufgerufen werden. 

### <a name="reset-user-access"></a>Zurücksetzen des Benutzerzugriffs
Wenn Sie nicht mehr auf das Stammverzeichnis Ihres virtuellen Linux-Computers zugreifen können, können Sie ein VMAccess-Skript starten, um den SSH-Schlüssel oder das Kennwort eines Benutzers zu aktualisieren.

Um den öffentlichen SSH-Schlüssel eines Benutzers zu aktualisieren, erstellen Sie eine Datei namens `update_ssh_key.json` und fügen ihr Einstellungen im folgenden Format hinzu. Geben Sie für die Parameter `username` und `ssh_key` Ihre eigenen Werte an:

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Führen Sie das VMAccess-Skript aus mit:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings update_ssh_key.json
```

Um ein Benutzerkennwort zurückzusetzen, erstellen Sie eine Datei namens `reset_user_password.json` und fügen ihr Einstellungen im folgenden Format hinzu. Geben Sie für die Parameter `username` und `password` Ihre eigenen Werte an:

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Führen Sie das VMAccess-Skript aus mit:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="restart-ssh"></a>Neustarten von SSH
Erstellen Sie zum Neustarten des SSH-Daemons und zum Wiederherstellen der Standardwerte der SSH-Konfiguration eine Datei namens `reset_sshd.json`. Fügen Sie folgenden Inhalt hinzu:

```json
{
  "reset_ssh": true
}
```

Führen Sie das VMAccess-Skript aus mit:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-administrative-users"></a>Verwalten von Administratoren

Erstellen Sie zur Erstellung eines Benutzers mit **sudo**-Berechtigungen, der einen SSH-Schlüssel für die Authentifizierung verwendet, eine Datei namens `create_new_user.json`, und fügen Sie Einstellungen im folgenden Format hinzu. Geben Sie für die Parameter `username` und `ssh_key` Ihre eigenen Werte an. Diese Methode wurde dafür konzipiert, Ihnen Zugriff auf eine VM zu verschaffen für den Fall, dass die aktuellen Anmeldeinformationen verloren gehen oder Sie diese vergessen haben. Als bewährte Methode sollten Konten mit **sudo**-Berechtigungen beschränkt werden.

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Führen Sie das VMAccess-Skript aus mit:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

Erstellen Sie zum Löschen eines Benutzers eine Datei namens `delete_user.json`, und fügen Sie folgenden Inhalt hinzu. Geben Sie für den Parameter `remove_user` Ihren eigenen Wert an:

```json
{
  "remove_user":"myNewUser"
}
```

Führen Sie das VMAccess-Skript aus mit:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>Überprüfen oder Reparieren des Datenträgers
Mithilfe von „VMAccess“ können Sie auch einen Datenträger überprüfen und reparieren, den Sie dem virtuellen Linux-Computer hinzugefügt haben.

Erstellen Sie zum Überprüfen und anschließenden Reparieren des Datenträgers eine Datei namens `disk_check_repair.json`, und fügen Sie ihr Einstellungen im folgenden Format hinzu. Geben Sie für den Namen von `repair_disk` Ihren eigenen Wert an:

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

Führen Sie das VMAccess-Skript aus mit:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```
## <a name="troubleshoot-and-support"></a>Problembehandlung und Support

### <a name="troubleshoot"></a>Problembehandlung

Daten zum Status von Erweiterungsbereitstellungen können über das Azure-Portal und mithilfe der Azure-Befehlszeilenschnittstelle abgerufen werden. Führen Sie über die Azure-Befehlszeilenschnittstelle den folgenden Befehl aus, um den Bereitstellungsstatus von Erweiterungen für einen bestimmten virtuellen Computer anzuzeigen.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

### <a name="support"></a>Support

Sollten Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie sich über das [MSDN Azure-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/forums/) mit Azure-Experten in Verbindung setzen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie „Support erhalten“ aus. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/support/faq/).
