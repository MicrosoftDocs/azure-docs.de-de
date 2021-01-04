---
title: 'Azure CLI-Skriptbeispiel: Ausführen eines Batchauftrags'
description: Dieses Skript erstellt einen Batch-Auftrag und fügt eine Reihe von Aufgaben zum Auftrag hinzu. Außerdem wird die Überwachung eines Auftrags und seiner Aufgaben veranschaulicht.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: b67925f48a9d2dbe0b4559d46d783b500e7a0773
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100914"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>CLI-Beispiel: Ausführen eines Auftrags und von Aufgaben mit Azure Batch

Dieses Skript erstellt einen Batch-Auftrag und fügt eine Reihe von Aufgaben zum Auftrag hinzu. Außerdem wird die Überwachung eines Auftrags und seiner Aufgaben veranschaulicht. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Für dieses Tutorial ist mindestens Version 2.0.20 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert. 

## <a name="example-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Führen Sie den folgenden Befehl aus, um die Ressourcengruppe und alle dazugehörigen Ressourcen zu entfernen:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Erstellt das Batch-Konto. |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | Authentifiziert das angegebene Batch-Konto zur weiteren CLI-Interaktion.  |
| [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create) | Erstellt einen Pool mit Computeknoten.  |
| [az batch job create](/cli/azure/batch/job#az-batch-job-create) | Erstellt einen Batch-Auftrag.  |
| [az batch task create](/cli/azure/batch/task#az-batch-task-create) | Fügt eine Aufgabe zum angegebenen Batch-Auftrag hinzu.  |
| [az batch job set](/cli/azure/batch/job#az-batch-job-set) | Aktualisiert die Eigenschaften eines Batch-Auftrags.  |
| [az batch job show](/cli/azure/batch/job#az-batch-job-show) | Ruft Details eines angegebenen Batch-Auftrags ab.  |
| [az batch task show](/cli/azure/batch/task#az-batch-task-show) | Ruft die Details einer Aufgabe aus dem angegebenen Batch-Auftrag ab.  |
| [az group delete](/cli/azure/group#az-group-delete) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in der [Azure CLI-Dokumentation](/cli/azure).
