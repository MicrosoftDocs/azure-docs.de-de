---
title: Verschieben von Dateien für Azure-Linux-VMs per SCP | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Dateien mit SCP und einem SSH-Schlüsselpaar auf sichere Weise auf eine bzw. von einer Linux-VM in Azure verschieben.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: danlep
ms.subservice: disks
ms.openlocfilehash: 7d5b2d2ee7e7320fb8bf91c8a62a0f46c403c977
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60328764"
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Verschieben von Dateien für eine Linux-VM per SCP

In diesem Artikel wird gezeigt, wie Sie Dateien per Secure Copy (SCP) von Ihrer Arbeitsstation auf eine Azure Linux-VM oder von einer Azure Linux-VM auf die Arbeitsstation verschieben. Das schnelle und sichere Verschieben von Dateien zwischen Ihrer Arbeitsstation und einer Linux-VM ist für die Verwaltung Ihrer Azure-Infrastruktur wichtig. 

Für diesen Artikel muss anhand von [Dateien mit öffentlichen und privaten SSH-Schlüsseln](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eine Linux-VM in Azure bereitgestellt werden. Sie benötigen auch einen SCP-Client für Ihren lokalen Computer. Er basiert auf SSH und ist in der standardmäßigen Bash-Shell der meisten Linux- und Macintosh-Computer sowie einigen Windows-Shells enthalten.

## <a name="quick-commands"></a>Schnellbefehle

Kopieren einer Datei auf die Linux-VM

```bash
scp file azureuser@azurehost:directory/targetfile
```

Kopieren einer Datei von der Linux-VM

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Ausführliche exemplarische Vorgehensweise

Als Beispiel verschieben wir eine Azure-Konfigurationsdatei auf eine Linux-VM und einem Protokolldateiverzeichnis von der Linux-VM – jeweils mithilfe von SCP und SSH-Schlüsseln.   

## <a name="ssh-key-pair-authentication"></a>Authentifizierung per SSH-Schlüsselpaar

SCP nutzt SSH für die Transportschicht. SSH übernimmt die Authentifizierung auf dem Zielhost, und verschiebt die Datei über einen verschlüsselten Tunnel, der für SSH standardmäßig bereitgestellt wird. Für die SSH-Authentifizierung können Benutzernamen und Kennwörter verwendet werden. Allerdings wird als bewährte Sicherheitsmethode die Authentifizierung mit einem öffentlichen und einem privaten SSH-Schlüssel empfohlen. Nachdem die Verbindung von SSH authentifiziert wurde, beginnt SCP mit dem Kopieren der Datei. Indem eine richtig konfigurierte Datei `~/.ssh/config` und öffentliche und private SSH-Schlüssel verwendet werden, kann die SCP-Verbindung nur mit einem Servernamen (oder einer IP-Adresse) hergestellt werden. Falls Sie nur über einen SSH-Schlüssel verfügen, sucht SCP im Verzeichnis `~/.ssh/` danach und verwendet ihn standardmäßig zum Anmelden bei der VM.

Weitere Informationen zum Konfigurieren der Datei `~/.ssh/config` und zu öffentlichen und privaten SSH-Schlüsseln finden Sie unter [Erstellen von SSH-Schlüsseln](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="scp-a-file-to-a-linux-vm"></a>Verschieben einer Datei per SCP auf eine Linux-VM

Für das erste Beispiel kopieren wir eine Azure-Konfigurationsdatei auf eine Linux-VM, die zum Bereitstellen von Automation verwendet wird. Da diese Datei Azure-API-Anmeldeinformationen sowie Geheimnisse enthält, spielt die Sicherheit eine große Rolle. Der verschlüsselte, von SSH bereitgestellte Tunnel schützt die Inhalte der Datei.

Mit dem folgenden Befehl wird die lokale Datei *.azure/config* auf eine Azure-VM mit dem FQDN *myserver.eastus.cloudapp.azure.com* kopiert. Der Benutzername des Administrators auf der Azure-VM lautet *azureuser*. Die Datei wird im Verzeichnis */home/azureuser/* gespeichert. Fügen Sie Ihre eigenen Werte in diesen Befehl ein.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>Verschieben eines Verzeichnisses von einer Linux-VM per SCP

In diesem Beispiel kopieren wir ein Verzeichnis mit Protokolldateien von der Linux-VM auf Ihre Arbeitsstation. Eine Protokolldatei kann möglicherweise sensible oder vertrauliche Daten enthalten. Mit SCP wird jedoch sichergestellt, dass die Inhalte der Protokolldateien verschlüsselt sind. Die Verwendung von SCP zum Übertragen der Dateien ist der einfachste und gleichzeitig ein sicherer Weg zur Übertragung des Protokollverzeichnisses und der Dateien auf Ihre Arbeitsstation.

Mit dem folgenden Befehl werden Dateien im Verzeichnis */home/azureuser/logs/* auf der Azure-VM in das lokale Verzeichnis „/tmp“ kopiert:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

Mit dem CLI-Flag `-r` wird SCP angewiesen, die Dateien und Verzeichnisse rekursiv von dem Punkt des Verzeichnisses zu kopieren, der im Befehl angegeben ist.  Beachten Sie auch, dass die Befehlszeilensyntax dem Kopierbefehl `cp` ähnelt.

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von Benutzern, SSH und Überprüfen oder Reparieren von Datenträgern auf Azure-Linux-VMs mit der VMAccess-Erweiterung](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
