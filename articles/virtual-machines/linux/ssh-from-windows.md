---
title: Verwenden von SSH-Schlüsseln mit Windows für virtuelle Linux-Computer | Microsoft Docs
description: Erfahren Sie, wie Sie SSH-Schlüssel auf einem Windows-Computer erstellen und verwenden, um Verbindungen mit virtuellen Linux-Computern in Azure herzustellen.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: 0ac62a99f5735647f67917d441645e30444b3818
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61473638"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Verwenden von SSH-Schlüsseln mit Windows in Azure

In diesem Artikel werden Möglichkeiten zum Generieren und Verwenden von *Secure Shell*-Schlüsseln (SSH) auf einem Windows-Computer beschrieben, um einen virtuellen Linux-Computer in Azure zu erstellen und eine Verbindung damit herzustellen. Wie Sie die SSH-Schlüssel von einem Linux- oder macOS-Client verwenden, erfahren Sie in der [Schnellanleitung](mac-create-ssh-keys.md) oder [ausführlichen](create-ssh-keys-detailed.md) Anleitung.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="windows-packages-and-ssh-clients"></a>Windows-Pakete und SSH-Clients
Die Verbindungsherstellung mit virtuellen Linux-Computern in Azure sowie deren Verwaltung wird mithilfe eines *SSH-Clients* durchgeführt. Computer, die Linux oder macOS ausführen, verfügen in der Regel über eine Suite von SSH-Befehlen zum Generieren und Verwalten von SSH-Schlüsseln und Herstellen von SSH-Verbindungen. 

Auf Windows-Computern sind nicht immer vergleichbare SSH-Befehle installiert. Neuere Versionen von Windows 10 bieten [OpenSSH-Clientbefehle](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) zum Erstellen und Verwalten von SSH-Schlüsseln und SSH-Verbindungen über eine Eingabeaufforderung. Bei aktuellen Windows 10-Versionen mit dem [Windows-Subsystem für Linux](https://docs.microsoft.com/windows/wsl/about) können Sie direkt aus einer Bash-Shell auf Hilfsprogramme, z.B. einen SSH-Client, zugreifen und diese ausführen. 

Andere gängige Windows-SSH-Clients, die Sie lokal installieren können, sind in den folgenden Paketen enthalten:

* [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [Git für Windows](https://git-for-windows.github.io/)
* [MobaXterm](https://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)

Sie können auch die SSH-Hilfsprogramme verwenden, die in der [Azure Cloud Shell](../../cloud-shell/overview.md) in Bash zur Verfügung stehen. 

* Greifen Sie in Ihrem Webbrowser unter [https://shell.azure.com](https://shell.azure.com) oder im [Azure-Portal](https://portal.azure.com) auf Cloud Shell zu. 
* Greifen Sie aus VS Code heraus auf Cloud Shell als Terminal zu, indem Sie die [Erweiterung „Azure Account“](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) installieren.

## <a name="create-an-ssh-key-pair"></a>Erstellen eines SSH-Schlüsselpaars
In den folgenden Abschnitten werden zwei Optionen zum Erstellen eines SSH-Schlüsselpaars unter Windows beschrieben. Sie können einen Shell-Befehl (`ssh-keygen`) oder ein GUI-Tool (PuTTYgen) verwenden.

### <a name="create-ssh-keys-with-ssh-keygen"></a>Erstellen von SSH-Schlüsseln mit „ssh-keygen“

Wenn Sie unter Windows eine Befehlsshell ausführen, die SSH-Clienttools unterstützt (oder wenn Sie Azure Cloud Shell verwenden), erstellen Sie mit dem Befehl `ssh-keygen` ein SSH-Schlüsselpaar. Geben Sie den folgenden Befehl ein, und beantworten Sie die Eingabeaufforderungen. Wenn im ausgewählten Speicherort bereits ein SSH-Schlüsselpaar vorhanden ist, werden diese Dateien überschrieben. 

```bash
ssh-keygen -t rsa -b 2048
```

Weitere Hintergrundinformationen finden Sie in den Beschreibungen der [schnellen](mac-create-ssh-keys.md) bzw. [ausführlichen](create-ssh-keys-detailed.md) Schritte zum Erstellen von SSH-Schlüsseln mit `ssh-keygen`.

### <a name="create-ssh-keys-with-puttygen"></a>Erstellen von SSH-Schlüsseln mit PuTTYgen

Wenn Sie ein GUI-basiertes Tool verwenden möchten, um die SSH-Schlüssel zu erstellen, können Sie den PuTTYgen-Schlüsselgenerator verwenden, der im [PuTTY-Downloadpaket](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) enthalten ist. 

So erstellen Sie ein SSH-RSA-Schlüsselpaar mit PuTTYgen:

1. Starten Sie PuTTYgen.

2. Klicken Sie auf **Generate (Generieren)** . PuTTYgen generiert standardmäßig einen 2048-Bit-SSH-2-RSA-Schlüssel.

4. Bewegen Sie die Maus über den leeren Bereich, um Zufälligkeit für den Schlüssel zu erzeugen.

5. Nach dem Generieren des öffentlichen Schlüssels können Sie optional eine Passphrase eingeben und bestätigen. Wenn Sie sich mit Ihrem privaten SSH-Schlüssel bei dem virtuellen Computer authentifizieren, werden Sie zur Eingabe der Passphrase aufgefordert. Ohne Passphrase kann sich jeder, der über Ihren privaten Schlüssel verfügt, bei den virtuellen Computern oder Diensten anmelden, die diesen Schlüssel verwenden. Daher empfiehlt es sich, eine Passphrase zu erstellen. Wenn Sie die Passphrase vergessen, besteht jedoch keine Möglichkeit, diese wiederherzustellen.

6. Der öffentliche Schlüssel wird am oberen Rand des Fensters angezeigt. Sie können diesen gesamten öffentlichen Schlüssel kopieren und dann beim Erstellen eines virtuellen Linux-Computers in das Azure-Portal oder eine Azure Resource Manager-Vorlage einfügen. Sie können auch auf **Öffentlichen Schlüssel speichern** klicken, um eine Kopie auf Ihrem Computer zu speichern:

    ![Speichern der Datei mit dem öffentlichen PuTTY-Schlüssel](./media/ssh-from-windows/save-public-key.png)

7. Optional können Sie auch auf **Privaten Schlüssel speichern** klicken, um den privaten Schlüssel im privaten PuTTY-Schlüsselformat (PPK-Datei) zu speichern. Sie benötigen die PPK-Datei später, wenn Sie PuTTY verwenden möchten, um eine SSH-Verbindung mit dem virtuellen Computer herzustellen.

    ![Speichern der PuTTY-Datei mit dem privaten Schlüssel](./media/ssh-from-windows/save-ppk-file.png)

    Wenn Sie den privaten Schlüssel im OpenSSH-Format speichern möchten, klicken Sie auf das von vielen SSH-Clients verwendete Format für private Schlüssel, und klicken Sie auf **Konvertierungen** > **OpenSSH-Schlüssel exportieren**.

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Angeben eines öffentlichen SSH-Schlüssels beim Bereitstellen eines virtuellen Computers

Um eine Linux-VM zu erstellen, die SSH-Schlüssel zur Authentifizierung verwendet, geben Sie Ihren öffentlichen SSH-Schlüssel beim Erstellen des virtuellen Computers im Azure-Portal oder mit anderen Methoden ein.

Das folgende Beispiel zeigt, wie Sie diesen öffentlichen Schlüssel beim Erstellen eines virtuellen Linux-Computers kopieren und in das Azure-Portal einfügen. Der öffentliche Schlüssel wird dann in der Regel auf Ihrem neuen virtuellen Computer im Verzeichnis „~/.ssh/authorized_key“ gespeichert.

   ![Verwenden des öffentlichen Schlüssels beim Erstellen eines virtuellen Computers im Azure-Portal](./media/ssh-from-windows/use-public-key-azure-portal.png)


## <a name="connect-to-your-vm"></a>Herstellen einer Verbindung mit Ihrer VM

Eine Möglichkeit, eine SSH-Verbindung mit Ihrer Linux-VM von Windows aus herzustellen, ist die Verwendung eines SSH-Clients. Dies ist die bevorzugte Methode, wenn ein SSH-Client auf Ihrem Windows-System installiert ist oder wenn Sie die SSH-Tools in Bash in Azure Cloud Shell verwenden. Wenn Sie ein GUI-basiertes Tool bevorzugen, können Sie die Verbindung mit PuTTY herstellen.  

### <a name="use-an-ssh-client"></a>Verwenden eines SSH-Clients
Stellen Sie mit dem auf Ihrer Azure-VM bereitgestellten öffentlichen Schlüssel und dem privaten Schlüssel auf Ihrem lokalen System mithilfe der IP-Adresse oder dem DNS-Namen Ihres virtuellen Computers eine SSH-Verbindung mit Ihrem virtuellen Computer her. Ersetzen Sie *azureuser* und *myvm.westus.cloudapp.azure.com* im folgenden Befehl mit dem Benutzernamen des Administrators und dem vollständig qualifizierten Domänennamen (oder der IP-Adresse):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Falls Sie beim Erstellen des Schlüsselpaars eine Passphrase konfiguriert haben, müssen Sie die Passphrase eingeben, wenn Sie während des Anmeldeprozesses dazu aufgefordert werden.

Wenn der virtuelle Computer die JIT-Zugriffsrichtlinie (Just-In-Time) verwendet, müssen Sie den Zugriff anfordern, bevor Sie eine Verbindung mit dem virtuellen Computer herstellen können. Weitere Informationen zur Just-In-Time-Richtlinie finden Sie unter [Verwalten des Zugriffs auf virtuelle Computer mithilfe der Just-In-Time-Richtlinie](../../security-center/security-center-just-in-time.md).

### <a name="connect-with-putty"></a>Herstellen der Verbindung mit PuTTY

Wenn Sie das [PuTTY-Downloadpaket](https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) installiert und zuvor eine PuTTY-Datei für private Schlüssel (PPK-Datei) generiert haben, können Sie mit PuTTY eine Verbindung mit einer Linux-VM herstellen.

1. Starten Sie PuTTY.

2. Geben Sie den Hostnamen oder die IP-Adresse des virtuellen Computers aus dem Azure-Portal ein:

    ![Herstellen einer neuen PuTTY-Verbindung](./media/ssh-from-windows/putty-new-connection.png)

3. Wählen Sie die Kategorie **Verbindung** > **SSH** > **Auth** aus. Navigieren Sie zu Ihrer PuTTY-Datei für private Schlüssel (PPK-Datei), und wählen Sie sie aus:

    ![Auswählen Ihrer PuTTY-Datei für private Schlüssel für die Authentifizierung](./media/ssh-from-windows/putty-auth-dialog.png)

4. Klicken Sie auf **Open (Öffnen)** , um die Verbindung mit Ihrer VM herzustellen.

## <a name="next-steps"></a>Nächste Schritte

* Ausführliche Schrittbeschreibungen, Optionen und komplexere Beispiele zum Arbeiten mit SSH-Schlüsseln finden Sie unter [Ausführliche Schritte: Erstellen und Verwalten von SSH-Schlüsseln für die Authentifizierung bei einer Linux-VM in Azure](create-ssh-keys-detailed.md).

* Sie können auch PowerShell in Azure Cloud Shell zum Generieren von SSH-Schlüsseln und Herstellen von SSH-Verbindungen mit virtuellen Linux-Computern verwenden. Siehe [PowerShell – Schnellstart](../../cloud-shell/quickstart-powershell.md#ssh).

* Sollten beim Herstellen der SSH-Verbindung mit Ihren virtuellen Linux-Computern Probleme auftreten, lesen Sie unter [Behandeln von Problemen, Fehlern oder Ablehnungen im Zusammenhang mit der SSH-Verbindung mit einem virtuellen Azure Linux-Computer](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) weiter.
