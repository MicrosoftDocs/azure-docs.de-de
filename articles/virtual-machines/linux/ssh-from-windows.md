---
title: Verwenden von SSH-Schlüsseln zum Herstellen einer Verbindung mit Linux-VMs
description: Erfahren Sie, wie Sie SSH-Schlüssel auf einem Windows-Computer generieren und verwenden, um eine Verbindung mit einem virtuellen Linux-Computer in Azure herzustellen.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.date: 10/31/2020
ms.topic: how-to
ms.author: cynthn
ms.openlocfilehash: 183b601a4521c3ff3e4578784f7adadd01045b0e
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147146"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Verwenden von SSH-Schlüsseln mit Windows in Azure

Dieser Artikel richtet sich an Windows-Benutzer, die SSH-Schlüssel ( *Secure Shell* ) [erstellen](#create-an-ssh-key-pair) und verwenden möchten, um eine [Verbindung](#connect-to-your-vm) mit virtuellen Linux-Computern (VMs) in Azure herzustellen. Sie können auch [SSH-Schlüssel im Azure-Portal generieren und speichern](../ssh-keys-portal.md), die dann beim Erstellen von VMs im Portal verwendet werden.


Informationen zum Verwenden von SSH-Schlüsseln von einem Linux- oder macOS-Client finden Sie in der [Kurzanleitung](mac-create-ssh-keys.md). Eine umfassendere Übersicht über SSH finden Sie unter [Ausführliche Schritte: Erstellen und Verwalten von SSH-Schlüsseln für die Authentifizierung bei einer Linux-VM in Azure](create-ssh-keys-detailed.md).

## <a name="overview-of-ssh-and-keys"></a>Übersicht über SSH und Schlüssel

[SSH](https://www.ssh.com/ssh/) ist ein Protokoll für verschlüsselte Verbindungen, das die sichere Anmeldung über ungesicherte Verbindungen ermöglicht. SSH ist das Standardverbindungsprotokoll für in Azure gehostete virtuelle Linux-Computer. SSH stellt zwar bereits eine verschlüsselte Verbindung bereit, bei Verwendung von Kennwörtern für SSH ist der virtuelle Computer jedoch weiterhin anfällig für Brute-Force-Angriffe. Es wird empfohlen, eine Verbindung mit einem virtuellen Computer über SSH mithilfe eines Schlüsselpaars aus öffentlichem und privatem Schlüssel (auch *SSH-Schlüssel* genannt) herzustellen. 

Das Paar aus einem öffentlichen und einem privaten Schlüssel ähnelt dem Schloss an Ihrer Haustür. Das Schloss ist **öffentlich** zugänglich, und jede Person mit dem richtigen Schlüssel kann die Tür öffnen. Der Schlüssel ist **privat** und wird nur an Personen weitergegeben, denen Sie vertrauen, da hiermit die Tür aufgeschlossen werden kann. 

- Beim Erstellen Ihrer Linux-VM wird der *öffentliche Schlüssel* auf der VM platziert. 

- Der *private Schlüssel* bleibt auf dem lokalen System. Bewahren Sie diesen privaten Schlüssel sicher auf. Geben Sie ihn nicht weiter.

Wenn Sie eine Verbindung mit Ihrer Linux-VM herstellen, testet die VM den SSH-Client, um sicherzustellen, dass er über den richtigen privaten Schlüssel verfügt. Wenn der Client über den privaten Schlüssel verfügt, erhält er Zugriff auf den virtuellen Computer. 

Abhängig von den Sicherheitsrichtlinien in Ihrer Organisation können Sie ein einzelnes Schlüsselpaar für den Zugriff auf mehrere Azure-VMs und -Dienste verwenden. Sie benötigen nicht für jede VM ein separates Schlüsselpaar. 

Sie können Ihren öffentlichen Schlüssel an beliebige Personen weitergeben, während auf Ihren privaten Schlüssel nur Sie (bzw. Ihre lokale Sicherheitsinfrastruktur) zugreifen können.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-clients"></a>SSH-Clients

Neuere Versionen von Windows 10 enthalten [OpenSSH-Clientbefehle](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) zum Erstellen und Verwenden von SSH-Schlüsseln und SSH-Verbindungen über PowerShell oder eine Eingabeaufforderung. Dies ist die einfachste Möglichkeit zum Erstellen einer SSH-Verbindung mit Ihrer Linux-VM von einem Windows-Computer aus. 

Sie können auch Bash in [Azure Cloud Shell](../../cloud-shell/overview.md) verwenden, um eine Verbindung mit Ihrer VM herzustellen. Sie können Cloud Shell in einem [Webbrowser](https://shell.azure.com/bash), über das [Azure-Portal](https://portal.azure.com) oder mithilfe der [Azure-Kontoerweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) als Terminal in Visual Studio Code verwenden.

Sie können auch das [Windows-Subsystem für Linux](/windows/wsl/about) installieren, um über SSH eine Verbindung mit Ihrer VM herzustellen und andere native Linux-Tools in einer Bash-Shell zu verwenden.

## <a name="create-an-ssh-key-pair"></a>Erstellen eines SSH-Schlüsselpaars

Erstellen Sie ein SSH-Schlüsselpaar mit dem Befehl `ssh-keygen`. Geben Sie einen Dateinamen ein, oder verwenden Sie den in Klammern angegebenen Standardwert (z. B. `C:\Users\username/.ssh/id_rsa`).  Geben Sie eine Passphrase für die Datei ein, oder lassen Sie sie leer, wenn Sie keine Passphrase verwenden möchten. 

```powershell
ssh-keygen -m PEM -t rsa -b 4096
```

## <a name="create-a-vm-using-your-key"></a>Erstellen einer VM mithilfe des Schlüssels

Um eine Linux-VM zu erstellen, die SSH-Schlüssel zur Authentifizierung verwendet, geben Sie Ihren öffentlichen SSH-Schlüssel beim Erstellen der VM an.

Bei Verwendung der Azure CLI geben Sie den Pfad und den Dateinamen für den öffentlichen Schlüssel mithilfe von `az vm create` und dem Parameter `--ssh-key-value` an.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVM \
   --image UbuntuLTS\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

Bei PowerShell verwenden Sie `New-AzVM` und fügen den SSH-Schlüssel der VM-Konfiguration hinzu. Ein Beispiel finden Sie unter [Schnellstart: Erstellen einer Linux-VM mit PowerShell in Azure](quick-create-powershell.md).

Wenn Sie eine große Anzahl von Bereitstellungen über das Portal vornehmen, können Sie Ihren öffentlichen Schlüssel in Azure hochladen, wo er dann beim Erstellen einer VM über das Portal problemlos ausgewählt werden kann. Weitere Informationen finden Sie unter [Hochladen eines SSH-Schlüssels](../ssh-keys-portal.md#upload-an-ssh-key).


## <a name="connect-to-your-vm"></a>Herstellen einer Verbindung mit Ihrer VM

Stellen Sie mit dem auf Ihrer Azure-VM bereitgestellten öffentlichen Schlüssel und dem privaten Schlüssel auf Ihrem lokalen System mithilfe der IP-Adresse oder dem DNS-Namen Ihres virtuellen Computers eine SSH-Verbindung mit Ihrem virtuellen Computer her. Ersetzen Sie *azureuser* und *10.111.12.123* im folgenden Befehl durch den Benutzernamen des Administrators, die IP-Adresse (oder den vollständig qualifizierten Domänennamen) und den Pfad zu Ihrem privaten Schlüssel:

```bash
ssh -i ~/.ssh/id_rsa.pub azureuser@10.111.12.123
```

Falls Sie beim Erstellen des Schlüsselpaars eine Passphrase konfiguriert haben, müssen Sie diese bei Aufforderung eingeben.

Wenn der virtuelle Computer die JIT-Zugriffsrichtlinie (Just-In-Time) verwendet, müssen Sie den Zugriff anfordern, bevor Sie eine Verbindung mit dem virtuellen Computer herstellen können. Weitere Informationen zur Just-In-Time-Richtlinie finden Sie unter [Verwalten des Zugriffs auf virtuelle Computer mithilfe der Just-In-Time-Richtlinie](../../security-center/security-center-just-in-time.md).


## <a name="next-steps"></a>Nächste Schritte

- Informationen zu SSH-Schlüsseln im Azure-Portal finden Sie unter [Generieren und Speichern von SSH-Schlüsseln im Azure-Portal](../ssh-keys-portal.md). Diese können dann beim Erstellen von VMs im Portal verwendet werden.

- Ausführliche Schrittbeschreibungen, Optionen und komplexere Beispiele zum Arbeiten mit SSH-Schlüsseln finden Sie unter [Ausführliche Schritte: Erstellen und Verwalten von SSH-Schlüsseln für die Authentifizierung bei einer Linux-VM in Azure](create-ssh-keys-detailed.md).

- Sie können auch PowerShell in Azure Cloud Shell zum Generieren von SSH-Schlüsseln und Herstellen von SSH-Verbindungen mit virtuellen Linux-Computern verwenden. Siehe [PowerShell – Schnellstart](../../cloud-shell/quickstart-powershell.md#ssh).

- Sollten beim Herstellen der SSH-Verbindung mit Ihren virtuellen Linux-Computern Probleme auftreten, lesen Sie unter [Behandeln von Problemen, Fehlern oder Ablehnungen im Zusammenhang mit der SSH-Verbindung mit einem virtuellen Azure Linux-Computer](../troubleshooting/troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json) weiter.
