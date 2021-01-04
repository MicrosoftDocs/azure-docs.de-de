---
title: Ausführliche Schritte zum Erstellen eines SSH-Schlüsselpaars
description: Lernen Sie die ausführlichen Schritte zum Erstellen und Verwalten eines SSH-Schlüsselpaars (öffentlich und privat) für virtuelle Linux-Computer in Azure kennen.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 07/31/2020
ms.author: cynthn
ms.openlocfilehash: 34a84ed333172ea0931c529d2dbeee1b774ae8c5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016365"
---
# <a name="detailed-steps-create-and-manage-ssh-keys-for-authentication-to-a-linux-vm-in-azure"></a>Ausführliche Schritte: Erstellen und Verwalten von SSH-Schlüsseln für die Authentifizierung bei einer Linux-VM in Azure

Mit einem SSH-Schlüsselpaar (Secure Shell) können Sie einen virtuellen Linus-Computer erstellen, bei dessen Authentifizierung SSH-Schlüssel verwendet werden. In diesem Artikel wird gezeigt, wie Sie eine Datei mit einem SSH-Schlüsselpaar (ein öffentlicher und ein privater RSA-Schlüssel) für SSH-Clientverbindungen erstellen und verwenden.

Schnellbefehle finden Sie unter [Erstellen eines SSH-Schlüsselpaars (öffentlich und privat) für virtuelle Linux-Computer in Azure](mac-create-ssh-keys.md).

Informationen zum Erstellen von SSH-Schlüsseln und deren Verwendung zum Herstellen einer Verbindung mit einem **Windows**-Computer finden Sie unter [Verwenden von SSH-Schlüsseln mit Windows in Azure](ssh-from-windows.md). Sie können auch das [Azure-Portal](../ssh-keys-portal.md) verwenden, um SSH-Schlüssel zum Erstellen virtueller Computer im Portal zu erstellen und zu verwalten.

[!INCLUDE [virtual-machines-common-ssh-overview](../../../includes/virtual-machines-common-ssh-overview.md)]

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-keys-use-and-benefits"></a>Verwendung und Vorteile von SSH-Schlüsseln

Wenn Sie eine Azure-VM erstellen, indem Sie den öffentlichen Schlüssel angeben, kopiert Azure den öffentlichen Schlüssel (im `.pub`-Format) auf dem virtuellen Computer in den `~/.ssh/authorized_keys`-Ordner. SSH-Schlüssel in `~/.ssh/authorized_keys` werden verwendet, um vom Client zu fordern, für eine SSH-Verbindung den entsprechenden privaten Schlüssel bereitzustellen. Bei einem virtuellen Azure-Computer unter Linux, bei dem SSH-Schlüssel für die Authentifizierung verwendet werden, konfiguriert Azure den SSHD-Server so, dass nur SSH-Schlüssel (und keine kennwortbasierten Anmeldungen) möglich sind. Wenn Sie virtuelle Azure Linux-Computer mit SSH-Schlüsseln erstellen, können Sie zur Sicherung der Bereitstellung des virtuellen Computers beitragen, und der üblicherweise nachträglich auszuführende Konfigurationsschritt zum Deaktivieren von Kennwörtern in der Datei `sshd_config` entfällt.

Falls Sie keine SSH-Schlüssel verwenden möchten, können Sie Ihren virtuellen Linux-Computer für die Kennwortauthentifizierung einrichten. Bei virtuellen Computern ohne Internetverbindung sind Kennwörter unter Umständen ausreichend. Allerdings müssen Sie weiterhin Ihre Kennwörter für die einzelnen virtuellen Linux-Computer verwalten und sich um angemessene Kennwortrichtlinien und -verfahren kümmern, indem Sie beispielsweise eine Mindestlänge für Kennwörter festlegen und sicherstellen, dass sie regelmäßig aktualisiert werden. 

## <a name="generate-keys-with-ssh-keygen"></a>Generieren von Schlüsseln mit „ssh-keygen“

Ein bevorzugter Befehl zum Erstellen der Schlüssel ist `ssh-keygen`, der mit OpenSSH-Hilfsprogrammen in Azure Cloud Shell, einem macOS- oder Linux-Host und Windows 10 verfügbar ist. `ssh-keygen` stellt eine Reihe von Fragen, und anschließend werden ein privater Schlüssel und ein passender öffentlicher Schlüssel geschrieben. 

SSH-Schlüssel werden standardmäßig im Verzeichnis `~/.ssh` gespeichert.  Wenn Sie nicht über das Verzeichnis `~/.ssh` verfügen, können Sie es mithilfe des Befehls `ssh-keygen` gleich mit den richtigen Berechtigungen erstellen.

### <a name="basic-example"></a>Einfaches Beispiel

Der folgende `ssh-keygen`-Befehl generiert Dateien für öffentliche und private 4096-Bit-SSH-RSA-Schlüssel standardmäßig im Verzeichnis `~/.ssh`. Wenn am aktuellen Speicherort bereits ein SSH-Schlüsselpaar vorhanden ist, werden diese Dateien überschrieben.

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

### <a name="detailed-example"></a>Ausführliches Beispiel
Das folgende Beispiel zeigt zusätzliche Befehlsoptionen, um ein SSH-RSA-Schlüsselpaar zu erstellen. Wenn am aktuellen Speicherort bereits ein SSH-Schlüsselpaar vorhanden ist, werden diese Dateien überschrieben. 

```bash
ssh-keygen \
    -m PEM \
    -t rsa \
    -b 4096 \
    -C "azureuser@myserver" \
    -f ~/.ssh/mykeys/myprivatekey \
    -N mypassphrase
```

**Befehlsbeschreibung**

`ssh-keygen` = zum Erstellen der Schlüssel verwendetes Programm

`-m PEM` = Formatieren des Schlüssels als PEM

`-t rsa` = Typ des zu erstellenden Schlüssels, in diesem Fall im RSA-Format

`-b 4096` = die Anzahl der Bits im Schlüssel, in diesem Fall 4096

`-C "azureuser@myserver"` = ein Kommentar, der zur einfachen Identifizierung am Ende der Datei mit dem öffentlichen Schlüssel angefügt wird. Normalerweise wird eine E-Mail-Adresse als Kommentar verwendet, aber Sie können je nach Eignung für Ihre Infrastruktur auch etwas anderes angeben.

`-f ~/.ssh/mykeys/myprivatekey` = der Dateiname der Datei des privaten Schlüssels, wenn Sie nicht den Standardnamen verwenden. Eine entsprechende Datei des öffentlichen Schlüssels mit `.pub` wird im gleichen Verzeichnis generiert. Das Verzeichnis muss vorhanden sein.

`-N mypassphrase` = eine zusätzliche Passphrase, die verwendet wird, um auf die Datei mit dem privaten Schlüssel zuzugreifen. 

### <a name="example-of-ssh-keygen"></a>Beispiel für ssh-keygen

```bash
ssh-keygen -t -m PEM rsa -b 4096 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:vFfHHrpSGQBd/oNdvNiX0sG9Vh+wROlZBktNZw9AUjA azureuser@myserver
The key's randomart image is:
+---[RSA 4096]----+
|        .oE=*B*+ |
|          o+o.*++|
|           .oo++*|
|       .    .B+.O|
|        S   o=BO.|
|         . .o++o |
|        . ... .  |
|         ..  .   |
|           ..    |
+----[SHA256]-----+
```

#### <a name="saved-key-files"></a>Gespeicherte Schlüsseldateien

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

Der Schlüsselpaarname für diesen Artikel. Standardmäßig wird ein Schlüsselpaar mit dem Namen `id_rsa` verwendet, und einige Tools erwarten den Namen `id_rsa` für die Datei mit dem privaten Schlüssel. Es ist also ratsam, dies zu berücksichtigen. Das Verzeichnis `~/.ssh/` ist der Standardspeicherort für SSH-Schlüsselpaare und die SSH-Konfigurationsdatei. Wenn kein vollständiger Pfad angegeben wird, erstellt `ssh-keygen` die Schlüssel im aktuellen Arbeitsverzeichnis anstatt im Standardverzeichnis `~/.ssh`.

#### <a name="list-of-the-ssh-directory"></a>Liste des `~/.ssh`-Verzeichnisses

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

#### <a name="key-passphrase"></a>Schlüsselpassphrase

`Enter passphrase (empty for no passphrase):`

Sie sollten *unbedingt* Ihrem privaten Schlüssel eine Passphrase hinzuzufügen. Wenn die Schlüsseldatei nicht mit einer Passphrase geschützt ist, kann sie von Personen, die über die Datei verfügen, zum Anmelden bei allen Servern mit dem entsprechenden öffentlichen Schlüssel verwendet werden. Das Hinzufügen einer Passphrase sorgt für mehr Schutz. Wenn sich eine Person Zugang zu Ihrer Datei mit dem privaten Schlüssel verschafft, haben Sie so mehr Zeit, die Schlüssel zu ändern.

## <a name="generate-keys-automatically-during-deployment"></a>Automatisches Generieren der Schlüssel während der Bereitstellung

Bei Verwendung der [Azure CLI](/cli/azure) zum Erstellen Ihres virtuellen Computers können Sie optional den Befehl [az vm create](/cli/azure/vm) mit der `--generate-ssh-keys`-Option ausführen, um Dateien für öffentliche und private SSH-Schlüssel zu generieren. Die Schlüssel werden im Verzeichnis „~/.ssh“ gespeichert. Beachten Sie, dass diese Befehlsoption keine Schlüssel überschreibt, wenn sie bereits an diesem Speicherort vorhanden sind.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Bereitstellen des öffentlichen SSH-Schlüssels beim Bereitstellen eines virtuellen Computers

Um eine Linux-VM zu erstellen, die SSH-Schlüssel zur Authentifizierung verwendet, geben Sie Ihren öffentlichen SSH-Schlüssel beim Erstellen des virtuellen Computers mit Azure-Portal, CLI, Resource Manager-Vorlagen oder anderen Methoden ein. Wenn Sie das Portal verwenden, geben Sie den öffentlichen Schlüssel selbst ein. Bei Verwendung der [Azure CLI](/cli/azure) zum Erstellen Ihres virtuellen Computers mit einem vorhandenen öffentlichen Schlüssel führen Sie den Befehl [az vm create](/cli/azure/vm) mit der `--ssh-key-value`-Option aus, um den Wert oder Speicherort dieses öffentlichen Schlüssels anzugeben. 

Falls Sie nicht mit dem Format öffentlicher SSH-Schlüssel vertraut sein sollten, können Sie Ihren öffentlichen Schlüssel anzeigen, indem Sie `cat` wie unten angegeben ausführen. Ersetzen Sie hierbei `~/.ssh/id_rsa.pub` durch den Speicherort Ihrer eigenen Datei mit dem öffentlichen Schlüssel:

```bash
cat ~/.ssh/id_rsa.pub
```

Die Ausgabe sieht in etwa wie folgt aus (redigierte Darstellung):

```
ssh-rsa XXXXXXXXXXc2EAAAADAXABAAABAXC5Am7+fGZ+5zXBGgXS6GUvmsXCLGc7tX7/rViXk3+eShZzaXnt75gUmT1I2f75zFn2hlAIDGKWf4g12KWcZxy81TniUOTjUsVlwPymXUXxESL/UfJKfbdstBhTOdy5EG9rYWA0K43SJmwPhH28BpoLfXXXXXG+/ilsXXXXXKgRLiJ2W19MzXHp8z3Lxw7r9wx3HaVlP4XiFv9U4hGcp8RMI1MP1nNesFlOBpG4pV2bJRBTXNXeY4l6F8WZ3C4kuf8XxOo08mXaTpvZ3T1841altmNTZCcPkXuMrBjYSJbA8npoXAXNwiivyoe3X2KMXXXXXdXXXXXXXXXXCXXXXX/ azureuser@myserver
```

Stellen Sie sicher, dass Sie weder zusätzliche Leerzeichen kopieren noch zusätzliche Zeilenumbrüche einfügen, wenn Sie den Inhalt der Datei mit dem öffentlichen Schlüssel im Azure-Portal oder in einer Resource Manager-Vorlage kopieren und einfügen. Bei Nutzung von macOS können Sie die Datei mit dem öffentlichen Schlüssel (standardmäßig `~/.ssh/id_rsa.pub`) beispielsweise per Pipe mit **pbcopy** verknüpfen, um den Inhalt zu kopieren (für diesen Zweck gibt es auch andere Linux-Programme, z.B. `xclip`).

Wenn Sie lieber einen öffentlichen Schlüssel verwenden möchten, der in einem mehrzeiligen Format vorliegt, können Sie aus dem öffentlichen Schlüssel, den Sie zuvor erstellt haben, in einem PEM-Container einen formatierten RFC4716-Schlüssel generieren.

So erstellen Sie einen Schlüssel im RFC4716-Format aus einem vorhandenen öffentlichen SSH-Schlüssel:

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="ssh-to-your-vm-with-an-ssh-client"></a>Herstellen einer SSH-Verbindung mit Ihrem virtuellen Computer mit einem SSH-Client
Stellen Sie mit dem auf Ihrer Azure-VM bereitgestellten öffentlichen Schlüssel und dem privaten Schlüssel auf Ihrem lokalen System mithilfe der IP-Adresse oder dem DNS-Namen Ihres virtuellen Computers eine SSH-Verbindung mit Ihrem virtuellen Computer her. Ersetzen Sie *azureuser* und *myvm.westus.cloudapp.azure.com* im folgenden Befehl mit dem Benutzernamen des Administrators und dem vollständig qualifizierten Domänennamen (oder der IP-Adresse):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Falls Sie beim Erstellen des Schlüsselpaars eine Passphrase angegeben haben, müssen Sie die Passphrase eingeben, wenn Sie während des Anmeldeprozesses dazu aufgefordert werden. (Der Server wird dem Ordner `~/.ssh/known_hosts` hinzugefügt, und Sie werden erst wieder zum Herstellen der Verbindung aufgefordert, wenn sich der öffentliche Schlüssel auf Ihrer Azure-VM ändert oder der Servername unter `~/.ssh/known_hosts` entfernt wird.)

Wenn der virtuelle Computer die JIT-Zugriffsrichtlinie (Just-In-Time) verwendet, müssen Sie den Zugriff anfordern, bevor Sie eine Verbindung mit dem virtuellen Computer herstellen können. Weitere Informationen zur Just-In-Time-Richtlinie finden Sie unter [Verwalten des Zugriffs auf virtuelle Computer mithilfe der Just-In-Time-Richtlinie](../../security-center/security-center-just-in-time.md).

## <a name="use-ssh-agent-to-store-your-private-key-passphrase"></a>Verwenden von „ssh-agent“ zum Speichern der Passphrase für den privaten Schlüssel

Sie können `ssh-agent` zum Zwischenspeichern der Passphrase für die private Schlüsseldatei verwenden, damit Sie die Passphrase nicht bei jeder SSH-Anmeldung eingeben müssen. Bei Verwendung eines Macintosh-Computers wird die Passphrase für private Schlüssel sicher im „Schlüsselbund“ von macOS gespeichert, wenn Sie `ssh-agent` aufrufen.

Überprüfen und verwenden Sie `ssh-agent` und `ssh-add`, um das SSH-System über die Schlüsseldateien zu informieren, damit Sie die Passphrase nicht interaktiv verwenden müssen.

```bash
eval "$(ssh-agent -s)"
```

Fügen Sie anschließend den privaten Schlüssel `ssh-agent` mit dem Befehl `ssh-add` hinzu.

```bash
ssh-add ~/.ssh/id_rsa
```

Die Passphrase für den privaten Schlüssel ist jetzt unter `ssh-agent` gespeichert.

## <a name="use-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Kopieren des Schlüssels auf einen vorhandenen virtuellen Computer mithilfe von „ssh-copy-id“

Wenn Sie bereits einen virtuellen Computer erstellt haben, können Sie einen neuen öffentlichen SSH-Schlüssel zu Ihrem virtuellen Linux-Computer mithilfe von `ssh-copy-id` hinzufügen.

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub azureuser@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Erstellen und Konfigurieren einer SSH-Konfigurationsdatei

Sie können eine SSH-Konfigurationsdatei (`~/.ssh/config`) erstellen und konfigurieren, um Anmeldungen zu beschleunigen und das Verhalten Ihres SSH-Clients zu optimieren. 

Das folgende Beispiel zeigt eine einfache Konfiguration, mit der Sie sich schnell mithilfe des standardmäßigen privaten SSH-Schlüssels bei einem bestimmten virtuellen Computer als Benutzer anmelden können. 

Erstellen Sie die Datei.

```bash
touch ~/.ssh/config
```

Bearbeiten der Datei zum Hinzufügen der neuen SSH-Konfiguration

```bash
vim ~/.ssh/config
```

Fügen Sie die geeigneten Konfigurationseinstellungen für den VM-Host hinzu. In diesem Beispiel lautet der VM-Name *myvm*, und der Kontoname ist *azureuser*.

```bash
# Azure Keys
Host myvm
  Hostname 102.160.203.241
  User azureuser
# ./Azure Keys
```

Sie können Konfigurationen für zusätzliche Hosts hinzufügen, um ihnen jeweils die Verwendung ihres eigenen dedizierten Schlüsselpaars zu ermöglichen. Unter [SSH CONFIG FILE](https://www.ssh.com/ssh/config/) (SSH-Konfigurationsdatei) finden Sie erweiterte Konfigurationsoptionen.

Da Sie jetzt über ein SSH-Schlüsselpaar und eine konfigurierte SSH-Konfigurationsdatei verfügen, können Sie sich schnell und sicher an Ihrer Linux-VM anmelden. Wenn Sie den folgenden Befehl ausführen, sucht SSH alle Einstellungen des `Host myvm`-Blocks in der SSH-Konfigurationsdatei und lädt sie.

```bash
ssh myvm
```

Beim ersten Anmelden bei einem Server mit einem SSH-Schlüssel werden Sie vom Befehl zur Eingabe der Passphrase für die Schlüsseldatei aufgefordert.

## <a name="next-steps"></a>Nächste Schritte

Der nächste Schritt besteht darin, Azure-Linux-VMs mit dem neuen öffentlichen SSH-Schlüssel zu erstellen. Virtuelle Azure-Computer, die mit einem öffentlichen SSH-Schlüssel als Anmeldung erstellt werden, sind besser gesichert als VMs, die mit der Standardanmeldemethode und Kennwörtern erstellt werden.

* [Erstellen Sie eines virtuellen Linux-Computers mit dem Azure-Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Erstellen einer Linux-VM mit der Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Erstellen einer Linux-VM mit einer Azure-Vorlage](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
