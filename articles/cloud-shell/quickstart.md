---
title: Schnellstartanleitung für Bash in Azure Cloud Shell | Microsoft-Dokumentation
description: Schnellstart für Bash in Cloud Shell
services: ''
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
ms.date: 03/12/2018
ms.author: damaerte
ms.openlocfilehash: b8f96de7214a46c9e38182c141343a46c0e28139
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60199583"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Schnellstart für Bash in Azure Cloud Shell

Dieses Dokument erläutert die Verwendung von Bash in Azure Cloud Shell im [Azure-Portal](https://ms.portal.azure.com/).

> [!NOTE]
> Ein Schnellstart zu [PowerShell in Azure Cloud Shell](quickstart-powershell.md) ist ebenfalls verfügbar.

## <a name="start-cloud-shell"></a>Starten von Cloud Shell
1. Starten Sie **Cloud Shell** über den oberen Navigationsbereich im Azure-Portal. <br>
![](media/quickstart/shell-icon.png)

2. Wählen Sie ein Abonnement aus, in dem ein Speicherkonto und eine Microsoft Azure Files-Freigabe erstellt werden sollen.
3. Wählen Sie „Speicher erstellen“ aus.

> [!TIP]
> Sie werden in jeder Sitzung automatisch bei Azure CLI authentifiziert.

### <a name="select-the-bash-environment"></a>Auswählen der Bash-Umgebung
Stellen Sie sicher, dass auf der linken Seite des Shellfensters im Dropdownmenü für Umgebungen die Option `Bash` ausgewählt ist. <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Festlegen des Abonnements
1. Führen Sie die Abonnements auf, auf die Sie Zugriff haben.
   ```azurecli-interactive
   az account list
   ```

2. Legen Sie Ihr bevorzugtes Abonnement fest: <br>
```azurecli-interactive
az account set --subscription 'my-subscription-name'
```

> [!TIP]
> Ihr Abonnement wird mithilfe von `/home/<user>/.azure/azureProfile.json` für künftige Sitzungen gespeichert.

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Erstellen Sie in „USA, Westen“ eine neue Ressourcengruppe mit dem Namen „MyRG“.
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Erstellen eines virtuellen Linux-Computers
Erstellen Sie einen virtuellen Ubuntu-Computer in Ihrer neuen Ressourcengruppe. Azure CLI erstellt SSH-Schlüssel und richtet den virtuellen Computer damit ein. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> Durch die Verwendung von `--generate-ssh-keys` wird Azure CLI angewiesen, auf Ihrer VM und im Verzeichnis `$Home` öffentliche und private Schlüssel zu erstellen und einzurichten. Standardmäßig befinden sich Schlüssel in der Cloud Shell unter `/home/<user>/.ssh/id_rsa` und `/home/<user>/.ssh/id_rsa.pub`. Ihr Ordner `.ssh` wird im 5-GB-Image Ihrer angefügten Dateifreigabe zur dauerhaften Aufbewahrung von `$Home` gespeichert.

Ihr Benutzername auf diesem virtuellen Computer ist der Benutzername, der in Cloud Shell verwendet wird ($User@Azure:).

### <a name="ssh-into-your-linux-vm"></a>Herstellen einer SSH-Verbindung mit Ihrem virtuellen Linux-Computer
1. Suchen Sie über die Suchleiste des Azure-Portals nach dem Namen Ihres virtuellen Computers.
2. Klicken Sie auf „Verbinden“, um Ihren VM-Namen und die öffentliche IP-Adresse abzurufen. <br>
   ![](media/quickstart/sshcmd-copy.png)

3. Stellen Sie mit dem Befehl `ssh` eine SSH-Verbindung mit Ihrer VM her.
   ```
   ssh username@ipaddress
   ```

Nach dem Herstellen der SSH-Verbindung sollte der Willkommensbildschirm von Ubuntu angezeigt werden. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Bereinigen 
1. Beenden Sie Ihre SSH-Sitzung.
   ```azurecli-interactive
   exit
   ```

2. Löschen Sie Ihre Ressourcengruppe und alle darin befindlichen Ressourcen.
   ```azurecli-interactive
   az group delete -n MyRG
   ```

## <a name="next-steps"></a>Nächste Schritte
[Erfahren Sie mehr über das Beibehalten von Dateien für Bash in Cloud Shell](persisting-shell-storage.md) <br>
[Erfahren Sie mehr über die Azure CLI](https://docs.microsoft.com/cli/azure/) <br>
[Erfahren Sie mehr über Azure Files-Speicher.](../storage/files/storage-files-introduction.md) <br>
