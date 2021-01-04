---
title: 'Azure CLI-Skriptbeispiel: Hinzufügen einer Anwendung in Batch'
description: Dieses Beispielskript veranschaulicht das Hinzufügen einer Anwendung für die Verwendung mit einem Azure Batch-Pool oder einer Aufgabe.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 414a401168b1b378ed33f4904607243de0267d22
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565805"
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>CLI-Beispiel: Hinzufügen einer Anwendung zu einem Azure Batch-Konto

Dieses Skript veranschaulicht das Hinzufügen einer Anwendung für die Verwendung mit einem Azure Batch-Pool oder einer Azure Batch-Aufgabe. Um eine Anwendung einzurichten, die Sie Ihrem Batch-Konto hinzufügen können, fassen Sie die ausführbare Datei mit sämtlichen Abhängigkeiten in einer ZIP-Datei zusammen. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Für dieses Tutorial ist mindestens Version 2.0.20 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert. 

## <a name="example-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Führen Sie den folgenden Befehl aus, um die Ressourcengruppe und alle dazugehörigen Ressourcen zu entfernen:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle.
Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Erstellt ein Speicherkonto. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Erstellt das Batch-Konto. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Authentifiziert das angegebene Batch-Konto zur weiteren CLI-Interaktion.  |
| [az batch application create](/cli/azure/batch/application#az-batch-application-create) | Erstellt eine Anwendung  |
| [az batch application package create](/cli/azure/batch/application/package#az-batch-application-package-create) | Fügt der angegebenen Anwendung ein Anwendungspaket hinzu  |
| [az batch application set](/cli/azure/batch/application#az-batch-application-set) | Aktualisiert die Eigenschaften einer Anwendung  |
| [az group delete](/cli/azure/group#az-group-delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).
